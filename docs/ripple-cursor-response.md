# Flash 脚本–方形波纹光标响应

> 原文：<https://www.sitepoint.com/ripple-cursor-response/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/sqripples.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/sqripples.swf"></object>**

[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/sqripples.zip)。

**1。**画一个边框简单没有颜色的正方形。任何尺寸都可以。

**2。**将正方形转换成图形对象，命名为“squareStatic”。

**3。**选择正方形图形并按 f8 将其转换为电影剪辑。

**4。**进入 movieclip 的时间轴，选择正方形，并减小其尺寸使其变小。

**5。**转到第 11 帧，按 f6 键创建一个新的关键帧。

**6。**选择第 11 帧。这将自动选择其中的所有对象。

**7。**接下来，再次调整正方形的大小，但这一次，要大得多。

**8。**转到效果调色板，将 alpha 减小到 0。

**9。**回到第一个关键帧，应用补间动画。

10。现在复制帧并粘贴到新层，如下所示:

![1085_tool1](img/d42b7fa6773dd0c9ce622c913b9407a4.png)

**11。**回到主时间轴，创建一个小的透明按钮。

**12。**转到方形 movieclip 时间轴，新建一个层，将按钮拖到这个层上。

**13。**双击第一个关键帧打开动作标签。插入:
`action stop().`

**14。**右键点击按钮，插入动作:

```
on (rollOver)  

{

gotoAndPlay(2)； `}`
 **17。**返回主时间轴，将电影剪辑拖放到舞台上。
18。完成了！按 ctrl+enter 运行电影。

```

## 分享这篇文章