# 深入了解 Ruby 中的哈希是如何工作的

> 原文：<https://www.sitepoint.com/diving-into-how-hashes-work-in-ruby/>

![](img/515f6a1f3960185686e0ef07d5e73e95.png)

Ruby 的 Hash 可以说是该语言中最有用的数据类型。利用一种非常适合于模拟现实世界问题的结构，结合快速查找，它经常是脚本、网页和复杂业务逻辑的主干。Ruby 甚至一度在内部使用它来存储所有的函数、变量和常量(尽管现代 Ruby 通过使用热路径的分支预测和调用点方法缓存来实现这一点；那是另一个帖子！)我们常常想当然地认为它“就是管用”。今天，我们将深入探讨 Hash 的内部原理，了解它的工作原理、效率以及实现方式。前进！

## 履行

```
big_four_stock_prices = {
    :google => 801.23,
    :apple => 115.57,
    :facebook => 128.35,
    :microsoft => 57.19
} 
```

这是怎么回事？让我们走一遍。

首先，运行时为数据结构分配一个连续的内存块。接下来，它散列第一个密钥:

```
:google.hash
=> 3592 
```

`hash`函数是在 Ruby 的内核类中声明的，所以它对任何继承链中有它的类都是可用的。实际数量会因多种因素而异，但重要的是它在整个会话中保持一致。

接下来，我们必须存储值。这个值被放在一个叫做“bucket”的地方(如果你来自其他语言，就叫做“bin”)。Ruby hash 最初会分配 11 个桶，所以我们需要决定在哪个桶中存储我们的 hash 值。为此，运行时只需执行以下计算:

```
 :google.hash % 11
 => 5 
```

这意味着`:google`的值`801.23`被放在哈希表的第 5 个桶中，其键的标识符类似于:

```
[:google, 801.23] 
```

它对其余的键/值对重复这个过程。为了把它拉回来，解释器简单地重复计算并从桶中取出值。简单吧？让我们进入边缘案例。

## 碰撞

你们中更精明的读者可能已经意识到这样一个事实，如果只有 11 个可能的桶，计算迟早会将两个值放在同一个桶中。接下来会发生什么？嗯，Ruby 的 hash 实现将以链表的形式将条目存储在同一个 bin 中。如果标识符(键)与存储的相匹配(用`equal?`检查)，它就返回值，如果不匹配，它就继续遍历列表。

当然，这个过程可能很耗时——如果一个项目与 1000 个其他项目共享一个存储桶会怎么样？理论上，返回所需的值可能需要很长时间，使用哈希表的全部意义在于它很快。露比，当然，你涵盖了。它通过保存整个表中每个箱中平均项目数的累计来实现这一点。一旦这个值超过 5，Ruby 就丢弃 11 个 bin 的数组，创建一个新的，在这个点上 Ruby 将重新散列这些值。通过这样做，bin 中的链表保持较短，从而减少了总的查找时间。

