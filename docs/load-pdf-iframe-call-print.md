# 将 PDF 加载到 iframe 中并调用 print

> 原文：<https://www.sitepoint.com/load-pdf-iframe-call-print/>

将 PDF 加载到 iframe 并调用 print 的代码片段。另见: [10 JQUERY 打印页面选项](http://www.jquery4u.com/plugins/10-jquery-print-page-options/)。

```
jQuery(document).ready(function($) {
  function print(url)
  {
      var _this = this,
          iframeId = 'iframeprint',
          $iframe = $('iframe#iframeprint');
      $iframe.attr('src', url);

      $iframe.load(function() {
          _this.callPrint(iframeId);
      });
  }

  //initiates print once content has been loaded into iframe
  function callPrint(iframeId) {
      var PDF = document.getElementById(iframeId);
      PDF.focus();
      PDF.contentWindow.print();
  }
});
```

## 分享这篇文章