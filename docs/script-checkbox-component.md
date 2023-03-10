# Flash 脚本–复选框组件

> 原文：<https://www.sitepoint.com/script-checkbox-component/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/checkbox.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/checkbox.swf"></object>**

利用复选框组件很容易。

**1。**按 ctrl+f7 打开组件面板。

**2。**将 checkbox 组件拖到舞台上，并选择第 1 帧中的 checkbox 组件。该组件的参数将显示在属性检查器中。

**3。**在属性检查器的“实例名称”文本框中键入“cbox ”,以命名组件。

**4。**接下来，我们需要指定我们希望复选框组件的初始状态是选中还是取消选中。

若要让注记格自动显示为选中，请访问“初始值参数”弹出式菜单，然后选择“真”。

**5。**在“标签放置参数”弹出菜单中，检查默认值是否设置为“右对齐”。这确保了标签将显示在复选框的右侧。

**6。**键入“f1”作为点击处理程序的名称。稍后，您将编写 ActionScript 来定义 Click 处理程序应该做什么。

**7。从现有层中选择第一个关键帧。按 f9 打开“动作”框，并插入以下动作:**

```
function f1() 

{ 

cbox.setLabel("Checked")  

}
```

**8。**上面的动作很简单。当你点击复选框时，它调用函数`f1()`。此函数将复选框的标签设置为“已选中”。就这么简单！

[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/checkbox.zip)。

## 分享这篇文章