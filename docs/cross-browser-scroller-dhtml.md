# 使用 DHTML 创建跨浏览器滚动条

> 原文：<https://www.sitepoint.com/cross-browser-scroller-dhtml/>

文本在屏幕上移动的方式很吸引人。自从第一次有可能使用 JavaScript 在网页中创建移动文本以来，无数所谓的滚动脚本被创建出来。但是，你有没有注意到，它们中的大多数都局限于在文本框或状态栏中滚动文本？点击[这里](https://www.webmasterbase.com/examples/dhtmlscroll1.html)可以看到一个在文本框中滚动文本的滚动条。

这并不是说文本框或状态栏有什么问题，但就我个人而言，我喜欢我的文本自己滚动，而不是包裹在任何其他元素中。换句话说，一个“真正的”文本滚动条。可能吗？显然，是的。经过几周的搜索，我终于在网上找到了一些滚动文本的例子，在这个过程中，我自己学会了如何创建它们。在这篇文章中，我希望与你分享这些知识。

我将教你如何使用 IE4+和 NS4+的 DHTML 特性创建一个基本的侧滚滚动条。点击[此处](https://www.webmasterbase.com/examples/dhtmlscroll2.html)观看演示。

不管你用的是 IE4 还是 NS4，滚动条都一样好用。它的实现涉及两个完全不同的概念。让我们自己看看如何创建一个文本滚动条！

##### 在 IE4 中滚动文本

在 IE4 中，滚动任何文本实际上非常简单，这要感谢 IE3 以来支持的默认标签:`<marquee>`标签。只需将你想滚动的任何文本放入其中，就可以了:

```
<marquee>This is scrolling text</marquee>
```

然而，在 IE4 中，似乎你现在也可以放入 HTML 标签，并且它们会被这样解释:

```
<marquee><big>This is a BIG scrolling text</big></marquee>
```

这就是 IE4 的内容。如果你只是为 IE4 创建一个文本滚动条，那么你已经知道了所有该知道的东西。然而，像许多其他人一样，我使用 NS4 来浏览网页，所以无论你为 IE4 用户计划了什么，我也希望看到它。让我们继续看看如何让 scroller 在 NS4 中同样工作，这稍微复杂一点。

##### 在 NS4 中滚动文本

要在 NS4 中滚动文本，一切都必须从头开始创建，包括界面。这是因为 NS4 中没有默认的标签或特性来模拟这个动作。

然而，NS4 中存在的是`<layer>`标签(浏览器的 DHTML 标签)。这个标签允许你在页面上自由地移动任何内容，通过应用一些控制，我们可以把它变成一个滚动条！

基本思路是这样的。我们定义了一个`<layer>`标签，并将文本放入其中进行滚动。然后，我们用`<ilayer>`标签将所有这些内容包装起来，这只是让它看起来与页面的其余部分内联(相对于由层的左侧和顶部位置定义的坐标)。

```
<ilayer name="scroll1" width=300 height=20>

<layer name="scroll2">This is scrolling text. This is scrolling text. This is scrolling text...</layer>

</ilayer>
```

然后，通过使用一个简单的脚本来增加该层的左侧位置，它会移动，就像在滚动器中一样。在我向您展示脚本本身之前，请允许我用图形来说明我刚刚谈到的内容:

![DHTML Scroller Schematic](img/a31f35ff575cadb6450bf0372d4e9293.png)

标签定义了“滚动条窗口”，滚动条的物理可视区域(绿色矩形)。另一方面，`<layer>`标签定义/包含滚动文本本身，并在上面表示为白色矩形。我们希望创建一个脚本，将这个白色矩形不断地向左移动，直到它到达文本的末尾，然后重新开始。

下面是实现这一点的函数:

```
function scrollit(){

/* get the total length of the scroller (white rectangle) */

scrollerlength = document.scroll1.document.scroll2.document.width;

/* if the scroller's left position is greater than -scrollerlength (hasn't reached the end) */

if (document.scroll1.document.scroll2.left >= scrollerlength*(-1)){

/* decrease it's left position by 6 pixels */

document.scroll1.document.scroll2.left -= 6;

setTimeout("scrollit()",100);

}

else{

/* else if the scroller has reached the end, reset the scroller position */

document.scroll1.document.scroll2.left=300;

/* and start things all over */

scrollit();

}

}
```

看看我在里面的评论，看看它是如何工作的。基本上，这个想法是不断减少层的“左”值，直到它到达层的末端。然后，重复并从原来的位置重新开始。

##### 整个滚动文本代码

将这些片段放在一起，加上一些添加的代码，这就是呈现您在本文开头看到的滚动条的完整脚本。我将首先列出它，然后解释可能需要澄清的任何部分:

```
<script language="JavaScript1.2">

/*比利·皮特(https://members.xoom.com/billypete/)的剧本*/ 
 /*灵感来自在 https://dynamicdrive.com 发现的卷轴*/  `/*指定字幕的滚动速度(越大越快)*/
var speed = 6；
 /*指定字幕内容*/
var marquee contents = '<font face = " Arial ">strong>这是正在滚动的文本脚本。这是一个滚动文本脚本。这是一个滚动文本脚本。</strong></font>'；

if(document . all)
document . write('<marquee scrollAmount = '+speed+' style = " width:300 ">'+marquee contents+'</marquee>')；

function initialize marquee(){
if(document . layers){
document . cmarquee 01 . document . cmarquee 02 . document . write(<nobr>'+marquee contents+'</nobr>')；
document . cmarquee 01 . document . cmarquee 02 . document . close()；
the length = document . cmarquee 01 . document . cmarquee 02 . document . width；
斯克罗利特()；
 } 
 }

函数 scrollit(){
if(document . cmarquee 01 . document . cmarquee 02 . left>= the length *(-1)){
document . cmarquee 01 . document . cmarquee 02 . left-= speed；
 setTimeout( "scrollit()"，100)；
}
else {
document . cmarquee 01 . document . cmarquee 02 . left = 300；
scrollit()；
 } 
 }

window . onload = intializemarquee；
</剧本>

 <ilayer width="300" height="20" name="cmarquee01"><图层名称= " cmarquee 02 "></图层>
</图层></ilayer>` 

我用`document.write()`为 IE 动态写出`<marquee>`标签(而不是简单的直接嵌入页面)。这是为了避免 NS 将来可能出现的问题，如果 NS 最终支持`<marquee>`；这方面的代码是专门为 IE 写的！函数`initializemarquee()`用于用 NS 所需的文本填充滚动条。它首先访问`<ilayer>`，然后是`<layer>`标签，最后是`document.write()`方法来完成这个。

所以你有它！一个很酷的跨浏览器滚动条，你可以在你的网页上使用。最后，我给你留下一些关于 DHTML 滚动条的例子:

*   动态驱动 DHTML 滚动条

*   [DevEdge Ticker 对象—需要 NS 4](https://developer.iplanet.com/docs/examples/dynhtml/ticker.html) 

看一眼他们的源代码…这就是你学习的方式！

## 分享这篇文章

```