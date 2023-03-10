# 在你的网站上使用开源软件

> 原文：<https://www.sitepoint.com/open-source-software-website/>

**我不时地犯一个错误。**

不真的。

让我告诉你一个故事，一个关于我如何学会为我的客户使用自由软件的故事。

去年夏天，我建了一个二手车经销商的网站。我在后端代码上无休止地工作，放弃了其他几项工作，转而致力于我客户的库存系统。最终的产品很漂亮，我为此感到骄傲。我收了钱，兴高采烈地继续赶路。几周后，我意识到一些事情改变了一切。

在浏览软件库时，我遇到了一个类似的代码集，它也处理库存系统。这个代码是非卖品。它是在 [GNU 通用公共许可证](http://www.gnu.org/) (GPL)下许可的自由软件。如果我选择使用它，我将能够下载代码并在很少修改的情况下实现它。我心烦意乱，简直想用鼠标垫打自己。使用这个软件可以节省我的精力、时间和金钱。我本可以接受那些我拒绝的工作，或者放松一下，享受乐趣。

但是已经太晚了。工作结束了。

幸运的是，我很少犯两次同样的错误。我决定找出如何使用自由软件来使我的网页设计生意受益。这是我的发现。

##### 自由软件:编码的自由

在我开发二手车网站的时候，我对自由软件并不陌生，我已经把我的操作系统切换到了 GNU/Linux。GNU/Linux 是[自由软件](http://www.gnu.org/philosophy/philosophy.html#AboutFreeSoftware)，这意味着任何人都可以获取、修改和分发它。自由软件中的“自由”指的是这样做的自由；这类软件的另一个流行术语是“开源”。虽然自由软件的源代码是免费的，但许多公司以很少的费用分发该软件的二进制(可执行)版本。其他公司收取技术支持费用或通过硬件销售赚钱。听起来很有趣，自由软件是赚钱的好方法。

##### 服务器上的免费软件

那些托管网站的人可以通过在他们的服务器上使用免费软件来省钱和省事。Apache web 服务器因其稳定性和安全性而获得广泛赞誉，成为使用最多的 Web 服务器。流行的脚本语言如 [Perl](http://www.perl.org) 、 [PHP](http://www.php.net) 和 [Python](http://www.python.org) 也是自由软件。数据库服务器，如 [mySQL](http://www.mysql.com) 和 [PostgreSQL](http://www.postgresql.org) 为大多数网站的需求提供高质量的数据库解决方案。特殊的服务器软件，如 [Zope](http://www.zope.org) 和 [ArsDigita 内容系统](http://www.arsdigita.com)，将网络服务器、脚本语言和数据存储整合到一个工具中。这些系统简化了设计，使站点维护更加容易。前面提到的只是为服务器编写的一篮子自由软件中的一小部分。

##### 为您的客户提供免费软件

为一个人的客户使用和修改自由软件对于那些按工作获得报酬、没有多少时间、或者被支付持续维护和改进客户站点的人来说是一个双赢的局面。毕竟，如果有人愿意免费赠送蓝图，为什么有人要重新发明轮子呢？

许多网站专用的免费软件是为网站的后端开发的，但是像[开源网站设计](http://www.oswd.org) (OSWD)这样的网站正在改变这种情况。OSWD 允许任何人发布 HTML 布局，免费分发给其他设计师。事实上，我经常使用 OSWD 设计作为复杂布局的起点。其他类似的网站提供免费的 DHTML 脚本或 Java 小程序。

在 GPL 或其他免费许可证之一下许可的软件不仅仅是免费的；没有任何限制禁止任何人修改它。许多人认为 GPL 禁止任何人对 GPL 软件的衍生产品收费。然而，这种观点是有缺陷的。

例如，让我们说 J. Random Hacker 采用 project Foo(这是 GPL-ed)并改进它以用于一个公司的网站。本作品不被认为是发行版，因为它是为一家公司的使用而写的。事实上，J. Hacker 不必在 GPL 下提供 FooI(改进的 Foo)的源代码，除非他开始出售拷贝或允许公司以外的人下载和使用成品。

然而，允许他人下载自己的代码非常有帮助。事实上，在我写这篇文章的时候，我接到了另一个二手车经销商的电话，他想使用我的库存系统。我没有亲自接受这份工作，而是推荐了我的一个朋友，他现在正愉快地为他的新客户实现我的代码。毫无疑问，他会把任何错误修复程序发给我，让我和我的雇主都高兴。这种类型的合作对每个人都有帮助。相互合作运作得非常好，并且产生了自由软件在质量、稳定性和安全性方面的高度赞誉。

##### 何时不使用自由软件

尽管我的狂热者讨厌这么说，但我承认有时候人们不应该使用自由软件。问题？截止日期。当你有很短的时间来重新构建一个使用非自由软件编写的网站时，你最好的选择是重新编写现有的代码。当时间紧迫时，尝试将网站的服务器换成 Linux 或者用另一种语言重写代码并不是一个好主意。它可能不漂亮，但它的工作。

幸运的是，我通常有从最开始开始的奢侈，那时我可以很容易地在我的设计中为自由软件腾出空间。

##### 结论

自由软件可以通过降低成本、节省时间，甚至为你现有的代码提供免费的错误修复程序来促进你的业务。它为服务器提供了稳定性和安全性，通常可以节省开发时间，并通过免除您的重复劳动来削减成本。所以，照我说的去做，而不是照我做的去做，你的生意会蒸蒸日上，你会对你的工作更加满意，你会在 40 岁之前赚到数百万。

嗯，也许吧。

## 分享这篇文章