# Flash 脚本–连续图像推子

> 原文：<https://www.sitepoint.com/sequential-image-fader/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/imagefader.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/imagefader.swf"></object>**

[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/imagefader.zip)。

**1。**拍一张照片，转换成电影剪辑。这里我使用了一个矩形作为图像，但是当然，你可以使用任何你选择的图像。

**2。**来自图书馆。将电影剪辑拖到舞台上。

**3。**我取了 6 个电影剪辑，并给每个剪辑取了一个实例名“blue1”、“blue2”、“blue3”、“blue4”、“blue5”或“blue6”。

**4。**现在来了动作脚本。在第一个关键帧中插入动作:

```
fade = 100;  

//starting movie number  

movienum = 1; 
```

**5。**创建如下所示的关键帧:

![1084_tool2](img/fcaad8180af1a4ea5344a727dffd33e8.png)

**6。**在第五个关键帧中，插入动作:

```
setProperty ("/blue" add movienum, _alpha, fade);  

fade = fade - 5;  

if(fade == 0)  

{  

fade = 100;  

setProperty ("/blue" add movienum, _alpha, fade);  

movienum = movienum + 1;  

gotoAndPlay (2);  

}  

if(movienum == 7)  

{  

fade = 100;  

movienum = 1;  

gotoAndPlay (1);  

} 
```

**7。**在最后一个关键帧给出动作。

```
gotoAndPlay (5);. 
```

**8。**测试电影，看电影如何褪色。运用你自己的想象力，为你的特定目的重新使用电影！

## 分享这篇文章