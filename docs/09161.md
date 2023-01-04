# PHP 名称空间

> 原文：<https://www.sitepoint.com/php-namespaces/>

名称空间是 PHP 中期待已久的特性。虽然 PHP 5 发布了许多其他重要特性，但直到版本 5.3 才支持名称空间。这导致了各种模仿它们的技术，尽管这些技术是必要的，但却是混乱的。尽管名称空间成为 PHP 的一部分已经一年多了，但这种技术仍然存在。许多开发人员根本不知道如何在他们的代码中使用适当的名称空间。在本文中，我将解释为什么名称空间很重要，以及如何在自己的 PHP 代码中使用它们。

## 为什么要使用名称空间？

假设你在一家没有很多员工的小公司工作。当谈论一个同事时，你可以直呼其名。在异常成功的一年后，你的老板决定将员工人数增加一倍。突然，当你提到“汤姆”时，人们不再确定你指的是谁，因为现在办公室里有很多汤姆。为了区分他们，你也开始使用他们的姓。

这个比喻说明了名称空间的概念。命名空间是标识符的命名容器，例如您在程序中使用的类名和函数名。它将它们组合在一起，消除了名称冲突的可能性。在本例中，雇员的姓氏用作名称空间。因为家庭成员经常被组合在一起，所以多个 Toms 能够共存而没有任何歧义。

大型应用程序通常有成百上千个组件，这导致了大量的名称冲突。名称空间允许您优雅地对组件进行分组，并避免这个问题。

在名称空间出现之前，开发人员通过在每个组件的类前面加上一个组件名，在面向对象的代码中模拟它们。例如，一个标准是使用下划线连接组件和类名；博客组件中的邮件类被命名为`Blog_Mail`。这种做法导致了许多长的、有时令人困惑的类名。考虑 PHPUnit 框架中的一个类名，`PHPUnit_Framework_MockObject_Matcher_StatelessInvocation`。这显然不理想，这就是为什么程序员需要 PHP 中对名称空间的适当支持。

## 定义名称空间

名称空间是用关键字`namespace`和一个名称定义的。它必须在任何其他代码之前声明，除了很少使用的`declare`语句。一旦定义，命名空间的范围就应用于整个文件。

```
<?php
namespace MyNamespace;
// ...
```

命名空间也可以通过用括号将内容括起来来定义。除了前面提到的`declare`，括号外不允许有代码。

```
<?php
namespace MyNamespace {
    // ...
}
```

这两种方法不能混用，选择一种，坚持下去。

可以通过用反斜杠分隔每个级别来定义嵌套的名称空间。

```
<?php
namespace MyProject\Blog\Admin;
```

通过多次使用`namespace`关键字，可以在一个文件中定义多个名称空间。在这种情况下，一旦下一个名称空间定义出现，前面的名称空间的范围就结束了。

```
<?php
namespace MyProject\Blog;
// following definitions are part of the MyProject\Blog namespace
// ...
namespace MyProject\Store;
// following definitions are now part of MyProject\Store
// ...
```

要在全局名称空间中设置文件的部分代码，请使用不带名称的`namespace`关键字。此功能只能与花括号语法一起使用。

```
<?php
namespace My\Project {
    // My\Project namespace
    // ...
}
namespace {
    // Global namespace
    // ...
}
```

## 引用命名空间标识符

尽管任何代码都可以出现在命名空间中，但是只有类、接口、函数和常数会受到它的影响。可以通过以下三种方式之一引用这些标识符:

1.  **全限定名**全限定名就像文件系统中的绝对路径。它是标识符的全名；因此，对于引用哪个标识符没有歧义。完全限定名以反斜杠开头。例如，`My\Foo\Bar`在名称空间`My\Foo`中寻找类`Bar`。
2.  **限定名**限定名就像文件系统中的相对路径。它只包含名称的一部分，并相对于当前命名空间进行解析。例如，名称空间`MyBaz`中的`Foo\myFunction()`解析为`\My\Baz\Foo\myFunction()`。
3.  **非限定名**
    非限定名与限定名相同，只是它只引用当前的名称空间，而不引用任何子名称空间。例如，名称空间`My\Foo\Blog`中的`getPosts()`解析为`Blog\getPosts()`。如果在当前范围内没有找到函数或常量，PHP 不会寻找`\My\Foo\getPosts()`，而是将标识符解析到全局名称空间。

让我们看一个例子。`file1.php`包含两个模拟定义，命名空间为`\MyProject\Blog`。

