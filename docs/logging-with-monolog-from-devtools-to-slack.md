# 用独白记录日志:从 Devtools 到 Slack

> 原文：<https://www.sitepoint.com/logging-with-monolog-from-devtools-to-slack/>

日志记录是应用程序开发/维护周期的重要组成部分。不仅仅是你记录的数据，还有你是怎么做的。在本文中，我们将探索 Monolog 包，看看它如何帮助我们利用我们的日志。

![big lot of document pile waiting for clear](img/3d4efd3dfd1ac0376f5eeb33f67c57ab.png)

## 装置

Monolog 在 Packagist 上可用，这意味着您可以通过 Composer 安装它。

```
composer require 'monolog/monolog:1.13.*'
```

然而，很有可能你正在为你的应用程序使用一个框架。Monolog 为大多数流行的框架提供了一个集成列表。我在这个演示中使用的是 Silex，但我不会使用提供的集成来展示如何为您的任何应用程序配置 Monolog。

## 伐木工

当您创建一个新的记录器时，您应该给它一个通道名。这就是你如何区分你的清单上的伐木工。我将把我的日志绑定到应用程序容器。

```
// app/bootstrap/container.php

$logger = new \Monolog\Logger('general');
$app->container->logger = $logger;
```

因为 Monolog 是 [PSR-3](http://www.php-fig.org/psr/psr-3/) ，所以你可以确信你遵守了 PHP-FIG 标准。如果您对 Monolog 感到不舒服，这将允许您切换到任何其他实现(我看不出您为什么不这样做)。根据您的[日志级别](https://github.com/Seldaek/monolog#log-levels)，您现在可以使用以下方法之一开始日志记录。(`log`、`debug`、`info`、`warning`、`error`、`critical`、`alert`、`emergency`)。

```
$app->container->logger->info("Just an INFO message.");
```

## 经理人

当您记录一些东西时，它会遍历已注册的处理程序列表。处理程序需要指定它将处理的日志级别和决定是否传播消息的气泡状态。您可以看到，到目前为止，我们还没有为我们的日志记录器注册任何处理程序。默认情况下，Monolog 将使用`\Monolog\Handler\StreamHandler`来记录标准错误输出。您可以在[文档](https://github.com/Seldaek/monolog#handlers)中看到可用处理程序的列表。

为了说明多个处理程序和冒泡的使用，让我们假设我们希望将信息消息记录到浏览器控制台，将错误消息记录到终端输出。

```
// app/bootstrap/container.php

$logger = new \Monolog\Logger('general');

$browserHanlder = new \Monolog\Handler\BrowserConsoleHandler(\Monolog\Logger::INFO);
$streamHandler = new \Monolog\Handler\StreamHandler('php://stderr', \Monolog\Logger::ERROR);

$logger->pushHandler($browserHanlder);
$logger->pushHandler($streamHandler);

$app->container->logger = $logger;
```

```
// app/routes.php

$app->get('/admin/users', function () use ($app) {
    $app->container->logger->info("Another INFO message");
    $app->container->logger->error("just another ERROR message");
    // ...
});
```

![Browser log](img/cdf60f3e462dea6aec9f9a0278894bf0.png)

![Stream log](img/24ae7bedb46003934481a67d16646f9e.png)

错误消息如预期的那样被记录到终端控制台，但是为什么它会出现在浏览器控制台上？这是 Monolog 提供的冒泡选项。错误消息首先被记录到终端，只要 bubbling 选项被设置为 true，它就会继续前进到处理程序队列的顶部。现在，让我们在流处理程序上将冒泡选项设置为 false。

```
// app/bootstrap/container.php

// ...
$streamHandler = new \Monolog\Handler\StreamHandler('php://stderr', \Monolog\Logger::ERROR, false);
// ...
```

![Browser log](img/bb425050564d52cbd768e5eecc06e6ff.png)

![Stream log](img/ac207f17cabb3f7ad800672c69d7a6c3.png)

现在，您可以看到浏览器控制台上没有显示错误日志，这就是您可以分隔日志级别的方式。

您也可以使用 HipChat、Slack 等外部服务作为消息接收者。假设我们在 Slack 上有一个开发者频道，我们想让每个人都知道发生了一个错误，并提供了必要的详细信息。

### 松弛处理器

在将 Slack 处理程序推入队列之前，您需要获得一个令牌来授权处理程序发送到您的 Slack 通道。转到[授权页面](https://api.slack.com/web#auth)并生成新的令牌。

```
// app/bootstrap/container.php

// ...
$slackHandler = new \Monolog\Handler\SlackHandler('xoxp-5156076911-5156636951-6084570483-7b4fb8', '#general', 'ChhiwatBot');
$logger->pushHandler($slackHandler);

// ...
```

您只需要指定您的 Slack 令牌和通道名称，第三个可选参数是机器人名称。Slack 的日志级别是`CRITICAL`，但是您可以使用`setLevel`方法来更改它。

```
$slackHandler->setLevel(\Monolog\Logger::ERROR);
```

发送日志消息后，您可以访问您的频道来查看记录的错误消息。

```
$app->container->logger->error("just an ERROR message");
```

![Slack log](img/daa4e00a4d035aa2c3178b8bb088f504.png)

## 格式化程序

到目前为止，我们所有的日志都是预先格式化的，我们没有机会指定日志格式。这就是格式化程序的用途。每个处理程序都有一个默认的格式化程序，如果你不指定一个，你将得到`LineFormatter`。对于每个格式化程序，你总是可以切换到另一个，比如`HtmlFormatter`。一些处理程序有自己的格式化程序，如`ChromePHPHandler`、`LogglyHandler`等。

```
// app/bootstrap/container.php

$browserHanlder = new \Monolog\Handler\BrowserConsoleHandler(\Monolog\Logger::INFO);
$browserHanlder->setFormatter(new \Monolog\Formatter\HtmlFormatter);

//...
```

现在，当您记录新消息时，它将被格式化为 HTML 格式，然后记录到浏览器控制台。

```
$app->container->logger->info("Another INFO message");
```

![Browser log](img/69516b603cfbc26fc68b9a2eadd8edca.png)

您还可以扩展`LineFormatter`来包含消息的更多细节。可用的变量有:

*   `message`:日志消息。
*   `context`:创建记录器时传递的数据列表。(`new Logger('channelName', ['user' => 'adam'])`)。
*   `level`:错误级别代码。
*   `level_name`:错误级别名称。
*   `channel`:记录器通道名称。
*   `datetime`:当前日期时间。
*   `extra`:前置处理器推送的数据。

## 预处理器

预处理程序对于向日志中添加更多细节非常有用。例如，`WebProcessor`添加了关于请求的更多细节，比如(`url`、`ip`等)。在创建 logger 实例时，您设置了处理程序和处理器的列表，但是您也可以使用`pushProcessor`方法在以后添加它们。

```
// app/bootstrap/container.php

$logger = new \Monolog\Logger('general');
$logger->pushProcessor(new \Monolog\Processor\WebProcessor);

$browserHanlder = new \Monolog\Handler\BrowserConsoleHandler(\Monolog\Logger::INFO);

$logger->pushHandler($browserHanlder);
//...
```

当记录任何东西时，日志记录将通过注册处理器的列表传递，您将得到如下所示的内容。

```
$app->container->logger->info("Another INFO message");
```

![Browser log](img/2c0baa5317e239e408be2eba7512bb73.png)

Monolog 提供了一系列有用的预处理器，包括内存峰值、用户 id 等细节。您可以在[文档](https://github.com/Seldaek/monolog#processors)中查看可用的预处理器列表。

## 包裹

Monolog 是最好的日志库之一，它也集成到了大多数流行的框架中，如 Symfony、Laravel、Silex、Slim 等。如果你有任何意见或问题，你可以把它们贴在下面，我会尽我所能回答它们。

## 分享这篇文章