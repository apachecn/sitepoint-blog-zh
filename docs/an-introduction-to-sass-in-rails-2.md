# Rails 中 Sass 的介绍

> 原文：<https://www.sitepoint.com/an-introduction-to-sass-in-rails-2/>

Sass 是一个非常用户友好的 CSS 实现。在我的[上一篇文章](https://www.sitepoint.com/an-introduction-to-sass-in-rails/)中，我对 Sass 做了一个大致的概述。在这里，我将更详细地描述 Sass 中的特性。我曾经写过，Sass 允许使用变量来存储十六进制值和执行颜色算术运算，从而降低了复杂性，但它还可以做更多的事情。

## 嵌套

Sass 允许程序员嵌套选择器和选择器属性，从而降低了编码的复杂性。自然，程序员会发现这其中的巨大价值。例如，将 CSS 规则嵌套在一个特定的类/id 中比重复输入要方便得多。因此，像其他广泛采用的编程语言(如 Ruby)一样，Sass 现在允许用 CSS 嵌套函数和循环。