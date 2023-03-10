# ObjectSwap:绕过 IE 中的 ActiveX 激活问题

> 原文：<https://www.sitepoint.com/activex-activation-issue-ie/>

在 EOLAS 的专利法诉讼之后，微软最近决定改变在 Internet Explorer 中处理 ActiveX 对象的方式，这给开发人员社区带来了严重的问题。

Internet Explorer 中的所有 ActiveX 控件——包括 Flash 和 shock wave——都需要通过单击鼠标(或点击 Tab 和 Enter 键)来激活，然后用户才能与控件进行交互。这势必会损害任何嵌入 Flash 的网站的用户体验，这就需要 Flash 和 HTML 开发人员来收拾残局。

##### 可用的解决方案

您可以通过使用外部链接的脚本(如 JavaScript)来嵌入 ActiveX 内容，从而绕过激活要求。Flash 目前有解决方案，比如 [FlashObject](http://blog.deconcept.com/flashobject) 和 UFO。

这些对于使用 JavaScript 嵌入新的 Flash 内容非常有用。但是现有的需要重写的对象标签或者禁用了 JavaScript 的浏览器怎么办？这些情况需要替代解决方案。

##### 对象交换

本文中介绍的 ObjectSwap 解决方案考虑了所有这些问题。它捕获所有现有的对象标签，并用…本身替换它们。这将强制在 Internet Explorer 中自动激活，而不影响其他浏览器。类似的解决方案已经并行开发出来，但是本文将只关注 ObjectSwap。

尽管这个解决方案主要是为 Flash 开发的，但它也应该能与其他 ActiveX 控件一起工作，比如 Shockwave。该脚本影响页面中的所有对象标签，但是开发人员可以通过将其类名设置为“noswap”来选择排除特定对象。

***实现***

编写 ObjectSwap 的目的是使实现尽可能简单，对现有代码的破坏最小。您需要对 HTML 页面进行的唯一更改是将脚本链接到包含 ActiveX 对象的每个页面的`<head>`标记中，如下所示:

```
<script type="text/javascript" src="objectSwap.js"> </script>
```

一旦你这样做了，你就可以继续使用你最喜欢的技术来嵌入 ActiveX 内容。对于 Flash，这意味着要么使用 object/embed 标签的 Adobe/Macromedia 默认设置，要么使用仅使用 object 标签的符合标准的技术(更好的说法是 [Flash Satay](http://www.alistapart.com/articles/flashsatay) )。

***闪光检测***

到目前为止，一切顺利。但是既然我们已经在使用 JavaScript 了，为什么不利用这个机会在混合中加入一些 Flash 检测呢？我们可以通过向 Flash 对象添加新的参数定义来实现这一点，例如:

```
<param name="flashVersion" value="7" /> 
```

脚本查找这个`param`，如果它存在，将把对象转换成显示替代内容的`div`。这些内容不是由脚本生成的，而是必须已经驻留在 object 标记中，并显示可选文本、图像、指向 Flash 安装程序的链接等。如果存在 Flash，Internet Explorer 通常会忽略此内容。当您使用 Flash Satay 方法时，其他浏览器也是如此，因此您可以简单地将内容添加到对象主体的任何位置。

另一方面，如果使用 object/embed 方法，基于 gecko 的浏览器如 Firefox 和 Netscape 将在嵌入的电影旁边显示替代内容。解决方案是将内容包含在 HTML 注释中，当显示内容时，脚本会将其剥离。注释标签和内容之间应该有一个空格或换行符，以避免与对象标签内的任何 IE 条件注释冲突:

```
<!--   

<p>You need <a href= "http://www.adobe.com/shockwave/download/download.cgi?P1_Prod_Version=ShockwaveFlash">Flash</a> to view this content.</p> 

-->
```

当然，你也可以选择忽略 Flash 检测选项，或者使用 Adobe 的 Flash 8 快速安装。

***浏览器支持***

ActiveX 对象的激活问题只影响 Internet Explorer，因此大部分代码也只影响 IE。然而，Flash 检测代码也需要与其他浏览器一起工作。这意味着，如果需要，将为所有浏览器调用 objectSwap 函数来执行 Flash 检测服务，但只会在 IE 上执行对象交换，其他浏览器不受影响。
这就是你开始使用这个脚本所需要知道的一切。你可以[在这里](https://www.sitepoint.com/examples/objectswap/objectswap.zip)下载脚本和例子。

然而，如果您想更多地了解 ObjectSwap 的工作原理，下面几节将揭示该脚本的内部工作原理。

##### 它是如何工作的

首先，脚本遍历 HTML 源代码中的所有对象标记，并检索它们的 outerHTML 值:

```
var objects = document.getElementsByTagName('object'); 

for (var i=0; i<objects.length; i++) { 

  var o = objects[i]; 

  var h = o.outerHTML; 
```

由于 Internet Explorer 在其`outerHTML`(或`innerHTML`)中不包含对象的任何`param`标签，因此需要将它们分别提取到一个字符串中:

```
var params = ""; 

for (var j = 0; j<o.childNodes.length; j++) { 

  var p = o.childNodes[j]; 

  if (p.tagName == "PARAM"){ 

     .... 

    params += p.outerHTML; 

  } 

}
```

将生成的`"params"`字符串拼接成`outerHTML`代码；

```
var tag = h.split(">")[0] + ">"; 

var newObject = tag + params + o.innerHTML + " </OBJECT>";
```

最后，新生成的 HTML 会替换原来的 HTML:

```
o.outerHTML = newObject; 
```

***隐藏物体***

还有几件事要做。首先，我们希望防止对象被加载两次——一次是在 HTML 代码中初始化时，另一次是在它们被交换后。我们通过在页面加载之前向文档写入新的样式表来实现这一点。该样式使用 display: none 将对象从文档流中取出，延迟它们的加载，直到交换完成:

```
document.write ("<style id='hideObject'> object {display: none;} </style>");
```

交换后，样式被禁用，对象被允许加载:

```
document.getElementById("hideObject").disabled = true;
```

***探测闪光***

在遍历每个对象的参数表时，`objectSwap`函数检查是否存在`flashVersion`参数，如果找到，则执行闪光检测方法:

```
if (p.name == "flashVersion") { 

  hasFlash = detectFlash(p.value);
```

该方法在两种类型的浏览器中查找 Flash。首先，它检查插件是否出现在`navigator.plugins`数组中，这适用于基于 gecko 的浏览器:

```
detectFlash = function(version) { 

  if(navigator.plugins && navigator.plugins.length){ 

    var plugin = navigator.plugins["Shockwave Flash"]; 

    if (plugin == undefined){ 

      return false; 

    }
```

如果找到插件，代码仍然需要检查已安装的版本。这是通过检索插件的描述属性中的第三项并对照传递的版本参数进行检查来实现的:

```
var ver = navigator.plugins["Shockwave Flash"].description.split(" ")[2]; 

  return (Number(ver) >= Number(version))
```

接下来，脚本在 Internet Explorer 中检查插件。在 JavaScript 中，它通过尝试使用传递的版本创建一个新的 Flash ActiveX 对象来实现这一点。如果 JavaScript 无法创建对象，它将抛出异常，这就是为什么整个表达式必须包含在 try-catch 块中的原因:

```
} else if (ie && typeof (ActiveXObject) == "function") {    

  try { 

    var flash = new ActiveXObject("ShockwaveFlash.ShockwaveFlash." + version); 

    return true; 

  } 

  catch(e) { 

    return false; 

  } 

}
```

以防其他浏览器有不同的方式来处理 Flash，该方法在结束时返回 true，作为一个安全网。如果浏览器没有 navigator.plugins 数组，并且不是 Internet Explorer，它仍会尝试显示 Flash 电影。

回到`objectSwap`方法，如果脚本没有找到正确的版本，对象的 id 被检索(或者分配一个新的)并添加到一个队列中:

```
if (!hasFlash){ 

o.id = (o.id == "") ? ("stripFlash"+i) : o.id; 

stripQueue.push(o.id); 

Later on, the queue is passed to the stripFlash method: 

if (stripQueue.length) { 

  stripFlash(stripQueue) 

}
```

***剥离闪光***

这个方法遍历队列中的 id，并检索每个对象的`innerHTML`:

```
for (var i=0; i<stripQueue.length; i++){ 

  var o = document.getElementById(stripQueue[i]); 

  var newHTML = o.innerHTML;
```

对于 object/embed 方法，替代内容已经通过注释对 Firefox 和 Netscape 隐藏，需要正则表达式从`innerHTML`中去除注释，以便新内容可以在浏览器中显示:

```
newHTML = newHTML.replace(/<!--s/g, ""); 

newHTML = newHTML.replace(/s-->/g, "");
```

另一个正则表达式通过用`span`替换 embed 标签来中和它:

```
newHTML = newHTML.replace(/<embed/gi, "span");
```

为了将对象转换成 div，最简单的方法是改变对象的`outerHTML`。然而，这在 Firefox 中不起作用；相反，一个新的`div`元素被创建并被赋予与对象相同的`innerHTML`、`id`和`className`:

```
var d = document.createElement("div"); 

d.innerHTML = newHTML; 

d.className = o.className; 

d.id = o.id;
```

最后，对象被换成新的`div`:

```
o.parentNode.replaceChild(d, o);
```

***发起`ObjectSwap`***

```
ObjectSwap must be executed after all the objects have loaded, by binding the objectSwap function to the window.onload event. The catch is that other linked scripts in your page might have their functions bound to the same event; the last script to do so will override all the earlier bindings, causing the other scripts to fail. This is resolved by catching existing functions bound to the event, and calling them as well: 

```

```
var tempFunc = window.onload; 

window.onload = function(){ 

  if (typeof (tempFunc) == "function"){ 

    try{ 

      tempFunc(); 

    } catch(e){} 

  } 

  objectSwap(); 

}
```

当然，如果后面的脚本使用`window.onload`，这将会失败，所以您必须确保这个脚本在最后，或者后面的脚本使用类似的技术。

##### 结论

```
ObjectSwap offers a complete, one-step solution to the problem resulting from the decision by Microsoft as a result of the EOLAS law suit. A single JavaScript file linked from the <head> tag of your page is all you need to avoid Internet Explorer's activation requirement. What's more, you can take advantage of the situation and enhance the user experience by adding some simple Flash detection to your page.

```

## 分享这篇文章