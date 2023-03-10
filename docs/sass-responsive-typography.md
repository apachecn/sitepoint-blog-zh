# 时髦而有反应的印刷术

> 原文：<https://www.sitepoint.com/sass-responsive-typography/>

随着网页字体的增长(在数量、质量和易用性方面)，网页排版已经成为网页设计的一个必要部分。网页排版是一个复杂的话题，包括许多较小的方面，如字体配对、性能、FOUT、连字和字体大小。

当你在建立网站时选择排版时，记住响应式设计是很重要的。在手机上阅读带有巨大桌面大小标题的页面令人沮丧；在宽屏显示器上看到一个网站上所有的字体大小都停留在 14px 到 20px 之间也很无聊。这些问题帮助我用三个部分定义了响应式网页排版:

1.  在断点处改变的基本字体大小
2.  在断点处变化的垂直节奏
3.  在断点处改变的一致类型刻度

这三件事是正常网页排版的重要组成部分:让它们在给定的断点处可调整，让我们能够根据用户的视窗来调整排版。

## 使用 Sass 地图组织数据

如果你以前从未在 Sass 中使用过地图，[这篇文章](https://www.sitepoint.com/using-sass-maps/)是一个很好的介绍。在 Sass 中，地图是数据的数组。您可以嵌套地图以实现详细的多层数据组织。让我们来看看一张地图，我们可以在它的基础上做出反应:

```
$rwd-typography: (
 default: (
 base-font-size: 16px,
 vertical-rhythm: 1.414,
 type-scale: 1.2,
 min-width: null
  ),
 medium: (
 base-font-size: 18px,
 vertical-rhythm: 1.5,
 type-scale: 1.414,
 min-width: 480px
  ),
 large: (
 base-font-size: 20px,
 vertical-rhythm: 1.618,
 type-scale: 1.5,
 min-width: 960px
  ),
 xlarge: (
 base-font-size: 24px,
 vertical-rhythm: 1.618,
 type-scale: 1.618,
 min-width: 1300px
  )
);
```

这张地图上有我们需要的三个排版细节，分布在四个断点上。它还具有在每个断点处使用的媒体查询的最小宽度度量。注意:媒体查询值可以是您的项目需要的任何值。毕竟断点应该由内容决定，而不是由框架默认值决定！

每个嵌套地图中的`base-font-size`值设置正文文本的正常字体大小。`vertical rhythm`值设置正常正文文本的行高，用于为标题和其他非`<p>`元素创建垂直节奏。`type-scale`值决定了文字刻度每增加一级会变大多少。例如，在我们的默认尺寸上，段落文本将是 16px，下一个尺寸(暂且称之为“块引用”)将是 16px × 1.2，或 19.2px。下一个尺寸“子标题”可能是？)会是 19.2px × 1.2，也就是 23px。

既然我已经介绍了段落、子副标题和副标题(大概也包括标题和主角的尺寸)，我们需要生成这些尺寸。让我们将类型标度标签放入一个 Sass 列表中:

```
$rwd-scale-labels: (p, bq, sh, h, hero);
```

现在我们已经存储了所有的数据，让我们写一些 Sass 来自动构建我们的尺寸！

## 使用 Sass 循环和混合生成大小

我们现在可以使用一些循环和混合来生成响应的字体大小。我们首先编写一个循环，遍历每个字体大小标签，并为每个标签生成一个 Sass [占位符选择器](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#placeholders)。

```
@each $label in $rwd-scale-labels {
  %#{$label} {
    // we’ll put more code here soon…
  }
}
```

在每个占位符中，我们将嵌套另一个`@each`语句来遍历断点并生成我们的版式:

```
@each $breakpoint, $data in $rwd-typography {
  // $breakpoint represents the breakpoint’s key,
  // $data is the nested map containing its data
}
```

在这个循环中，我们要做的第一件事是确定我们是否需要媒体查询。默认/第一个断点不需要媒体查询；其他人知道。

```
@if map-get($data, min-width) != null { //CHECK != syntax
 @media screen and (min-width: map-get($data, min-width)) {
    // generate CSS output here
  }
} @else {
  // generate CSS output here
}
```

让我们暂时抛开所有的嵌套代码，编写一些助手函数来生成我们的 CSS 值。我们将编写的第一个函数将获取当前断点处当前标签的字体大小:

```
@function rwd-generate-font-size($label, $breakpoint) {
 $label-position: index($rwd-scale-labels, $label);
 $breakpoint-base-font-size: map-get(map-get($rwd-typography, $breakpoint), base-font-size);
 $breakpoint-type-scale: map-get(map-get($rwd-typography, $breakpoint), type-scale);
 $return: $breakpoint-base-font-size;
 @for $i from 1 to $label-position {
 $return: $return * $breakpoint-type-scale;
  }
 @return $return;
}
```

上面使用了几个高级 Sass 函数:

*   获取项目在列表中的位置
*   `map-get($map, $key)`返回与给定映射中的指定键匹配的值

`@for`循环大致模拟了一个`power()`函数/指数数学。

我们将把它插入到我们的媒体查询检查上方的循环中:

```
$current-font-size: rwd-generate-font-size($label, $breakpoint);
```

现在，让我们扩展上面的函数来生成行高，以匹配我们的垂直节奏:

```
@function rwd-generate-font-size($label, $breakpoint) {
 $label-position: index($rwd-scale-labels, $label);
 $breakpoint-base-font-size: map-get(map-get($rwd-typography, $breakpoint), base-font-size);
 $breakpoint-type-scale: map-get(map-get($rwd-typography, $breakpoint), type-scale);
 $breakpoint-vertical-rhythm: map-get(map-get($rwd-typography, $breakpoint), vertical-rhythm);
 $font-size: $breakpoint-base-font-size;
 @for $i from 1 to $label-position {
 $font-size: $font-size * $breakpoint-type-scale;
  }
 $base-vertical-rhythm: $breakpoint-base-font-size * $breakpoint-vertical-rhythm;
 $line-height: round($font-size / $base-vertical-rhythm) * $base-vertical-rhythm / $font-size;
 $return: join($font-size, $line-height);
 @return $return;
}
```

现在这个函数的`$return`值是一个包含两个值的列表:字体大小和行高。我们需要更新循环中的变量赋值来匹配:

```
$generated-values: rwd-generate-font-size($label, $breakpoint);
 $font-size: nth($generated-values, 1);
 $line-height: nth($generated-values, 2);
```

Sass 中的`nth()`函数返回给定索引位置的列表值。

现在让我们把所有的东西放在一起:

```
@each $label in $rwd-scale-labels {
  %#{$label} {
 @each $breakpoint, $data in $rwd-typography {
 $generated-values: rwd-generate-font-size($label, $breakpoint);
 $font-size: nth($generated-values, 1);
 $line-height: nth($generated-values, 2);

 @if map-get($data, min-width) != "null" {
 @media screen and (min-width: map-get($data, min-width) {
 font-size: $font-size;
 line-height: $line-height;
        }
      } @else {
 font-size: $font-size;
 line-height: $line-height;
      }
    }
  }
}
```

现在要使用这些样式，从实际的选择器中扩展它们。例如:

```
body {
 @extend %p;
}

blockquote {
 @extend %bq;
}

h1 {
 @extend %h;
}

h2 {
 @extend %sh;
}

.hero-title {
 @extend %hero;
}
```

现在每个选择器都获得了我们用上面的循环和函数生成的所有基于媒体查询的输出！您可以在初始地图和列表中设置所有数据，然后扩展您创建的标签，以使用循环中自动生成的 CSS 输出创建响应性排版。



## 分享这篇文章