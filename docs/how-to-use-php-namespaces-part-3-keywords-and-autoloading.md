# 如何使用 PHP 名称空间，第 3 部分:关键字和自动加载

> 原文：<https://www.sitepoint.com/how-to-use-php-namespaces-part-3-keywords-and-autoloading/>

在本系列的第 1 部分和第 2 部分中，我们研究了 PHP 的名称空间基础知识、[使用操作符和名称解析](https://www.sitepoint.com/php-namespaces-import-alias-resolution/)。在这最后一篇文章中，我们将讨论一些更高级的名称空间选项。

## __NAMESPACE__ 常量

__NAMESPACE__ 是一个 PHP 字符串，它总是返回当前的名称空间名称。在全局空间中，它将是一个空字符串。

```
 <?php
namespace App\Lib1;
echo __NAMESPACE__; // outputs: App\Lib1
?> 
```

该值在调试过程中有明显的好处。它还可以用于动态生成完全限定的类名，例如

```
 <?php
namespace App\Lib1;

class MyClass {
	public function WhoAmI() {
		return __METHOD__;
	}
}

$c = __NAMESPACE__ . '\\MyClass';
$m = new $c;
echo $m->WhoAmI(); // outputs: App\Lib1\MyClass::WhoAmI
?> 
```

## 名称空间关键字

`namespace`关键字可用于显式引用当前名称空间或子名称空间中的项目。它是类中`self`的名称空间等价物:

```
 <?php
namespace App\Lib1;

class MyClass {
	public function WhoAmI() {
		return __METHOD__;
	}
}

$m = new namespace\MyClass;
echo $m->WhoAmI(); // outputs: App\Lib1\MyClass::WhoAmI
?> 
```

## 自动加载命名空间类

PHP 5 最节省时间的特性之一是自动加载。在全局(非命名空间)PHP 代码中，可以编写一个标准的自动加载函数:

```
 <?php
$obj1 = new MyClass1(); // classes/MyClass1.php is auto-loaded
$obj2 = new MyClass2(); // classes/MyClass2.php is auto-loaded

// autoload function
function __autoload($class_name) {
	require_once("classes/$class_<wbr>name.php");
}
?> 
```

在 PHP 5.3 中，你可以创建一个命名空间类的实例。在这种情况下，完全限定的名称空间和类名被传递给 __autoload 函数，例如$class_name 的值可以是“AppLib1MyClass”。您可以继续将所有 PHP 类文件放在同一个文件夹中，并从字符串中去掉名称空间，但是，这可能会导致文件名冲突。

或者，您的类文件层次结构可以用与名称空间结构相同的方式来组织。例如，可以在文件夹/classes/App/Lib1 中创建一个 MyClass.php 文件:

**\ class \ App \ lib 1 \ my class . PHP:**

```
 <?php
namespace App\Lib1;

class MyClass {
	public function WhoAmI() {
		return __METHOD__;
	}
}
?> 
```

然后，根文件夹中的文件可以使用以下代码:

**myapp.php:**

```
 <?php
use App\Lib1\MyClass as MC;

$obj = new MC();
echo $obj->WhoAmI();

// autoload function
function __autoload($class) {

	// convert namespace to full file path
	$class = 'classes\' . str_replace('\\', '/', $class) . '.php';
	require_once($class);

}
?> 
```

解释:

1.  类 AppLib1MyClass 别名为 MC。
2.  `new MC()`在编译时被翻译成`new AppLib1MyClass()`。
3.  字符串“AppLib1MyClass”被传递给 __autoload 函数。这将所有名称空间反斜杠替换为文件路径正斜杠，并修改字符串，以便加载文件“classes appli 1 my class . PHP”。

我希望这一系列 PHP 名称空间文章对您有用。你会在你的 PHP 代码中使用命名空间吗？

另请参见:

*   [如何使用 PHP 名称空间，第 1 部分:基础知识](https://www.sitepoint.com/php-53-namespaces-basics)
*   [如何使用 PHP 名称空间，第 2 部分:导入、别名和名称解析](https://www.sitepoint.com/php-namespaces-import-alias-resolution/)
*   [如何在 Windows 上安装 PHP 5.3](https://www.sitepoint.com/install-php53-windows/)

## 分享这篇文章