# 弹性搜索的高效中文搜索

> 原文：<https://www.sitepoint.com/efficient-chinese-search-elasticsearch/>

如果你玩过 Elasticsearch，你就会知道分析和标记化是索引内容时最重要的步骤，如果没有它们，你的相关性会很差，你的用户会不高兴，你的结果排序会很差。

即使是英文内容，如果词干不好，你也会失去针对性，如果省略不当，你会错过一些文档，等等。如果你索引的是另一种语言，那就更糟了；默认分析器不是通用的。

当处理中文文档时，即使只考虑作为中国官方语言和世界上使用最多的语言的普通话，一切都更加复杂。让我们深入研究中文内容标记化，并揭示使用 Elasticsearch 实现这一点的最佳方法。

![logo (6)](img/4ff2d108ea06da3de5221cf0e649d7f2.png)

## 中文搜索有什么难的？

汉字是语标，它们代表一个词或一个语素(最小的有意义的语言单位)。放在一起，它们的意思可以改变，代表一个全新的单词。另一个困难是单词或句子之间没有空格，这使得计算机很难知道一个单词从哪里开始或结束。

中国有成千上万的汉字，即使在实践中，书写中文也需要三到四千的知识。让我们看一个例子:“火山”这个词(火山)实际上是以下两者的结合:

*   火:火
*   山:mountainsky

我们的记号赋予器必须足够聪明，以避免分离这两个符号，因为当它们不在一起时，意义就改变了。

另一个困难是使用的拼写变体:

*   简体中文:书法；
*   繁体中文，更复杂更丰富:書法；
*   还有拼音，普通话的罗马化形式: shū fǎ 。

## 分析中文内容

在撰写本文时，Elasticsearch 提供了以下解决方案:

