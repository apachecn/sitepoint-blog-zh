# 本地缓存提取的 AJAX 请求:包装提取 API

> 原文：<https://www.sitepoint.com/cache-fetched-ajax-requests/>

*本文由客座作者**彼得·本特森**撰写。SitePoint 客座博文旨在为您带来 JavaScript 社区杰出作家和演讲者的精彩内容*

**这篇文章演示了如何实现获取请求的本地缓存**,这样如果重复执行，它就会从会话存储中读取。这样做的好处是，您不需要为每个想要缓存的资源定制代码。

如果您想在下一次 JavaScript 晚宴上看起来很酷的话，请跟随我们，在那里您可以展示各种各样的杂耍承诺技巧、最先进的 API 和本地存储。

## 获取 API

至此，希望你已经熟悉了[获取](https://www.sitepoint.com/introduction-to-the-fetch-api/)。这是浏览器中一个新的本地 API，用来取代旧的`XMLHttpRequest` API。

我能使用 fetch 吗？caniuse.com 各大浏览器支持抓取功能的数据。