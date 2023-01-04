# 理解命令设计模式

> 原文：<https://www.sitepoint.com/understanding-the-command-design-pattern/>

你知道今天有超过 40 亿部手机在使用吗？在澳大利亚，我们有大约 1100 万人口和超过 2200 万部手机——平均每人拥有两部手机！

很明显，手机的使用变得越来越普遍。鉴于智能手机和其他移动设备的普及，越来越多的客户现在选择通过短信而不是电子邮件接收通知。短信当然比电子邮件有优势——它们简短、即时，最重要的是垃圾邮件可以忽略不计。

那么，这和你问的命令模式有什么关系呢？让我们来看一个假想的场景。

一家公司有一个网站，每天都举办竞赛来赢取奖品。它有一个超过 250，000 注册用户的数据库，每个用户都被发送一个每日代码字，他们必须输入或点击链接，以注册他们的作品进入绘画。大多数用户选择接收电子邮件，但现在有相当数量的用户选择通过短信接收通知。问题来了:你如何通过两个不同的渠道向两组用户发送信息？

合乎逻辑的方法是将用户分成 2 组，电子邮件收件人和短信收件人，这将涉及运行 2 个不同的查询，并将代码字分别发送到每个组。使用我将在本文中向您介绍的命令模式，您可以在一个过程中向两组用户发送消息。

## 使用命令模式的消息队列

命令模式(有时也称为动作模式或事务模式)是一种设计模式，它描述了如何将请求封装为一个对象，以便您可以对具有不同请求的客户端进行排队或记录。为了演示命令模式是如何工作的，让我们使用一个简单的消息队列示例。下面是`MessageQueue`类的定义:

```
<?php
class MessageQueue
{
    private $queue;

    public function __construct() {
        $this->queue = array();
    }

    public function addMessage(IMessage $msg) {
        $this->queue[] = $msg;
    }

    public function execute() {
        $sendCount = 0;
        foreach ($this->queue as $msg) {
            if ($msg->send()) {
                $sendCount++;
            }
        }
        return $sendCount;
    }
}
```

消息队列提供了两种方法——一种是将消息对象添加到队列中的`addMessage()`方法，另一种是处理队列中每条消息的`execute()`方法。在这个例子中，`addMessage()`方法只是将消息附加到内部数组`$queue`，而`execute()`方法遍历`$queue`中的元素，并为每个消息对象调用`send()`方法。

命令模式对每个请求进行排队，以便以后处理；发送电子邮件或短信的实际机制将在对象的`send()`方法中实现。`MessageQueue`不需要知道如何处理请求，因为这是请求对象的责任。为了确保`send()`方法可用，消息对象必须实现`IMessage`接口。

```
<?php
interface IMessage
{
    public function send();
}
```

每个消息对象实现了`IMessage`接口，并提供了自己的`send()`方法的实现。

```
<?php
class DailyAlertEmail implements IMessage
{
...
    public function send() {
        // actual code here to send email
        // ...
        echo "Sending message via emailn";
    }
}

class DailyAlertSMS implements IMessage
{
...
    public function send() {
        // actual code here to send the SMS
        // ...
        echo "Sending message via SMSn";
    }
}
```

`DailyAlertEmail`消息实现了它的`send()`方法，将代码字作为电子邮件发送，而`DailyAlertSMS`消息对象实现了它的`send()`方法，将消息作为 SMS 发送。

然后，要向 SMS 和 email 接收者发送消息，您需要查询数据库以获得他们的通信偏好，实例化一个合适的`IMessage`对象并将其添加到消息队列，然后调用队列的`execute()`方法。顺便提一下，为用户创建正确的`IMessage`对象将是使用[工厂方法设计模式](https://www.sitepoint.com/understanding-the-factory-method-design-pattern/)的好机会！

```
<?php
// create a new queue
$msgQueue = new MessageQueue();

$result = $db->query("SELECT * FROM customers");
while ($customer = $result->fetch(PDO::FETCH_ASSOC)) {
    // factory creates a DailyAlertSMS or DailyAlertEmail object
    // based on the user's preferences
    $msg = MessageFactory::build($customer, $codeword);

    // add the message object to the queue
    $msgQueue->addMessage($msg);
}

// send to all customers now
$msgQueue->execute();
```

使用命令模式，您可以从数据库中检索所有客户，实例化适当的`IMessage`实现，而不考虑客户的通信偏好，并处理它们一次，而不是首先查询数据库中的所有 SMS 客户并处理它们，然后对电子邮件客户重复该过程。

请记住，这只是一个基本的例子；在现实世界的应用程序中，最好是批处理 SMS 和电子邮件消息，并在一天中定期发送它们，理想情况下作为后台进程。通过一些小的修改，您可以将其转换为作为 cron 任务运行的“延迟”消息队列，并使用数据库来监控进程的进度。

## 摘要

如您所见，命令模式非常适合以下情况:

*   您希望能够通过要执行的操作来参数化对象。
*   您需要在不同的时间指定、排队和执行请求。
*   当需要将一组数据更改封装为单个操作(即事务)时。

在本教程中，我向您展示了 Command 模式如何成为实现命令队列的有用设计模式，在命令队列中，请求可以排队以进行顺序处理，同时将执行的实际实现与队列本身分离。

<small>[堀岩](http://www.shutterstock.com/gallery-632761p1.html) / [梭托](http://shutterstock.com)</small>

## 分享这篇文章