# 使用 Sass 地图

> 原文：<https://www.sitepoint.com/using-sass-maps/>

Sass 的第三个主要版本[带来了一种新的数据类型`map`。虽然你可能不熟悉这个名字，但我们已经在其他语言中经常使用地图，通常以*关联数组*或*散列*的名字命名。换句话说，Sass 映射是一个将键与值相匹配的数组。](http://blog.sass-lang.com/posts/184094-sass-33-is-released)

并不总是清楚为什么我们需要 CSS 中的地图(是的，Sass 就是 CSS)，因此这篇文章给你一些提示。这个列表显然不是详尽的，所以请随意查找和分享其他用例。

## Sass 映射的语法

在进一步讨论之前，让我们对每个人都进行一下水平测试。

Sass 映射使用括号作为外部分隔符，使用冒号来映射键和值，使用逗号来分隔键/值对。下面是一个有效的 Sass 映射:

```
$map: (
  key: value,
  other-key: other-value
);
```

你需要知道的几件事:

*   有很多功能可以让你操作地图
*   映射中的最后一个键/值对可以选择后跟一个额外的逗号
*   密钥必须是唯一的
*   键和值可以是*任何* [Sass 类型](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#data_types)，包括列表和其他地图。

是的，键不一定是字符串；他们可以是任何东西。甚至`null`。甚至地图。几周前，Chris Eppstein、Micah Godbolt、Jackie Balzer 和我在 Twitter 上就此进行了一次对话。我有一点 JavaScript 的背景，除了字符串作为散列键，我不喜欢其他任何东西。反正在萨斯那是有可能的。

好了，现在你应该准备好出发了！

## 项目配置

让我们从一个常见的用例开始。当涉及到项目配置时，地图是完美的。想法很简单:您将值与键相关联，然后使用`map-get($map, $key)`从项目中的任何地方访问它们。

我已经在关于在 Sass 中管理响应断点的文章中深入探讨了这一点。这里有一个简单的例子:

```
// _config.scss
$breakpoints: (
  small: 767px,
  medium: 992px,
  large: 1200px
);

// _mixins.scss
@mixin respond-to($breakpoint) { 
  @if map-has-key($breakpoints, $breakpoint) {
    @media (min-width: #{map-get($breakpoints, $breakpoint)}) {
      @content;
    }
  }

  @else {
    @warn "Unfortunately, no value could be retrieved from `#{$breakpoint}`. "
        + "Please make sure it is defined in `$breakpoints` map.";
  }
}

// _component.scss
.element {
  color: hotpink;

  @include respond-to(small) {
    color: tomato;
  }
}
```

导致:

```
.element {
  color: hotpink;
}

@media (min-width: 767px) {
  .element {
    color: tomato;
  }
}
```

所以这很酷。另一个流行的用例是*颜色*。不同的颜色变量是好的，但是当你有几十个这样的变量时就会变得混乱。拥有一个颜色的地图，可能被分成主题的子地图，可能是一个好主意。

```
// _config.scss
$colors: (
  sexy: #FA6ACC,
  glamour: #F02A52,
  sky: #09A6E4
);

// _component.scss
.element {
  background-color: map-get($colors, sky);
}
```

最终，你会厌倦一遍又一遍地重复`map-get($colors, ...)`，这样你就可以使用一个小助手功能来减轻你的痛苦:

```
// _functions.scss
@function color($key) {
  @if map-has-key($colors, $key) {
    @return map-get($colors, $key);
  }

  @warn "Unknown `#{$key}` in $colors.";
  @return null;
}

// _component.scss
.element {
  background-color: color(sky); // #09A6E4
}
```

关于这个话题，你可能想读一读厄斯金设计公司的这篇文章。

在继续之前，让我们看看地图配置的最后一个用例: *z 索引图层*。我认为这首先来自 Chris Coyier，当时他写道[他如何在 Sass 项目中管理 z-index](http://css-tricks.com/handling-z-index/) 。你知道你以前怎么写`z-index: 999999999`？那些日子已经过去了。

```
// _config.scss
$z-layers: (
  bottomless-pit: -9999,
  default: 1,
  dropdown: 3000,
  overlay: 4000
  modal: 4001
);

// _functions.scss
@function z($key) {
  @if map-has-key($z-layers, $key) {
    @return map-get($z-layers, $key);
  }

  @warn "Unknown `#{$key}` in $z-layers.";
  @return null;
}

// _component.scss
.overlay {
  z-index: z(overlay);
}

.element {
  z-index: (z(default) + 1);
}
```

考虑到`z-index`会有多混乱，我认为将所有的索引收集到一个单一的地图中并坚持使用它们是一件好事。然后您就知道应用程序使用了多少层，这样就很容易理解发生了什么(并且，迟早要进行调试)。

## 模块配置

我们将坚持配置，但让我们更深入地了解应用程序级别，处理模块/组件配置。我已经[写了关于使用 Sass 映射作为配置对象](http://hugogiraudel.com/2014/05/05/bringing-configuration-objects-to-sass/)以及为什么抛弃多参数签名而支持单个映射参数是有趣的。概念的快速验证:

```
// _mixins.scss
@mixin module($options: ()) {
  $configuration: map-merge((
    color: grey,
    duration: 0s,
    border: null
  ), $options);

  color: map-get($configuration, color);
  transition: map-get($configuration, duration);
  border: map-get($configuration, border);
}

