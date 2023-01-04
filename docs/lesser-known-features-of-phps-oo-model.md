# PHP 面向对象模型鲜为人知的“特性”

> 原文：<https://www.sitepoint.com/lesser-known-features-of-phps-oo-model/>

当今绝大多数用 PHP 编写的应用程序都是面向对象的，一般来说，PHP 开发人员非常了解核心的 OOP 概念。这篇文章拓展了您的理解范围，并向您展示了 PHP 中 OOP 的一些技巧或潜在陷阱，这取决于您的观点。

## 接口和特征的继承

让我们从熟悉的领域开始:接口。PHP 中的接口允许我们定义一个契约，任何实现这个接口的对象必须自己实现它。但是您知道接口也可以继承其他接口，并且父接口或子接口都可以由一个类来实现吗？

考虑这段代码，它定义了一个接口，另一个扩展了该接口，还有一个实现子接口的类:

```
<?php
interface Reversible
{
    function reverse($target);
}

interface Recursible extends Reversible
{
    function recurse($target);
}

class Tricks implements Recursible
{
    public function recurse($target) {
        // something cool happens here
    }

    public function reverse($target) {
        // something backward happens here
    }
}
```

我定义了一个名为`Reversible`的接口，以及另一个名为`Recursible`的接口来扩展它。当我在`Tricks`类中实现`Recursible`时，来自`Recursible`接口的`recurse()`方法和来自`Reversible`接口的`reverse()`方法都必须存在。

当一个接口将包含跨一组类使用的方法，但另一组类需要这些方法和一组额外的方法时，这是一种有用的技术。您可以制作一个如此处所示的复合接口，而不是实现两个接口。

特质提供了类似的模式。如果您还没有机会使用 traits，它们看起来像类，可以包含完整的方法定义，可以应用于应用程序中的任何类，而不需要从公共位置继承。我们经常被告知，将代码移动到一个公共的父类比在类之间复制粘贴更好，但有时类是不相关的，继承是错误的。特征是一个很好的特性，因为它们允许我们重用代码，即使对象没有足够的相似性来证明继承。

让我们看一个简单的特征例子。(警告:上一个例子中滑稽的命名方案仍然存在。)

```
<?php
trait Reversible
{
    public function reverse($target) {
        return array_reverse($target);
    }
}
```

traits 的语法看起来非常像一个类，实际上 traits 可以包含属性和方法——包括抽象方法。然后通过使用关键字`use`将这些应用到一个类中，该类将特征带入其中…或者它们也可以应用到一个特征，正如我们在这里看到的:

```
<?php
trait RecursivelyReversible
{
    use Reversible;

    public function reverseRecursively($target) {
        foreach($target as $key => $item) {
            if(is_array($item)) {
                $target[$key] = $this->reverseRecursively($item);
            }
        }
        return $this->reverse($target);
    }
}
```

现在我们有了一个特征，它使用了另一个特征并添加了自己的方法。这个方法调用第一个特征中的一个方法。在这一点上，我们可以将 trait 应用到类中，因为 trait 包含了我想在这里说明的特性，所以类不包含任何其他东西。

```
<?php
class Mirror
{
    use RecursivelyReversible;
}

$array = [1, "green", "blue", ["cat", "sat", "mat", [0,1,2]]];
$reflect = new Mirror();
print_r($reflect->reverseRecursively($array));
```

如果您运行这段代码，您将会看到不仅数组的顶层被反转，PHP 还会深入并反转所有的子元素。

## 私有财产有多私有？

所以你认为私有属性只能从当前对象中访问？不完全正确！实际上，这种限制只针对类名，所以同一个类的对象可以访问彼此的私有属性和方法。为了说明这一点，我创建了一个具有私有属性和公共方法的类，该方法接受同一个 object 类的实例作为参数:

```
<?php
class Storage
{
    private $things = [];

    public function add($item) {
        $this->things[] = $item;
    }

    public function evaluate(Storage $container) {
        return $container->things;
    }
}

$bucket = new Storage();
$bucket->add("phone");
$bucket->add("biscuits");
$bucket->add("handcream");

$basket = new Storage();
print_r($basket->evaluate($bucket));
```

