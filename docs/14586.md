# 干扰 PrintJob 类

> 原文：<https://www.sitepoint.com/messing-with-the-printjob-class/>

任何事情都是一样的，除非你需要在一个项目中使用一个给定的类或对象的功能，否则你永远不会钻研它，除非你是一个热心的探索者！。我发现有必要快速探索 Flash MX 2004 的打印功能，并感到惊喜。

在 Flash MX 2004 中，新的 PrintJob 类比以前版本中的 print()和 printAsBitmap()函数强大得多，允许您简单地打印单个影片剪辑、影片剪辑的部分(使用打印区域参数)或整个级别。

在这里列出的例子中([源代码在这里找到](https://www.phireworx.com/sitepoint/blog/27052004.zip))，我需要快速打印出舞台上给定数量的电影剪辑(例子如下所示)。

[阶段截图](https://www.phireworx.com/sitepoint/blog/27052004.jpg)

我创建了以下函数来传入一个简单的 MovieClips 数组，允许我向打印队列添加多个页面。

 ``function PrintWhat(WhatToPrint:Array) {
var PrintQueue = new PrintJob();
var PrintStart:Boolean = PrintQueue.start();
if (PrintStart) {
for (i=0; i<=WhatToPrint.length; i++) { PrintQueue.addPage(WhatToPrint*);
}
PrintQueue.send();
}
}
//Prints Specific MovieClips
PrintWhat([MCPrint, MCPrint002,MCPrint003]);
//Prints a Level
//PrintWhat([0]);*` 

PrintWhat()函数接受 MovieClips 或 levels 数组作为参数，使用 print job 对象启动一个新的打印作业，调用 start()方法，然后该方法触发打印对话框。start()方法根据用户是单击“打印”对话框中的“确定”还是“取消”返回一个布尔值 true 或 false，并且当 PrintStart 为 true 时内部条件被触发或跳过。

如果用户选择打印并单击“OK ”,则解析传入的 WhatToPrint 数组，并使用 addPage()方法将页面添加到打印队列，使用 send()方法将作业发送到打印机。

注意:您可以为打印电影剪辑的部分传递额外的参数，但是我` 

## `分享这篇文章`