# PHP 中的自省和反思

> 原文：<https://www.sitepoint.com/introspection-and-reflection-in-php/>

内省是任何编程语言中的一个共同特征，它允许程序员操作对象类。当您不知道在设计时需要执行哪个类或方法时，您会发现自省特别有用。

PHP 中的自省提供了检查类、接口、属性和方法的有用能力。PHP 提供了大量的函数，可以用来完成这项任务。为了帮助您理解自省，我将使用 PHP 中的例子简要概述一些 PHP 的类、方法和函数，以突出它们的用法。

在本文中，您将看到几个如何使用一些最有用的 PHP 内省功能的例子，以及一个专门介绍提供类似于内省功能的 API(反射 API)的章节。

## PHP 自省函数

在第一个例子中，我将演示一些 PHP 的自省函数。您可以使用这些函数提取关于类的基本信息，比如它们的名称、它们的父类的名称等等。

*   `class_exists()`–检查是否已经定义了类别
*   `get_class()`–返回对象的类名
*   `get_parent_class()`–返回对象的父类的类名
*   `is_subclass_of()`–检查对象是否有给定的父类

下面是 PHP 代码示例，它包含对`Introspection`和`Child`类的定义，并输出由上面列出的函数提取的信息:

```
<?php
class Introspection
{
    public function description() {
        echo "I am a super class for the Child class.n";
    }
}

class Child extends Introspection
{
    public function description() {
        echo "I'm " . get_class($this) , " class.n";
        echo "I'm " . get_parent_class($this) , "'s child.n";
    }
}

if (class_exists("Introspection")) {
    $introspection = new Introspection();
    echo "The class name is: " . get_class($introspection) . "n"; 
    $introspection->description();
}

if (class_exists("Child")) {
    $child = new Child();
    $child->description();

    if (is_subclass_of($child, "Introspection")) {
        echo "Yes, " . get_class($child) . " is a subclass of Introspection.n";
    }
    else {
        echo "No, " . get_class($child) . " is not a subclass of Introspection.n";
    }
}
```

上述代码的输出应该如下所示:

```
The class name is: Introspection
I am a super class for the Child class.
I'm Child class.
I'm Introspection's child.
Yes, Child is a subclass of Introspection.
```

您可以使用`class_exists()`方法来确定给定的类是否已经被定义，该方法使用一个字符串参数来表示要检查的类的名称，并使用一个可选的布尔值来决定是否在这个过程中调用自动加载器。

`get_class()`和`get_parent_class()`方法分别返回对象的类名或其父类名。两者都将对象实例作为参数。

`is_subclass_of()`方法将一个对象实例作为它的第一个参数和一个表示父类名的字符串，并返回该对象是否属于作为参数给出的类的子类。

下面是第二个例子，它包含了一个`ICurrencyConverter`接口和`GBPCurrencyConverter`类的定义，并输出由上面列出的函数提取的信息。和第一个例子一样，我将首先列出函数，然后向您展示一些代码。

*   `get_declared_classes()`–返回所有声明类的列表
*   `get_class_methods()`–返回类方法的名称
*   `get_class_vars()`–返回类的默认属性
*   `interface_exists()`–检查接口是否已定义
*   `method_exists()`–检查对象是否定义了方法

```
<?php
interface ICurrencyConverter
{
    public function convert($currency, $amount);
}

class GBPCurrencyConverter implements ICurrencyConverter
{
    public $name = "GBPCurrencyConverter";
    public $rates = array("USD" => 0.622846,
                          "AUD" => 0.643478);
    protected $var1;
    private $var2;

    function __construct() {}

    function convert($currency, $amount) {
        return $rates[$currency] * $amount;
    }
}

if (interface_exists("ICurrencyConverter")) {
    echo "ICurrencyConverter interface exists.n";
}

$classes = get_declared_classes();
echo "The following classes are available:n";
print_r($classes);

if (in_array("GBPCurrencyConverter", $classes)) {
    print "GBPCurrencyConverter is declared.n";

    $gbpConverter = new GBPCurrencyConverter();

    $methods = get_class_methods($gbpConverter);
    echo "The following methods are available:n";
    print_r($methods);

    $vars = get_class_vars("GBPCurrencyConverter");
    echo "The following properties are available:n";
    print_r($vars);

    echo "The method convert() exists for GBPCurrencyConverter: ";
    var_dump(method_exists($gbpConverter, "convert"));
}
```

