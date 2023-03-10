# Flash 脚本–前进和后退按钮效果

> 原文：<https://www.sitepoint.com/backward-button-effect-2/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/forwardbackward.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/forwardbackward.swf"></object>**

本教程将向您展示如何构建一个 MC，其行为就像一个按钮，在翻转时播放动画，在卷展时向后播放。[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/forward-backward.zip)。

##### 说明

**1。**启动一个新的 Flash 项目，并转到插入，新符号。

**2。**给这个符号起个名字——姑且称之为“mc”。单击电影剪辑，然后单击确定。

**3。**现在，我们来制作动画。

创建您希望“mc”显示的任何动画，无论是形状补间动画、动作补间动画还是逐帧动画。只创建向前的动画，不需要创建向后的动画。

然后单击第一帧并按 F2 键(如果您使用的是 Flash MX，或者 F9 键，如果您使用的是 Flash 5)。这将打开操作窗口。在那里插入一个`stop();` 命令。然后，把它放入时间线。

**4。**完成动画制作后，将“mc”符号从库中拖到主时间轴上，并将其放入您选择的帧和层中。

**5。**现在单击“mc ”,在 Flash MX 中按 F2(或在 Flash 5 中按 F9 ),调出动作窗口，并应用以下代码。请注意，每次主持人被放在主舞台上时，您都必须这样做。

```
onClipEvent (enterFrame) { // runs a hit test 

  if (this.hitTest(_root._xmouse, _root._ymouse, true)) { 

    this.nextFrame(); // if true the mc plays forward 

  } else { 

    this.prevFrame(); // if not plays backwards to beginning 

  } 

}
```

如果你测试你的电影，你会注意到它不像按钮那样会把手举起来。

**6。真正简单的解决方法是，返回并编辑你的“mc ”,插入一个顶层。**

**7。**现在，在新图层中创建一个形状(或其他图形元素),它跨越动画帧的长度，并覆盖动画。

**8。**点击新图层选择你刚做的对象。然后单击，插入并转换为符号。称之为“隐藏”并选择按钮，然后确定。现在你的对象是一个按钮！

**9。**双击“隐藏”按钮，调出其编辑窗口。

10。右击 up 状态框架，点击“剪切框架”，粘贴到“命中”框架中，使按钮不可见。

**11。**现在，让“mc”像一个按钮:

*   在主时间线上，单击您希望播放头在单击“隐藏”按钮时转到的帧。
*   插入一个“此处”的框架标签。
*   现在，回到“mc”电影剪辑中，单击“隐藏”按钮以打开动作窗口，并应用以下代码:

```
on (release) { 

  _root.gotoAndPlay("here"); // _root. targets the main movie 

}
```

请注意，将按钮放在电影边缘附近会破坏这种效果，因为当鼠标离开 mc 时，电影会通过检测向后播放动画。

如果用户在动画后离开按钮边缘，而您的按钮离 mc 边缘太近，它将无法检测到鼠标离开 mc 区域，并且不会播放向后的动画。

## 分享这篇文章