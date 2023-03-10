# 轻松改进 jQuery 动画

> 原文：<https://www.sitepoint.com/easily-improving-jquery-animations/>

我们都曾使用 jQuery 的`animate()`在网页上创建漂亮的效果。然后，随着 CSS3 的引入和兴起，他们告诉我们，我们的代码是垃圾。他们建议我们放弃所有基于 jQuery 的动画(通常是基于 JavaScript 的动画)，转而使用基于 CSS 的动画。

这种变化迫使我们处理大量的浏览器兼容性问题和缺乏功能；更不用说不可能在旧版本的 Internet Explorer 上运行它们了。CSS 动画比 JavaScript 动画更快的事实证明了这种痛苦是有道理的。至少他们这样告诉我们。这是真的吗？jQuery 的`animate()`这么慢？有没有一种不用修改代码就能轻松增强的方法？答案是肯定的。

在本文中，我们将看到这两种创建动画的方法的一些局限性，以及如何使用 jQuery 代码获得更好的性能。

## jQuery 有什么问题？

我们都知道并热爱 jQuery ( [其实有些人不](http://youmightnotneedjquery.com/))。这个库，旨在简化 HTML 的客户端脚本，已经帮助了全世界成千上万的开发者。它使 HTML 文档遍历和操作、事件处理、Ajax 等事情变得轻而易举，承担了处理所有浏览器不兼容和错误的负担。

在其特性中，jQuery 还允许创建动画和效果。有了它，我们可以动画 CSS 属性，隐藏元素，淡化它们，以及其他类似的效果。然而，jQuery 的设计目标从来就不是成为一个高性能的动画引擎，也不是为了支持真正复杂的、消耗 cpu/gpu 的动画。作为这一事实的一个证明，jQuery 的内存消耗经常会触发垃圾收集，从而在执行动画时产生问题。此外，在幕后，jQuery 使用`setInterval()`而不是`requestAnimationFrame()` ( [了解更多关于`requestAnimationFrame()`](https://developer.mozilla.org/en/docs/Web/API/window.requestAnimationFrame) )来运行动画，这无助于产生高帧速率。

由于这些因素，那些“最了解”的人鼓吹使用 CSS 来创建我们的动画和效果。

## CSS 动画和过渡

先说清楚:CSS 动画胜于 jQuery 的。说到动画，jQuery 可以比 CSS 慢好几倍。CSS 动画和过渡的优势是由 GPU 进行硬件加速，这在移动像素方面非常好。这个因素似乎是一个很大的改进，尤其是在那些性能至关重要的情况下，比如移动设备。这太棒了，不是吗？事实是，这一切都有局限性和问题。

第一个限制是，并非所有的 CSS 属性都可以通过 GPU 来改进。因此，使用 CSS 动画总是会赢的假设只是错误的。另一个问题是 CSS 动画是不可移植的，至少不是在你可能瞄准的所有浏览器中。例如，过渡在 Internet Explorer 9 和更低版本中不起作用。好像这还不够，CSS 中的动画目前是基于百分比而不是时间(秒或毫秒)的。这意味着，除非你使用像 Sass 或更少的预处理器，否则在你完成动画后改变它的持续时间是一件非常痛苦的事情。最后，CSS 动画需要输入大量的厂商前缀。是的，我们可以委派一个工具来处理它们，但这只是另一件需要担心的事情。

除了前面的考虑之外，还有其他不打折扣的好理由。它们更多的是因为缺乏技能，而不是技术本身的弱点，但仍然值得一提。如果一个开发者曾经用 jQuery 制作动画，那么很有可能这个开发者*无法*使用 CSS 来完成同样的任务。也许开发人员要花很长时间才能在 CSS 中创造出同样的效果，这样的努力是得不偿失的。或者开发人员不想学习另一种技术来创建高度可定制的动画。这不是什么丢人的事。每个人在某个领域都有自己的极限。

这里的要点是，我们希望使用 jQuery 创建的动画具有更好的性能，这样我们就不必将它们转换成 CSS 动画。对我们来说幸运的是，确实存在一个解决方案。

## 改进 jQuery 的`animate()`

jQuery 的动画问题的答案叫做 [Velocity.js](https://github.com/julianshapiro/velocity) 。Velocity.js 是一个 jQuery 插件<q cite="https://github.com/julianshapiro/velocity">,它重新实现了`$.animate()`,以产生显著更高的性能(使 Velocity 也比 CSS 动画库更快),同时包括新的功能来改进动画工作流程。</q>与 jQuery 1 不同。x 适用于 IE 的旧版本，Velocity 适用于 IE8+。对于大多数项目来说，这应该不是一个大问题。

此时，您可能想知道 Velocity.js 的使用会如何影响代码库。答案是“以一种可笑的方式。”我们要集成 Velocity.js 所要做的就是下载它，并将其包含在我们想要使用的网页中。需要的最后一步是用`$.velocity()` *替换每一次出现的`$.animate()`，而*不改变任何参数！这种改变就像在我们选择的文本编辑器或 IDE 中执行搜索和替换一样简单。

一旦完成，我们的动画将在性能上有一个即时的提升。这太棒了，因为我们可以重用我们的知识，而不会对代码库产生太大影响。此外，由于它是一个维护链接能力的 jQuery 插件，我们可以继续创建 jQuery 典型的“方法调用链”。

## 结论

在本文中，我描述了一些影响基于 jQuery 的动画的问题。我们已经讨论了为什么 CSS 动画作为 jQuery 的替代品在过去的几年里如此受欢迎。然后，我强调了 CSS 的一些限制和它在性能方面的一些缺点。最后，我向您简要介绍了 Velocity.js，这是一个 jQuery 插件，它允许您在几乎不更改源代码的情况下提高 JavaScript 动画和效果的性能。

本文只是对 jQuery、CSS 和 JavaScript 动画之间的比较的介绍。如果你想深入研究这个话题，我强烈建议你阅读以下由 [GSAP](http://www.greensock.com/gsap-js/) 和 Velocity.js 作者写的文章:

*   [打破神话:CSS 动画 vs. JavaScript](http://css-tricks.com/myth-busting-css-animations-vs-javascript/)
*   [CSS 与 JS 动画:哪个更快？](http://davidwalsh.name/css-js-animation)

## 分享这篇文章