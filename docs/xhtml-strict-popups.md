# 在 XHTML 1.0 Strict 页面中创建动态弹出窗口

> 原文：<https://www.sitepoint.com/xhtml-strict-popups/>

我知道你在想什么:“弹出式广告？”但是，不要看某些网站和程序给不知情的大众带来这些垃圾的负面方式，让我们想想它们能做的好事。

现在，有几个教程和脚本可以让您在单击链接时创建弹出窗口；其他人在页面加载时打开弹出窗口。

但是，您是否找到了允许您创建动态弹出窗口(不同大小、形状和配置的窗口)的工具呢？没有吗？我也没有。

弹出窗口很容易创建:只需将`javascript:window.open()`粘贴到任何锚点标签的 HREF 部分。问题是这种方法会让你的 XHTML 网页无效。

##### 弹出验证

```
<a href="somewhere.html" onclick="javascript:popupWin()">Some Link</a>
```

XHTML 1.0 文档类型不允许在锚标记的 HREF 属性中放置 JavaScript 函数，但允许向 onclick 和 onmouseover 等事件处理程序添加函数。添加这些事件处理程序的理论是好的；问题是它们在页面的 XHTML 代码中看起来相当难看。

解决方案是把一切都放在 JavaScript 本身中——无论如何，事件处理程序本质上是 JavaScript！

##### 一个 REL 来统治他们

让我们先来看看是什么让我们的弹出窗口变得生动起来——锚的标记:

```
<p><a href="http://www.somewhere.com/" rel="popup|600|400|0|1">SitePoint</a></p>
```

注意到什么异常了吗？没错——锚标签的 REL 属性看起来与众不同。

该值:

*   告诉弹出 JavaScript 链接是否应该在弹出窗口中打开
*   指示弹出窗口的宽度和高度
*   提供打开或关闭新窗口属性的值

##### 啪，啪，弹出！

现在，让我们看一个打开弹出窗口的 JavaScript 函数的常见示例:

```
function popupWin(link,attribs) { 

     var popupWin = null; 

     popupWin = window.open(link,'winPopup',attribs);  

}
```

这个 JavaScript 函数看起来很像其他无数的弹出窗口脚本——但是外表可能具有欺骗性！

在上述函数中，弹出窗口的目的地和属性不是在`window.open()`函数中指定的，而是通过链接和`popupWin()`函数变量中指定的`attribs`变量传递到`window.open()`函数中。

##### JavaScript 流行了

现在，我们需要一个函数来抓取所有的锚标签，从这些标签中获取并使用几个值，如果他们找到了他们想要的，就把这些锚标签变成弹出链接。代码如下:

```
function popupWindows() { 

     if(!document.getElementsByTagName) { 

          return; 

     } 

     var scrW = screen.availWidth; 

     var scrH = screen.availHeight; 

     var anchors = document.getElementsByTagName("a"); 

     for (var i = 0; i < anchors.length; i++) { 

          var anchor = anchors[i]; 

          var linkDest = anchor.getAttribute("href"); 

          var relIndex = anchor.getAttribute("rel"); 

          var relSplit = relIndex.split("|"); 

          var windowAttributes = ""; 

          if(relSplit[0] == "popup") { 

               if (relSplit[1] > scrW) { 

                  pW = scrW - 10; 

               } 

               else { 

                  pW = relSplit[1]; 

               } 

               if (relSplit[2] > scrH) { 

                  pH = scrH - 40; 

               } 

               else { 

                  pH = relSplit[2]; 

               } 

               scrX = (scrW - pW - 10) * .5; 

               scrY = (scrH - pH - 30) * .5; 

               var windowAttributes = "width=" + pW + ",height=" + pH + ",left=" + scrX + ",top=" + scrY + ",screenX=" + scrX + ",screenY=" + scrY; 

               windowAttributes += ",location=" + relSplit[4] + ",resizable=" + relSplit[4] + ",scrollbars=" + relSplit[4]; 

               anchor.setAttribute("href", "javascript:popupWin('" + linkDest + "','" + windowAttributes + "')"); 

          } 

     } 

} 

window.onload = popupWindows;
```

##### 弹出细分

JavaScript 的第一部分检查打开文档的浏览器是否支持`getElementsByTagName`方法。如果没有，脚本将退出，链接将在同一个浏览器窗口中打开。

`if(!document.getElementsByTagName) {
         return;
    }`

接下来，我们分配变量:

*   `scrW`将保持屏幕的可用宽度
*   `scrH`将保持屏幕的可用高度
*   锚将保存页面上所有锚标记的数组

`var scrW = screen.availWidth;
    var scrH = screen.availHeight;
    var anchors = document.getElementsByTagName("a");`

在这些变量被赋值后，我们开始遍历锚标签。当我们这样做时，我们获得了当前锚标签的两个属性值，`HREF`和`REL`。然后我们拆分`REL`属性，获取竖条之间的值，并将它们放入`relSplit`数组。

