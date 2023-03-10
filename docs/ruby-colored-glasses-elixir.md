# 红宝石色眼镜:灵丹妙药

> 原文：<https://www.sitepoint.com/ruby-colored-glasses-elixir/>

今天是我们正在进行的新系列**红宝石色眼镜**的第一篇文章。本系列旨在简要研究 Ruby 社区之外有趣的项目。这些项目可能影响了 Ruby，也可能被 Ruby 影响了。在 Rubysource，我们认为环顾四周，看看我们能从其他社区学到什么是很重要的。享受吧。

Ruby 最可爱的一点是它优雅的语法和对开发者的友好性。那么 Erlang 是如何适应的呢？原来，一个聪明的巴西名字[何塞瓦里姆](https://github.com/josevalim)决定透过红宝石色的眼镜来看待 Erlang。

尽管 Erlang 是一种有 25 年历史的语言，但它有一些有趣的特征在今天的博客领域很流行:

*   [函数语言](http://en.wikipedia.org/wiki/Functional_language)
*   动态打字
*   对[并发](http://www.erlang.org/erlang_book_toc.html)的强大支持

当然还有其他的，但是我会让你去学习更多关于 Erlang 的知识。在这篇文章中，我将向你介绍长生不老药。

## 长生不老药

Jose 是一位获奖的 Rubyist，他决定花些时间探索其他编程语言，包括 Erlang。在尝试之后，Jose 决定建立自己的语言，一个 Erlang 的超集，命名为[仙丹](https://github.com/josevalim/elixir)。它在 GitHub 上的描述简明地描述为提供，

> Erlang VM 上迷人的语法、方法调度和元编程

自 2011 年 1 月首次提交 GitHub 以来，已经发布了三个版本(当前版本是 [0.3.0](http://blog.plataformatec.com.br/2011/06/elixir-v0-3-0-released/) )。虽然现在还为时尚早，但我认为探索其他编程语言是值得的，尤其是那些为 Ruby 开发人员提供一些熟悉感的语言。

在接下来的部分中，我将介绍基本概念，比如类型、操作符和函数，并在适当的地方提供与 Ruby 的比较。

## 类型

Elixir 有一些基本类型，它们是该语言的核心，并且在很大程度上与 Ruby 类型相关联。

### 数字

Elixir 只提供整数和浮点数类型，而 Ruby 有五种类型的数字:Integer、Fixnum、Bignum、Float 和 Rational。所有典型的算术运算符都按预期工作:

```
3 + 2  % => 5
2 - 1 % => 1
3 * 3 % => 9
6 / 3 % => 2.0
6 / 4 % => 1.5
```

`%`是如何定义注释的，在本文的例子中使用它来显示某个操作的输出。一切看起来都很正常，只有一个例外:为什么`6 / 3`结果是浮点型的？结果是，当[执行除法](http://learnyousomeerlang.com/starting-out-for-real#numbers)时，Erlang 总是返回浮点。如果你想执行整数除法，你必须使用`div`操作符(你也可以用`rem`操作符得到模):

```
6 div 3 % => 2
7 rem 2 % => 1
```

另一个很酷的特性是方法调度可用于数据类型，非常像 Ruby:

```
5.+ 3 % => 8
```

上面的代码片段可以在 Elixir 和 Ruby 中运行，很不错吧？

### 原子

如果你熟悉 Ruby 中的符号，那么你已经掌握了原子。原子是字面量，其值等于它们的名字，也不被垃圾收集(这在 Ruby 和 Erlang 中是相同的行为)。

```
'functional
'Functional
'so_very_functional
'"hello windows users"
```

### 布尔运算

`true`和`false`。你在期待别的什么吗？在这些数值的背后，实际上是原子`'true`和`'false`。这感觉有点罗嗦，也不能实现提供“迷人语法”的灵丹妙药，所以你可以像你期望的那样使用`true`和`false`。

### 元组

元组相当于不可变的固定大小的数组。

```
x = {1, 2, 3}
x.length   % => 3
x.set 0, 7  % => {7, 2, 3}
x   % => {1, 2, 3}
```

请注意，一旦创建了`Tuple`，内容就不能更改了。提供了一个`set`方法，它将克隆元组，更新克隆的值并返回它。Ruby 中最接近的东西是一个冻结的`Array`，它不完全像一个元组:

```
x = [1, 2, 3]
x.freeze
x[0] = 5     # RuntimeError: can't modify frozen array
```

### 列表

Elixir 中的列表与 Ruby 中的数组非常相似，它们可以包含任何内容。事实上，它们共享一个非常通用的 API，请查看一些 Elixir 示例:

```
x = [1, 2, 3]
x.length   % => 3
x.map do(val)  % => [3, 6, 9]
  val * 3
end
x.any? do (val)  % => true
  val &gt; 2
end
```

如何决定使用列表还是元组？元组最适合于不打算更新元素的情况。这是因为元组元素连续存储在内存中，这导致在执行更新时必须重新分配内存中的整个空间(尽管它允许真正快速的访问)。

另一方面，列表很容易更新和迭代，因为它们是作为链表实现的。Jose 为我提供了一个元组可能是合适的例子:

> 例如，如果在 Erlang 中从数据库中检索数据，将返回一个元组列表，其中每个元组包含每个记录信息(每行一个元组)和列表中的所有元组。

欲了解更多信息，请查看[列表](https://github.com/josevalim/elixir/blob/master/lib/list.ex)来源。

### 有序字典

正如您可能已经猜到的，Ruby 中的`OrderedDict`类似于`Hash`。它是有序的键值对的映射。然而，与 Ruby 不同，键不是通过插入来排序的，而是通过使用 Erlang [排序规则](http://learnyousomeerlang.com/starting-out-for-real#bool-and-compare)来排序的。

```
x = {1: 'a, 5: 'e, 3: 'c}
x.key? 1   % => true
x.key? 9   % => false
```

### 用线串

字符串被视为 UTF 8 二进制文件。那是什么意思？Elixir 将字符串存储为二进制文件，其中每个字符由 1 到 4 个字节表示。另一方面，Ruby 1.9 决定在字符串如何被[编码](http://blog.grayproductions.net/articles/ruby_19s_string)时允许很大的灵活性。通过使用`to_char_list`和`to_bin`可以在字符列表和字符串之间进行转换。

```
x = "hello"
x.to_char_list    % => [104, 101, 108, 108, 111]

[104, 101, 108, 108, 111].to_bin  % => "hello"
```

如果这种二进制的东西引起了您的兴趣，那么值得在 Erlangs 文档网站上阅读更多的内容。

## 经营者

Elixir 有许多常见的操作符，您已经习惯了，但是还有一些有趣的额外操作符。我会很快地把它们全部列出来，并突出那些有趣的。

### 学期

所有的标准术语运算符都可用:`==`、`!=`、`<=`、`<`、`>=`、`>`。然而，有两个额外的操作符对您来说可能是新的，除非您使用过 JavaScript: `===`和`!==`。这两个运算符提供了检查两个值是否完全相等的能力。

```
1 == 1   % => true
1 === 1 % => true
1 === 1.0 % => false
```

请注意，`1`不等于`1.0`，因为第一个值是整数，而后者是浮点数。

### 算术

这里也没什么新的:`+`、`-`、`*`和`/`。然而，如果你想执行整数到整数的除法或者得到模，你可以分别使用`div`和`rem`操作符。

```
4 div 2   % => 2
5 rem 2  % => 1
```

还有其他操作符，比如逻辑和按位操作符，它们与 Ruby 的非常相似。

## 功能

自然，函数在 Elixir 中起着主要作用，毕竟它是一种函数式语言。可以使用`do`或`->`创建函数，后者用于创建内嵌函数(类似于 Ruby 中的`lambda`)。

```
sum = do(x, y)
    x + y
end
sum.call 1, 3   % => 4
sum.apply([1,3]) % => 4
sum[4, 2]   % => 6
sum.(9, 1)  % => 10

[1, 2, 3, 4].map -> (val) val * 2  % => [2, 4, 6, 8]
```

在上面的代码中，你可以看到有几种方法可以调用一个函数。对于任何熟悉 JavaScript 的人来说，你会认出`call`和`apply`函数，它们就像你习惯的那样工作。

Elixir 提供的另一个巧妙技巧是匿名函数。例如，如果我想获得列表中每个字符串的长度，我可以这样做:

```
strings = ["hello", "everyone", "reading", "this"]
strings.map do (val)   % => [5, 8, 7, 4]
    val.length
end

% or a bit simpler
strings.map -> (val) val.length   % => [5, 8, 7, 4]
```

如你所见，我只是对列表中的每一项调用 length。使用匿名函数，我可以进一步简化它:

```
strings.map _.length    % => [5, 8, 7, 4]
```

很可爱，对吧？就这样吗？当然不是。函数可以作为参数传递:

```
sum = -> (a, b) a + b
double_sum = -> (f, a, b) f.call(a, b) * 2
double_sum.(sum, 5, 3)  % => 16
```

当然还有更多关于函数的东西需要学习，尤其是通过阅读[源](https://github.com/josevalim/elixir/blob/master/lib/function.ex)。

## 然后

我在这里只触及了皮毛，希望能给你足够的体验，以诱使你自己去探索更多。[药剂](https://github.com/josevalim/elixir)正在积极开发中，新功能和错误修复定期完成。接下来值得探索的一些领域包括:

*   模式匹配
*   例外
*   调用 erlang 方法

请留下你的想法和发现的反馈，如果有的话，你花时间探索 Ruby 之外的语言是非常好的。

## 分享这篇文章