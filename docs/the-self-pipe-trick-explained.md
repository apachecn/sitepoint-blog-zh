# 自我管道技巧解释

> 原文：<https://www.sitepoint.com/the-self-pipe-trick-explained/>

![ioselect](img/61e5d72723c358ee8689d9018112b120.png)

自管道技巧是一个很酷的 Unix 技巧。这是将一些简单的构件组合成可靠的解决方案的一个很好的例子。本文将帮助您理解它，在这个过程中建立对 Unix 信号、管道和 IO 多路复用的理解。

最近，我在探索[领班](http://github.com/ddollar/foreman/)的代码库。我认为这将为用 Ruby 编写 Unix 系统编程示例提供一些很好的素材。我没有失望。

`Foreman::Engine`班是特别好的学习。它使用的一种模式被称为自管道技巧。这不是 Ruby 特有的模式；当与 select(2)系统调用混合时，自管道技巧是处理信号的正确解决方案的一部分。值得一提的是, [Unicorn](http://unicorn.bogomips.org/) 服务器使用同样的技术来处理信号；这是用 Ruby 进行 Unix 系统编程的又一项伟大研究。

## 工头，简化

我将继续使用 Foreman gem 作为一个例子，但是我可能会忽略它内部的一些部分，把重点放在重要的部分上。

在其默认形式中，Foreman gem 获取一个 Procfile，该文件可能包含如下一些条目:

```
web: bundle exec thin start
job: bundle exec rake jobs:work 
```

然后获取这些命令，将它们生成到流程中，并管理这些流程。在这个 Procfile 示例中，不带选项运行`foreman start`将为每种应用程序类型(例如 web、job)生成一个子进程。我将忽略产生东西的过程，只讨论信号处理。

Foreman 提供的一个特性是，当您向它发送 Unix 信号告诉它终止时，它会将该信号转发给它所管理的进程。

## 关于信号的旁白

Unix 信号是进程间通信(IPC)的一种形式。它们允许您异步触发系统中任何进程的某些行为。这可能非常有用，但也会带来一些有趣的挑战。

如果您曾经使用过 kill(1)命令，那么您以前就使用过信号。例如，你几乎肯定做过这样的事情:

```
kill -9 <pid> 
```

杀死一个没有反应的程序。kill(1)命令，以及 Ruby 中的`Process.kill`，是我们向进程发送信号的方式。使用`kill -9`时，`-9`部分实际上是指一个特定的信号:`KILL`信号。

这有点令人困惑，我们使用 kill(1)命令来发送`KILL`信号，但是也可以发送其他信号。这里有几个例子:

```
$ kill -9
# same as
$ kill -KILL

$ kill -HUP
# same as
$ kill -1

$ kill -INT
$ kill -TERM
$ kill -QUIT 
```

`KILL`、`HUP`、`TERM`和`QUIT`都是可以发送给流程的不同信号的例子。这些信号**可以在任何时候发送给任何正在运行的进程。每个进程可能选择通过信号处理器不同地处理信号，但这是处理信号的挑战:它们可以在任何时候被传递。**

与事件代码不同，事件代码通常相对于系统中的代码块或动作异步运行，Unix 信号可以在任何时候中断您的程序。当这种情况发生时，信号处理代码将被执行，然后您的代码将从它停止的地方恢复。

## 处理信号

定义信号特定行为的方法是“捕获”信号，指定接收信号时应该执行的一些行为。

在 Ruby 中，我们用`Signal.trap`这样做:

```
Signal.trap("INT") {
  puts "received INT"
  exit
}

Signal.trap("QUIT") {
  puts "received QUIT"
  exit
}

Signal.trap("KILL") {
  puts "received KILL, but I refuse to go!"
}

Signal.trap("USR1") {
  puts "received USR1"
  puts "I think the time is #{Time.now.to_i}"
}

puts Process.pid
sleep 
```

尝试运行这个程序。在倒数第二行，打印当前进程 id (pid)。您可以将它插入 kill(1)命令，看看当您向您的进程发送信号时会发生什么。

```
$ kill -INT <pid>
$ kill -USR1 <pid> 
```

对于`INT`和`QUIT`,您应该看到打印的消息，随后流程退出。传统上，这些信号将进行任何必要的清理并退出进程。

然而，对于`KILL`信号，您的处理程序不会被调用。`KILL`信号是特殊的，因为它不能被捕获。这就是为什么杀死一个没有响应的进程是有用的；不能阻止或阻止它一劳永逸地结束这一进程。

`USR1`(和`USR2`)信号的特殊之处在于它们没有传统的行为。它们实际上是给你的应用程序挂额外的行为。

## 信号和重入

我提到过信号的棘手之处在于它们可以随时打断你的程序。我认为这值得重复。信号可以在任何时间中断你的程序*。当你的代码正在执行时，一个信号可能会到达并中断它。**当一个信号被处理时，另一个信号(或同一个信号！)，可以第二次到达并中断第一个处理程序。***

这实际上会导致程序崩溃。下面是一个程序的简单例子，当它接收到`QUIT`信号时，试图清理它创建的文件:

```
trap("QUIT") {
  File.delete('ephemeral_file')
  exit
} 
```

乍一看，这似乎是正确的，但是信号的真正异步性给这段代码带来了问题。如果信号的发送者很高兴触发，他们可以快速连续发送两次`QUIT`信号。当这种情况发生时，这个`QUIT`处理程序完全有可能在删除文件之后、退出进程之前被中断。

*为了使这种情况易于重现，尝试在`File.delete`和`exit`之间增加一个休眠，然后连续发送两次`QUIT`信号。*

如果`QUIT`处理程序的第一个实例进行到这一步(删除文件，但还没有退出)，那么当第二个退出信号到达时，它将转到信号处理程序块的开始，并再次尝试删除文件。这当然会导致一个`ENOENT`错误和悲伤。

这是一个非常良性的例子，但它显示了如果有更多破坏性的清理行为，事情可能会如何出错。

问题是这个信号处理器是不可重入的。这意味着这段代码被中断，然后在第一次调用完成之前重新启动是不安全的。这对信号处理者来说不是一件好事。您需要您的信号处理程序是可重入的，因为您永远不知道下一个信号什么时候到达并重新启动处理程序。

Foreman 和 Unicorn 都使用全局队列来解决这个问题。这是安全和正确处理信号的第一部分，也是设置自管道技巧解决的问题。

但是首先，这是 Foreman 和 Unicorn 实现信号排队的大概方式。

```
SIGNAL_QUEUE = []

[:INT, :QUIT, :TERM].each do |signal|
  Signal.trap(signal) {
    SIGNAL_QUEUE << signal
  }
end

# main loop
loop do
  case SIGNAL_QUEUE.pop
  when :INT
    handle_int
  when :QUIT
    handle_quit
  when :TERM
    handle_term
  else
    the_usual
  end
end 
```

独角兽和工头都有一个循环[像](https://github.com/defunkt/unicorn/blob/master/lib/unicorn/http_server.rb#L267-L281)T2 这个。

他们所做的是将逻辑从信号处理程序块中取出，并推迟到主循环中。通过这种方式，如果某个发送者正在发送`QUIT`信号，它不会每次都继续调用处理程序代码，它只会将信号排队等待主循环处理。

通过将逻辑移出信号处理器本身，它们现在是可重入的了！但是现在逻辑已经脱离了处理程序，这就打开了一个竞争条件。

## 一张跑步工头的照片

要理解竞争条件，您需要对 Foreman 如何处理它所管理的子进程的输出有一个高层次的理解。

Foreman 为它需要生成的每个子进程创建一个管道，并将子进程的 stdout 重定向到这个管道。然后，Foreman 进程监视所有这些管道上的输入。当一些输入到达时，它将它打印到控制台，带有特定于输出来自的进程的标记和颜色。

因此 Foreman 需要某种方法来监控所有这些管道，以查看数据何时到达。

内核提供了一个系统调用来做 Foreman 在这里需要的事情。select(2)系统调用获取一组您感兴趣的文件描述符(如管道、文件、套接字);当这些文件描述符准备好进行操作(数据可供读取或有更多的缓冲区空间可供写入)时，select(2)只返回为您准备好的文件描述符。

因此，如果 Foreman 管理 10 个子进程，它将使用 select(2)监控 10 个管道。如果其中一个子进程向其管道写入了一些输出，select(2)会将该管道返回给 Foreman，以便它可以采取正确的操作。

在 Ruby 中，文件描述符被映射到`IO`对象，select(2)被映射到`IO.select`。这意味着任何`IO`对象都可以传递给`IO.select`进行监控。

同样，我在简化事情，但是 Foreman 的主循环看起来像这样，其中`pipes`表示连接到每个子进程的 stdout 的管道数组。

```
# main loop
loop do
  case SIGNAL_QUEUE.pop
  when :INT
    handle_int
  when :QUIT
    handle_quit
  when :TERM
    handle_term
  else
    ready = IO.select(pipes)

    process_outputs(ready[0])
  end
end 
```

现在，我们已经在`else`块中填入了更接近 Foreman 实际工作的内容。

`IO.select`调用是一个阻塞调用，它不会返回，直到在那些管道之一上有一些数据可用。如果子进程没有向它们的 stdout 输出任何东西，这个调用将无限期地阻塞。

## 竞赛条件

在这段代码中，信号处理程序是在进入主循环之前定义的。如果`QUIT`信号在进入`case`语句之前到达，一切都将按预期工作。case 语句将从队列中弹出`QUIT`信号，并正确处理它。

然而，*有*可能让退出信号到达`else`代码块的顶部。记住，信号可以在任何时候中断你的程序。如果在调用`IO.select`之前发送了`QUIT`信号，那么它会被适当地推入信号队列，但是之后你的程序会进入阻塞的`IO.select`调用，这可能会无限期地阻塞。如果发生这种情况，该信号将永远不会被处理，或者至少处理会被延迟。

请注意，如果信号在`IO.select`已经开始阻塞之后到达，它仍然可能不会被中断，这意味着信号不会被处理，直到这些子进程之一写入输出，再次开始主循环。

这不是预期的行为，会导致 Foreman 忽略终止其管理的流程的请求。

## 自管道解决方案

自管道技巧通过在等式中添加另一个管道来解决这个问题:一个“自管道”。为了理解为什么这有帮助，你需要对管道的工作原理有一个基本的了解。

我几乎可以肯定您熟悉命令行 shell 中的管道。您可能已经使用它们将命令串到管道中。我们在这里讨论的是你的 shell 中管道的底层编程结构。

管道只是单向的字节流。

“单向”是指管道有一个读取端和写入端，一个输入端和一个输出端，数据只在一个方向上传输。我说的“字节流”是指管道没有像数组那样的位置，你只需在一端写入一些字节，从另一端读取一些字节。这种方式有点像文件。

与文件不同，管道也像有界队列一样。您可以将字节写入一端进行存储，直到其他人读取它们。但是如果没有人在读取另一端，它最终会变满并阻止写入。

您可以在 Ruby 中创建一个管道，如下所示:

```
reader, writer = IO.pipe 
```

对`IO.pipe`的调用返回两个`IO`对象，一个表示管道的读取端，一个表示管道的写入端。

传统上，管道是与 IPC 的子进程共享的(我简单地提到了 Foreman 这样做是为了观察子进程的输出)，但是自管道不会与另一个进程共享。因此它的名字。

因此，Foreman 进程创建了一个管道，然后它的信号处理器将向管道*写入一个字节，同时*将一个条目放入`SIGNAL_QUEUE`。

```
SIGNAL_QUEUE = []
self_reader, self_writer = IO.pipe

[:INT, :QUIT, :TERM].each do |signal|
  Signal.trap(signal) {
    # write a byte to the self-pipe
    self_writer.write_nonblock('.')
    SIGNAL_QUEUE << signal
  }
end 
```

然后，为了结束这个过程，主`select`调用还监视这个管道的读取端。如果在任何时候，一个信号到达并且一个字节被写入管道，那么`select`调用将被唤醒(因为它正在监视自管道)并且信号将被立即处理。

```
# main loop
loop do
  case SIGNAL_QUEUE.pop
  when :INT
    handle_int
  when :QUIT
    handle_quit
  when :TERM
    handle_term
  else
    ready = IO.select(pipes + [reader])

    # drain the self-pipe so it won't be returned again next time
    if ready[0].include?(reader)
      reader.read_nonblock(1)
    end

    # continue to process the other pending data
    if (pipes & ready[0]).any?
      process_outputs(ready[0])
    end
  end
end 
```

现在，即使在调用`select`之前有一个信号中断了你的程序，那个`select`调用也会发现自管道上有一个字节的数据要读取，并且会立即返回。

请注意，我们确实需要向最后的`else`块添加一点额外的逻辑，以查看`select`是否返回任何管道来指示有要处理的输出，并且还要清空自管道，以便它再次处于原始状态来指示信号何时到达。

## 结局

原来这就是自管绝招！正如我所说的，这是将一些简单的构件组合成一个可靠的解决方案的一个很好的例子。像 Foreman 这样的项目实际上是作为管理多个过程的简单代理而存在的，所以它在面对用户输入时保持响应是非常重要的。

我并不期望您在大多数 Ruby 项目中需要实现这一点，但是这表明了正确处理信号是多么的棘手。让我们感谢我们[所依赖的](https://github.com/ddollar/foreman/blob/master/lib/foreman/engine.rb)[软件](https://github.com/defunkt/unicorn/blob/master/lib/unicorn/http_server.rb)，它隐藏了这种复杂性，让我们变得简单。

如果您想了解更多，这里有一些关于自管道技巧的进一步阅读，一些替代方法，如 pselect(2)，以及 Ruby 中正确的信号处理:

*   [实现信号处理器——一些注意事项](http://blog.rubybestpractices.com/posts/ewong/016-Implementing-Signal-Handlers.html#fn1)
*   [自吹自擂的把戏](http://cr.yp.to/docs/selfpipe.html)
*   [安全的 UNIX 信号处理技巧](http://osiris.978.org/~alex/safesignalhandling.html)
*   [避免与 Unix 信号的竞争并选择()](http://evbergen.home.xs4all.nl/unix-signals.html)

我为 ruby 爱好者开设的[在线 Unix 编程课程的八月版刚刚开始注册，我将赠送一张免费的门票！您可以在此](http://www.jstorimer.com/products/unix-fu-workshop)参加赢取[的机会。](http://www.jstorimer.com/blogs/workingwithcode/8289030-unix-fu-workshop-august-edition-giveaway)

## 分享这篇文章