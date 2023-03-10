# 用 JavaScript 增强结构化标记

> 原文：<https://www.sitepoint.com/structural-markup-javascript/>

就在几年前，编写 HTML 的关键技能是了解足够多的表格技巧，以说服两大主流浏览器或多或少地做你想让它们做的事情。现代网络是一个非常不同的野兽，你的标记的质量是基于你如何使用结构元素，如标题，段落和列表来描述你的内容。

这种方法的好处以前已经解释过很多次了:更易维护的代码，更小的文件大小，更好的可访问性，以及从单个样式表控制网站的外观和感觉的能力，而不是在多个页面上分散的大量标记中进行处理。一个不经常讨论的优点是，结构良好的标记为基于长期被滥用的客户端 Web 的第三条腿 Javascript 的附加站点增强打开了大门。

本文将探讨 Javascript 和结构良好的标记协同工作的两种方式。第一个例子将展示 Javascript 如何通过挂钩 cite 属性来增强 blockquote。第二部分将演示一个“同类最佳”的脚本，用于构建切换页面上哪个面板可见的链接。

##### 整体引用引文

对于我们的第一个例子，让我们看一下简单的 blockquote 元素。经常被误用来应用缩进，这个元素的正确用法是标记应该在视觉上与周围文本分开的引用。开始的 blockquote 标记可以带有一个可选的 cite 属性，该属性应该包含引用所源自的页面的 URL。

cite 属性的唯一问题是浏览器完全忽略了它。标记纯粹主义者可能会欣赏它，但是，从纯粹实用的角度来看，除了对使用正确的标记感到沾沾自喜之外，使用它什么也得不到。这就是 Javascript 的用武之地。使用 DOM，可以在任何具有 cite 属性的 blockquote 的底部添加到报价源的链接。下面是实现这一功能的函数代码:

```
function extractBlockquoteCitations() { 
  var quotes = document.getElementsByTagName('blockquote'); 
  for (var i = 0; i < quotes.length; i++) { 
    var cite = quotes[i].getAttribute('cite'); 
    if (cite != '') { 
      var a = document.createElement('a'); 
      a.setAttribute('href', cite); 
      a.setAttribute('title', cite); 
      a.appendChild(document.createTextNode('Source')); 
      var p = document.createElement('p'); 
      p.className = 'blockquotesource'; 
      p.appendChild(a); 
      quotes[i].appendChild(p); 
    } 
  } 
}
```

让我们仔细看看函数的主体。

```
var quotes = document.getElementsByTagName('blockquote');
```

这一行使用 DOM 方法`getElementsByTagName`来查找当前页面中的所有 blockquote 元素，将它们分配给一个名为 quotes 的数组(它实际上是一个 HTMLCollection，但方便的是，这是一个行为类似于数组的数据结构)。

```
for (var i = 0; i < quotes.length; i++) { 
  var cite = quotes[i].getAttribute('cite'); 
  if (cite != '') {
```

现在，我们将遍历收集到的 blockquote 节点。每次，我们都使用`getAttribute`方法从元素中检索 cite 属性。如果已经设置了 cite 属性，我们继续有趣的部分:在报价的底部创建一个“Source”链接。

```
 var a = document.createElement('a'); 
    a.setAttribute('href', cite); 
    a.setAttribute('title', cite);
```

当我们希望使用 DOM 向页面动态添加新的 HTML 元素时，正确的做法是使用 createElement 方法以编程方式创建元素。上面几行创建了一个新的“a”元素，并赋予它`href`和`title`属性，这两个属性都被设置为引用的 URL。
`a.appendChild(document.createTextNode('Source'));`

我们希望 link 元素包含一些文本，用户可以单击这些文本来激活链接。使用`createTextNode`方法创建原始文本节点。DOM 将 HTML 元素视为一棵树，因此要将文本添加到我们新创建的链接中，我们需要调用它的`appendChild`方法。

```
 var p = document.createElement('p'); 
    p.className = 'blockquotesource'; 
    p.appendChild(a);
```

为了允许我们使用 CSS 灵活地设置新链接的样式，我们可以将它包装在一个段落元素中。上面的代码创建了这样一个元素，将它的类设置为'`blockquotesource`'来为我们的 CSS 提供一个钩子，然后使用`appendChild`向它添加链接。此时，我们构建的新文档片段相当于下面的 HTML:

```
<p class="blockquotesource"> 
<a href="[cite URL]" title="[cite URL]">Source</a> 
</p>
```

此时，我们的片段仍然是不可见的，因为虽然我们已经在内存中创建了它，但是我们还没有将它附加到文档中。函数中的最后一行就是这样做的:

```
 quotes[i].appendChild(p);
```

`quotes[i]`是我们当前正在处理的 blockquote 元素。`appendChild`将新段落附加到块引用，使其在过程中可见。

还有两步。首先，我们需要上面的函数在页面第一次加载时运行。有许多方法可以实现这一点。最简单的方法是向文档 body 元素的 onload 属性添加一个函数调用:

