# 学习 Ruby on Rails:终极初学者教程

> 原文：<https://www.sitepoint.com/learn-ruby-on-rails-3/>

## Ruby 中的面向对象编程

让我们在本章开始时讨论的理论基础上，来看看 Ruby 对 OOP 的实现。

正如我们已经知道的，基于 OOP 原则的应用程序的结构关注于与对象的交互。这些对象通常是真实世界对象的代表，如汽车。当我们给一个物体发信息或问它一个问题时，就发生了与它的互动。如果我们真的有一个名为`kitt`的`Car`对象(我们还没有——还没有),启动汽车可能会像下面这样简单:

```
irb> kitt.start
```

这一小段 Ruby 代码将消息 start 发送给对象`kitt`。使用 OOP 术语，我们可以说这段代码调用了`kitt`对象的`start`方法。

正如我之前提到的，与其他面向对象的编程语言如 Python 和 PHP 不同，在 Ruby 中，一切都是对象。尤其是与 PHP 相比，Ruby 的 OOP 并不像是一种“附加”的事后想法——很明显，它从一开始就打算成为该语言的核心特性，这使得在 Ruby 中使用 OOP 特性成为一种真正的乐趣。

正如我们在上一节中看到的，即使是 Ruby 中最简单的元素(如文字字符串和数字)也是可以向其发送消息的对象。

## 类别和对象

与任何其他 OOP 语言一样，在 Ruby 中，每个对象都属于某个类(例如，`PontiacFirebird`可能是类`Car`的对象)。正如我们在本章开始的讨论中所看到的，一个类可以将某种类型的对象分组，并为这些对象配备通用的功能。这种功能以方法的形式出现，体现在对象存储信息的能力中。例如，`PontiacFirebird`对象可能需要存储它的里程，类`Car`的任何其他对象也是如此。

在 Ruby 中，基于现有类的新对象的实例化是通过向该类发送新消息来完成的。结果是该类的一个新对象。下面几行代码展示了 Ruby 中一个非常基本的类定义——第三行是我们创建刚刚定义的类的一个实例的地方。

```
irb> class Car
irb> end
=> nil
irb> kitt = Car.new
=> #<Car:0x75e54>
```

OOP 的另一个基本原则是封装。根据这一原则，对象应该被视为独立的实体，每个对象负责自己的内部数据和功能。如果我们需要访问一个对象的信息——例如，它的内部变量——我们利用对象的接口，它是对象方法的子集，可供其他对象调用。

Ruby 为对象提供了两个层次的功能——对象层次和类层次——并且它坚持封装的原则！让我们深入了解一下。

## 对象级功能

在对象级，数据存储由实例变量处理(这个名字来源于上面提到的实例化过程)。可以把实例变量看作是附加到对象上的存储容器，但是其他对象不能直接访问它。

要从这些变量中存储或检索数据，另一个对象必须调用该对象的访问器方法。访问器方法能够设置(并获取)对象实例变量的值。

让我们看看实例变量和访问器方法是如何相互关联的，以及它们是如何在 Ruby 中实现的。

**实例变量**

实例变量被绑定到一个对象，并且只包含该对象的值。

再看看我们的汽车例子，许多不同的`Car`对象的里程值可能不同，因为每辆汽车都有不同的里程。因此，里程保存在一个实例变量中。

实例变量可以通过它的前缀来识别:单个“at”符号(`@`)。更重要的是，实例变量甚至不需要声明！只有一个问题:一旦它们确实存在，我们没有任何方法来检索或更改它们。这就是实例方法发挥作用的地方。

**实例方法**

数据存储和检索不是唯一可以绑定到特定对象的功能，功能也可以绑定到对象。我们通过使用特定于对象的实例方法来实现这种绑定。调用实例方法(换句话说，向对象发送包含方法名的消息)将只调用接收对象的功能。

实例方法是使用 def 关键字定义的，并以 end 关键字结束。在新的 Ruby shell 中输入以下示例:

```
$ irb
irb> class Car
irb>   def open_trunk
irb>     # code to open trunk goes here
irb>   end
irb> end
=> nil
irb> kitt = Car.new
=> #<Car:0x75e54>
```

您所做的是定义一个名为`Car`的类，它有一个名为`open_trunk`的实例方法。从这个类实例化的`Car`对象将(可能使用一些连接到我们的 Ruby 程序的奇特机器人)在它的`open_trunk`方法被调用时打开它的主干。(暂时忽略那个`nil`返回值；我们将在下一节研究零值。)

## 缩进您的代码

虽然代码的缩进是 Python 等语言语法的关键元素，但在 Ruby 中，缩进纯粹是装饰性的——它有助于可读性，但不会以任何方式影响代码。事实上，当我们在试验 Ruby shell 时，您不必太担心缩进任何代码。但是，当我们保存文件以便以后编辑时，您会希望通过缩进嵌套行来获得可读性好处。

Ruby 社区已经就缩进代码块(如类或方法定义)的最佳空间达成一致。我们将在整本书中坚持这种缩进模式。

有了我们的类，我们可以使用这个方法:

```
irb> kitt.open_trunk
=> nil
```

