# 探索 CSS 布局的局限性

> 原文：<https://www.sitepoint.com/exploring-limits-css-layout/>

随着像[有线新闻](http://www.wired.com/)和 [ESPN](http://www.espn.com) 这样的主要网站迁移到 CSS 布局，网页设计的“新浪潮”真正到来了。但是即使在最现代的浏览器中，CSS 也有其局限性。

理解它们可以帮你省去很多挫折和浪费的时间。

不管是好是坏，网页设计的时尚已经演变成倾向于类似报纸风格的布局。常见的设计元素包括:

*   水平跨越页面的页眉和页脚
*   内容受页面宽度限制
*   在合理的范围内，垂直滚动是可以接受的
*   导航和主要内容旁边的垂直栏中的次要内容

最后一个是真正的难题。可悲的现实是，当前的 CSS 规范(CSS2)在设计时并没有考虑到多列内容。因此，就像网页设计者总是不得不滥用 HTML 表格来实现复杂的页面布局一样，他们现在也必须滥用 CSS 定位来达到同样的目的。

我承认，这是一个有争议的立场。现在，所有迹象都表明 CSS 是实现网页布局的理想方式。不幸的是，CSS2 规范(于 1998 年完成)比当今网页设计的许多复杂性都要早，多栏设计就是其中之一。

![1213_ideal](img/53d29b7c01c4777c66e86d224337f6e3.png)

这里展示的是带有页眉和页脚的经典三栏设计，这是当前网页布局时尚的最基本表达。这当然很容易用 HTML 表格来完成，但是你可以做得更好！

为了在 CSS 中实现这一点，我们从一个自然的三块页面开始。顺便提一下，块只是 HTML 代码中的`<div>`标签。我所说的“自然”是指我们不对这些块做任何特殊的处理来使它们进入这种布局——浏览器会自然地垂直堆叠这三个块，每个块占据浏览器窗口的宽度。

![1213_natural](img/eac709bb02fa9178e2ecf8bf48ccbcb6.png)

接下来，我们通过向内容区域添加边距来为左右两列腾出空间。这是经过提炼的 CSS 代码:

```
#content {  

  margin-left: 100px;  

  margin-right: 100px;  

}
```

![1213_margins](img/d9a9dc9dc62715ffbc72698353f04c9a.png)

最后，我们添加左列和右列作为绝对定位的块:

```
#left, #right {  

  position: absolute;  

  top: 100px; /* height of the header */  

  width: 100px;  

}  

#left {  

  left: 0;  

}  

#right {  

  right: 0;  

}
```

![1213_absolute](img/c630383ebd3cc4ac4bb40b3c3b29aa5d.png)

离想要的效果不远吧？这个结果只有两个问题:

*   左右柱在身高部各做各的。如果不为所有三列设置一个固定的高度，CSS 就无法匹配列的高度——这很少是一个可行的选择！
*   因为绝对定位的左列和右列浮动在页面的其余部分之上，所以页脚的位置仍然只由内容区域的高度决定。当内容列比其他列短时，这会导致问题。

![1213_broken](img/e7d826f4a1e21ef2565f0085d703aa49.png)

如果您正在处理所有列中的纯色背景，前者对您来说可能不是问题。而后者，却是 CSS 布局新从业者面临的最头疼的问题。这个问题是 CSS2 中缺少多列支持的直接结果。作为设计师，我们不应该依靠绝对定位来创建多栏布局，但 CSS2 没有更好的东西可以提供。

目前，最好的解决方案是在浏览器执行初始布局后，使用 JavaScript 来均衡列高度。

##### JavaScript 拯救世界！

我们假设您有一个包含三列的页面。中间列使用自然(即静态)定位，并包括为使用绝对定位的左列和右列留出空间的边距。

列`<div>`标签的 id 属性是 left、content 和 right。

我们不会处理浏览器之间的差异，而是将它留给专业人士，使用来自 Cross-Browser.com 的优秀的 X 脚本。只需从该网站下载 x.js 并将其加载到页面的`<head>`标签中，如下所示:

```
<script src="x.js" type="text/javascript">   

</script>
```

现在，因为页脚在浏览器布局时可能会被左右两列覆盖，所以我们希望在调整列高度之前保持页脚不可见。

确保页脚`<div>`设置了`id="footer"`，并将此样式规则添加到文档的`<head>`标签中:

```
<style type="text/css">   

#footer {   

  visibility: hidden;   

}   

</style>
```

现在，当浏览器完成页面加载时(以及每当浏览器窗口被调整大小时)，我们想要找出哪一列是最高的，并将它们都调整到那个高度。然后我们可以显示页脚。

因为当用户调整浏览器窗口大小时，这个过程可能会重复发生，所以我们需要将每一列的内容包装在一个附加的`<div>`中。文档的结构变为:

```
<div id="left">   

  <div id="leftcontent"><!--left--></div>   

</div>   

<div id="content">   

  <div id="contentcontent"><!--content--></div>   

</div>   

<div id="right">   

  <div id="rightcontent"><!--right--></div>   

</div>
```

正是这些“内”`<div>`我们将在设置“外”`<div>`的高度之前检查每列的自然高度。

下面是使用 X 库的`xHeight`和`xShow`函数调整布局的 JavaScript 函数:

```
<script type="text/javascript">   

function adjustLayout()   

{   

  // Get natural heights   

  var cHeight = xHeight("contentcontent");   

  var lHeight = xHeight("leftcontent");   

  var rHeight = xHeight("rightcontent");   

  // Find the maximum height   

  var maxHeight =   

    Math.max(cHeight, Math.max(lHeight, rHeight));   

  // Assign maximum height to all columns   

  xHeight("content", maxHeight);   

  xHeight("left", maxHeight);   

  xHeight("right", maxHeight);   

  // Show the footer   

  xShow("footer");   

}
```

剩下的就是让这个函数在页面加载或调整大小时运行。这是通过`xAddEventListener`完成的:

```
window.onload = function()   

{   

  xAddEventListener(window, "resize",   

    adjustLayout, false);   

  adjustLayout();   

}   

</script>
```

这就行了！[亲自体验一下](https://www.sitepoint.com/examples/3col/)！

##### 未来

但是未来呢？CSS3 的工作草案包括[多栏布局模块](https://www.w3.org/TR/css3-multicol/)，但它是为跨等宽栏的流动文本而设计的，而不是为不同栏宽的报纸样式布局而设计的。

我在地平线上看到的纯 CSS 多栏布局的最佳选择是 [CSS3 框模型](https://www.w3.org/TR/css3-box)中的显示模型和显示角色属性。具有讽刺意味的是，这些属性允许您将列块设置为表格单元格，以便进行布局。设计技术将回到原点，同时仍然保留 CSS 布局的内容/风格划分。

## 分享这篇文章