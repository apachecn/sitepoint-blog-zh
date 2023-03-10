# SitePoint 播客#132:波士顿环球报做出回应

> 原文：<https://www.sitepoint.com/podcast-132-the-boston-globe-goes-responsive-with-ethan-marcotte/>

SitePoint 播客第 132 集现已推出！本周，我们的常规采访主持人路易斯·西蒙诺( [@rssaddict](http://twitter.com/rssaddict) )采访了伊桑·马科特([@哔声](http://twitter.com/beep)或 [@rwd](http://twitter.com/rwd) )，这位设计师被广泛认为是响应式设计这个术语的创始人。他也是第一次在所谓的“主流”网站[波士顿环球报](http://thebostonglobe.com/)上将此付诸行动的团队成员之一。

你可以下载这一集作为独立的 MP3 文件。以下是链接:

*   SitePoint 播客#132:波士顿环球报回应伊森·马科特

## 剧集摘要

路易斯和伊森抓住了波士顿环球报的设计工作是如何产生的，响应式设计的想法是如何出现的，以及“所有不响应式的东西都落后于时代了吗？”。

在[http://delicious.com/sitepointpodcast/132](http://delicious.com/sitepointpodcast/132)浏览展览中引用的链接的完整列表。

## 采访记录

路易斯:大家好，欢迎来到 SitePoint 播客的另一集。在本周的节目中，我们采访了一位特别嘉宾；SitePoint 播客的老听众会注意到，在过去的几个月里，我们在很多集中关注了响应式网页设计的一般概念，所以很高兴今天的节目中有一个人，很多人会说他是这个概念的创始人，伊桑·马科特，你好，欢迎来到节目，伊桑。

伊森:非常感谢你邀请我。

路易:绝对是我的荣幸。让你上节目的想法在我的脑海里已经有一段时间了，最近，我们在最近的一次小组讨论中讨论的《波士顿环球报》网站的重新设计使它成为了一个焦点。你过得怎么样，伊森？

伊森:好，好。这是一个疯狂的月份，像你说的那样推出了一个小网站，然后马上就和我的妻子去度蜜月了，所以我有点倒时差，有点累，还有点感冒，但除此之外，回来上网真的很好。

路易:多么迟来的蜜月？

伊森:事实上，这个星期一是我们的两周年纪念日。

路易斯:好吧，好吧，你最终会找到时间的。

伊森:我非常擅长时间管理(笑声)。

路易斯:就像我说的，我稍后会谈到《波士顿环球报》的重新设计，因为这是我想让你上节目的主要原因。

伊森:当然可以。

**Louis:** 但是，也许我们可以稍微回顾一下，谈谈响应式设计，这是我们在节目中经常谈到的东西，在过去的一年里，它已经成为网页设计和开发领域的热门话题。这一切都源于你为[写的一篇文章，那是大约两年前的事了，对吗？](http://alistapart/)

伊森:对，实际上是去年五月。

路易:哦，也没那么久。

伊森:不，不太多。我想我在去年西雅图的一次活动中创造了“四月”这个词，然后一个月后我又写了一篇文章。

**路易:**对。一个显而易见的问题浮现在脑海中，这个问题你可能会经常被问到，比如说，这个想法在网页设计社区中被接受的程度，以及人们愿意尝试它、实现它的程度，我们已经看到框架采用了它，互联网上的画廊；这些对你来说是不是一个很大的惊喜，或者当你想到它的时候，你有没有想过，哦，这将是下一个大事件，或者是什么？

伊森:(笑)嗯，我仍然对人们对它的兴奋程度感到惊讶。是的，这只是我一直热衷的东西，我想我不知道，灵活，愚蠢的术语，但无论如何，我的购买设计是这样的，这是我对结合灵活的布局和媒体查询感到兴奋的东西。因此，这似乎是——我们似乎达到了一个点，特别是在我写它的时候，我们的许多跨不同设备或不同引用/未引用上下文的设计方法肯定感觉变得非常支离破碎。这并不是说设备特定的设计有什么问题，我认为这有一个很棒的商业案例或很棒的论点，但在当时，无论如何，这肯定感觉像是默认的，就像每个人都有自己的 m. sites 或 WebKit . sites 或重定向 iPhone 用户，而忽略了其他人。所以，你知道这只是一种——把另一种选择放在桌面上，一种替代方案，所以很多人都这样做的事实真的很令人振奋，我的意思是他们用它做了一些我从来没有想象过的事情。所以，是的，有点令人兴奋。

路易:肯定。当你第一次看到 CSS 规范中增加了更复杂的媒体查询时，你的脑海中是否会立即闪过这样的想法，哦，等等，我想我们可以在屏幕尺寸上做些聪明的事情，或者这需要酝酿一段时间？

**Ethan:** 我不知道，我的意思是这是一个很好的问题，我想我实际上刚刚完成了一个移动专用网站——当时我在 Happy Cog 工作，几个月前我为他们举办的一个西南偏南派对完成了一个活动网站，其中一部分实际上是围绕它设计一种活动体验。在活动当晚，实际上会有某种类似现场投票的应用程序，观众可以访问，所以有必要创造这种特定的移动体验。所以我当时读了很多关于如何做到这一点的很棒的文章，因为我当时太专注于桌面了，是的，我认为它就是这样产生的；我在一个标签页上放了约翰·奥尔索普的《网页设计之道》,然后在另一个标签页上放了一些关于媒体查询的很棒的东西，我真的觉得这就是它发生的地方，你知道我们有所有这些很棒的工具来更灵活地构建，但是我们一直在媒体中引入的所有这些限制，我不知道， 这有点像我们可以把网络的灵活性设计成一种优势，而不是，我不知道，某种我们需要解决的限制或特征。

路易:对，绝对没错。我想这又引出了我的另一个问题，你觉得你最初对响应式网页设计的想法有什么误解吗？我想说的是，这似乎被认为是一种弥合移动和大屏幕之间差距的方式。因此，如果你看看你的文章中的第一个例子，以及其他各种类似的例子，它们都是非常流畅的设计，在各种宽度下看起来都很好，许多人都遵循了这一点，看起来更像是一个固定宽度的布局，然后是另一个固定宽度的布局，两者之间只有一条分界线。你是否觉得这不完全是你的本意，或者只是做同一件事的不同方式？

伊森:我的意思是这绝对是超越桌面的一种不同的设计方式。我认为媒体查询是一个非常强大的工具。对我来说，我最近有点谈论这个问题，但我有点认为有这种更大类别的引用/引用自适应设计使用某种前端技术，无论是 JavaScript，无论是样式表转换器，无论是媒体查询，以根据客户端的一些参数改变布局。对我来说，响应式设计一直是灵活布局和媒体查询的结合，所以我喜欢将这两个术语区分开来，这并不意味着任何判断，就像我最喜欢的适应性网站之一可能是 informationarchitects.jp，它是一个美丽的网站，内容精彩，设计可爱，但它们在三种不同的固定宽度布局之间穿梭，这很好。(笑)我认为应该由设计师和网站所有者来决定什么最适合他们的舒适度，最适合他们的内容，你知道，只是因为它没有响应并不意味着它不漂亮，所以这就是我的问题所在。所以我认为地球仪是一个传统的响应式设计的好例子，它是一个完全流动的网格，有一个上限，有一个最大宽度，因为——这比其他任何东西都更有时间限制，但实际的基础是灵活的，即使媒体的询问对它来说是不可用的，你知道内容会相应地充满屏幕，所以是的，这是我脑海中的一条线，但没有隐含的判断。

路易:对。你提到的关于环球网站的一件事是，如你所说，灵活性有一个上限，所以过了这个点你就不能再调整内容了，因为它处于一种合理的最大宽度。

伊森:是的。

Louis: 当我看响应式网站设计时，这是我一直在想的事情，重点似乎是我们如何将这些我们已经设计了十年的以桌面为中心的网站应用到 iPads 和小屏幕上。

**伊森**是的。

Louis: 但我想任何人都有过这样的经历，在你 24 英寸的影院显示器上看到一个 800 像素的固定宽度的网站。

伊森:(笑)是的，完全正确。

路易斯:就像，哇，这真是对空间的糟糕利用。

伊森:当然可以。

**路易:**似乎没有多少人朝那个方向努力，就像现在我们有了更多的空间，我们如何填满它？

**伊森:**是的，完全正确，我的意思是，我认为肯定有很多人觉得响应式设计很有吸引力，因为他们认为这是一种移动友好的捷径，最后，它肯定可以这样使用，你知道，你可以使用灵活的布局媒体查询将这种桌面特定的设计塞进一个更小的屏幕，这太棒了。但对全球来说，我的意思是——我在书中谈了很多，但实际上没有谈到这个项目，但基本上我们设计过程的很大一部分是采用这些非常以桌面为中心的设计，然后让设计的使用通知设计过程的其余部分，就像实际上从 comps 转移到一个响应模板框架，你知道，基本上把所有内容都注入进去， 把所有的图片都放进去，然后实际使用设备和浏览器，并与设计进行实际的实时互动，作为我们从那时起的设计审查的一部分，所以我们实际上可以进行很多很好的讨论，使用卢克·乌鲁布莱夫斯基的移动优先概念作为这一内容的一个楔子——这个有 58 个链接的模块对我们的移动用户有任何价值吗？如果没有，这对我们的任何读者有什么价值，你知道； 这真的属于这一页吗？这并不是说有一个简单的二进制，但它实际上是要认真看待我们正在设计的内容，并确保-对它做出承诺，跨越每种环境，跨越每种设备。我的意思是，前端技术可以让设计适合任何屏幕，但你真的知道，如果你能开始这个项目，认真看看你正在设计的内容，问问自己它是否真的有价值，我认为对所有读者来说，整体体验会好得多。

路易:对。你在文章中提到了设计构图的概念，我想这是非常有趣的，因为这些非常灵活的布局包括许多不同的方式来组织屏幕上的内容。这对于传统的设计工作流程来说是一个巨大的挑战吗？在传统的设计工作流程中，你通常会使用 Photoshop comp，这就是网站的外观。

伊森:对。

Louis: 然后把它传递给利益相关者，决策者，他们会说是的，这就是我们想要的网站外观，而当你在做响应式设计时，就像我们之前说的，可以有三到四种不同的固定布局，或者可以有一种在屏幕上排列内容的不同方式的无限渐变；你如何呈现它，它如何影响设计的工作流程？

伊森:是的，不，我是说这是个很棒的问题。特别是对于 Globe 来说，我的意思是当我来到现场时，已经有相当多的创造性工作完成了，当 Filament Group，实际上是我的项目客户，和我在某种程度上与 Globe 见面时，他们已经为网站的某些部分做了一些特定于桌面的设计，并且有点理解这是我们所有这些品牌资产和所有我们需要包括的功能的目录。因此，我们与他们进行的第一次讨论之一是，我们试图将 comps 定位为，好吧，这是我们对设计需求的理解目录， 这是我们对这种设计如何响应的最佳猜测，因为对我们来说，在所有这些不同分辨率范围和不同方向上，说出主页在平板电脑和手机上的外观是不可行的，所以它主要是关于，好的，我们将非常快速地进入 HTML CSS 原型，并继续基本上审查这些设计假设，你知道，在浏览器中运行。 所以，当我们将某些组件移植到所有这些不同的屏幕上并微调内容时，这就像是迭代某些最初不一定工作得很好的组件，但它也帮助我们变得更加敏捷——我想我不是说像资本一样敏捷，而是像——

路易:(笑)

伊森:我被告知这是一个东西(笑声)。是的，当我谈到响应式设计时，我一直在介绍这种更迭代的工作流程，每个人都喜欢你们听说过敏捷方法吗，这不是我在这方面受过很多正式培训的东西，但我们似乎决定采用这种混合流程，在这种流程中，设计和开发团队基本上会在整个流程中紧密合作， 表格的一半是 Photoshop 和 Illustrator 中的设计建议，另一半是 HTML 和 CSS 中的设计建议，但我猜这两者都是对设计需要如何发展提出建议，然后他们只是需要像一个统一的小组一样接受审查。

与几年前相比，你是否发现自己现在直接在浏览器中做更多的设计工作和设计思考？

伊森:哦，是的，甚至是一年前。我不知道，这个瀑布过程是，你知道，我工作过的每家商店都使用过它的一些变体，环球绝对是我最大的改变，你知道；你说我们不打算签字同意然后制作它们是什么意思？(笑声)所以——我的意思是，我们的工作方式可以更灵活一点，这很好，因为，我不知道，我一直在举的例子是地球上的刊头——它可能看起来不像，但它可能是整个设计中更复杂的部分之一。我不知道，我们在 CSS 中提出了一些初步的视觉建议，关于桌面级的内容如何适应不同的断点，我们对此并不满意。因此，当我们进行设计评审时，我们认为这是一个可能需要更多关爱的设计领域，每个人都认为我们的设计糟透了。所以基本上发生的事情是，你知道这是一个很好的练习，让我们在设计可能很快失败的情况下处理原型，但我们总是问自己，是否有任何东西需要更多的艺术指导，是否需要更多的视觉改进， 团队的一半设计人员离开了，想出了这些关于 480 像素或 600 像素或类似像素的标题的精彩草图，所以我们可以从那里实现，但你知道，如果我们没有意识到这是一个非常复杂的布局区域，我们很快就迭代了十几次，因为我们就像往常一样，好吧， 让我们看看这是否可行，我们将进入 HTML 和 CSS，把东西移回来，你知道从那里开始提炼。

**路易:**对。回到你之前说过的一些事情，你提到你是在项目的后期被带来的，有大量的设计已经完成了。

**伊森:**毫米。

**Louis:** 被雇佣来做这项工作的机构，然后出去雇佣了你，他们有没有想到他们要做一个响应式的设计，这就是他们来找你的原因，或者这到底是怎么实现的？

伊森:这是个很棒的问题。所以 Upstatement 是波士顿的设计公司，他们做了令人难以置信的工作，在整个过程中与他们合作真的很棒。但我认为环球很早就有了这个想法，我们需要让尽可能多的观众看到我们的内容，所以总会有一些类似多设备的组件，当 Filament Group 参与进来时，他们实际上正在做大量的制作工作，他们带我一起去了， 我认为这种期望已经存在，部分是由于 Filament，这将是一种响应性的框架，但他们仍然需要在内部围绕网站的特定外观建立共识。 所以，我的意思是，设计相当简单，我还没有真正使用过 comp，实际上我想它还没有提供某种响应方法，这有点像你在那之后进行的讨论，就像如果你主要从桌面环境进行思考，那么会出现各种各样的问题，关于内容，关于设计将如何进行，体验将如何适应不同的设备，你知道这些将随着项目的不同而变化。

Louis: 这个项目有几件特别有趣的事情，最值得一提的是，很多人都出来指出这是响应式网页设计概念的分水岭，因为到目前为止，我们看到的大多数使用这些技术的网站要么是小型代理网站，要么是网页设计师的个人博客，人们可以在那里自由地进行实验。

伊森:对。

**Louis:** 但是对于一个有很多可见性的主要网站来说，人们把它比作 ESPN.com 的当从基于表格的布局转向基于 CSS 的布局的时候。

伊森:嗯，好吧。

**Louis:** 你是不是有这种感觉，或者你是否知道有其他知名度很高的项目已经这样做了？

伊森:我在工作的时候绝对没有这样想过(笑声)。我的意思是，就像《连线新闻》的重新设计和《ESPN》一样，你知道这些都是——我的意思是，这些都是我们行业中的梦幻时刻。我认为对我来说，在这样规模的网站上工作是令人兴奋的，不管它是否会有响应，所以这很酷。但我认为——我的意思是，真正令人满意的是，它有点像基本的前端组件，又像灵活的网格、灵活的媒体和媒体查询，就像从一个书呆子设计师的角度来看，看到他们在这样规模的网站上工作是非常令人兴奋的，但真正的学习过程更多的是围绕着过程的角度，像让设计团队参与进来，实际上只是打破了设计开发的障碍，并试图在一些事情上进行更多的迭代合作。所以，是的，我不知道，我的意思是规模的问题是一个有趣的问题，但最终我希望，你知道，也许它会让人们更容易使自己成为一个响应式设计，因为我知道有很多人真的对此感兴趣，但你知道，也许就像我们在进行 CSS 与 tables 的争论时，你知道，每个人都在等待有人先尝试一下；我不知道，也许地球会让这种推销变得容易一点。

**路易:**对。甚至有一个例子，你可以向人们展示，这不是一个非常小的机构网站或个人博客，看起来这是一个主流网站，看看当我拖动窗口时会发生什么(笑声)。

伊森:是的，是的，完全正确。我的意思是拖动窗口的事情很有趣，因为我认为这主要是一种原型工具，对，我的意思是你知道我认为灵活的布局是响应式设计的重要基础，就像我说的那样，但归根结底，你是在为特定的设备、特定的分辨率范围进行设计，是的，我喜欢做浏览器窗口的事情，但这对我来说是一种次要的好处。

路易斯:是的，哦，当然，这肯定是只有查看网站的设计师才会做的事情。

伊森:是的。

几周前，我为了这个节目采访了克里斯·科伊尔。

伊森:哦，太棒了。

Louis: 他刚刚重新设计了他的网站，并且在所有元素的位置上添加了过渡效果。

伊森:我知道这是—

路易:我知道这很棒。当我们谈论地球仪的重新设计时，实际上这是两周前在小组讨论会上出现的事情之一，就像哦，是的，这真的很酷，但当我移动搜索框时，它不会嗖嗖地转。

伊森:是啊，是啊。我听到了那个节目，我意识到我们那天失去了一个读者，但是，(笑声)是的，我们必须在未来的版本中放弃它。

路易斯:你知道，拖动窗户，我们可以尽情嘲笑，但这是我们现在去一个地方要做的第一件事。关于地球，我还想问几个问题，然后我可能会回来谈论另一个问题，这个问题是我稍后才想到的。

伊森:当然可以。

对我来说，对于这种规模的网站来说，有趣的事情之一是处理性能背后的技术挑战，因为当人们谈论移动和专用移动网站的响应式设计时，他们提到的一点是，除非非常小心地完成，否则你可能会在移动设备上加载大量最终不会被使用的资源。

伊森:对，没错。

**Louis:** 你知道，比严格意义上必要的更大的图像，更大的样式表，可能被设置为不显示的内容。在你的工作中有没有任何与网站相关的特殊挑战，或者你们在这个网站工作时是如何应对的？

伊森:这可能是另一个半小时的讨论(笑声)。但是，我必须高度赞扬 Filament Group 的员工，他们在网站上做了大量非常繁重的技术工作，我的意思是，我的大部分工作都是在设计方面，比如大量繁重的布局工作，然后确定流程，但是 Filament Group 的 Scott Jehl 和 Mat Marquis、Todd、Patty 和 Maggie 等人都做了出色的工作，为您提到的代码税问题提供了一些很好的解决方案。我的意思是有大量的工作试图保持前期下载尽可能的轻，所以最初的 JavaScript 包和最初的样式表包是非常非常轻的；如果你在一个免费的 JavaScript 设备上，或者你不理解媒体查询，你基本上不会下载除了内容之外的任何东西，然后会有很多基于功能的测试，以确定什么资产适合你的体验，比如你在一个触摸显示器上，如果是这样，那么你会得到一些库，智能地增强设计，使它更具沉浸感，但否则我们不会惩罚其他任何人。你知道，这是一些相当基本的动态 JavaScript 和 CSS 插入，所以我们开始使用 YepNope，我想我们最终只是使用了更轻量级的自定义加载器，但这有点像 Scott Jehl 的孩子，我真的认为他是我最近使用的 JavaScript 的所有优秀之处的负责人(笑声)。是的，这是其中的一件事，是的，我们只是试图提出一个轻量级的 JavaScript 词汇表，这样我们就可以识别你的设备的一些特征，然后把它们结合起来，好吧，这是实际上与你相关的代码，然后试图对不会从中受益的用户隐藏所有的恶意代码。从内容方面来说，我的意思是我们真的试图避免显示不显示的问题，这就是为什么我们试图在整个设计过程中进行大量的讨论，讨论这些内容对我们的移动用户是否真的有价值。因为，再一次，给人们灌输他们不会从中受益的内容是我们真正想要避免的事情。这个项目确实产生了一个或几个东西，但是 responsive images 脚本，或 Scott Jehl 的 responsive images 脚本确实是早期出现的东西之一，我想是一种更智能的方式，可以知道有多少图像像素被推送到我们的读者面前。你不能在屏幕的大小和可用的带宽量之间建立关联，但是这个脚本是一种很好的表达方式，比如，好吧，如果你的屏幕高于某个分辨率阈值，我们将提供特定图像的更高分辨率版本，如果低于该阈值，那么你知道你将默认获得这个移动图像；这是我们一直采用的方法——在整个项目中，我们一直在改进或已经改进了很多次，我认为有些人最近在研究浏览器预加载器方面做了一些有趣的工作，也许客户端解决方案从长远来看是不可行的，但在当时，这无疑是我们最好的解决方案，许多人都采用了这种方法，并对其进行了改进。所以，是的，我不知道，这基本上就像——这是一个非常有趣的项目，看着细丝小组工作真的很有趣，我不知道，用他们巨大的大脑粉碎问题(笑声)。我只是一个 CSS 和设计猴子的大部分，但是，是的，这是一个很大的乐趣。

**Louis:** 这很有趣，不过，它确实提出了一个问题，似乎所有这些东西都是伟大的想法，它们是克服这些技术挑战的奇妙方式。

**伊森:**毫米。

Louis: 当你说，哦，你对 CSS 和设计做了什么，这确实会让你想起；你有没有感觉到过去制作一个网站所涉及的仅仅是这些，所有这些其他的技术挑战都被加入进来，使得这个学科变得更加复杂，并且可能提高了进入的门槛？

伊森:是的。

路易斯:这种工作对个人来说真的非常非常难。

伊森:对。嗯，是的，是的，我明白你的意思。

路易斯:再说一遍，我不是在争论这个问题，我只是想知道你对此有什么想法？

伊森:哦，我知道你在找茬，路易斯(笑声)。但是，不，那是一个奇妙的观点，我的意思是我在想。我想对我来说，这就像你知道如果这不是一个响应迅速的网站，就像如果这只是一个普通的固定宽度 960 居中的网站，我的意思是我认为这些东西仍然是有价值的，比如这种真正智能的资产加载，我认为这是这种规模的网站的关键。我的意思是，我想你知道，我有点觉得最近我们有点试图超越这种渐进增强的整体概念，你知道那种为每个人提供内容，但智能地知道我们如何从基线增强东西。我真的认为，特别是对于一个响应式项目，但对于任何项目来说，比如聪明地知道你在为谁服务，这真的很有价值，所以这不仅仅是一种简单的低经验对高经验，我的意思是你知道我真的可以看到这一点——我从 Globe 学到的一些东西是我其他客户工作的一个很好的框架， 所以，是的，我觉得它产生的很多东西有点像，我不知道，这听起来有点夸张，但总体来说，这似乎是网页设计的基本常识。 你知道，我不认为我们在做任何革命性的事情，只是有点像是，你知道，聪明地知道我们在提供什么，向谁提供；如果你像在一个古老的黑莓手机上浏览网站，你会得到一个非常基本的体验和许多有希望的好内容，但如果你在 iPhone 或 iPad 或新的亚马逊 Fire 上，你会得到更丰富的体验，是的，希望它只是感觉正确。

路易:对。对于许多这些技术来说，它还处于游戏的早期，所以当人们开发框架或者仅仅是一组好的东西时—

伊森:哦，完全正确。

**Louis:**——我想，易于改编并开始使用的脚本将会变得更容易被更广泛的专业人士所接受，他们会欣然接受这种想法。

**Ethan:** 是的，我的意思是现在有很多很棒的工具，我知道 Paravel 的人最近刚刚开发了很多很棒的东西，比如 [FitVids.js](http://FitVids.js/) ，我不知道你是否已经看到了。

路易斯:是的，我想我们两周前在节目中提到过。

**Ethan:** 哦，是的，Chris Coyier 也参与其中，Paravel 也推出了 FitText 插件，因为 Trent Walton 喜欢他博客上的大标题，但我认为这些都是很棒的工具，它们可以做出一些灵活的设计，不管它是否响应迅速，感觉有点难以逾越；所以，我不知道，我有点兴奋地看到所有这些东西都出来了，是的，真的很兴奋地看到接下来会发生什么。

路易斯:嗯，是的。关于地球仪还有一件事，我很想听听你的看法。

伊森:当然可以。

**Louis:** 所以，撇开这是一个响应式设计的事实不谈，有趣的是，这是一个付费内容网关，所以他们采取了一种方法，你知道，这不是一个广告支持的免费网站，而是付费内容。我想很多人提出的观点之一是，这种设计的响应特性可以作为一种卖点，因为有很多报纸在做付费的 iPad 应用程序，然后他们的网站有广告支持。在这种情况下，我想它背后的逻辑是我们可以做一次并收费，因为它在 iPad 上和在 iPad 应用程序上一样好，或者在 iPhone 上和在 iPhone 应用程序上一样好。

伊森:是的，我的意思是我对付费墙方面的商业决策没有什么深刻的见解，我的意思是我在发布日和其他人一样发现了付费墙的规则。

路易:(笑)

**Ethan:** 嗯，我的意思是广泛的访问总是有点像全球真的投资于此，所以，但是就像那会是什么样的而言，我的意思是他们自发布以来的许多营销信息都是围绕着它，就像在波士顿的地铁上看到这些海报有点奇怪，这些海报有像[Bostonglobe.com](http://Bostonglobe.com/)，然后那里有一张六个设备的图片，你知道，都显示着内容，这很酷但令人不安(笑声)。所以，是的，但是就商业决策而言，你知道环球公司比我更适合回答这个问题。特别是关于广告，我的意思是，这实际上是我花了很多时间来为他们的广告提供一个体面的前端实现，因为有这样的要求，如果他们有一个广告属性，我猜是在桌面上的 C 列，他们希望能够在视窗变小时在设计中明显地提升，所以我们跳过了几个有趣的环来实现这一点。但我发现，现在没有多少广告网络真正考虑桌面以外的问题，你知道他们中的一些人将移动和桌面视为两种不同的产品，我还没有见过真正优雅的方式，可以在页面中拥有广告属性，但我想可以从移动版本增强或增强触摸广告， 你知道，这很像是一个尺寸适合所有人，就像这里的这种广告槽，每个屏幕都会有这种情况。 所以，我不知道，我认为这可能是一个有趣的机会。

路易斯:是的，我想这仍然没有得到应有的重视。你会看到很多没有广告的专用移动网站，而完整版却有广告，只是因为我不知道该怎么办(笑声)。你必须认为这就是想法，对吧，就像我们只是要做一个移动版本，我们负担不起提供广告，或者我们不知道把它们放在哪里，所以我们不会把它们放在那里。

伊森:对。是啊，这是个很好的问题。

**Louis:** 此外，举例来说，如果你在移动设备上看《波士顿环球报》，广告仍然存在，但点击这些广告会将你带到 960 固定宽度的 Flash 动画和 30 个字段的巨型表格。

伊森:(笑)对，对。

**Louis:** 这显然对广告商不利，对吧，我的意思是，如果人们不以这样的思维方式思考，看，如果这将通过移动设备提供，我们给这些人的内容，我们给这些人的销售，也必须适合那些设备。

**伊森:**是的，完全正确，我并不是说这是一个简单的问题，哦，好吧，我们只要在这个 JavaScript 上盖章，问题就解决了，就像这肯定是一个大的内容问题，就像你知道如果我要购买一个广告活动，我需要开始考虑好吧，我需要，我不知道，一个移动广告位，一个平板广告位，一个桌面广告位，一个宽屏广告位，就像这些是怎么做的，你知道，什么样的这是一个大问题，我想你知道如果有人能想出一种方法让这种感觉可以解决，那将是巨大的，因为移动广告显然是最近爆炸式增长的，如果我们能想出一种更跨设备的服务方式，我不知道，我不敢相信我在谈论广告(笑声)，是的，我认为这将使我们作为设计师的工作容易得多。

Louis: 在大多数情况下，我们的内容很大程度上是由广告支持的。

伊森:当然，是的。

路易:像这种情况也有一些例外。随着网络技术的发展，可能会有一些滞后，这会影响事情的发展。

伊森:是的。

**Louis:** 你知道，再举一个例子，几天前我走在街上，看到一张有趣的海报，我不记得是什么了，但上面有一个二维码。

伊森:当然可以。

Louis: 所以我打开我的条形码扫描仪应用程序，只是为了向我的女朋友展示这个很酷的小发明，然后扫描代码，出现的页面对于移动设备来说是非常不合适的。

伊森:那真的很有意思。

路易:它是通过二维码推广的，我甚至看不懂这东西。

伊森:哇。

**Louis:** 因此，随着设备的激增，在很多层面上显然出现了脱节。

伊森:对。

路易斯:我想在整个课程中，这个特别的项目可能会有助于弥合这一点，因为它将它带入了一个更主流的文化世界。

伊森:嗯，好吧，我们走着瞧。就像我说的，这是个大问题。如果有人能解决这个问题，那就太棒了，特别是我们不用处理 1997 年写的 JavaScript，所以那会很棒。

Louis: 我之前想问你一个问题，让我们回到这个话题，有些人站出来说——我想说 Jeffery Zeldman 是我见过的第一个说这种话的人，意思是如果你现在不做响应式网页设计，你就不是网页设计师，你在做别的事情。

伊森:好的。

**Louis:** 我可能错误地引用了这句话，我还认为我见过杰里米·基思(Jeremy Keith)和安迪·克拉克(Andy Clark)这样的人说的话语气非常相似，我想知道你对这种说法有什么感觉，当我们谈论你加载一个页面，你开始拖动窗口，看看它对不同大小的反应是我们现在都在做的事情，你知道，我想知道你现在是否觉得这是最佳实践的一部分；2011 年 10 月，当你访问一个网站，一个全新的网站，它的设计并不符合要求，你会觉得有点像，啊，你会觉得像在 2005 年看到一个全 Flash 网站一样。

伊森:对，对。嗯，我可能会，但那只是因为我过着非常悲伤的生活(笑声)。但是，不，我的意思是，我认为杰里米·基思和我都有类似的帖子，好像相隔一两天，有点奇怪地在几个月前同时出现，我认为作为一个设计社区，我们有点抓住这些有点像黑色对白色，我不知道，移动对桌面，这些有点像两极，有一段时间有这种有点像移动对响应的讨论。我不知道，我认为一个阵营有点像响应式设计是作为移动网站的替代品提出来的，对此有一些受伤的感觉，我绝对没有，我从来没有那种感觉，就像我认为你真的需要将方法与你正在设计的内容结合起来， 如果你的平板电脑体验、移动体验和桌面体验之间有大量的重叠，那么适应性或响应性方法可能是正确的，但除此之外，就像你知道的那样，你真的必须为你设计的受众服务。 所以，我觉得我不知道响应式设计是否是最佳实践，但我确实觉得我们已经到了这样一个地步，我们需要开始问自己，我们如何开始超越笔记本电脑屏幕和台式机屏幕的设计，我们的策略是什么，我认为响应式方法可能是许多项目的正确选择，如果他们愿意考虑跨设备体验的话。我不认为对全球来说会是这样——就像如果我们达到了在页面上隐藏大量内容的地步，我会是第一个说，看，也许我们需要在这里找到一种不同的方法。我有点觉得——我最近已经说过几次了，但是教条并不能解决问题(笑声)。我对响应式设计很有热情，我想不出我们可以用任何其他方法来做 Globe，就像不考虑我的偏见一样，这是一个非常有趣的项目，但我真的想不出任何其他方法可以做到这一点，如果我们没有，你知道，如果我们没有从一个灵活的布局开始，真正迭代地设计，然后在我们很难看的内容上分层媒体查询，但这并不是说它对每个网站都是正确的方法所以是的，我真的没有一个简单的答案。

路易:对。显然，就这些技术而言，它还处于早期阶段，但你不会说它与 2005 年的全 Flash 网站有任何相似之处。

伊森:哦，不，这听起来完全太宋飞了，但就像我最喜欢的一些网站一样是固定宽度的，所以(笑声)，如果你设计了 960 网格，地狱或高潮而不放弃你的像素，这并不是说你是一个坏人，我的意思是，我认为当我们进入这样的讨论时，就像它是响应或死亡或它是设备检测或我不知道自由或死亡或其他什么，这就像我们把自己关在一个非常棒的解决方案之外如果我们能少一点教条，多一点思考我们如何评估这些东西，并为它提出一些实际的标准，而不是像我一样滔滔不绝地说，哦，你知道如果上下文之间有很多重叠，就像如果我们能在未来一两年内正式化，就像，好吧，x， y 和 z 对于这个项目的观众来说是合适的，那么响应的方法是错误的，那么我们可以对这种东西多一点科学，少一点宗教，所以这是我感到兴奋的，有点像是对这些东西什么时候有意义进行更大的讨论，是的，我认为我们都会更好。

路易斯:好吧，期待看到事情如何发展。

伊森:是啊，我也是。

路易斯:非常感谢你来参加我们的节目并谈论这个话题。就像我说的，自从我看到波士顿环球报的设计出来后，我很想有机会和你聊一聊。祝贺伟大的工作这是一个了不起的网站，我真的希望它是成功的，并激发一波增加响应。

伊森:(笑)那太棒了。

**路易:**在各大网站。

伊森:啊，谢谢，路易斯，是的，很高兴来到这里。

路易:绝对是。如果人们想在你的博客或推特上关注你。

是的，我在推特上的名字是[@哔声](http://twitter.com/Beep)，我在那里发誓；我最近也在 [@RWD](http://twitter.com/RWD) 开了一个响应式网页设计 Twitter 账户，我讨厌这个缩写，但它很好也很简短，我的作品集是[Ethanmarcotte.com](http://Ethanmarcotte.com/)，所以我很乐意收到你们的来信。

路易斯:好的，再次感谢你，祝你愉快。

伊森:非常感谢你让我四处逛逛，我真的很感激。

路易斯:嘿，一点也不担心。

感谢您收听本周的 SitePoint 播客。我很想听听你对今天节目的看法，所以如果你有任何想法或建议，只要去[sitepoint.com/podcast](https://www.sitepoint.com/podcast/)就可以了，你可以对今天的节目发表评论，你也可以下载或订阅我们以前的任何一集来自动观看节目。你可以在 Twitter 上关注 SitePoint[@ sitepointdotcom](http://twitter.com/sitepointdotcom)，那是 SitePoint d-o-t-c-o-m，你也可以在 Twitter 上关注我 [@rssaddict](http://twitter.com/rssaddict) 。本周的节目由卡恩·布罗德制作，我是路易斯·西蒙诺。感谢收听，再见。

迈克·梅拉的主题音乐。

感谢收听！请随时让我们知道我们做得怎么样，或者使用下面的评论栏继续讨论。

## 分享这篇文章