# 看看 Ruby 2.1

> 原文：<https://www.sitepoint.com/look-ruby-2-1/>

![ruby2.1_signal](img/1e85f2e0424ecf29bc161f650cb5b8d7.png)

在本文中，我们来看看 Ruby 2.1 的全新特性。它是由 Matz 在 2013 年巴塞罗那 Ruby 大会(BaRuCo)上首次宣布的。我们将关注于假期发布的 Ruby 2.1.0。

希望在本文结束时，您会对 Ruby 2.1 感到非常兴奋！

## 获得 Ruby 2.1

学习和探索各种特性的最佳方式是跟随示例。为此，您需要获得最新的 Ruby 2.1 副本:

如果您正在使用`rvm`:

(您需要运行`rvm get head`来安装 2.1.0 最终版本)

```
$ rvm get head
$ rvm install ruby-2.1.0
$ rvm use ruby-2.1.0
```

或者，如果您正在使用`rbenv`:

```
$ rbenv install 2.1.0
$ rbenv rehash
$ rbenv shell 2.1.0
```

注意，对于`rbenv`用户来说，你可能想在玩完这些例子后再来一次`rbenv shell --unset`——除非你喜欢生活在危险的边缘。或者你可以直接关闭终端窗口。

让我们确保我们都使用相同的版本:

```
$ ruby -v
ruby 2.1.0dev (2013-11-23 trunk 43807) [x86_64-darwin13.0]
```

## 有什么新鲜事吗？

这是我们今天要解决的功能列表。要获得更全面的列表，请看一下 Ruby 2.1.0 的发行说明。

1.  有理数和复数文字
2.  `def`的返回值
3.  风雅
4.  必需的关键字参数
5.  垃圾收集工
6.  对象分配跟踪
7.  异常#原因

## 1.有理数和复数文字

在以前的 Ruby 版本中，处理复数是一件很麻烦的事情:

```
% irb
irb(main):001:0> RUBY_VERSION
=> "2.0.0"
irb(main):002:0> Complex(2, 3)
=> (2+3i)
irb(main):003:0> (2+3i)
SyntaxError: (irb):3: syntax error, unexpected tIDENTIFIER, expecting ')'
(2+3i)
     ^
        from /usr/local/var/rbenv/versions/2.0.0-p247/bin/irb:12:in `<main>'
```

现在，随着`i`后缀的引入:

```
% irb
irb(main):001:0> RUBY_VERSION
=> "2.1.0"
irb(main):002:0> (2+3i)
=> (2+3i)
irb(main):003:0> (2+3i) + Complex(5, 4i)
=> (3+3i)
```

和理性主义者一起工作也更愉快。以前，如果你想处理分数或者使用`Rational`类，你必须使用浮点数。通过为`Rational`类提供简写，`r`后缀改善了这种情况。

因此，代替:

```
irb(main):001:0> 2/3.0 + 5/4.0
=> 1.9166666666666665
```

我们可以这样写:

```
irb(main):002:0> 2/3r + 5/4r
=> (23/12)
```

## 2.`def`的返回值

在 Ruby 以前的版本中，方法定义的返回值一直是`nil`:

```
% irb
irb(main):001:0> RUBY_VERSION
=> "2.0.0"
irb(main):002:0> def foo
irb(main):003:1> end
=> nil
```

在 Ruby 2.1.0 中，方法定义返回一个符号:

```
irb(main):001:0> RUBY_VERSION
=> "2.1.0"
irb(main):002:0> def foo
irb(main):003:1> end
=> :foo
```

这有什么用？到目前为止，我遇到的用例之一是如何定义`private`方法。我一直不喜欢 Ruby 定义私有方法的方式:

```
module Foo
  def public_method
  end

  private
    def a_private_method
    end
end
```

我遇到的问题是，当类变得真的很长时(尽管我们的意图很好)，有时很容易漏掉那个关键字。

有趣的是`private`可以接受一个[符号](http://ruby-doc.org/core-2.1.0/Module.html#method-i-private):

```
module Foo
  def public_method
  end

  def some_other_method
  end

  private :some_other_method

  private
    def a_private_method
    end
end

Foo.private_instance_methods
=> [:some_other_method, :a_private_method]
```

现在，我们可以简单地将`def` *返回*一个符号和`private` *接受*一个符号结合起来:

```
module Foo
  def public_method
  end

  private def some_other_method
  end

  private def a_private_method
  end
end

