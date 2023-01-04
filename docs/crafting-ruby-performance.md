# 为性能制作 Ruby

> 原文：<https://www.sitepoint.com/crafting-ruby-performance/>

![Craftsman making vase from fresh wet clay on pottery wheel](img/5e7b96e5f0f130a741fff865a3e03004.png)

在过去，Ruby 以其缓慢的编程语言而闻名。但是随着 Ruby 2.0+的出现，这现在已经是神话了。在本文中，我们将研究 Ruby 的 MRI 性能，以及这对您的程序意味着什么。

首先，我想从 OOP 中的一些好的实践开始:

*   确定要转化为“对象”的关键抽象。
*   将每个对象封装到具有独特关注点的单独职责中。
*   为每个封装分配一个目的，并进一步分离责任。
*   当一个抽象被太多的责任压得喘不过气来时，给自己一记耳光。
*   重复一遍。

其次，我想强调的是，这些实践对性能有影响。构建一个完整的全能应用程序既有趣又具有挑战性。它甚至可以在单个过程上运行！但是如果您将许多复杂的职责暴露给一个简单的请求，您的最终用户将会体验到这一点。

我曾经听一位智者说过，“如果你的 web 应用程序性能很差，那是你的错。”

让我们研究一下 Ruby 的性能如何与这些最佳实践相比较，好吗？

## 局部变量

对于我的基准测试，我将使用具有以下规格的“小白鼠”:

```
describe "A labrat" do
  it "has binding information" do
    assert LabRat.get_binding.kind_of?(Binding)
  end

  it "is a class" do
    assert LabRat.kind_of?(Class)
  end
end
```

我还需要一种方法来获得唯一的变量名，所以请尝试:

```
describe "A unique name" do
  it "is in fact unique" do
    names = {}
    20.times{ names[get_unique_name] = 1 }
    assert names.keys.length == 20
  end
end
```

我会让你想象通过这些测试需要什么。

现在是我的基准。在这种情况下，我将关注局部变量:

```
b = LabRat.get_binding
Benchmark.bm do |bench|
  bench.report "create #{Benchmarks::T_DATA} local variables" do
    Benchmarks::T_DATA.times do
      eval("#{get_unique_name} = 1", b)
    end
  end
end

lvarr = eval("local_variables", b)
Benchmark.bm do |bench|
  bench.report "retrieve #{Benchmarks::T_DATA} local variables" do
    Benchmarks::T_DATA.times do
      eval("#{lvarr.sample}", b)
    end
  end
end
```

这里有很多魔法。我要重新打开我的实验室老鼠，把一堆局部变量塞进去。然后我测试 Ruby 的性能。

这是结果:

```
user      system    total     real
create 5000 local variables    0.330000  0.000000  0.330000  (0.332978)
                               user      system    total     real
retrieve 5000 local variables  0.450000  0.010000  0.460000  (0.448220)
```

创建与查找局部变量花费的时间差不多。这可能是由于我在绑定变量时增加了开销。

使用我的 2013 MacBook Air 创建和检索 5000 个局部变量只需不到一秒钟。还不错。

那么，实例变量呢？

## 实例变量

所有的单元测试和基准测试都已完成。让我们进入代码:

```
o = LabRat.new
Benchmark.bm do |bench|
  bench.report "create #{Benchmarks::T_DATA} instance variables" do
    Benchmarks::T_DATA.times do
      o.instance_eval("@#{get_unique_name} = 1")
    end
  end
end

ivarr = o.instance_variables
Benchmark.bm do |bench|
  bench.report "retrieve #{Benchmarks::T_DATA} instance variables" do
    Benchmarks::T_DATA.times do
      o.instance_eval("#{ivarr.sample}")
    end
  end
end
```

我正在用`instance_eval`给我的实验室老鼠填充一堆实例变量。让我们看看这些数字:

```
user      system    total     real
create 5000 instance variables    0.170000  0.000000  0.170000  (0.165712)
                                  user      system    total     real
retrieve 5000 instance variables  0.030000  0.000000  0.030000  (0.033587)
```

令人着迷。实例变量比局部变量快两到三倍。这些基准开始讲述一个故事。

看看类变量的表现会很有趣。仅仅从这两个基准来看，封装代码似乎更有优势。封装是面向对象的重要组成部分。实例变量优于局部变量的事实表明 Ruby 更喜欢用这种方式编写代码。

但是，类变量呢？

## 类别变量

接下来，让我们看看一些基准测试代码:

```
Benchmark.bm do |bench|
  bench.report "create #{Benchmarks::T_DATA} class variables" do
    Benchmarks::T_DATA.times do
      LabRat.class_eval("@@#{get_unique_name} = 1")
    end
  end
end

cvarr = LabRat.class_variables
Benchmark.bm do |bench|
  bench.report "retrieve #{Benchmarks::T_DATA} class variables" do
    Benchmarks::T_DATA.times do
      LabRat.class_eval("#{cvarr.sample}")
    end
  end
end
```

