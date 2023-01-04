# 介绍 XUL——网络最大的秘密:第一部分

> 原文：<https://www.sitepoint.com/xul-nets-biggest-secret-1/>

这里有一个问题:如果我告诉你，你可以使用 HTML 和 JavaScript 之类的东西来编写你自己的 Word 版本，会怎么样？如果我补充说你可以在你的硬盘上运行，或者直接从你的网络服务器上启动，然后用它来更新你的网站内容，那会怎么样？我知道这听起来有点牵强，但它就在此时此地——它自称“Zool”。

以下是这个由三部分组成的系列将涵盖的内容:

*   XUL 革命:谁是 Zool？
*   回到学校:是时候掸掉 JavaScript 的灰尘了…
*   动物学:阅读启动你的第一个 XUL 应用程序
*   XUL 的 3D 浏览:直入深水区。
*   拼命寻找:寻找结束了。
*   外卖菜单:请加薯条！
*   但是没人用 Mozilla:回到浏览器检测。
*   富客户端的崛起:未来是 XUL。

##### XUL 革命

在互联网最安静的角落之一，[mozilla.org](http://www.mozilla.org)，一场革命正在发生。一种新的 XML 格式，称为 [XUL](http://www.mozilla.org/projects/xul/) (可扩展用户界面语言)，发音为“Zool”，正在重塑我们对互联网和桌面应用程序的认识。或许这是一个大胆的主张——但是一旦你读完这篇文章，你可能会发现自己也同意。

Mozilla 在概念上的飞跃是超越 Web 浏览器，将其视为浏览网页的简单工具，而是将其视为一个*框架*——一个执行应用程序的运行时环境，就像您可能在 Java 和。NET 运行时环境。

我们都在看的时候。NET 和 Java 争夺开发者和公司的“思想份额”，Mozilla 在没有大肆宣传或关注的情况下悄悄地进行了他们的革命；例如，现在在谷歌上搜索“XUL”只能得到 103，000 个结果。

当然，开源项目很少有钱做广告，Mozilla(实际上是 Netscape)必须克服“输掉”浏览器大战的耻辱。但是，如果你看看今天的 XUL，你会惊讶地发现关于它的报道是如此之少。有些事情告诉我，真正的乐趣才刚刚开始…

XUL 及其姊妹技术 [XPCom](http://www.mozilla.org/projects/xpcom/) (跨平台组件对象模型)的迷人之处在于，它们具有。NET 和 Java:

*   用于构建应用程序的“小部件”库(如。NET WinForms 和 Java 的 Swing)
*   将表现逻辑与应用逻辑分离，表现逻辑由 JavaScript 处理
*   支持 XML 消息协议，如 SOAP 和 XML-RPC
*   支持多种语言来构建“代码隐藏”组件，包括 C++、 [Python](http://aspn.activestate.com/ASPN/Downloads/Komodo/PyXPCOM/) 和 [Ruby](http://rbxpcom.mozdev.org/) ，并且 [Perl](http://plxpcom.mozdev.org/) 正在进行中——尽管，遗憾的是，还没有 [PHP](http://www.php.net) (!).
*   真正跨平台；在任何可以运行 Mozilla 的地方，你都可以运行你的 XUL/XPCom 应用。

但是这并没有停止——在 XUL 写一个应用程序就像用 DHTML 写一个网页，除了你的 XUL 应用程序*将*工作，而你的 DHTML *可能* …。XUL 提供了一种标记，对于任何有 HTML 经验的人来说都很容易学会，并且具有基于文本的标记语言的所有优点，比如能够用 PHP“动态”生成它。

更好的是，XUL 允许使用现有的技术，如 CSS 来修改您的 XUL 应用程序的外观和感觉，以及 SVG 来添加一些视觉效果。你也可以混合 HTML 和 XUL——你可以把混合页面放在一起，例如，用一些 XUL 的小部件把一个枯燥的 HTML 页面变得生动起来。不过，不要指望在 w3.org 找到 XUL 上市——这完全是 Mozilla 的主意。

要运行 XUL 应用程序，你只需要安装 Mozilla，对吗？嗯…差不多了。有许多项目旨在将 XUL 引入其他运行时和环境，如 Mozilla 的 [Blackwood 项目](http://www.mozilla.org/projects/blackwood)、[卢克索·XUL](http://luxor-xul.sourceforge.net)、 [jXUL](http://jxul.sourceforge.net/) 和 [XULUX](http://www.xulux.org/) ，所有这些都以某种方式将 XUL 引入 Java。也有可能[嵌入壁虎](http://www.mozilla.org/projects/embedding/embedoverview/EmbeddingGecko.pdf)，这可以将 XUL 带到手机和 PDA 等设备上。

不是说你今天需要担心这些；运行 XUL 应用程序只需要 Mozilla(或基于 Mozilla 的浏览器，如 AOL 或 Netscape 的最新版本)。

“一切都很好”，你可能会说，“但这看起来就像是另一项实施不佳的技术，除了浪费我的时间，什么也做不了。”

这个怎么样:ActiveState 已经发布了 [Komodo](http://aspn.activestate.com/ASPN/Mozilla) ，这是一个使用 Mozilla 代码库编写的 IDE，利用了 XUL 的优势。你会发现在 mozdev.org 的[列出了许多较小的项目。有人想玩一局](http://www.mozdev.org/projects.html) [XULMine](http://games.mozdev.org/) 吗？你可以在场外直接运行它(当然，你需要安装 Mozilla)！注意它看起来像其他窗口应用程序吗？看看它的加载速度有多快…？

现在你已经对 XUL 有了一个大概的了解，是时候刷掉上次浏览器大战留下的那些布满灰尘的“最佳观看方式”的标志了，把它们重新放到你的网站上。是时候和 XUL 一起忙碌了…

##### 回到学校

可能使 XUL 成功的一个因素是，它利用了现有的技术，并且只有在别无选择的情况下才定义新的元素和功能。从我们这些刚刚开始接触这项技术的人的角度来看，这是一个好消息！我们所要做的就是掌握 XUL 标记和 XPCom 库。然而，为了构建成熟的应用程序，您对这些技术的了解可能会达到新的极限。

以下是一些你可能会在《XUL》中用到的配角:

1.  CSS
    定制 XUL 应用程序外观的大部分工作都可以由 CSS 来完成。如果你还没有开发出一个保持你的 CSS 有条理的策略，现在*是*的时候了。制定一个可靠的命名规则，记录你做了什么，在哪里做的，否则你会很快迷失方向，最终得到一个意想不到的彩色网站。SitePoint 为[提供了很多关于 CSS](https://www.sitepoint.com/subcat/90) 的有用信息。注意，你不能用 CSS 做的事情，你可以用叠加来做。

*   XML
    XUL 是一种 XML 格式，所以你肯定会喜欢使用它。如果你不确定，请阅读[XML 介绍](https://www.sitepoint.com/blog/)——它只是文本…*   **XML 名称空间**
    你需要*了解*XML 名称空间是如何工作的，尽管你不需要成为专家。尝试一下 [XML 名称空间解释](https://www.sitepoint.com/blog/)，作为复习。*   JavaScript
    通过 JavaScript 实现 XUL 应用。如果你还不熟悉，请确保你已经阅读了 SitePoint 的 [JavaScript 库](https://www.sitepoint.com/subcat/89)，尤其是凯文·扬克的 JavaScript 101 系列，瑞安·弗里斯伯格的 JavaScript 面向对象编程系列，以及[DOM 粗略指南](https://www.sitepoint.com/blog/)。您知道 JavaScript 支持继承和类似 Java 的异常处理吗？*   **XML [文档类型定义(DTD)](http://www.devshed.com/Server_Side/XML/DTDDesign)**
    虽然你不一定要使用 DTD，但事实证明，它们非常有用，可以为 XUL 元素分离关于你的应用程序的某些信息，这些信息显示在哪里。例如，出现在按钮上的文本可以由 DTD 文档实体定义。例如，当您想要“国际化”您的应用程序，并为不同的语言选择不同的 DTD 文件时，这可能很有用。从概念上讲，它类似于使用 CSS 文件来存储关于应用程序外观的信息，不同之处在于 DTD 允许您单独存储特定的内容。*   此外，接触一下 [Xlink](http://www.devshed.com/Server_Side/XML/XLinkBasics) 可能会派上用场，了解一下 [RDF](http://www.xml.com/pub/a/2001/01/24/rdf.html) 也可能是个好主意，因为它是 XUL 应用程序的一个可选数据源。

换句话说，有很多你必须知道的东西，但是如果你曾经玩过 DHTML 并且因为糟糕的浏览器实现而沮丧地放弃了，XUL 是你重新开始的机会。所以(还)不要惊慌——要使用 XUL 美化网页，你需要的只是一些 XUL 小部件和一点 JavaScript 的知识。

在这里，我会给你看一些简单的例子，不会对大脑造成负担。不要期望在本文结束时成为专家——有很多东西需要你自己去发现:例如，解释如何用 XUL 构建你自己的 Word 版本，这超出了本文的范围！

**警告:**现在还没有大量的资料可以帮助你开始学习 XUL。我发现的最好的资源是 O'Reilly 的[用 Mozilla](http://books.mozdev.org/index.html) 创建应用程序，可以用 HTML 格式在线阅读，而且(谢天谢地)非常棒。我已经建议在这个系列的最后进一步阅读。当然，这意味着你有很好的机会建立自己的 XUL 资源网站…

##### Zool 学

正如我在本文开头提到的，XUL 允许你构建桌面和基于浏览器的应用程序。在这里，我将把事情缩小到浏览器——你如何“简化”一个网页。要让这里提供的示例正常工作，您需要做两件事:

Mozilla 1.3 或 Netscape 7.0+——我在这些例子中使用了 Mozilla 的最新版本:Mozilla 1.3 Beta。虽然 XUL 和 XPCOM 现在相当稳定，但仍有工作在进行，所以请仔细检查您正在使用的版本。

[PHP](http://www.php.net) —我将使用 PHP 来“启动”XUL 的应用程序。如果你需要一个 Windows 版本，一个很好的起点是 [Firepages phpdev](http://www.firepages.com.au) ，它将在一个简单的安装中为你设置 Apache、PHP 和 MySQL。在您的 php.ini 文件中，确保您有“`short_open_tag=Off`”，这样 php 就不会将您的 XML 标记误认为 PHP 标记。告诉 Apache 用 PHP 处理 xul 文件也是一个好主意，这样您就可以将 PHP 代码放在. XUL 文件中。没有什么可以阻止你简单地使用。不过，PHP——只要您告诉 Mozilla 您正在使用的 MIME 类型(我很快就会谈到这一点)。

这里有一个. htaccess 文件，应该可以一笔解决您的问题。只需将它放入您想要运行 XUL 的 Web 目录中(假设您已经在 httpd.conf 中为 Apache 设置了 AllowOverride All):

```
php_flag short_open_tag Off  

<files *.xul>  

    ForceType application/x-httpd-php  

</files>
```

你想要手头上的另一件东西是 [XUL 星球的元素参考](http://www.xulplanet.com/references/elemref/)，这大概是现在获得关于 XUL 和 XPCom 的参考信息的最好地方。

##### 使用 XUL 进行 3D 浏览…

与其慢慢地构建你的第一个 XUL 应用程序，不如让我们直接从最深处开始吧！

```
<?php   

header ( 'Content-type: application/vnd.mozilla.xul+xml' );   

?>   

<?xml version="1.0"?>   

<!-- example1.xul -->   

<?xml-stylesheet href="example1.css" type="text/css"?>   

<window   

     title="XUL in Action"   

     xmlns:html="https://www.w3.org/1999/xhtml"   

     >   

 <tabbox orient="vertical" flex="1">   

   <tabs>   

     <tab label="PHP &amp; MySQL Interviews, News and Views" />   

     <tab label="PHP &amp; MySQL Tips and Tutorials" />   

     <tab label="PHP &amp; MySQL Apps and Reviews" />   

   </tabs>   

   <tabpanels flex="1">   

     <browser src="https://www.sitepoint.com/subcat/97"/>   

     <browser src="https://www.sitepoint.com/subcat/98"/>   

     <browser src="https://www.sitepoint.com/subcat/99"/>   

   </tabpanels>   

 </tabbox>   

</window>
```

这是做什么的？从代码归档中打开 example1.xul 来查看它的运行情况——请不要走开！

你还在这里？还没晕倒？尝试点击每个选项卡中的一些链接，看看会发生什么…

对于那些没有用 Mozilla 查看文件的人来说，你刚刚错过了一次超感官的网络体验。如果你的好奇心战胜了你，你知道该怎么做…

好的——这不是真正的 3D 浏览，而是三个“标签”,每个标签都充当一个独立的浏览器。关键是所有这一切都在你的控制之下:网站管理员——你可以给访问者一个专门为浏览你的网站而定制的布局。

让我们详细检查一下上面的例子。首先，我已经使用 PHP 的 [`header()`](http://www.php.net/header) 函数指示浏览器(Mozilla)这个文档的 MIME 类型是“`application/vnd.mozilla.xul+xml`”，这样浏览器就知道如何处理它了。这是 PHP(目前)所做的唯一工作，可以很容易地用 Perl、JSP 和 ASP(.网)。

接下来，通常声明任何 XML 文档都需要`<?xml version="1.0"?>`。前面一行只是一个注释，所以你知道这是哪个文件(不要担心——它们都在[这个可下载的 ZIP 文件](https://www.sitepoint.com/examples/xul/xul_code.zip)中提供给你了)。

下一行定义了这个 XUL 文档使用的 CSS 样式表。这与包含样式表的 HTML 格式略有不同

```
<link rel="stylesheet" type="text/css" href="example1.css" />
```

一会儿我会看看样式表。

按照样式表，您有了第一个真正的 XUL 元素(！):[窗口](http://www.xulplanet.com/references/elemref/ref_window.html)。除了 title 属性，在`<window />`中声明了两个名称空间，默认的是 XUL 名称空间，而另外一个 HTML 名称空间是用`xmlns:html`定义的。虽然我在这个例子中没有使用 HTML 名称空间，但是我将在所有例子中声明它“以防万一”。窗口元素是 XUL 文档中五个根元素之一，可能是您使用最多的一个。其他的还有[叠加](http://www.xulplanet.com/references/elemref/ref_overlay.html)、[对话框](http://www.xulplanet.com/references/elemref/ref_dialog.html)、[页面](http://www.xulplanet.com/references/elemref/ref_page.html)和[向导](http://www.xulplanet.com/references/elemref/ref_wizard.html)。

接下来是 [tabbox](http://www.xulplanet.com/references/elemref/ref_tabox.html) 元素。这是一个“标签”元素的“容器”，有点像一个文件柜，你可以在其中放置标签式悬挂文件夹。属性“`orient`”指定了选项卡框的方向，而`flex`元素用于使选项卡框填满窗口中的可用空间。

Flex 由 [XUL 元素](http://www.xulplanet.com/references/elemref/ref_XULElement.html)定义，它是许多其他 XUL 元素的“父”元素。如果你遇到过面向对象编程或 DOM，XUL 有一个类似的层次结构，它将所有元素定义为父节点的后代，“祖父”是节点的[。](http://www.xulplanet.com/references/elemref/ref_Node.html)

接下来是[选项卡](http://www.xulplanet.com/references/elemref/ref_tabs.html)，它代表一个用于定义[选项卡](http://www.xulplanet.com/references/elemref/ref_tab.html)元素的容器。每个选项卡都有一个“`label`”属性，它定义了出现在选项卡上的文本。

在选项卡元素下面是[选项卡框](http://www.xulplanet.com/references/elemref/ref_tabbox.html)，在这里我可以定义对应于选项卡的元素。我使用了[浏览器元素](http://www.xulplanet.com/references/elemref/ref_browser.html)，它类似于 HTML 中的 iframe 元素，来指定要显示的三个网页。让事情变得有点混乱的是，XUL 也有自己的 iframe 元素，它的使用方式和你在 HTML 中使用 iframe 元素的方式差不多。

example1.css 文档如下所示。

```
window   

{   

  background-color: navy;   

}   

tab   

{   

  font-family: verdana;   

  font-size: 13px;   

  font-weight: bold;   

  color: navy;   

}
```

太简单了……！

## 分享这篇文章