# jQuery 函数获取元素的最大 X，Y 坐标

> 原文：<https://www.sitepoint.com/jquery-function-max-xy-coordinates-element/>

jQuery 函数 to **获取元素**(或者选择器中的一组元素)所占据的最远空间的坐标。如果你基于需要在容器内的子元素的绝对位置来设置容器的区域，这可能是有用的。

![max-location-of-element](img/92b206b6360d2c7f15b4dfd33e82bd3c.png "max-location-of-element")


```
jQuery.fn.getMaxOccupiedLocation = function()
{
    var maxX = 0, maxY = 0, tmpX, tmpY, elem;
    this.each( function(i,v)
    {
        elem = $(this),
        tmpX = elem .offset().left + elem.width(),
        maxX = (tmpX > maxX) ? tmpX : maxX,
        tmpY = elem .offset().top + elem.height(),
        maxY = (tmpY > maxY) ? tmpY : maxY;
    });
    // console.log(maxX+','+maxY);
    return { x:maxX, y:maxY }; //not the best implementation as it breaks the chain
};
```

## 分享这篇文章