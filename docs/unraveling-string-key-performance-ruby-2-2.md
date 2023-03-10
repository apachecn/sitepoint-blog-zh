# 解开 Ruby 2.2 中字符串键的性能

> 原文：<https://www.sitepoint.com/unraveling-string-key-performance-ruby-2-2/>

![Hanging Keys (XXXL)](img/1030c5604e22c30a644879a86e856c17.png)

每个人都希望他们的程序更快，占用更少的内存。然而，在不修改源代码的情况下实现这一点并不常见。这篇文章将介绍 Ruby 2.2.0 在使用哈希和字符串键时增加的优化。要理解优化，您需要了解当前的行为。

在 Ruby 2.1 和更早的版本中，如果你写了这样的代码:

```
hash["access_string"]
```

Ruby 将分配字符串`"access_string"`,以便查找具有该名称的键的条目。类似地，每次创建散列文字时，Ruby 都必须分配一个字符串:

```
hash = { "foo" => @variable }
```

这里，我们在每次调用这段代码时分配一个散列和`"foo"`字符串。这看起来没什么大不了的，但是如果你用相同的字符串键创建一个散列，并在每次请求时用相同的字符串键访问它们，这可能会累积起来，产生大量的垃圾对象。

例如，`"PATH_INFO"`在 Rack 项目的 15 个文件中被访问了 36 次，如下所示:

```
path = env["PATH_INFO"]
```

对每个请求都这样做，会创建数百个不需要分配的一次性字符串。这会降低程序速度并占用更多内存。

## 字符串与符号

可以通过使用符号来绕过散列中的字符串分配。通过一个简单的基准测试，我们可以看到这种类型的哈希访问要快得多:

```
require 'benchmark/ips'

STRING_HASH = { "foo" => "bar" }
SYMBOL_HASH = { :foo => "bar"  }

Benchmark.ips do |x|
  x.report("string") { STRING_HASH["foo"] }
  x.report("symbol") { SYMBOL_HASH[:foo]  }
end
```

结果:

```
Calculating -------------------------------------
              string   129.746k i/100ms
              symbol   152.476k i/100ms
-------------------------------------------------
              string      4.619M (± 5.0%) i/s -     23.095M
              symbol      8.587M (± 5.4%) i/s -     42.846M
```

使用符号的速度大约是字符串的两倍，因为我们不必进行分配。那么，我们为什么要使用字符串键呢？好吧，符号在 Ruby 2.2 中是垃圾收集的，但以前不是。因此，如果你正在创建一个基于用户输入的散列，那么你就有可能将你的应用程序暴露给一个[符号的拒绝服务攻击](https://www.ruby-lang.org/en/news/2013/02/22/json-dos-cve-2013-0269/)。

Ruby 2.2 增加了[符号 GC](http://www.slideshare.net/authorNari/symbol-gc) ，所以安全性问题减少了，但是如果你已经分配了一个字符串，那么在现有的字符串上调用`to_sym`会花费更长的时间。

## 字符串池带来乐趣和性能

Rack 使用了大量的字符串键，虽然我们不能简单地改变 API 来使用符号，但我们可以挤出一些改进的性能。通过告诉 Ruby 不要分配一个新的字符串，而是使用一个已经存在的字符串，并使用[字符串冻结优化](http://tmm1.net/ruby21-fstrings/)，我们获得了一些收益。

我们可以通过添加`freeze`来优化 Rack 内部基于字符串的哈希查找和创建:

```
path = env["PATH_INFO".freeze]
```

Ruby 理解`String#freeze`意味着字符串不会被修改。虽然它看起来像一个方法，但这是在编译时完成的优化。

```
require 'benchmark/ips'
HASH = { "PATH_INFO" => "bar" }

Benchmark.ips do |x|
  x.report("freeze") { HASH["PATH_INFO".freeze] }
  x.report("normal") { HASH["PATH_INFO"] }
end
```

结果:

```
Calculating -------------------------------------
              freeze   144.130k i/100ms
              normal   127.572k i/100ms
-------------------------------------------------
              freeze      7.326M (± 3.2%) i/s -     36.609M
              normal      4.566M (± 4.1%) i/s -     22.835M
```

使用`String#freeze`方法要快得多。我使用了类似的技术给 Rack 2 ~ 4%的减速带，并减少了内存使用量。

 [![tweet](img/dd8603113946767d16e2503c69c1483a.png)
](https://twitter.com/schneems/status/517467031034028033)

另外，我们还减少了 Rack 所需的运行内存。

## Ruby 2.2 字符串哈希性能提升

虽然这是一个非常简单的优化，并导致了相当好的性能提升，但它不是很像 Ruby 风格。为什么 Ruby 不能为我们做这些？嗯……可以。

在[补丁 r43870](https://github.com/ruby/ruby/commit/779ae78995977305aa5aec9cb5b562dcf54c22e7) 中引入的 Ruby master(即 Ruby 2.2.0+)现在自动为你做这种性能提升。我是通过 Rack Devel group 的 Eric Wong 得知这个优化[的。](https://groups.google.com/forum/#!topic/rack-devel/SKAE_yqN8-0)

以前，我们看到字符串和符号访问的比较非常有利于符号:

```
# Ruby 2.1.5

Calculating -------------------------------------
              string   129.746k i/100ms
              symbol   152.476k i/100ms
-------------------------------------------------
              string      4.619M (± 5.0%) i/s -     23.095M
              symbol      8.587M (± 5.4%) i/s -     42.846M
```

在 Ruby 2.2 中运行同样的基准测试:

```
# Ruby 2.2.0

Calculating -------------------------------------
              string    141371 i/100ms
              symbol    143241 i/100ms
-------------------------------------------------
              string  7475494.0 (±7.6%) i/s -   37039202 in   5.001749s
              symbol  8128373.4 (±10.6%) i/s -   40107480 in   5.011651s
```

尽管如此，使用符号比字符串快，但我们现在更接近了。字符串访问的速度不是一半，而是差不多。太神奇了。

## 字符串重复数据删除

在研究这些优化时，我发现有趣的是，直到最近 Java 才实现了这样一个[字符串重复数据删除特性，如这个哈希键优化](http://java-performance.info/java-string-deduplication/)。Ruby VM 不会删除每个字符串，它只关注由散列引起的热点。使用字符串键，这种能力将提高大多数大型 Ruby 应用程序的性能。

同样值得注意的是，虽然 Ruby 默认这样做，但是如果你绝对需要尽可能快的代码，手动使用`freeze`方法仍然更快。这类似于 Java 8，他们建议你手动实习字符串(类似于`freeze`)。你可以在这里[看到 Ruby 2.2.0 中不同访问模式速度的一些基准](https://github.com/rack/rack/pull/742#issuecomment-61005251)。

我喜欢这个特性的一点是，它允许我们编写 Ruby 代码并专注于意图。虚拟机可以看到何时可以优化这种情况，使我们的程序更快。我们不需要做任何事情来利用程序中的这种性能提升。

这是 Ruby 让我开心的又一个原因，免费表演。

* * *

如果你关心性能、字符串、Ruby 或基准测试，[在 twitter 上关注@ schneems](https://twitter.com/schneems)

## 分享这篇文章