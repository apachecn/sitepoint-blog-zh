# del.icio.us 的惊人之处在于

> 原文：<https://www.sitepoint.com/the-surprising-thing-about-delicious/>

至少对我来说，尽管我经常使用它，但我很少访问网站本身(或者至少 T2 在添加书签时看起来是这样的)。

这要感谢 Firefox 和两个扩展——[http://delicious.mozdev.org/](http://delicious.mozdev.org/)(在右击页面时添加上下文菜单，发布到 del.icio.us 等)和[fox ylius](http://dietrich.ganx4.com/foxylicious/)(允许你从书签菜单查看你的 del.icio.us 收藏夹)。合并扩展后，我使用普通的 del.icio.us UI 所需的点击/击键次数减少了至少 50%。

这又回到了我在[将浏览器与资源](https://www.sitepoint.com/blog/)分离中嘀咕的内容。

那么这如何转化为像 [DokuWiki](http://www.splitbrain.org/dokuwiki/) 这样的应用呢？稍微摆弄一下，创建一些页面，让我觉得那些按钮是多么多余(编辑页面，显示修改等等。).

相反，如果你右击任何一页，就会看到一个类似于；

.组织者

..编辑页面

..[查看修订](http://www.splitbrain.org/dokuwiki/wiki:playground?do=revisions)

..[反向链接](http://www.splitbrain.org/dokuwiki/wiki:playground?do=backlink)

…这些可以将您带到相关的页面/表单(可能会打开一个新的选项卡或弹出窗口)。此外，如果你有一个书签文件夹，向你显示[最近的变化](http://www.splitbrain.org/dokuwiki/?do=recent)以及[索引](http://www.splitbrain.org/dokuwiki/wiki:playground?do=index)，这基本上消除了除搜索之外的所有按钮(这很容易成为一个[搜索插件](https://www.sitepoint.com/blog/))。剩下的只是资源本身——维基页面。

这个简单的分析表明，Dokuwiki Firefox 扩展的代码与 del.icio.us 扩展不会有太大的不同……它能为其他在线应用程序工作吗？Sitepoint 的论坛呢？

现在进入未知领域……我们能不能开始用 PHP 生成一个[火狐 XPI 安装程序](http://www.bengoodger.com/software/mb/extensions/howto.html),它准备好添加上下文菜单，然后弹出必要的工具来使用网站/网络应用程序？我们有 [RAP](http://www.wiwiss.fu-berlin.de/suhl/bizer/rdfapi/) 和 [PEAR::Archive_Tar](http://pear.php.net/package/Archive_Tar) (见 [Davey 的](http://pixelated-dreams.com/blog/) [PHAR 的创意提案](http://pear.php.net/pepr/pepr-proposal-show.php?id=88))。

在我看来，这样做的好处是用户体验变得更好/更快，网站开发变得更容易——不再需要考虑 MVC 之类的东西。您“简单地”构建用于查看内容的页面，然后像编辑内容这样的工具作为具有独立 UI 的独立脚本来实现。

## 分享这篇文章