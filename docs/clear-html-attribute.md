# 清除(HTML 属性)

> 原文：<https://www.sitepoint.com/clear-html-attribute/>

### 描述

`clear`属性是一个不推荐使用的(表示性的)属性，用于清除任何预先存在的右对齐或左对齐。它确保`br`元素之后的内容出现在前一个对齐元素的基线之下；`br`不仅仅是在围绕左对齐或右对齐元素的内容中创建一个换行符。

在过去，这种方法通常用于阻止内容环绕右对齐或左对齐的图像或表格。

管理对齐问题的正确方法是使用 CSS `float`属性，而不是 HTML `align`属性。为了清除元素，我们像这样使用 CSS `clear`属性:

```
<br clear="left"/> the old-fashioned HTML way

<br style="clear:left;"/> the CSS way
```

请注意，您应该避免内联 CSS 样式——上面的内联样式仅用于直接比较目的。

### 例子

此示例显示了一个`br`，通过它，`clear`属性用于获取右对齐图像下方的内容:

```
<p><img src="pool.jpg" alt="sitting by the pool" align="right"/>
    The rest of the day was a lazy one, partly spent by the complex
    pool, partly inside watching British TV, but we couldn't be doing
    this for the rest of the holiday. Already we were missing having
    the car!
 *<br clear="all"/>*
    I decided to check out what the weather would be doing for the next
    few days, as that would help us make any decisions about future
    excursions.
</p>
```

## 分享这篇文章