// _component.scss
.element {
  @include module((
    color: pink,
    duration: .15s
  ));
}
```

这种解决方案的一个问题是它使得 mixin 签名非常通用。在现实生活中，签名是识别一个人的方式。当读取一个函数/mixin 签名时，我们应该能够识别什么参数是期望的。对于这种方法，它不是，因为您必须阅读 mixin 中的默认选项才能知道发生了什么。

要解决这个问题，用一个详细的签名(常规的多个参数)定义 mixin，然后使用`...`将它包含在一个配置图中。让我们重写我们之前的例子:

```
// _mixins.scss
@mixin module($color: grey, $duration: 0s, $border: null) {
  color: $color;
  transition: $duration;
  border: $border;
}

// _component.scss
.element {
  @include module((
    duration: .15s,
    color: pink
  )...);
}
```

在这种情况下，`...`扩展了值的映射，因此每一对都被视为一个[关键字参数](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#keyword_arguments_2)。它不仅使 mixin 签名更加明确，而且还抛弃了以前在 mixin 核心顶部需要的所有地图合并的东西。

这就是如何使用 Sass 映射来配置要求多个参数的 mixins。如果我没弄错的话，Python 是少数几种(众所周知的)允许你在函数要求多个参数时调用多维数组的语言之一。所以事实上，萨斯能够做到这一点是非常了不起的。

## 重复的东西

到目前为止，我们一直使用映射来获取与特定键相关联的值。Sass maps 的另一种用例是避免重复的东西。CSS 是一种非常湿的语言(每样东西都要写两遍)，代码重复是很常见的。这就是拥有一个循环和一个列表/映射有助于代码干燥的地方(不要重复)。

想想图标字体。图标字体通常是通过`:before`伪元素的`content`属性传递的编码字符的集合。[比如说](https://github.com/FortAwesome/Font-Awesome/blob/master/css/font-awesome.css#L184):

```
.fa-glass:before {
  content: "\f000";
}
.fa-music:before {
  content: "\f001";
}
.fa-search:before {
  content: "\f002";
}
.fa-envelope-o:before {
  content: "\f003";
}
.fa-heart:before {
  content: "\f004";
}

/* ... */
```

哇哦。非常重复。非常潮湿。如果我们将类名映射到编码字符会怎么样？

```
$icons: (
  glass: "\f000",
  music: "\f001",
  search: "\f002",
  envelope-o: "\f003",
  heart: "\f004"
);

@each $name, $icon in $icons {
  .fa-#{$name}:before {
    content: $icon;
  }
}
```

好多了，不是吗？尤其是当你的字体中有很多图标的时候。如果你想更进一步，弗雷德里克·佩兰在 CodePen 上用这个想法制作了一个很酷的小演示。还有一篇来自 ThoughtBot 的文章[对背景图片使用了同样的方法。](http://robots.thoughtbot.com/removing-sass-duplication)

## CSS 转储

这是另一个用例(如果我没弄错的话，第一个是由弥迦·戈德博尔特介绍的[)。我们将使用一个 Sass 映射作为 CSS 声明的列表。你看，Sass 维护者在实现新特性时，尽可能地坚持官方 CSS 语法。这就是为什么他们决定使用`()`作为列表/映射(在媒体查询中使用)和冒号作为映射操作符(在任何声明中使用)。](http://www.phase2technology.com/blog/exploring-maps-in-sass-3-3/)

因为 Sass 映射看起来像 CSS 规则列表，所以您可能有一个 mixin 来转储 Sass 映射的内容:

```
// _mixins.scss
@mixin print($declarations) {
  @each $property, $value in $declarations {
    #{$property}: $value
  }
}

// _component.scss
.element {
  @include print((
    margin: 0 auto,
    max-width: 50%,
    overflow: hidden
  ));
}
```

现在你可能在想为什么？为什么不从一开始就手动编写 CSS，而不是通过 mixin 传递它？这完全是关于环境的。当处理一个模块或其他东西时，通常会有一个存储了几个变量的映射，这些变量规定了组件应该如何工作。例如:

```
// _component.scss
$configuration: (
  padding: 1em,
  margin: 0 1em,
  color: grey
);

.element {
  color: map-get($configuration, color);
  padding: map-get($configuration, padding);
  margin: map-get($configuration, margin);

  &::before {
    background-color: map-get($configuration, color);
  }
}
```

您可以简单地打印带有`css()` mixin 的地图，而不是手动从键中获取每个值；

```
// _component.scss
.element {
  @include print($configuration);

  &::before {
    background-color: map-get($configuration, color);
  }
}
```

导致:

```
.element {
  padding: 1em;
  margin: 0 1em;
  color: grey;
}

.element::before {
  background-color: grey;
}
```

不算多，但也算了！

## 最后的想法

如您所见，Sass maps 功能是一个强大的工具。它不仅使代码更加结构化，还帮助您避免一些代码膨胀和重复。你呢，你如何使用萨斯地图？

## 分享这篇文章