# 与 RabbitMQ 的优雅网络通信

> 原文：<https://www.sitepoint.com/elegant-network-communication-rabbitmq/>

![RabbitMQ.sh-600x600](img/a48fc3d841e7607cc59d889d70e863e4.png)

网络通信是许多应用程序的基础，但是很难做到正确。处理传统套接字(TCP、UDP 等。)是令人沮丧的，因为与它们相关联的概念对于我们所做的许多工作来说是太低级了。例如，套接字作为流来操作，这意味着您已经想出了自己的方法来区分信息包。

这是消息传递系统填补的空白之一。它们通过提供基于消息的通信，而不是基于流的通信，使得编写网络应用程序更加容易。这意味着，您不必阅读看似永无止境的信息流，而是将您的信息很好地打包成消息。此外，消息传递系统允许我们轻松地设计中间有“队列”的复杂网络拓扑(稍后将详细介绍)。

在本文中，我们将探索 RabbitMQ。我们将看看它的基本概念，以及它与其他消息队列系统的比较。此外，本文还将介绍如何使用“Bunny”，这是让 Ruby 开发人员与 RabbitMQ 进行交互的工具。

我们开始吧。

## 概念

RabbitMQ 的基本概念非常清楚。它让两台计算机通过使用*消息*进行通信(这个概念在很高的层面上类似于数据包)。此外，RabbitMQ 还提供了队列。这意味着，如果计算机 A 向计算机 B 发送一条消息，而计算机 B 没有立即读取它，RabbitMQ 会将该消息保存在一个队列中(这是一种[数据结构的类型](http://en.wikipedia.org/wiki/Queue*(抽象* data_type))。通常，添加队列的节点被称为*生产者*，从队列中出列的节点被称为*消费者*。

与 ZeroMQ 不同，RabbitMQ 是一个消息*代理*，这意味着在计算机之间有一个“中间人”来“代理”消息。这个中间人确实增加了延迟(即等待时间)并降低了吞吐量，但是它实现了负载平衡、持久排队(即把未完成消息的队列保存到磁盘)等。简单多了。所以，这是一个权衡。此外，RabbitMQ 实现了一个名为 AMQP 的开放协议，而不是像 ZeroMQ 一样推出自己的协议。

了解 RabbitMQ 的最好方法是开始使用它，所以让我们开始吧！

## 兔子

由于 RabbitMQ 是一个代理消息传递系统，我们需要安装一个充当中间人的 RabbitMQ 服务器。如果你在 OS X，有[家酿酒](http://brew.sh/)，这个过程真的很简单:

```
brew install rabbitmq
PATH=$PATH:/usr/local/sbin
rabbitmq-server
```

请注意，我们必须更新路径。`/usr/local/sbin/`一般不在路径上，但那是安装`rabbitmq-server`的地方。如果你使用的是 Linux 或 Windows，RabbitMQ 有[独立安装程序和 deb](http://www.rabbitmq.com/download.html)供你使用。

为了让 Ruby 与 RabbitMQ 对话，我们需要一个名为 **bunny** 的 gem:

```
gem install bunny
```

我们可以从编写一个简单的消息发送器开始:

```
require 'bunny'

conn = Bunny.new
conn.start

ch = conn.create_channel
q = ch.queue("hello_world")

ch.default_exchange.publish("Hello, world!", :routing_key => q.name)
puts "Sent!"

conn.close
```

让我们分解代码。

```
conn = Bunny.new
conn.start
ch = conn.create_channel
```

在这里，我们连接到 RabbitMQ 服务器并创建一个通信通道。

```
q = ch.queue("hello_world")
```

这实际上是一段相当重要的代码。它在 RabbitMQ 代理中创建了一个名为`hello_world`的队列。如果这个队列已经存在，它只是给我们一个现有队列的句柄。队列名非常重要，因为它们允许我们区分消息(例如，队列“http *任务”和“新闻*消息”可能包含两种完全不同的信息)。

```
ch.default_exchange.publish("Hello, world!", :routing_key => q.name)
```

在这里，我们发布消息“你好，世界”。路由密钥可用于在您的网络中创建路由，这是我们目前不需要的功能。

现在我们有了一个发送者，我们需要一个接收者:

```
require 'bunny'

conn = Bunny.new
conn.start

ch = conn.create_channel
q = ch.queue('hello_world')
puts " Waiting for messages in #{q.name}. To exit press CTRL+C"
q.subscribe(:block => true) do |delivery_info, properties, body|
  puts "Received #{body}"
end
```

除了订阅块之外，大部分代码与发送方相同:

```
q.subscribe(:block => true) do |delivery_info, properties, body|
  puts "Received #{body}"
end
```

这会订阅队列中的新消息。传入`:block => true`告诉它这个调用可以阻塞，即等待直到一个消息实际可用。然后，一旦收到消息，我们传入的代码块(不要与“等待”类型的代码块混淆)打印出收到的消息的内容。

数据流非常简单。发送方向代理发送数据。接收者(通常称为消费者)从队列中“消费”它。让我们用同样的想法做一些更有趣的事情。

## HTTP 主/工人

事实证明，同样的概念可以应用于制作并行/分布式 HTTP 链接下载器。我们将有一个主服务器，它有一个 URL 列表，然后被添加到队列中。工人将消费和下载这些网址的内容。以下是生产商/主代码:

```
require 'bunny'

class HttpMaster

  #takes a list of urls
  #to submit to workers
  def initialize(urls)
    @conn = Bunny.new
    @urls = urls
  end

  def start
    @conn.start
    ch = @conn.create_channel
    q = ch.queue("http_tasks")

    @urls.each do |url|
      ch.default_exchange.publish(url, :routing_key => q.name)
      puts "Sent url: #{url}"
    end
  end
end

http_master = HttpMaster.new(['http://www.google.com/', 'http://yahoo.com/'])
http_master.start
```

乍一看，代码似乎更复杂一些。但是，我们真正改变的唯一东西是消息的类型(以及队列的名称，但这只是为了保持我们的组织):

```
ch.default_exchange.publish(url, :routing_key => q.name)
```

现在，不仅仅是发布“你好，世界！”，主服务器发布它想要下载的 URL。让我们来看看使用该 URL 的工作人员/消费者:

```
require 'bunny'
require 'open-uri'

class HttpWorker
  def initialize(folder)
    @conn = Bunny.new
    @folder = folder
    @counter = 0
  end

  def work(url)
    open("#{@folder}/#{@counter}.backup", 'w+') do |file|
      file << open(url).read
    end

    @counter += 1
  end

  def start
    @conn.start

    ch = @conn.create_channel
    q = ch.queue "http_tasks"
    puts "Waiting for tasks on queue #{q.name}"
    q.subscribe(:block => true) do |delivery_info, properties, body|
      puts "Received task/URL: #{body}"
      work body
    end
  end
end

if ARGV.length != 1
  abort 'Usage: http_worker.rb FOLDER'
end

worker = HttpWorker.new(ARGV[0])
worker.start
```

让我们关注重要的一点:

```
q.subscribe(:block => true) do |delivery_info, properties, body|
  puts "Received task/URL: #{body}"
  work body
end
```

工作者调用`work body`，其中 body 应该是从主服务器接收的 URL，而不是像“Hello，world”示例那样丢弃它接收的消息。然后，`work`将 url 下载到指定的文件夹。

注意 RabbitMQ 赋予我们的力量。而不是担心我们的数据是否通过了网络，检查帧的大小等等。就像我们处理套接字一样，我们只是处理消息。此外，如果我们运行 master 而不运行 worker，那么 URL 将被保存在队列中，而不是被丢弃。因此，主服务器将 URL 发送给代理服务器，代理服务器将它们放在一个队列中，消费者/工作者将它们下载到一个文件夹中。但是，如果工人在这样做的时候遇到错误怎么办？

## 请求

按照现在的安排，如果工作人员在下载 URL 时遇到错误(例如内存不足)，该 URL 无论如何都会出队，这意味着我们将没有下载的文件！显然，这可能会很成问题，因为我们没有数据的完整副本。幸运的是，RabbitMQ 以“ack”的形式提供了解决这个问题的方法。

我们可以配置队列，使其仅在收到来自消费者的消息(表示“好了，我已经处理了这个项目”)时才让项目出队，即 ACK 消息。值得注意的是，这在精神上是相似的，但与 TCP ACK 不同；它是由 RabbitMQ 实现的特性，而不是由底层传输协议实现的。RabbitMQ 让确认变得异常简单:

```
q.subscribe(:ack => true, :block => true) do |delivery_info, properties, body|
  puts "Received task/URL: #{body}"
  work body

  ch.ack(delivery_info.delivery_tag)
end
```

注意第一行中的参数`:ack => true`。这告诉 RabbitMQ 它应该让东西出列，直到收到 ACK。然后，在调用`work body`之后，我们用`ch.ack(delivery_info.delivery_tag)`通过信道发回一个 ack。为了正确地实现 ACK，我们只需要捕捉一些我们知道有可能发生的异常(例如，没有网络连接)。

## 交换

到目前为止，我一直在介绍 RabbitMQ 消息传递模型的简化版本。生产者实际上并不发送任何东西到队列中，而是将所有东西发送给一个叫做*交换*的中间人。交换决定向哪个队列发送消息。在所有代码示例中，我们都是这样做的:

```
ch.default_exchange.publish(message, :routing_key => q.name)
```

我们使用“默认交换”,它根据我们给它的路由关键字来路由消息，在本例中，这个关键字是`q.name`。这意味着消费者/工作者只有在操作同一个`q.name`时才会收到消息。

现在，如果我们运行两个工人，那么每个工人平均将得到总任务的一半。如果我们想安排一下，让每个员工都能收到每条信息，会怎么样？用例不难想象:假设我们在每台计算机上都有一个工作人员，并且每台计算机都需要主服务器将要提供的所有 URL 的副本。我们想要的是“发布/订阅”模式。

很容易想到一种方法来实现这一点:给每个工作者自己的队列，并确保从主服务器发送的所有消息都排队到每个工作者队列的*上。为此，我们将使用一个“扇出”交换，它将消息发送到与之相关的所有队列，而不是只发送到一个队列。*

这会在主服务器上产生一个新的`start`方法:

```
def start
  @conn.start
  ch = @conn.create_channel
  exchange = ch.fanout("http-fanout")

  @urls.each do |url|
    exchange.publish(url)
    puts "Sent url: #{url}"
  end
end
```

重要的一行是`exchange = ch.fanout("http-fanout")`我们告诉 RabbitMQ 创建什么样的交换。然后，我们用`exchange.publish(url)`发布我们的消息。请注意，我们不再需要路由关键字，因为扇出交换发送到所有与之相关的队列。

现在，我们将处理问题的下一部分，这是为每个工人创建一个队列，并将这些队列与交换相关联。我们在工人守则中有以下内容:

```
def start
  @conn.start
  ch = @conn.create_channel

  #instead of using a queue with a name,
  #we tell RabbitMQ that we want a temporary
  #queue (i.e. can get rid of it after this
  #worker dies) that only one worker
  #gets to use.
  q = ch.queue("", :exclusive => true)

  #get the exchange
  exchange = ch.fanout('http-fanout')

  #we need to tell RabbitMQ that this
  #queue is connected ("bound") to the
  #"http-fanout" exchange - see http_master_fanout.rb
  q.bind(exchange)

  puts "Waiting for tasks on queue #{q.name}"
  q.subscribe(:block => true) do |delivery_info, properties, body|
    puts "Received task/URL: #{body}"
    work body
  end
end
```

当我们用`q = ch.queue("", :exclusive => true)`创建队列时，通过提供一个空名称，我们让 RabbitMQ 选择一个随机的名称。使用`:exclusive => true`，我们告诉它这个队列只对这个工人有意义。然后，通过`q.bind(exchange)`,我们将队列与主设备正在使用的交换绑定(或关联)。就这样吧！当我们用几个工人运行主服务器时，我们可以看到 URL 被发送到每个工人，而不仅仅是在他们之间分发。

## 包装它

我从 C 和 TCP 套接字中的网络代码开始。总的来说，这是一次痛苦而乏味的经历。你必须在精神上处理一百万件事情，调试是一场噩梦。RabbitMQ 和 Ruby 走到一起，解决了这些问题，让沟通变得简单而优雅。

到目前为止，我们只讨论了 RabbitMQ 的一小部分，还有很多其他的东西可以用它来做。但是，本文中的基础知识应该足以让您编写一些非常棒的分布式代码。

## 分享这篇文章