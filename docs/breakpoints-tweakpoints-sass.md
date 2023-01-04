# Sass 中的断点和调整点

> 原文：<https://www.sitepoint.com/breakpoints-tweakpoints-sass/>

不久前，我在 SitePoint 上写了[用 Sass](https://www.sitepoint.com/managing-responsive-breakpoints-sass/) 管理响应式断点，介绍了几种使用变量和混合来处理响应式设计断点的方法。

我介绍的最后一个解决方案依赖于断点映射，通常存储在配置文件和 mixin 中，mixin 将断点名称作为唯一的参数，输出媒体查询块。它应该是这样的:

```
// In `_config.scss`
$breakpoints: (
  'small'  : 767px,
  'medium' : 992px,
  'large'  : 1200px,
) !default;

// In `_mixins.scss`
@mixin respond-to($breakpoint) {
 @if map-has-key($breakpoints, $breakpoint) {
 @media (min-width: map-get($breakpoints, $breakpoint)) {
 @content;
    }
  }

 @else {
 @warn "Unfortunately, no value could be retrieved from `#{$breakpoint}`. "
 + "Please make sure it is defined in `$breakpoints` map.";
  }
}

// In `_random-component.scss`
.foo {
 color: red;

 @include respond-to('small') {
 color: blue;
  }
}
```

这个解决方案非常适合许多项目，小到中等规模。然而，当我们开始拥有许多组件时，往往会有所欠缺。

事实上，我们可以区分两种不同的断点:

*   实际断点，涉及布局重组；
*   特定于组件的断点，有助于改善页面的外观。

布局相关的断点基本上是我们在前面的例子。它们决定了布局何时发生重大变化，例如何时在网格中添加或删除一列。它们允许改变布局，以避免看起来被破坏，因此得名*断点*。

但更多的时候，布局断点是不够的。组件的行为不应该由它们来决定。每个特定的组件都应该有自己的一组断点，触发该特定组件内部的微布局更改。那些断点实际上并不是断点，因为没有它们布局不会中断，因此应该被称为 T2 调整点，正如杰里米·基思在 T4 调整点中所建议的。

> 根据我的经验，并不是所有的断点都是一样的。当然，为了让内容看起来不像垃圾，有些地方的布局需要彻底改变——这些媒体查询可以被合理地称为断点。但是也有一些媒体查询，用于在不对布局进行任何重大更改的情况下修饰页面元素。称它们为断点感觉有点奇怪，好像没有它们布局会“中断”。那些媒体的询问是为了调整版面。它们不是断点。它们是转折点。
> ——出自[杰里米·基思](https://twitter.com/adactio)[中的](https://adactio.com/journal/6044)扭捏点

当然，这两者并不相互排斥。恰恰相反！如果需要的话，你会经常发现自己想要在布局断点上加入对组件的主要结构更改，并在特定于组件的调整点上应用微优化。

在本文中，我们将重载我们现有的系统，使之能够处理应用程序范围的断点和组件断点。

## 我们需要什么？

我的想法是在配置文件(`_config.scss`或`_variable.scss`)中保留应用程序范围的断点，但是在每个组件文件中也有一个本地调整点映射。例如，让我们考虑一个生活在`components/_logo.scss`的 T3 标志。为了适应设计的变化，这个标志碰巧有两种变化:一个内嵌版本和一个方块版本。

```
/// Logo-specific tweakpoints
/// @type Map
$tweakpoints: (
  'inline' : 650px,
  'block'  : 980px,
);

.logo { .. }
.logo__image { .. }
.logo__baseline { .. }
```

在`_logo.scss`中，我们定义了一个`$tweakpoints`映射，它包含了特定于这个 logo 组件的断点(好吧，调整点，但是你现在明白了)。它们只在这个上下文中有意义，因此应该只在这个文件中可用。也就是说，全局断点也应该是可用的，以防我们实际上需要它们来进行重大更改或其他事情。

那么我们需要什么？我们只需要让我们的 mixin 知道这个地图。它应该首先检查请求的断点是否在本地映射中，如果不在，就在全局映射中尝试。

## 使混音过载

令人惊讶的是，我们的 mixin 实际上非常简单，并不比上一个长多少:

```
/// Breakpoints/tweakpoints manager
/// @param {String} $point - Breakpoint/tweakpoint name
@mixin respond-to($point) {
 @if map-has-key($tweakpoints, $point) {
 @media (min-width: map-get($tweakpoints, $point)) {
 @content;
    }
  } @else if map-has-key($breakpoints, $point) {
 @media (min-width: map-get($breakpoints, $point)) {
 @content;
    }
  } @else {
 @warn "Could not find `#{$breakpoint}` in both local ($tweakpoints) and global ($breakpoints) contexts. Media block omitted.";
  }
}
```

这里涉及三个逻辑步骤:

1.  如果请求的点存在于`$tweakpoints`中，打开媒体查询并离开 mixin。
2.  如果请求的点不在`$tweakpoints`中，但在`$breakpoints`中，打开媒体查询并离开 mixin。
3.  如果请求点不在`$tweakpoints`或`$breakpoints`中，警告用户。

注意，如果断点同时出现在`$tweakpoints`和`$breakpoints`图中，调整点将覆盖断点。例如，如果您定义了一个`medium`调整点和一个`medium`断点，那么在包含`respond-to('medium')`时将使用该调整点。如果我们想在本地覆盖当前组件的断点，这是有目的的(尽管我不建议这样做)。

## 使用它

使用这种新鲜的 mixin 与使用我们以前的没有任何不同。API 没有太大的变化，每一个变化都是完全向后兼容的，这在我看来非常好。

```
<a href="/" class="logo">
  <img class="logo__image" src="..." alt="..." />
  <p class="logo__baseline">...</p>
