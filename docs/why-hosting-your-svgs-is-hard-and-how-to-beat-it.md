# 为什么 SVG 图像托管很难(…以及如何做)

> 原文：<https://www.sitepoint.com/why-hosting-your-svgs-is-hard-and-how-to-beat-it/>

最近一直在说 SVG。坦率地说，如果你是一名设计师、游戏制作人、UI 专家或动画师，有很多事情会让你兴奋不已。

![Game Characters designed an animated in SVG.](img/0040ab8fa91980f0df6c6b1a47a82ef2.png)

游戏角色设计了一个 SVG 动画。

工具变得越来越好。在设计工具的高端，Adobe Illustrator 在大约 2 年的时间里已经从令人尴尬的意大利面条式代码转变为真正令人印象深刻的 SVG 出口股份。

像 [Boxy SVG](https://boxy-svg.com) 这样的小规模工具以非常低的成本提供了优秀的替代品。

浏览器也运行良好。受众因网站而异，但在 2016 年，我们可以相信大多数用户能够看到我们的 SVG 正确呈现。

所以我的问题是:除了一些图标集和一两个标志——为什么我们没有看到 SVG 在网络上被更频繁地使用？

## 房间里的大象:SVG 图像托管

你如何把你的 SVG 放到人们可以看到的地方？

一开始听起来很蠢。你创造了这个很酷的矢量图形。你就不能像 JPG、GIF 或 PNG 那样上传吗？

是也不是。

默认情况下，大多数在线服务阻止上传 SVG 文件。试着上传一个 SVG 到 WordPress 安装程序，你会得到一个“`Illegal file type`”消息。我设法让我们的系统管理员改变我们的 SitePoint WordPress 安装，以允许 SVG 上传，但这可能不是每个人的选择。

同样，截至 2016 年年中，脸书、Twitter、Behance、Tumblr 和 Blogger 都阻止任何上传 SVG 的尝试。

那么，将您的 SVG 上传到大型图像托管服务怎么样呢？

没有骰子。不幸的是，你不会从 [Flickr](https://www.flickr.com) 、 [Photobucket](http://photobucket.com/) 、 [Imgur](https://imgur.com/) 、 [Google Photos](https://photos.google.com/) 或我能找到的任何公认的服务中得到爱。

如果你是一个系统管理员或者典型的程序员，这对你来说可能不是一个大问题。你可能可以在某个地方访问你控制的网络服务器*。*

但是如果你是一名设计师、动画师或插画师，这可能会是一个严重的阻碍。追溯到 2013 年的关于 StackExchange 的问题得到的回应大多是耸耸肩。在去年的 SitePoint 论坛上， [Ralph 提出了将 SVG 作为 dataURI](https://www.sitepoint.com/community/t/where-do-you-host-temporary-images-png-svg-for-use-in-a-codepen/190242/6) 嵌入的建议，这是一个很好的选择——特别是对于更小、更紧凑的图形。

但是对于地图、信息图和更大的图像来说，这可能有点笨拙。

## 为什么害怕 SVG 星球？

这个问题大概有两个部分的答案。

首先，虽然 SVG 并不是一个新的想法 W3C 规范是在 1998 年制定的——但是对 SVG 的支持还是有点新。这意味着构建一个支持 SVG 上传的服务直到最近都是在浪费时间。

其次，还有一些额外的安全… *查询*要处理。因为 SVG 是一种文本文件格式，所以在文件中嵌入实时 JavaScript 是 100%合法的。这使得 SVG 功能强大，但也容易被邪恶势力操纵。

目前，似乎没有人愿意承担承担这种风险的责任。许多应用程序——包括 WordPress、Tumblr、Behance 和 Blogger——会很乐意在你的页面中呈现一个 SVG。他们就是不允许你上传。这就像嵌入了一个 Youtube 视频。

那么，如何在线托管我的 SVG 呢？

虽然目前还没有简单的“拖放式”SVG 托管服务，但我已经找到了一个有用的权宜之计。

## Github 和 SVG:抓住要点

好吧，你们中的一半人可能会想，那又怎样？我每天都用 Github。如果你在这个阵营，跳到下面的“如何做”部分。

另一半可能会想什么？开发人员用的那种奇怪的，不可思议的“代码”东西？我绝不会用那个！”

当然，如果你不使用它，Github 看起来既混乱又无聊。然而，这无疑是我们在线托管 SVG 的最佳方式，我们只需要非常基本的理解就可以使用它。

Github 上的大多数活动都发生在代码库或“repos”中——包含项目中所有代码的版本控制的多用户库。这些可以容纳数千个文件和数百个用户。

但是 Github 也有一个东西叫‘Gists’。Gists 是为分享你喜欢的小代码样本而设计的。如果你创建了一个免费的 Github 帐户，你可以随时添加新的 gists 到你的帐户。它们是 100%免费的。

关于 gists 有趣的事情是，我们甚至不需要登录就可以创建一个。如果你[打开这个](https://gist.github.com/)页面，粘贴一些文本进去，给它一个文件名，然后点击`Add file`，你就会有一个可以公开访问的匿名文件的 URL。

当然，如果你从一开始就创建了一个免费的 GitHub 帐户，跟踪你的文件可能会更容易，但你不必*必须*去尝试它。

## 如何在 GitHub Gist 中托管您的 SVG

### 1).复制您的 SVG 代码

无论你是在 Illustrator、Boxy SVG、Sketch 或任何其他编辑器中创建 SVG，我们都需要直接访问代码。在您喜欢的代码编辑器(VS Code，Brackets，Atom，Sublime 等)中打开您的 SVG 文件，并将整个 SVG 文件复制到剪贴板。

### 2).创建一个新的要点

![Pasting your SVG code into a Github Gist](img/1025f6d5fa04233f3e5ea739a7ad677d.png)

接下来，[创建一个新的要点](https://gist.github.com/)，粘贴到您的 SVG 代码中，并给它一个文件名(即‘my-file . SVG’)。你会在右下角看到“创建秘密要点”按钮。我们需要每个人都能看到这个文件，所以将这个下拉菜单切换到“创建**公共**要点”，然后按下它。

![A  Gist containing your SVG code](img/0e63204805f3b7f308f7d340bbf42401.png)

保存后，您应该会看到类似上面的截图。点击右上角的“编辑”按钮，您可以随时再次编辑/更新/粘贴到文件中。

所以，我们现在有我们的文件在线，但我们不能只是'热链接'这个图像文件。我们需要得到正确的服务，否则它不会被视为一个图像。

### 3).获取原始网址

在上面框架的右侧，您会看到一个“ **Raw** ”按钮。点击该按钮，您将看到 SVG 源文件的原始转储。

我们所需要的就是你浏览器地址栏中的那个 URL 把它复制到剪贴板上。

### 4).通过 raw.githack.com 提供网址

2021 年 11 月更新:在我 2016 年的原始文章中，我们使用 rawgit.com 作为我们的主机 CDN，不幸的是，它已经停止接受新文件(尽管系统中已经存在的文件仍然可以得到服务)。令人高兴的是，raw.githack.com 几乎是罗吉特不可区分的替代品。

最后，转到~~[RawGit.com](https://rawgit.com/)~~<ins datetime="2021-11-14T22:41:37+00:00">raw.githack.com</ins>，将你的要点 URL 粘贴到提供的大文本框中。如果你做了所有正确的事情，这个框会立刻变成绿色，在下面你会看到两个新的 URL 供你使用——一个用于生产，一个用于开发。

![Githack in action](img/7fbbab4e3869e52241e30c6bdddb4293.png)

对我来说，你最好保持简单，只使用产品 URL。如果您将这些 URL 粘贴到浏览器地址栏中，您应该会看到 SVG 呈现。

这就是 T2 git hack 所做的全部工作。

这里有一个 GitHack 链接，链接到我在上面使用的 SVG 文件。

另一个隐藏的小奖励？您的映像现在也是“源代码管理”的。[点击你的要点页面](https://gist.github.com/alexmwalker/d898b8bc88a8aaa037a536efdb390964/revisions)上的“修订”链接，你会看到你的 SVG 作品的任何早期版本。

## 何必呢？

这条建议可能感觉有点枯燥、技术性和“代码化”，但它为聪明的设计师释放了许多可能性。例如:

*   您可以将新的 SVG 链接到一个更大的 Codepen 演示中。我一直用这种方式开发 HTML5 游戏图形。
*   您可以在 SVG 中创建一个旅行地图，并在旅行时实时更新它*——无需每次都导出并上传副本。*
**   您可以创建一个选举预测地图，并在新数据出现时更新 live 文档。*

 *SVG 确实为我们提供了更好的方法来做我们目前使用的巴布亚新几内亚和 JPG 的许多事情。

我希望 Gists 给你一个可靠的方法，让你在更多的地方释放创造力。

*原载于 [SitePoint 设计简讯](https://www.sitepoint.com/newsletter/)* 。* 

## *分享这篇文章*