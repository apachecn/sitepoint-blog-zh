# Ruby 如何使用内存

> 原文：<https://www.sitepoint.com/ruby-uses-memory/>

![ram 2](img/6acace5a22139c35c51762ce3de585e3.png)

我从来没有遇到过一个开发者抱怨代码变得更快或者占用的 RAM 更少。在 Ruby 中，内存尤其重要，但是很少有开发人员知道为什么他们的内存使用会随着代码的执行而上升或下降。本文将让您对 Ruby 对象与内存使用的关系有一个基本的了解，我们将介绍一些在使用较少内存的情况下提高代码速度的常用技巧。

## 对象保持

Ruby 增加内存使用量最明显的方式是保留对象。Ruby 中的常量永远不会被垃圾收集，所以如果一个常量引用了一个对象，那么这个对象就永远不会被垃圾收集。

```
RETAINED = []
100_000.times do
  RETAINED << "a string"
end
```

如果我们运行这个程序并用`GC.stat(:total_freed_objects)`进行调试，它将返回 Ruby 已经释放的对象的数量。在之前和之后运行这个代码片段产生的变化很小:

```
# Ruby 2.2.2

GC.start
before = GC.stat(:total_freed_objects)

RETAINED = []
100_000.times do
  RETAINED << "a string"
end

GC.start
after = GC.stat(:total_freed_objects)
puts "Objects Freed: #{after - before}"

# => "Objects Freed: 6
```

我们已经创建了 100，000 个`"a string"`的副本，但是因为我们**将来可能会**使用这些值，所以它们不能被垃圾收集。当对象被全局对象引用时，它们不能被垃圾回收。这适用于常量、全局变量、模块和类。从任何全局可访问的对象中引用对象时要小心，这一点很重要。

如果我们做同样的事情而不保留任何对象:

```
100_000.times do
  foo = "a string"
end
```

