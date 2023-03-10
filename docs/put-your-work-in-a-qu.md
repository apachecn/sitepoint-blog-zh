# 把你的工作放到一个任务中

> 原文：<https://www.sitepoint.com/put-your-work-in-a-qu/>

如果您曾经编写过一个应用程序，该应用程序必须在执行一些缓慢或长时间运行的任务的同时做出响应，那么您可能已经读过关于使用后台作业的内容。在 Ruby 的奇妙世界中，有很多处理后台工作的选项。今天，你将见到屈。

### 屈背景

屈是什么？像所有好的项目一样，自述文件提供了一个很好的描述:

> Qu 是一个用于队列和处理后台作业的 Ruby 库。它深受 delayed_job 和 Resque 的启发。

Brandon Keepers 创建 Qu 来克服现有排队库的缺点，同时帮助开发[和声](http://get.harmonyapp.com/)、[高斯](http://get.gaug.es/)和 [SpeakerDeck](http://speakerdeck.com/) at [有序列表](http://orderedlist.com/)。

屈试图克服的一些缺点包括:

*   无声失败
    *   一些现有的解决方案会失败，而你永远不会知道。
*   单一持久性后端
    *   仅为单个后端设计限制了队列库的使用环境。(例如 Resque/Redis 组合)
*   多次执行的作业
    *   一些排队库在 ActiveRecord 中出现争用，允许一个作业由多个工作线程运行。

Brandon 的[博客](http://opensoul.org/blog/archives/2011/10/19/why-another-queuing-library/)上的自述文件中还概述了其他几个问题。

除了致力于解决这些问题，曲还将在工人被杀时重新排队，这样就不会有人失业。此外，如果你熟悉 Resque，你会注意到 Qu 有一个“类似 Resque”的 API，这可能会帮助你过渡。

#### 无声失败

当作业运行时，如何处理失败？异常去哪里了？有些库什么都不做，而有些库会记录一个错误。当 Qu 执行一个作业时，它将捕获任何类型的异常，并将其记录到数据库中的“失败”集合中。这是一个方便的特性，但它不应该是您处理错误的唯一方法。

除了记录异常，您自己的作业代码应该捕捉异常并做出相应的反应。很自然，这种代码会患上“锅炉板炎”,这是一种你一遍又一遍地写同一种代码的疾病。

一旦[钩子](https://groups.google.com/forum/#!topic/qu-users/R8tehv4KMDA)准备好了，处理在执行工作时发生的错误将变得更加简单和干净。

#### 单一持久性后端

使用链接到单个持久性后端的队列库对于许多项目来说可能不是问题，但是对于一些项目来说可能是一个限制。也许你不确定是否要在你正在做的项目中使用 Redis 或 MongoDB。或者，也许你一直在使用 Redis，但发现它不能满足你的需求。在大多数情况下，这需要您更改队列库。

Qu 有一个抽象持久性后端的 API，允许使用许多持久性解决方案。从 0.1.3 版本开始，Qu 已经“开箱即用”地支持 Redis 和 MongoDB。在本文的后面，您将能够读到更多关于 Qu 的作者 Brandon 对支持额外的持久性后端的未来的想法。

#### 执行一次的作业

Qu 试图解决的一个问题是当多个工作人员访问数据库时可能出现的争用。Brandon 通过 ActiveRecord 发现了这个问题，并着手在 Qu 中解决这个问题。当工人抢工作时， [LPOP](http://redis.io/commands/lpop) 和 [BLPOP](http://redis.io/commands/blpop) 命令与 Redis 后端一起使用。MongoDB 通过其 [findAndModify](http://www.mongodb.org/display/DOCS/findAndModify+Command) 操作实现了同样的行为。

### 样品

下面是一个简单的例子，演示了如何创建一个作业并将其添加到队列中。之后，一个工人开始处理工作。

```
class ScrapeWebsite
  @queue = :data    # not required, defaults to :default

  def self.perform(url)
    results = url.scrape
    results.save
  end
end

# queue the job

job = Qu.enqueue ScrapeWebsite, "http://github.com"
```

从命令行，您可以启动一个 worker 来处理“数据”队列中的任何作业。

```
$ bundle exec rake qu:work QUEUES=data
```

很简单，对吧？您会注意到，您的“作业”只需要有一个名为 *perform* 的类方法(类似于 delayed_job)。这可能会在 Qu 的未来版本(当前版本是 0.1.3)中有所改变，以帮助支持钩子和其他特性。挂钩的目标是允许开发人员“挂钩”到工作生命周期(之前、之后等)。

另外，如果你在 Heroku 上部署应用，Qu 已经准备好了。它可以通过使用`MONGOHQ_URL`环境变量轻松连接到 MongoDB。类似地，Qu 可以通过使用`REDISTOGO_URL`环境变量连接到 Redis！s

### 采访布兰登

brandom 很友好，通过 github 和我做了一些问答。

有计划添加其他持久性后端吗？

> 我至少希望看到一个通用的 SQL 后端。基于 Ryan Smith 的 queue_classic 的特定于 PostgreSQL 的后端会非常棒。我还没有任何其他的想法，但是我认为在其他创造性的用途上有很大的潜力。

*有计划添加排班吗？(即每小时运行该作业)*

> 不，但是我不会反对它。这一点的实现在多个后端之间会有很大的不同，所以要想让 API 正确，需要进行大量的预先考虑。如果有人想做这个，我很乐意帮助他们。

Qu 的路线图是什么(例如，到 1.0)？

> 对于一些我们从其他排队系统中了解并喜欢的基本功能(钩子、插件、统计)，当然还有一个华丽的 web 界面，还有很多工作要做。GitHub Issues 中列出了我最关心的事情:[https://github.com/bkeepers/qu/issues](https://github.com/bkeepers/qu/issues?sort=created&direction=asc&state=open)。
> 
> 我已经开始了关于[钩子](http://groups.google.com/group/qu-users/browse_thread/thread/47cb5e86fe0a3030)的工作，这将是一个好的插件 API 必不可少的一步。为了支持钩子，我对作业的定义做了一些修改。jobs 是一个比 resque 采用的类方法更丰富的对象，这个想法让我非常兴奋。

你还有什么想说的吗？

> 已经有一些分叉和拉取请求，但我希望看到更多的人参与进来。

### 结论

Qu 是另一个在后台处理作业的工具，它有一些有趣的特性和一个干净的 API。对于项目来说还处于早期，有好有坏。很好，因为很容易获得帮助、提供反馈和做出贡献！当然，因为它“年轻”，所以肯定会有错误(所有软件都有)，不断变化的 API 和潜在的缺失功能。

你用过其他排队和作业处理库吗？如果有，是哪些？你选择了哪个，为什么？

## 分享这篇文章