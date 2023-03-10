# 鼠标悬停图像

> 原文：<https://www.sitepoint.com/mouseover-images/>

在万维网上出现的最受欢迎的“惊喜”项目之一是鼠标悬停。最简单的形式是，鼠标悬停是网页的一部分，当用户的鼠标经过它时，会导致页面显示发生变化。鼠标悬停的越来越多的创造性应用一直在出现，从为页面上链接的含义提供视觉提示，到在 DHTML 中添加完整的飞出式层叠菜单系统(见[我的弹出菜单脚本](http://www.webmasterbase.com/examples/dhtmlpopup.html)是一个很好的例子)。

随着 Netscape Navigator 2.0 的发布，鼠标悬停首次在技术上成为可能，Netscape Navigator 2.0 包括一种称为 JavaScript 的新脚本语言。这个想法是让网络第一次超越 HTML 的基本功能。有了 JavaScript，当用户点击一个超文本链接时，Web 开发人员能够做的不仅仅是跳转到一个新的位置——他们实际上可以修改当前加载页面的内容。当微软发布他们自己的网络浏览器 Microsoft Internet Explorer 3.0 时，他们包含了自己的主题变体— JScript。最近，微软和 Netscape 已经同意采用一种厂商中立的标准，这种标准是由欧洲计算机制造商协会以 ECMAscript 的名称开发的。

随着该领域标准的趋同，在 Web 上使用脚本变得更加可行。脚本驱动的 mouseover 的流行(相对于短暂流行的相对缓慢和笨拙的 Java 实现)证明了 JavaScript 及其同类的有用性。

##### 图像对象

严格地说，你不需要了解任何 JavaScript 来理解这篇文章，因为一切都是从头开始解释的。然而，如果你没有太多的编程经验，并且根本不知道任何 JavaScript，如果你真的开始觉得我在用希腊语写东西，你可能会想读一读 SitePoint 的 [JavaScript 101](http://www.webmasterbase.com/article/452) 。

在大多数方面，JavaScript 是一种面向对象的语言，这意味着在 JavaScript 的世界里，一切都是对象。浏览器窗口是一个对象(称为`window`)，网页是一个对象(称为`document`)，网页中的图像本身就是对象。对象的好处在于它们有可以改变的属性。

如果你熟悉 HTML 的基础知识(如果你不熟悉，请查看我们的 [HTML 教程](http://www.webmasterbase.com/article/453))，你会习惯使用`<IMG>` 标签在网页上创建图像对象的概念:

```
<IMG SRC="imagefile.gif" WIDTH="75" HEIGHT="40"> 
```

现在，在不知道的情况下，当您键入这个时，您会自动创建一个 JavaScript `image`对象，如果您愿意，它将允许您稍后更改它的属性。这正是我们想要做的来创建一个鼠标悬停。当鼠标移动到图像上时，我们想要改变图像的外观，而图像的外观是`image`对象的一个属性。

为了简化后面对这个特定的`image`对象的引用，我们可以给它指定一个`name`。这很容易做到，只需要我们将`NAME`属性添加到我们的`<IMG>` 标签中。

```
<IMG SRC="imagefile.gif" NAME="myimage" WIDTH="75" HEIGHT="40"> 
```

现在我们的`image`对象被命名为`myimage`，可以这么称呼。在这一点上，值得指出的是 JavaScript 在很大程度上是区分大小写的。换句话说，我们以“`myimage`命名的形象，不能简称为“`MYIMAGE`”或“`MyImage`”。

我说过 JavaScript 中的对象有我们可以改变的属性。我们可以使用下面的通用 JavaScript 语句来设置"`myimage`"对象的属性:

```
document.images["myimage"].property = newvalue; 
```

其中,`property`是您要更改的属性的名称，而`newvalue`是您要赋予它的新值。这一行可以读作:

<q>*"在当前`document`，在`image`中调用`myimage`，设置`property`为`newvalue`。"*</q>

到现在为止，我相信你一定很想知道如何将这个应用到简单的鼠标上。在鼠标悬停时，我们改变图像对象显示的`image`。正如在 HTML 中，我们使用`<IMG>`标签的 `SRC`属性来指示我们想要显示的图像文件的 URL，在 JavaScript 中，我们将`image`对象的`src`属性设置为我们想要它显示的图像文件的 URL。

使用我在上面展示的通用 JavaScript 语句，可以简单地写成:

```
document.images["myimage"].src = "newimage.gif"; 
```

##### 鼠标悬停如何工作

既然您已经知道了如何使用 JavaScript 更改图像的外观，我们需要弄清楚当用户将光标放在图像上时如何做到这一点。此外，我们需要知道当用户的鼠标不再停留在图像上时，如何将它切换回来。JavaScript 允许我们这样做的特性叫做事件处理程序。

事件处理程序可以被认为是“触发器”,当满足某个条件时，它会导致事情发生。当鼠标悬停时，我们感兴趣的两个事件处理程序是`onMouseOver`和`onMouseOut`。这些允许您定义当用户的鼠标悬停在给定的 HTML 元素上或离开给定的 HTML 元素时要运行的 JavaScript 片段。

虽然 [HTML 4.01 规范](https://www.w3.org/TR/PR-html40/)说我们应该能够为任何 HTML 元素定义 onMouseOver 和 onMouseOut 事件处理程序，但是 Netscape Navigator 只为 anchor reference ( `<A HREF>`)标签提供了这种功能，这就有点不足了。这意味着 Netscape 只能检测鼠标移过和移出超链接，如果我们要制作一个 Mouseover，对鼠标“敏感”的元素必须是链接。如果我们想制作一个没有链接的鼠标悬停，这不是太大的问题，因为我们可以很容易地在我们的图像周围制作一个“无处链接”,如下所示:

```
<A HREF="javascript:void(0)"><IMG SRC="imagefile.gif"   

NAME="myimage" WIDTH=75 HEIGHT=40 BORDER=0></A> 
```

这将创建一个单击时不做任何事情(无效)的链接。如果你想让它链接到某个东西，你只需要把你的 URL 放在`javascript:void(0)`的位置。添加我们的事件处理程序就像将它们作为属性插入到`<A HREF>`标签中一样简单:

```
<A HREF="javascript:void(0)" onMouseOver="overmyimage()"   

onMouseOut="outmyimage()"><IMG SRC="imagefile.gif" NAME="myimage"   

WIDTH=75 HEIGHT=40 BORDER=0></A> 
```

在上面的例子中，`overmyimage()`和`outmyimage()`是 JavaScript 函数，处理在“开”和“关”状态之间改变图像。函数是一段 JavaScript 代码，它被放在一边，以备以后“触发”。在大多数情况下，函数是在 HTML 文件的头中定义的(在`<HEAD>`和`</HEAD>`标签之间)。我们的`overmyimage()`函数如下所示:

```
<HEAD>  

<SCRIPT LANGUAGE="JavaScript">  

<!-- Hide from older browsers  

function overmyimage() {  

 ... code here ...  

}  

// End script hiding -->  

</SCRIPT>  

</HEAD> 
```

我们将在我指定的地方插入这个函数被调用(触发)时运行的代码。

现在，掌握了图像对象、事件处理程序和函数的基本知识，我们现在准备创建…

##### 我们的第一次鼠标悬停

如果您一直在密切关注并完全理解我到目前为止介绍的元素，那么它们组装成一个工作鼠标应该是显而易见的。如果你有一点困惑，不要担心——在这一部分，我会一步一步地带领你，让一切都变得清晰。

首先，让我们从创建我们的形象开始。它将是 70 像素宽和 30 像素高，“非活动”图像的文件名将是“`off.gif`”。我们像平常一样使用 HTML `<IMG>` 标签来做这件事。我们还将使用 `NAME`属性为它指定名称“`my1stMouseOver`”。

```
<IMG SRC="off.gif" WIDTH=70 HEIGHT=30 BORDER=0 NAME="my1stMouseOver"> 
```

现在我们将在图像周围添加一个“无处链接”,这样我们就可以挂接`onMouseOver`和`onMouseOut`事件处理程序，设置为触发 JavaScript 函数来“激活”和“停用”图像。恰当地说，我们将称这些函数为 `activate()`和`deactivate()`。

```
<A HREF="javascript:void(0)" onMouseOver="activate()"   

onMouseOut="deactivate()"><IMG SRC="off.gif" WIDTH=70   

HEIGHT=30 BORDER=0 NAME="my1stMouseOver"></A> 
```

现在剩下的就是编写 JavaScript 函数，`activate()`和`deactivate()`。`activate()` 功能会将`my1stMouseOver`的`src`属性更改为“活动”图像文件，我们称之为`on.gif`。

```
function activate() {  

 document.images["my1stMouseOver"].src = "on.gif";  

} 
```

类似地，`deactivate()`函数会将“`my1stMouseOver`”的`src`属性改回“非活动”图像文件“`off.gif`”。

```
function deactivate() {  

 document.images["my1stMouseOver"].src = "off.gif";  

} 
```

就是这样！我们现在已经创建了一个显示“`off.gif`”的图像，只是当用户将鼠标指针放在它上面时，它会变成显示“`on.gif`”。当用户再次移开鼠标时，它回到显示“`off.gif`”。

[See it in action!](http://www.webmasterbase.com/examples/mouseover/seeour1st.html) | [View the code](http://www.webmasterbase.com/examples/mouseover/codeour1st.html)

在接下来的几节中，我们将采用上一节中创建的基本鼠标悬停，并添加一些附加功能来提高其可扩展性和性能。总的来说，如果你打算在你设计的页面中认真使用鼠标悬停，这些都是很好的技巧。

##### 共享功能

在我们的基本鼠标悬停中，我们使用了一个`<IMG>`标签和两个 JavaScript 函数来获得我们想要的效果，但是在一个有 5 个鼠标悬停的页面上，这意味着必须编写 10 个不同的函数(更不用说为它们取 10 个不同的名字了！).如果有一种方法可以对页面上的所有鼠标悬停使用相同的两个函数会怎么样？

事实证明，这非常容易做到。首先，我们必须创建 JavaScript 变量，包含每个鼠标悬停的“开”和“关”图像的文件名。例如，如果我们已经创建了一个带有三个`<IMG>`标记的页面，我们希望将它们作为鼠标悬停对象，并且我们已经使用`<IMG>`标记的`NAME`属性将这些图像命名为“`first`”、`second`和`third`，我们将创建名称形式为 *`name`* `_on`和 *`name`* `_off`的变量，如下所示:

```
var first_off = "image1off.gif";   

var first_on = "image1on.gif";   

var second_off = "image2off.gif";   

var second_on = "image2on.gif";   

var third_off = "image3off.gif";   

var third_on = "image3on.gif"; 
```

我们要做的下一件事是设置`onMouseOver`和`onMouseOut`事件处理程序，将图像的名称传递给我们的函数。例如，我们的“第二次”鼠标悬停的代码如下所示:

```
<A HREF="second.html" onMouseOver="activate('second')"    

onMouseOut="deactivate('second')"><IMG SRC="image2off.gif"   

WIDTH=70 HEIGHT=30 BORDER=0 NAME="second"></A> 
```

注意，参数(传递给函数的文本)被单引号(`'`)括起来。这是为了避免它们干扰事件处理程序周围的双引号(`"`)。

通过将要更改的图像的名称传递给我们的`activate`和`deactivate`函数，我们给它们一种方法来知道当它们被触发时要更改哪个图像。这就是我们如何设法在尽可能多的鼠标悬停之间共享这两个功能。剩下的就是修改这些函数来接收这个图像名称，并使用它来修改正确的图像。

我们新函数的代码将如下所示:

```
function activate(imgName) {   

 document.images[imgName].src = eval( imgName + "_on" );   

}   

function deactivate(imgName) {   

 document.images[imgName].src = eval( imgName + "_off" );   

} 
```

通过将单词`imgName`放在函数名称后面的括号中，我们告诉它们接受传递给它们的一个值(在本例中，是要更改的图像的名称)，并将其放入一个名为`imgName`的变量中，直到我们处理完该函数。然后我们在两个地方使用这个变量。首先，我们用它来表示我们想要改变哪个图像对象的`src` 属性(`images[imgName]`)。其次，我们用它来引用包含正确文件名的变量。这是使用`eval(...)`内置函数完成的。

```
eval( imgName + "_on" ) 
```

所有这些都是获取 `imgName`变量的值，将它与文本段“`_on`”粘在一起，然后评估结果，就像它是一段普通的 JavaScript 代码一样。为了尽可能清楚地说明这一点，让我们来看看如果在名为“`third`”的图像上触发了`onMouseOver`事件处理程序会发生什么。

当 onMouseOver 事件处理程序被触发时，它运行 activate 函数，并向它传递图像的名称—“`third`”。该函数接受这个参数，并在变量`imgName`出现的地方放置`third`。

```
function activate("third") {   

 document.images["third"].src = eval( "third" + "_on" );   

} 
```

`eval(...)` 内置函数将它拥有的两个字符串粘在一起(如'`+`'操作符所示),然后对其求值，就好像它是一段普通的 JavaScript 代码一样:

```
document.images["third"].src = third_on; 
```

现在`third_on` 是包含“第三个”图像的“on”图像文件名的变量的名称，因此它的值被替换为:

```
document.images["third"].src = "image3on.gif"; 
```

就这样——我们只剩下一个与基本鼠标悬停相同形式的语句，它将把`"third"`图像的`src`属性更改为“`image3on.gif`”！关键是命名包含文件名的变量，这样`eval(...)`函数只需在图像名称的末尾加上“`_on`或“`_off`”就可以得到变量名。

[See it in action!](http://www.webmasterbase.com/examples/mouseover/seeshare.html) | [View the code](http://www.webmasterbase.com/examples/mouseover/codeshare.html)

##### 预加载图像

如今，大多数网络浏览器都相当智能。当他们加载某个东西时，他们会将它放入系统的缓存中，并在那里保留一段时间，以便在需要时可以快速方便地访问。不过，大多数浏览器都不够智能，无法提前加载内容。在大多数情况下，这不是一个大问题，但当鼠标悬停时，这可能是一个难看的弱点。

当用户将光标放在你的鼠标上时，网络浏览器会意识到它需要显示“打开”的图像。如果这是这个用户第一次访问你的站点，图像可能不在缓存中，必须从你的站点加载。这会导致延迟，通常比用户将鼠标从鼠标上移开所花的时间更长，因为用户从未见过您计划显示的漂亮的亮灯按钮。更糟糕的是，如果“打开”的图像只是部分加载，用户可能会在你计划的光滑图像的地方看到可怕的、模糊的混乱。

我们如何克服这个问题？解决方案是将鼠标悬停所需的图像预加载到用户的缓存中，这样我们就可以在尝试显示它们之前确保它们是可用的。为此，我们必须创造性地使用 JavaScript image 对象。

到目前为止，我们只创建了带有标准 HTML `<IMG>`标签的图像对象。然而，我们可以只用 JavaScript 创建它们。这种纯 JavaScript 的图像对象不会显示在网页上，但是它们可以加载和缓存，并且它们具有与用 HTML 创建的图像对象相同的属性。那么，策略就是为鼠标悬停时使用的每个图像文件创建纯 JavaScript 的图像对象。为了在上一节中使用的三个鼠标悬停示例中做到这一点，我们可以用以下图像声明替换我们的变量声明:

```
var first_off = new Image();    

first_off.src = "off1.gif";    

var first_on = new Image();    

first_on.src = "on1.gif";    

var second_off = new Image();    

second_off.src = "off2.gif";    

var second_on = new Image();    

second_on.src = "on2.gif";    

var third_off = new Image();    

third_off.src = "off3.gif";    

var third_on = new Image();    

third_on.src = "on3.gif"; 
```

对于每个图像文件，我们创建一个新的图像对象，然后将其`src` 属性设置为适当的文件名。在设置该属性时，浏览器决定需要下载图像文件，此时图像文件存储在缓存中，以便在实际需要显示时快速检索。我们遵守与前一节中文件名变量相同的图像对象命名约定。

现在剩下的就是调整我们的`activate`和`deactivate`函数(是的*和*)，让它们使用纯 JavaScript 的图像对象的`src` 属性值，而不是变量值。

```
function activate(imgName) {    

 document.images[imgName].src = eval( imgName + "_on.src" );    

}    

function deactivate(imgName) {    

 document.images[imgName].src = eval( imgName + "_off.src" );    

} 
```

最后，我们可以再做一次调整，以确保用户永远不会看到半加载的图像。尽管这种预加载技术确实提前加载了鼠标悬停图像，但用户仍然有可能在图像完全加载之前触发鼠标悬停。为了防止这种情况，我们在`activate`和`deactivate`函数中放了一个小“阀门”:

```
function activate(imgName) {    

 if ( eval(imgName + "_on.complete") ) {    

 document.images[imgName].src = eval( imgName + "_on.src" );    

 }    

}    

function deactivate(imgName) {    

 if ( eval(imgName + "_off.complete") ) {    

 document.images[imgName].src = eval( imgName + "_off.src" );    

 }    

} 
```

这利用了 JavaScript 图像对象的另一个属性。我们已经知道`src` 属性包含它所代表的图像文件的 URL。这里我们使用了`complete`属性，如果图像已经完成加载，则该属性为`true`，如果没有，则为`false`。这些新修改版本的`activate`和`deactivate`仅在负责预加载新图像的纯 JavaScript 图像对象是`complete`时才改变显示的图像。

[See it in action!](http://www.webmasterbase.com/examples/mouseover/seepreload.html) | [View the code](http://www.webmasterbase.com/examples/mouseover/codepreload.html)

##### 先进的技术

最后，这一节将向您展示一个简单的方法来确保鼠标悬停页面与旧浏览器兼容，以及一个巧妙的技巧来防止预加载鼠标悬停图像降低页面其余部分的速度。

***浏览器兼容对象检测***

既然你已经知道了如何让鼠标悬停工作得很好，那就值得花点时间来学习如何让鼠标悬停工作得不好。虽然到目前为止我所概述的鼠标悬停类型可以在所有最新的支持 JavaScript 的浏览器(Netscape 3+以及 MSIE 4+)上一致地工作，但是有一些浏览器支持 JavaScript，但是不完全支持`image`对象。

一个这样的浏览器是微软的 Internet Explorer 3.0。如果我们用我到目前为止在 MSIE 3.0 上使用过的代码加载包含鼠标指针的页面，我们将看到许多难看的 JavaScript 错误。由于我们不能在这个浏览器上做 Mouseovers，我们至少可以做的是摆脱错误消息。

虽然有几种方法可以检测用户的 JavaScript 浏览器版本并做出相应的反应，但在面对不太主流的 Web 浏览器时，这种技术有时会被证明是不可预测的。此外，每当 Netscape 或微软发布其浏览器软件的新版本时，任何使用这种浏览器检测脚本的页面都需要更新。

更可靠和有效的是一种鲜为人知的技术，称为对象检测。这个想法是，我们检测浏览器支持的特性，而不是检测浏览器的版本。因为任何支持图像对象的浏览器都能够进行鼠标悬停，所以在我们做任何需要支持图像对象的事情之前，我们只需要检测浏览器是否知道图像对象是什么。这是通过一个简单的 if 语句完成的:

```
if (document.images) {     

 ... code here ...     

} 
```

可以这样读:

<q>*`If`当前`document`包含`images`然后做以下…*</q>

如果我们在`activate`和`deactivate`函数中添加一个`if (document.images)`，并在图像声明周围放置一个，我们将会看到鼠标悬停在不支持`image`对象的浏览器上时悄然消失。

[See it in action!](http://www.webmasterbase.com/examples/mouseover/seeobjdet.html) | [View the code](http://www.webmasterbase.com/examples/mouseover/codeobjdet.html)

***延迟图像加载***

预加载图像固然很好，但是如果在给定的页面上有 20 个鼠标经过呢？你*真的*想让浏览器在开始加载页面的实际内容之前忙于下载 20 张图片吗？通常不会。解决这个问题的一个好方法是使用`<BODY>`标签的`onLoad`事件处理程序来触发一个函数，该函数只在页面的 HTML 代码被加载后*才加载鼠标经过的图像。这样，页面上实际显示的所有图像在下载顺序上都放在鼠标经过的图像之前。*

onLoad 的使用相当简单:

```
<BODY onLoad="loadImages()"> 
```

剩下的就是将鼠标悬停图像的预加载放在`loadImages()` 函数中。

```
if (document.images) {     

 var first_off = new Image();     

 var first_on = new Image();     

 var second_off = new Image();     

 var second_on = new Image();     

 var third_off = new Image();     

 var third_on = new Image();     

}     

function loadImages() {     

 if (document.images) {     

 first_off.src = "off1.gif";     

 first_on.src = "on1.gif";     

 second_off.src = "off2.gif";     

 second_on.src = "on2.gif";     

 third_off.src = "off3.gif";     

 third_on.src = "on3.gif";     

 }     

} 
```

请务必注意，我已经将纯 JavaScript 图像对象的创建放在了`loadImages()` 函数之外。这样做有两个原因。首先，对象的创建不会加载任何文件:只有`src`属性的设置会这样做，所以将对象声明留在函数之外不会减慢页面其余部分的加载。第二，在函数*中创建的对象或变量只存在于该函数中。*如果我们将图像对象的创建转移到函数内部，一旦函数运行完毕，它们就会消失，然后`activate`和`deactivate`函数将不再能够使用它们。

就是这样！现在，我们的鼠标悬停图像只有在触发了`<BODY>`标签的 onLoad 事件处理程序时才会被加载，并且只有当整个页面的 HTML 代码都已加载，并且该页面上的任何图像都已开始加载时，才会发生这种情况。这是否可取取决于您的判断，但是在页面上有很多鼠标经过并且它们是否立即可用并不那么重要的情况下，这可能是一个好主意。

[See it in action!](http://www.webmasterbase.com/examples/mouseover/seedelay.html) | [View the code](http://www.webmasterbase.com/examples/mouseover/codedelay.html)

##### 概括起来

在本文中，我试图展示一幅完整的图片，展示让网页元素响应用户鼠标移动所涉及的技术。我不仅讲述了基础知识，希望任何具有 Web 设计和一般编程基础知识的人都可以理解，而且我还讲述了一些更高级的主题，这些主题将对更有经验的 Web 开发人员有用。

有关本文的更多信息，请访问:

*   [CNET 建筑商。COM 的上标:“强大的鼠标悬停机器”](http://builder.cnet.com/webbuilding/pages/Programming/Kahn/012898/index.html)
*   [Web Review 的网络编码器:“MegaCoder 翻转脚本生成器”](http://www.webreview.com/1998/03_13/webauthors/03_13_98_1.shtml)
*   [网景的 JavaScript 指南](http://developer.netscape.com/docs/manuals/communicator/jsguide4/index.htm)
*   [微软脚本技术](http://msdn.microsoft.com/library/default.asp?url=/nhp/Default.asp?contentid=28001169)

## 分享这篇文章