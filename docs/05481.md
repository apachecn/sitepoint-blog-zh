# 代理:Ruby 中的类 Go 并发

> 原文：<https://www.sitepoint.com/agent-go-like-concurrency-ruby/>

![ruby-go](img/4c8d49b31fa5f785922600a89aaaa7bf.png)

并发和并行是很难的。使用传统的线程模型，开发人员必须不断确保线程不冲突，这是一项非常困难的任务。支持 [Go](http://golang.org/) 的团队在语言中实现了奇妙的并发原语，使得对并发代码的推理变得更加容易。

幸运的是，所有的美好不再被锁在围棋里面；现在有一个 Ruby 的图书馆了！它被称为[“Agent”](https://github.com/igrigorik/agent)，它在 Ruby 中实现了 Golang-concurrency 魔法。

在我们开始使用它之前，了解一些背景知识是很重要的。

## 基础

并发的 Go 模型基于[π演算](http://en.wikipedia.org/wiki/%CE%A0-calculus)和[通信顺序进程(CSP)](http://en.wikipedia.org/wiki/%CE%A0-calculus)的理论。尽管这些概念背后的数学非常有趣，但一个精简版将让我们更快地跟上速度。

在过去，我们有线程可以通过共享内存来相互通信。这引起了各种各样的问题。有时，两个线程会试图同时写入同一内容，或者一个线程会试图读取另一个线程正在写入的内容。尽管计算机科学家很聪明，但他们提出了锁、信号量等概念。来解决这些问题。

Go/CSP 采取了不同的方法。并发“进程”(这里，我不是指*nix 术语中的进程)与消息通信，而不是共享内存。Go 将这些“进程”实现为“goroutines ”,它类似于线程，但在它们消耗的资源方面要“轻量级”得多。最初，与仅仅让线程访问同一组变量相比，整个“消息传递”概念似乎非常有限。但事实证明，在并发代码的“正确性”方面，将我们自己限制在一点点会产生奇妙的结果。

Agent 很好地利用了 Go 并发性，并用 Ruby 实现了它。然而，在使用代理(或任何基于 ruby 的并发框架)时，有一点需要注意:标准的 Ruby 解释器实现了[绿色线程](http://en.wikipedia.org/wiki/Green_thread)。这意味着线程实际上不是运行在单独的处理器上。

代理，在我们所接触到的抽象之下，使用绿色线程。因此，它可以提供并发性(两个操作在同一时间段运行，但不一定都在同一时刻执行一个操作)，但不提供并行性(两个操作在完全相同的时刻运行)。这种安排有几个好处，但是缺点也很明显:如果您想在多个内核上分配工作负载，代理(可能还有标准的 Ruby 解释器)并不是最好的选择。

现在我们已经有了大致的概念，让我们继续看代码。

## 戈里普斯

首先，给你自己弄一份代理:

```
gem install agent
```

你可以在这里找到与本文[相关的所有代码。](https://github.com/ruprict/ruby-go-agent)

Agent/Golang 中并发的基本单元是一个“goroutine ”,它是一种轻量级线程。代理使创建和运行这些软件变得非常简单:

```
require 'agent'

#go routine
go! do
  puts 'hello, world!'
end

#wait around
loop do
end
```

让我们快速浏览一下我们正在做的事情。我们将一个块传递给`go!`函数(由代理提供)，从而运行一个执行该块的 goroutine。请注意，在这段代码的末尾有一个永久循环。如果没有这段代码，goroutine 将永远没有时间完成，因为程序将在 goroutine 完成之前退出！显然，这种“永远等待”的方法既不实际也不优雅。但是，为了想出更好的东西，我们需要某种机制让 goroutines 在它们之间以及与执行的主线程进行通信。

## 频道

通道是 goroutines 之间的通信机制。您可以将内容添加到频道中，也可以从中取出内容。

如果概念还不清楚，一些代码会有所帮助:

```
require 'agent'

chan = channel!(Integer)

go! do
  #the program should only end
  #when this goroutine ends
  sleep 10

  puts "Hello, world!"
  chan << 1
end

puts chan.receive.first
```

这个片段一直等到消息“Hello，world！”被打印，然后退出。在程序的开头，我们使用`channel!`创建`chan`。`channel!`的第一个参数是一个类名，它告诉代理`chan`应该只接受类型为`Integer`的消息。查看 goroutine 块内部，我们有语句`chan . This means: "take the` `Integer` 1 并将其添加到名为`chan`的通道中。“然后在 goroutine 之外，我们从通道中读取一个值。

这里要注意的非常重要的一点是，通道读取通常是阻塞的，这意味着如果通道中没有要读取的内容，它们不会立即返回。相反，他们会等到有书可读的时候。这对于上面代码片段的执行至关重要。如果`chan.receive.first`立即返回，程序将在完成 goroutine 之前完成执行。

碰巧的是，通道发送也阻塞了我们现在正在使用的通道类型。如果出于某种原因，您希望在通道上进行非阻塞发送，该怎么办？你可能会想到一种方法:

```
go! do
  chan << 1
end
```

我们稍后还将研究缓冲通道，它可以让您做大致相同的事情。

现在，让我们将一些代理知识应用到工作中。

## Echo 服务器:线程池与 Goroutines

在编写服务器时，我们经常需要使用并发性，因为它们必须同时服务大量的客户端。使用线程池作为并发的方法是解决这个问题的传统方法。实际上，在服务器初始化时会创建几个线程，每个线程一次服务一个客户端。然后，一旦一个线程处理完一个客户机，它就准备好为另一个服务。

但是这种方法有各种各样的问题。首先，很难以高性能的方式实现这一点，因为决定何时分配更多线程的算法(在现有线程池不足的情况下)会导致大量开销，要么创建太多线程，要么不必要地破坏它们。

您可能会问:“为什么我们不为每个客户端创建一个线程呢？”创建一个线程会有很大的开销。因此，对于必须处理成千上万个客户机的服务器(目前相当普遍)，这种方法是不实际的。另一方面，goroutines 比线程消耗的资源少。这种差异非常显著，为每个客户端创建一个 goroutine 是非常合理的。这可以使快速并发服务器的实现更快。

让我们看看如何在代理中做到这一点:

```
require 'agent'
require 'socket'

class EchoServer
  attr_accessor :host, :port

  def initialize(port)
    @port = port
    @server = TCPServer.new @port
  end

  def handle_client(client)
    loop do
      line = client.gets
      client.puts line
    end
  end

  def start
    loop do
      client =  @server.accept
      puts 'accepted'
      go! do
        handle_client(client)
        #could do other stuff here
      end
    end
  end
end

server = EchoServer.new(1337)
server.start()
```

大部分代码只是建立结构。中心思想保存在`EchoServer.handle_client`和`EchoServer.start`中。在后一种情况下，我们有一个主应用程序循环，它一直等到客户端被接受。然后，它调用`handle_client`作为一个 goroutine，与客户建立“回应”(即重复客户所说的话)。请注意，每个客户端都需要一个新的 goroutine。如果在生产中使用类似的东西，显然需要检查`handle_client`中的错误，并根据需要退出 goroutine。

## 经纪人和戈兰戈鲁丁斯的区别

如前所述，goroutines 比线程消耗更少的资源。事实上，一个普通的服务器可以执行多达 100，000 个并发的 goroutines。但是，代理在底层运行 Ruby green 线程。两者的实现不同(堆栈分配、上下文切换等。)这意味着 Go 的性能优势可能不会完美地转化为 Agent。因此，您必须了解(即基准测试)基于代理的应用程序的性能(就像您在尝试任何新技术时一样)。不管怎样，Agent 提供了一种极好的方式来推理 Ruby 中的并发性。

## 选择频道

一旦您开始使用 Agent 编写较大的代码，您将会意识到一个通道通常是不够的。大多数时候，你将会面对多个渠道。通常有一部分代码必须根据哪些通道准备好被读取或写入来决定做什么。这就是“选择”调用的来源。

`select!`在代理中，精神上类似于[的 POSIX 调用](http://linux.die.net/man/2/select)，
允许我们根据哪个通道准备好读/写来行动。

让我们看一个例子:

```
require 'agent'
require 'socket'

#this example involves selecting between
#two channels.

$string_chan = channel!(String, 1)
$int_chan = channel!(Integer, 1)

def process_chan
  loop do
    select! do |s|
      s.case($string_chan, :receive) do |value|
        puts "Received string: ", value
      end

      s.case($int_chan, :receive) do |value|
        puts "Received integer: ", value
      end
    end
  end
end

go! do
  process_chan
end

$string_chan << "hello"
$int_chan << 18
$string_chan << "world"

#in a typical design, this would not actually
#be here; more or less specific to this example
loop do
end
```

虽然代码一开始看起来有点复杂，但实际上相当简单。其症结在于`process_chan`法；其中包含了`select!`语句。这在两个通道之间“选择”`$string_chan`和`$int_chan`。使用`s.case($string_chan, :receive)`，检查`$string_chan`是否可以被读取(“接收”)。使用块符号，将从`$string_chan`读取的值存储到`value`中，然后打印出来。我们对`$int_chan`也是如此。在某种意义上，这可以被认为是事件驱动的编程风格:我们在等待一个给定的事件(其中一个通道变得可读)。

## 缓冲通道

到目前为止，我们大多使用无缓冲通道，尽管我在上一个例子中使用了缓冲通道。对于这些，发送和接收都是阻塞的，这意味着这样做:

```
unbuffered_chan << 1
```

等待直到`unbuffered_chan`被实际读取。

另一方面，缓冲通道允许您不必等到通道“满”就可以这样做。缓冲通道声明有一个大小，并且发送是非阻塞的，直到通道上“等待”(即未读取)的消息数达到该大小。如果您熟悉队列数据结构，缓冲通道的概念几乎是相同的:先进先出。也就是说，如果你在频道中放入 50 条信息，你先放入的那条信息会先被读取。如果通道的大小是 50，只有在试图将第 51 条消息发送到通道中时(此时 50 条消息尚未被读取),发送调用才会被阻塞。

让我们看一个简单的例子:

```
require 'agent'

buffered_chan = channel!(Integer, 2)

#this goroutine makes Ruby think
#there isn't a deadlock
go! do
  loop do
  end
end

#should not block
buffered_chan << 1
puts 'added one message to buffered_chan'

#should not block
buffered_chan << 1
puts 'added one message to buffered_chan'

#will block
buffered_chan << 1
puts 'this should never print'
```

代码的基本思想很简单。我们清除一个缓冲大小为 2 的缓冲通道。然后，尝试将第三条消息填充到缓冲通道中，而不先读取它，这将永远阻塞。我们确实需要使用一些技巧来确保 Ruby 不会认为存在死锁(也就是说，线程只是毫无理由地等待)。

那么，有什么意义呢？如果通道中的消息被缓冲，即保持到时间密集型的 goroutine 到达它们那里，进程可以更快地进行，而不是一个 goroutine 等待另一个(可能执行时间密集型的操作)从通道中读取和操作数据。这种类型的构造大量用于多核处理(如前所述，这在 Agent/Ruby 的绿色线程上是不可能的)，但它也可以应用于其他需要并发的领域。

## 包装它

我希望你喜欢这次 Agent 之旅，这是一个 Ruby 和最好的 Go 相碰撞的库。在过去的一年里，我做了很多 Go 开发，我经常发现自己在 Ruby: Agent 中寻找 Go 的并发特性，这是一个非常积极的解渴步骤。

## 分享这篇文章