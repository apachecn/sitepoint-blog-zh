# 动态编程中的设计模式

> 原文：<https://www.sitepoint.com/design-patterns-in-dynamic-programming/>

Via [reddit](http://reddit.com/) (如果你有注意力缺失的话，这是个很棒的网站):[动态编程中的设计模式](http://www.norvig.com/design-patterns/ppframe.htm)作者[彼得·诺维格](http://www.norvig.com/vita.html)(谷歌研究总监)。

在幻灯片形式中，要想提取出除了所讲内容的要点之外的任何东西有点棘手，但这里有一些真正有趣的东西。也许在/；

> 设计模式不为人知的秘密是它们与一种语言紧密相连。例如，从像 Java 这样的静态类型语言转换到具有转发功能的动态语言，极大地改变了工厂、代理、单例、观察者等的方法。事实上，它们经常被渲染得微不足道。

这是我第一次认真尝试识别动态语言特有的模式，并发现自己在点头。例如[部分评估【幻灯片 1】](http://www.norvig.com/design-patterns/img052.gif)和[幻灯片 2](http://www.norvig.com/design-patterns/img053.gif)；

> 意图:编写有文化的代码，编译成高效的代码

PHP 中一个典型的例子是 [WACT 的](http://www.phpwact.org) [模板组件](http://www.phpwact.org/wact/template_component_architecture)，它把[声明性](http://en.wikipedia.org/wiki/Declarative_programming)模板[标签](http://www.phpwact.org/tag/supported_tags)变成了“运行时”PHP 面条(你不需要关心)。相信这也是 [Rails](http://www.rubyonrails.org/) 的一大卖点，它很好地利用了 Ruby 的[宏功能](http://randomhacks.net/articles/2005/12/03/why-ruby-is-an-acceptable-lisp)来定义活动记录中的对象关系——通过比较，也许最接近的 PHP 可以用 [EZPDO](http://www.ezpdo.net) 来说明，它让你将这些语句放在 PHP 注释中，旁边是 EZPDO 提取的代码。

也就是说，并不是所有这些谈话都很适合 PHP，PHP 并不声称“一切都是对象”；例如，函数不是对象，在运行时以编程方式修改类的支持基本上是不存在的。我认为这是一种苦果——当 PHP5 的对象模型被设计的时候，Java 把 OOP 逼到了绝路，PHP5 把它推向了那个方向。随着像 Ruby、Python 和 Javascript ( [Lisp 披着 C 语言的外衣](http://www.fawcette.com/javapro/2002_09/online/javascript_dcrockford_09_13_02/))这样的语言成为主流，你可能会问“PHP 足够动态吗？”

## 分享这篇文章