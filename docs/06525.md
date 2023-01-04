# 寻找爱情，第 1 部分:介绍自然语言处理和治疗宝石

> 原文：<https://www.sitepoint.com/seeking-lovecraft-part-1-an-introduction-to-nlp-and-the-treat-gem/>

我们刚刚成立了不可知恐怖有限责任公司。我们的计划是在互联网的浩瀚字节海洋中进行筛选，以便找到下一个 H.P. Lovecraft，这样我们就可以通过出售高超的宇宙恐怖来发财。

或者，如果我们找不到下一个 H.P. Lovecraft，我们可能只是写我们自己的故事，并继续运行我们的程序来对抗它们，直到我们无法与 Lovecraft 区分开来。然后赚很多钱。

在本项目中，我们将做以下工作:

*   第一部分
    *   学习如何使用 treat gem 分析自然语言
*   第二部分
    *   想象不同作者和故事之间的差异，看看我们如何给作者做指纹识别
    *   建立一个系统来确定一个故事是否可能是洛夫克拉夫特写的

会思考的机器？检测他的工作？要是他知道就好了。

### 跟着走

该项目的数据可在[http://github.com/rlqualls/author-identification-tutorial](http://github.com/rlqualls/author-identification-tutorial)获得。

### 获取爱情故事

本教程中使用的所有非 Lovecraft 故事都可以在 github 资源库中找到。它们属于公共领域，是从古腾堡项目中获得的。为了获得最佳效果，它们已经被预先格式化，去掉了可能会干扰 treat 文本处理器的特殊格式。

然而，关于 H.P .洛夫克拉夫特的许多故事在美国是否受版权保护存在争议，因此这些故事被排除在外，除了《被避开的房子》。你需要得到他其余故事的拷贝，但是你可以很容易地从以下地方得到:

*   [古腾堡. net.au](http://gutenberg.net.au/ebooks06/0600031h.html)
*   [大公字节](http://www.dagonbytes.com/thelibrary/lovecraft/)(部分错别字)
*   [hplovecraft.com](http://www.hplovecraft.com/writings/fiction/)

我建议将 html 故事复制并粘贴到文本文件中，删除标题和任何可能存在的特殊格式。Treat 目前认为每一个换行符都是一个新段落，所以要检查每一段都在一行上。当然，首先要确保洛夫克拉夫特的故事在你的国家是公开的。

### 自然语言处理

> 它的声音是一种令人厌恶的窃笑，它会说各种语言

NLP 是人工智能和语言学的结合领域，涉及通过计算理解自然语言的能力。它代表了计算机在人类世界导航时面临的最可怕的障碍之一。因此，从事任何类型的 NLP 任务肯定不是一件小事，包括这个。在我们继续之前，重要的是我们要了解涉及到什么。

以这个字符串为例:

```
"the world is indeed comic, but the joke is on mankind." 
```

在最底层，计算机将字符串视为字节数组。接下来，它将这些字节与某种表示形式相关联，如 ASCII 或 UTF-8。例如，下面是通过十六进制以 ASCII 编码的字符串。可以用[ascitohex](http://www.asciitohex.com)自己看。

```
74 68 65 20 77 6F 72 6C 64 20 69 73 20 69 6E 64 65 65 64 20 63 6F 6D 69
    63 2C 20 62 75 74 20 74 68 65 20 6A 6F 6B 65 20 69 73 20 6F 6E 20 6D 61
    6E 6B 69 6E 64 2E
```

此时，计算机看到的是字符，而不是单词。为了从前面的句子中获取单词，我们需要告诉计算机单词是由什么符号组成的。这个过程叫做*标记化*。如果我们将字符串分解成一个令牌数组，它们看起来会像这样:

```
['the', 'world', 'is', 'indeed', 'comic', ',', 'but', 'the', 'joke', 'is',
 'on', 'mankind', '.']
```

经过标记化后，计算机知道哪些字符是单词，哪些是标点符号，但不知道其中任何一个是什么意思。这是事情从相当简单变得极其复杂的地方。

如果我们正在编写一种编程语言，这就是我们要构建解析器的地方。我们可以想出简单明了的规则，比如“所有语句都以换行符结尾”或者“do 和 end 标记之间的任何内容都是块”

现在，构建一个解析器很难被称为微不足道，但是这是我们开始欣赏编程语言和自然语言之间的区别的地方。

看看我们在句子的前两个词“世界”中遇到了什么问题哪个世界？整个世界？前几句引用的一个世界？如果我们一直在谈论一个特定的世界，但直到现在我们都在回避“世界”这个词，那会怎么样呢？接下来，一个世界漫画化意味着什么？也许作者的意思是“一个喜剧演员？”此外，“笑话”以前没有被提及，现在它似乎在“人类”之上。

此时，我们的系统看到了“漫画”和“笑话”，确定句子是幽默和轻松的，并通知我们它已经理解了一切。

这对我们的项目意味着什么？嗯，我们不可能指望教会计算机“理解”洛夫克拉夫特。让计算机有能力理解语言的每一个习惯用法是很难的。但是电脑可以做一些我们不能做的事情。

例如，对于一个人来说，手动计算一个故事中的名词、形容词、动词和副词的数量并不是一件小事。另一方面，计算机可以毫不费力地完成这样的壮举。我们将在后面看到，这样一个简单的计数可以告诉我们关于文档的很多信息。

寻找数字中隐藏的模式是迄今为止接近人工智能最成功的方法，我们的方法在这里也不会有什么不同。

## treat——Ruby NLP 工具包

根据项目页面，

> Treat 项目旨在为 Ruby 构建一个与语言和算法无关的框架，支持文档检索、文本分块、分割和标记化、自然语言解析、词性标注、关键字提取和命名实体识别。

你都明白了吗？

它能做的事情数量可能相当多，但是在本教程中我们将保持事情简单。

### 目标

*   成功安装 treat gem
*   做一些基本的自然语言处理
*   了解 treat 的树形结构
*   了解如何使用 treat 获得一些甜蜜的指标

### 装置

安装 treat 可能很棘手。首先，我们需要在 treat gem 上安装:

```
gem install treat --version 2.0.7
```

Treat 将其余的依赖项分解成语言包。因为本教程我们将使用英语，所以我们需要安装英语语言包。

但是，在此之前，请确保安装了 Java，并且将您的`$JAVA_HOME`环境变量设置为 Java 安装文件夹(带有 bin 和 include 的文件夹)。

接下来，打开一个`irb` shell 或编写一个 Ruby 脚本并运行以下代码:

```
require 'treat'
Treat::Core::Installer.install 'english'
```

这可能需要一段时间，具体取决于您的系统。观察以确保它安装了`stanford-core-nlp`包。

**如果 Java 没有安装或者`$JAVA_HOME`没有正确配置，该步骤将会失败，但是安装将会成功**。其结果是，当需要这个包时，Treat 一些功能会出错。

如果这一步仍然有问题，请尝试安装最新版本的 gem，因为语言包安装程序可能会有过时的代码。然而，这意味着本教程的某些方面可能不会像描述的那样起作用。

**注意**:treat 的一些功能需要在你的系统上安装其他的二进制文件，比如 Ocrupus 来读取图像。对于本教程，你应该不需要安装任何其他东西，但是如果你想使用所有的 treat，查看[Treat 手册](https://github.com/louismullie/treat/wiki/Manual)获取更多信息。

一旦安装了 treat，我们就可以开始玩它了。让我们看一些例子。

```
require 'treat'
include Treat::Core::DSL

'darkness'.category       # => "noun"
'abyss'.plural            # => "abysses"
'dreaming'.stem           # => "dream"
'think'.present_participle# => "thinking"
'towering'.synonyms       # => ["eminent", "lofty", "soaring", "towering"]
'perfection'.hypernyms    # => ["state", "ideal", "improvement"]
```

### 浏览零食树

> 在坟墓的一端，它的奇怪的根取代了时间染色的五旬节大理石块，生长着一棵不自然的大橄榄树，形状奇特而令人厌恶；如此像一些怪诞的人，或死亡扭曲的人的身体，乡下人害怕在晚上通过它，当月亮透过弯曲的树枝微弱地照耀。

让我们继续处理我们的第一个故事。Treat 为我们提供了“文档”方法。

```
story =
    document('collections/h_p_lovecraft/the_shunned_house.txt')
```

文章现在位于一个文档对象中，但还没有被处理。如果你尝试`story.paragraphs`或者`story.sentences`，你会得到一个空数组。这是因为，尽管文档树存在，但它没有节点。Treat 为创建节点提供了不同的文本处理器。

*   分块器–将文档分成章节和段落
*   分段——将段落和章节分成句子和标题
*   分词器——将句子和标题分解成单词

例如，如果我们想把我们的故事分成几个段落，我们可以使用这样的模块:

```
# Run the chunking processor
story.chunk

# Now we can access an array of all of the story's paragraphs
story.paragraphs

#Or the text of any paragraph
story.paragraphs.first.to_s
story.paragraphs[3].to_s
```

#### 小费

重要的是分块器**正确地完成它的工作**，因为其他处理器都依赖于它的结果。如果您分块一个名为`story`的文档，并且`story.paragraphs[0].to_s`没有在句子边界(句号)停止，那么句子和短语节点可能不会包含正确的文本。

如果你很难得到正确的结果，请仔细检查换行符是否只是描述段落，而不是其他。进行这种检查的一个简单方法是在编辑器中启用行号。

既然我们的故事被分成了几段，我们可以把第一段分成几个句子。请注意，由于我们有选择地对第一段进行分段，story.sentences 现在将只返回第一段中的句子。

```
# Run the segmenter on the first paragraph
story.paragraphs.first.segment

#=> An array of sentences in the first paragraph
story.paragraphs.first.sentences
story.sentences
```

### 使用应用

我们也可以使用`apply`方法来填充树。这一次，除了分块和分段，我们将把文档标记成单词。

```
# Instantiate a fresh document (we can't re-process documents)
story = document '/path/to/story'

# Run the chunker, segmenter, and tokenizer in succession.
story.apply(:chunk, :segment, :tokenize)

#=> An array containing all of the story's Word objects

story.words
#=> The first Word object in the first paragraph
story.paragraphs.first.words.first

#=> The number of words in the first sentence of the first paragraph
story.paragraphs[0].sentences[0].word_count
```

#### 小费

您将需要使用`to_s`来从节点获取字符串值。

```
# This gets an object, not a string
story.paragraphs.first

# This gets the actual text of the entire paragraph
story.paragraphs.first.to_s
# This will print nothing
puts story.words.first

# This prints the first word
puts story.words.first.to_s
```

对于洛夫克拉夫特的《被避开的房子》，`story.sentences[8].to_s`返回:

```
"The house was -- and for that matter still is -- of a kind to attract the attention of the curious."
```

### 添加词性节点

到目前为止，我们已经获得了段落、句子和单词。例如，如果能知道每段中有多少名词、动词和形容词，那不是很好吗？我们可以编写一个类似这样的帮助函数:

```
node_nouns = node.words.select { |word| word.category == "noun" }
```

然而，这不是必要的。通过输入`:category`到`#apply`，我们可以用词类节点来装饰树(注意:这假设我们之前应用了`:chunk`、`:segment`和`:tokenize`)。

```
story.apply(:category)
```

现在可以做如下事情:

```
# Get an array of all Word objects that are nouns
story.nouns

# Get an array of the lengths of all the verbs
story.verbs.map { |v| v.to_s.length }

# Get the number of conjuctions used
story.conjunction_count
```

### 不止一个故事——系列

Treat 集合有两个用途。首先，它们使得通过同一个树访问不同文档的节点成为可能。其次，它们允许我们在不知道文件名的情况下访问目录中的文档。您所需要做的就是将 stories 目录路径传递给`Kernel#collection`(假设已经包含 treat DSL)。

```
stories = collection 'collections/edgar_a_poe'

# Process the entire collection
stories.apply(:chunk, :segment, :tokenize, :category)

# Gets the noun percentage out of all the stories to two decimal places
(stories.noun_count.to_f / stories.word_count).round(2)

# Prints out the path of every story in the collection
stories.each_document do |story|
  puts story.file
end
```

### 衡量标准——单词流行度

可以使用各种度量来尝试对作者进行指纹识别。例如，我们可以使用每段的平均句子数。

不幸的是，使用简单的度量标准，我们会遇到混淆两位以相似风格写作的作者的风险。洛夫克拉夫特受到纳撒尼尔·霍桑和埃德加·爱伦·坡的影响，他的一些简单的度量标准表明了这一点。为了防止相似作者的混淆，我们需要比较作者故事的实际内容。

我们很容易得到的一个内容度量是单词流行度。如果一个故事的前 100 个单词大部分在另一个故事的后 100 个单词之内，那么这些故事可能不太相似。

Treat 提供了`Countable#frequency_of`，它返回一个单词出现的次数。如果我们创建一个散列，其中每个关键字都是一个单词，每个值都是该单词出现的次数，我们可以对其进行排序，以获得单词 popularity。

```
# Start off with an empty hash object
word_hash = {}

# Assign the word's frequency to its key in word_hash
# Note: frequency_of does perform #downcase internally,
# but iterating over a uniq downcased array prevents iterating over
# unnecessary instances in the story
downcased_words = story.words.map { |word| word.to_s.downcase }.uniq
downcased_words.each do |w|
  word_hash[w] = story.frequency_of(w)
end

# Create an array of [key, value] arrays, sorted greatest-to-least
word_popularity = word_hash.sort_by { |key, value| value }.reverse
```

这将获得故事中所有单词的单词流行度，但我们可能实际上对所有单词都不感兴趣。像“the”或“and”这样的词并不特定于任何类型的故事，它们在较高的排名中占据了很大的空间。由于形容词有助于确定故事的基调，我们可以通过单独对它们进行排序来获得大量信息。

```
adjective_popularity = word_popularity.select do |key, value|
  key.category == "adjective"
end
```

以下是我从《避开的房子》中得到的前 30 个形容词:

```
[["one", 35], ["more", 30], ["other", 19], ["two", 17], ["most", 16],
["old", 15], ["new", 14], ["many", 14], ["first", 12], ["great", 12],
["certain", 11], ["last", 11], ["strange", 10], ["such", 10],
["french", 9], ["same", 7], ["white", 7], ["next", 7], ["hideous", 7],
["light", 7], ["few", 6], ["ancient", 6], ["own", 6], ["sinister", 5],
["broken", 5], ["proper", 5], ["evil", 5], ["thin", 5], ["horrible", 5],
["terrible", 5], ["peculiar", 5]]
```

很明显，从这些词来看，这篇文章很可能是一部恐怖作品，像“奇怪”和“邪恶”这样的词在排名中靠前。

### 快速分析-名词百分比

在我们结束这个介绍之前，让我们看看是否能找到新闻文章和小说作品之间的区别信息。我们将比较集合的名词百分比。scripts 文件夹里有一个小脚本叫做 *noun_percentages.rb* 。

```
# !/usr/bin/env ruby

require 'pathname'
require 'treat'
include Treat::Core::DSL

def process_collection(path)
  puts "Author: #{Pathname.new(path).basename}"
  paths = Dir.glob(path + "/*")
  paths.each do |story_path|
    story = document story_path
    story.apply(:chunk, :segment, :tokenize, :category)
    noun_percentage = (story.noun_count / story.word_count.to_f).round(2)
    puts "#{Pathname.new(story_path).basename}: #{noun_percentage}"
  end
  puts ""
end

process_collection 'collections/edgar_a_poe'
process_collection 'collections/nathaniel_hawthorne'
process_collection 'collections/h_p_lovecraft'
process_collection 'collections/philip_k_dick'
process_collection 'collections/news'
```

这个脚本进入每个 author 文件夹，处理每个故事，并打印该故事中单词比名词多的百分比。运行时使用:

```
ruby scripts/noun_percentages.rb
```

您应该会得到这样的结果:

```
Author: edgar_a_poe
the_masque_of_red_death.txt: 0.2
the_fall_of_the_house_of_usher.txt: 0.2
the_pit_and_the_pendulum.txt: 0.19
the_black_cat.txt: 0.2
the_tell_tale_heart.txt: 0.16
the_premature_burial.txt: 0.19

Author: nathaniel_hawthorne
the_man_of_adamant.txt: 0.21
the_maypole_of_merry_mount.txt: 0.24
the_birth_mark.txt: 0.22
young_goodman_brown.txt: 0.24
the_minister's_black_veil.txt: 0.22

Author: h_p_lovecraft
the_shunned_house.txt: 0.24

Author: philip_k_dick
beyond_the_door.txt: 0.19
beyond_lies_the_wub.txt: 0.22
second_variety.txt: 0.24
the_variable_man.txt: 0.27

Author: news
obama_egypt.txt: 0.34
lab_mice.txt: 0.3
cambodian_vote.txt: 0.41
syria_war.txt: 0.37
```

注意到什么了吗？新闻文章的名词百分比明显高于小说作品。这是因为小说往往是高度描述性的，大量的文体词汇占据了词汇空间。另一方面，新闻通常写得又快又易读，所以避免了多余的话。

看起来，虽然它们可能会告诉我们一个故事是否可能是新闻，但名词百分比可能不足以确定洛夫克拉夫特或任何作者的指纹。在第 2 部分中，我们将建立一个更详细的分析系统，它将帮助我们弄清是什么使一个故事成为真正的洛夫克拉夫特式的。

### 资源

*   [对待项目页面](https://github.com/louismullie/treat)
*   [治疗手册](https://github.com/louismullie/treat/wiki/Manual)

## 分享这篇文章