# PHP UTF-8 0.1

> 原文：<https://www.sitepoint.com/php-utf-8-01/>

很长一段时间以来，我一直在摆弄这些代码，实际上是从第一次真正掌握 [Dokuwiki](http://wiki.splitbrain.org/) 开始，但最终还是让[第一次发布](http://prdownloads.sourceforge.net/phputf8/utf8-0.1.tar.gz?download)出来了。

[PHP UTF-8](http://sourceforge.net/projects/phputf8) 旨在使在 PHP 中处理 UTF-8 编码的字符串成为可能，而不需要 [mbstring](http://www.php.net/mbstring) 扩展(尽管它使用 mbstring，如果它可用的话)。简而言之，它提供了 PHP 字符串函数的版本(几乎所有你可以在[这个列表](http://www.phpwact.org/php/i18n/utf-8)中找到的东西)，以`utf_`为前缀，并支持 UTF-8 编码(1 个字符> = 1 个字节)。它也给你一些工具来帮助检查 UTF-8 字符串的“格式良好”，去除坏的序列和一些“ASCII 助手”。

部分代码是从别处拆来的——这要感谢[安迪·戈尔](http://www.splitbrain.org/) ( [多库维基·UTF-8](http://dev.splitbrain.org/view/darcs/dokuwiki/inc/utf8.php))和[亨利·西沃宁](http://hsivonen.iki.fi/)的 [UTF-8 到代码点数组转换器](http://hsivonen.iki.fi/php-utf8/)(从 Mozilla 代码库移植到 PHP)。

您不得不原谅我的一点骄傲，但是从最关键的本机(非 mbstring)函数的初始基准来看，性能几乎与 mbstring 函数一样好——在可接受的范围内。关键是[这个灵感提示](http://www.php.net/manual/en/function.strlen.php#45407)。否则就把那个 [/u PCRE_UTF8 模式修改器](http://www.php.net/manual/en/reference.pcre.pattern.modifiers.php)用好了。

无论如何——除了源代码中的内联之外，文档很少——最终需要做一个教程。对于“从零开始”的介绍，最好去的地方是 [WACT](http://www.phpwact.org/) 维基的[字符集](http://www.phpwact.org/php/i18n/charsets)和 [PHP utf-8](http://www.phpwact.org/php/i18n/utf8) 页面。

如果你打算使用它，一个很大的警告是——不要“盲目地”用本库的函数替换 PHP 的字符串函数——大多数函数是你偶尔会用到的，虽然性能是“可接受的”,但没有真正的 str*快。你必须清楚的两件关键事情是，ASCII-7 (US-ASCII)是 UTF-8 的子集，每个有效的 UTF-8 序列都是唯一的(不能被误认为是另一个更长序列的子序列)。值得花很长时间看一下[这一页](http://en.wikipedia.org/wiki/UTF-8)的表格。

无论如何——希望它有助于使 mbstring 独立的 PHP 应用程序[接近可能的](http://www.joelonsoftware.com/articles/Unicode.html)。如果做不到这一点，PHP6 可能会在今年年底问世。

## 分享这篇文章