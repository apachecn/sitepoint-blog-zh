# PHP4 的“克隆”

> 原文：<https://www.sitepoint.com/clone-for-php4/>

来自 Acko.net[的 Steven Wittens](http://www.acko.net/) 描述了[在 PHP4](http://www.acko.net/node/54) 中实现克隆功能的一种方式，以模仿 PHP5 的[克隆](http://www.php.net/manual/en/language.oop5.cloning.php)行为。

这个想法是编写在 PHP4 和 PHP5 中行为相同的面向对象的程序。显然，这样做的缺点是不能利用 PHP5 特有的特性，比如对象析构函数方法。但是如果你接受这些限制，你仍然可以为 PHP4 编写在 PHP5 上运行的代码，但是有一些重要的障碍。PHP5 对对象和引用的处理就是其中之一。

在 PHP4 中，您可以单独使用等号(=)操作符来创建对象的副本。然而，在 PHP5 中，这将创建对同一对象的另一个引用(很像 Java)。PHP5 定义了 [clone](http://www.php.net/manual/en/language.oop5.cloning.php) 关键字，可用于创建对象的单独副本。

PHP5 的[克隆](http://www.php.net/manual/en/language.oop5.cloning.php)严格来说不是一个函数。它是一个语言构造，和其他一些语言构造一样，比如 [exit](http://www.php.net/) 和 [include](http://www.php.net/include) ，它可以带括号也可以不带括号。如果我们要在 PHP 中使用一个函数来模拟它，我们将总是需要用圆括号括起来。

Steven 的方法在使用 PHP4 时将“克隆”定义为一个函数，但仍然允许在 PHP5 中运行时使用 PHP5 的内置[克隆](http://www.php.net/manual/en/language.oop5.cloning.php)。

他的方法的一个弱点是依赖于 eval，我认为这是一个不优雅的解决方案(见 T2·拉克伦对 eval 的咆哮)。这里使用 eval 是为了防止在使用 PHP5 时函数被定义(在 PHP5 中是保留字)。

如果您真的不关心 eval，一个可能的解决方案是使用条件包含。将 clone 的函数定义放在一个单独的文件 php4.inc.php 中，只有在不使用 PHP5 时才包含该文件。

因此，下面的代码应该在不使用 eval 的情况下提供 PHP5 的克隆的 PHP4 模拟:

php4.inc.php

 `// Define a PHP4 emulation of PHP5's clone
function clone($object)
{
return $object;
}`

您的应用:

 `if (version_compare(phpversion(), '5.0') < 0)
{
include 'php4.inc.php';
}`

 `//比如我们可以创建一个对象
$ object =&new；object()；` 

`// Clone it!
$myclone = clone($object);`

如果你不想克隆一个对象呢？如果您在 PHP4 中做过 OOP，那么您将会熟悉引用操作符的用法&向对象传递引用。好消息是，在 PHP5 中，这将继续工作。

另一方面，我们从你那里得到了很多关于[是什么阻碍你完全迁移到 PHP5](https://www.sitepoint.com/blog/) 的反馈。各种各样的问题包括 web 主机不想迁移、兼容性的破坏(特别是本文中讨论的克隆问题)和稳定性问题，而其他用户报告说他们很高兴只使用 PHP5，并且没有回头看。

## 分享这篇文章