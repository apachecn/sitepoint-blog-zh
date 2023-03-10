# 用 Ruby 构建一个端口扫描器

> 原文：<https://www.sitepoint.com/build-a-port-scanner-in-ruby/>

有时候，我们把自己封闭在只有 HTTP 的世界里。有时候，Rails 让 Ruby 的其他方面黯然失色。

在本文中，我们将用纯 Ruby 构建一个简单的端口扫描器，使用 Ruby 发行版附带的套接字支持。

端口扫描器并不是我们期望 Rubyists 创造的典型事物；这就是 C 语言的人要处理的事情。当许多人发现 [Metasploit](http://www.metasploit.com) (一个令人惊叹的渗透套件)实际上是用 Ruby 编写的时，他们感到很惊讶。

事实是，Ruby 已经成长为一种非常通用的语言，使它成为一种可以用于多种用途的工具，包括像 systems 这样的端口扫描器。

让我们开始吧！

## 基础知识

首先，什么是端口扫描器？听起来确实如此；它接受一个 IP 地址，并告诉你哪些端口是“开放的”，也就是说，计算机准备在哪些端口上进行通信。

扫描端口的目的是什么？开放端口可能会带来重大的安全风险，尤其是如果管理员不知道它们为什么一直开放的话。另一端的程序可能是某个服务器软件的旧版本，有大量公开的缓冲区溢出攻击，使机器成为攻击者的容易目标。所以，知道你打开了哪些端口是很重要的。

最后，我们如何知道端口是否开放？实际上有很多方法可以做到这一点，因为 TCP/IP(计算机通常用来相互通信的协议)提供了很多不同的方法(各有不同的优缺点)来检查端口。最简单的一种(也是我们将要使用的一种)叫做“TCP 连接扫描”。

想法是尝试在指定的端口上连接到主机，如果主机响应，则端口打开，否则不打开。这种方法确实有缺点。如果您是攻击者，主机可能会记录该请求。其次，它比其他一些方法(如 TCP SYN 扫描)效率低得多。

我们的端口扫描器将采用一种简单的技术；它需要一系列端口和一台主机，然后尝试在每个给定的端口上连接到主机。只要连接成功，端口就会打开。

让我们进入代码。

## 简单的方法

为了完成我们的目标，我们将使用 Ruby 提供的套接字库。代码如下:

```
require 'socket' #socket library

def open_port(host, port)
  sock = Socket.new(:INET, :STREAM)
  raw = Socket.sockaddr_in(port, host)
  puts "#{port} open." if sock.connect(raw)

rescue (Errno::ECONNREFUSED)
  rescue(Errno::ETIMEDOUT)
end

def main(host, start_port, end_port)
  until start_port == end_port do
    open_port(host, start_port)
    start_port += 1
  end
end

main ARGV[0], ARGV[1].to_i, ARGV[2].to_i
```

可以看出，代码非常简单。在我们进入细节之前，您可以在自己的机器上运行扫描仪:

```
ruby scanner.rb localhost 1 100
```

这将在本地主机(即运行代码的机器)上运行扫描器，检查从 1 到 100 的所有端口。

该代码将要扫描的地址作为第一个参数，起始端口作为第二个参数，结束端口作为第三个参数，并打印打开的端口。让我们分解代码，看看它是如何做到这一点的。

首先，使用`ARGV`数组从命令行读取参数。将这些参数传递给 main 方法，然后 main 方法简单地遍历所需的端口，并使用`open_port`方法检查它们是否打开(该方法还打印输出)。

如前所述，通过在指定端口打开与我们正在扫描的机器的连接来查看它是否响应。但是，这个实现有一些问题。它会等到扫描完一个端口后再移动到下一个端口。然而，它显然不需要；我们检查端口的顺序与结果无关。所以，让我们试着写一个不等待的端口扫描器的 a 版本。

## 更进一步

我们如何做到这一点？很明显，我们需要一些方法来委派任务，这样它们就可以在“正常”的事情过程之外完成，也就是说。我们不必等他们。这通常被称为并发。

在 Ruby 中进行并发有一些问题。首先，Ruby 的“官方”(意思是 MRI)实现实现了一个单独的“线程”(有点像进程，除了更轻量级)不能同时运行的系统；它们都在一个线程下运行。这意味着两件事不可能同时发生。因此，我们不能同时扫描两个端口。然而，这并不意味着 Ruby 在这种情况下是无用的——如果我们将端口分成不同的线程，当我们等待其他套接字时，这些线程仍然可以被调度运行。

我们将如何处理所有这些线程？我个人是 Ruby 的赛璐珞库的粉丝，它使用演员模型使线程化变得轻而易举。事实上，我非常喜欢它，我甚至写了一篇关于它的文章，你可能应该读一下，因为我将在本文的其余部分使用赛璐珞。

这里有一个非常快速的 n' dirty 实现，我们将对其进行改进:

```
require 'celluloid'
require 'socket'

class ScanPort
  include Celluloid

  def initialize(port, host)
    @port = port
    @host = host
  end

  def run
    begin
      sock = Socket.new(:INET, :STREAM)
      raw = Socket.sockaddr_in(@port, @host)

    puts "#{@port} open." if sock.connect(raw)

    rescue
      if sock != nil
        sock.close
      end
    end

  end
end

def main
  host = ARGV[0]
  start_port = ARGV[1].to_i
  end_port = ARGV[2].to_i

  until start_port == end_port do
    sp = ScanPort.new start_port, host
    sp.async.run

    start_port += 1
  end
end

main
```

哇，乍一看这似乎很复杂。实际上，我们重用了早期版本中的大量代码。首先，我们定义了一个名为`ScanPort`的类，它将用于产生“actors”(基本上是线程中的对象)。注意类中的`include`行；这实质上使这个类成为了一个演员。

我们在`ScanPort`类中有`run`方法(我只是选择称它为`run`；我们可以称它为任何我们想要的东西)它做的事情与早期版本中的`openport`方法基本相同，除了它在检查端口后立即关闭套接字。我们这样做是为了使打开的套接字数量不超过系统限制。

`main`方法很简单。最重要的一行是第 36 行——注意我们不叫`sp.run`,而是叫`sp.async.run`。这将异步调用`run`方法，也就是说，我们不等待它结束就继续前进。

另一个需要注意的要点是，为了简洁起见，代码示例省略了许多错误处理——它肯定应该使用某种监督组(这是一个赛璐珞概念)。

当运行时，它给出了与前面大致相同的结果。您可能会注意到，它的运行时间与同步版本差不多，甚至更长！问题是，我们正在为我们提供的范围内的每个 *单个*端口启动一个线程——这占用了时间和资源！相反，如果我们只给每个参与者分配一个要检查的端口范围，会怎么样呢？这将意味着更少的线程，因此启动和关闭线程花费的时间更少！让我们试一试:

```
require 'celluloid'
require 'socket'

class ScanPort
  include Celluloid

  def initialize(start_port, end_port, host)
    @start_port = start_port
    @end_port = end_port
    @host = host
  end

  def run
    until @start_port == @end_port do
      scan @start_port
      @start_port += 1
    end
  end

  def scan(port)
      begin
        sock = Socket.new(:INET, :STREAM)        #build the socket
        raw = Socket.sockaddr_in(port, @host)

        puts "#{port} open." if sock.connect(raw)
      rescue
        if sock != nil
          sock.close
        end
      end
    end
end

def main
  host = ARGV[0]
  start_port = ARGV[1].to_i
  end_port = ARGV[2].to_i

  segment_size = 100

  until start_port >= end_port do
    sp = ScanPort.new start_port, start_port + segment_size, host
    sp.async.run

    start_port += segment_size
  end
end

main
```

代码基本上是相同的，但是，我们添加了一个`segment_size`变量，您可以根据希望每个参与者处理多少端口来更改这个变量。将这个数字设置得太高意味着几乎没有并发性，设置得太低意味着仅仅创建和销毁线程就要花费大量时间。我在我的机器上运行了 1 到 2000 个端口，结果大约是同步版本实际时间的 50%。显然，有很多因素会影响这一点，但这是总体思路。

如果你想让你的代码以“真正的”并发方式运行，也就是说，让线程与其他线程同时运行，你必须选择一个没有“单线程下的一切”问题的 Ruby 实现。JRuby 是一个受欢迎的选择。

## 包装它

端口扫描器是非常有趣的软件。希望你喜欢用 Ruby 创建一个端口扫描器版本(尽管很简单)。

## 分享这篇文章