# Flash 脚本–落雪效果

> 原文：<https://www.sitepoint.com/script-falling-snow-effect/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/snow.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/snow.swf"></object>**

[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/snow.zip)。

**1。**创建一个小雪花我用一个小圆圈填充白色。

**2。**将这个雪花对象转换成一个电影剪辑，并给它一个实例名“snow”。

**3。**现在，进入这个雪电影剪辑的时间线，在这个层创建三个关键帧。

**4。**在第一个关键帧给出动作:

```
mov = 0; 

ran = getProperty("", _x);
```

**5。**在第二个关键帧给出动作:

```
setProperty ("", _y, mov=mov+2); 

snowy = getProperty("", _y); 

setProperty ("", _x, (ran+random(2))); 

if(snowy > 310) 

{ 

setProperty ("", _y, 50); 

mov=0; 

ran = ran - 10; 

}
```

**6。**在第三个关键帧给出动作:

```
gotoAndPlay (2);
```

**7。**现在回到主时间轴，在这一层创建两个关键帧。

**8。**将这些动作插入第一个关键帧:

```
if (snowparticles<150) 

{ 

duplicateMovieClip("snow","snow" add i,i) 

scale=random(60)+10 

setProperty("snow" add i,_x,random(450)) 

setProperty("snow" add i,_xscale,scale) 

setProperty("snow" add i,_yscale,scale) 

snowparticles++ 

i++ 

}
```

**9。**将此动作插入第二个关键帧:

```
gotoAndPlay (2);
```

就是这样！你创造了你自己的雪效果。

## 分享这篇文章