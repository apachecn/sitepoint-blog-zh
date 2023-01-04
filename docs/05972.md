# GSwR VI:用 Ruby 保持优雅

> 原文：<https://www.sitepoint.com/gswr-stay-classy-with-ruby/>

欢迎来到 Ruby 系列[入门的最后一篇文章。](https://www.sitepoint.com/series/getting-started-with-ruby/)

在前一篇文章中，我们讨论了如何编写我们自己的方法。在这篇文章中，我们将学习所有关于类是如何工作的:如何创建它们，如何为它们创建方法，以及如何使用继承来避免重新创建大量的行为。

类实际上是 Ruby 的构建模块——Ruby 中的一切都是对象，而类基本上是方法定义的列表，就像对象的蓝图。类用于创建对象，称为类的*实例*。这些对象拥有类中定义的所有方法。

我们将从研究如何将我们自己的方法添加到内置的类中开始，比如字符串、整数和数组。

## 更改内置类

我们已经看到了像`String`和`Integer`这样的类中的许多内置方法，比如`reverse`和`odd?`。Ruby 让我们通过打开类定义来给这些类添加额外的方法。这是通过键入关键字`class`，后跟类名来实现的。

下面是一个打开`Integer`类来添加一个名为`double`的新方法的例子:

```
class Integer
  def double
    self * 2
  end
end
```

请注意，单词“Integer”以大写字母开头，这对于所有的类名都是如此。

这个新方法返回调用该方法的整数值乘以 2。`self`关键字指的是对象本身，在本例中是调用方法的整数。现在所有的整数都会有这个方法。

我们可以在 IRB 中测试这段代码。首先，将上面的代码保存在一个名为“extensions.rb”的文件中，然后将它加载到 IRB 中，以便我们可以使用额外的方法。为此，导航到保存“extensions.rb”文件的文件夹，然后通过在终端提示符下键入`irb`来启动 IRB。一旦 IRB 启动，我们只需要键入以下内容:

```
load ‘./extensions.rb’
=> true
```

现在我们可以试一试我们的新方法:

```
2.double
=> 4
5.double
=> 10
12345.double
=> 24690
```

太好了，成功了！

您可以为所有内置类(如字符串和数组)添加新方法。事实上，你甚至可以重新定义已经存在的方法。这里有一个改变`String`类上的`reverse`方法行为的例子。将以下代码添加到“extensions.rb”文件的底部:

```
class String
  def reverse
    "no reversing"
  end
end
```

如你所见，通过打开 String 类，我们重新定义了`reverse`方法来返回一个表示“不可逆”的字符串。试着在 IRB 中运行这个程序——你需要保存“extensions.rb ”,然后再`load`一次以使修改生效。

改变内置方法的行为方式非常令人不快，因为人们希望方法以某种方式工作。添加新的方法来增加更多的功能(通常)被认为是一件好事。这是猴子打补丁。Ruby on Rails 有一个名为[主动支持](https://github.com/rails/rails/tree/master/activesupport)的模块，它向`String`类添加了很多额外的方法。其中一个新方法叫做`pluralize`，它返回一个字符串的复数形式。

这里有一个类似的`pluralize`方法的例子。将其添加到“extensions.rb”文件的底部:

```
class String

  def pluralize
    case self
      when "woman" then "women"
      when "person" then "people"
      when "octopus" then "octopi"
      when "sheep" then "sheep"
      else self + "s"
    end
  end

end
```

它使用 case 语句返回一些不规则单词的复数形式，然后在所有其他单词的末尾加上一个“s”。让我们来看看 IRB 中的运行情况(不要忘记，您需要再次加载 extensions.rb):

```
octupus.pluralize
=> octupi

book.pluralize
=> books

bus.pluralize
=> buss
```

正如上一个例子所示，它决不是完整的。不过，很明显，这可以用来为所有字符串添加一些非常有用的功能。

## 创建自己的类

不过，我们不仅仅需要摆弄内置的类，我们还可以创建自己的类！

在最近的几篇文章中，我们模拟了一个正在滚动的骰子。让我们创建一个可以用来创建大量骰子对象的`Die`类。然后我们可以“掷骰子”产生随机数。

创建一个名为 dice.rb 的文件，并添加以下代码:

```
class Die
  def roll
    rand(1..6)
  end
end
```

任何类都以关键字`class`开头，后跟类名(总是大写)。它以关键字`end`结束。然后，我们在这个类定义中添加任何方法。

为了创建一个新的`Die`对象，我们使用了`Die`类的`new`方法(不要忘记加载‘die . Rb’文件):

```
die = Die.new
 => #<Die:0x8e3459c>
```

这实例化了一个由`#<0x8e3459c>``–`表示的新骰子对象``

