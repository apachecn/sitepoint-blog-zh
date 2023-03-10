# PHP 中的魔法方法和预定义常数

> 原文：<https://www.sitepoint.com/magic-methods-and-predefined-constants-in-php/>

PHP 为你的程序提供了一组特殊的预定义常量和神奇的方法。与使用`define()`设置的常量不同，常量的值取决于它们在代码中的使用位置，用于访问关于代码和 PHP 的只读信息。神奇的方法是保留的方法名，您可以在您的类中使用它们来挂钩特殊的 PHP 功能。

如果您还没有听说过 PHP 的神奇方法和常量，那么这篇文章就是为您准备的！我将回顾一些更有用的方法，以及如何在代码中使用它们。

## 预定义常数

预定义常数用于访问有关代码的信息。这里的常量在双下划线之间都是大写字母，例如`__LINE__`和`__FILE__`。下面是 PHP 提供的一些有用的常量:

*   `__LINE__`返回常量在源文件中出现的行号，如下所示:

```
<?php
echo "line number: " . __LINE__; // line number: 2
echo "line number: " . __LINE__; // line number: 3
echo "line number: " . __LINE__; // line number: 4
```

*   `__FILE__`代表文件的名称，包括其完整路径，如下所示:

```
<?php
echo "the name of this file is: " . __FILE__;
// the directory and name of file is: C:wampwwwindex.php
```

*   `__DIR__`仅表示文件的路径:

```
<?php
echo "the directory of this file is: " . __DIR__;
// the directory of this file is: C:wampwww
```

*   `__CLASS__`返回当前类的名称:

```
<?php
class Sample
{
    public function __construct() {
        echo __CLASS__;
    }
}
$obj = new Sample(); // Sample
```

*   `__FUNCTION__`返回当前函数的名称:

```
<?php
function mySampleFunc() {
    echo  "the name the function is: " . __FUNCTION__;
}
mySampleFunc(); //the name of function is: mySampleFunc
```

*   `__METHOD__`代表当前方法的名称:

```
<?php
class Sample
{
    public static function myMethod() {
        echo  "the name of method is: " . __METHOD__;	
    }
}
Sample::myMethod(); // the name of the method is: myMethod
```

*   `__NAMESPACE__`返回当前名称空间的名称:

```
<?php
namespace MySampleNS;
echo "the namespace is: " . __NAMESPACE__;
// the name space is: MySampleNS
```

## 神奇的方法

神奇的方法提供了特殊 PHP 行为的挂钩。与之前的常量不同，它们的名字用小写字母/驼色字母书写，带有两个前导下划线，如`__construct()`和`__destruct()`。

是 PHP 调用来创建你的类的对象实例的神奇方法。它可以接受任意数量的参数。

```
<?php
class MySample
{
    public function __construct($foo) {
        echo __CLASS__ . " constructor called with $foo.";
    }
}
$obj = new MySample(42);
// MySample constructor called with 42
```

顾名思义，当 PHP 的垃圾收集器销毁对象时，就会调用`__destruct()`方法。它不接受任何参数，通常用于执行任何可能需要的清理操作，比如关闭数据库连接。

```
<?php
class MySample
{
    public function __destruct() {
        echo__CLASS__ . " destructor called.";
    }
}
$obj = new MySample; // MySample destructor called
```

我们下一个神奇的方法处理属性重载，并为 PHP 提供了一种方法来处理对尚未定义的(或我们无法访问的)属性和方法的调用。

如果属性未定义(或不可访问)并在 getter 上下文中调用，PHP 将调用`__get()`方法。该方法接受一个参数，即属性的名称。它应该返回一个被视为属性值的值。

对于 setter 上下文中未定义的属性，调用`__set()`方法。它接受两个参数，属性名和值。

```
<?php
class MySample
{
    private $myArray = array();
    public function __set($prop, $value) {
        $this->myAarray[$prop] = $value;
    }
    public function __get($prop) {
        return $this->myArray[$prop];
    }
    public function __isset($prop) {
        return isset($this->myArray[$prop]);
    }
    public function __unset($prop) {
        unset($this->myArray[$prop]);
    }
    public function __toString() {
        return __CLASS__ . ":" . $this->name;
    }
}
$obj = new MySample();
if (!isset($obj->name)) {
    $obj->name = "Alireza";
}
echo $obj->name; // Alireza
echo $obj; // MySample:Alireza
```

在上面的示例代码中，属性`name`没有在类中定义。我试图给它赋值“mysample”，PHP 调用神奇的方法`__set()`。它接收“name”作为参数`$prop`，接收“Alireza”作为参数`$value`，我将值存储在私有的`$myArray`数组中。`__get()`方法以类似的方式工作；当我输出`$obj->name`时，调用`__get()`方法，并为`$prop`参数传入“name”。

还有其他神奇的方法可以帮助我们检索和检查样本代码中出现的不可访问的成员变量:`__isset()`、`__unset()`和`__toString()`。`__isset()`和`__unset()`都是由 PHP 中同名的函数触发的(没有下划线)。

`__isset()`检查属性是否已设置，并接受一个参数，即我们要测试的属性。`__unset()`接收一个参数，它是程序想要取消设置的属性的名称。

在许多情况下，将对象表示为字符串是很有用的，例如输出给用户或另一个进程。通常 PHP 在内存中以 ID 的形式呈现它们，这对我们不好。`__toString()`方法帮助我们将对象表示为一个字符串。该方法在对象被用作字符串的任何情况下都会被触发，例如:`echo "Hello $obj"`。它也可以像任何其他普通的公共方法一样被直接调用，这比附加一个空字符串来强制强制更好。

## 摘要

OOP 编程可以产生更易维护和测试的代码。它帮助我们创建更好的标准 PHP 代码。此外，它使得利用 PHP 提供的神奇方法和常量成为可能。

图片 via [斯捷潘·卡普尔](http://www.shutterstock.com/gallery-640609p1.html)/[Shutterstock](http://www.shutterstock)

## 分享这篇文章