你可能认为`$basket`不能访问`$bucket`的私人数据，但实际上上面的代码工作得很好！问这种行为是陷阱还是特征就像问一株植物是花还是杂草；看你的意图和角度了。

## 抽象类是什么样子的？

抽象类通常被认为是不完整的类；我们只定义部分功能，并使用`abstract`关键字来阻止任何试图实例化它的行为。

```
<?php
class Incomplete
{
    abstract public function notFinished();
}
```

如果您尝试实例化`Incomplete`，您将看到以下错误:

```
PHP Fatal error: Class Incomplete contains 1 abstract method
and must therefore be declared abstract or implement the
remaining methods (Incomplete::notFinished) in /home/lorna/
sitepoint/oop-features/incomplete.php on line 5
```

该消息不言自明，但现在考虑另一个类:

```
<?php
abstract class PerfectlyGood
{
    public function doCoolStuff() {
        // cool stuff
        return true;
    }
}
```

除了关键字`abstract`之外，这是一个完全有效的类。事实上，如果您愿意，您可以将任何类标记为抽象的。其他类可以扩展它，但它本身不能被实例化。对于希望开发人员扩展他们的类而不是直接使用它们的库设计人员来说，这可能是一个有用的工具。正因为如此，Zend Framework 拥有丰富的抽象类传统。

## 类型提示不会自动加载

我们使用类型提示来确保方法的传入参数满足某些要求，方法是给出它必须是(或与之相关)的类或接口的名称。然而，如果类型提示中给出的类或接口还没有声明，PHP 不会调用自动加载器；我们只会看到缺少类声明的错误。

```
<?php
namespace MyNamespace;

class MyException extends Exception
{
}

class MyClass
{
    public function doSomething() {
        throw new MyException("you fool!");
    }
}

try {
    $myclass = new MyClass();
    $myclass->doSomething();
    echo "that went well";
}
catch (Exception $e) {
    echo "uh oh... " . $e->getMessage();
}
```

catch 子句中的类名实际上是一个类型提示，但是由于我们没有指出`Exception`类在顶级名称空间中，PHP 认为我们指的是不存在的`MyNamespaceException`。缺失的类不会引发错误，但是我们的异常现在缺失了`catch`子句。您可能认为我们会看到一条缺失的`MyNamespaceException`消息，但是我们得到的却是难以形容的“未捕获的异常”错误:

```
Fatal error: Uncaught exception 'MyNameSpaceMyException'
with message 'you fool!' in /home/lorna/sitepoint/
oop-features/namespaced_typehints.php:11
```

仔细想想，这种行为是完全有意义的——如果在类型提示中命名的东西还没有被加载，那么根据定义传入的参数不能匹配它。我自己也不得不犯这个错误，才真正思考过，而且只是一个错别字！如果你抓住了`Exception`而不是仅仅`Exception`，这就像我最初打算的那样。

## 最后

`finally`子句是 PHP 5.5 中最近引入的一个值得了解的特性。如果你使用过其他有异常的编程语言，那么你可能以前见过这个结构。永远只有一个`try`块，我们可以拥有任意多的`catch`块，从这个版本的 PHP 我们还可以添加一个`finally`。

这是之前的代码示例，添加了`finally`:

```
<?php
namespace MyNameSpace;

class MyException extends Exception
{
}

class MyClass
{
    public function doSomething() {
        throw new MyException("you fool!");
    }
}

try {
    $myclass = new MyClass();
    $myclass->doSomething();
    echo "that went well";
}
catch (Exception $e) {
    echo "uh oh ... " . $e->getMessage();
}
finally {
    echo "move along, nothing to see here";
}
```

无论我们是否到达了`try`块的末尾，是否进入了任何一个`catch`块，或者是否在途中有更多未被捕获的异常，都会发生`finally`子句。在这个例子中，`finally`块的输出实际上出现在关于未捕获异常的错误之前，因为直到我们结束了`try` / `catch` / `finally`部分，它才被捕获。

## 结论

当然，我在这里展示的一些例子是非常牵强的，希望你不会每天都碰到它们，但是我确实认为了解什么样的边缘情况是值得的，这样你就可以正确地设计和构建系统。理解构建模块当然可以帮助我们完成工作并快速调试问题，我喜欢分享想法来使这变得更容易！

你在 PHP 中发现了什么陷阱？请在评论区与我们分享。

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章