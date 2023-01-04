# Flash 脚本–Flash MX 中的动态 JPEG 图像加载器

> 原文：<https://www.sitepoint.com/jpeg-image-loader-flash-mx/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/loadjpg.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/loadjpg.swf"></object>**

在本教程中，我们将看到动态加载 jpg 图像到 Flash 是多么容易。

请注意，Flash 只能加载非渐进的 jpg 图像。

为了加载我们的图像，我们将使用 l `oadMovie`方法。可以从任何影片剪辑对象调用此方法，包括根对象。

它有两个参数:`URL`和可选的`variables` 参数。`URL` 参数为任何 JPEG 图像提供有效的网址，而可选的`variables`参数使用`HTTP`方法(`GET`或`POST`)将变量发送到 URL。

[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/loadjpg.zip)。

**1。**开始一部新电影——我的是 350×350 像素的。

**2。**按 ctrl+f7 打开组件面板。

**3。**在舞台上拖动 3 个`PushButton`组件实例，并选择第 1 帧中的第一个`PushButton` 组件。

**4。**组件的参数显示在属性检查器中。

**5。**在属性检查器实例名称文本框中键入“hen”。

**6。**键入“call_Img”作为点击处理程序的名称。

**7。**输入“显示母鸡”作为标签名称。同样，对于其他按钮，请键入:

————————————————-

ButtonName, LabelName, ClickHandlerName

————————————————-

(一)。母鸡，秀母鸡，call_Img

(二)。猎豹，秀猎豹，call_Img

(三)。母鸡，秀母鸡，call_Img

————————————————-

**8。**选择帧 1。按 F9 键(Windows)打开“动作”框。

**9。**插入动作:

```
filejpg = function(picName) 

{ 

//set the position of picHolder movie clip to center of stage 

picHolder._x = 80 

picHolder._y = 10 

//Load the pictures 

picHolder.loadMovie(picName); 

} 

function call_Img(name) 

{ 

//Give correct path here 

if(name == hen){filejpg("hen.jpg");} 

if(name == cheetah){filejpg("cheetah.jpg");} 

if(name == tiger){filejpg("tiger.jpg");} 

} 

//create a empty movie clip for holding the Jpg pics 

createEmptyMovieClip("picHolder", 1);
```

这就够了！在测试电影之前，请确保这些图片与电影在同一个文件夹中。

## 分享这篇文章