# 缓存来自 Sass 混合的值

> 原文：<https://www.sitepoint.com/caching-values-sass-mixins/>

让我们后退一点，这样我可以解释这篇文章的背景。最近几周，我一直在积极地比较 mixins 和占位符之间的渲染输出。结果，我想得越多，就越相信使用 Sass 文档中描述的混合和占位符是不够的。

让我们关注这里的主要目标:CSS 输出。在整个实验中，我没有考虑代码复杂性或代码质量。我只考虑最终的 CSS 输出。重点是让 CSS 输出尽可能的轻和瘦。

如果你读过我以前的文章，涵盖了[占位符与混合](https://www.sitepoint.com/sass-mixin-placeholder/)和 [`@extend`指令](https://www.sitepoint.com/sass-extend-nobody-told-you/)，你会意识到这两个特性都有一些缺点。当`@extend`指令被限制在它们的范围内时，Mixins 不合并选择器，因此它们在带有`@media`块的响应上下文中基本上是不可用的(我在关于[跨范围@extend](https://www.sitepoint.com/cross-media-query-extend-sass/) 的文章中处理并部分解决了这个问题)。

今天，我想处理另一个问题:Mixin 分组。

## 什么是 Mixin 分组？

当您想在变量代码中创建快捷方式时，Mixins 确实很方便。例如，您可以让一个 mixin 接受两个参数来输出`width`和`height`。或者[一个偏置定位的 mixin】。真好。](https://hugogiraudel.com/2013/08/05/offsets-sass-mixin/)

mixins 的问题是**每次你调用它们，它们都会转储 CSS 规则集**。如果您从两个不同的选择器中用相同的参数调用它们两次，它们不会合并到一个规则集中。相反，他们会两次转储相同的规则集——这有点糟糕。

对于本文，我们将处理一个非常简单的用例:一个分配`width`和`height`的`size` mixin。它看起来是这样的:

```
// Helper mixin to define dimensions
// ---
// @param [number] $width: width
// @param [number] $height ($width): height
// ---
@mixin size($width, $height: $width) {
  width: $width;
  height: $height;
}
```

使用它相当容易。

```
.element {
  @include size(25%, 5em);
}
```

这将产生以下 CSS:

```
.element {
  width: 25%;
  height: 5em;
}
```

很完美，不是吗？现在，假设我们在项目中的不同位置有另一段代码，也应该使用相同的维度。

```
.other-element {
  @include size(25%, 5em);
}
```

目前没有问题。但是最终的产出是什么呢？

```
.element {
  width: 25%;
  height: 5em;
}

/* [...] */

.other-element {
  width: 25%;
  height: 5em;
}
```

现在你可以看到问题了。因为这两个规则集完全相同，所以它们应该被合并。像那样有相同的规则集是相当蹩脚的，不是吗？

## 过度工程化的东西

基于这个问题，我想到了一个解决方案，保持 mixin 的用法不变，但是在后台使用占位符。基本上，它将是一个 mixin 扩展一个占位符在飞行中产生。听起来像一个冒险的赌注，但你会看到它实际上是非常热的。

首先，我们需要一个列表来存储我们的值。事实上，我们需要的不仅仅是一份清单——我们需要一张地图。这是严格的内部事务；开发人员不需要知道它，也不需要手动做任何事情。

```
$cache: (
  'width'  : (),
  'height' : ()
);
```

现在，让我们回到我们的 mixin，它没有任何签名变化。

```
@mixin size($width, $height: $width) {
  // Width
  $stored-widths: map-get($cache, 'width');
  @if not index($stored-widths, $width) {
    $cache-size: map-merge($cache, ('width': append($stored-widths, $width)));
    @at-root %width-#{length($stored-widths) + 1} {
      width: $width;
    }
  }

  // Height
  $stored-heights: map-get($cache-size, 'height');
  @if not index($stored-heights, $height) {
    $cache-size: map-merge($cache, ('height': append($stored-heights, $height)));
    @at-root %height-#{length($stored-heights) + 1} {
      height: $height;
    }
  }

  // Actually applying values
  @extend %width-#{index(map-get($cache, 'width'), $width)};
  @extend %height-#{index(map-get($cache, 'height'), $height)};
}
```

我不得不承认这一准则相当苛刻。

事情是这样的:首先，我们在`$stored-widths`变量中获取存储的宽度(第 3 行)。如果在列表中找到了`$width`值，我们什么也不做。如果没有找到它(第 4 行)，我们不仅将`$width`追加到列表中(第 5 行)，而且还在根级别生成一个占位符(第 6 行)，以新添加的索引命名。

然后我们对`$height`做完全相同的事情(第 12 到 15 行)。一旦我们完成了，我们有新生成的占位符要扩展，所以我们扩展它们(第 21 和 22 行)。故事结束。

如果我们回到最初的例子，下面是我们第一次调用 mixin(产生第一个规则集)时会发生的情况:

1.  `.element`调用宽度为`25%`高度为`5em`的`size()`。
2.  在存储的宽度中没有找到`25%`，所以它被存储，并且在根级别生成一个`%width-1`占位符。
3.  在存储的高度中没有找到`5em`，所以它被存储，并且在根级别生成一个`%height-1`占位符。
4.  这两个占位符都会扩展，从而产生以下 CSS:

```
.element { 
  width: 25% 
}

.element { 
  height: 5em 
}
```

现在，当用相同的参数第二次调用`size()`时(这应该产生第二个规则集，但是我们将看到为什么它没有产生):

1.  `.other-element`调用宽度为`25%`高度为`5em`的`size()`。
2.  `25%`在我们存储的宽度中的索引`1`处被找到，因此`%width-1`被扩展，将`.other-element`追加到现有的`.element { width: 25% }`选择器。
3.  在我们存储的高度中的索引`1`处找到`5em`，因此`%height-1`被扩展，将`.other-element`追加到现有的`.element { height: 5em }`选择器。

```
.element, .other-element { 
  width: 25% 
}

.element, .other-element { 
  height: 5em 
}
```

没错，这里只有两个电话，这没什么大不了的。但是想象一下，一个大型项目对同一个 mixin 有几十个调用，这些调用都有类似于`100%`或`1px`的公共值。不再有多余的行被转储，所有的选择器都被重新分组。

## 使用缓存混合实现自动化

虽然我们所做的很酷，但它涉及到代码重复，并且很快会变得令人讨厌，特别是那些有大量 CSS 输出的 mixins。如果我们做一个小的 mixin 来帮我们处理这个问题会怎么样？

这个想法是将属性/值的集合传递给我们的 mixin，这样它就可以在全局缓存映射中缓存这些值。因此，我们的 mixin 只需要一个参数就可以工作:CSS 声明的映射。

```
// Global cache map
$cache: ();

// Cache mixin
@mixin cache($declarations) {
  /* Sass magic */
}
```

现在，我们需要做的就是遍历映射中的所有声明。对于每个声明:

*   我们检查该值是否存在于给定属性的存储值列表中。
*   如果没有，我们将它添加到列表中，并在根级别创建一个占位符。
*   我们扩展已经存在或新创建的占位符。

```
@mixin cache($declarations) {
  // Looping through all properties/values from map
  @each $property, $value in $declarations {
    // Get the stored values for the current property
    $stored-values: map-get($cache, $property);

    // If the value doesn't exist in stored values
    @if not index($stored-values, $value) {
      // Add it
      $cache: map-merge($cache, ($property: append($stored-values or (), $value))) !global;
      // And create a placeholder at root level
      @at-root %#{$property}-#{length(map-get($cache, $property))} {
        #{$property}: $value;
      }
    }

    // Extend the placeholder
    @extend %#{$property}-#{index(map-get($cache, $property), $value)};
  }
}
```

给你。一个非常干净整洁的缓存混合。现在，如果我们回到最初的例子:

```
@mixin size($width, $height: $width) {
  @include cache((
    width: $width,
    height: $height
  ));
}
```

很简单，不是吗？最后，它看起来非常干净(大声读出来就不言自明了)，它缓存了内容，因此它充分利用了 Sass 占位符，并且我们避免了内容重复。谁还能要求更多？

## 这个有用吗？

很难说。在一个中型到大型的项目中，我可以看到用所有 mixins 做类似事情的好处。我们使用了一个简单的例子，但是你可以把这个技术应用到所有的混音上；您只需要将关键字添加到缓存映射中(例如，`padding`、`margin`、`color`……)。

有什么性能上的好处吗？大概吧。我没有做过任何深入的测试，但我不明白为什么不会有。最后，在可能的情况下，我们通过对选择器进行分组来减少从 mixins 生成的 CSS 数量。肯定不会差。

也就是说，Gzip 在重复字符串上做了一些惊人的工作，所以最终我不确定你是否真的能看到主要的不同。

请随意查看 SassMeister 上完整注释的代码:

在 SassMeister 上玩这个要诀。