```
<?php
namespace \MyProject\Blog;
class myClass {}
function myFunction() {}
```

现在`file2.php`声明了`MyProject`名称空间，包含了`file1.php`，并定义了看似冲突的模拟定义。但是，这些定义没有，因为它们位于单独的名称空间中。然后，该文件继续展示如何解析完全限定名、限定名和非限定名的例子。

```
<?php
namespace \MyProject;
require_once "file1.php";
class myClass {}
function myFunction() {}

// fully-qualified names
\MyProject\myFunction();
\MyProject\Blog\myFunction(); 

// qualified name
\Blog\myFunction(); //resolves to \MyProject\Blog\myFunction();

// unqualified name
$test = new myClass(); // resolves to \MyProject\myClass
myFunction(); // resolves to \MyProject\myFunction
```

注意，要在全局命名空间中引用类和接口，必须以反斜杠开始它们的名称，即使用它们的完全限定名。除非在当前范围内找到同名的标识符，否则函数和常量不需要这样做。为了说明这一点，请考虑以下示例:

```
<?php
namespace \Foo\Bar;
function file_exists($filename) { return true; }

// Incorrect: "Fatal error: Class '\Foo\Bar\ArrayObject' not found..."
$obj = new ArrayObject();
// Correct
$obj = new \ArrayObject();

// Doesn't need backslash; resolves to native function
echo strlen("Hello, world!");
// Resolves to dummy function, \Foo\Bar\file_exists()
echo file_exists('non-existent-file'); // True
// Resolves to real function
echo \file_exists('non-existent-file'); // False
```

## `use`关键字

在包含许多深度嵌套的名称空间的大型应用程序中，写出标识符名称可能会变得很麻烦。为了避免这种情况，您可以使用`use`关键字导入名称空间、类和接口。这使得它们可以通过别名而不是全名来引用。use 语句必须在文件的最外层范围内声明。

这里有一个例子:

```
<?php
use \Very\Long\Namespace;
use \Another\Long\Namespace\ClassName;

// resolves to \Very\Long\Namespace\aFunction();
Namespace\aFunction();
// resolves to \Another\Long\Namespace\ClassName
$obj = new ClassName();
```

别名只影响非限定名和限定名；完全限定名不是。因此，以下内容不会受到已定义别名的影响:

```
<?php
$obj = new \ClassName();
```

要指定特定的别名，请使用关键字`as`后跟别名。

```
<?php
use \Very\Long\Namespace as MyNamespace;
// resolves to \Very\Long\Namespace\ClassName
$obj = new MyNamespace\ClassName();
```

为了方便起见，PHP 允许用逗号分隔多个名称空间声明。

```
<?php
use \Very\Long\Namespace as MyNamespace,
    \Another\Long\Namespace\ClassName;
```

要显式使用当前名称空间并避免别名，请在标识符名称前使用`namespace`关键字。

```
<?php
// resolves to current namespace, not \Another\Long\Namespace\ClassName
$obj = new namespace\ClassName();
```

## 动态特征

PHP 也为动态使用名称空间提供了一些支持。神奇的`__NAMESPACE__`常量是由 PHP 在编译时定义的，它以字符串的形式包含当前的名称空间。在全局命名空间中，`__NAMESPACE__`包含一个空字符串。

命名空间标识符可以像普通标识符一样被动态引用，只有一个例外:标识符只能用完全限定名来引用。例如:

```
<?php
$class = '\MyProject\Blog\MyClass';
$obj = new $class();
```

当反斜杠在双引号字符串中时，您应该总是对它们进行转义，因为某些字符组合有特殊的含义。例如，“n”是换行符，“t”是制表符，等等。不这样做可能会导致意想不到的结果。例如，下面的代码将不会像预期的那样工作:

```
<?php
// "\n" in "\newFunction" seen as newline
$function = "\MyProject\Blog\newFunction";
$function(); // Fatal error: "Call to undefined function..."
```

正确转义的字符串是`"\\MyProject\\Blog\\newFunction"`。

## 摘要

名称空间是一种强大的语言特性，可以用来有效地对应用程序中的组件进行分组，而不用担心冲突。本文介绍了使用名称空间的原因以及定义和使用名称空间的语法。您还看到了`use`关键字如何允许您给名称空间起别名，以及如何动态地使用命名空间类和函数。现在，您应该对名称空间有了完整的理解，并准备开始在自己的应用程序中使用它们。

## 分享这篇文章