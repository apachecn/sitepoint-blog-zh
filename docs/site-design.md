# 网站设计

> 原文：<https://www.sitepoint.com/site-design/>

问:我在为我的房地产经纪人侄子建一个网站。它看起来很棒，但他在使用 AOL 时看不到它(或我的任何其他网页设计)。他输入了 URL，但是 AOL 浏览器显然将他重定向到了一个完全不同的 URL，这个 URL 没有以任何方式连接。我听说在美国在线网上冲浪有问题。作为一个构建者，我应该在我的 HTML 中加入什么不同的或者额外的东西来使它可以被 AOL 访问吗？—戴夫·科斯

美国在线已经建立了一个专门的网站，提供有关美国在线设计的具体信息。可以在 http://webmaster.info.aol.com 的[找到。](http://webmaster.info.aol.com/)

我正在使用 MS Front Page 重新设计我的网站，我有一个非常基本的问题。这个程序给了我 200 多种不同字体的选择，我计划在一些页面上使用本瓜伊特·弗里斯基·ATT 字体。我的问题是:所有的字体能被所有的浏览器浏览吗？该字体是否需要出现在网页浏览者的计算机上才能让他们像我一样看到网页？我知道大部分电脑都有 Arial，Times New Roman，Courier 等。我仅限于这些字体吗？—迪克·查普曼

不，你不仅仅局限于这些字体。浏览器将尝试使用所选字体显示您的页面，如果系统中没有该字体，页面将使用默认字体(Times New Roman)显示。作为设计师，你可以为每个项目选择多种字体。浏览器将简单地在列表中搜索访问者系统中的字体。如果找不到，它会尝试使用列表中的下一种字体。在 Frontpage 中，按 ALT+Enter，而不是从列表中选择字体，输入它的名称，然后添加一个逗号和下一行的字体名称。例如:“本瓜伊特弗里斯基 ATT，宋体，宋体”将导致浏览器首先寻找本瓜伊特弗里斯基 ATT，如果没有找到，那么它将尝试宋体，然后宋体，否则它将选择时代新罗马。

**问**是否有一个可用的 javascript 购物车，它可以被加密以保证安全吗？安全服务器和 perl 脚本购物车的成本超出了大多数小型企业的承受能力。我发现了两个半功能的 javascript 推车，但是还不能调试它们。请帮帮忙。—泰迪·哈特曼

不，JavaScript 完全是客户端的。如果程序需要服务器端功能(即购物车)您将不得不依靠 PERL 或其他语言来获得最大的安全性和功能性。还要记住，10%以上的互联网冲浪者禁用了 JavaScript。外面有几十种免费的 Perl 手推车，甚至像 [QUIKSTORE](http://www.quikstore.com/) 这样最好的手推车也只卖 175 美元。看看下面这些免费的 PERL 购物车:[网上商店](http://www.extropia.com/scripts/web_store.html)，[迷你商店](http://www.minivend.com/minivend/)， [S-Mart](http://www.stiusa.com/~brobison/scripts/)

**Q.** 在哪里可以免费获得质量好的照片(大部分)和剪贴画？或者至少在价格不太贵的地方。—本

免费拍照，试试[的留声机](http://www.megopolis.com/photos)。我最喜欢的剪贴画资源之一是 FreeGraphics.com 的 T4。

**问**我是一名有抱负的网站管理员，我很难找到关于最佳布局的信息，以便我的页面在许多不同的平台、浏览器、显示器、分辨率等上保持一致。-罗米·布里斯通

这里有一些技巧，可以让你的网站在尽可能多的不同平台/浏览器上保持一致:

1.)如果你使用的是固定宽度的表格，也就是说你为表格指定了特定的像素宽度，而不是百分比。将网站上所有页面的总宽度保持在 600 像素，这可以确保使用 640 x 480 像素显示器的访问者在浏览网站时不会水平滚动。

2.)将 216 色“网络安全”调色板用于图形。阅读[什么是浏览器安全调色板？](http://www.415wd.com/colorsafe/)文章了解更多信息。

3.)在 NS 3、NS 4、IE3 和 IE4 或 5 中测试并重新测试你的站点。如果可能的话，在几个不同的显示器上浏览你的网站，并使用浏览器在不同的分辨率下测试你的网站。

亲爱的博士，

我目前正在为我的客户开发一个网站。我遇到了客户提出的挑战。他们希望自己网站的主页能够自动将 web 用户浏览器的默认 URL 设置为该网站的 URL。有办法做到这一点吗？还是只能手动设置？雷内·罗

