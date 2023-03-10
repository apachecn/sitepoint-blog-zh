# 新手的常见错误，第一部分

> 原文：<https://www.sitepoint.com/common-trip-ups-new-rubyists-part/>

![Meanness of the red little man](img/4bbe668803464702bb7ac5ddb7cdf467.png)

一旦内在化，Ruby 就是一种相当简单的语言。然而，在这种情况发生之前，许多潜在的皈依者会被它的一些更不寻常的方面拒之门外。本系列希望能够澄清新手在学习 Ruby 时面临的一些困惑。

我们将尝试遵循 RDoc 符号来描述方法:

*   `Array#length`是指`Array`类上的一个**实例方法** `length`。
*   `Array::new`指一个**类的方法**上的`new`类的`Array`。
*   `Math::PI`是指`Math`模块中的一个**常量**T1。

假设是 Ruby 1.9+。

## 实例变量和类变量

在大多数编程语言中，实例变量必须在赋值之前声明。Ruby 正好相反——实例变量根本不能声明。相反，Ruby 中的实例变量**在第一次被赋值**时就存在了。

创建一个实例变量就像获取一个局部变量并在开头加上一个“@”一样简单。

```
class Item
  def initialize(title)
    @title = title
  end
end

item = Item.new('Chia Ruby')
```

在底层，实例变量只是存储在当前实例`self`中的一个变量。假设实例变量可以像 Python 中一样用`self.foo =`赋值可能很有诱惑力。事实上，`self.foo =`会将`foo=` **消息**发送给`self`，Ruby 会尝试调用相应的方法。这只有在方法存在的情况下才有效。

```
class Item
  def initialize(title)
    self.title = title
  end
end

item = Item.new('Chia Ruby') # => undefined method `title='...
```

为了在实例方法定义之外访问对象上的实例变量，需要定义 getter 和 setter 方法。

```
class Item
  def title=(t)
    @title = t
  end

  def title
    @title      
  end
end

item = Item.new
puts item.title.inspect # => nil
item.title = "Chia Ruby"
puts item.title # => "Chia Ruby"
```

这里省略了`#initialize`构造函数，以表明它是可选的。另外，注意**访问一个没有赋值的实例变量并不会引发错误**。`@title`在赋值前被访问时为`nil`。第二部分将演示如何将它用于惰性初始化。

一遍又一遍地定义 getters 和 setters 很快就会过时。幸运的是，Ruby 提供了三个助手方法:

*   `#attr_reader`–定义实例级 getters
*   `#attr_writer`–定义实例级设置器
*   `#attr_accessor`–定义两者

通常，这些助手位于类定义的开头。

```
class Thing
  attr_accessor :foo, :bar
  attr_reader :baz

  def initialize
    @baz = "cat"
  end
end

thing = Thing.new
thing.foo = 1
thing.bar = 2
puts thing.baz # => "cat"
```

实例变量只是在`self`中定义的变量，这一事实的结果是**实例变量也在类级别**工作。毕竟类只是`Class`的实例。这些通常被称为**类实例变量**。

```
class Parent
  @value = 1
  def self.value
    @value
  end
end

class Child < Parent
  @value = 2
end

puts Parent.value # => 1
puts Child.value  # => 2
```

除了实例变量，Ruby 还通过使用`@@`而不是`@`拥有所谓的“类变量”。不幸的是，由于它们取代了他们的后代/祖先的价值观，所以不被认可。出于这个原因，最好把它们看作是**阶级等级变量**。

```
class Parent
  @@value = 1
  def self.value
    @@value
  end
end

class Child < Parent
  @@value = 2
end

puts Parent.value #=> 2 (because Child overwrote @@value)
```

实际上，比那要糟糕得多。想想当一个类层次结构变量在顶层赋值时会发生什么，其中`self`是`main`—`Object`的一个实例。

```
@@value = 3
puts Parent.value # => 3
```

因为基本上每一个 Ruby 对象都是从`Object`继承而来的，所以它可以访问和`Object`相同的类层次结构变量。所以，类变量是潜在的**全局变量**。这使得它们高度不可预测并且容易被误用。

