# 与专家讨论 Node.js 这一切是如何进行的

> 原文：<https://www.sitepoint.com/talk-node-js-with-the-experts-how-it-all-ent-down/>

今天早上，我主持了我们与专家系列*谈话的另一个会议——这次的主题是 Node.js，专家是 Don Nguyen、Giovanni 费隆和 Mark Dalgliesh(我们即将推出的 [Jump Start 系列](https://www.sitepoint.com/jumpstart/ "Jump Start series")第一本书的作者、评论家和编剧)。一如既往，它既有启发性又有知识性。如果你错过了这个环节，你想知道我的第一条建议，那就是马克的建议:*

如果你之前没有尝试过 Node，至少今天要这么做:
1)安装 Node.js
2)用 1 行代码创建“Hello world . js”:console . log(“Hello world”)；
3)从终端运行“node hello world”
下面是我从会话中收集的资源列表:

**初学者参考资料:** [http://nodeup.com/](http://nodeup.com/) [http://nodejs.org/](http://nodejs.org/) [http://www.nodebeginner.org/](http://www.nodebeginner.org/) [http://www.nodecloud.org/](http://www.nodecloud.org/) [http://toolbox.no.de/](http://toolbox.no.de/) [http://book.mixu.net/](http://book.mixu.net/) [http://projecteuler.net](http://projecteuler.net)
[https://www.sitepoint.com/forums/forumdisplay.php?15-JavaScript-amp-jQuery](https://www.sitepoint.com/forums/forumdisplay.php?15-JavaScript-amp-jQuery)
**服务器框架:**
[http://expressjs.com/](http://expressjs.com/) [http://towerjs.org/](http://towerjs.org/)
[http://railwayjs.com/](http://railwayjs.com/)
[http://flatironjs.org/](http://flatironjs.org/)
[https://github.com/mikeal/tako](https://github.com/mikeal/tako)
**客户端框架:**
[http://angularjs.org/](http://angularjs.org/)
[http://emberjs.com/](http://emberjs.com/)
 [](http://backbonejs.org/) [http://stack overflow . com/questions/10374113/meteor-vs-derbyjs](http://stackoverflow.com/questions/10374113/meteor-vs-derbyjs) [http://blog . derbyjs . com/2012/04/14/our-take-on-derby-vs-meteor/](http://blog.derbyjs.com/2012/04/14/our-take-on-derby-vs-meteor/)
**还有一些比较随机的资源:** [https://trello.com/](https://trello.com/ "Trello")就是一个很牛逼的例子

[http://markdalgleish . com/2012/09/test-driven-Node-js-development-with-grunt/](http://markdalgleish.com/2012/09/test-driven-node-js-development-with-grunt/ "Mark's blog")可能有助于开始创建节点模块

你可以在这里注册未来类似会议的电子邮件提醒。

还有…如果你有兴趣知道*今天早上到底发生了什么，这是完整的文字记录。*

| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 所以…我想既然大家都到齐了，我就正式介绍一下。Giovanni、Don 和 Mark 是我们即将发布的 Node.js Jump Start book(这是我们即将推出的新系列的第一部)背后的团队。谢谢你们今天早上的时间，伙计们。 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 我是 SP 的社区经理，我将主持会议。

 |
| 微笑 | 

> 大家对 node 有什么体验？

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 如果你以前没有参加过这样的会议，它对所有人来说都是免费的。随时提问题&我会确保自己掌握任何可能被淹没的信息。 |
| jerryc | 

> 什么是节点？

 |
| 恶魔 | 

> @suniled 还没有，不过在一些招聘启事上看到过，所以想多了解一下

 |
| 微笑 | 

> 【http://nodejs.org/】

 |
| mb4 ![mb4](img/ccf565888b919ee382b1f301668a1e32.png) | 

> 你们有哪些已经投入生产的应用程序？

 |
| 斯特凡诺...。![stefano.boccuti](img/77a16cfdd55acc9207acf4a8beb75bf2.png) | 

> 这里大一(连个 hello world 都还没做)

 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> 我现在正在做一个相当大的项目……我有一个问题想问任何知道如何使用 socket . io .T3 转移 Backbone.js 应用程序状态的专家

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 虽然它有自己的特点，但一旦你习惯了，这是一个非常令人愉快的编程环境 |
| 租金收入 | 

> 只实验了

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | JerryE 是一个用于构建快速和可扩展的网络应用程序的平台。它通常用于实时网络应用 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> Node.js 是 Google 的 V8 JavaScript 引擎

的包装器 |
| 微笑 | 已经在 node 上工作了大约 6 个月，非常喜欢！！建立简单的网站聊天框…没有生产…所有的学习 |
| 微笑 | 

> 想进入实时应用……

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 它允许你像任何普通应用程序一样与系统交互

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 所以 Node.js 不必仅限于 web 应用

 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> @markdalgleish 说得好。我做了几个跨平台运行的小工具

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> @mb4，我当时负责 HouseFix 的技术。Node.js 做了我们在技术上想要的一切，但不幸的是，这家初创公司出于各种商业原因决定退出

 |
| 租金收入 | 我听到了一些对 node.js 的批评，也在 YouTube 上看了一些视频，我相信你知道我指的是哪几个？ |
| 恶魔 | 

> 我看 LinkedIn 现在都在用。它真的是为高流量网站而不是普通的静态类型网站而构建的吗？

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 你指的是哪些具体的批评？ |
| 微笑 | 我想知道是否有一些专家能谈谈 node 的未来…比如 1.0 出来后我们能期待什么？ |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 

> @dmon 嗯，你可以更轻松地处理成千上万的请求，因为它的异步性质

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> @dmon，它可以用于任何网站，但它真正的竞争优势在于实时网络应用

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> @dmon Node.js 可以作为静态站点，但那不是真正发挥它的长处

 |
| 租金收入 | 特别是关于事件循环，以及我们作为程序员如何处理更低级的东西，而这些问题已经解决了？ |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> Node 真正强大的地方在于它的实时性、事件驱动性

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 我想你的问题可能需要反复思考，也许这是一个最适合论坛的问题？ |
| 恶魔 | 

> 好的谢谢:)

 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> @ donngguyen 感谢提到这一点，我将带你去:>这里有一个更普遍的问题:在客户端重用服务器端代码有哪些策略？

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 参考消息任何节点问题都可以在我们的 JS 论坛这里提问【https://www.sitepoint.com/forums/forumdisplay.php? 15-JavaScript-amp-jQuery

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> @rettal，再问一遍你有什么‘低级玩意儿’的具体例子吗？我知道回调风格受到了批评，但是有一些可用的库使得这种风格读起来更像传统的编程风格

 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> 感谢

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 我不会说 Node.js 水平很低。我能看出人们会纠结于回调风格

 |
| 东古耶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> TJKoury，有一个叫 dnode https://github.com/substack/dnode 的包它允许 RPC 功能，可以用来在客户端和服务器上共享库

 |
| 微笑 | 

> 你在节点上工作得越多，虽然回调风格在我看来没什么大不了的

 |
| 蒂米戈![TimIgoe](img/29a9bbd98d96bccdb7fb4c721b4ba9d7.png) | 我猜这让人们对网络工作有了不同的想法？毋宁说是塔恩在“请求、处理和答复”方面的思考 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 回调风格迫使你从一开始就认为是异步的，这是很多节点的能量来自

的地方 |
| mb4 ![mb4](img/ccf565888b919ee382b1f301668a1e32.png) | 

> 编码服务器/ http 请求比大多数平台级别低。

 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> @donNguyen 我一定会查的，谢谢

 |
| 恶魔 | 对于 n00b 来说，它是否类似于 php+apache 的精简版，但效率更高？ |
| 租金收入 | 

> 我想我指的是 node.js

的非阻塞特性 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> @rettal 我不确定我们明白你在问什么

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> mb4，确实如此。在 PHP 这样的环境中，您有一个单独的 web 服务器，比如 Apache。在 Node.js 中，你没有这种解耦。虽然它迫使你创建自己的服务器(不到 10 行),但它也让你完全控制 http 请求响应周期

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> @dmon 在节点，你写服务器

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 有一些库是为了使标准服务器更简单而构建的，但是你可以创建一个更智能的服务器

 |
| 租金收入 | apache 会帮你处理这些事情，node，js 你自己处理，这样会增加复杂性，但是我想回调会有所帮助？ |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> 我发现 Node 与‘低级’东西的整合效率更高；尝试使用 PHP 观察文件的变化。(你可以这么做，但它会杀死你的 CPU)带有系统钩子的回调是一个巨大的优势，对 http 服务器的低级控制也是如此。

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> dmon，不仅仅是一个被剥离下来的版本。其他大的区别将是:前端和后端之间的单一语言，NoSQL 相对于 SQL，异步相对于同步。

 |
| 恶魔 | 好的，谢谢，我想我现在明白了。在我的脑海中，它在某种意义上“只是”另一个类似 Nginx 的 Apache 服务器，但显然不是:DT3 |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 

> @ rettal这比你想象的容易

 |
| 凯勒![keller](img/2f45d34235028c19dffc24b6d4f51d98.png) | 

> 作为一个对这个感兴趣但没有这方面经验的人。你建议我从哪里开始？文章、网站&书籍？

 |
| 恶魔 | 

> @donNguyen 好的谢谢。像 MySQL/Postgres 这样的“普通”数据库也可以开箱即用吗？或者是添加到一个库中吗？

 |
| 租金收入 | 

> 我想人们看到著名的七(或者随便什么)行服务器会想哇！的确如此，但我认为还有更多原因

 |
| 微笑 | 

> 这里有个不错的介绍……【http://book.mixu.net/】T2

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | rettal，回调不用于复杂性管理，而是用于异步编程。用 Node.js 写自己的服务器一点都不复杂  |
| 微笑 | 

> …和【http://toolbox.no.de/】

 |
| 凯勒![keller](img/2f45d34235028c19dffc24b6d4f51d98.png) | 

> @suniled 感谢

 |
| 微笑 | 

> 这里是一些博客/网站【http://www.nodecloud.org/】T2

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 我早在学习 Node.js 的时候就看过这个:【http://www.nodebeginner.org/

 |
| 租金收入 | 

> 好的，谢谢唐

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> dmon，MySQL 可以通过软件包系统

的一个命令安装 |
| 微笑 | 

> 保持亲密…四处活动…找个项目…然后离开

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 就我个人而言，我从尝试使用 Node 解决实际问题、将自己投入困境

中学到了很多 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> @rettal 我想你会发现，你在 Apache 或诸如此类的网站上所缺少的大部分“复杂性”对你正在做的事情来说是不必要的，所以你不必管理一个巨大的 httpd.conf 或诸如此类的网站(大部分是从 Apache 论坛上复制来的……)

 |
| 凯勒![keller](img/2f45d34235028c19dffc24b6d4f51d98.png) | 

> 都行！！4 新书签阅读

 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> 你只要配置好你需要的就行了！

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 此外，我建议你去当地的 JS 聚会，找一个 Node.js 专家来指导你学习基础知识

 |
| 微笑 | 

> 如果你对网络协议没有很好的理解(比如 http 是什么意思，等等),这是一个相当陡峭的攀登

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 我发现这可能是达到速度的最好方法

 |
| 恶魔 | 

> OK。因此，我已经为几个与 SAP 系统对话的站点建立了一个 API，使用 Node 代替当前的 apache/php/curl 设置，这是一个好的用途吗？不是高流量，而是让我在真实的环境中起步？

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> 凯勒，除了必不可少的跳转起点 Node.js，projecteuler.net 是我学习任何新编程语言的第一资源

 |
| 微笑 | 

> @markdalgleish 没错…但是找一个没那么容易:)

 |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 

> @dmon node 非常适合 API，尤其是如果你谈 json

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> dmon，是 REST API 吗？

 |
| 凯勒![keller](img/2f45d34235028c19dffc24b6d4f51d98.png) | 

> 谢谢@DonNguyen

 |
| 恶魔 | 

> @donNguyen Errr…它馈入/馈出文本文件，基本生成订单文件发送到 SAP，返回一个回复文件，里面有成功下单的订单【在 SAP 中】，缺货文件等。要么作为。txt 或者。CSV

 |
| mb4 ![mb4](img/ccf565888b919ee382b1f301668a1e32.png) | 

> 使用 node for web apps 的人似乎大多使用 Express 框架。我还没有发现没有错误的 web 应用程序的例子。可以说 Node 作为特定领域的支持技术工作得很好，但仍然没有一个成熟的 web 应用程序框架来在 Node 中构建整个系统，这公平吗？

 |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 

> @mb4 我不认为构建一个像 rail 一样完整的 web 框架是 node

的目标 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> @mb4 Trello 是用 Node.js

搭建大规模、高流量站点的一个很好的例子 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | dmon，Node.js 将会很好地服务于你的目的，但是这并不是它真正超越 PHP 的项目类型。作为一个学习项目，它非常有效 |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 

> @mb4 节点是为快速无阻塞应用而构建的(可以是 web 也可以不是)

 |
| mb4 ![mb4](img/ccf565888b919ee382b1f301668a1e32.png) | 

> 哦，我完全不知道特雷罗是在 Node。gvvn，这也是我的印象。

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 当然有可能，而且雾溪也说过他们有多享受使用 node . js:【http://blog.fogcreek.com/the-trello-tech-stack/

 |
| mb4 ![mb4](img/ccf565888b919ee382b1f301668a1e32.png) | 

> 斯泰芬森刚刚发微博说这是他建的[https://S3 . Amazon AWS . com/f . cl . ly/items/1z1r 100 k1 O3 r 2 i1 i3 f 43/Kinect % 20 png % 20 stream . mov](https://s3.amazonaws.com/f.cl.ly/items/1z1R100K1O3R2I1I3F43/kinect%20png%20stream.mov)[https://github.com/sstephenson/kinect](https://github.com/sstephenson/kinect)

 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 我认为你想做什么真的很重要。如果你在服务器上呈现页面是为了 SEO 或者其他什么，你可能想用别的东西。对我来说，因为我使用 Backbone.js 做任何事情并在客户端呈现视图，所以它作为数据聚合器/缓存器/验证器非常好用。 |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 

> @mb4 geeklist 也是一个节点 app。Node 给你积木，你可以随心所欲地构建你的应用

 |
| mb4 ![mb4](img/ccf565888b919ee382b1f301668a1e32.png) | 

> 从 Kinect 获取数据并显示可视化效果。看起来人们正在用 Node 做新的事情，这很酷。

 |
| 微笑 | 

> 服务器框架:【http://expressjs.com/】[【http://towerjs.org/】](http://expressjs.com/)[【http://railwayjs.com/】](http://railwayjs.com/)(如果你的

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> mb4，Express.js 是一个经过极好测试的框架。设计得更像 Sinatra 而不是 Rails。我想错误更多的是由于程序员而不是环境:-)在 Jump Start Node.js 中，我们花了相当多的时间来进行测试驱动的设计。

 |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 

> @mb4 如果你想构建一个快速且可扩展的应用，而不是一个带有 rails(或其他框架)的单一应用，那就太棒了

 |
| 微笑 | 

> 服务器框架:【http://expressjs.com/】[【http://towerjs.org/】](http://expressjs.com/)[【http://railwayjs.com/】](http://railwayjs.com/)(如果你来自 ruby)[【http://flatironjs.org/】](http://flatironjs.org/)

 |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 

> 如果你喜欢小溪试试【https://github.com/mikeal/tako】T2

 |
| 恶魔 | 

> @donNguyen ok 感谢理解

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> @mb4 我对 Express 的体验一直很棒。我遇到的唯一真正的问题是有内存泄漏的模块，而不是 web 框架。

 |
| 微笑 | 

> 客户端:【http://angularjs.org/】

 |
| mb4 ![mb4](img/ccf565888b919ee382b1f301668a1e32.png) | 

> @gvnn，我从来没有建立过一个铁板一块的东西，只是多个小应用。感谢你对框架的想法，很想在你的书发布时看看。

 |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 

> @mb4 好吧那么 node 可以是一个不错的选择……我当然建议 node 为 API 和数据流的处理

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> Node 非常适合制作轻量级但功能强大的应用

 |
| 微笑 | 

> 实时框架:【http://derbyjs.com/】[【http://meteor.com/】](http://derbyjs.com/)

 |
| mb4 ![mb4](img/ccf565888b919ee382b1f301668a1e32.png) | 用来构建 Postgres 支持的 JSON API 的最佳包有哪些？ |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> mb4，对于小应用看看 meteor.com。位于 Node.js 之上的一个较新的框架。你的下巴会掉下来。

 |
| mb4 ![mb4](img/ccf565888b919ee382b1f301668a1e32.png) | 

> @DonNguyen 我去查一下

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 流星是神奇的，虽然我会把它当作一个单独的实体来节点它是建立在节点之上的，但是把它抽象出来远离你

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 即不能从 npm

安装任意包 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> markdalgesh，表示同意。然而，对于较小的单页面 web 应用程序，我相信 Meteor 确实会大放异彩。就像你说的有些不同的动物T3

 |
| 微笑 | 

> 流星有自己的包经理，我觉得

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> @DonNguyen 这真是太神奇了，我在工作中用 Meteor 构建了一个工具，用于办公室之间的实时通信

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> mb4，我用过 https://github.com/brianc/node-postgres都没问题。Plain vanilla Express.js 很好服务 JSON

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> @suniled 耶，它有“智能包”

 |
| 诺克罗斯![Nokrosis](img/5407d5abb8bcd59f84f1d50cc2ad1dcb.png) | 

> 有人有 Node.js 对比其他 web 服务器性能测试吗？Node.js 如何在一个 app 的大流量下服务？

 |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 

> @nokrosis 我曾经尝试过做一些基准测试[https://github.com/gvnn/nodenight-benchmarks](https://github.com/gvnn/nodenight-benchmarks)但是它不是一个“真正的”基准测试，它只是一个斐波那契计算但是它给了你一个想法…而且 php 出奇的快

 |
| mb4 ![mb4](img/ccf565888b919ee382b1f301668a1e32.png) | 

> 有哪些痛点？

 |
| 微笑 | 

> @ NOK rosis for what ' s values[http://zgadzaj . com/benchmark-nodejs-basic-performance-tests-against-Apache-PHP](http://zgadzaj.com/benchmarking-nodejs-basic-performance-tests-against-apache-php)

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 我认为值得一提的是，选择支持还是反对 Node 应该基于你试图实现的目标，而不是它的性能

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | Nokrosis，基准测试是一个非常棘手的话题，它在很大程度上取决于您正在进行的确切测试。然而，作为一个非常宽泛的概括，与其他平台相比，它的基准测试通常更好。性能是其核心目标之一。 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 这并不是说它不够快(确实如此)，但对于某些类型的应用程序来说，这是一个更好的解决方案如果我想编写一个实时的 web 应用程序，Node.js 是一个明显的选择

 |
| 恶魔 | 我想这就是我最初想不通的。它适用于某些应用程序，不适用于其他应用程序。 |
| 微笑 | 

> 亦作[https://vertxproject . WordPress . com/2012/05/09/vert-x-vs-node-js-simple-http-benchmarks/](https://vertxproject.wordpress.com/2012/05/09/vert-x-vs-node-js-simple-http-benchmarks/)

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> Nokrosis，除了 markdalgesh 说的，一般更适合任何 IO 密集型的东西(数据库、网络、磁盘)

 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 有人是流星/德比的粉丝吗？ |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 我是流星的忠实粉丝

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | suniled，这是一个相当有争议的基准，我建议阅读所有的回复，尤其是艾萨克·施鲁特 T3 的回复 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 我认为流星采取的方法

有一个光明的未来 |
| 微笑 | 

> 没错……同意了

 |
| 诺克罗斯![Nokrosis](img/5407d5abb8bcd59f84f1d50cc2ad1dcb.png) | 

> @DonNguyen 我在运营一个流量很大的媒体网站，我将帖子保存为静态 html 文件，并用 php 将它们包含到页面布局中。我能使用 Node.js 以更少的服务器资源更快地运行它吗？

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | mb4 的软件包系统相当惊人，它支持许多深奥的软件和平台。痛点之一是包装体系的不成熟。因为任何人都可以发布一个包，你永远不知道你会得到什么质量。 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> @markdalgleish 你会说你喜欢它的前两大理由是什么？

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> @TJKoury 首先，编写一个自动数据绑定到服务器的单页 app 是多么容易的事情。没有什么能与之相比。@TJKoury 其次，默认情况下，它如何在服务器和客户端之间共享代码，这通常需要一点工作

 |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 

> @mb4 我想最好的做法总是选择那些经常维护的包…一个 1 年前推的包可能不是正确的选择

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | Nokrosis，是的，但我想说这更多的是一个架构问题，而不是一个平台问题。在不知道具体细节的情况下，第一次我会说使用索引数据库比保存为静态 HTML 文件要快得多 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> @markdalgleish 感谢

 |
| 微笑 | 

> @markdalgleish @TJKoury 模型-视图数据绑定[http://blog . derbyjs . com/2012/04/14/our-take-on-derby-vs-meteor/](http://blog.derbyjs.com/2012/04/14/our-take-on-derby-vs-meteor/)

 |
| 诺克罗斯![Nokrosis](img/5407d5abb8bcd59f84f1d50cc2ad1dcb.png) | 

> @DonNguyen 我会用 Node.js 和一个带索引的 MongoDB

来尝试那个解决方案 |
| 微笑 | 

> 走向中间他们谈论这些‘绑定’

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | Nokrosis，我建议先做一些小规模的基准测试，以帮助您决定性能差异是否值得进行全面迁移。 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> @suniled 那篇文章触及了很多我认为是流星最大的优点和缺点之一那就是流星创造了他们自己的生态系统他们让创建一个应用程序并将其部署到 meteor.com

变得简单得可笑 |
| 微笑 | 我没有任何使用 meteor 或 derby 的经验，但我读过很多关于它们的文章，我的一些同事使用 meteor，其他人使用 derby。看起来他们解决了同一个问题，但只是做了一些不同的调整 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 但是当然，这是以将自己从节点社区及其所有现有模块

中分离出来为代价的 |
| 微笑 | 

> 但是你说不好的一面是‘它有自己的生态系统’

 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> @markdalgleish 再次感谢。过去，我已经研究过不同的“端到端”解决方案，但最终总是编写自己的 b/c，看起来它们还不够成熟。

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> @suniled 看你是谁

 |
| 微笑 | 

> @ markdalgleish plus……我相信流星得到了可笑的资助金额才得以起飞

 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> @suniled 大概 1100 万……有点让人想知道他们的商业计划会是什么……

 |
| 微笑 | 

> from stock overflow—[http://stack overflow . com/questions/10374113/meteor-vs-derbyjs](http://stackoverflow.com/questions/10374113/meteor-vs-derbyjs)* stack

 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 我无法想象他们光靠主机服务就能赚钱 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> TJKoury 他们要发布一款名为银河T3 的企业产品

 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> 哦，好的。

 |
| 微笑 | 

> @TJKoury 托管流星 app

 |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 

> 老实说，我并不热衷于这些框架，我宁愿构建一组 API，然后构建一个前端应用程序来使用这些 API将两个层分开让我有机会在以后更改后端层，保持相同的前端

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 大家好，欢迎刚刚加入我们的人。会议还剩下大约 15 分钟，因此如果您有问题，请务必尽快回答。今天的专家团队是我们即将推出的 Jump Start 系列的第一部的幕后人员，该系列是关于 node . js[https://www . site point . com/get-a-Jump-Start-on-web-development-and-design/](https://www.sitepoint.com/21-steps-to-becoming-a-successful-web-developer/) |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> @gvnn 同意

 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> @gvnn 听到听到

 |
| mb4 ![mb4](img/ccf565888b919ee382b1f301668a1e32.png) | 

> @gvnn，我也不喜欢那些抽象了客户端/服务器划分的包罗万象的框架。

 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> *这里*这里

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> @gvnn 也就是说，我认为流星和德比之类的事情是即将到来的事情的征兆

 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> @markdalgleish 我觉得流星/德比会是新的 PHP

 |
| mb4 ![mb4](img/ccf565888b919ee382b1f301668a1e32.png) | 

> 你的 twitter / github 句柄有哪些？

 |
| 微笑 | 

> node . js 如何融入 WebRTC 技术？

 |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 你说得对，这有助于一个框架被更多的受众采用但是，到最后，你所需要的只是 HTTP :p |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> @mb4 地雷，恰如其分，markdalgleish

 |
| 微笑 | node.js 能很好地适应浏览器吗？ |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> @suniled 也许变成了 ChromeOS……

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> @gvnn 是啊，流星让 Node 里平常很棘手的事情变得简单得可笑，所以很多人都会被它吸引一如既往，不好的一面是当你想做一些不太“流星之道”

 |
| 微笑 | 我认为开发者在进入 meteor 时会因为生态系统而有点犹豫…让一个应用程序运行起来可能非常简单，但接下来呢？ |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> mb4，我的 GitHub 是 nodeninja。不幸的是，在推特上，我仍然停留在 90 年代

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 有很多不同种类的开发人员。一些开发人员只是想要一些简单而强大的东西，这为他们做了很多工作。当然，那种能力是有代价的，这是很多其他开发者不想要的 |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 

> @suniled 你能做的就是把你的核心构建成一个模块，并且尽可能地把它和流星分开，这样你就有能力在

之后改变框架 |
| 微笑 | 

> @markdalgleish @gvnn 有趣的

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 我们的会议还剩 5 分多钟。有人有一个问题没有得到满意的答案吗？ |
| muescha ![muescha](img/c4d6f7532d5d74055989a8ae95c2a792.png) | 

> 我刚刚在 ruby/Sinatra/noko giri(mechanize)有一个私人项目:登录网站，获取 html——并返回移动 html(iui)——我可以使用 node 的后端部分(登录，解析 html，响应类似于带有 json 的 api)吗？在客户端，我可以用你在上面的链接

中提到的客户端库来使用这个 json api |
| 微笑 | 

> 幻灯片。[http://www . slide share . net/stud geek/an-overview-of-derbyjs-and-meteor js-for-the-nova-nodejs-meetup](http://www.slideshare.net/studgeek/an-overview-of-derbyjs-and-meteorjs-for-the-nova-nodejs-meetup)

 |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 

> @suniled 你能做的也是运行自己的 npm 服务器:p

 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> @ gvnn…在他们的服务器上使用 Metor 应用…

 |
| 租金收入 | 我在这里学到了很多东西，发布的链接也是一个很好的见解，谢谢！ |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> muescha，那个项目将非常适合 Node.js。任何与 JSON 消费相关的东西都将像谚语中的手套

一样适合 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> @muescha 是的，那种设定肯定是可以的

 |
| 微笑 | 

> @gvnn 好玩！

 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> 太棒了，谢谢大家！

 |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 

> @TJKoury 可以工作…尽管我会建议【http://nodejitsu.com/】的[举办](http://nodejitsu.com/)的

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> @TJKoury Meteor 让你自己生成一个拥有一切所需的 tarball

 |
| 微笑 | 这是我正在创建的第一个“出柜”网站！ |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> @gvnn 我是元开玩笑，不过好建议

 |
| mb4 ![mb4](img/ccf565888b919ee382b1f301668a1e32.png) | 

> @gvnn，哪里可以跟踪你？

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 无忧无虑。感谢大家今天早上的参与。不要忘了，您可以很快查阅 Node.js 这本书。如果你想在发布时得到通知，你可以在这里注册[https://www . sitepoint . com/get-a-jump-start-on-web-development-and-design/](https://www.sitepoint.com/21-steps-to-becoming-a-successful-web-developer/)

 |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 

> @TJKoury 一切皆有可能……我们可以在你房间里飞行的四轴飞行器设备中运行网络服务器

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 如果你想得到类似这样的会议通知，你可以在这里注册【https://www.facebook.com/sitepoint/app_115462065200508

 |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 

> @mb4 推特上的 gvnnrules

 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> @markdalgleish 我确实在文档中看到了这一点，但老实说，一旦项目进行了 6 个月，在处理 DNS 注册

时，你最不想做的事情就是导出所有数据并重新接收 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 随便逛逛，想聊多久就聊多久——我会把房间敞开着，今天晚些时候我会在 sitepoint.com 和 jspro.com 张贴一份文字记录

 |
| muescha ![muescha](img/c4d6f7532d5d74055989a8ae95c2a792.png) | 

> @ DonNguyen @ markdalgleish mhhh 这个 html 解析让我有些头疼。或者可以用 css/xpath 解析 jquery 或者 DOMDocument 这样的 html 页面吗？

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 感谢@HAWK :)

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> 流星有一点就是安全。默认情况下，任何人都可以从他们的网络浏览器更新任何内容。这是默认设置，出于安全考虑，您实际上需要关闭它。一个小问题。

 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> @ gvnn lol…给我买个四轴飞行器，我在上面装个 raspberryPI！

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 非常感谢 Don、Mark 和 Giovanni 今天早上的宝贵时间——尤其是早上 7 点！ |
| 蒂米戈![TimIgoe](img/29a9bbd98d96bccdb7fb4c721b4ba9d7.png) | 

> 哈哈，现在还有很多节点开发的时间；)

 |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 

> @ TJ koury Nodeup 上有一整节播客是关于机器人的，node comunity 快疯了

 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> @gvnn 神圣的废话，我怎么没见过这个？谢谢你的提示。

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> @DonNguyen 没错，著名的流星试玩视频之所以成为可能，只是因为安全被默认关闭

 |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 

> ps…对于那些想知道更多关于 node(和他们的神祇)【http://nodeup.com/T2

 |
| 斯特凡诺...。![stefano.boccuti](img/77a16cfdd55acc9207acf4a8beb75bf2.png) | 感谢您的聊天，我一直在阅读帖子和跟随链接，即使我以前从未使用过 node.js..！非常有趣，我一定要试一试！ |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> 感谢莎拉。感谢大家收听和一些很棒的问题

 |
| 斯特凡诺...。![stefano.boccuti](img/77a16cfdd55acc9207acf4a8beb75bf2.png) | 

> 永不*

 |
| 恶魔 | 

> 感谢大家的聊天，觉得很有用。

 |
| mb4 ![mb4](img/ccf565888b919ee382b1f301668a1e32.png) | 

> 感谢大家，节点是 webscale。

 |
| 凯勒![keller](img/2f45d34235028c19dffc24b6d4f51d98.png) | 

> 感谢

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> muescha，我可能误解了你的问题，但是在软件包生态系统中有 HTML 解析库，使得这项工作非常容易

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 如果你之前没有尝试过 Node，今天至少要这么做:
> 1)安装 Node.js
> 2)用 1 行代码创建“Hello world . js”:console . log(“Hello world”)；
> 3)从终端，运行【节点 hello world】

 |
| muescha ![muescha](img/c4d6f7532d5d74055989a8ae95c2a792.png) | 

> @DonNguyen thx(解析只是其中一部分)

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> mb4，来自内存 MongoDB 是 webscale，Node.js 是摇滚明星 tech:-)

 |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 

> @ tjkory[【http://nodecopter . com/](http://nodecopter.com/)；)

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | muescha，你问题的另一部分是什么？ |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> @gvnn 我刚刚把自己加到邮件列表里了！

 |
| tjkory![TJKoury](img/9139cd2ebaa62e7a93c71e24b377f76f.png) | 

> 感谢大家，玩得很开心。

 |
| gvnn ![gvnn](img/90bd9635f805897853937069822743d8.png) | 

> 好了伙计们，感谢大家……公务在身，该上班了(墨尔本的一天刚刚开始)

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 谢谢大家，希望我至少能有所帮助

 |
| 微笑 | 

> 感谢提示！

 |
| jerryc | 

> 谢谢大家！

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 如果有人想问我别的，在 Twitter 上抓我我的句柄是@markdalgleish

 |
| 微笑 | 

> @gvnn 这里该回家了！

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> 谢谢亲们。我会在这里多呆一会儿，以防我们错过了什么。

 |
| 蒂米戈![TimIgoe](img/29a9bbd98d96bccdb7fb4c721b4ba9d7.png) | 

> 当然给出了关于节点使用的有趣见解:)和许多想法有人需要想出如何使每天可用的时间翻倍；)

 |
| muescha ![muescha](img/c4d6f7532d5d74055989a8ae95c2a792.png) | 

> 我认为解析只是“问题”——(a)登录网站(b)获取 html (c) pase html (d)用 json /类似外部 api-proxy 的东西响应——给网站添加 api(从 extern)

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 我最近也写了这篇博客文章，可能对你们中的一些人开始创建节点模块有所帮助:markdalgleish . com/2012/09/test-driven-Node-js-development-with-grunt/

 |
| muescha ![muescha](img/c4d6f7532d5d74055989a8ae95c2a792.png) | 

> @DonNguyen 我认为解析只是“问题”——(a)登录网站(b)获取 html (c) pase html (d)用 json /类似外部 api 的东西响应——代理——给网站添加 api(从 extern)

 |
| 地面波浪…![markdalgleish](img/c87ba85f1044b6a6e2154a206031bbba.png)t1 | 

> 公务在身，我现在要去工作了——墨尔本现在是早上 8:06，谢谢各位

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 感谢马克。

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> muescha，所有这些步骤都在 Node.js 中经过了正确的战斗测试，你做所有这些事情应该没有问题

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> 欢呼标记

 |
| muescha ![muescha](img/c4d6f7532d5d74055989a8ae95c2a792.png) | 

> @DonNguyen 我曾考虑从 sinatra 迁移到 rails——但我认为 node.js 可以是另一个想法——我喜欢作为 api 的 node act 的响应

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 我也要走了伙计们。感谢你今天早上加入我们。

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> 这是 Node.js 本来要做的工作

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> 感谢萨拉

 |
| muescha ![muescha](img/c4d6f7532d5d74055989a8ae95c2a792.png) | 

> @DonNguyen 你有一些有意思的关键词/包让这个深入研究一下？

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> 【https://github.com/tmpvar/jsdom】用于解析核心 http 模块应该处理的登录，Express.json 用于服务 JSON

 |
| muescha ![muescha](img/c4d6f7532d5d74055989a8ae95c2a792.png) | 

> thx 很多:-)

 |
| 唐古叶… ![DonNguyen](img/47b216d7ffe573cf9bdcec5459b16e9c.png) | 

> 不客气

 |

## 分享这篇文章