# 如何提高 Rails: the stale 的性能？方法

> 原文：<https://www.sitepoint.com/how-to-increase-performance-in-rails/>

SitePoint 的开发者马尔·柯蒂斯(Mal Curtis)最后一次出现在“与专家对话”节目中，被问及 Ruby on Rails 应用程序的性能问题以及提高速度的方法。他的一个建议是用*变味？* method，Rails 中一个有时未被充分利用的功能，它开箱即用，在自动减少感知延迟方面做得非常好。

总而言之，*变味了？方法防止在不必要的时候呈现整个模板。Rails 和浏览器会为您正确处理一切，因为返回的是状态 304(未修改),而不是正常状态 200。*

Mal 把下面的视频放在一起，在 3 分钟内展示如何使用*变味？在 Ruby on Rails 应用程序上提高速度的最基本形式的方法。很棒的建议。*

[youtube fzp14eDwJns]

## 分享这篇文章