# CSS 三角形的 Sass 混合

> 原文：<https://www.sitepoint.com/sass-mixin-css-triangles/>

有一个非常流行的 CSS 技巧，就是在一个宽度为 0，高度为 0 的元素上混合使用实心和透明的边框，来制造一个三角形。如果您还不了解这项技术，您可以在以下资源中找到更多相关信息:

*   [CSS 三角在 CSS-Tricks](http://css-tricks.com/snippets/css/css-triangle/)
*   大卫·沃尔什的 CSS 三角形
*   [CSS 三角形生成器](http://apps.eky.hk/css-triangle-generator/)
*   [CSS 箭头拜托！](http://cssarrowplease.com/)(CSS 三角形生成器)

虽然这种技术有一些奇怪之处，但它实际上很好地完成了工作，并且具有在我们必须支持的各种浏览器之间非常兼容的优点。

现在的问题是，我从来没有完全记住如何使用这一小段代码。哪些边框需要透明？哪个应该是实心的？我只是不明白这一点，我敢打赌，这对你们很多人来说也是一样的。因此，这通常是您希望使用 Sass 实现自动化的事情。

大概有多少 CSS 三角 Sass 混合就有多少 Sass 开发者。如果我向你展示我自己的 mixin 来处理 CSS 中的 CSS 三角形/箭头会怎么样？

## 我们需要什么？

在深入研究代码之前，最好检查一下我们需要什么来用 CSS 制作一个三角形。第一:**一个方向**。我们需要为箭头定义一个方向，可以是`top`、`right`、`bottom`或`left`。还有，**一个位置**呢？比如距离顶部 1.5em 的*，距离左侧 100%的*。我们也可以定义**一个颜色**，也许**一个尺寸**，尽管这两个参数可以有默认值。

所以最后，我们的 mixin 是一个简短的方法，我们说*生成一个指向这个方向的三角形，在这个点，用这个颜色，在下面的维度*。听起来不错，对吧？

注意，为了避免使用额外的标记，我喜欢对三角形使用伪元素。例如:

```
.element {
  /* Container of some kind */

  &::before {
    /* Including triangle mixin */
  }
}
```

## 使用我们的工具箱

上周，我写了一些[函数和 mixins 来启动你的项目](https://www.sitepoint.com/sass-mixins-kickstart-project/)。如果你还没有读那篇文章，请继续。我会等的。

好了，完成了？如果我们重复使用一些混音来使这个三角形混音尽可能的清晰和干净，会怎么样？例如，我们可以使用`size()` mixin，这样我们就不必同时定义宽度和高度。我们也可以保持一个一致的 API 来定位东西，并利用`absolute()` mixin(更多关于 mixin [的信息，请点击](http://hugogiraudel.com/2013/08/05/offsets-sass-mixin/))。

为了在这篇文章中包含所有的代码，请允许我在这里也包含 3 个混合:

```
// Sizing stuff
@mixin size($width, $height: $width) {
      width: $width;
      height: $height;
}

// Positioning stuff
@mixin position($position, $args) {
  @each $o in top right bottom left {
        $i: index($args, $o);

    @if $i and $i + 1 <;= length($args) and type-of(nth($args, $i + 1)) == number  {
          #{$o}: nth($args, $i + 1);
    }
  }

  position: $position;
}

// Absolutely positioning stuff
@mixin absolute($args) {
        @include position(absolute, $args);
}
```

此外，我们将需要`opposite-direction()`函数；要么是来自[指南针](http://compass-style.org/reference/compass/helpers/constants/)的，要么是我在之前的帖子中写的。

## 构建混音

现在我们有了所有需要的工具，让我们开始创建我们的三角形混合。首先，让我们创建 mixin 的骨架。

### 混音签名

这是我们的 mixin 的样子:

```
@mixin triangle(
  $direction,
      $position,
  $color: currentColor, 
      $size: 1em
) {
  /* Mixin content */
}
```

方向和位置都是非可选参数。同时颜色将默认为 [`currentColor`](https://www.w3.org/TR/css3-color/#currentcolor) CSS 值，这总比回落到黑色好。关于大小，因为我项目中的所有三角形都被标准化为具有相同的大小，所以我将大小设为可选参数，但如果你愿意，可以随意将其设为非可选参数。

对于那些喜欢非常正式的签名语法的人来说，下面是它的样子:

```
triangle(string $direction, list $position, color $color: currentColor, number $size: 1em)
```

### Mixin 核心

让我们先处理最简单的部分:mixin 核心。换句话说，三角形的尺寸和位置。我们以后会处理边界的。

```
@mixin triangle($direction, $position, $color: currentcolor, $size: 1em) {
  @include absolute($position);
  @include size(0);
  content: '';
  z-index: 2;

  /* Border stuff */
}
```

很干净，不是吗？它是这样做的:

1.  通过`absolute()` mixin 定位元素。
2.  使得元素`0`宽度，`0`高度。
3.  如果它是伪元素(几乎总是这样)，使用`content`属性使它出现。
4.  使用`z-index`来确保它在默认层之上(如果你愿意，我想你可以移除它)。

### 边界

好了，我们完成了最简单的部分。主要是因为我们有减轻疼痛的定制混音。现在我们需要处理所有的边界。这就是`opposite-direction`发挥作用的地方。用一个例子，让我们回忆一下这是如何工作的。假设我们想要定义一个指向*右边*的三角形:

*   不应定义右边框
*   左边框应该是彩色的
*   上边框应该透明
*   底部边框应该透明

这导致了下面的代码:

```
@mixin triangle($direction, $position, $color: currentcolor, $size: 1em) {
  /* Core stuff */

  border-#{opposite-position($direction)}: $size * 1.5 solid $color;
      $perpendicular-border: $size solid transparent;

  @if $direction == top or $direction == bottom {
    border-left:   $perpendicular-border;
        border-right:  $perpendicular-border;
  }

  @else if $direction == right or $direction == left {
    border-bottom: $perpendicular-border;
        border-top:    $perpendicular-border;
  }
}
```

首先，我们定义相反的边界。为此，我们使用了`opposite-direction()`函数。在我们之前的例子中，`opposite-direction`将返回`left`，因此属性将是`border-left`。关于尺寸，我想到稍微增加这个边框的尺寸会更好看。但那是相当武断的，所以如果你不喜欢它，请随时更新它。

最后，我们定义垂直边界。如果方向是`top`或`bottom`，则边界为`border-left`和`border-right`，如果方向是`left`或`right`，则垂直边界为`border-top`和`border-bottom`。两边都应该是透明的。

## 错误处理

和往常一样，我们不应该忘记处理 mixin 中的错误。为了保持混音简单，我确保了方向是正确的。也就是说，它必须是 4 种常见偏移之一。我认为进行这种检查很重要，因为人们可以决定传递一个角度(例如`42deg`)，就像对`linear-gradient()`函数所做的那样。

```
@mixin triangle($direction, $position, $color: currentcolor, $size: 1em) {
  @if not index(top right bottom left, $direction) {
    @warn "Direction must be one of `top`, `right`, `bottom` or `left`; currently `#{$direction}`.";
  }

  @else {
    /* Mixin content */
  }
}
```

注意我们如何使用`index()`功能来查看方向是否是 4 个偏移之一，而不是用`==`手动检查每个偏移。

在 Sass 3.3 环境中，我们可以通过首先将方向小写来使我们的函数更加可靠。这将防止在方向不完全小写的情况下出现错误。细节很重要。

我最近写了关于如何同时支持多个版本的 Sass 的文章。我强烈建议您阅读这篇文章，但基本上看起来是这样的:

```
@mixin triangle($direction, $position, $color: currentcolor, $size: 1em) {
  $direction: if(function-exists("to-lower-case") == true, to-lower-case($direction), $direction);

  @if not index(top right bottom left, $direction) {
    @warn "Direction must be one of `top`, `right`, `bottom` or `left`; currently `#{$direction}`.";
  }

  @else {
    /* Mixin content */
  }
}
```

关于位置，应该是`position()` mixin 处理错误处理的工作，而不是`triangle()` mixin。关于颜色和大小，我们可能会检查数据类型，但我觉得检查位置对于这个演示来说已经足够了。

## 用法和示例

使用它非常简单。但是请记住，因为包含三角形 mixin 的元素是绝对定位的，所以它的父元素应该有`position: relative`。

```
/**
 * 1\. Enable absolute positioning for pseudo-element
 * 2\. Using a pseudo-element to generate the arrow
 * 3\. Same as @include triangle(bottom,top 100% left 1em, $color);
     */
    .tooltip { 
      $color: #3498db;

  position: relative; /* 1 */

  background: $color;
  padding: .5em;
  border-radius: .15em;
  color: white;
  text-align: center;

  &::before { /* 2 */
    @include triangle( 
      $direction : bottom, 
          $position  : top 100% left 1em, 
      $color     : $color
    ); /* 3 */
  }
}
```

这里有一个现场演示供您使用:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [Jabzj](http://codepen.io/SitePoint/pen/Jabzj/) 。