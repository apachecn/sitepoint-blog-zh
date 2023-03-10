# PHP 5.4 新的内置网络服务器

> 原文：<https://www.sitepoint.com/php-54-web-server/>

PHP 5.4 中提供的一个更有趣的工具是一个内置的 web 服务器。它在 Windows、Mac 或 Linux 上从命令行运行。您只需要“cd”到应用程序所在的文件夹，然后执行:

```
 php -S localhost:8000 
```

这将启动一个基于控制台的 web 服务器。文档根目录位于当前文件夹中:

```
PHP 5.4.0 Development Server started at Mon Dec 19 11:56:05 2011
Listening on localhost:8000
Document root is /home/owner/myapp
Press Ctrl-C to quit

```

然后，您可以在浏览器中打开 http://localhost:8000/如果你没有明确地包括一个文件名，服务器将在根文件夹中返回 index.php 或 index.html。所有文件请求都记录在控制台窗口中。

也可以在命令行上指定一个 PHP“路由器”文件，例如

```
 php -S localhost:8000 -t ./home/owner/myapp routing.php 
```

服务器分析 routing.php 的输出:

*   如果脚本返回 false，则所请求的资源(或 404)按原样返回。
*   如果脚本返回任何其他内容，则输出将返回给浏览器。

例如，下面的路由器脚本将返回图像文件，但是所有其他请求将显示“欢迎使用 PHP 服务器！”：

```
 <?php
if (preg_match('/.(?:png|jpg|jpeg|gif)$/', $_SERVER['REQUEST_URI']))
	return false; // serve the requested image
else {
	echo "<p>Welcome to the PHP Server!</p>";
} 
```

PHP 5.4 的 web 服务器旨在用于开发目的，我怀疑它将被文本编辑器、ide 和可能的浏览器插件采用，作为测试 PHP 代码的一种简单方法。

没有理由不在小型内部服务器上使用它——有些人会尝试——但是肯定有更合适的解决方案。然而，我们可以看到一些有趣的用途，比如可以从 CD 上运行的 web 应用程序演示。

[PHP 5.4 RC1](http://downloads.php.net/stas/) 近日发布。我怀疑你需要等很久才能看到最终版本。

## 分享这篇文章