## 模块

在 Ruby 中，**模块**是方法的容器。另一方面，**类**是一种特殊的模块，它能够创建实例并拥有祖先。

```
module MyModule
  def hello
    puts "hello from instance"
  end
end

class MyClass
  def hello
    puts "hello from instance"
  end
end

instance = MyClass.new
instance.hello # => "hello from instance"
```

模块中的方法可以是实例方法或类方法——至少在语义上是这样。 **Ruby 类方法只是类对象上的实例方法**。这有点违背直觉，但是模块是类`Module`的实例…即使类是模块的种类。

```
module MyModule
  # Instance method defined on MyModule which is an instance of Module
  def MyModule.hello
    puts 'hello from module'  
  end

  # Same as "def MyModule.hello" because self is MyModule here
  def self.hello
    puts 'hello from module'
  end

  # Instance method for an instance of a class mixing-in MyModule
  def hello
    puts 'hello from instance'    
  end
end

MyModule.hello # => "hello from module"
```

模块没有实例工厂，不代表不能有数据。因为它们是`Module`的实例，所以它们可以有实例变量。

```
module Fooable
  def self.foo=(value)
    @foo = value
  end

  def self.foo
    @foo
  end
end

Fooable.foo = "baz"
puts Fooable.foo # => "baz"
```

在这一点上，一个看似矛盾的地方是模块中可以有实例方法，尽管模块不能创建实例。原来，模块的主要用途是以 **mixins** 的形式出现，其中模块的方法被合并到类中。模块的实例方法将在类的实例中使用。

### 混合蛋白

将一个模块混合到一个类中有两种方法:

1.  **# include**–添加模块的方法作为实例方法
2.  **# extend**–添加模块的方法作为类方法

示例:

```
module Helloable
  def hello
    puts "Hello World"
  end
end

class IncludeClass
  include Helloable
end

class ExtendClass
  extend Helloable
end

IncludeClass.new.hello
ExtendClass.hello
```

有时将类和实例方法混合到类中是可取的。显而易见的方法是将类方法放在一个模块中，将实例方法放在另一个模块中，尽管这种方法有些多余。然后，可以包含实例模块，然后可以扩展类模块。

与其这样，不如使用一点元编程的魔力。钩子方法检测一个模块何时被包含在一个类中。当模块被包含时，类可以**扩展包含类级方法的内部模块**(通常称为`ClassMethods`)。

```
module Helloable

  # Gets called on 'include Helloable'
  def self.included(klass)
    # 'base' often used instead of 'klass'
    klass.extend(ClassMethods)
  end

  # Method that will become an instance method
  def hello
    puts "hello from instance"
  end

  # Methods that will become class methods
  module ClassMethods
    def hello
      puts "hello from class"
    end
  end

end

class HelloClass
  include Helloable
end

HelloClass.hello # => "hello from class"
HelloClass.new.hello # => "hello from instance"
```

演示 mixins 效用的一个快速方法是使用`Comparable`模块。它根据组合比较运算符`<=>`方法的返回值定义比较运算符方法。让我们创建一个类`StringFraction`，它提供字符串中分数之间的适当比较。

```
class StringFraction
  include Comparable

  def initialize(fraction)
    @fraction = fraction  
  end

  def rational
    @fraction.to_r
  end

  def <=>(other)
    self.rational <=> other.rational
  end
end

f1 = StringFraction.new("1/2")
f2 = StringFraction.new("1/3")

puts f1 > f2  # => true
```

### 模块陷阱

**1** 。如果一个模块被包含两次，第二次包含将被忽略。

```
module HelloModule
  def say
    "hello from module"
  end
end

module GoodbyeModule
  def say
    "goodbye from module"
  end
end

class MyClass
  include HelloModule
  include GoodbyeModule
  include HelloModule
end

MyClass.new.say # => "goodbye from module"
```

**2** 。如果两个模块定义了相同的方法，则使用要包含的第二个方法。