`for (var i = 0; i < anchors.length; i++) {
         var anchor = anchors[i];
         var linkDest = anchor.getAttribute("href");
         var relIndex = anchor.getAttribute("rel");
         var relSplit = relIndex.split("|");`

下一个代码块主要检查`relSplit`数组的第一个值是否为“popup”。如果是，代码将根据上面的`scrW`和`scrH`变量检查`relSplit`数组的下两个值。如果任何一个数组值大于与之比较的变量，数组值将缩小以适应窗口。

接下来的变量`scrX`和`scrY`计算弹出窗口的左上角位置，以使其在屏幕上居中。

`if(relSplit[0] == "popup") {
              if (relSplit[1] > scrW) {
                 pW = scrW - 10;
              }
              else {
                 pW = relSplit[1];
              }
              if (relSplit[2] > scrH) {
                 pH = scrH - 40;
              }
              else {
                 pH = relSplit[2];
              }
              scrX = (scrW - pW - 10) * .5;
              scrY = (scrH - pH - 30) * .5;`

在代码的下一部分中，`windowAttributes`变量被赋予了几个通常在弹出窗口 JavaScript 中可以找到的值:width、height、left 和 top。控制弹出窗口在屏幕上的位置的另外两个变量也在这里定义:`screenX`和`screenY`。

`var windowAttributes = "width=" + pW + ",height=" + pH + ",left=" + scrX + ",top=" + scrY + ",screenX=" + scrX + ",screenY=" + scrY;`

您可以设置通常与 JavaScript 弹出窗口相关联的其他属性:只需将它们添加到`windowAttributes`变量中，并使用`relSplit`数组中的其他值作为属性值。请记住，对于添加到`windowAttributes`变量的每个属性，您必须向每个链接的`REL`值添加一个由竖线分隔的`'0'`或`'1'`。

对于这个特定的脚本，我不希望显示地址栏。我还希望能够调整窗口的大小。为了实现这些目标，我将以下内容添加到`windowAttributes`变量中:

`windowAttributes += ",location=" + relSplit[3] + ",resizable=" + relSplit[4];`

在设置了`windowAttributes`变量之后，我们需要将锚标签的`HREF`值设置为一个普通的 JavaScript 函数。在我们的示例中，这是通过以下代码实现的:

`anchor.setAttribute("href", "javascript:popupWin('" + linkDest + "','" + windowAttributes + "')");`

##### 突然行动起来

现在，为了让这个解决方案工作，我们必须在页面加载时加载 JavaScript 函数。向`BODY`标签添加一个 onload 事件处理程序可能就足够了，但是它不支持我们干净标记的目标。相反，在 JavaScript 的末尾，我们将添加`window.onload = popupWindows`。这使得 XHTML 代码看起来更加简洁，因为所有内容都保存在 JavaScript 中。

为了使您的 XHTML 代码更加清晰，您可以将这些 JavaScript 函数移动到一个外部文件中，并将其链接到使用这些弹出链接的每个文档。

现在，实现了这个解决方案，您可能会发现您已经拥有的任何使用`window.onload`方法的 JavaScript 都不再有效。不要绝望——只需创建一个名为 initialize 的新函数，并将页面加载时需要加载的两个函数的名称放入其中。使用`window.onload`方法调用这个函数。

例如，假设我有另一个名为`hideDivs`的脚本，它隐藏某些 div，直到用户点击一个链接。在这种情况下，JavaScript 函数如下所示:

```
function initialize() { 

     popupWindows(); 

     hideDivs(); 

} 

window.onload = initialize;
```

将这个脚本添加到您的页面并让它在页面加载时执行的另一种方法是使用 Simon Willison 的[闭包技术](https://www.sitepoint.com/blog/)。

##### 浏览器警告

这个脚本的一个问题是，与 Web 标准一样，每个浏览器都有自己实现这个脚本的方式。

例如，用于 Windows 和 Mac 的 Internet Explorer 5.x 和 Opera 跳过没有指定`REL`属性的锚标记，并继续下一个锚标记。另一方面，Mozilla 和 Netscape 浏览器似乎希望在任何没有指定`REL`属性的锚标记处“中断”脚本。他们停止执行脚本，并且根本不实现它。

记住，如果使用这个脚本，必须为页面中的每个锚标记指定一个`REL`属性。

##### 常见应用

像这样的脚本有多种用途。

一个示例应用程序可能涉及在线照片图库，单击图片缩略图会在弹出窗口中打开完整大小的图片。此外，如果图片大小不同，可以为每张照片指定弹出窗口的大小。

##### 进一步阅读

要了解有关弹出窗口的更多信息以及您可以使用它们做的所有好事，请查看以下链接:

*   创建没有死链接的弹出窗口
*   调整弹出窗口的大小以适应图片的大小
*   [JavaScript 弹出窗口入门](http://tech.irt.org/articles/js128/)

## 分享这篇文章