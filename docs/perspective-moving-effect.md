# Flash 脚本–3D 透视移动效果

> 原文：<https://www.sitepoint.com/perspective-moving-effect/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/3deffect.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/3deffect.swf"></object>**

许多闪客问我如何创建 3d 透视效果，所以在这里！[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/3deffect.zip)。

**1。**使用线条工具创建网格:

![1100_line1](img/45315f9b7a09deed8cb3969e14848b9e.png)

将其转换为图形对象。

**3。**复制图形，并如下所示进行镜像:

![1100_line2](img/ee44ef646b0dc605d1ecbb0d71c265dd.png)

**4。**如下图创建一条线。

![1100_line3](img/c99203d7a4b4bf06c4afcf4ab3d2f8d6.png)

将此转换为电影剪辑。

**5。**给这个影片剪辑一个实例名 line。

**6。**转到 line movieclip 的时间轴，创建如下所示的三个关键帧。

![1100_tool4](img/2fdf583b345e836efab1b2527c8a4348.png)

**7。**在第一个关键帧中，插入动作:

```
y = /:initpos 

incr = 1;
```

**8。**在第二个关键帧中，插入:

```
setProperty ("", _y, y); 

y = y+incr; 

incr = incr + .2;
```

**9。**在第三个关键帧中，插入动作:

```
gotoAndPlay (2);
```

10。回到主时间线，在现有图层之上新建两个图层，命名如下图。

![1100_tool5](img/86c8bcfe8e4c3fa1c6b85d51f4202562.png)

确保 line movieclip 位于 line 图层中。

**11。**选择所有图层的第 8 帧，按 f5。这将把帧扩展到第 8 帧。

**12。**转到动作层，在第一帧，插入动作:

```
initpos = getProperty("/line",_y); 

y = initpos; 

incr = 1; 

18.In the second frame give action: 

duplicateMovieClip ( "/line","line" add incr, incr); 

incr = incr + 1; 

if((getProperty("/line" add incr,_y)) > 400) 

{ 

removeMovieClip ("/line" add incr); 

} 
```

13。在最后一帧，插入动作:

```
gotoAndPlay (2);
```

## 分享这篇文章