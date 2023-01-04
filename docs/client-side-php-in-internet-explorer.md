# Internet Explorer 中的客户端 PHP

> 原文：<https://www.sitepoint.com/client-side-php-in-internet-explorer/>

在摆弄 PHP 5.0.0 的时候，我发现终于到了为 PHP 开发的时候了。疯狂永远不会结束吗？；)

简而言之，微软在 Windows 中提供了一种“嵌入”脚本引擎(例如 PHP)的机制，从而允许执行其他语言的代码。更多信息可以在 Windows 脚本界面的 [MSDN 上找到。](http://msdn.microsoft.com/library/default.asp?url=/library/en-us/script56/html/scripting.asp)

对于 PHP 来说，最好的信息来源是广泛的 README。

这里有一个简单的例子，它应该可以在 Win 2000 / Win XP 上第一次运行。

1.下载 [PHP 5](http://www.php.net/downloads.php) 并解压到某个地方(例如 C:PHP-5 . 0 . 0—**你需要创建这个目录！**)

2.从命令提示符；

 `C:> cd php-5.0.0
C:php-5.0.0> regsvr32 php5activescript.dll`

就是这样。现在好戏开始了。

创建一个(纯 HTML)网页一样；

 `<title>PHP ActiveScript</title>`