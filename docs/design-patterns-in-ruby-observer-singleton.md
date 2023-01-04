# Ruby 中的设计模式:观察者，单例

> 原文：<https://www.sitepoint.com/design-patterns-in-ruby-observer-singleton/>

我将会发布一些关于软件设计模式以及它们如何适用于 Ruby 的文章。将涉及的前两种模式是**观察者模式**和**单一模式**。

## 观察者模式

如果您不熟悉这种模式，不用担心，它基本上是一种机制，当一个对象的状态改变时，它会通知其他“感兴趣”的对象。为了更具描述性，这里有一段直接引自[维基百科](http://en.wikipedia.org/wiki/Observer_pattern)的话:

> 观察者模式(又名。dependencies，publish/subscribe)是一种软件设计模式，在这种模式中，一个名为 subject 的对象维护一个名为 observers 的从属对象列表，并自动通知它们任何状态变化，通常是通过调用它们的方法之一。它主要用于实现分布式事件处理系统。

Ruby 标准库中的 [Observable](http://ruby-doc.org/stdlib-1.9.3/libdoc/observer/rdoc/Observable.html) 模块为我们实现这种模式提供了必要的机制，并且相当容易使用。

### 规划

为了使用这个模式，我们首先需要想出一个场景。一个例子是一个应用程序，它跟踪汽车的里程，并提醒我们什么时候需要把汽车送去维修。这是一个非常简单的例子，但它将允许我们探索这种模式。

### 基本结构

我们要做的第一件事是为我们的通知类创建一个基本结构，它将作为一个观察者。这里真正需要注意的一项是 *update()* 方法。这是 [Observable](http://ruby-doc.org/stdlib-1.9.3/libdoc/observer/rdoc/Observable.html) 模块在通知观察者变化时将使用的回调，方法名需要为 *update()* 。

让我们从组装一个简单的*通知器*类开始:

```
class Notifier
  def update()
  end
end
```

这再简单不过了。接下来，让我们为主题创建结构，我们的 *Car* 类:

```
class Car
  attr_reader :mileage, :service

  def initialize(mileage = 0, service = 3000)
    @mileage, @service = mileage, service
  end

  def log(miles)
    @mileage += miles
  end
end
```

它包含属性*里程*和*服务*，方法*初始化()*和*日志()*。 *initialize()* 方法将为汽车的当前里程和需要维修的里程设置初始值。 *log()* 方法将记录最近行驶了多少英里，并将其添加到车辆总里程中。

### 修复*通知程序*类

现在我们已经了解了我们的 *Car* 类是做什么的，我们可以继续填充我们希望通知程序做什么的逻辑:

```
class Notifier
  def update(car, miles)
    puts "The car has logged #{miles} miles, totaling #{car.mileage} miles traveled."
    puts "The car needs to be taken in for a service!" if car.service &amp;lt;= car.mileage
  end
end
```

这意味着每次观察者得到通知时，它将在屏幕上打印一条关于里程使用情况的消息，如果总里程数超过服务里程数，还会打印一条关于服务状态的消息。

### 把所有的放在一起

随着*汽车*类的基本结构完成和*通知器*类就位，我们唯一要做的就是将[可观察](http://ruby-doc.org/stdlib-1.9.3/libdoc/observer/rdoc/Observable.html)模块就位。

首先，我们需要*将*包含在我们的*汽车*类中:

```
require 'observer'

class Car
  include Observable
  ...
end
```

接下来，我们将在每次创建 *Car* 类的新实例时添加一个观察者。让我们修改一下*初始化*的方法:

```
def initialize(mileage = 0, service = 3000)
  @mileage, @service = mileage, service
  add_observer(Notifier.new)
end
```

我们在这个类中需要做的最后一个改变是在我们的 *log* 方法中。我们需要告诉观察者，每次记录新里程时，我们的对象都发生了变化:

```
def log(miles)
  @mileage += miles
  changed
  notify_observers(self, miles)
end
```

这里我们调用`changed`，它设置对象的改变状态(默认为 true)。和`notify_observers(self, miles)`，它通知观察者一个变化。

完整的*车*级看起来是这样的:

```
require 'observer'

class Car
  include Observable

  attr_reader :mileage, :service

  def initialize(mileage = 0, service = 3000)
    @mileage, @service = mileage, service
    add_observer(Notifier.new)
  end

  def log(miles)
    @mileage += miles
    changed
    notify_observers(self, miles)
  end
end
```

总而言之，下面是我们对 *Car* 类所做的更改:

*   为了使用[可观测的](http://ruby-doc.org/stdlib-1.9.3/libdoc/observer/rdoc/Observable.html)模块，我们首先需要`require`它；
*   接下来我们用`include Observable`收录；
*   当我们的 *Car* 类的一个实例被创建时，一个观察者被添加；
*   当调用 *log()* 方法时，它通过断言对象已经改变来通知观察者

### 运行代码

现在是有趣的部分，让我们看看当我们创建一个新的 *Car* 类实例并调用 *log* 时会发生什么:

```
car = Car.new(2300, 3000)
car.log(100)
=> "The car has logged 100 miles, totaling 2400 miles traveled."
car.log(354)
=> "The car has logged 354 miles, totaling 2754 miles traveled."
car.log(300)
=> "The car has logged 300 miles, totaling 3054 miles traveled."
=> "The car needs to be taken in for service!"
```

首先，我们创建一个 2300 英里的 *Car* 类的实例，并且我们设置它需要在到达 3000 英里时进行维护。

每次记录新的里程时，*通知器*类输出增加的里程以及行驶里程的计数。当车辆的总里程数大于设定的保养里程数时，会产生另一个输出。很酷吧。

## 单一模式

单例模式的概念相当简单:一个类只能有一个实例。当应用程序只允许为给定的类实例化一个对象时，这很有用。

尽管开发人员对这种模式有不同的看法，但它也经常在其他语言中使用，比如 Java 和基于 C 的语言。在 Ruby 中，标准库中的 [Singleton](http://www.ruby-doc.org/stdlib-1.9.3/libdoc/singleton/rdoc/Singleton.html) 模块可以用来实现这种模式。

### 规划

假设我们需要设计一个类来保存应用程序的配置数据，并且这个配置只能有一个实例。当然，我们可以通过创建一个模块来模拟单例，但是我们必须确保它不能被复制或克隆，否则它将失去它的用途。

### 综合

创建单例类的第一步是`require`和`include`类中的单例模块:

```
require 'singleton'

class AppConfig
  include Singleton
end
```

如果您试图像平常一样实例化这个类，就会引发一个`NoMethodError`异常。该构造函数是私有的，以防止意外创建其他实例:

```
AppConfig.new

#=> NoMethodError: private method `new' called for AppConfig:Class
```

为了访问这个类的实例，我们需要使用 Singleton 模块提供的`instance()`方法。首次调用此方法时，会创建类的一个实例，所有后续调用都会返回创建的实例。好奇想看看这到底是不是真的？

```
first, second = AppConfig.instance, AppConfig.instance
first == second

#=> true
```

千真万确！现在我们知道了它是如何工作的，让我们修改一下 *AppConfig* 类并添加一些东西。

```
#...

  attr_accessor :data

  def version
    '1.0.0'
  end
```

这里我们添加了一个保存配置数据的`data`属性和一个返回当前版本的`version`方法。将所有这些放在一起，这是完整的类:

```
require 'singleton'

class AppConfig
  include Singleton
  attr_accessor :data

  def version
    '1.0.0'
  end
end
```

祝贺你，你刚刚在 Ruby 中实现了一个单例模式！现在，让我们来玩玩它:

```
AppConfig.instance.data = {enabled: true}
=> {:enabled=>true}
AppConfig.instance.version
=> "1.0.0"

second = AppConfig.instance
second.data = {enabled: false}
=> {:enabled=>false}
AppConfig.instance.data
=> {:enabled=>false}
```

我们首先用任意值设置`data`属性，并检查它的版本。接下来，我们复制单例实例，更改它的`data`值，并确认该值在单个实例中发生了更改。

### 结论

本文展示了如何将**观察者模式**和**单例模式**用于 Ruby，我希望这里展示的例子可以为您在自己的应用程序中实现它们提供基础。

(注意:本文图片中的图表是用 [http://yuml.me](http://yuml.me) 制作的)

## 分享这篇文章