```
<body onload="extractBlockquoteCitations();">
```

这很好，但我们可以做得更好。既然我们的 Javascript 函数将被托管在一个外部文件中，那么外部文件让函数也运行不是很有意义吗？最简单的方法是使用下面一行 Javascript 代码:

```
window.onload = extractBlockquoteCitations;
```

//

注意，我们已经提供了函数的名称，但是忽略了最后的`()`，这会导致函数执行。Javascript 支持函数式编程风格，这意味着函数可以像任何其他数据对象一样对待，可以作为参数传递，存储在数据结构中，甚至可以从其他函数返回。我将在以后的文章中更多地讨论这个话题，但是结果是，将一个函数分配给`window.onload`将导致它在页面完成加载后执行。

然而，这种解决方案也有一个缺点。如果您想在一个给定的页面上使用多个在页面加载完成后执行的脚本，那么最后一个向`window.onload`注册的脚本将是唯一要执行的脚本。真正需要的是将我们的函数附加到窗口对象的 onload 处理程序上，而不覆盖已经存在的内容。不幸的是，Internet Explorer 和其他浏览器在如何处理这种动态事件附件上有所不同；幸运的是，Scott Andrew 发布了一个函数来处理这些差异。函数如下:

```
function addEvent(obj, evType, fn){ 
  if (obj.addEventListener){ 
    obj.addEventListener(evType, fn, false); 
    return true; 
  } else if (obj.attachEvent){ 
    var r = obj.attachEvent("on"+evType, fn); 
    return r; 
  } else { 
    return false; 
  } 
}
```

下面是将 blockquotes 函数添加到窗口对象的 load 事件中的代码:

```
addEvent(window, 'load', extractBlockquoteCitations);
```

最后一步是使用 CSS 样式化我们的报价。下面是一个相对简单的处理块引号的 CSS 片段:

```
blockquote { 
  border-left: 0.25em solid navy;  
  padding: 0 0.5em;  
  margin: 0.5em 1.5em 0.5em 2.5em;  
} 
blockquote p.blockquotesource { 
  font-weight: bold; 
  font-size: 0.8em; 
  text-align: right; 
  padding-top: 0.5em; 
}
```

