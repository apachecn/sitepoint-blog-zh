# jQuery 检查 Div 是否滚动到末尾

> 原文：<https://www.sitepoint.com/jquery-check-div-scrolled/>

简单的 jQuery 代码片段，用于检查是否已经滚动到一个 div 的末尾并引发了一个事件。当您有一个内联元素(overflow:scroll)想要知道是否有人向下滚动到底部时，这很有用。可用于检查某人是否阅读了注册表单上的条款和条件。

```
$(document).ready(function(){

    $('div').bind('scroll',chk_scroll);
});

function chk_scroll(e)
{
    var elem = $(e.currentTarget);
    if (elem[0].scrollHeight - elem.scrollTop() == elem.outerHeight())
    {
        console.log("bottom");
    }

}
```

**其他方式:**

```
//scrollTop refers to the top of the scroll position, which will be scrollHeight - offsetHeight
if( obj.scrollTop == (obj.scrollHeight - obj.offsetHeight)) { }
```

## 分享这篇文章