# 比较 Ruby 和 Haskell，第二部分

> 原文：<https://www.sitepoint.com/comparing-ruby-and-haskell-part-ii/>

![](img/e9f3e572677c7548affc1d8f2c2da1fa.png "haskell")

让我们继续 Haskell 和 Ruby 之间的讨论。如果你错过了第一篇文章，[点击这里查看](https://www.sitepoint.com/comparing-haskell-and-ruby-part-i/)

## 内置的

这就是 Haskell 和 Ruby 难以置信的相似之处。两者都有内置于语言规范中的令人敬畏的实用程序，这意味着它们保证是可用的。

Haskell 提供的函数非常有用，尤其是处理数组/列表这样的事情——它们是函数式编程语言的基础。例如，`takeWhile`函数在某个谓词为真时，通过从现有列表中获取元素，从列表中创建一个新列表。

Ruby 似乎没有在全局函数中提供相同级别的有用性，但是，所有的对象都充满了令人敬畏的实例方法，这使得 Ruby 黑客的生活变得简单多了。

Haskell 的大量内置方法让它感觉很不像 C，更像 Ruby 或 Python。c 是一种简约的语言——只需要几天就可以学会语法和所有的内置功能。然而，当编写大型程序时，这使生活变得相当困难。对于 Haskell，情况就不一样了——它是一个大型规范，有大量的特性，在用这种语言编写代码时非常有用。

此外，在学习语言时，这些函数中的许多都是很好的学习工具。例如，在 Haskell 中有前面提到的`takeWhile`函数，您可以在这里看到它的运行: