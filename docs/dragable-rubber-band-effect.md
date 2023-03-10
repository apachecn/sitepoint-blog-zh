# Flash 脚本–Flash MX 可拖动橡皮筋效果

> 原文：<https://www.sitepoint.com/dragable-rubber-band-effect/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/rubberman.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/rubberman.swf"></object>**

**1。**首先创建一个尺寸为:宽度=400，高度=50 的电影。

**2。**创建鼠标指针。

**3。**现在，将鼠标指针转换为 movieclip，命名为“pointer”，然后给它一个实例名“pointer”。

**4。**使用文本工具，键入“RUBBER”——它应该会出现，如上面的电影所示。

**5。**选择文本并将其转换为电影剪辑。命名为“rubber”，给它一个实例名“rubber”。

现在，双击文本 movieclip 进入其时间轴。

**6。**新建一层，命名为“按钮”。

**8。**在按钮层，创建一个不可见的按钮。

**9。**对按钮应用这些操作:

```
on (rollOver) { 

  Mouse.hide(); 

  _root.pointer._visible = true; 

} 

on (rollOut) { 

  Mouse.show(); 

  _root.pointer._visible = false; 

} 

on (press) { 

  Mouse.hide(); 

  _root.pointer._visible = true; 

  _root.flag = true; 

  _root.elas = false; 

} 

on (release, releaseOutside) { 

  Mouse.show(); 

  _root.pointer._visible = false; 

  _root.flag = false; 

  _root.elas = true; 

}
```

10。返回主时间轴(即场景 1)，在默认关键帧中，插入动作:

```
//make the pointer invisible 

pointer._visible = false; 

flag = false; 

elas = false; 

//start width is width of movieclip 

startwidth = 100; 

//mvwidth is width of movie 

mvwidth = 400; 

function setwidth() 

{ 

  if(flag) { 

    width = mvwidth - _xmouse 

    width = width + 100 

    rubber._width = width 

    iws = rubber._width 

  } 

  if(elas){ 

    elastic += (startwidth - rubber._width); 

    elastic *= .4 

    rubber._width += elastic; 

  } 

} 

//this acts as a timer it calls the setwidth function  

every 50 milli seconds 

setInterval(setwidth,50)
```

完了！现在，测试电影！

## 分享这篇文章