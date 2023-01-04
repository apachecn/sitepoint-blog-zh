# Ruby 元编程:第一部分

> 原文：<https://www.sitepoint.com/ruby-metaprogramming-part-i/>

如果您正在使用 Ruby，那么现在您可能已经经常听到“元编程”这个词了。您可能已经使用过元编程，但是没有完全理解它的真正威力或用途。到本文结束时，您不仅应该对它是什么，而且应该对它的能力，以及如何在您的项目中利用 Ruby 的“杀手级特性”有一个牢固的掌握。

## 什么是“元编程”？

元编程最好解释为编程的编程。但是不要让这个抽象的定义吓跑了你，因为 Ruby 让元编程变得和使用它一样容易理解。

元编程可以用来在程序运行时添加、编辑或修改程序代码。使用它，您可以在对象上创建新的或删除现有的方法，重新打开或修改现有的类，捕捉不存在的方法，并避免重复编码以保持程序干燥。

## 理解 Ruby 如何调用方法

在理解元编程的全部范围之前，必须掌握 Ruby 在调用方法时是如何找到方法的。当您在 Ruby 中调用一个方法时，它必须在继承链内的所有代码中找到该方法(如果存在的话)。

```
class Person
  def say
    "hello"
  end
end

john_smith = Person.new
john_smith.say # => "hello"
```

在上例中调用`say()`方法时，Ruby 首先寻找`john_smith`对象的父对象；因为这个对象是`Person`类的一个实例，并且它有一个名为`say()`的可用方法，所以这个方法被称为。

然而，当对象是从另一个类继承的类的实例时，事情变得更加复杂:

```
class Animal
  def eats
    "food"
  end

  def lives_in
    "the wild"
  end
end

class Pig < Animal
  def lives_in
    "farm"
  end
end

babe = Pig.new
babe.lives_in # => "farm"
babe.eats # => "food"
babe.thisdoesnotexist # => NoMethodError: undefined method `thisdoesnotexist' for #<Pig:0x16a53c8>
```

当我们在混合中引入继承时，Ruby 需要考虑在继承链中更高层定义的方法。当我们调用`babe.lives_in()`时，Ruby 首先检查`Pig`类中的方法`lives_in()`；因为存在，所以叫。

然而，当我们调用`babe.eats()`方法时，情况略有不同。Ruby 通过询问`Pig`类是否可以响应`eats()`来检查该方法，如果`Pig`上不存在该方法，它将继续询问父类`Animal`是否可以响应；在我们的情况下，它可以，所以它将被称为。

当我们调用`babe.thisdoesnotexist()`时，因为方法不存在，我们得到一个`NoMethodError`异常。你可以把这看作是一种级联:无论在继承链的最底层定义什么方法，都将是最终被调用的方法；如果这个方法根本不存在，就会引发一个异常。

基于我们目前所发现的，我们可以总结 Ruby 查找每个方法的方式，就像下面这样:

*   询问对象的父类是否可以响应该方法，如果找到就调用它。
*   询问下一个父类是否可以响应这个方法，如果找到就调用它，继续这个步骤直到继承链的顶端。
*   如果继承链中没有任何东西可以响应被调用的方法，则该方法不存在，并且应该引发一个异常。

请注意，因为每个对象都是从最顶层的`Object`(或 Ruby 1.9 中的`BasicObject`)继承而来，所以该类总是最后一个被请求，但前提是它在继承链中一直向上，而没有找到可以响应的方法。

## 引入单例类

Ruby 给了你面向对象编程的全部能力，允许你创建从其他类继承的对象并调用它们的方法；但是如果只有一个对象需要添加、修改或删除呢？

“单例类”(有时被称为“特征类”)就是为此而设计的，它允许你做所有的事情，甚至更多。一个简单的例子就是:

```
greeting = "Hello World"

def greeting.greet
  self
end

greeting.greet # => "Hello World"
```

让我们一行一行地消化这里刚刚发生的事情。在第一行，我们创建了一个名为`greeting`的新变量，它代表一个简单的`String`值。在第二行，我们创建一个名为`greeting.greet`的新方法，并赋予它一个非常简单的内容。Ruby 允许您通过使用格式`some_object.method_name`来选择将方法定义附加到什么对象上，您可能认为这与将类方法添加到类中的语法相同(即`def self.something`)。在这种情况下，由于我们先有了`greeting`，该方法被附加到了我们的`greeting`变量上。在最后一行，我们调用刚刚定义的新的`greeting.greet`方法。

`greeting.greet`方法可以访问它所附加的整个对象；在 Ruby 中，我们总是把那个对象称为`self`。在这种情况下，`self`指的是我们赋予它的`String`值。如果我们将它附加到一个`Array`，`self`将会返回那个`Array`对象。

正如您将要看到的，使用`some_object.method_name`添加方法并不总是完成此类任务的最佳方式。这就是为什么 Ruby 提供了另一种更有用的方式来动态地处理对象及其方法。认识一下 Singleton 类:

```
greeting = "i like cheese"

