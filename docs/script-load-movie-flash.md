# Flash 脚本–在 Flash 中加载电影

> 原文：<https://www.sitepoint.com/script-load-movie-flash/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/loadmovie.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/loadmovie.swf"></object>**

[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/loadmovie.zip)。

请注意，在本教程中，我将假设您知道如何创建按钮。

**1。**创建两个按钮，一个标记为“加载电影 1”，另一个标记为“加载电影 2”。

**2。**创建一个新的空白影片剪辑，即其中没有对象的剪辑。

**3。**给这个新的空白影片剪辑一个实例名“dummy”。

**4。**右键点击“加载电影 1”按钮，选择“动作”，插入以下内容:

```
on (release) 

{ 

loadMovie ("mov1.swf", "/dummy"); 

}
```

这段代码将名为 mov1.swf 的 swf 文件加载到目标“/dummy”中。您可以在自己的 swf 文件中用替换此引用，该文件应该位于同一目录中。

**5。**右击“加载电影 2”按钮，选择“动作”，插入该动作:

```
on (release) 

{ 

loadMovie ("mov2.swf", "/dummy"); 

}
```

按 ctrl+enter 测试影片。

加载电影的语法是:

```
loadMovie(url [,location/target, variables]]);
```

**url:** 文件的路径。

```
location/target:
```

**location:** 可选参数，指定电影加载到的级别。

载入的影片会继承目标影片片段的位置、旋转和缩放属性。

**variables:** 可选参数，指定发送与要加载的电影相关的变量的方法。

参数必须是字符串“GET”或“POST”如果没有参数，则省略这些参数。

## 分享这篇文章