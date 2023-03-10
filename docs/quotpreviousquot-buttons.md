# Flash 脚本–带有“下一页”和“上一页”按钮的图片滚动条

> 原文：<https://www.sitepoint.com/quotpreviousquot-buttons/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/smoothscroller.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/smoothscroller.swf"></object>**

[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/smoothscroller.zip)。

**1。**打开一个宽 350 高 120 的新电影，给它 40 帧/秒的帧率。

对于这个例子，我将使用 8 张图片。每张图片的长度是 350 像素。

**2。**整理图片如下图。

![1136_image1](img/b65ea09be40500ba91aac21cf90cd7a4.png)

**3。**将图片转换成电影剪辑，给电影剪辑一个实例名“pic”。

**4。**创建两个按钮:一个用于“下一个”，另一个用于“上一个”。

**5。**创建一个空白的电影剪辑，并给它一个实例名“player”。

**6。**进入“玩家”电影的时间线，创建三个关键帧。

**7。**在第一帧中，插入以下动作:

```
stop (); 

incr = 6;
```

**8。**在第二帧中，插入动作:

```
xpos = getProperty ("/pic",_x); 

if(/:flag) 

{ 

if(xpos >= /:xpos) 

{ 

setProperty("/pic", _x,xpos - incr); 

} 

else 

{ 

gotoAndStop(1); 

} 

} 

if(!/:flag) 

{ 

if(xpos <= /:xpos) 

{ 

setProperty("/pic", _x,xpos + incr); 

} 

else 

{ 

gotoAndStop(1); 

} 

}
```

**9。**在第三帧中，插入动作:

```
gotoAndPlay (2);
```

10。现在，回到主时间线。在“下一步”按钮中，添加以下代码:

```
on (release)  

{ 

if((/:dummy - (piclen*minus)) <= /:xpos) 

{ 

flag=1; 

/:xpos = /:xpos - /:piclen; 

player.gotoAndPlay(2); 

} 

}
```

**11。**然后，在“上一步”按钮中，插入:

```
on (release)  

{ 

if((/:dummy - (piclen)) >= /:xpos) 

{ 

flag=0; 

/:xpos = /:xpos + piclen; 

player.gotoAndPlay(2); 

} 

}
```

**12。**现在，在主时间线的第一帧，插入这些动作:

```
xpos = getProperty("/pic", _x); 

dummy = xpos; 

nopic = 8; 

minus = nopic-2; 

setProperty("/pic", _x,xpos); 

piclen = getProperty("/pic", _width)/nopic;  

flag=0;
```

就是这样！该放电影了。

## 分享这篇文章