# 提高你的响应式网页设计方法的 7 种方法

> 原文：<https://www.sitepoint.com/7-responsive-web-design-tips/>

如果你的用户[要求在桌面和移动布局之间切换](/disable-responsive-design-option/)，也许你的响应式网页设计没有你希望的那么成功。在你追随 RWD 潮流之前，这里有七个考虑因素…

## 1.为什么要使用响应式网页设计？

RWD 是一个便宜的选择；您可以重新调整现有桌面布局，以确保它在较小的移动设备上也能正常工作。如果你正在开发一个新网站，有几个理由不使用 RWD。

然而，你考虑 RWD 是因为它是一种实现一些商业战略目标的廉价方式吗？你当然可以在几个小时内实现一个移动布局，但这有用吗？当一个响应式设计作为事后想法被添加时，这通常是显而易见的。坚持一个好的桌面布局可能比破坏你的在线形象更好。

## 2.考虑上下文，但是…

…避免刻板印象。

历史上，设计师认为桌面用户是静态的，智能手机用户是漫游的。火车公司通常会在桌面设备上突出显示购票，在移动设备上突出显示车站方向。这种方法有几个问题:

*   界限变得模糊了。人们使用任何实用的设备，例如，他们可以一边看电视一边用手机上网。
*   RWD 没有透露任何背景。设备分辨率是一个粗略的假设，尤其是当你考虑到现代手机和平板电脑提供的高密度显示屏时。

如果静态用户和漫游用户具有不同的上下文，则提供具有专用功能的单独的移动站点或应用可能更好。

## 3.提炼你的内容

为什么人们会访问你的网站？对那些用户来说什么是重要的？他们典型的旅程是怎样的？如果没有这些答案，你会很想把一切都放在主页上。这在大显示器上可能是可行的，但当切换到移动布局时，你会很难做到。

最佳选择:*先考虑小屏*，然后…

## 4.向上设计

一旦有了最小实用屏幕的设计，就可以在一系列媒体查询断点中向上工作。

虽然没有严格的规则，但断点步长不应太大，可能不超过 200 个像素。然而，每个步骤不必是主要的回流，例如额外的柱；这可能是字体大小的变化，浮动图像或另一个简单的调整。

## 5.保持流畅

理想情况下，你的设计应该在断点之间是流动的——而不是固定的。虽然固定布局更容易理解和编码，但对 RWD 来说太脆弱了(参考 [*你的响应式网页设计太脆弱了吗？*](/responsive-web-design-too-fragile/) )

理想情况下，即使您的媒体查询也应该使用比例单位，如%，em 和 rem，而不是像素。

## 6.绝不妥协

RWD 永远不应该被用来提供一个淡化版本的网站。在某些情况下，您应该考虑移动设备上的附加设施，如地理位置和离线支持。保持简单易用:

*   不要使用浏览器嗅探
*   不要禁用缩放
*   不要隐藏内容。

## 7.只有性能才重要

实施 RWD 可能成本低廉，但应该从一开始就计划好。将 RWD 应用到一个提供 1Mb 以上页面的站点上，永远不会在移动网络上的设备上产生最佳体验。

响应式网页设计是近年来出现的最重要的技术之一。但是，滥用 RWD 可能比根本不提供它更糟糕。

## 分享这篇文章