因为我们不希望所有汽车的行李箱同时打开，所以我们将该功能作为实例方法提供。

我知道，我知道:我们仍然没有修改任何数据。我们使用访问器方法来完成这项任务。

**访问器方法**

访问器方法是一种特殊类型的实例方法，用于读取或写入实例变量。有两种类型:读者(有时称为“getters”)和作者(或“setters”)。

reader 方法将查看对象内部，获取实例变量的值，并将该值返回给我们。另一方面，writer 方法会查看对象内部，找到一个实例变量，并将传递的值赋给该变量。

让我们添加一些方法来获取和设置我们的`Car`对象的`@mileage`属性。再次退出 Ruby shell，这样我们就可以创建一个全新的`Car`类定义。我们的类定义现在变得有点长了，所以要小心地输入每一行。如果输入错误，退出 shell 并重新开始。

```
$ irb
irb> class Car
irb>   def set_mileage(x)
irb>     @mileage = x
irb>   end
irb>   def get_mileage
irb>     @mileage
irb>   end
irb> end
=> nil
irb> kitt = Car.new
=> #<Car:0x75e54>
```

现在，我们终于可以修改和检索我们的`Car`对象的里程了！

```
irb> kitt.set_mileage(5667)
=> 5667
irb> kitt.get_mileage
=> 5667
```

这还是有点尴尬。如果我们能给我们的访问器方法起一个和它们读写的属性完全一样的名字，那不是很好吗？幸运的是，Ruby 包含了这项任务的简写符号。我们可以重写我们的类定义如下:

```
$ irb
irb> class Car
irb>   def mileage=(x)
irb>     @mileage = x
irb>   end
irb>   def mileage
irb>     @mileage
irb>   end
irb> end
=> nil
irb> kitt = Car.new
=> #<Car:0x75e54>
```

有了这些访问器方法，我们就可以读写实例变量，就好像它可以从对象外部获得一样。

```
irb> kitt.mileage = 6032
=> 6032
irb> kitt.mileage
=> 6032
```

这些访问器方法构成了对象接口的一部分。

## 类别级功能

在类级别，类变量处理数据存储。它们通常用于存储状态信息，或者作为为新对象配置默认值的一种方式。类变量通常设置在类的主体中，可以通过它们的前缀来识别:一个双“at”符号(`@@`)。

首先，在新的 Ruby shell 中输入下面的类定义。

```
$ irb
irb> class Car
irb>   @@number_of_cars = 0
irb>   def initialize
irb>     @@number_of_cars = @@number_of_cars + 1
irb>   end
irb> end
=> nil
```

上面的类`Car`的类定义有一个内部计数器，用于计算已经创建的`Car`对象的总数。使用特殊的实例方法`initialize`，每次实例化一个对象都会自动调用该方法，对于每个新的`Car`对象，该计数器都会递增。

顺便说一下，我们实际上已经使用了一个类方法。你喜欢我偷偷放进去的方式吗？新方法是 Ruby 附带的类方法的一个例子，可用于所有类——无论它们是由您定义的，还是构成 Ruby 标准库的一部分。(Ruby 标准库是一个大型的类集合，包含在每个 Ruby 安装中。这些类提供了广泛的通用功能，如访问网站、日期计算、文件操作等。)

定制类方法通常用于创建具有特殊属性的对象(比如我们的`Car`对象的默认颜色)，或者收集关于类使用的统计数据。

扩展前面的例子，我们可以使用一个名为 count 的类方法来返回`@@number_of_cars`类变量的值。记住，这是一个变量，每创建一个新的`Car`对象，这个变量就会增加。类方法的定义与实例方法相同:使用`def`和`end`关键字。唯一的区别是类方法名以`self`为前缀。在新的 Ruby shell 中输入以下代码:

```
$ irb
irb> class Car
irb>   @@number_of_cars = 0
irb>   def self.count
irb>     @@number_of_cars
irb>   end
irb>   def initialize
irb>     @@number_of_cars+=1
irb>   end
irb> end
=> nil
```

下面的代码实例化了一些新的`Car`对象，然后利用我们的新类方法:

```
irb> kitt = Car.new         # Michael Knight's talking car
=> #<0xba8c>
irb> herbie = Car.new       # The famous VolksWagen love bug!
=> #<0x8cd20>
irb> batmobile = Car.new    # Batman's sleek automobile
=> #<0x872e4>
irb> Car.count
=> 3
```

该方法告诉我们已经创建了三个`Car`类的实例。注意，我们不能在一个对象上调用一个类方法(Ruby 实际上提供了一种方法来调用一个对象上的一些类方法，使用`::`操作符，但是我们现在不担心这个。Rails 透露，我们将在第四章看到`::`操作符的使用。):

```
irb> kitt.count
NoMethodError: undefined method 'count' for #<Car:0x89da0>
```

顾名思义，count class 方法只能用于`Car`类，而不能用于从该类实例化的任何对象。

我偷偷在里面介绍了一些别的东西。你发现了吗？在许多语言中，包括 PHP 和 Java，++和—操作符用于将变量递增 1。Ruby 不支持这种符号；相反，在使用 Ruby 时，我们需要使用`+=`操作符。因此，在类定义中递增计数器的简写符号是:

