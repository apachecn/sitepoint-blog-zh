# Ruby 中的分叉和 IPC，第二部分

> 原文：<https://www.sitepoint.com/forking-ipc-ruby-part-ii/>

![Fork of three roads](img/2b8d0d099ca06778211ddc32755ff425.png)

在第一篇文章的[中，我们研究了为什么`fork()`系统调用是有用的，以及它在大计划中的位置。我们看到，通过将一个块传递给`Kernel#fork`或`Process#fork`，可以并发执行任意代码(或者如果有多个处理器，可以并行执行)。此外，我们看到，尽管分叉相对昂贵，但如果 Ruby 实现不浪费写时复制优化，它可以与线程竞争。](https://www.sitepoint.com/?p=73112 "Part 1")

不幸的是，如果一个方法在 fork 中被调用，由于进程隔离，它产生的任何数据对父进程都是不可用的。我们需要的是一个管道，通过它我们可以在进程之间传递数据。在这里，在第二部分中，我们将介绍一些进程间通信机制以及更多可以利用`fork`的方法。

本文中的代码可以从 [github](https://github.com/rlqualls/ruby_forking_tutorial) 获得。

## 管道

管道允许数据在一对文件描述符之间单向流动。由于 forks 继承了打开的文件描述符，管道可以用来在父进程和子进程之间传递数据。用 IO#pipe 在 Ruby 中创建管道很容易。

```
>> reader, writer = IO.pipe
=> [#<IO:fd 5>, #<IO:fd 6>]
```

在这里，作者只会写，读者只会读。听起来很简单，可惜有些细微差别。如果你想多次使用一个管道，`IO#puts`加`IO#gets`是最简单的方法，

```
>> writer.puts("hello world")
>> reader.gets
=> "hello world\n"

>> writer.puts("hello world, again")
>> reader.gets
=> "hello world, again\n"
```

管道使用字节流传递数据，因此它们需要分隔符来知道何时停止读取数据。`IO#gets`读取，直到收到一个“\n”。与`IO#puts`不同，`IO#write`不会自动将“\n”附加到数据上，因此后面跟有`IO#write`的`IO#gets`将无限期阻塞。

```
>>writer.write("this string is terminated\n")
>>reader.gets
=> "this string is terminated\n"

>> writer.write("this string is not terminated")
>> reader.gets
*waits indefinitely for \n*
```

与`IO#gets`不同，`IO#read`会无限期等待 EOF。IO 对象在关闭时会发出 EOF 信号，因此您将只对一次性写入使用`IO#read`。

```
>>reader,writer = IO.pipe
>>writer.write("hello world")
>>writer.close
>>reader.read
=> "hello world"
>>reader.read
=> ""
```

好，那么我们如何在一个父进程和它的一个子进程之间连接管道呢？由于变量和文件描述符将被共享，我们只需要创建一次管道。然而，由于 fork 将复制管道的两端(reader 和 writer)，我们需要选取两个额外的末端并关闭它们。以下是如何将数据从子节点发送到父节点的方法。

```
child_parent_pipe.rb
# creates a fork and pipes a string from child to parent

reader,writer = IO.pipe

fork do
  reader.close
  writer.puts "sent from child process"
end

writer.close
from_child = reader.gets
puts from_child
```

我们也可以让它以另一种方式工作。只要确保在将要读取的一端关闭 writer，在将要写入的一端关闭 reader。

```
parent_child_pipe.rb
# creates a fork and pipes a string from parent to child

reader,writer = IO.pipe

fork do
  writer.close
  from_parent = reader.gets
  puts from_parent
end

reader.close
writer.write "sent from parent process"
```

## UNIX-套接字

您可以将 UNIX 域套接字视为具有两大优势的管道:

*   UNIX 套接字是双向的，而管道是单向的
*   管道只能使用字节流，但是 UNIX 套接字也可以使用数据报

与常规套接字不同，它们只能在同一台机器上的两个点之间传递数据，使用文件系统作为它们的地址空间。因此，它们非常轻量级，非常适合进程间通信。

```
unix_sockets.rb
# creates a pair of UNIX sockets that send and receive a string

require 'socket'
parent_socket, child_socket = UNIXSocket.pair

fork do
  parent_socket.close
  child_socket.send("sent from child (#{$$})", 0)
  from_parent = child_socket.recv(100)
  puts from_parent
end

child_socket.close
parent_socket.send("sent from parent (#{$$})", 0)
from_child = parent_socket.recv(100)
puts from_child
```

## 分布式 Ruby

管道和 UNIX 套接字都有一些缺点:

1.  它们是低级的字节传输机制。对于复杂的行为，您需要实现现有的协议或定义自己的协议。
2.  它们不能跨越机器障碍进行通信，这限制了它们在大规模可扩展场景中的可用性。

分布式 Ruby 允许你创建和消费所谓的“分布式对象服务”这些服务允许您通过向分布式对象发送消息来远程执行代码。

我们一直在远程机器上执行代码，但是我们是通过相当多的间接方式来完成的。例如，假设您访问一个像 http://searchforallthestuffs.com/search?q=ponies.这样的网址，您的请求到达一个路由器，该路由器看到您指定的路由并将参数传递给相应的控制器，然后控制器执行与该路由相关的代码(生成 html 视图、JSON、XML 等)。

如果你的目标是远程执行代码，这…有点糟糕。每次添加新功能时，您都需要一条合适的路线，可能还需要一个新的控制器。仅仅是给一个类添加一个方法就有很大的开销。

分布式对象使您可以远程执行代码，但是对象接收消息而不是地址。

```
distributed.rb
# Creates several worker processes and concurrently waits for the fastest one

require 'drb'

NUM_WORKERS = 4

class Worker

  def calculate
    time_to_work = rand(1..7)
    sleep time_to_work
    return time_to_work
  end

  def stop
    DRb.stop_service
  end
end

# Start object services
NUM_WORKERS.times do |i|
  DRb.start_service("druby://:700#{i}", Worker.new)
  puts "Worker running at #{DRb.uri}"
end

# Create a local end-point for each service
workers = NUM_WORKERS.times.map { |i| DRbObject.new nil, "druby://:700#{i}" }

# Concurrently wait for the fastest calculation
thread_pool = []
NUM_WORKERS.times do |i|
  thread_pool << Thread.new do
    answer = workers[i].calculate
    puts "Worker #{i} finished in #{answer} seconds"
  end
end

# Wait for every thread to get its answer
thread_pool.each(&:join)

# Shut down each worker
workers.each { |w| w.stop }
```

## 在进程间移动对象

因为像管道和套接字这样的低级通信结构传输字节，而不是对象，所以您需要将您的对象编码成字节格式——即序列化它们——以便跨越流程障碍移动它们。

幸运的是，Ruby 附带了能够序列化大多数 T2 Ruby 对象的 T0。

来自[Ruby-Doc.org](http://ruby-doc.org/core-1.9.3/Marshal.html):“有些对象是不能转储的:如果要转储的对象包括绑定、过程或方法对象、类 IO 的实例或单例对象，将会引发一个类型错误。”

```
serialization.rb
# Ruby objects can be serialized by Marshal to move across pipes
# or sockets

Tire = Struct.new(:radius, :pressure)

reader, writer = IO.pipe

fork do
  reader.close
  tire = Tire.new(7, 28)
  tire_data = Marshal.dump(tire)
  writer.write tire_data
end

writer.close
tire_data = reader.gets
tire = Marshal.load(tire_data)
puts tire.inspect
```

## 为异步方法调用创建模块

如果您可以在进程间移动对象，那么您可以在另一个进程中执行一个方法，并在原始进程中获得结果。这有效地实现了异步方法执行。

这里，我有一个名为 Forkable 的模块，它给了一个类并行执行方法的能力。唯一与以前不同的是，管道是在一个新的线程中读取的，从管道中出来的内容被提交给块。

```
forkable.rb
# A module that lets you fork a method and get its result in a block
# Notes: 
#   1\. Forked methods can't take blocks in this implementation
#   2\. Call back threads will be destroyed if the main process exits
#   3\. Not production ready. For educational purposes only.
####################################################################

# If included in a class #fork_method will run a method in another process
# and yield the result to a block
module Forkable
  def fork_method(method, *args)
    reader, writer = IO.pipe
    fork do
      reader.close
      result = self.send(method, *args)
      child_data = Marshal.dump(result)
      writer.puts(child_data)
    end

    writer.close
    Thread.new do
      data_from_child = reader.gets
      yield Marshal.load(data_from_child) if block_given?
    end
  end
end

# An object that takes a random amount of time to instantiate
class ExpensiveObject

  attr_reader :expense

  def initialize(max_expense)
    @expense = rand(1..max_expense)
    sleep @expense
  end
end

# A worker that forks, a...forker
class Forker
  include Forkable

  def calculate(max_expense)
    return ExpensiveObject.new(max_expense)
  end
end

# Create 3 ExpensiveObjects and print how long they took to create
f = Forker.new
3.times do 
  f.fork_method(:calculate, 7) do |result|
    puts "result: #{result.inspect}"
  end
end

puts "waiting on results..."

Process.waitall
puts "main process finished"
```

## 鳕鱼

Cod 是一个旨在让 Ruby 中的 IPC 更简单的库。在上一节中，我演示了如何序列化对象，以便通过管道或套接字等字节传输机制进行传输。货到付款会帮我们处理的。它使用更高级别的 IPC 机制，称之为通道。通道不仅不需要在每个进程的一端关闭，还可以传输 Ruby 对象。用`Cod#pipe`在 Cod 中创建了一个通道(无论多么奇怪)。

```
cod.rb
# The cod gem simplifies IPC. Ruby objects can be sent across channels.
# Installation: 
#   $ gem install cod

require 'cod'

Tire = Struct.new(:radius, :pressure)

channel = Cod.pipe

3.times do
  fork do
    radius = rand(8..14)
    pressure = rand(24..33)
    channel.put Tire.new(radius, pressure)
  end
end

tires = 3.times.map { channel.get }
puts tires.inspect
```

cod gem 的功能超出了我在这里所能涵盖的范围(例如，它集成了 beanstalkd)。网站上有很多例子，所以一定要去看看。

## 分支前服务器

让我们看一个实践中常见的分叉例子:服务器。一些使用分叉来实现并行性的服务器包括:

*   预工作多重处理模块
*   独角兽企业
*   彩虹！(基于独角兽)

具体来说，分叉服务器一般使用预分叉。这个概念是这样工作的:派生一个过程可能比复制一个过程更便宜，但是它不是免费的。由于运行 web 服务器通常包括打开和关闭大量短期连接，所以等到新连接被接受后再创建新的子进程可能不是一个好主意。相反，我们可以继续分叉几次，让分叉等待连接。您将经常看到这被描述为运行一个“进程池”

那会是什么样子呢？

```
preforking.rb
# Starts an echo server that can service 3 clients in parallel

require 'socket'

server = TCPServer.new 'localhost', 3000

trap("EXIT") { socket.close }
trap("INT") { exit }

3.times do
  fork do
    sock = server.accept
    sock.puts "You are connected to process #{$$}:"
    while recv = sock.gets
      sock.puts("ECHO:> #{recv}")
    end
  end
end

Process.waitall
```

如果您运行这段代码，您应该能够连接 3 个独立的终端。

```
telnet localhost 3000
```

## 平行宝石

[parallel gem](https://github.com/grosser/parallel)提供了在 CRuby (MRI)中实现并行的能力，而无需深入 forks 和 IPC 的本质。这里有一个快速的方法让你所有的 CPU 内核并行运行。

```
parallel.rb
# Parallel iterates across collections with processes or threads
# Installation: 
#   $ gem install parallel

require 'parallel'

def calculate(magnitude)
  x = 0
  cycles = 10 ** magnitude
  cycles.times do
    x += 1.000001
  end
  return x
end

results = Parallel.map([6, 6, 6, 6, 7, 7, 7, 7]) do |mag|
  calculate(mag)
end

puts results
```

SitePoint 已经[有一篇关于并行 gem 的很棒的文章](https://www.sitepoint.com/code-safari-forks-pipes-and-the-parallel-gem/)，探索了它的内部工作方式(至少在 2011 年)。

程序员经常避免看源代码，因为害怕它变成一个巨大的时间接收器。然而，并行 gem 是一个相当小的单文件库。这是一个学习和贡献的好机会。

## 结论

即使其他解释器(JRuby、Rubinius)在没有全局解释器锁的情况下自由运行，以进程为中心的并发方法仍然是有用的。纵向扩展方式越多，它的局限性就越大。在多个内核上运行多个线程可能比提高时钟速度更加水平，但仍然比运行多台机器更加垂直。运行 100 个单核实例比运行 25 个四核实例更精细。从发送消息的流程的角度来考虑，可以让您接受这种粒度。这并不容易，但是一旦它与 10 个实例的集群一起工作，为什么不是 100 个呢？还是 1000？为什么不在一个供应商那里放 500，在另一个供应商那里放 500 呢？它们只是发送信息的过程。

在你可能会用到或可能不会用到的资源上预先花一大笔钱代表了旧的思维方式。为什么要买比你真正需要的更多的东西呢？

## 分享这篇文章