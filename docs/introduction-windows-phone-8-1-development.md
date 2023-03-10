# Windows Phone 8.1 开发简介

> 原文：<https://www.sitepoint.com/introduction-windows-phone-8-1-development/>

在我们开始之前，我想让你知道一些关于为 Windows Phone 8.1 开发应用的事情。您将需要*Visual Studio Professional 2013 Update 4 IDE*作为编程环境，并对面向对象编程语言有基本的了解。编程语言是 *C#* ，如果你觉得更熟悉的话，类似于 *Java* 。您还需要安装 Windows 8.1。今天，我将向您展示如何从安装 IDE 开始，一步一步地构建您的第一个 Windows Phone 应用程序。所以让我们把手弄脏吧！

## 准备好东西

首先，你需要安装*Visual Studio Professional 2013 更新 4* 。如果你是学生，你可以在 *[梦想火花](https://www.dreamspark.com/Product/Product.aspx?productid=93)* 免费下载。下载完成后，解压位于*内的文件。iso* 带 [WinRar](http://www.win-rar.com/download.html?&L=0) ， [WinZip](http://www.winzip.com/win/en/downwz.html) ，[守护程序工具](http://www.daemon-tools.cc/downloads)或任何你喜欢使用的等效工具。最后，解压缩后的文件夹应该如下所示:

![VS Installation files](img/c7316742e57b1dc96327e9f87d84589c.png)

双击 *vs_professional.exe* 将带您完成安装过程。选择您要安装文件的位置，同意许可条款和隐私政策，然后点击“下一步”继续。选择所有复选框，然后单击安装。别担心，我不会带你去任何不安全的地方，我向你保证！根据你的计算机能力，完成安装需要一些时间，所以不要只盯着进度条。在此过程中，安装程序可能需要重新启动才能完成安装。完成后，你会看到一条确认信息和一个启动按钮。点击它，我们就可以开始我们的冒险了！

您可能希望使用您的 Microsoft 帐户登录 Visual Studio，或者留待以后再登录。下一步是在蓝色、深色和浅色之间选择一个颜色主题。我选择了黑暗，因为屏幕亮度让我室友保持清醒，但你可以选择任何你喜欢的。点击*启动 Visual Studio* ，我们就可以开始了。

在我开始这个项目之前，我想给你一个提示，关于你在程序中保持打开的窗口。这个是可选的，但是我觉得会帮助你有更好的体验。当你设计应用程序时，最好将最常用的工具放在手边。第一个窗口是*工具箱*。在窗口顶部，点按大头针以停靠它。您还会发现固定*文档大纲*窗口非常有用。起初，它看起来好像什么都没有，但是当你有一层一层的元素时，它会很方便。您会发现很容易选择、隐藏/显示或在正确的层次结构中放置您想要的元素。最后是属性窗口。在那里，您可以修改任何元素，而无需进入代码。如果你没有马上看到这些窗口，你可以在*视图- >其他窗口*菜单选项中找到它们，或者在加载你的项目之后。

## 我们的第一个项目

进入 Visual Studio 后，我们会看到一个起始页。在左侧点击*新项目*创建一个新项目。如果出于某种原因，你看不到这个选项，不要担心。点击*文件- >新建- >项目*菜单选项。在这两种情况下，您都会看到下面的窗口:

![New Project window](img/adb1a123926652361c9ab3f3917ed424.png)

在左侧，确保您选择了*模板- > Visual C# - >商店应用- > Windows Phone 应用*。选择*空白 App (Windows Phone)* ，命名为 *Hello World* 。最后点击*确定*。起始页关闭，自动打开 *App.xaml.cs* 。

在这个类中，有几个方法可以告诉应用程序在执行时的行为，比如将要打开的第一个页面是什么。你可以继续关闭这个文件，因为我们不需要它。在*解决方案浏览器*窗口中删除 *MainPage.xaml* 文件。这只是一张白纸，所以不要为此难过。我们将使用一个具有 Windows Phone 设计的更通用的页面来简化操作。再次在*解决方案浏览器*中，*右键*点击 *Hello World 项目- >添加- >新项目*。在打开的窗口中选择*空白页*并将其命名为 *MainPage.xaml* 。

![New Item window](img/826d05dc7e45f25f150575da258a4bb8.png)

点击*添加*，然后*是*。如果您遵循了我之前的提示，您的 Visual Studio 窗口应该是这样的:

![Visual Studio](img/888248927afe0c3731fea2ed4327890b.png)

在设计器中选择*我的应用*，在*属性*窗口中将其*文本属性*更改为 *Hello World* 。然后选择*页面标题*并按照与主页面相同的方式进行更改。我们将在后面的帖子中查看*工具箱*中的每个工具，但是对于这个项目，我们将保持原样。现在我们准备调试。

## 在仿真器上调试

位于菜单栏和编码窗口之间，点击绿色的*调试按钮仿真器 8.1 WVGA 4 英寸 512MB* 。您将看到您的应用程序在几乎真实的 Windows Phone 设备中打开。顾名思义，屏幕尺寸将是 4 英寸，模拟器将使用 512 兆内存。第一次加载操作系统时要有耐心，这需要一段时间。但最终，它出现了！您的第一个 Windows Phone 应用程序！

![WP Emulator](img/130d3d76e64d2995c557db53d018572e.png)

如果你没有真正的设备可用，Windows Phone 模拟器是一个很好的工具。在右侧，您可以看到一个竖条，上面有一系列图标。我将从头开始解释它们的用途。

![WP Emulator tools](img/7f3b2adb883a0a50901ee1f92a67cc5e.png)

前两个很容易猜测，关闭和最小化。接下来是*单点输入*你可以用它来按压，按住，滑动。在那下面，*多点触摸输入*帮助你放大和缩小，就像你用两个手指一样。*向左旋转*和*向右旋转*在两侧转动手机。*适合屏幕*将手机缩放至适合您屏幕高度的大小。另一方面，您可以使用缩放来自定义缩放级别。点击问号可以在网上找到一些帮助。最后，最后一个按钮将我们带到*附加工具*。

![WP Emulator Additional tools](img/aaa971259971c90fd409d8fbea520ffb.png)

您可以看到八个不同的选项卡。它们都有助于在真实环境中测试你的应用。我们将在以后的其他文章中更仔细地研究它们。

## 在真实设备上调试

如果你想在真正的 Windows Phone 上调试你的应用程序，或者安装它，你需要一个开发者帐户。如果你还没有，你可以在这里找到关于如何加入“开发者俱乐部”的信息。如果你已经加入，点击*开始*，输入你的 Windows Phone 开发者注册详情。

通过 USB 电缆连接您的 Windows Phone 并解锁屏幕。电话应该会被自动识别。用你的开发者帐户凭证登录，几分钟后你的手机就可以用于调试和测试了。在 Visual Studio 中，选择 Device 而不是 4 英寸模拟器，然后点击按钮。你会看到奇迹就在你眼前发生！

## 结论

在本教程中，我们看到了如何为 Windows Phone 8.1 开发做好一切准备。我们第一次体验了一个适度的 Hello World 程序，我们将在以后继续修改它。可以从 GitHub 下载源代码[。我希望这篇文章对你是一个很好的介绍。](https://github.com/sitepoint-editors/HelloWorld_project)

谢谢你的时间。愿代码与你同在！

## 分享这篇文章