成品可以在这里查看[。](https://www.sitepoint.com/examples/markup/blockquotes-citations-demo.html)

##### 面板切换

现在，让我们考虑一个更高级的动态效果——面板切换器。这里的目标是在一个页面上有许多面板(用 div 标记),一次只能看到其中的一个。一组持续可见的链接可用于选择当前显示的面板。这对于构建类似选项卡式界面的东西很有用，它可以浏览一系列相关的屏幕，而不需要每次选择一个选项卡就刷新页面。

每当使用 Javascript 增强页面时，要记住的一条好规则是，在禁用 Javascript 的情况下，页面必须仍然可用。在这种情况下，这意味着理想的解决方案将在 Javascript 打开的情况下工作，但将在非 Javascript 环境中显示页面上的所有面板，每个链接都使用 URL 片段直接链接到相关面板。

下面是可能有效的最简单的标记:

```
<a href="#p1">Panel 1</a> | <a href="#p2">Panel 2</a>  

<div id="p1">This is Panel 1</div>  
<div id="p2">This is Panel 2</div>
```

令人惊讶的是，以上几乎是我们需要的所有标记，以便挂钩一些 Javascript 来创建想要的效果。我们可以直接使用上面的代码，但是让我们给链接添加一个类来明确声明我们希望对它们做一些特殊的事情:

```
<a href="#p1" class="toggle">Panel 1</a> |   
<a href="#p2" class="toggle">Panel 2</a>
```

//

下面是 Javascript 的工作方式。当页面加载时，脚本将扫描页面上的所有链接，查找任何在它们的类中有“toggle”的链接。对于找到的任何元素，都将检查 href 属性，并找到具有指定 ID 的元素，将其添加到目标元素的数组中。除了第一个元素之外，所有这些元素都将被“关闭”，因此当页面加载时，只有第一个面板将保持可见。链接本身会附带 Javascript 事件处理程序，这样当它们被激活时，就可以显示相应的面板。

完整的脚本可以在这里[查看。接下来是对代码工作原理的介绍。](http://simon.incutio.com/code/js/easytoggle/easytoggle2.js)

```
var et_toggleElements = [];
```

第一行创建了一个全局空数组，它将保存对页面上面板元素的引用。因为这个脚本有一个全局变量和许多函数，我们将在每个函数前面加上前缀“`et_`”(表示“简单切换”)—这降低了我们的函数与同一页面加载的其他脚本发生名称冲突的可能性。

```
/* Initialisation */  
function et_init() {  
  var i, link, id, target, first;  
  first = true;  
  for (i = 0; (link = document.links[i]); i++) {
```

到目前为止，我们已经初始化了一些变量，将第一个标志设置为 true，并开始遍历文档中的所有链接。使用 var 声明变量很重要，因为它确保变量是函数的局部变量。如果没有这一步，它们将是全局可访问的，并可能干扰其他脚本。

```
 if (/btoggleb/.exec(link.className)) {
```

该条件检查当前链接的类中是否有“toggle”。我们使用一个正则表达式，而不仅仅是检查是否`link.className == 'toggle'`，因为 class 属性可以包含多个类，用空格分隔。`/btoggleb/`是正则表达式；`b`部分匹配一个“单词边界”，它可以是一个空格，也可以是字符串的开头或结尾。

```
 id = link.href.split('#')[1];

If the link has toggle in its list of classes, we assume that the target of the link is a URL fragment.

```

```
link.href.split('#') splits the link href at the # mark -- we know that the part we're interested in comes after the #, so we directly index the resulting array with [1] to pull out the targeted ID.

```

```
 target = document.getElementById(id);  
      et_toggleElements[et_toggleElements.length] = target;
```

这里，我们做另一个假设——链接指示的元素实际存在。我们使用`getElementById()`方法获取该元素，然后通过将它赋给等于当前数组长度的数组索引，将其添加到我们的元素数组中。这是可行的，因为数组从 0 开始索引，但是数组长度从 1 开始计数；因此，数组的长度也是数组中下一个空槽的索引。

```
 if (first) {  
        first = false;  
      } else {  
        target.style.display = 'none';  
      }
```

这就是我们前面定义的第一个标志发挥作用的地方。我们希望网站上的第一个面板保持可见，而其他面板使用 CSS 中的 Javascript 等价物'`display: none`'隐藏。旗帜允许我们这样做。

```
 link.onclick = et_toggle;  
    }  
  }  
}
```

最后，我们将`et_toggle`函数分配给链接的`onclick`事件，使得该函数在链接被激活时被调用。下一步是定义函数。

```
function et_toggle(e) {   
  if (typeof e == 'undefined') {   
    var e = window.event;   
  }   
  var source;   
  if (typeof e.target != 'undefined') {   
    source = e.target;   
  } else if (typeof e.srcElement != 'undefined') {   
    source = e.srcElement;   
  } else {   
    return true;   
  }
```

第一块代码的存在是为了弥补 Internet Explorer 和其他浏览器处理事件的方式之间的另一个差异。我们需要知道函数被调用时哪个链接被激活，因为这将允许我们确定应该显示哪个面板。上面的代码标识了事件起源的元素，并将其放入源变量中，代码改编自 Peter-Paul Koch 在 [QuirksMode](http://www.quirksmode.org/js/events_properties.html) 上对该问题的精彩解释:

```
 if (source.nodeType == 3) {   
    source = targ.parentNode;   
  }
```

这段代码是为了与 Safari 兼容。我测试的所有其他浏览器都返回实际的 link 元素作为 click 事件的源，但是 Safari 返回的是 link 中包含的文本节点。文本节点的`nodeType`设置为 W3C DOM 定义的常数)，因此，通过检查这一点，我们可以识别这个问题，并将目标重置为文本节点的父节点，它应该是 link 元素。

```
 var id = source.href.split('#')[1];
```

//

我们再次使用 split 方法从链接中提取 ID。

`var elem;
for (var i = 0; (elem = et_toggleElements[i]); i++) {
if (elem.id != id) {
elem.style.display = 'none';
} else {
elem.style.display = 'block';
}
}`
现在我们知道了想要显示哪个面板，我们可以循环遍历我们的元素数组，除了 ID 与所需面板的 ID 相匹配的面板之外，关闭所有的元素。

```
 return false;   
}
```

通过返回 false，我们可以防止链接在被激活时被跟踪，这可能会导致在浏览器中查看页面时出现不希望的向下跳转。

最后一步是使用前面描述的 addEvent 函数向窗口的 load 事件注册`et_init`函数。

```
addEvent(window, 'load', et_init);
```

你可以在这里看到完成的代码。

##### 结论

在本文中，我们看到了两种将结构良好的标记与 Javascript 和 W3C DOM 结合起来实现有用效果的方法。我希望这篇文章能够启发您寻找使用 Javascript 和智能标记的新方法。

##### 进一步阅读

基于结构化标记的 Javascript 效果还有很多其他优秀的例子。以下是值得一试的几个例子:

*   Labels.js by Aaron Boodman
    [https://www.sitepoint.com/article/1206/](https://www.sitepoint.com/blog/) 
*   Nice Titles by Stuart Langridge
    [http://www.kryogenix.org/code/browser/nicetitle/](http://www.kryogenix.org/code/browser/nicetitle/) 
*   aqLists by Stuart Langridge
    [http://www.kryogenix.org/code/browser/aqlists/](http://www.kryogenix.org/code/browser/aqlists/) 
*   sorttable by Stuart Langridge
    [http://www.kryogenix.org/code/browser/sorttable/](http://www.kryogenix.org/code/browser/sorttable/) 
*   彼得·保罗·科赫
    [http://www.quirksmode.org/dom/toc.html](http://www.quirksmode.org/dom/toc.html)目录

## 分享这篇文章