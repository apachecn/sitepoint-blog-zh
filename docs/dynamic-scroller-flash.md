# Flash 脚本–在 Flash 中创建动态滚动条

> 原文：<https://www.sitepoint.com/dynamic-scroller-flash/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/dynscroller.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/dynscroller.swf"></object>**

[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/dynscroller.zip)。

**1。**打开一个新电影，指定宽度为 200，高度为 150。

**2。**创建一个宽 150 高 550 的矩形。

**3。**为滚动条创建一个小矩形。我的宽 16，高 35。

**4。**现在将较大的矩形转换为电影剪辑，并给它一个实例名“bar”。

**5。**将滚动条转换为按钮。选择滚动条，按 f8，并将其转换为电影剪辑。

**6。**给 scroller 一个实例名“scroll”。

7 .**。**现在，进入滚动电影剪辑的时间轴。右键单击该按钮，并选择“操作”。在下面插入操作:

```
on (press) 

{ 

//left=180 right=180 top=10 bottom=100 

startDrag ("/scroll", false, 180, 10, 180, 100); 

_root.flag = true; 

} 

on (release)  

{ 

stopDrag (); 

}
```

**8。**接下来，回到主时间轴。进入菜单插入>新符号> movieclip，并将其命名为“代码”。

**9。**将代码 movieclip 从库中拖到舞台上，并进入其时间轴。

10。创建两个空白关键帧。在第一个关键帧中，插入以下动作:

```
if(_root.flag) 

{ 

vtop = 12; 

scroll_length = 110; 

bar_length = getProperty("/bar", _height); 

incr = bar_length/scroll_length; 

scroll_y = vtop - getProperty("/scroll", _y); 

y_pos = scroll_y * incr; 

y_pos = y_pos + vtop; 

setProperty ( "/bar", _y, y_pos ); 

} 

if(!_root.flag) 

{ 

setProperty ( "/bar", _y, 20); 

}
```

**11。**在第二个关键帧，插入:
`gotoAndPlay (1);`

**12。**现在回到主时间线。选择“bar”movie clip 并转到其时间轴。

13。定位对象，使对象的中心点位于顶部。为此，请选择对象，按 shift，然后使用向下箭头。

这就够了！测试电影。

## 分享这篇文章