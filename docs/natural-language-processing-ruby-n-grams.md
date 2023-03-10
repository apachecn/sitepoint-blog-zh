# 用 Ruby 进行自然语言处理:n-grams

> 原文：<https://www.sitepoint.com/natural-language-processing-ruby-n-grams/>

[![Blackboard "NLP"](img/ab1d8e18c3d45b27b256b5765bd33143.png)](https://www.sitepoint.com/wp-content/uploads/2013/09/Fotolia_51392976_Subscription_XXL.jpg)

自然语言处理(简称 NLP)是用计算机处理书面方言的过程。处理可以是任何事情——语言建模、情感分析、问题回答、关系提取，以及[更多的](https://en.wikipedia.org/wiki/Natural_language_processing#Major_tasks_in_NLP)。在这个系列中，我们将研究对各种形式的输入数据执行一些基本的和一些更高级的 NLP 技术的方法。

NLP 中最基本的技术之一是 *n* -gram 分析，这就是我们将在本文中开始的内容！

## 什么是 *n* -gram 分析

给出一个非常简单的句子:

> 那只敏捷的棕色狐狸跳过了那只懒狗。

对于一些语法分析来说，将这个句子分成连续的成对单词是非常有用的:

> (那个，快)，(快，棕色)，(棕色，狐狸)，…

在上面的例子中，我们将句子分成了连续的[个单词元组](https://en.wikipedia.org/wiki/Tuple)。上面的例子是*2*-克，通常被称为“二元模型”。一个 *1* 的 gram 称为“一元 gram”，一个 *3* 的 gram 称为“三元 gram”。对于有 4 个或更多成员的*n*-gram，我们通常坚持称之为 4-gram、5-gram 等。以下是一些例子:

*   单字:`(The), (quick), (brown), ...`
*   二元模型:`(The, quick), (quick, brown), (brown, fox), ...`
*   三元模型:`(The, quick, brown), (quick, brown, fox), (brown, fox, jumps), ...`
*   4-gram: `(The, quick, brown, fox), (quick, brown, fox, jumps), (brown, fox, jumps, over), ...`

这有什么用？假设我们有一个巨大的句子集合(其中一个是我们上面的“quick brown fox”句子)，我们在其中创建了一个包含所有可能二元模型的巨大数组。然后，我们得到一些用户输入，如下所示:

> 我真的很喜欢棕色敏捷的狐狸

如果我们也把这个句子分成两个词，我们可以相当肯定地说，两个词`(brown, quick)`很可能在语法上是不正确的，因为我们通常会以相反的方式遇到这两个词。对文本应用 *n* 文法分析是一种非常简单而强大的技术，经常在语言建模问题中使用，就像我们刚刚展示的那样，并且通常是更高级的 NLP 应用程序的基础(其中一些我们将在本系列中探讨)。

到目前为止，在我们展示的例子中，“ *n* -grams”的“grams”部分可以理解为“word”，但并不一定是这样。例如，在 DNA 测序中，“克”可能意味着碱基对序列中的一个字符。以碱基对序列“ATCGATTGAGCTCTAGCG”为例——我们可以从该序列中构建二元模型来计算哪些碱基对最常出现在一起，从而让我们能够预测序列中接下来可能会出现什么。例如，“A”后面经常跟着“G”，所以如果我们下次看到“A”，我们可以假设序列中的下一项将是“G”。

## 选择一个好的数据源

大多数 gram 分析依赖的一件事是数据源。在上面的例子中，我们需要大量的句子，以确定用户提供的句子中有错误，我们还需要足够的碱基对序列数据，以进行未来的 DNA 预测。

我们更普遍需要的是一些好的“训练数据”。我们需要一些我们可以信任的数据，我们可以使用这些数据来断言我们的分析正在做正确的事情，然后我们才能获取任何旧的用户数据并对其进行处理。

对于特定于语言分析的任务，事实证明，像这样为*n*gram 分析任务建立庞大的训练数据集已经由许多学术机构承担。这些巨大的文本数据集合被称为“文本语料库”(这是复数，其中一个集合被称为“文本语料库”)。这些语料库是有效标记的书面文本集。

1967 年，一部开创性的美国英语文集出版了，现在被称为“布朗文集”。汇编这本集子的目的是尽可能详尽地记录当时使用的美国英语。其结果是一个巨大的句子数据集，以合理的准确度反映了 1967 年人们在普通美国英语句子中可能遇到的某些单词的频率。

随着它的首次出版，它已经应用了“词性标注”(通常缩写为“词性标注”)(因此现在被称为语料库)。词性标注是用它的角色(动词、名词)来标记整个语料库中的每个单词的方法。维基百科维护着布朗语料库使用的标签列表，至少可以说是详尽的！

Brown 语料库仍然是最受欢迎的分析语料库之一，因为它对于非商业目的是免费的，并且容易获得。还有很多其他可用的语料库，包括谷歌的 *n* 克语料库，包含超过 1500 亿个单词！

## 获取棕色语料库

在这个系列的第一部分中，我们将获得 Brown 语料库的副本，并用它做一些简单的*n*gram 分析。布朗语料库的标记版本可以从[http://nltk . Google code . com/SVN/trunk/nltk _ data/packages/corpora/Brown . ZIP](http://nltk.googlecode.com/svn/trunk/nltk_data/packages/corpora/brown.zip)的 ZIP 文件中获得。

下载并解压这个 ZIP 文件，这样您就会看到一个包含许多`.txt`文件的`brown`目录:

`sh
$ ll brown
total 10588
-rw-r--r-- 1 nathan nathan 20187 Dec 3 2008 ca01
-rw-r--r-- 1 nathan nathan 20357 Dec 3 2008 ca02
-rw-r--r-- 1 nathan nathan 20214 Dec 3 2008 ca03
(...)` 

## 编写一个 *n* -grams 类

我们需要一种方法从一个句子中生成一组 *n* -grams。它应该取一个句子，把它分成几个块，然后以 *n* 长的组返回连续的成员。谢天谢地，这在 Ruby 中是微不足道的:

```
def ngrams(n, string)
  string.split(' ').each_cons(n).to_a
end
```

`each_cons`方法是在`Enumerable`模块中定义的，它通过返回每一组连续的`n`元素来完成我们所需要的任务。一旦我们将输入数据转换成`Array`格式，这实际上是一个内置的方法来完成我们想要做的事情。

让我们用一个非常简单的类来总结它:

```
class Ngram
  attr_accessor :options

  def initialize(target, options = { regex: / / })
    @target = target
    @options = options
  end

  def ngrams(n)
    @target.split(@options[:regex]).each_cons(n).to_a
  end

  def unigrams
    ngrams(1)
  end

  def bigrams
    ngrams(2)
  end

  def trigrams
    ngrams(3)
  end
end
```

我们添加了一个构造器方法，它接受要处理的`target`字符串，并允许人们在生成*n*grams 之前随意定义我们如何拆分给定的目标(例如，默认情况下是将其拆分成单词，但是您可以将其更改为拆分成字符)。)

然后我们为我们最常见的 *n* -grams 定义三个助手方法，即`unigrams`、`bigrams`和`trigrams`。

让我们看看它是否有效:

```
bigrams = Ngram.new("The quick brown fox jumped over the lazy dog").bigrams
puts bigrams.inspect # = >[["The", "quick"], ["quick", "brown"], ["brown", "fox"], ["fox", "jumped"], ["jumped", "over"], ["over", "the"], ["the", "lazy"], ["lazy", "dog"]]
```

成功！我们得到了我们想要的结果，而且几乎没有写任何代码！现在让我们用这个类对我们下载的棕色语料库做一些酷的事情。

## 从语料库中提取句子

在其原始形式中，我们获得的语料库包含带标签的句子。为了对语料库的内容应用任何种类的*n*gram 分析，我们需要通过移除标签并仅保留原始单词来提取原始句子。

标记语料库中的一个基本句子看起来像这样:

> /at Fulton/NP-TL County/nn-TL Grand/jj-TL Jury/nn-TL said/vbd Friday/NR an/at investigation/nn of/in Atlanta ' s/NP $ recent/jj 初选/nn 选举/nn 产生/vbd`/`no/at evidence/nn "/" that/cs any/DTI 违规行为/nn 取代/vbd place/nn。/.

单词和标签用一个`/`隔开。标签描述了我们正在看的单词的种类，例如“名词”或“动词”，以及单词的时态和角色，例如“过去时态”和“复数”。

给定一行，我们知道我们需要做的第一件事是获得所有单词的数组:

```
words = sentence.strip.split(' ')
```

我们调用`strip`只是为了清除我们可能有的任何前面或后面的空白，然后我们通过使用空格字符作为分隔符将句子分成单词。

一旦我们有了单词数组，我们需要拆分每个单词，只保留第一部分:

```
words.map do |word|
  word.split('/').first
end.join(' ')
```

这将在包含单词和标记的行上，返回一个没有标记和任何尾随空格的句子。

我们需要注意的另一件事是空行，它们散落在整个语料库中。假设我们有一个语料库文件的路径，这里有一个完整的脚本将它转换成一组句子:

```
def sentences(path)
  File.open(@path) do |file|
    file.each_line.each_with_object([]) do |line, acc|
      stripped_line = line.strip

      unless stripped_line.nil? || stripped_line.empty?
        acc << line.split(' ').map do |word|
          word.split('/').first
        end.join(' ')
      end
    end
  end
end
```

我们在这里调用的一个有趣的方法是`each_with_object`。这是由`Enumerable`模块提供的，实际上是`inject`的一个特殊版本。下面是一个不使用这两种方法的示例:

```
sentences = []
file.each_line do |line|
  # ...
  unless stripped_line.blank?
    # ...
    sentences << words.map do |word|
      # ...
    end
  end
end
```

你可以看到我们现在必须自己管理`sentences`变量。`inject`方法让我们更进一步:

```
file.each_line.inject([]) do |acc, line|
  # ...
  unless stripped_line.blank?
    # ...
    acc << words.map do |word|
      # ...
    end
  end
  acc
end
```

然而，我们必须返回`acc`(“accumulator”的缩写，这是在使用`inject`或其他折叠样式变体时构建的对象的通用命名法。)如果我们不这样做，那么下一次循环运行时的`acc`变量将变成我们在上一次循环中返回的值。因此，`each_with_object`诞生了，它负责总是返回你正在构建的对象，而你不必担心它。

> *提示:*`inject`和`each_with_object`之间的一个变化是`acc`参数以相反的顺序传递给程序块。在使用这些方法之前，请务必检查文档，以确保您已经掌握了正确的方法！

既然我们已经编写了一个从语料库的单个文件中获取句子的方法，那么让我们用一个类来总结它:

```
class BrownCorpusFile
  def initialize(path)
    @path = path
  end

  def sentences
    @sentences ||= File.open(@path) do |file|
      file.each_line.each_with_object([]) do |line, acc|
        stripped_line = line.strip

        unless stripped_line.nil? || stripped_line.empty?
          acc << line.split(' ').map do |word|
            word.split('/').first
          end.join(' ')
        end
      end
    end

  end
end
```

## 增强语料库类

为了进行有用的分析，我们需要编写最后一个类，允许我们对语料库中的一个或多个文件进行*n*gram 分析。

```
class Corpus
  def initialize(glob, klass)
    @glob = glob
    @klass = klass
  end

  def files
    @files ||= Dir[@glob].map do |file|
      @klass.new(file)
    end
  end

  def sentences
    files.map do |file|
      file.sentences
    end.flatten
  end

  def ngrams(n)
    sentences.map do |sentence|
      Ngram.new(sentence).ngrams(n)
    end.flatten(1)
  end

  def unigrams
    ngrams(1)
  end

  def bigrams
    ngrams(2)
  end

  def trigrams
    ngrams(3)
  end
end
```

这个类超级简单。我们有一个构造函数，它采用 glob 模式来选择 corpus 文件夹中的文件(您之前提取的`brown`文件夹)，还有一个类来传递找到的每个文件。如果你不熟悉 glob 模式，它们经常在终端中使用通配符，例如，`*.txt`是一个 glob 模式，它会找到所有以`.txt`结尾的文件。

然后我们定义`files`和`sentences`方法。前者使用 glob 来查找所有匹配的文件，遍历它们，并创建一个我们传递给构造函数的类的新实例。后者调用 files 方法，并在那些创建的类上循环，在类上调用`sentences`方法，并将结果展平到一个单层深度`Array`。

现在来看看包装我们的`Ngram`类的一些方便的方法。`ngrams`方法调用`sentences`并返回这些句子的一个数组 *n* -grams。注意，当我们调用`flatten`时，我们要求它只拉平一级，否则我们会丢失*n*-克`Array`。`unigrams`、`bigrams`和`trigrams`方法只是让事情看起来更好的辅助方法。

## 做一些克分析

我们已经准备好了一个基本的工具，所以让我们试一试，在语料库上做一些分析测试，看看我们能以一种简单的方式提取多少专有名词。在 NLP 行话中，这种技术被称为“命名实体识别”。

```
corpus = Corpus.new('brown/c*', BrownCorpusFile)
```

我们首先创建我们编写的`Corpus`类的一个新实例，并告诉它我们正在查看的语料库使用了`BrownCorpusFile`格式。

在文本编辑器(`ca01`)中打开第一个语料库文件，有许多专有名词(包括地点和名称)事先提到了“of”这个词。一个例子是句子“北平原的珍妮特·乔西[…]”。大多数专有名词可能只有 2 个单词或更少，所以让我们遍历三元模型中的所有句子，查找第一个成员为“of”而第二个成员以大写字母开头的任何内容。如果三元模型的第三个成员也以大写字母开头，我们将在结果中包含它。

我们正在寻找的输出格式是一个类似于以下格式的`Hash`:

```
{
  "Some Noun" => 2
}
```

其中本例中的`2`是给定专有名词出现的次数。

下面是这个问题的完整解决方案:

```
capitals = ('A'..'Z')
results = Hash.new(0)

corpus.trigrams.each do |trigram|
  if trigram.first == "of" && capitals.include?(trigram[1].chars.first)
    result = [trigram[1]]

    if capitals.include?(trigram[2].chars.first)
      result << trigram[2]
    end

    results[result] += 1

  end
end
```

该示例首先定义一个包含所有大写字母的范围。这个范围稍后用于检查三元组成员是否以大写字母开头。我们还创建了一个结果`Hash`，尽管我们使用`Hash.new(0)`来这样做，默认情况下将所有值设置为`0`。这允许我们增加每个键的值，而不必担心这个键以前是否被创建和设置过。

然后，代码开始通过遍历语料库中的所有三元模型来构建结果。对于每个三元模型:

*   检查第一个成员是否为“of”。
*   检查第二个成员的第一个字符是否是大写字母。
*   如果这两个都是真的，通过创建一个只包含第二个成员的`Array`来准备只存储第二个成员。
*   如果第三个成员也以大写字母开头，则将第三个成员添加到`Array`中。
*   用一个空格字符连接`Array`，并将其存储在结果`Hash`中，将该键的值递增 1。

最终，我们得到了填充了键/值对的结果`Hash`。关键字是我们找到的专有名词，值是我们在语料库中看到该专有名词的次数。以下是在整个 Brown 语料库中运行该代码后的前 10 名:

```
English: 22
Washington: 23
India: 23
New York: 26
Europe: 26
State: 35
American: 46
America: 61
God: 100
Af: 104
```

除了“Af”(如果你深入研究语料库，你会发现这是物理学中加速度计标度函数的名称——多么令人激动！)，其余的结果非常有趣——尽管你能从中得出什么结论完全取决于你自己！

## 源代码

我们在本文中撰写的所有内容的源代码都可以在 GitHub 上的存储库中获得:[https://github.com/nathankleyn/ruby*NLP*](https://github.com/nathankleyn/ruby_nlp)。具体见本系列这一部分的文件，网址为[https://github . com/nathanklein/rubynlp/blob/master/examples/part _ one . Rb](https://github.com/nathankleyn/ruby_nlp/blob/master/examples/part_one.rb)。

## 下次

在本系列的下一部分，我们将通过探索马尔可夫链来研究如何让我们的*n*gram 变得更加智能。这种数学概率的迷人应用允许我们通过近似语言模型来生成伪随机文本。

## 分享这篇文章