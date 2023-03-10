# Code Safari:探索 Selenium 以寻找套接字

> 原文：<https://www.sitepoint.com/spelunking-selenium-in-search-of-sockets/>

在[对话](http://dev.theconversation.edu.au)中，我们最近得到了一台漂亮的新八核机器，坐在角落里充当我们的构建服务器。它是顶级的机器:快速处理器、固态硬盘——没有缺点。我们称之为“坦克”。通过并行化我们的 [Selenium](http://seleniumhq.org/) 测试套件，我们将构建时间从 20 分钟减少到了 4 分钟。这是一个值得一看的景象，八个 Firefox 窗口同时启动，点击你的应用程序。这五倍的改善似乎太好了，难以置信，事实上很快魔鬼暴露了自己:

```
Errno::EBADF:
  Bad file descriptor
# .../lib/selenium/webdriver/firefox/socket_lock.rb:57:in `initialize'
# .../lib/selenium/webdriver/firefox/socket_lock.rb:57:in `new'
# .../lib/selenium/webdriver/firefox/socket_lock.rb:57:in `can_lock?'
# .../lib/selenium/webdriver/firefox/socket_lock.rb:43:in `lock'
# .../lib/selenium/webdriver/firefox/socket_lock.rb:29:in `locked'
# .../lib/selenium/webdriver/firefox/launcher.rb:32:in `launch'
# .../:21:in `initialize'
```

随机不合格规格。任何开发者存在的克星。大约 30%的情况下我们会得到这个错误。不酷。堆栈跟踪指出 selenium web driver 是罪魁祸首；让我们深入研究一下，看看能不能搞清楚发生了什么事。

## 潜入水中

这是一个棘手的问题，因为没有明显的可重复的测试用例。重新运行构建不会削减它——如果它工作了，我们不知道这是我们的改变还是它碰巧工作了。因此，我们的第一个任务将是缩小违规代码的范围，并提取一个允许我们进行试验的测试用例。让我们看看堆栈跟踪的源代码。

我们运行的是当前发布的 gem 的旧版本——这个问题在新版本中也没有得到解决，但是为了突出重点，我用相同的旧版本开始了我的调查。您通常可以找到一个 git 标签或一个方便的提交消息(“升级到 0.1.2”)来定位正确的代码版本，但是在这种情况下，简单地解包 gem 更容易。

```
gem unpack selenium-webdriver -v 0.1.2
cd selenium-webdriver-0.1.2
```

从那里，我们可以打开违规的方法:

```
# lib/selenium/webdriver/firefox/socket_lock.rb
def can_lock?
  @server = TCPServer.new(Platform.localhost, @port)
  ChildProcess.close_on_exec @server
&nbsp;
  true
rescue SocketError, Errno::EADDRINUSE => ex
  $stderr.puts "#{self}: #{ex.message}" if $DEBUG
  false
end
```

`Errno`类异常[从操作系统](http://www.ruby-doc.org/docs/ProgrammingRuby/html/ref_m_errno.html)中冒出来，在这种情况下，从打开一个监听给定端口的新服务器开始。鉴于这段代码可能会被多个进程同时执行(因为我们为八个内核中的每一个都启动了一个进程)，并且并发负载似乎很可能会导致奇怪的错误 <sup>tm</sup> ，这似乎是一个很好的起点。让我们通过将这段代码转移到一个独立的 Ruby 脚本中来收紧我们的反馈循环，我们可以用它来集中调查并发行为，而不是每次我们想要测试一个变化时都重新运行我们的构建。这是任何调试工作中重要的第一步:尽可能缩短做出更改和看到更改结果之间的时间。

```
require 'socket'
50.times do
  fork do
    1000.times do
      sleep (rand / 1000.0)
      begin
        server = TCPServer.new('127.0.0.1', 5678)
        server.close
      rescue SocketError, Errno::EADDRINUSE => e
      end
    end
  end
end
```

五十个进程，都试图同时使用同一个端口。如果这不能暴露错误，那就没有什么可以了。不幸的是，它在我的本地 OSX 机器上运行良好。沮丧之余，我想起了调试的第二个重要步骤:总是尽可能接近目标环境地检查假设。我的本地机器运行的是 OSX，但是 Tank 运行的是 Ubuntu Linux，这可能会对套接字级别的操作产生不同的行为。我进入 Tank，运行上面的脚本。果然，坏的文件描述符到处都是。

但这意味着什么呢？这个错误是否和`Errno::EADDRINUSE`一样是“预期的”,或者如果我们在`can_lock?`方法中悄悄地挽救它，它会导致失败吗？为了回答这个问题，我们需要更多来自周围代码的上下文。`SocketLock`课上还有一些方法:

```
# lib/selenium/webdriver/firefox/socket_lock.rb
def locked(&amp;blk)
  lock
&nbsp;
  begin
    yield
  ensure
    release
  end
end
&nbsp;
private
&nbsp;
def lock
  max_time = Time.now + @timeout
&nbsp;
  until can_lock? || Time.now >= max_time
    sleep 0.1
  end
&nbsp;
  unless did_lock?
    raise Error::WebDriverError, "unable to bind to locking port #{@port} within #{@timeout} seconds"
  end
end
```

只要失败,`lock`方法就会继续重试`can_lock?`,所以这表明我们应该能够简单地重试一个由于错误的文件描述符而失败的连接。`locked`只是一个包装器，在块被执行后自动释放锁。向上移动堆栈跟踪来确认我们为什么锁定，我们发现这个漂亮的自记录方法:

```
# lib/selenium/webdriver/firefox/launcher.rb
def launch
  socket_lock.locked do
    find_free_port
    create_profile
    start_silent_and_wait
    start
    connect_until_stable
  end
&nbsp;
  self
end
```

驱动程序使用一个端口作为互斥体，以确保任何时候都只有一个进程在扫描空闲端口。它在新找到的端口上创建一个连接，然后释放锁定的端口，这允许另一个进程找到一个空闲端口。因为所有八个进程都是在“默认”selenium 端口上启动的，所以它们将一个接一个地排队(继续使用`can_lock?`方法——重现上面最小测试用例所复制的情况),并且一次一个地扫描潜在的端口。

我对这个逻辑背后的推理很好奇，所以想在 IRC 上试试运气。我猜想`#selenium`频道会存在，事实上它有 58 个成员。问我可以和谁谈论 Ruby web 驱动程序让我和`jarib`进行了一次讨论，他把我带到了[维基](http://code.google.com/p/selenium/wiki/FirefoxDriverInternals)，它记录了以下理由:

> 选择使用套接字作为互斥体的方法是因为它允许不同的 JVM 甚至语言在同一时间使用相同的算法，而不用担心在同一个端口上启动多个 Firefox 实例来监听 T2(这看起来很可怕，我可以向你保证:)。机制非常简单，大多数语言都可以用来实现它，而不需要任何特殊的库。

这似乎很公平。它还包含了对启动过程的描述，这与我们在上面得出的结论是一致的，尽管有更多的细节。

这项调查表明了两个结论。首先，用`can_lock?`方法无声地营救`Errno::EBADF`是一件安全且值得做的事情。第二，为每个进程显式指定唯一的端口可能会完全避免这个问题，并提供更快的(尽管实际上可能可以忽略)启动时间。

## 包扎

乍看之下，这似乎是一个令人讨厌的、难以从我们底层库的深处找到的错误，结果却很容易找到。这是 Ruby 的主要优势之一:第三方代码是透明的，很容易被发现。除了最低级别的代码(在 C 库中)，研究和试验所有的代码是微不足道的。

为了提高你的技能，试着用一个简单的文件锁代替`SocketLock`(一个独特的文件是否存在？)并了解这将如何影响`selenium-webdriver`代码库。

(在撰写本文时，这个问题尚未在 selenium 中得到解决——如果它影响到您，您可以关注[这张票](http://code.google.com/p/selenium/issues/detail?id=1611)上的状态)

下周敬请关注代码丛林中更多令人兴奋的冒险！

## 分享这篇文章