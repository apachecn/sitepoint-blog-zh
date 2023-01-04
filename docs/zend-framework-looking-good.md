# Zend 框架:看起来不错

> 原文：<https://www.sitepoint.com/zend-framework-looking-good/>

我想和很多人一样，被[新框架](http://framework.zend.com)的[第一版](https://www.sitepoint.com/zend-framework-is-out/)吸引住了。

最初一直在记录印象，但觉得此时没有太多意义——第一版是“预览”质量，粗糙的边缘是已知的。现在，它只适合有经验的 PHP 开发人员，他们想知道接下来会发生什么——等待，避免沮丧。

有人可能会说它发布得太早了——它没有通过马库斯的 [install me](http://www.lastcraft.com/blog/index.php?p=11) 测试，至少就控制器而言是这样，我想这是大多数人想看到的。与 Ruby 不同，PHP 是一种很多人都很熟悉的语言，并且更能形成有效的观点，因此受众可能会更加挑剔，早期发布会有负面评论的风险。

也就是说，我个人很高兴 Zend 有所收获。嗅了一会儿之后，我也持谨慎乐观的态度。

**建立共识**

我从预览版中得到的最积极的印象是，Zend 为如何在 PHP 中“做”库和可重用代码制定了各种约定。

你可以通过阅读 [Rob Allen 的](http://www.akrabat.com/)博客中的[前端控制器](http://www.akrabat.com/2006/03/04/zend-framework-front-controller/)和[视图](http://www.akrabat.com/2006/03/05/zend-framework-views-and-the-front-controller/) (Rob [参加了 PHP 伦敦会议](http://www.akrabat.com/2006/02/12/php-uk-conference-2006-in-detail/)BTW)——注意他是如何在文件系统上组织自己的名称空间并实现一些 Zend 接口的；

```
/lib/Akrabat
/lib/Akrabat/Action.php
/lib/Akrabat/Router.php

```

如果您的包含路径中有上面的`lib`目录，那么您可以通过 [Zend::loadClass()](http://framework.zend.com/manual/zend.loadclass.html) 方法加载这些类。

这只是建议性的，但是因为 Zend 有推动这个球运动的力量，可以看到他们正在就如何部署库和框架扩展达成协议和约定。如果 Zend 框架开始出现在 [linux 发行版](http://www.sebastian-bergmann.de/blog/archives/581-Zend-Framework-in-Gentoo-Linux.html)中，并且由托管公司默认安装，那么就有强烈的动机以同样的方式做事情。

这本身对 PHP 来说是一个巨大的优势，鼓励组织并为编写开源 PHP 库的人提供关注点。今天，使用第三方代码将一个项目组合在一起意味着要么使用大量胶水，要么破解源代码——以 Zend framewark 为中心点，我们终于开始从混乱中获得一些组织。除此之外，框架本身消除了重新发明某些轮子的需要，比如由[输入过滤器](http://framework.zend.com/manual/zend.inputfilter.html)提供的轮子，以及提供扩展点，比如[定制视图助手](http://framework.zend.com/manual/zend.view.helpers.html#zend.view.helpers.custom)或它们已经包含的一些接口(DI 是必需的——更多内容见下文)。

**选择仍然是一种选择**

如果你准备在中心接受 Zend 框架(除了操作码缓存可能是个好主意之外，我不认为这真的有什么坏处)，有足够的空间将你的东西作为附加组件分发到它上面。

例如，框架没有实现模板引擎，你的[视图脚本](http://framework.zend.com/manual/zend.view.html#zend.view.introduction.view)是直接的 PHP(我猜这是基于[保罗的](http://paul-m-jones.com/blog/)与[学者](http://www.phpsavant.com/yawiki/)的经历)。没有什么可以阻止 [Smarty](http://smarty.php.net) 或 [WACT](http://www.phpwact.org) 以一种与 Zend Framework“随时可用”的形式发布他们的模板引擎，事实上*有很好的理由*这样做——如果 Zend Framework 得到广泛接受，是“Zend Framework 兼容的”(有人知道吗？)会增加合法性。

类似的情况也发生在 [eZ 组件](http://ez.no/download/ez_components)上——这些组件可以很容易地适应 Zend 框架，提供一堆框架不做的东西和[选择](http://ez.no/doc/components/view/(file)/1.0/classtrees_Mail.html)它做[做的一些事情](http://framework.zend.com/manual/zend.mail.html)。

此外，看看控制器，我得到的印象是*默认*设计将以某种类似 Rails 的方式工作——用户定义的控制器类，其类方法对应于特定的动作/URL。就我个人而言，我并不特别迷恋这种方法，我更希望看到类似于 [web.py](http://webpy.org/) 的东西；

```
 class WikiPage extends Resource {
    public function GET() {
        // response to HTTP GET
    }

    public function POST() {
        // response to HTTP POST
    }
} 

```

到目前为止，情况似乎是，我将能够拥有我的蛋糕并吃掉它——应该有可能在当前的控制器设计中构建这种 API，对现有的 API 进行一点扩展和实现。

**黑仔专题**

现在我认为它提供的最强的特性是搜索工具 T1(虽然还不完整/稳定)，它是 PHP 的 Apache Lucene (Java)项目 T3 的移植。目前基本上还没有一个 PHP 自带的像样的搜索工具包(除了[对像](http://blogs.phparch.com/mt/?p=60) [Xapian](http://www.xapian.org/) 这样的项目构建扩展)。已经能想到一个[一个项目](http://wiki.splitbrain.org/)那只好[滚它自己的](http://wiki.splitbrain.org/wiki:search#some_background_on_the_searchindex)。

同样，按照 API 的设计方式，使用 Zend Framework 作为核心的项目有可能利用搜索索引功能，从而允许用户在他们网站上使用的各种应用程序之间进行“统一”搜索。

否则，尽管不完全是一个特性，但看着代码以一种[一致的方式](http://framework.zend.com/manual/coding-standard.html)清晰地写出来，并且[有很好的文档记录](http://framework.zend.com/manual/index.html)，真的很好。

**关注点**

现在我可以提出一些小问题(例如，Feed API 非常酷，但是字符编码呢？)但鉴于预览状态，此时不会。

不过，我认为有两件事被忽略了。

首先是单元测试，这不是下载的一部分。考虑到发生的一两个[发布停顿](http://andigutmans.blogspot.com/2006/03/quick-zend-framework-update.html)，我暗自怀疑这可能是因为还没有任何单元测试(如果我错了就打我一巴掌)。我知道让这个框架“出来”有压力，但是不要牺牲单元测试(单元测试需要在[这个列表](http://framework.zend.com/manual/coding-standard.html))上！很快会有一个[漏洞跟踪器](http://framework.zend.com/bugs)上线。一个额外的(我认为更有价值的)反馈来源是测试结果——如果用户运行测试并交付，比如说，通过电子邮件和一些关于他们环境的信息(PHP 版本，操作系统等)。)，您可以很好地了解这个框架有多稳固——如果您愿意，可以称之为一个巨大的构建场。否则，单元测试对于与用户(开发人员)建立信任是必不可少的。

另一个，更不为人知的，是[依赖注入](http://www.martinfowler.com/articles/injection.html)，在 [Pawel 的演讲](http://www.phpconference.co.uk/2006/talks/pico/dependency_injection_-_pawel_kozlowski.html)之后，我被说服了。我相信[贡献者](http://framework.zend.com/contributors)的[塞巴斯蒂安](http://www.sebastian-bergmann.de)在这方面有些经验。Pawel 用 [picocontainer](http://www.picocontainer.org/) 的 [PHP 版本](http://cvs.codehaus.org/viewrep/picocontainer/php/picocontainer/trunk/container/src/)说服我的是，它可以用 PHP 的“轻量级”方式完成，不会消耗资源。对于 Zend 框架来说，如果它要成为其他项目的中心“集成”点，这似乎是至关重要的——依赖注入不是专门加载和实例化对象，而是允许组件通过框架获得“服务”,让用户决定他们想要使用哪个实现。

**底线**

Zend 框架是 PHP 长期以来严重缺乏的东西。它还没有准备好投入使用，但是它当前的形式应该让库开发人员了解如何使他们的代码适合它。从我的角度来看，这绝对不是一个威胁，这是我们将众多 [成千上万](http://freshmeat.net/browse/183/)[小时](http://sourceforge.net/softwaremap/trove_list.php?form_cat=183) [工作](http://www.phpclasses.org/)结合成一个连贯平台的最佳机会。要么接受，要么放弃。

## 分享这篇文章