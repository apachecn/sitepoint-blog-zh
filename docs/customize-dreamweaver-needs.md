# 根据您的需要自定义 Dreamweaver

> 原文：<https://www.sitepoint.com/customize-dreamweaver-needs/>

漫画家挑选他们的钢笔。承包商选择他们的木材。老师装饰他们的教室。为什么开发者不能自定义自己的编辑器？等一下…他们可以！

本文解释了 Macromedia Dreamweaver 用户可以实现的一些最有用的自定义技巧。

*重要提示*

本教程是为 Macromedia Dreamweaver MX 编写的，它是 2002 年 12 月的最新版本。但是，所提供的许多示例可以在早期版本中实现(尤其是 v4，一个几乎相同的 Dreamweaver 产品)。

Dreamweaver 调整可能包括对/Configuration/目录的修改。该文件夹中的文件对软件的稳定性至关重要。因此，建议进行备份。

编辑 Dreamweaver 配置文件时，不要在 Dreamweaver 中打开文档。这可能会损害稳定性。其他选择可能包括 Microsoft Notepad 或 Macromedia HomeSite。我假设您对该产品及其功能有基本的了解。

##### 控制您的文档扩展名

购买 Macromedia 的 Dreamweaver MX 后，我注意到的第一件事是 HTML 文件的默认命名为。htm 文档。虽然这和传统的一样可以接受。坦率地说，html 扩展，从我自己的标准的过渡，让我很烦恼。为了管理您的文档扩展，让我们从以下位置获取 DW 的扩展配置文件:

../Macromedia/Dreamweaver MX/Configuration/document types/mmdocumenttypes . XML

在这里您可以找到我们程序的文档扩展名设置。要更改默认的 HTML 扩展名，请找到第 3 行，并将以下代码从:

winfileextension="htm，html，shtml，shtm，stm，lasso，xhtml，inc "

收件人:

winfileextension="html，htm，shtml，shtm，stm，lasso，xhtml，inc "

此外，Mac 用户可以对 macfileextension 属性进行同样的修改。我们在这里所做的只是改变了超文本标记语言扩展的优先级。很简单，真的。通过在文件中编辑类似这样的内容，我们可以为 Dreamweaver 生成全新的动作。

这是我编辑的另一个。我发现浏览器可读的 PHP 文档类型 PHPS 缺少颜色编码，所以我决定将这个扩展添加到 PHP 的扩展库中。怎么会？在第 67 行，我更改了 winfileextension 和 macfileextension 属性以包含。PHP 文件类型。他们最初读到:

winfileextension="php，php3 "

所以我把它们改成了:

winfileextension="php，php3，phps "

当然，也可以对 macfileextension 进行同样的更改。有了这些信息，您应该能够毫不费力地控制您的 Dreamweaver 扩展操作。

##### 更改默认文档模板

标准在变，个人喜好在变，有时我们只是变得很懒。幸运的是，那些善良的 Macromedia 开发者给我们留下了一些方便的选择。让我们在以下位置打开新的文档目录:

../Macromedia/Dreamweaver MX/Configuration/document types/new documents/

在这里，您可以找到 Dreamweaver 启动的文件的所有基本文档模板。您只需打开适当的 filetype 文档并编辑模板以满足您的需求。对于本例，假设我们希望在可预见的将来为每个 HTML 文档使用相同的外部级联样式表位置。为了使这成为 Dreamweaver 自然代码的一部分，我们将编辑 HTML 模板 Default.html 的内容。看起来是这样的:

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"> 

<html> 

<head> 

<title>Untitled Document</title> 

<meta http-equiv="Content-Type" content="text/html; charset="> 

</head> 

<body> 

</body> 

< /html> 
```

简单地编辑你的代码，如下所示，然后保存。

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"> 

<html> 

<head> 

<title>Untitled Document</title> 

<meta http-equiv="Content-Type" content="text/html; charset="> 

<link href="/stylesheet.css" rel="stylesheet" type="text/css"> 

</head> 

<body> 

</body> 

< /html> 
```

就这么简单！

##### 编辑标签库

Dreamweaver 标记库是所有标记设置的来源，包括换行符、内容和大小写。这主要用于优化代码的文件大小和浏览器兼容性，因为诸如空白之类的东西往往会导致问题。但是为了方便起见，您可能还希望更改一些其他选项。要打开标签库编辑器，请转到:

编辑>标签库…

Dreamweaver 编写的标签和属性的主列表提供了关于该软件能够产生什么的简明见解。新增功能将扩展代码视图下拉菜单，以及 Dreamweaver 理解文档的能力。还应该利用标记格式。尽管 Macromedia 已经创建了最受尊敬的互联网开发软件，但其默认设置并不出色。

##### 主要 Dreamweaver 首选项

对于典型的 Dreamweaver 新手来说，大量的软件调整可能会令人望而生畏。然而，大多数可以完全忽略，因为只有少数会影响你的工作环境。我将强调您应该定制的主要首选项。首先，让我们进入首选项面板，选择:

编辑>偏好设置…

***类别重要调整***

以下是您应该批准的最重要的更改列表。

*   **常规—**文档选项:在新窗口打开文件；编辑选项:用<强>和< em >代替< b >和< i >
*   **可访问性—**无
*   **代码着色—**无
*   **代码格式-**缩进；选项卡大小；自动包装；默认标记大小写；默认属性案例；覆盖的大小写；定中心
*   **代码提示—**延迟
*   **代码重写-**重写代码:粘贴时重命名表单项
*   **CSS 样式–**无
*   **文件类型/编辑器—**无
*   **字体—**代码视图
*   **高亮显示—**无
*   **隐形元素—**展示；服务器端包括
*   **层—**Netscape 4 兼容性
*   **布局视图—**无
*   **新建文档—**默认文档类型；使文档符合 XHTML
*   **面板—**无
*   **在浏览器中预览—**浏览器
*   **快速标签编辑器—**选项
*   **站点—**所有
*   **状态栏—**连接速度(最好是 56k)
*   **验证器—**全部

