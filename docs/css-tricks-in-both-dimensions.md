# 两个维度的 CSS 技巧

> 原文：<https://www.sitepoint.com/css-tricks-in-both-dimensions/>

卡梅伦·亚当斯和戴夫·谢伊最近都带来了一些聪明的 CSS 新技术。

卡梅隆的技巧，名为[分辨率依赖布局](http://www.themaninblue.com/writing/perspective/2004/09/21/)，为液体与固定宽度设计的长期争论提供了一个受欢迎的新角度。标准的困境是固定宽度的设计会浪费浏览器的空间，但是流动的设计会导致过长的文本。CSS 规范以 max-width 属性的形式提供了一个解决方案，但这不受 Internet Explorer 的支持(除非聪明但无效的[表达式黑客](http://www.svendtofte.com/code/max_width_in_ie/))，并且仍然会使流畅的设计在非常宽或窄的浏览器窗口中看起来不太理想。

Cameron 的解决方案很巧妙:使用 JavaScript 来检测窗口的大小，并根据可用空间的大小来交换不同的样式表。在本例中，当浏览器窗口调整到某一宽度以下时，三列布局元素被动态切换为使用单列。

使用 JavaScript 根据页面宽度修改页面并不是一个新的想法:我以前见过它在页面槽中提供更大的广告——但是无缝地改变实际的页面布局绝对是一个有趣的事情。最重要的是，Cameron 的 JavaScript 简洁而不引人注目，应该很容易以最小的努力适应其他目的。

另一方面，戴夫·谢伊一直在垂直工作:在[最小高度:固定；](http://mezzoblue.com/archives/2004/09/16/minheight_fi/)他接手了设计元素的棘手问题，这些元素需要保持一定的高度，但当它们的内容超出它们时就会扩展。Dave 的最终解决方案是一个名副其实的 CSS 浏览器黑客之旅，包括 Windows 和 Mac 上的 IE、Opera 7 和 Safari 的变通办法！在[戴夫的评论](http://mezzoblue.com/archives/2004/09/16/minheight_fi/comments/)中明智地讨论了使用这么多黑客技术的利弊。和往常一样，我会提醒设计师在将该技术应用到实际项目之前，确保他们完全了解正在使用的黑客技术以及它们的用途。

## 分享这篇文章