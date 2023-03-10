# 正则表达式的乐趣[3]

> 原文：<https://www.sitepoint.com/the-joy-of-regular-expressions-3/>

继[的上一部分](https://www.sitepoint.com/the-joy-of-regular-expressions-2/)之后，这一部分更像是一个幕间休息——总结了目前为止看到的正则表达式语法，以及反馈后的几个链接。

[第四部在这里](https://www.sitepoint.com/the-joy-of-regular-expressions-4/)。

## 读

首先你*有*去看看[安德烈的 Regex 诊所](http://www.gravitonic.com/do_download.php?download_file=talks/phptek-2006/regex-clinic_phptek2006.pdf)(幻灯片/pdf)——即使你没有得到全部，为了开头的图片也是值得的；)这是 Andrei，在[中的 Zmievski](http://www.gravitonic.com/) ，在雅虎中的[工作，可以被指责为](http://www.gravitonic.com/about/resume.php) [Smarty](http://smarty.php.net) 和 [PHP-GTK](http://gtk.php.net) ，是 PHP6(带 Unicode)上市时需要感谢的人之一，很久以前，甚至[接受了 Sitepoint](https://www.sitepoint.com/article/php-gtks-andrei-zmievski) 的采访。

Andrei 的讲话也促使我坦白:我没有资格告诉你正则表达式背后的理论(如果你感兴趣，从这里开始和谷歌了解更多-或者惹恼[这些家伙](http://lambda-the-ultimate.org/))-我来自一个实用的角度，所以尽管这些博客将(有希望)帮助你发现正则表达式是一个有用的工具，但不要指望找到如何编写自己的正则表达式引擎。

另一篇文章是 [Jeff 的](http://www.procata.com/blog)解释[两个 preg_replace()转义 gotcha 的](http://www.procata.com/blog/archives/2005/11/13/two-preg_replace-escaping-gotchas/)，它描述了问题的本质，并提供了一个解决转义*替换*字符串的方法。

## 累积语法备忘单

所以——一个包含“regexy”术语的复习。

*   **表达式分隔符:**–标记模式本身的开始和结束–在此讨论并注意与`preg_quote()`的相关性–第二个参数–在此讨论。
*   **文字:**与真实字符“一对一”匹配，例如字母数字字符:模式`/a/`与“a”匹配——例如这里的。
*   **模式修饰符:**–改变 regex 引擎的全局行为，并放在第二个表达式分隔符之后。到目前为止，您看到的模式修饰符有:
    *   `/i`–“不区分大小写”匹配–模式中使用的字母表中的文字字符将匹配正在搜索的字符串中的小写或大写字母–例如这里的。
        *注意:*到目前为止我还没有提到的是，这种行为也可能受到您的服务器的区域设置的影响，类似于`w`的问题——参见“细节过载！”注[此处](https://www.sitepoint.com/the-joy-of-regular-expressions-2/#word_metacharacters)
    *   `/e`—`preg_replace()`的“邪恶 eval”——此处见但注意[这个评论](https://www.sitepoint.com/the-joy-of-regular-expressions-2/#comment-60660)re:“eval 是邪恶的”。
    *   `/x`—“扩展模式”,允许在你的模式中添加注释——在这里简单提一下..
*   **字符类:**多对一匹配，即一个模式中的一个字符类可以匹配正在搜索的字符串中的一个字符列表。到目前为止你看到的角色类是 [PCRE](http://www.pcre.org) “内置”；
    *   `w`——一个“单词字符”——基本上是字母表中的任何字母或数字——这里讨论的
    *   `W`–除了单词字符之外的所有东西(空格、标点符号等)——这里也讨论了
    *   `.`(句号)——表示“任何东西”——匹配任何字符(但是警告:换行字符是一种特殊情况，我还没有谈到)——这里的例子
    *   …或者 DIY，例如`[a-zA-Z0-9_]`–这里的例子
*   **量词:**应用于字符(或元字符)前面的*来改变它们匹配的次数(它们影响匹配的长度)。到目前为止你见过的量词是；*
    *   `+`——意为“一个或多个”——这里的例子
    *   `?`——意为“零或一”——这里的例子
    *   `*`——意为“零或更多”——这里也用
    *   用花括号括起你自己的，例如{5，20 }–参见[此处](https://www.sitepoint.com/the-joy-of-regular-expressions-1/#Quantifying_Length)
*   断言:强加一个条件，需要满足该条件才能进行匹配，但该条件本身并不成为匹配的一部分。你已经见过的断言元字符有:
    *   `^`–断言“一行的开始”或被搜索字符串的开始——这里讨论。
    *   `$`–断言“行尾”或被搜索字符串的结尾–这里的[也涵盖了](https://www.sitepoint.com/the-joy-of-regular-expressions-1/#Exact_Match)。
    *   `b`–断言一个“单词边界”——由`w`(单词字符类)匹配的一个或多个字符遇到由`W`(非单词字符类)匹配的一个或多个字符的*点*——此处包含。
*   **子模式**——允许你对模式的部分进行分组，允许你为`preg_replace()`和其他我们还没见过的好东西“捕捉”它们——这里讨论。
*   **反斜杠**——这里讨论的[转义字符](https://www.sitepoint.com/the-joy-of-regular-expressions-2/#Escaping_meta_characters)(另见`preg_quote()`的[讨论](https://www.sitepoint.com/the-joy-of-regular-expressions-2/#preg_quote))

仅此而已。

## 分享这篇文章