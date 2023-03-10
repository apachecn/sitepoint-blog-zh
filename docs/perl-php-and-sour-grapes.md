# Perl，PHP 和酸葡萄？

> 原文：<https://www.sitepoint.com/perl-php-and-sour-grapes/>

在 [CPAN](http://search.cpan.org/) (可重用模块的 Perls 库)中搜索“PHP”，你可能会对结果感到惊讶。

比如 [PHP::Strings](http://search.cpan.org/~kudarasp/PHP-Strings-0.28/Strings.pm) 。这个模块有趣的地方在于，它部分地用 Perl 实现了 PHP 的字符串函数，或者告诉你在哪里可以找到更多的信息。

以下面的代码为例；

 `#!/usr/local/bin/perl -w
use strict;`

 `使用 PHP::Strings qw(:trim)；` 

`my $word = " Hello World! ";
$word = trim($word);`

导致以下消息；

> <:strings::trim will="" not="" be="" implemented="">。

…并让您参考关于[如何从字符串的开头/结尾去除空格的 Perl 常见问题？](http://faq.perl.org/perlfaq4.html#How_do_I_strip_blank)。如果你是一个用 Perl 编程的 PHP 程序员，这是个好东西。

不太好的是[相关阅读](http://search.cpan.org/~kudarasp/PHP-Strings-0.28/Strings.pm#RELATED_READING)，它链接到一个虚拟的网络圈“我讨厌 PHP”咆哮。浏览列表，有一些反对 PHP 的有效的技术观点，但在开始使用 PHP 时，很大程度上混合了困扰 Perl 编码人员的常见“陷阱”所导致的误解。虽然强调的是技术比较，但给人的印象是有一些，更深刻的(非理性？)为什么(一些)Perl 程序员觉得有必要尝试一下 PHP。

为什么？

有人认为 PHP 窃取了 Perl 作为#1 web 语言的“合法地位”,这很有诱惑力。换句话说就是“酸葡萄”，但这可能不公平。

可能是因为(表面上)Perl 和 PHP 看起来太相似了(兄弟竞争等等。).但现在我不认为这是真的。PHP 已经成为解决“网络问题”的专业语言，而 Perl 仍然是一种强大的通用语言。尽管 PHP 最初是受 Perl 启发的，但 Java 似乎是 PHP 当前设计中更重要的因素。

不得不承认我不是 Perl 的最大粉丝，尽管对于批处理和网络之类的任务，我会选择 Perl(或者更好，Python)而不是 PHP。

对我来说，Perl 提供了太多编写不可读代码的机会。PHP::Strings 拒绝实现的 [trim()](http://www.php.net/trim) 函数凸显了这个问题。(对一个人来说)什么更有意义？

 `$string = trim($string);`

或者

 `$string =~ s/^s+//;
$string =~ s/s+$//;`

但那只是我自己的喜好。

无论如何，最终圣战不会有任何结果。更有用的是[这一期的 Perl 评论](http://www.theperlreview.com/Issues/v0i7.shtml)，它对 Perl 和 PHP 进行了出色的并排比较，如果你知道其中一个并需要使用另一个，这是一个很好的起点。

## 分享这篇文章