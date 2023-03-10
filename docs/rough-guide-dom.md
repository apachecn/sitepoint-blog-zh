# 大教堂粗略指南

> 原文：<https://www.sitepoint.com/rough-guide-dom/>

欢迎来到大教堂粗略指南。

在两个部分中，本系列介绍了文档对象模型，解释了它的优点，并探索了它的实现。

**[DOM 粗略指南——第 1 部分](http://www.webmasterbase.com/article/872/9)**

这是所有 Web 开发人员的祸根——冲突的标准、浏览器的不兼容性，以及每次新的浏览器版本上线时都要修改的代码。

但是不要害怕——一位英雄骑士骑着白色骏马冲了进来，他穿着新 W3C DOM 的服装，带着让这场噩梦永远结束的工具。请继续阅读，了解新的 DOM 如何最终为绝对非标准的 Web 世界带来一些标准。

**[DOM 粗略指南——第二部分](http://www.webmasterbase.com/article/872/101)**

既然您已经了解了新 DOM 背后的理论，是时候脱下手套，亲自动手了。

在本文中，我们将了解新规则如何应用于图像交换、表单验证和框架导航等旧规则，然后学习如何使用普通的 JavaScript 动态地在文档树中添加和删除元素。

##### DOM 粗略指南—第 1 部分

丹麦的现状

在莎士比亚的《哈姆雷特》中，其中一个角色说了一句著名的话，“丹麦国有些东西已经腐烂了”。每次我坐下来写一些 dHTML 代码时，我都会对他的清晰感到惊讶。这个评论充满了厄运的意味，是对两个主要浏览器之间众多不兼容性的完美评估，以及它们给开发者带来的日常问题，很难不让人发笑。我会的…如果我还没有哭成一团。

这些不兼容性在被称为 DOM 或文档对象模型的领域中尤其突出，DOM 是访问文档中每个元素及其属性的标准方法。当最初引入 DOM 的原始版本时，开发人员立即意识到它在为静态 Web 页面添加新的交互性方面是多么有用。然而，随着两个主要浏览器向不同方向发展，开发互不兼容的 DOM 结构，这种兴奋很快变成了失望——毕竟，没有开发人员喜欢为不同的浏览器编写同一脚本的不同版本。

当然，并非一切都没了。努力已经开始，最引人注目的是在 W3C 为所有浏览器建立通用标准。CSS 规范的发布，以及随后 DOM Level 0 和 Level 1 规范的发布，使得大多数主流浏览器都符合所提议的标准。另一方面:既然现在已经有了一个标准，浏览器制造商将很快停止支持他们以前的 DOM 版本……这意味着你写的所有代码和你设计的聪明的变通办法将不再适用于新版本的浏览器。

您已经可以看到这种情况的发生——专门为 Netscape 4.x 编写的代码不再适用于 Netscape 6.x，后者是基于 Mozilla 引擎构建的——因此，每个开发人员都需要了解新的 DOM 标准及其对 dHTML 代码开发的影响。

在接下来的几页中，我将举例说明一些新的 DOM 构造，以及如何在“真正的”HTML 文档中使用它们的例子。在这次旅程中，我最信任的马将是 Mozilla，这是一款在 http://www.mozilla.org/推出的非常棒的开源浏览器，号称是目前最兼容标准的浏览器。

在我们开始之前，一些免责声明。

首先，本教程并不意味着是对 DOM 的详尽参考——你可以为此买本书。它只是帮助您过渡到新的对象模型的指南。

其次，我并不自称是 DOM 专家，本教程中的许多材料都是基于我自己作为开发人员的经验。

最后，随着新的 DOM 标准的提出和处理，这里的材料可能会失效；您应该始终参考位于[https://www.w3.org/DOM/](https://www.w3.org/DOM/)的最新标准或建议，以获取最新信息(这是我最喜欢的文档之一，我失眠时会经常使用)。

手续办完了，我们开始吧。

##### 回归基础

我们将从基础开始——一个非常简单的 HTML 页面。

```
<html>  

<head></head>  

<body bgcolor="white">  

<div id="a" style="font-family: Arial; color: white;   

background: black">Wassup?</div>  

</body>  

</html>
```

让我们改变`<div>`中文本的字体颜色。在 Internet Explorer 中，这通常是通过

```
<script language="JavaScript">  

document.all.a.style.color = "red";  

</script>
```

以下是我会在 Mozilla 中使用的代码:

```
<script language="JavaScript">  

var obj = document.childNodes[0].childNodes[1].childNodes[0];  

obj.style.color = "red";  

</script>
```

这里需要解释一下。在新的 DOM 下，HTML 文档中的每个元素都是“树”的一部分，您可以通过浏览树的“分支”直到到达相应的“节点”来访问每个元素。考虑到这一点，下面是我对上面的 HTML 文档的“树形”表示。

```
document  

| -- <html>  

| -- <head>  

| -- <body>  

| -- <div>
```

现在，为了到达`<div>`，我需要:

1.  从顶部开始(“`document`”)；

3.  向下移动到主分支——`<html>`标记，或“`document.childNodes[0]`”；

5.  然后是第二个子分支——`<body>`标签或“`document.childNodes[0].childNodes[1]`”；

7.  然后到`<div>`—`document.childNodes[0].childNodes[1].childNodes[0]`；

此时，我已经成功地导航到了文档树中的`<div>`元素。验证这一点的快速方法是在对象上使用一个`alert()`

```
<script language="JavaScript">  

var obj = document.childNodes[0].childNodes[1].childNodes[0];  

alert(obj.nodeName);  

obj.style.color = "red";  

</script>
```

它在一个警告框中显示标签的名称–`DIV` 。

在这一点上，我可以开始 futzing 与对象属性-在上面的例子中，我已经改变了“颜色”样式属性。暂时不要担心这个；简单地验证您已经理解了我通过文档树导航到 `DIV`的方式。

版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### 浏览家谱

除了各种`childNodes[]`之外，DOM 还提供了许多其他对象/属性，可以简化文档元素之间的导航。

*   **`firstChild`** –对集合中第一个子节点的引用
*   **`lastChild`**–集合中最后一个子节点的引用
*   **`parentNode`**–对树中上一级节点的引用
*   **`nextSibling`** –对当前集合中下一个节点的引用
*   **`previousSibling`**–当前集合中上一个节点的引用

因此，参考上面的例子，我可以使用下面的任何一条替代路线来导航到`<div>`标记。

```
document.childNodes[0].childNodes[1].firstChild   

document.childNodes[0].firstChild.nextSibling.firstChild   

document.childNodes[0].childNodes[1].firstChild.firstChild.parentNode
```

树中的每个子节点可以是一个 HTML 标签，也可以是一个“文本节点”。这带来了一个重要的问题——各种标签之间的空格和回车会影响文档树结构，在树结构中创建文本节点，并在您调整代码以适应新的树时导致许多咬牙切齿。

##### 名称又能代表什么呢

正是因为这个原因，DOM 提供了一种更快、更有效的方法来访问页面中的元素——`getElementById()`方法。

我重写了上面的例子来演示如何使用这个方法。

```
<script language="JavaScript">   

var obj = document.getElementById("a");   

obj.style.color = "red";   

</script>
```

正如你所看到的，这是非常简单的阅读…和编码。

每个节点都有一些对开发人员来说很方便的基本属性——例如,“nodeName”属性返回标记名，而“nodeType”属性返回一个表示节点类型的数字(HTML tag = 1；HTML 标签属性= 2；文本块=3)。如果节点碰巧是文本节点而不是标记，则“数据”和“节点值”属性返回文本字符串。

以下示例演示了如何访问各种节点属性–取消对各种 alert()方法调用的注释，以显示各种对象属性。

```
<html>   

<head></head>   

<body id="body" bgcolor="white"><font face="Arial"    

size="2">This stuff is giving me a headache already!</font>   

<script language="JavaScript">   

// get to the <font> tag   

var fontObj = document.getElementById("body").childNodes[0];   

// check the tag - returns "FONT"   

// alert(fontObj.nodeName);   

// check the type of node - returns 1   

// alert(fontObj.nodeType);   

// get the text within the <font> tag   

var textObj = fontObj.childNodes[0];   

// check the text value - returns "This stuff is giving    

me a headache already!"   

// alert(textObj.data);   

// check the type of node - returns 3   

// alert(textObj.nodeType);   

</script>   

</body>   

</html>
```

顺便提一下——一个不包含数据的文本节点向“nodeName”属性返回值“`#text`”——试着用几个空格替换上面的`<font>`标记中的文本行，看看我的意思是什么。

##### 鸭子排成一行

除了通常用于获取对特定元素的引用的`getElementById()` 方法，DOM 还提供了用于返回特定类型元素集合的`getElementsByTagName()` 方法。例如，代码

```
document.getElementsByTagName("form");
```

将返回一个集合或数组，包含对文档中所有`<form>`标签的引用。每个引用都是一个节点，然后可以使用标准的 DOM 方法和属性进行操作。

考虑下面的文档，它包含三个`<div>`，每个包含一行文本

```
<html>   

<head>   

</head>   

<body bgcolor="white">   

<div id="huey">   

Huey here!   

</div>   

<div id="dewey">   

Dewey in the house!   

</div>   

<div id="louie">   

Yo dude! How's it hangin'?   

</div>   

</body>   

</html>
```

然后研究我用来在第二个`<div>`中操作文本的代码

```
<script language="JavaScript">   

// get a list of all <div> tags   

var divCollection = document.getElementsByTagName("div");   

// get a reference to the second <div> in the collection   

var deweyObj = divCollection[1];   

// verify that we are where we think we are   

// alert(deweyObj.getAttribute("id"));   

// change the text string within the <div>   

deweyObj.childNodes[0].data = "Dewey rocks!";   

</script>
```

一个文档中所有标签的集合(很像“`document.all`”)可以用

```
document.getElementsByTagName("*");
```

版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### 改变周围的事物

现在您已经知道如何找到文档中特定 HTML 元素的方法，是时候学习如何操作它们了。因为这种操作大部分都涉及动态改变标签属性，所以 DOM 提供了`getAttribute()`和`setAttribute()` 方法，它们是专门为此目的设计的。

考虑对您刚才看到的示例的以下修改，它使用这两种方法来改变字体大小和文本字符串。

```
<html>    

<head></head>    

<body id="body" bgcolor="white"><font face="Arial"     

size="2">This stuff is giving me a headache already!</font>    

<br>    

Click to <a href="javascript:increaseFontSize();">increase font size</a>     

or <a href="javascript:changeText()">change text string</a>    

<script language="JavaScript">    

// get to the <font> tag    

var fontObj = document.getElementById("body").childNodes[0];    

// check the tag - returns "FONT"    

// alert(fontObj.nodeName);    

// check the type of node - returns 1    

// alert(fontObj.nodeType);    

// get the text within the <font> tag    

var textObj = fontObj.childNodes[0];    

// check the text value - returns "This stuff is giving     

me a headache already!"    

// alert(textObj.data);    

// check the type of node - returs 3    

// alert(textObj.nodeType);    

function changeText()    

{    

// alter the node value    

textObj.data = "I need some aspirin. Now.";    

}    

function increaseFontSize()    

{    

// get the value of the "size" attribute of the node    

var size = fontObj.getAttribute("size");    

// increase by 1    

size += 1;    

// set the new value    

fontObj.setAttribute("size", size);    

}    

</script>    

</body>    

</html>
```

我在这里使用了两种不同的方法。为了改变字体大小，我首先使用`getAttribute()` 方法返回属性的当前值，然后使用`setAttribute()`方法写入一个新值。然而，改变文本字符串只是简单地改变文本节点的“数据”属性的值。

使用`getAttribute()`和`setAttribute()`时，有几件事要记住。所有属性名都应该是小写的，名称和值都应该用引号括起来(如果省略引号，值将被视为变量)。显然，您应该只使用与所考虑的标签相关的属性——例如，您不能在`<font>`标签上使用`setAttribute("src")`。

##### 可供选择的事物

获取(和设置)属性值的另一种方法是通过`attributes[]` 集合，它本质上是一个数组，包含特定标记的所有属性值对的列表。我修改了前面的例子来说明这是如何工作的——取消对各种`alert()`的注释来查看不同属性的值。

```
<html>    

<head></head>    

<body id="body" bgcolor="white">    

<font face="Arial" size="2">This stuff is giving me     

a headache already!</font>    

<script language="JavaScript">    

// get to the <font> tag    

var fontObj = document.getElementById("body").childNodes[0];    

// return the number of attributes of the <font> tag    

// or the length of the attributes[] collection    

// returns 2    

// alert(fontObj.attributes.length);    

// returns the name of the first attribute - "face"    

// alert(fontObj.attributes[0].name);    

// returns the value of the first attribute - "Arial"    

// alert(fontObj.attributes[0].value);    

// changes the value of the first attribute to "Verdana"    

fontObj.attributes[0].value = "Verdana";    

// returns the new value of the first attribute - "Verdana"    

// alert(fontObj.attributes[0].value);    

</script>    

</body>    

</html>
```

版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### 沙赞姆。

DOM 还允许您修改特定 HTML 标签的 CSS 属性——如下例所示:

```
<html>     

<head>     

<script language="JavaScript">     

function disappear()     

{     

var obj = document.getElementById("mirage");     

obj.style.display = "none";     

}     

</script>     

</head>     

<body>     

<div id="mirage">     

Now you see it...     

</div>     

<a href="javascript:disappear()">...now you don't!</a>     

</body>     

</html>
```

我已经在本文的第一个例子中做了类似的事情——当你在做的时候，也看看那个例子。

使用这种技术，几乎可以对页面上的元素应用任何内联样式。请记住，用连字符连接的样式属性(例如，“`background-color`”和“`font-family`”)需要写成一个单词，连字符后的第一个字符大写(例如，“`backgroundColor`”和“`fontFamily`”)。下一个例子应该清楚地说明了这一点:

```
<html>     

<head>     

<script language="JavaScript">     

function transform()     

{     

var obj = document.getElementById("marvel");     

obj.style.fontFamily = "Verdana";     

obj.style.fontSize = "40pt";     

obj.style.backgroundColor = "red";     

obj.style.color = "black";     

obj.style.textDecoration = "underline";     

obj.style.textAlign = "center";     

obj.style.letterSpacing = "10";     

}     

</script>     

</head>     

<body>     

<div id="marvel">     

Captain Marvel     

</div>     

<a href="javascript:transform()">shazam!</a>     

</body>     

</html>
```

暂时就这些了。在本文的第二部分，我将使用新的 DOM 结构浏览一些简单 JavaScript 应用程序的简单代码示例——图像交换、表单验证和框架导航。我还将讨论`appendChild()`和`createNode()`函数，它们允许开发人员通过程序代码向文档树添加新元素。不要错过！

注意:本文中的所有例子都已经在 Mozilla (build 18)上测试过了。这些示例仅用于说明，并不适用于生产环境。YMMV！
<font size="1" color="#aaaaaa" face="Verdana,Arial,Helvetica">版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。</font>

##### DOM 粗略指南-第 2 部分

深入挖掘

在本文的第一部分中，我向您介绍了通过 DOM 导航 HTML 文档的基础知识，并解释了您可以使用的各种方法和集合。如果您理解了所有这些(我希望您理解了)，那么您现在应该对如何操作一个典型的 HTML 文档，以及如何动态地更改界面元素有了一个非常清晰的概念。

在接下来的几页中，我将更深入地研究 DOM，并举例说明 DOM 如何与表格、表单、图像和框架进行交互。我还将讨论一些通过 JavaScript 向 DOM 树添加(和修改)节点的方法，并向您介绍一些关于这个主题的更好的 Web 资源。

让我们开始吧！

##### 制作`Swap()`

今天议程的第一项是演示如何使用 DOM 来实现最流行的 dHTML 应用程序之一——图像交换。看看下面的 HTML 文档:

```
<html>      

<head>      

</head>      

<body>      

<a href="http://www.melonfire.com/" onMouseOver="javascript:imageSwap();"      

onMouseOut="javascript:imageSwap();"><img id="logo" src="logo_n.gif"      

width=50 height=50 border=0></a>      

</body>      

</html>
```

现在，我已经设置好了，图像上的“`mouseover`”和“`mouseout`”事件由 JavaScript 函数`imageSwap()`处理。这个函数负责在每次事件发生时交换图像——所以让我们来看看它。

```
<script language="JavaScript">      

var normal = "logo_n.gif";      

var hover = "logo_h.gif";      

function imageSwap()      

{      

var imageObj = document.getElementById("logo");      

var imageSrc = imageObj.getAttribute("src");      

  if (imageSrc == normal)      

    {      

    imageObj.setAttribute("src", hover);      

    }      

  else      

    {      

    imageObj.setAttribute("src", normal);      

    }      

}      

</script>
```

如果你还记得我上次教你的，这些都不应该是一个惊喜。我首先定义了“`normal`”和“`hover`”状态图像，然后创建了一个名为`imageSwap()`的函数，每当鼠标移出图像时都会调用这个函数。

`imageSwap()`函数通过它的 ID 获得对图像的引用，然后获得图像的“`src`”属性的当前值。然后，它根据“`normal`”和“`hover`”变量的值检查该值，并相应地更改图像源。

##### 扭转局势

接下来，桌子。看看下面的 HTML 文档，它包含一个有两行、每行三个单元格的表格。

```
<html><head></head><body><table border="1" cellspacing="5"      

cellpadding="5"><tr><td>R1, C1</td><td>R1, C2</td><td>R1,      

C3</td></tr><tr><td>R2, C1</td><td>R2, C2</td><td id="r2c3">R2,      

C3</td></tr></table></body></html>
```

现在，当在一个表格中导航时，需要注意一个要点——从 DOM vie 来看，一个表格必须被视为在`<table>`标签之后和`<tr>` 标签之前包含一个额外的`<tbody>`标签。将此添加到等式中，上面的页面现在看起来像这样:

```
<html><head></head><body><table border="1" cellspacing="5"      

cellpadding="5"><tbody><tr><td>R1, C1</td><td>R1, C2</td><td>R1,      

C3</td></tr><tr><td>R2, C1</td><td>R2, C2</td><td id="r2c3">R2,      

C3</td></tr></tbody></table></body></html>
```

通常的 DOM 导航规则现在适用，如下例所示。这个脚本深入到第二行的最后一个单元格，改变单元格的背景颜色和其中的文本字符串。

```
<script language="JavaScript">      

// get to the cell      

// you could also use       

// var cellObj =      

document.childNodes[0].childNodes[1].childNodes[0].childNodes[0].      

childNodes[1].childNodes[2];      

var cellObj = document.getElementById("r2c3");      

// get to the text within the cell      

var cellTextObj = cellObj.childNodes[0];      

// set background colour      

cellObj.setAttribute("bgcolor", "red");      

// and text      

cellTextObj.data = "Second row, third column";      

</script>
```

版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### 格式良好的

JavaScript 的另一个非常流行的应用是表单验证——验证输入到在线表单中的数据。在下一个示例中，我将使用 DOM 和一些简单的 JavaScript 来确保复选框被选中，并且在文本字段中输入的电子邮件地址是正确的格式。这是表格:

```
<html>       

<head>       

</head>       

<body>       

<form action="somescript.cgi" method="post"        

onSubmit="return check();">       

Email address:       

<br>       

<input id="email" type="text" name="email" size="30">       

<br>       

<input id="spam_me" type="Checkbox" name="spam_me">Yes, I        

want you to send me megabytes of useless advertisements via        

email. I love buying crummy products from people who        

probably flunked kindergarten.       

<br>       

<input type="submit">       

</form>       

</body>       

</html>
```

这是验证脚本:

```
<script language="JavaScript">       

function checkEmail()       

{       

// get to the field       

var obj = document.getElementById("email");       

// value of field       

var str = obj.value;       

// define pattern to match email address       

var pattern =       

/^([a-zA-Z0-9])+([.a-zA-Z0-9_-])*@([a-zA-Z0-9_-])+       

(.[a-zA-Z0-9_-]+)+/;       

// match the string to the pattern       

var flag = pattern.test(str);       

  if(!flag)       

  {       

  alert ("Please enter a valid email address");       

  return false;       

  }       

  else       

  {       

  return true;       

  }       

}       

function checkSpam()       

{       

// get to the field       

var obj = document.getElementById("spam_me");       

// checkbox ticked?       

  if (obj.checked)       

  {       

  return true;       

  }       

  else       

  {       

  alert ("Please check the box");       

  return false;       

  }       

}       

function check()       

{       

// perform validation and submit form if all is well       

  if(checkEmail() && checkSpam())       

  {       

  return true;       

  }       

  else       

  {       

  return false;       

  }       

}       

</script>
```

如您所见，只有在从 JavaScript 函数 `check()`收到肯定的(`true`)结果后，表单才会被提交。这个函数依次调用函数`checkEmail()` 和`checkSpam()`，它们首先获取对各自表单元素的引用，然后检查它们的值是否有效。

版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### 在相框里

了解 DOM 如何处理框架也很有趣。考虑下面的例子，它设置了两个帧，“T0”和“T1”。

```
<html>        

<head>        

</head>        

<frameset  cols="20%,*">        

    <frame name="left" src="left.html" scrolling="auto"         

    frameborder="no">        

    <frame name="right" src="right.html"  scrolling="auto"         

    frameborder="no">        

</frameset>        

</html>
```

为了说明如何跨框架导航，我将编写一个简单的脚本，当在左框架中单击适当的链接时，它会改变右框架的背景颜色。这是正确的框架，

```
<html>        

<head>        

</head>        

<body id="body">        

</body>        

</html>
```

这里是左边的框架——注意每个链接如何调用带有颜色参数的`changeFrameBackground()` 函数。

```
<html>        

<head>        

</head>        

<body>        

<a href="javascript:changeFrameBackground('red')">Red</a>        

<br>        

<a href="javascript:changeFrameBackground('blue')">Blue</a>        

<br>        

<a href="javascript:changeFrameBackground('green')">Green</a>        

<br>        

<a href="javascript:changeFrameBackground('black')">Black</a>        

</body>        

</html>
```

最后，让我们看看完成所有工作的函数。

```
 <script language="JavaScript">        

var bodyObj = top.right.document.getElementById("body");        

function changeFrameBackground(col)        

{        

bodyObj.setAttribute("bgcolor", col);        

}        

</script>
```

因为这是一个框架集，所以有必要在`document.getElementById()`方法调用前面加上对适当框架的引用。这个前缀对于向 DOM 标识哪个框架被调用以及获得对正确文档树的引用是必要的。

一旦获得了对右边框架的`<body>`标签的引用，改变框架的背景颜色就简单了 `setAttribute()`。

##### 分支

DOM 还附带了许多内置方法，用于操纵 DOM 树，动态地添加和删除节点。正如您已经看到的，DOM 树上的节点可以是 HTML 标记或文本片段——DOM 附带了通过程序代码将这两种类型的节点添加到树中的方法。

我将从`createElement()` 方法开始，它用于创建一个新的 HTML 标签。下面的代码片段创建了一个`<img>`标签作为节点，并将其命名为“`imageObj`”。

```
<script language="JavaScript">        

var imageObj = document.createElement("img");        

</script>
```

一旦创建了节点，就可以使用`setAttribute()`方法为其分配属性。例如，代码片段

```
<script language="JavaScript">        

imageObj.setAttribute("src", "logo_n.gif");        

imageObj.setAttribute("width", "50");        

imageObj.setAttribute("height", "50");        

</script>
```

相当于标签

```
<img src="logo_n.gif" width="50" height="50">
```

一旦创建了节点，下一步就是将它添加到文档树中——这个任务由`appendChild()`方法完成。`appendChild()`方法用于将新创建的节点附加到树中的特定位置。

下面的代码片段将把“`imageObj`”节点附加为由“`heading1`”标识的元素的子元素。

```
<script language="JavaScript">        

document.getElementById("heading1").appendChild(imageObj);        

</script>
```

版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。

##### 把它变笨

正如您可以将 HTML 标记创建为节点一样，DOM 也允许您使用名副其实的`createTextNode()` 方法在树上创建新的文本节点。这里有一个例子:

```
 <script language="JavaScript">         

var insultObj = document.createTextNode("Could you *be* any dumber?");         

</script>
```

再次使用`appendChild()`方法将新的文本节点附加到文档树的适当分支。

```
<script language="JavaScript">         

document.getElementById("heading1").appendChild(insultObj);         

</script>
```

让我们看看这在现实生活中是如何发生的。我创建了一个简单的 HTML 页面，除了一组`<p>`标签和一些 JavaScript 代码之外，它什么也不包含。JavaScript 将创建一个新的文本节点和一个新的`<img>`标签，并使用我刚才演示的代码片段将它们作为`<p>`标签的子标签添加到文档树中。

```
<html>         

<head>         

</head>         

<body>         

<p id="heading1"></p>         

<script language="JavaScript">         

// set up the image         

var imageObj = document.createElement("img");         

imageObj.setAttribute("src", "logo.gif");          

imageObj.setAttribute("width", "50");          

imageObj.setAttribute("height", "50");          

document.getElementById("heading1").appendChild(imageObj);         

// set up the text node         

var insultObj = document.createTextNode("Could you *be* any dumber");         

document.getElementById("heading1").appendChild(insultObj);         

// use this for testing         

var pObj = document.getElementById("heading1");         

// returns IMG         

// alert (pObj.childNodes[0].nodeName);         

// returns #text         

// alert (pObj.childNodes[1].nodeName);         

</script>         

</body>         

</html>
```

尽管该页面只包含一个`<p>`标签，但是运行该脚本将向文档树添加一个`<img>`标签和一行文本，这将在浏览器中立即可见。

当然，DOM 还附带了许多其他方法——这里有一个简短的列表，并解释了它们的功能。

*   **`removeNode()`**–从文档树中删除一个节点(和/或其所有子节点)
*   **`replaceNode()`**–用另一个节点替换一个节点
*   **`cloneNode()`**–复制现有节点
*   **`swapNode()`**–交换文档树中两个节点的位置
*   **`insertBefore()`**–在文档树的特定点插入一个节点

其中大部分都是不言自明的，它们并不经常被使用，所以我不打算详细讨论它们——为了完整起见，它们被包括在这里。

##### 结论

如果您有兴趣了解更多关于 DOM 的知识，可以在网上找到很多资源。这里有一个简单的列表:

官方的 W3C DOM 规范，在[https://www.w3.org/DOM/](https://www.w3.org/DOM/)

Mozilla.org 开发者资源，在[http://www.mozilla.org/docs/](http://www.mozilla.org/docs/ )和

[http://www.mozilla.org/docs/web-developer/](http://www.mozilla.org/docs/web-developer/)

http://www.mozilla.org/docs/dom/samples/[的 DOM 样本代码](http://www.mozilla.org/docs/dom/samples/)

一篇关于从专有 DOM 向 W3C 标准过渡的有趣文章，在 http://sites.netscape.net/ekrockhome/standards.html 举行

DOM 的结构(逻辑)视图，位于[http://www.xml.com/1999/07/dom/xml_dom.gif](http://www.xml.com/1999/07/dom/xml_dom.gif)

对 DOM 的 XML 介绍，http://www.xml101.com/dom/

在我离开之前，最后一点意见。虽然新的 DOM 可能看起来远不如开发人员已经习惯的专有模型灵活和易用，但事实仍然是它提供了一个非常重要的优势:标准化。这个 DOM 的编写方式使得页面上的每个元素最终都可以通过标准导航规则提供给开发人员，并且可以使用标准对象方法和属性进行操作。

从短期来看，按照新的 DOM 重新编码网页可能很困难，甚至令人沮丧；然而，我相信这种努力是值得的，因为它可以立即确保您的页面可以在所有符合标准的浏览器上查看。应该注意的是，过去的许多混乱(以及随之而来的专有 DOM 接口的泛滥)是由于 W3C 缺乏明确的方向；既然 DOM 规范已经最终确定并发布，所有主流浏览器的未来版本都应该完全支持它，我们应该有希望看到过去困扰开发人员的浏览器不兼容性的终结。

希望如此！

注意:本文中的所有例子都已经在 Mozilla (build 18)上测试过了。这些示例仅用于说明，并不适用于生产环境。YMMV！
<font size="1" color="#aaaaaa" face="Verdana,Arial,Helvetica">版权所有[梅龙菲尔](http://www.melonfire.com/)，2000 年。保留所有权利。</font>

## 分享这篇文章