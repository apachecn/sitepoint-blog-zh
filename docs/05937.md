# 使用 TDD 以责任为中心的设计

> 原文：<https://www.sitepoint.com/responsibility-centric-design-using-tdd/>

![Responsibility](img/00eabb659d56c883a75461ba03475ac3.png)

在本文中，我们将通过应用以责任为中心的技术来重新审视处理外部排序问题。这个特定问题中的每一项职责都被封装起来，使得测试驱动开发变得容易，并留给我们灵活且可维护的代码。

希望在这篇文章结束时，你会学到一种解决激动人心的问题的激进方法。

## 外部分类

外部排序是一个非常有趣的问题，因为对输入文件进行排序会受到可用内存量的限制。

这篇[维基百科](http://en.wikipedia.org/wiki/External_sort)文章陈述了问题的症结所在:

> 外部排序是一类可以处理大量数据的排序算法的术语。当被排序的数据不适合于计算设备的主存储器(通常是 RAM)并且它们必须驻留在较慢的外部存储器(通常是硬盘驱动器)中时，需要外部排序。

我将问题限制在一个随机生成的大小为`M`的输入文件和一个大小为`N`的数组中，其中的`N * N = M`使事情变得简单。还需要检查我们的工作，因此我们将编写一个检查实用程序来确保排序成功。

## 以责任为中心的设计

这种设计模式的基本方法是将问题分解成契约，这些契约将成为我们的公共 API。每个*契约*将在解决方案中封装一个单独的角色。这里的关键是关注**每个封装做什么**，而不是**如何做**。

这篇[练习 Ruby](https://practicingruby.com/articles/responsibility-centric-vs-data-centric-design) 的文章详细介绍了这种方法的要点:

> 在以责任为中心的设计中，系统由它们必须实现的行为集合来划分。这种划分的目标是用多个相互作用的过程来描述系统的行为，每个过程扮演一个独立的角色。

我将问题分解为作者、读者和排队者。`Writer`和`Reader`将为我提供一个通用 API 来读取或写入持久存储。由于我必须将大的主输入文件分解成一个较小的缓存，`Queuer`将作为外部排序的每个单独文件的访问点。这里一个有趣的观察是，角色倾向于以动词命名，因为它们被设计出来做一些事情。此时，请注意每个封装是如何不关心**如何**完成的。

所以，让我们来分解一下:

```
module FIO
  class Writer
    def open(file)
    end
    def append(n)
    end
    def close
    end
  end

  class Reader
    def open(file)
    end
    def read
    end
    def close
    end
  end

  class Queuer
    def open(file)
    end
    def [](index)
    end
    def close
    end
  end
end
```

## 测试驱动开发

既然我们已经完成了合同，我们可以开始考虑编写测试了。对于测试驱动的开发，我想把你的注意力集中在我们如何从概念上解决这个问题上。这里的关键是关注每一份合同，以及什么是恰当的行为。幸运的是，作为一种动态语言，Ruby 使这变得非常容易，所以我们不必花时间通过编写接口代码来嘲笑和破坏类。

所以还是说说我们需要什么吧。

为了生成主要的随机生成的输入文件，我们需要某种类型的`Export`实用程序。例如:

```
class Export
  def initialize(writer)
    @writer = writer
  end
  def out(max, file_name)
  end
end
```

为了进行外部排序，我们需要一个`Sort`实用程序:

```
class Sort
  def initialize(reader, writer, queuer)
    @reader = reader
    @writer = writer
    @queuer = queuer
  end
  def external(max, chache_size, file_in, file_out)
  end
end
```

最后，为了在最后进行检查，我们需要一个`Check`实用程序:

```
class Check
  def initialize(reader)
  end
  def is_sorted(max, file_out)
  end
end
```

应该可以了。

这些工具对象和我们的契约之间的关系类似于客户机/服务器模型。在这里，服务器作为一个依赖项被注入。然后，客户端代码将调用公共 API 或*契约*来完成请求。

打个比方，从我们的合同中流出的数据就像我们在家里得到的供水服务。实用物品就像是我们家中完成服务所必需的管道。要记住的一点是，在以责任为中心的方法中，数据流经系统，而不是放在一个单一的地方。

注意，在开发过程的这一点上，我们不关心任何实现细节。我们的单元测试将被设计来指导创造性的过程。

## 单元测试

出于本文的目的，我使用`mocha`来帮助我编写测试。

在测试中，我们将定义`M = 9`和`N = 3,`。最后，像输入的`in`和输出的`out`这样的测试文件名就可以了。

让我们对我们的导出实用程序进行概念化:

```
class ExportTest < Test::Unit::TestCase
  def test_export_out
    # Mock dependency and add expectations
    writer = mock()
    writer.expects(:open).with('in').at_least_once
    writer.expects(:append).with(anything).times(9)
    writer.expects(:close).at_least_once
    # Call utility
    Export.new(writer).out(9, 'in')
  end
end
```

要成功通过这项测试，我们可以做到:

```
def out(max, file_name)
  @writer.open(file_name)
  max.times { @writer.append(Random.rand(10)) }
  @writer.close
end
```

同样的模式也适用于检查实用程序:

```
class CheckTest < Test::Unit::TestCase
  def test_check_is_sorted
    reader = mock()
    reader.expects(:open).with('out').at_least_once
    reader.expects(:read).times(9).returns(0)
    reader.expects(:close).at_least_once
    target = Check.new(reader).is_sorted(9, 'out')
    assert target
  end
```

要成功通过这项测试，我们可以做到:

```
def is_sorted(max, file_out)
  return true if max <= 1
  sorted = true
  @reader.open(file_out)
  a = @reader.read
  (max - 1).times do
    b = @reader.read
    sorted = false if a > b
    a = b
  end
  @reader.close
  sorted
end
```

对于我们最后的测试，我们需要实现外部排序。第一件事是将输入文件分成有序的块。这意味着我们正在从我们的输入文件中读取 9 次。我们还将*编写*来分离大块文件总共 9 次。最后，我们将总共 9 次将最终结果写入输出文件，这样我们总共有 18 次。

我们的`Queuer`在这个测试中有一些细微的差别。queuer 对象的每个索引将允许我访问一个单独的块。我使用 queuer 来提供排序数据，直到它用完为止。在测试用例中，总共是 9 加 3，因为它需要告诉我它什么时候不再有任何东西了。

这是一个测试:

```
class SortTest < Test::Unit::TestCase
  def test_sort_external
    writer = mock()
    reader = mock()
    queuer = mock()
    reader.expects(:open).with('in').at_least_once
    reader.expects(:read).times(9)
    reader.expects(:close).at_least_once
    writer.expects(:open).with('out').at_least_once
    writer.expects(:open).with('in1').at_least_once
    writer.expects(:open).with('in2').at_least_once
    writer.expects(:open).with('in3').at_least_once
    writer.expects(:append).with(anything).times(18)
    writer.expects(:close).at_least_once
    queuer.expects(:open).with(3, 'in').at_least_once
    queuer.expects(:[]).with(anything).times(12).returns(0)
    queuer.expects(:close).at_least_once
    Sort.new(reader, writer, queuer).external(9, 3, 'in', 'out')
  end
end
```

为了通过测试，我们可以:

```
def external(max, cache_size, file_in, file_out)
  capacity = Array.new(cache_size)
  # Break file_in into chunks
  @reader.open(file_in)
  max.times do |index|
    capacity[index % cache_size] = @reader.read
    if is_end_of_chunk(index, max, cache_size)
      capacity.sort!
      # Use naming convention, e.g., file_in1
      @writer.open(file_in + ((index + 1) / cache_size).to_s)
      cache_size.times { |i| @writer.append(capacity[i]) }
      @writer.close
    end
  end
  @reader.close
  # Merge chunks onto file_out
  @writer.open(file_out)
  @queuer.open(cache_size, file_in)
  cache_size.times { |i| capacity[i] = @queuer[i] }
  max.times do
    lowest = min_index(capacity)
    @writer.append(capacity[lowest])
    capacity[lowest] = @queuer[lowest]
  end
  @queuer.close
  @writer.close
end

private
  def is_end_of_chunk(index, max, size)
    (!index.zero? && ((index + 1) % size).zero?) || max == 1
  end
  def min_index(arr)
    index = 0
    (1..arr.length - 1).each do |i|
      next if arr[i].nil?
      index = i if arr[index].nil? || arr[index] > arr[i]
    end
    index
  end
```

当我们运行这些测试时，实际上没有任何东西被写入您的文件系统。同样，这里的要点是从概念上测试我们的代码以确保正确的行为。单元测试是在不跨越功能边界的情况下测试独立代码的艺术，比如写入文件或发送网络请求。

## 结论

测试驱动开发带来了很多挑战，从一个非常抽象的角度考虑解决方案。然而，以责任为中心的解决方案已经通过分离关注点和加强设计良好的抽象层为您做了大量繁重的工作。

我省略了实现细节，作为读者的练习。如果你感兴趣，我已经把剩下的代码贴在了 [GitHub](https://github.com/beautifulcoder/external_sort) 上。

黑客快乐！

## 分享这篇文章