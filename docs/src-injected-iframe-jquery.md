# 用 jQuery 获取注入 iFrame 的 Src

> 原文：<https://www.sitepoint.com/src-injected-iframe-jquery/>

简单的 jQuery 代码片段，通过简单地获取“src”参数的值来获取注入的 iframe 的 src(即 url)。从这里，您可以访问 src 中的参数。

另请参见:

*   [检查窗口是否在 iframe 中](http://www.jquery4u.com/snippets/jquery-check-window-iframe/)
*   [如何重新加载 iframe](http://www.jquery4u.com/dom-modification/jquery-reload-iframe/)

```
//selector for injected iframe
var iframeSrc = $('#iframe').attr('src');
```

## 分享这篇文章