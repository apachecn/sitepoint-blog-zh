# 代码 Safari:线程化 Ruby

> 原文：<https://www.sitepoint.com/threading-ruby/>

这一周我遇到了一个问题:给定一个可能的 URL 列表，哪些是真实的？看起来像是一个完美的计算机解决的工作。我把手伸进我的 Ruby 工具箱，发现了一些巧妙的技巧。在本周的代码探索中，我们将做一些稍微不同的事情——我们将探索 Ruby 标准库本身，而不是研究一个流行的宝石。

URL 问题有几个不同的部分。第一个也是最明显的是能够判断一个 URL 是否是活动的。我不需要 100%彻底，所以我选择了最简单的方法——尝试从 URL 中获取一些东西，看看会发生什么。如果没有出现异常，可能是正常的。Ruby 的标准库中有一些从 URL 获取内容的方法。 [`Net/HTTP`](http://www.ruby-doc.org/stdlib/libdoc/net/http/rdoc/index.html) 是最常见和最灵活的，但对于简单的情况，我更喜欢 [`open-uri`](http://www.ruby-doc.org/stdlib/libdoc/open-uri/rdoc/) ，它位于`Net/HTTP`之上，提供了一个真正简单的接口，尤其是对于这个问题:

```
require 'open-uri'

def link_valid?(url)
  open(url)
  true
rescue
  false
end
```

注意这里你可以在一个方法上使用一个`rescue`块，而不是打开一个`begin`块。这是一个巧妙的技巧，可以通过缩进层次来整理代码。我还使用了一个开放的`rescue`(没有指定要捕捉哪些异常)，这通常是一种糟糕的形式；你应该明确你预期的错误。然而，在这种情况下，有太多不同的可能异常没有得到很好的记录——例如`SocketError`、`ArgumentError`和来自 [Errno](http://www.ruby-doc.org/docs/ProgrammingRuby/html/ref_m_errno.html) 家族的错误——并且考虑到我们只涉及一个 Ruby 调用，现在全面的救援就足够了。

现在我们有了检查链接有效性的方法，让我们用一些数据来反对它。

```
urls = %w(
  http://www.sitepoint.com
  http://xaviershay.com
  http://twitter.com
  not_a_link
  http://bogus.bogus
)

invalid_links = urls.reject {|x| link_valid?(x) }

puts invalid_links
```

正如所料，这段代码打印出了最后两个假的 URL。

## 请进步

这将是故事的结尾，除了检查每个链接花了相当长的时间，而且一路上没有任何进展更新。对于五个 URL 来说，这可能没问题，但是我的列表有几千个。我怎么知道程序没有被挂起？将一个`puts`放入`reject`块中很容易，但是这不是很灵活。让我们将代码转移到一个方法中，给我们一些空间来移动和处理这些代码。

```
def validate_urls(urls)
  urls.each do |url|
    yield url, link_valid?(url)
  end
end
&nbsp;
validate_urls(urls) do |url, valid|
  puts "#{url} was #{valid ? "valid" : "invalid"}"
end
```

我们现在得到更新，因为每个网址被检查。

## 速度更快

这很好，但是我们还没有解决根本的问题。我们刚刚展示了这段代码有多慢。主要的问题是，由于各种原因，一个 URL 可能需要一段时间才能到达`open`，可能是 DNS 解析、服务器运行缓慢，或者仅仅是互联网速度慢。由于我们是按顺序处理 URL，当我们等待一个 URL 时，我们不能做任何其他工作，我们的代码只是闲置在那里。由于每个 URL 检查都独立于任何其他检查，这是并行化或同时检查多个链接的理想情况。有一些技术可以做到这一点(几周前我介绍了一种基于光纤的方法，但是在这种情况下最简单的是使用`Thread`。

如果你真的对 Ruby 并发感兴趣，或者打算大量使用它，那么就值得仔细阅读一下你的 Ruby 版本是如何实现其线程模型的，因为在实现之间有一些重要的注意事项和差异。对于这种不经意的用法，只要知道它们允许我们同时做多件事就足够了。

我们的第一次迭代如下:

```
def validate_urls(urls)
  number_per_thread = (urls.length / 3.0).round # 3 threads
&nbsp;
  urls.each_slice(number_per_thread).map {|urls_subset|
    Thread.new do
      urls_subset.each do |url|
        yield url, link_valid?(url)
      end
    end
  }.each {|thread| thread.join }
end
```

这里发生了一些事情。首先，我将快速解释线程代码。`Thread.new`获取一个将在新线程中执行的块。这个代码块现在将与你的代码的其余部分并行执行*。如果我们仅仅停留在那里，我们将会有一个问题，因为`validate_urls`方法将会返回，我们的程序将会终止，而不会给线程块内部的代码一个执行的机会！这就是`join`方法派上用场的地方——它暂停主程序的执行，直到线程完成运行它的代码。在我们创建的每个线程上调用`join`(在*创建完所有线程后调用*)可以防止`validate_urls`返回，直到每个线程都完成。*

另一个重要的方法是`each_slice`。 [`Enumerable`模块](http://www.ruby-doc.org/core/classes/Enumerable.html)提供了一套丰富的方法，这些方法在`Array`和`Hash`上都可以找到，定期阅读列表非常值得，因为你通常会发现一种让你的生活更轻松的方法。对于任何类型的算法挑战，通常都有一个预先构建的解决方案可供您使用。在这种情况下，`each_slice`为每个`n`元素生成块，有效地为我们分块数组(注意在`Enumerable`上还有一个`chunk`方法，有时很有用)。我们只需要做少量的数学运算来计算出每个片段应该有多大，这将取决于 URL 的数量和所需线程的数量(这里是三个，但是可以随意使用这个数字来为您的数据和机器找到最佳值)。

这段代码是一个改进，但它仍然有一些缺点。它没有最大限度地利用可用的线程——很可能所有“慢”的 URL 都在第一个线程的块中结束，而另一个线程快速通过一个明显无效的列表，只是在第一个线程做所有艰苦工作时无所事事地等待。

## 共享线程

我们需要一种更好的方式来调度线程的工作。在我们知道每项任务要花多长时间之前，我们可以让线程不断地从一个公共池中获取 URL，直到这个池变空，而不是在开始时将所有的工作分开。

一个简单的实现如下所示:

```
def validate_urls(urls)
  urls = urls.dup
  &nbsp;
  3.times.map {
    Thread.new do
      while !urls.empty?
        url = urls.pop
        yield url, link_valid?(url)
      end
    end
  }.each {|thread| thread.join }
end
```

首先，我们创建传递的数组的副本，因为改变传递给方法的参数是不礼貌的。然后我们旋转三个线程，每个线程不断地从列表中获取 URL，直到列表为空。但是在螺纹王国里并不都是玫瑰！我们正在进入并发性的泥潭，肮脏的恶臭从泥沼中喷涌而出，事情并不像最初看起来的那样。这些代码在概念上看起来很好，实际上大多数时候它也运行得很好，但是当线程共享对数据的访问时，我们必须确保对数据的访问是所谓的*线程安全的*。允许两个线程随意访问内存会带来灾难性的、不可预知的后果，远远超出你我的预料。

你永远不应该假设类是线程安全的，除非它们被明确地记录下来。(这里是关于 Ruby 核心类的线程安全的[伟大但乏味的讨论](http://www.ruby-forum.com/topic/174086)。)安全总比后悔好，我妈妈常说。我们有两个选择来修复我们的代码:要么我们使用线程安全的数据结构，要么我们同步访问我们的数组(意味着我们一次只允许一个线程访问它)。前一种选择应该是首选，因为它更容易和更不容易出错。搜索“Ruby 线程安全数组”将我带到了标准库中的 [`Queue`](http://www.ruby-doc.org/stdlib/libdoc/thread/rdoc/classes/Queue.html) 类，它以线程安全的方式提供了我们想要的行为——文档中的例子实际上与我们的用例相同。

```
require 'thread'

def validate_urls(urls)
  queue     = Queue.new
  urls.each {|url| queue << url }
&nbsp;
  3.times.map {
    Thread.new do
      while !queue.empty?
        url = queue.pop
        yield url, link_valid?(url)
      end
    end
  }.each {|thread| thread.join }
end
```

砰，线程安全。作为最后一个细节，让我们确保在任何时候只发生一次让步，这样该方法的用户就不需要为任何并发性的想法而烦恼。就他们而言，他们只会有一个非常快的`validate_urls`方法。为此，我们将使用上面两个选项中的第二个:同步。这使用了另一个内置的 Ruby 类`Mutex`,它允许我们确保任何时候都只有一个线程在执行代码块。我将把它包括在整个计划的完整列表中:

```
require 'open-uri'
require 'thread'
&nbsp;
def link_valid?(url)
  open(url)
  true
rescue
  false
end
&nbsp;
def validate_urls(urls)
  queue     = Queue.new
  semaphore = Mutex.new
  urls.each {|url| queue << url }
&nbsp;
  3.times.map {
    Thread.new do
      while !queue.empty?
        url = queue.pop
        valid = link_valid?(url)
        semaphore.synchronize {
          yield url, valid
        }
      end
    end
  }.each {|thread| thread.join }
end
&nbsp;
urls = %w(
  http://www.sitepoint.com
  http://xaviershay.com
  http://twitter.com
  not_a_link
  http://bogus.bogus
)
&nbsp;
validate_urls(urls) do |url, valid|
  puts "#{url} was #{valid ? "valid" : "invalid"}"
end
```

`synchronize`方法是线程安全的，如果另一个线程当前在该代码块中，它将被阻塞。一切结合起来，给我们一个快速和强大的链接检查。

## 包扎

我们取了一小段简单的代码，把它变得非常复杂。您对自己的代码进行调优的程度将取决于您的特定需求，但是了解范围的两端将允许您针对您的环境做出最佳选择。

以下是一些需要进一步研究的问题:

*   使用事件并发模型，这段代码会是什么样子？(比如 EventMachine)
*   你将如何扩展这种技术来编写一个蜘蛛程序？(发现页面上的链接并跟踪它们的人)

请在评论中让我们知道你的进展，并在下周关注更多令人兴奋的代码丛林冒险。

## 分享这篇文章