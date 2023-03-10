# 保持你的字体堆不倒

> 原文：<https://www.sitepoint.com/keep-your-font-stacks-from-falling-over/>

对于大多数网页设计者和开发者来说，测试是工作中很大的一部分。他们将投入大量的时间来确保他们的网站在各种浏览器中看起来相似，如果不是完全相同的话。然而，站点测试的一个关键部分似乎经常被遗忘:字体堆栈测试。

**问题**

大多数网站设计者在很大程度上依赖一套免费的、广泛分发的字体作为他们网站的大部分文本内容。然而，不管一种字体有多普遍，它仍然有可能会从某个访问者的系统中消失。幸运的是，CSS 允许我们指定一个字体堆栈，以便在首选字体丢失的情况下使用。通过以这种方式指定三种或四种字体，然后是一个通用的 catch-all `serif`或`sans-serif`，我们确保我们的内容以至少类似于我们想要的字体显示。理论上这很好，但是如果网站的外观没有用每种潜在的备用字体进行测试，就会出现问题。例如，在我的家用机器(运行 Ubuntu Linux)上查看 PharmQD 网站时，我看到以下内容:

![PharmQD website, broken due to a missing font](img/4053217b92a526f4a5064b952841cadb.png "PharmQD website, broken due to a missing font")

正如你所看到的，标题会换行，这显然是设计者在设计布局时没有预料到的。为什么？因为这些标题使用的字体堆栈是`Tahoma, Verdana, Arial, Helvetica, sans-serif;`，而 Tahoma 明显比 Verdana 或 Arial 窄(同样字体大小和粗细)。因此，当在没有 Tahoma 的系统上查看时，使用回退字体，因为所有回退字体都更宽，所以文本占用的空间比设计中预期的要多。下面是这个设计本来的样子，这一次是在我运行 OS X 的工作机器上看到的:

![The correct appearance of the PharmQD website](img/f644334d520312cda12892a3396c46a0.png "The correct appearance of the PharmQD website")

宋体并不是唯一易受这种故障影响的字体。微软的 Vista 字体(Calibri、Cambria、Candara 和 Constantia)在相同的字体大小下比大多数其他字体小得多，因此在堆栈中使用这些字体中的任何一种都会导致类似的问题。
 **测试工具**

奇怪的是，尽管 web 开发人员可以使用大量的测试工具，但是测试字体堆栈却没有简单的答案。一个不太理想的解决方案是使用 Firebug 手动编辑与高度依赖字体大小的页面部分相关联的`font-family`声明:按钮、导航项、标题等等。

快速的谷歌搜索也显示了 Github 上这个看起来很有希望的项目:[字体堆栈测试器](http://github.com/leeky/Font-Stack-Tester)，由 Robert Lee-Cann 开发。这是一个 jQuery 脚本，它在页面顶部添加了一个覆盖层，上面有按钮可以禁用页面上的任何字体。开发者希望最终把它变成一个 bookmarklet，但目前它肯定还处于起步阶段。

***更新:**在我最初发布这篇文章的几天后，一个有进取心的人注册了[http://fontstacktester.com/](http://fontstacktester.com/)，并发布了 GitHub 项目的托管版本。只需输入你的网站网址，它会带你去那里，在顶部添加一个栏，你可以用它来逐个禁用字体，以测试你的堆栈是否支持。这是对克里斯开发速度的称赞！*

不管你怎么做，字体堆栈测试绝对不应该被忽视:当一些东西像上面的例子一样坏的时候，它真的会损害访问者对你的站点的印象。

## 分享这篇文章