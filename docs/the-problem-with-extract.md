# “提取”的问题是

> 原文：<https://www.sitepoint.com/the-problem-with-extract/>

在周末的新闻中，[斯特凡·埃塞尔](http://blog.php-security.org/authors/1-Stefan-Esser)在 [PHP 安全博客](http://blog.php-security.org/) [上写了一篇强烈批评](http://blog.php-security.org/archives/5-Tips-That-Every-PHP-Developer-Should-NOT-Know.html)的新文章[每个 PHP 开发人员都应该知道的 10 个技巧，第二部分](http://www.phpbuilder.com/columns/vaska20050812.php3)(两个 T10 部分系列的一部分)。

除了这篇文章的作者杰弗里·瓦斯卡似乎不会数到十(感谢[朱尔斯](https://www.sitepoint.com/blog/)发现了这一点)之外，就斯特凡而言，这篇文章还包含了一些可疑的建议，尤其是他对一个建议表示异议，建议 5(第二个建议 5；我们遗传性地给出了两个标记为“tip 5”的提示，解释了如何使用 [extract](http://www.php.net/extract) 语言结构将 [$_POST](http://www.php.net/manual/en/reserved.variables.php#reserved.variables.post) 变量的内容提取到局部变量中。

来自斯特凡的:

> 使用 extract()而不使用前缀或参数 EXTR_SKIP 通常是一个非常大的安全漏洞，因为它允许外部攻击者覆盖每个变量，包括超级全局变量(除非您使用硬化补丁),这在许多情况下会导致 SQL 注入甚至远程代码执行漏洞

PHP 应用程序使用未初始化的变量是相对常见的(使用[进行调试会注意到](http://au2.php.net/manual/en/ref.errorfunc.php)开启是个好主意的一个原因)。如果您引用一个变量$username 而没有预先初始化它，您可能会错误地认为它的初始值将是 null。像 [register_globals](http://au.php.net/register_globals) 这样的 [extract](http://www.php.net/extract) 可以伪造这个假设，允许最终用户初始化变量$username。简而言之，就像再次打开 [register_globals](http://au.php.net/register_globals) 一样。extract 可以安全地使用，因此它本身并不是一个安全问题，但是如果与许多其他草率的编码实践结合起来，可能会对您的安全造成致命的威胁。

在我看来，[提取](http://www.php.net/extract)的最大问题，也是它是安全风险的最大原因，实际上是[提取](http://www.php.net/extract)使得代码很难阅读和调试。试着在有人使用过[提取](http://www.php.net/extract)的地方维护一些代码，你可能会发现自己在大喊‘这个变量是从哪里来的？!'变量似乎凭空出现。请考虑以下情况。

 `// see if the user is authorised`

 `$ details = $ this-> get user details()；

摘录($详情)；` 

`if ($access[$accesszone]) $this->authorise();`

$access 和$accesszone 从何而来？巫毒教！

在我看来，这种混乱的代码实践导致了代码整体安全性的真正问题。代码越难阅读，就越难调试或审查，也就越容易无意中引入安全漏洞。

## 分享这篇文章