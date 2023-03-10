# 创建没有死链接的弹出窗口

> 原文：<https://www.sitepoint.com/pop-ups-dead-links/>

它们是网络上许多热烈讨论的话题，也是那些想用“当面”广告骚扰我们的坏人最喜欢的玩具——弹出窗口。

但是，我们不要以操作工具的小人来评价工具。事实上，在某些时候和条件下，弹出窗口可能是一种令人满意的效果；不过，这些情况通常出现在 Web 应用程序中，而不是网站中。

弹出窗口的一个主要缺点是对 JavaScript 的依赖。Javascript 是一个很好的工具，可以增强标记的交互性，而无需重新加载。然而，我们应该意识到它的问题。

创建一个弹出窗口非常容易，有许多过时的教程和代码生成器会诱使您认为创建一个优秀的弹出窗口只需要一个带有`javascript:window.open()`和适当参数的链接。

##### 忘记你所学的

让我们不要浪费时间讨论为什么这是错误的——许多其他文章，如 [this evolt tute](http://www.evolt.org/links_javascript/) 和[this A List Apart](http://www.alistapart.com/articles/pop-uplinks/)文章，已经这样做了。相反，让我们想想手头的问题，并找到另一种方式来实现一个良好的结果。

因为一些好的原因(例如，没有它客户会不高兴，你不能刷新主页，等等。):

*   我们想要一个我们可以控制的弹出窗口(根据它的大小，位置，关闭浏览器 chrome 的一部分，等等)。).
*   我们希望弹出窗口对不使用 JavaScript 的人有用。弹出调用应该只是打开一个新的浏览器窗口(或选项卡)。
*   不考虑我们复制浏览器任务的事实，我们希望在弹出窗口中有一个“关闭窗口”链接。

这些是我们作为交互设计师都必须满足的共同要求。如果我们分析它们，我们可以得出结论:

