# Sass 基础:嵌套

> 原文：<https://www.sitepoint.com/sass-basics-nesting/>

当您刚刚开始使用 Sass 时，您首先会听到的特性之一就是嵌套。我们使用预处理器的一个原因是为了减少创建 CSS 规则所需的输入量。嵌套允许我们使用快捷方式来创建规则。所有伟大工具的问题是，误用的可能性总是存在。嵌套没有什么不同，因为过度使用会创建复杂的、难以管理的样式表。

## 什么是嵌套

嵌套允许你编写模拟 HTML 结构的选择器。这允许你使用快捷键来创建你的 CSS。例如:

```
div {
    p {
 color: black;
    }
}
```

这是最简单的嵌套。`div`元素包含 P 元素。这将依次编译成。

```
div p { color: black; }
```

我们也可以赋予`div`它自己的属性。

```
div {
 font-size: 14px;
    p {
 color: black;
    }
}
```

这反过来编译成两个独立的规则，一个用于`div`本身，另一个用于`div`中的`p`元素。

```
div { font-size: 14px;}
div p { color: black; }
```

## 如何使用嵌套

嵌套样式足够简单。您只需将一个选择器(或多个选择器)放在另一个选择器的大括号内。

```
.parent {
    .child {
    }
}
```

嵌套可以扩展到你希望的深度。这意味着您可以将元素嵌套在一个元素中，而这个元素又嵌套在另一个元素中。

```
.first-level {
    .second-level {
        .third-level {
            .fourth-level {
            }
        }
    }
}
```

您可以嵌套的元素的深度实际上是没有限制的。要记住的主要一点是，你能做某事并不意味着你应该做。一般来说，嵌套深度不要超过三层是个好主意。超过这个范围就会影响代码的可读性。Sass 是为了帮助我们更快地编写 CSS，而不是创建一堆不可维护的样式。例如

```
.page {
 font-family: sans-serif;
    .content {
 background-color: black;
        .text {
 color: white;
 font-size: 12px;
            .headline {
 font-weight: bold;
                a {
 color: blue;
 &:visited {
 color: green;
                    }
 &:hover {
 color: red;
                    }
 &:active {
 color: yellow;
                    }
                }
            }
        }
    }

}
```

延伸五层深的巢看起来很无辜。看一下编译后的输出，就可以看出嵌套太深的问题。

```
.page { font-family: sans-serif; }

.page .content { background-color: black; }

.page .content .text { color: white; font-size: 12px; }

.page .content .text .headline { font-weight: bold; }

.page .content .text .headline a { color: blue; }

.page .content .text .headline a:visited { color: green; }

.page .content .text .headline a:hover { color: red; }

.page .content .text .headline a:active { color: yellow; }
```

如果我们改变一个网页的结构，就会出现问题。假设我们改变了。content to .article。所有底层类都必须重写，因为它们都依赖于 content。

我们也有这样的问题，我们创建的规则只在我们代码的一部分有用。如果我们想用这些样式。文本在我们网站的其他地方，因为我们不能。文本被绑定到包围它的元素。

## 引用父选择器

在上面这个糟糕的例子中，我们使用了一个&符号来指定父选择器应该放在哪里。在上面的示例中，使用:来创建锚元素的伪类。

```
a {
 color: blue;
 &:visited {
 color: green;
    }
 &:hover {
 color: red;
    }
 &:active {
 color: yellow;
    }
}
```

这将编译为

```
a { color: blue; }

a:visited { color: green; }

a:hover { color: red; }

a:active { color: yellow; }
```

这本身可读性很强，因此任何人都可以维护。您还可以使用&来构建复合选择器。您可以通过在&符号后面加上后缀来实现这一点。例如:

```
.col {
    &-span1 { width: 8.33%; }
    &-span2 { width: 16.66%; }
    &-span3 { width: 24.99%; }
}
```

这给了我们

```
.col-span1 { width: 8.33%; }
.col-span2 { width: 16.66%; }
.col-span3 { width: 24.99%; }
```

正如您所看到的，父选择器被放置在与号所在的位置。

## 嵌套属性

Sass 还提供了一种使用 CSS 名称空间编写样式的简写方式。通常当在同一个名称空间中设置属性时，例如`border`，我们必须在样式表中写出单独的属性。使用 Sass，我们可以编写一次名称空间并嵌套它的属性。

```
.example {
 border: {
 style: dashed;
 width: 30px;
 color: blue;
    }
}
```

这将编译为

```
.example {
    border-style: dashed;
    border-width: 30px;
    border-color: blue;
}
```

如您所见，名称空间被附加到属性中。与上面的例子不同，我们不需要在正确的位置显示& for 边框。

## 结论

既然你知道什么是嵌套，请负责任地使用它。有时候，在构建一个项目时，我们可能会忽略我们正在构建的范围。当用 Sass 嵌套时，很容易产生可读性的噩梦。何时以及如何使用嵌套由你决定，如果你决定深入 8 层，那就去做吧。如果一个项目有重大调整，就要做好通宵工作的准备。

## 分享这篇文章