# Dr Design 回答你的问题

> 原文：<https://www.sitepoint.com/dr-design-answers-questions/>

***时隔很久，设计博士又回来了！他找到了听诊器，穿上白大褂，现在正在给病人看病。所以，如果你的布局有问题，你的 PNGs 有问题，或者你需要为那些麻烦的样式表做点什么，为什么不[寻求咨询](mailto:drdesign@sitepoint.com)？好医生来了！***

##### 精致的框架

亲爱的设计博士，
我有一个使用框架的网站。当用户点击链接时，我可以改变单个框架的内容。但现在我想知道，当用户单击单个链接时，如何更改多个框架中的内容。你能帮我吗？

我相信你知道，大多数框架链接看起来都是这样的:

```
<a href="hello.html" target="main">my link</a>
```

通过使用一点像这样的智能 JavaScript:

```
<script language="javascript" type="text/js"> 

<!-- 

function doSwitch(frame1,frame2,frame1loc,frame2loc) 

{ 

 top.frame1.location.href=frame1loc; 

 top.frame2.location.href=frame2loc; 

} 

// --> 

</script>
```

您可以重用代码。这使您可以做以下事情:

```
<a href="doSwitch('main','top','main.asp','top.asp');"> test</a>
```

让我们先退后一步。不需要调整 JavaScript。唯一会改变的方面是 doSwitch()内部的代码。通过使用如上所述的封装 JavaScript，我们可以省去为每个链接编写函数的麻烦。

##### 将 default.asp 作为您的主页

设计博士，我正在 NT 服务器上使用 ASP，我想知道是否有一种简单的方法可以让 default.asp 成为加载的主页(而不是 index.html 或。htm)？我需要联系我的主机吗，或者有没有一种方法可以自动做到这一点？

这是一个很好的问题——尽管答案可能不是你所期望的。许多主机采用非常强大的控制面板，这往往可以在 www.mydomain.com/cp 或一些类似的地址。如果您的主机没有控制面板，或者控制面板没有提供指定默认主页的选项，您需要联系他们以使更改生效。

由于 Internet Information Server 的设置方式，每当在没有指定文件的情况下访问目录(www.mydomain.com/mydirectory)时，它都会查找一个默认页面列表。IIS 维护一个“允许”作为默认主页的文件列表(由您的主机设置)。设置这个实际上是一个相当简单的任务，但是你的 default.asp 页面没有出现的事实表明你需要联系他们，让他们把它添加到列表中。这实际上是一个 20 秒的工作。祝你好运！

##### ASP 资源

嘿，设计博士，有没有 ASP 手册这种东西(相当于 ASP 的 PHP 手册)？有哪些学习 ASP 的好资源？

