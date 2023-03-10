# Flash 脚本–Flash MX 中的动态屏蔽

> 原文：<https://www.sitepoint.com/dynamic-masking-flash-mx/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/mask.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/mask.swf"></object>**

Flash Mx 具有动态屏蔽功能！现在，您可以使用动作脚本来遮罩对象。在这个小教程中，我将向你展示如何使用`setMask` 方法来遮罩一个对象。

[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/mask.zip)。

##### 方向

**1。**创建新电影。

**2。**将两幅图像导入 Flash。我用了一只公鸡和一只老虎的图像。

**3。**将图像转换为电影剪辑。

**4。**给出两个 movieclips 实例的名称。

**5。**现在，创建遮罩对象——在本例中，我使用了一个小的圆形对象。

**6。**将圆形对象转换成影片剪辑，并给它一个实例名“circle”。

**7。**创建三个按钮:一个蒙版老虎图像，另一个蒙版公鸡图像，第三个去除蒙版。

**8。**给按钮实例命名。在我为按钮插入 actionscript 之前，给它们命名实例:我给我的命名为“left”、“right”和“remove”。

**9。**输入以下代码。选择当前层的第一帧，按 f9 打开动作窗口，并输入以下动作:

```
left.onPress = function() 

{ 

  rooster.setMask(null); 

  tiger.setMask(circle); 

} 

right.onPress = function() 

{ 

  tiger.setMask(null); 

  rooster.setMask(circle); 

} 

remove.onPress = function() 

{ 

  rooster.setMask(null); 

  tiger.setMask(null); 

}
```

10。就是这样！测试电影。

## 分享这篇文章