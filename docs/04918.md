# 你可能没有尝试过的 4 个 Sass 特性

> 原文：<https://www.sitepoint.com/sass-features-may-never-tried/>

一旦你把工作流程转换成 Sass，就很容易陷入熟悉的模式。您可能会对 Sass 通过一些简单的技术所能实现的强大功能感到非常满意，以至于您从来不去探索一些更高级的特性。使用像 Sass 这样强大的预处理器有很多优点，值得探索一些不太熟悉的使用方法。

## 1.CSS 和行内注释

当设计一个页面的样式时，你可能需要在你的 CSS 中包含注释，以便让后面的人明白你的意图。根据你的 CSS 有多复杂或具体，在代码中留下注释可以对下一个进入代码并需要做出改变的人有所帮助——而那个人很可能就是你，六个月后，当你忘记了你写的那个漂亮的代码应该如何工作的时候！

Sass 允许您包含常规的 CSS 注释，开头是`/*`，结尾是`*/`。像这样的注释可以跨越多行，并在最终生成的 CSS 中保持不变。所以对于以下评论 Sass:

```
a {
 color: #ccc;
  &.halloween { 
    /* This is for seasonal links */
 color: #ff8c00;
  }
}
```

生成的 CSS 将如下所示:

```
a {
 color: #ccc;
}

a.halloween {
  /* This is for seasonal links */
 color: #ff8c00;
}
```

Sass 还允许您包含特定于 Sass 编写方式的行内注释(或 Sass 注释)。与 CSS 注释不同，Sass 注释不会在编译后的 CSS 中打印出来。Sass 注释只有一行，以一个`//`开头，以区别于要编译的 Sass 代码。

所以一个注释的 Sass 文件是这样的:

```
a {
 color: #ccc;
  &.halloween { 
  // Chosen colour below after trying #ff8d00, #ff8b00 & ff8a00.
  /* This is for seasonal links */
 color: #ff8c00;
  }
}
```

仍然会生成干净的 CSS，没有关于颜色历史的注释:

```
a {
 color: #ccc;
}

a.halloween {
  /* This is for seasonal links */
 color: #ff8c00;
}
```

## 2.局部和全局变量

管理范围可能是任何语言中最棘手的问题之一，Sass 也不例外。Sass 允许您定义全局和局部变量，并根据需要覆盖它们。

Sass 全局变量的一个常见用途是创建颜色名称，以及这些颜色的语义用法。例如，在主 Sass 文件的顶部，您可以将`$color_success`定义为用于成功操作的强调色，因此它可以被依赖于它的任意数量的选择器继承和修改。

```
//defining a global success color
$color_success: #090;

.state_success {
 color: lighten($color_success, 50%);
 background-color: $color_success;
}
```

这将生成如下 CSS:

```
.state_success {
 color: #9f9;
 background-color: #090;
}
```

`$color_success`变量是一个全局变量，因为它是在任何选择器之外定义的，它将影响嵌套在其自身范围之下的任何东西。但是它不是一个常量，所以你可以在任何时候改变它的值，新的值将开始用于你在那之后定义的任何选择器。例如，如果 Sass 文件中的下一个选择器如下所示:

```
.state_success_particular {
  //overriding the global success color locally
 $color_success: #900;
 color: lighten($color_success, 50%);
 background-color: $color_success;
}
```

您生成的 CSS 中的下一个选择器将反映`$color_success`的新值:

```
.state_success_particular {
 color: #f99;
 background-color: #900;
}
```

在这种情况下，您只在选择器的局部范围内更改了全局变量的值。在该作用域关闭后编写的任何其他选择器都将使用原始的全局值。相反，如果您在选择器之外更改了变量，如下所示:

```
//Overriding earlier globals globally (a bad idea)
$color_success: #900;

.state_success_particular {
 color: lighten($color_success, 50%);
 background-color: $color_success;
}
```

您可能已经为 CSS 中的每个后续选择器更改了全局变量的值。这可能是个坏主意，因为同一个变量对于多个选择器来说意味着不同的东西，仅仅是因为它们被处理的顺序。请记住这一点，否则您可能会发现自己期望在一个选择器中对全局变量进行的更改会应用到该选择器之外。

## 3.扩展占位符

