# Adams 和 Clarke 谈 CSS、W3C 和设计

> 原文：<https://www.sitepoint.com/css-w3c-design/>

在悉尼举行的 2007 年 Web Directions South 大会上，我遇到了 SitePoint 的作者卡梅隆·亚当斯([themaninblue.com](http://www.themaninblue.com))和著名设计师安迪·克拉克([stuffandnonsense.co.uk](http://www.stuffandnonsense.co.uk))，他们都在会上发了言。如果你愿意，你可以[听这个采访](https://media.sitepoint.com/presentations/2007-09-28_wds07andycam.mp3)。

你好。我是来自 SitePoint 的凯文·杨克，我现在在悉尼的 2007 年南方网站。今天早上，两位演讲者加入了我的行列:Cameron Adams，穿蓝衣服的人，他是三次 SitePoint 的作者，最近与人合著了简单的 JavaScript，还参与了 CSS 的艺术科学 T4 和 JavaScript 选集 T3。著名设计师安迪·克拉克也加入了我的行列，安迪经营着自己的设计公司 Stuff &废话，最近写了《超越 CSS 》,这本咖啡桌上的书应该放在每个设计师的桌子上。伙计们，欢迎。

安迪·克拉克:嗨！

卡梅隆·亚当斯:谢谢你，凯文！

安迪，你昨天的演讲从漫画书的世界中获得了灵感，或许他们可以教给我们一些关于网页设计的知识。我注意到演讲中明显缺少的东西是排版。排版——至少在我这个漫画迷看来——在漫画艺术中扮演着重要角色。这是有意的疏忽吗？它是网络字体技术状态的反映吗？

安迪·克拉克:我认为这是一个有趣的问题。有一个大问题围绕着网络上的字体，以及网络上的排版。但是具体回答你关于演讲的问题，真的，只有 45 分钟，所以，为了把所有东西都弄懂——我可以和你连续几天不间断地谈论漫画书，只要我们有足够的咖啡——

我想这将是另一个播客。

安迪·克拉克:那将是另一个播客。所以，真的，这是一个问题，你知道，什么该放进去，什么该留出来。

是的。

安迪·克拉克:而且，当你把这些演讲放在一起时，你总是会意识到听众是什么类型的人，他们是网页设计师还是开发人员。对于一次会议，这与一次研讨会不同，对于一次会议，你总是意识到你不希望在某个特定的问题上过于深入…

**SP:对。**

**安迪·克拉克:**……就设计细节和排版而言，这是一条特别的路，所以，尽量保持它的一般性。

嗯。嗯，如果我们能具体一秒钟，我想像尼尔·盖曼的“睡魔”系列，例如，偶尔使用印刷效果很好。我知道你在自己的网站上经常使用 sIFR 的方法来获得一些独特的字体，如果这样做有效的话。你认为，只是随口说说，从我们在漫画中看到的排版中可以学到什么吗？

**安迪·克拉克:**我认为，正如我昨天谈到的，字体的布局方式肯定是一种可以帮助引导读者浏览页面的方式…

是的。

**安迪·克拉克:**但是围绕 CSS 已经有了很多讨论，特别是在自定义字体和导入字体方面，以及我们可能在未来使可用字体的选择范围更广的方式。

嗯。

安迪·克拉克:但是我总是小心谨慎。我们不想回到那种一切都只是对你大喊大叫的阶段。仅仅因为我们有很多字体，并不意味着我们必须使用它们。(轻笑)

卡梅隆·亚当斯:安迪，这也意味着你提倡更多地使用漫画吗？

安迪·克拉克: Comic Sans 是我最喜欢的字体之一，我希望它能在几个项目中出现。

**卡梅隆·亚当斯:**你先在这里听到的。

卡梅隆，你谈了很多——至少对我来说——关于成熟的 JavaScript 库和网络上其他高级 UI 技术提供的可能性，特别是关于给用户一个真正可定制的用户体验。现在，你提到这给你的设计工作增加了限制，并且使得建立一个设计良好的工作网站变得更加困难。作为一名像你这样地位的自由开发人员，你有多少机会从事这些值得你付出额外努力的特殊项目？

**卡梅伦·亚当斯:**他们非常少，但是我认为，你知道，当你在这个行业工作并为自己赢得名声时，你可以选择你实际上要去做的项目，而不仅仅是为了钱去做项目，而且……我越来越多地发现，我只是在等待合适的客户过来说，“这是我想做的事情。你能做到吗？”

是的。

卡梅隆·亚当斯:这是一个很好的位置，因为，你知道，没有多少人能做到这一点，但是……是的，你必须坐在那里等他们。

从推动设计的角度来看，最近有没有一个网站让你非常投入？

卡梅隆·亚当斯:啊，是的，有一个。我真的不能谈论它。(轻笑)

**SP:哦！嗯…你先在这里听到的！嗯，是的，期待看到这一点。**

卡梅隆·亚当斯:是的，那是一个非常有趣的项目。是为了一个非常棒的客户。

你在昨天的演讲中展示了澳大利亚白页网站，作为一个可以适应不同浏览器大小的网站的例子。据你所知——我的意思是，我很惊讶这样一个主流网站会采用这样一种，我猜，冒险的方法。

卡梅隆·亚当斯:是的。

**SP:你是如何找到的——你是否不得不扭转任何局面才能得到它—**

卡梅隆·亚当斯:不，Sensis 是澳大利亚真正伟大的网络公司之一。他们真的在推进尖端技术。有一件事 Sensis 真的很不擅长，那就是暴露他们实际上在做什么。

是的！

**卡梅伦·亚当斯:**他们有一大堆产品从未面世，因为他们正在做的东西太多了，都被其他一些东西吞噬了，你知道……黄页和白页营销。

**SP:爽。**

**卡梅伦·亚当斯:**但是，让 Sensis 参与这种事情真的很容易，因为他们那里有一些非常棒的人，所以如果你只是和他们聊天，然后说，“嘿，这是你可以做的很酷的事情，”他们真的很乐意尝试。

SP:很高兴听到这个消息。我想问一下你们两位，昨天分别听了你们的演讲，听起来好像你们都在从非常不同的方向看网页设计。安迪，你似乎把它当作一门艺术，你从头到尾都在创造一种体验，寻找一种既能满足客户需求又能为网站访问者提供有用和有吸引力的体验的愿景，而卡梅隆，听你的演讲，你实际上是在谈论让用户建立自己的体验，也许是一种新的网页设计方法。当你着手任何一个给定的项目时，这些方法是否会产生分歧，或者对于任何一个项目来说，有没有一个合适的折中方案？

卡梅隆·亚当斯:我不认为他们有冲突。我认为他们彼此非常合适。他们都在推动同一点，即尽可能为用户提供最佳体验。根据您正在进行的项目，这两种方法都可行。你只需要看看用户需要什么，他们真正想从网站上得到什么，无论它是一个精心设计的网站，还是一个你可以获得更多互动的网站。

嗯，安迪？

安迪·克拉克:有时我认为人们会孤立地思考网页设计。当很多人谈论可用性的时候，它就像几乎成为一种艺术形式或者一门科学一样，他们有时会忘记从网络之外的其他类型的媒体中寻找灵感。与此同时，人们——在日常生活中，他们会接触到杂志、漫画书、报纸、电影和所有其他东西。对我来说，用户体验的一部分是品牌。你知道，品牌非常非常重要，品牌是体验的一部分。所以实际上，得到一些能让人们兴奋或震惊的东西，或者只是让他们坐下来说，“哦！这不是我所期望的”，都可以成为品牌体验的一部分。所以现在这些事情，它们并不矛盾，因为在一天结束的时候，我们实际上试图从头到尾设计一个体验。

**SP: Mm.**

安迪·克拉克:当人们谈论网站设计与可用性的关系时，我有时会感到沮丧，好像尽快从 A 点到达 B 点很重要。对我来说，重要的是中间发生了什么。你知道，这就是旅程，这就是你试图告诉人们的故事，从他们第一次登录到他们实际结束的时候，例如，注册或购买产品。所以，我认为我们可以在设计中引入很多东西，让人们稍作休息，让他们潜在地思考他们所看到的，而不是让实际的设计本身变得透明，这样使用网站的唯一功能就是完成任何任务。

**SP:对。**

**卡梅伦·亚当斯:**可能会把更多的价值放在 it 上，而不仅仅是投资回报率，着眼于底线。

好吧。安迪，昨天在你的演讲结束时，你宣布了 CSS 项目，我想也许我应该给你一个机会，让你在那里进行电梯推介。

安迪·克拉克:是的，我的意思是，这件事已经困扰我很久了。我一直是那些关注 CSS 工作组内即将发生的事情或正在发生的事情的设计师之一，有点像是从外部扔石头的人，也是那些抱怨事情进展缓慢、行动似乎太少以及我们在未来某个时间点可能看到的解决方案可能不完整的人之一。然后我很幸运地在去年被邀请加入工作组。

我很惭愧地说，就实际贡献而言，我什么也没做。但是——原因之一——我的意思是 a)我很忙。但另一个原因是，实际上从内部看事情让我意识到，不仅从工作组的角度来看，要让这些事情发生是多么困难，而且从一个局外人的角度来看，在一个项目进行到一半的时候，真正理解正在谈论的到底是什么。

工作组的人非常好，他们工作非常努力，但这并不意味着他们知道我们需要发生的一切。它们当然可能没有相同的优先级——如果你昨天看了伯特·波斯关于 CSS3 愿望清单上所有东西的演讲，你知道，MathML、幻灯片格式和所有这类东西都在里面……好吧，让我们停止“一切”的废话，专注于可能完成的几件事情。

所以，实际上，我们的想法是——看到工作组内部缺乏真正的设计师知识，也看到从外部与工作组沟通是多么困难——我的意思是，他们一直通过邮件列表等获得大量的建议。，但实际上与工作组以及 W3C 的任何人沟通都是相当困难的。没有这层透明性。我们真的不知道外面在讨论什么，优先级是什么，事情有多接近，例如，讨论的事情是否基于政治或经济或浏览器供应商的因素而被接受或拒绝。浏览器厂商会不会想，“我们不想实现这个功能，因为它会让我们花很多钱在浏览器中实现。”你知道，我们想知道原因。

所以，真的，a)我对缺乏进展感到沮丧，显然，像许多其他人一样，但也因为站在外面扔石头真的没有任何好处。我认为，更好的办法是，实际上尝试找到某种方式——也许这不是正确的方式，但某种方式——在这种方式中，我们可以利用在 CSS3 方面已经完成的工作——并且有许多工作正在进行；有很多草稿已经相当成熟，而且有一群在设计和 CSS 方面非常非常有经验的人，在脚本技术方面也是如此，因为很多时候我们使用脚本来支撑 CSS 中不可能的东西，比如说，看看多栏模块，然后说，“这是一个成熟的草稿。已经在 Firefox 中部分实现了，但是坏了。这很容易解决。我们想做的事情是什么？我们还需要 CSS 的哪些方面？”

