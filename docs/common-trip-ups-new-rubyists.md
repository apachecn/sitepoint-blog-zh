# 新 ruby 爱好者更常见的失误

> 原文：<https://www.sitepoint.com/common-trip-ups-new-rubyists/>

![Fotolia_50633248_Subscription_XXL-272x300](img/c915a882d7f266186122c329e26e3195.png)

## 自我和其他对象

在我的[上一篇关于出错的文章](https://www.sitepoint.com/common-trip-ups-new-rubyists-part/)中，我们看到类级方法是这样定义的:

```
class MyClass
  def self.hello
    "hello"
  end
end

MyClass.hello # => "hello"
```

来自其他语言的开发人员可能会认为`def self.method_name`是某种创建类方法的特殊语法。然而，这也是可能的:

```
class MyClass
  def MyClass.hello
    "hello"
  end
end

MyClass.hello # => "hello"
```

而且**更重要的是**，这也是可能的:

```
MyClass = Class.new
def MyClass.hello
  "hello"
end

MyClass.hello # => "hello"
```

所以，你可以看到 Ruby 允许你在类上定义方法。但是类只是类`Class`的对象，所以逻辑上你应该能够在其他类型的对象上定义方法，比如一个字符串。

```
a_string = "blerp"
def a_string.hello
  "hello"
end

a_string.hello # => "hello"
```

事实证明，`def self.whatever`并不是特殊的语法。 **Ruby 允许你在对象**上定义方法，包括类，以及某种名为`self`的对象。

但是什么是`self`？

对于任何一行 Ruby 代码，都有一个名为`self`的引用。在代码中总是引用当前的对象，这很重要，因为 Ruby 的许多其他方面都依赖于它。例如，实例变量只是存储在`self`中的变量。

```
# At the top level, self is a special object called "main"
self # => main
self.class # => Object

# class definitions change the value of self
class SelfClass
  self # => SelfClass

  # Will be called on instances
  def self
    self
  end

  # Same as SelfClass.self
  def self.self
    self
  end
end

SelfClass.self # => SelfClass
SelfClass.new.self # => Instance of SelfClass
```

所以**谁在乎**，对吧？嗯，事实上`self`总是有一些有趣的结果:

*   实例变量可以在任何地方被赋值和解析(尽管这在语义上可能没有意义，但在语法上总是可能的)
*   方法可以在任何地方定义

这与其他语言明显不同，在其他语言中，某些事情在哪里发生有严格的规则。

## 惰性初始化

习惯于要求在类中声明实例变量的语言的开发人员可能会尝试在构造函数中“定义”实例变量，如下所示:

```
class Item
  attr_accessor :title

  def initialize
    @title = ""
  end
end
```

这种方式会带来一些问题:

1.  子类可能需要担心调用`super`。
2.  不管对象是否被使用，它们都会被分配。
3.  仅仅通过观察来判断一个 getter 将要返回什么是不可能的。

第 1 部分展示了一个未赋值的实例变量返回`nil`而不是引发一个错误。这可以与`||=`操作符结合使用，在没有构造函数的情况下设置默认值。只有当左边的操作数是 **falsy** ，即`nil`或`false`时，它才执行赋值。

```
class Item
  attr_writer :title

  def title
    @title ||= ""
  end
end
```

现在，很明显，当一个`Item`实例接收到`#title`消息时，它要么返回一个空字符串，要么返回最近分配的标题。更好的是，如果我们不关心一个条目的标题是什么，它的内存永远不会被分配。

## 范围解析运算符(::)

当您的代码中有一个模块或类与您想要使用的核心 Ruby 模块同名时，就会出现问题。

```
module MyModule
  class File
  end

  class Thing
    def exist?(path)
      File.exist?(path)
    end
  end
end

thing = MyModule::Thing.new
thing.exist?('/etc/hosts') # => udefined method `exist?' for MyModule::File:Class
```

解决这个问题最简单的方法是在碰撞常数的开头添加`::`操作符。

```
module MyModule
  class File
  end

  class Thing
    def exist?(path)
      ::File.exist?(path)
    end
  end

end

thing = MyModule::Thing.new
thing.exist?('/etc/hosts') # => true
```

## 一切都是运行时间

从历史上看，语言区分了声明的内容和运行的内容。例如，在 C++中，类是在编译时声明的，实例是在运行时创建和使用的。这可能会令人沮丧，因为编译时代码可能会受到缺乏信息访问的限制。

在 Ruby 的世界里，一切都是运行时的。

```
class Hello
  puts "Hello World"
end
```

这个例子打印了字符串，因为 Ruby 类的“声明”只是像在其他地方一样运行的代码。这打开了一扇通向各种可能性的窗户——不仅仅是**编写代码来编写代码**。当代码可以改变应用程序本身时，没有必要为你的应用程序准备一个特殊的插件容器。

例如，有时动态定义方法可能很有用:

```
print "What noise does the animal make?: "
speak_noise = gets

Animal = Class.new do
  define_method :speak do
    puts "Animal: #{speak_noise}"
  end
end

Animal.new.speak
```

也可以动态继承(大多是为了打动朋友，但是谁知道呢？)

```
class RandomClass < [Array, Hash].sample
end

RandomClass.superclass # => either Array or Hash
```

## 内部 DSL

特定于领域的语言使开发人员能够编写适合特定情况的优雅代码。DSL 的例子包括 RSpec、黄瓜、对虾和[鞋](https://www.sitepoint.com/gui-applications-shoes/)。

DSL 有两种类型:

1.  内部–基于主机语言的子集
2.  外部——使用成熟的解析器

由于 Ruby 是一种灵活的、无语法干扰的语言，它非常适合开发内部 DSL。这些 DSL 实际上只是 Ruby，但是是为特定领域定制的。

那么，一个人如何着手创造这些东西呢？

在第 1 部分中，Ruby 模块被展示为可以混合到类中的方法包。如果你检查一个类的`#ancestors`，你会看到任何已经被包含的模块。

看看`Object`的祖先，你会注意到一个古怪的模块:

```
Object.ancestors #=> [Object, PP::ObjectMixin, Kernel, BasicObject]
```

Ruby 中的每个对象都附带了 [`Kernel`](http://ruby-doc.org/core-2.1.0/Kernel.html) 模块。`Kernel`包含类似于`#puts`和`#lambda`的方法。在 Ruby 中创建 DSL 的一种方式是在内核中添加方法，这样它们就可以在顶层工作。

```
module Kernel
  def task(name, &block)
    tasks << block
  end

  def tasks
    @tasks || @tasks = []
  end

  def run_task
    tasks.each { |t| t.call }
  end
end
```

现在，在使用 DSL 的文件中，我们可以运行:

```
task :hello do
  puts "Hello world"
end

task :goodbye do
  puts "Later world"
end

run_tasks
```

当然，`run_tasks`是多余的，在现实世界的 DSL 中可能会被称为 underneath the hood。

## `instance_eval`和`class_eval`

如果在`Kernel`中定义方法是制造它们的唯一方法，那么 DSL 将会非常有限。好在 Ruby 提供了`#instance_eval`。当提供块或字符串时，将在实例的上下文中计算内容。

```
a_string = "word"
a_string.instance_eval("reverse") # => "drow"
```

让我们为运行 DSL 的任务制作一个更便携的版本。当以这种方式使用`#instance_eval`时，定义的方法形成了 DSL 的关键字。我们就做一个 DSL 关键词:`task`。

```
module TaskList
  # Make every instance method a class method
  # Shorter than putting "self" in front of every method
  extend self

  def run(&block)
    instance_eval(&block)
    process_tasks
  end

  private

  def task(name, &block)
    # Leaving the "&" off converts it to a storeable proc
    tasks << block
  end

  def tasks
    @tasks || @tasks = []
  end

  def process_tasks
    tasks.each { |t| t.call }
  end
end

TaskList.run do
  task :hello do
    puts "Hello"
  end

  task :goodbye do
    puts "Goodbye"
  end
end
```

那个版本在一个模块上使用了`#instance_eval`。不要让这迷惑了你——模块确实是类`Class`的实例。下面是典型意义上的版本中的**实例(即不是`Class`的类的实例):**

```
class TaskList
  def run(&block)
    instance_eval(&block)
    process_tasks
  end

  private

  def task(name, &block)
    tasks << block
  end

  def tasks
    @tasks || @tasks = []
  end

  def process_tasks
    tasks.each { |t| t.call }
  end
end

TaskList.new.run do
  task :hello do
    puts "Hello"
  end

  . . .
end
```

与`#instance_eval`不同，`#module_eval` / `#class_eval`分别只作用于模块和类。在`#class_eval`块中定义的实例方法将为该类的所有对象定义。这对猴子修补核心类很有用。

```
String.class_eval do
  def spaceify
    self.split("").join(" ")
  end
end

"hello".spaceify # => "h e l l o"
```

## 隐私

假设您有一个包含一些实例变量和私有方法的类，如下所示:

```
class Secret
  def initialize
    @hidden = 123
  end

  private

  def hidden_method
    "You can't call me!"
  end
end
```

由于实例变量需要从类外操作方法，所以它们是**私有的**，对吗？嗯…

```
secret = Secret.new
secret.instance_eval { @hidden } # => 123
secret.instance_eval { hidden_method } # => "You can't call me!"
```

好吧，但是即使有了`#instance_eval`，也需要有人知道实例变量和方法的**名**，对吗？

```
secret.instance_variables # => [:@hidden]
secret.private_methods # => [:initialize, :hidden_method]
```

查看 Ruby 对象的内部并了解它拥有什么以及它能做什么是非常容易的。这被称为**反射**。

```
secret.hidden_method # => NoMethodError: private method `hidden_method'...
```

在对象上调用私有方法不起作用。但是如果我们**向对象**发送一条消息呢？

```
secret.send(:hidden_method) # => "This method is private - you can't call me!"
```

Ruby 中私有方法的实际工作方式非常简单。**私有方法只是不能用显式接收者**调用的方法。这意味着私有方法不能用显式接收者**调用，即使显式接收者是`self`** 。

```
class Secret
  def initialize
    self.private_method
  end

  private

  def private_method
    "You can't call me!"
  end
end

secret = Secret.new # => private method `private_method' called for...
```

因此，Ruby 中的封装基本上是在荣誉系统上工作的。没有真正的隐私，只有伪隐私。这是否意味着打破封装是可以接受的？嗯，不。如果一个开发者做了一些私有的东西，那可能是有很好的理由的。尽管如此，您还是会经常看到它，并且知道为什么开发人员可能会向对象发送消息而不仅仅是调用方法是很好的。

## 晦涩的方法

许多编程语言依赖于类是什么和它能做什么之间的紧密联系。在 Ruby 中，这种关系破裂了。与其关心一个对象来自哪里，更重要的是考虑它能接收什么样的**消息**。

在 Ruby 中，如果一个对象知道如何处理我们发送给它的消息，那么它可能就是正确的对象。这种思维方式通常被称为[鸭式打字](https://www.sitepoint.com/making-ruby-quack-why-we-love-duck-typing/)。

例如，这可能不是好的 Ruby 代码:

```
if user.is_a? EmailUser
  "Email:" #{user.email}"
end
```

尽管这种类型检查可以提高我们代码的健壮性，但是它们完全破坏了 duck 类型化的解耦优势。相反，最好问一问一个类是否能对你给它的消息做出回应。

```
if user.respond_to?(:email)
  "Email: #{user.email}"
end
```

到目前为止，没什么太奇怪的。然而，这是事情发生转折的地方。因为对象接收消息是为了调用方法，所以当接收到消息时可以进行干预。这允许调用甚至不存在的方法。这种能力支持开发灵活的类，无需修改一行代码就可以包含新的特性(例如，web API 的客户端)。

为了了解我们如何做到这一点，让我们看看当一个对象接收到一条消息时 Ruby 采取的步骤:

1.  方法是在接收方的类中定义的还是在它的祖先中的任何地方定义的？
2.  `#method_missing`是在接收者的类中定义的还是在它的祖先中的任何地方定义的？
3.  升起`NoMethodError`

所以，要处理未定义的方法，只需定义`#method_missing`。

```
class MethodHandler
  def method_missing(method_name, *args)
    puts "Handling instance method #{method_name}"
    puts "The arguments were: #{args.join(', ')}"
  end
end

handler = MethodHandler.new
handler.iammadeup!(1, 2, "cat")
```

一种常见的模式是对照正则表达式检查方法名，否则通过调用`super`来引发`NoMethodError`(除非父类已经定义了`method_missing`)。注意，方法名是一个符号，而不是一个字符串。创建正则表达式时记住这一点很重要，因为符号是以冒号开头的。一些开发人员更愿意称之为`method_sym`来更好的描述。

不幸的是，即使`#method_missing`响应了一个方法，也不意味着`#respond_to?`或`#method`会响应。因此，在定义`#method_missing`时，一定要定义`#respond_to_missing`，这一点很重要。

Thoughtbot 在这个主题上有一个很好的帖子。

## 单例类/特征类

很难解释接下来会发生什么。我认为最好用几句话来介绍:

1.  方法实际上是在类上定义的，而不是在对象上。
2.  任何 Ruby 对象都可以有自己的方法。

听起来矛盾？让我们用一些代码来证明第二种说法。

```
word = "Hello"
def word.double
  self * 2
end
puts word.double # => "HelloHello"
```

对于 Ruby 来说，这两种说法都是正确的。然而——怎么做？如果方法存在于类中，那么一个对象怎么会有没有为它的类或祖先定义的方法呢？肯定有…另一个班。在某个地方，一定有一个隐藏的类，这样的方法存在于其中。

```
puts word.method(:length).owner # => "String"
puts word.method(:double).owner # => "#<Class:#<String:0x00000001bf4680>>"
```

事实也确实如此。

最好不要为这个隐藏类附加任何特定的名称，因为多年来已经使用了几个名称:元类、特征类、幽灵类、单例类、匿名类。官方上，Ruby 已经在 1.9.2 中采用了`#singleton_class`。请注意，单词“singleton”不是在 singleton 模式的意义上使用的。由于这可能会引起混淆，从现在开始，本文将使用术语“特征类”。

在对象的特征类中定义事物的标准方法是使用

```
class << [some object]
```

符号。

```
letters = ['a', 'b', 'c']

class << letters
  def capitalize
    self.map do |letter|
      letter.upcase
    end
  end
end

letters.capitalize #=> ['A', 'B', 'C']
```

前面我们看到私有类方法并不那么私有。这可以通过定义特征类的方法来解决。

```
class Secretive
  class << self
    private

    def hidden
      "This method is private - You can't call me!"
    end
  end
end

Secretive.hidden #=> private method `hidden' called for Secretive:Class (NoMethodError)
```

## 制作类似 attr_accessor 的方法

在第 1 部分中，我们看到了 Ruby 如何让您用助手方法`attr_reader`、`attr_writer`和`attr_accessor`定义 getters 和 setters。

```
class Foo
  attr_accessor :bar, :baz
end
```

如果我们想让你自己的 helper 方法像这样呢？定义一个类级别的方法是有意义的，因为`self`是类定义中的类。

```
class Foo
  def self.attr_printer(*properties)
    puts "attr_printer called with #{properties}"
  end

  attr_printer :bar, :baz
end
```

这是可行的，但是在每个使用`attr_printer`的类中定义方法违背了初衷。我们需要找到另一个地方来定义它，它将适用于所有的职业。

```
class Class
  def attr_printer(*properties)
    puts "attr_printer called with #{properties}"
  end
end

class Something
  attr_printer :attr_one, :attr_two, :attr_three
end
```

现在所有的类都有了新的方法，但是我们可能也不希望这样。相反，最好将代码放在一个模块中，该模块可以单独包含在类中，尤其是在有不同的 helper 方法实现的情况下。这是我们再次利用本征类的机会。

```
module AttrPrinterable
  def self.included(klass)
    class << klass
      def attr_printer(*properties)
        puts "attr_printer called with #{properties}"
      end
    end
  end
end

class Something
  include AttrPrinterable
  attr_printer :attr_one, :attr_two, :attr_three
end
```

或者，您可以在`Class`实例上使用`#instance_eval`。注意使用的是`def attr_printer`而不是`def self.attr_printer`。

```
module AttrPrinterable
  def self.included(klass)
    klass.instance_eval do
      def attr_printer(*properties)
        puts "attr_printer called with #{properties}"
      end
    end
  end
end
```

## 摘要

*   `self`始终是当前对象。
*   与其像在静态语言中那样在构造函数中声明实例变量，有时利用惰性初始化方法会更好。
*   `#instance_eval`在实例的上下文中运行块或字符串，包括`Class`的实例
*   使用`#class_eval`为一个类的所有实例定义方法。
*   在 Ruby 中，编译时代码和运行时代码没有区别。
*   在 Ruby 中，常量是以大写字母开头的任何单词。
*   常量主要用于类名和模块名，而不是像其他语言那样作为枚举。
*   范围解析操作符`::`用于指定常量
*   将`::`放在常数的前面，指定一个根级常数。
*   私有方法是不能有显式接收者的方法，包括`self`。
*   对象接收**消息**来调用方法。
*   `.`操作符是`#send`向对象发送消息的简写
*   当一个对象不知道如何回应信息时，可以介入。
*   在定义`#method_missing`的时候，定义`#respond_to_missing`很重要。
*   一个**特征类**或**单例类**是一个对象链接到的唯一类。
*   语法可以用来打开一个对象的特征类，包括一个类对象。
*   类方法不能是私有的，但是在类上定义的特征类方法可以。
*   像`attr_accessor`这样的方法可以定义为类的类级方法或者`Class`的实例级方法。

## 分享这篇文章