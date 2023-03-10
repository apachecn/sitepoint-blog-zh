# 使用 Web 动画 API 将页面变得生动

> 原文：<https://www.sitepoint.com/bringing-pages-to-life-web-animations-api/>

本文由客座作者杜德利·斯托里撰写。SitePoint 客座博文旨在为您带来 JavaScript 社区杰出作家和演讲者的精彩内容。

## 一个 API 来管理它们

网络上的动画长期以来被分成四个不同的阵营:

*   **[CSS 转场和动画](http://thenewcode.com/806/Web-Developer-Reading-List-CSS-Transforms-and-Animation#animation)** 非常有表现力，提供关键帧，但构建起来也很耗时，在 CSS 和 [JavaScript](https://www.sitepoint.com/css3-animation-javascript-event-handlers/) 中只提供基本的开始和结束控制。这倾向于将它们归为简单的 UI 响应动画、循环和页面加载动画。
*   **[【SMIL】](https://www.w3.org/TR/REC-smil/)(同步多媒体集成语言)**非常强大，但它也语法过多，并且不完全支持浏览器。它也仅限于控制 SVG 上下文中的元素。
*   JavaScript 提供了对元素的直接控制，但不理解关键帧或缓动等设计者友好的功能，并且缺乏 CSS 的原生优化和性能。 [Canvas API 动画](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial/Basic_animations)很精彩，但还是缺乏对动画基础知识的理解，无法动画任意 DOM 元素。
*   像[格林斯托克](https://greensock.com/)这样的 JavaScript 动画框架试图弥补 JavaScript 在动画方面的传统不足，但是却有框架的所有相关缺点:页面加载、性能和学习新语法。

Web Animations API 试图将所有这些的最佳特性集成到一个单一的统一规范中，同时消除缺点，在 JavaScript 中创建对关键帧、缓动和元素控制的本地理解，并具有与 CSS 相同的屏幕性能。随着规范的核心组件[现在在 Chrome 和 Firefox](http://caniuse.com/#feat=web-animation) 中得到支持，以及其他浏览器中已经宣布或正在进行的开发，包括 [Safari](https://webkit.org/status/#specification-web-animations) 和 Edge，再加上[强大的 polyfill](https://github.com/web-animations/web-animations-js) 的可用性，是时候认真考虑 Web 动画 API 了，让网页变得生动起来。

> Web Animations API 有助于使动画成为 Web 设计的主要内容，为供应商优化的性能和第三方工具打开了大门。—蕾切尔·纳伯斯(@ rachelnabors)[2016 年 9 月 29 日](https://twitter.com/rachelnabors/status/781563533934813184)