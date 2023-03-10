# jQuery 重新加载 iFrame

> 原文：<https://www.sitepoint.com/jquery-reload-iframe/>

通过访问 contentWindow 属性获取 location 对象来重新加载 iFrame 的简单 jQuery 代码片段。jQuery 代码片段实际上遍历并刷新页面上的所有 iFrames。

另请参见:

*   [检查窗口是否在 iframe 中](http://www.jquery4u.com/snippets/jquery-check-window-iframe/)
*   [获取注入的 iframe 的源 href](http://www.jquery4u.com/snippets/src-injected-iframe-jquery/)

```
//reload 1 iframe
$('#iframe').contentWindow.location.reload(true);

//reload all iFrames
$('iframe').each(function() {
  this.contentWindow.location.reload(true);
});
```

## 分享这篇文章