# Flash 脚本–简单的打字机效果

> 原文：<https://www.sitepoint.com/simple-typewriter-effect/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/typewriter.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/typewriter.swf"></object>**

[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/typewriter.zip)。

**1。**创建一个文本字段，并给它一个变量名“type”。

**2。**创建如下所示的 3 个关键帧:

![1067_tool1](img/8bbe21e61674a48ab6f4f3cc8b4a283d.png)

**3。**在第一个关键帧中，插入动作:

```
text = "This text is being displayed with type writer effect"; 

max = length(text);  

loop = 1; 
```

**4。**在第二个关键帧插入动作:

```
loop = loop + 1  

type = substring(text,1,loop); 
```

**5。**在第三个关键帧插入动作:

```
if (i > max)  

{ stop(); }  

else  

{ gotoAndPlay (2); } 
```

**6。**就是这样！您已经建立了自己的类型编写器效果！

## 分享这篇文章