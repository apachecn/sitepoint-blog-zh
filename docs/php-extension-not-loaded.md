# 如何在运行时处理卸载的 PHP 扩展

> 原文：<https://www.sitepoint.com/php-extension-not-loaded/>

除非你正在创建非常简单的应用程序，否则你很快就会需要一个或多个 PHP 扩展。扩展是扩展语言核心功能的代码库。

## 加载扩展

出于本文的目的，我们假设您需要创建或操作图像，并且需要 PHP 的 GD 库。要在您的系统上加载 GD 库:

*   对于 Windows，您需要在 php.ini 配置文件中包含/取消注释行 **extension=php_gd2.dll** 。
*   对于 Linux/Unix，应该使用 PHP 配置选项`--with-gd`。
*   对于麦克 OS X …呃，你最好谷歌一下。很抱歉没有提供具体的细节，但似乎有多种方式来配置 GD 支持。这取决于您的 OS X 版本，以及您使用的是内置的 web 服务器还是自定义安装的 Apache。

但是，当您希望将 web 应用程序迁移到另一个配置了不同扩展集的主机或平台时，会发生什么情况呢？

## 检查是否加载了扩展

PHP 提供了一个[**extension _ loaded**(*name*)](http://www.php.net/manual/en/function.extension-loaded.php)函数，当指定的库可用时，该函数返回 true，例如

```
 <?php
if (extension_loaded('gd')) {
	echo 'GD extension is loaded and everything is fine!';
}
else {
	echo 'Where is the GD library?';
	exit();
}
?> 
```

或者，您可以使用[**function _ exists**()](http://www.php.net/manual/en/function.function-exists.php)来检查特定库函数的存在，例如

```
 <?php
if (function_exists('gd_info')) {
	echo 'gd_info() is available so the GD library is probably available.';
}
else {
	echo 'gd_info() cannot be found?';
	exit();
}
?> 
```

然而，function_exists()风险更大——另一个开发人员可以编写他们自己的名为“gd_info”的函数。

我建议在 PHP 的更高版本中引入函数的情况下使用 function_exists()。例如，如果您的应用程序同时在 PHP4 和 PHP5 中运行，您可以在尝试修改图像之前检查是否存在 [imagefilter()](http://www.php.net/manual/en/function.imagefilter.php) (一个 PHP5 专用的 GD 函数)。

## 处理卸载的扩展

PHP 提供了一个 [dl()](http://www.php.net/manual/en/function.dl.php) 函数，用于在运行时动态加载扩展。不幸的是:

*   它可能在您的系统上被禁用
*   必须通过引用文件名来加载扩展名；这些因平台而异
*   有点不稳定…
*   所以它在 PHP 5.3 中被弃用了
*   并将从 PHP6 中移除。

我建议避免使用 dl()。这给了我们三个选择:

1.  在开始编码之前，确保在所有目标平台上都启用了该扩展！
2.  安装过程中某个重要扩展不可用时，提醒管理员。例如，如果没有 PDO 数据库接口，应用程序将无法运行，那么应用程序应该优雅地停止，并提供进一步的说明。
3.  提供降级体验。例如，如果 GD 库不可用，您的应用程序可以禁用图像操作。您可以在安装过程中提醒管理员，但仍然让应用程序运行。

对于处理缺失的 PHP 扩展，你还有其他的小技巧吗？

## 分享这篇文章