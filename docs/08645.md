# jQuery 检查元素是否在视图中

> 原文：<https://www.sitepoint.com/jquery-check-element-view/>

更多的 jQuery 代码片段来检查元素是否在视图中。


```
function isScrolledIntoView(elem)
{
    var docViewTop = $(window).scrollTop();
    var docViewBottom = docViewTop + $(window).height();

    var elemTop = $(elem).offset().top;
    var elemBottom = elemTop + $(elem).height();

    return ((elemBottom < = docViewBottom) && (elemTop >= docViewTop));
}

isInView: function(elem)
{
    var docViewTop = $(window).scrollTop(),
        docViewBottom = docViewTop + $(window).height(),
        elemTop = $(elem).offset().top,
        elemBottom = elemTop + $(elem).height();
    return ((elemBottom < = docViewBottom) && (elemTop >= docViewTop));
}
```

到目前为止，我发现的最好的方法是 jQuery appear 插件。很管用。

## 分享这篇文章