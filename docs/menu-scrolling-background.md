# Flash 脚本–带有滚动背景的滚动菜单

> 原文：<https://www.sitepoint.com/menu-scrolling-background/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/menu.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/menu.swf"></object>**

这是一个受欢迎的效果，而且很容易做到！以下是如何…

**1。**先设置框架属性。尺寸应为 420 X 90 像素，帧速率应为每秒 25 帧。

**2。**导入背景图片。背景图片是我用 photoshop 做的，尺寸是 700 X 85 像素。

**3。**选择背景图片并将其转换为电影剪辑。命名为 bg_static。

**4。**给这个实例命名为“bg”。

![1190_1](img/2e5e5e72ed67fa7123002ae740d2906a.png)

**5。**创建一个 1045 X 40 像素的电影剪辑。将此实例命名为“menu”。

**6。**选择第一帧并插入该动作:

```
xm = 0; 

//function to set the xpos of the movieclip 

function xpos(bar_length,mul) 

{ 

hpos = 0; 

scroll_length = 420; 

incr = bar_length/scroll_length; 

xm = _xmouse; 

if(_xmouse <= 10){xm = 10;} 

if(_xmouse >= 400){xm = 400;} 

scroll_x = hpos - xm; 

scroll_x = scroll_x * mul; 

x_pos = scroll_x * incr; 

x_pos = x_pos + hpos; 

return x_pos; 

} 

_root.onEnterFrame = function () 

{ 

// call function xpos 

x_pos = xpos(700,.20); 

with (bg) 

{ 

_x += (x_pos - _x)*.4; 

} 

// call function xpos 

x_pos = xpos(950,.75); 

with (menu) 

{ 

_x += (x_pos - _x)*.4; 

} 

}
```

现在你完了！测试电影。

[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/menu.zip)。

## 分享这篇文章