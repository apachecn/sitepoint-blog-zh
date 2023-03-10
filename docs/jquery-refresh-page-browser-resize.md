# 浏览器上的 jQuery 刷新页面调整大小

> 原文：<https://www.sitepoint.com/jquery-refresh-page-browser-resize/>

使用 JavaScript 在浏览器上刷新页面的基本代码片段。

```
//refresh page on browser resize
$(window).bind('resize', function(e)
{
  console.log('window resized..');
  this.location.reload(false); /* false to get page from cache */
  /* true to fetch page from server */
});
```

如果 window.location.reload()在 Firefox 中不起作用，那么尝试以下方法。

```
//refresh page on browser resize
$(window).bind('resize', function(e)
{
  if (window.RT) clearTimeout(window.RT);
  window.RT = setTimeout(function()
  {
    this.location.reload(false); /* false to get page from cache */
  }, 200);
});
```

## 分享这篇文章