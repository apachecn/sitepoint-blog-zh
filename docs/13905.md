# PHP 服务器 API 差异

> 原文：<https://www.sitepoint.com/php-server-api-differences/>

有用的表格: [Apache 和 IIS PHP $_SERVER 超全局比较](http://koivi.com/apache-iis-php-server-array.php)。

如果您编写了必须在多个 web 服务器环境下运行的代码(甚至 PHP 作为 CGI 与 PHP 作为 Apache 下的模块)，您可能会遇到诸如$_SERVER['SCRIPT_FILENAME']不可用之类的头痛问题——服务器 API(SAPI)各不相同。

几天前偶然发现了这个链接表，它很好地说明了潜在的问题(我假设 IIS 列是从 PHP 创建的，作为 ISS“模块”运行，而不是作为 CGI 可执行文件)。也许最关键的两个是$_SERVER['SCRIPT_FILENAME']和$_SERVER['REQUEST_URI']，如果您正在做任何“框架性”的事情，您可能会需要这两个工具，但是它们通常只对作为 Apache 模块运行的 PHP 可用。

不过，这个链接并没有引出完整的故事。我记得，当 PHP 作为 Apache 下的 CGI 时，$_SERVER['SCRIPT_FILENAME']是 CGI 可执行文件的路径，*而不是*它正在执行的脚本——这涉及到对一个名为 __MAIN__ 的 PHP [常量的请求，以标识开始执行的脚本。](https://www.sitepoint.com/python-name-main-in-php/)

否则＄_ SERVER 数组中的各种 HTTP_ variables 可能会因请求而异——这些变量告诉您关于传入 HTTP 请求的信息。还有信息性的 X_HTTP_ 有时会出现，比如 X _ HTTP _ FORWARDED _ FOR——代理服务器之间的约定。顺便说一句，这些你都不应该相信。

使用 Apache 2 和$_SERVER['PATH_TRANSLATED']，还会有更多的乐趣——参见这里的。PHP 函数 [php_sapi_name()](http://www.php.net/php_sapi_name) 对于确定您的脚本在哪个环境下运行非常有用。这里边注——相信当 PHP 作为 apache 1.x *和* 2.x 下的一个模块运行时，它会返回“Apache”

如果你在想——“谁在乎呢？我将仅仅依靠好的旧$_SERVER['PHP_SELF']对吗？”那么你应该读一下[这个](http://blog.phpdoc.info/archives/13-XSS-Woes.html)——在谷歌上搜索“表单教程 PHP_SELF”简直是噩梦。

已经做了一些工作来提供特定于 SAPI 的功能，以帮助“理解”环境的各个部分(例如， [Apache](http://www.php.net/manual/en/ref.apache.php) 和 [NSAPI](http://www.php.net/manual/en/ref.nsapi.php) 允许您获得关于传入请求的更多信息),但是，在我看来，底线是如果您正在编写应该在多个 SAPI 下运行的代码，就会有一个雷区。

现在没有任何特定的答案或结论。PHP 是一种*跨*平台的技术，而不是平台*独立*。在我看来，第一个问题是，似乎没有任何明确的文档说明差异在哪里。有一堆关于[预定义变量](http://www.php.net/manual/en/reserved.variables.php)的提示附在手册上，但大多数都是“这对我有用”的那种。在一个完美的世界中，所有这些都可能隐藏在一个 API 后面，这个 API 可以神奇地解决所有问题，但是我不知道有什么东西可以提供这种功能。

想法/观点/链接等。？

## 分享这篇文章