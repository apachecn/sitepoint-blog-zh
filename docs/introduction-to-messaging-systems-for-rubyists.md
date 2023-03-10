# 面向 ruby 爱好者的消息系统介绍

> 原文：<https://www.sitepoint.com/introduction-to-messaging-systems-for-rubyists/>

## 什么是信息传递

随着 Ruby 社区的成熟和我们的应用程序的增长，我们寻找新的方法来管理复杂性、减少耦合和提高可伸缩性。尽管 REST 服务是解决各种问题的好方法，但是当流量和移动部分的数量增加时，时间耦合变得越来越难以管理。如果处理每个请求都需要多次远程调用，那么几乎不可能保证快速响应。

这个问题的一个众所周知的解决方案是消息传递，最近在 Ruby 社区获得了一些流行。所有基于消息的系统和模式背后的核心思想非常简单:

*   你不会通过 REST 或者 SOAP 同步调用其他应用。
*   您向消息代理发送消息。消息代理将消息异步传递给其他应用程序(或者只是应用程序内部的工作人员)。

因此，您的应用程序可以响应，而不会被外部资源阻塞。

## 消息代理

为什么我们需要一个消息代理？使用代理而不直接向其他应用程序发送消息有几个非常显著的优点。您不需要管理消息的存储和传递。由消息代理来完成。它保证了交付、持久性，并可以提供一些附加服务，如过滤、日志记录、故障转移等。但是使用消息代理的主要优点是保持应用程序的不同部分(或不同的服务)相互独立:

*   它提供了时间分离，因此您可以独立地重新部署所有服务。如果您的某个服务不可用或被阻止(例如第三方 web 服务关闭)，您的应用程序可以继续工作并快速响应。消息代理保存所有消息，一旦问题得到解决，它们将被处理。
*   它提供了结构解耦。代理本身并不这样做，但是，将消息代理作为消息传递基础设施的一部分，可以更容易地添加独立于应用程序的消息转换。

消息代理是额外的间接层，非常有用。它最大限度地减少了系统的两个部分应该相互了解的信息量。例如，发送方可能不知道它的所有消息都由许多客户端处理，在它的消息被传递给客户端之前执行了一些转换，等等。发送者唯一应该担心的是接收消息的代理。

## AMQP 模式

几年来已经开发了几个成熟的消息传递系统，如 MSMQ、ActiveMQ、OpenMQ、ZeroMQ、RabbitMQ 等。在我看来，基于 AMQP 标准的系统最适合 ruby 爱好者。其中之一——rabbit MQ——我将在本文中使用它来演示使用消息传递的非常基本的场景。

但是在我进入正题之前，我想介绍一下 AMQP 模式的一些基础知识:

*   **消息代理**。消息代理是一个系统，它从一个应用程序获取传入的消息，并将它们传递给另一个应用程序。RabbitMQ 服务器是一个消息代理。
*   **制作人。**它是一个向消息代理发送消息的系统。
*   **消费者。**它是一个从消息代理接收消息的系统。
*   **交换。**它是所有传入消息的入口点。生产者向交易所发送信息。
*   **排队。**它是一个存储所有消息的实体。消费者从队列中读取消息。
*   **装订。**交换和队列之间的关系。

### 基本工作流程:

*   应用程序中的一些配置代码定义了一个交换 E 和一个队列 q。
*   它还绑定了 E 和 q。因此，发送到 E 的每条消息都将存储在 q 中。
*   您的应用程序(生产者)将消息发送到 exchange E。
*   RabbitMQ 将所有传入的消息从 E 路由到 q。
*   另一个应用程序(消费者)从 q 读取消息。

正如您所看到的，生产者和消费者之间的所有交互都是通过消息代理进行的。它们不会直接相互作用。这会导致松散耦合。

## 兔子

有很多种 ruby gems 可用于消息传递。我目前的选择是兔子，因为我发现它是最简单的一个。它不需要任何事件机器或异步编程的知识。

## 例子

足够的理论背景，我们来看看实践中是怎么做的。

我想展示的第一个例子是 1 交换 1 队列的情况: