# 调试和剖析 Dojo——选择 5 种武器/技术

> 原文：<https://www.sitepoint.com/debugging-and-dissection-dojo-5-weaponstechniques-of-choice/>

大约一年前，当我开始使用 Ruby 时，一开始我感到有点失落。由于有很强的 Java 背景，我只是不习惯这么多的元编程、混合和动态/鸭类型。在通过结对编程学到很多东西的同时，我也开始简单地使用 IRB 和 Rails 控制台。当然，对于 Java 程序员来说，这种事情并不自然，但是当你发现自己带着上网本坐在穿过城镇的公交车上，没有互联网连接时，IRB 是保持清醒的最有趣——看起来也很怪——的方式之一。

但是不管一个交互式的 Ruby shell 有多酷，并不是每一个 bug 或者更奇特的语言结构都可以用它来解决。在学习 Ruby 的过程中，我收集了一些代码战的武器。特别是在试图掌握 Rails 的大量元编程时，您可以使用一个很好的武器库来赢得这场战斗，所以我决定向您展示我最喜欢的五个。

### 1.赤手空拳

首先是每个编程新手的最爱:经典的打印调试。

```
obj = Dir.new('/usr/bin')
puts obj
=> <Dir:0x94a6470>
# which should be replaced with
puts obj.inspect
=> <Dir:/usr/bin>
# or even shorter
p obj
=> <Dir:/usr/bin>
```

这个技巧很简单，如果你的对手触手可及的话，它会非常有效。然而不利的一面是，你的手会在几次击打后开始流血，这就是为什么你应该尽快学会用更好的武器战斗。

### 2.致命的一瞥

当你一直盯着你的敌人时，你的眼睛变得漆黑一片，直到他突然变成碎片。我想我们小时候都练习过这个…或者我只是一个奇怪的孩子。然而，这里的类比是从各个方面看待你的问题，并试图理解它的每个方面。所以首先我们应该更多地了解我们的对手。

```
n = 12 ** 34
n.class
=> Bignum
n.methods.sort
=> [:to_s, :coerce, :-@, :+, :-, :*, :/, :%, :div, :divmod, :modulo, ..., :class, :dup, ...]
```

哇这是很多方法！但是等等:class 和:dup？这些看起来很熟悉，它们可能是从 Object 类本身继承的。也许我们应该看看 Bignum 提供的方法。

```
n.public_methods(false).sort
=> [:%, :&, :_, :_*, :+, :-, :-@, :/, :<, :<<, :<=, :<=>, :==, :===, :>, :>=, :>>, :[], :^, :abs, :coerce, :div, :divmod, :eql?, :even?, :fdiv, :hash, :magnitude, :modulo, :odd?, :remainder, :size, :to_f, :to_s, :|, :~]
```

现在看起来清楚多了。但这里有另一个提示。假设您对所有以“to_”开头的方法感兴趣，您可以

```
n.methods.sort.grep /^to_/
=> [:to_c, :to_enum, :to_f, :to_i, :to_int, :to_r, :to_s]
```

### 3.威胁朋友和家人

现在我们知道了我们的敌人是谁，我们应该找出更多关于他的朋友和家人的信息，以防我们需要像一部优秀的老黑手党电影那样威胁这个混蛋。

```
n.class.superclass
=> Integer
Integer.superclass
=> Numeric
n.class.ancestors
=> [Bignum, Integer, Numeric, Comparable, Object, Kernel, BasicObject]
```

啊哈！所以，Bignum 显然继承了 Integer，Integer 继承了 Numeric。让我们快速检查一下。

```
Integer > Bignum && Numeric > Integer
=> true
```

好吧，酷，有道理。所以我们只知道谁是 Bignum 的父母和祖父母，但是他的兄弟、叔叔或孩子呢？您可能想知道是否有类似于 Class.ancestors 方法的东西，但是用于以相反的方向遍历继承树(或者图形，如果您愿意的话)。简而言之:没有。长回答:可以自己建。

```
class Class
  def descendants
    ObjectSpace.each_object(::Class).select { |c| c < self }
  end
end
```