*   [默认*中文*分析器](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/analysis-lang-analyzer.html#chinese-analyzer)，基于 Lucene 4 中被否决的类；
*   令人遗憾的是， [*paoding* 插件](https://github.com/medcl/elasticsearch-analysis-paoding)，没有被维护，但是基于非常好的字典；
*   对你的内容进行二进制分析的 [*cjk* 分析器](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/analysis-lang-analyzer.html#cjk-analyzer)；
*   在官方支持的插件下发布的智能中文分析器；
*   最后是 [ICU 插件和他的记号赋予器](http://www.elasticsearch.org/guide/en/elasticsearch/guide/current/icu-tokenizer.html#icu-tokenizer)。

这些分析器非常不同，我们将用一个简单的测试词来比较它们的性能:手机。
意为“手机”，由两个 logograms 组成，分别表示“手”和“机器”。这机语标也由许多其他单词组成:

*   机票:飞机票
*   Robot: robot
*   机枪:机关枪
*   机遇:机会

我们的符号化不能分割这些符号，因为如果我搜索“手机”，我不想要任何关于兰博拥有机关枪和看起来很坏的文档。

![rambo](img/b0065882e46aa15bdd9bcb95833d6024.png)

我们将使用出色的`_analyze` API 来测试我们的解决方案:

```
curl -XGET 'http://localhost:9200/chinese_test/_analyze?analyzer=paoding_analyzer1' -d '手机'
```

还有，我有没有提到这个[很棒的弹性搜索小抄](http://elasticsearch-cheatsheet.jolicode.com/)？

### 默认*中文*分析仪

这个分析器已经在你的 Elasticsearch 实例中可用，它使用 Lucene 的 *ChineseTokenizer* 类，该类只将所有的语标分离成标记。所以我们得到了两个代币:**手**和**机**。

弹性搜索*标准*分析仪产生完全相同的输出。为此，*中文*已被弃用，即将被*标准*取代，请避免使用。

### *paoding* 插件

Paoding 几乎是一种行业标准，被认为是一种优雅的解决方案。可悲的是，Elasticsearch 的插件没有被维护，我只设法让它在版本 1.0.1 上工作，在[做了一些修改](https://github.com/damienalexandre/elasticsearch-analysis-paoding)。以下是如何手动安装它:

```
git clone git@github.com:damienalexandre/elasticsearch-analysis-paoding.git /tmp/elasticsearch-analysis-paoding
    cd /tmp/elasticsearch-analysis-paoding
    mvn clean package
    sudo /usr/share/elasticsearch/bin/plugin -url file:/tmp/elasticsearch-analysis-paoding/target/releases/elasticsearch-analysis-paoding-1.2.2.zip -install elasticsearch-analysis-paoding

    # Copy all the dic config files to the ES config path - make sure to set the permissions rights, ES needs to write in /etc/elasticsearch/config/paoding!
    sudo cp -r config/paoding /etc/elasticsearch/config/
```

在这个笨拙的安装过程(要在所有节点上完成)之后，我们现在有了一个新的 *paoding* tokenizer 和两个收集器: *max_word_len* 和 *most_word* 。默认情况下不公开任何分析器，因此我们必须声明一个新的分析器:

```
PUT /chinese_test
    {
      "settings": {
        "number_of_shards": 1,
        "number_of_replicas": 0,
        "analysis": {
          "tokenizer": {
            "paoding1": {
              "type": "paoding",
              "collector": "most_word"
            },
            "paoding2": {
              "type": "paoding",
              "collector": "max_word_len"
            }
          },
          "analyzer": {
            "paoding_analyzer1": {
              "type": "custom",
              "tokenizer": "paoding1",
              "filter": ["standard"]
            },
            "paoding_analyzer2": {
              "type": "custom",
              "tokenizer": "paoding2",
              "filter": ["standard"]
            }
          }
        }
      }
    }
```

这两种配置都提供了良好的结果，具有干净和唯一的令牌。用更复杂的句子行为也很好。

### *cjk* 分析仪

非常简单的分析器，它只将任何文本转换成二元语法。“蝙蝠侠”变成了一串毫无意义的令牌:Ba，at，tm，ma，an。对于亚洲语言来说，这个标记器是一个很好的非常简单的解决方案，代价是索引更大，有时结果并不完全相关。

在我们的例子中，一个由两个字母组成的单词只有**手机**被索引，这看起来不错，但是如果我们用一个更长的词，比如**元宵节**(元宵节)，生成两个代币:**元宵**和**宵节**，分别意为*灯会*和*箫节*。

### 智能中文插件

非常容易安装，这要感谢 Elasticsearch 的维护人员:

```
bin/plugin -install elasticsearch/elasticsearch-analysis-smartcn/2.3.0
```

它公开了一个新的 *smartcn* 分析器，以及*smartcn _ token izer*token izer，使用 Lucene 的[*SmartChineseAnalyzer*](http://lucene.apache.org/core/4_9_0/analyzers-smartcn/org/apache/lucene/analysis/cn/smart/SmartChineseAnalyzer.html)。

它使用[隐马尔可夫](http://en.wikipedia.org/wiki/Hidden_Markov_model)模型和大量训练文本，运行一个概率套件来找到单词的最佳分离。所以已经嵌入了一个训练词典，它在普通文本上表现很好——我们的例子被适当地标记化了。

### ICU 插件

另一个官方插件。Elasticsearch 支持[“Unicode 国际组件”](http://site.icu-project.org/)库。

```
bin/plugin -install elasticsearch/elasticsearch-analysis-icu/2.4.1
```

这个插件是[也推荐](http://www.elasticsearch.org/guide/en/elasticsearch/guide/current/icu-plugin.html)如果你处理除了英语以外的任何语言，我一直用它来处理法语内容！

它公开了一个我们将要使用的 *icu_tokenizer* tokenizer，以及很多很棒的分析工具，比如 *icu_normalizer* 、 *icu_folding* 、 *icu_collation* 等。

它与包含词频信息的中文和日文文本字典一起工作，以推断语标组。在**手机**，一切正常，工作正常，但是在**元宵节**，产生两个代币:**元宵**和**节**——那是因为*灯会*和*节*比*灯会*更常见。

### 结果细分

| 分析者 | 手机(手机) | 元宵节(元宵节) | 元宵節(传统元宵节) |
| 中国的 | [hand] [machine] | [Yuan] [Night] [Festival] | [Yuan] [Night] [Festival] |
| paoding most_word | [cell phone] | [Lantern Festival] [Lantern Festival] | [Lantern Festival] [Festival] |
| paoding max_word_len | [cell phone] | Lantern Festival | [Lantern Festival] [Festival] |
| 中日韩 | [cell phone] | [Lantern Festival] [Lantern Festival] | [Lantern Festival] [Lantern Festival] |
| smartcn | [cell phone] | Lantern Festival | [Lantern Festival] [Festival] |
| icu_tokenizer | [cell phone] | [Lantern Festival] [Festival] | Lantern Festival |

*除了 ES 1.0.1* 中的 Paoding 外，这些测试都是使用 Elasticsearch 1.3.2 进行的。

在我看来， *paoding* 和 *smartcn* 效果最好。*中文*分词器很烂*ICU _ 分词器*对*有点失望元宵节*，但是处理繁体中文很好。

## 支持繁体中文

正如简介中所述，您可能需要处理来自文档或用户搜索请求的繁体中文。你需要一个规范化的步骤来将这些传统输入翻译成现代汉语，因为像 *smartcn* 或 *paoding* 这样的插件无法正确地操作它。

你可以从你的应用程序中这样做，或者尝试用[Elasticsearch-analysis-ST convert](https://github.com/medcl/elasticsearch-analysis-stconvert)插件直接在 elastic search 中处理它。它可以双向转换传统和现代汉语中的单词。遗憾的是，你必须手动编译它，就像上面显示的 paoding 插件一样。

最后一个解决方案是使用 *cjk* :如果不能正确地对输入进行标记化，你仍然有很好的机会捕捉到你需要的文档，然后用一个基于 *icu_tokenizer* 的信号来提高针对性，也是相当不错的。

## 用中文更进一步？

对于使用 Elasticsearch 进行分析来说，没有一个完美的通用解决方案，不管你处理的是什么内容，对于中文来说也是如此。你必须用你得到的信息组成和构建你自己的分析器。例如，我将对我的搜索字段使用 *cjk* 和 *smartcn* 标记化，使用[多字段](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/_multi_fields.html)和[多匹配查询](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl-multi-match-query.html)。

为了学习更多的中文，我推荐 [Chineasy](http://chineasy.org/) 这是获得一些基本阅读技巧的好方法！学习如此丰富的语言并不容易，你也应该在开始学习之前阅读[这篇文章](http://pinyin.info/readings/texts/moser.html)，这样你就知道你将进入什么样的环境了！快乐编码！

## 分享这篇文章