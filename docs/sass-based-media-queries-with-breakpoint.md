# 具有断点的基于 Sass 的媒体查询

> 原文：<https://www.sitepoint.com/sass-based-media-queries-with-breakpoint/>

这篇文章不会谈论任何旧的 Sass 媒体查询 mixin。不，我们要看看@import(以前的 Team Sass) [断点混合](http://breakpoint-sass.com/)。五月，[雨果·吉劳德](http://hugogiraudel.com/)写了一篇[的帖子，比较了 Sass](https://www.sitepoint.com/managing-responsive-breakpoints-sass/) 处理媒体询问的各种方式。他在那篇文章的结尾提到了断点，今天我们将深入了解如何使用断点插件通过 Sass 管理响应断点。

## 为什么要用插件？

如果你像我一样，你可能已经开始阅读这篇文章，并认为，“我已经有自己的媒体查询 mixin，为什么要使用这个？”几个月前，我也会问同样的问题。我已经建立了自己的 mixin，我真的很舒服，我不认为有必要切换。尽管如此，还是有一些很好的理由去看看和尝试另一个 mixin。

首先，我们都有盲点。虽然我可能对自己写的代码非常满意，但我可能错过了一些改进代码的好方法，或者忽略了一个 bug。接触别人的代码是扩展你的技能和改进你自己的代码的好方法。

第二，依赖别人的代码完全没问题。作为一名“独行侠”程序员没有额外的奖励。当然，您需要评估代码及其作者和维护者的可靠性，但是拥有一个由一组熟练的志愿者维护的有用插件是一种不可思议的奢侈。

第三，熟悉多种工具有助于开发人员变得更加全面。如果您经常“继承”现有代码，这一点尤其正确。无论你喜欢还是讨厌你尝试的新代码，最终都没有关系，尝试它会让你变得更加多才多艺和自信。

## 基本断点

### 安装断点

断点是一个 Compass 扩展，所以在使用它之前你需要运行 Compass。一旦准备好了，你可以在断点网站上找到[安装说明。](http://breakpoint-sass.com/#get_started)

### 设置断点变量

要开始使用断点，您需要为断点设置一系列变量。断点变量可以是简单的测量值(`$bp-large: 1200px`)，也可以是复杂的值列表(`$bp-complex: (min-height 720px) (orientation landscape) (min-resolution: 300dpi);`)。让我们看看这些变量的选项。

#### 一次测量

如果您在变量中设置的都是数字，断点将默认为最小宽度的媒体查询。

```
$bp-one-measurement: 30em;
//output
@media (min-width: 30em) { ... }
```

#### 两次测量

如果变量包含两个度量值，断点将使用较小的一个作为最小宽度，较大的一个作为最大宽度:

```
$bp-two-measurements: 20em 40em;
//output
@media (min-width: 20em) and (max-width: 40em) { ... }
```

#### 特征和测量

如果变量是一个包含字符串和数字的列表，断点将使用字符串作为媒体查询功能名称，数字作为其值。

```
$bp-feature-measurement: max-height 700px;
//output
@media (max-height: 700px) { ... }
```

#### 特征和值

您可能已经明白我们要做什么了:您可以将任何媒体查询特征值对放入一个变量中，并将其传递给断点:

```
$bp-feature-value: orientation portrait;
//output
@media (orientation: portrait) { ... }
```

#### 多个特征对

让我们疯狂吧。您可以将任意多的媒体查询特征值对放在一起，只需将每一对放在括号中。

```
$bp-multiple-feature-pairs: (min-height 720px) (orientation landscape) (min-resolution: 300dpi);
//output
@media (min-height: 720px) and (orientation: landscape) and (min-width: min-resolution 300dpi) { ... }
```

#### 媒体类型

记得媒体类型吗？屏幕、印刷品、电视、投影等？将其中一个标签传递到断点，它将成为输出的一部分:

```
$bp-include-type: tv 30em;
//output
@media tv and (min-width: 30em) { ... }
```

#### 错误处理

假设你在断点变量中放了一些不好的东西。它能让你逃脱多少？断点相当宽松:无论你给它什么，它都返回。(GIGO 吧？)

```
$bp-error: not-a-feature-or-value;
//output
@media (min-width: not-a-feature) { ... }

$bp-error: bogus pair;
//output
@media (pair: bogus) { ... }

$bp-error: (fake list) what;
//output
@media (list: fake) and (min-width: what) { ... }
```

这里可能让您吃惊的是，如果您将无效字符串作为特征值对传递，断点会颠倒它们的顺序。我期望`$bp-error: bogus pair;`产生`@media (bogus: pair) { ... }`。这是因为断点处理双字符串对的逻辑只检查第一个字符串是否是有效的媒体查询值名称。如果不是，断点假定它是值，不检查第二个字符串，并假定第二个字符串是有效的特性名。虽然一个`@warn`在这里会很好，但它基本上是一个有争议的问题，因为在这个场景中没有任何东西是有效的。

### 致电媒体询问

现在，您已经知道可以在断点的媒体查询变量中加入什么样的疯狂元素，让我们看看如何在代码中使用它。当然，要做的第一件事是将断点库放入 Sass。一旦你完成了这些并设置了你的变量，你就可以使用`breakpoint()` mixin 了。

```
body {
 color: black;
 @include breakpoint($bp-one-measurement) {
 color: red;
  }
}
//output
body {
 color: black;
}
@media (min-width: 30em) {
  body {
 color: red;
  }
}
```

我建议在任何受影响的选择器中使用`breakpoint()` mixin:断点将自动在标准 CSS `@media (){}`语法中重新创建选择器。在我看来，在相关选择器中嵌套媒体查询变体使得您的 Sass 更容易维护。

但是，如果您的项目或组织需要单独的媒体查询片段，您仍然可以使用断点:只需调用 mixin，然后将您想要的选择器放入其中:

```
// _global.scss
body {
 color: black;

}
// _mq-30em.scss
@include breakpoint($bp-one-measurement) {
  body {
 color: red;
  }
}

//output = exactly the same
body {
 color: black;
}
@media (min-width: 30em) {
  body {
 color: red;
  }
}
```

## 断点——超越基础

### IE8 的回退

如果您支持 IE8 或更低版本，或其他无 mq 浏览器(不使用 JavaScript polyfill，如 [respond.js](https://github.com/scottjehl/Respond) ，您可以为“后退”状态指定样式。(就个人而言，我很乐意为 IE8 提供一个更宽版本的单列移动优先风格，但你可能需要为这些用户提供一个“全桌面”风格的网站版本。)断点让您可以用`$breakpoint-no-queries`和`$breakpoint-no-query-fallback`变量来做这件事。

```
$breakpoint-no-queries: false;
$breakpoint-no-query-fallbacks: true;
body {
 color: black;
 @include breakpoint(567px, $no-query: '.no-mqs') {
 color: red;
  }
}
//output
body {
 color: black;
}
@media (min-width: 567px) {
  body {
 color: red;
  }
}
.no-mqs body {
 color: red;
}
```

在这个例子中，我们使用了 [Modernizr 的`.no-mqs` body 类](http://modernizr.com/docs/#mq)来定位不支持媒体查询的浏览器，并给它们一个网站的“大”视图，而不打破“移动优先”的层叠顺序。

如果您需要在单独的样式表中打印这些回退样式，[查看断点文档的这一部分](http://breakpoint-sass.com/#no_query_fallback)。

### 媒体查询上下文

你有没有写过一个 mixin，它会根据断点输出不同的风格？如果有，断点会给你一个[帮助函数](http://breakpoint-sass.com/#media_query_context)来找出:`breakpoint-get-context($feature)`。

```
body {
 @include breakpoint($bp-multiple-feature-pairs) {
 orientation: breakpoint-get-context('orientation');
  }
}
//output
@media (min-height: 720px) and (orientation: landscape) and (min-width: min-resolution 300dpi) {
  body {
 orientation: landscape;
  }
}
```

## 结论

断点是使用 Sass 管理媒体查询的强大工具。然而，为了完全公开起见，我不经常使用它。一个原因是:我将断点放在了 Sass 映射中(并且通常只使用最小宽度查询)。然而，对于更复杂的项目(例如，涉及打印、方向或分辨率查询)，断点覆盖的范围远远超过我简单的最小宽度混合，您可以从[断点 github repo wiki](https://github.com/at-import/breakpoint/wiki) 中找到更多信息。

你可以在 SassMeister 上玩这些样本[背后的代码。](http://sassmeister.com/gist/5b58acc3202772e8001c)

## 分享这篇文章