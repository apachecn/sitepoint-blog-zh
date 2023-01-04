# Ruby 中的 Clojure 循环

> 原文：<https://www.sitepoint.com/clojure-loops-ruby/>

![rubyclojure](img/de0bb3da776a26214703d416fcc76471.png)

我打赌你对`for`和`while`循环非常熟悉。不过，你最后一次在 Ruby 中使用它是什么时候？Ruby 向我介绍了一个全新的循环世界，叫做迭代器。这是我第一次尝试`each`和`map`。从那以后，我们就一直很亲密，我也没有回头。

不过最近，我一直在花时间学习 Clojure。Clojure 偏爱可变类的值对象，提供丰富的不可变数据结构，强调函数式编程。就语言而言，它与 Ruby 相去甚远。在我的研究中，我惊讶地发现了另一种循环方式。

受到启发，我决定将这个新的 Clojure 循环移植到 Ruby 中。我决定使用 continuations，一个鲜为人知的 Ruby 特性，来让它工作起来。让我们看看这些循环是如何工作的，什么是延续，以及当这些世界碰撞时会发生什么。

哦，在我们开始之前，还有一件事。你需要参加 Clojure 的速成班。这只需要一分钟，其余的不会读起来像天书。

### Clojure 基础

让我们从函数语法开始。了解一个基本的函数调用是如何工作的很重要。我们将从熟悉的开始，在 Ruby 中添加一个整数数组:

```
> [1, 2, 3, 4].reduce(:+)
10
```

在 Ruby 中，你从一个数组开始，并对它调用`reduce`。你告诉`reduce`它应该对数组中的所有元素使用`+`。它完成工作并返回给你`10`。

我之前提到过 Clojure 有一个功能焦点。这意味着你不只是把一个对象举起来，然后在上面调用东西。相反，你把所有被使用的东西都传递给它自己。下面是 Clojure 中的相同代码:

[clo jure]
>(reduce+[1 2 3 4])
10
[/clo jure]

你会注意到的第一件事是圆括号把一切都括起来了。在它们内部，从要调用的函数开始，在本例中是`reduce`。然后，用函数 reduce with、`+`和要减少的数组来跟进。

有了这些知识，你就可以开始学习了。

### Clojure 环

在 Clojure 中，`loop`具有与函数相同的布局。您提供的第一个参数是一个初始值数组，后跟迭代期间要调用的代码:

[clo jure]
(loop[x 0]
(println x))
[/clo jure]

上面代码中比较棘手的部分是`[x 0]`。你看，`loop`接受一个 pairs 数组。每对都由一个变量和该变量的初始值组成。这段代码在循环的第一次迭代中将`x`设置为`0`。如果我们有多个变量，我们会一个接一个地添加。它可能看起来像这样:

[clo jure]
【x 0
y 1
z 2】
[/clo jure]

它也可以写成`[x 0, y 1, z 2]`(逗号在数组中是可选的，充当空格)，但是竖排样式看起来更容易一些。

在设置好变量之后，给`loop`至少一段代码在每次迭代中运行。我们将保持简单，使用`(println x)`，它在法律上相当于`puts x`。

在这一点上，你可能想知道我们如何停止这个循环。我告诉你一个秘密。我们的代码实际上根本不循环。它将运行一次迭代并退出。如果我们想要另一次迭代，我们必须调用`recur`并为变量传递新值。

[clo jure]
(loop[x 0]
(println x)
(recurve(+x 1)))
[/clo jure]

`recur`函数接受参数并调用它找到的最近的`loop`或`function`，同时传递这些参数。这就是 Clojure 处理递归函数和创建递归循环的方式。我们的`loop`需要`x`的一个值，所以我们发送`(+ x 1)`，它使用这个值进行下一次循环迭代。您可能已经发现`(+ x 1)`只是`x + 1`，所以下一次迭代将`x`作为`2`运行。现在，我们已经创建了一个无限循环。我怪你。

Clojure 这样工作是因为你不能重新分配变量。我们必须创建一个新的循环迭代，它有自己的作用域，其中`x`是`2`，并且只能是`2`，不能是除了`2`之外的任何东西。

在 Ruby 的上下文中，这感觉非常陌生。不过它有一个有趣的优势:你可以在循环中任意多个不同的地方调用`recur`。

让我们来看看为什么会有帮助。

### 主要因素

一个数的质因数是可以相乘得到那个数的质数。对于 15，这意味着 3 和 5。在 60 的情况下，质因数是 2、2、3 和 5。注意，你可以重复一个素数。

我们如何计算一个数的质因数呢？我们从除数 2 开始，第一个质数。我们将看看我们的数字是否能被除数整除。如果是这样，我们将把它存储在素数列表中，并在新的较小的数上再试一次。如果没有，我们将增加除数并尝试这样做。当我们数到 1，我们就完成了。

下面是 Clojure 中`prime-factors`函数的实现:

[clojure]
(defn 素数因子[数]
(loop[剩余数
素数[]
除数 2】
(cond
(=剩余 1)；；停在 1
素数
(= (rem 剩余除数)0)；；可整除
(递归(/剩余除数)(conj 素数除数)除数)
:else；；不能被整除
(重现剩余素数(+除数 1))))
[/clo jure]

我用的是`cond`，你可以把它想成`case`。每种情况都被考虑并与适当的动作配对。需要注意的是第 9 行和第 11 行。在第 9 行，我用新的更小的数调用`recur`，这是一个添加了除数的素数列表(`conj`表示`push`)，并且除数相同。

在第 11 行，当除数未能除尽该数时，我增加除数并重试。

实现变得非常简单。代码读起来很像描述我们如何计算质因数的文本。

好了，够了，Clojure。

### 让我们红宝石

我们如何在 Ruby 中实现`loop` / `recur`？我喜欢从一个我希望它如何工作的例子开始。

让我们用 Ruby 编写同样的质因数函数:

```
def prime_factors(number)
  Clojure.loop(number, [], 2) do |remaining, primes, divisor|
    case
    when remaining == 1
      primes
    when remaining % divisor == 0
      recur(remaining / divisor, primes.push(divisor), divisor)
    else
      recur(remaining, primes, divisor + 1)
    end
  end
end
```

Ruby 已经有了自己的`loop`，所以我把我们的放在了一个`Clojure`类中。我觉得那看起来很不错。实施时间！

### 谈正事

首先，让我们看看我们是否能让程序块运行:

```
class Clojure
  def self.loop(*initial_args, &block)
    block.call(*initial_args)
  end
end
```

这将得到一个循环，就像 Clojure 版本一样。

```
> Clojure.loop(1) { |x| puts x }
1
```

我们需要一种方法用新的论点再次调用它。递归似乎是显而易见的选择，但是这个计划有问题。我们如何在块中提供`recur`方法？即使我们知道如何做到这一点，Ruby 也没有针对大量递归调用进行优化。我们可能会导致堆栈溢出。

救援的延续！Ruby 将延续作为标准库的一部分。你要做的就是`require 'continuation'`。

如果你读了之后想，“一个连续的什么？”别担心，这是正常、健康的反应。现在，请允许我扭曲你的大脑。

延续的基本原理并不复杂。你在代码中设置一个标记，做一些事情，点击你的脚后跟三次，最后回到你最初标记的代码行。

让我们看一个从 1 数到 10 的例子:

```
require 'continuation'

mark = nil
number = callcc { |continuation| mark = continuation; 1 }
puts number
mark.call(number + 1) unless number == 10
```

让我们从第 3 行开始分解它。首先将变量`mark`设置为`nil`,这样当我们在下一行赋值时，它在正确的范围内可用。说到下一条线，4 号线发生了很多。

延续是使用`callcc`创建的。你会注意到它需要一块积木。它将立即执行该块，并向它传递一个 continuation 对象。在块中，将`mark`设置为 continuation 对象，以便我们保留对它的访问。然后，返回`1`，它被`callcc`用作返回值。

当我们到达第 5 行时，`mark`保存我们的延续对象，`number`等于`1`。第 5 行不言自明。第 6 行是魔术的后半部分。

在延续对象上使用`call`可以返回到创建延续的那一行。在这种情况下，这是第 4 行。传递给`call`的值作为第 4 行`callcc`的新返回值。
当执行完第 6 行时，我们返回到第 4 行，`number`被设置为`2`，从第 4 行继续执行。它将这样运行，直到`number`等于`10`。

回到我们的`loop`代码。我们需要一种方法来开始循环的另一次迭代。在 continuation 上使用`call`就能得到这样的结果。我们将创建一个延续，然后在延续的上下文中执行该块:

```
require 'continuation'

class Clojure
  def self.loop(*initial_args, &block)
    continuation = nil
    callcc { |c| continuation = c }

    continuation.instance_exec(*initial_args, &block)
  end
end
```

如果我们在`block`中的任何地方使用`call`，它会把我们送回上面代码中的第 5 行。现在，我们有了可以无限循环的代码。再说一次，我怪你。

```
> Clojure.loop(1) { |x| puts x; call }
1
1
...
```

我们越来越接近了，但是我们仍然不能把值传递给下一次迭代。让我们解决这个问题。

```
require 'continuation'

class Clojure
  def self.loop(*initial_args, &block)
    continuation = nil
    args = callcc do |c|
      continuation = c

      initial_args
    end

    continuation.instance_exec(*args, &block)
  end
end
```

就像我们的计数示例一样，从第 5 行开始，准备一个变量来保存延续。在第 6 行添加一个`args`变量，并将其设置为`initial_args`开始。现在，当值被传递给`call`时，它们将在`args`中结束。

再次从 1 数到 10:

```
Clojure.loop(1) do |number|
  puts number
  call(number + 1) unless number == 10
end
```

至此我们的`loop`作品。剩下唯一要做的就是将`recur`别名为`call`，这样我们就可以使用我们想要的方法名了。

```
require 'continuation'

class Clojure
  def self.loop(*initial_args, &block)
    continuation = nil
    args = callcc do |c|
      continuation = c

      class << continuation
        alias :recur :call
      end

      initial_args
    end
    continuation.instance_exec(*args, &block)
  end
end
```

我们做到了！

### 我们唯一需要害怕的是…

如果在此期间的任何时候，你想到了“GOTO ”,尖叫着从你的办公桌前跑出来，让你的同事们感到困惑，那也不是完全没有根据的。像 GOTO 一样，延续可以用于邪恶。如果您不小心在代码中添加了延续，您可能会看到一条无法遵循的执行路径。这也意味着你做错了。

我们已经看到了延续的能力，在代码中跳跃和携带数据。它们是一个惊人的工具，能够构建强大的控制流原语。延续可以用来给语言添加异常处理或者创建生成器。

它们不是你经常接触的东西，但是当你想做一些事情，比如说，构建一个 Clojure 风格的循环，它们会支持你。

## 分享这篇文章