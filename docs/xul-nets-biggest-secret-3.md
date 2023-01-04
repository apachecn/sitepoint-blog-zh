# 介绍 XUL——网络的最大秘密:第三部分

> 原文：<https://www.sitepoint.com/xul-nets-biggest-secret-3/>

在过去的两周里，我们去了趟 XUL。我们已经介绍了[浏览](https://www.sitepoint.com/blog/)、[搜索和导航](https://www.sitepoint.com/blog/)。但是使用 UXL 在现实世界中意味着什么呢？现在让我们考虑这些。

##### 但是没人用 Mozilla…

根据目前的估计，Mozilla 1.0+ / Netscape 6.0+是 2%到 20%的互联网用户的首选浏览器。当 AOL 最终决定向其客户提供某个版本的 Netscape 时，这个数字可能会大幅上升。但你是对的。大多数网络用户使用 Internet Explorer，这种情况在近期内不可能改变。

问题是:作为一个领先的网站建设者，你想给你的访问者提供最好的浏览体验吗？你想给潜在客户留下深刻印象，并向他们展示构建可在线部署的“富”客户端的可能性吗？学习 XUL 并展示你的才华，可以在预算紧张和竞争激烈的今天带来至关重要的领先优势。

为了帮助你，这里有一些来自 PHP 和 [phpSniff](http://phpsniff.sourceforge.net) 的实用帮助，这是一个优秀的 PHP 项目，可以帮助你决定访问者使用哪种浏览器。

```
<?php 

// Browser detection script using PHP Sniff 

// http://phpsniff.sourceforge.net 

// Set this to the URL for your site 

$url='https://www.sitepoint.com/'; // Trailing slash important! 

// Include phpsniff code 

require_once ('phpsniff-2.1.2/phpSniff.core.php'); 

require_once ('phpsniff-2.1.2/phpSniff.class.php'); 

// Instantiate phpSniff 

$phpSniff=new phpSniff(); 

// Get the browser type and version 

$browserCode=$phpSniff->property('browser'); 

$browserVer=$phpSniff->property('maj_ver').$phpSniff->property('min_ver'); 

// Lookout for Mozilla 1.0+ and Netscape 6.0+ 

if ( ( $browserCode == 'mz' && $browserVer >= 1.3 ) 

        || ( $browserCode == 'ns' && $browserVer >= 7 ) ) { 

    // Redirect them to the xul index for the site 

    header ( 'Location: '.$url.'index.xul' ); 

} else { 

    // Send all other browsers to the normal index page 

    header ( 'Location: '.$url.'index.html' );     

} 

?>
```

我用过 phpSniff 2 . 1 . 2 版。上面的代码是针对 Mozilla 1.3+和 Netscape 7.0+的。如果任何人进入您的站点使用这些浏览器，脚本会将他们重定向到 index.xul，这是您的站点的“xul 增强”版本。

您可以将这段代码放在主页的顶部(例如 index.php)。请注意，如果您计划将来自 index.xul 的 index.php*与 xul“浏览器”一起使用，您将需要做一些进一步的修改，否则您将通过无限循环重定向 Mozilla 查看器。别忘了包括这一点。htaccess 文件，这样您就可以在 index.xul 中生成正确的 MIME 头。*

##### 富客户端的崛起

HTML、表单和 JavaScript 非常适合你的典型网站。但是总有一些事情，不管你怎么努力，你就是做不到，比如构建一个能够“实时”更新的页面(这里我不是说元刷新)，或者尝试在线模拟一个桌面应用程序。

近年来，已经有许多方法将“富客户端”引入互联网。

首先，我们有 Java 小程序，它受益于优秀的 Java 类库。不幸的是，Java 的客户机构建库 Swing 是出了名的慢，甚至给 Java 本身带来了慢的名声(事实并非如此)。此外，学习 Java 肯定不是一个周末的事情…

接下来是 Flash。自从 MX 发布以来，Flash 作为一种构建富 Web 客户端的好技术受到了一些关注，这是理所当然的。Macromedia 正在推广 Flash 作为构建 J2EE 和微软前端的工具。NET 应用程序…但并不是一切都很好。Flash 是以二进制格式交付的，这意味着如果您构建了一个大型应用程序，您的用户可能要等待很长时间。此外，使用 PHP、Perl、ASP 等服务器端技术很难呈现复杂的 Flash 应用程序。NET)和 Java。像 [Ming](http://ming.sourceforge.net/) 这样的项目已经取得了惊人的进展，但是用 PHP 构建 Flash 表单仍然比构建 HTML 表单困难得多。

与 Flash 类似，还有 SVG，这是一种 W3 XML 标准，Adobe 和 Apache Group 为此做了大量工作。SVG 的伟大之处在于它是纯 XML 的；这意味着向浏览器发送 SVG 文档和发送普通网页一样快。尽管在 SVG 中实现表单这样的东西是可能的，但最终最大的线索还是在 SVG 代表什么:可伸缩向量*图形*。作为一种标记语言，它不便于构建我们期望的用户界面组件，如按钮和菜单。

XUL 是一个完全不同的故事。这是一种专门为构建图形用户界面而设计的标记。在本文中，我只看了一些简单的例子，说明如何使用 XUL 完成 Web 构建者一直在努力使用 DHTML 完成的任务。你在这里看到的只是对页面的增强(最终是(X)HTML)。

但是 XUL 并没有就此止步——事实上，远非如此。XUL 是一种技术，它允许任何能够使用 HTML 的人构建完整的桌面和/或基于 Web 的应用程序。基本上，你可以用 Mozilla 浏览器做的任何事情(例如，打开文件、编辑文档、查看图像、发送电子邮件等。)可以在 XUL 应用程序中重用。

即使在 PHP 等技术驱动的动态网站时代，我们仍在努力构建真正易于使用的在线应用程序。向网站添加内容应该像编辑 Word 文档一样简单——不需要 HTML。XUL 是能让这一切发生的技术。

“魔法”在 XPCom 库中，除了别的以外，它还提供了对 SOAP 和 XML-RPC 这两种 Web 服务协议的支持(更多信息，请参见 Kevin Yank 的 [Web 服务揭秘](https://www.sitepoint.com/blog/))。

“启动”一个应用程序，作为 HTML 的所见即所得编辑器，并能够更新网站现在已经成为现实。该应用程序的 XUL 可以由 PHP“动态”呈现，然后由 Mozilla 在几秒钟内加载。一旦它开始运行，用户就可以编辑文本，而不需要了解 HTML，然后更新他们的网站，于是 XUL 应用程序将内容传递给另一个 PHP 脚本，该脚本充当 SOAP 服务器。Mozblog 是一个桌面 XUL 应用程序，演示了这个原理，允许你通过 XML-RPC 更新博客。在 O'Reilly 上，你会发现一篇关于使用 Mozilla SOAP API 的优秀文章，它描述了所有的技术细节。要知道 XPCom 中的 WSDL 支持仍然是一项正在进行的工作，所以构建 XUL SOAP 客户端是一项体力劳动。

这一切有什么蹊跷？坦白说(在追求 XUL 的过程中掉了更多的头发！)，我可以说 Mozilla(或任何人)真的需要清醒过来，开始思考事情从外部看起来是怎样的*。*

出于安全原因，使用直接从网站加载的 JavaScript 中的 XPCom SOAP API 需要访问者在浏览器中运行之前给予他们明确的许可。这是一件好事，但它要求开发人员对 JavaScript 进行数字签名，这样任何加载应用程序的人都可以在执行之前检查您的凭证。很公平…

不幸的是，官方对你必须如何签署你的 JavaScript 的解释读起来就像啤酒垫涂鸦，分散在多个网站上，其中一些看起来好像是在半小时内完成的。不要让我开始阅读他们推荐的签名工具中的自述文件，它会指引你到[这个最新的网址](http://developer.iPlanet.com/docs/manuals/signedobj/signtool/index.htm)。

也许这些年来，我被 PHP 及其优秀的文档和令人惊叹的社区宠坏了，但是为什么要花费这么多精力来构建这样一个伟大的技术，旨在使开发人员的生活变得容易，然后在最后的障碍中倒下呢？与微软在推广 ASP.NET 并使其成为真正吸引用户的技术方面所做的出色工作相比，也许你可以原谅我的抱怨。这种事情让我想起了 IBM 试图销售桌面操作系统的日子…

撇开抱怨不谈，XUL 确实是一项伟大的技术。现在它已经得到了官方的“它能工作”的批准，但是油漆还没有干。XUL 已经达到了一个绝对值得努力学习的水平，但要做好准备:要建立学习 XUL 和发展它的资源，还有很多事情要做。现在#1 开发工具是不起眼的文本编辑器，尽管[卢克索 SWT 原型](http://sourceforge.net/project/showfiles.php?group_id=28946)可能很快将 XUL 带到[日蚀](http://www.eclipse.org)，并导致作为 XUL 运行时的 Mozilla 的另一个替代品。

引用 Mozilla 团队自己的话:

<q>*“一开始有 3 个前端(FE):MAC、windows、unix。每个都需要一组开发人员来维护。添加一个新功能(甚至只是一个按钮)需要 3 名工程师浪费至少一天(更常见的是一周)的时间，直到该功能完成。这必须改变。*</q>

如果我们生活在一个只需编写一次代码的世界里会怎么样？非程序员可以使用 W3C 标准而不是 C++来设计产品的外观和感觉？平台差异可以在样式表中表达，而不是用#ifdefs 硬编码。

你喜欢那个世界吗？我们也是。"

##### 资源

[Mozilla 的 XUL 项目页面](http://www.mozilla.org/projects/xul/)

用 Mozilla 创建应用程序——这是 O'Reilly 的一本书的免费在线版本，涵盖了 XUL 和 XPCom。

网景公司的 XUL 开发者中心。

XUL 星球 —不断增长的教程和资源集合。入门教程应该有助于掌握所有重要的 XUL 元素。

XP 工具包—XUL 的更多例子。

[Mozilla 的 XPCOM 项目页面](http://www.mozilla.org/projects/xpcom)

MozDev.org——Mozilla 自己的“Sourceforge ”,用于构建 XUL 应用。这里有很多有用的例子，甚至可以开始你自己的项目。

关于为 XUL 应用程序实现“代码隐藏”逻辑的 IBM Python 和 XPCom 教程系列的第 1 部分、[第 2 部分](http://www-106.ibm.com/developerworks/webservices/library/co-pyxp2.html)和[第 3 部分](http://www-106.ibm.com/developerworks/webservices/library/co-pyxp3/)。引用:“实现如此简单，令人害怕…”

XPCom 简介 —这是一个关于 XPCOM 库的技术性文章系列。

如果你使用 Linux，在 XUL 和 Glade T1 之间有一座“桥”正在搭建，叫做 T2 MozGlade T3，这将使构建 XUL 应用程序更加容易。

## 分享这篇文章