*   弹出和关闭链接的控制需要 JavaScript。
*   在新窗口中打开链接可以通过 target 属性在标记中实现(XHTML strict 文档除外，但有[一个变通方法](http://www.zeldman.com/daily/0503a.shtml#strictlyspeaking))。

现在我们需要确保，在我们的开发中，合适的工具只做它需要做的事情。让我们从 HTML 标记开始。

```
<p>This will be our demo page that  

<a href="popkid.html" target="_blank"> 

shows a pop-up window(where possible)</a> 

and simply <a href="popkid.html">links to the  

page without a pop-up.</a></p>
```

这就是我们所需要的。我们不需要任何像 onclick 或 onkeypress 这样的内联事件处理程序——这些是 JavaScript，应该添加到 JavaScript 代码中。

##### 使用更干净的 JavaScript

让我们从定义窗口属性和“关闭窗口”链接的文本内容开始。由于这个链接只能用于 JavaScript，我们稍后将通过 DOM 添加它。

```
var closeElementId='closewindow'; 

var closeLinkText='Close window'; 

var windowAttributes='width=310,height=400,left=0,top=0,scrollbars=no,location=no';
```

##### 更改产生弹出窗口的页面

我们需要一个 JavaScript 函数将页面上的 HTML 链接转换成弹出链接。我们可以通过遍历所有链接并检查哪些链接具有目标属性来轻松实现这一点。然而，这应该只发生在不包含在弹出窗口中的文档上；因此，我们检查是否有开启文件。

```
if(!window.opener) 

{ 

  var as,i,popfun 

  as=document.getElementsByTagName('a'); 

  for (i=0;i<as.length;i++) 

  { 

    if(as[i].target=='_blank') 

    { 

      popfun=function(){ 

        window.open(this.href,'',windowAttributes); 

        return false; 

      }; 

      as[i].onclick=popfun; 

      as[i].onkeypress=popfun; 

    } 

  }
```

遍历文档的所有`<a>`元素，我们检查它们是否有一个设置为`'_blank'`的目标属性。如果有，我们在链接被点击时，或者在链接被聚焦时按下一个键时应用 window.open 函数(正如我们所知，可访问性也意味着独立于鼠标)。返回 false 确保链接不会在打开器文档中打开，它只在弹出窗口中打开。通过读取链接的 href 属性，我们知道在弹出窗口中打开哪个文档。

如果我们不想循环所有链接(这可能需要一些时间)，我们可以只循环页面的特殊部分。比方说，我们用`<div id="content">`定义了一个内容部分。在这种情况下，我们需要做的就是找到以下内容:

```
as=document.getElementsByTagName('a');
```

我们将替换为:

```
as=document.getElementById('content').getElementsByTagName('a');
```

##### 向弹出窗口中的文档添加附加内容

这解决了开篇的问题。现在，让我们来看看弹出窗口，它们确实有一个窗口。打开器:

```
} else { 

  var closep,closelink,closetext; 

  closelink=document.createElement('a'); 

  closetext=document.createTextNode(closeLinkText); 

  closelink.href='#'; 

  closelink.appendChild(closetext); 

  closelink.onclick=function(){self.close();}; 

  closelink.onkeypress=function(){self.close();}; 

  if(document.getElementById(closeElementId)) 

  { 

    document.getElementById(closeElementId).appendChild(closelink);   

  } else { 

    closep=document.createElement('p'); 

    closep.id=closeElementId; 

    closep.appendChild(closelink); 

    document.body.insertBefore(closep,document.body.firstChild);   

  } 

}
```

首先，我们创建一个新的 link 元素，并应用前面定义的文本作为文本内容。然后，为了让它看起来像一个链接，我们将链接的 href 设置为`"#"`。为了让链接关闭窗口，我们对它应用了函数`self.close()`。当用户单击链接时，或者当链接具有焦点时按下某个键时，该选项被激活。

现在，我们变得贪婪起来:我们希望 HTML 开发人员能够定义链接应该出现在哪里。

如果他们不想定义位置，我们只需在它自己的段落中应用链接作为文档的第一个元素。但是，为了确保我们仍然可以对它应用 CSS，我们给了它一个 ID。

这个 ID 就是我们之前定义的`closeElementId`。现在，如果 HTML 已经包含一个具有这个 ID 的元素，我们只需使用`appendChild()`将我们的链接作为一个新的子元素添加到这个元素中。如果我们还没有一个具有该 ID 的元素，我们创建一个新的段落元素，并将 ID 应用于它。我们将链接添加到这个新段落，并使用`insertBefore`将该段落作为新的第一个子元素添加到文档的主体。

##### 行动起来！

这就是我们需要做的。剩下唯一要做的就是将上述所有内容封装在一个函数中，在页面加载时调用它，并将所有内容添加到一个`.js` include 中，该 include 将被添加到每个相关文档中。

onload 调用可以通过多种方式实现。一种方法包括将事件附加到窗口；另一种方法是简单地添加一个`window.onload=functionname;`作为`.js` include 的最后一行。

Scott Andrew 为[编写了一个方便的函数](http://www.scottandrew.com/weblog/articles/cbs-events)，将一个加载事件附加到窗口上。这样做的好处是，它添加到其他 onload 事件中，而不是覆盖它们。它的一个问题是，它不能在 Mac 电脑上的 Internet Explorer 中运行。

点击这里下载完整的脚本和 HTML 演示页面[。](https://www.sitepoint.com/examples/popuplink/popstuff.zip)

##### 其他应用

我们还可以用这个解决方案做什么？一个很好的特性是让用户选择打开带有样式的弹出窗口，或者不带有样式的弹出窗口。我们可以通过将包含复选框的表单应用到打开页面来实现这一点，并且仅当复选框被选中时才应用窗口属性。

这个设置应该存储在一个 cookie 或者后端，因为如果用户不得不在每个页面上重新定义它，那么这个设置是没有用的。

由于我们通常从弹出窗口中移除浏览器工具栏，并且我们不能假设每个人都知道打印页面的键盘快捷键，我们也可以添加一个“打印此页面”链接来调用`window.print()`函数。

另一个想法可能是通过向用户应用图标或更改其显示来标记这些特定链接在弹出窗口中打开。但是，无论我们做什么，都要确保用 JavaScript 来做，并且不伤害 HTML。

## 分享这篇文章