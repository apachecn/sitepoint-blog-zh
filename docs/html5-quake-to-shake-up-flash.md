# HTML5 震动了 Flash

> 原文：<https://www.sitepoint.com/html5-quake-to-shake-up-flash/>

随着围绕苹果新的有意识的无 Flash 消费品(你知道的那种)的到来，有一场新的讨论围绕着那个古老的栗子——Flash 与 HTML 的相对优点。

虽然很多讨论已经无聊地两极化(闪光是上帝/闪光杀死小海豹和/或小猫)，但明智的中间观点是这样的:

> Flash 经常被用来使简单、有用的东西变得不那么简单和不那么有用。然而，当涉及到游戏和应用程序中常见的更复杂的图形和交互时，HTML-5 或其他-有其固有的限制。闪光在那里有它的位置。

这当然是我的看法，但是谷歌人雷·克伦威尔、斯蒂芬·豪斯坦和乔尔·韦伯刚刚发布了一个令人瞠目结舌的“20%项目”，让你质疑 HTML5 *是否有天然的局限性。*

![Quake 2 in the browser](img/bd85f62fadff450965ab8d52cfedf5bd.png)

令人难以置信的是，他们主要使用谷歌网络工具包(GWT)，将 id 软件备受尊崇的雷神之锤 2 ( [Jake2](https://bytonic.de/html/jake2.html) )的 Java 端口转换为在 Chrome 和 Safari 中本机运行——没有插件、扩展或第三方技巧。

如果 GWT 对你来说毫无意义，这是谷歌的开源框架，允许他们用 Java 开发 Wave、Maps 和 GMail，但用 HTML、JavaScript 和 CSS 部署在你的浏览器中。

GWTQuake 项目使用:

*   作为基础的 Canvas API
*   声音的 HTML5 音频元素
*   HTML5 的本地存储，用于保存游戏和分数
*   HTML5 [网络套接字](https://dev.w3.org/html5/websockets/)
*   [web GL](https://en.wikipedia.org/wiki/WebGL)——JavaScript 3D 图形处理引擎

现在我不得不承认，随着 4 月 1 日的出版日期，我确实怀疑这是否只是一个精心制作的极客笑话。显然不是。你需要一些技术手段来让它嗡嗡作响，但这不是恶作剧。

目前，GWT 还不能在 Firefox 上工作，但听起来这主要是因为 Firefox 相对较慢的 JavaScript 引擎，而不是因为它本身缺乏功能支持。目前还没有关于 GWT 地震是否在 Opera 上运行的消息，在我们至少得到一个合适的 IE9 版本之前，IE 是一个不值得开始的话题。

显然，这是一个相当原始、令人讨厌的概念验证型项目，但它确实改变了我对 HTML5 的看法。

放马过来。

## 分享这篇文章