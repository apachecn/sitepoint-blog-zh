# Ruby 哈希的算法乐趣

> 原文：<https://www.sitepoint.com/algorithmic-fun-ruby-hashes/>

![Futuristic Technology](img/b5f793cbc27bffac788046de87c62d13.png)

所有程序员都使用它们。你可以称它们为映射、字典、散列映射或关联数组。在 Ruby 的世界里，我们称它们为*哈希*，它们非常棒。简而言之，散列是键值对的集合。它类似于数组，只是它是通过任何对象类型的任意键而不是增量整数来索引的。如果你看一看[哈希 API](http://ruby-doc.org/core-2.2.0/Hash.html) ，你会发现它提供了一系列令人印象深刻的功能。在本文中，我们将不会关注散列 API 本身，而是使用散列来建模、抽象和解决常见的逻辑问题。

## 真值表和捕获算法

许多人纯粹将散列用作简单的数据查找表或方法的命名参数。这些都很好，但是我们可以通过利用 Hash 固有的灵活性和强大的特性来实现逻辑和复杂的算法，从而做更多的事情。让我们来看看面试官最喜欢的 FizzBuzz 测试。事情是这样的:

> 写一个程序，打印从 1 到 100 的数字。对于 3 的倍数，打印“Fizz”而不是数字，对于 5 的倍数，打印“Buzz”。对于 3 和 5 的倍数，打印“FizzBuzz”

这里我们有两个影响结果的条件:(`n % 3 == 0`)和(`n % 5 == 0`)。一旦我们发现了这一点，解决问题就简单了:

```
def conditional(n)
  if (n % 3 == 0) && (n % 5 != 0)
    'Fizz'
  elsif (n % 3 != 0) && (n % 5 == 0)
    'Buzz'
  elsif (n % 3 == 0) && (n % 5 == 0)
    'FizzBuzz'
  else n
  end
end
```

要运行 FizzBuzz 从 1 到 100 的序列，我们需要:

```
(1..100).each {|n| puts conditional(n)}
```

那是小菜一碟！但是散列在哪里起作用呢？嗯，我们可以把这个条件逻辑作为一个散列来捕获。要获得如何做到这一点的线索，写下 FizzBuzz 的真值表:

| (n % 3 == 0) | (n % 5 == 0) | 结果 |
| --- | :-: | --: |
| 真实的 | 错误的 | “嘶嘶” |
| 错误的 | 真实的 | 嗡嗡声 |
| 真实的 | 真实的 | “嘶嘶嗡” |
| 错误的 | 错误的 | 数字 |

注意到对于任何给定的输入，*输出*列由离散的非零值组成。这将是我们散列的一个好的关键字。让我们重写我们的真值表来适应散列:

| 钥匙 | 价值 |
| --- | :-: |
| “嘶嘶” | (n % 3 == 0) && (n % 5！= 0) |
| 嗡嗡声 | (n % 3！= 0) && (n % 5 == 0) |
| “嘶嘶嗡” | (n % 3 == 0)和(n % 5 == 0) |
| 数字 | (n % 3！= 0) && (n % 5！= 0) |

### 听着，妈，没有如果语句！

现在，很容易在真值表上建立我们的散列模型:

```
def declarative(n)
  h = {
    'Fizz' => (n % 3 == 0) && (n % 5 != 0),
    'Buzz' => (n % 3 != 0) && (n % 5 == 0),
    'FizzBuzz' => (n % 3 == 0) && (n % 5 == 0),
    n => (n % 3 != 0) && (n % 5 != 0)
  }
  h.key(true)
end
```

这里，我们使用散列来实现一些声明式的编程。我们没有告诉我们的应用程序如何计算每个数字的结果，我们只是声明哪些条件决定了这些键，并让数据结构进行提升。有趣的是:由于用于确定密钥的两个条件是互斥的，所以对于任何数字输入，散列中只有一个*真*值。这意味着输入的正确键将具有值`true`，而所有其他键都将是`false`，因此该方法返回给定输入的唯一一个值为`true`的键。

```
puts declarative(2) #=> 2
puts declarative(3) #=> Fizz
puts declarative(5) #=> Buzz
puts declarative(7) #=> 7
puts declarative(15)#=> FizzBuzz
```

以声明方式运行 FizzBuzz，顺序从 1 到 100:

```
(1..100).each {|n| puts declarative(n)}
```

我不知道你怎么想，但是这种方式比多条件方式更吸引我。我觉得它比那种方式更优雅、更整洁。不幸的是，优雅是有代价的。正如你从基准测试中看到的那样，声明式哈希方法比命令式条件语句慢得多。

能不能在优雅和性能之间达成一个愉快的妥协？为什么，是的，我们可以！

## 记忆化

记忆是简单地存储计算或检索的数据以备将来使用的技术。对你我来说也叫做*缓存*，哈希对于存储东西来说非常好。让我们再试试记忆式的 FizzBuzz:

```
memoization ||= Hash.new do |hash,key|
  hash[key] = case
  when (key % 3 == 0) && (key % 5 != 0)
    'Fizz'
  when (key % 3 != 0) && (key % 5 == 0)
    'Buzz'
  when (key % 3 == 0) && (key % 5 == 0)
    'FizzBuzz'
  else key
  end
end
```

我们正在创建一个散列，对于传递给它的每个数字键，它被初始化为正确的 FizzBuzz 值。运行范围最大为 100:

```
(1..100).each  { |n| memoization[n] }
```

已经创建了记忆化散列，并且已经将从 1 到 100 的每个键映射到它的 FizzBuzz 值。下次我们想要这个范围内的数字的 FizzBuzz 值时，一个简单的`Hash#fetch`将提供它。

除了解决 FizzBuzz 式的问题，记忆化还有一些非常实际的应用。我们都使用 web，所以下面是我们如何使用 Hash 实现一些原始的、健壮的响应缓存:

```
require 'net/http'
http = Hash.new{|hash,key| hash[key] = Net::HTTP.get_response(URI(key)).body }
puts http['http://www.google.com'] # make actual request
puts http['http://www.google.com'] # get cached value
```

现在，我们来看看性能。以下是在我的机器上运行两次 FizzBuzz 解决方案的基准测试，测试范围为 1..1 *000* 000 。

| 用户 | 系统 | 总数 | 真实的 |
| --- | :-- | :-: | --- |
| 有条件的 | 0.230000 | 0.000000 | 0.230000 |
| 有条件的 | 0.240000 | 0.000000 | 0.240000 |
| 宣言的 | 0.990000 | 0.000000 | 0.990000 |
| 宣言的 | 0.980000 | 0.000000 | 0.980000 |
| 记住 | 1.040000 | 0.020000 | 1.060000 |
| 记住 | 0.370000 | 0.000000 | 0.370000 |

第一次运行记忆代码时，速度非常慢。Ruby 必须构建散列，并执行条件逻辑。但是第二次和之后的每一次，它的执行速度都大大加快了。这是因为散列已经包含了该范围的所有值，所以只要我们需要它，它就必须查找它们，而不是重新计算它们。

您可能会注意到，尽管对于后续的运行来说很快，但是记忆技术仍然没有`if..elsif`方法那么快。这是因为我们没有通过将缓存应用于 FizzBuzz 这样简单的线性算法来获得缓存的全部好处。为了充分利用记忆，我们需要在更复杂的多项式或指数级算法逻辑上使用记忆。

## 递归散列

递归是快速增长算法的典型领域。让我们看一个递归的经典例子:*阶乘*问题。通常的递归实现是这样的:

```
def factorial(x)
  x == 1 ? 1 : x * factorial(x-1)
end
```

我们可以用一个散列来记忆它:

```
factorial_hash ||= Hash.new do |hash,key|
  hash[key] =  key * hash[key-1]
end
# initialize special cases
fib_hash[1] = 1; fib_hash[2] = 2
```

在这里，我们以这样的方式初始化一个散列，对于输入 *n* ，强制初始化代码执行 *n* 次。第一次运行时，比方说，`factorial_hash[5]`代码将运行 5 次，为键 2-5 创建一个散列。然后，每次我们需要得到一个数字的阶乘值，直到 5，散列将只做一个简单的查找，并获取正确的值。

每种方法运行两次，得到 3000 的阶乘:

```
factorial(3000) 
factorial(3000) 
factorial_hash[3000]  
factorial_hash[3000]
```

以下是时间安排:

| 用户 | 真实的 |
| --- | :-- |
| 阶乘 | 0.005829 |
| 阶乘 | 0.006082 |
| 阶乘散列 | 0.011202 |
| 阶乘散列 | 0.000002 |

和以前一样，第一次散列运行比非散列运行花费的时间更长，但是随后的运行现在快得惊人。

让我们试试更复杂的东西:斐波纳契数列。以下是标准方法:

```
def fibonacci(n)
  n <= 2 ? 1 : fibonacci(n - 1) + fibonacci(n - 2)
end
```

这是我们的缓存哈希方法:

```
fib_hash ||= Hash.new do |hash,key|
  hash[key] = hash[key-1] + hash[key-2]
end
# initialize special cases
fib_hash[1] = 1; fib_hash[2] = 1
```

将每种方法运行两次以获得 35 处的斐波纳契值给了[以下计时](https://gist.github.com/39b9199540ca0ad976f6):

```
fibonacci(35) 
fibonacci(35) 
fib_hash[35]  
fib_hash[35]
```

| 用户 | 真实的 |
| --- | :-- |
| 斐波那契 | 0.987518 |
| 斐波那契 | 0.981804 |
| 斐波那契散列 | 0.000041 |
| 斐波那契散列 | 0.000002 |

现在，即使是散列方法的第一次运行也比非缓存递归方法快得多。随后的运行让非缓存方法相形见绌！问题越复杂，记忆化带来的好处就越大。

但是每一线希望都有阴云。使用哈希递归地将更多的数据添加到我们的应用程序的堆栈框架中，这意味着递归哈希方法对于堆栈溢出类型的错误有更低的阈值(那是*错误*，*而不是*网站)。因此，对于具有大量输入值的算法，递归散列可能不是最佳解决方案。

## 结论

散列不仅仅是将选项传递给方法的一种方式。如果使用得当，它们有助于使我们的代码更优雅、更快，有时两者同时进行。然而，我们需要意识到它们的缺点，并知道如何以及何时有效地使用它们。

本文中介绍的代码可以在以下列表中找到:

*   [FizzBuzz](https://gist.github.com/39b9199540ca0ad976f6)
*   [记忆化](https://gist.github.com/3da28faccd818f1a52da)

## 分享这篇文章