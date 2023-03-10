# jQuery 只从元素 id 中获取数字

> 原文：<https://www.sitepoint.com/jquery-numbers-element-id/>

假设我们将条目 id 存储在元素容器 id 属性中，我们只想从 id 属性字符串中提取数字。通过使用一个简单的 JavaScript 正则表达式 replace 语句，您可以从任何元素 id 标记中获取数字。


```
$(this).attr('id').replace(/[^d]/g, '');
```

**之前:**集装箱 1
T3 之后: 1

假设 id 标记包含字母和数字，我们只对从 div 容器 id 中获取数字感兴趣。这可以用于类似当您试图从父容器元素中获取 id 时的情况:

```
 //elements...
    //a button...with a bound click event

    //elements...
    //a button...with a bound click event

    //elements...
    //a button...with a bound click event 
```

因此，当我们单击任何元素时，我们可以获取它的容器 id 来使用。

```
//gets the container id number only from element
function getIdNum(elem)
{
    if (elem.attr('id'))
    {
        return elem.attr('id').replace(/[^d]/g, '');
    }
    else
    {
        return elem.parents('.widget').attr('id').replace(/[^d]/g, '');
    }
}

//example call
var containerId = getIdNum($('some button'));
```

## 分享这篇文章