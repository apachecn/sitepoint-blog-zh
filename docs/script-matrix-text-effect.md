# Flash 脚本–矩阵文本效果

> 原文：<https://www.sitepoint.com/script-matrix-text-effect/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/matrix.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/matrix.swf"></object>**

你已经看过电影 *The Matrix* ，现在你想从演职员表中创建绿色文字效果吗？没问题！[在这里下载样本文件](https://www.sitepoint.com/examples/flashscript/matrix.zip)。

**1。**创建如下所示的文本字段。

![](img/aa3f35aee32c4a4b0cdf39dd52698156.png)

给它一个变量名“text”。

**2。**接下来，把这个转换成电影剪辑，命名为“text”。

**3。**选择现有的电影剪辑，按 f8，并再次将其转换为电影剪辑。

**4。**给它一个实例名“矩阵”。

![](img/5a55105a12072900b83b47ac7ed0a868.png)

**5。**现在，进入电影剪辑文本的时间线，并在现有层上创建一个新层。将其命名为“动作”。

**6。**在动作层创建两个空白关键帧。在第一帧中，插入:

```
text = random(2)+"r"+random(2)+"r"+random(2)+"r"+random(2)+"r"+random(2)+"r"+random(2) 

+"r"+random(2)+"r"+random(2)+"r"+random(2);
```

**7。**在第二帧中，插入此动作:

```
gotoAndPlay (1);
```

**8。**现在，转到主电影时间线，进入《黑客帝国》电影片段的时间线。

**9。**在该层创建三个关键帧，并在第一个关键帧插入如下动作:

![](img/54805839d72af979d529f0f77fadf174.png)

```
mov = 0; 

ran = getProperty("", _x);
```

10。在第二个关键帧中，插入:

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

**11。**在第三个关键帧插入:

```
gotoAndPlay (2);
```

**12。**现在，回到主时间轴，在这一层创建两个关键帧。

13。将此动作插入第一个关键帧:

```
if (matrixparticles<150) 

{ 

duplicateMovieClip("matrix","matrix" add i,i) 

scale=random(60)+10 

setProperty("matrix" add i,_x,random(450)) 

setProperty("matrix" add i,_xscale,scale) 

setProperty("matrix" add i,_yscale,scale) 

matrixparticles++ 

i++ 

}
```

**14。**在第二个关键帧中，添加:

```
gotoAndPlay (2);
```

就是这样！您已经创建了自己的矩阵文本效果。

## 分享这篇文章