上述代码的输出应该如下所示:

```
ICurrencyConverter interface exists.
The following classes are available:
Array
(
    [0] => stdClass
    [1] => Exception
    [2] => ErrorException
    [3] => Closure
    [4] => DateTime
    [5] => DateTimeZone
    [6] => DateInterval
    [7] => DatePeriod
    ...
    [154] => GBPCurrencyConverter
)
GBPCurrencyConverter is declared.
The following methods are available:
Array
(
    [0] => __construct
    [1] => convert
)
The following properties are available:
Array
(
    [name] => GBPCurrencyConverter
    [rates] => Array
        (
            [USD] => 0.622846
            [AUD] => 0.643478
        )
)
The method convert() exists for GBPCurrencyConverter: bool(true)
```

您可能已经猜到了，`interface_exists()`方法与第一个例子中讨论的`class_exists()`非常相似。它确定给定的接口是否已经定义，并接受一个接口名称字符串参数和一个可选的 autoloader 布尔值。

`get_declared_classes()`方法返回一个数组，其中包含所有已定义的类的名称，并且不接受任何参数。根据您加载的库(编译成 PHP 或用 require/include 加载)，可能会出现其他类。

`get_class_method()`将一个对象实例或一个字符串作为表示所需类的参数，并返回由该类定义的方法名数组。

请注意，在`ICurrencyConverter`类中定义并由`get_class_vars()`方法返回的所有属性中，只有`$name`和`$rates`出现在输出中。跳过了私有和受保护的属性。

## PHP 反射 API

PHP 通过其反射 API 支持反射。正如你从 PHP 手册中看到的，反射 API 比内省要慷慨得多，它提供了大量的类和方法，你可以用它们来完成反射任务。`ReflectionClass`类是 API 的主类，用于在类、接口和方法上应用反射，并提取关于所有类组件的信息。反射很容易在应用程序代码中实现，并且像内省一样，也非常直观。

这里有一个例子来说明使用相同定义的`ICurrencyConverter`接口和`Child`和`GBPCurrencyConverter`类的反射:

```
<?php
$child = new ReflectionClass("Child");
$parent = $child->getParentClass();
echo $child->getName() . " is a subclass of " . $parent->getName() . ".n";

$reflection = new ReflectionClass("GBPCurrencyConverter");
$interfaceNames = $reflection->getInterfaceNames();
if (in_array("ICurrencyConverter", $interfaceNames)) {
    echo "GBPCurrencyConverter implements ICurrencyConverter.n";
}

$methods = $reflection->getMethods();
echo "The following methods are available:n";
print_r($methods);

if ($reflection->hasMethod("convert")) {
    echo "The method convert() exists for GBPCurrencyConverter.n";
}
```

上述代码的输出应该如下所示:

```
Child is a subclass of Introspection.
GBPCurrencyConverter implements ICurrencyConverter.
The following methods are available:
Array
(
    [0] => ReflectionMethod Object
        (
            [name] => __construct
            [class] => GBPCurrencyConverter
        )

    [1] => ReflectionMethod Object
        (
            [name] => convert
            [class] => GBPCurrencyConverter
        )

)
The method convert() exists for GBPCurrencyConverter.
```

`getInterfaceNames()`方法返回一个带有类实现的接口名称的数组。`getParentClass()`方法可以返回父类的`ReflectionClass`对象表示，如果没有父类，则返回 false。要列出`ReflectionClass`对象的名称，可以使用`getName()`方法，如上面的代码所示。

`getMethods()`方法检索一个方法数组，并可以将一个由`ReflectionMethod::IS_STATIC`、`IS_PUBLIC`、`IS_PROTECTED`、`IS_PRIVATE`、`IS_ABSTRACT`和`IS_FINAL`组成的位掩码组合作为可选参数，根据可见性过滤列表。

反射 API 提供了一个很好的反射实现，让您能够创建更复杂的应用程序，比如 [ApiGen](http://apigen.org/) ，尽管进一步的讨论超出了本文的目标。

## 摘要

在本文中，您已经看到了如何使用 PHP 的自省函数和反射 API 来获取关于类、接口、属性和方法的信息。提取这些信息的目的是为了在运行时更深入地了解代码，并创建复杂的应用程序。

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章