##### 编辑键盘快捷键

键盘快捷键是开发人员最好的朋友。一旦你习惯了它们，工作就会变得容易得多。但是，如果您从使用不同快捷方式的程序切换到 Dreamweaver 会怎么样呢？或者你真的讨厌 Macromedia 提供的那些？不要担心！

编辑>键盘快捷键…

这是 Dreamweaver 键盘快捷键的主控制面板。“设置”只是一个可以激活或停用的快捷方式的数据库。

例如，让我们假设我们正在对我们的朋友乔开一个恶作剧。我们的朋友总是使用 Ctrl+N 和 Ctrl+O 快捷键来创建和打开文档。是的，我们是邪恶的。让我们把它们调换一下。

确保选择 Macromedia Standard 作为活动集，然后单击第一个图标复制数据库。将该集重命名为“我们的邪恶计划”。在“菜单命令”部分，最大化文件类别并找到“新建…”。通过高亮显示旧的快捷方式并点击减号来删除它。将指针放在文本字段中，并按下键盘上的 Ctrl+O(不要逐字键入—执行操作)。然后选择更改。

现在我们有了一个重复的 Ctrl+O 条目，但是 Dreamweaver 将只使用数据库中出现的第一个条目。因此，像我们之前做的那样，返回并用 Ctrl+N 替换“打开…”动作。点击确定。

嗯，我们的恶作剧是成功的…某种程度上。乔必须在明天之前修好软件，否则他将被老板解雇！打开键盘快捷键编辑器。选择 Macromedia Standard 作为您的设置，然后单击 OK。把它打开，扔进垃圾桶。确认删除我们的邪恶计划，这样就没有证据了。点击确定并退出。就是这样！

我们不要滥用这个功能，好吗？

##### 利用 Macromedia 扩展

如果你还没有安装扩展软件，你可以在这里下载[。](http://www.macromedia.com/exchange/em_download/)

扩展管理器是 Macromedia 定制 Dreamweaver 读取、写入和加速方式的最有用的工具。广泛的受欢迎程度和产品的目标受众产生了一个与该软件一起工作的附加模块的特殊数据库。

要找到适合你的插件，请访问[http://www.macromedia.com/exchange/](http://www.macromedia.com/exchange/)。在这里，您可以找到可扩展的 Macromedia 产品列表，以及 Exchange Manager 的链接。将您的浏览器指向 Dreamweaver (MX)部分，并获取一个 Macromedia ID。一旦你注册了，你就可以获得无数的下载。

登录，使用“浏览扩展…”下拉菜单找到合适的插件和平台。将所需文件保存到:

../Macromedia/扩展管理器/下载/

传输完成后，打开 Macromedia Extension Manager。

选择:

文件>安装扩展…

然后只需按照屏幕上的指示，最后重新启动 Dreamweaver。有关模块使用和位置的说明可在管理器的扩展列表中找到。

##### 重要参考

以下是 Macromedia Dreamweaver MX 官方文档和参考资料的列表，将有助于定制过程。这些文件中的一些已经在本教程的创建中使用过，并可能进一步用于增强您的 Dreamweaver 体验。

不要忘记备份重要的文件！

**MM DW 交换**
数据库下载–帮助> Dreamweaver 交换
[http://dynamic.macromedia.com/bin/MM/exchange/main.jsp?product=dreamweaver](http://dynamic.macromedia.com/bin/MM/exchange/main.jsp?product=dreamweaver)

**MM DW 支持中心**
信息数据库帮助> Dreamweaver 支持中心
【http://www.macromedia.com/support/dreamweaver/】T4****

**扩展 DW MX**
本地信息数据库帮助>扩展 Dreamweaver

**MM 支持:在线论坛**
在线留言板帮助> Macromedia 在线论坛 **[http://www.macromedia.com/support/forums](http://www.macromedia.com/support/forums)**

 ****定制 MM DW MX**
压缩 Adobe PDF 文件 **[http://www . macromedia . com/support/Dreamweaver/custom/Customizing _ dwmx/](http://www.macromedia.com/support/dreamweaver/custom/customizing_dwmx/ )**

 ******MM DW MX 快捷方式矩阵**
压缩 Adobe PDF 文件
[http://www . macromedia . com/support/Dreamweaver/documentation/dwmx _ Shortcut s/](http://www.macromedia.com/support/dreamweaver/documentation/dwmx_shortcuts/ )

**MM DW 更新**
文件下载
[http://www . macromedia . com/support/Dreamweaver/Downloads _ updaters . html](http://www.macromedia.com/support/dreamweaver/downloads_updaters.html)

##### 缩略图生成器和优化器

缩略图生成器/优化器是该软件的一个鲜为人知的功能。虽然它没有以任何方式隐藏，但我知道几乎没有人利用过这个方便的工具。转到:

命令>创建网络相册…

在这里，您可以找到一种快速、简单、轻松的方法来随时随地处理图像。选择源文件夹和目标文件夹，调整其他可选设置，让 Dreamweaver 完成剩下的工作。我通常会扔掉自动生成的 HTML 页面，尽管您可能会发现它很有用。

有了这些提示，您应该可以成功地改变 Dreamweaver 软件在 Web 开发中帮助您的方式。祝你好运！****** 

## ****分享这篇文章****