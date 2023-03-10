# PHP 安全:哑用户还是哑 API？

> 原文：<https://www.sitepoint.com/php-security-dumb-users-or-dumb-apis/>

又有一轮“PHP 安全吗？”辩论正在进行。克里斯引起了人们的注意，他指向了[安德鲁·范·德·斯托克](http://www.greebo.net/)(他是 [OWASP](http://www.owasp.org) 的撰稿人) [PHP 不安全:领导失败](http://www.greebo.net/?p=320)。

所以通常的否认已经出现了(见对 Chris 条目的回复)“该死的新手”、“基于 PHP 的应用程序有漏洞！= PHP 不安全”等。，这些我都同意。但是…

认为*是*安全的假设尖头发的老板正在接收包含“PHP”和“漏洞”的[信号](http://news.bbc.co.uk/1/hi/technology/4117711.stm)。在这一点上，也许你不在乎——你的堡垒把尖头发的人挡在外面。但是一旦你碰到了这面墙，在和 PHP 竞标/争论的时候(我也遇到过后一种情况)，你就开始怀疑了。

因此，让我们暂时扭转局面，尝试一些新的讨论。

如果安德鲁说的有道理呢？如果我们生活在否认中呢？我认为安德鲁最初被“杀死”是因为他的论证过于谨慎，而不是太令人反感。但后来(见他对克里斯帖子的第二条评论)认为他终于搞定了；

> 支柱和。默认情况下，NET 都使最容易使用的输出方法 XSS 变得安全。
> 
> `<bean:write ...`
> 
> 和
> 
> 。NET 的 web 表单有页面验证方法，可以自动检测大多数形式的 XSS 攻击**，而无需编写任何代码**。

现在让我们对比一下这个看起来很简单的 PHP 和 HTML 代码片段；

```
  <form action="<?php echo $_SERVER['PHP_SELF']; ?>">
   <input type="hidden" name="submitted" value="1" />
   <input type="submit" value="Submit!" />
  </form>

```

我们甚至不需要接受任何用户输入——我们只是显示一个表单，对吗？这是一个在网上广为流传的例子——一个避免硬编码脚本名称的好方法。哎呦——对 [XSS](http://blog.phpdoc.info/archives/13-XSS-Woes.html) 感到抱歉。

那么谁更笨呢？“愚蠢的新手”没有理解$_SERVER superglobal 和 [CGI](http://en.wikipedia.org/wiki/Common_Gateway_Interface) 或 PHP 之间的关系，因为他们没有为这个非常常见的问题提供一个简单的*和*安全解决方案:如何显示一个提交给自己的表单，而不用硬编码 action 属性？

让我们变得更有争议，选择[短开放标签](http://www.php.net/manual/en/language.basic-syntax.php)。伟大的东西，因为它允许你这样做:`<?=$someVar?>`。哦，等等……XSS 有更多的潜力。[艾伦·诺尔斯](http://www.akbkhome.com/wiki.php)很好地总结了这个话题[这里](https://www.sitepoint.com/savant-template-engine/#comment-2297)；

> 这就是为什么 PHP 风格的模板只是一个坏主意..—除非您到处复制和粘贴 htmlspecialchars，在这种情况下，您必须通过树来查看 bugs
> 
> 如果可能的话，输出层应该默认为转义代码，并使其容易找到没有进行转义的地方，而不是相反。

也许“智能”是让短标签样式自动通过 [htmlspecialchars](http://www.php.net/htmlspecialchars) 传递输出？

进入一个更加灰色的区域…另一个 PHP 现实是，如果你真的希望能够严格地进行用户输入验证，你需要[正则表达式](http://www.php.net/pcre)——这是没有办法的。

如果你知道如何使用正则表达式是没问题的，但是真正的新手(比如第一次编码)宁愿跑一英里。PHP 可以默认提供一些东西来解决这个问题吗，比如一组函数，用来验证*公共*输入？是的，我知道这是一条通向带有“charset”等可选参数的函数的黑暗之路，但重点是单词 *common* ，就像在通用用例中一样——可能那些家伙仍然将页面编码为 ISO-8859-1。现在大多数“每天十几个”的 PHP 开发框架都有一套默认的验证助手——他们为什么要这么做呢？

另一个途径是过滤。[吴镇男](http://www.derickrethans.nl/)提到[这个 PECL 扩展](http://pecl.php.net/package/filter)，他一直在和拉斯莫斯一起工作。这是一个白名单/黑名单的事情，怀疑者可能会说，这将导致 V2 的神奇报价。那么从 [Myspace 蠕虫](http://shiflett.org/archive/158)中吸取的教训呢？

也就是说，[杰夫](http://www.procata.com/blog/)在输入过滤[这里](http://www.procata.com/blog/archives/2005/03/31/the-usability-of-input-filtering/)提出了很好的观点，值得记住他的结束语；

> 我想我已经编程 23 年了。做的时间越长，就越不愿意对用户输入严格要求。超净化、超结构化的数据对程序员来说可能很有吸引力，但对用户来说却是痛苦的，合法的异常出现只是时间问题。欧洲电话号码、美国第 51 个州、加拿大邮政编码、新千年等等。例外是规则。可以理解，必须阻止 XSS，但这很容易走得太远。

继克里斯的帖子之后，马尔科也有一些有趣的事情要说。

> 我不认为安全性属于语言本身。

你必须在“语言”这个词上停顿一下。斯特林·休斯([博客](http://www.edwardbear.org/blog/)现在翻录但这里引用曾经说过的话；

> 如果 PHP 是什么的话，那么它就是一个框架，包括一个非常可爱的脚本语言

不管你对此有什么看法，这都不重要。重要的是你得到的[与](http://www.loudthinking.com/arc/000553.html)相比。

先生，你想说什么？

事实上，我真的没有。这里只有艰难的选择和努力。打破向后兼容性来“修复”像`$_SERVER['PHP_SELF'];`或短标签这样的东西显然不是一个选项。增加替代品和更多功能可能也没有多大帮助——让人们使用它们是一件困难的事情。

我认为我们需要停止指责“愚蠢的新手”,并承认存在问题。如果你愿意，可以称之为学习曲线问题。或者承认 PHP 是在 web 天真无邪的时代构思出来的。随便啦。指着[可以得到的](http://www.amazon.com/gp/product/059600656X) [书籍](http://www.phparch.com/shop_product.php?itemid=98)警告他们要小心。

而且很可能我们将不得不基于这个平台 ( [早发布，经常发布](http://www.catb.org/~esr/writings/cathedral-bazaar/cathedral-bazaar/ar01s04.html))进行未来的投标！)，并鼓励其他人也这样做。为了新手，我希望“极度简单”和 [MVC](https://www.sitepoint.com/mvc-and-web-apps-oil-and-water/) 能够放在同一个句子里。

但是在比雅尼·斯特劳斯特鲁普的帮助下，以积极的方式结束；

> 世界上只有两种语言:人们抱怨的语言和没人使用的语言

## 分享这篇文章