就像前面的例子一样，我给我的小家伙填充了一堆类变量。魔法通过`class_eval`来创造我的变量。让我们看看这些结果:

```
user      system    total     real
create 5000 class variables    0.160000  0.010000  0.170000  (0.163653)
                               user      system    total     real
retrieve 5000 class variables  0.030000  0.000000  0.030000  (0.035327)
```

看起来类变量的表现和实例变量差不多。现在，是我给你们讲故事的时候了。

如前所述，在 OOP 中，封装是一件大事。这是驱动你的抽象的面包和黄油。为了保持 Ruby 的性能，您需要确保远离大型方法。这里有一个很好的经验法则:如果你发现自己写了太多的局部变量，是时候把它们分开了。这里一个好的设计原则是将一个关注点隔离到一个方法中。封装可以协调关注点之间的责任。

最后，让我们快速看一下散列和数组。既然我们已经讨论了变量，我们可以预期它们也遵循类似的模式。

所以，我们来验证一下。

## 数组

与代码略有不同。我在这里总结一下。

要创建本地阵列，您需要:

```
name = get_unique_name
eval("#{name} = []; #{name} << 1", b)
```

本地数组可通过以下方式检索:

```
laarr = eval("local_variables", b)
name = eval("#{laarr.sample}", b)
eval("#{name}[0]", b)
```

必须分配一个局部变量`name`确实增加了一些开销。让我们看看这些结果:

```
user      system    total     real
create 5000 local arrays    0.360000  0.000000  0.360000  (0.356111)
                            user      system    total     real
retrieve 5000 local arrays  0.500000  0.010000  0.510000  (0.508840)
```

还不错，和我的局部变量差不多。

现在，要创建实例数组，您需要:

```
o.instance_eval("@#{name} = []; @#{name} << 1")
```

实例数组可通过以下方式检索:

```
iaarr = o.instance_variables
o.instance_eval("#{iaarr.sample}[0]")
```

结果是:

```
user     system     total     real
create 5000 instance arrays    0.230000  0.000000  0.230000  (0.229147)
                               user      system    total     real
retrieve 5000 instance arrays  0.050000  0.000000  0.050000  (0.047157)
```

比我的实例变量慢。但仍然比我的本地数组快得多。

现在，要创建类数组，您需要:

```
LabRat.class_eval("@@#{name} = []; @@#{name} << 1")
```

类数组可通过以下方式检索:

```
caarr = LabRat.class_variables
LabRat.class_eval("#{caarr.sample}[0]")
```

现在结果是:

```
user      system    total     real
create 5000 class arrays    0.210000  0.000000  0.210000  (0.215799)
                            user      system    total     real
retrieve 5000 class arrays  0.050000  0.000000  0.050000  (0.048175)
```

有了这些结果，我们得到了一致的模式。这很好，因为它显示了语言的稳定性。正如所料，类数组的性能与实例数组大致相同。

## 混杂

哈希的基准代码与数组非常相似，所以我不会在这里展示。

结果是:

```
user      system    total     real
create 5000 local hashes       0.390000  0.000000  0.390000  (0.384721)
                               user      system    total     real
retrieve 5000 local hashes     0.550000  0.010000  0.560000  (0.558132)
                               user      system    total     real
create 5000 instance hashes    0.260000  0.000000  0.260000  (0.253513)
                               user      system    total     real
retrieve 5000 instance hashes  0.050000  0.000000  0.050000  (0.048339)
                               user      system    total     real
create 5000 class hashes       0.230000  0.000000  0.230000  (0.236260)
                               user      system    total     real
retrieve 5000 class hashes     0.060000  0.000000  0.060000  (0.052424)
```

这些结果告诉我同样的故事。在 Ruby 中，当您使用实例和类变量而不是局部变量时，您将获得性能提升。

## 结论

这些年来，Ruby 的性能有了很大的提高。Ruby MRI 2.0 最显著的改进之一是[位图垃圾收集器。](http://patshaughnessy.net/2012/3/23/why-you-should-be-excited-about-garbage-collection-in-ruby-2-0)

抛开这些结果不谈，请记住，应用程序性能更像是艺术而非科学。我曾经听说过一个项目，客户抱怨应用程序“慢”。所有的性能测试都表明它可以处理负载。事实证明，正是令人困惑的用户界面和工作流程使得人们的工作变得缓慢。以我的经验来看，合理的 OOP 设计原则会带来直观的 ui。物体之间的关系变得可以理解。这使得应用程序在客户看来“很快”。如图所示，Ruby 是这种设计的完美候选。

如果有兴趣，可以在 [GitHub 上下载代码。](https://github.com/beautifulcoder/ruby_performance)

黑客快乐！

## 分享这篇文章