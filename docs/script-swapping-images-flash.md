# Flash 脚本–在 Flash 中交换图像

> 原文：<https://www.sitepoint.com/script-swapping-images-flash/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/swap.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/swap.swf"></object>**

本教程向您展示如何使用`swapDepths`功能。

想象一个场景，你有三张图片，一张在另一张的上面，你想移动上面图片下面的图片。在 Flash 中有很多方法可以做到这一点，在这里，我们将使用`swapDepths`特性。

[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/swap.zip)。

**1。在上面的例子中，我使用了三个矩形的电影胶片。我给每个实例取了名字:“红”、“蓝”和“绿”。**

**2。**这些是我应用于每个按钮的动作:

蓝色按钮:

```
on (press){_root.blue.swapDepths(1);}
```

红色按钮:

```
on (press){_root.red.swapDepths(1);}
```

绿色按钮:

```
on (press){_root.green.swapDepths(1);}
```

用法:

```
myMovieClip.swapDepths(depth) 

myMovieClip.swapDepths(target)
```

参数:

```
target
```

这是指深度被`myMovieClip`中指定的实例交换的电影剪辑的实例。两个实例必须具有相同的父电影剪辑。

```
depth
```

这是指定放置`MovieClip`的深度级别的数字。

这将指定实例(`MovieClip`)的堆叠或 z 顺序(深度级别)交换为:

*   由目标参数指定的电影，或者
*   当前占据深度参数中指定的深度级别的影片。

两部电影必须有相同的父电影剪辑。交换影片剪辑的深度级别具有将一部影片移到另一部影片前面或后面的效果。如果调用此方法时电影正在补间，则补间会停止。

## 分享这篇文章