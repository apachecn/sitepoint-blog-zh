# 伊瓦尔死了。Eval 万岁！

> 原文：<https://www.sitepoint.com/eval-is-dead-long-live-eval/>

我是拉克伦·唐纳德，SitePoint 的新人。我将和这里的其他人一起帮助 PHP 博客内容。

最近，Eval 似乎是一个热门的讨论话题，特别是考虑到最近[vBulletin](http://www.vbulletin.com/)利用和[过去利用](http://www.osvdb.org/8400)在常见应用程序中，如 [phpMyAdmin](http://www.phpmyadmin.net/) 。Eval 是 PHP 中可以执行任意代码的函数之一。一般来说，eval 要么被缺乏经验的程序员出于各种错误的原因使用，要么被试图突破 PHP 界限的人使用。激励我发表这篇文章的是 PHP 的创始人拉斯马斯·勒德尔夫的一句话:

> 如果 eval()是答案，那么您几乎肯定问错了问题。

也许这只是我喜欢争论的天性，但是当人们引用这句话时，我总是想知道是否有一些共同的问题只能通过使用 eval 来解决。在我进入本质细节之前，简要回顾一下 [eval 实际上做了什么](http://www.php.net/manual/en/function.eval.php) [PHP 手册](http://www.php.net/manual/)中的:

`mixed **eval** ( string code_str )`

`**eval()** evaluates the string given in code_str as PHP code.
Among other things, this can be useful for storing code in a database
text field for later execution.`

对于一个没有经验的程序员来说，这听起来可能是一个奇妙的想法，允许代码片段存储在数据库中，并根据与记录一起存储的其他标准来执行。实际上，编写一个解释与用户数据一起存储的代码片段的应用程序是在自找麻烦。以这个例子为例，它调用一个函数并将结果赋给一个变量。

 `function getTemplate($tpl) { return "a template"; }`

`eval('$content = getTemplate("'.$_GET['tpl'].'");');`

禁用[魔术引号](http://au2.php.net/manual/en/ref.info.php#ini.magic-quotes-gpc)会产生一个巨大的安全漏洞，因为攻击者要执行任意 php 代码只需将代码插入请求 url。例如，以下查询字符串导致当前目录的内容输出到屏幕上。

`/eval.php?tpl=%22);+passthru(%22ls+-al`

当然，这个例子是人为设计的，但是它非常类似于最近 PHPBB 和 VBulletin 攻击的目标代码。这种代码通常是懒惰编程或糟糕设计的结果。Eval 的一些更高级的用途危险性更小，也更有趣，例如:

*   在简单测试中生成[模拟对象](http://www.lastcraft.com/mock_objects_documentation.php)
*   定义一个[兼容 PHP4 的 clone()函数](http://www.acko.net/node/54)
*   通过引用函数来传递可变数量的参数

请随时张贴反馈或使用 eval 的好或不好的例子。

## 分享这篇文章