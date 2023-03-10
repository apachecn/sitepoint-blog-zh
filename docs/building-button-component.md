# Flash 脚本–构建按钮组件

> 原文：<https://www.sitepoint.com/building-button-component/>

 **<object type="application/x-shockwave-flash" data="https://i2.sitepoint.com/flash/button.swf" width="400" height="400" bgcolor="black"><param name="movie" value="https://i2.sitepoint.com/flash/button.swf"></object>**

[在此下载示例文件](https://www.sitepoint.com/examples/flashscript/button.zip)。

您可以使用 Macromedia Flash UI 组件快速轻松地将简单的用户界面元素添加到您的 Flash 文档中。

在本教程中，我们将看看使用按钮组件有多简单。

**1。**点击 ctrl+f7 打开组件面板。

**2。**将按钮组件拖到舞台上。

**3。**选择第 1 帧中的按钮组件。该组件的参数显示在属性检查器中。

**4。**要命名组件，请在属性检查器的“实例名称”文本框中键入“btn1”。

**5。**键入“f1”来命名点击处理程序。稍后，您可以编写 ActionScript 来准确定义 Click 处理程序应该做什么。

![1180_1](img/8ecde0adf4bf609a7a878e3a4a9882fd.png)

**6。**从现有图层中选择第一个关键帧，按 f9 打开动作框。

**7。**插入以下行动:

```
function f1(name) 

{ 

if(name == btn1) 

{btn1.setLabel("Button Pressed ")} 

}
```

这个动作很简单——当你按下按钮时，它调用函数`f1()`。

**8。**该函数有一个参数名，它采用按钮的实例名——在本例中是 btn1。

**9。**If 条件检查事件是否来自 btn1。如果它来自 btn1，它将按钮的标签设置为“按钮预置”。

这只是一个简单的例子。您可以在 If 块中插入任意数量的事件——试验一下，看看您能得到什么！

## 分享这篇文章