```
module Module1
  def hello
    "hello from module 1"
  end
end

module Module2
  def hello
    "hello from module 2"
  end
end

class HelloClass
  include Module1
  include Module2
end

HelloClass.new.hello # => "hello from module 2"
```

**3** 。模块方法不能替换已经在类中定义的方法。

```
module HelloModule
  def hello
    'hello from module'
  end
end

class HelloClass
  def hello
    'hello from class'
  end

  include HelloModule
end

HelloClass.new.hello # => 'hello from class'
```

## 标志

在其他语言(可能还有 Ruby)中，您可能见过像这样作为名称使用的常量:

```
NORTH = 0
SOUTH = 1
EAST = 2
WEST = 3
```

在 Ruby 之外，用作名称而不是它们的值的常量被称为**枚举器**(不要与 Ruby 的`Enumerator`混淆)。通常，有一种更简洁的方法可以做到这一点，比如在**枚举类型**(这里是 Java):

```
public enum Direction {
  NORTH, SOUTH, EAST, WEST 
}
```

Ruby 有比枚举类型更好的东西:**符号**。符号是以`:`开头的任何引用，包括像`:name`、`:@foo`或`:+`这样的词。这种灵活性非常重要，因为符号被用来表示方法名、实例变量和常量等。

从实用的角度来看，符号——像枚举器一样——基本上是快速的、不可变的字符串。然而，**与枚举器不同，符号不需要手动创建**。如果你需要一个符号存在，就像它已经存在一样使用它。下面是如何创建一个将一些符号作为关键字的散列:

```
worker = {
  :name => "John Doe",
  :age => "35",
  :job => "Basically Programmer"
}

puts worker[:name] # => "John Doe"
```

一个潜在的陷阱是动态创建符号，尤其是基于用户输入的符号。这不是一个好主意，因为**符号不是垃圾收集的**。一旦创建，它们就存在，直到程序退出。当用户间接负责创建符号时，这就成了一个安全问题。恶意用户可能会消耗大量永远不会被垃圾收集的内存，从而可能导致虚拟机崩溃。

## 块、进程和 Lambdas

Ruby 方法调用后面可以跟有包含任意代码的`{` / `}`或`do` / `end`标记对。该任意代码可以接收代码中引用的管道括起的(例如:|i，j|)参数。

```
[1,2,3].each { |i| print i } # => '123'
```

这些封闭标记之间的代码称为块。**块是附加在方法调用**上的一段代码。这里，对于数组中的`#each`项，执行该块。在这种情况下，它只是将项目打印到标准输出。

对于 array 新手来说，不明显的是**数组中的每一项是如何**进入程序块的。理解这一点的一个好方法是写我们自己的`#each`。

```
class Colors
  def self.each
    yield 'Red'
    yield 'Green'
    yield 'Blue'
  end
end

Colors.each { |color| print color } # => 'RedGreenBlue'
```

关键字`yield`执行传递了参数的块。换句话说，**一个块中的参数(例如:|i|)来自对该块附加到**的方法内部的`yield`的调用。如果你想遍历一个集合，你只需要`yield`其中的每一项。

但是，如果在没有块的情况下调用该方法，会发生什么呢？

```
Colors.each #=> ...no block given (yield) (LocalJumpError)...
```

结果是`yield`将试图执行这个块，而不管是否真的有一个。如果我们想要一个灵活的方法，只有在提供了一个块的情况下才屈服于块，Ruby 提供了`#block_given?`。

```
class Colors
  @colors = ['Red', 'Green', 'Blue']
  def self.each
    if block_given?
      # send each color to the block if there is one
      # #each returns the array
      @colors.each { |color| yield color }
    else
      # otherwise just return the array
      @colors
    end
  end
end
```

### 存储块

对于来自 JavaScript **这样的语言的人来说，块有点像函数调用**中定义的匿名函数(注意每个调用只能有一个)。JavaScript 主义者也将习惯于 JavaScript 函数可以存储在变量中并传递给其他函数的事实。这些被称为**一级功能**。严格来说， **Ruby 没有一级函数**，但是它的块可以存储在可调用对象中。

