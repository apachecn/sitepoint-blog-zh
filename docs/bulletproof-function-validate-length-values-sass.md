# 在 Sass 中验证长度值的防弹函数

> 原文：<https://www.sitepoint.com/bulletproof-function-validate-length-values-sass/>

一旦你开始熟悉 Sass，使用一些函数并编写自己的 mixins，很快你就会想要验证开发人员的输入。这意味着对函数和 mixin 增加一些额外的检查，以确保给定的参数在函数或 mixin 中的使用方式是正确的。

在看过十几个不同的旨在验证长度的函数后，我想我应该试一试，并在上面写点东西。所以我们在这里。

我们先回忆一下根据官方 CSS 规范什么是长度[:](https://www.w3.org/TR/css3-values/#lengths)

> 长度指的是距离测量[…]长度是一种尺寸。然而，对于零长度，单元标识符是可选的(即，可以在语法上表示为“0”)。尺寸是一个数字，后面紧跟着一个单位标识符。

如果我们总结一下刚才读到的，长度就是一个数字后面跟着一个单位，除非它是零。这有点简单，但我们将从这一点开始。

## 设置功能

如果一个函数返回一个[布尔值](https://www.sitepoint.com/boolean-data-type/)，用一个前导`is-`来命名这个函数通常是一个好习惯。根据问题的本质*是【这个】【那个】？*，我们暗示答案要么是*是* ( `true`)要么是*否* ( `false`)。

在我们的例子中，我们希望确保给定的值是一个长度，所以我建议使用`is-length`，非常简单。

```
/**
 * Check whether `$value` is a valid length.
 * @param {*} $value - Value to validate.
 * @return {Bool}
 */
@function is-length($value) {
  // Do some magic and return a Boolean.
}
```

## 返回一个有效值

由于我们的复习，我们知道长度是一个有单位的数。让我们从那个开始。

```
@function is-length($value) {
 @return type-of($value) == "number" and not unitless($value);
}
```

在继续之前，让我提醒您一些事情:

*   我们返回一个布尔值，所以没有必要用不必要的`@if`、`@else if`和`@else`指令污染函数的核心。
*   我们给字符串加引号(使用双引号)是因为这样更优雅，而且因为未加引号的字符串严格来说等于相同的加引号的字符串。

好吧，这是个好的开始。不幸的是，大多数进行单元验证的 Sass 框架将止步于此。有了这么好的开始，我们不能停下来！此外，我们未能正确实现 CSS 规范，即如果值为零，则单位是可选的。如果`$value`是`0`，我们的函数将返回`false`，因为它是无单位的。让我们修理它！

```
@function is-length($value) {
 @return $value == 0 or type-of($value) == "number" and not unitless($value);
}
```

在大多数语言中，`or` ( `||`)操作符是这样实现的:一直解析条件，直到一个令牌的值为`true`。也就是说如果`$value`是`0`，函数停止解析，直接返回`true`，否则继续。

注意，我们也可以这样写我们的条件(为了可读性，包括了括号):

```
@function is-length($value) {
 @return type-of($value) == "number" and (not unitless($value) or $value == 0);
}
```

如果`$value`是`0`或者`$value`是一个带有单位的数字，我们的第一个实现读作*。而如果`$value`是一个数字并且或者有一个单位或者是`0`T8，则该实现读作*。你可以挑一个你觉得最舒服的。**

## 允许共同的价值观

您可能知道有几个值适用于所有 CSS 属性:`initial`和`inherit`。我们还可以将`auto`添加到列表中，因为大多数基于长度的属性都接受`auto`作为有效值。所以我们有三个值需要允许，如果算上之前已经添加的`0`，就是四个。让我们从丑陋的方式开始:

```
@function is-length($value) {
 @return $value == 0 
          or $value == "auto" 
          or $value == "initial" 
          or $value == "inherit" 
          or type-of($value) == "number" 
          and not unitless($value);
}
```

哇…真的不优雅，是吗？幸运的是，在 SitePoint 网站上的一篇[文章中，我向您展示了我们如何使用`index`函数来优化这样的语句。](https://www.sitepoint.com/tips-help-level-up-sass/)

`index`函数返回第一个参数(一个列表)中第二个参数(我们的值)的索引。如果在列表中找不到该值，它将返回 Sass 3.4 中的`null`或 Sass 3.3 中的`false`。

```
@function is-length($value) {
 @return index(0 "auto" "inherit" "initial", $value) 
          or type-of($value) == "number" 
          and not unitless($value);
}
```

问题是，我们的`is-length`值现在可以返回一个整数。例如，如果`$value`是`auto`，那么函数返回`2`，因为`auto`是允许值列表中的第二项。虽然如果使用`@if is-length($value)`应该没什么大不了的，但是在某些情况下，您可能希望一直有一个布尔值。

为了确保函数返回一个`bool`类型，您可以使用 *not-not* 技巧(参见上述同一篇文章)将值转换为布尔值。虽然这并不优雅，但它工作得很好:

```
@function is-length($value) {
 @return not not index(0 "auto" "inherit" "initial", $value) 
          or type-of($value) == "number" 
          and not unitless($value);
}
```

如果你想让它更清楚，你可以构建一个小的`contains`函数来隐藏它:

```
/**
 * Check whether `$list` contains `$value`.
 * @param  {List} $list  - List of values.
 * @param  {*}    $value - Value to check in the list.
 * @return {Bool}
 */
@function contains($list, $value) {
 @return not not index($list, $value);
}
```

…然后:

```
@function is-length($value) {
 @return contains(0 "auto" "inherit" "initial", $value) 
          or type-of($value) == "number" 
          and not unitless($value);
}
```

既清晰又雄辩，但需要一个额外的功能。我会让你决定哪个最适合你的需要。

*注意:记住`auto`是`padding`的无效值。很危险的情况，但是你应该记住。*

## 允许`calc()`

我们的功能开始发挥作用了，不是吗？然而，我们仍然需要允许`calc()`，它经常被长度检查器遗忘或忽略。检查`calc()`的问题是它需要 Sass 3.3，因为我们需要 [`str-slice`](http://sass-lang.com/documentation/Sass/Script/Functions.html#str_slice-instance_method) 方法。

这个想法很简单。我们从值中去掉前四个字符。如果结果是“calc”，那么它(可能)意味着该值是一个`calc()`函数，这是有效的:

```
@function is-length($value) {
 @return not not index(0 "auto" "initial" "inherit", $value) 
          or type-of($value) == "number" and not unitless($value) 
          or str-slice($value + "", 1, 4) == "calc";
}
```

这里要注意两件事:

1.  我们使用`$value + ""`将值转换为字符串，以防它是一个数字(例如`42px`)，以避免`str-slice`函数期望一个字符串的错误。
2.  我们把这项检查放在最后，只在需要时才执行，因为它可能比其他两项检查频率更低，成本也更高。

功能目前看起来还不错！

## 扩展到尺寸

在这一点上，我们有一个相当健壮的函数来验证长度。我们可以像这样用它来上浆:

```
/**
 * Set `width` and `height` in a single statement.
 * @param {Number} $width - Width.
 * @param {Number} $height ($width) - Height.
 * @output `width` and `height`.
 * @requires {function} is-length
 */
@mixin size($width, $height: $width) {
 @if is-length($width) {
 width: $width;
  }

 @else {
 @warn "Invalid length `#{$width}` for `$width` parameter in `size` mixin.";
  }

 @if is-length($height) {
 height: $height;
  }

 @else {
 @warn "Invalid length `#{$height}` for `$height` parameter in `size` mixin.";
  }
}
```

如果你试一试，你会发现它非常有效。我应该注意到，如果我们试图使用[固有大小](http://dev.w3.org/csswg/css-sizing/#width-height-keywords)，我们可能会遇到问题。如果您不熟悉这个概念，为了给开发人员提供对元素大小的更多控制，内在大小调整涉及到新的`width`和`height`属性的有效值。

问题是，我们不能更新函数来包含固有值，因为它们对于大多数使用长度的属性(`padding`、`margin`、`background-size`等等)是无效的。然而，我们可以做的是，使用我们已经构建的函数创建另一个函数。那`is-size`呢？

```
/**
 * Check whether `$value` is a valid size.
 * @param {*} $value - Value to validate.
 * @return {Bool}
 * @requires {function} is-length
 */
@function is-size($value) {
 @return is-length($value)
          or not not index("fill" "fit-content" "min-content" "max-content", $value);
}
```

…或者如果您也使用`contains`功能:

```
@function is-size($value) {
 @return is-length($value)
          or contains("fill" "fit-content" "min-content" "max-content", $value);
}
```

现在，我们可以安全地使用我们全新的`is-size`函数的固有大小值，而不用担心错误的返回！很漂亮，对吧？

## 最后的想法

正如我在本文中所展示的，要构建一个防弹函数来验证长度，您必须确保您涵盖了最常见的边缘情况(`calc()`、固有大小等)。

我们仍然可以通过过滤角度(`deg`、`rad`、`grad`、`turn`)和持续时间(`s`、`ms`)来改进该函数，因为它们目前被我们的函数视为长度，因为它们基本上是一个带有单位的数字。但是由于长度、角度和持续时间在非常不同的上下文中使用，所以当函数需要一个长度时，很少有机会传递一个角度。

涵盖边缘情况是好的，但是涵盖一切，包括最不可能的场景，有时是过度的。

你可以在这个 Sassmeister 上找到和[一起玩的完整代码。](http://sassmeister.com/gist/5c4237518e62754e4a0a)

## 分享这篇文章