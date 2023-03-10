# SitePoint 播客#103: Ruby 已经到了可以喝酒的年龄了

> 原文：<https://www.sitepoint.com/podcast-103-ruby-is-old-enough-to-drink-with-peter-cooper-and-jason-seifer/>

*第 103 集 site point 播客*现已推出！本周，路易斯·西蒙诺( [@rssaddict](http://twitter.com/rssaddict) )与红宝石秀的彼得·库珀([@彼得·克](http://twitter.com/peterc))和杰森·塞弗( [@jseifer](http://twitter.com/jseifer) )畅谈红宝石世界的最新动态。我们讨论了 Rails 3.0，Ruby beyond Rails，以及 Rails 是否可以伸缩这个永恒的问题。

## 在浏览器中收听

直接在你的浏览器里播放这一集！只需点击下方橙色的“播放”按钮:

<audio controls="" preload="metadata"><source src="https://s3.amazonaws.com/s3.sitepoint.com/audio/podcasts/sitepoint/sitepointpodcast103.mp3" type="audio/mpeg; codecs=&quot;mp3&quot;"></audio>

## 下载这一集

你也可以下载这一集作为一个独立的 MP3 文件。以下是链接:

*   SitePoint Podcast #103: Ruby 已经到了可以和 Peter Cooper 和 Jason Seifer 一起喝酒的年龄了

## 本集提到的链接

*   [红宝石秀](http://5by5.tv/rubyshow)
*   JRuby
*   [MonkeyBars](http://monkeybars.rubyforge.org/)
*   [鲁宾纽斯](http://rubini.us/)
*   [Alioth 枪战基准](http://shootout.alioth.debian.org/u32/which-programming-languages-are-fastest.php)
*   [Phusion 乘客](http://www.modrails.com/)
*   [Heroku](http://heroku.com/)
*   [为什么是 Ruby 的辛酸指南](http://mislav.uniqpath.com/poignant-guide/)

## 采访记录

路易斯:今天和我们一起参加播客的是彼得·库珀和杰森·塞弗。你好彼得。

彼得:嗨，你好，最近怎么样？

路易:很好。嗨杰森。

杰森:你好，你好吗？

路易:我很好，你好吗？

杰森:我做得很好。

**Louis:** 好的，Peter 和 Jason 现在是[Ruby Show](http://5by5.tv/rubyshow)的主持人，这是一个关于 Ruby 所有事情的播客，所以我想我们今天应该让他们来谈谈 Ruby 在哪里，Ruby 社区发生了什么，Ruby on Rails 是如何发展的；框架的第 3 版最近才发布，所以我认为这是一个从 Ruby 社区向我们的听众进行更新的好机会，你们中的一些人可能对 Ruby 感兴趣，但你们中的许多人并不仅仅是为了看看事情的进展。但是在我们开始之前，你们能给我一些关于你们自己的背景吗，你们是如何进入这个行业的，你们是如何进入 Ruby 的，你们是如何开始播客的，你们现在在做什么；那么彼得。

**Peter:** 对，自从我父亲在 80 年代给了我一些旧的微型电脑后，我就一直在编程，我们在 80 年代早期在英国有一台旧的微型电脑，主要是一台叫做 BBC Micro 的机器，我不认为它曾经在英国生产过多少，但它在这里确实是一件大事。 所以我在很小的时候就学会了摆弄它和编程等等，你知道，这是我从小到大的一个爱好，先是 Basic，然后是 C，然后是 Pasquale，我又回到了前面； 我开始用 C 语言，然后我开始在这个链上向下移动。然后我做了大约八年的 Perl，我想这可能在 Ruby 出现之前把我逼到了自杀的边缘(笑声)。是的，大约在 2004 年，我加入了 Ruby，起初我并不想加入，因为那时编程对我来说已经变得有点消极了，但它完全吸引了我，完全赢得了我，从那以后我真的很喜欢它。就播客而言，它绝对不是我真正想做的事情，但在过去一年左右的时间里，我认识了一些参与其中的人， 做这样的采访似乎很有趣，然后有机会与 Jason 在 Ruby Show 上合作，所以我只是——我们一直相处得很好，所以这似乎是一个很好的机会，在旧 Ruby 场景中进行一点社区，而不是社区，一点喜剧，所以我必须抓住它。

路易斯:好的，很好。你呢，杰森？

**Jason:** 和 Peter 的故事相似，我一生都在编程，我从大约六岁开始，虽然有点相反，我从基本开始，只是看着我爸爸做一些编程，然后随着时间的推移，我转向 PHP，写了一堆 Perl，实际上一点也看不懂(笑声)，然后可能就在 Ruby on Rails 1.0 发布之前，我开始真正进入 Rails，然后用 Rails 做 web 开发咨询，从那以后一直在那里。

**路易:**对。那么，我想接下来我想为我们的观众简单介绍一下，对于可能听说过 Ruby 或 Ruby on Rails 的人来说，你能简单介绍一下 Ruby 的特别之处吗，是什么吸引了你，Rails 的令人兴奋之处是什么，为什么你们两个都参与了这件事？

杰森:彼得，你想先来吗？

**Peter:** 是的，我会用 Ruby，然后我会让 Jason 更深入地研究 Rails。是的，我真正喜欢 Ruby 的一点是，人们称它为纯粹的面向对象语言，我不敢说它是 100%纯粹的，但与我用过的其他语言相比，它非常接近。这实质上意味着，你在 Ruby 中拥有的几乎所有东西，任何类型的数据，甚至像你只需键入一个数字，你就可以调用它的方法，所以一切都像一个对象。是的，我的意思是这只是 oft 的吸引力，因为你从来没有这样做，你没有明显的模糊性，无论你在什么东西上调用一个方法，或者你是否有一个函数，或者像你在 Python 中一样，例如，在 Ruby 中你可以只取一个字符串，你可以只问它的长度，你只需要通过那个方法接口做任何事情，这真的很吸引我。这是因为 Ruby 的背景，创造它的人叫松本幸宏，但我们都知道他叫 Matz 他在 1993 年开始，他对他正在使用的一些语言有点厌倦，想做一些我认为他用了“joy”这个词的东西，他想用起来有趣，他会发现真的很喜欢使用的东西。所以他和其他人一起想出了一些主意，想出了 Ruby 这个名字，这个名字有点基于 Perl 的成功，但会在字母表中向上移动，所以他一直热衷于它必须是令人愉快的，它必须是令人愉快的，但他也相信所谓的最小惊讶原则，所以如果你读一段代码，它应该是相当明显的。正如我和 Jason 所说的，Perl 肯定不是这样，但是你实际上可以用 Ruby 做很多事情，因为它非常动态和灵活，你可以重写 Ruby 的工作方式，所以你可以重写加法函数，或者是加法方法，对整数或其他任何东西进行重写，这样数字相加的方式就完全改变了，所以你实际上也可以用这种方式使用 Ruby。但是，总的来说，如果你遵循惯例，并且 Ruby 爱好者非常热衷于遵循惯例和最佳实践，如果你遵循这些，那么大多数 Ruby 代码看起来非常容易，非常容易阅读，这是真正吸引我的地方，因为我来自 Perl，在 Perl 中，即使你自己的代码也可能在几周内变得难以理解。

路易:对。所以 Ruby 是作为这种脚本语言开发的，就像你说的那样，它意味着快乐和真正面向对象，然后我想发生了一些事情，人们突然开始在 Web 上使用它，我想 Ruby on Rails 可能是最大的驱动力。Jason，你想给人们一些关于 Rails 的背景知识；我知道很多人可能至少对 Rails 背后的思想非常熟悉，因为它启发了其他语言中的许多框架，所以在 PHP 中，有一个 CakePHP 与 Rails 非常相关，但可能只是简单介绍一下它是如何产生的背景，以及为什么您认为 Rails 是蜜蜂的膝盖。

**Jason:** 所以，在 Rails 发布之前，肯定有大量的 web 开发在进行，我的意思是互联网转变为丰富的互联网应用，大量的 web 开发；在 Rails 出现之前，当启动一个新的应用程序时，有许多仪式和事情需要重做几次。所以，Rails 来了，说，嘿，有很多我们经常做的东西，我们可以打包，使它更容易使用，省去编写 web 应用程序的许多仪式，所以过去需要很长时间才能设置好的东西，现在基本上已经为你完成了；在一个 Java 应用程序中，不是有很多配置，而是有很多约定。

Louis: 在 Rails 中，像将一组 URL 映射到类中的方法或者仅仅是与数据库交互这样的事情，在大多数时候你甚至都不用考虑。

杰森:没错。你知道一个很好的例子，假设你想要——如果你有一个图书的数据库表，你可以调用一个类图书，并从 ActiveRecord 扩展它，Ruby 和 Rails 会为你做很多事情，给你一大堆非常简单的方法来查询数据库，我的意思是对象关系映射器不是新的，但 Rails 使它非常非常容易进入，开始并富有成效。

路易斯:好了，现在我们来谈谈 Rails 3。我猜可能有很多人在听，他们熟悉 Rails，熟悉对象关系映射以及 Rails 和其他 web 开发框架使之成为可能的所有事情，但可能只是在有新版本时快速浏览了一下；如果你不经常使用 Rails，你知道，我不会注意到 Django 的新版本，所以我不知道有什么新东西。所以，也许让我们来聊聊 Rails 3 中的新特性及其原因——我的意思是，从我所看到的一切来看，它看起来像是一个相当重要的版本，所以也许你可以谈谈这一点。

**Peter:** 我想 Jason 可能对它的一些日常问题知道得更多，但我想首先介绍的一件事是，从某种意义上来说，Rails 3 几乎是整个重写，你知道，Yehuda Katz 是它背后的主要驱动力，它来自于 Rails-Merb 的合并，但它并不完全是合并， 这更像是让我们带着 Yehuda 和一些 Merb 的工作人员，像他们在 Merb 上做的那样，实现他们关于如何构建一个 web 框架的一些想法。

Louis: 简单介绍一下背景，Merb 是另一个 Ruby web 框架。

**Peter:** 过去是，现在已经不是了，因为它被合并到了 Rails 中，但是仍然有一个单独的 Merb 版本，但是它并不是特别受欢迎，它实际上是出于维护的原因而保留下来的。但他们做了这种合并，并带来了耶胡达·卡茨和其他一些人，他从根本上接受了 Merb 中的许多模块化并将其融入 Rails，因此这涉及到重写许多核心内容，如路由系统的改变和 Rails 中所有这些微小的东西，但它们都必须以显著的方式进行改变，以处理这种模块化， 实际上，令人惊讶的是，他成功地协调并实现了这一点，但他做到了，我们现在有了 Rails 3，但 Rails 3 确实是，你知道，许多 API 表面上看起来非常相似，但实际上它几乎是一个全新的东西。

Jason: 当你真正开始使用它并编写一个 Rails 应用程序时，这真的很令人惊讶。以 Rails 的前一版本 Rails 2 为例，将 Rails 2 升级到 Rails 3 只需进行很少的重写，因为正如 Peter 所说，API 在很大程度上非常相似，所以我的意思是，就像我移植了几个应用程序一样，这可能需要四个小时到一天的工作，实际上并不像你想象的那样糟糕，本质上是对整个框架进行重写。但是就像 Peter 说的，Merb 是 Rails 的一个竞争框架，但是在这个意义上的竞争对于如何进行完整的 web 开发有不同的想法，所以 Rails 会给你这个庞大的框架和应用程序，Merb 给你这个选项，但是让你把它分成不同的部分，所以你可以说我不需要整个框架，只要给我这几个部分，我就可以构建我的应用程序。现在，如果你打算在 Rails 3 上写一个新的应用程序，它会给你一切你需要的东西，但是你也可以选择更多的模块化部分。

路易斯:对，没错。对于那些只是坐在外面看着 Rails 3 的人来说，你已经有了这种模块化，你可以举一个例子，如果你正在启动一个 Rails 应用程序，你可能会换出一个不同的组件；什么类型的东西，比如在你的开发中有没有你经常使用的替代模块？

**Jason:** 是的，一个很好的例子是，有一个很多人使用的测试框架，Rails 捆绑了测试单元，但是 Ruby 社区在测试方面非常强大，大约每三周半到四周就会发布一个新的测试框架，你可以很容易地将它们替换掉。另外，默认的 ORM 叫做 Active Record，如果你想选择 MongoDB 之类的东西，你可以很容易地放入 Mongo Mapper 之类的东西来与 MongoDB 对话，Rails 会很容易适应它。

**Louis:** 我想这在以前的 Rails 版本中是可能的，很多人在 Rails 2 中使用 RSpec，但你所说的是 Rails 3 只是内置了更多的模块化，所以它真的被设计成允许你去掉任何你不喜欢的组件，并用你喜欢的另一个组件替换它。

杰森:没错。

**Louis:** 你刚才说好的，所以 Rails 社区非常重视测试，每三周半到四周就会发布一个新的测试框架，现在你可能对这个数字开了一点玩笑，但我确实从 Rails 社区得到了这样的印象，哦，我们现在都是这样做的。这可能有点吓人，但是如果你回顾 Rails 1 和 Rails 2 的早期，你会发现，哦，这就是我们在 Rails 中做事情的方式，这就是你如何构建一个示例应用程序，非常简单，继续学习它并构建你的应用程序。如今，这个社区似乎已经成长了很多，每个人都有自己喜欢的做事方式，所以对于那些来自外部的人来说，这可能是一种威胁。如果有人想开始使用 Rails 进行开发，并且感觉到，哦，社区中的每个人对我应该如何做每件事都有不同的想法，那么我该如何找到做一件事的最佳方式，或者真正开始使用 Rails 呢？

**Jason:** 嗯，Rails 提供了许多内置的默认值，您可以立即开始运行，您知道，使用 Rails 的默认值，然后一切就绪。在 [Rails Guides](http://guides.rubyonrails.org/) 网站上有很多很棒的文档。

**Louis:** 是的，我看到了，与以前相比，这确实是一项巨大的文档工作；我发现 Rails Guides 是一些现成的文档。

**Jason:** 他们在这方面做得非常好，但是，是的，我的意思是，这将带你完成制作 Rails 应用程序所需的一切，然后一旦你更深入地了解它，你可以阅读更多内容，并对你想要使用的东西做出自己的决定，但这就是为什么 Rails 至少给了你一些好的默认设置，我的意思是，你不会在启动 Rails 时说，嘿， 我不知道该做什么，我不能工作，就像有些好东西是为你选择的，默认情况下，你会得到活动记录，你会得到测试单元，我相信 Rails 提供的默认 JavaScript 库目前是原型，但你肯定可以开始使用。

**Louis:** 那么 Rails 还在用 Prototype 作为默认吗？

杰森:是的。

**Louis:** 好的，这真的很有趣，因为我不认为——我想这是一个有趣的选择，因为正如你所知，在过去的两年中，很多人开始更多地使用 jQuery，所以你认为是什么促使了这个决定，是因为 Rails 社区对这个特定的库太熟悉了吗？

Jason: 我认为这可能只是一个向后兼容的想法，你可以我的意思是在大约两秒钟内投入 jQuery，它就可以开箱即用了。

路易:对。

**Peter:** 是的，我想我刚才想提出的一件事，实际上有几件事你想围绕着做，那就是如果人们还不喜欢 Ruby，他们如何收听这个节目，他们可能想要开始学习，显然这很重要，以及如何跟上 Ruby 世界的发展。但我只是想简单介绍一下周围的不同实现，因为这在几年前并不是什么大事，但现在有不同的生产可行方法来实际运行和部署 Ruby 代码。所以有一种核心，实际上是官方的，在引号中，Ruby 实现被称为 MRI，代表 Matz 的 Ruby 实现或 Matz 的 Ruby 解释器，我不记得确切的 I 是什么，但它有几个版本， 目前的主要版本是 1.8.7，这是一个稍微旧一点的版本，但人们仍然在使用它，因为它对一些旧的应用程序 Rails 2 应用程序和许多已经存在的代码有好处，但在去年，自 8 月份发布以来变得重要的是 Ruby 1.9，特别是 8 月份发布的 Ruby 1.9.2，这是一种重写，真的有更快的虚拟机， 性能更好，内存使用更好，等等，这些都是事实上的官方版本，它们是用 C 编写的，针对大多数平台编译的，你可以很好地使用它们。 但也有 [JRuby](http://www.jruby.org/) 在过去几年里已经完全可以生产，运行在 Java JVM 上，它是用 Java 编写的，你可以与所有不同的 Java 库进行交互，你可以组装一个 swing 应用程序，有几个不同的库可以使它变得简单，例如 [Monkeybars](http://monkeybars.rubyforge.org/) ，这也是一个很好的方法；内存使用有点高，但就能够推出它和信任它而言，你肯定可以做到这一点。还有一些其他的目前不太流行，但是有 [MacRuby](http://www.macruby.org/) 是针对 OS X 的，它给你一个更好的方法来访问一些 Mac 框架。

Louis: 那么，这是为 Mac 开发桌面应用程序的东西吗？

彼得:这不是特别针对那个，但对那个有好处；在过去一年左右的时间里，它已经成为在 OS X 推出桌面应用程序的一种好方式，我认为在未来几年里，你将会看到更多这种情况，尤其是在 Mac App Store 上。最后但并非最不重要的是，我的意思是还有一些其他的，但最后但并非最不重要的是主要的 Rubinius，它本质上是一个用 Ruby 实现的 Ruby，它不是那么明显，但基本上有一个用 C++编写的非常快速的 VM，它实际上只是需要运行的核心内容， 但是所有的主要类，像数组和散列以及不同的数据结构等等都是在 Ruby 的这种子集上实现的，所以如果你是一个 Ruby 爱好者，你想帮助和改变 Ruby 的工作方式，你可以用 [Rubinius](http://rubini.us/) 来做，我想你会看到在接下来的几年里这将成为一个更大的交易，它最终可能会取代 MRI。

Louis: 是的，你刚才谈到了 Rubinius，所以在大多数情况下，如果有人想开始使用 Ruby，那么说核心 Ruby 实现这样的主流是正确的吗？

彼得:是的。

路易斯:或者有什么好的理由去买像鲁宾纽斯这样的东西？

**Peter:** 不，现在 MRI 几乎是所有的方式，这可能是一个稍微有争议的说法，但我看不出有什么理由你会想用其他东西，除非你非常喜欢 Java，在这种情况下，你可能想从 JRuby 开始，这样你就可以使用你现有的工具。

**Louis:** 我们之前谈过测试，你说这是 Ruby 社区的一件大事，但也许可以多谈一点，因为我想这已经成为 web 开发人员的标准，而如果你看看几年前，我想 PHP 有点像业余爱好者的东西，所以人们没有写很多测试，现在这甚至成为了像 PHP 这样的东西的标准，但 Ruby 从一开始就有这种心态。那么，你思考的是什么，是社区特有的东西还是语言特有的东西；是什么让它如此专注于测试，你知道，定义 Ruby 社区的东西，甚至当你思考它时，你知道 Ruby 人是一种不同类型的程序员，你认为是什么推动了这一点？

Jason: 我不太清楚 Ruby 社区对测试的驱动力是什么，但它确实根深蒂固，我的意思是，甚至到了现在你在博客上看到的许多教程之类的东西会带你通过测试驱动开发 Ruby 的程度。但我认为这更多的是一种症状，一般来说，这只是发展的一种成长，并说这是我们所做的，而不是前一段时间编写测试是可选的，就像这些天来无论你使用什么语言都很容易找到测试框架并使用测试框架，通常测试比不测试更有意义。我认为 Ruby 有助于它的一个事实是，Ruby 是如此开放，你可以进入它，你可以对几乎任何 Ruby 类做任何事情。所以如果你像彼得之前说的那样改变，你可以改变加法运算符在某些类中的工作方式；如果你这样做了，却没有测试它能做什么，你团队中的一些其他程序员会跳到那里，他们可能不知道为什么会发生这样的事情，但是如果你有一个好的测试套件，有人弄乱了其他东西，那么你会在过程的早期而不是后期知道很多，这是你想知道的。

**路易:**对。Ruby 世界还发生了什么事情吗，我想这是我在这里感兴趣谈论的事情之一，对于那些旁观者来说，除了 Rails 3.0 的发布，在过去一年左右的时间里有什么主要的发展？

彼得:是的，我的意思是在过去的一年里发生了很多事情，我的意思是每年都以这样或那样的方式为 Ruby 而忙碌，但在过去的一年里，我们当然有了 Rails 3，但这只是许多事情中的一件， 我认为 Ruby 整体上最大的变化是 Ruby 1.9.2 的发布，我知道这听起来只是一个单点发布，但它有点像 Ruby 1.9 的第一个可靠的完整产品质量版本，你可以真正依赖它，人们实际上已经开始转向它了。 所以 Rails 3，关于 Rails 3 的一件大事是，它可以在 Ruby 1.9.2 上完美而流畅地运行，这让开发人员可以升级到 1.9，性能通常更好，并且在幕后有很多增强，对 VM 的增强以及一些语言增强，它让人们可以全面升级到最新的技术，所以你可以运行 Ruby 1.9，你可以运行 Rails 3，这几乎都是在去年发生的，就像在过去的六个月内，所以就有了这些。但是除此之外，正如我所说的，这些实现已经有了很大的进步，JRuby 的一些重要版本已经使它变得更快，性能更好；Rubinius 现在有些人会说是完整的产品质量，我还不是 100%确定，但我还没有真正玩够它，MacRuby 也是一样，整个想法是能够用 MacRuby 构建一个完整的 Mac 应用程序；这在一年前是可能的，但你不一定想依赖它，而现在它相当值得信赖。因此，我认为去年发生的最大的事情不一定只是某些库的发布之类的东西，它实际上只是实现的成熟，当然还有一些项目，如 Rails，但就像它形成的整个基础一样成熟，它更容易部署，更容易托管，事情更可靠，我认为这是 2010 年的标志。

Louis: 好的，对那些现在可能正在考虑搬到 Ruby 的人来说，这显然是个好消息。你在那里谈了一点关于性能的问题，我想很快地谈一下，因为这是你经常听到的从其他语言进入 Ruby 的开发人员的抱怨之一，就是没有他们习惯的那么快。你能不能解决这个问题，谈谈这个问题，因为我知道更高的版本，比如 Ruby 1.9，要快得多，但也许只是谈谈一般情况，性能有什么变化，它有什么影响，以及在 Ruby 领域人们如何处理这个问题。

彼得:我想实际上是以前；我要做的是检查 Ruby 端，让 Jason 检查 Rails 端，因为两者都有重要的考虑因素。但是对于 Ruby 来说，它的历史观点和声誉一直是 Ruby，哦，Ruby 是一种缓慢的语言，但是很明显，与其他任何东西相比，这并没有考虑到开发 Ruby 脚本所花费的时间。这是一种开发速度非常快的语言，即使与 Python 和 Perl 等其他语言相比，过去的一些实现非常慢。但是用 Ruby 1.9 完成的工作，特别是用 JRuby 完成的工作，JRuby 具有即时编译功能，所以随着它的发展和完善，你的脚本实际上会变得越来越快，从编译的角度来看，这些事情实际上已经将 Ruby 带入了那些类型语言的正确性能范围， 因此，如果你看看流行的 [Alioth Shootout 基准测试套件](http://shootout.alioth.debian.org/u32/which-programming-languages-are-fastest.php)，它们在网上发布了大量不同的每种语言的基准测试，你看看现在的结果，JRuby 基准测试实际上有更低的，抱歉，有点像 a——在运行测试所需的时间方面，它的中值分数比 PHP 低，所以它实际上比 PHP 快一点。 这是非常好的，因为只有这样才能在不影响语言开发容易程度的情况下快速前进，所以很明显，如果你喜欢 C 语言，如果你喜欢图表顶端的语言，这很好，但 Ruby 确实给了你很多很酷的东西，比如这种纯面向对象和最小惊奇原则，以及所有这类覆盖和制作 DSL 的东西，所有这类东西， 但是现在它给你一个预期的速度水平，所以它不像以前那样完全像狗一样慢，它现在更像你所习惯的。 当然，Rails 也与此相关，因为很多或者大部分开发人员在 Ruby 上使用 Rails，这也使得 Rails 的速度稍微快了一点，但是这些年来，Rails 也有自己的性能问题，特别是可伸缩性，我知道 Jason 非常想谈论这个场景。(笑声)

路易斯:我的意思是，在开始讲这个笑话之前，你可以先介绍一下这个笑话的背景，以及你为什么会笑，因为我们的一些听众可能不熟悉这个笑话的背景。

**Jason:** 好吧，很长一段时间以来，有一个我不想说的长期笑话，但似乎有一段时间有大量的博客帖子写道为什么 Rails 不能扩展，为什么人们坚持使用 PHP 或 Java 来满足他们的 web 开发需求，我认为这在当时非常有趣，因为即使在那个时候， 这可能是三年或四年前，可能接近三年，当时仍有相当多的高流量应用程序构建在 Rails 上，可伸缩性良好； 我决定在 Ruby 社区里把它变成一个笑话，于是我注册了 CanRailsScale.com 的，事实上我对此很自豪；如果你在谷歌上搜索 Rails Scale，这是第二个结果，至少在美国是这样，它只是一个说“不”的网页(笑声)。哦，哇，现在是第一了。

路易斯:所以你只是想拥有它，然后彻底改变它。

杰森:对，没错。

路易:那么我们现在处于什么阶段？所以你说，也许三年前有一些高流量的 Rails 应用和网站，现在有了更多。

杰森:是的，当然。

路易斯:你对目前的形势有什么看法？

**Jason:** 和以前一样，你可以扩展一个架构，而扩展是依赖于应用而不是框架的。我不认为有太多的变化，我仍然不知道有任何框架会让你成为“web scale”，就像这些天孩子们说的那样，开箱即用，你知道这真的是依赖于应用程序的东西，每个 web 应用程序特别需要不同的伸缩，并且在如何实现这一点上有不同的需求；可能有很多相似之处，但是你不应该完全依赖这个框架。

路易:好的。所以，说回来有点跑题了，我们最近在谈论 Rails 和 Ruby 的变化，你说很多实现已经成熟，事情变得更快了。Rails 的另一个有趣之处是，我想对于学过一点 web 开发 PHP 的人来说，它最初吸引 web 开发学生的一个原因是，你可以编写一些 PHP 脚本，将它们放在一个共享的托管服务器上，它附带了 PHP 和 MySQL，一切都为你配置好了。在 Rails 的早期，需要设置大量的服务器来启动和运行你的应用，现在有什么不同吗？如果有，有什么不同？

是的，明显不同。在过去的几年里，它确实发生了变化，而且变得更好了，你知道，过去有必要专门在自己的守护进程上部署一个 Ruby web 应用程序或 Rails 应用程序，并在某个端口上运行这种守护进程集群，然后设置你的 Apache 或你想用来代理这些应用程序的任何东西， 在 2005、2006 年左右，FastCGI 是一种非常流行的部署 Rails 应用程序的方式，它非常糟糕，非常不可靠，性能不好，等等。 但我记得是在 2008 年初，一家名为 [Phusion](http://www.phusion.nl/) 的公司，或者更准确地说是后来成为 Phusion 的几名计算机科学家，发布了一款名为 [Passenger](http://www.modrails.com/) 的东西，当时我记得它只是被称为 mod_rails，类似于 Apache 传统的 mod _ what，比如 mod_perl 或其他什么， 这是 Apache 的扩展，它主要做的是在后台运行 Ruby 进程，管理它们，管理它们，自动代理所有的东西，它仍然以类似的方式运行，但现在也适用于 Nginx。 是的，你现在可以像一个乘客一样运行任何你喜欢的 Ruby web 应用程序，在任何安装了乘客模块的地方都非常简单，虽然不完全像 PHP 那么简单，但也非常接近了。此外，还有其他服务以略微不同的方式推出了这种工作，但例如 [Heroku](http://heroku.com/) 托管服务非常受欢迎，这有点像 Ruby web 应用程序的云托管服务， 你可以做的是，如果你用 Git 管理你的应用程序，例如，你可以用 Git 将你的应用程序推送到 Heroku 服务，他们将使用 Git 将你的应用程序推送到 Heroku 服务，他们将部署和运行它，并在特定的 URL 上提供它。非常简单，你知道有一个基本级别的服务，它实际上可以免费使用你的应用程序，直到它们达到一定的规模。 所以，你知道，在过去的几年里，就部署而言，事情真的发生了很大的变化，现在你不需要拥有一个系统管理员或任何人的学位就可以完成它。

**路易:**对。这是我对它的理解。有意思的是，我以前没有听说过 Passenger 现在也能在 Nginx 下运行，所以对于那些对使用 Apache 做这类事情不感兴趣的人来说，这是一个有趣的发展。

彼得:当然。

Louis: Jason 你有什么要补充的吗？

杰森:这是一个很棒的总结(笑声)。

**Louis:** 我在听的时候，我在想 Jason 会在某个时候加入进来，随着时间的推移，我想，哦，这真的涵盖了我想问的所有问题，我不认为我们能从这一点上取得任何进展。

Jason: 是的，我想我可能会提到的唯一一件事就是 Heroku 最终变得非常棒，就好像这个想法也被复制到了其他框架中。

路易:是的，我只是很短暂地看过赫罗库，也许你可以在这方面详细介绍一下。那么，它是什么，它是一种云托管服务，类似于亚马逊的 web 服务，你只需为你使用的处理能力和带宽付费，但基本上就像你说的那样，负责所有 Rails 应用程序的部署，你所要做的就是推出源代码，其余的就交给你了，对吗？

杰森:没错。

路易:彼得说在一定程度上是免费的，截止值是多少？

彼得:是的，我不确定具体的截止时间是多少，但这是基于 CPU 的数量和同时连接的数量。

路易斯:所以如果你想写一个演示应用程序，这是一个很好的方式，只要把它放到网上，你就可以在真实的环境中与它互动，而且它可以是免费的，特别是如果它只是一个演示，没有人使用它。

彼得:是的，而且扩大规模也不贵。我的意思是 Heroku 确实有一些，你知道该服务有一些昂贵的部分来添加某些额外的功能，但就基本服务而言，它不会因为成本太高而无法扩展，但你知道你可以利用你所学到的知识，然后推出自己的 VPS 或使用 EC2 或任何东西，然后从那里开始自己做，有了像 Passenger 这样的东西，它就很容易安装，如果你不想付钱给 Heroku，你也可以很容易地推出自己的东西。

**Jason:** 我想说的是，与一年前、两年前相比，托管的另一件大事是我们为您的应用程序提供的监控工具，几年前您还没有这些工具，我说的是像[新遗迹](http://newrelic.com/)和[侦察](https://scoutapp.com/)这样的东西，以及 Phusion 的人即将推出的这款新工具，它可以为您提供部署的应用程序的真正精细指标，并让您看到您在请求中花费了大量时间；这些都是非常好的工具。

**路易:**对。如果我们回顾一下我们已经讨论过的内容，这似乎是主题，所以 Rails 3 已经推出，并且已经解耦，所以你可以使用任何你想要的不同组件，托管变得很容易，在某些情况下变得非常便宜，所以如果你正在使用 Heroku，你可以在一定程度上免费使用它，所有的实现都变得非常成熟，有很多不同的实现，事情变得越来越快。这似乎是进入 Ruby 和 Rails 开发的最佳时机。

杰森:我同意这一点。

彼得:没有。

路易:(笑)彼得:不，没有比这更好的时机了，还是不，我不同意？

**Peter:** 嗯，实际上两者都有一点，因为这是一个参与的好时机，有很多工作要做，有很多机会，人们仍然在开发自己的网络应用，即使只是为了这个也很棒。但是另一方面，这些关于流行语言的指数也显示了 Ruby 的热潮，Ruby 在那些日子里处于绝对前沿的想法在那方面逐渐减弱。但我认为这在某种程度上是一件好事，因为人们现在不一定只是因为它是最新的时尚或最新的流行语而转向 Ruby，他们实际上是在了解它并看到它的优势，而不是因为他们认为它是一种时尚而自动与之对抗。所以，Ruby 已经达到了这种成熟点，这真的很有趣，因为 Ruby 上个月刚满 18 岁，所以我认为它现在已经成熟了，不再是过去的时尚了。

路易:对，你是说露比现在可以喝酒了。

彼得:对，这取决于你所在的州。

**Louis:** 好的，假设有人在听，并且他们对学习 Ruby 和 Rails 感兴趣，你会推荐什么资源？

杰森:一定要听 Ruby 秀(笑声)，我们网站上的每一集。

彼得:的确如此。

路易斯:永远不要错过红宝石秀的任何一场演出。

杰森:(笑)直接下载到 iPod 里。你知道我看到了机会，我必须跳进去。

路易:这完全公平。

Jason: 不，如果你想开始使用 Ruby 和 Rails，实际上有相当多的资源，有大量的书籍、截屏、播客，每一种你可能想吸收信息的方式都有很多。我提到红宝石秀了吗？

路易斯:(笑)红宝石秀，绝对是。我注意到最近出现的一个东西是针对僵尸的[轨道](http://railsforzombies.org/)。你想快速解释一下什么是僵尸轨道吗？因为我认为这是一个非常简洁的方式让人们可以一头扎进去。

杰森:彼得？

彼得:我只是想看看杰森会说什么。是的，基本上是针对僵尸的 Rails，我只是粗略地看了一下，它基本上是一种使用 Rails 的一些基本部分的在线训练营，所以你可以进去，你可以观看一些由 Greg Pollack 和 [Envy Labs](http://envylabs.com/) 的其他团队制作的截屏，这是一个 Rails 开发团队， 你可以观看一些截屏，然后你实际上在网络浏览器中得到这个控制台，它给你一些小练习，像[尝试 Ruby](http://tryruby.org/) 在线 Ruby 解释器，但是让你用 ActiveRecord 做一些基本练习，等等，你需要通过它来学习课程，所以这是一种有趣的交互方式来学习一些基础知识。 它不会取代一本书或任何东西，也不是学习 Ruby 的一种方式，它只是对一些与 Rails 相关的概念的介绍，但如果你想学习 Ruby，你需要走另一条路。但是，仅仅为了快速掌握一些 Rails 基础知识，这是很有趣的，尤其是对于僵尸来说，这是一种非常有吸引力的方式。

**路易:**对。所以，如果你说人们应该已经对 Ruby 有一点点熟悉了，你会说什么是跟上 Ruby 语言本身的最好方法呢？

彼得:嗯，有几种不同的方法，我的意思是，如果有人完全是编程新手，或者他们可能只知道一点 HTML 和 CSS，但对其中一些语言的逻辑方面不太了解，克里斯·派恩有一本很棒的书叫《T2 学习编程》，重点是 Ruby。我还写了一本书并出版了，书名是《T4》开头的 Ruby ，这本书实际上是写给任何以前做过一些编码的人看的，他们大概知道该做什么，但是有点想从头到尾都有自己的手，从基本的数字相加到建造像地牢一样的游戏，这是我们在书中做的事情。但是你也有更高端的参考书，比如 David Flannigan 和 Matz 的 Ruby 编程语言,所以 Matz 实际上参与了那本书的创作，那绝对是一本关于 Ruby 1.9 的极好的参考书。还有一种东西叫做鹤嘴锄，这不是它的正式名称，因为它的封面上有一张鹤嘴锄的图片，这是一本叫做[编程 Ruby](http://pragprog.com/titles/ruby3/programming-ruby-1-9) 的书，作者是迪夫·托马斯和其他几个人，不幸的是我不记得他们的名字了，但这也是 Ruby 世界中另一本受欢迎的书。所以这些都是好书。

杰森:还有同样经典的[为什么是 Ruby 的辛酸指南](http://mislav.uniqpath.com/poignant-guide/)。

路易:那还在吗？它还在网上的某个地方吗？因为我想我最近试图找到它，但我很难找到它。

杰森:是的，如果你在谷歌上搜索《为什么》这本书，你肯定能找到它的副本，如果你已经——如果你是一个 Ruby 初学者，但不是一个初级程序员，我发现的一本非常棒的书是 Russ Olsen 的《Ruby 中的[设计模式》](http://www.amazon.com/Design-Patterns-Ruby-Russ-Olsen/dp/0321490452),这本书将介绍四人组的一些设计模式，并向你展示如何在 Ruby 中实现它们。

**路易:**对。好吧，那太好了。我想我最不想涉及的事情可能是快速浏览一下那里有什么；现在，Ruby 已经成为一种更成熟的语言，而以前很多人考虑学习 Ruby 的唯一原因是在 Rails 中构建 web 应用程序，现在有许多其他项目使用 Ruby，或者基于 Ruby 的不同应用程序，您想谈谈 Rails 之外的 Ruby 以及那个世界正在发生什么吗？

彼得:对。在 Web 领域实际上还有其他项目，只是暂时关注一下，例如 [Sinatra](http://www.sinatrarb.com/) 是 Rails 的一个流行替代方案，这不像你可以拿一个 Rails 应用程序说好的，我只是要在 Sinatra 中重新实现所有这些，这将非常容易，Sinatra 确实采取了一种非常不同的方法， 它认为自己基本上是一个工具库，用于以一种非常轻量级和非常 ruby 的方式构建真正的 web 应用程序，因为你实际上可以控制一切，而 Rails 提供了所有这些约定和助手，在 Sinatra 中，你有点像是自己，你需要开发一切，但这也是一个好处， 因此，如果你试图获得绝对的最高性能，或者你正在做一些不太寻常的事情，不适合 Rails 的做事方式，Sinatra 是一个很好的方法，我想昨天，对不起，不是昨天，是上周刚刚发布了一个新版本，所以这很有趣。 还有其他系统，比如，实际上有一个几乎比 Rails 更早的系统，叫做 Nitro，我在过去几年里没怎么见过它，但我相信它仍在发布中，还有一个叫做 [Ramaze](http://ramaze.net/) 的系统，最近也在积极开发中。因此，有许多小项目专门用于研究 web 内容，但除了 Web 内容之外，我们还接触了 MacRuby 和制作 Mac 应用程序的整个想法，我还提到了 JRuby 和创建 swing 应用程序的能力，所以人们一直在做这些，这不像是实验性的或太前沿的东西，如果你确实需要用 Ruby 建立一个完全可能的基于 GUI 的应用程序。但是，我喜欢使用 Ruby 的一个原因是，我可以在幕后做很多事情，比如制作服务器进程，比如消息传递、提示或者与 Web 没有直接关系的事情，或者甚至是你可以从 Web 上调用但实际上不是典型的 HTTP 服务的事情。例如，像新的 HTML5 Web Socket 之类的东西，你可以在后台构建服务，提供所有这些 Web Socket 的东西，也许还可以链接到 Redis 或 Mongo 之类的数据存储中，你可以构建服务器进程，你的 JavaScript 将在 Ruby 中调用它，并单独运行它，所以你实际上不是用它来提供你的应用程序，但你仍然可以用它来控制应用程序的某些部分。所以有很多这样的东西真的很有趣，而且它肯定已经成为一种很好的语言，可以把在后台运行的东西、服务、守护进程之类的东西放在一起。

路易:杰森，你有什么要补充的吗？

**Jason:** 是的，我想说我知道很多人使用 [Capistrano](https://github.com/capistrano/capistrano) 进行 Rails 之外的部署，Capistrano 是一个宝石，它有另一种预定义的方式来管理应用程序的部署，我知道很多人在 WordPress 上使用它，沿着同样的路线；你知道我们之前讨论过测试框架，我有朋友在 WordPress 上开发，并且使用[黄瓜](http://cukes.info/)来测试 WordPress 安装和类似的东西。

路易斯:好的，那你能给我一点黄瓜的背景知识吗？

Jason:Cucumber 是一个集成测试工具，你可以用这种纯文本的故事语言来编写你的测试，它背后的想法是，将会有更多的利益相关者，而不仅仅是一个应用程序的开发者，所以那些可能不是开发者的人实际上可以进去写一些故事，并且说嘿，这是一个网站行为的某个方面应该如何工作， 每个人都同意这一点，然后你实际上可以把人们写的这些纯文本故事，与你的应用程序，与你的实际应用程序进行对比，然后它就会通过或失败。

路易斯:好的，就像“我希望当我点击我的电子邮件时，能够访问我所有的电子邮件。”

**Jason:** 没错，这与你实际编写功能的方式非常相似，然后你会说“假设我在主页上，当我点击登录并输入正确的凭据时，我应该会看到我的电子邮件。”

Louis: 好的，然后你编写后台程序，把它变成一个可以直接运行的测试。

杰森:没错。

路易斯:好了，我想我们差不多可以结束了，伙计们，非常感谢你们今天来参加我们的节目并和我交谈。

杰森:谢谢你邀请我们。

**Louis:** 我们在开始的时候在 Skype 上遇到了一点问题，但过去的一段时间非常好，所以希望我们可以一起编辑它。你们只是想让我们知道听众可以在网上哪里找到你们，让我们从彼得开始吧？

**彼得:**好的，我会让杰森谈谈播客，但是我在 RubyInside.com 的[运营着可能是最受欢迎的 Ruby 博客，里面有一些文章和技巧之类的东西，但我也运营着【Rubyflow.com】的](http://www.rubyinside.com/)[的](http://www.rubyflow.com/)，这是一个社区网站，任何人都可以在那里张贴他们的 Ruby on Rails 相关新闻和链接，还有[的 Rubyweekly.com](http://rubyweekly.com/)，这是一个每周一期的时事通讯，里面有最新的文章等等。

路易:好吧，你很忙。所以我会在展示笔记里贴上所有这些东西的链接；你在推特上吗？

是的，我是推特上的[@彼得克](http://twitter.com/peterc)。

路易:好的。杰森呢。

杰森:我有没有提到过鲁比秀(笑声)。

路易斯:我认为你没有；我认为现在是最佳时机。

**杰森:**我们在 Rubyshow.com 做了一个 Ruby 新闻播客，你可以在推特上找到我 [@jseifer](http://twitter.com/jseifer) ，我的博客是【Jasonseifer.com】T4，我在[Twistedmind.com](http://twistedmind.com/)做 Ruby on Rails 咨询。

彼得:暗示，暗示(笑声)。

路易斯:好的，非常感谢各位。

彼得:很好。

杰森:谢谢你邀请我们。

路易斯:好的，这是我的荣幸。再见。

杰森:再见。

彼得:再见。

迈克·梅拉的主题音乐。

感谢收听！请随时让我们知道我们做得怎么样，或者使用下面的评论栏继续讨论。

## 分享这篇文章