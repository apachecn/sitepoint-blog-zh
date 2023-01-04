# 帮助你充分利用 Sass 的 8 个技巧

> 原文：<https://www.sitepoint.com/8-tips-help-get-best-sass/>

Sass 创建了语法上很棒的样式表，或者至少这是它应该做的。

当有效使用时，Sass 有助于构建可伸缩的和 <abbr title="Don't Repeat Yourself">DRY</abbr> CSS。然而，如果使用不当，Sass 实际上会增加文件大小，并添加不必要的或重复的代码。

下面是一系列的提示和建议，帮助你充分利用 Sass…

## 1.构建你的 Sass

对于任何一个新的 Sass 项目来说，从一开始就让你的站点结构正确是至关重要的。使用[片段](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#partials)允许你将 CSS 分解成更小更易管理的代码块，这样更容易维护和开发。

部分文件是使用下划线创建的，不会作为单独的 CSS 文件输出。应该使用 Sass 文件夹根目录中的主 Sass 文件(global.scss)导入每个部分。

例如，这里有一个示例文件夹结构来演示这一点:

```
vendor/
base/
|
|-- _variables.scss
|-- _mixins.scss
|-- _placeholders.scss

framework/
modules/
global.scss
```

这种文件夹结构确保网站易于使用和添加。例如，新模块可以很容易地添加到模块文件夹中，然后使用`@import`添加到 global.scss 中。

为了演示，下面是一个示例 global.scss 文件:

```
/* VENDOR - Default fall-backs and external files.
========================================================================== */

@import 'vendor/_normalize.scss';

/* BASE - Base Variable file along with starting point Mixins and Placeholders.
========================================================================== */

@import 'base/_variables.scss';
@import 'base/_mixins.scss';
@import 'base/_placeholders.scss';

/* FRAMEWORK - Structure and layout files.
========================================================================== */

@import 'framework/_grid.scss';
@import 'framework/_breakpoints.scss';
@import 'framework/_layout.scss';

/* MODULES - Re-usable site elements.
========================================================================== */ 

@import 'modules/_buttons.scss';
@import 'modules/_lists.scss';
@import 'modules/_tabs.scss';
```

顺便提一下，你可能想看看 [Hugo 在 Sass architecture](https://www.sitepoint.com/architecture-sass-project/) 上发表的内容丰富的帖子，了解这方面的更多技巧。

## 2.更有效地使用 Sass 变量

变量是 Sass 更直接的特性之一，但有时仍会被错误地使用。使用变量时，创建一个站点范围的命名约定至关重要。如果没有，它们会变得难以理解和重用。

以下是创建有用变量的一些提示:

*   命名变量时不要含糊。
*   拥有并坚持一个命名约定(模块化，BEM 等。)
*   确保变量的使用是合理的。

以下是一些很好的例子:

```
$orange: #ffa600; 
$grey: #f3f3f3; 
$blue: #82d2e5;

$link-primary: $orange;
$link-secondary: $blue;
$link-tertiary: $grey;

$radius-button: 5px;
$radius-tab: 5px;
```

还有一些不好的例子:

```
$link: #ffa600;
$listStyle: none;
$radius: 5px;
```

## 3.减少混音的使用

混合是在一个站点中多次包含代码段的好方法。然而，包含一个 mixin 等同于在整个 CSS 文件中复制和粘贴样式。它会产生大量的重复代码，并且会使你的 CSS 文件膨胀。

因此，只有在参数存在的情况下，才应该使用 mixin 来快速创建修改后的样式。

这里有一个例子:

```
@mixin rounded-corner($arc) {
 -moz-border-radius: $arc;
 -webkit-border-radius: $arc;
 border-radius: $arc; 
}
```

这个`rounded-corner`混音可以在任何情况下使用，只需改变`$arc`的值，使其成为一个有价值的混音:

```
.tab-button {
 @include rounded-corner(5px); 
}

.cta-button {
 @include rounded-corner(8px); 
}
```

一个糟糕的例子可能是这样的:

```
@mixin cta-button {
 padding: 10px;
 color: #fff;
 background-color: red;
 font-size: 14px;
 width: 150px;
 margin: 5px 0;
 text-align: center;
 display: block;
}
```

这个 mixin 没有参数，因此最好将[写成占位符](https://www.sitepoint.com/sass-mixin-placeholder/)，这就引出了第 4 点。

## 4.拥抱占位符

与 mixins 不同，[占位符](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#placeholder_selectors_)可以多次使用，无需添加任何重复代码。这使得它们成为输出干巴巴的 CSS 的更友好的选择:

```
%bg-image {
 width: 100%;
 background-position: center center;
 background-size: cover;
 background-repeat: no-repeat;
}

.image-one {
 @extend %bg-image;
 background-image:url(/img/image-one.jpg");
}

.image-two {
 @extend %bg-image;
 background-image:url(/img/image-two.jpg");
}
```

以及编译后的 CSS:

```
.image-one, .image-two {
 width: 100%;
 background-position: center center;
 background-size: cover;
 background-repeat: no-repeat;
}

.image-one {
 background-image:url(/img/image-one.jpg") ;
}

.image-two {
 background-image:url(/img/image-two.jpg") ;
}
```

占位符中重复的代码只输出一次，只有唯一的样式应用于各个选择器。如果未使用，占位符样式根本不会输出。

结合第 3 点，占位符可以与 mixin 一起使用，以减少重复代码，同时仍然保持 mixin 的灵活性…

```
/* PLACEHOLDER 
============================================= */

%btn {
 padding: 10px;
 color:#fff;
 curser: pointer;
 border: none;
 shadow: none;
 font-size: 14px;
 width: 150px;
 margin: 5px 0;
 text-align: center;
 display: block;
}

/* BUTTON MIXIN 
============================================= */

@mixin  btn-background($btn-background) {
 @extend %btn;
 background-color: $btn-background;
 &:hover {
 background-color: lighten($btn-background,10%);
    }
}

/* BUTTONS
============================================= */

.cta-btn {
 @include btn-background(green);
}

.main-btn {
 @include btn-background(orange);
}

.info-btn {
 @include btn-background(blue);
}
```

## 5.使用函数进行计算

[函数](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#functions)用于执行计算。Sass 函数不输出任何 CSS。相反，它返回一个可以在 CSS 中使用的值。这对于将在整个站点进行的计算非常有用。

例如，函数对于计算给定元素的百分比宽度非常有用:

```
@function calculate-width ($col-span) {
 @return 100% / $col-span 
}

.span-two {
 width: calculate-width(2); // spans 2 columns, width = 50%
}

.span-three {
 width: calculate-width(3); // spans 3 columns, width = 33.3%
}
```

## 6.订购您的工作

将所有混合、函数、占位符和变量放入它们相关的部分文件中。将代码块放在一起将确保它们在将来易于编辑和重用。

站点范围内的元素应该放在一个基本文件夹中。基本文件夹应包含全局变量，如字体和配色方案:

```
$font-primary: 'Roboto', sans-serif; 
$font-secondary: Arial, Helvetica, sans-serif;

$color-primary: $orange;
$color-secondary: $blue;
$color-tertiary: $grey;
```

特定于模块的混合、函数和变量应该保存在正确的模块的分部文件中:

```
$tab-radius: 5px;
$tab-color: $grey;
```

## 7.限制嵌套

在 Sass 中过度使用嵌套规则会导致很多问题，从复杂的代码到过于具体和过于依赖页面的 HTML 结构。这些事情会导致进一步的问题，并可能增加包含`!important`的需要，这通常是应该避免的。

这里有一些筑巢的黄金法则:

*   永远不要超过 3 层。
*   确保 CSS 输出是干净的和可重用的。
*   在有意义时使用嵌套，而不是作为默认选项。

## 8.保持事情简单

在这篇文章中，我想说的最后一点是让事情尽可能简单。Sass 的目的是编写更干净、更易管理的 CSS。在创建任何新的 mixins、变量或函数之前，确保它们的存在将增强开发，而不是使事情过于复杂。当在正确的情况下适度使用时，所有的 Sass 特性都是有用的。

创建一个没有明确用法的无止境的变量列表，或者一个除了作者之外的任何人都难以理解的复杂函数，这不是我们的初衷，也不会有助于开发或者产生枯燥的 CSS。

## 结论

这就是我列出的无礼小贴士。你可能不同意所有这些。Sass 仍然是一项相当新的技术，因此我们将继续学习新的最佳实践。如果你有自己的建议，请随时发表评论。

## 分享这篇文章