class << greeting
  def greet
    "hello! " + self
  end
end

greeting.greet # => "hello! i like cheese"
```

语法很奇怪，但是结果和我们添加方法的`some_object.method_name`方式是一样的。这种单例类方法允许您一次添加许多方法，而不必为所有方法名添加前缀。该语法还允许您添加通常在类声明期间添加的任何内容，包括`attr_writer`、`attr_reader`和`attr_accessor`方法。

## 它是如何工作的？

那么它实际上是如何工作的呢？“singleton class”这个名字可能稍微泄露了这一点，但是 Ruby 很狡猾，它向我们的继承链中添加了另一个类。当你试图在 singleton 类上操作时，Ruby 需要一种方法来向我们要添加的对象添加方法，这是语言所不允许的。为了解决这个问题，它创建了一个秘密的新类，我们称之为“单例类”。这个类被赋予方法和变化，成为我们正在处理的对象的父类。这个单例类也是我们对象的前一个父类的实例，因此继承链基本保持不变:

```
some object instance > singleton class > parent class > ... > Object/BasicObject
```

回到我们所知道的 Ruby 方法查找，我们之前决定 Ruby 解释器在查找方法时遵循三个简单的步骤。单例类给这个查找过程增加了一个额外的步骤:

*   询问对象它的单例类是否可以响应这个方法，如果找到就调用它。
*   询问对象的父类是否可以响应该方法，如果找到就调用它。
*   询问下一个父类是否可以响应这个方法，如果找到就调用它，继续这个步骤直到继承链的顶端。
*   如果继承链中没有任何东西可以响应被调用的方法，则该方法不存在，并且应该引发一个异常。

正如我们之前所讨论的，你可以把这看作是一个级联，这对我们理解 Ruby 中的对象有着非常重要的影响:对象不仅可以从它们继承的类中获得方法，而且现在它们可以在程序运行时获得各自唯一的方法。

## 使用`instance_eval`和`class_eval`进行元编程

拥有单例类固然很好，但是要真正动态地使用对象，你需要能够在运行时在其他函数中重新打开它们。不幸的是，Ruby 在语法上不允许在函数中使用任何`class`语句。这就是`instance_eval`的用武之地。

`instance_eval`方法是在 Ruby 的标准`Kernel`模块中定义的，它允许你向一个对象添加实例方法，就像我们的 singleton 类语法一样。

```
foo = "bar"
foo.instance_eval do
  def hi
    "you smell"
  end
end

foo.hi # => "you smell"
```

`instance_eval`方法可以接受一个块(将`self`设置为您正在操作的对象的块)，或者一串要评估的代码。在块内部，你可以定义新的方法，就像你在编写一个类一样，这些方法将被添加到对象的单例类中。

由`instance_eval`定义的方法将是实例方法。重要的是要注意范围是实例方法的范围，因为这意味着你不能做像`attr_accessor`这样的事情。如果你发现自己需要这样做，你会想要对对象的类进行操作，而不是使用`class_eval`:

```
bar = "foo"
bar.class.class_eval do
  def hello
    "i can smell you from here"
  end
end

bar.hello # => "i can smell you from here"
```

正如您所看到的，`instance_eval`和`class_eval`非常相似，但是它们的范围和应用稍微有些不同。你可以通过记住你使用`instance_eval`做实例方法，使用`class_eval`做类方法，来记住在每种情况下用什么。

## 我为什么在乎？

此时，您可能会想“这很好，但我为什么要在乎呢？这些东西有什么现实价值？”简单的回答是“是的，你应该”和“很多”。

元编程允许您创建更灵活的代码，无论是通过漂亮的 API 还是易于测试的代码。不仅如此，它还允许您使用强大而优雅的编程技术和语法来做到这一点。元编程允许您创建干巴巴的、高度可重用的、极其简洁的代码。

在本系列的第二部分中，我们将看看如何将元编程应用于日常问题，并看看它的优雅和强大如何能永远改变你开发解决方案的方式。

## 分享这篇文章