# Python 自然语言处理入门

> 原文：<https://www.sitepoint.com/natural-language-processing-python/>

**今天生成的数据中有很大一部分是非结构化的。非结构化数据包括社交媒体评论、浏览历史和客户反馈。你有没有发现自己处于这样一种情况:有一堆文本数据要分析，却不知道如何着手？Python 中的自然语言处理可以有所帮助。**

本教程的目标是使您能够通过自然语言处理(NLP)的概念来分析 Python 中的文本数据。您将首先学习如何将文本标记成更小的块，将单词规范化为它们的词根形式，然后去除文档中的任何干扰，为进一步的分析做准备。

我们开始吧！

## 先决条件

在本教程中，我们将使用 Python 的`nltk`库对文本执行所有的 NLP 操作。在写这篇教程的时候，我们使用的是 3.4 版本的`nltk`。要[安装库](https://www.nltk.org/install.html)，可以在终端上使用`pip`命令:

```
pip install nltk==3.4 
```

要检查系统中的`nltk`版本，您可以将库导入 Python 解释器并检查版本:

```
import nltk
print(nltk.__version__) 
```

要在本教程的`nltk`中执行某些操作，您可能需要下载特定的资源。我们将在需要时描述每种资源。

但是，如果您想避免以后在教程中下载单个资源，并且现在就一次性获取它们，请运行以下命令:

```
python -m nltk.downloader all 
```

## 第一步:兑换成代币

一个计算机系统无法自己在自然语言中找到意义。处理自然语言的第一步是将原始文本转换成标记。一个**令牌**是连续字符的组合，具有某种意义。如何把一句话分解成记号，由你自己决定。例如，一个简单的方法是用空格将一个句子分割成单个的单词。

在 NLTK 库中，可以使用`word_tokenize()`函数将字符串转换成标记。然而，您首先需要下载`punkt`资源。在终端中运行以下命令:

```
nltk.download('punkt') 
```

接下来，您需要从`nltk.tokenize`导入`word_tokenize`来使用它:

```
from nltk.tokenize import word_tokenize
print(word_tokenize("Hi, this is a nice hotel.")) 
```

代码的输出如下所示:

```
['Hi', ',', 'this', 'is', 'a', 'nice', 'hotel', '.'] 
```

您会注意到`word_tokenize`不仅仅是基于空白分割字符串，还将标点符号分隔成记号。你可以决定是否在分析中保留标点符号。

## 步骤 2:将单词转换成它们的基本形式

当你处理自然语言时，你会经常注意到同一个单词有不同的语法形式。例如，“去”、“去”和“去了”是同一个动词“去”的形式。

虽然您的项目的必要性可能要求您保留各种语法形式的单词，但让我们讨论一种将同一单词的各种语法形式转换为其基本形式的方法。有两种方法可以将一个单词转换成它的基本单词。

第一个技巧是词干。**词干提取**是一个简单的算法，从一个单词中去除词缀。NLTK 中有[多种词干算法](http://www.nltk.org/howto/stem.html)可供使用。在本教程中，我们将使用波特算法。

我们首先从`nltk.stem.porter`导入`PorterStemmer`。接下来，我们将词干分析器初始化为`stemmer`变量，然后使用`.stem()`方法找到单词的基本形式:

```
from nltk.stem.porter import PorterStemmer 
stemmer = PorterStemmer()
print(stemmer.stem("going")) 
```

上面代码的输出是`go`。如果您为上述其他形式的“go”运行词干分析器，您会注意到词干分析器返回相同的基本形式“go”。然而，由于词干提取只是一种基于去除单词词缀的简单算法，当单词在语言中不常用时，它就失效了。

例如，当您尝试对单词“constitutes”使用词干分析器时，它会给出一个不直观的结果:

```
print(stemmer.stem("constitutes")) 
```

您会注意到输出是“constitut”。

这个问题可以通过一种更复杂的方法来解决，即在给定的上下文中找到单词的基本形式。这个过程被称为词汇化。**词条规范化**根据文本的上下文和词汇对单词进行规范化。在 NLTK 中，可以使用`WordNetLemmatizer`类对句子进行词汇化。

首先，您需要从 Python 终端中的 NLTK 下载器下载`wordnet`资源:

```
nltk.download('wordnet') 
```

下载完成后，您需要导入`WordNetLemmatizer`类并初始化它:

```
from nltk.stem.wordnet import WordNetLemmatizer 
lem = WordNetLemmatizer() 
```

要使用 lemmatizer，请使用`.lemmatize()`方法。它需要两个参数:单词和上下文。在我们的例子中，我们将使用“v”表示上下文。在查看了`.lemmatize()`方法的输出之后，让我们进一步探究上下文:

```
print(lem.lemmatize('constitutes', 'v')) 
```

您会注意到,`.lemmatize()`方法正确地将单词“constituents”转换为它的基本形式“constituent”。您还会注意到，词汇化比词干化需要更长的时间，因为算法更复杂。

让我们看看如何以编程方式确定`.lemmatize()`方法的第二个参数。NLTK 有一个`pos_tag()`函数，帮助确定一个单词在句子中的上下文。但是，您首先需要通过 NLTK 下载程序下载`averaged_perceptron_tagger`资源:

```
nltk.download('averaged_perceptron_tagger') 
```

接下来，导入`pos_tag()`函数并对一个句子运行它:

```
from nltk.tag import pos_tag
sample = "Hi, this is a nice hotel."
print(pos_tag(word_tokenize(sample))) 
```

您会注意到输出是一个配对列表。每一对都由一个标记和它的标签组成，标签表示标记在整个文本中的上下文。请注意，标点符号的标签是它本身:

```
[('Hi', 'NNP'),
(',', ','),
('this', 'DT'),
('is', 'VBZ'),
('a', 'DT'),
('nice', 'JJ'),
('hotel', 'NN'),
('.', '.')] 
```

如何解码每个令牌的上下文？这里有一个[网上所有标签及其相应含义](https://www.ling.upenn.edu/courses/Fall_2003/ling001/penn_treebank_pos.html)的完整列表。注意，所有名词的标签都以“N”开头，所有动词的标签都以“V”开头。我们可以在我们的`.lemmatize()`方法的第二个参数中使用这个信息:

```
def lemmatize_tokens(stentence):
  lemmatizer = WordNetLemmatizer()
  lemmatized_tokens = []
  for word, tag in pos_tag(stentence):
    if tag.startswith('NN'):
      pos = 'n'
    elif tag.startswith('VB'):
      pos = 'v'
    else:
      pos = 'a'
    lemmatized_tokens.append(lemmatizer.lemmatize(word, pos))
  return lemmatized_tokens

sample = "Legal authority constitutes all magistrates."
print(lemmatize_tokens(word_tokenize(sample))) 
```

上面代码的输出如下:

```
['Legal', 'authority', 'constitute', 'all', 'magistrate', '.'] 
```

这一产出是预期的，其中"构成"和"治安法官"分别转换为"构成"和"治安法官"。

## 步骤 3:数据清理

准备数据的下一步是清理数据，删除任何对您的分析没有意义的内容。概括地说，我们将从你的分析中去除标点符号和停用词。

去掉标点符号是一件相当容易的事情。`string`库的`punctuation`对象包含所有英文标点符号:

```
import string
print(string.punctuation) 
```

该代码片段的输出如下:

```
'!"#$%&amp;\'()*+,-./:;&lt;=&gt;?@[\\]^_`{|}~' 
```

要从令牌中删除标点符号，只需运行以下命令:

```
for token in tokens:
  if token in string.punctuation:
    # Do something 
```

接下来，我们将重点关注删除停用词。停用词是语言中常用的词，如“我”、“a”和“the”，在分析文本时，这些词对文本没有什么意义。因此，我们将从分析中删除停用词。首先，从 NLTK 下载程序下载`stopwords`资源:

```
nltk.download('stopwords') 
```

下载完成后，从`nltk.corpus`导入`stopwords`，并使用`.words()`方法，将“英语”作为参数。这是英语中 179 个停用词的列表:

```
from nltk.corpus import stopwords
stop_words = stopwords.words('english') 
```

我们可以将词汇化示例与本节中讨论的概念结合起来，创建下面的函数`clean_data()`。此外，在比较一个单词是否是停用词列表的一部分之前，我们将它转换成小写。这样，如果停用词出现在句子的开头并且是大写的，我们仍然可以捕获它:

```
def clean_data(tokens, stop_words = ()):

  cleaned_tokens = []

  for token, tag in pos_tag(tokens):
    if tag.startswith("NN"):
      pos = 'n'
    elif tag.startswith('VB'):
      pos = 'v'
    else:
      pos = 'a'

    lemmatizer = WordNetLemmatizer()
    token = lemmatizer.lemmatize(token, pos)

    if token not in string.punctuation and token.lower() not in stop_words:
      cleaned_tokens.append(token)
  return cleaned_tokens

sample = "The quick brown fox jumps over the lazy dog."
stop_words = stopwords.words('english')

clean_data(word_tokenize(sample), stop_words) 
```

该示例的输出如下:

```
['quick', 'brown', 'fox', 'jump', 'lazy', 'dog'] 
```

如您所见，标点符号和停用词已被删除。

## 词频分布

现在你已经熟悉了 NLP 中的基本清理技术，让我们试着找出文本中单词的频率。在这个练习中，我们将使用古腾堡免费提供的童话文本 *[【老鼠、小鸟和香肠】](https://www.gutenberg.org/files/2591/2591-h/2591-h.htm#link2H_4_0021)* 。我们将把这个童话的文本存储在一个字符串中，`text`。

首先，我们对`text`进行标记，然后使用上面定义的函数`clean_data`对其进行清理:

```
tokens = word_tokenize(text)
cleaned_tokens = clean_data(tokens, stop_words = stop_words) 
```

要查找文本中单词的频率分布，可以使用 NLTK 的`FreqDist`类。用标记作为参数初始化该类。然后使用`.most_common()`方法找到经常出现的术语。让我们试着找出这种情况下的前十个术语:

```
from nltk import FreqDist

freq_dist = FreqDist(cleaned_tokens)
freq_dist.most_common(10) 
```

以下是这个童话中最常出现的十个术语:

```
[('bird', 15),
('sausage', 11),
('mouse', 8),
('wood', 7),
('time', 6),
('long', 5),
('make', 5),
('fly', 4),
('fetch', 4),
('water', 4)] 
```

不出所料，这三个最常见的术语是童话中的三个主要人物。

在分析文本时，单词的频率可能不是很重要。通常，NLP 的下一步是生成一个统计数据— [TF-IDF](https://medium.freecodecamp.org/how-to-process-textual-data-using-tf-idf-in-python-cd2bbc0a94a3) (术语频率—逆文档频率)—它表示一个单词在文档列表中的重要性。

## 结论

在本教程中，我们已经初步了解了 Python 中的自然语言处理。我们将文本转换为标记，将单词转换为它们的基本形式，最后，清理文本以删除任何对分析没有意义的部分。

虽然我们在本教程中已经看到了简单的 NLP 任务，但是还有更多技术需要探索。例如，我们可能希望[在文本数据上执行主题建模](https://towardsdatascience.com/topic-modelling-in-python-with-nltk-and-gensim-4ef03213cd21)，目标是找到文本可能谈论的共同主题。NLP 中一个更复杂的任务是实现[一个情感分析模型](https://www.sitepoint.com/premium/books/sentiment-analysis-with-python)来确定任何文本背后的情感。

有任何意见或问题吗？请随时在推特上给我打电话。

## 分享这篇文章