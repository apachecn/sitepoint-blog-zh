# Ruby 中的线程

> 原文：<https://www.sitepoint.com/threads-ruby/>

![Threads](img/6212815c01d50b618c17b205e94b5ae0.png)

Ruby 有许多吸引开发人员的很酷的特性，比如能够在运行时创建类，改变任何特定对象的行为，使用 ObjectSpace 监控内存中的类的数量，以及大量的测试套件。所有这些都让开发人员的生活变得更加轻松。今天我们将讨论计算机科学中最基本的概念之一:线程以及 Ruby 如何支持它们。

## 介绍

首先让我们定义“线程”。根据[维基百科](http://en.wikipedia.org/wiki/Thread_(computing))

> 在计算机科学中，执行线程是可由操作系统调度程序独立管理的最小编程指令序列。线程是一个轻量级的进程。

*线程是一个轻量级的进程*。属于同一进程的线程共享该进程的资源。这就是为什么在某些情况下使用线程更经济。

让我们看看线程是如何对我们有用的。

## 基本示例

考虑下面的代码

```
def calculate_sum(arr)
  sum = 0
  arr.each do |item|
    sum += item
  end
  sum
end

@items1 = [12, 34, 55]
@items2 = [45, 90, 2]
@items3 = [99, 22, 31]

puts "items1 = #{calculate_sum(@items1)}"
puts "items2 = #{calculate_sum(@items2)}"
puts "items3 = #{calculate_sum(@items3)}"
```

上述程序的输出将是

```
items1 = 101
items2 = 137
items3 = 152
```

这是一个非常简单的程序，有助于理解为什么我们应该使用线程。在上面的代码清单中，我们有 3 个数组，并计算它们的总和。所有这些都很简单。但是，有一个问题。除非我们收到了`items1`结果，否则我们无法获得`items2`数组的总和。对于`items3`来说也是同样的问题。让我们稍微修改一下代码来说明我的意思。

```
def calculate_sum(arr)
  sleep(2)
  sum = 0
  arr.each do |item|
    sum += item
  end
  sum
end
```

在上面的代码清单中，我们添加了一条`sleep(2)`指令，它将暂停执行 2 秒钟，然后继续执行。这意味着`items1`将在 2 秒后得到总和，`items2`将在 4 秒后得到总和(2 代表`items1` + 2 秒代表`items2`),而`items3`将在 6 秒后得到总和。这不是我们想要的。

我们的 items 数组并不相互依赖，所以最好能独立计算它们的和。这就是线程派上用场的地方。

线程允许我们将程序的不同部分移动到不同的执行上下文中，这些上下文可以独立执行。让我们编写上述程序的线程/多线程版本:

```
def calculate_sum(arr)
  sleep(2)
  sum = 0
  arr.each do |item|
    sum += item
  end
  sum
end

@items1 = [12, 34, 55]
@items2 = [45, 90, 2]
@items3 = [99, 22, 31]

threads = (1..3).map do |i|
  Thread.new(i) do |i|
    items = instance_variable_get("@items#{i}")
    puts "items#{i} = #{calculate_sum(items)}"
  end
end
threads.each {|t| t.join}
```

`calculate_sum`方法与我们之前添加了`sleep(2)`的代码示例相同。我们的项目数组也是一样的。最重要的变化是我们在每个数组上调用`calculate_sum`的方式。我们将对应于每个数组的`calculate_sum`调用包装在一个`Thread.new`块中。这就是如何在 Ruby 中创建线程。

我们做了一些元编程来根据循环中的索引`i`获得每个条目数组。在程序的最后，我们要求线程处理我们给它们的块。

如果您运行上面的代码示例，您*可能会*看到下面的输出(我使用*可能会*，因为您的输出可能在项目数组和序列方面有所不同)

```
items2 = 137
items3 = 152
items1 = 101
```

我们没有在 4 秒后得到对`items2`数组的响应，也没有在 6 秒后得到对`items3`数组的响应，而是在 2 秒后收到了所有数组的总和。这很棒，向我们展示了线程的力量。我们不是一次计算一个数组的和，而是一次或同时计算所有数组的和。这很酷，因为我们节省了 4 秒钟，这无疑是更好的性能和效率的标志。

## 竞赛条件

每个功能都是有代价的。线程是好的，但是如果你正在编写多线程应用程序代码，那么你应该知道如何处理竞争情况。什么是竞争条件？根据[维基百科](http://en.wikipedia.org/wiki/Race_condition#Software)

> 当独立的计算机进程或执行线程依赖于某种共享状态时，软件中就会出现竞争情况。对共享状态的操作是必须互斥的关键部分。不遵守这条规则可能会破坏共享状态。

简而言之，如果我们有一些可以被多个线程访问的共享数据，那么在所有线程完成执行后，我们的数据应该是正常的(意思是，没有损坏)。

## 例子

```
class Item
  class << self; attr_accessor :price end
  @price = 0
end

(1..10).each { Item.price += 10 }

puts "Item.price = #{Item.price}"
```

我们已经创建了一个简单的带有类变量`price`的`Item`类。`Item.price`循环递增。运行这个程序，您将看到以下输出

```
Item.price = 100
```

现在让我们看看这个代码的多线程版本

```
class Item
  class << self; attr_accessor :price end
  @price = 0
end

threads = (1..10).map do |i|
  Thread.new(i) do |i|
    item_price = Item.price # Reading value
    sleep(rand(0..2))
    item_price += 10        # Updating value
    sleep(rand(0..2))
    Item.price = item_price # Writing value
  end
end

threads.each {|t| t.join}

puts "Item.price = #{Item.price}"
```

我们`Item`班也一样。然而，我们改变了增加`price`值的方式。我们特意在上面的代码中使用了`sleep`,向您展示并发性可能带来的问题。多次运行这个程序，您将观察到两件事。

```
Item.price = 40
```

首先，输出不正确且不一致。输出不再是 100，有时你可能会看到 30 或 40 或 70，等等。这就是**竞争条件**所做的。我们的数据不再正确，并且每次运行程序时都会被破坏。

## 互斥现象

为了修正竞争条件，我们必须控制程序，使得当一个线程正在工作时，另一个线程应该等待，直到工作线程完成。这被称为**互斥**，我们使用这个概念来删除程序中的竞争条件。

Ruby 提供了一种非常简洁优雅的互斥方式。观察:

```
class Item
  class << self; attr_accessor :price end
  @price = 0
end

mutex = Mutex.new

threads = (1..10).map do |i|
  Thread.new(i) do |i|
    mutex.synchronize do 
      item_price = Item.price # Reading value
      sleep(rand(0..2))
      item_price += 10        # Updating value
      sleep(rand(0..2))
      Item.price = item_price # Writing value
    end
  end
end

threads.each {|t| t.join}

puts "Item.price = #{Item.price}"
```

现在运行这个程序，您将得到以下输出

```
Item.price = 100
```

这是因为`mutex.synchronize`。任何时候都有且只有一个线程可以访问包装在`mutex.synchronize`中的块。其他线程必须等待，直到正在处理的当前线程完成。

我们已经使我们的代码线程安全。

Rails 是线程安全的，当多个线程试图访问相同的代码时，它使用互斥类的实例来避免竞争情况。从`Rack::Lock`中间件看下面的[代码](https://github.com/rack/rack/blob/master/lib/rack/lock.rb#L16)。您将看到`@mutex.lock`被用来阻塞试图访问相同代码的其他线程。要深入了解 Rails 中多线程的细节，请阅读我的文章。此外，你可以访问 Ruby [互斥](http://www.ruby-doc.org/core-2.0.0/Mutex.html)类页面，参考`Mutex`类。

## 不同 Ruby 版本中的线程类型

在 Ruby 1.8 中，有“绿色”线程。绿色线程由解释器实现和控制。以下是绿色线程的一些优点和缺点:

**优点**

*   跨平台(由虚拟机管理)
*   统一的行为/控制
*   轻量级->速度更快，内存占用更小

**缺点**

*   未优化
*   限于 1 个 CPU
*   阻塞 I/O 会阻塞所有线程

从 Ruby 1.9 开始，Ruby 使用原生线程。原生线程意味着 Ruby 创建的每个线程都直接映射到操作系统级生成的线程。每一种现代编程语言都实现了原生线程，所以使用原生线程更有意义。下面是本机线程的一些优点:

**优点**

*   在多个处理器上运行
*   由操作系统安排
*   阻塞 I/O 操作不会阻塞其他线程。

尽管 Ruby 1.9 中有本地线程，但是在任何给定的时间都只有一个线程在执行，即使我们的处理器中有多个内核。这是因为 MRI Ruby (JRuby 和 Rubinius 没有 GIL，因此有“真正的”线程)使用的 GIL(全局解释器锁)或 GVL(全局虚拟机锁)。如果一个线程已经被 Ruby 执行了，这可以防止其他线程被执行。但是 Ruby 足够聪明，如果一个线程正在等待某个 I/O 操作完成，它会将控制权切换到其他等待线程。

在 Ruby 中使用线程非常容易，但是我们必须小心各种陷阱和并发问题。我希望你喜欢这篇文章，并能在你的 Ruby 上应用线程技术。

## 分享这篇文章