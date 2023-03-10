# 条件类声明——不好的做法？

> 原文：<https://www.sitepoint.com/conditional-class-declaration-bad-practice/>

在 Zend 的 [PHP5 上看到一条评论时，我感到很不安——问问专家们](http://www.zend.com/php/ask_experts.php)；

> 问:在 PHP 5 (rc1-dev)…有什么理由不能这样做吗？
>  `if (!defined('SOMETHING')) {
> class HelloWorld {
> ....
> }
> }`

> 答:这是我们知道的问题。一般来说，我们不建议对类**进行条件声明，因为这不是一个很好的编程实践**。然而，尚不清楚它是否会得到支持，也许会。

好的——一般来说，有条件地声明类，没有其他好的理由，只是因为你可以，可能是一个坏主意。

但是正如我在这里提倡的那样，有很好的理由，在我看来，为什么条件声明(即条件包含)是一个好主意，第一个理由是，当在典型的共享 PHP 主机上运行应用程序时(PHP 加速器通常不可用)，可以减少每个页面请求中包含的代码“足迹”。

如果是声明哪些文件可以包含在任何给定的脚本中，为了可读性等等。，一个简单的提示通过[杰森汗](https://www.sitepoint.com/forums/member.php?u=30025)，是这样的；

 `if ( FALSE ) {
require_once 'dbconn.php';
require_once 'logger.php';
require_once 'auth.php';
// etc.
}` 

尽管在这一点上没有一个类会被加载，但是它使得稍后脚本中可能包含的*变得很容易看到，所以它们对其他开发人员是可见的，并且很容易用[PHP document](http://www.phpdoc.org)记录。*

可能是我对一个声明反应过度了，这个声明只是需要更多的限定，但在 Zend 上看到它让我紧张，来自 PHP 的一个核心开发者。

## 分享这篇文章