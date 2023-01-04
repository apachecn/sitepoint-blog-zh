# 在 PHP 5.4 中使用特征

> 原文：<https://www.sitepoint.com/using-traits-in-php-5-4/>

通过更好的组织和代码重用来最小化代码重复是面向对象编程的一个重要目标。但是在 PHP 中，由于它使用的单一继承模型的限制，这有时会很困难；您可能想在多个类中使用一些方法，但是它们可能不太适合继承层次结构。

像 C++和 Python 这样的语言允许我们从多个类继承，这在某种程度上解决了这个问题，而 Ruby 中的 mixins 允许我们混合一个或多个类的功能，而不使用继承。但是多重继承也有一些问题，比如[菱形问题](http://en.wikipedia.org/wiki/Diamond_problem)问题，混合可能是一个复杂的工作机制。

在本文中，我将讨论特性，这是 PHP 5.4 中引入的一个新特性，用来解决这些问题。traits 的概念本身对于编程来说并不新鲜，在 Scala 和 Perl 等其他语言中也有使用。它们允许我们跨不同类层次结构中的独立类水平重用代码。

## 一个特征是什么样子的

trait 类似于一个抽象类，它本身不能被实例化(尽管它更经常被比作一个接口)。PHP 文档将特征定义如下:

> Traits 是 PHP 等单一继承语言中的代码重用机制。Trait 旨在通过使开发人员能够在位于不同类层次结构中的几个独立类中自由地重用方法集，来减少单一继承的一些限制。

让我们考虑这个例子:

```
<?php
class DbReader extends Mysqli
{
}

class FileReader extends SplFileObject
{
}
```

如果两个类都需要一些共同的功能，例如都是单例的，那就有问题了。由于 PHP 不支持多重继承，要么每个类都必须实现必要的代码来支持单例模式，要么就会有一个没有意义的继承层次结构。特质为这类问题提供了一个解决方案。

```
<?php
trait Singleton
{
    private static $instance;

    public static function getInstance() {
        if (!(self::$instance instanceof self)) {
            self::$instance = new self;
        }
        return self::$instance;
    }
}

class DbReader extends ArrayObject
{
    use Singleton;
}

class  FileReader
{
    use Singleton;
}
```

trait `Singleton`用一个静态方法`getInstance()`直接实现了 Singleton 模式，这个静态方法使用这个 trait 创建了一个类对象(如果还没有创建)并返回它。

让我们尝试使用方法`getInstance()`创建这些类的对象。

```
<?php
$a = DbReader::getInstance();
$b = FileReader::getInstance();
var_dump($a);  //object(DbReader)
var_dump($b);  //object(FileReader)
```

我们可以看到，`$a`是`DbReader`的一个对象，`$b`是`FileReader`的一个对象，但是两者现在都表现为单例。来自`Singleton`的方法已经被水平注入到使用它的类中。

特征不会给类强加任何额外的语义。在某种程度上，您可以将其视为编译器辅助的复制和粘贴机制，其中 trait 的方法被复制到组合类中。

如果我们只是从一个拥有私有`$instance`属性的父类中子类化`DbReader`，该属性就不会显示在`ReflectionClass::export()`的转储中。然而有了特质，就有了！

```
Class [  class FileReader ] {
  @@ /home/shameer/workplace/php54/index.php 19-22

  - Constants [0] {
  }
  - Static properties [1] {
    Property [ private static $_instance ]
  }
  - Static methods [1] {
    Method [  static public method instance ] {
      @@ /home/shameer/workplace/php54/index.php 6 - 11
    }
  }
  - Properties [0] {
  }
  - Methods [0] {
  }
}
```

## 多重特征

到目前为止，我们在一个类中只使用了一个特征，但是在某些情况下，我们可能需要合并多个特征的功能。

```
<?php
trait Hello
{
    function sayHello() {
        echo "Hello";
    }
}

trait World
{
    function sayWorld() {
        echo "World";
    }
}

class MyWorld
{
    use Hello, World;
}

$world = new MyWorld();
echo $world->sayHello() . " " . $world->sayWorld(); //Hello World
```

这里我们有两个特征，`Hello`和`World`。特质`Hello`只会说“你好”，特质`World`会说“世界”。在`MyWorld`类中，我们应用了`Hello`和`World`，这样`MyWorld`对象将拥有来自这两个特征的方法，并且能够说“Hello World”。

## 由性状组成的性状

随着应用程序的增长，很有可能我们会有一组跨不同类使用的特征。PHP 5.4 允许我们拥有由其他特征组成的特征，这样我们可以在所有这些类中只包含一个而不是多个特征。这让我们将前面的示例重写如下:

```
<?php
trait HelloWorld
{
    use Hello, World;
}

class MyWorld
{
    use HelloWorld;
}

$world = new MyWorld();
echo $world->sayHello() . " " . $world->sayWorld(); //Hello World
```

这里我们使用特征 Hello 和 World 创建了特征`HelloWorld`，并将其包含在`MyWorld`中。由于`HelloWorld`特征有来自其他两个特征的方法，这就好像我们自己在类中包含了这两个特征一样。

## 优先顺序

正如我已经提到的，特征的工作方式就好像它们的方法已经被复制并粘贴到使用它们的类中，并且它们完全被扁平化到类的定义中。在不同的特征中或者在类本身中可能有同名的方法。您可能想知道哪一个将在子类的对象中可用。

优先顺序是:

1.  特征的方法覆盖从父类继承的方法
2.  当前类中定义的方法覆盖特征中的方法

下面的例子说明了这一点:

```
<?php
trait Hello
{
    function sayHello() {
        return "Hello";
    }

    function sayWorld() {
        return "Trait World";
    }

    function sayHelloWorld() {
        echo $this->sayHello() . " " . $this->sayWorld();
    }

    function sayBaseWorld() {
        echo $this->sayHello() . " " . parent::sayWorld();
    }
}

class Base
{
    function sayWorld(){
        return "Base World";
    }
}

class HelloWorld extends Base
{
    use Hello;
    function sayWorld() {
        return "World";
    }
}

$h =  new HelloWorld();
$h->sayHelloWorld(); // Hello World
$h->sayBaseWorld(); // Hello Base World
```

我们有一个从`Base`派生而来的`HelloWorld`类，两个类都有一个名为`sayWorld()`的方法，但是实现不同。此外，我们已经在`HelloWorld`类中包含了特征`Hello`。

我们有两个方法，`sayHelloWorld()`和`sayBaseWorld()`，前者称为`sayWorld()`，它同时存在于两个类和特征中。但是在输出中，我们可以看到子类中的一个被调用。如果我们需要从父类中引用方法，我们可以通过使用`sayBaseWorld()`方法中所示的 parent 关键字来实现。

## 冲突解决和别名

当使用多个特征时，可能会出现不同的特征使用相同的方法名的情况。例如，如果您因为方法名冲突而试图运行以下代码，PHP 将会给出致命错误:

```
<?php
trait Game
{
    function play() {
        echo "Playing a game";
    }
}

trait Music
{
    function play() {
        echo "Playing music";
    }
}

class Player
{
    use Game, Music;
}

$player = new Player();
$player->play();
```

这种性格冲突不会自动为你解决。相反，您必须使用关键字`insteadof`选择应该在组合类中使用哪种方法。

```
<?php
class Player
{
    use Game, Music {
        Music::play insteadof Game;
    }
}

$player = new Player();
$player->play(); //Playing music
```

这里我们选择在作曲类中使用`Music`特征的`play()`方法，这样`Player`类将播放音乐，而不是游戏。

在上面的例子中，从两个特征中选择了一种方法。在某些情况下，您可能希望同时保留它们，但仍要避免冲突。可以为特征中的方法引入一个新名称作为别名。别名并不重命名方法，而是提供一个可以调用它的替代名称。使用关键字`as`创建别名。

```
<?php
class Player
{
    use Game, Music {
        Game::play as gamePlay;
        Music::play insteadof Game;
    }
}

$player = new Player();
$player->play(); //Playing music
$player->gamePlay(); //Playing a game
```

现在任何一个类`Player`的对象都会有一个方法`gamePlay()`，和`Game::play()`一样。这里应该注意的是，我们已经明确地解决了任何冲突，甚至在别名之后。

## 反射

反射 API 是 PHP 的强大特性之一，用于分析接口、类和方法的内部结构，并对它们进行逆向工程。既然我们在讨论特征，你可能有兴趣了解反射 API 对特征的支持。在 PHP 5.4 中，四个方法被添加到`ReflectionClass`中以获取关于类中特征的信息。

我们可以使用`ReflectionClass::getTraits()`来获得一个类中使用的所有特征的数组。`ReflectionClass::getTraitNames()`方法将简单地返回该类中特征名称的数组。`ReflectionClass::isTrait()`有助于检查某事物是否是一种特质。

在上一节中，我们讨论了特性的别名，以避免由于同名特性而导致的冲突。`ReflectionClass::getTraitAliases()`将返回一个映射到其原始名称的特征别名数组。

## 其他功能

除了上面提到的，还有其他的特征使特征更有趣。我们知道在传统继承中，子类不能访问类的私有属性。Traits 可以访问构成类的私有属性或方法，反之亦然！这里有一个例子:

```
<?php
trait Message
{
    function alert() {
        echo $this->message;
    }
}

class Messenger
{
    use Message;
    private $message = "This is a message";
}

$messenger = new Messenger;
$messenger->alert(); //This is a message
```

当特征被完全展平到由它们组成的类中时，该特征的任何属性或方法都将成为该类的一部分，我们可以像访问任何其他类属性或方法一样访问它们。

我们甚至可以在 trait 中使用抽象方法来强制组合类实现这些方法。例如:

```
<?php
trait Message
{
    private $message;

    function alert() {
        $this->define();
        echo $this->message;
    }
    abstract function define();
}

class Messenger
{
    use Message;
    function define() {
        $this->message = "Custom Message";
    }
}

$messenger = new Messenger;
$messenger->alert(); //Custom Message
```

这里我们有一个特征`Message`和一个抽象方法`define()`。它需要所有使用这个特征的类来实现这个方法。否则，PHP 会给出一个错误，说有一个抽象方法没有实现。

与 Scala 中的 traits 不同，PHP 中的 traits 可以有一个构造函数，但它必须声明为 public(如果是 private 或 protected，将会抛出一个错误)。不过，无论如何，在 traits 中使用构造函数时要小心，因为这可能会导致构成类中的意外冲突。

## 摘要

Traits 是 PHP 5.4 中引入的最强大的特性之一，我已经在本文中讨论了几乎所有的特性。它们允许程序员跨多个类水平地重用代码片段，这些类不必在同一个继承层次结构中。它们为我们提供了代码重用的轻量级机制，而不是复杂的语义。尽管 traits 有一些缺点，但它们肯定有助于改进应用程序的设计，消除代码重复，使其更加简洁。

图像通过[视频](http://www.shutterstock.com/gallery-196033p1.html) / 

## 分享这篇文章