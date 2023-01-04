# 云中的简单电子邮件

> 原文：<https://www.sitepoint.com/simple-email-in-the-cloud/>

在本文中，我将解释 Amazon 的简单电子邮件服务(SES)是如何更好地从您的 web 应用程序发送电子邮件的，并且我将提供一个入门路线图。

一般来说，互联网上的电子邮件是使用 SMTP 服务器发送的。Web 应用程序也不例外。但是，任何通过 web 应用程序处理过大规模电子邮件发送的人都知道，使用 SMTP 发送电子邮件是一件非常痛苦的事情。

要了解亚马逊的 SES 服务如何改进传统的电子邮件发送方式，最简单的方法是将 SES 与 SMTP 进行比较:

| **功能** | **传统 SMTP** | **亚马逊 SES** |
| 有效性 | 您或您的 web 主机必须维护一个 SMTP 服务器。如果服务器关闭，您的应用程序将无法发送电子邮件。 | 与传统的单点故障 SMTP 服务器不同，Amazon SES 是分布式的，并且被设计为高度可用。 |
| 可量测性 | 随着您的 web 应用程序发送越来越多的电子邮件，您需要升级您的 SMTP 服务器。最终，在负载平衡或集群配置中，您将需要多台服务器。 | Amazon SES 可以轻松应对应用程序扩展时电子邮件数量的增长。无需投资服务器、安装或维护。你只需为你所使用的东西付费。 |
| 费用 | 当然，您可以尝试建立自己的可伸缩、高可用性的 SMTP 平台。您所需要的只是位于多个数据中心的几台服务器，以及两到三名全职技术人员来监控和维护服务。那在你的预算之内，对吗？ | 你可以免费使用亚马逊 SES 每天多达 2000 条消息(你只需支付流量)。如果你发送的数量超过这个数，你只需支付几分钱就可以发送数千封电子邮件。 |
| 供应能力 | 主要的电子邮件提供商严格过滤收到的邮件。为了达到高交付率，你必须能够并且愿意跨越各种各样的技术障碍。 | 通过 SES，亚马逊采取措施确保您的信息得以传递。 |
| 跟踪 | SMTP 服务器日志可以告诉你你的电子邮件是否被接收的 ISP 所接受(享受使用服务器日志来跟踪发送的乐趣)。 | Amazon SES 提供了对电子邮件传递数据的便捷访问。你不仅可以发现你的电子邮件是否被退回，而且通过 SES 你可以看到你的电子邮件有多少被主要 ISP 的收件人标记为垃圾邮件。 |

