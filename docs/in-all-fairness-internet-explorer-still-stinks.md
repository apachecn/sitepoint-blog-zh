# 平心而论……Internet Explorer 仍然很糟糕

> 原文：<https://www.sitepoint.com/in-all-fairness-internet-explorer-still-stinks/>

这是一个关于 SitePoint 如何试图给 Internet Explorer 一个战斗机会的故事…但它还是输了。

如果你一直在关注，你会发现[微妙的](https://www.sitepoint.com/the-great-specificity-swindle/)(和[不那么微妙的](https://www.sitepoint.com/design-the-cover-of-sitepoints-next-book/))暗示，SitePoint 一直在悄悄地进行一系列的引用，从**开始，最终的 CSS 引用**。

![position property sneak peek](img/39e0e3cf90406442d145362792dbb9ca.png)

*还没有透露(直到现在)的是，这份参考资料将不仅仅作为一本精美的 SitePoint 书籍发布，还将作为一个可以在 sitepoint.com 上免费访问的参考资料部分发布！我们这个项目的目标是为*制作*权威的 CSS 参考资料，包括网络版和印刷版。*

显然，汇编该参考资料的很大一部分是编译浏览器兼容性信息。虽然我们辛勤工作的作者可能不同意，但项目中最棘手的部分之一是确定信息应该如何呈现。

## 继承问题

一个很好的例子是`inherit`值，根据规范，所有 CSS 属性都支持该值[。一年多以前，](https://www.w3.org/TR/CSS21/cascade.html#value-def-inherit)[大卫·哈蒙德的网站](http://www.webdevout.net/browser-support-summary)对浏览器标准的符合性进行评级，在克里斯·威尔逊的博客上引起了轩然大波，当时它把缺乏对`inherit`的支持作为每个 CSS 属性反对 IE 的一个点。

我们的引用将类似地指出每个主要浏览器对每个属性的支持级别，但是我们对不支持`inherit`值的 IE 表示什么级别的支持呢？我们是把这算作 IE 对每个属性支持的失败，还是把它作为一个单独的不受支持的特性放在一边，在没有`inherit`的情况下评价 IE 对属性的支持？

一方面，当 IE 支持的一个值不起作用时，宣称它完全支持一个属性可能会被视为误导。另一方面，如果我们在 IE 中为任何属性列出的最佳支持级别是“部分”，那么你不能一眼看出 IE *何时完全支持某个属性(在其 CSS 实现的限制内)，我们的参考就变得不那么有用了。*

在与作者进行了长时间的讨论后，我们决定将`inherit`视为一个独立的不受支持的特性，并将那些如果没有`inherit`可以在 IE 中完美工作的属性列为完全受支持。投票当然不是一致的，但我觉得我们在 IE 上做了正确的事情——给了微软在 IE7 上所做的工作一个展示的机会。

## 除了…它没有

![position property compatibility table](img/bb286ea4c382974db9b36c8d35fd7dce.png)

在评价属性支持时忽略`inherit`,我们的目的是让 IE7 中许多新支持的 CSS 特性在我们的兼容性表中显示出来。

毕竟，IE7 现在跨所有元素支持`position: fixed`，完成了对该属性的支持(当然除了`inherit`)。IE7 还引入了许多其他新功能，比如支持儿童选择器(`>`)。我们天真地认为，如果我们的兼容性表能反映这一点就好了，事实证明。

一旦作者汇编了所有这些兼容性信息，我们发现争论 IE 中“部分”和“完全”支持之间的区别是一个学术练习…因为 IE 中绝大多数 CSS 功能都有太多的错误，无法评价！

在 IE7 中,`position`属性确实支持`fixed`,但是将该属性设置为除了`static`之外的任何属性都会导致浏览器错误地建立一个新的“堆叠上下文”,从而打乱重叠元素的堆叠，因此我们被迫将该属性评为“错误的”。

![child selector compatibility table](img/a840ac5d6659416e8c77cb77548d7724.png)

微软*和*在 IE7 中实现了子选择器作为一个全新的功能，但即使在这个标准的黄金时代，这个新功能也有明显的解析错误(例如`A > /* comment */ B`将无法工作)。

在绞尽脑汁寻找一个能够在 IE7 中获得“完全”支持而不会被错误困扰的 CSS 特性后，我偶然发现了 dimension 属性。`width`、`height`在 IE7 中修复了严重的 bug，IE7 增加了对`min-height`、`max-height`、`min-width`、`max-width`的支持。在我们 CSS 参考的当前草案中，这些属性在 IE7 中被列为“完全”支持！万岁！

可悲的是，一项小小的研究揭示了 IE7 中影响所有这些属性的一个 bug 的[报告](http://www.webdevout.net/browser-support-css#support-css2propsbasic)。我们还没有确认这个错误，但如果这种事情会影响这些属性的真实使用，它们也会失去“完全”评级。

## Internet Explorer 仍然很糟糕

所有这些加起来使得 ie 浏览器在我们的兼容性列表中表现很差，尽管我们尽力给它一个机会。

我们可以诚实地列出 IE 中“完全”甚至“部分”支持的 CSS 特性少之又少(`color`是一个，`font-size`不是)。即使在 IE7 中，它们中的大多数都是“错误的”……我们预计一旦我们发布参考的网络版本以征求公众意见，将会有更多的 IE 错误出现。

显然，随着 IE7 的推出，微软在纠正 IE6 中困扰开发者的最明显、最痛苦的问题方面取得了长足的进步。但是这篇参考文献揭示的不可回避的事实是，Internet Explorer 仍然落后于竞争对手。

也许是新的布局引擎和 IE 的其他改进。Next 将弥补一些差异…或者也许微软只是对修复(在 IE7 的情况下，避免)那些不明显的错误不感兴趣。

## 分享这篇文章