# 用 Sass 居中

> 原文：<https://www.sitepoint.com/centering-with-sass/>

众所周知，在 CSS 中居中是一项单调乏味的任务。这是一种来自语言的连续插科打诨，导致笑话，如“我们设法把人送上月球，但我们不能在 CSS 中垂直对齐”。

虽然 CSS 在处理居中，尤其是垂直居中时确实有点棘手，但我觉得那些笑话有点不公平。事实上，在 CSS 中有很多方法可以使内容居中，你只需要知道如何去做。

本文并不打算解释这些方法是如何工作的，而是我们如何将它们包装在一个 Sass mixin 中，以便友好和方便地使用。因此，如果您对 CSS 居中感到有点不舒服，我可以推荐一些参考资料供您提前阅读:

*   [如何在 CSS 中居中](http://howtocenterincss.com/)
*   [CSS 居中:完整指南](https://css-tricks.com/centering-css-complete-guide/)

一切都好吗？那我们开始吧。

## 这是怎么回事？

首先，我们将重点关注元素在其父元素中的居中，因为这是绝对居中最常见的用例(模态、部分内容等)。).当你问别人关于 CSS 居中的问题时，通常得到的回答是:*你知道元素的尺寸吗？*这个问题背后的原因是，如果你不知道它们，最好的解决方案是依靠 CSS transforms。它稍微降低了对浏览器的支持，但是非常灵活。如果你不能使用 CSS 转换或者不知道元素的宽度和高度，那么很容易依赖负边距。

所以我们的 mixin 基本上是这样做的:将元素的左上角完全定位在容器的中间，然后根据尺寸是否传递给 mixin，使用 CSS 变换或负边距将其移回一半宽度和一半高度。没有维度:进行转换；尺寸:使用边距。

你可以这样使用它:

```
/**
 * Enable position context for the child
 */
.parent {
 position: relative;
}

/**
 * Absolutely center the element in its parent
 * No dimensions are passed to the mixin, so it relies on CSS transforms
 */
.child-with-unknown-dimensions {
 @include center;
}

/**
 * Absolutely center the element in its parent
 * Width is passed to the mixin, so we rely on a negative margin for the
 * horizontal axis and CSS transforms for the vertical axis
 */
.child-with-known-width {
 @include center(400px);
}

/**
 * Absolutely center the element in its parent
 * Height is passed to the mixin, so we rely on a negative margin for the
 * vertical axis and CSS transforms for the horizontal axis
 */
.child-with-known-height {
 @include center($height: 400px);
}

/**
 * Absolutely center the element in its parent
 * Width is passed to the mixin, so we rely on a negative margins for both
 * horizontal axis and vertical axis
 */
.child-with-known-dimensions {
 @include center(400px, 400px);
}
```

编译时，它应该输出以下内容:

```
§.parent {
  position: relative;
}

.child-with-unknown-dimensions {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.child-with-known-width {
  position: absolute;
  top: 50%;
  left: 50%;
  margin-left: -200px;
  width: 400px;
  transform: translateY(-50%);
}

.child-with-known-height {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translateX(-50%);
  margin-top: -200px;
  height: 400px;
}

.child-with-known-dimensions {
  position: absolute;
  top: 50%;
  left: 50%;
  margin-left: -200px;
  width: 400px;
  margin-top: -200px;
  height: 400px;
}
```

好吧，这看起来有点冗长，但是请记住，这个输出是用于演示的。在给定的情况下，你不太可能发现自己在使用它们。

## 构建混音

好吧，我们挖吧。从前面的代码片段中，我们已经知道了 mixin 的签名:它有两个可选参数，`$width`和`$height`。

```
/// Horizontal, vertical or absolute centering of element within its parent
/// If specified, this mixin will use negative margins based on element's
/// dimensions. Else, it will rely on CSS transforms which have a lesser
/// browser support but are more flexible as they are dimension-agnostic.
///
/// @author Hugo Giraudel
///
/// @param {Length | null} $width [null] - Element width
/// @param {Length | null} $height [null] - Element height
///
@mixin center($width: null, $height: null) { .. }
```

继续前进。在所有情况下，mixin 都需要使元素绝对定位，所以我们可以从这开始。

```
@mixin center($width: null, $height: null) {
 position: absolute;
 top: 50%;
 left: 50%;

  // Moar magic here...
}
```

我们必须聪明地使用我们的代码。让我们暂停一下，分析一下我们拥有的不同选项:

| 宽度 | 高度 | 解决办法 |
| --- | --- | --- |
| 不明确的 | 不明确的 | `translate` |
| 清晰的 | 清晰的 | `margin` |
| 清晰的 | 不明确的 | `margin-left` + `translateY` |
| 不明确的 | 清晰的 | `translateX` + `margin-top` |

我们就这样吧。

```
@mixin center($width: null, $height: null) {
 position: absolute;
 top: 50%;
 left: 50%;

 @if not $width and not $height {
    // Go with `translate`
  } @else if $width and $height {
    // Go width `margin`
  } @else if not $height {
    // Go with `margin-left` and `translateY`
  } @else {
    // Go with `margin-top` and `translateX`
  }
}
```

现在我们已经为 mixin 建立了框架，我们只需要用实际的 CSS 声明来填补空白。

```
@mixin center($width: null, $height: null) {
 position: absolute;
 top: 50%;
 left: 50%;

 @if not $width and not $height {
 transform: translate(-50%, -50%);
  } @else if $width and $height {
 width: $width;
 height: $height;
 margin: -($width / 2) #{0 0} -($height / 2);
  } @else if not $height {
 width: $width;
 margin-left: -($width / 2);
 transform: translateY(-50%);
  } @else {
 height: $height;
 margin-top: -($height / 2);
 transform: translateX(-50%);
  }
}
```

*注意:`#{0 0}`技巧是一个肮脏的黑客行为，以防止 Sass 的轻微侵略性缩小，这将导致`margin: mt 0 ml`而不是`margin: mt 0 0 ml`。*

到目前为止，一切顺利。

## 更进一步

我们可以做几件事来进一步推动我们的 mixin，比如在 mixin 中包含一个`@supports`规则来检查 CSS 转换支持，或者假设有(或允许) [Modernizr](http://modernizr.com/) ，并根据是否支持 CSS 转换来输出条件样式。我们还可以对参数做一些更积极的检查，以确保它们是`width`和`height`的有效值。

虽然你要问问自己走那么远是不是一件好事。mixin 的圈复杂度已经达到了 6，这对于 Sass 助手来说已经是很高的了。这仍然没问题，但是添加更多的代码可能意味着进一步增加圈复杂度。

## Flexbox 呢？

我敢肯定，你们中的一些人正跃跃欲试，想着如何使用 Flexbox 将一个元素置于其父元素的中心。事实上，这是可能的，而且如果你负担得起，这也是最简单的解决方案。

我们刚刚建立的解决方案与 Flexbox one 的主要区别在于，后者构建在父代之上，而前者主要关注子代(前提是其任何祖先的`position`与`static`不同)。

要使元素的子元素(ren)居中，只需打印三个属性。你可以为此创建一个 mixin、占位符、类或者任何你喜欢的东西。

```
@mixin center-children {
 display: flex;
 justify-content: center;
 align-items: center;
}
```

如果你添加了相关的厂商前缀(通过 mixin 或 Autoprefixer)，这个解决方案应该*可以在[的许多浏览器](http://caniuse.com/#search=flexbox)中运行*。

```
.parent {
 @include center-children;
}
```

屈服，你肯定能猜到:

```
.parent {
  display: flex;
  justify-content: center;
  align-items: center;
}
```

## 最后的想法

我们想要一个短的 mixin 来轻松地将一个元素放在它的父元素的中心；这个能完成任务，而且做得很好。它不仅足够聪明，无论元素是否有特定的维度都可以工作，而且它还提供了一个非常重要的友好且显而易见的 API。

通过查看代码，任何人都会马上明白,`@include center`行包含了一个帮助器，它执行一些逻辑，以便使元素在其父元素内居中。但是请记住，后者(或者 DOM 树中的任何父节点)必须有一个不同于`static`的位置才能工作！；)

你可以在萨斯梅斯特上玩代码:[http://sassmeister.com/gist/550809f5aa00b73d932c](http://sassmeister.com/gist/550809f5aa00b73d932c)。

## 分享这篇文章