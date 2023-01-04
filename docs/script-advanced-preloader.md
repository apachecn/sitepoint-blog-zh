# Flash 脚本–高级预加载程序

> 原文：<https://www.sitepoint.com/script-advanced-preloader/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/adloader.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/adloader.swf"></object>**

[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/adloader.zip)。

**1。**在一部新电影中设置三层。将第一个命名为“加载器”，第二个命名为“动作”，第三个命名为“图片”。

**2。**创建两个文本框。进入类型 pallet，将它作为动态文本，变量名为“totalbytes ”,没有选中任何选项。将另一个变量命名为“加载的字节”。

**3。**将该文本字段放置在加载器层。

**4。**在图片层，导入你选择的大图。设为 5 帧长，最后一帧设为关键帧。然后，删除前四帧，只留下第五帧。将第五帧命名为“结束帧”。将`stop();`动作插入该层。

**5。现在转到动作层，创建两个关键帧。**

**6。**在第一个关键帧中，插入动作:

```
loadedbytes = getBytesLoaded()/1000;  

totalbytes = getBytesTotal()/1000;  

if (loadedbytes == totalbytes)  

{  

gotoAndPlay ("endframe");  

}
```

**7。**在第二个关键帧中，插入动作:

```
gotoAndPlay (1); 
```

**8。**在键盘上按两次 Ctrl+Enter。搞定了！预装程序完成。

## 分享这篇文章