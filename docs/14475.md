# PHP 和应用进化

> 原文：<https://www.sitepoint.com/php-and-application-evolution/>

[Ian Bicking](http://blog.colorstudy.com/ianb/weblog/) 在 [PHP 和应用程序进化](http://blog.colorstudy.com/ianb/weblog/2004/07/29.html#P137)中引发了一些有趣的想法，在 Python 和 Zope 的背景下，灵感来自于[不久前我](http://www.kuro5hin.org/comments/2004/7/14/232752/474/74#74)[在博客](https://www.sitepoint.com/blog/)上对那篇文章的开明评论。

> 我认为 Python 的命令式方法是一个真正的特性。通过构建对象(或进行“整体设计”)开始一个应用程序不是一个好主意，除非你非常了解这个领域

然后从他链接的评论中。

> PHP 面向对象是最少的，只用于第三方包(这是它在 web 应用中应该的方式)，而且它的语法也非常简单。

如果我们认为 web 应用程序通常有三个“层”(即使它们在源代码中无法区分)——一个数据层(例如从 db 获取数据)，一个应用层(在 PHP 应用程序中通常很薄，但可能包括简单的数字处理、排序等)和一个表示层(响应传入的请求，呈现 HTML)。 看起来最成功/最实际的方法(就完成项目/达到工作原型而言)是面向对象的数据层，程序代码的表示层和两者的混合应用层。 根据我自己的经验以及对像 [WordPress](http://wordpress.org/) 这样成功的应用的观察，只要你不疯狂地使用全局变量，这种方法很难失败，至少在你的应用*真正开始增长之前。*

同时 [Jeff](http://www.procata.com/blog) 做了出色的工作，为 WACT[MVC](http://wact.sourceforge.net/index.php/ModelViewController)的实现奠定了基础；即使 PHP 不是你所喜欢的，他所做的事情也是值得探究的。然而硬币的另一面是，正如你从[参考 CRUD 应用](http://wact.sourceforge.net/examples/show.php?group=apps/crud/)中看到的，突然出现了大量的*类，即使是相对简单的应用。最近与 Jeff 交谈时，当前的设计最终可能会再次变得“愚蠢”——失去的是基本的简单性。在构建一个真正灵活的 MVC 实现时，让不寻常的用例*成为可能*似乎会妨碍让普通用例变得简单。*

总之——只是在沉思这头[大象](http://ddickerson.igc.org/php-elephant-ani.gif)的状态。

除此之外，phpPatterns(以及我的电子邮件)在过去的几天里一直处于关闭状态。我正在使用的 DNS 服务提供商决定等我去度假的那一周再做彻底的改变。希望将在 24 天内恢复(任何紧急电子邮件；请重新发送)。

当那种轻松的假期感觉在回家后瞬间消失时，你难道不喜欢吗？

## 分享这篇文章