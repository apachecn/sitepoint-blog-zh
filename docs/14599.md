# 通过 DHTML 旋转常规 HTML 内容

> 原文：<https://www.sitepoint.com/rotate-content-with-dhtml/>

使用客户端技术(如 JavaScript)来按需显示内容的一个很大的缺陷是所有内容都必须包含在变量中。这使得添加和更新内容非常麻烦。

一个例子是在 JavaScript 滚动条中输入显示的消息——这通常不是一个有趣的体验:

```
<script type="text/javascript"> 

var message=new Array(); 

message[0]="<b><a href='http://www.javascriptkit.com'>Click here</a> for JavaScript Kit!</b>"; 

message[1]="<a href='http://www.google.com'>Google it</a>"; 

" 

" 

</script>
```

在本教程中，我们将研究如何使用 DHTML 来帮助突破这一限制，以便页面上的普通 HTML 内容可以按照脚本的指示动态显示，而无需对内容本身进行任何更改。

##### 总的想法

基本思想是这样的——所有现代浏览器(IE4+/NS6+)都支持 CSS 属性 display:none，它完全隐藏它所应用的任何内容。通过利用这个有用的 CSS 属性，我们可以选择我们想要隐藏的页面内容，然后使用脚本根据我们的脚本议程动态地将它们重新激活。关键是要设计一种健壮的方法来“标记”页面上的这些内容，这样我们的脚本就可以轻松地识别、收集和操作它们。

让我们把理论变成行动，一步一步来。

##### 使用 CSS 隐藏 HTML 内容

为了从我们的页面中提取常规的 HTML，并基于更具选择性的过程——我们的脚本——来显示它，首先，我们需要隐藏内容。如前所述，CSS 属性显示:none 将很好地完成工作，但也有一个 NS4 兼容性问题。你看，NS4 把这个属性的所有载体单向发送到黑洞。为了确保我们的内容是向后兼容的，最简单的解决方案是动态地写出样式表:

```
<script type="text/javascript"> 

if (document.all || document.getElementById){ //if IE4 or NS6+ 

  document.write('<style type="text/css">'); 

  document.write('.dyncontent{display:none;}'); 

  document.write('</style>'); 

} 

</script> 

<p class="dyncontent">This content is hidden from view initially</p> 

<p class="dyncontent">This content is hidden from view initially as well</p> 

<p >This content is not hidden from view.</p>
```

请注意，我们使用了 CSS 的“class”属性来标识所选择的内容，并对其应用样式。这允许使用比使用 ID 属性更通用的方法来“标记”内容。在 NS4 等传统浏览器中，任何内容都不会被隐藏，所以这些用户至少可以阅读你的页面。

隐藏了内容之后，我们现在需要的是使用 DHTML 从这些隐藏的内容项中创建一个自定义集合。天空是我们对这样一个收藏所能做的极限；例如，我们可以选择在滚动条中一次显示一个内容项。

##### 用页面上的“类”元素创建自定义集合

到目前为止，我们已经通过动态生成适当的样式表并通过 CSS 的“class”属性将其应用于内容，隐藏了页面上的某些内容:

```
<script type="text/javascript"> 

if (document.all || document.getElementById){ //if IE4 or NS6+ 

  document.write('<style type="text/css">'); 

  document.write('.dyncontent{display:none;}'); 

  document.write('</style>'); 

} 

</script> 

<p class="dyncontent">This content is hidden from view initially</p> 

<p class="dyncontent">This content is hidden from view initially as well</p> 

<p >This content is not hidden from view.</p>
```

真正的问题是，我们如何将这些内容放到我们的脚本中？当然是通过崇拜现代浏览器的 DOM(文档对象模型)啦！

DOM 允许我们从理论上评估页面上的任何元素并对其进行操作。现在，您可能熟悉像`document.getElementById`或`document.getElementsByTagName`这样的方法，它们允许您基于这两个标准访问一个元素。然而，目前还没有通过类名(从 IE6/NS7 开始)检索元素的预构建方法，这是我们在上面选择的识别内容的方法。然而，并非一切都没了。我们还是可以用迂回的方式:

```
<script type="text/javascript"> 

function getElementbyClass(classname){ 

  var inc=0; 

  var alltags=document.all? document.all : document.getElementsByTagName("*"); 

  for (i=0; i<alltags.length; i++){ 

    if (alltags[i].className==classname) 

      customcollection[inc++]=alltags[i]; 

  } 

} 

getElementbyClass("dyncontent"); 

</script>
```

这里的关键是行:

```
var alltags=document.all? document.all: document.getElementsByTagName("*");
```

我们依靠`document.getElementsByTagName("*")`首先检索页面上的所有元素(*表示“一切”)，然后扫描每个元素，看它是否带有我们感兴趣的类名。如果是这样，它将被添加到我们的自定义收藏中。注意，如果支持的话,`document.all`也会被使用——这是为了不识别`document.getElementById`的 IE4 浏览器，但是用`document.all`可以模拟完成工作所需的内容。

这是一个很大的障碍。有了我们的定制集合，您对常规 HTML 页面内容的处理就变成了一件充满想象力的事情。现在让我们用它制作一个幻灯片！

##### 示例:丰富的 HTML 幻灯片

你准备好利用我们到目前为止所讨论的来创造一些有用的东西了吗？在你的页面上旋转显示常规 HTML 内容的 DHTML 幻灯片怎么样？

```
<script type="text/javascript"> 

if (document.all || document.getElementById){ //if IE4 or NS6+ 

  document.write('<style type="text/css">n'); 

  document.write('.dyncontent{display: none; width: 250px; height: 60px;}n'); 

  document.write('</style>'); 

} 

var curcontentindex=0; 

var messages=new Array(); 

function getElementByClass(classname){ 

  var inc=0; 

  var alltags=document.all? document.all : document.getElementsByTagName("*"); 

  for (i=0; i<alltags.length; i++){ 

    if (alltags[i].className==classname) 

      messages[inc++]=alltags[i]; 

  } 

} 

function rotatecontent(){ 

  //get current message index (to show it): 

  curcontentindex=(curcontentindex<messages.length-1)? curcontentindex+1 : 0; 

  //get previous message index (to hide it): 

  prevcontentindex=(curcontentindex==0)? messages.length-1 : curcontentindex-1; 

  messages[prevcontentindex].style.display="none"; //hide previous message 

  messages[curcontentindex].style.display="block"; //show current message 

} 

window.onload=function(){ 

  if (document.all || document.getElementById){ 

    getElementByClass("dyncontent"); 

    setInterval("rotatecontent()", 2000); 

  } 

} 

</script> 

Dynamic Content: 

<div class="dyncontent" style="display: block">First scroller content (visible)</div> 

<div class="dyncontent">Second scroller content</div> 

<div class="dyncontent">Third scroller content</div> 

The rest of your page.
```

来自[动态驱动](http://www.dynamicdrive.com/)的 [ProHTML Scroller](http://www.dynamicdrive.com/dynamicindex2/prohtmlticker.htm) 脚本使用这种技术创建一个滚动器，旋转页面上的常规 HTML 块。尽情享受吧！

## 分享这篇文章