Foo.private_instance_methods
=> [:some_other_method, :a_private_method]
```

如果你对这个新特性的实现感兴趣，可以看看这个博客[的帖子](http://franck.verrot.fr/blog/2013/08/21/method-definitions-in-ruby-2-1-0-will-not-be-void-anymore/)。

## 3.风雅

Ruby 2.1 中的改进不再是实验性的。如果你是改进的新手，把它比作猴子补丁会有所帮助。在 Ruby 中，所有的类都是开放的。这意味着我们可以愉快地将方法添加到现有的类中。

为了理解这可能造成的破坏，让我们重新定义一下`String#count`(原来的定义是[这里是](http://ruby-doc.org/core-2.1.0/String.html#method-i-count)):

```
class String
  def count
    Float::INFINITY
  end
end
```

如果你将上面的内容粘贴到`irb`，*中，当`count`编辑时，每个*字符串返回`Infinity`:

```
irb(main):001:0> "I <3 Monkey Patching".count
=> Infinity
```

细化提供了另一种方式来*限定*我们修改的范围。让我们做一些稍微有用的东西:

```
module Permalinker
  refine String do
    def permalinkify
      downcase.split.join("-")
    end
  end
end

class Post
  using Permalinker

  def initialize(title)
    @title = title
  end

  def permalink
    @title.permalinkify
  end
end
```

首先，我们定义了一个模块，`Permalinker`用一个新的方法定义了字符串类。该方法实现了一种先进的永久链接算法。

为了使用我们的细化，我们简单地将`using Permalinker`添加到我们的示例`Post`类中。之后，我们可以把`String`类当作有`permalinkify`方法。

让我们来看看实际情况:

```
irb(main):002:0> post = Post.new("Refinements are pretty awesome")
irb(main):002:0> post.permalink
=> "refinements-are-pretty-awesome"
```

为了证明`String#permalinkify`只存在于`Post`类的范围内，让我们尝试在其他地方使用该方法，并观察代码爆炸:

```
irb(main):023:0> "Refinements are not globally scoped".permalinkify
NoMethodError: undefined method `permalinkify' for "Refinements are not globally scoped":String
        from (irb):23
        from /usr/local/var/rbenv/versions/2.1.0/bin/irb:11:in `<main>'
```

## 4.必需的关键字参数

在 Ruby 2.0 中，引入了关键字参数:

```
def permalinkfiy(str, delimiter: "-")
  str.downcase.split.join(delimiter)
end
```

不幸的是，没有办法将`str`标记为*必需的*。这在 Ruby 2.1 中将会改变。为了将参数标记为必需的，只需省略默认值，如下所示:

```
def permalinkify(str:, delimiter: "-")
  str.downcase.split.join(delimiter)
end
```

如果我们填写了所有必需的参数，一切都会如预期的那样工作。然而，如果我们遗漏了一些东西，就会抛出一个`ArgumentError`:

```
irb(main):001:0> permalinkify(str: "Required keyword arguments have arrived!", delimiter: "-lol-")
=> "required-lol-keyword-lol-arguments-lol-have-lol-arrived!"
irb(main):002:0> permalinkify(delimiter: "-lol-")
ArgumentError: missing keyword: str
        from (irb):49
        from /usr/local/var/rbenv/versions/2.1.0/bin/irb:11:in `<main>'
```

## 5.受限世代垃圾收集器(RGenGC)

Ruby 2.1 有一个新的垃圾收集器，它使用了一个[世代垃圾收集](http://en.wikipedia.org/wiki/Garbage_collection_(computer_science)#Generational_GC_.28ephemeral_GC.29)算法。

关键的想法和观察是，最近创建的对象通常死得很早。因此，我们可以根据对象是否在垃圾收集运行中存活下来，将它们分为新对象和旧对象。这样，垃圾收集器可以集中精力释放年轻一代的内存。

如果我们在对年轻一代(次要 GC)进行垃圾收集后耗尽了内存，垃圾收集器将继续对老一代(主要 GC)进行垃圾收集。

在 Ruby 2.1 之前，Ruby 的垃圾收集器运行的是保守的 stop-the-world 标记和清除算法。在 Ruby 2.1 中，我们仍然使用标记和清除算法对年轻/年老代进行垃圾收集。然而，因为我们标记的对象较少，所以标记时间减少了，从而提高了收集器的性能。

然而，也有一些警告。为了保持与 C 扩展的兼容性，Ruby 核心团队不能实现“完全”分代垃圾收集算法。特别是，他们不能实现[移动垃圾收集算法](http://en.wikipedia.org/wiki/Garbage_collection_(computer_science)#Moving_vs._non-moving)——因此是“受限的”。

也就是说，看到 Ruby 核心团队非常认真地对待垃圾收集性能是非常令人鼓舞的。要了解更多细节，请查看 Koichi Sasada 的精彩演讲。

## 6.异常#原因

实现了这个[特性](https://bugs.ruby-lang.org/issues/8257)的 Charles Nutter 对此做了最好的解释:

> 通常，当一个较低级别的 API 引发一个异常时，我们希望重新引发一个特定于我们的 API 或库的不同异常。目前在 Ruby 中，用户只能看到我们的新异常；最初的异常将永远丢失，除非用户决定挖掘我们的库并记录下来。
> 
> 我们需要一种方法让异常带有一个“原因”。

下面是一个关于`Exception#cause`如何工作的例子:

