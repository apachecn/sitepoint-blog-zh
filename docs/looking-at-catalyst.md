# 关注催化剂

> 原文：<https://www.sitepoint.com/looking-at-catalyst/>

一直在阅读 [London.pm](http://london.pm.org) 上的[伦敦网络框架](http://blog.unixdaemon.net/cgi-bin/blosxom.pl/2005/10/27) [验尸](http://london.pm.org/pipermail/london.pm/Week-of-Mon-20051114/000165.html)。那件事和那件事让我希望我当时在那里，而不是现在在这里。

无论如何，显然 Perl 人群感到[催化剂](http://catalyst.perl.org/)“丢失了”。什么是催化剂？基本上 Perl 的[回答](http://dev.catalyst.perl.org/wiki/VersusRails)到 [Rails](http://wiki.rubyonrails.com/rails/pages/Compare) 。

读了西蒙·威斯特的评论，我个人对催化剂更有信心，因为催化剂是灵活的。

需要有一个类似于“[框架是为生活服务的，而不仅仅是为圣诞节服务的](http://pets1st.com/articles/00074PuppyBootCamp.asp)”的口号。一旦你开始针对一个框架编写代码，你就会被它缠住，至少只要你愿意维护这些代码。

我以前被 PHP 框架烧过，有[非常](http://www.phpwact.org/php/mvc_frameworks) [许多](http://dmoz.org/Computers/Programming/Languages/PHP/Scripts/Frameworks/)可供选择。我一次又一次遇到的主要问题是:

1.  它不完整——工作正在进行中(可能已经停止)
2.  当涉及到边缘情况时，它太不灵活了，我的经验是，实际上，实际项目中有 50%以上是边缘情况。一旦你遇到一个边缘情况，很容易把你在其他地方节省的时间浪费掉，以至于你甚至认为“普通 PHP”(或者你正在使用的任何东西)可能更聪明。
3.  中途我意识到我误解了它的用途，现在有一堆代码需要彻底检查。与第二点相结合，没有什么便宜的方法可以阻止我的出路——重写。
4.  没有集成–花费太多时间将不同的库粘在一起
5.  简单地说，这个框架很糟糕(这些天我可以提前发现)。

附注:遗憾的是，WACT 仍然属于第一类。这里(非常诚实地标记为 alpha)这是一个耻辱，因为个人认为[杰夫](http://www.procata.com/blog)坐在 PHP 的“Rails 的答案”上，不需要比较。事实上，我认为杰夫的设计真正挑战了 web 上 MVC 的概念——只有通过链接到他的[刽子手](http://cvs.sourceforge.net/viewcvs.py/wact/wact/examples/apps/hangman/index.new.php?view=auto)的例子才能真正表达出来(我猜你要么“得到”要么得不到)。杰夫的 [MVC](http://www.phpwact.org/pattern/model_view_controller) 笔记也很出色。我对 WACT 的愿望在这里[描述](http://www.catb.org/~esr/writings/cathedral-bazaar/cathedral-bazaar/ar01s04.html)，也许有一天会从这里[开始滚动](http://channel.phpwact.org/)。

因此，很长一段时间以来，我一直避免使用 MVC，坚持以页面为中心的设计，因为它们很有效，能把事情做好。旁注:那些大喊“spagetti”的人应该进化——通过自律(是的——这通常不会扩展到团队开发),你可以写出这样的东西。

无论如何——我开始认真考虑一个个人项目，这个项目将围绕购物车和婴儿服装展开。没什么大不了的，但在调查了“现成的”开源选项后，它意识到这是一项 DIY 工作。

一个朋友，我相信他的建议，说服我看一看 Catalyst，我越看越喜欢。我更加确信[阅读这篇](http://www.perl.com/pub/a/2005/11/17/handel.html)，具体到我想做的事情。我的主要问题是时间——第二个孩子很快就要出生了，之后的时间就更少了，我想要的是一个可以预见未来的东西。

所以，假设我开始工作，我将看看 Catalyst 的进展如何。如果给我更多的时间，我可能会记录一些经验——这些“PHP 程序员的 Perl”笔记在我的脑海里已经有很长时间了，可能会适合这一点。

也许这是对感到 Catalyst 丢失的 Londom.pm 人群的某种安慰，例如“PHP 粉丝想 Catalyst”；)

## 分享这篇文章