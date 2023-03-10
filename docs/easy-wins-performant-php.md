# 轻松赢得更高性能的 PHP

> 原文：<https://www.sitepoint.com/easy-wins-performant-php/>

## 介绍

让你的网站更有性能可能需要很多层。从使用 Varnish 设置反向代理缓存到配置一组负载平衡器，有许多有据可查的选项。

如果你才刚开始呢？如果你有一个更小的应用程序呢？你现在能做些什么来改变现状吗？

本文将探索更高性能 PHP 的简单成功之处，您可以将其实现为开发团队的标准实践，也可以将其实现为现有应用程序的改进原则。

## 至少升级到 PHP 5.4

截止到本文，PHP 的最新版本目前是 5.5.5。如果您对您的服务器环境有适当的控制，您应该考虑使用这个版本。虽然 5.5 引入了许多新概念，但它提供的安全更新应该是值得认真考虑的。

如果 PHP 5.5.5 不是一个选项，你必须至少从 PHP 5.4 开始。PHP 5.3 不仅快 4 年了，而且比 PHP 5.4 慢，比 PHP 5.4 用的内存多。

运行 Drupal？在 PHP 5.4 上快了 7%，而使用的内存却少了将近 50%。

## 自动装载

自动加载包括在整个应用程序中使用的文件中包含类，而不必手动引用文件路径。仅在实例化类时加载文件可以获得更好的性能。

我们可以使用 Composer 来实现这个结果。

Composer 已经在过去的文章中涉及到依赖管理。对于自动加载，只需在 composer.json 文件中添加类的位置。唯一的配置选项是由 JSON 数组中的 key 指定的文件映射机制。

例如，如果在一个`src`文件夹中有 PSR-0 格式的类:

```
{
    "autoload": {
        "psr-0": { "": "src/" }
    }
}
```

如果您有分散在`resources`和`library`文件夹中的类，格式会有些不同:

```
{
    "autoload": {
        "classmap": ["resources/", "library/", "Something.php"]
    }
}
```

但是，请注意，虽然这减少了使用`require`或`include_once`的需要，但是如果试图加载大量文件，性能可能会受到影响。

然而，如果您使用加速器或操作码缓存，您可以[减轻这个](http://phpixie.com/blog/benchmarking-autoloading-vs-combining-classes-into-a-single-file/)。

另一个值得探索的自动加载器是 Symfony2 Classloader 组件，这是一个在早期文章中介绍的技术。

## 减少代码中的内存使用

在使用操作符、循环或变量赋值时，有一些技巧可以使用更少的内存，并可能使整个应用程序的性能更高。

如果合适，将对象属性设为公共属性以避免不必要的方法，因为这样在执行时使用的内存较少:

```
class A {
    public $foo;
    public function setFoo($bar) { $this->foo = $bar; }
    public function getFoo() { return $this->foo; }
}

$bat = new A();

// Slower
$bat->setFoo('baz');
$baz = $bat->getFoo();

// Faster
$bat->foo = 'baz';
$baz = $bat->foo;
```

迭代前定义循环的大小:

```
// Slower
for ($i = 0; $i < count($j); $i++)

// Faster
$count = count($j);
for ($i = 0; $i < $count; $i++)
```

在任何操作之前使用语言结构`isset()`:

```
// Check for existence and type of variable
if (isset($foo) && is_array($foo)) { return $bar; }
```

*编辑:`isset()`的用法已被编辑，以纠正一个不正确的建议。之前的代码样本是基于我对 http://phpbench.com[的基准测试的误解。](http://phpbench.com)*

这些技术是谷歌推荐的[的部分列表，并用](https://developers.google.com/speed/articles/optimizing-php)[基准](http://www.phpbench.com/)进行了记录。很明显，它们依赖于数据值的大小和代码的复杂性，但是一开始就养成良好的习惯会减少将来的麻烦。

## 轮廓

对你的杰作运行情况的客观测量提供了有价值的洞察力，减少了主观干扰。剖析代码非常容易，但是需要在 PHP 运行时编译一个扩展。

您可以使用 [XDebug 扩展](https://www.sitepoint.com/debugging-and-profiling-php-with-xdebug/)或[高级 PHP 调试器(APD)](http://us1.php.net/manual/en/book.apd.php) 扩展来分析您的代码。只需更新您的 php.ini 或为您的扩展添加一个带有适当配置选项的 ini 文件:

OSX w/ brew 的 XDebug ini 配置示例:

```
[xdebug]
zend_extension="/usr/local/Cellar/php54-xdebug/2.2.3/xdebug.so"   
xdebug.profiler_enable=1   
xdebug.profiler_output_dir="/tmp"
```

了解哪些功能或流程最耗时，并做出相应的调整。

为了获得更强大的分析和监控体验，您还可以考虑安装来自 [AppDynamics](http://www.appdynamics.com/?utm_source=sitepoint&utm_campaign=sitepoint%20partnership%202013&utm_medium=referral&utm_term=partnership%202013%7Csponsored%20article) 的工具，当这些工具作为服务器守护进程和 PHP 扩展安装时，可以对复杂的大型应用程序进行深入分析。这是优化遗留代码的一个很好的选择。

## 操作码缓存

最后但并非最不重要的一点是，从内存中执行代码是屡试不爽的。这是高性能的，因为它减少了从磁盘读取代码并编译它的需要。

从 PHP 5.5 开始(更新的另一个原因)，Zend 的开源缓存项目 [OpCache](http://php.net/manual/en/book.opcache.php) 默认包含**。**

 **对于早期版本，你只需要通过 PECL 安装 [ZendOpCache](http://pecl.php.net/package/ZendOpcache) 。

OSX w/ brew 上的 ZendOpCache ini 配置示例:

```
[opcache]
zend_extension="/usr/local/Cellar/php54-opcache/7.0.2/opcache.so"   
zend_optimizerplus.memory_consumption=128   
zend_optimizerplus.interned_strings_buffer=8   
zend_optimizerplus.max_accelerated_files=4000   
zend_optimizerplus.revalidate_freq=60   
zend_optimizerplus.fast_shutdown=1   
zend_optimizerplus.enable_cli=1   
zend_optimizerplus.enable_file_override=1   
apc.cache_by_default = false
```

## 摘要

希望上面的技术给你一个让你的 PHP 应用程序更快的起点。无论是重构代码以更好地与 PHP 5.5 兼容，还是简单地让循环使用更少的内存，现在都有很多简单的方法可以实现。别等了！

* * *

这是一篇赞助文章。赞助它的公司付钱给 SitePoint 来撰写和发表它。由 SitePoint 独立撰写的赞助文章允许我们开发和展示对读者最有用和最相关的内容。** 

## **分享这篇文章**