被释放的物体暴涨:`Objects Freed: 100005`。当保留对对象的引用时，您还可以验证内存是否比 12mb 小得多，大约 6mb。如果你喜欢，可以用 [get_process_mem gem](https://github.com/schneems/get_process_mem) 亲自测量。

可以使用`GC.stat(:total_allocated_objects)`进一步验证对象保持力，其中保持力等于`total_allocated_objects - total_freed_objects`。

## 保持速度

Ruby 的人都很熟悉干或者“不要重复自己”。对于对象分配和代码来说都是如此。有时，保留对象以便重用比一次又一次地重新创建它们更有意义。Ruby 为字符串内置了这个特性。如果你在一个字符串上调用`freeze`,解释器会知道你不打算修改这个字符串，它可以保留下来并被重用。这里有一个例子:

```
RETAINED = []
100_000.times do
  RETAINED << "a string".freeze
end
```

运行这段代码，您仍然会得到`Objects Freed: 6`，但是内存使用量非常低。用`GC.stat(:total_allocated_objects)`验证，只有几个对象被分配，因为`"a string"`被保留和重用。

Ruby 不需要存储 100，000 个不同的对象，而是可以存储一个包含 100，000 个对象引用的 string 对象。除了内存减少之外，运行时间也减少了，因为 Ruby 在对象创建和内存分配上花费的时间减少了。如果你愿意，可以用 [benchmark-ips](https://github.com/evanphx/benchmark-ips) 再检查一遍。

虽然 Ruby 内置了这种消除常用字符串重复的工具，但是您也可以对任何其他对象做同样的事情，方法是将它赋给一个常量。这已经是存储外部连接时的一种常见模式，例如 Redis:

```
RETAINED_REDIS_CONNECTION = Redis.new
```

因为常量引用 Redis 连接，所以它永远不会被垃圾收集。有趣的是，有时通过小心保留的对象，我们实际上可以降低内存使用。

## 寿命短的对象

大多数对象都是短命的，也就是说在创建后不久它们就没有引用了。例如，看一下这段代码:

```
User.where(name: "schneems").first
```

从表面上看，这似乎需要几个对象来运行(散列、`:name`符号和`"schneems"`字符串)。然而，当您调用它时，会创建更多的中间对象来生成正确的 SQL 语句，使用准备好的语句(如果有的话)，等等。这些对象中的许多只有在创建它们的方法被执行时才有效。如果对象不会被保留，我们为什么要关心创建它们呢？

生成适度数量的中长期对象会使你的记忆随着时间而增加。如果 GC 在那些对象仍然被引用的时候触发，它们会导致 Ruby GC 需要更多的内存。

## 红宝石内存上升

当使用的对象超过 Ruby 的内存容量时，它必须分配额外的内存。向操作系统请求内存是一项开销很大的操作，所以 Ruby 很少尝试这样做。它不是一次请求几 KB，而是分配一个比它需要的更大的块。您可以通过设置`RUBY_GC_HEAP_GROWTH_FACTOR`环境变量来手动设置该数量。

例如，如果 Ruby 消耗了 100 mb，并且您设置了`RUBY_GC_HEAP_GROWTH_FACTOR=1.1`，那么当 Ruby 再次分配内存时，它将获得 110 mb。当 Ruby 应用程序启动时，它将保持相同的百分比增长，直到达到一个稳定状态，在这个稳定状态下，整个程序可以在分配的内存量内执行。这个环境变量的值越小，意味着我们必须更频繁地运行 GC 和分配内存，但是我们接近最大内存使用量的速度会更慢。更大的值意味着更少的 GC，但是我们可能会分配比我们需要的更多的内存。

为了优化一个网站，很多开发者宁愿认为“Ruby 从来不释放内存”。这并不完全正确，因为 Ruby 确实会释放内存。这个我们以后再说。

如果将这些行为考虑在内，那么非保留对象对整体内存使用的影响可能会更有意义。例如:

```
def make_an_array
  array = []
  10_000_000.times do
    array <<  "a string"
  end
  return nil
end
```

当我们调用这个方法时，会创建 10，000，000 个字符串。当方法退出时，这些字符串不被任何东西引用，将被垃圾回收。然而，当程序运行时，Ruby 必须分配额外的内存来为 10，000，000 个字符串腾出空间。这需要超过 500mb 的内存！

![](img/e02cd8b879a281238981c46d7550bba9.png)

不管你的应用程序的其余部分是否适合 10mb，这个过程都需要分配 500mb 的内存来构建这个数组。虽然这只是一个简单的例子，但是想象一下，在一个非常大的 Rails 页面请求过程中，进程耗尽了内存。现在，如果 GC 不能收集足够的槽，它必须启动并分配更多的内存。

Ruby 会在一段时间内保留这些分配的内存，因为分配内存是很昂贵的。如果该进程使用了该最大内存量一次，它可能会再次发生。内存会逐渐释放，但是很慢。如果您关心性能，最好尽可能减少对象创建热点。

## 速度的就地修改

我用来加速程序和减少对象分配的一个技巧是通过修改状态而不是创建新对象。例如，下面是取自 [mime-types](https://github.com/halostatue/mime-types) gem 的一些代码:

```
matchdata.captures.map { |e|
  e.downcase.gsub(%r{[Xx]-}o, '')
end
```

这段代码采用从 regex `match`方法返回的 [matchdata 对象](http://ruby-doc.org/core-2.2.1/MatchData.html)。然后，它生成由正则表达式捕获的每个元素的数组，并将其传递给块。这个块将字符串变成小写，并删除了一些内容。这看起来是非常合理的代码。但是，恰好在需要`mime-types`宝石的时候被调用了上千次。对`downcase`和`gsub`的每个方法调用都会创建一个新的字符串对象，这需要时间和内存。为了避免这种情况，我们可以进行就地修改:

```
matchdata.captures.map { |e|
  e.downcase!
  e.gsub!(%r{[Xx]-}o, ''.freeze)
  e
}
```

结果当然更冗长，但也更快。这个技巧之所以有效，是因为我们从不引用传递到块中的原始字符串，所以如果我们修改现有的字符串而不是创建一个新的字符串，这并不重要。

注意:你不需要使用一个常量来存储正则表达式，因为所有的正则表达式都被 Ruby 解释器“冻结”了。

就地修改是一个你真的会遇到麻烦的地方。修改一个你没有意识到在其他地方被使用的变量真的很容易，导致微妙的和难以发现的回归。在进行这种类型的优化之前，请确保您有好的测试。此外，只优化“热点”，这是您已经测量并确定会创建过多对象的代码。

如果认为“物体是慢的”，那就错了。正确使用对象可以使程序更容易理解和优化。即使是最快的工具和技术，如果使用效率低下，也会导致速度变慢。

捕捉不必要分配的一个好方法是在应用程序级别使用[脱轨基准 gem](https://github.com/schneems/derailed_benchmarks) 。在较低的级别上，使用[分配 _ 追踪宝石](https://github.com/ko1/allocation_tracer)或[记忆 _ 剖析宝石](https://github.com/SamSaffron/memory_profiler)。

注:我写的是脱轨 _ 基准创业板。内存统计请看`rake perf:mem`。

## 自由真好

如前所述，Ruby 确实释放了内存，尽管很慢。在运行了导致内存膨胀的`make_an_array`方法之后，您可以通过运行以下命令来观察 Ruby 释放内存的情况:

```
while true
  GC.start
end
```

很慢，应用程序的内存会减少。当内存分配过多时，Ruby 会释放少量的空页面(一组槽)。当前用于分配内存的操作系统对`malloc`的调用也可能将释放的内存释放回操作系统，这取决于 malloc 库的操作系统特定实现。

对于大多数应用程序，比如 web 应用程序，导致内存分配的操作可以通过点击端点来触发。当端点被频繁命中时，我们不能依赖 Ruby 释放内存的能力来保持我们的应用程序占用空间较小。此外，释放内存需要时间。尽可能减少热点中的对象创建。

## 你起来了

既然您已经为理解 Ruby 如何使用内存打下了坚实的基础，那么您就可以开始测量了。选择一些我提到的工具:

*   [出轨 _ 基准](https://github.com/schneems/derailed_benchmarks)
*   [分配 _ 追踪](https://github.com/ko1/allocation_tracer)
*   [memory_profiler](https://github.com/SamSaffron/memory_profiler)
*   [基准测试-ips](https://github.com/evanphx/benchmark-ips)

然后，去测试一些代码。如果你找不到任何基准，试着在这里复制我的结果。一旦掌握了这一点，就试着深入自己的代码，寻找对象创建热点。也许它会出现在你写的东西里，或者会出现在第三方的宝石里。一旦你发现了一个热点，试着优化它。不断重复这种模式:找到热点，优化它们，并进行基准测试。是时候驯服你的红宝石了。

* * *

如果你喜欢关于 Ruby 内存统计的推文，请关注 [@schneems](https://twitter.com/schneems) 。

## 分享这篇文章