如果您正在研究某个更大项目的奇怪数据模型，这个小技巧会非常有用。它简单地查看整个对象空间中的所有类，并选择从被检查的类继承的类。我知道这有点残忍，但它完成了任务。让我们用我们的例子来验证一下。我们刚刚了解到 Integer 和 Numeric 是 Bignum 的父代和祖代。这意味着我们现在可以向他们询问 Bignum 的兄弟和叔叔，以及 big num 的孩子。

```
Integer.descendants
=> [Bignum, Fixnum]
# Bignum has one brother</h1>
Numeric.descendants
=> [Complex, Rational, Bignum, Float, Fixnum, Integer]
# And 3 uncles: Complex, Rational and Float
Bignum.descendants
=> []
# Unfortunately Bignum has no child
```

### 4.武士刀

虽然非常优雅和多才多艺的武士刀或武士刀是一种强大的武器。它锋利无比，可以在瞬间展开，造成致命的伤害。在我们的例子中，这将是调试器，它被方便地内置到 Ruby 基础系统中。你可以用调试库运行你的脚本

```
$ ruby -r debug [options] [programfile] [arguments]
```

或者您可以将调试调用放在您的代码中

```
# boring code above
require 'debug'
# interesting code below
```

但实际上你应该看看 Gem 的“ruby-debug ”,它可以做内置调试器所做的一切，只是更快更好。这里有一个很好的小抄，这里的甚至是关于它的一整集 RailsCasts。一旦你掌握了使用基于控制台的调试器，它的用法是非常直观的，当你这样做的时候，你可能不想再使用任何图形用户界面了。

### 5.战斧

如果所有其他方法都失败了，而你仍然不知道如何打败你的对手，这可能是暴力是唯一解决办法的时候。在这种情况下，我们抓住战斧。

```
set_trace_func proc { |event, file, line, id, binding, classname|
  printf "%8s %s:%-2d %10s %8sn", event, file, line, id, classname
}
```

这个内核函数是调试的最后手段，因为它能够监控程序执行过程中发生的每一个事件。有了这个东西，你甚至可以区分纯 Ruby 方法调用和本地 C 方法调用，所以准备好一些 grep'n'read，因为这个小脚本的输出

```
set_trace_func proc { |event, file, line, id, binding, classname|
  printf "%8s %s:%-2d %10s %8sn", event, file, line, id, classname
}

def foo(bar)
  bar / Math::E
end

n = 12 ** 34
puts foo(n)
```

看起来像这样

```
$ ruby test.rb
c-return test.rb:3  set_trace_func   Kernel
    line test.rb:4
  c-call test.rb:4  method_added   Module
c-return test.rb:4  method_added   Module
    line test.rb:5
  c-call test.rb:5          **   Fixnum
c-return test.rb:5          **   Fixnum
    line test.rb:6
    call test.rb:4         foo   Object
    line test.rb:4         foo   Object
  c-call test.rb:4           /   Bignum
c-return test.rb:4           /   Bignum
  return test.rb:4         foo   Object
  c-call test.rb:6        puts   Kernel
  c-call test.rb:6        puts       IO
  c-call test.rb:6        to_s    Float
c-return test.rb:6        to_s    Float
  c-call test.rb:6       write       IO
1.8107891504915702e+36
c-return test.rb:6       write       IO
  c-call test.rb:6       write       IO
c-return test.rb:6       write       IO
c-return test.rb:6        puts       IO
c-return test.rb:6        puts   Kernel
```

所以你可以想象更大的东西会是什么样子。当然，你可以写一个选择性更强的函数，比如按事件类型或类名过滤。

希望你喜欢我的第一篇文章，这些武器中的一些将在你的下一场战斗中给你带来优势。毕竟，还记得天元帅特哈特·梅鲁在《星河战队》里说的话:“要对抗臭虫，我们必须了解臭虫。我们负担不起另一个克伦达图。”

也可以随意提供你可能有的任何有趣的技巧。只需发表评论并与我们分享！

## 分享这篇文章