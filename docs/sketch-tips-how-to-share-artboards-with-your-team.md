# #SketchTip:如何与团队共享画板

> 原文：<https://www.sitepoint.com/sketch-tips-how-to-share-artboards-with-your-team/>

能够不费吹灰之力地分享你的草图设计对你的团队大有裨益，尤其是对那些不使用草图或者其角色不是技术性的团队。至少，它让过于热情的团队成员不断回到你的办公桌前“看看一切进展如何”！

![Sketch to team](img/0653e8c7655bc0506561b7d1c2eef9eb.png)

那么，为什么还要和你的团队分享画板呢？因为:

*   项目经理可以更轻松地管理设计任务
*   开发者可以权衡设计的实用性
*   所有团队成员都可以提供反馈，而不需要草图
*   远程团队可以确信正在取得进展
*   减少不必要的耗时会议

## 与本地团队共享画板

与在同一办公室工作的团队成员(通过互联网)共享画板可能看起来有点多余，但会议确实会占用很多时间，我确信许多设计师和开发人员不喜欢整天被打扰或检查。

让我们来看看解决这些问题的两种方法。

### 本地网络共享

Sketch 3.4 引入了一个名为*本地网络共享*的新功能，基本上可以让 Sketch 用户与其他团队成员共享他们的画板，即使他们没有或没有使用 Sketch。收件人只需确保他或她使用的是与草图用户相同的网络连接。然后，团队的多个成员可以在自己舒适的办公桌前，使用自己的网络浏览器，查看设计师在做什么。

首先打开一个. sketch 文档(有多个画板的文档最合适)，从工具栏中选择“共享”，然后切换*启用本地共享*按钮。

![Local Network Sharing](img/2f70754f1fcb85a49c73c8bb40614ead.png)

然后，您的整个画板将在默认的 web 浏览器中打开，并且地址栏中的 URL 可以与任何人共享。本地共享的独特之处在于，您的整个画布将被划分为名称旁边的画板，URL 的接收者可以单击每个画板以全屏查看。

本质上，您正在共享整个文档，就像这样:

![Local sharing as the recipient](img/077745c3d13abaa18a2aa6ceea1ab8da.png)

### 二维码本地分享

QR 码是机器可读的黑白方块形式，通常用于存储 URL。任何拥有带摄像头的智能手机的人都可以从这些代码中提取一个网址，这为手机用户省去输入冗长网址的麻烦。

如果您不想强迫团队成员键入您的本地共享的 URL。草图文档，给他们发送一个二维码。通过使用名为 [QRCode 的草图扩展进行草图本地共享](https://github.com/dex1t/qrcode-for-sketch-local-sharing)，我们可以为我们的生成 QRCode。草图文档和团队成员只需扫描二维码即可读取网址。

由于没有快捷键来激活它，你必须从*插件→二维码本地共享→显示二维码*来访问它。之后截图( **command+shift+3** )，将二维码作为邮件/消息附件发给团队成员。

![Sending QR codes instead of URL’s](img/a002d2fb1d90c47e8d4966d78c5a5a09.png)

插件可以帮助扩展 Sketch 的功能，远远超出原有的功能，如果你以前没有用过的话[这里有一个快速设置教程](https://www.sitepoint.com/unleash-your-sketch-app-powers-with-7-fantastic-plugins/)。

## 与远程团队共享画板

Sketch 没有提供与远程团队共享画板的方式；对于这种功能，你肯定需要寻找插件，尽管以下两种方法对内部团队也非常有用。

### 松弛度

在过去的几年里，Slack 确实一直在增长，许多团队为了他们的核心沟通需求而从 HipChat 转向 Slack，理由是 Slack 提供了一个更令人兴奋的聊天界面，更好的客户支持和丰富的集成。

Sketch 用户可以从 [Sketch to Slack 插件](https://github.com/shahruz/Send-to-Slack)中受益，帮助 Sketch 用户轻松地将他们的画板直接分享到 Slack 团队对话中。

在我们设置之前，您需要一个来自 Slack 的 [API 令牌。滚动到网页底部，然后单击“生成测试令牌”开始请求。](https://api.slack.com/web)

![Generating an API token for Slack](img/589ed61fc20895dc11c01df76bd19ddc.png)

为 Slack 生成 API 令牌

现在，点击你想要连接的 Slack 团队旁边的“创建令牌”,按照说明进行操作，直到 Slack 给你一个 API 令牌。之后，安装 *Sketch to Slack 插件*如果你还没有，导航到*插件→ Sketch To Slack →更新 API 令牌*并使用 Slack 给你的令牌。

![Choosing a Slack team to connect to](img/be196c2a041da862d8edab15ec20bdf8.png)

设置完毕后，选择任意数量的画板，并导航回*插件→草图松弛*以选择您希望草图将画板发送到的位置。你可以选择一个频道、一个组、一个用户——甚至 Slackbot 本身(用于测试)。非常适合与大型团队共享设计！

### easy . cc

如果你在更小的团队中工作，那么[更容易。cc](http://easier.cc/) 可能更容易，真的。Easier.cc 获取你的画板，上传到他们的服务器，并创建一个自动过期的链接。在那之后，你可以自由地与任何人分享你的画板，但是，你喜欢；电子邮件，信使，松弛，无论什么。

当你想把你的团队对话和你的客户对话分开时，或者当客户没有时间加入你的松散团队时，Easier.cc 是理想的选择。

选择一个画板，使用键盘快捷键**command+option+e**；会出现一个小对话框，告诉你一个临时 URL 已经被复制到剪贴板。当然，如果您有多个画板要共享，这不是一个合适的方法，但如果您需要一个快速简单的解决方案，这肯定是方便的。

![Easier.cc takes your artboard](img/76599d6b45ae404facb6b669c3570139.png)

![Previewing the artboard in the browser](img/abe5f2da6c00c0e9a238ad7f246cbf7b.png)

## 原型工具

[InVision App](http://www.invisionapp.com/) 是一种协作/原型工具，有助于促进反馈、用户流和团队之间的交流。InVision 使用他们自己的 InVision Sync(和 Dropbox)来保持一切有序和同步，所以如果你走这条路，就不需要在 Sketch 上安装插件。

当然，InVision App 不是免费的，它也需要一些时间来了解这个网络应用的来龙去脉，因为它不仅仅是一个插件。

视觉并不是唯一的选择。以下是与 Sketch 集成的协作/原型工具列表:

*   [成帧器](https://github.com/bomberstudios/sketch-framer)
*   泽普林
*   [弗林特](https://www.flinto.com/mac_sketch_plugin/)
*   [漫威](https://marvelapp.com/prototype-with-sketch/)

如果你已经在使用 Sketch 和其中的一个工具，那么将两个工作流合并成一个是很容易的。

## 结论

Sketch 可以从类似于 Adobe Creative Cloud 的云服务中受益，但正如我们所知，Sketch 通常喜欢让事情简单得令人愉快，选择改善用户对其已有功能的体验，而不是发明新的功能。

也许*本地网络共享*功能会在未来发展以适应远程团队？

## 分享这篇文章