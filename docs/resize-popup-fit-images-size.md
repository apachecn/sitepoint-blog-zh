# 调整弹出窗口的大小以适合图像的大小

> 原文：<https://www.sitepoint.com/resize-popup-fit-images-size/>

如果你是一个需要处理图片库的网站管理员，那么记下这个巧妙的脚本。它为您提供了一个非常常见的问题的解决方案——如何调整弹出窗口的大小以适应其中显示的图像大小。该脚本可以在 NS 4/5/6/7 和 IE 4/5/6 中运行。

首先，您需要一个主 HTML 页面，其中包含指向图库中全尺寸图片的链接:

```
<HTML> 

  <HEAD> 

    <TITLE>The Image Gallery</TITLE> 

    <script language="Javascript"> 

    function PopupPic(sPicURL) { 

      window.open( "popup.htm?"+sPicURL, "",  

      "resizable=1,HEIGHT=200,WIDTH=200"); 

    } 

    </script> 

  </HEAD> 

<BODY bgcolor="#FFFFFF"> 

    <a href="javascript:PopupPic('Image1.gif')">Image 1</a><br> 

    <a href="javascript:PopupPic('Image2.gif')">Image 2</a><br> 

    <a href="javascript:PopupPic('Image3.gif')">Image 3</a><br> 

</BODY> 

</HTML>
```

让我们稍微研究一下上面的代码。我们有一个简单的 HTML 页面，包含几个链接，并定义了一个简单的 Javascript 函数:`PopupPic()`。通过点击这个页面上的任何链接，您将调用`PopupPic()`函数。这个函数非常简单:它唯一做的事情就是创建一个弹出浏览器窗口，并在其中加载 popup 页面。

诀窍在于，当创建弹出窗口时，我们在查询字符串中传递图像的 URL(相对于图像库网页位置):

```
window.open( "popup.htm?"+sPicURL, "",  

"resizable=1,HEIGHT=200,WIDTH=200");
```

现在，看一下 popup.htm 页面的代码:

```
<HTML> 

<HEAD> 

  <TITLE>Fit the Pic Script</TITLE> 

  <script language='javascript'> 

    var arrTemp=self.location.href.split("?"); 

    var picUrl = (arrTemp.length>0)?arrTemp[1]:""; 

    var NS = (navigator.appName=="Netscape")?true:false; 

      function FitPic() { 

        iWidth = (NS)?window.innerWidth:document.body.clientWidth; 

        iHeight = (NS)?window.innerHeight:document.body.clientHeight; 

        iWidth = document.images[0].width - iWidth; 

        iHeight = document.images[0].height - iHeight; 

        window.resizeBy(iWidth, iHeight); 

        self.focus(); 

      }; 

  </script> 

</HEAD> 

<BODY bgcolor="#000000" onload='FitPic();' topmargin="0"  

marginheight="0" leftmargin="0" marginwidth="0"> 

  <script language='javascript'> 

  document.write( "<img src='" + picUrl + "' border=0>" ); 

  </script> 

</BODY> 

</HTML>
```

首先应该引起我们注意的是，我们使用的是 Javascript 代码，它在页面加载时直接执行:

```
var arrTemp=self.location.href.split("?"); 

var picUrl = (arrTemp[1].length>0)?arrTemp[1]:""; 

var NS = (navigator.appName=="Netscape")?true:false;
```

首先，我们获取页面 URL 字符串，并用“`?`”字符将其拆分。这种分割的结果保存在`arrTemp`数组变量中。

在第二行中，我们检查临时数组的第二个元素— `arrTemp[1]` —的长度是否大于 0，如果是这样，我们将第二个数组元素的值赋给变量`picURL`。

在第三行中，如果浏览器是 Netscape，我们将为 NS 变量赋值 true，否则我们将为 false。您可能已经猜到了，`PicURL`变量包含将在 popup.htm 页面中显示的图片的相对 URL。

有了`PicURL`变量后，我们可以使用`document.write`轻松地将图像写入文档主体:

```
document.write( "<img src='" + picUrl + "' border=0>" );
```

在页面完全加载到浏览器中之后，触发了`Load`事件，因为我们使用了 onLoad 事件处理程序，所以调用了函数`FitPic()` 。该函数的前两行查找浏览器的窗口宽度和高度(取决于浏览器)。然而，接下来的 3 行是这个函数中最重要的几行。让我们好好看看它们:

```
iWidth = document.images[0].width - iWidth; 

iHeight = document.images[0].height - iHeight; 

window.resizeBy(iWidth, iHeight);
```

页面完全加载后，我们可以访问文档的图像集合，从而访问图像属性。因为我们的页面上只有一个图像，并且图像集合是从零开始的，所以可以使用`document.images[0]`访问图像——这样，我们就可以获得图像的宽度和高度。

然后，我们从实际的图像宽度中减去初始的浏览器宽度——结果是浏览器宽度必须调整的数值。我们对初始浏览器高度和实际图像高度做了同样的处理，以确定我们应该调整浏览器高度以适合图片的像素数。

第三行是由 JavaScript 内置的`resizeBy()`函数完成的实际大小调整。如果你不熟悉`resizeBy()`函数，这里有一个简短的解释。

根据定义，这个函数将当前浏览器窗口的大小调整一定的量。它需要两个参数:`window.resizeBy(X, Y):`

*   **X**–窗口水平增长的像素数
*   **Y**–窗口垂直增长的像素数

下面一行将窗口的宽度缩小 10px，高度增加 13px:

```
window.resizeBy(-10, +13);
```

函数的最后一行将焦点放在弹出窗口上。

因此，为了总结脚本是如何工作的，我们将图像相对 URL 传递给 popup.htm(popup . htm？Images/Image1.gif)，然后我们用 document.write 将 Image 标签写入页面的正文，当页面被加载时，我们调用`FitPic()`，它将浏览器窗口的大小调整为图片大小。

要查看我们的脚本，请点击这里。

## 分享这篇文章