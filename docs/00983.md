# 如何用 Bootstrap 构建反应型量表

> 原文：<https://www.sitepoint.com/build-responsive-type-scale-bootstrap/>

在本教程中，我们将深入了解 Bootstrap 如何处理排版，以及我们如何以几种不同的方式修改代码来创建一个响应式字体缩放。这通常被称为“响应排版”，其目的是让你的排版在所有屏幕尺寸上都可读，避免手机上的大标题！

## 默认情况下 Bootstrap 如何设置版式

为了理解引导排版的工作方式，我们需要开始查看源文件`SCSS`来探究设置和默认设置。

*注意:为了在整个教程中保持清晰，我已经注释掉了引导代码中与排版*无关*的样式。*

### `html`元素

让我们先来看看样式的根元素，在 [`_reboot.scss`](https://github.com/twbs/bootstrap/blob/v4-dev/scss/_reboot.scss#L27) 的第 27 行找到:

```
html {
  font-family: sans-serif;
  line-height: 1.15;
  -webkit-text-size-adjust: 100%;
  -ms-text-size-adjust: 100%;
  // -ms-overflow-style: scrollbar;
  // -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
} 
```

从`html`元素来看，在建立*类型的规模*方面没有太多要报告的。然而，值得注意的是`-*-text-size-adjust: 100%;`声明。这些被用来防止一些移动浏览器自动增加字体大小。

### `body`元素

下面是`body`元素的样式，如第 57 行的 [`_reboot.scc`中所示:](https://github.com/twbs/bootstrap/blob/v4-dev/scss/_reboot.scss#L57)

```
body {
  // margin: 0; // 1
  font-family: $font-family-base;
  font-size: $font-size-base;
  font-weight: $font-weight-base;
  line-height: $line-height-base;
  color: $body-color;
  text-align: left; // 3
  // background-color: $body-bg; // 2
} 
```

现在我们可以开始看到一些印刷样式被应用。在类型尺度上，我们只需要关注`font-size`。默认情况下，这是通过`_variables.scss`中的`$font-size-base`变量设置的，等于`1rem`。

### `p`元素

在第 109 行的 [`_reboot.scss`中可以找到`p`元素的这些样式:](https://github.com/twbs/bootstrap/blob/v4-dev/scss/_reboot.scss#L109)

```
p {
  margin-top: 0;
  margin-bottom: $paragraph-margin-bottom;
} 
```

这里没有太多要报告的:正如您所料，`p`标记只是从主体继承了它的`font-size`和`line-height`。

### `h1`到`h6`元素

这些风格出现在 [`_type.scss`的第 16 行到第 21 行](https://github.com/twbs/bootstrap/blob/v4-dev/scss/_type.scss#L16):

```
h1, .h1 { font-size: $h1-font-size; }
h2, .h2 { font-size: $h2-font-size; }
h3, .h3 { font-size: $h3-font-size; }
h4, .h4 { font-size: $h4-font-size; }
h5, .h5 { font-size: $h5-font-size; }
h6, .h6 { font-size: $h6-font-size; } 
```

您可以看到元素和实用程序类通过一个变量被赋予了`font-size`。相应的变量可以在第 246 到 251 行的`_variables.scss`中找到。查看这些变量，我们可以看到适用于所有浏览器和视口宽度的默认大小:

```
$h1-font-size: $font-size-base * 2.5 !default;
$h2-font-size: $font-size-base * 2 !default;
$h3-font-size: $font-size-base * 1.75 !default;
$h4-font-size: $font-size-base * 1.5 !default;
$h5-font-size: $font-size-base * 1.25 !default;
$h6-font-size: $font-size-base !default; 
```

查看上面的类型标度，可以清楚地看到，`.25rem`的增加被使用，直到`h1`被给予`.5rem`的增加。

如果您使用 Sass 编译器，这些大小可以在自定义变量文件中被覆盖，但是它仍然为所有浏览器和视窗宽度的每个标题留下一个`font-size`。

### 从`.display-1`到`.display-4`实用程序类

从第 29 行到第 48 行的 [`_type.scss`中有以下代码:](https://github.com/twbs/bootstrap/blob/v4-dev/scss/_type.scss#L29)

```
// Type display classes
.display-1 {
  font-size: $display1-size;
  // font-weight: $display1-weight;
  // line-height: $display-line-height;
}
.display-2 {
  font-size: $display2-size;
  // font-weight: $display2-weight;
  // line-height: $display-line-height;
}
.display-3 {
  font-size: $display3-size;
  // font-weight: $display3-weight;
  // line-height: $display-line-height;
}
.display-4 {
  font-size: $display4-size;
  // font-weight: $display4-weight;
  // line-height: $display-line-height;
} 
```

与标题元素一样，显示实用程序类大小被定义为第 259 到 262 行的`_variables.scss`文件中的变量。同样，如果您正在使用 Sass 编译器，您可以在自定义变量文件中覆盖这些。

以上介绍了 Bootstrap 的设置，现在我们可以看看如何制作快速可调的响应式电子秤。

## 创建反应型量表

我所说的*反应型量表*值得进一步阐述。默认情况下，使用`_variables.scss`中的变量和`_type.scss`中的规则显式设置标题及其`display-*`类的引导程序`font-size`。

为所有屏幕和视窗尺寸的标题直接设置一个`font-size`会很快导致标题过大，从而影响用户体验。

当然，您可以创建一些媒体查询，当它适合下拉看起来过大的字体大小时，但是在这一点上，您会失去任何形式的字体缩放层次结构。我们 *[需要类型层次](https://www.sitepoint.com/focus-on-typography-part-2-hierarchy/)* 来跟随文档的流程。

接下来是*响应式量表*和一个将它实现到一个引导项目中的漂亮时髦的方法！如果你不使用 Sass 或 SCSS，你可以简单地更新我用于示例的笔，并提取编译后的 CSS。

### Bootstrap 的反应型量表综述

我们将建立三个主要的东西:

*   用于快速更改和实验的字体比例图
*   检查秤是否有效的功能
*   两个 mixins 允许我们在任何给定时间调整字体大小的灵活性。

值得注意的是，如果你的设计不包括一个适用于公倍数的类型标度，那么使用`mixin`将不起作用，你需要查看笔中编译过的`CSS`来获得更新字体大小所需的代码。

### 反应型比例尺地图

根据在[type-scale.com](https://type-scale.com)上找到的模型，创建预定义印刷比例`$type-scales`的[萨斯地图](https://www.sitepoint.com/using-sass-maps/)。地图中的比例可以传递给 [Sass mixin](https://www.sitepoint.com/sass-basics-the-mixin-directive/) ，它通过使用来自`key: value`对的`key`来创建字体大小。

在标度映射之后，定义了两个变量:`$heading-type-scale-base`和`$display-type-scale-base`。这些变量保存从零宽度视口或浏览器及以上使用的初始比例。这些变量接受来自`$type-scales`地图的一个关键字，或者可以传递一个无单位值:

```
$type-scales : (
  minor-second: 1.067,
  major-second: 1.125,
  minor-third: 1.200,
  major-third: 1.250,
  perfect-fourth: 1.333,
  augmented-fourth: 1.414,
  perfect-fifth: 1.500,
  golden-ratio: 1.618
);

$heading-type-scale-base : minor-third;
$display-type-scale-base : minor-third; 
```

### 如何检查响应式刻度值

重要的是，不要仅限于地图中的值，因为这可能不适合您的设计。

因此，下面的函数将检查传递给 mixin 的值是否是在`$type-scales`映射中设置的值之一，或者它必须是一个无单位值以创建类型刻度:

```
@function check-type-scale-value($scale) {

  // Check $scale against the values in $type-scales.
  @if map-has-key($type-scales, $scale) {

    // If the value of $scale is defined in
    // $type-scales, return the value of $scale.
    @return map-get($type-scales, $scale);

  // If the value of $scale is not defined in the
  // $type-scales map, check if the value is a number
  // and that the number is a unitless value.
  } @else if type-of($scale) == number and unitless($scale) {

    // If the value of $scale is a unitless number,
    // return the number.
    @return $scale;

  // Lastly, should the value passed to $scale be neither
  // found in the $type-scales map nor a unitless number,
  // throw a Sass error to explain the issue.
  } @else {

    // Throw a Sass error if the $scale value is
    // neither found in the $type-scales map nor
    // a unitless number.
    @error "Sorry, `#{$scale}` is not a unitless number value or a pre-defined key in the $type-scales map.";
  }

} 
```

接下来，我们将建立创建初始字体大小的混合。

### 创建标题和显示字体大小

当 Bootstrap 4 的 alpha 版本可用时，我第一次编写了`CSS`来实现响应式规模。但是自从库的稳定版本发布以来，我已经修改了一些东西来使用默认的`SCSS`设置，这使得在引导项目中包含代码变得很方便。

第一个`mixin`用于创建从`h6`到`h1`的标题字体大小:

```
@mixin create-heading-type-scale($scale) {

    // Check the $scale value and store in a variable to be
    // used when calculating the font sizes.
    $the-heading-type-scale: check-type-scale-value($scale);

    // Starting from h6, multiply each previous value by the scale
    // to get the next font size
    $font-size-h6 : $font-size-base;
    $font-size-h5 : $font-size-h6 * $the-heading-type-scale;
    $font-size-h4 : $font-size-h5 * $the-heading-type-scale;
    $font-size-h3 : $font-size-h4 * $the-heading-type-scale;
    $font-size-h2 : $font-size-h3 * $the-heading-type-scale;
    $font-size-h1 : $font-size-h2 * $the-heading-type-scale;
    // $font-size-display-base is made global to allow for accessing the
    // varibale in the next mixin.
    $font-size-display-base : $font-size-h1 !global;

    // Add the created font sizes to the elements and classes
    h1, .h1 { font-size: $font-size-h1; }
    h2, .h2 { font-size: $font-size-h2; }
    h3, .h3 { font-size: $font-size-h3; }
    h4, .h4 { font-size: $font-size-h4; }
    h5, .h5 { font-size: $font-size-h5; }
    h6, .h6 { font-size: $font-size-h6; }
} 
```

上面，字体大小首先被创建并存储在从`$base-font-size`开始的变量中，然后将每个先前的值乘以字体比例值。同样的原理适用于下文，但我们从`$font-size-display-base`开始:

```
@mixin create-display-type-scale($scale) {

    // Store default type scale in a variable for calculations
    $the-display-type-scale: check-type-scale-value($scale);

    // Create variables to reference the previous font size
    $font-size-display-4 : $font-size-display-base + $font-size-base;
    $font-size-display-3 : $font-size-display-4 * $the-display-type-scale;
    $font-size-display-2 : $font-size-display-3 * $the-display-type-scale;
    $font-size-display-1 : $font-size-display-2 * $the-display-type-scale;

    // Add the created font sizes to the elements and classes
    .display-4 { font-size: $font-size-display-4; }
    .display-3 { font-size: $font-size-display-3; }
    .display-2 { font-size: $font-size-display-2; }
    .display-1 { font-size: $font-size-display-1; }
} 
```

### 放下根`font-size`

我喜欢把手机的词根从`font-size`下降到`14px`，然后一路上升到`16px`，然后是`18px`。下面是使用断点大小`md`和`lg`来完成的`SCSS`:

```
html {
    font-size: 14px;
    @media (min-width: 768px) {
        font-size: 16px;
    }
    @media (min-width: 992px) {
        font-size: 18px;
    }
} 
```

## 包装它

一旦所有这些工作都完成了，这是所有关于包括您的混音和选择您想要的规模！

```
// Create the heading font sizes
@include create-heading-type-scale($heading-type-scale-base);

// Create the display font sizes
@include create-display-type-scale($display-type-scale-base);

// At the Bootstrap md breakpoint, adjust the heading font sizes.
@media (min-width: 768px) {
    @include create-heading-type-scale(minor-third);
} 
```

如您所见，您可以在任何断点处包含 mixin，并完全改变您的类型范围。每个字体大小仍然可以被覆盖，如果必要的话，一切看起来不错。

## 自举反应型量表在行动中

您可以在这支笔中看到自举响应式电子秤:

参见 [CodePen](https://codepen.io) 上 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )的笔 [Bootstrap 4 响应式量表](https://codepen.io/SitePoint/pen/GQEyvQ/)。