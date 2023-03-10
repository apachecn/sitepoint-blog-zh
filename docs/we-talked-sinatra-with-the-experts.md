# 我们和专家讨论了辛纳特拉…

> 原文：<https://www.sitepoint.com/we-talked-sinatra-with-the-experts/>

在几个月的中断之后，我们又回到了与专家会议的对话中。在今天上午的会议上，我们与达伦·琼斯——我们新系列中最新一本书(昨天发布)的作者， [Jump Start Sinatra](https://www.sitepoint.com/blog/ "Jump Start Sinatra") 和该书的专家评论家康斯坦丁·哈斯(Konstantin Haase)讨论了 Sinatra 的所有事情。这是一次有趣而非正式的会议，大多数与会者对这个主题都不熟悉。如果你错过了，不要担心…下面是一个来自聊天的资源列表，以及完整的文字记录。

**会话相关资源:**

达伦正在回答辛纳特拉的问题(与书相关或无关)[这里](https://www.sitepoint.com/forums/showthread.php?960208-New-Book-Jump-Start-Sinatra&p=5299210#post5299210 "Sinatra forum thread")。

如果你是 Ruby 或 Sinatra 的新手，需要一些支持，为什么不加入这个 Podling 群组呢？

如果您因为忘记而错过了本次会议，您可以在此[注册接收未来会议的电子邮件提醒。](https://www.facebook.com/sitepoint/app_115462065200508 "Email reminder sign-up")

如果你还没看过，请在这里看看达伦的新书。

当我谈到这本书的主题时，我们将向 nicksh 赠送一本书，感谢他在今天会议中的精彩参与。恭喜你！

 **通用会话中提到的 Ruby 和 Sinatra 资源:**

这是辛纳特拉自己[http://www.sinatrarb.com/wild](http://www.sinatrarb.com/wild)

这里有几个学习基础 Ruby 的好地方:[http://pine.fm/LearnToProgram/](http://pine.fm/LearnToProgram/)和[http://tryruby.org/](http://tryruby.org/)

这里有一篇关于 Darren 如何使用 Sinatra 在几个小时内构建和部署一个简单站点的博客:[https://www . site point . com/Sinatra-heroku-super-fast-deployment/](https://www.sitepoint.com/sinatra-heroku-super-fast-deployment/)

下面是一个完整的聊天实现，所有东西都在一个文件里:[https://github . com/Sinatra/Sinatra/blob/master/examples/chat . Rb](https://github.com/sinatra/sinatra/blob/master/examples/chat.rb)

安全–这款支架保护宝石配有辛纳特拉:[https://github.com/rkh/rack-protection](https://github.com/rkh/rack-protection)

对于辛纳特拉的文章，请查看[https://www.sitepoint.com/](https://www.sitepoint.com/)

这里有一篇比较 Sinatra 和 Rails 的文章:[https://www . site point . com/Rails-or-Sinatra-the-best-of-two-worlds/](https://www.sitepoint.com/rails-or-sinatra-the-best-of-both-worlds/)

这里是 SitePoint Ruby 论坛:[https://www.sitepoint.com/forums/forumdisplay.php?227-Ruby-amp-Rails](https://www.sitepoint.com/forums/forumdisplay.php?227-Ruby-amp-Rails)

最后，这是完整的文字记录。享受…

| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 好了，我们可以开始了。对于那些不知道的人来说，@daz4126 是《Jump Start Sinatra》的作者，@rkh 是专家评论者。他们是当今的专家。你对辛纳屈有什么兴趣。有人已经是用户了吗？ |
| 乔治![George](img/48ace59e558ff52b54012bbe7335b7da.png) | 我在朗达沃工作。住在洛杉矶。开发 Sinatra API。 |
| srpsco ![srpsco](img/8fb76461ac4f6e470dfbb70ba0b01c98.png) | 有人已经开始读这本书了吗？ |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 目前为止你对它有什么想法@srpsco

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 嗨伙计们！

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> 还有@daz4126 在这里。:)

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 我还没用过辛纳特拉

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 

> 我没有时间开始看这本书，但我很期待

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 但是很酷

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 我感兴趣的是，Sinatra 的抽象是否会伤害到潜在的发展？ |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @ServerStorm 什么意思？

 |
| 斯尔普斯科 | 

> @HAWK 到目前为止还好我只做了 1/3

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @serverStorm，什么样的抽象？

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 由于您不是直接使用 RAILS，如果出现问题会怎么样呢？您可以在 Sinatra 中轻松地调试它吗？或者您必须深入 RAILS 才能做到这一点？ |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 对于那些还没有看过这本书的人来说，你可以在这里找到它【https://www.sitepoint.com/launch/8396b1

 |
| 斯尔普斯科 | 

> @HAWK 我在想等我做完了

给 dzone 做个复习 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @ServerStorm Sinatra 没有坐在铁轨上面。

 |
| 乔治![George](img/48ace59e558ff52b54012bbe7335b7da.png) | 

> 我有一个问题。我想为证券交易所开发一个 Sinatra API。我想象现有的大多数证券交易所都使用 C++/C#/Java。在 Sinatra 开发高容量 API 可行吗？

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @serverStorm 你根本不需要用 Rails，Sinatra 只用 Rack 和 Ruby

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 听起来不错@ srpsco–如果你在 Twitter 上，确保你给我们发了一个链接

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> @George 我觉得 Sinatra 是为了更简单的应用

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 哦，好吧，那我还有很多要学；) |
| 乔治![George](img/48ace59e558ff52b54012bbe7335b7da.png) | 

> 能否扩展到每秒百万次请求？

 |
| 斯尔普斯科 | 

> @霍克会做

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 感谢@srpsco！

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @George 可扩展性其实并不是语言或者框架的问题。更多的是关于建筑

 |
| alme1304 ![alme1304](img/12817efc1eaf9627119aa1fe712ad19a.png) | 我对辛纳特拉很好奇，但我还没有时间开始学习。Sinatra 会相当于 PHP 的 Codeigniter 吗？就重量和自由而言？ |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @George 我知道 Sinatra 应用的响应时间在 24 毫秒以下

 |
| 乔治![George](img/48ace59e558ff52b54012bbe7335b7da.png) | 创建支持这样一个 Sinatra API 的架构实际可行吗？ |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 我认为你可以在 Sinatra 写这种东西，这取决于你连接到的股票交易所使用的 API |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | Sinatra 相对于竞争框架的主要优势是什么？ |
| 斯尔普斯科 | 

> @alme1304 我觉得更接近拉勒维尔

 |
| 乔治![George](img/48ace59e558ff52b54012bbe7335b7da.png) | 

> 我在创造交易所。其他人使用 REST 连接到它。

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 我想在这里告诉大家，在课程结束时，我会向提出很好问题的人免费赠送几本达伦的书。 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @alme1304 Sinatra 比 CodeIgniter 轻量多了Code Igniter 基于 Rails

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 欢迎@ Hiro——我们刚刚开始。随时欢迎提问，或者如果你愿意的话，就潜伏起来！ |
| 什么？T0 | 

> 在 Windows 上启动并运行很难吗？

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 不客气，使用“Ruby 安装程序”让它在 Windows 上运行其实很容易。前几天刚刚试过。 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 窗户真是一团糟..但应易

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @Hiro Asari?

 |
| Hiro  ![Hiro](img/f5fd4fd684c785e301d4c99849ebbbd5.png) | 

> @霍克谢

 |
| Hiro  ![Hiro](img/f5fd4fd684c785e301d4c99849ebbbd5.png) | 

> @rkh 是，长官。

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 

> 安全性怎么样？和辛纳屈一起执行安全措施太难了吗？我假设它有散列函数和所有的

 |
| 乔治![George](img/48ace59e558ff52b54012bbe7335b7da.png) | 达伦在吗？你对我的问题有答案吗？ |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> @莫洛娜好问题

 |
| alme1304 ![alme1304](img/12817efc1eaf9627119aa1fe712ad19a.png) | 至于部署，它是如何工作的，我是否必须使用 heroku 之类的东西，或者与 rail 相比有更广泛的部署选项选择？ |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @molona 与 Rails 等相比，Sinatra 只与 HTTP 有关，但它确实提供了开箱即用的安全机制。

 |
| alme1304 ![alme1304](img/12817efc1eaf9627119aa1fe712ad19a.png) | 

> 铁轨*

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @serverStorm Sinatra 其实并不是一个框架，它是一种 DSL(领域特定语言)。它的优势在于它的灵活性——你可以选择如何构建你的应用程序的架构

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 乔治，我来了！ |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 如果你正在使用 REST API，那么你应该可以很容易地用 Sinatra

来构建它 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @alme1304 凡是能运行 rails 的都可以运行 sinatra，选择面很广，你基本上只需要让 ruby 运行就可以了。

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 它是使用 OOP 惯例还是过程化的，或者两者都用？ |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 

> 感谢

 |
| 沃滕 | 

> 我对辛纳特拉知之甚少。它管理会话和 cookies 吗？

 |
| 斯尔普斯科 | 

> @alme1304 Heroku 是一个选项，也是一个非常容易的选项，但它可以用 vps，aws 来完成，我还认为 engine yard 提供支持

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @watjen yes Sinatra 为你处理会话和 cookies。这本书里提到过

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @ServerStorm 在内部，它是面向对象的，你可以很容易地编写完全面向对象的应用程序，但 DSL 是减速的

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 谢谢

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 别忘了 Appfog

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 欢迎@dorthe。如果你有任何问题，请随时提问。

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 哎呀，也许我今天可以学着说英语了:() |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 是的，现在有很多部署选项，任何支持 Ruby 的选项 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> @ServerStorm 你会说法语？

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 是的，有一些，但我应该是一个以英语为母语的人，只是目前表现不好 |
| Hiro  ![Hiro](img/f5fd4fd684c785e301d4c99849ebbbd5.png) | 

> 我可以插入我正在使用的平台即服务吗？；——)

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 哈哈

 |
| 蛋糕![dorthe](img/a4d6cad6a15829088148e891d28d5806.png) | 

> Tx——我对辛纳屈一无所知——只是好奇

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> (@Hiro 来自 RedHat，在 JRuby 团队)

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 我认为即使它不需要 Rails，它也可以很好地使用 Rails？还是我想多了？ |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> @ dorthe Sweet——那就随意潜伏吧！

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> @haWK 跑题链接-允许发帖？

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @molona 用 rails 很好用

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 

> @服务器风暴……你的英语还是比我说得好:D

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 你知道任何使用 Sinatra 开发的大型商业项目吗？ |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> @ nicksh Yeah——这是相当不正式的。我可以通过

从文字稿中编辑它们 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @molona github 是一个 rails app 和~30 个 sinatra apps，都运行在同一个进程

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 

> 还有不对，我不说法语:p

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @ServerStorm 是，lots

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @dorthe Sinatra 是一种特定于领域的语言，通过帮助你连接到 HTTPT3，你可以用 Ruby 构建 web 应用

 |
| 沃滕 | 

> Sinatra 是独立服务器吗？

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @ server storm【http://www.sinatrarb.com/wild】T2

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @ server storm gauge . es 用它

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 感谢 rkh

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @watjen ruby 自带服务器，sinatra 可以帮你管理

 |
| 斯尔普斯科 | 

> siantra 或 rack 可以查看和布局

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> 是

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @srpsco yes

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @ srpsco it※

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 书里有一点关于缓存的缓存控制真的很好用

 |
| 斯尔普斯科 | 

> @daz4126 谢谢我才熬到第三章今晚就完成剩下的

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 开发 Sinatra 时用什么工具最好？有没有特定的 ide 或者你使用简单的文本编辑器

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @srpsco 太棒了，很想知道你的想法。我在这里也写了一点关于缓存的内容【https://www.sitepoint.com/sinatras-little-helpers/

 |
| 蛋糕![dorthe](img/a4d6cad6a15829088148e891d28d5806.png) | 

> 啊啊。我是 Ruby 的新手，但我想买一本 sitepoint book

 |
| 沃滕 | 

> 一款 Sinatra 的 app 会在乘客身上运行吗？

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @serverStorm 绝对只是一个文字编辑器！我用的是 gedit

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> @ServerStorm 一切我更喜欢崇高文字 2

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @ServerStorm 我用的是 textmate，有它的捆绑包，你也可以用 RubyMine IDE。

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> Gedit 用外挂

也不错 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @watjen yes

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 我用 eclipse，但我相信它也能工作:)

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> 大概是

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @serverStorm 应做

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> 我曾经用 NetBeans 做过 Sinatra 开发

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 辛纳屈存在多久了？

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> 自 2007 年

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 你可以在一个文件中构建应用程序，所以你甚至可以使用…(震惊恐怖)…记事本！！！ |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> (恐怖音乐)

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 

> 嘿！我用记事本…++:p

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 至少记事本++

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 自从@rkh 开始维护

它就真的开始负荷了 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 全部塞进一个文件真的有优势吗？

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 是由布雷克·米泽拉尼(Heroku 的)

开始的 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> 使用 Sinatra 的公司包括苹果、BBC、GitHub、LinkedIn、houghtbot 等等

 |
| 格雷戈... | 

> 我目前最喜欢的两个开发应用是 PHPStorm(虽然有时有点矫枉过正(读起来很慢),但最近我很喜欢在崇高文本 2

中工作 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 好样的 rkh！

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @serverStorm 不适合大型应用程序，但适合小型演示

 |
| 蛋糕![dorthe](img/a4d6cad6a15829088148e891d28d5806.png) | 

> 能否制作一个快速入门指南——maybo 放在脸书页面上

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @ServerStorm 感谢

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 【get】/【do】【你好世界】end这就是你的基础 app

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 哇，连我都可以做到；) |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 上手…安装 Ruby，安装 Sinatra 宝石

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 看来“Hello World”是我唯一学会用任何编程语言编写的应用程序:p |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 将上面的几行另存为 hello.rb

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> 如果你安装了像样的 ruby 版本，也可以在命令行运行这个:ruby-rsinatra-e " get('/'){ ' Hello World ' } "

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 然后在终端/控制台输入 hello . Rb@ molona 哈哈，这里同样适用于我用多国语言

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 如果考虑在 ASP.NET 用 Coffee Script 构建同样的小应用，Symphony、Node.js 和 Sinatra 的开发周期是更快，还是更慢？代码基数最小吗？

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> 这里是一个完整的聊天实现，所有东西都在一个文件里:[https://github . com/Sinatra/Sinatra/blob/master/examples/chat . Rb](https://github.com/sinatra/sinatra/blob/master/examples/chat.rb)

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 感谢 rkh

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @serverStorm 有些是语言(比如 ruby)有些是框架所以不好比较

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 

> @rkh 感谢。巨大资源:D

 |
| alme1304 ![alme1304](img/12817efc1eaf9627119aa1fe712ad19a.png) | 

> 开发 app 时是否有一套社区标准或准则或者每个人都有不同的方法

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @alme1304 大问题！

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> 其他语言有大量的辛纳特拉克隆，btw。

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 没有真正的标准——这是一件好事也是一件坏事！ |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 是节点。JS 的快递类似 Sinatra？

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> 是

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 虽然使用 Sinatra 很容易构建 MVC 架构(就像 Rails 使用的)

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 是的，我只是想了解它相对于其他语言的其他框架选择有什么优势。我知道这是一个加载的问题，但从来没有在辛纳特拉写一行这是一个有效的问题

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @nicksh express 由前辛纳特拉撰稿人撰写，灵感来自于它

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 优点是开发非常快，你可以选择如何开始

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 我想知道关于资源的问题…这本书似乎是一个很好的起点，但是在哪里可以找到更多的例子/高级信息呢？ |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 感谢 daz

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) |  |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @ServerStorm 简单、安全、成熟

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 爽

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 一些较大的公司使用它，因为他们可以在一周或更短的时间内完成完整的安全审查 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 对于那些对例子感兴趣的人来说，这里有一篇关于我如何使用 Sinatra[https://www . site point . com/Sinatra-heroku-super-fast-deployment/](https://www.sitepoint.com/sinatra-heroku-super-fast-deployment/)

在几个小时内构建和部署一个简单站点的博文 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 一些公司使用它是因为它可以很好地配合他们现有的软件 |
| watzit ![watzit](img/4cea3c67be27e6a710aaed0d8b5aa771.png) | 

> 安全性呢？辛纳特拉不可能像铁轨一样扣得紧紧的？【http://guides.rubyonrails.org/security.html】[](http://guides.rubyonrails.org/security.html)

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> 例如，linkedin 将其添加到他们的 java 业务逻辑之上

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 一个很大的优势是它使用了 Ruby …而且 Ruby 是一种很好的语言！ |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @watzit 的确不行，因为它不知道你的数据库

 |
| Hiro  ![Hiro](img/f5fd4fd684c785e301d4c99849ebbbd5.png) | 

> * JRuby *；——)

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> @daz4126 更不能同意:D

 |
| 沃滕 | 为什么我要用 Sinatra 而不是 Rails？ |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @watzit 这是辛纳特拉自带的:【https://github.com/rkh/rack-protection

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @molona RubySource 有文章还有其他东西在网上飘

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> @watjen 简单

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 

> 我不懂 Ruby…你会建议我先学 Ruby 吗？还是像我学 SinatrayT3 那样学就好了？

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 【https://www.sitepoint.com/】

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 但是在读完这本书之后，最主要的事情是提高你的 Ruby …因为用 Ruby 写的任何东西都可以通过 SinatraT3 插入网络，所以限制因素变成了你的 Ruby 技能  |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 

> 是的，网络上有很多信息，但总是很难知道哪些是好的，哪些是坏的:(

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 辛纳特拉谷歌群也是一个寻找信息的好地方

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | @molona Sinatra 很简单，所以最重要的是学习 Ruby。@莫洛娜这就是@daz4126 写书的原因:) |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 这里是学习基础红宝石的好地方【http://pine.fm/LearnToProgram/】T2

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 

> @rkh 好回复:D

 |
| 蛋糕![dorthe](img/a4d6cad6a15829088148e891d28d5806.png) | 

> @daz4126 好看链接

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> @daz4126 别忘了 TryRuby :D

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 对于任何打算进入 Ruby/Sinatra 的人，我已经建立了一个 Podling 群组以寻求支持[https://Podling . com/invitations/298-talk-Sinatra-with-the-experts/325/edh FZ 8 b 6 yp](https://podling.com/invitations/298-talk-sinatra-with-the-experts/325/edHfz8B6yP)

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 辛纳特拉对阿贾克斯的处理是不是相当轻松？数据库连接呢？

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @watjen 这里有一个链接，人们用 Sinatra 和 Rails[https://www . site point . com/Rails-or-Sinatra-the-best-of-two-worlds/](https://www.sitepoint.com/rails-or-sinatra-the-best-of-both-worlds/)

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 当然还有 https://www.sitepoint.com/forums/forumdisplay.php?的论坛[227-红宝石轨道](https://www.sitepoint.com/forums/forumdisplay.php?227-Ruby-amp-Rails)

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @ServerStorm 没错，做 ajax 就是直来直去。

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 当然@ nicksh the interactive one【http://tryruby.org/】T2

 |
| srpsco ![srpsco](img/8fb76461ac4f6e470dfbb70ba0b01c98.png) | 

> @服务器风暴数据库连接非常简单，支持几种不同的数据库

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 特别是这个帖子，其中@daz4126 是专门回答[https://www.sitepoint.com/forums/showthread.php?的问题 960208-New-Book-Jump-Start-Sinatra&p = 5299210 # post 5299210](https://www.sitepoint.com/forums/showthread.php?960208-New-Book-Jump-Start-Sinatra&p=5299210#post5299210)

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @ServerStorm 有几个数据库库可以选择

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 感谢 rkh 和 srpsco

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 你可以很容易地连接到任何数据库。在书中，我们使用 DataMapper 连接到 sqlite 数据库，但是如果 nosql 是你的东西，你也可以使用 MongoDB 之类的东西 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 那 NoSQL 支持呢

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 阿贾克斯在本书第六章

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> 我们的产品使用 ActiveRecord，它最初来自 Rails

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> …而阿贾克斯在这里就涵盖了【https://www.sitepoint.com/the-robot-factory-part-two/】T2

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 我爱 active record

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 任何流行和最不受欢迎的 nosql 解决方案都有库。辛纳特拉自己并不在乎。 |
| 斯尔普斯科 | 

> @serverStorm 和 postgres 对于 heroku 人来说，你可以用 sqlite 在本地进行测试，几乎不需要做任何修改就可以转移到 heroku/postgres

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 我从来都不喜欢它，但我听说它最近变得更好了 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> 对我们来说，是我们从一个 rails 应用程序转移到许多 sinatra 应用程序

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 真好 srpsco

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @srpsco 是的，在书中，我们就是这么做的——本地使用 sqlite，然后在 Heroku 上使用 postgres 进行实时部署我非常喜欢 mongo 语法和无模式数据库的整体理念，所以今年将尝试使用它

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 

> 也得试试……我从来没用过没有 sql 的数据库

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> btw 我刚装了辛纳特拉

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 是的，我仍然不清楚对 mongoDB 或其他 NoSQL 数据库的支持 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @serverStorm 是的它支持其中大部分

 |
| 斯尔普斯科 | 

> @serverStorm mongodb 支持

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 伟大的

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @nicksh 好样的！你会喜欢的

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @ServerStorm 有一个名为 MongoMapper 的库和一个名为 Mongoid 的替代库，它们通过 Sinatra@ server storm【http://mongomapper.com/】

为您提供顶级的 MongoDB 支持 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 谢谢 rkh :)

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 我非常喜欢 Sinatra 的另一点是，你可以更多地看到正在发生的事情……魔法变少了，所以你可以更好地控制你的应用程序的行为 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 今天晚些时候

，我会把所有这些资源和文字记录一起汇集到 sitepoint.com 上的一个帖子里 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @ServerStorm MongoMapper 也有一个类似于 DataMapper 的 API，用来和书中的 SQL 数据库对话

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 如何让它在 80 端口运行？

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> ——p80

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> ruby main.rb -p 80

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 是的，我对不同的数据库模式非常熟悉，所以我期待这本书

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 

> @daz4126 你是说 Sinatra 更容易调试？

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> 大概跟一个须藤

 |
| 蛋糕![dorthe](img/a4d6cad6a15829088148e891d28d5806.png) | 

> tx 鹰

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @molona 不一定……那要看你的代码了！

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> @rkh 还运行在 4567

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 但是你只是更了解你的应用程序中的所有东西是如何组合在一起的，所以如果你想改变一些东西，那么你知道它是如何工作的 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 欢迎@guido2004 和@ richybailey——随时跳进去我们的专家是@daz4126 和@rkh

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @richybailey 嗨 mate！

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> btw 我爱辛纳特拉[查看/编辑粘贴](https://sitepoint.talkerapp.com/rooms/28392/pastes/36c2eedaf07d20488834 "Paste #36c2eedaf07d20488834")
> 
> ```
> == Sinatra has ended his set (crowd applauds)
> ```

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @nicksh 你看到缺页和错页了吗？

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @nicksh 怪，对我有用

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 没有

 |
| guido200… ![guido2004](img/a52f50cdd09b498004f9a699ac45c37e.png) | 

> 嗨尽

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> @nicksh 太搞笑了！

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 哈哈牛逼

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 

> @ Guido 2004嗨 

 |
| 斯尔普斯科 | 

> @Daz4126 我在书上看到你用了一个类似‘what/time/is/it/in/:number/hours’的 url，在很多中间 URL 都不会存在的制作中你会这么做吗？

 |
| guido200… ![guido2004](img/a52f50cdd09b498004f9a699ac45c37e.png) | 

> 嗨莫洛娜:)

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 

> 欢迎来到辛纳特拉世界:p

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> @ Guido 2004嗨 

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> @daz4126 错误页面令人惊叹:D

 |
| guido200… ![guido2004](img/a52f50cdd09b498004f9a699ac45c37e.png) | 

> 嗨服务器风暴

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @srpsco 可能没有(除非我直接链接到它)这只是为了演示命名参数@nicksh 很棒不是吗…你可以让你自己的真的很容易虽然

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 是的给我唱首歌 guido2004，Sinatra 这里:0

 |
| 斯尔普斯科 | 

> @daz4126 感谢只想澄清

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @nicksh not_found { "那一页不见了" }

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 哇好听

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 你就是这样自定义缺页的

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 哦我忘了把端口设为 80【http://www.sinatrarb.com/configuration.html】

 |
| 沃滕 | 

> 我如何在运行 Apache 的服务器上运行 Sinatra 应用程序？

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @watjen with passenger(又名 mod_ruby)这基本上与 mod_php

的工作方式非常相似 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> NGINX 如何支持 Sinatra？

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> 啊，modrails 的名字是

 |
| 斯尔普斯科 | 

> @watjen 您也可以设置代理转发到辛纳特拉港

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 

> 我也在想同样的问题@serverStorm

 |
| richybai…  ![richybailey](img/f50354794cc2d1df1cd012414798f6ff.png) | 

> 嗨 daz。大家好。我是入侵者，因为我使用。但由于 daz 一直在试图改变我，我想我应该看看社区对 Sinatra 的看法。

 |
| 沃滕 | 

> 爽

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 顺便问一下我如何传递参数？

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @nicksh 要什么？

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 我的意思是从网址

访问东西 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 我用的是 C#，Node.js，PHP

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @nicksh 为网址'/hello/:name'

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @nicksh:那就是你用 params 换

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 您可以使用 params[:name]

访问 url 中的名称部分 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 感谢

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 那就是所谓的命名参数

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 哦

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 欢迎@headmin 和@ Sebastien

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 这样就可以做get '/hello/:name '做

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> 顺便说一句，对于所有非母语人士(比如我)来说，sinatra 官方文档已经被翻译成除英语

之外的 10 种不同语言 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> “你好# { params[:name]}”end

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> @daz4126 感谢

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @ nicksh NP

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> @ molona NGINX 的一个选项是 Phusion 客运[http://www . modrails . com/documentation/Users % 20 guide % 20 NGINX . html](http://www.modrails.com/documentation/Users%20guide%20Nginx.html)

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @richybailey Sinatra 比大多数其他选择更简单，它让你可以更实际地构建应用

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 

> 爽。很高兴看到文档也是西班牙语的…毕竟它是世界上最好的语言(至少对我来说，因为那是我说得最好的语言:p)

 |
| 标题 | 

> 今天有点晚了，看了 sinatra jumpstart 样除了潜伏在这里……

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> 可惜@daz4126 的书只有英文

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 西班牙语是一种美丽的语言而不是英语的大杂烩

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @头民希望你喜欢:)

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 下面是我目前为止所写的[查看/编辑粘贴](https://sitepoint.talkerapp.com/rooms/28392/pastes/43232a8ea2352a31c866 "Paste #43232a8ea2352a31c866")
> 
> ```
> require 'sinatra'
> 
> set :port, 80
> 
> get "/" do
> 	"Ahoy World!"
> end
> 
> get "/hello/:name" do
> 	"Hello, #{params[:name]}"
> end
> 
> not_found do
> 	"Yerr port not scoured!"
> end
> ```

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @rkh 你没提供翻译！

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 

> @服务器风暴坦克:D

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @nicksh 干得漂亮！

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @daz4126 我连自述都没翻译，别人那么做；)

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 

> 我的意思是感谢:D

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @rkh …你真懒；)

 |
| srpsco ![srpsco](img/8fb76461ac4f6e470dfbb70ba0b01c98.png) | 

> @nick "yerr port …"看来你已经准备好像海盗一样说话了

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 顺便说一句，如果有人感兴趣的话，这是我的 GitHub 账号【https://github.com/shvelo也许我很快会发布一些 Sinatra 代码

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @nicksh 绝对希望看到它在你做的时候推特我@daz4126

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 本次会议还剩 15 分钟，如果您有任何未回答的问题，现在可以在

提问 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> @srpsco 是的我是@daz4126 确定

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @nicksh 和@srpsco 随意把书翻译成盗版:)

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 语言发展有路线图了吗？如果是，框架多久更新一次？ |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> @daz4126 好主意！

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @richybailey …然后你就可以和辛纳特拉T3 一起使用 Ruby 了

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 更新了

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @serverStorm atm Sinatra 只是获得增量更新，因为它基本上做了它需要做的事情

 |
| 沃滕 | 

> Sinatra 内置支持对象关系映射吗？还是需要安装 ActiveRecord 之类的东西？

 |
| 斯尔普斯科 | 

> 感谢辛纳特拉演唱的那幅图像，是什么让那只坏血病的瘸腿蚂蚁认为它能移动那棵橡胶树

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @watjen 你需要安装一些东西，但那真的很简单

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @watjen 否，你需要安装任何你想用的 ORM

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> @daz4126 可以卖翻译吗？:D

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 感谢@ daz 4126

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @当然可以，不过要 50%的版税！

 |
| 蛋糕![dorthe](img/a4d6cad6a15829088148e891d28d5806.png) |  |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 该走了来不及了

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 如果你看看 Sinatra 的代码库，你会看到我所见过的最接近完美的代码。它基本上做了所有需要做的事情，仅此而已。 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> @dorthe 我不太明白你的问题。到哪里可以安装 Ruby 的最佳链接？

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 你用的是 Linux，Mac 还是 Windows？ |
| 蛋糕![dorthe](img/a4d6cad6a15829088148e891d28d5806.png) | 

> 是

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 出发前@nicksh

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 很高兴收到你的来信@nicksh，希望能在推特上看到你

 |
| 蛋糕![dorthe](img/a4d6cad6a15829088148e891d28d5806.png) | 

> 视窗

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 我想你今天的努力已经为你赢得了一本免费的达伦的书(如果你还没有的话)？ |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 哦对了我的 twitter 句柄是@ shvelo 96没有我没有 

 |
| 斯尔普斯科 | 

> @Daz4126 有没有指导什么时候用宝石 vs 架中品和自己滚

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 哇感谢

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 感谢@ nicksh

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @服务器风暴…而且大多数对 Sinatra 的更新基本上都是修复 bug 或者实现一些新的东西(比如补丁请求)

 |
| 尼克什![nicksh](img/3a7858151c7098237bc23f5fda69a82c.png) | 

> 不客气，见丫

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @srpsco 机架中间件就是代码是什么，gem 就是你怎么装。大多数机架中间件都是瑰宝。

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @nicksh 见亚

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> Ok @ daz 4126感谢 

 |
| 蛋糕![dorthe](img/a4d6cad6a15829088148e891d28d5806.png) | 

> 感谢

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 有什么问题是人们没有充分答案的？

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 我确实认为我会在这个周末认真阅读:) |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @srpsco 我总是喜欢自己制作(有很多乐趣和学习)…但是对于制作来说，你通常使用已经存在的宝石更安全。让我知道你如何使用@莫洛娜

 |
| 蛋糕![dorthe](img/a4d6cad6a15829088148e891d28d5806.png) | 

> @ molona–me too

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 同@dorthe

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 我已经筋疲力尽了，所以我来竞拍。感谢@rkh 和@ daz 4126 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 和@mibsrpsco

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 见雅妮子。

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @serverStorm 感谢所有的问题，会在推特上抓住你

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 当然…我真的很期待。从未尝试过 Rube，我猜这是一个很好的借口开始 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 是的，你会的，我会更详细地调查 Sinatra，然后开始写你的书，谢谢！ |
| 蛋糕![dorthe](img/a4d6cad6a15829088148e891d28d5806.png) | 

> 很高兴参加这个环节

 |
| ServerSt… ![ServerStorm](img/e4fd87eca33bd28e72546376c89082c7.png) | 

> 再见

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @molona 这很有趣，Sinatra 会让你很容易快速开始构建应用@ server storm byee！

 |
| 沃滕 | 我想我得试试辛纳特拉！ |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 很荣幸有你在这里！ |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @watjen 你绝对应该！

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 如果有人想到了什么问题，就在论坛上发帖，我会尽力回答的[https://www.sitepoint.com/forums/showthread.php?960208-New-Book-Jump-Start-Sinatra&p = 5299210](https://www.sitepoint.com/forums/showthread.php?960208-New-Book-Jump-Start-Sinatra&p=5299210)

 |
| 沃滕 | 

> 把一个 rails app 移植到 Sinatra 很难吗？

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @george 关于股票 api 的答案对你来说没问题吧？

 |
| 斯尔普斯科 | 

> @daz4126 感谢你的时间和 myway 系列的帖子在我学习 Sinatra 的时候帮了很大的忙(大概和你写帖子的时间差不多。

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @srpsco 哇，很高兴听到

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @watjen 取决于 app，但一般不会，我们做了那个

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> 我希望很快能让我的方式重新上线！

 |
| 沃滕 | 

> 好听的

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> 既然都用 rack 和 ruby 很多零件真的很相似

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 在我解雇我们的专家之前最后一次征集问题……

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 

> @daz4126 在这样的日子里，会有更多的“黑夜中的陌生人”……这些地区周围是夜晚，毕竟我们大多数人从未见过面:p

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @莫洛娜…哎呀…:o 

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> “最好的复仇就是巨大的成功。”

 |
| molona  ![molona](img/2bba7665aebcd40fc89293e43290126f.png) | 我也要去睡觉了。很棒。谢谢你回答我的问题(在这里和在论坛里)。非常感谢你的时间(你的也是@rkh)@rkh LOL |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> 不管复仇是为了什么，但辛纳特拉的名言

很好听 |
| 蛋糕![dorthe](img/a4d6cad6a15829088148e891d28d5806.png) | 

> @莫洛娜好一个。我在看丹麦普朗手球:-)

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @rkh 大语录，之前没听过

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @莫洛娜确定的事情

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 我一直不太明白手球到底是怎么回事！@rkh 需要尝试在网站上或代码库中某处获得报价；) |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 嗯，那是我的一个裹友。我想说非常感谢达伦和康斯坦丁的时间。

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 没问题，非常有趣！ |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 你可以随意在这里逗留，只要你喜欢，可以聊天，但不会有节制。 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) |  |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> 我马上就要走了，晚点给我开饭。

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 

> 优秀。我得走了，因为我有唠叨的孩子让我伤心，但我们会在网络空间再见的。

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @rkh 你应得的:D

 |
| 霍克![HAWK](img/59d81dcdaeee5a382748342403165ae8.png) | 今天晚些时候，我会把文字记录贴上去。 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @HAWK np，很快就抓到你

 |
| 蛋糕![dorthe](img/a4d6cad6a15829088148e891d28d5806.png) | 

> @霍克见 u

 |
| rkh ![rkh](img/f27d628f6d4a1c00c5787a82bede02e4.png) | 

> @daz4126 谢谢……但是你写的书:D

 |
| daz4126 ![daz4126](img/abe58e3bfd73d09c4d9776dc64b1c010.png) | 

> @rkh 多多帮忙…而你维护辛纳屈！

 |

## 分享这篇文章