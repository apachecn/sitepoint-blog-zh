# 用 Icicle 在几分钟内建立一个超快的 PHP 服务器

> 原文：<https://www.sitepoint.com/build-a-superfast-php-server-in-minutes-with-icicle/>

对于 PHP 开发人员来说，基于事件的编程是一个奇怪的话题。用一种程序性的语言；事件只不过是函数调用。事件之间没有发生任何事情，所有有意义的代码仍然是阻塞的。

像 JavaScript 这样的语言向我们展示了如果事件循环处于中心位置，PHP 会是什么样子。一些人利用这些见解，将它们编码到事件循环和 HTTP 服务器中。今天我们要用 PHP 创建一个 HTTP 服务器。我们将它连接到 Apache 来快速提供静态文件。其他一切都将通过我们的 PHP HTTP 服务器，基于 Icicle。

![Icicles illustration](img/a2d627a5e1b479a65dd52a5821fab76b.png)

你可以在[https://github.com/sitepoint-editors/icicle-http-server](https://github.com/sitepoint-editors/icicle-http-server)找到示例代码

## 配置 Apache

当浏览器请求现有文件时，最好不要使用 PHP 解释器来服务它们。Apache 在处理这些文件时速度很快，效率也很高，所以让我们配置它来处理所有静态文件请求:

```
RewriteEngine on RewriteCond  %{REQUEST_FILENAME}  !-f RewriteCond  %{REQUEST_FILENAME}  !-d RewriteRule  ^(.*) http://%{SERVER_NAME}:9001%{REQUEST_URI} [P]
```

您可以将这段代码放在虚拟主机条目中。

这些`mod_rewrite`指令告诉 Apache 将丢失文件的请求发送到不同的端口。换句话说:当浏览器请求`example.com/robots.txt`时，Apache 将首先查看该文件是否存在。如果是这样，Apache 将返回它，而不启动 PHP 解释器。如果没有，Apache 会将请求发送到`http://example.com:9001/robots.txt`。

## 一个简单的 HTTP 服务器

Icicle 附带一个事件循环。我们可以用一个 HTTP 服务器来包装它，这样新的请求就会以事件的形式出现。这个过程的大部分都被抽象掉了，但是让我们看一个例子。首先，让我们下载`icicleio/http`:

```
composer require icicleio/http
```

这个给我装的版本`0.1.0`。如果你在让我的例子工作时遇到问题，你可能有一个更新的版本。请尝试安装此特定版本。

这将允许您运行以下代码:

```
// server.php

require __DIR__ . "/vendor/autoload.php";

use Icicle\Http\Message\RequestInterface;
use Icicle\Http\Message\Response;
use Icicle\Http\Server\Server;
use Icicle\Loop;
use Icicle\Socket\Client\ClientInterface;

$server = new Server(
    function(RequestInterface $request, ClientInterface $client) {
        $response = new Response(200);
        $response = $response->withHeader(
            "Content-Type", "text/plain"
        );

        yield $response->getBody()->end("hello world");
        yield $response;
    }
);

$server->listen(9001);

Loop\run();
```

## 处理不同的路线

这是一个人可以创建的最基本的 HTTP 服务器。它接收所有请求并回复“hello world”。为了让它更有用，我们需要加入某种路由器。看起来是个不错的候选人:

```
composer require league/route
```

现在，我们可以将各个请求分开，并发送更有意义的响应:

```
// server.php

use League\Route\Http\Exception\MethodNotAllowedException;
use League\Route\Http\Exception\NotFoundException;
use League\Route\RouteCollection;
use League\Route\Strategy\UriStrategy;

$server = new Server(
    function(RequestInterface $request, ClientInterface $client) {
        $router = new RouteCollection();
        $router->setStrategy(new UriStrategy());

        require __DIR__ . "/routes.php";

        $dispatcher = $router->getDispatcher();

        try {
            $result = $dispatcher->dispatch(
                $request->getMethod(),
                $request->getRequestTarget()
            );

            $status = 200;
            $content = $result->getContent();
        } catch (NotFoundException $exception) {
            $status = 404;
            $content = "not found";
        } catch (MethodNotAllowedException $exception) {
            $status = 405;
            $content = "method not allowed";
        }

        $response = new Response($status);
        $response = $response->withHeader(
            "Content-Type", "text/html"
        );

        yield $response->getBody()->end($content);
        yield $response;
    }
);
```

我们已经拉进了`League\Route`，并启用了`UriStrategy`。这是确定哪个路由属于哪个请求的四种不同方法之一。`League\Route`常与请求和应答连用。我们需要将请求方法和路径/目标提供给调度程序。

如果一个路由匹配，我们得到一个 Symfony\HttpFoundation 响应，所以我们得到带有`getContent`的正文内容。如果没有匹配的路由，或者匹配路由允许的方法，那么我们返回适当的错误。那么`routes.php`长什么样？

```
$router->addRoute("GET", "/home", function() {
    return "hello world";
});
```

## 渲染复杂视图

字符串适用于简单的页面。但是当我们开始构建更复杂的应用程序时，我们可能需要一个更好的工具。我们用`League\Plates`怎么样？它是一个模板引擎，在普通 PHP 的基础上增加了布局和模板继承等功能。

```
composer require league/plates
```

然后，我们将创建一个布局模板，供我们站点中的所有视图继承:

```
<!doctype html>
<html lang="en">
    <head>
        <meta charset="utf-8" />
        <title>
            <?php print $this->e($title); ?>
        </title>
    </head>
    <body>
        <?php print $this->section("content"); ?>
    </body>
</html>
```

这是来自`templates/layout.php`。

`e`方法转义 HTML 实体。`section`方法将是页面内容被呈现的地方:

```
<?php $this->layout("layout", ["title" => "Home"]); ?>
<p>
    Hello, <?php print $this->e($name); ?>
</p>
```

以上出自`templates/home.php`。

最后，我们改变我们的`/home`路径，返回一个呈现的模板，而不是一个简单的字符串:

```
$router->addRoute("GET", "/home", function() {
    $engine = new League\Plates\Engine(
        __DIR__ . "/templates"
    );

    return $engine->render("home", [
        "name" => "Chris"
    ]);
});
```

以上出自`routes.php`。

当然，我们可以创建一个快捷函数，使我们不必每次都创建引擎:

```
function view($name, array $data = []) {
    static $engine = null;

    if ($engine === null) {
        $engine = new League\Plates\Engine(
            __DIR__ . "/templates"
        );
    }

    return $engine->render($name, $data);
}
```

以上出自`helpers.php`。

…如果我们包括它(或将它添加到 Composer 自动加载定义中)，那么我们的`/home`路线就变成了:

```
$router->addRoute("GET", "/home", function() {
    return view("home", [
        "name" => "Chris"
    ]);
});
```

## 结论

我们已经设法用`Icicle\Http`和几个联盟库拼凑了一个合理的应用框架。希望这向您展示了 Apache(或 Nginx)之外的生活是可能的。而这仅仅是开始…

我获得了以下数据(在 13 英寸 Macbook Pro Retina 2014 上运行 Chrome 和 iTunes 时):

```
Concurrency  Level:  100  Time taken for tests:  60.003 seconds Complete requests:  11108  Failed requests:  0  Total transferred:  3810044 bytes
HTML transferred:  2243816 bytes Requests per second:  185.12  [#/sec] (mean)  Time per request:  540.182  [ms]  (mean)  Time per request:  5.402  [ms]  (mean, across all concurrent requests)  Transfer rate:  62.01  [Kbytes/sec] received
```

我想这些数字会随着复杂性的增加而波动，与流行的框架相比，它们没有任何意义。重点是这个小小的基于事件的 HTTP 服务器可以在一分钟内服务 11.1k 个请求，没有失败。如果您小心避免内存泄漏，您可以由此创建一个稳定的服务器！

那是令人兴奋的，不是吗？

你对这个设置有什么想法？你玩过冰柱吗？让我们知道！

* * *

编辑:Aaron Piotrowski，Icicle 的作者提供了一些额外的信息，解释了为什么上面的基准测试可能有缺陷(也在评论中讨论了)。以下是他的(更令人印象深刻的)结果。他说:

*“我使用`ab -n 10000 -c 100 http://127.0.0.1:9001/home`*命令获得了以下统计数据(在 3.4 GHz i7 iMac 上运行 iTunes、Chrome 和其他几个程序时):

```
Concurrency  Level:  100  Time taken for tests:  5.662 seconds Complete requests:  10000  Failed requests:  0  Total transferred:  2650000 bytes
HTML transferred:  2020000 bytes Requests per second:  1766.04  [#/sec] (mean)  Time per request:  56.624  [ms]  (mean)  Time per request:  0.566  [ms]  (mean, across all concurrent requests)  Transfer rate:  457.03  [Kbytes/sec] received
```

我想这些数字会随着复杂性的增加而波动，与流行的框架相比，它们没有任何意义。关键是，这个基于事件的小 HTTP 服务器有可能在一分钟内处理超过 100，000 个请求，而不会出现故障。如果您小心避免内存泄漏，您可以由此创建一个稳定的服务器！

谢谢你的插话，亚伦！

## 分享这篇文章