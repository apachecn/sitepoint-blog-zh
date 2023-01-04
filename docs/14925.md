# Flash 脚本–从 Flash 控制 Visual Basic

> 原文：<https://www.sitepoint.com/visual-basic-flash/>

****1。**创建一个新的 VB 项目。**

**2。**将 Shockwave Flash ActiveX 控件添加到项目中。在窗体中创建控件的实例。

**3。**将控件命名为“flashmov”。

**4。**打开窗体的代码窗口(即 Form_Load 事件)，并添加以下代码:

```
'movie.swf is movie file 

flashmov.Movie = App.Path & "movie.swf"
```

请注意，上面的代码假设电影与 vb 项目在同一个文件中。

**5。**使用`fscommand`可以从 flash 中调用 vb 函数。

例如，您可以在 flash 中创建一个按钮并插入动作:

```
//the below code is for flash not for vb 

fscommand("execute");
```

**6。**创建 swf 文件。

**7。**响应 Flash 命令的 vb 代码是:

```
Private Sub flashmov_FSCommand(ByVal command As String,  

ByVal args As String) 

If command = "execute" then  

MsgBox "Hey i can control vb" 

End Sub
```

**8。**当您按下闪光灯按钮时，代码显示一个消息框。

## 分享这篇文章