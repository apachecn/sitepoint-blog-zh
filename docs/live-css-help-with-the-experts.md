# 在专家的 CSS 帮助下，发生了什么

> 原文：<https://www.sitepoint.com/live-css-help-with-the-experts/>

一年多来，我一直在各种不同的平台上与专家进行实时聊天，但是今天我们决定尝试一些不同的东西。我们邀请您向经验丰富的 CSS 设计和开发人员 Alex Walker 询问您遇到的所有样式问题。Alex 在我们闪亮的新定制聊天室帮助你解决棘手的问题和混乱的代码。会议很忙，但亚历克斯处理得很沉着，我们设法在一个小时的会议中涵盖了大量内容。

如果你错过了，下面是一个资源列表(和一些非常惊人的 CSS 例子),后面是一个完整的文字记录。

[SitePoint CSS 引用](https://reference.sitepoint.com/css/ "SitePoint CSS Reference")

@纽伯格
的 CSS 动画的一个很棒的例子；基思·克拉克的一个聪明的 [CSS3 实验](http://www.keithclark.co.uk/labs/css3-fps/ "CSS3 experiment")

什么是[少](http://lesscss.org/ "LESS")和[萨斯](http://sass-lang.com/ "Sass")？
这里有一篇文章[比较了这两者。](http://css-tricks.com/sass-vs-less/ "SASS vs LESS")

[启动 LESS](https://learnable.com/courses/launch-into-less-2764 "LESS course on Learnable") 关于可学的新课程
要了解更多关于 Sass 的信息，请务必参加我们 6 月 19 日[与 Alex 在](https://experts.learnable.com/ "SASS session")即将举行的会议

[Scout](http://mhs.github.io/scout-app/ "Scout app") 是一个很棒的跨平台 SASS 应用程序(如果你想避免学习 Rails)
或者你可以试试 [CodeKit](http://incident57.com/codekit/ "CodeKit") 如果你在 Mac 上并且不介意支付少量费用的话

浏览器的 HTML5 就绪图

[CSS3 兼容性表](http://caniuse.com/ "Compatibility tables")

一篇关于[重置](http://csswizardry.com/2011/10/reset-restarted/  "Resets")的文章，也是[媒体询问](http://css-tricks.com/snippets/css/media-queries-for-standard-devices/ "Media queries")的好参考

最后，关于这些会议，你可以在这里查看[即将到来的日程](https://experts.learnable.com/ "Talk with the Experts")或者在这里注册[未来会议的电子邮件提醒](https://www.facebook.com/sitepoint/app_115462065200508 "Email reminders")。

如果你们想阅读今天会议的完整记录，这里是:

【21:19】<鹰派>迎宾亚历克斯

【21:19】<alexm walker>谢霍克。大家好。

【21:20】<alexm walker>含咖啡因准备摇滚

【21:23】<alexm walker>大家有没有看到@neoberg 的那个 Macbook air CSS 动画？

【21:23】<alexm walker>[http://cssdeck.com/labs/css-only-macbook-air](http://cssdeck.com/labs/css-only-macbook-air)

【21:23】<alexm walker>很酷的作品

【21:23】<鹰派>哇！那真是太棒了 alexmwalker

【21:24】<alexm walker>一张 png 换屏。一个苹果标志的 SVG。剩下的就是纯 css 了。

【21:25】<Toffy>如何在响应式设计中将多个分割水平居中

【21:26】<霍克>那么 alexmwalker，你准备好开球了吗？看起来 Toffy 提出了一个问题。

【21:26】<Toffy>不好意思没打招呼就发问题发的这么快

【21:26】<鹰派>无忧 Toffy :)

【21:27】<alexm walker>我是。你好，陶菲。所以你说的是，比如多个水平列？

【21:28】

【21:29】

【21:30】<杰瑞>托菲:div 垂直堆叠，每个都需要水平居中？

【21:30】<Toffy>它们没有堆叠

【21:30】<Toffy>挨着的

【21:30】<Toffy>如果我让它们浮动，就很难居中

【21:30】<alexm walker>是的，所以你在左右两边设置了自动边距？

【21:30】

【21:30】<马莫鲁>不就是用“绝对位置”的作品来创作吗？左:50% div 内-50%？我不太明白这个问题，但这是我经常做的事情

div 的宽度是固定的吗？

【21:31】

【21:31】<Toffy>但是想知道有没有别的办法

【21:31】<johnlacey>问:什么是 SASS 和 LESS？我们应该使用它们吗？

【21:31】<Toffy>div 不固定

【21:31】<Toffy>正因如此其难

【21:31】<alexm walker>啊，好吧，他们是屏幕对面的三个人？抓住你了

【21:31】<Toffy>是

【21:32】<Murali>你好，Alex，我有一个容器 div，里面有两个子容器(标题和主体)。容器有滚动条。我希望标题 div 应该固定在顶部，只有身体 div 应该滚动。

【21:32】<ralphm>如果你把它们放在一个容器里，你可以把它们设置成 display: inline-block，把容器设置成 text-align:center；

【21:32】<Toffy>ie7 中内嵌块工作吗？

【21:32】<穆拉利>样于[【http://cssdeck.com/labs/gomiazlb】](http://cssdeck.com/labs/gomiazlb)

【21:32】<穆拉利>可能吗？

什么决定了 div 的宽度？

【21:33】<alexm walker>所以我想我；试着塑造容器的形状，让它们均匀地填满容器(比如每个 33%)

【21:33】

【21:33】<约翰莱西>感谢霍克。

【21:33】

【21:34】<jcollado>霍克:现在正在回答的问题是什么？

【21:34】<ralphm>@ Toffy 不为块元素(如果我没记错的话)。但是 IE7 只有不到 1%的用户群。无论如何，如果你需要支持这个浏览器，只需要在容器的中间加上宽度和自动边距，并在其中浮动 div。

【21:34】<alexm walker>Hi @ johnlacey。好问题。

【21:35】<霍克> jcollado 我们刚刚跳入 johnlacey 的问题:问:什么是 SASS 和 LESS？我们应该使用它们吗？

【21:35】<jcollado>霍克:好的，谢谢

【21:36】<Paul>Toffy 通过将元素设置为 display:inline，然后添加 haslayout，可以让 inline-block 在 IE7 及以下版本中工作。参见 Sitepoint 参考中的注释

【21:36】如果你有问题要问亚历克斯，请随时提出来，我会把它们排好队

【21:36】<鹰派>仅供参考你也可以@标记人来引起他们的注意

【21:36】<alexm walker>它们都是对编写 CSS 的巨大改进。如果你习惯于编写 CSS，是的，你绝对应该考虑在你的工作流程中添加一个这样的预处理程序。

【21:36】<alexm walker>他们基本上让 CSS 做一些在帮助下不可能完成的事情。

【21:37】<霍克>约翰莱西——我不知道你是否注意到，我们刚刚在 Learnable 推出了一门更少的课程

【21:37】<约翰莱西>感谢@霍克

【21:37】<罗伯特姆>绝对是少了一个粉丝

【21:37】<RobertRM>虽然萨斯看起来更强大？

【21:37】<alexm walker>我是一个喜欢与人斗嘴的人，主要是因为我们在这里使用了很多 rails，而且它还带有草皮。从发展的角度来看，它也可能有更多的动力。

【21:38】<杰瑞>我对萨斯不熟悉。你能提供两者的简单比较吗？

【21:38】<Jason>Alex，标签在类前或类后的 CSS 选择器有什么区别？示例:h3.myclass {}和有什么区别？myclass h3 {}？

刚刚加入的朋友们，你们好。你可以随时提出要排队的问题。:)

【21:39】<alexm walker>我必须说 though Less 有一个更好、更整洁的语法。如果你从零开始，我认为你选择哪一个并不重要。

【21:40】<alexm walker>RobertRM 是的，我认为 SASS 和一些帮助 SASS 的库比 Less 更先进

【21:41】<Jerry 95>@ Jason 我知道你把问题贴给了 Alex，但我很确定那很简单。类前面的标签表示应用了该类的所有 h3，而标签后面的类表示指定了该标签的该类的所有子类。

【21:41】<Jerry 95>萨斯和少到底是什么？

【21:42】<alexm walker>杰森，是的，杰瑞基本上涵盖了这一点。

【21:42】<费戈尔>参见[http://lesscss.org/](http://lesscss.org/ and http://sass-lang.com/)和

【21:42】<mamoru>Jerry 95 想到 CSS 中的变量，说得真短

【21:42】<alexm walker>好吧，所以有很多人对 Sass 和 Less 感兴趣。

【21:43】<alexm walker>我会稍微讲一下那些。

【21:43】<Jerry 95>所以它就像 PHP(前超文本处理器)只与 CSS？

【21:43】<杰瑞>感谢费戈尔

【21:43】<霍克>谢费戈尔:)

【21:43】<mamoru>@ HAWK:这份笔录以后还会贴在其他地方吗？我真的应该去睡觉了，因为这里已经是午夜了:-)

【21:43】<alexm walker>CSS 是一种很酷的语言，但它在许多方面都有局限性，W3C 需要很长时间来改进。

【21:44】<马莫鲁> Jerry95 排序

【21:44】<杰伦>只是好奇:你们很多人都在用样板或者框架吗？我已经找了几个，但不知道我应该进一步调查哪一个

【21:44】<鹰派>是的 mamoru——今天晚些时候我会把它贴在 SitePoint 上。感谢加入我们。:)

【21:44】

【21:44】<alexm walker>Sass 和 LESS 是看起来很像 CSS 的语言，但是有强大的新功能。

【21:44】<mamoru>谢谢 HAWK，继续努力:-)如果我第二天有空，我会坐一整天聊天:-)

【21:45】<mamoru>@ Jay Twitter Bootstrap 和 littlesparkcvt 的 flatstrap

【21:45】

【21:45】<拉尔夫姆> @kcs 在将 CSS 与 LESS 或 SASS 混淆之前，我肯定会说了解 CSS 是如何工作的。

【21:45】

【21:47】

【21:47】<Jerry 95>XSLT 是否可以使用 SASS 或更少的代码，比如说，如果有一个标签是另一个标签的子标签，那么应用一些 CSS 规则，如果没有，应用另一个规则？

【21:48】<霍克>穆拉利——等事情平息下来后，我们会在最后回答你的问题。如果你不能留下来，给我发邮件:)

【21:48】我应该先学 JS 再学 jQuery 吗？还是反过来？有强烈推荐的资源/书籍吗？

【21:48】<kcs>感谢。拉尔夫姆。

<ScallioXTX>Jerry 95 你可以用 css 来做这些事，一点也不含糊。只需执行. class1 .class2，它将把 class2 的所有元素作为 class1 的子节点。深度不重要。

【21:48】<穆拉利> NP。我会等的。

【21:49】<Jay>@ John lace:我对这两者都没有太多的经验，但是据我所见，它们在上传到你的服务器之前被“编译”成有效的 css。所以最后，你只剩下纯粹的 css

【21:49】<alexm walker>johnlacey Sass 是在 Rails 上安装 Ruby 时内置的。但是有相当多的桌面应用程序可以监视你的文件夹，并在你每次保存时将你的 Sass 转换成 CSSS。

【21:49】<HAWK>欢迎刚刚加入的朋友们。随时提出问题。我们目前正在讨论 SASS 和 LESS，但其他问题将排队。

<Jerry 95>t 感谢 ScallioXTX。有人能再举一个例子说明少说话能做什么吗

【21:50】<bsteffl>@ mamoru 我听说 twitter bootstrap 不一定以正确的方式使用 html 元素，你有过这样的经历吗？

【21:50】<alexm walker>我推荐使用 Scout——它是免费的，几乎可以在所有系统上运行。Codekit 很不错，但是我觉得大概 50 美金左右

【21:50】<alexm walker>[http://mhs.github.io/scout-app/](http://mhs.github.io/scout-app/)

【21:51】<kkl>CodeKit 值得。

【21:51】<alexm walker>kkl 同意

<ScallioXTX>bsteffl 无论你在 HTML 中构建什么，总有人认为它是不对的。选择那些值得战斗的战斗。

【21:51】<RobertRM>Jerry 95 LESS 让你更快地编写 CSS，并且以一种更易维护的方式

【21:52】<ralphm>@ kkl jQuery 更容易上手使用 JS，但理想情况下，如果不是先学习 JS 的工作原理，至少也要晚一点。

【21:52】<霍克>可学网上有大量的 JavaScript 和 JQuery 资源:)

<ScallioXTX>我个人发现 LESS 离 CSS 太远了，最终结果比我自己写的要臃肿得多。虽然我同意用 LESS 编写比纯 CSS 更容易。

【21:53】<bsteffl>大葱感谢

有人知道 WordPress Live Preview 吗？

【21:53】<Jerry 95>能不能在一个大的样式表中少检查出重复的类或 id？

【21:53】<alexm walker>Jerry 95——如果你发现自己写了相同的浏览器前缀(-webkit-，-moz)——sass 让你把它写成一个“mixin”，然后每次你需要的时候用一行代码调用它

【21:54】<kkl>虽然这是一篇 LESS vs SASS 的文章，但是它向您展示了那些预处理器可以做什么。

【21:54】<kkl>[http://css-tricks.com/sass-vs-less/](http://css-tricks.com/sass-vs-less/)

【21:55】<alexm walker>当你开始使用这种语言的 5%时，你就会受益——随着时间的推移，情况会越来越好

【21:55】<霍克>还有谁有问题要问吗？

【21:55】<约翰莱西>哇萨斯看起来很厉害。感谢链接@kkl

【21:55】<霍克>或者他们见过的牛逼 CSS 的例子…

【21:56】<kkl>你看到了吗？

【21:56】<kkl>[http://www.keithclark.co.uk/labs/css3-fps/](http://www.keithclark.co.uk/labs/css3-fps/)

【21:56】<kkl>太神奇了。

【21:56】<alexm walker>kkl，jquery 会立刻解决你很多问题。你会做一些东西，这很酷，也很有动力。

【21:57】<kkl>alexm walker 所以你建议我先用 jQuery 破解？

【21:57】当你需要做一些事情时，你是否发现自己在检查新的功能，或者像我一样——在互联网上搜索并使用它们一段时间，却发现使用 html5/css3 有新的和更容易的改进？

【21:57】<kkl>然后了解一下底层 JS？

【21:57】<NIteFlite>说起 HTML5 和 CSS3，目前能处理的浏览器有哪些？

【21:57】

【21:58】<alexm walker>我很高兴成为 jquery 的一员，当我需要纯粹的 JS 能力时，我会寻求帮助:

【21:58】

谁能推荐一些制作简单的 CSS3 动画的工具？

【21:59】<Jerry 95>感谢所有人，我有点困惑但是除了 mixins 和 loop，你还能用 LESS 和 SASS 做什么？一旦有了 html，有什么办法可以提前计划级联吗？

【21:59】<南希> html5 css3 浏览器就绪图[http://html5readiness.com/](http://html5readiness.com/)

【22:00】<fel gall>jQuery 是 JavaScript，所以为了正确使用 jQuery，你需要了解最基本的 JavaScript 知识。它运行在本地计算机上，而不是服务器上，所以它不会在服务器上停止运行——但如果它包含错误或使用特定浏览器不理解的命令，它可能无法在浏览器中运行

【22:00】<ralphm>@ NIteFlite 目前除了 IE*以外的所有浏览器都在一定程度上支持 HTML5 和 CSS3，一直在加位。你可以在[http://caniuse.com/](http://caniuse.com/)查看每个特性的支持度

【22:00】<alexm walker>NIteFlite With jquery，也许你的 jquery libray 但是损坏了。但是服务器上没有任何东西可以阻止 javascript 运行——因为 JS 运行在浏览器中。

【22:01】<johnlacey>IE 永远是个问题。lol

<Jerry 95>@ NiteFlite，我不是前端专家，但我只能想到 Jquery 无法正常工作的两个原因:要么是服务器提供的版本太旧，这是 CDN 的解决办法，要么是库与另一个 JS 库发生冲突，这是你应该使用 noConflict 方法的原因。

【22:02】

【22:03】<kkl>最终用户可以禁止脚本运行。

【22:04】<Jerry 95>@是的，那也是，既然如此，有人知道最新或最现代的检查 Javascript 启用的方法吗？

理想情况下，确保页面在 JS 关闭的情况下也能正常工作。那么如果用户把它关掉，没问题。

<alexm walker>Nancy——当人们在演示/示例等中使用新功能时，我往往会发现它们——大多数时候，这比我可以在生产中使用它们要早得多。但也许 18 个月后，它们会成为一个挑战，我会去谷歌搜索，看看我在哪里看到那个人使用了那个奇怪的 CSS 属性。

【22:06】<kkl>对于跨浏览器问题尝试 Modernizr……太神奇了……

【22:06】<kkl>esp。，如果你在写 HTML5/CSS3

【22:06】<鹰派>欢迎任何刚刚加入的人。请随时提问——我会把它们排好队，它们不需要在上下文中:)

【22:06】<南希>感谢 alexmwalker

【22:06】<kkl>仅仅一行 javascript 就修复了我所有的问题。

【22:06】<霍克>今天晚些时候我也会在 SitePoint 上发布完整的文字记录

【22:07】<Jerry 95>@ kkl 谢谢我去查查 modernizr

【22:07】<ralphm>@ kkl Modernizr 不会在不存在的地方添加功能。您仍然必须决定如果特性不被支持会发生什么。

<Jerry 95>再来说说 SASS 和 LESS，如果某个特定的浏览器不支持某些规则，你能预先编程使用什么规则吗？

【22:08】<alexm walker>johnlacey——我发现学习 CSS 动画最好的方法就是关注 CSSdeck.com 和 codepen.io，看看那里的人是怎么用的。你可以把代码复制到你自己的窗口中，然后把它拆开，看看它是如何工作的。

【22:09】

【22:09】<NIteFlite>但是我的问题不仅仅是局部的…每个人都看到了和我一样的问题…原本很好的功能突然不工作了，所以这不是局部的问题。它也是断断续续和不一致的。呃。

【22:11】<yyy>@ NiteFlite 你试过关机再开机吗

我正在尝试一个媒体查询。你们讨论过吗？我刚刚进来..@media (max-width: 320px)以为窗口 320 或者更小的话我可以做个 div 显示，也就是 iphone5。

【22:11】<alexmwalker>NIteFlite。如果你在我很乐意在网站周围逛一逛，看看我是否能发现什么之后逗留一下

【22:11】<霍克>不，我们没有萝莉——我会把你的问题排好队:)

【22:12】<萝莉>让 div 不显示我的意思。

【22:13】<kkl>我们能谈谈职业机会吗？人们雇佣仅仅是为了 HTML/CSS 技能吗？如果有，在哪里？还是 jQuery/JS 有必要？

【22:13】<Jerry 95>所以也许有人可以回答:你能用:after 和:before 属性做什么？

【22:13】<NIteFlite>是的，试着开了又关。去掉了任何无关的自定义 JS。我正在寻找任何可以修复的错误。有些事情已经解决了。其他的仍然是个谜。

【22:15】<alexm walker>Jerry 95——如果我经常使用 modernizer 来处理某些事情，则以浏览器为目标。它要求你在你的页面中安装一个 javascript，但是允许你编写 CSS 规则，比如“. ie10 h1 {一些 css)

【22:15】<NIteFlite>alexm walker——这次我得走了。但是如果有一种方法可以联系到你，在你方便的时候，我不会在这里占用更多的时间…:)

【22:15】<alexm walker>其他浏览器不会得到那个类，所以他们会忽略那个规则

【22:16】<拉尔夫姆> @Jerry95 你可以用:before 和:after 做出惊人的事情。基本上，一个元素上有两个钩子，可以用来做一些事情——设置样式、添加内容——而不需要使用额外的元素。(尽管不是所有的元素都支持它们。)

你会推荐使用 CSS 重置吗？

你可以用 before 和 after 做很多事情。你可以在元素中添加额外的文本或者图片，但是它们不应该被用来添加“真正的内容”,因为它们应该在 html 中。

谢谢，但我的意思是，我如何保持我需要在 JS 中检查的最新属性，以测试特定浏览器的兼容性。我发现记录他们中的大多数是非常困难的

【22:17】<杰瑟温>你是否经常需要针对 IE 以外的浏览器？也就是说，当你可以使用条件注释来放置 ie 类名时，为什么要使用 modernizr 来定位浏览器呢？

【22:17】<萝莉>是之前:css3 之后？

【22:17】<杰瑞>浏览器对前后的支持如何？当 a 看的时候(前一段时间)它很不稳定，所以我忽略了它们

【22:18】<Jerry 95>是的我认为 html5 样板文件是一个选项，但它不是只针对 html 5 吗？

【22:18】<ralphm>@ Lori No，:before 和:after 都是 CSS2 的一部分。它们已经存在很长时间了。

【22:18】<alexm walker>@ Lori 我很确定:before 和:after 都支持右回 IE7。

【22:18】<jessirwin>诚然，样板文件是 html5，但同样的条件注释肯定也可以用于 xhtml

【22:18】<杰西文> IE7 不支持:前后:

【22:19】<杰西温>除非我做错了…

你知道有关于他们的教程吗？我刚在网上看到一个很棒的！非选择器

【22:19】<保罗>:之后和之前是 ie8+

【22:19】

【22:19】<alexmwalker>jessirwin——非常确定，但有点问题

【22:20】<保罗>萝莉:前后都是 css2

【22:20】

【22:21】<萝莉>感谢

【22:21】<保罗>没有 iE7[https://reference.sitepoint.com/css/pseudoelement-before](https://reference.sitepoint.com/css/pseudoelement-before)

【22:22】<alexm walker>@ Paul 谢了，一定是在想别的事情

【22:22】<鹰派>我们的会议还剩 5 分钟多一点，大家还有什么问题没有回答吗？

【22:22】

【22:23】<johnlacey>@ alexm walker:你会推荐使用 CSS 重置吗？

【22:23】

【22:23】<NIteFlite>alexm walker——要走了。我可以通过某种联系方式稍后给你发邮件吗？

【22:23】<霍克> NIteFlite 你可以在 Twitter @alexmwalker 上得到 Alex

【22:24】<硝石>好的，谢谢。WCW

关于重置，我确实推荐它们，但我更喜欢精简版的。HTML 样板版本非常长，很难理解所有的代码

【22:24】<保罗>alexm walker——你找到一种动画自动标注的方法了吗？这似乎正是需要但不支持的东西。

【22:24】<霍克>啊是的——谢谢萝莉

【22:25】

【22:25】<alexm walker>johnlacey 这篇文章是重置[http://csswizardry.com/2011/10/reset-restarted/](http://csswizardry.com/2011/10/reset-restarted/ )的明智方法

【22:26】<约翰莱西>感谢@alexmwalker

【22:26】<穆拉利>我问了一个关于重叠绝对定位子的问题

【22:26】<霍克>好了，我想我们先来看看萝莉的问题，然后是你的问题，然后我们就结束了今天的讨论:)

【22:27】<杰瑟温>萝莉对于 320px 的 iphones 或智能手机试试(最小设备宽度:320px)

好的，我会试试的。感谢

【22:27】<jessir win>@ Lori——我经常查询媒体查询的一个很好的参考是[http://CSS-tricks . com/snippets/CSS/media-queries-for-standard-devices/](http://css-tricks.com/snippets/css/media-queries-for-standard-devices/)

【22:27】<Jerry 95>感谢你们。霍克，你能告诉我哪里会有文字记录的链接吗？因为有很多资源可以查看

【22:27】<ralphm>@ Lori @ media only screen 和(max-width : 480px) { }，此外，为了更好地衡量，请在您的页面标题中包含以下内容:<meta name = " viewport " content = " width = device-width ">

【22:27】<alexm walker>@ Lori 没有理由不这样做。您可以设置“最大宽度:320 像素{显示:无)”

【22:28】<鹰派>马上就上 sitepoint.com Jerry 95

【22:28】

【22:28】<霍克>感谢今天加入我们的每一位。我们每周进行这些会议(不同的主题)

【22:28】<保罗>萝莉——确保媒体查询遵循原来的规则，以免被取消。级联 stil 适用于媒体查询

【22:29】<萝莉>嗯，最大宽度不起作用，所以我想我会试试最小设备宽度，看看是否可行？

【22:29】<ralphm>@ Lori 我的意思是说，最大宽度:480px(或者 iPhone5 实际上是 560px)将覆盖 iPhone 上的两种视图。

【22:29】<alexm walker>@ lor 你能在 CSSdeck 或 codepen 中弹出你的代码吗？

【22:29】<萝莉>我只需要它不要在肖像中显示，因为元素比它大。

【22:29】<alexm walker>Murali——现在看你的代码:)

【22:30】<杰瑞>感谢亚历克斯、霍克和所有其他响应者。很好的会议！

【22:30】<拉尔夫姆>@萝莉好吧，那就把我贴的改成 max-width: 320px 吧。但是您也可以调整元素的大小，而不是完全隐藏它。

【22:30】<BlackVlXENN>谢谢霍克，亚历克斯……

【22:30】<鹰派>今天的提问到此结束。谢谢大家。请随意留下来看看 Alex 如何解决 Murali 的问题——你好，Alex，我有一个容器 div，有两个子容器(头和体)。容器有滚动条。我希望标题 div 应该固定在顶部，只有身体 div 应该滚动。

【22:30】<鹰派>上图为 q[【http://cssdeck.com/labs/gomiazlb】](http://cssdeck.com/labs/gomiazlb)

【22:30】<alexm walker>很荣幸，各位。感谢

【22:30】

【22:34】<alexm walker>所以在你的演示中，头是固定的？有用吗？

【22:34】<alexmwalker>而身体是由看起来的东西的头部下？

【22:35】

【22:35】<BlackVlXENN>身体的宽度……不一样的宽度

【22:37】

【22:37】

【22:38】<alexm walker>[http://cssdeck.com/labs/hy1bp6si](http://cssdeck.com/labs/hy1bp6si)

【22:38】<alexm walker>这就是我这里的

【22:38】<alexm walker>拉尔夫姆所说的

【22:38】<alexm walker>(我把容器做成了红色，这样我就能看到了)

【22:39】<ralphm>或者，保持页眉宽度为 100%但给出容器位置:相对；

【22:40】<拉尔夫姆>编辑:哎呀，那使割台停止不动，所以取消它。:)

【22:41】

【22:42】<Murali>是的，但是滚动条应该在容器中，而不是在主体中

【22:42】<保罗>这样？[http://www.pmob.co.uk/temp/fixed-header2.htm](http://www.pmob.co.uk/temp/fixed-header2.htm)

【22:43】<穆拉利>@保罗我需要容器滚动条

【22:43】<BlackVlXENN>表头用固定的带，而不是百分比？

【22:43】<保罗>如果降低视口高度，内容会出现滚动条

【22:44】<alexm walker>这样标题就不会遮住滚动条的顶部了吗？

【22:45】<alexm walker>[http://cssdeck.com/labs/0hivmtvo](http://cssdeck.com/labs/0hivmtvo )

【22:45】<alexm walker>怎么样？

【22:45】

【22:46】<alexm walker>这有点古怪，并且假设你知道宽度，但是..

【22:47】

【22:47】<alexm walker>没错，这就是黑客的部分..；)

我喜欢这种类型的布局。我在这里也做了类似的事情，但是在页脚和侧边栏也做了同样的事情。[http://www.spruzstuff.spruz.com/](http://www.spruzstuff.spruz.com/ If)如果你想深入了解 css，请随意。前往音乐会

【22:48】

【22:48】<穆拉利>*比破了更好

【22:49】<alexmwalker>想一想..你在推特上吗？我可能会有一个更好的答案，一旦我把这个问题搁置一会儿

【22:49】

【22:50】<穆拉利>@穆拉利 _rk

【22:50】<阿莱克斯塔尔>我们的荣幸穆拉利，感谢光临:

【22:50】<alexm walker>感谢。再喝一杯咖啡后我会集中注意力的

【22:52】<主战派>感谢所有人

【22:52】<鹰派>文字稿随后就到:)

## 分享这篇文章