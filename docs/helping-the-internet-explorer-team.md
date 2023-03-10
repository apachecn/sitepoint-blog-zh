# 帮助 Internet Explorer 团队

> 原文：<https://www.sitepoint.com/helping-the-internet-explorer-team/>

Internet Explorer 网络平台的首席项目经理克里斯·威尔逊在 IE 开发博客上发表了[文章，就 IE7 应该支持哪些标准征求人们的意见。虽然有很多“IE 不支持标准！”在网上大声疾呼，令人耳目一新的是看到这个团队确实在征求 web 开发社区希望他们做什么的意见。](http://blogs.msdn.com/ie/archive/2005/03/09/391362.aspx) [PPK 贴出了他的五大建议](http://www.quirksmode.org/blog/archives/2005/03/ie_and_standard.html)，并建议其他人也这样做。我的是:

[list = 1]
[*]DOM 事件模型。如果有必要，保留 window.event 以实现向后兼容性，但支持将事件对象传递给事件处理程序，事件对象是 DOM 事件规范、stopPropagation()和 preventDefault()中描述的对象，而不是 cancelBubble 和 returnValue。这本身会使 DOM 脚本更加简洁；这本身并不容易，因为每个人都知道您需要检查 window.event，然后从 srcElement 或诸如此类的东西中获取目标，但是事件处理程序将会丢失顶部和底部的所有跨浏览器分叉内容。这是我要解决的第一件事。
[*]在链接以外的东西上支持 CSS :hover 选择器。有很多真正酷的 CSS 专用的东西可以用这个实现(Eric 的[纯 CSS 菜单](http://www.meyerweb.com/eric/css/edge/menus/demo.html)是最简单的例子)。
[*]修复 rich text content 可编辑框生成的 HTML。这是可怕的标签汤，可以干净得多。我怀疑，这是一个相当低的水平，因为它会影响像从 IE 复制和粘贴 HTML 到 Outlook 或类似的东西，所以它可能不是严格的“web 组件”的一部分。
[*]位置:固定。求你了。
[*]扩大 bookmarklet javascript: urls 的长度限制，这样像样的 bookmarklet 就能工作了。那是我的清单。前两个是最重要的。我希望看到 CSS 的一些小问题得到解决，但本质上这只是“让它像 Firefox 一样工作”，这是一个非常不公平和无益的抱怨。我会把抱怨留给 CSS 专家，他们可以诊断具体的问题。

## 分享这篇文章