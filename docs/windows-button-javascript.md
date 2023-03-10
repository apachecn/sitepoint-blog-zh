# 使用 JavaScript 模拟一个类似 Windows 的按钮

> 原文：<https://www.sitepoint.com/windows-button-javascript/>

**你在网站上见过这些花哨的自定义图形按钮吗？你有没有想过他们是怎么做的？事实是，实现它们并不难！今天，我们将使用 HTML `<img>`标签和 JavaScript 来实现这个效果。**

我们的按钮有三种不同的状态，每种状态有不同的图像:

第一种状态是“打开”或“正常”，其中图像的文件名将是“ButtonSubmit.gif”

第二种状态是“结束”,当用户的鼠标光标在按钮上时就会出现。图像的文件名将是“ButtonSubmit-over.gif”

第三种状态是“按下”,在单击按钮时出现。图像的文件名将为“ButtonSubmit-down.gif”。

让我们看一下代码:

```
<script> 

function ReplaceImage(sImgName,sImgFile){ 

  document.images[sImgName].src = sImgFile; 

} 

</script> 

<a href="#" onMouseOver="ReplaceImage('ImgSubmit', 

'ButtonSubmit_over.gif')"  

onMouseDown="ReplaceImage('ImgSubmit','ButtonSubmit_down.gif')"  

onMouseOut="ReplaceImage('ImgSubmit','ButtonSubmit.gif')">  

<img src="ButtonSubmit.gif" name="ImgSubmit" border="0"></a>
```

函数`ReplaceImage()`有两个参数:`sImgName`和`sImgFile`。

```
sImgName is the name of the image object in the document.images collection. sImgFile is the actual image file path relative to the current page location. The function simply replaces the old image displayed by the image object with the one specified by sImgName. 让我们将我们的图像对象命名为“`ImgSubmit`”。因为 Netscape 只会检测鼠标在链接上的移动和离开，所以我们需要将`<img>`标签放在一个链接标签中。当然，我们的链接不会指向任何地方:它只是检测光标的移动。

现在，我们可以使用 `<a>`标签的 3 个重要事件处理程序:`onMouseOver`、`onMouseOut`和`onMouseDown`。我们页面中显示的第一个图像将是“ButtonSubmit.gif”。当我们将光标移到这张图片上时，我们希望它被替换为“ButtonSubmit-over.gif”。为此，我们简单地使用链接的`onMouseOver` 事件处理程序:

```
<a href="#" onMouseOver="ReplaceImage('ImgSubmit', 

'ButtonSubmit-over.gif')">
```

现在我们已经对脚本进行了修改，当用户将光标移到按钮上时，“ButtonSubmit.gif”图像将被替换为“ButtonSubmit_over.gif”。
但是如果访问者将光标从图像上移开会发生什么呢？答案是什么都不会改变——图像保持不变(“ButtonSubmit-over.gif”)。为什么？因为我们还没有使用`onMouseOut` 处理程序。
我们需要检测`MouseOut` 事件并再次调用`ReplaceImage()`函数，以便将按钮恢复到初始状态(即再次显示“ButtonSubmit.gif”)。在我们引入了 `onMouseOut()` 事件处理程序之后，我们的图形按钮的代码将如下所示:

```
<a href="#" onMouseOver="ReplaceImage('ImgSubmit', 

'ButtonSubmit-over.gif')"  

onMouseOut="ReplaceImage('ImgSubmit','ButtonSubmit.gif')">  

<img src="ButtonSubmit.gif" name="ImgSubmit" border="0"></a>
```

现在我们的按钮几乎是完美的...我来解释一下为什么说“差不多”！目前，如果用户点击按钮，图像不会改变。为了允许它改变，我们需要对我们的代码进行最后一次修改。
这一次我们需要检测`MouseDown`事件，用“ButtonSubmit-down.gif”作为第二个参数调用`ReplaceImage()`。这将简单地用“ButtonSubmit-down.gif”替换已经显示的“ButtonSubmit-over.gif”。最后，我们得到了完美的类似 Windows 的按钮:

```
<a href="#" onMouseOver="ReplaceImage('ImgSubmit', 

'ButtonSubmit-over.gif')"  

onMouseDown="ReplaceImage('ImgSubmit','ButtonSubmit-down.gif')"  

onMouseOut="ReplaceImage('ImgSubmit','ButtonSubmit.gif')">  

<img src="ButtonSubmit.gif" name="ImgSubmit" border="0"></a>
```

总之，要用图像和 JavaScript 制作一个图形按钮，我们必须:

```

*   将图像放在`<a>`标签中。
*   使用`onMouseOver`、`onMouseOut`和`onMouseDown`事件处理程序来检测鼠标光标向下、向上和离开链接的移动。
*   让 3 个事件处理器中的每一个调用`ReplaceImage()`函数，将适当的图像文件作为第二个参数。

你可以在这里看到一个用 JavaScript 实现的图形按钮的例子。

## 分享这篇文章