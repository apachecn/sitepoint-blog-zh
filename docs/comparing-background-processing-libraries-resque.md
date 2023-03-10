# 比较后台处理库:Resque

> 原文：<https://www.sitepoint.com/comparing-background-processing-libraries-resque/>

[![People and door blue](img/69d2421314be9713afee641580ee3d95.png)](https://www.sitepoint.com/wp-content/uploads/2013/09/Fotolia_52657937_Subscription_XL.jpg)

这是比较 Ruby 后台处理框架系列的第二部分。可以查看上面链接的[上一篇](https://www.sitepoint.com/comparing-ruby-background-processing-libraries-delayed-job "Delayed Job")。总结第一篇文章，它涵盖了后台处理(队列、流程等)背后的原理。).此外，本文还深入研究了 delayed_job(以及如何使用它来创建后台作业并处理它)，这对于将后台处理真正快速地融入您的应用程序来说是非常惊人的。然而，它使用 ActiveRecord，所以它有点慢，你最终会在整个应用程序中混淆背景和前景代码。

Resque 希望解决这两个问题，所以，让我们开始吧！

## 雷斯克

为了使用 Resque，您需要安装 Redis。幸运的是，他们有很好的安装文档。如果你在 Mac OS X(像我们很多人一样)，你要做的就是:

```
brew install redis
```

在 Linux 上，通过您的包管理器安装 redis 是可行的。例如，在 Debian/Ubuntu 上:

```
sudo apt-get install redis-server
```

遗憾的是，Redis 不支持 Windows。提交了一个端口，但没有合并。

安装 redis 并运行 Redis 服务器后，可以将 Resque 添加到您的 gem 文件中:

```
gem 'resque'
```

然后安装软件包:

```
bundle install
```

您需要设置 rake 任务，这将允许您让工作人员运行您的 Rails 应用程序。为此，向“lib/tasks”目录添加一个名为“resque.rake”的文件(这将定义与 resque 相关的 rake 任务):

```
require 'resque/tasks'
```

如果您的员工需要访问模型(即，您将要进行的后台处理需要访问 ActiveRecord 等)。)，您应该将以下内容添加到同一个文件(lib/tasks/resque.rake):

```
task "resque:setup" => :environment
```

最后，您应该为您的工作人员创建一个单独的目录，因为他们将独立于您的 Rails 应用程序的其他方面运行。我用“app/workers”来表示这一点。

现在我们已经完成了初始设置，让我们用 Resque 编写第一个 worker。在`app/workers/print_worker.rb`下保存以下内容

```
class PrintWorker
  @queue = :print

  def self.perform(str)
    puts "From print worker: " + str
  end
end
```

代码本身非常简单，工作人员通常遵循这种模式。首先将队列的名称定义为一个实例变量(您可以随意命名)。然后，所有工人都有一个叫做`perform`的类方法。这是处理队列中的作业时调用的方法。

例如，如果您正在编写一个需要发送一些电子邮件的工人，这就是发送电子邮件的代码将被编写的地方。

特别是，`PrintWorker`执行一个非常简单的任务；它获取一个字符串，然后打印出来，前面有一个特殊的“来自打印工人”的注释。

然而，现在，这并没有真正做任何工作；该类是简单定义的(即队列中没有作业)。让我们解决这个问题。

下面是一个简单的索引控制器(显然，您可以将动作中的内容放入应用程序的任何控制器中):

```
class IndexController < ApplicationController
  def index
    Resque.enqueue(PrintWorker, params[:to_print])
  end
end
```

另一段非常简单的代码。它所做的只是获取用户对“index#index”的请求，然后为打印工人添加一个作业。但是，重要的位是`params[:to_print]`，它实际上指定了`PrintWorker`将打印的字符串由传递给`index`动作的`GET`参数决定。

如果我们去“/index/index？to_print=somestring "，则`PrintWorker`应该打印出“somestring”。

然而，这到底要在哪里印刷呢？我们需要设置一个工作进程！与 delayed_job 不同，Resque 要求您设置一个工作进程来“消耗”作业队列。现在，让我们在终端中键入以下命令:

```
rake resque:work QUEUE=print
```

我们简单地使用了 resque 提供的 rake 任务之一，以便在名为“print”的队列上启动一个工作进程。现在，这应该会给你一个看起来没有任何作用的过程。

将浏览器指向“/index/index？to_print=lol”(当然是正确的主机名)，然后查看该过程的输出，您应该看到打印工人打印了:“From the print worker: lol”。

显然，这似乎是一个非常简单的例子，事实也的确如此。但是，它确实涵盖了 resque 的中心思想；worker 用一个`perform`方法定义为类，队列被命名，您可以将变量传递给 worker。

好了，现在我们知道了，让我们来看看 Resque 与 delayed_job 的比较。显然，delayed_job 更容易上手。但是，在我看来，如果不小心的话，delayed_job 代码很快就会变得错综复杂，因为对于同一个方法，可以将异步调用和同步调用混合在一起。Delayed_job 也可能比较慢，因为默认情况下它不使用内存存储。

然而，delayed_job 确实有一些优点。如前所述，它非常容易启动和运行。此外，它的能力还包括一些 Resque 做得不太好的事情，比如区分任务的优先级。

就个人而言，Resque 是我日常使用的产品。它结合了一种很好的方式，将您的代码、Redis 和性能都组织到一个很棒的包中。

## 示例应用程序

为了演示 Resque 的基本用法(以及快速测试您的 Redis 实例)，我创建了一个小的示例应用程序，让您可以在您的机器上运行一些简单的 workers。它执行与 delayed_job 的示例应用程序完全相同的功能:异步和同步地对文件执行页面计数。这是一个非常简单的应用程序，但它可以作为你前进的工作范例。

点击查看

## 敬请期待！

本文的第三部分将讨论 Sidekiq，并对到目前为止讨论的三个框架(DelayedJob、Resque、Sidekiq)进行总体比较。

## 分享这篇文章