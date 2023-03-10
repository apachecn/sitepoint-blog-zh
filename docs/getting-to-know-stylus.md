# 了解手写笔

> 原文：<https://www.sitepoint.com/getting-to-know-stylus/>

如果你是前端场景的一部分，你可能听说过 [Stylus](https://learnboost.github.io/stylus/) ，来自 Sass 的远房表亲，没有人非常了解。和 Sass 一样，Stylus 是用 Node.js 编写的 CSS 预处理器，根据它的 [GitHub 库](https://github.com/stylus/stylus)，它把自己描述为:

> [……]一种革命性的新语言，提供了一种高效、动态和富于表现力的生成 CSS 的方法。

好吧，*革命性*可能有点夸张。但其他都是真的。

## 什么，又一个！？

算是吧。但是手写笔并不是全新的。它从 2011 年初就存在了，但我认为它有一个相当独立的社区。顺便问一下，你知道最新的 [Mozilla 开发者网络](https://developer.mozilla.org/en-US/)重新设计是用手写笔做的吗？[参与该项目的大卫·沃什](http://davidwalsh.name/)，也[写了如何开始使用手写笔](http://blog.teamtreehouse.com/getting-started-stylus)。

那么手写笔相对于 Sass 有什么优势呢？嗯，它内置在 Node.js 中，对我来说这听起来是一个有利的方面。尽管由于 [Node-Sass](https://github.com/sass/node-sass) 对 [LibSass](https://github.com/sass/libsass) 的包装，在节点工作流中使用 Sass 是非常好的，但它并没有让 LibSass 在节点中编写。

此外，Stylus 有一个非常宽松的语法，这可能是一件好事，也可能是一件坏事，这取决于你的项目、你的团队和你坚持严格编码准则的倾向。我认为，只要样式表中不涉及太多逻辑，并且在提交之前先处理代码，许可语法应该没问题。

总而言之，Stylus 和 Sass 都支持几乎相同的东西；你可以看一下[触控笔功能的完整列表](https://github.com/stylus/stylus#features)，但不要期待任何突破性的东西(尽管有一些简洁的功能)。Stylus 还支持多种语法，尽管界限比 Sass 模糊得多:您可以按照自己的意愿编写样式(缩进、CSS 样式)，并且可以在同一个样式表中混合和匹配(编写解析器一定很有趣)。

你觉得怎么样？想试试吗？

## 入门指南

如前所述，Stylus 是用 Node.js 编写的，所以我们可以像安装任何其他 npm 包一样安装它:

```
$ npm install stylus -g
```

从那里，您可以使用 [JavaScript API](https://github.com/stylus/stylus/blob/master/docs/js.md) 将其插入到您的节点工作流中，或者您可以使用[命令行可执行文件](https://github.com/stylus/stylus/blob/master/docs/executable.md)来编译您的样式表。为了简单起见，我们将使用`stylus`命令行工具，但是可以从节点脚本中随意处理它， [Gulp](https://github.com/stevelacy/gulp-stylus) 或 [Grunt](https://github.com/gruntjs/grunt-contrib-stylus) 。

```
stylus ./stylesheets/ --out ./public/css
```

前面的命令告诉`stylus`从`stylesheets`文件夹中编译所有手写笔样式表(`.styl`，并在`public/css`文件夹中生成它们。当然，您也可以观察目录的变化:

```
stylus --watch ./stylesheets/ --out ./public/css
```

## 书写手写笔样式

如果你刚刚开始，不想被一个新的语法弄得不知所措，你可以在一个`.styl`文件中编写普通的 CSS。由于 Stylus 确实支持标准的 CSS 语法，所以从 CSS 代码开始只是为了慢慢增强它是完全可以的。

### 基本语法

关于语法本身，几乎所有东西都是可选的。牙套:何苦呢？分号:加油！也扔掉它们。括号:请。以下是完全有效的手写笔代码:

```
.foo
.bar
  color tomato
  background deepskyblue
```

起初有点令人不安，但我们可以习惯它，尤其是当有语法高亮可用时。正如您可能猜到的，前面的代码编译成:

```
.foo, .bar {
  color: tomato;
  background: deepskyblue;
}
```

### 变量

CSS 预处理程序最常用的特性是定义变量的能力。毫不奇怪，Stylus 也提供了这种功能。虽然与 Sass 相反，但它们是用等号(`=`)而不是冒号(`:`)来声明的。此外，前导美元符号(`$`)是可选的，可以安全地省略。

```
// Defining a `text-font-stack` variable
text-font-stack = 'Helvetica', 'Arial', sans-serif;

// Using it as part of the `font` property
body
  font 125% / 1.5 text-font-stack
```

现在有了 Stylus 所做的 Sass 或任何其他预处理器所不做的事情:属性值查找。假设您想要应用宽度一半的负左边距；在 Sass 中，您必须将宽度存储在一个变量中，而不是在手写笔中:

```
.foo
  width 400px
  position absolute
  left 50%
  margin-left (@width / 2)
```

通过使用`@width`，我们告诉 Stylus 获取当前块的`width`属性的值，将其视为一个变量。相当整洁！另一个有趣的用例是根据属性是否已经定义，有条件地输出属性:

```
.foo
  // ... other styles
 z-index: 1 unless @z-index
```

在这种情况下，`z-index`将被设置为 1，除非`.foo`已经为`z-index`属性分配了一个值。再加上 mixins，你就真的有东西了。

### 混合蛋白

说到这里，让我们定义一个 mixin，因为它可能是 Sass 最流行的特性之一！手写笔中的 mixin 不需要特定的关键字；只要名称末尾有括号(空或空),它就是 mixin。

```
size(width, height = width)
  width width
  height height
```

同样，包含一个 mixin 不需要像`@include`或`+`这样的特定语法:

```
.foo
  size(100px)
```

如果你愿意，你甚至可以去掉括号，这样看起来你使用了一个完全标准的 CSS 属性。这种机制被称为*透明混合*，因为它们的内含物是不可见的。

```
.foo
  size 100px
```

乍一看，这似乎是一个不必要的技巧，但是仔细想想，这个特性实际上允许作者扩展默认的 CSS 语法。考虑以下`overflow` mixin:

```
overflow(value)
  if value == ellipsis
    white-space nowrap
    overflow hidden
    text-overflow ellipsis
  else
 overflow: value
```

如果给定的值是`ellipsis`，它将打印一行省略号溢出所需的众所周知的声明三元组。否则，它打印给定值。下面是你如何使用它:

```
.foo
  overflow ellipsis
```

它会产生:

```
.foo {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
```

你得承认，这是个很酷的把戏。虽然这可能令人困惑(也可能很危险)，但是能够用额外的值扩展标准 CSS 属性实际上是一个有趣的概念。

如果你想以一种`@content`的方式传递一些内容给 mixin，通过一个`{block}`变量是可能的。在包含过程中，你只需要在 mixin 名称前面加上`+`就可以传递额外的内容。

```
has-js()
  html.js &
    {block}

.foo
 +has-js()
    color red
```

该代码将编译为:

```
html.js .foo {
  color: #f00;
}
```

Stylus mixins 最后一个非常有趣的特性:它们总是有一个`arguments`局部变量，包含所有传递给 mixin 的参数(如果有的话)。这个变量可以作为一个数组来操作和处理，例如使用 JavaScript 中的`[..]`来获取特定索引处的值。

## 最后的想法

浏览 Stylus 的所有特性和语法技巧太长了，我认为我们已经有了一个不错的介绍，至少足够开始了！

正如你所看到的，手写笔是非常宽容的。在所有现有的帮助编写 CSS 的工具中，Stylus 无疑是使 CSS 最接近真正的编程语言的工具。

注意，手写笔也有自己的框架，就像萨斯有[指南针](http://compass-style.org/)一样，它被称为[笔尖](http://tj.github.io/nib/)。Nib 是一个提供额外帮助的工具箱，跨浏览器支持混合输入笔。

有些人可能喜欢，有些人可能不喜欢。我的建议是语法要非常严谨。处理这样一种宽容的语法可能并不总是一件乐事。无论如何，很高兴看到一些体面的赞同萨斯。

## 分享这篇文章