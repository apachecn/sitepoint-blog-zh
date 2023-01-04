# 如何在 PHP 中使用 RabbitMQ

> 原文：<https://www.sitepoint.com/use-rabbitmq-php/>

**AMQP** ( [高级消息排队协议](https://en.wikipedia.org/wiki/Advanced_Message_Queuing_Protocol))是一种网络协议，可以将消息从一个应用端点传递到另一个应用端点。它不关心所述应用的平台或语言，只要它们支持 AMQP。

实际上，其中一个应用程序端点通过 AMQP 代理发送消息，从而成为生产者。反过来，代理将把消息传递给另一个应用程序端点，称为消费者。

**[RabbitMQ](https://www.rabbitmq.com)** 是 AMQP 的一个代理，支持多种编程语言，比如 PHP。

拥有像 RabbitMQ 这样的消息代理和作为网络协议的 AMQP 的优势在于，生产者、代理和消费者可以生活在不同地理位置的不同物理/虚拟服务器上。

此外，由于网络不可靠，应用程序可能无法完全处理消息，AMQP 支持消息确认，可以自动确认，也可以在应用程序端点决定发送消息时确认。

RabbitMQ 实现了 *[AMQP 0-9-1](https://www.rabbitmq.com/resources/specs/amqp0-9-1.pdf)* 协议。乍一看，它遵循以下模型。

![Hello world example routing](img/56d7e6cf8cb13185082422dc0235ac6e.png)

## 词汇表

| 概念 | 定义 | 图标 |
| --- | --- | --- |
| *制作人* | 发送消息的应用程序端点 | ![Producer icon](img/57645c70a5dfd90d579ba5d5f8d45254.png) |
| *消费者* | 接收消息的应用程序端点 | ![Consumer icon](img/ce5ae3e23e7fdf3d7ff385d4d13ce204.png) |
| *连接* | 处理协议、错误、认证等。连接使用 TCP 协议完成。 | – |
| *频道* | 连接通过信道被多路复用。尽管所有通道共享同一个 tcp 连接，但一个通道的通信完全独立于另一个通道。 | – |
| *交换* | 从生成器接收消息，并将它们推送到队列。根据具体情况，这对于开发人员来说是透明的。 | ![Exchange representation](img/8d0bfd7de499c58893fc56af4f813503.png) |
| *队列* | 存储消息的缓冲区 | ![Queue icon](img/fefad716d7c7494ef7b61944441c87a9.png) |
| *消息* | 符合 AMQP 格式的任意信息，通过代理从生产者发送给消费者。代理不能修改消息中的信息。 | – |
| 承认 | 消费者发回的通知，告知服务器消息已被接收和处理，因此服务器可以将其从队列中删除。 | – |

AMQP 0-9-1 的另一个优点是应用程序定义了路由逻辑，而不是代理管理员。这给了开发者很大的灵活性，不需要学习新的编程/脚本/标记语言。

你可以在“ [AMQP 0-9-1 模型解释](https://www.rabbitmq.com/tutorials/amqp-concepts.html)”指南中了解更多关于 AMQP 和 RabbitMQ 的信息。虽然对本教程来说不是必要的，但我鼓励你完整地阅读它。

## 安装 RabbitMQ

你可以在这里找到你的平台[的详细文档，但是 Ubuntu 的大致情况如下:](https://www.rabbitmq.com/download.html)

```
sudo vim /etc/apt/sources.list
```

将下面一行添加到文件中:`deb https://www.rabbitmq.com/debian/ testing main`

![sources.list](img/b419b88639a3e5be6c598460d320d112.png)

在这之后，只需运行以下命令

```
wget https://www.rabbitmq.com/rabbitmq-signing-key-public.asc
sudo apt-key add rabbitmq-signing-key-public.asc
sudo apt-get update    
sudo apt-get install rabbitmq-server
sudo rabbitmqctl status
```

您应该会看到如下所示的内容:

```
sudo rabbitmqctl status

Status of node 'rabbit@localhost-sprabbitmq-926807' ...
[{pid,790},
 {running_applications,[{rabbit,"RabbitMQ","3.3.4"},
                        {os_mon,"CPO  CXC 138 46","2.2.14"},
                        {mnesia,"MNESIA  CXC 138 12","4.11"},
                        {xmerl,"XML parser","1.3.5"},
                        {sasl,"SASL  CXC 138 11","2.3.4"},
                        {stdlib,"ERTS  CXC 138 10","1.19.4"},
                        {kernel,"ERTS  CXC 138 10","2.16.4"}]},
 {os,{unix,linux}},
 {erlang_version,"Erlang R16B03 (erts-5.10.4) [source] [64-bit] [smp:8:8] [async-threads:30] [kernel-poll:true]\n"},
 {memory,[{total,53400096},
          {connection_procs,2704},
          {queue_procs,34432},
          {plugins,0},
          {other_proc,13983664},
          {mnesia,64504},
          {mgmt_db,0},
          {msg_index,26056},
          {other_ets,770880},
          {binary,16112},
          {code,16372077},
          {atom,594537},
          {other_system,21535130}]},
 {alarms,[]},
 {listeners,[{clustering,25672,"::"},{amqp,5672,"::"}]},
 {vm_memory_high_watermark,0.4},
 {vm_memory_limit,12677506662},
 {disk_free_limit,50000000},
 {disk_free,899366912},
 {file_descriptors,[{total_limit,199900},
                    {total_used,5},
                    {sockets_limit,179908},
                    {sockets_used,1}]},
 {processes,[{limit,1048576},{used,133}]},
 {run_queue,0},
 {uptime,34}]
...done.
```

如果您得到一个错误消息，服务器可能需要用下面的命令启动

```
sudo invoke-rc.d rabbitmq-server start

 * Starting message broker rabbitmq-server
   ...done.
```

RabbitMQ 定义了一个默认用户:

*   用户名:**客人**
*   密码:**客人**

请注意，只有从本地主机连接到 RabbitMQ 时，才能使用该用户。对于真正的分布式系统，您必须定义用户和角色。您可以在[文档](https://www.rabbitmq.com/access-control.html)中阅读更多关于访问控制的内容。对于以下示例，我们将使用上述凭据。

为了能够将您的 php 应用程序与 RabbitMQ 集成，您将需要 [php-amqplib](https://github.com/videlalvaro/php-amqplib) 库。使用 composer 很容易得到它，只需在您的`composer.json`文件中定义需求:

```
{
    ...
    "require": {
        ...,
        "videlalvaro/php-amqplib": "2.2.*"
    }
    ...
}
```

执行完一次后，你就一切就绪了。

## 放弃

无论如何，这些代码都不应该在生产就绪的应用程序中使用，也不应该在生产服务器上执行。没有实施任何安全检查和/或验证。这段代码只是为了教育目的而编写的，只展示了基本的功能。性能、效率或可重用性不是优先考虑的。

请注意，尽管下面的示例为生产者、代理和消费者应用程序使用相同的主机来简化开发、部署和测试，但在现实生活中，在同一个机器中使用“分布式系统”是没有意义的。

要获得完整的源代码列表，您可以查看 GitHub 资源库，其中包含了以下示例中使用的应用程序。

## 简单的例子:发送异步处理数据的请求

假设我们有一家比萨饼公司，我们接受在线订单。让我们假设我们有一个处理订单的自动化系统，但是这个系统不能直接暴露给公众…

![enter image description here](img/35dcdf8d857c9977ada0a0e03bd36b92.png)

我们将实现最简单的模式:

![enter image description here](img/5cc5e8965b4ef9f46f2c507434f2f541.png)

首先，我们有以下脚本来接受来自表单的请求:

```
<?php
chdir(dirname(__DIR__));
require_once('vendor/autoload.php');

use Acme\AmqpWrapper\SimpleSender;

$theName = filter_input(INPUT_POST, 'theName', FILTER_SANITIZE_STRING);
$simpleSender = new SimpleSender();
$simpleSender->execute($theName);
header("Location: orderReceived.html");
```

这段代码将简单地检查一个名为`theName`的 POST 参数，并将其发送给我们创建的一个对象进行处理。让我们来看看 SimpleSender::execute()方法:

```
<?php

// ... SOME CODE HERE ...

    /**
     * Sends a message to the pizzaTime queue.
     * 
     * @param string $message
     */
    public function execute($message)
    {
        /**
         * Create a connection to RabbitAMQP
         */
        $connection = new AMQPConnection(
            'localhost',    #host - host name where the RabbitMQ server is runing
            5672,           #port - port number of the service, 5672 is the default
            'guest',        #user - username to connect to server
            'guest'         #password
            );

        /** @var $channel AMQPChannel */
        $channel = $connection->channel();

        $channel->queue_declare(
            'pizzaTime',    #queue name - Queue names may be up to 255 bytes of UTF-8 characters
            false,          #passive - can use this to check whether an exchange exists without modifying the server state
            false,          #durable - make sure that RabbitMQ will never lose our queue if a crash occurs - the queue will survive a broker restart
            false,          #exclusive - used by only one connection and the queue will be deleted when that connection closes
            false           #autodelete - queue is deleted when last consumer unsubscribes
            );

        $msg = new AMQPMessage($message);

        $channel->basic_publish(
            $msg,           #message 
            '',             #exchange
            'pizzaTime'     #routing key
            );

        $channel->close();
        $connection->close();
    }
```

逐行细分如下:

```
<?php
/* ... MORE CODE HERE ... */

        $connection = new AMQPConnection(
            'localhost',    #host - host name where the RabbitMQ server is runing
            5672,           #port - port number of the service, 5672 is the default
            'guest',        #user - username to connect to server
            'guest'         #password
            );

/* ... MORE CODE HERE ... */
```

首先，我们创建一个连接对象。请注意，凭据 guest:guest 是 RabbitMQ 的默认凭据。但是，如果您从同一个主机(localhost)中连接，则只允许您使用它们连接到服务器。

因为 RabbitMQ 使用单个端口进行监听和服务，所以我们需要用`$channel = $connection->channel();`创建一个通道(可以把它看作一个虚拟端口),这样其他客户机就能够连接到服务器。

```
<?php
/* ... MORE CODE HERE ... */

        $channel->queue_declare(
            'pizzaTime',    #queue name - Queue names may be up to 255 bytes of UTF-8 characters
            false,          #passive - can use this to check whether an exchange exists without modifying the server state
            false,          #durable - make sure that RabbitMQ will never lose our queue if a crash occurs - the queue will survive a broker restart
            false,          #exclusive - used by only one connection and the queue will be deleted when that connection closes
            false           #autodelete - queue is deleted when last consumer unsubscribes
            );

/* ... MORE CODE HERE ... */
```

一旦我们准备好了通道，让我们声明一个队列来发送请求。RabbitMQ 的好处是我们可以直接从客户端创建队列，但是我们必须小心如何创建它。让我们简单解释一下用`$channel->queue_declare()`创建队列的参数

*   *队列名称*:这是一个任意的名称，将用于标识队列
*   *Passive* :如果设置为 true，服务器将只检查是否可以创建队列，false 将实际尝试创建队列。
*   *Durable* :通常，如果服务器停止或崩溃，所有的队列和消息都会丢失…除非我们声明队列是持久的，在这种情况下，如果服务器重新启动，队列将会持续存在。
*   *Exclusive* :如果为 true，队列只能由创建它的连接使用。
*   *自动删除*:如果为真，一旦队列中没有消息并且没有连接的订户，队列将被删除

在我们的示例中，如果服务器重新启动，队列将不再存在，可以由其他连接使用，并且如果没有更多订阅者，队列也不会被删除。

接下来，我们创建了一个消息对象，其中的`$msg = new AMQPMessage($message);`、`$message`是 POST 参数，`theName`是我们从表单中接收到的。消息可以是任何字符串。

```
<?php
/* ... MORE CODE HERE ... */

        $channel->basic_publish(
            $msg,           #message 
            '',             #exchange
            'pizzaTime'     #routing key
            );

/* ... MORE CODE HERE ... */
```

现在我们必须将消息发布到队列中。但是，如果不通过交换，我们不能将消息直接发布到队列。我们从未宣布交换，所以这怎么可能？事实证明，当我们创建一个队列而没有定义一个绑定队列的交换时，将使用一个默认的交换。我们可以通过与`$channel->basic_publish()`的默认交换将消息发布到队列中，它使用的参数是:

*   消息:我们要发送的消息
*   交换:注意我们使用的是空字符串，因为我们将使用默认的交换
*   路由关键字:我们希望消息传递到的队列名称。

```
<?php
/* ... MORE CODE HERE ... */

        $channel->close();
        $connection->close();

/* ... MORE CODE HERE ... */
```

完成后，我们必须关闭到通道和服务器的连接。

请注意，我们没有收到服务器的任何回复。我们最多可以确定消息已经排队，但是我们完全忽略了消息是否到达了最终接收者。这是 AMQP 的魅力之一…我们可以在我们的公共网站上快速调度客户，并在后台应用程序上异步处理订单。

比萨饼订单在队列中，我们如何检索它们呢？首先，我们必须意识到，消费者必须与队列服务器建立一个持续的连接(也称为 subscribe ),以便从服务器接收消息。服务器不会自己将这些消息推送到我们的应用程序。幸运的是，建立这种联系非常容易。

```
<?php
namespace Acme\AmqpWrapper;

use PhpAmqpLib\Connection\AMQPConnection;

class SimpleReceiver
{
    /* ... SOME CODE HERE ... */

    /**
     * Listens for incoming messages
     */
    public function listen()
    {
        $connection = new AMQPConnection(
            'localhost',    #host 
            5672,           #port
            'guest',        #user
            'guest'         #password
            );

        $channel = $connection->channel();

        $channel->queue_declare(
            'pizzaTime',    #queue name, the same as the sender
            false,          #passive
            false,          #durable
            false,          #exclusive
            false           #autodelete
            );

        $channel->basic_consume(
            'pizzaTime',                    #queue 
            '',                             #consumer tag - Identifier for the consumer, valid within the current channel. just string
            false,                          #no local - TRUE: the server will not send messages to the connection that published them
            true,                           #no ack - send a proper acknowledgment from the worker, once we're done with a task
            false,                          #exclusive - queues may only be accessed by the current connection
            false,                          #no wait - TRUE: the server will not respond to the method. The client should not wait for a reply method
            array($this, 'processOrder')    #callback - method that will receive the message
            );

        while(count($channel->callbacks)) {
            $channel->wait();
        }

        $channel->close();
        $connection->close();
    }

    /**
     * @param $msg
     */
    public function processOrder($msg)
    {
        /* ... CODE TO PROCESS ORDER HERE ... */
    }
}
```

正如我们在生产者中连接、创建通道和声明队列一样，我们必须在消费者中做完全相同的事情。然而，在消费者中，我们必须订阅带有`$channel->basic_consume()`的频道，使用的参数定义如下:

*   队列:必须与我们在生成器中定义的队列名相同
*   消费者标签:给消费者的任意名称。如果此字段为空，服务器将生成一个唯一的标签
*   No local:这是一个模糊的参数，如果被激活，服务器将不会发送自己的消息
*   No Ack(nowledgement):将自动确认消费者收到了消息，因此我们不必手动这样做。
*   无等待:如果设置，服务器将不等待消费者中的进程完成
*   回调:可以是一个函数名，一个包含对象和方法名的数组，或者是一个将接收排队消息的闭包。这个回调必须接受一个包含这样一个消息的参数。在我们的例子中，`array($this, 'processOrder')`用于将当前对象的`processOrder()`方法定义为回调。

```
<?php

/* ... SOME CODE HERE ... */

        while(count($channel->callbacks)) {
            $channel->wait();
        }

/* ... SOME CODE HERE ... */
```

“魔法”发生在`while`循环内部。如果订户至少定义了一个回调，我们将等待通道中的任何事件。每次收到消息时，我们定义的回调`processOrder()`都会被执行，所以我们可以根据需要处理消息。

我们怎么发动它？简单创建一个调用`SimpleReceiver::listen()`方法的脚本，如下所示:

```
<?php
chdir(dirname(__DIR__));

require_once('vendor/autoload.php');

use Acme\AmqpWrapper\SimpleReceiver;

$receiver = new SimpleReceiver();
$receiver->listen();
```

现在在控制台中用`php <script name>`启动这个过程，让它完成它的工作。如果你想杀死消费者，一个简单的`Ctrl + C`就会打断这个过程。

拥有队列服务器的一个好处是，无论由于什么原因，您的消费者作业崩溃，它都不会中断对您的公共应用程序用户的服务。消息将简单地堆叠在队列中，一旦您重新启动消费者，消息将被一个接一个地交付给它进行处理。

## 结论

在这一部分，我们介绍了 AMQP 和排队系统的理论，并通过一个简单的例子演示了它们的用法。在这篇文章的后续部分，我们将处理两个增加复杂性和高级概念的例子。敬请期待！

## 分享这篇文章