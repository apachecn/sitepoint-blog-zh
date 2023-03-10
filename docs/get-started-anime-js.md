# 如何用 Anime.js 制作网页动画

> 原文：<https://www.sitepoint.com/get-started-anime-js/>

有许多 JavaScript 动画库，但 [Anime.js](https://animejs.com/) 是其中最好的一个。它很容易使用，有一个小而简单的 API，并提供你想要的现代动画引擎的一切。该库文件较小，支持所有现代浏览器，包括 IE/Edge 11+。

唯一能让你立刻停止使用 Anime.js 的是它最小的、禅宗式的文档。我喜欢它所采用的紧凑、结构化、优雅的方法，但我认为更详细的解释会有所帮助。我将在本教程中尝试解决这个问题。

## Anime.js 入门

首先，下载并在 HTML 页面中包含`anime.js`文件:

```
<script src="path/to/anime.min.js"></script> 
```

或者，您可以使用 CDN 上托管的最新版本的库:

```
<script src="https://cdn.jsdelivr.net/npm/animejs@3.0.1/lib/anime.min.js"></script> 
```

现在，为了创建一个动画，我们使用了`anime()`函数，它将一个对象作为参数。在那个对象中，我们描述了所有的动画细节。

```
let myAnimation = anime({
  /* describe the animation details */
}); 
```

有几种属性用于描述动画。它们分为四个不同的类别:

*   **目标**–这包括对我们想要制作动画的元素的引用。它可以是 CSS 选择器(`div`、`#square`、`.rectangle`)、DOM 节点或节点列表，或者普通的 JavaScript 对象。还可以选择在一个阵列中混合使用上述方法。
*   **Properties**——这包括在处理 CSS、JavaScript 对象、DOM 和 SVG 时可以动画化的所有属性和特性。
*   **属性参数**——包括`duration`、`delay`、`easing`等与属性相关的参数。
*   **动画参数**——包括`direction`、`loop`等动画相关参数。

现在让我们看看这在实践中是如何应用的。考虑下面的例子:

```
let animation = anime({
  targets: 'div',
  // Properties 
  translateX: 100,
  borderRadius: 50,
  // Property Parameters
  duration: 2000,
  easing: 'linear',
  // Animation Parameters
  direction: 'alternate'
}); 
```

参见 [CodePen](https://codepen.io) 上的笔 [AnimeJS:基础示例](https://codepen.io/SitePoint/pen/XvEMbV/)by SitePoint([@ SitePoint](https://codepen.io/SitePoint))
。