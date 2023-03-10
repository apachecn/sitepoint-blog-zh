# 萨斯:当我们谈论所有语法棒的事情时发生了什么

> 原文：<https://www.sitepoint.com/we-talked-all-things-syntactically-awesome/>

今天早上，我们的设计师和 CSS 魔术师 Alex Walker 成为我们[聊天室](https://experts.learnable.com/ "Chatroom")的中心人物，在最新的*与专家对话*环节中侃侃而谈。我们聊了从 mixin 库到将 Sass 整合到您的工作流中的一切。时间一小时一小时地过去了，从中获得了一些好的资源——如果你没有时间仔细阅读文字记录，你会发现下面列出了这些资源。

首先，Alex 用 Sass
[CSS3 Pong with scoring](https://www.sitepoint.com/css3-pong-insane-things-to-do-with-css/ "CSS Pong")
[写了一些很酷的 CSS 东西，CSS 中圣诞节的故事](https://xmas.sitepoint.com/story/css#day/1 "The Story of Xmas in CSS")

一些有用的信息: [Sass vs LESS](http://css-tricks.com/sass-vs-less/ "Sass vs LESS")–预处理器的比较
[使用 CSSDeck 的 Sass 演示](http://cssdeck.com/labs/sass-demo "Sass demo")Sulieman lead bitter on[从 Sass 开始](http://www.youtube.com/watch?v=cCizAq_XXb0 "Starting out with Sass")

和一些方便的工具:
[Scout](http://mhs.github.io/scout-app/ "Scout")-一个运行 Sass 和 Compass
Bourbon 的跨平台应用程序-一个用于 Sass
[Prepros](http://alphapixels.com/prepros/ "Prepros") 的 mixin 库-另一个预处理程序
[LiveReload](http://livereload.com/ "LiveReload")-在浏览器中重新加载预处理后的代码而无需刷新
[SMACSS](http://smacss.com/ "SMACSS")-CSS 的可扩展和模块化架构

[推出成少](https://learnable.com/courses/launch-into-less-2764 "Launch into LESS")上可学

如果您因为不知道而错过了今天的会议，您可以在此注册接收未来会议的电子邮件提醒。
下周是[Russ Weakley](https://experts.learnable.com/ "Talk with the Experts")的响应式网页设计。

如果你想知道今天早上到底发生了什么，这里有一份完整的记录:

【21:32】<鹰派>有人已经有一些 Sass 经验了吗？

【21:32】<费尔南德斯>我不要。

【21:32】<特里>否

【21:32】<尼克尼维姆>非我

【21:32】<Jan>什么是 Sass？？抱歉，我不知道。

我的经验比较少……少跟人顶嘴:)

我对任何 css 预处理程序都没有经验。

【21:33】<香农>在 WordPress 中使用 sass 有什么影响？

【21:33】<杰西温>我有萨斯的经验，但我认为我还没有发挥出它的全部潜力。

【21:33】<香侬>刚开始学萨斯

<alexm walker>好的，CSS 的一个问题是它的发展非常缓慢，尽管 web 已经以极快的速度发展了多年。

【21:34】<alexm walker>SASS 和其他预处理程序让我们编写类似 CSS 的代码，更高级，坦率地说，也更酷。

【21:35】

【21:35】<大卫尼>霍克——为了排队:)也许快速比较一下萨斯和少萨斯的利弊会更好

【21:36】<霍克> NP 大卫尼

【21:36】<alexm walker>它让我们做更聪明的事情，而不必等待 W3C 和浏览器制造商赶上来

【21:37】<大卫尼>alexm walker——Sass 处理/编译下来的是服务器端还是客户端？(我知道 LESS 提供了一个 js 客户端编译器，但它对生产网站来说是不允许的)

【21:37】<霍克>你能举一个‘更聪明的东西’的例子吗

【21:37】<费尔南多·巴索>我第二个问题。

【21:38】你是把它们放在自己的部分中，还是用相关的选择器把它们分散在部分中？

【21:38】<鹰派>欢迎刚刚加入的你们。Alex 正在解释预处理器的基本功能

【21:38】<大卫尼> +1 为杰瑟温·RWD 提问

【21:39】<alexmwalker>DavidNY，SASS 最初是一种红宝石，但最近它被分解成许多不同的环境。我发现最简单的开始方式是使用客户端应用程序。

【21:39】<alexm walker>[http://mhs.github.io/scout-app/](http://mhs.github.io/scout-app/ )

【21:39】<alexm walker>这是 SCout。使用是免费的，而且非常容易上手

【21:39】<Jan>好的，谢谢

【21:39】<森>(我喜欢 codekit) :)

【21:39】<alexm walker>是个 Adobe Air app，所以有点迟钝..

【21:41】<alexm walker>但是它可以让你为你的 sass 文件选择一个文件夹，并自动将它们写到附近标记为 CSS 的文件夹中。

【21:41】<杰瑟温>刚开始的时候，我用 Scout 试了一下，但没能让它按照我想要的方式工作。我一头扎进命令行，意识到如果你不惊慌，这并不难。

【21:41】<大卫尼>我去看看 scout——我听说过 compass，code kit——所有这些似乎都有 Sass“内置”

【21:41】

【21:41】<大卫尼>*搞定

【21:41】<alexm walker>Codekit 真的不错。这是 25 美元，但值得一花

【21:41】<johnlacey>这样就可以用变量跟 Sass 了？

【21:42】<杰西温>我们在本地编译，所以 css 已经为浏览器预编译好了。

【21:42】<alexm walker>johnlacey 是的，那是第一次大获全胜的比赛之一

【21:43】<alexm walker>DavidNY，当我们说客户端 SASS 时，我指的是您在工作时在本地笔记本电脑上编译 CSS。最终用户只能得到完成的 CSS

【21:44】<alexm walker>LESS 在最终用户 PC 上编译

你好，最近我开始少用了，我觉得它很好，它的语法也不难学，现在，我的问题是，我应该少用 SASS over 吗？

【21:44】<alexmwalker>(虽然现在已经不要了)

【21:44】<大卫尼>没错——至少可以选择通过 JS 在终端用户的机器上进行更少的编译，这是我不想做的

【21:44】<大卫尼>感谢澄清回复:萨斯

【21:45】<alexm walker>好了萨斯 vs 少问。在过去，较少依赖客户端 JS，我不喜欢这样的想法。

【21:45】<大卫尼> ^字:)

【21:46】<动物>嗨

【21:46】<alexm walker>现在 LESS 可以在自己的机器上编译了——我觉得你得装 NodeJS，把 LESS 装成节点包

【21:46】<dimlo 305>没错

【21:46】<alexm walker>这是个不错的解决方案。

【21:47】<alexm walker>Less 实际上有一些更整洁的语法，所以现在完全没有理由不选择 Less。

【21:47】<alexm walker>甚至手写笔如果你喜欢的话

【21:47】

【21:48】<alexm walker>他们都做类似的事情。我们在 Learnable 上使用 rails，所以 Sass 是一个简单的选择。

【21:49】<BioC>好的，那么指南针 VS 波旁呢？如果我问这个问题还不算太晚的话:)

【21:49】<alexm walker>所以，Sass 有两个真正不错的‘mixins’插件库——Compass 和 Bourbon。

【21:49】<大卫尼>人们可能会觉得这是一本好书:[http://css-tricks.com/sass-vs-less/](http://css-tricks.com/sass-vs-less/)

【21:50】<alexm walker>这些库给了你很多我认为非常强大的预写特性

【21:50】<dimlo 305>谢谢！

【21:50】<alexm walker>[http://bourbon.io/](http://bourbon.io/)

【21:50】<洛里奥>波旁威士忌和指南针有什么区别？

【21:50】<霍克>感谢大卫尼的链接——如果其他人有资源，请联系我们，我会在做笔录时列出清单

【21:50】<alexmwalker>波旁是我个人最喜欢的

【21:50】<洛里奥>为什么？

【21:50】

【21:51】<alexm walker>指南针很酷，但它很大，在我看来可能有点夸张。波旁威士忌正好有我需要的东西，没有别的了。

【21:52】<洛里奥>波旁威士忌会像指南针一样注意错误吗？

【21:52】<杰瑟温>所以有了波本威士忌，你会在每个网站上安装它？你可以直接复制 mixin 文件，而不用每次都通过命令行安装吗？

【21:53】<BioC>好的，它支持任何响应式布局框架吗，比如 Sasy？(我最近才开始用，已经喜欢上了)

【21:53】<alexm walker>有了 SASS，你往往会为你写了很多的 CSSS 片段写一些小的快捷方式。许多 CSS3 属性的前缀可能是一个很好的例子……

【21:54】<alexm walker>Bourbon(和 Compass)是已经为你写了很多那些快捷方式(或 mixins)的库

【21:54】<鹰派>欢迎刚刚加入的你们。:)我们目前正在谈论 Mixin 库

【21:54】<鹰派>随时欢迎提问，我会把问题排好队，直到我们有空档

【21:55】<鹰派>对于任何不知道的人，你可以@标记人来引起他们的注意

【21:55】<alexm walker>jessirwin——在 rails 中，您可以在每个新项目上使用一个命令来设置 SASS 和 Bourbon。Codekit 附带指南针和波旁酒捆绑在里面。

【21:55】<洛里奥> @BioC 你是说苏西吗？

【21:56】

【21:56】<alexm walker>有了 Scout，每次只需将文件复制到正确的位置

【21:57】

【21:57】<alexm walker>提到的

【21:57】

【21:58】<苏莱曼>抱歉刚回来，抓到将军聊到此为止。

【21:58】<alexm walker>有了大网站，管理一堆较小的 CSS 文件就容易多了..可能是' layout.css '和 text.css 等

【21:58】<苏莱曼>来自 Zurb 使用 Sass too 的 awesome 基金会

【21:58】<BioC>johnlacey 是的，但是你启动了一个命令行程序，比如 sass 或 compass，它会根据你的变化，重新编译你的脚本

那些会是部分音吗？

【21:59】<alexm walker>但是你最好不要为每个页面向服务器发出 25 个 CSS 文件请求..

<alexm walker>SASS 让你写很多小文件，然后把它们粘贴成一个大文件发送给用户

【22:00】<比奥奇>约翰莱西相信我，起初它对我来说也是一个负担，但现在，没有它我无法工作，它变得轻松多了！

【22:00】<Suleiman>那么，模块化 SMACSS 方法很棒吗？

【22:00】<大卫尼> +1 for SMACSS :)

【22:00】<大卫尼>我相信 Snook rec 在用 Sass

【22:00】<alexmwalker>Suleiman 到底！非常适合这种方法

【22:01】<alexm walker>johnlacey yes you do

【22:02】<Suleiman>我在基层遇到的主要问题是正确使用@extend 来保持代码“干燥”

【22:02】

【22:02】<alexm walker>[http://cssdeck.com/labs/sass-demo](http://cssdeck.com/labs/sass-demo)

【22:02】<alexm walker>这是一个非常简单的例子，但是您可以随意使用它

这真的是你最喜欢的颜色吗？

【22:04】<大卫尼>嘿

【22:04】<alexm walker>呵呵呵..嗯……这是最初的 SitePoint orange，所以可以说它永远留在了我的脑海里

【22:04】<杰瑟温>哈哈！

【22:04】<大卫尼>所以这就是变数

【22:04】<大卫尼>遗产怎么样——我忘了 sass / less 术语

【22:05】<大卫尼>但基本上，一个类继承了另一个类的所有风格，并以此为基础进行构建

【22:05】<alexm walker>正是。因此，如果我们在 100 个样式表中使用这个变量，然后改变徽标的颜色，我们可以通过几次击键来更新整个网站

【22:05】<大卫尼>耶，爱那个

【22:05】<大卫尼>总有一天会出现在 CSS 里:)

【22:06】<alexm walker>我不认为变量会影响到真正的 CSS

【22:06】<大卫尼>嘘

【22:06】<alexm walker>因为他们会弄坏老浏览器

【22:06】<johnlacey>那么去 Sass 是如何改变你的整体工作流程的呢？

【22:06】<罗伯特>需要添加$base-color 才能运行

【22:07】<alexm walker>johnlacey 我开始用它来简化 CSS 动画的代码——圣诞节的东西，甚至 CSS Pong。

【22:08】<alexm walker>老实说，如果没有预处理器的帮助，我永远也写不出那些东西

【22:08】<霍克>大家看过 Alex 的圣诞& Pong 作品吗？

【22:08】<苏莱曼>没有

【22:08】<霍克>如果没有我也能挖出来

【22:08】<鹰派>挂住

【22:08】<alexm walker>浏览器前缀的东西会让它变得太难了

【22:09】<苏莱曼>指南针也很神奇:)

【22:09】

【22:09】<alexmwalker>正是，苏莱曼。

【22:10】<霍克>苏莱曼[https://www . site point . com/css3-pong-pine-things-to-do-with-CSS/](https://www.sitepoint.com/css3-pong-insane-things-to-do-with-css/)

【22:10】

【22:10】<苏莱曼>感谢霍克:)

【22:10】听起来很有效率。

【22:11】<alexm walker>好的一面是，你可以将普通的 CSS 语法写入你的 SASS 文件中，这样就可以很好地工作了。然后，如果你逐渐开始利用 SASS，你会看到越来越多的好处。

【22:11】<霍克>苏莱曼[https://xmas.sitepoint.com/story/css#day/1](https://xmas.sitepoint.com/story/css#day/1)

【22:11】<大卫尼【Sass mixins 和 in LESS 一样吗？

【22:11】<alexm walker>所以它不会强迫你开始学习一门全新的语言。

【22:12】<alexm walker>一个要小心的“陷阱”..

【22:12】..

【22:13】<杰瑟温>你花了多长时间来准备圣诞礼物？

【22:13】<alexm walker>如果你写更多的 SASS，你的改变将覆盖他/她的 CSS

【22:14】<alexm walker>过了一会儿。例如，火车需要几个小时的调整。

【22:14】<alexm walker>要不是萨斯，我想这一切都不可能

【22:15】<Suleiman>我做了我的第一个演讲，它是关于 Sass 的——人们可以在这里查看—[http://www.youtube.com/watch?v=cCizAq_XXb0](http://www.youtube.com/watch?v=cCizAq_XXb0)在头脑中，我很紧张，有一些技术问题，我不能拿出我的笔记:P 但是它是针对那些对它完全陌生的人的。

【22:15】<alexm walker>太多代码，太多浏览器不一致需要修复的 bug

【22:15】<苏莱曼>霍克那乓的说大话出类拔萃

【22:16】<鹰派>的确是

【22:16】<alexm walker>也不确定你们有多少人用的是 Live Reload？[http://livereload.com/](http://livereload.com/)

【22:16】<alexm walker>这是 10 美元，包括 SASS 和 less 处理。

【22:16】

【22:16】<alexm walker>它会在你每次更改 CSS 或 HTML 时自动重新加载你的浏览器

【22:17】<alexm walker>jessir win 真扫兴。当你让它工作的时候，它是杀手。没有它我无法工作

【22:18】<杰瑟温【livereload 和 codekit 有什么区别？

【22:19】<霍克>嘿，大家好，我们还有大约 10 分钟的时间，所以如果你们有问题，一定要把它们放在一起

【22:19】..它还有一些其他的技巧。总的来说，这是一个设计更好的应用程序，但它们并没有什么不同

【22:19】<alexm walker>我自己都有

【22:19】<大卫尼>霍克是 sitepoint 编写 Sass 课程或书籍等吗？

【22:19】<大卫尼> :)

【22:20】<Suleiman>好的，alexmwalker 你能告诉我如何最大限度地利用@extend 功能吗，因为我还没搞清楚

【22:20】<alexm walker>大卫尼我很可能就参与了这样的课程；)

【22:20】<洛里奥>伟大的

【22:21】<苏莱曼>我用@extend ul.classname li -？

【22:22】

【22:22】<霍克>嘿，卢克，我们马上就要结束了，如果你有问题，就直接提问吧:)

【22:22】<alexmwalker>Suleiman 到目前为止，我唯一一次尝试使用@extend 时，我发现我写的代码有点傻..重复我自己。所以我重组了它。

【22:23】<alexm walker>但也许我们都在延伸中错过了美丽。我还没完全明白

【22:23】<大卫尼> extend 与 mixins 不同？

【22:23】<大卫尼>例如

【22:23】<【davidney】>。BTN { }

【22:23】<Suleiman>alexm walker 我就是这样，我知道这很好，但我就是搞不清楚:P

【22:24】<大卫尼>。btin-extend。btn} /*继承所有。btn 规则

【22:24】<大卫尼>不管叫什么:)对我来说这是更强大的功能之一

【22:24】<alexm walker>mixin 是全 CSS 规则。扩展看起来像是更小的片段。

【22:25】<Suleiman>DavidNY——假设你有一个按钮的一些样式，你可以@扩展它来做别的。这并不是复制编译后的 CSS 的所有样式——item 1，item2 {style in here}

【22:25】<jessirwin>我发现当我不能将类名添加到 html 中时，我会使用@extend。

【22:25】<改为苏莱曼>或者 item 1 { code }—then item 2 { repeated code }

【22:26】<杰瑟温>在一个响应式网站中，应对媒体询问的最佳方式是什么？你是把它们放在自己的部分中，还是用相关的选择器把它们分散在部分中？

【22:26】<alexm walker>在我的例子中，我最终将这些类组合在一起…框，。侧栏{ etc etc}

【22:26】<alexm walker>得到同样的结果

【22:27】<杰瑟温>我用它来把东西集中在同一个局部。

【22:27】<大卫尼>啊。好的，谢谢苏莱曼——虽然不确定我想在哪里做。为什么两个类会有相同的一套样式

【22:27】<alexm walker>我确实把它们放在主文档的末尾。尽管在这个问题上有几个学派。

【22:27】<Suleiman>DavidNY 可以是一段引号和一些其他文本。您可以在@extend 之后添加额外的样式

【22:27】<苏莱曼>真的可能是任何东西

【22:28】<大卫尼>我得试试看

【22:28】<特里>感谢你让我加入——我真的相信潜移默化！

【22:28】<alexm walker>代码要点:[https://gist.github.com/5818705](https://gist.github.com/5818705)

【22:28】<Suleiman>Sass 很棒，因为你可以只使用标准的普通 CSS，并随着你的进展添加一些小的内容。我喜欢那个

【22:29】<alexm walker>哇，我还不知道这个可以这样！

再过几分钟，我就要放了亚历克斯，让他继续他的一天。还有最后的问题吗？

【22:29】<alexm walker>那是 learnable 的主 SASS 文件的底部

【22:29】<大卫尼>杰西温——如果你遵循 SMACSS 方法，我认为推荐的媒体查询方法是将它们放在模块(“选择器”)中，而不是放在它们自己的部分中(例如“mobile.css”或“480 px . CSS”)

你就是这样导入分音的吗？

【22:29】<苏莱曼>alexm walker——讽刺:/

【22:30】<alexm walker>除了特里比特；)

【22:30】<alexm walker>lorio 这是

【22:31】

【22:31】<洛里奥>一本关于 smaccs 的好教程？

【22:31】<大卫尼>嗯，还有网站和书——smacss.com

【22:31】每个断点的 scss 文件？

【22:31】<洛里奥>好的谢谢。来看一看

【22:33】<大卫尼>gotta bolt——感谢霍克和 alexmwalker 以及所有人:)有文字记录要分享吗？

【22:33】<alexm walker>我认为你需要一套能在所有尺寸的设备上运行的基本风格..然后我用每个尺寸的导入部分来扩充这些样式

【22:34】<苏莱曼>感谢 alexmwalker :)

【22:34】<alexm walker>非常荣幸，感谢大家的到来，给了我一些话题

【22:35】<霍克>今天晚些时候我会贴出一份文字记录

【22:35】<杰瑟温>感谢阿列克斯·沃克和霍克

【22:35】<霍克>下周加入我们，与 Russ Weakley 一起探讨响应式网页设计

## 分享这篇文章