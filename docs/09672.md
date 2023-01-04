# 代码旅行:分叉、管道和并行 gem

> 原文：<https://www.sitepoint.com/code-safari-forks-pipes-and-the-parallel-gem/>

几周前，我写了一篇文章，关于把要做的工作分解成线程来提高 URL 检查器的性能。一位评论者指出，[平行宝石](https://github.com/grosser/parallel)可以用来达到同样的效果，这让我很好奇。我研究了 gem，发现它不仅可以并行化成线程，还支持将工作分成多个*进程*。

我想知道这是怎么回事？

## 砸开它

和往常一样，我从通常在 GitHub 上找到的代码副本开始:

```
git clone https://github.com/grosser/parallel.git
```

自述文件将我们引向`Parallel.map`作为代码的入口点。很容易找到，因为所有东西都在一个文件中:`lib/parallel.rb`。追溯这一点将我们引向 [`work_in_processes`](https://github.com/grosser/parallel/blob/master/lib/parallel.rb#L108) 和 [`worker`](https://github.com/grosser/parallel/blob/master/lib/parallel.rb#L161) 方法，这是我们试图弄清楚的核心。让我们从`work_in_processes`的顶部开始，目的是弄清楚它的结构。

```
# lib/parallel.rb
def self.work_in_processes(items, options, &amp;blk)
  workers = Array.new(options[:count]).map{ worker(items, options, &amp;blk) }
  Parallel.kill_on_ctrl_c(workers.map{|worker| worker[:pid] })
```

这产生了许多工作线程，然后如果 control C 被发送到父管理器进程，则注册它们以正确终止。如果没有这一点，杀死你的脚本将导致额外的进程被遗弃在你的系统上运行！`worker`方法实际上创建了新的过程，这就是事情开始变得有趣的地方。

```
def self.worker(items, options, &amp;block)
  # use less memory on REE
  GC.copy_on_write_friendly = true if GC.respond_to?(:copy_on_write_friendly=)

  child_read, parent_write = IO.pipe
  parent_read, child_write = IO.pipe

  pid = Process.fork do
    begin
      parent_write.close
      parent_read.close

      process_incoming_jobs(child_read, child_write, items, options, &amp;block)
    ensure
      child_read.close
      child_write.close
    end
  end

  child_read.close
  child_write.close

  {:read => parent_read, :write => parent_write, :pid => pid}
end
```

这里有三个重要的概念，我将依次介绍。首先是对`Process.fork`的调用。这是一个系统级调用(在 Windows 上不可用)，它有效地复制当前进程来创建一个新进程，称为“子进程”。就大多数意图和目的而言，这两个过程完全相同——相同的局部变量、相同的堆栈跟踪、相同的一切。然后 Ruby 指示原来的进程跳过给`Process.fork`的块，但是新的进程进入它，允许在每个进程中执行不同的行为。换句话说，给`Process.fork`的块只由新的子进程执行。

有了两个过程，我们现在需要一种方法在它们之间进行通信，以便我们可以安排要完成的工作。这就是第二个重要概念的来源:`IO.pipe`。由于这两个新进程是独立的，它们不能通过改变变量来进行通信，因为尽管它们最初会*在每个进程中*共享相同的名称和值，但它们是*复制的*，因此父进程不会看到子进程中的改变。管道是进程间通信的一种方法(同样，在 Windows 上不可用)。它就像一个文件，可以被一个进程写入，也可以被另一个进程读取。Parallel 设置两个管道来实现双向通信。我们将在本文后面进一步研究这种方法，现在只需认识到这是在建立一个通信通道。

最后一个重要的概念是`copy_on_write_friendly`调用，这需要暂时脱离内存管理来解释。`fork`如此高效的原因是，尽管它看起来像是在做一个精确的复制，但它实际上一开始并没有复制任何内存——两个进程将读取完全相同的内存位置。只有当一个进程*将*写入内存时，它才会被复制。这不仅意味着新进程的快速启动时间，而且如果它们只是读取和处理，也允许它们占用非常少的内存，正如我们经常期望我们的并行工作者所做的那样。

例如，假设一个典型的进程是 20mb。单独运行五个实例将导致 100mb 的内存使用量(5 批 20 个)，但是运行一个实例并派生它将导致仍然只有 20mb 的内存使用量！(实际上，由于制作新流程的一些开销，它会稍微高一些，但这是可以忽略的。)

但是我们有一个问题，那就是 Ruby 的垃圾收集器(它如何管理内存)。它使用一种称为“标记和清除”的算法，这是一个两步过程:

1.  扫描内存中的所有对象，并向它们写入一个标志，指示它们是否在使用中。
2.  清理所有未标记为使用中的对象。

您在步骤 1 中发现问题了吗？Ruby 垃圾收集器一运行，就对每个对象执行写操作，触发内存的复制！即使使用分叉，我们 20mb 脚本的五个实例最终仍将使用 100mb 内存。

正如关于的代码片段中的注释所示，一些非常聪明的人已经解决了这个问题，并将其发布为 [Ruby Enterprise Edition](http://www.rubyenterpriseedition.com/) 。如果你有兴趣，他们的[常见问题](http://www.rubyenterpriseedition.com/faq.html)有更多的细节供你继续阅读。

## 沟通

关于相关的分叉没有太多要说的，所以我想在本文的剩余部分集中讨论沟通渠道:`IO.pipe`。在`parallel`分支的子端，通过将适当编码的 Ruby 对象——结果或异常——写入管道，处理结果被发送回父端(参见`process_incoming_jobs` 的[端)。](https://github.com/grosser/parallel/blob/master/lib/parallel.rb#L195)

父进程为每个子进程生成一个线程，阻止等待数据出现在管道上，然后在将更多工作发送给进程之前整理结果。这种情况会持续下去，直到不再有工作需要调度为止。

```
# lib/parallel.rb:124
workers.each do |worker|
  listener_threads << Thread.new do
    begin
      while output = worker[:read].gets
        # store output from worker
        result_index, output = decode(output.chomp)
        if ExceptionWrapper === output
          exception = output.exception
          break
        elsif exception # some other thread failed
          break
        end

        result[result_index] = output

        # give worker next item
        next_index = Thread.exclusive{ current_index += 1 }
        break if next_index >= items.size
        write_to_pipe(worker[:write], next_index)
      end
    ensure
      worker[:read].close
      worker[:write].close
    end
  end
end
```

注意，`parallel`没有像我们在[上一篇文章](https://www.sitepoint.com/threading-ruby/)中那样使用`Queue`，而是使用`Thread.exclusive`来保存当前索引的线程安全计数器。

## 包装它

我们现在对如何创建新流程并在新流程之间进行通信有了一个大致的概念，让我们通过构建一个玩具应用程序来验证我们所学的方法来尝试和测试我们的知识:`fork`和`pipe`。

```
reader, writer = IO.pipe

process_id = Process.fork do
  writer.write "Hello"
end

if process_id
  # fork will return nil inside the child process
  # only the parent process wil execute this block
  puts "Message from child: #{reader.read}"
end
```

乍一看，这看起来很好，但是运行它，你会发现进程挂起。我们错过了一些重要的东西！`read`似乎被阻塞了，因为它没有从管道接收到文件结束信号。我们可以通过发送一个换行符并使用`gets`(它接收最多一个换行符的输入)来确认通信是否正常工作:

```
reader, writer = IO.pipe

process_id = Process.fork do
  writer.write "Hello"
end
```

```
if process_id
  puts "Message from child: #{reader.gets}"
end
```

该脚本按预期运行。那么为什么我们的第一个剧本没有成功呢？如果你不习惯使用并发代码，答案并不明显，但是在 [`IO.pipe`文档](http://www.ruby-doc.org/core/classes/IO.html#M000886)中有清晰的解释:

> 如果有任何写入程序仍打开管道，则管道的读取端不会生成文件结束条件。在父进程的情况下，如果不首先发出一个`writer.close`，`read`将永远不会返回。

答对了。尽管我们的子进程在退出时隐式地关闭了它的`writer`副本，但是我们的父进程仍然打开了对`writer`的原始引用！我们可以通过在尝试读取之前关闭它来解决这个问题:

```
reader, writer = IO.pipe

process_id = Process.fork do
  writer.write "Hello"
end

if process_id
  writer.close
  puts "Message from child: #{reader.read}"
end
```

以下是一些需要你进一步练习的内容:

*   扩展我们的示例脚本，允许父母也向孩子发送消息。
*   使用`Thread.exclusive`(如 parallel 所做的)而不是`Queue`(如我们的[上一篇文章](https://www.sitepoint.com/threading-ruby/)所做的)进行调度有什么优点/缺点？

请在评论中告诉我们你的进展。下周请继续关注代码丛林中更激动人心的冒险。

## 分享这篇文章