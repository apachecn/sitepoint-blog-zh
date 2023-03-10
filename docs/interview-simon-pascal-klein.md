# 与西蒙·帕斯卡尔·克莱因的打字对话

> 原文：<https://www.sitepoint.com/interview-simon-pascal-klein/>

又到了一年中的这个时候 web 开发人员的日记被会议、夏令营和事件塞满的时候。在世界边缘的珀斯，网络会议的[边缘](http://www.edgeoftheweb.org.au/)是为期三天的演示、研讨会和与一些网络领军人物的交流机会。我采访了其中一位演讲者，西蒙·帕斯卡·克莱因，和他聊了聊他的演讲，网络上的字体状况，以及一些启发他的作品。

Edge of the Web is just a few weeks away now. What will we take home from your talk?

我希望做一些事情:我希望人们对网页排版充满热情——我们需要克服一些弥漫在空气中的绝望情绪，那就是基于标准的网页排版是有限的，无聊的，而且非常糟糕。

我打算通过介绍一些可用于操作文本的标准化的、得到良好支持的 CSS 选项，来说明印刷术不仅仅是“哦，是的，我正在选择一种很酷的字体”。我还简要介绍了网络字体领域的最新发展，这些发展扩展了我们在网络上可以使用的字体数量。

We’re crying out for more available fonts on the Web. We have wider support for `@font-face` but sometimes it’s no option, given that the fonts we’d like to use are usually licensed in a manner that prevents us from including them that way. What gives?

老实说，我无法预见字体铸造厂会以一种可以轻松共享或下载字体的格式发布他们在网络上使用的商业资产。Typekit 和其他类似的 web 字体托管和许可服务采用了一系列隐藏字体数据的方法，包括拼接字体，以便它在浏览器中只是 *拼凑* 本身，以不同的格式或编码设置字体数据，用 JavaScript 隐藏字体数据，等等。这是为了防止精明的网络用户查看网站 CSS 并访问`@font-face`网络字体 URL 来直接下载链接字体以便在网站之外使用。

正是这种版权保护技术最近催生了网络字体，因为铸造厂以前没有办法在不冒被下载和共享的风险的情况下在网络上提供他们的字体。

我猜想，支持这些服务的另一个简单答案是，由创建了自己的网络字体服务或通过类似 Typekit 的系统对其进行分许可的 type foundries 提供的范围，可能会比你看到的一些非专业字体更好。当然，某种东西是否比*更好的想法是通过与其他东西进行比较来判断的——在这种情况下，这通常是印刷世界。*

 *在印刷中，定义高质量字样的属性包括整个字符集的良好字距调整、替代或额外的样式和字形、一系列连字等等。在 web 上，这些属性中的许多不太重要，或者不可用，例如，在设置 Web 类型时，高级 OpenType 功能不可用。这将专业的、打印优化的字体置于与 *非专业的* 字体更加平等的地位。* 

 *Since we have all these options available to us, it’s fair to say we’ll see an explosion of font use around the Web. How do we avoid being one of those who’s just “pickin’ a cool font?”

我建议在设置网页字体时要谨慎，不管它是来自一个铸造厂的授权网页字体还是你在一个字体共享网站上看到的免费授权字体。这些字体的一个很好的用例是传统的大字体——所以想想英雄文本、标题、标注等等。

Everyone loves inspiration. Whose work delights you?

ilovetypography.com 的约翰·博德利是唯一一个真正值得称赞的人，他让排版，尤其是网页排版，重新成为人们关注的焦点。那个人提醒我们许多人打字需要注意，值得表扬。

以下是我最喜欢的一些:

1.  [寿司&机器人，](http://sushiandrobots.com)纪娜波顿的个人网站。我喜欢她对网格系统的使用，并且对她将一切都应用到一个公共基线上印象深刻，这甚至是图形化可见的。使用好的网格系统和基线可以提供良好的节奏感。

2.  [Black Estate，](http://blackestate.co.nz/)位于新西兰的葡萄园 Black Estate。我喜欢用如此简单的东西创造出如此美丽的东西:一个好的、多功能的网格和简单的字体选择(正文用 Times New Roman 字体，标题用更大的 Georgia 字体——两种都是网页安全字体)。

3.  [信息架构师。该网站简单、干净……最重要的是结构清晰、易于阅读。同样，场景主要设置在佐治亚州，这是为屏幕使用而设计和优化的，非常适合，有助于获得良好的阅读体验。](http://informationarchitects.jp/)

4.  [陳陈乔恩，](http://jontangerine.com/)陈乔恩的个人网站。宽度以 ems 设定，页面缩放完美——更不用说他使用的各种字体风格(粗体、斜体、小型大写字母)和坚固的网格。浏览乔恩的网站绝对是一种享受。

5.  [减法，](http://subtraction.com/)会宁遗址。Khoi 的网格是惊人的，并说明了一个单一的，简单的字体选择(Arial)和一个辉煌的网格，你可以建立一个美丽的，结构化的网站，令人想起瑞士风格。他的网站的 7.x 版本也已经发布了一段时间，但仍然值得一提，提醒人们认识到网格系统确实可以从印刷世界转换到网络世界。*  *谢谢帕斯卡。

如果你正在访问网络边缘，你会在 11 月 5 日听到[帕斯卡的演讲、*美丽的网络排版、*](http://www.edgeoftheweb.org.au/program/simon-pascal-klein/) 。当你在做的时候，一定要去[看看 SitePoint 的凯文·杨克在同一天早上做的关于 CSS 框架的演讲——它在上周的 Web Directions South conference 上受到了好评。](http://www.edgeoftheweb.org.au/program/kevin-yank/)* 

## *分享这篇文章*