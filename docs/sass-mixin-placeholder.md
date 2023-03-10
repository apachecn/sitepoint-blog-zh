# 萨斯:Mixin 还是占位符？

> 原文：<https://www.sitepoint.com/sass-mixin-placeholder/>

当我大约一年半前开始玩 Sass 时，我花了一些时间才理解了包含 mixin 的[和扩展占位符的](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#mixins)[的区别。事实上，即使占位符的概念对我来说也是一种巫术。](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#placeholders)

如果你也是类似的情况，不用担心，因为我会努力开导路径。今天我们将学习 mixin 的确切用途，以及何时使用 Sass 占位符。你会明白它们都有不同的用途，不应该混淆。

注意:虽然我要谈论的是 Sass，但是这篇文章也可以适用于任何其他 CSS 预处理器，无论是 Stylus、LESS 还是您碰巧使用的任何东西。这些技术通常都在做同样的事情，所以请根据您选择的工具随意修改本文的内容。

首先，当我们提到 **Sass 占位符和 mixins** 时，我们可能应该补上我们正在谈论的内容，所以现在就开始吧。

## 混合起来

mixin 是一个指令，允许您根据几个参数定义多个规则。可以把它想象成一个转储 CSS 内容而不是返回值的函数。以下是来自 [Sass 参考](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#mixin-content)的定义:

> Mixins 允许您定义可以在整个样式表中重用的样式，而不需要求助于非语义类，如。向左浮动。Mixins 还可以包含完整的 CSS 规则，以及 Sass 文档中其他地方允许的任何内容。他们甚至可以接受参数，这样你就可以用很少的混音产生各种各样的风格。

现在我们已经讨论了术语，假设您发现了几个在样式表中重复多次的声明。你知道代码重复是不好的，你也熟悉 DRY ( [不要重复自己](http://en.wikipedia.org/wiki/Don't_repeat_yourself))的概念。要纠正这一点，您可以为这些重复的声明编写一个 mixin:

```
@mixin center() {
  display: block;
  margin-left: auto;
  margin-right: auto;
}

.container {
  @include center();
  /* Other styles here... */
}

/* Other styles... */

.image-cover {
  @include center;
}
```

*注意:如果没有向 mixin 传递参数，可以省略括号。事实上，你甚至可以在`@mixin`定义中省略它们。*

有了这种新创建的 mixin 方式，你不必在每次需要将一个元素居中时重复这三行；你只需包括混音。很方便，不是吗！

有时，您希望一个 mixin 构建一些属性的“简写”。比如`width`和`height`。反复写两行不累吗？尤其是当两者具有相同的值时？让我们用 mixin 来处理这个问题吧！

```
@mixin size($width, $height: $width) {
  width: $width;
  height: $height;
}
```

很简单，不是吗？请注意，我们是如何通过将参数`$height`默认为与 mixin 签名中的`$width`具有相同的值来使其成为可选参数的。现在，每当您需要定义元素的尺寸时，您只需这样做:

```
.icon {
  @include size(32px);
}

.cover {
  @include size(100%, 10em);
}
```

*注意:另一个语法-糖混合的好例子是[，这个](http://hugogiraudel.com/2013/08/05/offsets-sass-mixin/)是我做的，以避免每次我想使用不同于`static`的位置系统时写`top`、`right`、`bottom`、`left`和`position`。*

## 了解你的占位符

占位符有点奇怪。它们是编译 SCSS 时不输出的类。那么你可能会想*“这有什么意义？”*。实际上，如果没有`@extend`指令，这一点将是微不足道的。但首先要做的是。这是你写占位符的方法:

```
%center {
  display: block;
  margin-left: auto;
  margin-right: auto;
}
```

编辑注意:就像占位符一样，除非被引用，否则 mixin 同样是无用的，所以这一节并不一定说它们在这方面是不同的，但这只是澄清了一点，即使它看起来类似于 CSS 声明块，它自己也不会被编译。

基本上你写它就像写一个类一样，除了你用符号`%`代替点作为前缀。此外，它遵循与类相同的命名规则。

现在，如果您尝试编译您的 SCSS，您将不会在生成的文件中看到这个代码示例。正如我所说的:**占位符不会编译到源代码**中。

所以目前来看，这个占位符是绝对没用的。除非看`@extend`面，否则不能有任何用处。指令的目的是从 CSS 选择器/ SCSS 占位符继承属性。以下是您使用它的方法:

```
.container {
  @extend %center;
}
```

通过这样做，Sass 将获得`%center`占位符的内容，并将其应用到`.container`(即使实际上并不是这样做的——但现在这并不重要)。正如我所说的，你也可以像这样扩展现有的 CSS 选择器(除了 SCSS 占位符之外):

```
.table-zebra {
  @extend .table;

  tr:nth-of-type(even) {
    background: rgba(0,0,0,.5);
  }
}
```

这是`@extend`指令的一个非常常见的用例。在这种情况下，我们要求`.table-zebra`类的行为与`.table`类完全一样，然后我们添加特定于 zebra 的规则。当您在模块化组件中开发站点/应用程序时，扩展选择器非常方便。

## 用哪个？

所以问题仍然存在:*你应该使用哪一个？*嗯，至于我们这个领域的一切:**看情况**。这取决于背景和你最终想做什么。

最好的建议是:如果你需要变量，使用 mixin。否则，扩展一个占位符。这有两个原因:

*   首先，你不能在占位符中使用变量。实际上你可以，但是你不能将变量传递给你的占位符，所以你不能像 mixin 一样生成上下文相关的 CSS。
*   第二，Sass 处理 mixins 的方式使它们在不与上下文变量一起使用时非常不方便。简而言之:Sass 会在每次使用 mixin 时复制它的输出，不仅会产生重复的 CSS，还会产生更大的样式表。

考虑本文中的第一个例子:

```
@mixin center {
  display: block;
  margin-left: auto;
  margin-right: auto;
}

.container {
  @include center;
}

.image-cover {
  @include center;
}
```

这将产生以下 CSS 输出:

```
.container {
  display: block;
  margin-left: auto;
  margin-right: auto;
}

.image-cover {
  display: block;
  margin-left: auto;
  margin-right: auto;
}
```

注意到重复的 CSS 了吗？如果只是重复三行代码，不会造成太大的伤害，但是如果 mixins 转储了几十行 CSS 代码，并且在一个项目中多次使用，那么这三行代码很容易变成 300 行。如果我们修改我们的例子，使用一个占位符呢？

```
%center {
  display: block;
  margin-left: auto;
  margin-right: auto;
}

.container {
  @extend %center;
}

.image-cover {
  @extend %center;
}
```

这是生成的 CSS:

```
.container, .image-cover {
  display: block;
  margin-left: auto;
  margin-right: auto;
}
```

好多了！编译利用了[选择器分组](https://reference.sitepoint.com/css/selectorgrouping)，没有任何重复的样式。因此，每当您希望避免反复编写相同的属性，知道它们永远不会改变时，扩展占位符是一个好主意。这将导致编译后的样式表更加精简。

另一方面，如果你想在不同的地方写相同的属性，但是用不同的值(大小，颜色，等等。)一个 mixin 是最好的办法。如果您有一组固定值和变量值，您应该尝试两者的组合。

```
%center {
  margin-left: auto;
  margin-right: auto;
  display: block;
}

@mixin skin($color, $size) {
  @extend %center;
  background: $color;
  height: $size;
}

a { @include skin(pink, 10em) }
b { @include skin(blue, 90px) }
```

在这种情况下，mixin 扩展了固定值的占位符，而不是将它们直接转储到它的主体中。这会生成干净的 CSS:

```
a, b {
  margin-left: auto;
  margin-right: auto;
  display: block;
}

a {
  background: pink;
  height: 10em;
}

b {
  background: blue;
  height: 90px;
}
```

## 结论

所以你有它。我希望我不仅清楚了混合和占位符的用途，还清楚了何时应该使用它们，以及它们会对编译后的 CSS 产生什么影响。

如果你对 CSS 预处理程序中的这些特性有任何补充，请在评论中分享。

本文由 Pierre Choffé在[La Cascade](http://la-cascade.ghost.io/sass-mixin-ou-placeholder/)翻译成法文

## 分享这篇文章