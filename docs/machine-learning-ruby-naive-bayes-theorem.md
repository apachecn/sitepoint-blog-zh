# 机器学习:Ruby 和朴素贝叶斯定理

> 原文：<https://www.sitepoint.com/machine-learning-ruby-naive-bayes-theorem/>

我们都听说过计算机科学中的“大数据”趋势:每个人和他们的祖母都试图从大型数据集获得洞察力。通常，这项工作的工具是机器学习。Ruby 社区的很大一部分围绕着 web 开发；即使在这里，一些基本的机器学习知识在创造性应用时也能派上用场。本文将带您了解一种简单(但通常非常有效)的机器学习技术:朴素贝叶斯分类器。

我们将通过基础理论，这确实需要一些数学背景，但不是太多。然后，我们将构建一个可重用的分类器实现，并通过一个示例数据集进行操作。

## 分类器

首先，什么是“量词”，具体来说？这个概念非常简单。给定关于给定对象的一些描述(称为“特征”)，分类器将该对象分类到一个类别中。显然，这不是一个完美的过程，我们不时会得到一个具有两种特征的物体。分类器通常根据其分类的准确性进行评级。

分类的一个示例是垃圾邮件检测。给定一段文本，分类器必须决定它是否是垃圾邮件。一个很好的问题是，“这段文本的哪些特征决定了它是否是垃圾邮件？”。这些特性或特征可以是诸如文本的长度、某些单词的存在或电子邮件中的链接数量。然后，分类器使用这些特征的值(它们可以是布尔值或实数)进行分类。

## 朴素贝叶斯分类器和贝叶斯定理

到目前为止，我们已经考虑了分类器背后的一般思想，但没有考虑实际的机制。有很多方法可以对物品进行分类，每种方法都有自己的优点和缺点。在本文中，我们将考虑朴素贝叶斯分类器(NBC)。

