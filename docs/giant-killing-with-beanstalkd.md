# 用豆子杀死巨人

> 原文：<https://www.sitepoint.com/giant-killing-with-beanstalkd/>

如果你曾经涉猎过面向服务的架构(SOA ),甚至读过一些关于它的有趣文章,你可能会遇到“消息队列”这个术语。

对消息队列(MQ)的最简单的解释是，它允许你的架构中的服务采用一种“发射并忘记”的方式与其他服务交互。通过在系统中放置一个队列，对时间不敏感的操作可以在关心它们的服务空闲时执行，而不考虑技术或编程语言。

举个例子，让我们看一下求职公告板应用程序中的“发送给朋友”功能。一旦用户完成表单并点击“发送”,我们真的希望给朋友发邮件的细节存在于我们的工作板应用程序中吗？

## 后台作业

解决这个问题的一个常见方法是使用后台工作人员，如 [Resque](https://github.com/resque/resque) 或 [Sidekiq](http://sidekiq.org/) 。对于手头的问题，这些都很好，而且更适合。对此我唯一的问题是:

1.  发送电子邮件的逻辑*存在于我们的应用程序中*,它不一定关心电子邮件。
2.  我可能会通过架构中的一些应用程序复制与我的 SMTP 服务器通信的过程。
3.  后台工作人员对他们的来源知道得有点多，例如，他们来自什么模型，他们可以访问什么(我的整个应用堆栈)。

如果您的体系结构正在发展，可能值得考虑将一些后台工作人员转移到 MQ。对我来说，MQ 只是工作。您放入一些数据，一个守护进程或应用程序会在一段时间后获取这些数据并对其进行操作。与此同时，创始人继续专注于其核心业务。

随着架构的发展，您添加了更多的服务，其中一些服务可能也需要发送电子邮件。至此，你已经建立了一个清晰、可信的发送电子邮件的方法。你只需在邮件队列中放入一些数据，它就会被发送出去。

## Beanstalkd

希望现在你已经明白了为什么 MQ 这么棒了。有一些开源 MQ 可用，最值得注意的是 [RabbitMQ](http://www.rabbitmq.com/) (在 [RubySource 上有一篇很好的文章，详细介绍了](https://www.sitepoint.com/introduction-to-messaging-systems-for-rubyists/))和我个人最喜欢的，我们今天将使用的 [Beanstalkd](http://kr.github.io/beanstalkd/) 。

开始使用 Beanstalkd 真是再简单不过了。在 OSX，你想用自制软件(`brew install beanstalkd`)，或者对于 debian linux 版本，你可以用`sudo apt-get install beanstalkd`。它似乎得到了跨平台的大多数包管理者的很好的支持。详细内容可以在 [Beanstalkd 下载文档](http://kr.github.io/beanstalkd/download.html)上看到。

安装完成后，您可以打开终端并执行`beanstalkd`。这将在前台使用本地主机上的默认端口`11300`启动一个 Beanstalkd 实例。在前台运行它并不总是理想的，所以我的典型命令看起来像这样:

```
beanstalkd -b ~/beanstore &
```

这只是将队列数据保存在目录`~/beanstore`下的 binstore 中，而不仅仅是内存中，并在后台运行流程(与号)。对于开发来说，这些设置很好。说到产品，我建议你阅读一下 Beanstalkd 附带的管理工具[T2 的相关文档。](https://github.com/kr/beanstalkd/tree/master/adm)

## Beanstalkd 行话

Beanstalkd 有一些很好的词汇来描述主要玩家和操作。让我们穿过它们。

### 地铁

管道是您的消息的名称空间。一个 Beantstalkd 实例可以有多个管道。在香草靴上，Beanstalkd 将有一个名为`default`的单管。

这个想法是你希望有一个特定的进程来监听通过特定管道传入的消息。如前所述，管道只是充当队列消费者的名称空间。

### 乔布斯

工作就是我们放在试管里的东西。我经常把 JSON 放在一个管子里，然后在另一端把它封起来。

Beanstalkd 并不真正关心工作的内容，所以像 YAML、纯文本或者节俭这样的东西就可以了。

在正常的快乐路径操作中，作业有两种状态:

1.  就绪–等待处理。
2.  保留–正在处理

如果一切顺利，该作业将被删除。如果作业出现问题，比如说我们的 SMTP 服务器出现故障，作业就会被置于“隐藏”状态。它将保持“被埋”的状态，直到管子被“踢开”。这只会将作业重新置于“就绪”状态。因此，随着 SMTP 的备份，我们踢了管，世界继续旋转。

我们还没有提到的另一个州是“延迟”。这仅仅意味着，在经过某个预先确定的时间间隔之前，作业不会进入“就绪”状态。我个人没怎么用过这种状态，所以除了提到它的存在之外，我就不再赘述了。

## 奥姆诺姆诺姆

现在我们已经在我们的开发机器上运行了 Beanstalkd，我们希望在队列中获得一些作业。为了达到这个目的，我通常选择的武器是[食豆者](https://github.com/beanstalkd/beaneater)宝石。进入地铁工作非常简单:

```
require 'beaneater'
require 'json'

beanstalk = Beaneater::Pool.new(['localhost:11300'])
tube = beanstalkd.tubes['my-tube']
job = {some: 'key', value: 'object'}.to_json

tube.put job
```

就是这样。现在我们到了有趣的部分，消费地铁和所有生活在那里的工作。

我非常喜欢用守护进程来处理这个问题。如果管道开始变得太满，我们可以启动更多的守护进程来帮助清除积压的作业。当然，我们也可以根据需要将它们杀死。

到目前为止，我已经使用了[但丁](https://github.com/bazaarlabs/dante)宝石包装脚本到守护进程。它看起来比[守护工具包](https://github.com/kennethkalmer/daemon-kit)要轻一点，我喜欢让我的守护进程不会变得臃肿。对我来说，使用 Dante 而不是像`ruby script/my_mailer_script.rb`这样的东西的好处无非是 Dante 提供了开箱即用的进程 ID (PID)文件生成。这样，我可以用 [monit](http://mmonit.com/monit/) 来检查守护进程。

Beaneater 提供了一个非常好的 API，以两种方式消费作业。第一种方法是手动执行保留作业的过程，处理作业，如果正确完成就删除，如果出现异常就隐藏。它看起来像这样:

```
beanstalkd.tubes.watch!('my-tube')
loop do
  job = beanstalk.tubes.reserve
  begin
    # ... process the job
    job.delete
  rescue Exception => e
    job.bury
  end
end
```

这里有几件事值得一提。是的，我正在使用一个无限循环，tube 上的`reserve`方法实际上会等待一个作业“就绪”，保留它，然后继续。

Beaneater 为长时间运行的任务提供了一个更好的界面，上面的内容可以简单地简化为:

```
beanstalkd.jobs.register('my-tube') do |job|
  # ... process the job
end

beanstalkd.jobs.process!
```

这个方法包装了前一个例子的行为(尽管方式更好),保留、处理，然后根据结果删除或隐藏。

## 没有魔豆

Beanstalkd 的美在于它的绝对简单。我真的不愿意做更多的介绍。就让事情快速运行而言，它并不比前面讨论的任何后台工作者解决方案更复杂。

老实说，在您采用 MQs 时采取务实的态度是有意义的。Resque、Sidekiq 等。所有这些都有它们自己的位置，并且工作得非常好，但是 Beanstalkd 解决了更多的问题，也就是说，服务之间的接口可能是用 Ruby 编写的，也可能不是。Beanstalkd 的. NET 客户端可用)。

事实上，整个事情完全是语言不可知的。与 beanstalkd 通信的 neckbeard 方式是通过它自己的 TCP 协议。正如你可能知道的，食豆者 gem 为我们将所有的协议抽象成一个打包好的 API。可以肯定的说，在接下来的一段时间里，我将会依赖食豆者宝石来使用食豆者。

如果我对设计/组成电子管消费者有任何建议，尽可能坚持[单一责任原则(SRP)](http://www.oodesign.com/single-responsibility-principle.html) 。总有一天，你将不得不放弃一份被埋没的工作。如果该作业向数据库**写入数据，并且**发送电子邮件，当电子邮件发送失败时会发生什么？重放所述消息将导致重复的数据库条目。通过将工作的处理分成合理的最小责任，您就不必担心执行重复的操作。

我强烈建议您随着应用程序体系结构的增长也要考虑 Beanstalkd。以我个人的经验来看，我发现它运行起来很简单，管理和维护起来也很简单，通过 Beaneater 的 ruby 客户端是我用过的最好的界面之一。

## 分享这篇文章