```
irb>     @@number_of_cars+=1
```

该代码与以下代码相同:

```
irb>     @@number_of_cars = @@number of cars + 1
```

这两行都可以读作“`my_variable`变得等于`my_variable`加 1。”

## 遗产

如果您的应用程序处理的不仅仅是我们到目前为止探索过的平面层次结构，您可能希望构建一个场景，其中一些类从其他类继承。继续以汽车为例，假设我们有一辆名为 Larry 的绿色豪华轿车(给汽车命名可能感觉有点奇怪，但这对本例很重要，所以请耐心等待)。在 Ruby 中，`larry`对象可能来自于`StretchLimo`类，而后者又来自于`Car`类。让我们实现它，看看它是如何工作的:

```
$ irb
irb> class Car
irb>   @@wheels = 4
irb> end
=> nil
irb> class StretchLimo < Car
irb>   @@wheels = 6
irb>   def turn_on_television
irb>     # Invoke code for switching on on-board TV here
irb>   end
irb> end
=> nil
```

现在，如果我们实例化一个类`StretchLimo`的对象，我们将得到一辆不同类型的汽车。标准的`Car`对象没有常规的四个轮子，而是有六个轮子(存储在类变量`@@wheels`中)。它还具有额外的功能，通过一个额外的方法——`turn_on_television`——可以被其他对象调用。

然而，如果我们要实例化一个常规的`Car`对象，汽车将只有四个轮子，并且没有打开车载电视的实例方法。可以把继承看作是一种方式，随着我们沿着继承的道路越走越远，类的功能变得越来越专门化。

如果你正在努力理解 OOP 的所有方面，不要担心——当你阅读这本书时，你会自动习惯它们。不过，您可能会发现回到这一节很有用，尤其是如果您稍后需要关于某个术语的提醒。

## 返回值

收到反馈总是很棒。还记得我们说过的将参数传递给方法吗？不管一个方法是否接受参数，在 Ruby 中调用一个方法总是会得到反馈——它以返回值的形式出现，要么显式返回，要么隐式返回。

要显式返回值，请在方法体中使用`return`语句:

```
irb> def toot_horn
irb>   return "toooot!"
irb> end
=> nil
```

在这种情况下调用`toot_horn`方法会产生以下结果:

```
irb> toot_horn
=> "toooot!"
```

但是，如果没有使用 return 语句，则最后执行的语句的结果将用作返回值。这种行为是 Ruby 独有的:

```
irb> def toot_loud_horn
irb>   "toooot!".upcase
irb> end
=> nil
```

在这种情况下调用`toot_loud_horn`方法会产生:

```
irb> toot_loud_horn
=> "TOOOOT!"
```

## 标准输出

当您需要向应用程序的用户显示输出时，使用`print`和`puts`(“put string”)语句。这两种方法都将传递给它们的参数显示为`String`s；`puts`还在其输出的末尾插入一个回车。因此，在 Ruby 程序中，下面几行:

```
print "The quick "
print "brown fox"
```

会产生这样的输出:

```
The quick brown fox
```

然而，像这样使用`puts`:

```
puts "jumps over"
puts "the lazy dog"
```

会产生这样的输出:

```
jumps over
the lazy dog
```

在这个阶段，你可能想知道为什么我们输入到 Ruby shell 中的所有试错代码片段实际上都产生了输出，因为我们还没有使用`print`或`puts`方法。原因是在显示 irb 提示符之前，irb 会自动将它执行的最后一条语句的返回值写到屏幕上。这意味着在 Ruby shell 中使用 print 或 puts 可能会产生两行输出——应该显示您指定的输出，以及最后执行的命令的返回值，如下例所示:

```
irb> puts "The quick brown fox"
"The quick brown fox"
=> nil
```

这里，`nil`实际上是`puts`语句的返回值。回头看看前面的例子，你会遇到 nil 作为类和方法定义的返回值，你会收到一个十六进制地址，比如`<#Car:0x89da0>`，作为对象定义的返回值。这个十六进制值显示了我们实例化的对象在内存中占据的位置，但幸运的是，我们不需要再为这些令人讨厌的细节费心了。

了解了`print`和`puts`语句后，您应该知道 Rails 应用程序实际上有一种完全不同的显示输出的方法，称为模板。Rails 透露，我们将在第 4 章中讨论模板。

**Go to page:** [1](https://sitepoint.com/learn-ruby-on-rails-1/) | [2](https://sitepoint.com/learn-ruby-on-rails-2/) | [3](https://sitepoint.com/learn-ruby-on-rails-3/) | [4](https://sitepoint.com/learn-ruby-on-rails-4/) | [5](https://sitepoint.com/learn-ruby-on-rails-5/) | [6](https://sitepoint.com/learn-ruby-on-rails-6/) | [7](https://sitepoint.com/learn-ruby-on-rails-7/) | [8](https://sitepoint.com/learn-ruby-on-rails-8/) | [9](https://sitepoint.com/learn-ruby-on-rails-9/) | [10](https://sitepoint.com/learn-ruby-on-rails-10/)

## 分享这篇文章