> 以下示例可以复制并粘贴到本页的[文本框中，以便更清晰地呈现。](https://www.mathjax.org/demos/)

假设我们有类(即类别)\[y_1，y_2，…，y_k\],并且我们被给定一组观察值\[X = x_1，x_2，…，X _ n \ ], NBC 试图找到以下值:

\[P(y_k \vert X = X = x_1，x_2，…，x_n)\]

换句话说，给定一个对象和一些特征值，NBC 试图计算出该对象属于给定类别的概率。我们对每个类都这样做，然后将对象分类到给我们最高概率的类中。当然，到目前为止，我们只是指定了想要查找的内容，而不是如何查找。这就是贝叶斯定理发挥作用的地方。上面写着:

\[p(a \ green b)= \ frac { p(b \ green a)\ CDO p(a)} { b }]

用英语来说，就是:

*   假设事件 B 发生(例如，多云的天空)，找出事件 A 发生的概率(例如，下雨)
*   将 B 发生的概率乘以给定的 A(即，给定下雨的多云天气)和 A 发生的概率(即，下雨的可能性)
*   除以概率 B(即多云天气的概率)。

这有点复杂；多读几遍解释，直到它“定型”可能会有帮助(在下面的评论中提出问题！).

我们可以用贝叶斯定理来说:

\[P(y_k \vert X = x_1，x_2，…，x_n) =
\frac{P(X = x_1，x_2，..x_n \vert y_k) P(y_k)}{P(x_1，x_2，… x_n)} \]

这是一个非常复杂的表情。但是，这只是贝叶斯定理的一个应用。我们使用的是类和特征值，而不是雨和多云的天空。

我们还不知道怎么算出\[P(X = x_1，x_2，..x_n \vert y_k)\](这是特征值出现的概率，假设它们是某个类的一部分。)这里我们做了一个叫做“独立性假设”的东西，它说“给定一个特定的类，特性的值是相互独立的。”

例如，如果我们知道现在是早餐时间，那么阳光明媚和我睡着的几率是相互独立的。有些情况下，这种假设是灾难性错误的，但也有很多情况下，它是相当准确的。

碰巧，我们可以把这写成我们的表达式:

\[P(y_k \vert X = X = x_1，x_2，…，X _ n)=
\ frac { P(X _ 1 \ vert y _ k)P(x_2 \ vert y _ k)…\ cdot P(X _ k \ vert y _ k)P(y _ k)} { P(X _ 1，X _ 2，… x_n)} \]

我们可以把这个概率想象成一个分数。一旦我们的 NBC 查看了特征值，它会给每个类别分配一个分数，最高的分数就是特征值的正确分类。但是表达式的分母与类\[y_k\]无关，因此对于所有的类都是常数，所以我们可以去掉它。我们最终的分数表达式可以写成:

\[\ text { class score for } y _ k = p(x _ 1 \ green y _ k)p(x _ 2 \ green y _ k)…\ CDO p(x _ k \ green y _ k)p(y _ k)\]

因此，我们已经将问题分解到我们只需要知道两种类型的事情:一个给定的特征值是一个类的一部分的概率\[P(x_i \vert y_k)\]和一个类出现的概率\[P(y_k)\]

如果假设类的大小大致相同，那么后者可以变得非常简单:

\[P(y_k) = \frac{1}{k}\]

第二个有点难。幸运的是，这个 18 世纪的叫高斯的家伙为我们找到了答案(使用正态分布，它并不是在所有地方都适用，但在许多情况下应该适用):

\[p(x _ I | y _ k)= \frac{1}{\sqrt{2\pi\sigma^2_c}}\,e^{ -\frac{(v-\mu_c)^2}{2\sigma^2_c} } \]

在上面的表达式中，\[\sigma_c\]表示\[y_k\]类的第 I 个特征的标准偏差，\[\mu_c\]表示其平均值。如果这个等式看起来有点像胡言乱语，你真正需要理解的唯一一件事就是，它使用了远离平均值的值出现的几率较低的思想(或者，更准确地说，它使用了正态分布)。

暂时足够的数学；我们去红宝石吧。

## 履行

首先，我们需要解决如何表示我们的训练数据。我们需要有类名，以及每个类名对应的一组训练特征值。我们可以让它看起来像这样:

```
{
  "class1" => [
    [feature1, feature2, feature3, ..., featureN],
    [feature1, feature2, feature3, ..., featureN]
  ],

  "class2" => [
    [feature1, feature2, feature3, ..., featureN],
    [feature1, feature2, feature3, ..., featureN]
  ],
}
```

对于部分实现(比如当计算高斯概率时)，我们需要计算关于某些集合的统计信息。为此，我们将使用[描述性统计 gem](https://github.com/thirtysixthspan/descriptive_statistics) 。

我们可以从勾勒出一些基本方法开始:

```
#used for mean, standard deviation, etc.
require 'descriptive_statistics'

class NaiveBayes

  #training data format:
  #{class-name: [[parameter1, parameter2, parameter3, ...],
  #              [parameter1, parameter2, parameter3,...]}
  def initialize(training_data, dim)
    @training_data = training_data
    @dimension = dim

  end

  def num_classes
    return @training_data.length
  end

  def classes
    return @training_data.keys
  end
```

注意`@dimension`指的是我们在每个特征集中拥有的特征数量(在上述训练数据格式中表示为“N”)。现在我们有了一些实用的方法，我们实际上可以开始破解 NBC 算法了。为了计算\[P(x_i \vert y_k)\]我们必须计算一个特征出现的平均值和标准差。

我的意思是。如果我们有以下训练集:

```
{
  ...
  "class1" => [
  [1, 1, 2],
  [1, 2, 2],
  [1, 16, 2],
  [1, 4, 2]
  ]
}
```

那么第一个和最后一个要素(不变)的标准差将为 0，而第二个要素的相同测量值为正数。为了计算这些信息，我们需要一种方法，可以将给定类中某个索引的所有特征放入一个集合中。提问，您将获得:

```
def feature_set(index, class_name)
  feature_set = []
  training_set = @training_data[class_name]

  training_set.length.times do |i|
    feature_set << training_set[i][index]
  end

  return feature_set
end
```

这也概述了机器学习代码与 web 代码的区别。在网络上，你可以比在许多 ML 应用程序中更快地发现错误。因为你的分类器的分类总是有可能是错误的，所以很难发现错误的来源是编码错误还是算法缺陷。因此，根据我的经验，ML 代码通常比我们为 web 编写的东西更加明确。

现在，我们可以为\[P(x_i \vert y_k)\]编写代码

```
#given a class, this method determines the probability
#of a certain value ocurring for a given feature
#index: index of the feature in consideration in the training data
#value: the value of the feature for which we are finding the probability
#class_name: name of the class in consideration
def feature_probability(index, value, class_name)
  #get the feature value set
  fs = feature_set(index, class_name)

  #statistical properties of the feature set
  fs_std = fs.standard_deviation
  fs_mean = fs.mean
  fs_var = fs.variance

  #deal with the edge case of a 0 standard deviation
  if fs_std == 0
    return fs_mean == value ? 1.0 : 0.0
  end

  #calculate the gaussian probability
  pi = Math::PI
  e = Math::E

  exp = -((value - fs_mean)**2)/(2*fs_var)
  prob = (1.0/(Math.sqrt(2*pi*fs_var))) * (e**exp)

  return prob
end
```

回到我们最后的课堂分数表达式:

\[\ text { class score for } y _ k = p(x _ 1 \ green y _ k)p(x _ 2 \ green y _ k)…\ CDO p(x _ k \ green y _ k)p(y _ k)\]

我们需要将所有索引的\[P(x_i \vert y_k)\]相乘，最后再乘以\[P(y_k)\]得到结果。Ruby 让这变得简单:

```
#multiply together the feature probabilities for all of the 
#features in a class for given values
def feature_mult(feature_values, class_name)
  res = 1.0
  feature_values.length.times do |i|
    res *= feature_probability(i, feature_values[i], class_name)
  end

  return res
end

#this is where we compute the final naive Bayesian probability
#for a given set of features being a part of a given class.
def class_probability(feature_values, class_name)
  class_fraction = 1.0 / num_classes
  feature_bayes = feature_mult(feature_values, class_name)
  res = feature_bayes * class_fraction
  return res
end
```

最后，我们只需按得分对类进行排序，然后返回得分最高的类(这是给定的一组特征值的分类):

```
#This the method we should be calling!
#Given a set of feature values, it decides
#what class to categorize them under
def classify(feature_values)
  res = classes.sort_by do |class_name|
    class_probability(feature_values, class_name)
  end

  return res[-1]
end
```

所有这些部分的代码都非常简单(可能除了`feature_probability`函数，但它本质上是将公式翻译成 Ruby ),这在很大程度上要归功于 Ruby。块，良好的循环语法，用一个键块进行简单的排序，等等。所有这些都使得 Ruby 成为一种非常适合机器学习的语言。

## 试车

既然我们已经构建了 NaiveBayes 类，我们就可以测试它了。Iris 数据集可以被视为机器学习社区中的某种标准。它提供了某些鸢尾花的花瓣和萼片尺寸的数据以及每个条目的物种标签。

条目的一个例子是:

```
4.8,3.0,1.4,0.3,Iris-setosa
```

我们的目标是训练我们的朴素贝叶斯分类器来获取特征值(数字)，并告诉我们这些数字可能是哪种物种。本质上，我们只需将训练数据集放在一起，并使用不属于训练集的一个单独的条目文件来测试分类器。您可以在本文的资源库中找到代码和数据。碰巧的是，NBC 能够正确识别 12 个特征值中的 11 个，考虑到训练集很小，这已经很不错了。

## 包装它

如今，最流行的语言似乎是 Python。在某种程度上，这可能是因为 Python 以 scikit-learn 和 numpy 的形式拥有广泛的库支持，也因为 Ruby 的以 web 为中心的社区在机器学习方面并不活跃。我认为 Ruby 是实现机器学习算法(特别是简单的算法)的一种很好的语言，而且我觉得 Web 开发人员也可以很好地掌握一些 ML 技巧！

## 分享这篇文章