# Flash 脚本–光线

> 原文：<https://www.sitepoint.com/flash-script-ray-light/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/rayoflight.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/rayoflight.swf"></object>**

[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/rayoflight.zip)。

**1。**打开一个宽度=400，高度=100 的新电影。

**2。**用您选择的名称创建一个文本对象。我用了“光线”。

**3。**将文本对象转换为电影剪辑，并赋予其实例名称“ray”。

![1071_tool1](img/ddb82454d2f8c957a31de93d97151619.png)

**4。**转到电影《雷》的时间线。

**5。**在现有层上创建一个新层。

![1071_tool2](img/a9f930c85314b96519812a77e621afc8.png)

**6。在新图层中，创建一个矩形，如下图所示。将其转换为图形对象。**

![1071_tool3](img/a9403a6105e96ec93ecee4ab48717cd9.png)

**7。**在第 20 帧创建关键帧。然后将矩形移动到第 20 帧处的文本末尾。

**8。**给图形一个补间动画。

**9。**蒙版图层。

10。回到主时间线。

**11。**创建一个新层，将文本 movieclip 的一个实例从库中拖到新层。

**12。**在新图层中，插入动作:

```
i = "1";  

alpha = "0.8";  

maxlight = "20";  

while (Number(i)<=Number(maxlight))  

{  

duplicateMovieClip ("ray0", "ray" add i, 800-i);  

setProperty ("/ray" add i, _xscale,  

getProperty("/ray" add (i-1), _xscale)+i*alpha);  

setProperty ("/ray" add i, _yscale,  

getProperty("/ray" add (i-1), _yscale)+i*alpha);  

setProperty ("/ray" add i, _alpha, 10-i*(0.5/50));  

i = Number(i)+1;  

}  

setProperty ("ray0", _visible, "0");  

stop (); 
```

13。运行电影。你会看到上面的效果！恭喜你！

## 分享这篇文章