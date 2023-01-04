# jQuery 检查窗口是否在 iFrame 中

> 原文：<https://www.sitepoint.com/jquery-check-window-iframe/>

检查窗口是否在 iFrame 中的简单 JavaScript 代码片段。对于一些可能会使用页面地址栏中的 URL 的功能来说非常有用。

就这么简单。

```
var isInIframe = (window.location != window.parent.location) ? true : false;
```

另请参见:

*   [获取注入的 iframe 的源 href](http://www.jquery4u.com/snippets/src-injected-iframe-jquery/)
*   [如何重新加载 iframe](http://www.jquery4u.com/dom-modification/jquery-reload-iframe/)

## 分享这篇文章