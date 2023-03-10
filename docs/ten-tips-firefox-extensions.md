# 在火狐扩展学校他们永远不会告诉你的 10 件事

> 原文：<https://www.sitepoint.com/ten-tips-firefox-extensions/>

**这是我最近的电子书[Build Your Own Firefox Extension](https://www.sitepoint.com/books/byofirefoxpdf1/)的后续文章——这是我从为 Firefox 构建扩展的经验中收集的有用技巧、诀窍和窍门的额外精选，包括 [CodeBurner](https://www.sitepoint.com/browser-devtools-secrets-start-up-network-and-performance/) ，SitePoint 参考扩展。假设你对如何构建 Firefox 扩展有基本的了解，所以如果你还没有这样做的话，你可能想先拿一本免费的电子书。**

这里的大部分内容都没有明确地记录在 Mozilla 开发者中心(MDC)的文档中，要么是因为它仍然在他们的“待办事项”列表中，要么是因为我自己编的。其中一些有很好的文档记录，但它非常有用，我认为无论如何都值得将您的注意力引向它。

这些提示是按照复杂程度排列的，从最短和最简单的开始，然后是更长更复杂的想法。

*请注意*:在整篇文章的许多地方，我将创建一些小方法来打包自包含的技术，在所有情况下，我都将它们创建为顶级函数(使用 function 关键字)。但实际上，您应该将它们创建为主扩展对象的方法。

##### 1.用`list-style-image`添加图标

许多 XUL 元素不支持 CSS 背景图像属性，但其中许多*支持`list-style-image`。这包括`<menuitem>`、`<button>`和`<textbox>`。您可以使用这些来将应用程序的图标添加到它的主菜单项中，或者将一个小放大镜图标附加到用于搜索的文本框中:*

```
textbox[type="search"] 
{ 
  list-style-image:url(chrome://myextension/conteimg/magglass.png); 
}
```

##### 2.在 Mac OS X 中使`<tab>`元素可通过键盘访问

```
<tab> elements are natively inaccessible to the keyboard in Firefox for Mac OS X. To make them accessible you need to manually insert them into the tab order, by adding a tabindex attribute with the value 0. This value is effectively "auto", and places the element at its source-order position in the overall tab order:
```

```
<tab label="About" tabindex="0"/>
```

完成后，你可以使用箭头键在标签之间切换，就像在 Windows 和 Linux 中一样。

![](img/70d2917d5128d2b259c7b3f1c55bffa5.png)*Mac OS X 的一个键盘焦点标签*

##### 3.引用上下文菜单事件的原始鼠标目标

当您点击 XUL 上下文菜单中的一个项目时，事件`target`引用指向您点击的`<menuitem>`。但是如果您想要引用最初的*目标元素呢；也就是说，您右键单击的元素首先会生成菜单？这非常简单，因为 Firefox 提供了一个包含这个引用的属性。它叫做`popupNode`，是`document`的财产。使用它最简单的方法是通过菜单项的`command`事件传递它:*

```
<popup id="contentAreaContextMenu"> 
  <menuitem label="This bloke won't haggle"  
            oncommand="offerMeFourteen(document.popupNode)" 
  /> 
</popup>
```

##### 4.防止元素继承`flex`

如果您将 [`flex`](https://developer.mozilla.org/en/XUL/Attribute/flex) 属性添加到大多数 XUL 元素中，它们将会扩展以填充可用空间。但是`flex`是继承的，所以它的子节点*也会*扩展，这在某些情况下是非常不可取的。例如，如果子元素是一个`<image>`，你会希望它有精确的尺寸；但是没有办法明确否定继承的`flex`。

但是它只继承了一层深度，所以您*可以通过添加一个中间包装元素*来否定它，而无需声明`flex`属性:

```
<hbox flex="1"> 

  <hbox> 
    <image  
       src="chrome://myextension/conteimg/logo.png"  
       width="135"  
       height="130" 
     /> 
  </hbox> 

</hbox>
```

##### 5.从 Chrome load 事件产生一个对话框

如果你使用`window.openDialog`从 chrome `load`事件中产生一个带有`modal`和`centerscreen`特性的对话框，这个对话框在 Mac OS X 中几乎是不可见的，隐藏在屏幕的左上角。这是因为对话框是在窗口大小确定之前定位的，所以`centerscreen`属性不能像预期的那样工作。同样的问题也出现在`alert`函数上，如果你把它作为一个快速而肮脏的调试工具来使用，这可能是一个问题。

一种解决方案是将`openDialog`或`alert`函数包装在一个快速的`setTimeout`中。这可以确保在对话框启动之前调整好主窗口的大小，因此它将被正确定位:

```
setTimeout(function(){ alert(foo.bar); },1);
```

##### 6.为 Windows 和 Linux 添加自定义对话框图标

要在对话框中添加自定义图标，首先在扩展的 chrome 目录中创建一个名为`icons`的文件夹。然后，在`icons`文件夹中，创建另一个名为`default`的文件夹。在默认文件夹中，保存一个与`<dialog>`元素 ID 同名的图标。

因此，举例来说，如果对话框的 ID 为`myextension-preferences`，你可以创建一个名为`myextension-preferences.ico`的图标(对于 Windows，或者是 Linux 的`.png`)。MDC 文档说在 Linux 上使用 XPM 映像，但是它们缺乏对 alpha 通道透明性的支持。PNG 文件确实提供了支持，而且它们也工作得很好。

在 Windows 中，图标也会显示在任务栏中:

![](img/9e8fbbe6d207153a8901c3f5ed78e1f9.png)*Windows XP 中的一个自定义对话框图标*

这与 Mac OS X 不同，因为它的对话框显示时没有图标。

##### 7.获取最近打开的窗口的引用

你可以使用 Firefox 的[窗口中介接口](https://developer.mozilla.org/en/nsIWindowMediator)来获取最近打开的浏览器窗口的引用。如果你想从一个外部对话框打开一个网络链接，这可能是有用的，并且比`window.opener`更可靠。

这里有一个简短的小方法，它返回窗口引用，或者如果没有打开浏览器窗口，返回`null`:

```
function getRecentWindow()  
{ 
  var wm = Components.classes["@mozilla.org/appshell/window-mediator;1"].getService(Components.interfaces.nsIWindowMediator); 
  var win = wm.getMostRecentWindow("navigator:browser"); 

  return win; 
}
```

##### 8.获取每个打开标签的 URL

在前一篇技巧文章的基础上，我们可以遍历所有当前打开的浏览器窗口，提取它们的 URL，并将它们打包成一个分层数组(首先按窗口分组，然后按选项卡分组)。

下面的方法就是这样做的。最终矩阵的每个成员本身都是一个数组，包含标签的 URL 和一个布尔标志(`selected`)，用于指示它是否是该窗口中当前选中的标签:

```
function getTabsHeirarchy()  
{  
  var heirarchy = [],   
      wm = Components.classes["@mozilla.org/appshell/window-mediator;1"].getService(Components.interfaces.nsIWindowMediator),  
      benumerator = wm.getEnumerator('navigator:browser');  

  while(benumerator.hasMoreElements())   
  {  
    var browserwin = benumerator.getNext(),  
        tabbrowser = browserwin.getBrowser(),   
        tabs = [];  

    for(var i=0; i<tabbrowser.browsers.length; i++)  
    {  
      var browser = tabbrowser.getBrowserAtIndex(i);  
      tabs[i] = {  
        'uri' : browser.currentURI.spec,   
        'selected' : (tabbrowser.selectedTab == tabbrowser.mTabs[i])  
      };  
    }  

    heirarchy.push(tabs);  
  }  

  return heirarchy;  
}
```

##### 9.让你的界面响应窗口焦点的变化

当窗口失去焦点时，大多数 Mac 窗口会改变它们的外观:例如，更亮的背景、窗口装饰或变灰的按钮。要在你自己的界面控件上实现这种效果，你需要知道窗口何时获得和失去焦点。

您的第一反应可能是使用窗口`focus`和`blur`事件，但事实证明它们并不可靠，因为它们有时表现得不直观。例如，如果应用程序焦点移动到嵌入式`<browser>`中的一个文档，主窗口`blur`事件将被触发，即使该窗口仍然是焦点所在的窗口。这是因为`<window>`元素本身不再具有应用程序焦点。虽然这种行为是合乎逻辑的，但也可能是意料之外的。好消息是 Firefox 的主界面窗口有一个`active`属性，当窗口真正失去焦点时，它会从`true`变为`""`(一个空字符串)。您可以用一个 DOM [突变事件](https://www.w3.org/TR/DOM-Level-2-Events/events.html#Events-eventgroupings-mutationevents)监听器观察这个属性，并使用它作为您需要做的任何事情的触发器:

```
window.addEventListener('DOMAttrModified', function(e)  
{  
  if(e.attrName == 'active')  
  {  
    if(e.newValue == 'true')  
    {  
      //window has gained the focus  
    }  
    else  
    {  
      //window has lost the focus  
    }  
  }  
}, false);
```

小心你如何使用这个。例如，如果你用它来触发一个像`alert`这样的模态对话框，产生对话框的动作会导致窗口失去焦点；消除它将重新获得焦点，这将重新触发对话框！

或者，如果您想要实现的效果可以用纯 CSS 实现，您可以使用一个[属性选择器](https://reference.sitepoint.com/css/attributeselector)和[否定伪类](https://reference.sitepoint.com/css/pseudoclass-not)。例如，要在图标的正常和禁用状态之间切换图标:

```
window[active="true"] #main-icon  
{  
  list-style-image:url(chrome://myextension/conteimg/main-icon.png);  
}  

window:not([active="true"]) #main-icon  
{  
  list-style-image:url(chrome://myextension/conteimg/main-icon-disabled.png);  
}
```

##### 10.实现特定于平台的样式表

Firefox 可以在多个平台上使用，每个平台在外观甚至界面组件的放置方面都有自己的惯例。一个很好的例子就是`alert`对话框中的 OK 和 Cancel 按钮:在 Windows 和 Linux 上 OK 按钮在 Cancel 按钮的左边，而在 Mac OS 上正好相反。关闭和最小化窗口按钮的外观是另一个例子，因为它们因平台而异。

因此，考虑到这些变化，能够将特定于平台的样式表应用到您自己的扩展接口通常是有用的。这使您能够实现各种变化，如按钮的替代图标、自定义界面控件的不同字体等等。

幸运的是，Firefox 提供了一个简单的机制来实现这一点，它使用了一个特殊的文件夹层次结构和一组清单文件。

首先，您需要创建文件夹层次结构。下图中的顶层`platform`文件夹应该在扩展的根文件夹中——与`chrome`文件夹在同一层。除了样式表本身的名称之外，所有的文件夹名称和文件名必须与这里显示的完全一样(它们也区分大小写)；这个可以是你喜欢的任何东西，当然每个平台的文案都必须一样。

![](img/459cf3207e08ac9accec2a36ed035793.png) *特定平台样式表的文件夹层次*

以防不太明显，“达尔文”是麦克·OS X，“WINNT”是 Windows，“Linux”是……呃……Linux。这些`chrome.manifest`文件中的每一个都应该包含这个相同的制表符分隔的行(用您的扩展名替换“myextension”):

```
skin  myextension  classic/1.0  chrome/skin/classic/
```

要将样式表添加到您的界面中，只需使用以下 URL 模式添加一个 xml-stylesheet 处理指令:

```
<?xml-stylesheet href="chrome://myextension/skin/browser.css"?>
```

看看你所需要做的就是参考`skin`目录，Firefox 会根据它运行的平台计算出包含哪个特定的样式表。您可以用任意多的不同样式表扩展这个原则:只需在每个平台文件夹中创建一个版本，然后使用相同的 URL 模式将其添加到 XUL 文档中。

##### 11.将 URL 添加到浏览器历史记录中

这里有一个额外的额外小费。MDC 上的 [XUL 引用](https://developer.mozilla.org/en/XUL_Reference)告诉你如何[创建一个带有历史自动完成](https://developer.mozilla.org/En/XUL/Textbox_%28Toolkit_autocomplete%29#Examples)的文本框。不幸的是，它没有告诉你如何在历史中添加新的 URL，所以我不得不通过搜索 Firefox 的源代码来解决这个问题。我将在这里向您展示的方法添加 URL，检索和保存 favicons，并包括自动完成的基本历史记录！

![](img/d234854ebe0d53e5d9ec1ac3f815e51d.png) *带有历史自动完成菜单的文本框，显示我们以编程方式添加的条目*

*注意*:添加到浏览器的历史记录将在 Firefox 3 或更高版本中工作，但检索 favicon 将仅在 3.5 或更高版本中工作。

因此，首先我们需要一个具有必要属性的`<textbox>`。在下面的代码示例中，函数名`addURLToHistory`可以是您想要的任何名称，并且`flex`属性是可选的，但是其他所有内容必须完全如下所示:

```
<textbox flex="1"  
         newlines="stripsurroundingwhitespace"  
         type="autocomplete"   
         autocompletesearch="history"   
         completeselectedindex="true"  
         onkeydown="if(event.keyCode == KeyEvent.DOM_VK_RETURN) { addURLToHistory(this); }"  
       />
```

`type`和`autocompletesearch`属性触发了主要的自动完成行为。`completeselectedindex`属性是这样的，当你从自动完成菜单中选择一个项目时，它的值被自动写入文本框；这允许您直接按下回车键来启动命令功能。`newlines`属性很简单，这样我们可以避免手动解析不需要的空白(比如前导或尾随空格)的值。

注意命令功能是如何由`onkeydown`而不是`oncommand`触发的。这是因为`<textbox>`元素缺少一个`oncommand`事件。通常用于它的事件有`oninput`(当输入可显示的文本时触发)和`onchange`(当值改变时触发)。因为该值会频繁地改变以响应自动完成建议，其中大部分是不需要的值，所以我们将命令操作推迟到按下 Enter 键之后。

我们这里已经有足够的功能自动完成历史箱。您可以在文本框中键入或粘贴文本，然后会出现一个下拉菜单，显示您的历史记录，并根据输入的内容进行过滤。然后，您可以从菜单中进行选择，您选择将被写入文本框。

您还可以添加一个箭头按钮，通过添加`enablehistory="true"`来显示下拉菜单。

现在让我们来看看当你按下回车键时触发的命令函数。实际上，在这之后你会继续执行另一个任务，(比如将指定的 URL 加载到一个`<browser>`中)，但是我只关注如何将它添加到历史中。我先给你看代码，然后一点一点看:

```
function addURLToHistory(textbox)    
{  
  var url = textbox.value;  

  if(!/^(((ht|f)tp[s]?):)/i.test(url))  
  {  
    url = 'http://' + url;  
  }  
  textbox.value = url;  

  if(url.indexOf(' ') != -1   
     || url.split('?')[0].indexOf('..') != -1)  
  {  
    alert('Malformed URL');  
    return;  
  }  

  var ioService = Components.classes["@mozilla.org/network/io-service;1"].getService(Components.interfaces.nsIIOService);    
  var nsIURI = ioService.newURI(url, null, null);    

  var historyService2 = Components.classes["@mozilla.org/browser/nav-history-service;1"].getService(Components.interfaces.nsIGlobalHistory2);  
  historyService2.addURI(nsIURI, false, true, null);  

  try  
  {  
    var faviconService = Components.classes["@mozilla.org/browser/favicon-service;1"].getService(Components.interfaces.nsIFaviconService);  
    var faviconURI = ioService.newURI('http://' + nsIURI.host + '/favicon.ico', null, null);  
    faviconService.setAndLoadFaviconForPage(nsIURI, faviconURI, false);  
  }  
  catch(err) {}  
}
```

首先，我们做一些验证，如果 URL 没有协议，就添加一个协议(这样用户只需输入“www。”)，然后将[修改后的] URL 写回文本框。然后，如果它包含 CGI 参数之外的任何空格或多个点，我们会抛出一个错误语法的警告并退出函数。这是我们阻止 Firefox 窒息所需要的所有验证。您可能更喜欢优雅地处理错误，例如通过将错误抛出到控制台，或者实现一个自定义方法来警告用户发生了错误。

接下来，我们做的是将 URL 添加到历史记录中。历史服务不接受普通的 URI 字符串，所以我们需要创建一个所谓的 IURI。这是一个包含各种元数据的 URI 对象，包括它的宿主，这在以后会派上用场。我们使用 [IO 服务](https://developer.mozilla.org/en/nsIIOService)创建 IURI 对象，然后将其传递给[全局历史服务](https://developer.mozilla.org/en/nsIGlobalHistory2)，将其添加到浏览器的历史中。

剩下的代码是用来抓取 favicon 的，之所以把它放在`try ... catch`块中有两个原因。首先，如果 favicon 因为任何原因不在预期的 URL 上，就不会抛出错误，其次，因为它只在 Firefox 3.5 或更高版本中工作。因此，我们首先初始化 [favicon 服务](https://developer.mozilla.org/en/nsIFaviconService)，然后为 favicon 的地址创建一个 IURI 对象(使用原始 IURI 中的主机名)。然后，我们将 favicon IURI 对象传递给 favicon 服务，以加载和保存 favicon。

我们做到了！下次我们在文本框中输入相同的地址时，它将会出现在自动完成菜单中，同时还有它的收藏夹图标。

请注意，favicon 进程是异步的。如果您想直接在文本框中显示它，您需要运行一个`setInterval`循环来不断检查它是否存在。您可以使用如下代码来实现:

```
var count = 0, faviconclock = window.setInterval(function()  
{  
  var fsURI = faviconService.getFaviconImageForPage(nsIURI);  
  if(++count == 20 || /moz-anno:favicon:/.test(fsURI.spec))  
  {  
    window.clearInterval(faviconclock);  
    textbox.setAttribute('style', 'list-style-image:url(' + fsURI.spec + ')');  
  }  
}, 500);
```

这段代码有点棘手:每隔 500 毫秒(`setInterval`的第二个参数)，我们向 favicon 服务请求页面的 favicon。它将返回一个用`moz-anno:favicon:`协议(如果 favicon 已经被下载)或者 chrome:协议(如果它返回默认图像)格式化的 URI。如果我们已经尝试了 20 次(总共 10 秒)，或者如果我们已经成功地为页面下载了一个 favicon 如 URI 的`moz-anno:favicon:`所示——那么我们将它设置为文本框的`list-style-image` url。

我们将快速提示列表到此为止。如果你还没有这样做，下载我的电子书[构建你自己的火狐扩展](https://www.sitepoint.com/books/byofirefoxpdf1/)，它与 [CodeBurner](https://www.sitepoint.com/browser-devtools-secrets-start-up-network-and-performance/) 扩展一起免费提供。

请密切关注另一篇关于构建 Firefox 扩展的文章，以及 CodeBurner 家族的一些新成员，即将发布！

## 分享这篇文章