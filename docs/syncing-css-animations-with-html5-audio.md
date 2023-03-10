# 将 CSS 动画与 HTML5 音频同步

> 原文：<https://www.sitepoint.com/syncing-css-animations-with-html5-audio/>

CSS 和 HTML 为向您的网页、web 应用程序和电子书项目添加多媒体内容打开了一个丰富的竞技场。结合这两种技术的一种创新方法是使用`<audio>`元素向 CSS 动画添加声音效果，并用一点 JavaScript 触发它们。

虽然网络上的声音并不普遍受欢迎，但也有一些情况下，它可以丰富用户体验，而不会成为不必要的烦恼。例如艺术家和游戏网站或儿童内容。在某些情况下，声音甚至对残疾游客有用。

声音动画过程中最重要的部分是根据音频调整 CSS 动画，以获得准确同步的视听体验。在本文中，我将使用一个有趣的程式化心脏跳动的例子来介绍将动画关键帧与音频定时数据同步的步骤。

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看带声音的笔 [CSS 动画:心跳](http://codepen.io/SitePoint/pen/bVEmLO/)。