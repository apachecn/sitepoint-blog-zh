# 反应过来的第一眼

> 原文：<https://www.sitepoint.com/a-first-look-at-react/>

在过去的几年里， [Node.js](http://nodejs.org/ "node.js") 作为一项有前途的网络技术吸引了越来越多的关注。虽然它有一些优点，比如是事件驱动的，但是有些人只想坚持使用 PHP。然而，一年多以来，有一个类似的 PHP 项目被命名为 [React](http://reactphp.org/ "React") 。

React 主要由 Igor Wiedler 编写，他也是 T2 Silex 框架的重要贡献者。在阅读 React 示例时，它看起来确实与 Node.js 相似。

像 Node.js 一样，我们鼓励您编写异步执行的应用程序。对于那些可能不熟悉异步编程的人来说，服务器用单线程来响应请求，但使用工作线程来执行耗时的任务，如写入文件、从数据库读取、从 Twitter API 检索结果等。当任务完成时，worker 将结果返回给最终完成请求的主线程。这种编程风格可以极大地提高速度。

## 做出反应

React 可以通过 Packagist 与 Composer [一起使用(如果你不熟悉使用 Composer，请查看我的](https://packagist.org/packages/react/react "react/react - Packagist")[以前的文章](https://www.sitepoint.com/re-introducing-composer/ "PHP Dependency Management with Composer"))。要安装 React，请使用以下内容创建一个新的`composer.json`文件:

```
{
"require": {
"react/react": "0.2.*"
}
}
```

打开控制台，导航到文件所在的位置，然后运行`php composer install`。React 及其依赖项将被检索，然后可以通过包含`vendor/autoload.php`文件来使用。

## 创建简单的服务器

使用 Node.js，您可以自己创建服务器脚本，而不是使用 Apache 或 Nginx 之类的服务器应用程序。您设置一个事件函数，定义一个您的服务器将监听的端口，然后等待请求。每个请求都会执行事件函数。React 里也是这样。

这个例子摘自 React 项目的`README.md`文件:

```
<?php
require_once 'vendor/autoload.php';

$i = 0;
$app = function ($request, $response) use (&$i) {
$i++;
$text = "This is request number $i.n";
$headers = array('Content-Type' => 'text/plain');

$response->writeHead(200, $headers);
$response->end($text);
};

$loop = ReactEventLoopFactory::create();
$socket = new ReactSocketServer($loop);
$http = new ReactHttpServer($socket);

$http->on('request', $app);

$socket->listen(1337);
$loop->run();
```

首先，它包含 Composer autoloader 文件，然后将变量`$i`设置为零，这将用于对请求进行计数。然后，这在 PHP 社区中并不常见，我们的事件函数将处理所有传入的请求，并存储到一个变量中。该函数设置响应的头，并打印已处理请求的数量。

该脚本创建了一个事件循环，为我们处理未阻塞的 I/O，一个套接字，然后是服务器。最后，分配事件函数，将套接字设置为监听端口 1337，然后运行事件循环。

如果您将代码保存为`server.php`并使用`php server.php`从命令行运行它，然后将浏览器导航到 *http://localhost:1337* ，您将看到响应。不要忘记检查您的命令行，看看已经处理了多少请求！

## 进一步探索反应

React 可以做的不仅仅是充当 HTTP 服务器。我们可以用它来做一些更酷的事情。想想这个例子:你是中情局的间谍，你想把数据传给你的老板。老板创建一个服务器，你通过一个套接字作为客户端连接，然后你输入的所有内容都记录在服务器的日志中。

对套接字的支持由 React 中的 socket 组件处理，对于这个示例，您不需要完整的 React 库。以下`composer.json`应该足够了:

```
{
"require": {
"react/socket": "0.2.*"
}
}
```

这是示例代码:

```
<?php
require_once 'vendor/autoload.php';

$loop = ReactEventLoopFactory::create();
$socket = new ReactSocketServer($loop);

$socket->on('connection', function ($conn) use ($loop) {
$conn->write("Successfully connected to the writing servern");
echo 'client connected';
$dataStream = new ReactStreamStream(fopen('data.txt', 'w'), $loop);

$conn->on('data', function($data) use ($conn, $dataStream) {
$dataStream->write($data);
});

$conn->on('end', function() {
echo 'connection closed';
});
});

$socket->listen(4000);
$loop->run();
```

我们再次创建一个事件循环，然后创建一个套接字。当发出连接事件时，我们向控制台打印一条消息。我们创建一个新的写入流，并将其命名为`$dataStream`,当我们接收到数据时，我们将它推送到流中，数据将被写入我们的文件。当客户端断开连接时，流被关闭。

运行`php server.php`后，您可以使用`nc localhost 4000`进行连接。你输入的所有内容以及之后按下的回车键都会被记录到`data.txt`中。

## 谁会使用 React？

React 提供了一些非常棒的功能，但是你为什么要使用一个还不稳定的年轻库，而不是更成熟的 Node.js，它也有一个很大的社区呢？我自己也在想这个问题，所以我问伊戈尔·威德勒:

> 我不建议在生产环境中运行 react。一些较低层次的部分相当稳定，较高层次的东西不那么稳定。现在的目标受众是那些喜欢尝试前沿技术，并且不怕在出现问题时进行调试的人。我特别感兴趣的是基于 react 事件循环的新库，这样它们就可以一起使用了。

## 结论

React 是一个有趣的项目吗？我认为是这样的，即使它只是移植 Node.js 的功能，但它是以一种很酷的方式这样做的，而且谁会想到在 PHP 中也可以做这样的事情呢？至少我没有。

React 似乎是一个有前途的项目，尽管它还没有准备好投入生产，但它似乎有一些很酷的功能和一个很好的开发人员来维护它。当然，我无法在这篇短文中解释 React 的每个方面，所以如果您想了解更多关于 React 的内容:

*   查看韦德勒的演示文稿。
*   在 [React 网站](http://reactphp.org/ "React")上浏览示例。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章