我给你举个例子。多列模块创建多列，但是您不能设置这些列的样式。选择器模块允许您非常精确地选择特定的行，例如，包含第 n 个子代的表行。所以我可以用第 n 个子选择器来设计表格的前六行，或者第九、十九和二十九行。但是没有什么可以让我对多列模块生成的替换列进行样式化。

嗯。

安迪·克拉克:这样的小东西不见了。还有经典排版的元素:标题在栏内工作的方式，图像可能跨越多个栏的方式。这是设计师想要和需要的东西，但问题是，工作组没有时间或经验，真的，来制作适当的例子，说明一些东西应该如何工作。

**SP:对。**

**安迪·克拉克:**这就是召集一群人来完成这项工作背后的想法，或者至少可以做到这一点，这样当我们回到工作组的时候——我也不相信会花太多的时间——回到他们身边，给他们一些不仅是人类可读的东西——因为如果你曾经试图阅读一个规范，这是非常困难的，因为它们不是为人类写的，它们是为计算机科学家写的，它们是为实现者、浏览器制造者写的；它们不是作为说明书写给设计极客的…

他们应该是这样的，但是……是的。

安迪·克拉克:完全正确。所以，让我们回到一些非常清晰，非常精确的东西上来，说，“这就是我们想要的工作方式，”伴随着一些非常好的，真实的，图形化的例子。

