# PHP 中的动态全局函数

> 原文：<https://www.sitepoint.com/dynamic-global-functions-in-php/>

像许多其他人一样，我更喜欢使用[过程化 PHP 作为模板语言](http://thephppro.com/articles/pte.php)。虽然 PHP 的语法使它成为一个实用的选择，但是嵌入动态内容有一个问题。大多数 PHP 应用程序产生 HTML 输出，所以你最终使用这种技术写了很多的`<?php echo htmlspecialchars($foo);?>`。或者你忘了它，让你的应用程序容易受到各种讨厌的 XSS 攻击 T4。

除了多余输入的烦恼，还有变懒的危险，因为`<?php echo $foo;?>`输入起来非常短。在某些情况下，它本身也不会成为问题，因为某些内容类型从不包含 HTML 特殊字符(例如数字)。这尤其令人讨厌，因为视图层中的错误是出了名的难以追踪，而且不像 [SQL 注入](http://imgs.xkcd.com/comics/exploits_of_a_mom.png)——一个类似的问题——其后果往往会伤害网站的用户，而不是直接伤害网站。

## 吻

最近，我看了一些为 [CakePHP](http://www.cakephp.org/) 写的代码。我注意到了一个函数`e`，它是`echo`的简写。单个字母的正则函数无疑是扩展 PHP 语法的最简单方式。想想看，这是相当明显的，但我从来没有想到过。好吧，CakePHP 开发者犯了一个错误，因为它应该是`echo htmlspecialchars`的简写。尽管如此，*语法*工作得很好。所以我开始使用一个全局定义的函数，看起来有点像这样:

```
 function e($string) {
  echo htmlspecialchars($string);
} 

```

这也很有效。它节省了我键入*和*的时间，使我不会忘记对输出进行转义，因为我必须做更多的*工作来输出未转义的字符串。简单，但功能强大。*

## 冲突

但是有一个问题。既然这是一个如此好的函数名称，那么其他人可能会用它来做不同的事情，甚至可能是相同的事情。自从我从 CakePHP 得到这个想法以来，我已经知道至少有一个潜在的域名冲突。

处理这种情况的通常方法是使用名称空间，但是——唉——PHP 没有名称空间([还没有](http://marc.info/?l=php-internals&m=118355320225178)),常见的解决方案[伪名称空间](http://pear.php.net/manual/en/standards.naming.php)(例如，在名称前加前缀)在这里不起作用，因为这违背了函数的初衷。

还有另一个问题。在少数情况下，当我们不呈现 HTML/XML 输出时，我们不想转义字符串以嵌入到 HTML/XML 中——相反，我们想转义字符串以嵌入到目标语言中。即使在 HTML 中，如果文本编码不是 ISO-8859-1，也可能需要对带有 [htmlentities](http://www.php.net/htmlentities) 的字符串进行转义，而不是对 [htmlspecialchars](http://www.php.net/htmlspecialchars) 进行转义。或者将字符串编码为 UTF-8，如果模板是 UTF-8 的话。

## 将静态变为动态

所有这些的问题是，函数是静态的——这是 PHP 中全局函数的本质。其他解释语言允许我们在运行时重定义函数，但是 PHP 就没有这样的运气了(严格来说， [runkit](http://www.php.net/runkit) 允许这样做，但是没有一个头脑正常的人会在生产环境中使用它)。
然而有一个漏洞；使用[回调](http://www.php.net/callback)，我们可以委托给一个动态定义的处理程序:

```
 if (!function_exists('e')) {
  function e($args) {
    $args = func_get_args();
    return call_user_func_array($GLOBALS['_global_function_handler_e'], $args);
  }
} 

```

好多了—现在我可以将自己的输出处理程序定义为:

```
 $GLOBALS['_global_function_handler_e'] = 'my_global_function_handler_e';
function my_global_function_handler_e($string) {
  echo htmlspecialchars($string);
} 

```

CakePHP 可以使用:

```
 $GLOBALS['_global_function_handler_e'] = 'cakephp_global_function_handler_e';
function cakephp_global_function_handler_e($string) {
  echo htmlspecialchars($string);
} 

```

我们仍然使用一个全局符号，但至少它是动态的，而不是静态的。

因此，这里有一个对 PHP 世界的框架编写者的请求:如果我们都同意对任何函数都这样做**，在全局范围内定义，这显然有名称冲突的风险**，我想我们都会更好。

当然只是一个小小的建议。

## 分享这篇文章