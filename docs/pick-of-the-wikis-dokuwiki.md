# 维基精选:Dokuwiki

> 原文：<https://www.sitepoint.com/pick-of-the-wikis-dokuwiki/>

在过去的几个月里，我慢慢地评估了一些 [wiki 的](http://c2.com/cgi/wiki?WikiEngines),并打算(敢于)推荐一两个 PHP wiki。

货比三家的主要原因是现在的 [WACT 维基](http://wact.sourceforge.net/index.php/WikiWikiWeb)，用的是 [PHPWiki](http://phpwiki.sourceforge.net/) 。

虽然 PHPWiki 没有什么本质上的错误，但是您拥有的文档和文档版本越多，它似乎就越慢。很多次我试图研究*为什么*变得缓慢，我发现代码缺乏[透明性](http://www.catb.org/~esr/writings/taoup/html/ch01s06.html#id2878054)——可能只是我，但真的没有兴趣花很多时间去了解代码库。所以现在 PHPWiki 相当于一只“野兽”,我不想和它乱搞。

另外，正如[杰夫提到的](http://wact.sourceforge.net/index.php/WactDocumentationPhilosophy)；

> wiki 标记风格看起来几乎是不确定的。当我编辑页面时，我永远不知道它会变成什么样子。

另一个问题(我们收到的投诉)是缺乏某种索引/网站地图。WIKI 中有很多页面在任何地方都不会被引用，让它们被发现的唯一方法是手动创建链接页面(正如我们在这里所做的)。

否则，由于 PHPWiki 代码库是一个我认为我们任何人都不想接触的“东西”，我们不能真正在其他地方利用它，例如真正将生成的 [API 文档](http://wact.sourceforge.net/api/)与 Wiki 集成(这里有一个单独的博客，关于将生成的 API 文档与动态内容集成，供用户评论等。或者从它生成某种可下载的“手册”。

最后，我对存储在数据库中的内容感到不舒服——除了网络界面之外，很难通过其他方式修改维基。

总之，我看过一两个其他的 WIKI(这些都是基于 PHP 的，但是其他的像 [TinyWiki](http://c2.com/cgi/wiki?TinyWiki) 非常有吸引力)；

–[MediaWiki](http://wikipedia.sourceforge.net/)–现在 [Wikipedia](http://en.wikipedia.org/wiki/Main_Page) 由 MediaWiki 支持(正如他们在这里提到的–PHP 和扩展的终极案例？)这显然使其成为非常强的候选人。它有一些很棒的特性，比如 [docbook export](http://meta.wikimedia.org/wiki/DocBook_XML_export) ，代码库通常是一个令人愉快的探索。但我不喜欢的是，它再次使用数据库(尽管它提供了优秀的支持管理工具),并且如果我们使用它，它会成为“宇宙的中心”,使得与 API 文档的集成变得棘手。

–[ya wiki](http://wiki.ciaweb.net/yawiki/)，这是来自[保罗·琼斯](http://pear.php.net/user/pmjones)的作品，你可能从[学者](http://phpsavant.com/yawiki/)那里知道。总的来说，我喜欢它的发展方向，尤其是干净的代码库。不过它再次使用了 DB，并且确实在寻找更高级的东西(上次我看到的是 0.17)。不过在从 PHPWiki 迁移时，我们可以利用 [Text_Wiki](http://pear.php.net/package/Text_Wiki) 。

–[pm Wiki](http://www.pmwiki.org/)是一个基于文件的 wiki，几乎是我所寻找的内容的绝对“赢家”。从开发人员似乎在所有情况下都使用了可能的最简单的实现的意义上来说，我对开发人员探索代码的理智性有一个很好的感觉，我怀疑就它所管理的信息量而言，它会扩展得很好。不太好的是遍布代码的关键字“global”。此外，我对存储内容的格式也有疑问；它是一种 ini 文件，除了用于 wiki 标记的解析器之外，还需要一个专门的解析器。此外，更改(差异)与内容本身一起存储(在单个文件中)，这可能会导致一些相当大的文件，因为一个页面经历了多次编辑(请注意，它确实将页面的最新版本存储为单个实体，这很好，它不是从差异中重建页面)。否则，该标记类似于 PHPWiki 的标记，很可能再次导致决定论的问题。

–[DokuWiki](http://www.splitbrain.org/dokuwiki/)–越看越喜欢(这里的代号是)。我认为作者安德烈亚斯·高尔已经设法完全正确地掌握了基本原理…

+它是基于文件的，get 存储的内容正是您键入的内容。如果您需要使用标准的文件系统工具进行编辑，这将非常有帮助。例如，默认情况下，DokuWiki 使用 Unix find(1)和 grep(1)进行搜索。

+它在创建页面时使用[名称空间](http://www.splitbrain.org/dokuwiki/wiki:namespaces)。名称空间与 URL 相关。每个名称空间都是一个目录，所以如果我的页面 ID 是 wact:tags:list，它将对应于一个文件/目录结构，如。/wact/tags/list . txt——这使得为维基建立有用的[索引](http://www.splitbrain.org/dokuwiki/start?do=index)成为可能。

wiki 语法看起来很确定，而且 Dokuwiki 附带了一个[编辑工具栏](http://www.splitbrain.org/dokuwiki/wiki:quickbuttons)来帮助标记，并且还支持[键盘快捷键](http://www.splitbrain.org/dokuwiki/wiki:accesskeys)。

+文档的旧版本单独存储在压缩档案中。支持在线[差分](http://www.splitbrain.org/dokuwiki/wiki:features?do=revisions)。

+ Dokuwiki 管理冲突，当两个人编辑同一个文档时，以类似于 CVS 的方式-第二个保存他们文档的人被迫管理更改(在 diff 的帮助下)。

+它是时髦的兼容(CSS，XHTML，RSS 等。).

不利的一面是，它似乎还不能处理 i18n 字符集(这可能需要重新编写它的 wiki 解析器，目前它依赖于 [PCRE](http://www.php.net/pcre) )但是代码库(令人惊讶地)很小，而且通常很容易理解——代码还有一些改进的空间(无论如何从我的角度来看),但是没有什么大的改进，我想增量式的修改会很容易。因为基本原理是正确的(特别是文件存储的方式)，我可以看到自己能够将这与 WACT 的 API 文档相结合。

它是否会扩展也是一个没有答案的问题。例如，索引生成可能需要一些检查，一些配置文件(如 wordblock 文件)可能需要随着文件的增长而分成更小的部分。同样，因为我认为 Dokuwiki 在存储文件方面做得很好，所以针对容量进行调整应该不是一个大问题。

无论如何，希望这是一些有用的研究。感谢 Andreas 的 Dokuwiki——有机会在 Sourceforge 上得到它吗？

## 分享这篇文章