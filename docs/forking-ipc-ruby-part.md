# Ruby 中的分叉和 IPC，第一部分

> 原文：<https://www.sitepoint.com/forking-ipc-ruby-part/>

![Fork of three roads](img/2b8d0d099ca06778211ddc32755ff425.png)

我喜欢把分叉看作并发世界的失败者。事实上，在这一点上，很多程序员可能连听都没听过。术语“多线程”几乎已经成为“并发”或“并行”的同义词

系统调用 [fork()](http://pubs.opengroup.org/onlinepubs/009695399/functions/fork.html) 创建当前流程的“副本”。对于我们在 Ruby 中的目的，它支持任意代码异步运行。由于该代码将在操作系统级别进行调度，因此它将像任何其他进程一样并发运行。

本文的目标是让读者能够从进程而不是线程的角度来思考问题。我们将会看到，以这种方式思考有很多好处。

本教程的源代码可以从 github 上的[获得。如果有疑问，试着从那里运行代码。](http://github.com/rlqualls/ruby_forking_tutorial)

注意:由于 fork()是一个 POSIX 系统调用，所以如果您在 Windows 中运行 Ruby，本教程中的代码不会起太大作用。如果你想尝试 Linux、BSD 或其他类似 UNIX 的操作系统，我推荐使用 virtualbox。

## 全局解释器锁

花几分钟阅读一下 Ruby 中的并发性，你会发现一个很有争议的话题:全局解释器锁。坦白地说，由于 Ruby 社区中流传的大量关于 GIL 的错误信息，它的名声比它应得的还要差。要了解 GIL 带给你什么，有必要了解并发性和并行性之间的[差异](https://vimeo.com/49718712):

*   **并发**–两个任务在重叠的时间段内执行(但执行的速度让他们感觉是同时进行的)，即一边听音乐一边编辑文档。

*   **并行性**–两个任务同时在不同的处理器内核上执行

在撰写本文时，CRuby 的 GIL 将 Ruby 虚拟机限制为一次一个本机线程。如果虚拟机中有多个线程，每个线程轮流在本机线程上运行。因此，线程可以并发执行，但不能并行执行。现在，这并不是说并发是可能的，而并行是不可能的，我们马上就会看到。

如果你想更多地了解 GIL，我推荐这些帖子:

*   [mer BIST——关于并发性和 GIL](http://merbist.com/2011/10/03/about-concurrency-and-the-gil/)—[杰西·斯托里默——没人理解 GIL](http://www.jstorimer.com/blogs/workingwithcode/8085491-nobody-understands-the-gil)
*   [Igvita——并行是 Ruby 的一个神话](http://www.igvita.com/2008/11/13/concurrency-is-a-myth-in-ruby/)
*   [Yehuda Katz–Threads(Ruby):已经够了](http://yehudakatz.com/2010/08/14/threads-in-ruby-enough-already/)

## 快速实验

您可以从 CRuby (1.9.3、2.0.0 等)的存储库中运行这段代码，看看使用 forks 和 threads 的区别。

```
# thread_fork_comparison.rb
# runs 4 tasks in 4 threads and 4 forks and reports the times for each

def time_forks(num_forks)
  beginning = Time.now
  num_forks.times do 
    fork do
      yield
    end
  end

  Process.waitall
  return Time.now - beginning
end

def time_threads(num_threads)
  beginning = Time.now
  num_threads.times do 
    Thread.new do
      yield
    end
  end

  Thread.list.each do |t|
    t.join if t != Thread.current
  end
  return Time.now - beginning
end

def calculate(cycles)
  x = 0
  cycles.times do
    x += 1
  end
end

cycles = 10000000

threaded_seconds = time_threads(4) {  calculate(cycles) }
puts "Threading finished in #{threaded_seconds} seconds"

forked_seconds = time_forks(4) {calculate(cycles) }
puts "Forking finished in #{forked_seconds} seconds"
```

输出:
1.670291209 秒完成穿线
0.419124546 秒完成分叉

与线程相比，使用 forks 花费了 1/4 的时间来完成。这是一个显著的性能提升。

注意:我使用四核处理器来获得这些结果。

## 分叉与线程

尽管限制了线程级并行性，但 CRuby core 团队决定暂时保留 GIL，他们有一个很好的理由:使用全局锁编写正确执行的多线程代码“更容易”。此外，每当一个解释器有一个 GIL 时，它所提供的保证周围的特性往往会增加，使得以后很难移除。

与“线程安全”不同，您很少会遇到“叉安全”这个词线程共享相同的内存，因此它们可以同时操作数据，这可能会损坏数据。另一方面，分叉的进程被赋予了一个新的虚拟内存空间，因此对分叉中数据的任何更改都将发生在新的空间中，而不是原来的空间中。这个概念被称为[进程隔离](http://en.wikipedia.org/wiki/Process_isolation)。

一个简单的比较如下:

*   穿线:
    *   并行性很容易破坏全局数据
    *   需要有选择地锁定数据以防止损坏
    *   比分叉便宜
    *   当程序退出时，线程被杀死

*   分叉:
    *   通过并行性破坏数据变得更加困难
    *   需要有选择地共享数据以实现合作
    *   有些昂贵，尤其是在不使用写入时复制的情况下
    *   当主进程正常退出时，子进程不会被终止

先把准备工作放在一边，让我们看看 Ruby 中的 forking 是如何工作的。

## 躲避僵尸

在 Ruby 中创建一个分支很容易。`Kernel#fork`可以获取一个块，并将该块的代码在另一个进程中执行。因为 fork 从它的父节点继承了终端，所以它的输出可以在同一个终端中看到。

```
# basic_fork.rb
# A fork inherits the terminal of its parent process

fork do
  sleep 2
  puts "Fork: finished"
end

puts "Main: finished"
```

分叉的最大危险之一是失去对工作进程的控制。与线程不同，当主进程正常退出时，子进程不会被杀死。虽然在某些情况下这可能是一件好事，但是很容易建立一个必须手动终止的僵尸进程集合。如果您将要创建许多流程，您可能希望方便地访问流程管理器。我个人喜欢 [htop](http://htop.sourceforge.net/) 。

```
# zombie_process.rb
# creates a process that won't end on its own. 
# Terminate it in the console with: 
#   $ kill [whatever pid the zombie has]

fork do
  puts "Zombie: *comes out of grave*"
  puts "Zombie: rahhh...kill me with: $ kill #{$$}"
  loop do
    puts "Zombie (#{$$}): brains..."
    sleep 1
  end
end

puts "Main (#{$$}): finished"
```

$$返回当前进程的 pid。如果您希望父进程知道它创建的任何子进程的 pid，您可以使用由`fork`返回的值。

```
# pid.rb
# Shows different ways of getting pids for parent and child processes

fork_pid = fork do
  puts "child: my pid is #{$$}"
  puts "child: my parent's pid is #{Process.ppid}"
end

puts "parent: my pid is #{Process.pid}"
puts "parent: my child's pid is #{fork_pid}"
```

有时子进程会无限循环运行。您可以存储由 fork 调用生成的 PID，并使用`Process#kill`在代码中终止子进程。

```
# process_kill.rb
# Shows how to terminate processes programmatically

puts "initializing worker processes..."

pids = 5.times.map do |i|
  fork do
    trap("TERM") do
      puts "Worker#{i}: kill signal received...shutting down"
      exit
    end

    loop do
      puts "Worker#{i}: *crunches numbers*"
      sleep rand(1..3)
    end
  end
end

sleep 5
puts "killing worker processes..."
pids.each { |pid| Process.kill(:TERM, pid) }
```

防止引入僵尸进程的一种方法是等待子进程完成。这样如果有孩子挂了，在终端会很明显。要做到这一点，只需在您希望程序阻塞的地方添加对`Process#waitall`的调用，直到每个分支完成其工作。如果您知道想要等待的任何进程的 pid，您可以使用`Process#wait`。

```
# process_wait.rb
# Sometimes it's useful to wait until all processes have finished

fork do
  3.times do
    puts "Zombie: brains..."
    sleep 1
  end
  puts "Zombie: blehhh *dies*"
end

Process.waitall

puts "Main: finished"
```

前面我说过，与线程不同，当主进程结束时，分叉不会自行终止。如果主进程正常完成，这是真的。如果它接收到中断信号(`SIGINT`)，就像`ctrl-c`一样，它会将中断信号传递给它的所有子节点，它们也会被中断。

所以，如果你使用`Process#waitall`，你有机会用一个快速的`ctrl-c`来中断每一个进程，如果其中任何一个进程挂起的话。

```
# shutup_kids.rb
# If a process receives an interrupt signal, it will pass it on to its children
# Send SIGINT with ctrl-c to make the kids shut up

kids = %w{Bubba Montana}

kids.each do |kid|
  fork do
    loop do
      puts "#{kid}: when.will.we.get.there."
      sleep 1
    end
  end
end

Process.waitall
```

有时像这样直接终止进程是不可取的。幸运的是，您可以使用 [`Kernel#trap`](http://ruby-doc.org/core-1.9.3/Kernel.html#method-i-trap) 优雅地关闭一个收到信号的进程。

如果您为特殊行为使用陷阱，请确保不要忘记用陷阱结束子进程。否则信号不会终止进程，因为默认行为会被覆盖。如果你发现自己在这种情况下，使用不同的杀死信号。例如，如果陷阱处理`SIGTERM`，发送一个`SIGKILL`或`SIGINT`。gnu.org 有一个关于信号的[伟大页面](http://ftp.gnu.org/old-gnu/Manuals/glibc-2.2.3/html_chapter/libc_24.html)。

```
# i_said_shutup_kids.rb
# Signal responses can be customized using Kernel#trap or Signal#trap
# Send interrupt with ctrl-c to shutup

kids = %w{Bubba Montana}

kids.each do |kid|
  fork do
    @whiny = true
    trap("INT") do
      puts "#{kid}: Ugh! Shutup signal RECEIVED, dad!"
      @whiny = false
    end

    loop do
      puts "#{kid}: when.will.we.get...there"
      sleep rand(1..2)
      break if not @whiny
    end

  end
end

Process.waitall
```

## 共用存储器

当执行 fork 时，预先创建的对象将可用于新流程。

```
# shared_memory.rb
# Forks have access to objects created before the fork

data = [1,2,3]

fork do
  puts "data in child: #{data}"
end

puts "data in parent: #{data}"
```

输出:

```
data in parent: [1,2,3]
data in child: [1,2,3]
```

分叉可以看到数据，因为分叉从它们的父进程继承状态。这包括变量和打开的文件描述符。最初，信息是共享的，而不是复制的。一旦发生写入，数据将被复制到子进程。如果在 fork 之后共享对信息的更改，就不会有流程隔离。

```
# copy_on_write.rb
# Changes to memory after the fork do not cross the process barrier

data = [1,2,3]

fork do
  sleep 1
  puts "data in child: #{data}"
end

data[0] = "a"
puts "data in parent: #{data}"

Process.waitall
```

输出:
父数据:["a "，2，3]
子数据:[1，2，3]

在写入发生之前共享进程数据被称为**写入时拷贝优化**。使用来自父进程的数据大大降低了创建子进程的成本，使分叉可以与线程竞争。

这就是为什么通过多个进程实现并行性在 Unix 中很流行的原因。

不幸的是，尽管这与一般的分支高度相关，但它在很长一段时间内并不适用于 Ruby。在 2.0 的变化之前，Ruby 的垃圾收集器的标记和清除算法会对对象本身进行修改，迫使操作系统复制内存。这个问题在 Ruby Enterprise Edition 中得到了修复，但是在很长一段时间里，大多数 Ruby 用户都只能进行低效的分叉。

因此，出于编程的目的，分叉数据并不是真正的“共享”，也不应该是因为需要进程隔离。尽管如此，对于许多场景，我们需要一种方法让父流程和子流程共享分叉后发生的数据更改。一种简单的方法可能是轮流写入资源。然而，在 POSIX 中有可靠的进程间通信机制，允许我们的进程来回发送数据。

## 结论

至此，您应该对 fork()为什么有用以及如何在 Ruby 中使用它有了基本的了解。在[第二部分](https://www.sitepoint.com/forking-ipc-ruby-part-ii "Part II")中，我们将讨论进程间通信。

## 分享这篇文章