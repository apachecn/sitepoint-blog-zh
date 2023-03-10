# 代码旅行:推特搜索的 TWSS 和贝叶斯分类

> 原文：<https://www.sitepoint.com/code-safari-twss-and-bayesian-classification-of-twitter-searches/>

也许我最喜欢的 Ruby 用法是作为管道——连接大型库的小脚本，以某种方式做一些有趣的事情。能够在一个晚上迅速写出这些脚本是一项基本技能。

今天我在`twss` gem 中遇到了这样一个例子。

> 因为自动化没有界限……包括低级喜剧。TWSS 是从 Twitter #twss 搜索中训练出来的简单贝叶斯分类器。

正是这种项目赋予了生态系统生命和特性。让我们看看它是如何工作的。

## 查找条目

文档向我们展示了探索代码的潜在切入点，因为唯一公开的公共接口是一个方法:

```
require 'twss'
TWSS("hey, did you resolve that ticket?") # => false
TWSS("not yet, it's taking a while") # => false
TWSS("well hurry up, you're not going fast enough") # => true
```

对于 ruby 代码来说，大写的方法名看起来很奇怪，但是完全有效。通常这样做是为了“重用”已经存在的类或模块名来提供默认行为( [Hpricot](http://hpricot.com/) 是另一个这样做的 gem)。往里面看`lib/twss.rb`(所需的文件通常是一个很好的起点)，我们看到了这个可爱的技巧:你确实可以有一个与模块同名的方法。

```
# lib/twss.rb
module TWSS
  # ...
end

def TWSS(str)
  TWSS.classify(str)
end
```

不过，这分散了我们的核心任务。让我们继续这个项目的核心部分。找到`twss`方法委托给的`classify`方法(使用您最喜欢的文件查找工具)，我们解除这个管道操作的第一个主要子系统的转换:T2 gem。

```
# lib/twss/engine.rb
require 'classifier'

def initialize(options = {})
  @data_file = options[:data_file] || DATA_FILE
  @threshold ||= options[:threshold] || 5.0
  @classifier = load_classifier_from_file!(@data_file) || new_classifier
end

def classify(str)
  if basic_conditions_met?(str)
    c = @classifier.classifications(str)
    c[TRUE] - c[FALSE] > threshold
  else
    false
  end
end

def new_classifier
  Classifier::Bayes.new(TRUE, FALSE)
end
```

来自创业板的[首页](http://classifier.rubyforge.org/):

> 分类器是一个通用模块，允许贝叶斯和
> 其他类型的分类。

这是相当简单的代码。自己没有实现任何分类逻辑，而是委托给一个现有的框架来完成繁重的工作。这是一种强大的技术，应该尽可能经常使用。最好的程序员知道如何让别人为他们编程。

然而这只是等式的一半。鉴于`twss`为 classier 提供了一个预先训练好的数据文件，我们首先需要找到它是如何创建这个文件的。这段代码驻留在哪里是相当明显的:我们没有看到的另外两个代码文件名为`trainer.rb`和`tweet_collector.rb`。

## 获取数据

先说 Twitter。能够收集和处理推文是你工具箱中特别方便的技术。

```
# lib/twss/tweet_collector.rb
require 'twitter'

# ...

def run
  o = File.open(filename, 'a')
  page, per_page = 1, 100
  begin
    Twitter::Search.new(search).per_page(per_page).page(page).each do |tweet|
      puts tweet.text
      o.puts tweet.text
    end
    page += 1
    sleep 2
  end while page * per_page < limit
  o.close
end
```

这表明我们可以使用一个简单的 API 来进行自己的 Twitter 查询。跳进来，我们可以四处玩玩。

```
$ irb -rtwitter
irb> Twitter::Search.new('#ruby').each {|x| puts x.text }
TypeError: can't convert String into Hash
  from twitter-1.2.0/lib/twitter/api.rb:13:in `merge'
```

…嗯，这很尴尬。看来`twss`代码被破解了。这种事情在快速发展的 ruby 世界中经常发生，第一步是不要惊慌。`twss`的最后一次提交是在 2010 年 8 月，所以很有可能`twitter` gem 从那时起已经改变了它的 API。在一个完美的世界里，`twss`将会指定一个更小范围的版本(通常是一个主要版本和一个次要版本)，但是在这种情况下，我们必须自己做一些研究。

我在 GitHub 中搜索“twitter”来查找 gem 的[源代码库。浏览自述文件，似乎 1.0 版本打破了许多与早期版本的向后兼容性。特别是，这一变化看起来与我们的代码完全相关:](https://github.com/jnunemaker/twitter)

> `Twitter::Search`类基本保持不变，但是
> 它不再接受构造函数中的查询。您可以使用`#containing`方法为
> 指定一个查询，该方法的别名是`#q`。

```
# Pre-1.0
Twitter::Search.new("query").fetch.first.text
# Post-1.0
Twitter::Search.new.q("query").fetch.first.text
```

让我们用这个新信息再次尝试我们的搜索。

```
irb> Twitter::Search.new.q('#ruby').each {|x| puts x.text }
#Ruby 1.9 is fast than #Python 2.7 so much................
The @bronxzooscobra was a big hit, maybe I'll do one for the snake in my house. Maybe #Zeus and #Ruby need their own twitter accounts too.
Amanhã tem curso de #ruby com os mestres @caironoleto e @cleitonfco, acho que seria melhor manerar no café. #not
RT @haacked: What's the gold standard in OSS project documentation? #Ruby #RoR #Python #PHP #Linux #GSoC2011
ANTIQUE RETRO DIAMOND 1.8ct RUBY WIDE RING 1940 size 10.5 #ring #ruby #diamond #gold #antique http://w.sns.ly/BQc0y4
http://www.pulist.net/max-amp-rubys-four-seasons-max-and-ruby.html #max #and #ruby #different Max &amp;amp; Ruby's Four Seasons (Max and Rub
#hippa #pci #security #ruby do you guys have any ideas for #securesmsvoting. ?  Does @americanidol do it?  Let's scale it to the world.
How many people have access to a cell phone for #voting?  In the world? 75%? SecureSMS voting! #ruby #sms #twitter @twitter #g8 #un #egypt
I'm watching @AJEnglish. Can't we fix world troubles with software?  Secure voting for everyone.  @AJListeningPost #sysadmin #ruby #facebook
Wow, #Ruby'sMoney kicks ass!!!
ICMembers -  Firmen-Network http://bit.ly/bjsPkl #Firma #Network #Skript #Software #Mitgliedschaft #Ruby #On #Rails #2.0.2 #MySQL
#Ruby Engagement Rings : Shop online Ruby Engagement Rings for Low Price. Compare Prices on Ruby Engagement Rings. find Sale items and more.
Watchr – More Than An Automated Test Runner http://tinyurl.com/3vsvklm #ruby
All this @antirez talk re #ruby perf has finally made me play w/ sinatra. Very cool, like #mojolicious which I love. Is mojo a #perl clone?
RT @ruby2itter: georgi/rack_dav - GitHub: HTTPGit Read-OnlyThis URL has Read+Write accessDismissOctotip: You've activated ... http://bit.ly/fDDw7o #ruby
```

太美了。我们已经学会了一种从 ruby 代码中获取 tweets 的非常简单的方法。

## 训练分类器

我们有一个 tweets 列表，我们有代码来分类任何给定的字符串，但是我们仍然缺少中间的一步:训练我们的分类器。这本身就是一篇文章，但基本概念是我们需要给分类器一组匹配我们每个类别的数据。然后，分类器可以使用该信息来猜测给定文本属于每个组的可能性有多大。其代码在剩下的文件`trainer.rb`中。

```
# lib/twss/trainer.rb
# ...
puts "Training NON-TWSS strings..."
File.read(File.join(path, 'non_twss.txt')).each_line do |l|
  engine.train(TWSS::Engine::FALSE, strip_tweet(l))
end

puts "Training TWSS strings..."
File.read(File.join(path, 'twss.txt')).each_line do |l|
  engine.train(TWSS::Engine::TRUE, strip_tweet(l))
end
# ...
```

在这种情况下，只有两个类别(假和真，在`engine.rb`中定义为‘0’和‘1’)。我们还没有看到文件`non_twss.txt`和`twss.txt`，但是如果我们搜索它们，我们会在`script/`目录中找到一些代码，它们分别填充了“:)”和“#twss”的 Twitter 搜索结果。

```
# script/collect_twss.rb
require File.join(File.dirname(__FILE__), '../lib/twss')
require File.join(File.dirname(__FILE__), '../lib/twss/tweet_collector')

TWSS::TweetCollector.new('#twss', File.join(File.dirname(__FILE__), '../data/twss.txt')).run
```

搜索“:)”是生成一组已知的非 twss 文本的一种有趣方式，但它似乎足以处理这种类型的数据。

## 把它放在一起

我们获取了一系列带有`#ruby`标签的推文，但其中许多都与珠宝有关，而不是代码。让我们结合我们刚刚学习的两个库——`classifier`和`twitter`——尝试过滤掉与代码无关的推文。最困难的部分将是用“代码”和“珠宝”类别的可靠数据来训练我们的分类器。让我们尝试一下`twss`使用的相同技术作为起点，简单地使用 twitter 搜索“#jewelery”和“#code”。

```
require 'twitter'
require 'classifier'

file_name = "classifier.dump"
categories = %w(jewelery programming)

classifier = Classifier::Bayes.new *categories
categories.each do |category|
  Twitter::Search.new.q("##{category}").per_page(500).each do |x|
    classifier.train(category, x.text)
  end
end

Twitter::Search.new.q("#ruby").per_page(25).each do |x|
  puts x.text
  puts "  => #{classifier.classifications(x.text)}"
  puts "  => #{classifier.classify(x.text)}"
  puts
end
```

我们只用了二十行代码就把 Twitter 和贝叶斯分类器结合起来了。第一次尝试的结果很有希望，尽管显然并不完美:

```
RT @haacked: What's the gold standard in OSS project documentation? #Ruby #RoR #Python #PHP #Linux #GSoC2011
  => {"Jewelery"=>-48.675845247282666, "Programming"=>-46.948563939601286}
  => Programming

ANTIQUE RETRO DIAMOND 1.8ct RUBY WIDE RING 1940 size 10.5 #ring #ruby #diamond #gold #antique http://w.sns.ly/BQc0y4
  => {"Jewelery"=>-52.61580762140005, "Programming"=>-55.656377490626184}
  => Jewelery

How many people have access to a cell phone for #voting?  In the world? 75%? SecureSMS voting! #ruby #sms #twitter @twitter #g8 #un #egypt
  => {"Jewelery"=>-70.68382032808533, "Programming"=>-73.42867953661468}
  => Jewelery

I'm watching @AJEnglish. Can't we fix world troubles with software?  Secure voting for everyone.  @AJListeningPost #sysadmin #ruby #facebook
  => {"Jewelery"=>-70.68382032808533, "Programming"=>-66.52092425763254}
  => Programming

#Ruby Engagement Rings : Shop online Ruby Engagement Rings for Low Price. Compare Prices on Ruby Engagement Rings. find Sale items and more.
  => {"Jewelery"=>-94.997002062587, "Programming"=>-86.66119916641394}
  => Programming

All this @antirez talk re #ruby perf has finally made me play w/ sinatra. Very cool, like #mojolicious which I love. Is mojo a #perl clone?
  => {"Jewelery"=>-107.46543897258468, "Programming"=>-101.50632914155935}
  => Programming
```

从这里，我们可以尝试应用一些数据质量算法`twss`的使用，比如剔除非单词和排除短推文，或者我们可以考虑为训练集改进我们的底层数据。

快速有效地学习和使用新库的能力是一项需要培养的宝贵技能。练习组合这些库，即使是简单的应用程序，也会让你成为更好的程序员。

以下是进一步练习的一些想法:

*   调查我们的 ruby tweet 标识符的其他训练数据集。
*   派生并修补`twss` gem，使其与最新版本的`twitter`兼容。

请在评论中告诉我们你的进展。

## 分享这篇文章