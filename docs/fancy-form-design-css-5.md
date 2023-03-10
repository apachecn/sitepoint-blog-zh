# 使用 CSS 文章的花式表单设计

> 原文：<https://www.sitepoint.com/fancy-form-design-css-5/>

**更改默认字段集布局**

尽管字段集和图例元素是标记表单组的最容易使用的方法，但在过去，很多人都没有使用过它们，因为他们不喜欢浏览器强加给这些元素的默认样式——字段集周围的边框、与框边缘相交的图例。但是可以改变这个默认布局，让你的表单不那么方方正正。

我们的第一步是将字段集元素放在一起，消除它们之间的空白。为此，我们可以使字段集元素底部的边距为零，但这实际上会如图 19 所示。

![fieldset-collapse](img/877c6ee0f4cc0d2a8bf8951e95ef6810.png)
*图 19:图例增加了额外的高度，这样 fieldset 元素就不会碰到*

字段集元素顶部的图例防止两个字段集元素结合。为了避免这个问题，我们可以在每个字段集的底部使用一些负边距。这将“拉”起下面的字段集，使其与上面的字段集重叠，使它们看起来像是在接触。

为了防止底部字段集与任何表单元素重叠，我们还应该在字段集元素的底部添加一些填充，以便它们有一些空间可以移入:

```
fieldset {      
float: left;      
clear: both;      
width: 100%;      
margin: 0 0 -1em 0;      
padding: 0 0 1em 0;      
border: 1px solid #BFBAB0;      
background-color: #F2EFE9;     
}
```

将`fieldsets`向上移动`1em`足以覆盖它们之间的间隙，1em 的底部填充抵消了这种移动，确保没有表单元素在字段集元素下消失。

当删除空白时，一些视觉上的调整是必要的。如果字段集背景色和普通页面背景色之间没有联系，我们就不再需要渐变背景图像，所以这一点被忽略了。

边框样式也发生了变化，我们移除了所有边框，只替换了顶部边框:

```
fieldset {      
float: left;      
clear: both;      
width: 100%;      
margin: 0 0 -1em 0;      
padding: 0 0 1em 0;      
border-style: none;      
border-top: 1px solid #BFBAB0;      
background-color: #F2EFE9;     
}
```

所有的`[fieldset](https://reference.sitepoint.com/html/fieldset)`元素被连接在一起，额外的左边和右边的边框使表单看起来很混乱。仅使用顶部边框，我们已经创建了一个更加整洁的外观，如图 20 所示。

![fieldset-alternating](img/a9c718325fb2d973fe2edd8f5fb22485.png)
*图 20:连接的字段集元素*

将字段集元素连接在一起的另一个副作用是图例现在看起来不合适，在两个字段集之间平衡。解决这个问题的方法是将图例完全放在其字段集的边界内。

直觉上，您可能会对图例使用相对或绝对定位，将其向下移动到字段集中。然而，Firefox 抵制任何重新定位图例的尝试——它就是不动。

不幸的是，解决这个问题的唯一方法是在表单中增加一点点标记。通过在每个图例元素中插入一个多余的 span，Firefox 允许我们对其进行样式化，并将文本向下移动到字段集中:

```
fieldset-alternating.html (excerpt)     
<legend>      
<span>Contact Details</span>     

</legend>
```

可以绝对定位该跨度，并使用 margin-top 将其向下移动到字段集中。同时，让我们也增加图例文本的字体大小，使其更加突出:

```
fieldset-alternating.css (excerpt)     
legend span {      
position: absolute;      
margin-top: 0.5em;      

font-size: 135%;     
}
```

实际上在 Firefox 的某些版本中有一个难以理解的错误(据我所知，在 Windows XP 上是 Firefox 1.5.0.6，但不是 OSX ),使得绝对定位的 span 元素表现得好像它们都位于 form 元素的顶部。给图例元素一个相对位置似乎不会影响 span 元素，所以我们实际上需要相对定位每个 fieldset 元素，并给 span 元素一些显式坐标，以避开这个 bug:

