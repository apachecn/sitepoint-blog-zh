# Flash 脚本–球和线效果

> 原文：<https://www.sitepoint.com/script-ball-line-effect/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/ballline.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/ballline.swf"></object>**

[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/ballline.zip)。

**1。创建两个圆形物体，并使其中一个比另一个大。**

**2。**将这两个对象转换成电影剪辑。

**3。**给较大的圆形对象一个实例名“parent”。

**4。**给较小的一个实例命名为“child1”。

**5。**如下图创建一条对角线。

![1081_tool1](img/ff222583e7088c2094fbdb6c63a8dd2f.png)

**6。**将其转换为电影剪辑，并给它一个实例名“line”。

**7。**右键单击“child1 ”,转到“actions ”,然后插入操作:

```
onClipEvent (load)  

{  

spring = false;  

startX = _x;  

startY = _y;  

spX = 0;  

spY = 0;  

_root.line._visible = 1;  

parentx = _root.parent._x;  

parenty = _root.parent._y;  

_root.line._x = parentx;  

_root.line._y = parenty;  

_root.line._xscale = _root.child1._x-parentx;  

_root.line._yscale = _root.child1._y-parenty;  

}  

onClipEvent (mouseDown)  

{  

startDrag ("", true);  

spring = true;  

}  

onClipEvent (mouseUp)  

{  

stopDrag ();  

spring = false;  

}  

onClipEvent (enterFrame)  

{  

parentx = _root.parent._x;  

parenty = _root.parent._y;  

_root.line._x = parentx;  

_root.line._y = parenty;  

//////
_ root . line . _ xscale = _ root . child 1 . _ x-parentx；
_ root . line . _ y scale = _ root . child 1 . _ y-parenty；

//!spring = if false 
 if(！
 { `//code to create the movements  

spX += (startX-_x);  

spY += (startY-_y);  

spX *= .09;  

spY *= .09;  

_x += spX;  

_y += spY;  

}  

}`
 **8。**就是这样！按 ctrl+enter 测试影片。

```

## 分享这篇文章