既然您已经知道在您的应用程序中使用 Amazon SES 可以让生活变得更加美好，那么让我们来看几个例子。我们将从之前题为“[Amazon AWS PHP SDK](https://www.sitepoint.com/getting-started-with-the-aws-php-sdk/)入门”的文章中学习知识。

在使用以下任何示例之前，您必须注册 Amazon SES 服务。为此，只需登录你的 AWS 账户，访问亚马逊 SES 产品页面([http://aws.amazon.com/ses/](http://aws.amazon.com/ses/))，然后点击“立即注册”。

## 沙盒限制和电子邮件验证

关于使用 Amazon SES，你需要知道的第一件事是，当你第一次注册这项服务时，你的帐户在功能上会受到限制。这意味着两件事:

*   您只能从您已验证的电子邮件地址发送邮件。
*   您只能向您已验证的电子邮件地址发送邮件。

这显然不适用于生产部署，但对于测试来说很好。所以你需要做的第一件事就是验证几个电子邮件地址。您可以用几行代码开始验证过程:

```
// You'll need the SDK Class for these examples.
// Learn more at: https://www.sitepoint.com/getting-started-with-the-aws-php-sdk/
require_once 'sdk.class.php';

// Create an instance of the SES class.
$email = new AmazonSES();

// Call the verification method using your own email address
$response = $email->verify_email_address('someemail@example.com');

print_r($response);
```

只需运行上面的代码示例就可以启动验证。您将在您提交的地址收到一封电子邮件，其中包含一个编码验证链接。然后只需点击链接，您的电子邮件地址将得到验证。

在测试过程中，对您想要发送或接收的任意数量的电子邮件地址重复此过程。

## 发送第一封电子邮件

现在，您已经有了一两个经过验证的电子邮件地址，可以开始发送电子邮件了。这非常简单:

```
require_once 'sdk.class.php';
$email = new AmazonSES();

// Your verfified sending email address.
$sendFrom = 'someemail@example.com';

// The email address you're sending to
// (must be a verified email while your account
// is in the sandbox).
$sendTo = 'someemail@example.com';

// The message and body of your email:
// (Adding .date('c')  to the subject
// makes it easy to track separate tests)
$messageSubject = 'My First AWS Email: '.date('c');
$messageBody = 'Hi there! This is my first message.';

// Create the request:
$response = $email->send_email(
    $sendFrom,
    array('ToAddresses' => $sendTo),
    array(
        'Subject.Data' => $messageSubject,
        'Body.Text.Data' => $messageBody
    )
);

// If your send fails,
// You'll be able to find the reason why
// the response information.
print_r($response);
```

这就是全部了！

## 发送 HTML 电子邮件

将 HTML 添加到您的电子邮件中，可以很容易地添加格式和链接，从而使您的电子邮件更加有效。PHP SDK 让这变得简单:

```
require_once 'sdk.class.php';
$email = new AmazonSES();

$sendFrom = 'someemail@example.com';
$sendTo = 'someemail@example.com';
$messageSubject = 'My SES Email with HTML: '.date('c');
$messageBody = 'Hi there! This is a message with a link: http://www.sitepoint.com';

// You can specify an HTML alternative
// for your email which will allow you to
// add links, formatting, tables,
// and other html elements to your message:
$messageBodyHtml = 'Hi there! This is a message with a link: <a href="http://www.sitepoint.com" style="color: #D90000;">http://www.sitepoint.com</a>';

$response = $email->send_email(
    $sendFrom,
    array('ToAddresses' => $sendTo),
    array(
        'Subject.Data' => $messageSubject,
        'Body.Text.Data' => $messageBody,
        'Body.Html.Data' => $messageBodyHtml,
    )
);

print_r($response);
```

## 更多高级选项

除了发送格式化电子邮件的常见任务之外，SDK 还提供了一些非常有用的附加方法:

**发送原始电子邮件**–对于更高级的功能，PHP SDK 允许您发送原始电子邮件消息。这允许您在电子邮件中添加多个部分，包括附件。

**列出已验证的电子邮件地址**–获得您之前已验证的电子邮件地址列表会很方便，在某些情况下，您甚至可能希望使用“已删除已验证的电子邮件地址”方法来删除不应再允许从您的帐户发送的电子邮件。

**获得发送配额**–根据你的电子邮件习惯，你的亚马逊 AWS 账户将限制你每天可以发送的电子邮件数量。这一限制范围从每天 1，000 封电子邮件到 100 万封或更多。

**获取发送统计数据**–这种强大的方法允许您跟踪实时统计数据，包括成功的投递尝试、被拒绝的邮件、退回和投诉。

## 离开沙盒

一旦您准备好启用新的电子邮件服务，您将需要请求生产访问以发送电子邮件。可以在 Amazon SES 产品页面上找到提出这一请求的链接，请求通常会在一天左右的时间内得到批准。

## 更多提示

**滥用**–亚马逊 SES 的设计并不是为了方便向你购买的邮件列表发送未经请求的电子邮件。如果你用 SES 发送不想要的邮件，你会很快被关闭。

**配额**–亚马逊 SES 动态决定每日发送限额。提高你的限制的最好方法是发送高质量的邮件(低跳出率和投诉)。你的上限从 1，000 封电子邮件开始，但如果你每天持续发送多达 1，000 封高质量的电子邮件，它会在几天内增加。

**限制**–消息不能大于 10MB。如果你需要发送更大的文件，我会推荐使用亚马逊 S3 服务，安全，有时间限制的链接，而不是附加大文件。电子邮件还限制为 50 个收件人(收件人、抄送人和密件抄送人的总和)。

**DNS 设置**–为了最大化投递率，亚马逊建议设置特殊的 DNS 记录，告知其他 ISP 您的域名授权亚马逊代表您发送电子邮件。要了解更多信息，请阅读 Amazon SES 文档中的“SPF、发件人 ID 和 DKIM”部分。

## 更好

将我的交易和其他系统生成的电子邮件切换到 Amazon SES 大大减少了我在 web 应用程序中维护可靠的电子邮件服务所需的时间、精力和金钱。很高兴知道，随着我的应用程序的增长，我不需要担心我的电子邮件服务的增长。

有问题或建议吗？下面贴评论！

## 分享这篇文章