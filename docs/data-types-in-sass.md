# Sass 中的数据类型

> 原文：<https://www.sitepoint.com/data-types-in-sass/>

数据类型存在于几乎所有的编程语言中，Sass 也不例外。数据类型是一种将数据分类到特定类别以用于不同用途的方式。例如，`2`是一个`number`，`SitePoint`是一个`string`。在本教程中，我将介绍 Sass 中存在的所有七种数据类型，并尽可能提供一些示例来说明每种类型的用法。

## 空

`null`是 Sass 中所有数据类型中最基本的。它不是`true`或`false`，它只是空的。它没有任何价值。你应该知道的一件事是`null`的任何变体，即使有一个字母在不同的情况下也不会是`null`。这意味着`NULL`和`Null`实际上都不是`null`。它们都是字符串。

即使`null`什么都不代表，当传递给`length(..)`函数时，它仍然返回长度 1。这是因为`null`仍然是一个实体，即使它已经被用来仅仅代表什么。此外，您不能将`null`与其他字符串连接在一起。由于这个原因，`'text' + null`将会无效并抛出一个错误。

## 布尔运算

这个数据类型只有两个可能的值:`true`和`false`。Sass 中只有两个值评估为`false`——`false`本身和`null`。其他都返回`true`。考虑以下代码:

```
$i-am-true: true;
$a-number: 2;

body {
 @if not $i-am-true {
 background: rgba(255, 0, 0, 0.6);
  } @else {
 background: rgba(0, 0, 255, 0.6); // expected
  }
}

.warn {
 @if not $a-number {
 color: white;
 font-weight: bold;
 font-size: 1.5em;
  } @else {
 display: none; // expected
  }
}
```

这里，我使用了两个变量— `$i-am-true`和`$a-number`。在进一步解释代码之前，我应该提到 Sass 中的`not`操作符相当于其他语言中的`!`操作符。因此，条件`@if not $i-am-true`等同于`if (!$i-am-true)`，其最终评估为假，因为`$i-am-true`是`true`。这导致蓝色背景。

正如我前面提到的，除了`false`和`null`之外的任何值都计算为`true`。这意味着变量`$a-number`也将计算为`true`。因此，不应该显示带有类别`warn`的段落。正如你在下面的演示中所看到的，事实确实如此。

在 [CodePen](http://codepen.io) 上看笔[Sass Booleans](http://codepen.io/SitePoint/pen/yOjbWa/)by site point([@ site point](http://codepen.io/SitePoint))。