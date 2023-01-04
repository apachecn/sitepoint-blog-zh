# 用 JRuby 创建机器学习系统

> 原文：<https://www.sitepoint.com/creating-machine-learning-systems-with-jruby/>

![](img/fe427ced6f141bf40931e5d59cbd49fb.png)

所有不同的编程语言似乎都比 Ruby 更适合机器学习任务，对吗？Python 有 [scikit-learn](http://scikit-learn.org/) ，Java 有 [Weka](http://www.cs.waikato.ac.nz/ml/weka/index.html) ，C++中还有用于机器学习的[幕府](http://shogun-toolbox.org)等等。另一方面，Ruby 在快速原型开发方面享有盛誉。

那么，为什么不应该用 Ruby 来原型化机器学习系统呢？接受挑战！在本教程中，我们将构建一个可以为您自动分类 BBC 体育文章的系统。

哦，我们用红宝石做，好吗？嗯，这并不完全正确——我们将通过 [weka gem](https://rubygems.org/gems/weka) 使用 JRuby 和 Java 的 Weka 库。

### 准备

首先安装 [JRuby](https://jruby.org) v9.0.0.0+。然后创建一个`ml_with_jruby`目录，并将下面的 Gemfile 放入其中:

```
source 'https://rubygems.org'

# use your JRuby version here
ruby '2.3.1', engine: 'jruby', engine_version: '9.1.5.0'

gem 'weka'    # this provides us with the weka lib
gem 'scalpel' # used for text processing 
```

在 JRuby 环境中，运行`bundle install`来安装 gems。

接下来，[下载](http://mlg.ucd.ie/files/datasets/bbcsport-fulltext.zip)、 [BBC 体育文章](http://mlg.ucd.ie/datasets/bbc.html)的免费数据集，并将解压后的文章目录移动到`./data/training`目录中。

最后，将每个运动类型的最后两篇文章移到单独的`./data/test`目录中。

您的项目结构应该如下所示:

```
└── ml_with_jruby
    ├── data
    │   ├── test
    │   │   ├── athletics
    │   │   ├── cricket
    │   │   ├── football
    │   │   ├── rugby
    │   │   └── tennis
    │   └── training
    │       ├── athletics
    │       ├── cricket
    │       ├── football
    │       ├── rugby
    │       └── tennis
    └── Gemfile 
```

目录中的文本将是我们的测试文件，并用我们的分类器进行分类。

等等……训练，分类，分类器？这里有很多术语。让我们快速看一下它们的意思。

### 什么是分类？

分类就是“给定数据贴标签”。例如，一篇文章可以被标记为*网球*或*板球*。这些标签，*网球*和*板球*，被称为*类*。为我们的一篇文章选择标签的算法叫做*分类器*。

现在，有不同类型的分类问题:*有监督的*和*无监督的*。第一种通常被称为“聚类”，在这种情况下，您没有任何示例数据，并且您事先不知道您的算法会将您的数据划分到哪些类中。

*监督*意味着我们有预先标记的数据，就像我们标记的文章一样。我们使用这些标记的文章来训练我们的分类器，或者换句话说:建立一个可以决定如何对新数据进行分类的模型。训练结束后，我们可以将未标记的文章传递给我们的分类器，它会给我们一个标签。

也就是说，构建监督分类系统的三个步骤是:

*   从原始数据创建训练数据集
*   用训练数据集训练分类器
*   用训练好的分类器对新数据进行分类

### 创建数据集

让我们从编译我们的训练数据开始。

我们需要一些示例数据来告诉我们的分类器不同的文章类型是什么样子的。计算机是聪明的，但是我们不能期望它们接受文本并且对它是关于什么运动有一个好的直觉。因此，第一步是将原始文本转换成分类器可以使用的表示形式。计算机应该是擅长数字的，所以我们会用一组数字来描述文本的属性(所谓的*特征*)。

我们必须找到一些能够最好地将我们的数据分成不同文章类型的特征。例如，我们可以计算文本的总长度或文本中某些关键词的数量。在这一步，你可以发挥创造力，选择任何你想到的、有意义的东西。有些特征组合可以很好地协同工作，而有些则会降低分类器的性能。一旦你有了一个特征池，你就可以使用算法来选择最有价值的特征。为了简单起见，我们不会在本教程中讨论特性选择，而只是用我们良好的判断力来选择一小部分特性。

#### 从文本中提取特征

我们将在一个`FeatureExtractor`类中进行特征提取，该类获取一段文本并返回属性及其数字表示的散列。让我们直接把给定的文本加工成段落、句子(使用[手术刀](https://rubygems.org/gems/scalpel))和单词。当我们填充我们的功能散列时，我们将很快需要这些:

```
# feature_extractor.rb

require 'scalpel'

class FeatureExtractor
  attr_reader :text, :paragraphs, :sentences, :words

  def initialize(text)
    @text       = text.strip
    @paragraphs = text.split(/\n{2,}/)
    @sentences  = Scalpel.cut(text)
    @words      = text.scan(/[\w'-]+/)
  end

  def features
   {} # to be implemented next :)
  end
end 
```

首先，我们将添加一些明显的特征:统计描述运动本身的词语的出现次数，如一篇关于网球的文章中的“网球”，一篇关于板球的文章中的“板球”等。(请注意，例如，“运动员”既包括“运动员”，也包括“运动员”，等等)。

```
class FeatureExtractor
  # ...

  def features
    {
      athletics_hints_count: match_count('athlet'),
      cricket_hints_count:   match_count('cricket'),
      football_hints_count:  match_count('football'),
      rugby_hints_count:     match_count('rugby'),
      tennis_hints_count:    match_count('tennis')
    }
  end

  private

  def match_count(word)
    text.scan(/#{word}/i).count
  end
end 
```

文本中出现了多少专有名词，如名称和团队，这可能很有趣。所以我们会添加一个`capitalized_words_count`特性。

关于例如网球和田径的文章可能比例如足球文章更可能谈论女性。因此，我们将在一个扫描男性和女性关键词的功能中涵盖这一点，并指出哪一个出现得最频繁。姑且称之为`gender_dominance`。

此外，添加一些更通用的文本特征，如`text_length`、`sentence_count`、`paragraphs_count`和`words_per_sentence_average`。

当你通读我们的几篇培训文章时，似乎有些人比其他人说得更多，所以让我们也来数一下文中的引用。

你明白了。试着提取一些可能区分文章内容的属性。

我们将添加一些额外的功能，通过计算代词(我、我的、我与我们、我们的、我们)等提示的数量，以及一个可能指示分数或时间很重要的运动的`number_count`功能，来指示它更像是团队还是个人运动。

有了这个，我们现在就可以完成我们的提取器类了:

```
class FeatureExtractor
  # ...

  def features
    {
      athletics_hints_count:      match_count('athlet'),
      cricket_hints_count:        match_count('cricket'),
      football_hints_count:       match_count('football'),
      rugby_hints_count:          match_count('rugby'),
      tennis_hints_count:         match_count('tennis'),
      capitalized_words_count:    capitalized_words_count,
      gender_dominance:           gender_dominance,
      text_length:                text.length,
      sentences_count:            sentences.count,
      paragraphs_count:           paragraphs.count,
      words_per_sentence_average: words_per_sentence_average,
      quote_count:                quote_count,
      single_sport_hints_count:   terms_count(%w(I me my)),
      team_sport_hints_count:     terms_count(%w(we us our team)),
      number_count:               number_count
    }
  end

  private

  def match_count(word)
    text.scan(/#{word}/i).count
  end

  def capitalized_words_count
    words.count { |word| word.start_with?(word[0].upcase) }
  end

  def gender_dominance
    terms_count(%w(she her)) > terms_count(%w(he his)) ? 1 : 0
  end

  def terms_count(terms)
    words.count { |word| terms.include?(word.downcase) }
  end

  def words_per_sentence_average
    sentences.count.zero? ? 0 : (words.count / sentences.count)
  end

  def quote_count
    text.scan(/"[^"]+"/).count
  end

  def number_count
    text.scan(/\d+[\.,]\d+|\d+/).count
  end
end 
```

#### 编译训练数据集

我们希望从我们的文本特征中编译一个数据集，并将其保存为一个文件，以便我们可以在以后加载它并训练我们的分类器。我们也可以在内存中完成所有操作，但是当将数据集存储为文件时，我们可以查看它并更好地理解这一步中实际发生的事情。Weka 用它的`Weka::Core::Instances`类提供了一个很好的方法来做这件事。

在一个单独的脚本中，加载我们的训练文本并提取我们的特征。用它们创建一个`Instances`对象，最后将我们的数据集存储在我们的磁盘上。在我们开始之前，让我们创建另一个`FileLoader`和`Text`类，它们将很好地抽象我们的文件加载和从给定文件中提取特征。

`FileLoader`将从给定的数据目录中返回所有文本文件:

```
# file_loader.rb

class FileLoader
  attr_reader :data_directory

  def initialize(data_directory)
    @data_directory = File.expand_path("../#{data_directory}", __FILE__)
  end

  def files_for(article_type)
    Dir.glob("#{data_directory}/#{article_type}/*.txt")
  end
end 
```

我们的`Text`类允许我们通过使用上面创建的`FeatureExtractor`来传递一个文本文件并获取它的特性:

```
# text.rb

require_relative 'feature_extractor'

class Text
  attr_reader :text

  def initialize(file)
    file_path = File.expand_path(file, __FILE__)

    # There seem to be some invalid UTF-8 characters in the texts,
    # so we remove them here.
    @text = File.read(file_path).encode!('UTF-8', 'UTF-8', invalid: :replace)
  end

  def features
    FeatureExtractor.new(text).features
  end
end 
```

我们现在可以使用这些类来编写用于创建训练数据集的脚本。创建一个名为`create_dataset.rb`的新文件。首先，创建一个空的`Instances`对象来表示我们的训练数据集。为每个要素添加一个数字属性和一个名义类属性。我们将不同的文章类型配置为可能的类值:

```
# create_dataset.rb

require 'weka'
require_relative 'feature_extractor'
require_relative 'file_loader'
require_relative 'text'

article_types   = %i(athletics cricket football rugby tennis)
attribute_names = FeatureExtractor.new('').features.keys

dataset = Weka::Core::Instances.new.with_attributes do
  attribute_names.each do |name|
    numeric(name)
  end

  nominal(:class, values: article_types, class_attribute: true)
end
# ... 
```

接下来，计算所有文章的特征，并将它们添加到我们的`instances`对象中:

```
# ...
def feature_list_for(article_type)
  files = FileLoader.new('data/training').files_for(article_type)

  files.map do |file|
    # Remember that Text#features returns a Hash.
    # We only need the feature values.
    # Since the class value is still missing, we append the
    # article_type as the class value.
    Text.new(file).features.values << article_type
  end
end

article_types.each do |article_type|
  feature_list = feature_list_for(article_type)
  dataset.add_instances(feature_list)
end
# ... 
```

最后，我们可以将所有计算出的特征保存到`/generated`目录下的一个文件中。`Instances`允许在不同文件格式之间保存和加载数据集，如 CSV、JSON、ARFF 和不太常见的 C.45 文件格式。让我们在这里选择 [ARFF](http://weka.wikispaces.com/ARFF) ( *属性关系文件格式*)，这是专门为机器学习任务开发的数据集，对人类来说也非常易读:

```
# ...
dataset.to_arff('generated/articles.arff') 
```

在您的终端中运行脚本以创建训练数据集:

```
$ jruby create_dataset.rb # If you're using RVM, this is just `ruby...` 
```

如果您快速查看生成的`.arff`文件，您会看到一个带有可定制关系名称和已定义属性的标题，后面是实际的数据行:

```
@relation Instances

@attribute athletics_hints_count numeric
@attribute cricket_hints_count numeric
# ...
@attribute class {athletics,cricket,football,rugby,tennis}

@data
1,0,0,0,0,47,1,1237,11,3,19,2,1,0,7,athletics
1,0,0,0,0,46,1,901,7,2,20,0,0,2,5,athletics
# ... 
```

随着我们的训练数据集的编译，我们现在可以继续训练我们的分类器并对我们的测试文章进行分类。

### 训练分类器

有许多不同的内置分类器可供我们选择。我们可以使用贝叶斯分类器、神经网络、逻辑回归、决策树等等。为了简单起见，我们将使用 [*随机森林*](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) 分类器。有了 RandomForest，我们得到了一个易于配置的分类器，它基于决策树，对常见问题表现良好。

是时候加载训练数据集，然后训练一个 RandomForest 分类器了。让我们在一个名为`run_classification.rb`的新文件中完成它。

```
# run_classification.rb

require 'weka'

instances = Weka::Core::Instances.from_arff('generated/articles.arff')
instances.class_attribute = :class

classifier = Weka::Classifiers::Trees::RandomForest.new

# The -I option determines the number of decision trees that are used in each
# learning iteration, the default is 100, we increase it to 200 here to gain a
# better performance.
classifier.use_options('-I 200')

classifier.train_with_instances(instances) 
```

请注意，在加载数据集之后，我们必须手动设置 class 属性。这是必要的，因为在我们的 ARFF 文件中没有关于我们的类属性的位置的信息(它不总是最后一个！).

这很容易。我们的测试文章已经在等着我们了！

### 测试物品分类

我们现在可以使用我们训练过的分类器来对`data/test`目录中的(假设)未标记文章进行分类。

在我们将测试文章传递给分类器之前，我们必须从它们中提取与训练文本相同的特征。幸运的是，我们可以再次使用我们的`FileLoader`和`Text`类:

```
# run_classification.rb

require 'weka'
require_relative 'file_loader' # <= added!
require_relative 'text'        # <= added!

# ...

article_types = %i(athletics cricket football rugby tennis)

def feature_list_for(article_type)
  files = FileLoader.new('data/test').files_for(article_type)

  files.map do |file|
    # Remember again that Text#features returns a Hash.
    # We only need the feature values.
    # The class value is still missing, but this time, we append a "missing"
    # as class value. You can use nil, '?' or Float::NAN.
    Text.new(file).features.values << '?'
  end
end

article_types.each do |article_type|
  feature_list = feature_list_for(article_type)

  feature_list.map do |features|
    label = classifier.classify(features)
    puts "* article about #{article_type} classified as #{label}"
  end
end 
```

在这里，我们加载我们的测试文本，并将它们提取的特征传递给我们的分类器的`classify`方法。分类后，将我们预测的类输出到 stdout。

运行脚本并让查看输出:

```
$ jruby run_classification.rb

* article about athletics classified as athletics
* article about athletics classified as athletics
* article about cricket classified as cricket
* article about cricket classified as cricket
* article about football classified as football
* article about football classified as football
* article about rugby classified as rugby
* article about rugby classified as rugby
* article about tennis classified as tennis
* article about tennis classified as tennis 
```

耶。看起来我们所有的文章都贴对了标签！

然而，这并不意味着我们的分类系统是完美的。在训练分类器时，可以通过一种叫做 [*交叉验证*](https://en.wikipedia.org/wiki/Cross-validation_(statistics))# k-fold _ cross-validation)的方法来评估分类器的性能。Weka 还为我们的分类器提供了一个`cross_validate`方法。

交叉验证将训练数据集分成 N 个不同的部分，每个部分都有相同数量的实例。默认情况下，它使用 10 个拆分。然后用 9 个子集训练分类器，对剩余集进行分类。这样做，直到在用其他 9 个子集训练分类器之后，每个子集都被分类。通过这个过程，您可以了解分类器的性能有多好，因为您已经知道所有的标签，并且可以计算某些度量。

让我们看看我们的分类器的 10 重交叉验证:

```
evaluation = classifier.cross_validate(folds: 10)
puts evaluation.summary

# Correctly Classified Instances         602               82.8061 %
# Incorrectly Classified Instances       125               17.1939 %
# Kappa statistic                          0.7708
# Mean absolute error                      0.1223
# Root mean squared error                  0.2281
# Relative absolute error                 39.9808 %
# Root relative squared error             58.3231 %
# Coverage of cases (0.95 level)          97.9367 %
# Mean rel. region size (0.95 level)      52.7373 %
# Total Number of Instances              727 
```

在前两行中，我们看到，我们的分类器仅对大约 83%的文章进行了正确分类。对于我们小而不自然的特性集来说，这实际上还不算太坏。通过一组精心选择的特性，您可以预期性能会有所提高。现在就看你的了，开始寻找最好的特性吧！

### 结论

在本文中，我们使用 JRuby 对体育文章进行了自动分类。我们经历了构建分类系统的三个基本步骤:从原始文本中提取特征、构建训练数据集和训练分类器。利用训练好的分类器，我们对未标记的文章进行了分类。

看起来 Ruby 也可以成为你机器学习任务的最好的朋友，我真的鼓励你去看看 Weka 框架并尝试一下。这不仅是一个很好的练习，而且让你发现基本的机器学习实际上不是火箭科学！试一试，让我知道进展如何。

## 分享这篇文章