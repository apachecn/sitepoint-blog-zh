# Flash 脚本–创建 Flash 画板

> 原文：<https://www.sitepoint.com/create-flash-sketchpad/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/linedrawer.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/linedrawer.swf"></object>**

Flash MX 现在能够通过使用 movie 对象的新绘制方法动态绘制直线和曲线。我将教你如何使用内置的 Flash 绘图 API 创建一个简单的画线应用程序。

[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/linedrawer.zip)。

##### 使用的功能

我在这个应用程序中使用的主要函数是:

```
myMovieClip.createEmptyMovieClip (instanceName, depth)
```

此方法创建一个空的影片剪辑作为现有影片剪辑的子级。该方法的行为类似于`attachMovie`方法，但是您不需要为新的电影剪辑提供外部链接名称。新创建的空影片剪辑的注册点位于左上角。如果缺少任何参数，此方法将失败。

```
myMovieClip.lineStyle (thickness,rgb,alpha)
```

这个方法指定了 Flash 在后续调用`lineTo`和`curveTo`方法时使用的线条样式，直到你用不同的参数调用`lineStyle`为止。您可以在绘制路径的过程中调用`lineStyle`方法，为路径中的不同线段指定不同的样式。

```
myMovieClip.moveTo (x, y)
```

该方法将当前绘图位置移动到`(x, y)`。如果缺少任何参数，此方法将失败，并且当前绘图位置不会更改。

```
myMovieClip.lineTo (x, y)
```

该方法使用当前线条样式从当前绘制位置到`(x, y)`绘制线条；然后当前绘图位置被设置为`(x, y)`。如果您正在绘制的电影剪辑包含使用 Flash 绘图工具创建的内容，那么对`lineTo`的调用将绘制在内容的下方。如果在调用 moveTo 方法之前调用`lineTo`方法，当前绘图位置默认为`(0, 0)`。如果缺少任何参数，此方法将失败，并且当前绘图位置不会更改。

##### 该过程

**1。创建一个新电影，并选择第一帧。**

打开 actionscript 窗口并输入:

```
createEmptyMovieClip("Line",1);
```

上面的代码创建了一个空的 movieclip，其实例名为 Line，深度为 1

**2。输入下面的代码来设置线条样式:**

```
Line.lineStyle(1,0x000000,100);
```

上面的代码动态设置线条样式。线条的粗细为 1 磅，十六进制颜色为黑色，alpha 值为 100。

**3。输入画线的代码:**

```
onMouseDown = function (){ 

  Line.moveTo(_xmouse, _ymouse); 

  onMouseMove = function (){ 

  Line.lineTo(_xmouse, _ymouse);} 

} 

onMouseUp=function(){ 

  onMouseMove=null; 

}
```

上面的代码会在你点击并拖动鼠标的时候画出这条线。

让我们看看每一行是如何工作的:

```
onMouseDown = function (){
```

点击鼠标按钮时，功能`onMouseDown`被激活。

```
Line.moveTo(_xmouse, _ymouse);
```

这将直线移动到起始点，该点将直接位于您单击的位置。`_xmouse`和`_ymouse`是鼠标的 x 和 y 位置。

```
onMouseMove = function (){
```

功能 `onMouseMove`仅在移动鼠标时激活。

```
Line.lineTo(_xmouse, _ymouse);}
```

这将创建一条直线，直到鼠标被拖动的位置。

```
onMouseUp=function(){
```

释放鼠标按钮时，功能 `onMouseUp`被激活。

```
onMouseMove=null;
```

该功能关闭`onMouseMove()`功能，以便在用户再次点击鼠标按钮之前不会再次绘制线条。

这是完整的代码:

```
createEmptyMovieClip("Line",1); 

Line.lineStyle(1,0x000000,100); 

onMouseDown = function () { 

  Line.moveTo(_xmouse, _ymouse); 

} 

onMouseMove = function ()  {  

  Line.lineTo(_xmouse, _ymouse);} 

} 

onMouseUp=function() { 

  onMouseMove=null; 

} 

//The above code should be entered in the first frame of the movie
```

测试电影。单击并拖动以查看正在绘制的线。如果你按照正确的步骤去做，你会得到和我一样的结果。

## 分享这篇文章