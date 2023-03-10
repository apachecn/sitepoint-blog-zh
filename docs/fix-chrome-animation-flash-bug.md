# 如何修复 Chrome 动画 Flash Bug

> 原文：<https://www.sitepoint.com/fix-chrome-animation-flash-bug/>

我在开发 [HTML5 和 CSS3 日历图标](/create-calendar-icon-html5-css3/)的时候出现了一个奇怪的问题。动画在 Firefox 和 IE 中看起来很棒，但当你悬停在图标上时，Chrome 和 Opera 15+会使整个屏幕变成白色。它也可能出现在 Safari 的某些版本中。

这个问题似乎会影响 Chrome 在某些操作系统和硬件组合上的运行。 [**将鼠标悬停在演示页面上的日历图标上，您看到闪光了吗？…**](http://cssdeck.com/labs/animation-flash-fix)

如果你看不到，那就认为自己很幸运。屏幕闪烁会擦除整个页面，然后在几分之一秒后重新绘制——每当你悬停在图标上时都会发生这种情况。在你问之前，设置白色背景并不能解决问题！

这个问题在 2011 年的 [Chromium bug tracker](http://code.google.com/p/chromium/issues/detail?id=87512) 上被提出。它已经关闭了一年多，但不清楚它是否真的得到了修复。我以前没有经历过，所以它可能会在某些装置中重新出现。

悬停或聚焦事件后，在动画中应用 CSS3 变换(如旋转或倾斜)时，会出现闪烁。不幸的是，这并不一致，所以我不能更具体。

幸运的是，有一个简单的——尽管有些奇怪——解决方法:

```
#element
{
	-webkit-backface-visibility: hidden;
	-webkit-transform: scale(1);
}
```

`backface-visibility`必须在元素的非变换状态下设置为隐藏。

此外，我需要应用初始转换——任何类型都可以。这可以是诸如`scale(1)`或`rotate(0deg)`之类没有明显效果的东西。或许这初始化了硬件加速或者另一个渲染特性？

其他开发人员报告说`-webkit-transform: translate3D(0, 0, 0);`或`-webkit-transform-style: preserve-3d;`也可以工作。对我来说没有，但我怀疑这取决于被动画化的页面和元素。

我很想知道你是否在 Chrome 或 Safari 中体验到动画闪光，以及修复是否有效: [**请尝试演示页面……**](http://cssdeck.com/labs/animation-flash-fix)

为一个有点不寻常的文章主题道歉，但我敢打赌，今天在某个地方的某个人会用他们的头去撞这个问题。它让你渴望简单的 IE6 修复的无忧无虑的日子！

## 分享这篇文章