# Flash 脚本–滚动图像库

> 原文：<https://www.sitepoint.com/scrolling-image-gallery/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/imgscroller.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/imgscroller.swf"></object>**

[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/imgscroller.zip)。

**1。**创建一个空白的影片剪辑，给它一个实例名“drag”。

**2。**我使用了一个正方形的 movieclip，并简单地制作了同一个剪辑的 5 个实例。出于文件大小的考虑，我使用了一个普通的电影剪辑，当然，你也可以使用图片。

将每个图像转换为影片剪辑，并为每个实例命名，例如“a1”、“a2”、“a3”、“a4”和“a5”。

**3。**转到“拖动”电影剪辑的时间线，并创建两个关键帧。

**4。**在第一个关键帧中，插入动作:

```
mw = getProperty("/a1",_width);  

for(i=5; i>=1; i--)  

{  

mx = getProperty("/a"add i,_x);  

//right side  

if(mx > 400)  

{  

if(i==5){mv=1};  

if(i==4){mv=5};  

if(i==3){mv=4};  

if(i==2){mv=3};  

if(i==1){mv=2};  

mx = getProperty("/a"add mv,_x);  

setProperty("/a" add i,_x,mx - (mw + 2));  

}  

//left side  

if(mx < 0)  

{  

if(i==1){mv=5};  

if(i==2){mv=1};  

if(i==3){mv=2};  

if(i==4){mv=3};  

if(i==5){mv=4};  

mw = getProperty("/a"add mv,_width);  

mx = getProperty("/a"add mv,_x);  

setProperty("/a" add i,_x,mx + (mw + 2));  

}  

}  **5。**在第二个关键帧中，插入动作:

```
gotoAndPlay (1); 
```

 **6。**进入主时间轴，新建一层，命名为“动作”。
 **7。**在“动作”层创建三个关键帧。
![1083_tool2](img/54805839d72af979d529f0f77fadf174.png)
 **8。**在第一个关键帧中，插入动作:

```
mx = getProperty("/a1",_x);  

mw = getProperty("/a1",_width);  

nomc = 5; 
```

```
9. Insert the following action into the second key frame:

```
mc = _root.drag._x  

if (mc>0 and mc<180)  

{  

for (i=1; i<=nomc; i++)  

{  

mx = getProperty("/a" add i, _x);  

setProperty ("/a" add i, _x, mx+10);  

}  

}  

if (mc>220 and mc<400)  

{  

for (i=1; i<=nomc; i++)  

{  

mx = getProperty("/a" add i, _x);  

setProperty ("/a" add i, _x, mx-10);  

}  

} 10。在第三个关键帧中，插入:

```
gotoAndPlay (2); 
```

 **12。**大功告成！按 ctrl+enter 测试影片。

```

```

```

## 分享这篇文章