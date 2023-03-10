# jQuery 检查元素是否存在

> 原文：<https://www.sitepoint.com/jquery-check-element-exists/>

用于检查当前网页上是否存在 html 元素的 jQuery 代码片段。我发现最简单的方法是检查一个对象的长度，看看它是否存在于 DOM 中。

```
//check if an element exists by using length
if ($("#id").length) {
  //it does!
}

//or length equals zero
$('element').length == 0; // no element found

//or using plain javascript
document.getElementById('eid') != null)
```

## 时髦的 jQuery 函数，先生？

```
jQuery.fn.exists = function(){return jQuery(this).length>0;}

if ($(selector).exists()) {
    // Do something
}
```

**或者检查 jQuery 对象数组的存在:**

```
if ( $('#myDiv')[0] ) { //do something }
```

## 分享这篇文章