```
fieldset-alternating.css (excerpt)     
fieldset {      
position: relative;      
float: left;      

clear: both;      
width: 100%;      
margin: 0 0 -1em 0;      
padding: 0 0 1em 0;      
border-style: none;      

border-top: 1px solid #BFBAB0;      
background-color: #F2EFE9;     
}     
legend span {      
position: absolute;      

left: 0.74em;      
top: 0;      
margin-top: 0.5em;      
font-size: 135%;     
}
```

left 的 0.74em 值实际上与我们给有序列表的 1em 填充相匹配，因为 span 具有更大的字体大小。

因为我们现在为跨度指定了一个左纵坐标，所以我们还必须从它的父图例中去掉左边的空白，这样我们就不会得到双倍的间距。只需省略我们之前使用的保证金规则:

```
fieldset-alternating.css (excerpt)     
legend {      
padding: 0;      
color: #545351;      
font-weight: bold;     

}
```

那只虫子现在被压扁了！

当我们将图例向下移动到字段集中时，我们需要确保图例不会与任何表单元素重叠，所以让我们在有序列表的顶部添加更多的填充:

```
fieldset-alternating.css (excerpt)     
fieldset ol {      
padding: 3.5em 1em 0 1em;      
list-style: none;     

}
```

不要忘记在我们的 Internet Explorer 专用样式表中更改匹配值:

```
fieldset-alternating-ie.css (excerpt)     
legend span {      
margin-top: 1.25em;     

}     
fieldset ol {      
padding-top: 3.25em;     
}
```

Internet Explorer 在 legend 元素的跨度上有稍微不同的间距，所以让我们也调整一下 margin-top 的值。

经过所有这些更改后，有一个字段集看起来有点不合适:submit 字段集。因为 submit fieldset 没有图例，submit 按钮会上移得太高，所以我们需要把它向下推一点。最简单的方法是在这个字段集的顶部添加一些填充。此外，因为提交字段集将与它上面的字段集重叠，我们需要为提交字段集提供纯色背景，否则前面字段集的背景色将会显示出来。这意味着将背景色值从透明更改为您的正常页面背景色:

```
fieldset-alternating.css (excerpt)     
fieldset.submit {      
float: none;      
width: auto;      

padding-top: 1.5em;      
padding-left: 12em;      
background-color: #FFFFFF;     
}
```

之前，我们也从提交字段集中删除了边框，但是对于这个相邻的布局，我们需要提交字段集保留应用于所有字段集元素的上边框。我们将让规则无干扰地级联到提交字段集中。

一旦我们实现了所有这些更改，表单的布局就完成了。表单如图 21 所示，但是需要一些细微的美学调整。

![fieldset-headings](img/08430449088b28c1ced81980b49dfd8d.png)
*图 21:所有字段集元素被连接，图例元素被移动到框内*

因为我们将所有的字段集元素放在一起，所以它们在视觉上很容易碰到一起。通过微妙地改变奇数和偶数字段集元素中的背景色元素，可以更好地区分每个字段集。实现这一点的唯一跨浏览器方法是每隔一个字段集添加一个新类。这允许我们使用 CSS 选择器给那些字段集元素不同的背景颜色。我通常使用一类 alt，但是你可以使用任何你认为合理的:

```
<fieldset>     
...     
</fieldset>     
<fieldset class="alt">     

...     
</fieldset>     
<fieldset>     
...     

</fieldset>     
<fieldset class="alt">     
...     
</fieldset>     

...
```

那么你所要做的就是想出一种不同的背景色:

```
fieldset-alternating.css (excerpt)     
fieldset.alt {      
background-color: #E6E3DD;     

}
```

我们带有交替字段集元素的最终表单如图 22 所示！

**Go to page:** [1](/fancy-form-design-css) | [2](/fancy-form-design-css-2/) | [3](/fancy-form-design-css-3/) | [4](/fancy-form-design-css-4/) | [5](/fancy-form-design-css-5/) | [6](/fancy-form-design-css-6/) | [7](/fancy-form-design-css-7/)

## 分享这篇文章