查找时间是 hash 的关键卖点。对于你们中间的[大 O 符号](https://en.wikipedia.org/wiki/Big_O_notation)，散列表将在常数时间内查找值；也就是说，随着表中值数量的增加，查找时间保持不变。在实践中，它更接近于 O (log n) 的[，但我们不要太迂腐！](http://lemire.me/blog/2009/08/18/do-hash-tables-work-in-constant-time/)

## 好的哈希算法的重要性

为了巩固我们对散列实现的理解，让我们尝试一个简单的实验:如果自定义散列函数每次都返回相同的整数，会发生什么？我们对散列的心理模型表明，这将把每个项目附加到同一个 bin 中的一个链表，并且在那个链表的末尾查找一个项目的时间将会更长。让我们尝试一些代码:

```
require 'benchmark'

class GoodHash
end

class BadHash
  def hash
    7
  end
end

hashing_algorithm == BadHash  # switch to GoodHash for inverse

17.times do |ex|
  lookup_key = nil
  size = 2**ex
  test_hash = {}
  (1..size).each do |n|
    index = hashing_algorithm.new
    lookup_key = index if n > size/2 and lookup_key.nil?
    test_hash[index] = rand
  end

  Benchmark.bm do |bench|
    bench.report("Hash size: #{size} elements 10000 times\n") do
      10000.times do
        test_hash[lookup_key]
      end
    end
  end
end 
```

这将生成如下所示的散列(大小不同):

```
test_hash = {
    #<BadHash:0x007f92941eaae0> => 0.029655456018705673,
    #<BadHash:0x007f92941e3c68> => 0.973576967117432,
    #<BadHash:0x007f92941e1508> => 0.2443654110192186,
    #<BadHash:0x007f92941d1ab8> => 0.7536013342129247
} 
```

让我们浏览一下代码:

首先我们创建一个类`GoodHash`，它使用 Ruby 的本地散列算法。然后我们循环 17 次(65536 项似乎是一个很好的停止点！)，对 2 的幂进行幂运算(`ex`)。然后，我们使用一个`GoodHash`实例作为密钥，将一个随机值插入到`test_hash`中。在用`BadHash`重复这个过程之前，我们对这个值查询 1000 次，并对结果进行基准测试

结果(从“真实”栏读取的读数):

| 哈希大小 | 古德哈希 | 恶汉 |
| --- | --- | --- |
| one | 0.002808 | 0.003077 |
| Two | 0.002841 | 0.003663 |
| four | 0.003018 | 0.004167 |
| eight | 0.005757 | 0.004750 |
| Sixteen | 0.003502 | 0.006898 |
| Thirty-two | 0.003174 | 0.011138 |
| Sixty-four | 0.002843 | 0.019534 |
| One hundred and twenty-eight | 0.003080 | 0.041806 |
| Two hundred and fifty-six | 0.003159 | 0.086643 |
| Five hundred and twelve | 0.003137 | 0.147724 |
| One thousand and twenty-four | 0.003179 | 0.287432 |
| Two thousand and forty-eight | 0.002843 | 0.554049 |
| Four thousand and ninety-six | 0.002828 | 1.101022 |
| Eight thousand one hundred and ninety-two | 0.005380 | 2.192948 |
| Sixteen thousand three hundred and eighty-four | 0.003037 | 4.385111 |
| Thirty-two thousand seven hundred and sixty-eight | 0.002830 | 8.739381 |
| Sixty-five thousand five hundred and thirty-six | 0.002830 | 17.314654 |

这相当确凿地证明了我们的假设。随着 hash 中条目数量的增加，`BadHash`类的查找时间也呈指数增长，而`GoodHash`实现仍然牢牢地围绕着相同的时间进行分组。`GoodHash`列中的异常值也说明了 Ruby 在桶变得太满时的重新散列。

有趣的是，Ruby 2.0 以后的版本对于较小的哈希值不是这样的。如果您的散列是 6 项或更少，它被存储为一个数组列表并被遍历，直到键匹配查找。虽然哈希表很快，但是到目前为止最大的开销是计算哈希值；这种方法巧妙地避开了这个过程。如果散列增长到 7 项，则丢弃该数组并使用传统散列。这在结果表中显示为散列大小为 8 的条目。

## Ruby 是如何分配 bin 的？

由于 bin 的数量并不随着条目的数量线性增加，Ruby 如何决定一个 hash 应该有多少个 bin 呢？Ruby 计算了一系列接近 2 的幂的质数。这样做的好处是，当数字与哈希值组合时，不太可能共享一个公共因子。如果这不是真的，那么我们之前讨论的模数计算每次都会产生相同的数字，这将把所有东西放入同一个容器中，与我们之前的实验没有什么不同。

## 结论

对于深入 Ruby 的内部，这是相对简单的。虽然我们已经非常广泛地介绍了散列，但是知道何时何地使用它们是很重要的。到目前为止，散列中最大的开销是重新散列、散列计算和分桶，所以当性能是一个因素时，要注意这些开销([JRuby 实现避免在内部依赖散列正是因为这个原因](http://www.javaworld.com/article/2074780/core-java/avoiding-hash-lookups-in-a-ruby-implementation.html))。然而，一般来说，考虑到它的智能设计和仔细的优化，hash 是一个很好的工具。我们现在确切地知道了散列是如何工作的，为什么它如此有性能，并且永远不要为一个对象重写散列函数！

## 分享这篇文章