# Flash 脚本–瀑布效果

> 原文：<https://www.sitepoint.com/script-waterfall-effect/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/waterfall.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/waterfall.swf"></object>**

[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/waterfall.zip)。

**1。**将瀑布的图像导入 Flash。

**2。**将这张图片设置为你的背景，命名，锁定图层。

**3。**新建一层，命名为“瀑布”。

**4。**接下来，用渐变填充创建一个小圆。将其转换为图形对象，并将其命名为“drop”。

**5。**将您的“drop”图形对象转换为电影剪辑，并将其命名为“dropmov”。

**6。**在" dropmov "时间线上创建三个关键帧。将以下动作插入第一个关键帧:

```
pos = _y;
```

**7。**在第二个关键帧中，插入这个动作:

```
_y = _y+2; 

if (_y >300) 

{ 

_y = pos; 

}
```

**8。**然后，在第三个关键帧中插入:

```
gotoAndPlay (2);
```

**9。**现在，回到主时间线，将“drop mov”movie clip 的 alpha 降低到 30%。复制“dropmov”电影剪辑，使其覆盖整个瀑布。

运行电影，你应该会看到上面的效果。干得好！

## 分享这篇文章