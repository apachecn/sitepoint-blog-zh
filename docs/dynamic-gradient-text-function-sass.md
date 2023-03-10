# Sass 中的动态渐变文本函数

> 原文：<https://www.sitepoint.com/dynamic-gradient-text-function-sass/>

在这篇文章中，一个普通的文本颜色函数将被扩展到渐变背景。该效果是通过首先从值列表创建渐变背景来实现的。然后，该函数将相应的颜色值附加到一个新列表中，以创建文本渐变。

**一点背景…**

根据背景改变文本颜色的能力是一个常见的要求，一种方法是测试背景的亮度，然后相应地修改文本。有一个流行的、记录良好的函数就是用来做这件事的…

```
@function set-notification-text-color($color) {
 @if (lightness($color) > 50) {
 @return #000000; // Lighter background, return dark color
  } 
 @else {
 @return #ffffff; // Darker background, return light color
  }
}
```

上面的函数是这样应用的:

```
.notification-confirm {
 background: $notification-confirm;
 color: set-notification-text-color($notification-confirm);
}
```

## 步骤 1–背景渐变混合

在我们查看文本渐变功能之前，我们需要创建一个列表来保存背景渐变的值，并创建混合渐变

```
$list: $orange, $white, $orange, $white;
```

上面的列表和一个方向被用作背景混合的参数。使用列表有两个主要优点，首先，它有一个开放的参数数量，允许任何颜色和/或百分比的组合。其次，对这个函数来说很重要的是，我们可以遍历列表，为文本渐变创建相同数量的新值。关于 mixin 参数以及何时使用它们的更多信息可以在本帖的[中找到。](https://www.sitepoint.com/sass-multiple-arguments-lists-or-arglist/)

渐变混合看起来像这样:

```
@mixin gradient($direction, $list) {
 background: -webkit-linear-gradient($direction, $list);
}
```

它是这样应用的:

```
.text {
 @include gradient(left, $list);
}
```

## 步骤 2–文本渐变功能

在基于 webkit/blink 的浏览器中，可以使用文本剪辑属性和文本填充颜色显示文本渐变，如下所示:

```
.text {
 background: -webkit-linear-gradient(left, #000, #fff); // We will need to generate gradient colors to complement the background.
 -webkit-background-clip: text;
 -webkit-text-fill-color: transparent;
}
```

首先，我们需要创建一个与参数具有相同$list 值的函数。该函数将创建一个新的空列表来存储我们的新值，然后返回它们:

```
@function text-color($list){
 $text-list:();
 @return $text-list;
}
```

然后我们遍历$list 中的每个值:

```
@function text-color($list){
 $text-list:();
 @each $color in $list {
    // Do something...
  }
 @return $text-list;
}
```

在我们的 for each 循环中，我们可以运行与本文开头提到的纯色函数相同的检查:

```
@function text-color($list){
 $text-list:();
 @each $color in $list {
 @if lightness($color) > 50% {
      // Create a dark text color
    } 
 @else {
      // Create a light text color
    }
  }
 @return $text-list;
}
```

要创建深色或浅色的文本颜色，我们需要将该值添加到$text-list 中。这是使用 append 函数完成的:

```
$text-list:append($text-list, $black, comma);  // Adds $black to $text-list
$text-list:append($text-list, $white, comma); // Adds $white to $text-list
```

因此，最终功能是:

```
@function text-color($list){
 $text-list:();
 @each $color in $list {
 @if lightness($color) > 50% {
 $text-list:append($text-list, $black, comma);
    } 
 @else {
 $text-list:append($text-list, $white, comma);
    }
  }
 @return $text-list;
}
```

该函数应用于文本元素上的$list 渐变参数:

```
.text-block {
 @include gradient(left, $list);
  p {
 @include gradient(left, text-color($list));
 -webkit-background-clip: text;
 -webkit-text-fill-color: transparent;
  }
}
```

## Sass 颜色函数:

这个函数在大多数情况下都可以，但是有一点限制，因为文本只能是黑色或白色。通过使用一些内置的 Sass 颜色操作，该函数可以基于输入动态地生成文本颜色，从而消除了对 if 语句的需要。这方面的几个例子是:

```
@function text-color($list){
 $text-list:();
 @each $color in $list {
 $text-list:append($text-list, invert($color), comma);
  }
 @return $text-list;
}

@function text-color($list){
 $text-list:();
 @each $color in $list {
 $text-list:append($text-list, complement($color), comma);
  }
 @return $text-list;
}

@function text-color($list){
 $text-list:();
 @each $color in $list {
 $text-list:append($text-list, adjust-hue($color, 40deg), comma);
  }
 @return $text-list;
}
```

## 更进一步说:

正如我前面提到的，使用列表作为渐变参数的一个关键原因是它能够作为多维列表使用。下面的例子增加了百分比，给渐变更多的控制…

```
$list: ($orange 0%),
       ($orange 50%),
       ($white 50%),
       ($white 100%);
```

这个百分比值也需要添加到函数中:

```
@function text-color($list){
 $text-list:();
 @each $color, $percentage in $list {
 $text-list:append($text-list, invert($color), $percentage, comma);
  }
 @return $text-list;
}
```

使用 CSS 关键帧，我们可以制作渐变动画，为进度条或加载动画创造一些有趣的效果。在这个代码笔中可以找到一些讨论过的效果的演示。

## 局限性和倒退

正如我之前提到的，文本渐变只在 blink/webkit 浏览器中起作用，但是我们可以确保它们在其他浏览器中优雅地退化。为此，只需将 webkit 前缀用于该技术的渐变、填充颜色和背景剪辑属性。你也可以添加一个备用颜色和背景到混合…

```
@mixin gradient($direction, $list) {
 background: $white; // Fallback solid background color for unsupported browsers
 background: -webkit-linear-gradient($direction, $list);
}

p {
 color: $orange; // Fallback solid text color for unsupported browsers
 @include gradient(left, text-color($list));
 -webkit-background-clip: text;
 -webkit-text-fill-color: transparent;
}
```

## 分享这篇文章