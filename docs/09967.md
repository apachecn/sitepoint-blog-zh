# jQuery 检查元素是否可见/隐藏

> 原文：<https://www.sitepoint.com/jquery-check-element-visiblehidden/>

检查 DOM 中的元素是否对用户隐藏的 jQuery 代码片段。这在确定切换元素的状态时很有用。

```
var isVisible = $('#myDiv').is(':visible');
var isHidden = $('#myDiv').is(':hidden');
alert(isVisible);
alert(isHidden);
```

如果您只是基于元素的可见性对其进行操作，只需在选择器表达式中包含“:visible”或“:hidden”即可。例如:

```
$('#myDiv:visible').animate({left: '+=200px'}, 'slow');
```

## 分享这篇文章