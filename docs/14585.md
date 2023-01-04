# CSS 和 JavaScript 的圆角

> 原文：<https://www.sitepoint.com/rounded-corners-css-javascript/>

圆角是最常见的 CSS 技术之一。正如 CSS 中的许多事情一样，有各种方法可以解决这个问题。在本文中，我将探讨一些常见技术的优缺点，并介绍一种利用 CSS 和 JavaScript 的新技术。

在我们深入研究 CSS 之前，让我们提醒自己这个问题的老式解决方法，它使用布局表格:

```
<table width="200" cellpadding="0" cellspacing="0"> 
<tr> 
  <td width="15"><img src="tl.gif" alt="" /></td> 
  <td bgcolor="#1b5151"></td> 
  <td width="15"><img src="tr.gif" alt="" /></td> 
</tr> 
<tr bgcolor="#1b5151"> 
  <td>&nbsp;</td> 
  <td>Content goes here</td> 
  <td>&nbsp;</td> 
</tr> 
<tr bgcolor="#1b5151"> 
  <td><img src="bl.gif" alt="" /></td> 
  <td></td> 
  <td><img src="br.gif" alt="" /></td> 
</tr> 
</table>
```

几年前，这可能是一个可以接受的解决方案。今天，这是一个丑陋的黑客:这是一个相对不重要的视觉装饰的大量冗余标记。事实上，上面的代码在使用严格的 doctype 的文档中甚至不会起到预期的作用——边角图像下方会出现小间隙，这是由图像是行内元素这一事实引起的，因此，会在图像下方为字母“尾巴”如“y”和“j”留出空间。正如 Eric Meyer 在[图片、表格和神秘的空白](http://devedge.netscape.com/viewsource/2002/img-table/)中所解释的，解决方案是在样式表中添加以下规则:

```
td img { display: block; }
```

这产生了期望的结果，如这里的[和](https://www.sitepoint.com/examples/jscorners/tables-strict.html)所示。

但是，现在我们使用 CSS 来修复丑陋的表格！让我们看看只用 CSS 实现同样效果的方法。

一般来说，任何装饰性的图像都应该在现有的页面元素上实现为 CSS 背景图像，而不是使用`<img>`标签放到页面中。很容易判断一个图像是装饰性的还是包含实际内容:问问自己，没有图像是否会对页面的整体内容产生影响。在圆角的情况下，答案显然不是。

CSS 背景图片是非常强大的东西。你只需要看看在 [CSS 禅宗花园](http://www.csszengarden.com/)展出的许多精彩设计就能找到证据。使用 CSS，背景图像可以应用于页面上的任何元素。此外，它可以水平重复、垂直重复或根本不重复；它可以使用绝对测量定位在图像的背景区域内，或者相对于四个角中的一个；当元素的内容滚动时，它甚至可以保持不动。不幸的是，CSS 2 有一个很小但很重要的限制:页面上的每个元素只能应用一个背景图像。为了正确渲染一个`<div>`上的圆角，我们需要应用四个背景图像，每个角一个。

##### 固定宽度框

如果我们要应用装饰角的盒子的宽度是固定的，那么问题已经解决了一半。如果我们知道盒子将总是 200 像素宽，而不是创建四个背景图像(每个角一个)，我们可以创建两个:一个用于盒子的顶部，一个用于底部。现在的挑战是将两幅背景图像应用到我们的`<div>`中。是时候利用我们的标记了。

一个圆角的盒子如果不包含任何内容就没什么意思了。请考虑以下情况:

```
<div class="rounded"> 
<h3>Exciting features!</h3> 
<p>Your new Widget2000 will...</p> 
<ul> 
 <li>... clean your shoes</li> 
 <li>... walk your dog</li> 
 <li>... and balance your cheque book!</li> 
</ul> 
</div>
```

很简单，是吧？盒子的标题存在于一个`<h3>`(我假设`<h1>`和`<h2>`已经在页面的层次结构中被进一步使用了)后面的内容是一个段落和一个无序列表。解决我们两个背景问题的关键在于`<h3>`，它就在盒子的顶部。我们所要做的就是将一个背景图像应用到`<h3>`的顶部，并将另一个应用到包含`<div>`的底部，这样效果就完成了:

```
div.rounded { 
  width: 200px; 
  background: #1b5151 url(200pxbottom.gif) no-repeat bottom center; 
  padding-bottom: 15px; 
} 
div.rounded h3 { 
  padding-top: 15px; 
  background: transparent url(200pxtop.gif) no-repeat top center; 
}
```

点击[此处](https://www.sitepoint.com/examples/jscorners/fixed-width.html)查看结果。

结构良好的文档通常充满了这样的钩子，可以小心地利用它们来应用多种背景并实现特定的视觉效果。学习识别它们是使用 CSS 的重要部分。

##### 嵌套元素

对一个 div 应用四个背景仍然超出了我们的能力范围。但是如果我们嵌套四个 div，每个 div 对应一个背景，会怎么样呢？这样做解决了我们的问题，但代价是没有结构价值的额外标记:

```
<div class="rounded"><div><div><div> 
Content goes here 
</div></div></div></div>
```

并且，在 CSS 中:

```
div.rounded { 
  width: 200px; 
  background: #1b5151 url(tr.gif) no-repeat top right; 
} 
div.rounded div { 
  background: transparent url(tl.gif) no-repeat top left; 
} 
div.rounded div div { 
  background: transparent url(br.gif) no-repeat bottom right; 
} 
div.rounded div div div { 
  background: transparent url(bl.gif) no-repeat bottom left; 
  padding: 15px; 
}
```

代码显示如下图所示[这里](https://www.sitepoint.com/examples/jscorners/four-nested-divs.html)。

这里的情况应该很清楚了。四个 div 中的每一个都分配有圆角背景图像，分别位于右上、左上、右下和左下。虽然包含 div 的宽度设置为 200px，但也可以轻松地设置为更灵活的值，以用于液体设计——无论包含 div 有多大或多小，边角仍然有效。

我们现在有了一个解决这个问题的方法，它使用的标记比原来的表格示例少得多。但是，它仍然不完美:它使用了三个额外的 div，这对整个文档结构没有任何价值。我们能做得更好吗？是时候看看 JavaScript 了。

##### 使用 DOM

使用 JavaScript 和 DOM，可以在浏览器加载文档后操纵文档的结构。圆角是一种外观效果，可以对非 JavaScript 用户代理隐藏，而不会显著降低他们对网站的整体体验，因此使用 JavaScript 进行这种转换不存在道德问题。我们的最终解决方案将只需要源文件中的一个`<div>`。我们将使用 JavaScript 动态添加圆角效果所需的三个额外 div。

下面是标记:

```
<div class="rounded"> 
Content goes here. 
</div>
```

我想你会同意，除了如果内容在结构上被更好地定义为一个段落，也许可以将`<div>`换成`<p>`,我们没有什么可以做得比这更简单。进行这种转换是留给读者的一个练习。

下面是 JavaScript 代码:

```
function roundedCorners() { 
  var divs = document.getElementsByTagName('div'); 
  var rounded_divs = []; 
  /* First locate all divs with 'rounded' in their class attribute */ 
  for (var i = 0; i < divs.length; i++) { 
    if (/broundedb/.exec(divs[i].className)) { 
      rounded_divs[rounded_divs.length] = divs[i]; 
    } 
  } 
  /* Now add additional divs to each of the divs we have found */ 
  for (var i = 0; i < rounded_divs.length; i++) { 
    var original = rounded_divs[i]; 
    /* Make it the inner div of the four */ 
    original.className = original.className.replace('rounded', ''); 
    /* Now create the outer-most div */ 
    var tr = document.createElement('div'); 
    tr.className = 'rounded2'; 
    /* Swap out the original (we'll put it back later) */ 
    original.parentNode.replaceChild(tr, original); 
    /* Create the two other inner nodes */ 
    var tl = document.createElement('div'); 
    var br = document.createElement('div'); 
    /* Now glue the nodes back in to the document */ 
    tr.appendChild(tl); 
    tl.appendChild(br); 
    br.appendChild(original); 
  } 
} 
/* Run the function once the page has loaded: */ 

window.onload = roundedCorners;
```

该脚本分为两个逻辑部分。第一部分遍历文档中的所有`<div>`元素，构建一个数组，其中包含其`class`属性中的`'rounded'`(记住，元素可以有多个由空格分隔的类)。脚本的第二部分依次遍历这些元素，创建三个额外的 div 并将它们包装在原 div 周围。让我们更详细地看看代码:

```
original.className = original.className.replace('rounded', '');
```

在这里，我们从最初的`<div>`中完全删除了类`"rounded"`。其原因将在 CSS 中变得清楚；本质上，我们不希望应用的原始样式再影响那个元素。

```
var tr = document.createElement('div'); 
tr.className = 'rounded2';
```

我们已经创建了最外面的`<div>`，它将用于应用右上角的背景图像以及盒子的整体宽度。请注意，我们已经将该类设置为`'rounded2';`，这将在我们的 CSS 中定义，与提供给不支持 JavaScript 的客户端的`'rounded'`类略有不同。

```
/* Swap out the original (we'll put it back later) */ 
original.parentNode.replaceChild(tr, original);
```

W3C DOM 没有提供用另一个节点替换文档中的一个节点的直接方法。相反，您必须使用节点的`replaceChild()`方法用另一个节点替换它的一个子节点。替换您正在查看的节点的一个有用技巧是使用`parentNode`属性访问它自己的父节点，然后使用/#c#。replaceChild 用来交换其他东西。如果这对您没有意义，不要担心——只要把上面的代码想象成用我们刚刚创建的新的`tr`节点替换我们原来的节点。

```
/* Create the two other inner nodes */ 
var tl = document.createElement('div'); 
var br = document.createElement('div'); 
/* Now glue the nodes back in to the document */ 
tr.appendChild(tl); 
tl.appendChild(br);
```

我们现在已经创建了三个新的`<div>`元素，并将它们插入到文档中。剩下的就是重新插入我们的原始节点，并完成它的内容:

```
br.appendChild(original);
```

在这一点上，我们实际的文档树与上面四个嵌套的`<div>`例子中的几乎相同，唯一的不同是外部元素有一个类`'rounded2'`而不是`'rounded'`。这是 CSS:

```
div.rounded { 
  width: 170px; 
  padding: 15px; 
  background: #1b5151; 
} 

div.rounded2 { 
  width: 200px; 
  background: #1b5151 url(tr.gif) no-repeat top right; 
} 
div.rounded2 div { 
  background: transparent url(tl.gif) no-repeat top left; 
} 
div.rounded2 div div { 
  background: transparent url(br.gif) no-repeat bottom right; 
} 
div.rounded2 div div div { 
  background: transparent url(bl.gif) no-repeat bottom left; 
  padding: 15px; 
}
```

下面是结果。

第一组规则用于`div.rounded`，仅用于不执行 JavaScript 的浏览器。注意宽度是 170px，填充是 15px，加起来总宽度是 200px(宽度加上左右填充)。如果你需要这在 IE 5/Windows 中工作，它解释填充值不同，你需要应用臭名昭著的[盒子模型黑客](http://css-discuss.incutio.com/?page=BoxModelHack)。您已经看到了上一个示例中的第二组规则。

##### 展望未来

上述技术将在所有现代浏览器和所有未来支持 CSS2 和 DOM 2 标准的浏览器中工作。CSS 3 引入了许多新的方法来实现这种效果，这将使上述技术过时。除了[原生圆角支持](https://www.w3.org/TR/2002/WD-css3-border-20021107/#the-border-radius)(已经在 Mozilla 系列浏览器中可用)之外，CSS 还具有强大的[:](https://www.w3.org/TR/2003/WD-css3-content-20030514/#wrapping)外部伪元素，它允许以类似于本文所示 JavaScript 示例的方式插入其他可设置样式的元素。如果这还不够的话，[边框图片](https://www.w3.org/TR/2002/WD-css3-border-20021107/#the-border-image)将允许你能想到的几乎任何边框装饰。

不幸的是，CSS 3 的广泛支持还需要几年时间。在那之前，JavaScript 完全有能力弥补一些不足。

## 分享这篇文章