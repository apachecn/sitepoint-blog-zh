# 用 Node.js 和 Elasticsearch 搭建搜索引擎

> 原文：<https://www.sitepoint.com/search-engine-node-elasticsearch/>

这篇文章由[马克·布朗](https://www.sitepoint.com/author/mbrown)、[维尔丹·索蒂奇](https://www.sitepoint.com/author/vildansoftic/)和[莫里茨·克罗格](https://www.sitepoint.com/author/morkro/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

[Elasticsearch](https://www.elastic.co/products/elasticsearch) 是一个开源搜索引擎，由于其高性能和分布式架构而越来越受欢迎。在本文中，我将讨论它的主要特性，并带您了解使用它创建 Node.js 搜索引擎的过程。

## 弹性搜索简介

Elasticsearch 建立在 Apache Lucene 的基础之上，Apache Lucene 是一个高性能的文本搜索引擎库。尽管 Elasticsearch 可以执行数据的存储和检索，但它的主要目的不是作为数据库，而是作为一个搜索引擎(服务器),其主要目标是索引、搜索和提供数据的实时统计。

Elasticsearch 有一个分布式架构，允许通过添加更多节点和利用额外硬件进行水平扩展。它支持数千个节点来处理数 Pb 的数据。它的水平扩展还意味着，如果有任何节点出现故障，它可以通过重新平衡数据来实现高可用性。

数据导入后，可以立即进行搜索。Elasticsearch 是无模式的，将数据存储在 JSON 文档中，可以自动检测数据结构和类型。

Elasticsearch 也完全由 API 驱动。这意味着几乎任何操作都可以通过一个简单的 RESTful API 使用 HTTP 上的 JSON 数据来完成。它有许多几乎任何编程语言的客户端库，包括 Node.js。在本教程中，我们将使用官方客户端库。

Elasticsearch 在硬件和软件需求方面非常灵活。尽管推荐的生产设置是 64GB 内存和尽可能多的 CPU 内核，但您仍然可以在资源受限的系统上运行它，并获得不错的性能(假设您的数据集不是很大)。对于本文中的例子，一个 2GB 内存和一个 CPU 内核的系统就足够了。

您可以在所有主流操作系统(Linux、Mac OS 和 Windows)上运行 Elasticsearch。为此，您需要安装最新版本的 Java 运行时环境(参见*安装 Elasticsearch* 部分)。为了遵循本文中的示例，您还需要安装[node . js](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)(v 0 . 11 . 0 之后的任何版本都可以)，以及 [npm](https://www.sitepoint.com/beginners-guide-node-package-manager/) 。

### 弹性搜索术语

Elasticsearch 使用自己的术语，在某些情况下与典型的数据库系统不同。下面列出了 Elasticsearch 中的常用术语及其含义。

**索引**:这个术语在 Elasticsearch 上下文中有两个意思。首先是添加数据的操作。当添加数据时，文本被分解成记号(例如单词),并且每个记号都被索引。然而，索引也指所有索引数据的存储位置。基本上，当您导入数据时，它被编入索引。每当您想要对数据执行任何操作时，您需要指定它的索引名。

**Type** : Elasticsearch 在一个索引中提供了更详细的文档分类，称为 Type。索引中的每个文档也应该有一个类型。例如，我们可以定义一个`library`索引，然后将`article`、`book`、`report`、`presentation`等多种类型的数据编入索引。由于索引的开销几乎是固定的，所以建议少索引多类型，而不是多索引少类型。

**搜索**:这个词的意思是你可能会想到的。您可以在不同的索引和类型中搜索数据。Elasticsearch 提供多种类型的搜索查询，如术语、短语、范围、模糊，甚至地理数据查询。

**过滤器** : Elasticsearch 允许您根据不同的标准过滤搜索结果，以进一步缩小结果范围。如果向一组文档添加新的搜索查询，可能会根据相关性改变顺序，但如果添加相同的查询作为过滤器，顺序将保持不变。

**聚合**:这些为您提供不同类型的聚合数据统计信息，比如最小值、最大值、平均值、总和、直方图等等。

**建议** : Elasticsearch 为输入文本提供不同类型的建议。这些建议可以是基于术语或短语的，甚至是完成建议。

### 安装弹性搜索

Elasticsearch 在 Apache 2 许可下可用；它可以免费下载、使用和修改。在安装它之前，您需要确保您的计算机上安装了 Java 运行时环境(JRE)。Elasticsearch 是用 Java 写的，依赖 Java 库运行。要检查您的系统上是否安装了 Java，您可以在命令行中键入以下内容。

```
java -version 
```

建议使用 Java 的最新稳定版本(在撰写本文时是 1.8)。你可以在这里找到在你的系统上安装 Java 的指南。

接下来，要下载 Elasticsearch 的最新版本(撰写本文时为 2.4.0)，请进入[下载页面](https://www.elastic.co/downloads/elasticsearch)并下载 ZIP 文件。Elasticsearch 不需要安装，单个 zip 文件包含在所有支持的操作系统上运行该程序的完整文件集。解压下载的文件，就大功告成了！还有其他几种方法可以让 Elasticsearch 运行起来，比如获取不同 Linux 发行版的 TAR 文件或包(请看[这里](https://www.elastic.co/guide/en/elasticsearch/reference/current/setup.html))。

如果你运行的是 Mac OS X，并且安装了[自制软件](http://brew.sh/)，你可以使用`brew install elasticsearch`安装 Elasticsearch。Homebrew 会自动将可执行文件添加到您的路径中，并安装所需的服务。它还可以帮助您用一个简单的命令更新应用程序:`brew upgrade elasticsearch`。

要在 Windows 上运行 Elasticsearch，从解压后的目录中，从命令行运行`bin\elasticsearch.bat`。对于其他操作系统，从终端运行`./bin/elasticsearch`。此时，它应该正在您的系统上运行。

正如我前面提到的，几乎所有可以用 Elasticsearch 完成的操作都可以通过 RESTful APIs 来完成。Elasticsearch 默认使用端口 9200。为了确保运行正确，在浏览器中进入`http://localhost:9200/`，它应该会显示一些关于您正在运行的实例的基本信息。

要进一步了解安装和故障排除，您可以访问[文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/_installation.html)。

### 图形用户界面

Elasticsearch 通过 REST APIs 提供几乎所有的功能，并且不附带图形用户界面(GUI)。虽然我介绍了如何通过 API 和 Node.js 执行所有必要的操作，但是有几个 GUI 工具可以提供关于索引和数据的可视化信息，甚至一些高级分析。

由同一家公司开发的 Kibana 提供了数据的实时摘要，以及几个定制的可视化和分析选项。基巴纳是免费的，有详细的[文档](https://www.elastic.co/guide/en/kibana/current/index.html)。

社区还开发了其他工具，包括 [elasticsearch-head](http://mobz.github.io/elasticsearch-head/) 、 [Elasticsearch GUI](http://www.gridshore.nl/esgui/) ，甚至还有一个名为 [ElasticSearch Toolbox](https://chrome.google.com/webstore/detail/elasticsearch-toolbox/focdbmjgdonlpdknobfghplhmafpgfbp) 的 Chrome 扩展。这些工具帮助您在浏览器中浏览索引和数据，甚至尝试不同的搜索和聚合查询。所有这些工具都提供了安装和使用的演练。

## 设置 Node.js 环境

Elasticsearch 为 Node.js 提供了一个官方模块，名为`elasticsearch`。首先，您需要将模块添加到项目文件夹中，并保存依赖项以备将来使用。

```
npm install elasticsearch --save 
```

然后，您可以在脚本中导入该模块，如下所示:

```
const elasticsearch = require('elasticsearch'); 
```

最后，您需要设置处理与 Elasticsearch 通信的客户端。在这种情况下，我假设您在本地机器上运行 Elasticsearch，IP 地址为`127.0.0.1`，端口为`9200`(默认设置)。

```
const esClient = new elasticsearch.Client({
  host: '127.0.0.1:9200',
  log: 'error'
}); 
```

`log`选项确保记录所有错误。在本文的其余部分，我将使用同一个`esClient`对象与 Elasticsearch 进行通信。节点模块的完整文档在[这里](https://www.elastic.co/guide/en/elasticsearch/client/javascript-api/current/index.html)提供。

**注**:本教程的所有源代码都是[在 GitHub](https://github.com/sitepoint-editors/node-elasticsearch-tutorial) 上提供的。最简单的方法是将回购克隆到您的 PC 上，并从那里运行示例:

```
git clone https://github.com:sitepoint-editors/node-elasticsearch-tutorial.git
cd node-elasticsearch-tutorial
npm install 
```

## 导入数据

在本教程中，我将使用带有随机生成内容的学术文章数据集。数据以 JSON 格式提供，数据集中有 1000 篇文章。为了显示数据的样子，下面显示了数据集中的一个项目。

```
{
    "_id": "57508457f482c3a68c0a8ab3",
    "title": "Nostrud anim proident cillum non.",
    "journal": "qui ea",
    "volume": 54,
    "number": 11,
    "pages": "109-117",
    "year": 2014,
    "authors": [
      {
        "firstname": "Allyson",
        "lastname": "Ellison",
        "institution": "Ronbert",
        "email": "Allyson@Ronbert.tv"
      },
      ...
    ],
    "abstract": "Do occaecat reprehenderit dolore ...",
    "link": "http://mollit.us/57508457f482c3a68c0a8ab3.pdf",
    "keywords": [
      "sunt",
      "fugiat",
      ...
    ],
    "body": "removed to save space"
  } 
```

字段名是不言自明的。唯一需要注意的一点是，`body`字段没有在这里显示，因为它包含一篇完整的、随机生成的文章(有 100 到 200 段)。你可以在这里找到完整的数据集[。](https://github.com/sitepoint-editors/node-elasticsearch-tutorial/blob/master/data.json)

尽管 Elasticsearch 提供了用于[索引](https://www.elastic.co/guide/en/elasticsearch/client/javascript-api/current/api-reference.html#api-index)、[更新](https://www.elastic.co/guide/en/elasticsearch/client/javascript-api/current/api-reference.html#api-update)和[删除](https://www.elastic.co/guide/en/elasticsearch/client/javascript-api/current/api-reference.html#api-delete)单个数据点的方法，但我们将利用 elastic search 的[批量](https://www.elastic.co/guide/en/elasticsearch/client/javascript-api/current/api-reference.html#api-bulk)方法来导入数据，该方法用于以更高效的方式对大型数据集执行操作:

```
// index.js

const bulkIndex = function bulkIndex(index, type, data) {
  let bulkBody = [];

  data.forEach(item => {
    bulkBody.push({
      index: {
        _index: index,
        _type: type,
        _id: item.id
      }
    });

    bulkBody.push(item);
  });

  esClient.bulk({body: bulkBody})
  .then(response => {
    console.log('here');
    let errorCount = 0;
    response.items.forEach(item => {
      if (item.index && item.index.error) {
        console.log(++errorCount, item.index.error);
      }
    });
    console.log(
      `Successfully indexed ${data.length - errorCount} out of ${data.length} items`
    );
  })
  .catch(console.err);
};

const test = function test() {
  const articlesRaw = fs.readFileSync('data.json');
  bulkIndex('library', 'article', articles);
}; 
```

这里，我们调用了`bulkIndex`函数，将`library`作为索引名，`article`作为类型和我们希望索引的 JSON 数据。`bulkIndex`函数依次调用`esClient`对象上的`bulk`方法。这个方法接受一个带有`body`属性的对象作为参数。提供给`body`属性的值是一个数组，每个操作有两个条目。在第一个条目中，操作的类型被指定为 JSON 对象。在这个对象中，`index`属性决定了要执行的操作(在本例中是索引一个文档)，以及索引名、类型名和文档 ID。下一个条目对应于文档本身。

请注意，将来您可能会以这种方式将其他类型的文档(如书籍或报告)添加到同一索引中。我们还可以为每个文档分配一个唯一的 ID，但这是可选的——如果您没有提供，Elasticsearch 将为您的每个文档分配一个随机生成的唯一 ID。

假设您已经克隆了存储库，现在您可以通过从项目根目录执行以下命令将数据导入到 Elasticsearch 中:

```
$ node index.js
1000 items parsed from data file
Successfully indexed 1000 out of 1000 items 
```

### 检查数据索引是否正确

Elasticsearch 的一大特色是近乎实时的搜索。这意味着一旦文档被编入索引，它们就可以在一秒钟内被搜索到(参见[这里的](https://www.elastic.co/guide/en/elasticsearch/guide/current/near-real-time.html))。一旦数据被索引，您可以通过运行`indices.js` ( [链接到源](https://github.com/sitepoint-editors/node-elasticsearch-tutorial/blob/master/indices.js))来检查索引信息:

```
// indices.js

const indices = function indices() {
  return esClient.cat.indices({v: true})
  .then(console.log)
  .catch(err => console.error(`Error connecting to the es client: ${err}`));
}; 
```

客户端的`cat`对象中的方法提供了关于当前运行实例的不同信息。`indices`方法列出了所有的索引、它们的健康状态、它们的文档数量以及它们在磁盘上的大小。`v`选项为来自`cat`方法的响应添加了一个头。

当您运行上面的代码片段时，您会注意到它输出一个颜色代码来指示您的集群的健康状态。红色表示集群出现问题，并且没有运行。黄色表示集群正在运行，但是有一个警告，绿色表示一切正常。在本地机器上运行时，很可能(取决于您的设置)您会得到一个黄色状态。这是因为默认设置包含集群的五个节点，但是在您的本地机器上只有一个实例在运行。虽然在生产环境中您应该始终以绿色状态为目标，但在本教程中，您可以继续在黄色状态下使用 Elasticsearch。

```
$ node indices.js
elasticsearch indices information:
health status index   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   library   5   1       1000            0     41.2mb         41.2mb 
```

### 动态和自定义映射

正如我前面提到的，Elasticsearch 是无模式的。这意味着在导入数据之前，您不必定义数据的结构(类似于在 SQL 数据库中定义一个表)，而是由 Elasticsearch 自动检测。但是尽管被称为无模式，数据结构还是有一些限制。

Elasticsearch 将数据的结构称为映射。如果不存在映射，当数据被索引时，Elasticsearch 会查看 JSON 数据的每个字段，并根据其类型自动定义映射。如果该字段已经存在映射条目，它将确保添加的新数据遵循相同的格式。否则，它将抛出一个错误。

例如，如果`{"key1": 12}`已经被索引，Elasticsearch 会自动将字段`key1`映射为`long`。现在，如果您尝试索引`{"key1": "value1", "key2": "value2"}`，它会抛出一个错误，即它期望字段`key1`的类型是`long`。同时，对象`{"key1": 13, "key2": "value2"}`将毫无问题地被索引，类型`string`的`key2`被添加到映射中。

映射超出了本文的范围，在大多数情况下，自动映射工作得很好。我建议看一下 [elasticsearch 文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping.html)，它提供了对映射的深入讨论。

## 构建搜索引擎

一旦数据被索引，我们就可以实现搜索引擎了。Elasticsearch 提供了一个名为 *Query DSL* 的直观的全搜索查询结构——它基于 JSON——来定义查询。有许多类型的搜索查询可用，但在这篇文章中，我们将看看几个更常见的。查询 DSL 的完整文档可以在[这里](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html)找到。

> 请记住，我提供了每个示例背后的代码链接。在设置好您的环境并索引测试数据之后，您可以克隆[repo](https://github.com/sitepoint-editors/node-elasticsearch-tutorial)并在您的机器上运行任何示例。为此，只需从命令行运行`node filename.js`。

### 返回一个或多个索引中的所有文档

为了执行我们的搜索，我们将使用客户提供的各种搜索方法。最简单的查询是`match_all`，它返回一个或多个索引中的所有文档。下面的例子显示了我们如何在一个索引中获取所有存储的文档([链接到源](https://github.com/sitepoint-editors/node-elasticsearch-tutorial/blob/master/search_all.js))。

```
//search_all.js

const search = function search(index, body) {
  return esClient.search({index: index, body: body});
};

const test = function test() {
  let body = {
    size: 20,
    from: 0,
    query: {
      match_all: {}
    }
  };

  search('library', body)
  .then(results => {
    console.log(`found ${results.hits.total} items in ${results.took}ms`);
    console.log(`returned article titles:`);
    results.hits.hits.forEach(
      (hit, index) => console.log(
        `\t${body.from + ++index} - ${hit._source.title}`
      )
    )
  })
  .catch(console.error);
}; 
```

主搜索查询包含在`query`对象中。正如我们将在后面看到的，我们可以向这个对象添加不同类型的搜索查询。对于每个查询，我们添加一个查询类型的键(在本例中为`match_all`)，其值是一个包含搜索选项的对象。本例中没有选项，因为我们想要返回索引中的所有文档。

除了`query`对象，搜索主体还可以包含其他可选属性，包括`size`和`from`。`size`属性决定了响应中包含的文档数量。如果该值不存在，默认情况下将返回十个文档。`from`属性决定了返回文档的起始索引。这对分页很有用。

### 了解搜索 API 响应

如果您要从搜索 API(上例中的`results`)中注销响应，它最初看起来可能会令人不知所措，因为它包含了大量信息。

```
{ took: 6,
  timed_out: false,
  _shards: { total: 5, successful: 5, failed: 0 },
  hits:
   { total: 1000,
     max_score: 1,
     hits:
      [ [Object],
        [Object],
    ...
        [Object] ] } } 
```

在最高级别，响应包括一个属性`took`，表示找到结果所用的毫秒数，`timed_out`，如果在最大允许时间内没有找到结果，则只有`true`，`_shards`，表示不同节点的状态信息(如果部署为节点集群)，以及`hits`，其中包括搜索结果。

在`hits`属性内，我们有一个具有以下属性的对象:

*   `total` —表示匹配项目的总数
*   `max_score` —找到的项目的最高分
*   `hits` —包含找到的项目的数组。在`hits`数组的每个文档中，我们有索引、类型、文档 ID、分数和文档本身(在`_source`元素中)。

这很复杂，但是好消息是一旦你实现了一个提取结果的方法，不管你的搜索查询是什么，你将总是得到相同格式的结果。

还要注意，Elasticsearch 的一个优点是它会自动给每个匹配的文档分配一个分数。这个分数用于量化文档的相关性，默认情况下，返回的结果按分数降序排列。在我们用`match_all`检索所有文档的情况下，分数是没有意义的，所有分数都计算为 1.0。

### 匹配字段中包含特定值的文档

现在，让我们看一些更有趣的例子。为了匹配在字段中包含特定值的文档，我们可以使用`match`查询。下面显示了一个带有`match`查询的简单搜索主体([链接到源](https://github.com/sitepoint-editors/node-elasticsearch-tutorial/blob/master/search_match.js))。

```
// search_match.js

{
  query: {
    match: {
      title: {
        query: 'search terms go here'
      }
    }
  }
} 
```

正如我前面提到的，我们首先用搜索类型向查询对象添加一个条目，在上面的例子中是`match`。在搜索类型对象内部，我们标识要搜索的文档字段，这里是`title`。在里面，我们放了与搜索相关的数据，包括`query`房产。我希望在测试了上面的例子之后，你会开始对搜索的速度感到惊讶。

上面的搜索查询返回标题字段与`query`属性中的任何单词匹配的文档。我们可以如下设置匹配项的最小数量。

```
// search_match.js

...
match: {
  title: {
    query: 'search terms go here',
    minimum_should_match: 3
  }
}
... 
```

该查询匹配标题中至少包含三个指定单词的文档。如果查询中的单词少于三个，则所有单词都必须出现在要匹配的文档的标题中。搜索查询的另一个有用特性是模糊性。如果用户在编写查询时出现输入错误，这将非常有用，因为模糊匹配将找到拼写相近的术语。对于字符串，模糊度值基于每个术语的最大允许 [Levenshtein 距离](https://en.wikipedia.org/wiki/Levenshtein_distance)。下面是一个模糊的例子。

```
match: {
  title: {
    query: 'search tems go here',
    minimum_should_match: 3,
    fuzziness: 2
  }
} 
```

### 在多个字段中搜索

如果您想在多个字段中搜索，可以使用`multi_match`搜索类型。它类似于`match`，除了我们没有将字段作为搜索查询对象中的键，而是添加了一个`fields`键，它是要搜索的字段的数组。这里，我们在`title`、`authors.firstname`和`authors.lastname`字段中进行搜索。([链接到信号源](https://github.com/sitepoint-editors/node-elasticsearch-tutorial/blob/master/search_multi_match.js))

```
// search_multi_match

multi_match: {
  query: 'search terms go here',
  fields: ['title', 'authors.firstname',  'authors.lastname']
} 
```

`multi_match`查询支持其他搜索属性，比如`minimum_should_match`和`fuzziness`。Elasticsearch 支持通配符(如`*`)来匹配多个字段，所以我们可以将上面的例子简化为`['title', 'authors.*name']`。

### 匹配完整的短语

Elasticsearch 还可以精确匹配输入的短语，而无需在术语级别进行匹配。这个查询是常规的`match`查询的扩展，称为`match_phrase`。下面是一个`match_phrase`的例子。([链接到来源](https://github.com/sitepoint-editors/node-elasticsearch-tutorial/blob/master/search_match_phrase.js))

```
// match_phrase.js

match: {
  title: {
    query: 'search phrase goes here',
    type: 'phrase'
  }
} 
```

### 组合多个查询

到目前为止，在示例中，我们对每个请求只使用了一个查询。然而，Elasticsearch 允许你合并多个查询。最常见的复合查询是`bool`。`bool`查询接受四种类型的键:`must`、`should`、`must_not`和`filter`。顾名思义，结果*中的文档必须*匹配`must`内的查询，*不得*匹配`must_not`内的查询，如果匹配`should`内的查询，将获得更高的分数。所提到的元素中的每一个都可以接收查询数组形式的多个搜索查询。

下面，我们将使用`bool`查询和一种叫做`query_string`的新查询类型。这允许您使用关键字如`AND`和`OR`编写更高级的查询。关于`query_string`语法的完整文档可以在[这里](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html)找到。此外，我们使用`range`查询(文档[这里是](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-range-query.html))，它允许我们将一个字段限制在一个给定的范围内。(链接到[源](https://github.com/sitepoint-editors/node-elasticsearch-tutorial/blob/master/search_bool.js))

```
// search_bool.js

{
  bool: {
    must: [
      {
        query_string: {
          query: '(authors.firstname:term1 OR authors.lastname:term2) AND (title:term3)'
        }
      }
    ],
    should: [
      {
        match: {
          body: {
            query: 'search phrase goes here',
            type: 'phrase'
          }
        }
      }
    ],
    must_not: [
      {
        range: {
          year: {
            gte: 2011,
            lte: 2013
          }
        }
      }
    ]
  }
} 
```

在上面的示例中，查询返回的文档中，作者的名字包含`term1` *或*，他们的姓氏包含`term2`、*和*，他们的头衔包含`term3`，并且他们是在 2011、2012 或 2013 年出版的**而不是**。此外，在`body`中包含给定短语的文档会获得更高的分数，并显示在结果的顶部(因为`match`查询在`should`子句中)。

## 筛选器、聚合和建议

除了高级搜索功能，Elasticsearch 还提供其他功能。这里，我们来看三个更常见的特征。

### 过滤

通常，您可能希望根据特定标准来优化搜索结果。Elasticsearch 通过过滤器提供这种功能。在我们的文章数据中，假设您的搜索返回了几篇文章，您希望只选择在五年内发表的文章。您可以简单地从搜索结果中过滤掉所有不符合条件的内容，而无需改变搜索顺序。

过滤器与`bool`查询的`must`子句中的相同查询的区别在于，过滤器不会影响搜索分数，而`must`查询会。当返回搜索结果并且用户根据某些特定标准进行过滤时，他们不希望改变原始结果的顺序，相反，他们只希望从结果中删除不相关的文档。过滤器遵循与搜索相同的格式，但更常见的是，它们定义在具有确定值的字段上，而不是文本字符串上。Elasticsearch 推荐通过`bool`复合搜索查询的`filter`子句添加过滤器。

继续上面的例子，假设我们希望将搜索结果限制在 2011 年到 2015 年之间发表的文章。为此，我们只需要在原始搜索查询的`filter`部分添加一个`range`查询。这将从结果中删除任何不匹配的文档。下面是一个过滤查询的示例。(链接到[来源](https://github.com/sitepoint-editors/node-elasticsearch-tutorial/blob/master/filter.js)

```
// filter.js

{
  bool: {
    must: [
      {
        match: {
          title: 'search terms go here'
        }
      }
    ],
    filter: [
      {
        range: {
          year: {
            gte: 2011,
            lte: 2015
          }
        }
      }
    ]
  }
} 
```

### 聚集

聚合框架基于搜索查询提供各种聚合数据和统计信息。两种主要类型的聚合是指标聚合和分桶聚合，其中指标聚合跟踪并计算一组文档的指标，分桶聚合构建桶，每个桶与一个关键字和一个文档标准相关联。度量聚合的示例包括平均值、最小值、最大值、总和以及值计数。范围、日期范围、直方图和术语都是分时段聚合的示例。关于聚合器的深入解释可以在[这里](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html)找到。

聚合放在一个`aggregations`对象中，该对象本身直接放在`search`对象体中。在`aggregations`对象中，每个键都是用户分配给一个聚合器的名称。聚合器类型和选项应该作为该键的值。下面，我们来看两个不同的聚合器，一个指标和一个桶。作为一个指标聚合器，我们试图在我们的数据集(最早的文章)中找到最小的年份值，对于 bucket 聚合器，我们试图找到每个关键字出现的次数。(链接到[来源](https://github.com/sitepoint-editors/node-elasticsearch-tutorial/blob/master/aggregations.js))

```
// aggregations.js

{
  aggregations: {
    min_year: {
      min: {field: 'year'}
    },
    keywords: {
      terms: {field: 'keywords'}
    }
  }
} 
```

在上面的例子中，我们将指标聚合器命名为`min_year`(这个名称可以是任何名称)，它的类型是字段`year`上的`min`。存储桶聚合器被命名为`keywords`，它的类型是字段`keywords`上的`terms`。聚合的结果包含在响应的`aggregations`元素中，在更深的层次上，它们包含每个定义的聚合器(这里是`min_year`和`keywords`)及其结果。下面是这个例子的部分回答。

```
{
...
  "aggregations": {
    "keywords": {
      "doc_count_error_upper_bound": 0,
      "sum_other_doc_count": 2452,
      "buckets": [
        {
          "key": "pariatur",
          "doc_count": 88
        },
        {
          "key": "adipisicing",
          "doc_count": 75
        },
        ...
      ]
    },
    "min_year": {
      "value": 1970
    }
  }
} 
```

默认情况下，响应中最多返回 10 个桶。您可以在请求中的`field`旁边添加一个`size`键，以确定返回的桶的最大数量。如果您想要接收所有的存储桶，请将该值设置为 0。

### 建议

Elasticsearch 有多种类型的建议器，为输入的术语提供替换或完成建议(文档[此处](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-suggesters.html))。我们将在这里看术语和短语建议者。术语 suggester 为输入的文本中的每个术语提供建议(如果有的话)，而短语 suggester 将输入的文本视为一个完整的短语(而不是将其分解为术语)并提供其他短语建议(如果有的话)。要使用建议 API，我们需要在 Node.js 客户机上调用`suggest`方法。下面是一个术语建议者的例子。(链接到[来源](https://github.com/sitepoint-editors/node-elasticsearch-tutorial/blob/master/suggest_term.js))

```
// suggest_term.js

esClient.suggest({
  index: 'articles',
  body: {
    text: 'text goes here',
    titleSuggester: {
      term: {
        field: 'title',
        size: 5
      }
    }
  }
}).then(...) 
```

在请求体中，与所有其他客户端方法一致，我们有一个`index`字段来确定搜索的索引。在`body`属性中，我们添加我们正在寻找建议的文本，并且(和聚合对象一样)我们给每个建议者一个名字(在本例中是`titleSuggester`)。它的值决定了建议者的类型和选项。在这种情况下，我们为`title`字段使用了一个`term`建议器，并将每个令牌的最大建议数限制为五个(`size: 5`)。

来自 suggest API 的响应包含您请求的每个 suggester 的一个键，这是一个数组，大小与您的`text`字段中的术语数量相同。对于数组中的每个对象，都有一个包含其`text`字段中的建议的`options`对象。以下是对上述请求的部分回应。

```
...
"titleSuggester": [
  {
    "text": "term",
    "offset": 0,
    "length": 4,
    "options": [
      {
        "text": "terms",
        "score": 0.75,
        "freq": 120
      },
      {
        "text": "team",
        "score": 0.5,
        "freq": 151
      }
    ]
  },
  ...
]
... 
```

要获得短语建议，我们可以遵循与上面相同的格式，只需将 suggester 类型替换为`phrase`。在下面的示例中，响应遵循与上述相同的格式。(链接到[来源](https://github.com/sitepoint-editors/node-elasticsearch-tutorial/blob/master/suggest_phrase.js))

```
// suggest_phrase.js

esClient.suggest({
  index: 'articles',
  body: {
    text: 'phrase goes here',
    bodySuggester: {
      phrase: {
        field: 'body'
      }
    }
  }
}).then(...).catch(...); 
```

## 进一步阅读

Elasticsearch 提供了广泛的特性，远远超出了这篇文章的范围。在这篇文章中，我试图从一个较高的层次来解释它的特性，并向您推荐适当的资源来进行进一步的学习。Elasticsearch 非常可靠，并且具有出色的性能(我希望您在运行示例时注意到了这一点)。这一点，加上不断增长的社区支持，增加了行业中弹性搜索的采用，特别是在处理实时或大数据的公司中。

在看完这里提供的例子之后，我强烈建议看一下文档。它们提供了两个主要来源，一个是关于 Elasticsearch 及其特性的参考资料，另一个是关于实现、用例以及最佳实践的[指南](https://www.elastic.co/guide/en/elasticsearch/guide/current/index.html)。你也可以在这里找到 Node.js 客户端[的详细文档。](https://www.elastic.co/guide/en/elasticsearch/client/javascript-api/current/index.html)

你已经在用 Elasticsearch 了吗？你有哪些经历？或者，读完这篇文章后，你可能会尝试一下。请在下面的评论中告诉我。

## 分享这篇文章