我将把这个问题留给 [SitePointForums](http://www.sitepointforums.com) 的成员，因为他们已经积累了惊人的资源列表。我认为这个列表已经尽可能的完整了，我建议你把它保存在方便的地方。

<q>***通用 ASP 网站***</q>

尽管有数以千计的基于 ASP 的网站，这里列出了我们最喜欢的——它们是最全面和最有用的。

*   [LearnASP](http://www.learnasp.com/learn/newbie.asp)
*   4 个来自罗拉的家伙
*   [ASP101](http://www.asp101.com/)
*   [ASP 区域](http://www.aspzone.com/)
*   [今日 ASP](http://www.asptoday.com/)
*   [总 ASP](http://www.totalasp.co.uk/)
*   [DevDex — ASP](http://www.developersdex.com/asp/)
*   [ASP 常见问题解答](http://www.aspfaqs.com/aspfaqs/)
*   [ASP 商场](http://www.aspemporium.com/aspEmporium/index.asp)
*   [阿斯帕兰](http://www.aspalliance.com/default.aspx)
*   [ASP 程序员资源](http://www.programmersresource.com/)
*   一个小网站，却是一块真正的宝石。有我读过的关于数据验证的最好的文章(在线或离线)

***微软链接***

作为 ASP 的开发者，微软提供了名副其实的资源宝库，其中最主要的是微软开发者网络(MSDN)。这些文件可能会令人困惑，但值得一读。

*   [动态服务器页面指南](http://www.msdn.microsoft.com/library/default.asp?url=/library/en-us/iisref/html/psdk/asp/aspguide.asp)
*   [平台 SDK:互联网信息服务 5.1-Visual Basic 对象模型](http://www.msdn.microsoft.com/library/default.asp?url=/library/en-us/iisref/html/psdk/asp/vbob74bw.asp)
*   [ADO 程序员指南](http://www.msdn.microsoft.com/library/default.asp?url=/library/en-us/ado270/htm/mdmscadoprogrammersguide.asp)
*   [ADO API 引用](http://www.msdn.microsoft.com/library/default.asp?url=/library/en-us/ado270/htm/mdmscadoapireference.asp)
*   [为 NTS 介绍 CDO](http://www.msdn.microsoft.com/library/default.asp?url=/library/en-us/cdo/html/_denali_cdo_for_nts_library.asp)
*   [关于 Windows 2000 的 CDO](http://www.msdn.microsoft.com/library/default.asp?url=/library/en-us/cdosys/html/_cdosys_about_cdo_for_windows_2000.asp)
*   [MSDN 杂志——大量关于 ASP/ADO/COM/XML 等的文章](http://www.msdn.microsoft.com/msdnmag/default.asp?ID=0)

***杂项 MS 数据库链接***

MSDN 中几个特别重要的部分是与微软的数据库相关的部分。以下是几个重要的方面:

*   [Microsoft Jet SQL for Access 2000 基础版](http://www.msdn.microsoft.com/library/default.asp?url=/library/en-us/dnacc2k/html/acfundsql.asp)
*   [Access 2000 的中级 Microsoft Jet SQL](http://www.msdn.microsoft.com/library/default.asp?url=/library/en-us/dnacc2k/html/acfundsql.asp)
*   [用于 Access 2000 的高级 Microsoft Jet SQL](http://www.msdn.microsoft.com/library/default.asp?url=/library/en-us/dnacc2k/html/acadvsql.asp)
*   [SQL 服务器](http://www.msdn.microsoft.com/library/default.asp?url=/nhp/default.asp?contentid=28000409)

***ASP 文档***

Microsoft Internet Information Server 的每个完整安装都提供了 ASP 文档。如果已安装，可通过 http://localhost/iishelp 查看。如果没有，请检查您的安装选项。尽管微软个人 Web 服务器允许 ASP，并提供 ASP 文档，但它可能既稀疏又不准确。

##### 弹出窗口灾难

设计博士，帮帮我！弹出窗口对某些人来说似乎很简单，但对我来说它们绝对是一种痛苦…有没有简单的方法来制作它们？

是啊！Builder.com 的[窗口生成器](http://builder.cnet.com/webbuilding/pages/Programming/Scripter/092497/toolwb.html?tag=st.bl.7701-8-4561792-1.txt.CoolTool)正是你需要的。它允许您指定您的设置，以找到最适合您的组合。然后，您可以简单地将代码剪切并粘贴到您的 HTML 页面中！我一直在用它…

##### 彩色滚动条很简单

医生，我在很多网站上见过彩色滚动条，但我不知道如何创建它们！有什么想法吗？

很简单，用 CSS 就搞定了。CSS 允许我们作为设计者做各种以前用 HTML 不可能做的事情。这就是你要找的代码！

```
BODY  

{  

 scrollbar-face-color: #738A8C;  

 scrollbar-shadow-color: #738A8C;  

 scrollbar-highlight-color: #738A8C;  

 scrollbar-3dlight-color: #738A8C;  

 scrollbar-darkshadow-color: #738A8C;  

 scrollbar-track-color: #ffffff;  

 scrollbar-arrow-color: #ffffff;  

}
```

##### 停止我的背景移动！

**设计博士，在很多网页上我都见过这些不动的背景。这是怎么做到的？**

有两种方法可以创造这种效果，但在我向你展示它们之前，让我鼓励你适度使用它们。就像在屏幕上飞来飞去的时钟一样，使用有趣的效果可以很容易地从吸引人跨越到业余水平。一定要小心使用这个，并且只在适当的时候使用！

我们的第一个例子使用 CSS。就我个人而言，我更喜欢这种方法，因为我可以通过外部样式表链接到它，并从一个文件中保持对整个网站的控制。

```
BODY  

{  

 background-image: url(starbackground.gif);   

 background-repeat: no-repeat;  

}
```

在上面的例子中，我增加了一行额外的代码来给你更多的能力。`background-repeat`属性允许你让图像只出现一次(就像经典水印一样)，让它只在水平方向重复出现(通过将`no-repeat`改为 `x`)，或者在垂直方向重复出现(通过将`no-repeat`改为 `y`)。

还有另一种简单的方法来创建这种效果，使用下面的代码:

```
<body background="images/mainback.jpg" bgproperties="fixed">
```

##### HTML 到 Word 文档转换

**设计博士，有什么快速简单的方法可以把 HTML 文档变成 Word 文档？如果有帮助的话，我在我的虚拟主机账户上安装了 ASP。**

好问题！就数据收集和让人们在线填写表格而言，网络常常是一种痛苦。将数据以不同于 HTML 的格式传输给我们的用户是一个好主意。

使用 MIME 和 Content-type，我们能够将数据流式传输给我们的用户，就好像它不是 HTML 一样。在下面的例子中，我们将把数据作为一个 Word 文件传输给你的用户。

```
<%  

 Response.ContentType = "application/msword"  

%>
```

这是一个伟大的技术。然而，MS Word 并不是您可以传输数据的唯一格式。以下是其他几个例子:

*   `"text/plain"`
*   `"text/html"`
*   `"application/pdf"`
*   `"text/richtext"`
*   `"text/xml"`
*   `"application/wordperfect"`
*   `"application/mswrite"`
*   `"application/msexcel"`
*   `"application/msword"`
*   `"application/mspowerpoint"`
*   `"text/vnd.wap.wml"`
*   `"text/vnd.wap.wmlscript"`
*   `"image/vnd.wap.wbmp"`

传播爱！
设计博士

非常感谢本周的病人。设计博士咨询室将在下周同一时间再次开放，但你可以随时联系医生，他会在 SitePoint 回答你的设计问题。

## 分享这篇文章