```
class ExceptionalClass
  def exceptional_method
    cause = nil
    begin
      raise "Boom!"" # RuntimeError raised
    rescue => e
      raise StandardError, "Ka-pow!"
    end
  end
end

begin
  ExceptionalClass.new.exceptional_method
rescue Exception => e
  puts "Caught Exception: #{e.message} [#{e.class}]"
  puts "Caused by       : #{e.cause.message} [#{e.cause.class}]"
end
```

这是你将得到的:

```
Caught Exception: Ka-pow! [StandardError]
Caused by       : Boom! [RuntimeError]
```

## 7.对象分配跟踪

如果你有一个臃肿的 Ruby 应用程序，查明问题的确切来源通常不是一件容易的事情。MRI Ruby 仍然没有可以与之匹敌的分析工具，例如， [JRuby profiler](https://github.com/jruby/jruby/wiki/Profiling-Object-Allocations) 。

幸运的是，为 MRI Ruby 提供对象分配跟踪的工作已经开始。

这里有一个例子:

```
require 'objspace'

class Post
  def initialize(title)
    @title = title
  end

  def tags
    %w(ruby programming code).map do |tag|
      tag.upcase
    end
  end
end

ObjectSpace.trace_object_allocations_start
a = Post.new("title")
b = a.tags
ObjectSpace.trace_object_allocations_stop

puts ObjectSpace.allocation_sourcefile(a) # post.rb
puts ObjectSpace.allocation_sourceline(a) # 16
puts ObjectSpace.allocation_class_path(a) # Class
puts ObjectSpace.allocation_method_id(a)  # new

puts ObjectSpace.allocation_sourcefile(b) # post.rb
puts ObjectSpace.allocation_sourceline(b) # 9
puts ObjectSpace.allocation_class_path(b) # Array
puts ObjectSpace.allocation_method_id(b)  # map
```

虽然知道我们可以获得这些信息是很好的，但是它对您(开发人员)的用处并不明显。

进入山姆·罗林斯写的 [`allocation_stats`](https://github.com/srawlins/allocation_stats) 宝石。

让我们安装它:

```
% gem install allocation_stats
Fetching: allocation_stats-0.1.2.gem (100%)
Successfully installed allocation_stats-0.1.2
Parsing documentation for allocation_stats-0.1.2
Installing ri documentation for allocation_stats-0.1.2
Done installing documentation for allocation_stats after 0 seconds
1 gem installed
```

除了这次我们使用`allocation_stats`之外，这里的例子和前面的一样:

```
require 'allocation_stats'

class Post
  def initialize(title)
    @title = title
  end

  def tags
    %w(ruby programming code).map do |tag|
      tag.upcase
    end
  end
end

stats = AllocationStats.trace do
  post = Post.new("title")
  post.tags
end

puts stats.allocations(alias_paths: true).to_text
```

运行这个命令会生成一个格式良好的表:

```
sourcefile  sourceline  class_path  method_id  memsize  class
----------  ----------  ----------  ---------  -------  ------
post.rb             10  String      upcase           0  String
post.rb             10  String      upcase           0  String
post.rb             10  String      upcase           0  String
post.rb              9  Array       map              0  Array
post.rb              9  Post        tags             0  Array
post.rb              9  Post        tags             0  String
post.rb              9  Post        tags             0  String
post.rb              9  Post        tags             0  String
post.rb             17  Class       new              0  Post
post.rb             17                               0  String
```

山姆做了一个精彩的[展示](http://confreaks.com/videos/2870-rubyconf2013-new-ruby-2-1-awesomeness-fine-grained-object-allocation-tracing)，展示了`allocation_stats`宝石的更多细节。

## 节日快乐！

Ruby 2.1 计划在圣诞节当天发布。如果一切顺利，这将是给所有 ruby 爱好者的一份美妙的礼物。我特别兴奋地看到 Ruby 的垃圾收集器的改进，以及更好的分析能力融入到语言中，允许构建更好的分析工具。

编码快乐，节日快乐！

## 分享这篇文章