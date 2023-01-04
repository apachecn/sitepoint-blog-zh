# 为什么我不再使用指南针

> 原文：<https://www.sitepoint.com/dont-use-compass-anymore/>

指南针是一个地狱般的项目。这是目前为止最大的 Sass 框架，因为它是由两个 Sass 核心设计师之一的[克里斯·埃普斯坦](https://twitter.com/chriseppstein)本人维护的，我认为它不会很快失去人气。

然而，大约一年前，我完全停止使用指南针。我把它从我们的工作项目中删除了。我从我自己的网站上删除了它。我把它从我的大部分兼职项目中删除了。发生了什么事？我如何从“指南针是最好的”变成“我不再需要它了”？

我认为我对 Sass 越来越好，越来越有信心，以至于 Compass 不再给我带来太多好处。至少，这还不足以让它包含在项目中，特别是考虑到它降低了 Ruby Sass 的速度，而 Ruby Sass 本身已经很慢了…

在继续深入之前，让我先澄清一件事:我并不是说指南针毫无用处。我也不是说你应该扔掉指南针。我只想向你展示其他选项和其他做事方式，不需要 Compass 成为项目的一部分。

现在，为了充分理解为什么我们能够或不能消除对指南针的依赖，我们需要理解指南针在一天结束时做什么。根据文件，Compass 提供:

*   [基于 CanIUse 数据库的供应商前缀](http://compass-style.org/reference/compass/helpers/cross-browser/)(从 1.0.0 版开始)；
*   [数学助手](http://compass-style.org/reference/compass/helpers/math/)像`cos`、`sin`、`pi`之类的；
*   [颜色助手](http://compass-style.org/reference/compass/helpers/colors/)如`shade`、`tint`等；
*   [形象助手](http://compass-style.org/reference/compass/helpers/image-dimensions/)像`image-width`和`image-height`；
*   一个精灵建造者。

其他的都是附属品。

## Autoprefixer 也是如此

在 Compass 1.0.0 之前，前缀是在 Compass 中手动管理的。这意味着每当一个新的前缀出现或消失时，必须向 Compass 提交一个 pull 请求，以便从相关的 mixin 中删除该前缀。[不理想](https://github.com/Compass/compass/search?q=prefix&type=Issues&utf8=%E2%9C%93) …这就是为什么 Chris 在 1.0.0 版中采用了更智能的方式，直接从[can use](http://caniuse.com)中提取数据。

与此同时，非常流行的 [Autoprefixer](https://github.com/postcss/autoprefixer) 库也做了同样的事情。因此，在这一点上，Compass 和 Autoprefixer 都是 100%最新的供应商前缀，但是仍然有一个主要的区别:您仍然必须使用 Compass mixins 来为您的属性和值添加前缀，而 Autoprefixer 是作为您的部署过程的一部分来做这件事的，对您的样式表进行后处理。

为了获得以下 CSS 输出:

```
.unicorn {
-webkit-transform: translateX(42em);
-ms-transform: translateX(42em);
transform: translateX(42em);
}
```

你需要在指南针上写下这个:

```
.unicorn {
@include transform(translateX(42em));
}
```

这是插着 Autoprefixer 的:

```
.unicorn {
transform: translateX(42em);
}
```

不仅后者更简单、更简短，而且在假设的美好未来，我们不再需要属性的前缀，我们也不需要回到代码中来摆脱 mixin 调用。一切都很好。

## 你不需要每天都学数学

我喜欢 Sass 为我们提供了在样式表中执行数学运算的能力。当我们想到 CSS 预处理程序时，我们经常提到变量和混合，但忽略了数学支持。对于像 CSS 这样基于数字的语言来说，这是一件大事。

除此之外，Compass 还提供了一些高级数学助手，比如`cos`、`sin`、`tan`、`sqrt`、`pow`、`pi`，或许还有用处不大的`acos`、`asin`、`atan`、`logarithm`和`e`。在某些情况下，我不得不承认使用这些功能可能是有用的。例如，
[在上一篇文章](https://www.sitepoint.com/ultimate-long-shadow-sass-mixin/)中，我使用了`cos`和`sin`来构建完美的长影混合。[不久前](https://www.sitepoint.com/building-step-wizard-bem-sass/)，我记得需要`sqrt`来正确对齐旋转的伪元素，作为步进向导的一部分。所以有用例。

然而，它们是如此罕见，以至于我不能认为 Compass 的数学助手是在项目中保留它的有效理由。在这一点上，对我来说，这太轶事了。尤其是因为这些函数中的大部分都可以用 Sass 填充(多亏了基本的数学运算)。例如，下面是一个纯 Sass 版本的`pow`函数:

```
/// Power function
/// @param {Number} $x
/// @param {Number} $n
/// @return {Number}
/// @source https://github.com/adambom/Sass-Math/blob/master/math.scss Sass-Math
@function pow($x, $n) {
$result: 1;

@if $n >= 0 {
@for $i from 1 through $n {
$result: $result * $x;
}
} @else {
@for $i from $n to 0 {
$result: $result / $x;
}
}
@return $result;
}
```

如果你非常需要 Sass 中的高级数学函数，我推荐你看一下 [Sassy-Math](https://github.com/at-import/Sassy-math) ，这是一个轻量级的 Ruby Sass 库，提供了所有的数学功能。如果你想自己填充一个数学函数，看看[这篇文章](https://unindented.org/articles/trigonometry-in-sass/)。此外，Ana Tudor 在 Sass Way 的 Sass 中写过关于[反三角函数的文章。](http://thesassway.com/advanced/inverse-trigonometric-functions-with-sass)

## 您可以聚合填充颜色函数

我必须说我不是指南针颜色功能的专家，因为我从来没有使用过它们。事情是 Sass 已经提供了[这么多处理颜色的原生函数](http://sass-lang.com/documentation/Sass/Script/Functions.html)，我从来不觉得还需要更多。

尽管，这并不完全正确。在 [Sass 指南](http://sass-guidelin.es/#lightening-and-darkening-colors)中，我解释了我如何认为使用 [`mix`](http://sass-lang.com/documentation/Sass/Script/Functions.html#mix-instance_method) 函数通常比 [`darken`](http://sass-lang.com/documentation/Sass/Script/Functions.html#darken-instance_method) 和 [`lighten`](http://sass-lang.com/documentation/Sass/Script/Functions.html#lighten-instance_method) 更合适。为了让使用`mix`稍微容易一点，我写了两个很短的函数，恰好存在于 Compass 中的[:](http://compass-style.org/reference/compass/helpers/colors/)

```
/// Slightly lighten a color
/// @access public
/// @param {Color} $color - color to tint
/// @param {Number} $percentage - percentage of `$color` in returned color
/// @return {Color}
@function tint($color, $percentage) {
@return mix($color, white, $percentage);
}

/// Slightly darken a color
/// @access public
/// @param {Color} $color - color to shade
/// @param {Number} $percentage - percentage of `$color` in returned color
/// @return {Color}
@function shade($color, $percentage) {
@return mix($color, black, $percentage);
}
```

无论如何，我认为这还不足以让指南针只用于此。

## 你可以不需要图像助手

让我们进入正题。Compass 是用 Ruby 编写的，它提供了基于给定路径返回图像尺寸的[图像助手](http://compass-style.org/reference/compass/helpers/image-dimensions/)，比如`image-width`和`image-height`。这有多棒，尤其是图像替换技术？通过这种方式，您可以调整元素的大小以适应图像，而不用担心尺寸会发生变化。

```
.logo {
$logo-path: '/asseimg/logo.png';
width: image-width($logo-path);
height: image-height($logo-path);
// ...
}
```

不幸的是，我们没有办法填充这样的东西。Sass 无法访问文件系统，因此无论如何也不可能计算出映像的大小。

也就是说，我个人倾向于在我的工作中使用越来越少的图像，当使用 SVG 或任何等同物是一种选择时。因为 SVG 代表*可缩放矢量图形*，我认为知道精确尺寸的需求每天都在降低。就我个人而言，我现在已经有一段时间没有错过这个功能了。

## SVG 精灵构建者是军团

好了，现在我们谈了。如果你问我，我会说[精灵生成器](http://compass-style.org/reference/compass/utilities/sprites/)一直是 Compass 的主要功能。正因如此，它被认为是非常好和方便的。用 Compass 从一个图像文件夹构建一个 spritesheet 只需要几分钟，如果不是更少的话。

```
@import "compass/utilities/sprites";
@import "my-icons/*.png";
@include all-my-icons-sprites;
```

好吧。虽然没有严格意义上的图像助手，但我们都知道有无数的 sprite 构建器。以 [Grunticon](https://github.com/filamentgroup/grunticon) 为例，一个运行在 [Grunt](http://gruntjs.com/) 上的 SVG sprite 构建器。它甚至还配备了一个名为 Grumpicon 的基于浏览器的应用程序。这可能是最好的之一，因为它是由[灯丝组](http://www.filamentgroup.com/)发布的，但还有许多其他选择，运行在任何工具上漂浮你的船。

因此，虽然让 Compass 直接从我们的样式表中完成这一点很好，但还有其他选择，而且确实有可能在不使用 Compass 的情况下处理精灵。同样的，我会发现只为精灵构建器使用 Compass 是非常奇怪的，不管它有多好。sprite builder 是一个可以完全脱离样式表本身的工具。没有必要将两者紧密联系在一起，即使这可能会带来一些好处。

## 最后的想法

远离指南针，如果这是你想要的，应该相对简单。首先，确保你没有使用任何来自 Compass 的助手，比如`tint`或`sqrt`。如果你这样做了，在你的函数文件中填充它们(只要谷歌一下，你很快就会找到一个纯 Sass 版本)。然后，去掉所有的 CSS 前缀 mixins，在项目中设置 [Autoprefixer](https://github.com/postcss/autoprefixer) (显然，如果可能的话)。最后，如果你需要的话，使用自定义的精灵生成器。

再说一遍，这并不是说指南针是一个不好的工具或什么的。相反，Compass 是一个非常棒的框架！如果你对指南针满意，请继续使用它。仅仅因为你从某篇文章中读到一个你喜欢的工具就放弃它是没有意义的，所以如果你对它没问题，就坚持使用指南针。

另一方面，如果你想知道是否值得远离指南针，因为你不怎么用它，那么你可能会像我一样考虑摆脱它。

## 分享这篇文章