</a>
```

```
/// Logo-specific tweakpoints
/// @type Map
$tweakpoints: (
  'inline' : 650px,
  'block'  : 980px,
);

/**
 * Logo wrapper
 * 1\. Turn the link into a block level element
 * 2\. Size it based on the narrowest child when in *block* format
 */
.logo {
 display: block; /* 1 */

 padding: .5em;
 color: currentcolor;
 text-decoration: none;

 @include respond-to('block') {
 width: min-content; /* 2 */
 margin: 1em auto;
 text-align: center;
  }
}

/**
 * Logo image
 * 1\. Give it a max-width when inline'd so it does break out
 */
.logo__image {
 vertical-align: middle;

 @include respond-to('inline') {
 max-width: 3em; /* 1 */
  }

 @include respond-to('block') {
 display: block;
 height: auto;
 margin: 0 auto .5em;
  }
}

/**
 * Logo baseline
 * 1\. Inline it when in *inline* format
 */
.logo__baseline {
 margin: 0;
 vertical-align: middle;

 @include respond-to('inline') {
 display: inline-block; /* 1 */
  }
}
```

## 更进一步

理想情况下，您希望在组件文件的末尾重新初始化`$tweakpoints`映射，这样它就不会泄漏到下一个部分。像这样简单的事情就足够了:

```
$tweakpoints: ();
```

这确保了下一个组件没有配置任何调整点。问题是很容易忘记这一行，如果下一个组件自己定义了一个`$tweakpoints` map，这实际上没什么大不了的。但是如果没有，并且前一个组件用一个调整点覆盖了一个全局断点，那么下一个组件将拥有被覆盖的断点，而不是全局断点。

例如，考虑一下这个:

```
// In `_config.scss`
$breakpoints: (
  'small': 767px,
  'medium': 992px,
);
```

```
// In `_component-1.scss`
$tweakpoints: (
  'custom': 500px,
  'medium': 1170px;
);

// A lot of Sass rules here, but no `$tweakpoints: ();` at the end of file
```

```
// In `_component-2.scss`

// No `$tweakpoints` map defined for this component
```

```
// In `main.scss`
@import "utils/config";

@import "components/component-1";
@import "components/component-2";
```

现在，如果我们碰巧在`_component-2.scss`中有`@include respond-to('medium')`，断点的值将是`1170px`，而不是`992px`。发生这种情况是因为前一个组件(`_component-1.scss`)用一个本地值覆盖了全局`medium`，并且没有重置文件末尾的调整点映射。

好吧，这很糟糕，也很容易出错。我们肯定可以做得更好！

事实上我们可以。如果我们有一个负责分配和重置`$tweakpoints`的`component` mixin 会怎么样？它基本上是我们组件的包装器。

```
/// Component wrapper
/// @param {Map} $component-tweakpoints [()] - Component tweakpoints
@mixin component($component-tweakpoints: ()) {
 $tweakpoints: $component-tweakpoints !global;
 @content;
 $tweakpoints: () !global;
}
```

这就是我们所需要的。现在，让我们重写我们之前的例子:

```
// In `_config.scss`
$breakpoints: (
  'small': 767px,
  'medium': 992px,
);
```

```
// In `_component-1.scss`
@include component((
  'custom': 500px,
  'medium': 1170px,
)) {
  // A lot of Sass rules here, but no `$tweakpoints: ();` at the end of file
}
```

```
// In `_component-2.scss`

@include component {
  // No `$tweakpoints` map defined for this component
}
```

```
// In `main.scss`
@import "utils/config";

@import "components/component-1";
@import "components/component-2";
```

就是这样！没有更多的问题，因为`component` mixin 负责在组件的末尾重置`$tweakpoints` map，确保没有不幸的泄漏。

## 最后的想法

老实说，我还没有在真实项目中测试过这个解决方案，但是我认为它是断点管理的一个有价值的额外特性。由于这个小小的增加，处理应用程序范围的断点和特定于组件的调整点变得非常容易。

我可能应该警告您这种方法的一个潜在缺点:将每个组件包装在 mixin 调用(`@include component(..) { .. }`)中会阻止您声明特定于组件的 mixin，因为您不能在 mixin 调用中定义 mixin。也就是说，我不太喜欢特定于组件的混合和函数，所以如果你碰巧像我一样思考，这可能不是一个实际的缺点。

在结束这篇文章之前，我想提一下来自 [Kaelig](https://twitter.com/kaelig) 的 [Sass-MQ](https://github.com/sass-mq/sass-mq) 允许类似的东西。

```
// _random-component.scss
$tweakpoints: (
  'custom': 500px,
  'medium': 1170px,
);

.foo {
 @include mq('medium') {
 color: red;
  }

 @include mq('medium', $breakpoints: $tweakpoints) {
 color: blue;
  }
}
```

如您所见，`mq` mixin 接受一个断点映射作为额外的参数，默认为`$mq-breakpoints`(Sass-MQ 用来存储全局断点的映射)。如果您想让 API 稍微方便一点，您可以随时编写自己的小 mixin:

```
@mixin component-mq($from: false, $until: false) {
 @include mq($from: $from, $until: $until, $breakpoints: $tweakpoints) {
 @content;
  }
}
```

然后你这样用它:

```
.foo {
  // Global medium: 992px
 @include mq('medium') {
 color: red;
  }

  // Local medium: 1170px
 @include component-mq('medium') {
 color: blue;
  }
}
```

就是这样！我希望你喜欢它，如果你有任何建议，一定要在评论中分享！:)

## 分享这篇文章