**SP: Mm.**

**安迪·克拉克:**潜在地，代码的开始——HTML 和 CSS——可能是测试套件的基础。把事情做完。

表面上，这可能被视为对 W3C 在 CSS 上的工作的批评或攻击，但实际上你可能最终成为他们的救星，这正是他们所需要的——这种将正在进行的工作和存在的可能性的信息传播出去的方式。

安迪·克拉克:我确实认为当前的模式已经崩溃，原因有几个。第一点，也可能是最有争议的一点是，正如我所说的，工作组是由非常优秀、勤奋、善良的人组成的，但他们中的大多数是 W3C 成员的代表，这些成员是数百万美元的公司，其中大部分肯定是 CSS 工作组的成员。特别是，是否应该由浏览器制造商来决定我们的工具在未来会是什么样，他们认为应该是这样的。

我认为应该由我们来决定，W3C 来批准，解决实施问题——这并不是说——工作组中的实施者应该看着事情，然后他们说，“你知道吗？事实上，我们不能这样做，因为计算机不够强大，但我们可以这样做。”让对话继续下去。这很重要。然后浏览器制造商要么实现，要么不实现。但是，目前决定什么进入、沟通和透明度的模式被打破了。所以这只是一个想法…

好吧。

安迪·克拉克:……至于我们能解决的方法。

嗯，我很期待看到那部作品！卡梅隆和安迪在 Web Directions South 2007 上的演讲将会在 webdirections.org 的[播出。我鼓励你去看看，听听他们在这里谈论的更多内容。谢谢你们的加入，伙计们！](http://www.webdirections.org)

安迪·克拉克:这是我的荣幸。

卡梅隆·亚当斯:谢谢你，凯文。

## 分享这篇文章