# 如何使用 PHP 名称空间，第 1 部分:基础知识

> 原文：<https://www.sitepoint.com/php-53-namespaces-basics/>

名称空间是 PHP 5.3 中最重要的变化之一。C#和 Java 开发人员会很熟悉它们，它们很可能会更好地改变 PHP 应用程序的结构。

## 为什么我们需要名称空间？

随着 PHP 代码库的增大，您就越有可能意外地重用以前声明过的函数或类名。如果你试图添加第三方组件或插件，问题会更加严重；如果两个或更多的代码集实现一个“数据库”类会怎样？

到目前为止，唯一的解决方案是长类名/函数名。例如，WordPress 会在每个名字前加上“WP_”。Zend 框架使用高度描述性的命名约定，导致冗长的类名，例如 Zend _ Search _ Lucene _ Analysis _ Analyzer _ Common _ Text _ case insensitive。

名称冲突问题可以用名称空间来解决。PHP 常量、类和函数可以分组到命名空间库中。

## 名称空间是如何定义的？

默认情况下，所有常量、类名和函数名都放在一个全局空间中——就像它们在名称空间得到支持之前一样。

命名空间代码是使用 PHP 文件顶部的一个关键字`namespace`定义的。它**必须**是第一个命令(除了`declare`)，并且命令前面不能有非 PHP 代码或空格，例如

```
 <?php
// define this code in the MyProject namespace
namespace MyProject; 
```

这一行之后的代码将属于 MyProject 命名空间。不能嵌套命名空间或为同一个代码块定义两个或多个命名空间(只能识别最后一个)。但是，您可以在同一个文件中定义不同的命名空间代码，例如

```
 <?php
namespace MyProject1;
// PHP code for the MyProject1 namespace

namespace MyProject2;
// PHP code for the MyProject2 namespace

// Alternative syntax
namespace MyProject3 {
	// PHP code for the MyProject3 namespace
}
?> 
```

然而，我强烈建议为每个文件定义一个名称空间。

PHP 允许你定义一个命名空间名称的层次结构，这样库就可以被细分。子名称空间使用反斜杠(\)字符分隔，例如

*   我的项目\子名称
*   MyProject\Database\MySQL
*   公司名称\MyProject\Common\Widget

## 调用命名空间代码

在名为 lib1.php 的文件中，我们将使用 App\Lib1 命名空间定义一个常量、函数和类:

**lib1.php**

```
 <?php
// application library 1
namespace App\Lib1;

const MYCONST = 'App\Lib1\MYCONST';

function MyFunction() {
	return __FUNCTION__;
}

class MyClass {
	static function WhoAmI() {
		return __METHOD__;
	}
}
?> 
```

要调用这段代码，我们可以使用 PHP 代码，例如:

**myapp.php**

```
 <?php
header('Content-type: text/plain');
require_once('lib1.php');

echo \App\Lib1\MYCONST . "\n";
echo \App\Lib1\MyFunction() . "\n";
echo \App\Lib1\MyClass::WhoAmI() . "\n";
?> 
```

没有为 myapp.php 定义名称空间，因此代码存在于全局空间中。对 MYCONST、MyFunction()或 MyClass 的任何直接引用都将失败，因为它们存在于 App\Lib1 命名空间中。因此，我们必须添加前缀\App\Lib1 来创建完全限定的名称。当我们加载 myapp.php 时，会输出以下结果:

```
 App\Lib1\MYCONST
App\Lib1\MyFunction
App\Lib1\MyClass::WhoAmI 
```

完全限定名显然会变得很长，定义一个类名 App-Lib1-MyClass 并没有什么好处。然而，在下一篇文章中，我们将讨论别名，并进一步了解 PHP 如何解析名称空间名称。

另请参见:

*   [如何使用 PHP 名称空间，第 2 部分:导入、别名和名称解析](https://www.sitepoint.com/php-namespaces-import-alias-resolution/)
*   [如何使用 PHP 名称空间，第 3 部分:关键字和自动加载](https://www.sitepoint.com/how-to-use-php-namespaces-part-3-keywords-and-autoloading/)
*   [如何在 Windows 上安装 PHP 5.3](https://www.sitepoint.com/install-php53-windows/)

## 分享这篇文章