**A.** 使用 IE5 的访问者可以通过点击链接来自动设置默认页面。代码提供在文章[创新营销理念](http://www.webmasterbase.com/article.php3?aid=22&pid=0)中。你必须在你的网站上为使用网景的访问者提供指导，没有自动的方法可以做到这一点。

**问。**我想在我的网站上集成一个可搜索的数据库。我该怎么做？我知道这并不简单，但任何朝着正确方向的努力都会有所帮助。理查德·麦康贝

这真的取决于你想付多少钱，你需要多少电力，以及你希望它有多人性化。您可以用几个用 PERL 编写的 CGI 脚本来完成，但是最好的解决方案是在专用服务器上使用 MySQL 和持久 CGI。您也可以使用 Cold Fusion 或 ASP(动态服务器页面),这两者都要求您在安装了正确的服务器扩展的 NT 服务器上进行托管。如果你决定使用冷融合或者 ASP，像 [Virtualscape](http://www.virtualscape.com/) 这样的主机是你需要的。

问:我见过许多网站在顶部有链接到其他页面的标签(比如文件夹)(比如 mothernature.com 的)。我想为自己的网站创建这个，但找不到任何东西教我如何做。有什么程序可以帮我做吗？—塔米·戈尔德

**A.** 这些标签只是链接到其他页面的图形。看看拥有它们的页面的源代码(Netscape 4 中的 View - >页面源代码，IE 4 中的 View - >源代码)。任何文本编辑器都会帮助你做到这一点，你所需要的是每个网站管理员在开始网站设计项目之前都应该具备的 HTML 基础知识。

**问**我经营着一个棒球网站(【www.astrosconnection.com】T2)，看到这些难以置信的清晰、闪亮、完美的图形……我不常看到这种质量的图形，但我非常想知道如何制作它们……怎么可能有人能制作出如此清晰的图形呢？——克里斯·鲍耶

图形的质量在很大程度上取决于你的艺术能力、图像的压缩程度以及用来制作图像的程序。前往该网站的下载部分，看看可用的共享图形程序，并阅读 Fireworks 和 Adobe ImageReady 的[评论](http://www.webmasterbase.com/subcats.php3?id=5)。如果你像我一样面临艺术挑战，那就下载一份 [Xara Websyle](http://www.club.xara.com/webstylereferrer.asp?ID=58745) 吧，你马上就能做出令人瞠目的图形了。

我一直在用新的 Shockwave Flash 技术创作 mconet.com。我想知道你对这个问题有什么看法或答案。我是否应该花时间为我的网站创建一个新的多媒体语言的网站，让一些浏览者看不到它，但是为那些看不到它的人提供第二个旧版本？或者，我应该只有 html/旧版本吗？—杰森·范德布姆

这个问题的答案取决于你愿意在你的网站上花多少时间，以及网站更新的频率。如果你打算每天更新网站，维护两个副本可能会成为一个非常耗时的过程。另一方面，如果你一周更新一次网站，只改变一两个页面，那么维护两个副本是完全可行的。

你还必须考虑你的目标受众。如果你知道 90%的用户安装了插件，那么维护一个只有 shockwave flash 的网站是相对安全的。然而，如果你不这样做，那么你要么维护网站的两个副本，要么只有一个标准的 HTML 版本。

亲爱的博士:

我只做了几个月的设计，不知道如何在网页的左侧或右侧放置链接，也许是用一个沿着页面长度的彩色条。我知道这是一个愚蠢的问题，但毕竟我不能找到一个真正好的资源来教我如何做到这一点。救命啊！—希望保持匿名

你提到的导航条是用表格创建的，为了创建有吸引力的、有用的网站，每个网站管理员都应该对表格了如指掌。你会在 www.htmlgoodies.com 的[上找到表格教程，我也建议你给自己买一本《傻瓜 HTML》或者另外一本《初学者 HTML》。有了正确的技术，您会惊奇地发现有什么是可能的。](http://www.htmlgoodies.com/)

**Q.** 你如何保证浏览器之间的一致性？我说的不仅仅是旧版本和新版本的浏览器。我的网站在个人电脑上的 Netscape、Internet Explorer 和 AOL 都能很好地工作。它在 Mac 上的 Netscape 和 Internet Explorer 中也能很好地工作。但是，在 Mac 上，AOL 浏览器会将所有带有信息和/或图形的居中表格向上移动到左边。对此有解决方法吗？—k·舍恩伯格

确保你的网站能在所有浏览器中正常浏览的唯一方法是测试和再测试。我的建议是，你可以在 http://www.browsercaps.com/[T3 和 http://www.anybrowser.org/campaign/](http://www.browsercaps.com/)[T5 查看不同浏览器的具体信息。](http://www.anybrowser.org/campaign/)

## 分享这篇文章