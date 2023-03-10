# 使用 CSS 和 JavaScript 的逐帧动画教程

> 原文：<https://www.sitepoint.com/frame-by-frame-animation-css-javascript/>

![Frame by frame animation with HTML, CSS and JavaScript](img/41a1779e157585ef871d7e4d263ded54.png)

*这篇文章由[朱利奥·迈纳迪](https://www.sitepoint.com/author/gmainardi/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

想象一下，你的设计师让你为你一直在做的令人敬畏的项目实现一个漂亮的逐帧动画。作为该项目的前端开发人员，不仅要由您来设计一个可用的动画，还要交付一个流畅、高性能、可维护的逐帧动画，它可以在不同的浏览器上运行，包括桌面和移动设备。

本教程向您展示了使用 HTML、CSS 和 JavaScript 创建这种类型的动画的各种方法，同时在每次迭代中进行改进，以获得项目的最佳结果。

## 什么是逐帧动画？

根据 Adobe 的这个[定义，一帧一帧的动画:](https://helpx.adobe.com/animate/using/frame-by-frame-animation.html)

> …在每一帧中更改舞台的内容。它最适合于复杂的动画，其中图像在每一帧都发生变化，而不是简单地在舞台上移动。

换句话说，动画的主题是在一组图像中表示的。该组中的每个图像占据一个动画帧，并且每个帧替换下一个帧的变化率会在图像中产生运动的错觉。

我将演示整个工作流程，因为您将在蔡司网站上制作这个伟大的眨眼动画。

这是您将用来构建动画帧的一组图像:

![Blinking eye sprite for frame by frame animation](img/f56650e9c1c3f2fcbce817985fac56ac.png)

这是最后的结果:

![Blinking eye frame by frame animation](img/c490f22e7e82d75da904230e7046c521.png)

对于本教程，我选择使用 [SVG 图像](https://www.sitepoint.com/svg-101-what-is-svg/),因为它们非常适合在不同的屏幕尺寸下进行响应性网页设计。但是，如果出于某种原因不想使用 SVG 图形，可以创建 PNG、JPEG 和 GIF 图像格式的 web 动画，或者使用 HTML5 Canvas。我会在文章的最后分享我对这些替代方案的想法。

为了简单起见，在本教程中，您将使用 [jQuery](http://jquery.com/) 库并启动和运行 [Autoprefixer](https://github.com/postcss/autoprefixer) ，因此代码将没有特定于浏览器的 CSS 前缀。

现在，让我们做一些编码！

## 1-通过更改图像源来实现逐帧动画

第一个选项非常简单，这也是我喜欢它的原因之一。

在您的 HTML 文档中，创建一个`img`元素，它充当图像的容器，每次一个，因为每个动画帧都被下一个替换:

```
<img class="eye-animation"
  srcimg/Eye-1.svg" 
  alt="blinking eye animation"/>
```

CSS:

```
.eye-animation {
  width: 300px;
}
```

下一步是在一段时间内动态地用下一个图像替换现有的图像，从而产生运动的错觉。

你可以用`setTimeout`来做，但是[最好用`requestAnimationFrame`](https://www.sitepoint.com/watch-performance-with-requestanimationframe/) 。从性能的角度来看，这样做有一些好处。例如:

*   屏幕上其他动画的质量不会受到影响
*   如果用户导航到另一个选项卡，浏览器将停止播放动画。

下面是 jQuery 代码:

```
const $element = $('.eye-animation');
const imagePath = '/images';
const totalFrames = 18;
const animationDuration = 1300;
const timePerFrame = animationDuration / totalFrames;
let timeWhenLastUpdate;
let timeFromLastUpdate;
let frameNumber = 1;

function step(startTime) {
  if (!timeWhenLastUpdate) timeWhenLastUpdate = startTime;

  timeFromLastUpdate = startTime - timeWhenLastUpdate;

  if (timeFromLastUpdate > timePerFrame) {
    $element.attr('src', imagePath + `/Eye-${frameNumber}.svg`);
    timeWhenLastUpdate = startTime;

    if (frameNumber >= totalFrames) {
      frameNumber = 1;
    } else {
       frameNumber = frameNumber + 1;
    }        
  }

  requestAnimationFrame(step);
}
```

当页面加载时，您第一次调用`requestAnimationFrame`并将`step`函数传递给它，同时将动画的`startTime`参数内置到`requestAnimationFrame`函数中。

在每一步中，代码检查从最后一次图像源更新已经过去了多长时间，如果超过了每帧所需的时间，则刷新图像。

因为您正在构建一个无限的动画，上面的代码检查它是否到达了最后一帧，如果是，它将把`frameNumber`重置为 1；如果不是，它将把`frameNumber`加 1。

图像应具有相同的名称结构，由递增的数字系列和相同的位置组成(例如，images/Eye-1.svg、images/Eye-2.svg、images/Eye-3.svg 等)。)以便代码可以轻松地遍历它们。

最后再次调用`requestAnimationFrame`继续整个过程。

看起来不错。然而，如果你尝试这样做，你会发现它不起作用，因为当动画开始时，只有第一个图像被加载。这是因为浏览器不知道您想要显示的图像，直到代码在动画循环中更新图像元素的`src`属性。为了一个平滑的动画，你需要在循环开始前**预加载图像**。

有不同的方法可以做到这一点。这是我最喜欢的一个。它包括添加隐藏的`div`并设置它们的`background-image`属性指向所需的图像。

jQuery 代码:

```
$(document).ready(() => {
  for (var i = 1; i < totalFrames + 1; i++) {
    $('body').append(`<div id="preload-image-${i}" style="background-image: url('${imagePath}/Eye-${i}.svg');"></div>`);
  }
});
```

这是 CodePen 上完整的工作演示:

参见 [CodePen](https://codepen.io) 上的笔[逐帧-动画-1-来源](https://codepen.io/SitePoint/pen/ayBYKx/)by site point([@ site point](https://codepen.io/SitePoint))。