# 与路易斯和雷切尔谈论 CSS:抄本和资源

> 原文：<https://www.sitepoint.com/talk-css-with-louis-and-rachel-the-transcript/>

今天早上与专家的谈话让我比预期的更兴奋，但这是有充分理由的。不仅我们的专家是 SitePoint 的长期作者和 CSS 明星 Louis Lazaris 和 Rachel Andrew，而且这次会议标志着我们全新的实时代码窗口的推出。新的界面允许专家和其他指定的参与者协作编码，以便演示示例和解决问题。

时间飞逝，我们讨论了一系列话题，从预处理器到响应式解决方案。它提供了大量的参考资料，为了让你不去仔细阅读整个文稿，我把它们抽出来，列在下面供你阅读。

**来自路易和瑞秋的一些 SitePoint CSS 出版物:**
[跳转开始 CSS](https://learnable.com/books/jscss1 "Jump Start CSS") [CSS3 选集](https://learnable.com/books/the-css3-anthology-4th-edition "The CSS3 Anthology") [你所知道的关于 CSS 的一切都是错的](https://learnable.com/books/everything-you-know-about-css-is-wrong "Everything you know about CSS is wrong") [HTML5 和 CSS3 对于现实世界来说](https://learnable.com/books/html5-css3-for-the-real-world "HTML5 CSS3")

**一些有用的工具:**
[Dust Me 选择器](https://addons.mozilla.org/en-us/firefox/addon/dust-me-selectors/ "Dust Me Selectors")(用于整理你的 CSS)
[CSS3 派](http://css3pie.com/ "CSS3 Pie")(使 IE 6-9 能够渲染几个最有用的 CSS3 装饰特性)
[现代 IE](http://www.modern.ie/en-us "modern.ie")(IE 开发的工具)
[Web 工具周刊](http://webtoolsweekly.com/ "Web Tools Weekly")(路易周刊快讯)

**萨斯 vs 少主辩论:**
[萨斯 vs 少主](http://css-tricks.com/sass-vs-less/ "Sass vs LESS") [预处理器枪战](http://net.tutsplus.com/tutorials/html-css-techniques/sass-vs-less-vs-stylus-a-preprocessor-shootout/ "Preprocessor shootout")

**网格和布局:**
[学习 CSS 布局](http://learnlayout.com/ "Learn CSS Layout")
[CSS3 网格布局](https://www.w3.org/TR/css3-grid-layout/ "CSS3 Grid Layout")
[CSS3 网格和区域](https://speakerdeck.com/rachelandrew/css3-grids-and-regions "CSS3 Grids and Regions") [CSS 招数:网格](http://css-tricks.com/almanac/properties/g/grid/ "Grid") [居中多个 div](http://www.impressivewebs.com/center-multiple-divs/ "Centre Multiple Divs")

**响应式解决方案:**
[选择响应式图像解决方案](http://mobile.smashingmagazine.com/2013/07/08/choosing-a-responsive-image-solution/ "Responsive Image Solution")
[响应式菜单概念](http://css-tricks.com/responsive-menu-concepts/ "Responsive Menu Concepts")
[响应式模式](http://bradfrost.github.io/this-is-responsive/patterns.html "Responsive Patterns")
[响应式导航](http://bradfrostweb.com/blog/web/responsive-nav-patterns/ "Responsive Navigation")

**其他资源:**
[CSS 日演示文稿](https://vimeo.com/channels/cssday "CSS Day")
[停止解决你还没有解决的问题](http://www.rachelandrew.co.uk/archives/2012/03/21/stop-solving-problems-you-dont-yet-have/ "Stop Solving Problems")

您可以注册接收未来会议的电子邮件通知，并查看我们即将推出的时间表[这里](https://experts.learnable.com/ "Talk with the Experts")。
[下周是 Rails 4](https://experts.learnable.com/ "Rails 4") 安迪·霍桑和格伦·古德里奇。

如果你想知道今天早上到底发生了什么，这是完整的文字记录:

【19:27】

【19:28】<鹰派>显摆！

【19:28】<鹰派>我打开了三个浏览器，一共 22 个标签页。我觉得那很好

【19:29】前，所以我的异常高

【19:29】<杰瑞>46——在三个窗口之一

【19:29】<鹰派>听起来不错的通迅

【19:29】<HugoGiraudel>6 此处。

【19:30】<霍克>好，我宣布开球

【19:30】<霍克>欢迎大家——感谢参加本次会议

【19:30】<霍克>特别欢迎并感谢 LouisLazaris 和 rachelandrew——他们都是 SitePoint 的长期作者

【19:31】<rachelandruw>大家好:)

【19:31】<主战派>他们最新的合作是跳跃式启动 CSS[https://learnable.com/books/jscss1](https://learnable.com/books/jscss1)

【19:31】

【19:31】

【19:32】<鹰派>今天晚些时候我会在 SitePoint 上发布一份完整的会议记录

>【211】杰瑞

【19:32】<twigsta>好。我讨厌 irc 上的人工添加剂

【19:32】<路易萨里斯>杰瑞胜

【19:32】<霍克>神圣的莫利杰瑞——这太荒谬了！

【19:32】<詹姆士>嗨霍克！大家好。只知道传统的 CSS，接下来该学什么？SASS 或更少？

【19:33】<霍克>我很自豪地说，本次会议是我们新的实时代码窗口的发布会，您将在本次聊天的右侧看到

【19:33】<HugoGiraudel>好看。:)

【19:33】<TaoistTotty>我更喜欢这个造型

【19:33】<杰瑞>还有聊天滚动条！！！！！！谢谢！！

【19:33】

【19:33】<LouisLazaris>@ James 总的来说，我觉得 Sass 才是正道。它变成了预处理程序的 jQuery(很多人都在谈论它，为它写东西，等等)

【19:34】<鹰派>很高兴你们喜欢它的家伙们

我有一个关于垂直节奏的问题，可以吗？

<>有*

【19:34】<鹰派>嗯，什么问题都可以

【19:34】<LJ>我第二个 sass or less 的问题……哪个是未来？

【19:35】<rachelandrew>@ James 你也不用*学。然而，Sass 似乎有更多的人使用它，因此有更多的工具和例子。

【19:35】<Hugo giraudel>* caugh * Sass * caugh *

【19:35】<RCT Neil>我今天开始研究使用指南针垂直节奏模块，真的很喜欢。完美的，一旦我得到了它的工作与雷姆的和 px 的后备！但是，当客户通过 CMS 使用图像时，您如何保持垂直节奏呢？

【19:36】<TaoistTotty>整理你的 CSS，或者更好的规划它的最好方法是什么？–我经常发现我有不必要的课程等。当我检查代码时(可以做得更好的事情)

【19:36】<concept fusion>我喜欢 SaSS(目前在一个 rails 团队工作),但我总是担心以 Rails 为中心的方面…另一方面，LeSS 的客户端处理也让我担心…还没有决定我更喜欢哪个

有没有工具可以用来检查特异性？

【19:37】<rctneil>Sass 太棒了，我最喜欢它的缩进语法！

【19:37】<LJ>你为什么更喜欢少？

【19:37】<杰瑞>什么是垂直节奏？

【19:38】<rctneil>Sass 还有拥有指南针框架的好处

【19:38】<LouisLazaris>TaoistTotty 有个东西叫 Dust Me 选择器:[https://addons . Mozilla . org/en-us/Firefox/addon/Dust-Me-Selectors/](https://addons.mozilla.org/en-us/firefox/addon/dust-me-selectors/)(以及类似的工具)

【19:38】<特维斯塔>排版杰瑞

【19:38】<RCT Neil>ljI haven；t 用的少！:-)

【19:38】<LJ>真正的节奏是两列在横轴上同步

【19:38】<rctneil>但是总的来说 Sass 的语法对我来说似乎更合理

<LouisLazaris>TaoistTotty 但是要小心，因为我相信它是逐页检查的，所以如果某个页面上的东西没有被使用，并不意味着它没有在项目中使用(但是我已经看了一段时间了，但是现在可以更新)

【19:39】<詹姆斯>至此，萨斯> 5，LESS = 0

【19:39】<鹰派> LouisLazaris 和 TaoistTotty——不，我不相信我们已经有 1000 多年没更新了

【19:39】<LouisLazaris>哦是尘我一个 SitePoint 的东西？没想到…

【19:39】<佐伊>如果你给 DustMeSelectors 一个 XML 网站地图，它会扫描所有的页面。

【19:39】<鹰派>正是:)

【19:40】<chinebro>我好像总听说，顶嘴比少顶嘴厉害。

【19:40】<LouisLazaris>@佐伊很高兴知道

【19:40】

【19:40】<RCT Neil>有人能帮忙解答我的 VR 问题吗？

【19:40】<HugoGiraudel>Sass 也有更健壮的语法。LESS 的主要问题是他们选择使用@符号作为变量，这与常规 CSS 中的@指令相冲突。由于这个问题，在 LESS 中有很多你不能(或几乎不能)做的事情在任何其他 CSS 预处理器中是绝对微不足道的。

【19:40】

【19:41】<concept fusion>Compass 相当酷…特别是对于跨浏览器 CSS 简化

老实说，我和少少没什么关系。SASS 虽然我是一个大风扇

【19:41】<RCT Neil>甚至，如果没有 CMS，你如何处理图像以保持节奏？

【19:41】<RCT Neil>特别是在做那件事的时候，让它保持友好

【19:41】<LJ>能解释一下派吗？

<TaoistTotty>谢谢 LouisLazaries 我会看看那个

【19:42】<LouisLazaris>rctneil CMS 的永远不会完美。只要用户能做东西，他们就会找到打破东西的方法。也就是说，你可以使用像 max-width 和 max-height 这样的东西，甚至可以使用自动换行:break-word；防止东西摔坏，但不能保证。

【19:42】<杰瑞>LJ–3.14159 还是樱桃？

【19:42】<LouisLazaris>@ LJ 你说的“派”是什么意思？

【19:43】<RCT Neil>LouisLazaris 还好感谢

【19:43】<LJ>杰瑞都不是..对 pie 的支持，与 clearfix 有关

【19:43】<>[【http://css3pie.com/】](http://css3pie.com/)

【19:43】<LJ>实际上我对什么时候使用清除修复很困惑。vs 清除两者

<LouisLazaris>对于萨斯 vs. LESS 的辩论，这两篇帖子是必读的:[http://css-tricks.com/sass-vs-less/](http://css-tricks.com/sass-vs-less/)和[http://net . tuts plus . com/tutorials/html-CSS-techniques/Sass-vs-LESS-vs-stylus-a-preprocessor-shootout/](http://net.tutsplus.com/tutorials/html-css-techniques/sass-vs-less-vs-stylus-a-preprocessor-shootout/)

【19:44】<LJ>感谢雨果 G

【19:44】<HugoGiraudel>Clearfix =父方。清除双方=兄弟方。

【19:44】<LJ>再次感谢

<rctneil>LJ 当一个元素的所有子元素都浮动时，使用 clearfix，然后父元素通常会折叠，所以你使用 clear fix 下拉父元素以包含所有浮动的子元素，clear 属性用于使一个浮动的项目位于另一个项目的下方，而不是并排——抱歉解释得不好

【19:45】<LJ>但是我有一个身体在 G dev 工具里看不到底

我们失去瑞秋了吗？:(

【19:45】<the bwiz>RCT Neil–解释得好！:)

【19:45】<the Dutch coder>@ LJ:那也可能是因为绝对/固定定位的元素(假设你的 HTML 是正确的)

【19:45】<LJ>我身材 100%高

【19:45】<特维斯塔>问:萨斯好像有点喜欢敏捷。为了让它发挥作用，每个人都需要同意这个想法。许多大公司有多个团队在同一个代码库上工作。有没有减轻困惑的方法？

这个网站非常擅长解释这一切:

【19:46】<LJ>我想我没有指定任何固定或绝对的

【19:47】<the bwiz>@ LJ——可能会出问题——试试 min-height:100%；高度:1px

【19:47】<LouisLazaris>我在代码窗格贴了一个 CSS 示例:)

【19:47】<rctneil>LJ 如果你试图获得与背景高度相等的列，那么这是一个棘手的问题，背景、最小值和最大值都是可以使用的方法

<The Dutch coder>@ LJ 那么一定有什么东西在它的容器外面浮动…如果适当地清除固定，主体应该包装所有东西…或者，如果你不支持 IE7 和更低版本，你可能想考虑使用内联块。尤其是使用内嵌块的网格系统比 floats 更好。

【19:48】<LouisLazaris>twigsta 研究使用源地图，这基本上允许查看 Sass 的人看到 CSS 来自原始 CSS 文件中的哪一行

<concept fusion>我对 SaSS 的部分{潜在}问题是，除非你正在进行一个 Rails 项目，最好启用集成资产管道并进行动态编译，否则只需要一个团队成员就可以对编译后的 CSS 进行工作，从而打破整个流程…像 CodeKit 这样的编译器对个人来说是很棒的，但不是一个可靠的工作流

【19:48】<团队概念融合>

【19:48】<LJ>在 safari 和 FF 中一切看起来都很好，但在 Google Dev Tools 中，它显示我的身体只能达到这么低。

【19:48】<LJ>我在尝试隔离…即不漂浮

【19:49】

【19:50】<HugoGiraudel>这不触犯 CSS。它破坏了工作流程。

【19:50】<HugoGiraudel>高度不同的东西。

【19:50】>拉尔夫姆【LouisLazaris】尼斯颂样。:-)

【19:50】<HugoGiraudel>工作流(Workflow)是一个在单独做一个项目时几乎不存在的词。

【19:50】<concept fusion>与其说他们能打破 css，不如说他们是在往黑洞里写……即。他们做出改变，然后这些改变被改写…

【19:51】<the Dutch coder>rachelandrew 你是什么时候决定开发 Perch 的，在开发过程中你学到的最大的一点是什么？

【19:51】<twigsta>LouisLazaris 谢谢，我会的！

【19:51】<詹姆士>谁的前途更光明？SASS 或更少？

【19:52】<概念融合>萨斯目前似乎更有吸引力……

【19:52】* LouisLazaris(AE 599 FCC @ localhost)已加入#sitepoint

【19:53】<HugoGiraudel>参与 Sass 的开发者多了，参与的就少了。

<>比*

【19:53】

<rachelandruw>@ TheDutchCoder 我被邀请到这里来谈论 CSS，最好不要离题——尽管总是很乐意谈论其他地方的栖木；)

<the Dutch coder>哎呀我的糟了！:D

<霍克>我有点忘了我们现在在哪里。让我们让事情回到正轨。有人有尚未回答的问题吗？

【19:54】<RCT Neil>我最喜欢的预处理器是 mixins，非常有价值

【19:54】<LJ>Concept me too 和 Sass 很容易学，你也可以在你和 SCSS 做不到的任何地方插入普通的 CSS

【19:54】<space beers>rachelandruw——你对等高元素的顶级解决方案是什么？

【19:54】<the Dutch coder>CSS 问题然后:你最近一次在 CSS 中学习新东西时“惊叹”的时刻是什么？

<rctneil>我告诉那些刚刚开始学习 web 的人，他们说他们想学习 Sass，主要的事情是先学习普通的 CSS，并知道预期的输出是什么样子

<>RCT Neil 同意

【19:55】<the bwiz>当我试图拖动一个图像的容器时，如何阻止它被拖出页面？

【19:56】<the Dutch coder>RCT Neil 类似 jQuery<->JavaScript IMO；——)

【19:56】<LJ>肯定你得很了解 css 才能进入 sass

<rachelandraw>我最惊叹的时刻是我一直在做的 CSS 网格布局的工作，我认为这是拥有一个合适的布局方法的最佳解决方案，目前处于早期阶段，但值得一看

【19:56】<LouisLazaris>the webwiz 你说的“拽”是什么意思？它可以调整大小？

【19:56】<概念融合>非常真实

【19:56】<HugoGiraudel>rachelandrew LouisLazaris 你可能看过我写的关于萨斯的文章。我是一个无条件的 Sass 黑客，我喜欢它。你是否觉得一些预处理程序的特性跨越了 CSS 应该做什么和不应该做什么的界限？你对这个话题持什么立场？

<RCT Neil>the Dutch coder 今天，当我第一次看垂直节奏时，它很棒，漂亮的排版和 Sass 和指南针是无价的！

【19:56】<the duthcoder>rachelandruw nice，你想要什么样的系统？它是否基于内联数据块且响应迅速？

【19:57】<LJ>你能再谈谈 css 的网格布局吗？

【19:57】<space beers>CSS 网格中相等的高度是我持续痛苦的来源。谁有最好的解决方案？

【19:57】<LJ>指南针网站和文档过时混乱

【19:58】<LouisLazaris>HugoGiraudel 我认为 Sass 是 CSS 应该的方式，也应该一直是这样。它的编程能力更加强大。当它只是普通的 CSS 时，就太简单了，太违背基本的软件工程原理了(干等)

<rachelandruw>@ theduthcoder 我不明白，我说的是新的 CSS 网格布局模块[https://www.w3.org/TR/css3-grid-layout/](https://www.w3.org/TR/css3-grid-layout/)而不是任何网格系统

【19:58】<the bwiz>LouisLazaris——用 JS 拖动图像库——在 DIV 上工作，但是图像自己移动！！

【19:59】<HugoGiraudel>the Dutch coder 目前仅在 IE10+中支持。Chrome Canary 将很快加入这一行列。

这只是一个可悲的现实，但至少有一种方法。

【19:59】<the bwiz>space beers–显示:表格-行/列作品 IE 8+

【19:59】<LJ>网格布局多快可以使用？

【19:59】<the Dutch coder>啊，好吧，我现在明白了，还以为你在做一个框架；)

【19:59】<太空啤酒>rachelandruw——看起来很有趣。

【19:59】

【19:59】<LouisLazaris>space beers 是的，thewebwiz 说的。这也是一种选择

【20:00】<鹰派>欢迎刚刚加入的人。请随意提问——我正在为瑞秋和路易排队

【20:00】<LouisLazaris>the ebwiz——还是那句话，我不知道你说的“拖”是什么意思？

【20:00】<RCT Neil>@ LJ 试试 beta.compass-style.com 更多最新消息。不知道 Chris Eppstein 什么时候才能最终发布 0.13？

【20:00】但是渐变可能会有帮助(SVG？)

【20:00】<太空啤酒>the ebwiz——是的，我喜欢那个。我自己一直喜欢负保证金的把戏

【20:01】<rachelandraw>网格布局在 IE10 中是存在的(但是是旧的实现)但是使用很有趣

你对《罗盘》的发布时间表有什么想法吗？

【20:01】

【20:01】<concept fusion>"目前仅在 IE10+中支持。Chrome Canary 很快也会加入进来。”谁想到我们会读到这样的声明！lol

【20:01】

【20:01】<HugoGiraudel>CSS-Tricks 在网格布局上有一个历书条目:[http://css-tricks.com/almanac/properties/g/grid/](http://css-tricks.com/almanac/properties/g/grid/)

老实说，我一直在回避网格这种东西，因为它太新了，而且几乎没有浏览器支持。我认为 flexbox 值得研究，但同样，由于跨浏览器的问题，它并不实用。

【20:01】<LJ>rachelandrews 谢谢！！还有 rctneil

【20:02】<RCT Neil>rachelandrew 够公平了，谢谢！

【20:02】

【20:02】<HugoGiraudel>和 IE11 真的很贴近生活。

【20:03】<LJ>我试图在默认隔离模式下使用 Singularitygs，但我的 css 出现了浮动。

【20:03】<LouisLazaris>对于还没看过这个的人来说，最近 CSS 日的视频都在网上:[https://vimeo.com/channels/cssday](https://vimeo.com/channels/cssday)

【20:03】<concept fusion>是否让 IE 6、7、8、9 奇迹般消失？；——)

【20:03】<HugoGiraudel>肯定不是。

【20:03】<HugoGiraudel>但是我们马上就要谈到了。

【20:04】<LouisLazaris>有人知道 IE10 是不是要自动更新到 IE11？

如果你不登顶，他们会被观看吗？

【20:04】<LouisLazaris>@ LJ 是的，当然！

太棒了，我对它非常感兴趣。

【20:04】<rachelandruw>在 [http://www.modern.ie](http://www.modern.ie) 有 IE11 虚拟机现已上市

【20:04】<HugoGiraudel>LouisLazaris 据我所知，没有

【20:04】<HugoGiraudel>LouisLazaris 至少在桌面上。

【20:05】<Hugo giaudel>LouisLazaris 但是我可能错了。

【20:05】

【20:05】<LouisLazaris>HugoGiraudel 那是可悲的，真的，如果那是真的。

【20:05】<HugoGiraudel>好消息是我们将不得不寻找新的黑客！:D

【20:05】<解码器>我认为微软浏览器的生命周期与其支持的操作系统有关

【20:06】<the bwiz>LouisLazaris——通过拖动，我的意思是转到任何带有 FF 或 Chrome 图片的页面，用鼠标拖动图片。它会复制到你放它的任何地方。如何阻止这种情况？

【20:06】<LJ>测试 css 跨浏览器除了 browserstack 还有别的方法吗，测试一个本地文件？

【20:06】<詹姆斯>LJ[http://www . modern . ie](http://www.modern.ie)

【20:06】<rachelandruw>@ LJ 使用虚拟机

【20:06】<HugoGiraudel>据我所知没有比 Browserstack 更好的了。

【20:06】<the bwiz>concept fusion——我想你指的是 IE 8 吧！

【20:07】<概念融合>浏览器虚拟机是我发现的唯一可靠的测试方式…无论是本地还是远程…

【20:07】<LouisLazaris>the ebwiz Ummm，这是无法阻止的。但它不会真的复制到任何地方。我不认为这种事情会像你描述的那样发生…？

【20:07】<LJ>复杂到在 Browserstack 中查看本地文件

<概念融合>我的错……更糟！lol (SVG 噩梦)

【20:08】<LouisLazaris>The webwiz——唯一可能发生的情况是将它拖入一个< iframe >中，但我甚至不确定这一点

【20:08】<the bwiz>LouisLazaris——这样做的话，我的桌面上会有很多不想要的图像！:)

【20:08】<拉尔夫姆>我最近在读 IE 将会自动更新。但不知道它是如何工作的。

【20:08】<HugoGiraudel>rachelandruw 在 modern.io 上找不到 IE11 VM .:(

【20:09】我以为你的意思是，拖到页面的另一部分。

【20:09】<LouisLazaris>the bwiz 你为什么要停下来？

>【HugoGiraudel】rachelandrew 找到了！:)

【20:09】<rachelandraw>至少在 Mac 和 VirtyualBox 上肯定有——你需要选择你的测试环境和虚拟化平台才能看到

【20:09】<the bwiz>LouisLazaris——因为我正在制作一个可拖动的图库。:)

【20:10】<LouisLazaris>@ the webiz 但这不应该影响你的图片库功能。你的脚本应该覆盖它，AFAIK

【20:11】一些进一步的研究将有所帮助)

【20:11】<鹰派>欢迎刚刚加入的你们。如果你有问题，随时加入，不要等空档。

【20:11】<霍克>今天晚些时候我会在 SitePoint 上贴出完整的文字记录

【20:11】<the bwiz>LouisLazaris 嗯。不知道怎么弄。(A)使用本地 JS 作为响应站点。)

你在使用拖放插件吗？你必须使用拖放功能，而不仅仅是依靠用户本地拖动的能力。

【20:14】<保罗> @thewebwiz:使用 img{pointer-events:none}禁止拖动图像(在支持指针事件的浏览器中)

【20:15】<the bwiz>LouisLazaris–不是拖放–拖到下一张幻灯片。使用 JS，但想停止浏览器移动图像。找到了 Webkit 的 CSS 属性，但没有找到 FF。有人吗？

【20:15】<artfulnes nl>你好，我现在开始建立一个响应式网站。具有大的图像背景。这在桌面上至少是 1024 像素宽，但在移动设备上大约是 320 像素:但是你需要很多时间来加载小设备？或者这是如何工作的？

【20:15】

【20:15】<LJ>如果可行的话，这是一个伟大的魔法…让人们更难获得有版权的图片

【20:16】<LJ>webbiz

我想我还是不明白你在说什么。使用联系表格(页脚是链接)在我的网站上联系我，一旦我明白了你想要解决的问题，也许我会有更多的时间来解释(www.impressivewebs.com)

【20:16】<the Dutch coder>LouisLazaris rachelandrew 你对 bootstrap 这样的框架有什么想法？你认为它有助于构建好的 CSS，还是会妨碍它？

【20:16】<the bwiz>LouisLazaris 还好吧。谢了。

【20:16】<rachelandraw>@ artfulnesnl 本文有一些好的建议 re responsive images[http://mobile . smashingmagazine . com/2013/07/08/choosing-a-responsive-image-solution/](http://mobile.smashingmagazine.com/2013/07/08/choosing-a-responsive-image-solution/)

【20:17】<拉尔夫姆>@霍克—我们可以在代码编辑器中发布保罗的代码吗？

【20:17】<美工 nl > tnx！我也有和荷兰人一样的问题，不知道是不是因为我也是荷兰人，哈哈:)

【20:17】<theduthcoder>呵呵；)

【20:17】<霍克>拉尔夫好主意——我可以允许保罗这么做

【20:17】<HugoGiraudel>保罗好消息，IE 11 将是第一个支持指针事件的 IE 版本。

【20:18】<LJ>我是说保罗..看起来是一个非常简单的解决方案。我见过有人为了这个目的把透明的图像放在真实的图像上

【20:18】<>(我们显然是非常怀疑的人)

【20:18】<rachelandruw>@ TheDutchCoder 我不是一个粉丝，对原型设计很感兴趣，但是那些东西会给你留下一些令人难以置信的臃肿的 CSS。

关于 Bootstrap 这样的框架，LouisLazaris > TheDutchCoder:这些框架所做的事情之一，我很喜欢，是帮助推广模块化，或 OOCSS，这很好。我发现有时人们把这些框架添加到他们的网站上只是为了使用一两件小事，这是膨胀的，但总的来说，我认为它们有助于快速解决问题。我

【20:19】<LouisLazaris>相信瑞秋写了一篇关于解决我们没有的问题的好文章，也许她能把它联系起来。

【20:19】<霍克>保罗——如果你想发布拉尔夫提到的代码，你有权使用代码编辑器

【20:19】<保罗> @HugoGiraudel 是的，指针事件是有用的，目前已经在 Firefox 和 Chrome 中得到支持

【20:20】<雨果·劳德尔>保罗和歌剧。和狩猎。还有安卓。

【20:20】<rachelandruw>这一个——更多关于 HTML5 样板文件之类的东西真的，但是耶:)[http://www . rachelandruw . co . uk/archives/2012/03/21/stop-solution-problems-you-dont-yet-have/](http://www.rachelandrew.co.uk/archives/2012/03/21/stop-solving-problems-you-dont-yet-have/)

【20:20】<the bwiz>the Dutch coder——我喜欢框架。我研究得最多，从他们每个人身上学到了很多东西。制作自己的… :)

【20:20】<the Dutch coder>我之所以这么问，是因为我目前不得不为一个使用 bootstrap 的客户工作，我发现 bootstrap 对我的 OOCSS 的干扰大于帮助……也许这也是因为它支配了很多设计元素。

【20:20】

【20:21】<the Dutch coder>the bwiz 是的，它们对学习来说很棒，但是我已经建立了我自己的，没有实际的样式(它更像是一个布局框架)。回去感觉很繁琐

【20:21】<霍克>保罗您可以将代码保存到一个要点中，该要点会将它放入脚本中(使用设置目录)

谁发布了那个 img 代码？非常感谢。那看起来像一个胜利者。

【20:22】<鹰派>本届飞。我们还有大约 10 分钟的时间。如果你有任何未问的问题，现在是时候进入

【20:22】<保罗>被救编辑:[https://gist.github.com/6083386](https://gist.github.com/6083386)

【20:22】<霍克>the bwiz 我相信是保罗:)

<LouisLazaris>theduthcoder 同意了。我有一个初学者朋友向我寻求关于 Bootstrap 问题的帮助，老实说，如果不移除 Bootstrap，我无法修复它。它与东西相冲突，如果不全部删除并从头开始，很难找出问题所在。我认为 CSS 的本质有时让构建东西变得更容易

【20:22】<LouisLazaris>从零开始比学习一个框架的错综复杂。

【20:22】(这种方法过去对我很有效)

【20:23】<the Dutch coder>LouisLazaris 是的，模块化非常好，我不喜欢它的美学部分。举个简单的例子:Bootstrap 移除按钮活动状态下的背景图像。诸如此类的事情让我觉得这更像是“我是一名设计师，不想学代码”的事情。；——)

【20:23】<the bwiz>Thx Paul。

【20:24】<艺术感 nl >有没有一个响应导航的好来源？

【20:24】<保罗>不客气地说:

【20:24】<>[【http://css-tricks.com/responsive-menu-concepts/】](http://css-tricks.com/responsive-menu-concepts/)

蒂姆·皮特鲁斯基在 CSS-Tricks 的文章【20:24】<【HugoGiraudel>。

【20:24】<艺术性 nl >对于手机:链接到电话号码的按钮/链接？

【20:24】

【20:25】<艺术

【20:25】<美工> :)

【20:25】<美工 nl > tnx！

【20:25】<HugoGiraudel>使用电话号码作为 href。<a href = " 0123456789">叫我也许< /a >。

你可能想把它添加到代码编辑器中。:)

【20:25】<rachelandraw>@ artfulnesnl[http://bradfrost.github.io/this-is-responsive/patterns.html](http://bradfrost.github.io/this-is-responsive/patterns.html)一套很棒的设计模式包括导航

幸运的是，有些事情也很简单！

【20:25】<the bwiz>更多模式在此:[http://bradfrost . github . io/this-is-responsive/patterns . html #导航](http://bradfrost.github.io/this-is-responsive/patterns.html#navigation)

【20:25】<HugoGiraudel>Paul Irish 嗨，保罗，很高兴在这里见到你！:)

【20:26】<鹰派>HugoGiraudel——你能行:)

【20:26】<拉尔夫姆>另一个反应灵敏的导航链接(也是布拉德·弗罗斯特):[http://bradfrostweb.com/blog/web/responsive-nav-patterns/](http://bradfrostweb.com/blog/web/responsive-nav-patterns/)

【20:26】

【20:26】<保罗·爱尔兰>:)来自俄国的大家好！

【20:26】<鹰派> HugoGiraudel 别忘了保存要诀

【20:26】<保罗·爱尔兰>"电话:"必填？

【20:27】<保利瑞> < 3z

【20:27】<HugoGiraudel>对，忘了这个。:P

【20:27】<美工 nl >我要偷了:叫我也许概念肯定

【20:27】<HugoGiraudel>已保存编辑:[https://gist.github.com/6083436](https://gist.github.com/6083436)

【20:27】<保罗爱尔兰>路易，没问题。只是过来看 n 学

【20:27】<霍克> 5 分钟通话，我的朋友们

【20:28】<TaoistTotty>在桌面上使用 Skype 的人也可以打电话给我吗？

【20:28】

那就太棒了，哈！

【20:28】<LouisLazaris>应该有<a href = " snail mail:55 Whatever drive ">我的地址< /a >！！:)

【20:29】<the bwiz>q . tel:link——如何拨打国际电话号码。包括+和国家代码？

【20:29】<艺术我总是对居中的东西有问题。像导航那样有一个浮动左

【20:30】<美工 nl >有窍门吗？

【20:30】<HugoGiraudel>我猜无论你在` tel:`(只要是个数字)后面放什么都会被解析。所以<a href = ' tel:+33123456789′>Call</a>应该管用。虽然我可能是错的，但我真的不确定。

【20:30】<LouisLazaris>不确定这是否有帮助:[http://demosthenes . info/blog/536/Adding-Phone-Numbers-To-Web-Pages-With-html 5-&-Microdata](http://demosthenes.info/blog/536/Adding-Phone-Numbers-To-Web-Pages-With-HTML5-&-Microdata)

【20:30】

【20:30】<paulirish>道士，如果 skype 安装了这个讨厌的浏览器扩展程序，它就能劫持你所有的电话号码。否则不行。大多是移动的东西

【20:30】

【20:30】

【20:31】<ralphm>@ artfulnesnl 在那种情况下有比 float 更好的选项，比如 display: inline-block 和 display: table …

他们在使用内联块时有什么问题吗？

【20:31】<LouisLazaris>artfulnesnl 我在这里写到了:[http://www.impressivewebs.com/center-multiple-divs/](http://www.impressivewebs.com/center-multiple-divs/)这也适用于列表项目

【20:31】<主战派>那就是我们该起床的人了。感谢您的参与。

【20:31】

非常感谢路易斯和瑞秋抽出时间来帮助我们。

【20:31】<HugoGiraudel>对抗直列块之间的缝隙。

<霍克>你们都可以留下来，想聊多久就聊多久，但是我会在我们的专家准备好的时候把他们解放出来

【20:32】<保罗·爱尔兰>* *为路易斯&瑞秋鼓掌！* *【20:32】<LouisLazaris>谢谢大家，我再待一会儿

<the bwiz>听听你的掌声

【20:32】<杰瑞>谢谢大家！

<rachelandraw>谢谢——很高兴和你们聊天:)这里已经很晚了，我马上就要消失了

【20:32】<霍克>对于任何错过它的人，这里是路易和瑞秋的最新著作[https://learnable.com/books/jscss1](https://learnable.com/books/jscss1)

【20:32】<the Dutch coder>LJ 不如果你是为 IE8+ 开发的话

【20:32】<陶伊斯托蒂>同样在这里就掌声雷动

【20:33】

【20:33】<LJ>Chrome 字体渲染怎么样…非常感谢瑞秋和路易斯以及所有人

【20:34】或者你通常只是使用 MDN 或其他类似的参考？

我喜欢这本书。爱不释手。从头到尾读一遍

【20:35】<the duthcoder>LouisLazaris both tbh。我发现官方规范有时有点让人不知所措，而 MDN 在其用例中又有点太有限，所以两者都有点！

【20:35】<HugoGiraudel>MDN 大部分时间。官方规范当我想深入挖掘某个东西的时候。

【20:35】<HenriHelvetica>@ LL 搜索到的任何东西！；)但是我确实喜欢 MDN

【20:35】<保罗·爱尔兰> lj..最近在 windows 上有很多关于 Chrome 文本渲染的工作。基本上，这意味着将文本呈现后端从 GDI 移植到 DirectWrite。这是一项巨大的努力，但我们现在正在快速推进

<>阅读本书到。但是当我把它付诸实践时，我总是发现自己在挣扎。当你读到的时候，一切都显得那么简单

【20:35】

【20:35】<艺术感>然后你花了 3 个小时在一个没有去你想去的地方的分区上:D

【20:36】<保罗爱尔兰>LJ:[https://code.google.com/p/chromium/issues/detail?id=137692#c104](https://code.google.com/p/chromium/issues/detail?id=137692#c104) 有最新更新

【20:36】<LJ>感谢 PaulIrish 的更新…我还以为可能是我呢。有没有变通办法？

<LouisLazaris>我同意 HugoGiraudel 所说的，我两者都用，但我个人认为规范中有太多无关紧要的东西，我认为可以删减。它也有太多日常人们不理解的术语和东西。

【20:36】

【20:37】<保罗爱尔兰>*铬。这基本上相当有效。

【20:37】<HugoGiraudel>说真的，官方规格不是用来日常阅读的。它过于复杂(顺便说一下，这是完全正常的)。

【20:37】？

<the Dutch coder>LouisLazaris 我认为规范*应该*非常详细，只是针对那些边缘情况，但是，是的，一个更可读的版本对开发者和新商业来说会很好

【20:37】

【20:37】<解码器> -m

我认为有人可以很容易地建立一个叫做 QuotesFromW3C.com 的有趣的报价网站:)，上面有很多难以理解的例子。

【20:37】<HugoGiraudel>我会说当你让元素 A 和 B 浮动，并且 C 是 B 的直接兄弟，那么你就可以用 C 清除了

【20:38】<chinebro>顺便说一句，响应图像前沿的任何进展。没有一个解决方案看起来是理想的——画布、通过媒体查询的多个图像大小等

我认为 http://webplatform.org/应该是那个地区的一个中间地带。

【20:38】<LJ>这样我就可以为 Chrome 指定 svg 了，不知何故，就用 Modernizr 吧？当然，我有所有的格式在那里

【20:38】<LouisLazaris>但我觉得它在游戏中来得太晚了，无法产生重大影响。

我对 WebPlatform 寄予厚望。期待它的成长。

【20:39】<HenriHelvetica>HugoGiraudel hmmm……我希望我能看到一个例子。我绿@这个

webplatform.org 仍然在运输货物。只是在外展方面很安静，但内容做得很好

我认为这将一直是一个问题，直到我们找到一个单一的、跨浏览器的解决方案来解决所有问题。

【20:40】

【20:40】<HenriHelvetica>也许就是这样，我还没看够遍。但是，我想我会问。

【20:40】..使用它来使用前面有 svg 的 diff @ff 定义..你还想在其他人面前炫耀吗

【20:41】<【hugogiraudel】>【henrhelvetica】[【https://jsfiddle . net/e26 xs/](https://jsfiddle.net/e26Xs/)

<LouisLazaris>chineebro 我对 resp 的了解。图像现在很有限。我大部分时间只是在阅读别人的想法，等待一些好的东西出现，所以我不能在这方面说太多。:)

【20:41】<HugoGiraudel>HenriHelvetica 试着从 C.

【20:41】<拉尔夫姆>@亨利当一个非浮动元素跟随浮动元素时，它会倾向于试图在浮动元素旁边向上滑动，这往往是你不希望的。所以设置为 clear 可以阻止这种情况发生。

【20:42】<对于大多数图像，我只是在浏览器中缩放它们，因为大多数需要响应式布局的设备往往会很好地渲染它们，但这远非理想(例如，它仍然可以下载完整的图像)

【20:42】<LJ>感谢保罗爱尔兰人……这个演讲让我开心了一天。得走了。再见。期待看到许多资源和链接的脚本

<霍克>好了，伙计们，我得走了——我还没吃早饭呢。

【20:43】<鹰派>想待多久就待多久，但请注意，会议将不再主持。

【20:43】<保罗>henrihelvetica 这是一篇很老的文章，但应该有所帮助:[http://www . search-this . com/2007/09/05/let-be-clear-about-this/](http://www.search-this.com/2007/09/05/lets-be-clear-about-this/)

【20:44】

如果某种突破性的数据传输技术能让整个问题变得没有实际意义，那就太理想了……但我怀疑这不会很快发生。

【20:45】<保罗·爱尔兰>我出局了。后来的路易，雨果。< 3z

<the Dutch coder>chineebro 同意…并且不要忘记，对于真正响应的图像，你需要生成同一图像的几个版本，有时还需要进行不同的裁剪…也许有一天这在 CSS 中会更容易实现。

【20:45】<LouisLazaris>好的，谢谢保罗

【20:45】<HugoGiraudel>再见保罗！谢谢路过。:)

【20:46】<中文网>是的，我觉得最好的解决办法是有一种新的可以很好压缩的文件格式。但是这将意味着引入一个全新的标准… :)

<>感谢链接。

【20:47】

【20:48】<LouisLazaris>再来，除了这本书([【https://learnable.com/books/jscss1/online/ch01.html】](https://learnable.com/books/jscss1/online/ch01.html))你还可以看看我的最新项目，一份新的面向前端开发者的每周简讯:[【http://webtoolsweekly.com/](http://webtoolsweekly.com/)</不要脸的插头> :)

【20:48】<Andy chups>不好意思，我是这里的新手。Louis 你能给我解释一下 FnExpr 和 FnDecl 吗？

【20:49】<拉尔夫姆>感谢路易。我报名了。:-)

我不知道那些是什么…你从哪里得到这些话的？

【20:50】<the Dutch coder>我想那是 Java 的说法吧？；)

【20:52】<约翰>伙计们，你们知道 BEM 吗？

【20:52】<the Dutch coder>John 是的，我用它来做我们的项目

约翰:我知道这个方法，我认为对于从事大型项目的大型团队来说，这是一个非常好的方法。但是我没用过。

<【20:53】HugoGiraudel>确定。

【20:55】<LouisLazaris>我觉得 andychups 在耍我们

【20:55】

## 分享这篇文章