Ruby 提供了两个存储块的容器: **procs** 和 **lambdas** 。这些容器可以通过多种方式创建:

```
time_proc = proc { Time.now }
time_lambda = lambda { Time.now }

# The popular, Ruby 1.8 compatible way to create a proc
old_proc = Proc.new { |foo| foo.upcase }

# Shorthand to create a lambda
stabby_lambda = ->(a, b) do
  a + b
end

# Turning a block passed to a method into a Proc
def a_method(&block)
  block
end
```

所有可调用对象都用`#call`执行。

```
add = lambda { |a, b| a + b }
puts add.call(1,2) # => 3
```

Procs 和 lambdas 实际上都是`Proc`对象。但是，至少要记住两个细微差别:

1.  他们对待关键字`return`的方式不同。**过程中的显式返回从定义过程的上下文中返回**。lambda 中的显式返回只是从 lambda 返回。
2.  检查参数的数量。过程将`nil`分配给缺失的参数。

第一点值得特别注意，因为它会导致不透明的错误。

```
return_proc = proc { return }
return_lambda = lambda { return }

def first(p)
  second(p)
end

def second(p)
  p.call
end

first(return_lambda)
first(return_proc) # => LocalJumpError
```

lambda 执行起来没有任何问题。然而，当我们试图调用 proc 时，我们得到了一个`LocalJumpError`。这是因为 proc 是在顶级定义的。绕过 proc/block 返回问题的最简单方法是**避免显式返回**。相反，利用 Ruby 的隐式返回。

**注意:在 Ruby 1.8 中，`proc`创建了检查参数数量的过程，比如 lambda，而`Proc.new`创建了现在所谓的过程。在 Ruby 1.9+中，`proc`被修复为与`Proc.new`行为一致。当使用为 1.8 编写的代码时，请记住这一点。**

## 局部变量范围

Ruby 有两个**局部范围障碍**——局部变量和参数不能通过的点:

1.  模块定义
2.  方法定义

因为类是模块，所以有三个关键词需要寻找:**模块**、**类**和**定义**。

```
lvar = 'x'

def print_local
  puts lvar
end

print_local #=> NameError: undefined local variable or method...
```

那么，如何解决范围障碍呢？原来，**块继承了它们在**中定义的范围。我们可以利用这一点，通过使用采用阻塞的替代方法来传递局部变量。

```
lvar = 'x'

MyClass = Class.new do
  define_method :print_local do
    puts lvar
  end
end

MyClass.new.print_local #=> 'x'
```

因为每个嵌套块都包含较高块的作用域，所以最深的块包含带有局部变量的作用域。以这种方式使用嵌套块来提供对局部变量的访问通常被称为**拉平作用域**。

## 摘要

*   **实例变量**是前缀为“@”的引用，属于当前实例`self`。
*   可以在任何地方创建实例变量，包括模块。
*   未赋值的实例变量的值为`nil`
*   Ruby 也有**类变量**，但是它们几乎是全局变量，不应该使用。
*   模块是方法的容器。
*   类是恰好也是实例工厂并且有祖先的模块。
*   Ruby 没有多重继承，但是多个模块可以“混合”到类中。
*   在 Ruby 中所有的方法都是实例方法。类方法是类对象上的实例方法。
*   **块**是附加到方法调用上的一段代码，它继承了定义它的作用域。
*   块中的参数(例如:|i|)来自方法内部对`yield`的调用。
*   块可以存储在**进程**或**进程**中。
*   过程就像便携块。Lambdas 就像是可移植的方法。
*   **符号**是 Ruby 对枚举类型的回答。
*   符号不是垃圾回收的，所以当动态生成时，它们可能是一个安全问题。
*   局部变量不能跨越模块和方法定义的范围障碍。
*   块可以用来携带局部变量越过范围障碍。

接下来，在第二部分中，我们将探索 Ruby 语言的更多内部工作原理。

## 分享这篇文章