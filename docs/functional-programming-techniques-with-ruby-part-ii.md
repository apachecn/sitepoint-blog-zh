# Ruby 函数式编程技术:第二部分

> 原文：<https://www.sitepoint.com/functional-programming-techniques-with-ruby-part-ii/>

在本系列的第一部分[中，我们看了看函数式编程的基础，并详细访问了不变性和无副作用代码。](https://www.sitepoint.com/functional-programming-techniques-with-ruby-part-i/)

今天，我们来看看高阶函数和 currying，这是 Ruby 用 sublime class 支持的两个非常有用的函数风格特性。首先，让我们探索一下 Ruby 中不同类型的函数及其特点。

## 方法、块、过程和 Lambdas

如你所知，Ruby 支持许多不同类型的函数。在 Ruby 中，块、过程、方法和 Lambdas 都只是这些类型的微小变化。它们之间的细微差别使得大多数 Ruby“函数过载”新手感到绝望。不要屈服，这比你想象的要容易得多！

我们将从方法开始，我们都知道并且喜欢它。你一直都在使用它们，它们快速且易于声明，它们帮助我们使用基于子程序编程的早期原则:

```
def some_method # ... end 
```

Ruby 中的方法经常被忽略的一点是，它们并不是严格意义上的“一级函数”(可以作为对象传递的函数)。此代码不起作用: