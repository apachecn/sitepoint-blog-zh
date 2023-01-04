# Flash 脚本–带有默认文本显示的随机文本效果

> 原文：<https://www.sitepoint.com/effect-default-text-display/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/stopdisplay.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/stopdisplay.swf"></object>**

看起来很酷…而且很简单！这里是你需要创建自己的随机文本效果。

**1。**创建一个文本字段并命名。我给我的名字取了一个变量名“shuffle”。

**2。**将 textfield 转换成电影剪辑，命名为“Random”。

**3。**右键单击电影剪辑并访问其动作。插入以下行动:

```
onClipEvent (enterFrame)  

{ 

if(!_root.flag) 

{ 

shuffle = chr(random(26)+65); 

} 

}
```

**4。**创建电影剪辑的 4 个副本。

我希望 RandomText 影片剪辑显示 Flash，然后暂停片刻，然后继续。单词“flash”中有 5 个字母，所以我将使用 5 个电影剪辑。

**5。**选择第一个 movieclip，给它一个实例名“T1”；将第二个命名为 t2，其他电影剪辑以此类推。

**6。**将当前图层命名为“text”，延长三个关键帧，然后锁定图层。

**7。**在文本层上面新建一层，命名为“动作”。在该层创建三个空白关键帧。在第一个关键帧中，插入此动作:

```
i = 0 

flag = 0 

lapse = 20; 

display = 10;
```

**8。**在第二个关键帧中，插入以下代码:

```
i = i + 1 

if(i > display) 

{ 

flag = 1; 

_root.t1.shuffle = "F"; 

_root.t2.shuffle = "L"; 

_root.t3.shuffle = "A"; 

_root.t4.shuffle = "S"; 

_root.t5.shuffle = "H"; 

} 

if(i > lapse) 

{ 

flag = 0; 

i = 0; 

}
```

**9。**在第三个关键帧中，插入这个动作:

```
gotoAndPlay (2);
```

10。就是这样！运行电影，你会看到上面的效果。

## 分享这篇文章