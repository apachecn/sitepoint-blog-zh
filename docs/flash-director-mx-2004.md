# 集成闪存 MX 2004 和 Director MX 2004

> 原文：<https://www.sitepoint.com/flash-director-mx-2004/>

毫无疑问:Flash MX 2004 是一个强大的开发环境，可以创建有趣且吸引人的应用程序。然而，有时您可能需要对与系统相关的功能进行更多的控制，例如能够检测用户正在使用的操作系统、启动外部程序、检测默认文件处理应用程序和控制系统窗口，尤其是在创建分布式应用程序时。

当您希望创建教育参考资料、演示文稿、CD-r om 或复杂的应用程序时，Flash 通常会(根据项目的复杂程度)满足 brief 的需求。但是，有时 Flash 本身并不能解决问题，需要更大的马力来达到预期的效果。

在本文中，我们将了解如何通过集成 Flash 和 Director 来查询运行应用程序的操作系统、返回信息并根据这些数据做出决策。
本文提供的信息应该会启发您探索 Flash 和 Director 集成的可能性，并让您体验一下通过一点想象力和天赋可以实现的东西。点击[这里](https://www.sitepoint.com/examples/flashdirector/flashdirectorsource.zip)可以下载文章源文件。

##### 示例场景

您正在 Flash 中构建一个应用程序，该应用程序需要打开几种不同的文件类型(电子表格、pdf、演示文稿、可执行文件等)，但是为了使应用程序尽可能流畅，您需要检查处理您尝试打开的文件类型的应用程序是否存在，并采取相应的措施。

在我看来，如果没有第三方或定制的集成工具，使用 Flash 来检查您试图打开的给定文件是否有注册的文件处理应用程序是极其困难的——如果不是不可能的话。

…进入舞台左边:导演 MX /MX 2004！

我最近在做一个 CD-ROM 应用程序的项目，并决定在 Flash 中创建界面。应用程序中有几个部分需要打开 PDF 文件，我在 CD-ROM 中包含了一个到 Adobe Acrobat Reader 的链接。一切都运行良好，直到客户决定向项目添加特性，包括打开多种文档类型。

当然，这提出了一个新问题:我需要知道并通知用户他们是否安装了打开他们选择的文档所需的应用程序，而 Flash 本身并不能胜任这项工作。

我没有放弃我已经写好的东西，而是决定研究其他方法来检索这些信息，并发现导演 MX 2004 是这项工作的最佳候选人。

##### 它如何组合在一起

我想到的解决方案是 Flash 和 Director 的融合，其中我使用 Flash 来创建接口，并作为“存根”来调用 Director 应用程序中的函数，而 Director 应用程序又使用 Xtra 来执行一系列功能。

当我研究检索信息的替代方法时，我遇到了一个名为[‘Buddy API’](http://www.mods.com.au)的 Director Xtra，它包含 140 多个用于与 Windows API 和 Macintosh 工具箱接口的函数。

这是最适合这项工作的 Xtra，虽然它是一个商业 Xtra，但未注册的免费版本包括两个免费功能。Xtra 包含许多函数，有些函数比其他函数更有用。点击[此处](http://www.mods.com.au/budapi/)了解更多详情。

幸运的是，我可以使用未注册的版本，因为我只需要 Xtra 的两个函数:

*   **FindApp:** 查找与文件类型相关的应用程序
*   **OpenFile:** 使用相关程序打开一个文件

实质上，Flash 电影嵌入在 director 应用程序中，我们不生产 Flash 投影仪，而是生产一个 Director 投影仪，它通过 Director Xtra 与操作系统交互，以检查某些标准，然后执行特定功能。

下图概述了本例中从 Flash 界面通过 Director 投影仪的信息流；虽然很简单，但是让我们快速看一下涉及的步骤以及这两个应用程序是如何通信的。

![1342_image1](img/9c2966e2e7a2d6396f15b774645b9a5d.png)

**步骤 1** 在嵌入式 Flash 界面中，用户单击一个按钮(在下面的示例中，它被命名为`openPDFDoco`)，该按钮调用 Director 应用程序中定义的函数，格式如下:

```
openPDFDoco.onRelease = function() { 

  getURL('lingo:FunctionHandler.FunctionName(Parameters)'); 

};
```

**步骤 2** 将文件的参数(名称、扩展名、类型)传递给 Director movie，并调用一个自定义函数，该函数通过 Buddy API Xtra 检查我们希望打开的文件是否有文件处理程序。

**步骤 3**Buddy API Xtra 使用`FindApp` ( `"file type"`)函数检查文件处理程序是否存在，并向 Director 投影仪返回一个`true`或`false`值。

**步骤 4** 根据文件处理程序是否存在，使用 Buddy API 的`OpenFile(filename, windowSize)`函数打开文件(步骤 5)。

**步骤 5** 验证文件处理程序，并在相关应用程序中打开文件。

**步骤 6** 如果我们试图打开的文件没有找到文件处理程序，我们可以在 Director projector 中触发一个警告来通知用户，或者我们可以调用 Flash 中的一个函数。

**Step 7** 我们调用 Flash 中特定帧要执行的函数；这可能很简单，只需将播放头移动到特定的帧，通知用户他们试图打开的文件缺少应用程序。

通过规划 Flash SWF 和 Director 投影仪的行为和集成，我们可以两全其美-一个易于维护的应用程序，具有强大的界面，能够执行许多不同的任务。

在下面的例子中，我实现了这个方法来创建一个可分发的 Director 投影仪。这可作为应用程序的安装程序，两者都是 Macromedia 扩展管理器文件(*。mxp)和 PDF 文档(*。但是，当您需要扩展 Flash 的功能时，这里概述的基本思想可以用于任何数量的不同项目。

![1342_image2](img/d66c3c448193dbf80f787ddce0a22c7d.png)

我们已经从流程的角度讨论了应用程序是如何工作的，所以让我们构建应用程序，看看它是如何结合在一起的。

##### 创建可伸缩的目录结构

正如任何使用多种信息源的开发项目一样，我们需要建立一个工作目录结构，我们可以很容易地理解它，并在使用时尽量避免混淆；在这种情况下，我们使用 Flash FLA / SWF /。作为文件，以及导演电影/投影仪和 pdf。

创建一个合适的工作目录结构，如下所示:

![1342_image3](img/35e5e7faedb9ab8db9e081a3dcf4d702.png)

这真的很简单，但是，如果没有基本的基础知识，我们很容易在编辑多个文件时陷入混乱。

在应用程序的根文件夹(“Director 应用程序”)中，我们有两个文件夹(“闪存文件”和“ISO”)。Flash 界面和相关的 ActionScript 文件将位于“Flash Files”文件夹中。“ISO”文件夹包含我们稍后将创建的 Director 电影和投影仪文件，以及“files”文件夹，其中将包含应用程序的资源文件(pdf)。

##### 创建 Flash 界面

你可以让你的 Flash 界面如你所愿的复杂或简单；在这个例子中，我们将创建一个带有单个按钮的简单界面(只是为了简单起见),它将启动 PDF 打开过程。

***设定场景***

*   打开 Flash，创建一个 450 像素宽 300 像素高的新 Flash 文档。接受默认帧速率，然后单击“确定”。
*   重命名默认层`Actions`，并在下面添加另外两层，称为`Buttons`和`Interface`。
*   选择`Interface`层的第一帧，创建一个带有相关标题和其他信息的界面，以满足您的需求。
*   选择`Buttons`层的第一帧，创建一个名为`launchButton`的新按钮。添加相关的图形和文字装饰，使按钮的功能。一旦你对这个按钮满意了，从库面板拖动一个实例到`Buttons`层的第一帧，命名这个实例为`launchButton`。

![1342_image4](img/d57ea6bdf877dc400fccf77c23bff910.png)

我们已经完成了设置 Flash 电影的第一阶段。目前，没有 ActionScript 来控制按钮的行为；一旦我们设置了 Director 项目、导入了 SWF 并设置了 roundtrip 环境，我们将在后面添加它。

*   将您的 Flash 文档作为`Flash Interface.fla`保存到您之前创建的“Flash Interface”文件夹中，并将您的 SWF(文件>导出>导出电影)作为`Flash Inteface.swf`。

##### 将 Flash 接口导入 Director

既然我们已经创建了基本界面，我们需要将其导入 Director 并设置一些参数。

***设定场景***

1.  在 Director 中，创建一部新电影(“文件”>“新电影”)。在属性检查器的“电影”选项卡中，将舞台大小设置为 450 x 300。
2.  选择文件>导入并找到您刚从闪存中导出的`Flash Interface.swf`。单击“添加”将其添加到导入列表，然后单击“导入”。

4.  如果“投射”窗口不可见，请选择“窗口”>“投射”来显示窗口并根据您的喜好排列它。

现在，您应该可以在内部演员窗口中看到导入的 Flash 电影。

6.  选择 Flash 电影，然后选择属性检查器的“成员”选项卡。更改要测试的电影的“名称”。在同一选项卡中，单击“编辑”按钮并找到 SWF 文件的源文件(`Flash Interface.fla`)。
7.  将导入的 SWF 的一个实例拖到舞台上，在舞台上选择 Flash 电影后，将“属性”检查器的“Sprite”选项卡中的“L”(左)和“T”(上)参数编辑为 L:0，T:0。

现在，我们可以通过 Director 中的往返方法轻松编辑 Flash SWF 文件。

9.  将您的 Director 电影保存在您之前创建的 *Director 应用程序/ISO* 目录中。

如果您现在双击舞台上的 Flash 电影，它将自动启动 Flash 进行编辑。当文件在 Flash 中打开时，单击时间轴左上角的“完成”按钮会自动更新 Director 中嵌入的 SWF。

![1342_image5](img/4f7d318bf065605c2db0997ec0bb0076.png)

这让生活变得容易多了；使更新变得无缝，并减少 Flash SWF 编辑丢失或被覆盖的机会。

##### 安装伙伴 API Director Xtra

为了充分利用我们在这里的工作成果，你需要安装 Buddy API 3.6 Director Xtra。此 Xtra 可从这里下载。

下载完成后，提取。拉链还是。hqx 文件，并将 budapi.x32 文件复制到 Director Xtras 文件夹中，在 Windows 和 Macintosh 中，该文件夹看起来如下所示:

```
Windows - c:windowsprogram filesmacromediadirector mx 2004configurationxtras 

Mac OS X - /Users/~USERNAME/Library/Application Support/Macromedia/Director MX 2004/Configuration/Xtras
```

##### 添加行话

如果您现在尝试发布您的 Director 投影仪，事情看起来会有点奇怪—投影仪会成功创建，但是当您尝试加载它时，它会打开大约一秒钟，然后关闭。

为什么？…嗯，Director 中没有添加任何术语或 JavaScript 代码。虽然它可以在 Director 中进行内部预览，没有任何问题，但是一旦它被导出到投影仪中，播放头就会碰到 Flash 电影的最后一帧并退出。

我们需要开始在 Director 中添加代码来控制这一点—一个简单的乐谱脚本。

*注意:如果乐谱窗口没有打开，确保是选择窗口>乐谱。*

1.  选择行为通道的第一帧，右键单击并选择“帧脚本”。

![1342_image6](img/a8ca5cc467a2be06705f1f37c67dfd53.png)

3.  当脚本窗口打开时，命名演职人员`pauseMovie`并添加以下代码:

```
on exitFrame 

  go to the frame 

end
```

这个脚本只是暂停电影，并去掉放映机中令人讨厌的退出行为。

如果您现在预览您的投影仪(文件>发布)，您将看到投影仪窗口保持打开，我们可以继续添加更多的控制代码。

##### 将 BuddyAPI Director Xtra 添加到项目中

为了让代码正确运行，我们需要将 BuddyAPI 3.6 Xtra 添加到项目中；它将在发布时包含在 Director Projector 中。

项目打开后，选择“修改”>“电影”>“附加”，然后点击“添加”。从 Xtras 文件夹中找到 budapi.x32 文件，然后单击“确定”。

我们现在可以插入代码来控制刚刚添加的 Xtra。

##### 创建文档处理功能

我们的下一个任务是添加控制功能，使我们能够接受来自 Flash 的传入功能请求，信不信由你，这并没有你想象的那么难。

如果我们查看导演电影的内部演员表，我们会看到，在位置 1，我们有之前创建的导入的 SWF 文件，在位置 2，我们有我们添加的脚本。

我们现在将向位置 3 和 4 添加 2 个脚本，以促进闪存和控制器之间的通信。

1.  在“角色转换”窗口(“窗口”>“角色转换”)和“行为检查器”打开的情况下(“窗口”>“行为检查器”)，选择位置 3。从行为检查器中，点按“+”按钮并选择“新行为”。

![1342_image7](img/97af619f908667b6eafb411689b37151.png)

3.  将行为命名为`prepareMovie`并点击“确定”。单击“脚本窗口”图标并添加以下代码:

```
global DMXDocHandler  

on prepareMovie   

  DMXDocHandler = new ( script "DMXDocHandlerClass" )  

end prepareMovie
```

5.  关闭脚本窗口。
6.  在角色转换中选择了`prepareMovie`脚本后，在属性检查器的“脚本”选项卡中，将类型从`Behavior`更改为`Movie`。

我们在这里做的是使用`prepareMovie`事件处理程序来创建我们将要添加的脚本的新实例，称为`DMXDocHandlerClass`。

8.  在“投射”窗口打开(“窗口>投射”)和“行为检查器”打开(“窗口>行为检查器”)的情况下，选择位置 4。在行为检查器中，单击“+”按钮并选择“新行为”。
9.  将行为命名为`DMXDocHandlerClass`，并将属性检查器中的“类型”更改为`Parent`。单击“脚本窗口”图标并添加以下代码:

```
property pDefaultDocDir  

on new (me)   

    pDefaultDocDir = "Files"  

  return me     

end   

on OpenFile me , p_FileType , p_Dir , p_FileName  

  set FileToOpen = the moviePath & pDefaultDocDir  & "" &  p_Dir & "" & p_FileName   

  -- Open the FileType  

  if (p_FileType = "PDF") then  

    me.StartAcrobat ( FileToOpen )  

  end if  

end OpenFile  

--Acrobat File opening  

on StartAcrobat me, p_PDFFile   

   if ( baFileExists( p_PDFFile ) = 1 )  then -- check if file exists  

    set AcroExe = baFindApp( "pdf" ) -- see if acrobat is installed  

    if AcroExe <> "" then -- if they have acrobat and the file exists  

      baOpenFile(  p_PDFFile , "Maximised" ) -- open the file  

    else --  

      alert("You do not have Adobe Acrobat installed")  

    end if   

  end if  

end StartAcrobat
```

11.  关闭脚本窗口。

让我们检查一下代码，看看发生了什么以及这个函数是如何工作的。

首先，我们声明 pDefaultDocDir，以便以后可以访问它。这是文件的默认位置，可以在我们之前创建的目录结构中引用。

当创建脚本的新实例时，我们用存储文件的目录名填充 pDefaultDocDir 的值，在本例中是“files”。

```
on new (me)   

    pDefaultDocDir = "Files"  

  return me     

end
```

然后我们有了一个新函数，它接受以下参数:

```
p_FileType:	The filetype (PDF, XLS, DOC, MXP etc)

p_Dir:		The directory in which the file is located 

p_FileName:	The name of the file当在 Flash 电影中按下按钮时，这些参数将从 Flash 电影传递到 Director 投影仪。我们稍后将在 Flash 中添加此代码。

```
on OpenFile me , p_FileType , p_Dir , p_FileName  

  set FileToOpen = the moviePath & pDefaultDocDir  & "" &  p_Dir & "" & p_FileName   

  -- Open the FileType  

  if (p_FileType = "PDF") then  

    me.StartAcrobat ( FileToOpen )  

  end if  

end OpenFile
```

然后，这些参数被连接成一个新的变量`FileToOpen`，这是我们试图打开的文件的完整路径。
如果在“PDF”中找到了`p_FileType`，我们调用`StartAcrobat()`函数，传递文件名。
*注意:您可以创建任意多的不同函数来检测不同的文件类型，而不仅仅是 pdf，如本例所示。*
然后，我们进入刚刚添加的定向器 Xtra 的内部。
当文件的完整路径被传递给`StartAcrobat()`函数时，我们使用 BuddyAPI Xtra 的`baFindApp(filetype)`函数来检测是否安装了 Adobe Acrobat。然后我们使用`baOpenFile(filename, windowSize)`功能打开文件。
如果 PDF 文件没有文件处理程序(即没有安装 Adobe Acrobat)，我们可以触发一个简单的警告来通知用户。

```
on StartAcrobat me, p_PDFFile   

      set AcroExe = baFindApp( "pdf" ) â€“ Is Acrobat installed?  

    if AcroExe <> "" then â€“ Acrobat is installed, enter condition  

      baOpenFile(  p_PDFFile , "Maximised" ) -- open the file  

    else   

      alert("You do not have Adobe Acrobat installed")  

    end if   

  end StartAcrobat
```

就这么简单！当然，您可以将用户带到 Flash 电影中的一个画面，并允许他们安装 Acrobat Reader，只需替换为:

```
alert()

for:

sprite(1).callFrame(frameNumber)
```

这是一个简单的修改，因为它允许我们通过引用内部转换中的相关 sprite 来调用 Flash 中特定帧上的函数(在这种情况下，导入的 Flash 电影位于位置 1)。
我们现在唯一要做的就是在 Flash 中添加代码来调用`DMXDocHandlerClass`行话脚本。
返回闪存，向控制器添加通信代码
在我们添加代码来引用我们希望打开的 PDF 文件之前，将 PDF 添加到*ISO/Files/PDF*文件夹中，以便我们可以检查它是否工作。
本文的源文件已经包含了一个，但是您可以轻松地添加自己的 PDF 文件；如果这样做了，不要忘记在下面的函数中更改文件名和/或文件类型。

1.  双击舞台上导入的 SWF 文件，在 Flash 中自动编辑 FLA。
3.  选择动作层的第一帧，并添加以下代码:

```
//==========================Load Documents Via Director  

launchButton.onRelease = function() {  

  getURL('lingo:DMXDocHandler.OpenFile("PDF","PDFs","SmartTabs QuickStart Guide.pdf")');  

};
```

*注意:在本文的源代码 FLA 中，我将 ActionScript 外部化为一个。作为文件，以提高可伸缩性和可用性。*

我们使用`getURL()`函数与导演投影仪对话，而`OpenFile()` lingo 脚本传递所需的参数(`p_FileType`、`p_Dir`和`p_FileName`)。

通过这种方式，我们可以高度控制打开的文件及其位置。这对于具有许多不同文件类型的大型项目来说非常重要，因为用户想要打开的资源位于不同的位置。

我们都在瞬间完成了！现在是时候回到 Director 来发布和测试应用程序了。

9.  单击时间线左上角的“完成”按钮返回到 Director。

测试应用程序
是时候测试应用程序了。选择文件>发布。
现在，您将看到一台 Director 投影仪。单击按钮，看看会发生什么—如果一切顺利，您应该可以成功打开文章源文件(或您添加的自定义文件，如果您安装了阅读器)中包含的 PDF。
要将您的项目刻录到 CD 上，只需复制 ISO/文件夹的内容(删除 Director Movie *。dir)。如果您希望光盘放入驱动器后自动运行，请添加一个自动运行文件。
该应用程序运行良好，并且将通过创建新的处理函数来访问您希望打开的任何文件类型，从而轻松地进行扩展。它允许您检查是否存在合适的程序来打开文件并执行适当的操作，而不是给出无信息的错误消息。
您可以使用该应用程序作为基础来创建复杂的安装程序、精美的 CD 前端，甚至是时髦的简历！
此外，由于试用版的限制，我们只触及了 BuddyAPI Director Xtra 的表面，但是，如果您希望使用更多功能，请购买它，并一起探索 Director 和 Flash 的额外功能。
现在，您已经掌握了闪存和控制器集成的基础知识，请尝试一下，看看您能完成什么。我相信 SitePoint 社区会很乐意看到你的作品！

```

## 分享这篇文章