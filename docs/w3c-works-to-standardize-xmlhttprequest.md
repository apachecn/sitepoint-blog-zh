# W3C 致力于标准化 XMLHttpRequest

> 原文：<https://www.sitepoint.com/w3c-works-to-standardize-xmlhttprequest/>

[万维网联盟](https://www.w3.org/)的(W3C)新 [Web API 工作组](https://www.w3.org/2006/webapi/)发布了一份官方规范的[工作草案](https://www.w3.org/TR/XMLHttpRequest/)，该规范是 AJAX 的核心。

`XMLHttpRequest`对象使 JavaScript 代码能够向服务器发出请求并处理响应，目前大多数浏览器都支持该对象，包括 Mozilla/Firefox、Safari、Opera 和即将推出的 Internet Explorer 7。Internet Explorer 5/6 通过同名的 ActiveX 控件提供了`XMLHttpRequest`的功能。

根据草案，规范的第一个版本旨在记录目前唯一可用的跨浏览器功能。仅存在于一个浏览器中的功能将不包括在规范中，任何新功能也不包括在规范中。在少数情况下，不同的浏览器会对特定的特性如何工作有不同的意见，规范会描述“最正确的”行为，这是由规范的作者决定的。对于网络开发人员来说，结果应该是他们可以依赖和使用的有用文档。与此同时，该文档将提供一个目标，使新的实现能够确保互操作性。

在这个 W3C 规范之前，唯一一个关于`XMLHttpRequest`应该做什么的厂商中立的规范是在 Web 超文本应用技术工作组(WHAT-WG) [Web 应用 1.0](http://www.whatwg.org/specs/web-apps/current-work/#scripted-http) 工作草案规范中找到的，它包含了大量的歧义和问题。W3C 的规范在很大程度上基于 WHAT-WG 的工作，它清除了许多这样的问题，通常是开发人员更坚实的基础。

## 分享这篇文章