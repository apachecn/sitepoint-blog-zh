# 魔术报价头痛

> 原文：<https://www.sitepoint.com/magic-quotes-headaches/>

当我遇到在线应用程序有魔力引号和字符串转义问题时，我经常感到惊讶和/或沮丧。甚至我日常使用的商业 PHP 应用也有这样的问题。最常见的症状是斜线(/)出现在整个内容中。例如:

> 斜线是干什么的？

然而，还有其他不太明显的问题，由引号和转义处理不当引起，可能会更严重。当然，不对数据库查询中发送的代码进行转义等同于一个 [SQL 注入漏洞](https://www.sitepoint.com/article/sql-injection-attacks-safe)，这是 PHP 应用程序中最常见也是最严重的安全漏洞之一。

对于外行来说， [Magic Quotes](http://www.php.net/manual/en/security.magicquotes.php) 是 PHP 的一个特性，可以自动转义输入到 PHP 的字符串。例如，引号('或')被转义为
('或”)。其他字符(如空字符和反斜杠)也会被转义。

我认为引入魔术引号是 PHP 开发人员犯的一个错误——开发人员至今仍在为这个错误付出代价。虽然现在许多 PHP 安装已经关闭了所有的 Magic Quotes 功能，但有些 PHP 安装仍然支持向后兼容，所以问题依然存在。

那么，我对神奇的引用到底有什么问题呢？

名字

“魔力语录”这个名字很难不言自明。“魔法”一点也无助于描述它的意思，“引用”部分是误导。对于这个麻烦的特性，更恰当的名字应该是“自动字符串转义”。不仅仅是引用被这个过程转义了。

神奇的引用是为了让脚本对初学者来说更安全。然而,“魔力语录”这个名字对初学者来说很迷惑。它的存在还有助于防止初学者学习为什么在 SQL 中必须对字符串进行转义。

不同类型

如果这还不够混乱的话，PHP 中有三种不同类型的“神奇引号”设置，PHP 手册中描述了[。](http://www.php.net/manual/en/security.magicquotes.php)

最常见的类型是 magic_quotes_gpc，它通过 GET、POST 或 cookie 向提交给 PHP 应用程序的变量添加斜杠。

应用程序开发人员需要知道它是否打开

事实上，魔术报价设置可以打开和关闭是一个主要问题。如果它要么一直开着，要么一直关着，问题就不会这么严重。

对于应用开发者来说，知道在编码时是打开 magic_quotes_gpc 还是 magic_quotes_runtime 是至关重要的。如果 magic_quotes_gpc 是打开的，而一个程序员认为它是关闭的，并且自己对字符串进行转义，那么他将会在他的内容中以可怕的“额外斜线”结束。如果 magic_quotes_gpc 是关闭的，而程序员认为它是打开的，那么他就很容易受到 SQL 注入的攻击。

没有办法在脚本中禁用 magic_quotes_gpc，因为损害已经造成了。但是，可以检测它是打开还是关闭，并相应地采取行动。在[之前的一篇博客文章](https://www.sitepoint.com/blog/)(以及[的书](https://www.sitepoint.com/books/phpant1/))中，哈利描述了一种开发人员创建代码的方法，无论 magic_quotes_gpc 是打开还是关闭都可以工作。这段代码可以插入到应用程序中的某个地方(靠近开头):

 `// Is magic quotes on?
if (get_magic_quotes_gpc()) {`

 `//是吗？去掉添加的斜线

$ _ REQUEST = array _ map(' strip slashes '，$ _ REQUEST)；
$ _ GET = array _ map(' strip slashes '，$ _ GET)；
$ _ POST = array _ map(' strip slashes '，$ _ POST)；
$ _ COOKIE = array _ map(' strip slashes '，$ _ COOKIE)；` 

`}`

Magic Quotes 没有提高安全性或简化 SQL 命令中使用的字符串转义过程，而是给开发人员带来了额外的麻烦，并且在某些情况下，当脚本在使用与开发人员预期不同的设置的 PHP 安装上运行时，会在应用程序中产生令人讨厌的错误或导致安全漏洞。

在这篇文章中，Harry Fuecks 深入探讨了这个问题。具有讽刺意味的是，托管这篇文章的应用程序也有自己与魔术引号相关的问题——代码示例中的斜线(例如绿色文本)不见了！

现在，我建议开发人员自己进行字符串转义，就像 magic quotes 关闭一样，并使用上面的代码去掉 magic quotes 打开时 PHP 安装产生的额外斜杠。

## 分享这篇文章