# Sass 混合和媒体合并

> 原文：<https://www.sitepoint.com/sass-mixin-media-merging/>

如果您不太熟悉 Sass，您可能不知道 Sass 增强了媒体查询(实际上是`@media`指令)以提供额外的有趣特性。其中之一被称为(或者更确切地说经常被称为)*媒体融合*。

在向你解释什么是媒体合并之前，你知道关于媒体查询的 CSS 规范实际上[允许](https://www.w3.org/TR/css3-conditional/#processing)媒体查询被嵌套吗？有些浏览器像火狐、Chrome、Opera 都支持；其他一些像 Safari 和 Internet Explorer 目前没有。

因为浏览器支持并不理想，Sass 将嵌套的媒体查询合并到一个媒体条件中。例如，考虑以下代码:

```
@media (min-width: 42em) {
 @media (max-width: 1337px) {
    .foo {
 color: red;
    }
  }
}
```

这将被编译为:

```
@media (min-width: 42em) and (max-width: 1337px) {
  .foo {
    color: red;
  }
}
```

很方便，不是吗？这就是所谓的*媒体融合*。当嵌套的媒体查询被合并到单个语句中时。

## 我们想要什么？媒体查询！

既然我已经介绍完了，让我进入正题。前几天，我在玩这个想法。基本上，我想构建一个非常简单的 mixin，它将查询映射作为输入，并将它们合并到一个`@media`指令中的单个条件中作为输出。

回到前面的例子，我想这样写:

```
@mixin media($queries) { .. }

.foo {
 @include media((min-width: 42em, max-width: 1337px)) {
 color: red;
  }
}
```

并且在编译时，得到与上面的 CSS 片段相同的结果。现在，在我的脑海中，我能想到至少两种建造它的方法。让我们先解决丑陋的一个。

## 丑陋的版本

最直接的想法是在我们的地图上构建一个字符串。我们迭代映射，对于每个新的键/值对，我们将它们连接成结果字符串，用关键字`and`分隔成对。

```
/// Media query merger (the ugly version)
/// Create a single media condition out of a map of queries
/// @param {Map} $queries - Map of media queries
@mixin media($queries) {
 $media-condition: ';

  // Loop over the key/value pairs in $queries
 @each $key, $value in $queries {
    // Create the current media query
 $media-query: '(' + $key + ': ' + $value + ')';

    // Append it to the media condition
 $media-condition: $media-condition + $media-query;

    // If pair is not the last in $queries, add a `and` keyword
 @if index(map-keys($queries), $key) != length($queries) {
 $media-condition: $media-condition + ' and ';
    }
  }

  // Output the content in the media condition
 @media #{$media} {
 @content;
  }
}
```

好吧，不是*丑。它做得很好，但你必须承认这不是很优雅，是吗？*

## 优雅的方式

当 Sass 提供了如此优雅的方式来处理媒体查询时，我对操纵字符串感到不太舒服。肯定有更好的方法来做到这一点。然后它抚摸我:*递归*。根据*免费字典*，递归是:

> 一种定义对象序列的方法，如表达式、函数或集合，其中给定一些初始对象，每个连续的对象都是根据前面的对象定义的。斐波纳契数列是由递归定义的。

那有点难。简单地说，递归是一种机制，函数用不同的参数一遍又一遍地调用自己，直到某一点。JavaScript 中使用递归的函数的一个实际例子是:

```
function factorial(num) {
  if (num < 0) return -1;
  else if (num == 0) return 1;
  else return (num * factorial(num - 1));
}
```

正如你所看到的，这个函数调用自己，直到变量`num`变得小于`1`，每次运行都减少`1`。

我为什么要告诉你这些？我发现我们可以使用递归来构建我们的媒体条件，使用 Sass 媒体合并。如果我们让 mixin 为 map 中的第一个查询输出一个媒体，然后在没有这个查询的情况下调用自己传递 map，直到 map 中没有留下任何查询，会怎么样？让我们尝试一下，因为它可能有点复杂，我们将一步一步来。

首先，我们知道如果我们的地图不包含(更多)查询，我们只需输出内容。我们从这个开始怎么样？

```
@mixin media($queries) {
 @if length($queries) == 0 {
 @content;
  } @else {
    // ...
  }
}
```

现在，我们想为映射中的第一个媒体查询输出一个媒体块。为了得到一个地图的第一个键，我们可以使用`nth(..)`和`map-keys(..)`函数。

```
$first-key: nth(map-keys($queries), 1);

@media ($first-key: map-get($queries, $first-key)) {
  // ...
}
```

到目前为止，一切顺利。现在，我们只需要调用 mixin 本身，尽管我们不想传递给它相同的`$queries` map，否则我们将面临一个无限循环。在移除第一个键/值对之后，我们需要传递它`$queries`。谢天谢地，有这个`map-remove(..)`函数。

```
$queries: map-remove($queries, $first-key);

@include media($queries) {
 @content;
}
```

现在整个 mixin:

```
/// Media query merger
/// Create a single media condition out of a map of queries
/// @param {Map} $queries - Map of media queries
@mixin media($queries) {
 @if length($queries) == 0 {
 @content;
  } @else {
 $first-key: nth(map-keys($queries), 1);

 @media ($first-key: map-get($queries, $first-key)) {
 $queries: map-remove($queries, $first-key);

 @include media($queries) {
 @content;
      }
    }
  }
}
```

## 更进一步

在[之前的一篇文章](https://www.sitepoint.com/managing-responsive-breakpoints-sass/)中，我们看到了在 Sass 中管理响应断点的几种不同方法。上一个版本使用的 mixin 如下所示:

```
/// Breakpoints map
/// @type Map
$breakpoints: (
  'small': (min-width: 767px),
  'medium': (min-width: 992px),
  'large': (min-width: 1200px),
);

/// Responsive breakpoint manager
/// @param {String} $breakpoint - Breakpoint
/// @requires $breakpoints
@mixin respond-to($breakpoint) {
 $media: map-get($breakpoints, $breakpoint);

 @if not $media {
 @error "No query could be retrieved from `#{$breakpoint}`. "
 + "Please make sure it is defined in `$breakpoints` map.";
  }

 @media #{inspect($media)} {
 @content;
  }
}
```

这个 mixin 工作起来很有魅力，但是它不支持像`(min-width: 42em) and (max-width: 1337px)`这样的多查询条件，因为它依赖于`inspect(..)`函数，该函数只不过打印值的 Sass 表示。

因此，一方面，我们有一个断点管理器，它从全局断点映射中选择并处理错误消息，另一方面，我们有一个断点管理器，它允许使用多查询条件。选择是艰难的。

或者是？

通过稍微调整`respond-to(..)` mixin，我们可以让它包含`media(..)` mixin，而不是打印一个`@media`指令本身。那么，我们就拥有了两个世界的精华。

```
@mixin respond-to($breakpoint) {
  // Get the query map for $breakpoints map
 $queries: map-get($breakpoints, $breakpoint);

  // If there is no query called $breakpoint in map, throw an error
 @if not $queries {
 @error "No value could be retrieved from `#{$breakpoint}`. "
 + "Please make sure it is defined in `$breakpoints` map.";
  }

  // Include the media mixin with $queries
 @include media($queries) {
 @content;
  }
}
```

最好的事情是，如果你已经使用了这个 mixin，你可以通过调整`respond-to(..)`和添加`media(..)`来完全包含多查询特性，因为 API 根本没有改变:`respond-to(..)`仍然需要一个断点名称来工作，和以前一样。

## 最后的想法

我必须说我发现这非常令人兴奋，因为这是我第一次发现嵌套媒体查询和 mixin 递归的良好用例。虽然可以跳过这一步，像我们在第一个版本中看到的那样简单地构建一个字符串，但用递归 mixin 处理它肯定更优雅、更有趣。我希望你喜欢它！离开前的最后一个例子:

```
// _variables.scss
$breakpoints: (
  'small': (min-width: 767px),
  'small-portrait': (min-width: 767px, orientation: portrait),
  'medium': (min-width: 992px),
  'large': (min-width: 1200px),
);

// _mixins.scss
@mixin media($queries) { .. }
@mixin respond-to($breakpoint) { .. }

// _component.scss
.foo {
 @include respond-to('small-portrait') {
 color: red;
  }
}
```

产生以下 CSS:

```
@media (min-width: 767px) and (orientation: portrait) {
  .foo {
    color: red;
  }
}
```

## 分享这篇文章