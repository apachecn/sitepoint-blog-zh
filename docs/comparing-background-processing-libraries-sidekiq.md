# 比较后台处理库:Sidekiq

> 原文：<https://www.sitepoint.com/comparing-background-processing-libraries-sidekiq/>

[![People and door blue](img/69d2421314be9713afee641580ee3d95.png)](https://www.sitepoint.com/wp-content/uploads/2013/09/Fotolia_52657937_Subscription_XL.jpg)

这是后台处理系列的最后一部分。你可以通过上面的链接查看以前的帖子。我将总结一下到目前为止我们所讲的内容。

*   后台处理(队列、流程等)背后的原则。).
*   使用 delayed_job，这对于快速将后台处理整合到应用程序中来说非常神奇，但是，它使用 ActiveRecord，所以它有点慢，并且最终会在整个应用程序中混淆后台和前台代码。
*   使用 Resque，它希望通过使用 Redis 和单独的工人类来解决 delayed_job 的问题。

然而，我们仍然可以改进 Resque 的性能。这就是 Sidekiq 的用武之地。

## Sidekiq

如果您通读了前几篇文章中的理论部分(这是您真正应该做的)，您可能会注意到我将进程作为唯一的并发选项。事实证明这不是真的，Sidekiq 充分利用了其他东西:线程。

为了提高性能，线程是一种超级轻量级的并发措施。Sidekiq 页面上的标题直截了当地指出了这一点

> > "What happens if one Sidekiq process can complete the work of 20 Resque or DelayedJob processes?"

让我们看看如何使用 Sidekiq。如果您通读了 delayed_job 和 Resque 介绍，Sidekiq 在某种程度上是这两者的结合。Sidekiq 确实使用 Redis，所以您需要安装并运行它(查看以前文章中的 Resque 部分，了解如何做到这一点)。

首先，将下面一行添加到您的 gem 文件中:

```
gem 'sidekiq'
```

安装程序:

```
bundle install
```

幸运的是，这就是我们需要的设置。SideKiq 不像 Resque 那样直接使用 rake 系统，所以我们不用瞎摆弄“lib/tasks”。让我们开始在“app/workers/print_worker.rb”中编写我们的打印工人。这是:

```
class PrintWorker
  include Sidekiq::Worker

  def perform(str)
    puts str
  end
end
```

同样，我们必须在某个地方排队作业(我们将在索引控制器中完成):

```
class IndexController < ApplicationController
  def index
    PrintWorker.perform_async(params[:to_print])
  end
end
```

最后，我们必须启动 Sidekiq 过程。在 Rails 应用程序根目录下的 shell 中键入以下内容:

```
bundle exec sidekiq
```

这将启动一个等待作业的进程。让我们把一个放在队列里。

如果你去“/index/index？to_print=sidekiqisgreat "，您应该在 sidekiq 进程的输出中得到“sidekiqisgreat ”( sidekiq runner 包括一些其他信息，出于示例的原因，您可以安全地忽略这些信息。)

如果你学过 Resque，Sidekiq 是很容易的，但是它有一个我们还没有讨论的很大的“问题”:线程安全。因为 Sidekiq 使用线程，所以只有在线程安全的情况下才能使用 Sidekiq 中的库——其他任何东西都可能导致大量难以追踪的问题。这个*真的*限制了你可以用 Sidekiq 做什么。

其次，你必须确保*你的*代码是线程安全的(例如，全局变量是不允许的)。ruby(更确切地说，是“默认的”Ruby 解释器 MRI)也有一个叫做“解释器锁”的东西，这意味着一次只能运行一个线程，所以 Sidekiq 可以更好地与 Ruby 的替代实现一起工作，比如 Rubinus 和 JRuby。

经历这些麻烦有什么意义？为什么不直接用 Resque？最大的原因是性能；如果您正在处理大量受益于并发性的作业，这种差异会非常大。

## 最后的比较

在这个由三部分组成的系列中，我们已经介绍了后台处理背后的理论和三个后台处理框架:delayed_job、Resque 和 Sidekiq。各有沉浮。

有了 delayed _ job:
* Pros
–*难以置信的*快速轻松搞定
–不增加你的“筹码”；它可以很好地运行 ActiveRecord
——初学者或将代码从前台迁移到后台的绝佳选择
*缺点
——在 active record 上运行，因此它可能会比在 Redis 上运行的东西运行得更慢
——使得在文件中混合异步和同步代码变得非常容易，在我看来，这是一件坏事
—“这是一个错误”。分散在您的代码库中的“延迟”调用将使您在大约六个月后难以推理

有了 Resque:
* Pros
–它在 Redis 上运行，速度很快
–后台代码与工人类很好地分离
–**它有一个奇妙的 web 仪表板**
–它使后台处理变得相当容易
–这是我的最爱！
*缺点
——比 delayed_job
更难运行——仍然不是最快的！
–做优先的、基于时间的工作不像 delayed_job 那么容易

有了 Sidekiq:
*优点
-相当快，工人是轻量级的
-你可以非常容易地从 Resque 移植代码
-代码的很好分离
*缺点
-比 delayed_job
更难–(这是最重要的)你*必须*使用线程安全的库并编写线程安全的代码

正如我提到的，我最喜欢雷斯克。我避免 delayed_job 的主要原因是混合了同步和异步代码。当考虑 Sidekiq 时，让我害怕的是线程安全库的要求。

如果你的需求不同，你的选择也会不同。花时间去理解你的选择的后果吧！确保你不要过早地尝试优化，否则你会把时间花在与你的应用程序中真正的瓶颈无关的事情上！

## 示例应用程序

我已经使用 Sidekiq 构建了一个小的示例应用程序。它执行与 delayed_job 和 Resque 示例相同的操作，但是使用了 Sidekiq。它可以同步和异步地保存和显示上传的 PDF 文件的页数。需要注意的最重要的一点是，为了从 Resque 切换到 Sidekiq，我必须修改大约三行代码，因为我用来计算 pdf 页面数的库*恰好是线程安全的*。然而，如果不是这样，编写 Sidekiq 端口将会困难得多，因为我将不得不要么使用自己的库，要么使用 C 扩展。

示例应用程序的代码可以在[这里](https://github.com/RubySource/sidekiq-demo)找到。

## 包装它

正如您所看到的，Ruby 社区已经针对一个问题提出了三种独特的解决方案。各有利弊。选择一个你最喜欢的，并在你的下一个惊人的应用程序中使用它！

如果你有任何问题或建议，请在下面的评论中提出来！

## 分享这篇文章