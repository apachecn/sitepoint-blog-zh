# 使用 PostCSS 启用即将推出的 CSS 功能

> 原文：<https://www.sitepoint.com/enabling-upcoming-css-features-with-postcss/>

从上一篇文章“改进选择器和媒体查询的 PostCSS 指南”中我们停下来的地方继续，我们将看看更多的 PostCSS 插件，它们扩展了 CSS 的视野。前一篇文章关注于通过扩展选择器和媒体查询来改进样式表的结构。这一个将集中于实现来自即将到来的规范的新属性和值。本文中介绍的插件实现了不同的功能，可以根据您的需要有效地一起或单独使用。

我们从我个人最喜欢的开始。

## 将重置提升到一个新的水平

CSS3 引入了两个不错的特性: [`initial`](https://developer.mozilla.org/en-US/docs/Web/CSS/initial) 值和 [`all`](https://developer.mozilla.org/en/docs/Web/CSS/all) 属性。与值`inherit`和`unset`一起使用的`initial`值允许您将属性重置为其原始值。`all`属性作为一个简写属性，将每个属性重置为这三种状态之一。虽然每一个单独使用都很有趣，但当一起使用时，它们允许您快速重置特定元素的所有样式，并防止它从页面的父元素继承样式。编写模块化 CSS 的又一步！

可悲的是，IE 仍然不支持这两个功能。但是，你可能已经猜到了，有一个插件可以做到这一点。

[Postcss-initial](https://github.com/maximkoretskiy/postcss-initial) 增加了对`initial`值和`all: initial`组合的支持。它是这样工作的:

```
.article {
  font-size: initial;
  color: initial;
  padding: initial;
  margin: initial;
} 
```

被编译成:

```
.article {
  font-size: medium;
  font-size: initial;
  color: #000;
  color: initial;
  padding: 0;
  padding: initial;
  margin: 0;
  margin: initial;
} 
```

默认情况下，对于原生支持该特性的浏览器，它保留带有`initial`的原始属性。

反过来，`all`属性将被转换成一长串重置属性。

```
.container {
  all: initial;
} 
```

被传输到:

```
.container {
  animation: none 0s ease 0s 1 normal none running;
  backface-visibility: visible;
  background: transparent none repeat 0 0 / auto auto padding-box border-box scroll;
  border: medium none currentColor;
  border-collapse: separate;
  border-image: none;
  border-radius: 0;
  border-spacing: 0;
  bottom: auto;
  box-shadow: none;
  box-sizing: content-box;
  caption-side: top;
  clear: none;
  clip: auto;
  color: #000;
  columns: auto;
  column-count: auto;
  column-fill: balance;
  column-gap: normal;
  column-rule: medium none currentColor;
  column-span: 1;
  column-width: auto;
  content: normal;
  counter-increment: none;
  counter-reset: none;
  cursor: auto;
  direction: ltr;
  display: inline;
  empty-cells: show;
  float: none;
  font-family: serif;
  font-size: medium;
  font-style: normal;
  font-variant: normal;
  font-weight: normal;
  font-stretch: normal;
  line-height: normal;
  height: auto;
  hyphens: none;
  left: auto;
  letter-spacing: normal;
  list-style: disc outside none;
  margin: 0;
  max-height: none;
  max-width: none;
  min-height: 0;
  min-width: 0;
  opacity: 1;
  orphans: 2;
  outline: medium none invert;
  overflow: visible;
  overflow-x: visible;
  overflow-y: visible;
  padding: 0;
  page-break-after: auto;
  page-break-before: auto;
  page-break-inside: auto;
  perspective: none;
  perspective-origin: 50% 50%;
  position: static;
  right: auto;
  tab-size: 8;
  table-layout: auto;
  text-align: left;
  text-align-last: auto;
  text-decoration: none;
  text-indent: 0;
  text-shadow: none;
  text-transform: none;
  top: auto;
  transform: none;
  transform-origin: 50% 50% 0;
  transform-style: flat;
  transition: none 0s ease 0s;
  unicode-bidi: normal;
  vertical-align: baseline;
  visibility: visible;
  white-space: normal;
  widows: 2;
  width: auto;
  word-spacing: normal;
  z-index: auto;
  all: initial;
} 
```

如果你使用 BEM 或 Suit，这个插件可以很好地配合 [postcss-autoreset](https://github.com/maximkoretskiy/postcss-autoreset) 自动重置块和组件级元素的样式。

## 自定义属性

在处理布局时，我们经常需要在样式表中共享一些值。例如，您的品牌颜色可能用作按钮的背景、链接的文本颜色或文本块的边框。目前要做到这一点，我们需要在每一个使用颜色的地方重复多次。这种重复使得在应用程序中改变颜色时保持调色板一致变得很乏味。

Less 和 Sass 等 CSS 预处理器使用变量解决了这个问题。幸运的是，W3C 正在研究一个类似的概念，叫做[自定义属性](https://www.w3.org/TR/css-variables/)。虽然处理的是同一个问题，但它们的工作方式与预处理器中的变量不同。Less 和 Sass 变量在编译时被解析。当您将 Less 或 Sass 编译为 CSS 时，编译器会寻找对应于当前编译范围的变量声明，并用相应的值替换每个实例。这意味着，变量的解析值只取决于它在代码中的使用位置。反过来，自定义属性是为 DOM 中的元素定义的，并且只能由它们的子元素访问。这意味着变量的值取决于元素在 DOM 中的位置，只能在运行时解析。

此时，你应该皱眉或扬起眉毛。如果一个变量的值只有在运行时才知道，那么 PostCSS 插件如何解析它呢？事实是，它不能。但是，它确实提供了一种使用该功能子集的方法。如果我们在`:root`元素中定义了所有的自定义属性，那么页面上的所有元素都可以访问这些属性。这意味着，我们可以在编译时解决它们。

下面是一个简单的例子:

```
:root {
  --text-color: red;
  --background: blue;
}

h1 {
  color: var(--text-color);
  font-size: var(--font-size, 20px);
}
button {
  background-color: var(--background);
} 
```

将被编译成:

```
h1 {
  color: red;
  font-size: 20px;
}
button {
  background-color: blue;
} 
```

注意，`--font-size`变量没有被定义，所以它被替换为一个回退值`20px`。这里重要的部分是将所有的自定义属性保存在`:root`中。在别处定义的那些将被忽略，因为插件不能充分处理它们。您可以以此为起点，并在更多的浏览器开始支持它时扩展您的使用。Chrome [已经从 49](https://www.chromestatus.com/feature/6401356696911872) 开始支持它们了。

## 逻辑属性

如果你曾经开发过一个跨越不同书写方向的文化的国际网站，你应该知道在维护几个版本的界面时会涉及到什么，比如从左到右或者从右到左。为了满足这种需求，W3C 引入了一个新概念[逻辑属性](https://drafts.csswg.org/css-logical-props/)。这是一种将我们从物理方向(如左或右)的思维中抽象出来的方式，而是一种逻辑方向——开始和结束。该规范仍然是一项进行中的工作，但是你已经可以使用 [postcss-logical-props](https://github.com/bmds/postcss-logical-props) 插件来尝试这些东西了。

它支持使用某些逻辑属性生成从左到右和从右到左的网站版本，例如`border-block-start`和`border-block-end`、`offset-block-start`和`offset-block-end`。这些属性被编译成它们的左或右替换项。您可以指示插件编译 LTR 和 RTL 版本的样式表，然后在用户更改语言时在应用程序中切换它们。

例如，如果您有以下 CSS:

```
.text {
  border-block-start: 1px solid blue;
  text-align: start;
  padding-block-end: 10px;
  margin-block-start: 20px;
} 
```

用选项`{ dir: 'LTR' }`调用插件会产生以下结果:

```
.text {
  border-left: 1px solid blue;
  text-align: left;
  padding-right: 10px;
  margin-left: 20px;
} 
```

而使用`{ dir: 'RTL' }`会给你一个镜像:

```
.text {
  border-right: 1px solid blue;
  text-align: right;
  padding-left: 10px;
  margin-right: 20px;
} 
```

## 新的颜色特性

PostCSS 提供了一整套插件，这些插件提供了处理颜色的新特性。

### 颜色反应

[postcss-color-function](https://github.com/postcss/postcss-color-function) 插件实现了新的 [`color()`](https://drafts.csswg.org/css-color/#modifying-colors) 函数。此功能允许您使用一个或多个“颜色调节器”功能来修改基色。每个颜色调节器都可以以特定的方式处理颜色。

这里有几个如何使用它的例子:

```
body {
  color: color(red green(50));
  color: color(red blue(50) a(50%));
  color: color(red tint(50%));
  color: color(red shade(50%));
} 
```

将被编译成以下颜色

```
body {
  color: rgb(255, 50, 0);
  color: rgba(255, 0, 50, 0.5);
  color: rgb(255, 128, 128);
  color: rgb(128, 0, 0);
} 
```

颜色调节器的完整列表可在规格中找到[。这个插件和自定义属性一起使用会非常有效。您可以定义一组基本颜色，并基于它们计算其他颜色。这样，如果你决定改变你的基色，调色板的其他部分也会相应地更新。](https://drafts.csswg.org/css-color/#funcdef-color-mod)

### HWB 颜色符号

HWB 代表色调-白色-黑色，这是定义颜色的另一种方法。它使用从 0 到 360 的色调值来描述颜色，然后添加从 0%到 100%的白度和黑度。这种符号类似于 HSL，比 RGB 更容易理解。 [postcss-color-hwb](https://github.com/postcss/postcss-color-hwb) 插件实现了新的 [`hwb()`](https://drafts.csswg.org/css-color/#the-hwb-notation) 函数，用于定义 hwb 颜色。举几个例子:

```
body {
  color: hwb(0, 0%, 0%);
  color: hwb(120, 40%, 20%);
  color: hwb(240, 0%, 100%, 0.5);
} 
```

将生成以下颜色:

```
body {
  color: rgb(255, 0, 0);
  color: rgb(102, 204, 102);
  color: rgba(0, 0, 0, 0.5);
} 
```

### `Gray()`功能

CSS 颜色模块还引入了一个方便的 [`gray()`](https://drafts.csswg.org/css-color-4/#grays) 函数。它可用于生成灰色，而无需指定任何冗余信息，例如 RGB 颜色中的所有三个通道。 [postcss-color-gray](https://github.com/postcss/postcss-color-gray) 插件为该函数实现了一个聚合填充，使用起来非常简单:

```
body {
  color: gray(0);
  color: gray(25);
  color: gray(50%);
  color: gray(128, 20%);
} 
```

上面的代码会产生不同的灰度:

```
body {
  color: rgb(0, 0, 0);
  color: rgb(25, 25, 25);
  color: rgb(127, 127, 127);
  color: rgba(128, 128, 128, 0.2);
} 
```

## 把一切都包起来

这绝不是一个完整的 CSS 插件列表，而是一些更有趣的选择。你可以在 [postcss.parts](http://postcss.parts/) 上探索更多。

CSS 正在蓬勃发展，PostCSS 也随之蓬勃发展。是的，我们都在急切地等待原生浏览器对这些新特性的支持，但是 PostCSS 给了我们一个很有希望的机会来尽早采用和评估这些特性。这里的一般建议是，尝试从熟悉的预处理程序用法后退一步，从一个全新的角度来编写样式表。试着列出能让你更有效率的可用功能，并在你的工作流程中使用它们。你可能很快就会意识到，这些正是你一直缺少的功能。

## 分享这篇文章