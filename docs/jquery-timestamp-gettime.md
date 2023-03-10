# jQuery Get Timestamp getTime()示例

> 原文：<https://www.sitepoint.com/jquery-timestamp-gettime/>

简单的 jQuery 代码片段，用于获取事件发生后的时间。用于跟踪用户与页面元素交互的时间。

```
var last, diff;
$('div').click(function(event) {
  if ( last ) {
    diff = event.timeStamp - last
    $('div').append('time since last event: ' + diff + '
');
  } else {
    $('div').append('Click again.
');
  }
  last = event.timeStamp;
});
```

或者只是

```
new Date().getTime()
```

**不要忘记**将 jQuery 代码放在文档就绪函数中。参见 [4 个不同的 jQuery 文档就绪示例](http://www.jquery4u.com/dom-modification/types-document-ready/)了解更多信息。

另见:[用 jQuery 改变 CSS](http://www.jquery4u.com/dynamic-css-2/change-css-jquery/)

[来源](http://api.jquery.com/event.timeStamp/)

## 分享这篇文章