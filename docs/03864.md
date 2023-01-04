# SIGKILL 的许可证

> 原文：<https://www.sitepoint.com/license-to-sigkill/>

![Silhouette of secret agent. No transparency and gradients used.](img/3b0ad1bd0e237cfeacb18284397e2c1f.png)

雪人，理查德雪人。

每个程序都想长生不老。当一个程序在运行完之前被迫退出会发生什么，我们为什么要这样做？

## Unix 信号

如果您熟悉信号，请随意跳过。

在 Unix 中，进程可以用预定义的信号相互通信。你可以在这里看到 unix 信号的列表。这种交流能力在面向过程的程序中是非常重要的。例如， [Puma webserver](https://github.com/puma/puma) 可以通过生成子“工作”进程来增加并发性。它接受主进程的请求，然后将它们交给下一个可用的子进程。如果运行 Puma 主进程的系统需要关闭或重启，我们不希望所有当前的请求都停止。相反，如果可以的话，我们希望子工作进程完成请求的处理，清除所有外部连接或它们可能生成的临时文件，然后退出。系统可以通过向父“主”进程发送信号来安全地做到这一点，该信号然后被发送到子进程。

你可能看过电影[创遗产](https://en.wikipedia.org/wiki/Tron:_Legacy)。这部电影以一名黑客闯入公司网络开始。首席执行官看到了这种情况，并通过在终端中键入一个`$ kill -9`命令来机敏地做出反应。linux(和 Mac OS X)中的这个`kill`命令向一个进程发送 9 号信号，也就是`SIGKILL`。`SIGKILL`表示“现在结束，无需清理”。这类似于在 windows 上使用 CTRL+ALT+DELETE(尽管 windows 不符合 [POSIX](https://en.wikipedia.org/wiki/POSIX#POSIX_for_Windows) 并且不支持进程)。

当我们需要长时间运行的流程优雅地退出时，信号`SIGKILL`太强了。该信号迫使进程立即退出，并可能使您的系统处于不良状态。你应该用什么来代替？信号`SIGTERM`(信号编号 15)是“终止信号”。这告诉一个程序，它需要停止正在做的事情，并在退出前清理干净。

## 生与死

当 Ruby 接收到一个`SIGTERM`信号时，它会引发一个`SignalException`错误。在 Ruby 中，异常可能在进程运行的任何时候发生，所以关键的清理代码应该总是使用`ensure`块。

```
begin
  # do something
ensure
  # clean up something
end
```

这里有一些值得注意的注意事项，比如当一个确保块已经在运行时可能会引发一个异常，所以我们不能总是依赖它来执行。为了深入了解 Ruby 中的错误，我推荐 Avdi 的[excellent Ruby](http://exceptionalruby.com/)。也就是说，使用`ensure`块来保护您的代码仍然是一个最佳实践。

因为我们已经有了这种故障安全行为，所以 Ruby 会在引发`SignalException`时使用它。为了验证这一点，我们可以编写一个简单的脚本:

```
Thread.new do
  begin
    while true
      sleep 1
    end
  ensure
    puts "ensure called"
  end
end

current_pid = Process.pid
signal      = "SIGTERM"
Process.kill(signal, current_pid)
```

当您运行该程序时，您会看到:

```
ensure called
Terminated: 15
```

您会注意到，除了“确保调用”之外，我们还获得了用于退出进程的信号的编号(15，对应于`SIGTERM`)。干净利落。这种行为非常方便，因为任何具有错误处理的程序都已经准备好优雅地退出。通过将敏感的操作放在一个`ensure`块中，我们使得程序更有可能做正确的事情。毕竟，`ensure`块被调用，然后程序将退出。注意，如果你用`SIGKILL`重新运行程序，它会以不同的数字退出，我们不会从`ensure`模块得到输出。

## 明天不会消逝

我敢肯定，你的电脑上有一个令人沮丧的应用程序，它被冻结了，无论你点击多少次“关闭”按钮都不会死。有些顽固的程序永远不会退出，不管你给它们发多少次`SIGTERM`。这可能发生在程序试图清理自己的时候。我们可以很容易地重现这一点:

```
thread = Thread.new do
  begin
    while true
      sleep 1
    end
  ensure
    while true
      puts "ensure called"
      sleep 1
    end
  end
end

current_pid = Process.pid
signal      = "SIGTERM"
Process.kill(signal, current_pid)
```

输出将如下所示:

```
ensure called
ensure called
ensure called
ensure called
ensure called
# ...
ensure called
```

在机器重启或发送`SIGKILL`之前，它不会结束。与这个微不足道的例子不同，很容易想象您的 Ruby 程序等待数据库查询或网络调用完成。如果它被挂起，你的程序永远得不到响应，它就永远不会退出。这就是为什么超时敏感代码总是至关重要，尽管[要小心 timeout.rb](http://www.mikeperham.com/2015/05/08/timeout-rubys-most-dangerous-api/) 。

值得注意的是，当一个`SignalException`被引发时，作用域内的所有`ensure`块都将被调用。这意味着，除了您自己的代码之外，任何依赖关系中的所有代码都将被调用。如果你的系统在退出时挂起，你不能确定你提交的错误的`ensure`块，它可能来自你正在使用的库。

## 对信号陷阱说不

另一种阻止程序退出的方法是使用`Signal.trap`。当您运行这段代码时，信号将被捕获，程序将不会退出。

```
Signal.trap('TERM') do
  puts "Die Another Day"
end

current_pid = Process.pid
signal      = "SIGTERM"
Process.kill(signal, current_pid)
```

当您执行程序时，您会得到输出`"Die Another Day"`,但它会继续执行。有可能[捕获并重新发出相同的信号](http://stackoverflow.com/questions/29568298/run-code-when-signal-is-sent-but-do-not-trap-the-signal-in-ruby)，然而这是一个非常大的锤子。我们不能依赖发送给程序的信号，也不能依赖代码在程序块中运行。更糟糕的是，当我们收到信号时，系统需要我们尽快清理并退出。最佳实践是尽可能使用`ensure`模块，只有在真正必要时才求助于信号捕获。

## 来自俄罗斯的爱和信号

到目前为止，我们已经了解了 Ruby 代码是如何处理信号的，但是您如何知道要发送什么信号呢？在任何重启或关闭之前，你会发送一个`SIGTERM`让它清理，然后监视进程，看看它是否在合理的时间框架内关闭。如果没有，发送一个`SIGKILL`来关闭进程，结束任何无限的确保阻塞。您应该记下您的进程何时没有从`SIGTERM`中退出，因为这可能意味着，当您强制终止该进程时，您正在中断一些重要的工作或清理进程。我工作的公司，Heroku T4，每次你部署或重启你的应用程序时都会经历这些步骤。如果由于某种原因，您的应用程序不能按时退出，系统会发出一个[R12——退出超时](https://devcenter.heroku.com/articles/error-codes#r12-exit-timeout),并在您的仪表板视图上记录该错误，以便您稍后进行调查。

虽然这很难概念化，但是一个异常可能会在任何时候停止你的整个程序，所以知道把`ensure`添加到应该已经有它们的地方是你为了安全所需要做的。无论你是为英国特勤局工作，还是在皇家赌场的 IT 部门工作，你都可以放心地知道你的程序可以优雅地退出。

## 分享这篇文章