保持源代码的整洁对开发来说是一大福音。Sass 允许您嵌套选择器以帮助消除重复代码，但代价是每一级嵌套都会在最终的 CSS 中产生更多的选择器。[定义一个`@mixin`](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#mixins) 对于将预定义的样式子集`@include`到多个选择器中来说也是很棒的，但是这会给最终的 CSS 带来冗余和重量。虽然这两种熟悉的技术都很有用，但是如果你想保持你的 CSS 文件小而有效，就需要小心处理它们。

幸运的是，Sass 还允许开发人员扩展现有的选择器，通过一个简单的`@extend`命令将所有的样式定义从一个选择器应用到另一个选择器。例如，下面的 Sass 允许多个选择器利用`.prominent`选择器中的相同样式:

```
.prominent {
 font-style: bold;
 font-size: 1.5rem;
}

.important {
 color: #900;
 @extend .prominent;
}

.notice {
 color: #090;
 @extend .prominent;
}
```

要生成这个 CSS:

```
.prominent, .important, .notice {
 font-style: bold;
 font-size: 1.5rem;
}

.important {
 color: #900;
}

.notice {
 color: #090;
}
```

但是，如果您想定义一些有用的样式定义，并且不想在 CSS 中为每个样式定义创建单独的公共选择器，Sass 允许您使用占位符选择器。它们的定义就像类选择器一样，但是它们在选择器的开头使用了百分比符号(`%`)而不是句点(`.`)。因此，在 Sass 中使用占位符:

```
%prominent {
 font-style: bold;
 font-size: 1.5rem;
}

%subtle {
 font-style: regular;
 font-size: 0.75rem;
}

.important {
 color: #900;
 @extend %prominent;
}

.notice {
 color: #090;
 @extend %prominent;
}
```

生成以下 CSS，任何地方都没有对`%prominent`或`%subtle`的可见引用，除非在继承的样式中:

```
.important, .notice {
 font-style: bold;
 font-size: 1.5rem;
}

.important {
 color: #900;
}

.notice {
 color: #090;
}
```

事实上，在这种情况下，我们从未使用过`%subtle`占位符样式，它对我们生成的 CSS 中的大小或选择器没有影响。

当使用全局变量扩展样式和占位符样式时，有一点需要注意，那就是它们通过向基本样式添加选择器来施展魔法，而不是复制 CSS 代码。这意味着定义原始源样式时有效的变量值将继续保留，即使新样式在本地重新定义了变量。例如，以下带有全局变量的 Sass:

```
$color_highlight: #090;

%prominent {
 color: $color_highlight;
 font-style: bold;
 font-size: 1.5rem;
}

.important {
 @extend %prominent;
}

.notice {
 $color_highlight: #900;
 @extend %prominent;
}
```

忽略`.notice`中的覆盖，在生成 CSS 时仅使用原始全局值:

```
.important, .notice {
 color: #090;
 font-style: bold;
 font-size: 1.5rem;
}
```

## 4.父选择器的&符号

开发人员了解的 Sass 的一个基本特性是&符号(`&`)。当您在嵌套的 Sass 选择器中的参数前添加一个&符号时，该选择器将附加到父选择器，而不是嵌套在父选择器之下。这对于需要与选择器相关联的伪类选择器非常有用，比如`:hover`或`::after`。例如，这个 Sass:

```
.hoverable {
 color: #fff;
 &:hover {
 color: #ff0;
  }
}
```

产生以下 CSS 选择器:

```
.hoverable {
 color: #fff;
}

.hoverable:hover {
 color: #ff0;
}
```

“和”符号最酷的地方在于，它们不一定要出现在嵌套样式定义的开头。无论您在 Sass 选择器定义中放入一个&符号，它都被解释为当前定义的样式的父作用域。有时，您需要定义一个样式，该样式采用现有样式的上下文，但仅适用于特殊情况。例如，如果当父类是`.special`时，我们需要对`.hoverable`元素进行不同的边界处理，该怎么办:

```
.hoverable {
 color: #fff;
 &:hover {
 color: #ff0;
  }
}

.special .hoverable {
 border: 1px solid #f00;
}
```

这样做需要我们跳出我们的`.hoverable`选择器，然后在新的选择器中重新定义它。如果有更多的嵌套层次，或者需要设置更多的上下文，这可能是一个相当复杂的操作。

但是有了&符号，Sass 允许我们在不离开`.hoverable`选择器范围的情况下做同样的事情:

```
.hoverable {
 color: #fff;
 &:hover {
 color: #ff0;
  }
  .special & {
 border: 1px solid #f00;
  }
}
```

这两个 Sass 片段都将产生以下 CSS:

```
.hoverable {
 color: #fff;
}

.hoverable:hover {
 color: #ff0;
}

.special .hoverable {
 border: 1px solid #f00;
}
```

你看到有符号的那一行做了什么吗？Sass 用父选择器替换了&符号，在`.special`选择器中定义了一个新的选择器。

在开发下一套 Sass 风格时，考虑一下这些想法会有什么用处。因为这些都是 Sass 核心的一部分，所以您可以立即开始在您的项目中使用它们，并利用这个机会进行更干净、更易维护和更高效的开发。

## 分享这篇文章