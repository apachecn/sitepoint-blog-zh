# @伦敦媒体 2007–第二天总结

> 原文：<https://www.sitepoint.com/media-london-2007-day-2-wrapup/>

第二天以乔恩·希克斯的精彩演讲开始，演讲名为“如何成为一个有创造力的海绵”(T3)。乔恩在书中概述了他获取灵感的技巧，如何让 T4 真正开始一项设计。他解释说，成为“创意海绵”有三个步骤:

1.  收集
2.  目录
3.  创造

他从各个地方收集原材料:观察生活、书籍、杂志、互联网、t 恤、传单架、包装标签等。

为了编目，你可以使用各种不同的方法，但是你需要一个系统。你可以使用速写本、活页夹、盒装文件等。在你的电脑上，你可以使用 iPhoto 或者其他照片管理软件，比如 [yojimbo](http://www.barebones.com/products/yojimbo/) 。然而，真正的杀手级应用是[Flickr](http://flickr.com/)——标记、分组、与其他设计师分享。

然后，你可以将你的收藏作为灵感来源，实际创作东西:评论你的收藏，创建 [moodboards](http://www.stuffandnonsense.co.uk/archives/design_mood-boards.html) (与你的主题相关的收藏图像拼贴——你甚至可以让客户制作它们，并使用它们来讨论项目。它们很酷，因为它们制作速度很快)，图像中的样本颜色，专色设计图案等..

他最后说:“吸收一切，你永远不知道你什么时候会需要它。”

kon Wium Lie 是 CSS 背后的人，现在是 Opera 的首席技术官。他做了一个名为**“一个网络，酸 2，CSS3”**的演讲

他说了一些有趣的事情(包括他的观察，即在 Wii 等设备上安装浏览器可能会改变我们使用网络的方式——在休息室与他人分享我们的浏览体验)，但对我来说，最有趣的部分是他对 HTML 5 和 CSS3 的看法

HTML5 将包括**视频**元素，但目前还没有就格式达成一致，我们需要推动一种免费的、不受专利阻碍的格式: [Ogg Theora](http://www.theora.org/) 将是一个不错的选择。

然后，他继续查看 CSS3 中一些有趣的东西:

*媒体查询*允许您根据参数有选择地应用 CSS，例如:如果设备是手持设备且宽度小于 300 像素，则应用不同的样式表。

随着更宽的屏幕越来越流行，多栏布局(类似报纸的栏)将变得更加重要。您将能够定义列数、列宽和列间距。

*分页介质*–用于打印。哈肯向我们展示了最新版的[他的书](http://www.amazon.com/exec/obidos/tg/detail/-/0321193121/)，他用 HTML 编写了这本书，并用 CSS 生成的 PDF 格式发送给了打印机！

*网络字体*–下一件大事！你可以上传一种字体供浏览器下载和使用。Web 字体实际上是在 98 年在 CSS2 中引入的，但是没有对 OpenType 的支持(当然还有微软和 Netscape 的不同实现),它们从未起飞。还没有实现，但这应该很快就会改变。我们可以使用成千上万种免费的字体。

下午， [Dan Webb](http://www.danwebb.net/) 做了一个题为**“JavaScript Fu 的奥秘”**的演讲，这是一个关于 JavaScript 技巧和诀窍的大杂烩。

Dan 首先谈到了事件处理，以及使用脚本化事件处理程序的好处——使用脚本化事件处理程序几乎总是更好！脚本处理程序非常枯燥，很好地将 JavaScript 从 HTML 中分离出来。然而，大量的事件处理程序会阻塞浏览器。

最佳策略是默认使用基于脚本的处理程序。如果这导致页面没有响应，尝试使用[事件委托](http://icant.co.uk/sandbox/eventdelegation/)，然后，如果所有其他方法都失败，则进入内联

Dan 接着谈到了优化。

*   如果不需要，就不要包含大量的库文件，浏览器访问的 JavaScript 越少越好。
*   浏览器通常只能同时加载两个资源，所以将 JavaScript 文件组合在一起。
*   总是使用 Gzip 压缩而不是缩小——一旦使用了 Gzip，缩小并不会给你更多的节省。
*   确保一切都是可缓存的
*   小心使用 JavaScript 库选择器——使用时要非常具体
*   使用即时方法——只在需要的时候运行代码，不要提前做所有的工作。

最后，Dan 谈到了跨浏览器工作和测试:

*   为 Parallels 购买 Mac
*   获取其他浏览器上的 Firebug 和 Firebug Lite
*   使用[硒](http://www.openqa.org/selenium/)进行测试

这一天以有趣的“热门话题”小组结束，由 Jeremy Keith 和小组成员 Richard Ishida、Joe Clark、Dan Cederholm 和 Drew McLlellan 介绍。他们回答了与会者提出的各种问题(包括有争议的“为什么小组中没有女性？”).

这一天以乔·克拉克从网页无障碍领域退休的惊人消息而结束——他将专注于他的字幕工作。现场响起了热烈的掌声，感谢 Joe 在这一领域所做的努力——我认为，如果没有 Joe，web accessibility 这个话题就失去了一个冠军，像@media 这样的会议没有他会变得更加无趣。

总的来说，我认为@media 是今年一次非常成功的会议。与会者人数之多令人震惊，代表们来自欧洲各地。演示的质量非常高——通常在网络会议上，我倾向于发现一些演示并不真正有用或有趣(更多的是“填充”)；这里的情况不是这样。然而，会场缺乏 wi-fi 令人失望——这需要在明年解决。

## 分享这篇文章