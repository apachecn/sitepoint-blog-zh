# jQuery 获取当前元素的索引

> 原文：<https://www.sitepoint.com/jquery-index-current-element/>

获取当前元素索引的 jQuery 代码段。适用于那些你一直在使用的任务，并且可以简单地放入一个函数中重用。

```
$.fn.getIndex = function(){
	  var $p=$(this).parent().children();
    return $p.index(this);
}
```

## 分享这篇文章