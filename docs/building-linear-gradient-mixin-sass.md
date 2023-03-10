# 在 Sass 中构建线性渐变混合

> 原文：<https://www.sitepoint.com/building-linear-gradient-mixin-sass/>

首先:您可能不应该使用预处理器(这里是 Sass)来为 CSS 属性添加供应商前缀。这不是你想在你的代码库中加入的东西。这通常应该是您希望用类似于 [Autoprefixer](https://github.com/postcss/autoprefixer) 的东西来处理的那种事情。

Autoprefixer 不仅在打印准确的前缀方面比你做得好得多(我指的是你，编写`-ms-linear-gradient`和`-moz-border-radius`的开发人员)，而且更新、维护和扩展也很容易。一切都是从配置文件中完成的，而不是从样式表中。

总之，免责声明我看完了。在本文中，我将向您展示如何编写一个 mixin 来打印线性渐变以及后备颜色和 WebKit 的旧版本(仍然使用旧的角度符号)。虽然您可能不一定需要它，但我相信您可以从这段代码中学到很多东西。

*注:请让我以这篇文章为契机，提醒你[线性渐变只需要 WebKit 前缀](http://lea.verou.me/2013/04/can-we-get-rid-of-gradient-prefixes/)，其他都不需要。请停止打印样式表中过时和缺少前缀的渐变。:)*

## 构建混音

构建一个 mixin 来从参数中创建一个渐变，转换一个角度，给线性渐变属性加上前缀，所有其他的事情听起来都像是一个繁重的任务，但是你会发现，只要你努力让事情变得简单，这实际上是很容易实现的。

我突然想到，写渐变前缀的人经常试图解析和控制一切。这当然不是一个好主意。这不仅非常繁琐，而且最终会产生大量完全没有必要的复杂代码。

让我们回顾一下我们需要做的事情:

*   如果不支持 CSS 渐变，输出第一种颜色作为后备。
*   将角度转换为旧语法时，输出`-webkit-`前缀值。
*   输出给定的标准声明。

那么我们的 mixin 需要两样东西:方向和任何数量的额外参数，它们将按照传递的方式打印出来。

```
/// Mixin printing a linear-gradient
/// as well as a plain color fallback
/// and the `-webkit-` prefixed declaration
/// @access public
/// @param {Keyword | Angle} $direction - Linear gradient direction
/// @param {Arglist} $color-stops - List of color-stops composing the gradient
@mixin linear-gradient($direction, $color-stops...) {
 background: nth(nth($color-stops, 1), 1);
 background: -webkit-linear-gradient(legacy-direction($direction), $color-stops);
 background: linear-gradient($direction, $color-stops);
}
```

现在不要太担心`legacy-direction`函数和回退。我们稍后将回到这些问题。现在，方向在线性梯度符号中是可选的。例如，下面的声明完全有效:

```
.selector {
  background-image: linear-gradient(hotpink, tomato);
}
```

这将使用`180deg`作为方向的默认值，产生一个垂直渐变(对于旧的 WebKit 是水平的，由于语法的变化)。为了让我们的 mixin 完全符合 CSS 的常用符号，我们需要允许这样做。让我们看看如果我们使用 mixin 重写前面的例子会发生什么:

```
.selector {
 @include linear-gradient(hotpink, tomato);
}
```

在这种情况下，`$direction`是`hotpink`，`$color-stops`是单值的`arglist`:`tomato`。因此，如果不是有效的方向(角度或方向关键字)，我们需要 mixin 为`$direction`定义一个默认值。在这种情况下，当前的颜色`$direction`需要添加到`$color-stops`列表中。

这听起来可能有点复杂，但请相信我，使用下面的代码片段，一切都会很清楚:

```
@mixin linear-gradient($direction, $color-stops...) {
  // Direction has been omitted and happens to be a color-stop
 @if is-direction($direction) == false {
 $color-stops: $direction, $color-stops;
 $direction: 180deg;
  }

 background: nth(nth($color-stops, 1), 1);
 background: -webkit-linear-gradient(legacy-direction($direction), $color-stops);
 background: linear-gradient($direction, $color-stops);
}
```

让我们回顾一下:如果`$direction`参数碰巧不是一个方向(我们稍后将检查`is-direction`函数)，我们将它添加到`$color-stops`列表中(因为它是一个颜色停止符)，并且我们将`$direction`重新定义为默认值`180deg`。很简单，对吧？

我们就要完成了，让我解释一下回退是如何工作的，因为它看起来可能很奇怪。我们希望在不支持线性渐变的情况下，`$color-stops`列表的第一个颜色值作为后备颜色。

有人可能认为`nth($color-stops, 1)`已经足够了，但是如果第一个值恰好是一个包含一种颜色和一个色标(例如`hotpink 42%`)的 2 项长列表呢？因此，我们确保总是使用`nth(nth($color-stops, 1), 1)`来获取颜色值，也称为`$color-stops`的第一个值。

## 测试一个值是否是方向

根据 CSS 规范，mixin 的第一行测试`$direction`参数是否是有效的方向。这个没有内置函数，所以需要做一个。这本身并不十分困难，但事实证明在 CSS 中有相当多的方法来定义方向。如果我们坚持线性梯度，这里有不同的选择:

*   [`to top`、`to top right`(或`to right top`)、`to right`、`to bottom right`(或`to right bottom`)、`to bottom`、`to bottom left`(或`to left bottom`)、`to left`、`to left top`(或`to top left`)中的一个关键字](http://dev.w3.org/csswg/css-images-3/#typedef-side-or-corner)值；
*   [在`deg`、`rad`、`grad`或`turn`中的一个角度](http://dev.w3.org/csswg/css-values-3/#angle-value)。

因此，检查一个值是否是一个方向就像测试它是否是一个有效的关键字，或者是一个具有有效单位的数字一样简单。

```
/// Test if `$value` is a valid direction
/// @param {*} $value - Value to test
/// @return {Bool}
@function is-direction($value) {
 $is-keyword: index((to top, to top right, to right top, to right, to bottom right, to right bottom, to bottom, to bottom left, to left bottom, to left, to left top, to top left), $value);
 $is-angle: type-of($value) == 'number' and index('deg' 'grad' 'turn' 'rad', unit($value));

 @return $is-keyword or $is-angle;
}
```

## 将角度转换为旧语法

WebKit 版本依赖于旧的语法，其中:

*   `to left`是`right`之类；
*   一个角度是`90deg - <standard></standard>`(见 [Lea Verou 关于这个](http://lea.verou.me/2012/07/important-prefix-free-update/)的文章)。

所以我们需要我们的`legacy-direction`函数来:

*   如果输入是关键字，则返回准确的关键字；
*   如果输入是角度，则返回准确的角度；
*   如果输入既不是关键字也不是角度，则抛出错误。

```
/// Convert a direction to legacy syntax
/// @param {Keyword | Angle} $value - Value to convert
/// @require {function} is-direction
/// @require {function} convert-angle
/// @throw Cannot convert `#{$value}` to legacy syntax because it doesn't seem to be a direction.;
@function legacy-direction($value) {
 @if is-direction($value) == false {
 @error "Cannot convert `#{$value}` to legacy syntax because it doesn't seem to be a direction.";
  }

 $conversion-map: (
    to top          : bottom,
    to top right    : bottom left,
    to right top    : left bottom,
    to right        : left,
    to bottom right : top left,
    to right bottom : left top,
    to bottom       : top,
    to bottom left  : top right,
    to left bottom  : right top,
    to left         : right,
    to left top     : right bottom,
    to top left     : bottom right
  );

 @if map-has-key($conversion-map, $value) {
 @return map-get($conversion-map, $value);
  }

 @return 90deg - $value;
}
```

*注意:截至发稿时，LibSass 3 还不支持`@error`。如果你想增加对 LibSass 的支持，可以用`@warn`替换`@error`。我目前在[维护不同版本 Sass (Ruby 和 LibSass)](https://gist.github.com/HugoGiraudel/48393b942f7b3a65eab4)* 之间的兼容性表

## 使用它

好吧！我们完成了代码。剩下的就是使用我们的 mixin 来确保它的工作。

```
.selector-1 {
 @include linear-gradient(#31B7D7, #EDAC7D);
}

.selector-2 {
 @include linear-gradient(to right, #E47D7D 0%, #C195D3 50%, #4FB4E8 100%);
}

.selector-3 {
 @include linear-gradient(42deg, #B58234 0%, #D2B545 50%, #D7C04D 50.01%, #FFFFFF 100%);
}
```

```
.selector-1 {
  background: #31B7D7;
  background: -webkit-linear-gradient(-90deg, #31B7D7, #EDAC7D);
  background: linear-gradient(180deg, #31B7D7, #EDAC7D);
}

.selector-2 {
  background: #E47D7D;
  background: -webkit-linear-gradient(left, #E47D7D 0%, #C195D3 50%, #4FB4E8 100%);
  background: linear-gradient(to right, #E47D7D 0%, #C195D3 50%, #4FB4E8 100%);
}

.selector-3 {
  background: #B58234;
  background: -webkit-linear-gradient(48deg, #B58234 0%, #D2B545 50%, #D7C04D 50.01%, #FFFFFF 100%);
  background: linear-gradient(42deg, #B58234 0%, #D2B545 50%, #D7C04D 50.01%, #FFFFFF 100%);
}
```

## 最后的想法

就这样了，伙计们。如您所见，构建起来并不困难，也不涉及一些奇怪的解析和检测。你可以[在 SassMeister](http://sassmeister.com/gist/b58f6e2cc3160007c880) 上玩代码。

## 分享这篇文章