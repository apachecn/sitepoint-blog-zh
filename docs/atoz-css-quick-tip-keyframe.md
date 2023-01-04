# AtoZ CSS 快速提示:关键帧动画

> 原文：<https://www.sitepoint.com/atoz-css-quick-tip-keyframe/>

本文是我们 AtoZ CSS 系列的一部分。你可以在这里找到系列[的其他条目。
查看关键帧动画的完整截屏和文字记录](https://www.sitepoint.com/blog/)[点击这里](https://www.sitepoint.com/atoz-css-screencast-keyframe)。

欢迎来到我们的 AtoZ CSS 系列！在这个系列中，我将探索不同的 CSS 值(和属性),每个值都以字母表中不同的字母开始。我们知道有时仅仅截屏是不够的，在这篇文章中，我为你添加了一个新的关于关键帧动画的快速技巧/课程。

![k1b-01](img/f35cb95a4c1a7fa11d7801c3775ab729.png)

## k 代表`@keyframes`

在本周的技巧中，我们来看看处理厂商前缀的方法和一个将你的`@keyframes`重构为更简洁格式的快速技巧。

在关于字母 k 的[视频](https://www.sitepoint.com/atoz-css-screencast-keyframe)中，我们讨论了很多关于`@keyframes`的内容。这里有一些额外的提示，你可能会发现在处理 CSS 动画(以及任何当前或未来的实验属性)时很有用。

### 提示 1

为动画设置关键帧时，动画的某些点共享特定属性的相同值是很常见的。您可以将它们组合成一个逗号分隔的列表，而不是一个很长的关键帧列表，就像共享相同属性和值的选择器一样。

```
@keyframes pulse {
  0%, 50% {font-size: 10px;}
  25%, 100% {font-size: 20px;}
}

/* just like */
h1, h2, h3 {
  font-family: 'Avenir', sans-serif;
}
```

### 提示 2

对 CSS 动画的支持非常好——在编写[的时候，全球支持是 89.5%](http://www.caniuse.com/animation) ，所有支持的浏览器都有`@keyframes`和`animation`前缀。

然而，为了确保向后兼容 Safari 8 和 iOS 8.4 及更低版本，你需要`-webkit`供应商前缀来确保你的动画实际播放。手动添加这些会使您的代码变得臃肿和难以维护，因为每一个微小的变化都需要在多个地方进行复制。这很容易出现人为错误，这绝不是一件好事。

有两种方法可以让你的代码保持整洁，并且避免重复的手工任务。

你可以使用由 [Lea Verou](http://www.twitter.com/leaverou) 开发的[无前缀 JavaScript 库](http://leaverou.github.io/prefixfree/)。这将为您处理所有前缀，因此您只需编写不带前缀的代码，脚本将在浏览器中运行，并根据对当前使用的浏览器功能的支持，为您注入所有必要的前缀。

另一种选择是使用[自动修复程序](https://github.com/postcss/autoprefixer)。这是一个 CSS 后处理器，在你写完无前缀代码后运行——通常作为自动任务运行器的一部分，比如 [Grunt](https://gruntjs.com/) 或 [Gulp](http://www.gulpjs.com) 。Autoprefixer 使用来自[can use](http://caniuse.com)的信息来决定需要什么前缀。

我个人使用 Autoprefixer 完成吞咽任务，已经几个月没有写厂商前缀了。这是一个加快工作流程的好技巧，适用于所有 CSS 属性，而不仅仅是`@keyframes`！

## 分享这篇文章