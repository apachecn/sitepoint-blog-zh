# 使用 Prowl 推送通知

> 原文：<https://www.sitepoint.com/push-notifications-with-prowl/>

向移动设备发送推送通知是一种快速、廉价的方式，可以向身处任何地方的人发送短信——当然，前提是他们的手机处于开机状态。但是要发送一个通知，通常有许多关卡要通过；更重要的是，注册您的设备，以便发送服务可以针对您的手机。当然，这是可以理解的，因为存在巨大的滥用可能性。

然而，假设你想要一种快速简单的方式给你自己或一组特定的人发送信息，而不必向苹果公司注册一个应用程序，并使用各种证书和程序。你可能有兴趣了解巡游侠。

Prowl 是一个基于 iOS 的 Growl 客户端，是苹果 MAC 电脑上流行的短消息系统。你可以为你的手机购买一个副本，然后“连接”到各种服务和应用程序——也许最有趣的是，使用它的 API 从你自己的应用程序发送消息。这就是我们将在本文中探讨的内容。

## 入门指南

你首先需要的是 iOS 设备上的 Prowl 应用程序。你可以从应用商店[下载它](https://itunes.apple.com/us/app/prowl-growl-client/id320876271 "Prowl: Growl Client for iPhone, iPod touch, and iPad on the iTunes App Store")，在撰写本文时，它的价格是 2.99 美元。

接下来，你需要在 [Prowl 网站](http://www.prowlapp.com/ "Prowl - iOS Push Notifications")上创建一个账户。注册后，您可以创建 API 密钥。API 密钥是一个标识符，用于定位使用您的帐户注册的设备。您可以为不同的用途设置多个 API 键，但是现在您只需要一个。你需要知道的是，如果你发送一条针对某个 API 密钥的消息，你会在所有注册了你的帐户的设备上收到它。

您还需要将您的 Prowl 副本与您的帐户相关联。打开 iOS 设备上的应用程序，输入您在 Prowl 网站注册时提供的登录信息。

一切都准备好了，让我们来测试一下。登录 Prowl 网站，进入“添加消息”。发送一条短信，您应该会在 iOS 设备上收到。您还可以设置邮件的优先级，以及可以从通知中打开的 URL。

值得注意的是，还有许多其他的发送信息的方式。Prowl 网站上列出了各种平台的许多应用程序，其中一些甚至是网络应用程序(比如 [Prowl Chrome 扩展](https://chrome.google.com/webstore/detail/prowl/afjmhofmjbffpakdiiilmkcdclabhmmb "Chrome Web Store - Prowl")，它允许你直接从浏览器向你的设备发送你当前的网址)。你可能也注意到了，当你以*<API _ key>@ API . prowl app . com*的形式添加一个 API key 时，会生成一个电子邮件地址。如果你发送邮件到那个地址，你会通过 Prowl 收到推送通知。

因为创建 API 密匙的过程有点复杂，而且密匙的格式对用户不友好，所以 Prowl 可能不是公共应用程序的理想选择。让不太懂技术的人输入电子邮件地址或手机号码是一回事，让他生成一个密钥，然后复制并粘贴到一个表单中是另一回事。但是如果你要给自己或者一个小团队发送消息，这个过程可能就不是太重要了。

此外，Prowl API 有一个速率限制——可以说是一个相当慷慨的限制——每个 IP 地址每小时 1000 次调用。如果您需要更多，您可以创建一个提供者(从 API keys 页面)并在 API 调用中使用它。

假设一切正常，您正在接收消息，我们可以进入下一部分。

## 构建您自己的应用程序

这就是事情变得有趣的地方——使用 Prowl 的 API 从 PHP 应用程序发送消息！我们将使用 ProwlPHP，一个 PHP 库来与 API 接口——但是如果您想亲自动手探索 API，请参考[在线文档](http://www.prowlapp.com/api.php "Prowl: API")。该库在 GitHub 和 Composer[上可用。](https://github.com/xenji/ProwlPHP "xenji/ProwlPHP - GitHub")

```
{
    "require": {
        "xenji/ProwlPHP": ">=1.0.2"
    }
}
```

要发送消息，首先需要实例化 Prowl 连接器，可选地设置一个提供者键，并告诉连接器您将发出一个 POST 请求:

```
$prowl = new Prowl\Connector();
$prowl->setProviderKey('YOUR-PROVIDER-KEY');
$prowl->setIsPostRequest(true);
```

接下来，您需要添加一个过滤器。您可以使用闭包来定义消息文本的过滤器，例如净化用户输入、限制文本数量、附加额外信息等等。

```
$prowl->setFilterCallback(function ($text) {
    // ... do something
    return $text;
});
```

或者，您可以通过扩展`ProwlSecurityPassthroughFilterImpl`并实现它的`filter()`方法，以更加面向对象的方式创建一个过滤器:

```
class CustomFilter extends ProwlSecurityPassthroughFilterImpl
{
    public function filter($sContentToFilter) {
        // Do something
        return $sContentToFilter;
    }
}

$filter = new CustomFilter();
$prowl->setFilter($filter);
```

现在创建一个`ProwlMessage`的实例:

```
$msg = new ProwlMessage();

$msg->setApplication('My Prowl Application');
$msg->addApiKey('YOUR-API-KEY');
$msg->setPriority(0);
$msg->setEvent($event);
$msg->setDescription($description);
$msg->setUrl($url);
```

`addApiKey()`方法是您指定接收者的地方；您可以多次调用此方法将消息发送给多个收件人。

赋予`setPriority()`的优先级应为以下值之一:

![prowl-01](img/4e0510add7fcd91df85963fb6695a645.png)

`setEvent()`方法设置事件的名称，最多 1024 个字符。此外，您可以使用`setDescription()`为事件设置一个可选的描述，并使用`setUrl()`设置一个可选的 URL，该 URL 可以直接从通知中打开。

最后，为了实际发送消息，调用`push()`方法:

```
$response = $prowl->push($msg);
```

## 使用 Prowl 的应用程序日志记录

假设您已经构建了一个被大量用户使用的 web 应用程序。出现严重错误时会发生什么？您可能已经设置了日志记录，并且可能将消息记录到文件、数据库或 Redis 之类的数据存储中，但是您可能希望立即了解严重的错误。还有什么比发送推送通知更好的方式呢！

许多日志记录实现提供了定义大量日志编写器的能力，并对它们进行配置以响应不同的错误级别。例如，您可能希望将信息性消息记录到文件中，但通过电子邮件发送重要消息。Zend_Log 就是这样一个实现。尽管它在技术上是 Zend Framework 的一部分，但如果您让它的依赖项对您的应用程序可用，它也可以以独立的方式使用。你可以从 [Zend Framework 网站](http://framework.zend.com/ "Zend Framework")下载或者使用 Composer 安装。

```
{
    "require": {
        "zendframework/zend-log": "2.2.*",
        "xenji/ProwlPHP": ">=1.0.2"
    }
}
```

让我们创建自己的`Zend\Log\AbstractWriter`实现，通过 Prowl 发送某些日志消息。该课程将需要:

1.  有一个属性来存储徘徊的钥匙
2.  通过其构造函数接受密钥
3.  实现`doWrite()`方法

```
namespace SitePoint\Log\Writer;
use Zend\Log;

class Prowl extends \Zend\Log\Writer\AbstractWriter
{
    private $apiKeys;

    public function __construct($apiKeys) {
        if (is_array($apiKeys)) {
            $this->apiKeys = $apiKeys;
        }
        else {
            $this->apiKeys = array($apiKeys);
        }

        $this->_formatter = new Log\Formatter\Simple('%message%');
    }

    protected function doWrite(array $event) {
        $event = $this->_formatter->format($event);

        $prowl = new \Prowl\Connector();
        $prowl->setFilterCallback(function ($text) {
            return $text;
        });
        $prowl->setIsPostRequest(true);

        $msg = new \Prowl\Message();
        $msg->setApplication('My Custom Logger');
        $msg->setPriority(2);
        $msg->setEvent($event);
        foreach ($this->apiKeys as $key) {
            $msg->addApiKey($key);
        }

        $prowl->push($msg);
}
```

关于该课程的几点说明:

*   我在构造函数中设置了格式化程序，它将简单地按原样发送消息。您可以将其设置为您想要的任何格式，或者稍后覆盖它。
*   我在用`doWrite()`方法做一切事情——例如实例化连接器——以保持简单。您可能希望扩展构造函数以接受应用程序名、提供者密钥等。
*   我将优先级硬编码为 2——紧急——但是，您可能希望将日志优先级映射到 Prowl 优先级，记住后者很少(而且您可能只对关键消息使用 Prowl)。

最后，向记录器注册编写器如下所示:

```
$apiKeys = array(
    'YOUR-API-KEY',
    'ANOTHER-API-KEY'
);

$logger = new Zend\Log\Logger();

$writer = new SitePoint\Log\Writer\Prowl($apiKeys);
$writer->addFilter(
    new Zend\Log\Filter\Priority(Zend\Log\Logger::CRIT)
);
$logger->addWriter($writer);
```

## 结论

在本文中，我展示了如何使用 Prowl 向自己或他人发送特别的推送通知。我已经演示了如何将重要的日志消息直接发送到您的移动电话，并且您可以使用 Prowl 通知来处理各种事情。这里还有一些例子:

*   多因素认证
*   新电子邮件或评论通知
*   群发消息
*   安全警报(例如入侵检测)

也许你能想到更多？请在下面的评论中告诉我！

## 分享这篇文章