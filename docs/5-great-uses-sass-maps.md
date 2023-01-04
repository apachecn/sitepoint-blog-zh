# Sass 地图的 5 大用途

> 原文：<https://www.sitepoint.com/5-great-uses-sass-maps/>

Sass map 是一个非常棒的工具，可以用来组织您的代码，最小化重复，并使您的代码库更不容易出现总体粗心错误。他们允许我们以一种有助于他们整体架构的方式概述和记录我们的项目。 [Maps](http://sass-lang.com/documentation/file.SASS_CHANGELOG.html#330_7_march_2014) 最早出现在 Sass 3.3 中，已经改变了开发者构建代码的方式。去年，Hugo 写了一篇关于使用 Sass 地图的很棒的[帖子](https://www.sitepoint.com/using-sass-maps/)，我推荐你也看看。

请注意，Sass **源地图**和 Sass **地图**不是一回事。这两个特性出现在同一个版本中，有时会相互混淆。

## 基本用法

地图用括号`()`括起来，并以`key: value`对的形式构成。它们可以通过`map-get()`访问，并且可以相互嵌套，允许对分组内容进行组织。

```
$map-name: (
 key-1: val-1,
 key-2: val-2,
 key-3: val-3
);
```

现在我们已经了解了基本结构，让我们深入研究利用地图组织代码的五个实用策略！

## 1.清晰的层次

让我们从一些基本的地图用法开始。地图对于组织您的代码非常有用，这种组织真正有用的一个地方是处理项目中的元素层(即 z 索引)。

```
$layers: (
 modal: 8,
 logo: 7,
 header: 6,
 footer: 5,
 sidebar: 4,
 caption: 3,
 image: 2,
 text: 1
);
```

现在，我们可以清楚地看到，我们所有的元素之间的关系。然后，我们可以用下面的 mixin 来访问这个地图:

```
@mixin layer($layer-name) {
 z-index: map-get($layers, $layer-name);
};
```

并像这样使用它:

```
header {
 @include layer('header');
}
```

## 2.色彩管理

还记得我说过地图非常适合嵌套和组织信息组吗？让我们将色彩管理付诸实践吧！假设我们有一个带有特定色调和阴影的调色板(在一个基色上有更亮和更暗的变化)。

```
$colors: (
  'red': (
 tint: #f66,
 base: #c00,
 shade: #a00
  ),
  'orange': (
 tint: #f94,
 base: #f50,
 shade: #f12
  ),
  'yellow': (
 tint: #ffa,
 base: #ff0,
 shade: #ff5
  )
);
```

然后，我们可以编写一个函数来获取我们想要的颜色:

```
@function color($color-name, $tone: base) {
 @return map-get(map-get($colors, $color-name), $tone);
}
```

这个函数将读取我们的颜色的地图，并返回默认的基础。如果我们为*色调*输入一个值，那么我们就得到地图上颜色的色调。因此`background: color('yellow', 'tint')`将返回较浅的阴影。作为一个(非常清晰的)例子，

```
// .scss
h1 {
 background: color('yellow');
 color: color('red', 'shade');
}
```

变成了:

```
h1 {
  background: #ff0;
  color: #a00;
}
```

您也可以开始通过编号或任何其他对您的项目有意义的命名技术来标记着色。

## 3.媒体查询

在 Twitter 上做了一点调查后，我得出了一个关于如何最好地命名断点变量的结论。旧的命名思想涉及到特定于设备的断点，如`$phone`、`$desktop`或`$browser`。然后，人们开始意识到这是一个糟糕的策略——网络是流动的，人们无法假设一部手机会有多大的“尺寸”,所以他们开始命名变量`small`、`medium`和`large`。但这些也没那么大意义。

相反，我喜欢采用最显著的布局变化，并根据这种变化命名媒体查询的方法。例如，如果您的布局从一列更改为两列，则将该媒体查询命名为`mq--2-col`，如果您的主要更改是文本居中，则可以将其命名为`$mq--content-center`。

*题外话:当在公开场合使用变量时，我喜欢在组内给它们加前缀，即`color--`、`mq--`或`layer--`。*

然而，为了更好地管理这些名称，我们可以使用地图。

```
$breakpoints: (
 1-col: 0px,
 left-align-text: 400px,
 2-col: 680px,
 wide-header: 900px
);
```

你能看出这是怎么回事吗？像大纲一样，断点图讲述了页面扩展时发生的事情。从最小的点开始(这是可选的，我只是用它来证明一点)，我们有一个 1 列，居中文本布局。当它达到 400 像素时，文本向左对齐。在 680 像素，我们分成两列布局。在 900px，标题转换成宽格式迭代。从这张地图上我们可以看到主要的布局变化。

现在一个 mixin 使用地图:

```
@mixin smaller-than($point-name) {
 $width: map-get($breakpoints, $point-name);
 @media (max-width: $width) {
 @content;
  }
}

@mixin larger-than($point-name) {
 $width: map-get($breakpoints, $point-name);
 @media (min-width: $width) {
 @content;
  }
}

//usage
.heading {
 font-size: 2em;
 @include smaller-than(left-text-align) {
 font-size: 1.5em;
  }
 @include larger-than(2-col) {
 font-size: 3em;
  }
}
```

## 4.可存取图示

如果你在你的网站上为一些简单的图标(如社交媒体图标)使用图标字体(如 [IcoMoon](https://icomoon.io/) ),那么代码会非常非常难看。你可以制作一个快速的图标映射，*把图标的字符*映射到一个类。

```
$icon-map: (
 dribbble: '\e601',
 facebook: '\e607',
 instagram: '\e60c',
 twitter: '\e602',
 github: '\e603'
);
```

通过这种方式，您可以使用伪元素快速创建类和可访问图标，如下所示:

```
@each $name, $visual in $icon-map {
  .icon--#{$name} {
 font-size: 0;

 &:before {
 font-size: 1rem;
 font-family: 'IcoMoon';
 content: $visual;
    }
  }
}
```

这会将每个图标生成为一个`:before`伪元素，链接到一个类名`icon-`，同时使实际元素的文本不可见，但对屏幕阅读器来说仍然清晰可辨。同时，通过单独设置字体大小，图标的图形元素仍然可见。

所以如果我们有这一行 HTML: `<div class="icon--facebook">facebook</div>`，我们只会看到映射到角色`\e607`的 facebook 图标。

## 5.模式库

地图的另一个重要用途是构建模式库。开发人员对模式库最大的抱怨之一是它们体积庞大，充满了沉重的代码，没有人使用。为了使我们的 CSS 更有性能，我们不想包含任何我们实际上没有使用的代码。

嗯，**地图可以解决这个问题！**因为地图只是一种数据结构，并且在我们使用`map-get`访问它们之前不会输出任何代码，所以它们非常适合这一点！

以动画为例。您可以在地图中构建动画。

```
$animations: (
  'fade-in': (
 0%: (
 opacity: 0
    ),
 100%: (
 opacity: 1
    )
  ),
  'fade-flicker': (
 0%: (
 opacity: 1
    ),
 30%: (
 opacity: 0
    ),
 60%: (
 opacity: 1
    ),
 78%: (
 opacity: 0
    ),
 100%: (
 opacity: 1
    )
  )
);
```

与关键帧不同，这些都不会输出任何代码，除非我们要求它这样做。所以目前，在我们的项目中没有关键帧可供我们使用。为了减轻这一点，让我们为我们将包含在项目中的动画制作一些混音和一张新地图。

```
@mixin keyframe-gen($name) {
 @keyframes #{$name} {
 @each $position, $change in map-get($animations, $name) {
      #{$position} {
 @each $prop, $val in $change {
 #{$prop}: #{$val};
        }
      }
    }
  }
};

$included-animations: ();

@mixin animate($name, $duration: 2s, $timing: ease-in) {
 $exists: index($included-animations, '#{$name}');
 @if not $exists {
 @at-root {
 @include keyframe-gen($name);
    }
 $included-animations: append($included-animations, '#{$name}') !global;
  }

 animation: $name $duration $timing;
};
```

这样做的目的是写出要包含在我们项目中的动画，并设置要在元素上调用的动画。没有重复，我们的代码保持真正的**需要使用的输出基础**。为了证明:

```
.fade-me-in {
 @include animate('fade-in');
}

.also-fade-me-in {
 @include animate('fade-in', 3s);
}
```

我们唯一的输出是:

```
@keyframes fade-in {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}

.fade-me-in {
  animation: "fade-in" 2s ease-in;
}

.also-fade-me-in {
  animation: "fade-in" 3s ease-in;
}
```

## 作为轮廓的地图

显然，萨斯地图非常棒。它们是一种数据结构，你可以用它来真正帮助你*规划出*(所有双关语都是有意的)你的项目，并以整洁有序的方式显示其各个方面的摘要。最大的优势是它们的组织结构(作为项目各个方面的大纲),并且 map 从不输出任何代码，直到被调用这样做。所以，尝试一下这些技巧，用你自己的想法来评论使用地图吧！

## 分享这篇文章