# 用 Twilio 创建一个基于 SMS 的股票报价服务

> 原文：<https://www.sitepoint.com/create-an-sms-based-stock-quote-service-with-twilio/>

随着基于云的电话服务的出现，比如 Twilio T1，编写一个支持短信和电话的应用程序变得前所未有的简单。除了允许您通过简单的 API 发送 SMS 消息，Twilio 还允许您处理收到的消息，这打开了一个可能的应用程序的世界。

在本文中，我将向您展示如何使用 Twilio 来构建一个简单的基于 SMS 的股票报价服务，该服务处理传入的 SMS 消息和发送的响应。该应用程序将允许用户通过 SMS 请求给定公司的股票价格，并在稍后通过相同的方法接收它。

## 开始之前

首先——如果你还没有这样做——你需要[在 Twilio 上创建一个账户](https://www.twilio.com/try-twilio)。

一旦你注册了，你需要申请(实质上是租用)一个电话号码，人们会通过这个号码发送信息来使用这项服务。前往[twilio.com/user/account/phone-numbers/incoming](https://www.twilio.com/user/account/phone-numbers/incoming)并点击“购买号码”选择一个可用号码。您可能希望选择一个特定于您所在地区的数字，但您可以自由选择任何可用的数字，唯一的变量是成本。

然后转到您选择的号码，选中 SMS 框，输入您将用于您的应用程序的 URL(它必须在某个公共可访问的地方)，然后是`/callback`。例如:

```
http://example.com/callback
```

记下您的帐户 SID 和相应的身份验证令牌(它们出现在您的帐户页面的顶部)，您稍后会用到它们。

## 应用程序

用户将通过 SMS 请求股票价格，应用程序将处理传入的消息，提取相关数据，使用外部 web 服务获取请求的信息，并在第二条 SMS 中将响应发送回用户。

该过程将如下:

*   用户通过 SMS 向指定的号码发送命令
*   SMS 由 Twilio 处理，它“pings”已配置的端点(我们的应用程序)
*   我们的应用程序解析命令并提取公司代码
*   应用程序 pings 外部 web 服务，将公司代码作为参数传递
*   该 web 服务返回一个价格
*   应用程序用一个响应和发送 SMS 的号码 ping Twilio API
*   用户收到一条带有所请求的股票价格的 SMS

下图说明了这一过程:

![twilio-stock-01](img/01ae7f9d7eee4371b1b0c8d79143c387.png)

听起来很复杂？放心吧，真的不是！让我们开始吧。

## 建立基础

最简单的入门方法是使用 composer。在项目根目录下创建一个`composer.json`文件，如下所示:

```
{
    "require": {
        "slim/slim": "2.3.0",   
        "slim/extras": "dev-develop",
        "twilio/sdk": "dev-master"
    }
}
```

我用的是 Slim 框架；然而，大部分代码将可以跨您喜欢的任何框架重用(如果您确实希望使用框架的话)。

现在运行:

```
php composer.phar install
```

您会发现 Slim 框架现在驻留在新创建的`vendor`文件夹中，还有官方的 Twilio SDK 和一些额外的好东西(我们稍后将使用 Slim Extras 的一些日志功能)。

创建一个`includes`文件夹，其中有一个名为`config.php`的文件:

```
return array(
    'twilio' => array(
        'account_sid'  => 'YOUR-ACCOUNT-SID',
        'auth_token'   => 'YOUR-AUTH-TOKEN',
        'phone_number' => 'YOUR-PHONE-NUMBER',
    ),
);
```

当然，您需要用之前从 Twilio 获得的值替换这些值。

在项目根目录下创建一个`public`目录，并将 Apache 配置指向该文件夹中的文件。

现在在公共文件夹中创建一个`.htaccess`文件:

```
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^ index.php [QSA,L]
```

这将把对不存在文件的所有请求路由到中央控制器文件`index.php`。

现在对于`index.php`文件:

```
<?php
require '../vendor/autoload.php';

$config = require '../includes/config.php';

$app = new SlimSlim();

$app->get('/', function () use ($app) {
  print 'Installation successful';
});

$app->run();
```

尝试浏览应用程序–您应该会看到“安装成功”消息。

因为我们正在构建一个不会为浏览器呈现内容的应用程序，所以实现一些日志记录会非常有帮助。默认情况下，Slim 日志记录到 STDERR，但是在`composer.json`文件中引用的 Slim Extras 库包含一个基于文件的日志记录器。

将新的`Slim`对象的实例化更改为:

```
$app = new SlimSlim(array(
        'log.writer' => new SlimExtrasLogDateTimeFileWriter(array(
            'path' => '../logs',
            'name_format' => 'Y-m-d',
            'message_format' => '%label% - %date% - %message%'
        ))
));
```

然后，在项目根目录下创建`logs`文件夹，并通过 web 服务器使其可写。

现在，您可以按如下方式访问记录器:

```
$log = $app->getLog();
```

并记录如下消息:

```
$log->info('This is a message');
```

## 处理收到的短信

我们已经指定了我们的回调，当收到一个传入的短信时，它会被 pinged。具体来说，我所说的“pinged”是指向指定的 URL 发出 POST 请求，其中包含两条关键信息:

*   发件人的号码
*   短信的内容

发送者的号码和消息分别作为 POST 变量–`From`和`Body`发送。

因此，让我们创建回调——现在我将保持简单，只记录信息:

```
$app->post('/callback', function () use ($app) {
    $log = $app->getLog();

    $sender = $app->request()->post('From');
    $message = $app->request()->post('Body');

    $log->info(sprintf('Message received from %s: %s', $sender, 	$message));
});
```

尝试发送短信到您配置的号码，然后检查日志-日志条目应该在几分钟内显示。如果您看不到任何东西，Twilio 会记录活动，这样您就可以从那里调试进程。

应用程序将接受的消息格式是:`COMMAND ARGUMENT`。它只接受一个命令——`PRICE`——比如“给我……的股价。”和一个参数，即公司代码。

这里有一个例子:

```
PRICE AAPL
```

该命令的意思是“给我发送苹果公司的当前股价”。

通过合并一个命令，我们为以后添加额外的功能留有余地。例如，也许你想为`SUBSCRIBE`提供定期更新的功能。

要解析传入的消息，我们只需提取命令和参数——我们可以使用一个简单的正则表达式:

```
$pattern = '/^([A-Z]+) ([A-Z]+)/';
preg_match($pattern, $subject, $matches);
print_r($matches);
```

如果您尝试使用前面的示例，您应该会看到:

```
Array
(
    [0] => PRICE AAPL
    [1] => PRICE
    [2] => AAPL
)
```

相关零件位于位置 1 和 2。

```
$command = $matches[1];
$argument = $matches[2];
```

尽管只有一个有效的命令，但是提前考虑并使用开关来决定基于命令采取什么行动是没有坏处的。

```
switch ($command) {
    case 'PRICE':
        // perform action
        break;
    default:
        print 'Invalid command!';
}
```

## 获取股票价格

我将使用一个 web 服务来获取给定公司的最新股价。免费的股票服务少之又少——即使这样也经常会有延迟，但这里有一个[T1。](http://www.webservicex.net/stockquote.asmx)

在项目根目录下创建一个名为`src`的目录，在这个目录下创建一个名为`SitePoint`的目录，在这个目录下创建一个名为`StockService.php`的文件:

```
<?php
namespace SitePoint;

class StockService
{
}
```

为了让 autoloader 获得这个类的位置，在您的`composer.json`中添加一个 autoloader 声明:

```
"autoload": {
    "psr-0": {"SitePoint\": "src/"}
}
```

现在运行:

```
php composer.phar update
```

要实现获取给定公司股票报价的方法，在`StockService`类中创建一个公共方法，如下所示:

```
public static function GetQuote($symbol) {
    $client = new SoapClient('http://www.webservicex.net/stockquote.asmx?WSDL');

    $params = array(
        'symbol' => $symbol,
    );
    $response = $client->__soapCall('GetQuote', array($params));
    $quotes = simplexml_load_string($response->GetQuoteResult);

    return $quotes->Stock[0];
}
```

我所做的就是创建一个`SoapClient`的实例，并把 URL 传递给服务的 WSDL 文件，然后调用`GetQuote`方法，处理 XML，并返回第一个(也是唯一的)`Stock`元素。

您可以添加一条新路线来测试它:

```
$app->get('/quote', function () use ($app) {
    $symbol = $app->request()->get('symbol');
    $quote = SitePointStockService::GetQuote($symbol);
    var_dump($quote);
});
```

如果调用 *http://localhost/quote？symbol=AAPL* ，你应该看到这样的东西:

```
object(SimpleXMLElement)#39 (16) {
  ["Symbol"]=>
  string(4) "AAPL"
  ["Last"]=>
  string(6) "431.31"
  ["Date"]=>
  string(9) "7/18/2013"
  ["Time"]=>
  string(6) "1:03pm"
  ["Change"]=>
  string(5) "+1.00"
  ["Open"]=>
  string(6) "433.55"
  ["High"]=>
  string(6) "434.87"
  ["Low"]=>
  string(6) "430.61"
  ["Volume"]=>
  string(7) "4878034"
  ["MktCap"]=>
  string(6) "404.8B"
  ["PreviousClose"]=>
  string(6) "430.31"
  ["PercentageChange"]=>
  string(6) "+0.23%"
  ["AnnRange"]=>
  string(15) "385.10 - 705.07"
  ["Earns"]=>
  string(6) "41.896"
  ["P-E"]=>
  string(5) "10.27"
  ["Name"]=>
  string(10) "Apple Inc."
}
```

在下一节中，我们将把这些信息整理成一条 SMS 消息，并把它发送到请求用户的手机上。

## 通过短信回复

这里要做的第一件事是构造应用程序将要回复的消息:

```
// construct the response
$response = sprintf('%s: %s (%s) [Hi: %s Lo: %s]',
	$quote->Name,
	$quote->Last,
	$quote->Change,
	$quote->High,
	$quote->Low
);
```

根据前面的信息，您最终会得到这样的消息:

```
Apple Inc.: 431.31 (+1.000) [Hi: 434.87 Lo: 430.61]
```

要将其作为 SMS 发送，实例化`Twilio`类并调用对应于所需 REST 端点的方法:

```
// instantiate the Twilio client	
$twilio = new Services_Twilio(				
    $config['twilio']['account_sid'],
    $config['twilio']['auth_token']
);

// send the SMS
$sms = $twilio->account->sms_messages->create(
    $config['twilio']['phone_number'],  // the number to send from
    $sender,
    $response
);
```

…就是这样！

还有一些工作要做，比如执行适当的错误处理，命令解析有点不宽容。您可以随意从 GitHub 下载代码并进行体验！

## 摘要

在本文中，我展示了如何使用 Twilio 接收和发送 SMS 消息，以及如何从头开始构建一个简单的应用程序，将请求的信息直接发送到某人的手机上。实际上，我们的例子并不十分有用；股票价格波动的速度远远快于你发送短信的速度——但或许你能想到其他用途？

您可能还希望尝试添加新的命令。也许你订阅的不是即时响应的服务，而是定期更新？或者甚至设置一些阈值，比如“股价跌破 x 就给我发短信”)。欢迎在下面的评论中提出任何其他想法！

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章