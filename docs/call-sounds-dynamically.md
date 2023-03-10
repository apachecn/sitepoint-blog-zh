# Flash 脚本–动态呼叫声音

> 原文：<https://www.sitepoint.com/call-sounds-dynamically/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/soundcall.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/soundcall.swf"></object>**

[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/soundcall.zip)。

假设您有一个 Flash 应用程序，允许用户选择 50 首不同的歌曲。如果你在应用程序中存储了 50 个不同的歌曲文件，那么这个项目的文件将会非常大。

如何才能避免这种情况？我们将创建 50 个 swf 文件，每个文件包含一首不同的歌曲。我们将根据用户对歌曲的选择动态调用该文件。

听起来很难？不是的！我们开始吧！请注意，本教程假设您已经将一个声音文件导入 Flash。

**1。**创建一个新的 Flash 电影，并在其第一帧上添加以下脚本:

```
function mysound (flag) 

{ 

mysound = new Sound(this); 

mysound.stop(); 

//test is id of the sound file 

mysound.attachSound("test"); 

//if true play 

if(flag){mysound.start();} 

//if false stop 

if(!flag){mysound.stop();} 

}
```

**2。**打开库面板，右键单击声音文件。转到“链接属性”，并选择显示“导出此符号”的单选按钮。在“标识符”字段中为符号指定一个 id 名称。

![1098_tool1](img/ddb82454d2f8c957a31de93d97151619.png)

**3。**现在将声音文件从库中拖到舞台上。

**4。**选择声音层，进入“声音”面板，如下图所示。应用此处显示的设置。

![1098_tool2](img/a9f930c85314b96519812a77e621afc8.png)

**5。**将电影 you 保存为“sound.fla”，仅发布电影 swf。

**6。**打开一个新的 flash 电影，如上图创建两个按钮。将第一个标记为“播放声音”，第二个标记为“停止声音”。

**8。**创建一个空白电影剪辑。给它一个实例名“snd”。

**9。**在第一帧中，插入动作:

```
loadMovie("sound.swf","/snd");
```

10。右击“播放声音”按钮，插入动作:

```
on (release) { _root.snd.mysound(true);}
```

**11。**右击“停止声音”按钮，插入动作:

```
on (release) { _root.snd.mysound(false);}
```

你完了！放电影！

使用这种技术，您可以调用任意多的声音文件。

## 分享这篇文章