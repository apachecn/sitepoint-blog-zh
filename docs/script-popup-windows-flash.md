# Flash 脚本–Flash 中的弹出窗口

> 原文：<https://www.sitepoint.com/script-popup-windows-flash/>

这个教程将向你展示如何创建一个没有工具栏的弹出窗口。

**1。在你的 Flash 文件中创建一个按钮。**

**2。**右键单击按钮，转到操作给出操作:

```
 getURL ("JavaScript:openWin('test.html', '250', '250')");
```

**3。**发布您的电影，选择您喜欢的 HTML 和 Flash 选项。

**4。**在记事本中打开 html 页面，在`<head>`和`</head>`标签之间插入以下代码。

```
 <script language="JavaScript"> 

 function openWin(url, w, h) 

 { 

 var winprop = "width=" + w + ",height=" + h; 

 openwin = window.open(url,'',winprop); 

 } 

 </script>就是这样！在浏览器中预览页面。

```

## 分享这篇文章