# Ruby on Medicine:统计文件中的词频

> 原文：<https://www.sitepoint.com/ruby-on-medicine-counting-word-frequency-in-a-file/>

![](img/0f87c324c1d467defe975de32f86096a.png)

欢迎阅读 SitePoint 的 [Ruby on Medicine](https://www.sitepoint.com/ruby-on-medicine-converting-dicom-to-jpg/) 系列中的一篇新文章，在这篇文章中，我展示了 Ruby 编程语言如何应用于与医学领域相关的任务。

新手、中级和高级 Ruby 程序员都可以从本系列中受益，除此之外，健康/医学研究人员和从业者也期待着学习 Ruby 编程语言，并将其应用到他们的领域中。

在本文中，我将向您展示如何使用 Ruby 来检查某个文件中单词的出现频率。统计单词出现的频率在大型文本文件中会很方便，比如我们在[上一篇文章中处理过的](https://www.sitepoint.com/counting-real-words-with-ruby/)[omim^ ^-man^ ^在线孟德尔遗传](http://www.omim.org/about)文件。。统计单词的频率是有益的，因为浏览单词列表和它们的频率会让我们对文档的内容有更多的了解。它还可以指出任何错误和拼写错误，特别是当我们有一本字典来比较的时候。例如，当词典中的某个单词没有在输出中列出时，您可以简单地推断该单词出现了拼写错误，或者使用了缩写而不是单词本身。

在本文中，我们将使用的文件是 OMIM——人类孟德尔遗传在线文件。让我们开始吧。

## 获取 OMIM 文件

我在上一篇文章中讲过如何获取 OMIM 文件: [Ruby on Medicine:用 Ruby 统计真实单词](https://www.sitepoint.com/counting-real-words-with-ruby/)，但我在这里再重复一遍，以防你不想在文章之间来回奔波。

可通过以下步骤获得 OMIM^ ^:

去这个匿名的 ftp 地址:[ftp://ftp.ncbi.nih.gov](ftp://ftp.ncbi.nih.gov)。您应该会看到一个如下所示的对话框:

![dialogueBox](img/0a9efe284028c7db2f305d4c50b98b88.png)

选择*旁边的*客人*连接为:*，然后点击*连接*按钮，此时会看到如下目录:

![directory](img/e00b66836b58da732a1f80f3e2162c3d.png)

我们需要的文本文件是 **omim.txt.Z** 文件(66.3 MB)，可以在 **/repository/OMIM/ARCHIVE** 目录下找到。

解压缩文件，在这种情况下，您将获得 **omim.txt** (151.2 MB)。

## 混杂

由于我们将*检查文件中的单词*，并且*统计每个单词出现的频率*,[**散列**](http://ruby-doc.org/core-2.2.0/Hash.html) 将非常有帮助。

但是，什么是 [Ruby 哈希](http://www.tutorialspoint.com/ruby/ruby_hashes.htm)？

当我开始本节时，我提到我们将检查文件中的单词，以及每个单词出现的频率。哈希表示一组*键值*对。在我们的例子中，这将如下所示:

```
 "word" => frequency 
```

其中`word`是文件中的某个单词，`frequency`是表示该单词在该文件中出现的次数的数字。

因此，散列看起来很像一个数组，但不是一个整数值的索引，索引是*键*，可以是任何类型。

在 Ruby 中创建一个散列非常简单，可以按如下方式完成，这将创建一个*空的*散列:

```
 Hash.new 
```

如果您想创建一个带有默认值*的散列，您可以执行以下操作:*

```
 Hash.new(0) 
```

以上哈希阿哈的默认值为`0`。这意味着，如果您试图访问一个用默认值创建的散列中不存在的键，将返回默认值。为了阐明我在这里的意思，请看下面的例子，其中下面脚本中的两个`puts`将返回`0`:

```
 frequency = Hash.new(0)
puts "#{frequency[0]}"
puts "#{frequency[653]}" 
```

关于 Ruby 哈希的更多信息，你可以阅读[这篇文章](http://www.tutorialspoint.com/ruby/ruby_hashes.htm)和[文档](http://ruby-doc.org/core-2.2.0/Hash.html)。

## 计算出现的次数

在本节中，我们将构建 Ruby 程序，该程序将检查文件中每个单词的出现频率，并将结果存储在输出文件中。

我们要做的第一件事是创建一个新的 hash，并给它分配默认值`0`:

 <codere>频率= Hash.new(0)</codere>

我们希望将结果存储在一个文件中，所以 Ruby 脚本会将结果`write`到一个输出文件中。我们可以在`w`(写)模式下使用 [File.open()](http://ruby-doc.org/core-1.9.3/File.html#method-c-open) :

```
 output_file = File.open('wordfrequency.txt', 'w') 
```

操作的前端是读取 OMIM 文件，在我们的例子中，这个文件是 **omim.txt** 。该操作的语句如下所示:

```
 input_file = File.open('omim.txt', 'r') 
```

当我们在文件中查找单词时，我们需要的 Ruby 函数是 [scan(pattern)](http://ruby-doc.org/core-2.2.0/String.html) 。Ruby 文档中提到了以下关于这个函数的内容:

> scan 遍历*str*，匹配模式(可能是 Regexp 或字符串)。对于每个匹配项，都会生成一个结果，该结果或者被添加到结果数组中，或者被传递给块。如果模式不包含组，则每个单独的结果都由匹配的字符串$&组成。如果模式包含组，每个单独的结果本身就是一个数组，每个组包含一个条目。

当我们谈论模式匹配时，在这种情况下匹配文件中的单词，正则表达式(regex)就派上了用场。我在另一篇文章中谈到过正则表达式: [Ruby on Medicine:寻找基因序列](https://www.sitepoint.com/ruby-medicine-hunting-gene-sequence/)。

本教程中我们需要的主正则表达式是`\b`，即[字界](http://www.rexegg.com/regex-boundaries.html#wordboundary)。你可能已经从它的名字猜到了，它帮助我们匹配单词。

请参见以下示例，了解单词 boundary 是如何工作的:

```
 \bhat\b 
```

这个正则表达式匹配`red hat`中的`hat`。

如果我们只取一个边界如下:

```
 \bhat 
```

这将匹配一个开头为`hat`的单词，例如在`hatrat`中。

而且，如果我们想在最后匹配`hat`，比如`rathat`，我们可以使用这个正则表达式:

```
 hat\b 
```

下面的脚本将返回文件中每个单词出现的频率:

```
 frequency = Hash.new(0)
input_file = File.open('omim.txt', 'r')
output_file = File.open('wordfrequency.txt', 'w')
input_file.read.downcase.scan(/\b[a-z]{3,16}\b/) {|word| frequency[word] = frequency[word] + 1}
frequency.keys.sort.each{|key| output_file.print key,' => ',frequency[key], "\n"}
exit 
```

在向您展示输出之前，我只想快速澄清上面脚本中的一些要点。

[`downcase`](http://ruby-doc.org/core-2.1.0/String.html#method-i-downcase) 函数返回所有被小写字母替换的大写字母。我们在这里使用它，这样无论大小写都可以进行单词匹配。

正则表达式`/\b[a-z]{3,16}\b/`基本上是告诉我们返回长度(字符数)在 3 到 16 之间的所有单词。例如，我们可以跳过那些没有特别兴趣的词，如 *if，or，on* 。长度大于 16 的单词可能是一个基因序列，我们不会认为是一个单词。

最后，`sort`会将输出文件中列出的关键字(*即*单词)按字母顺序排序。

## 输出

使用上面的 Ruby 脚本，OMIM 文件中的单词列表及其出现频率将生成这个输出文件:[word frequency . txt](https://www.dropbox.com/s/h5dzvzfhw7cuclf/wordfrequency.txt?dl=0)(2.37 MB)

在列表中，您可能会注意到一些出现频率很低的单词。在像 OMIM^ ^这样的大型文本文件中，这意味着什么呢？它们是没有意义的词吗？拼错单词？你怎么想呢?

本系列的最后两篇文章分别介绍了如何使用正则表达式对特定单词和所有单词进行计数。我希望这两个例子能为您提供在自己的领域中应用这种技术的工具。

## 分享这篇文章