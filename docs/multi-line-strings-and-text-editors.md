# 多行字符串和文本编辑器

> 原文：<https://www.sitepoint.com/multi-line-strings-and-text-editors/>

PHP 方便地允许使用两种主要方法来使用[多行字符串](http://www.php.net/manual/en/language.types.string.php)，其中一个字符串常量被分成多行。然而，很难找到一个语法高亮文本编辑器来正确地高亮 PHP 中的多行字符串。

当我最近切换到文本编辑器时，语法高亮时对多行字符串的支持在我的必需特性列表中排在前列。

我发现多行字符串对于编写 SQL 来说很方便，因为在一行中编写一个重要的 SQL 查询不会产生可读性很好的代码。

下面是 PHP 中多行字符串的两个例子。第一种是简单的多行引用字符串。

 `$sql = "
SELECT
product.productid,
product.title
FROM
product
WHERE
product.type='boardgame'
";`

下一个例子使用了 [heredoc](http://www.php.net/manual/en/language.types.string.php#language.types.string.syntax.heredoc) 语法，它允许您指定一个标记来结束字符串。

 `$sql = <<`

 `我现在用的文本编辑器是 [Syn](http://syn.sourceforge.net/) ，支持多行引号字符串但不支持 heredoc 语法。如果可以选择，我宁愿使用带引号的字符串，而不是 heredoc。

这是我评估过的所有编辑。

Syn 可以突出显示多行字符串，但是我在他们网站上的最新版本中发现了几个错误，这个版本是 2004 年 2 月发布的。顺便说一下，如果你给开发者发邮件，他会很乐意给你一个修正了错误的版本。唉，这些修正还没有成为正式版本。

我对 [Notepad++](http://notepad-plus.sourceforge.net/) 的速度和响应能力以及代码折叠等附加功能印象深刻。Notepad++确实可以正确地突出显示多行字符串，但是我找不到一种方法来显示一个类似于 Syn、HTML-Kit、JEdit 和 Editplus 等编辑器中的“文件夹视图”面板。如果有人知道方法，请告诉我。

[TextPad](http://www.textpad.com/) 做多行字符串。让我对这个编辑器不感兴趣的是，我找不到文件夹视图面板，并且在设置语法突出显示时，我无法指定粗体或斜体。缺少粗体和斜体的支持对我来说并不重要。

JEdit 在其语法高亮中支持多行字符串。这是一个令人印象深刻的编辑器，有大量的插件，但不幸的是，我发现它加载非常慢。响应性是我的主要标准之一，也是我现在选择轻量级 [Syn](http://syn.sourceforge.net/) 的原因之一。

UltraEdit 看起来很棒，但不幸的是没有多行字符串的语法高亮显示。根据他们的网站，这将在版本 11 中得到支持。

[Crimson 编辑器](http://www.crimsoneditor.com/)不错，但是不支持多行字符串。不知道以后的版本会不会加这个。

HTML-Kit 是一个很棒的编辑器，并且一直是我的首选编辑器，直到最近。虽然它支持 heredoc 语法，但不支持引号中的常规多行字符串。根据他们的 FAQ，由于架构限制，支持将不会被添加，也不会在插件中提供。但是，如果您不关心多行字符串，我肯定会推荐这个编辑器。

[EditPlus](http://www.editplus.com/) 不支持多行字符串，也不支持配置语法高亮时的粗体/斜体。除此之外，它非常好，有一个文件夹视图，还有许多不错的额外功能。

为什么需要支持多行字符串？这只是我个人的喜好。然而，还有其他方法可以达到同样的效果。首先，有 heredoc 语法，它(令我惊讶的是)被更多的编辑器支持，而不是常规的多行字符串，尽管仍然不是大多数。其他替代方法是变通方法，如下所示:

 `$sql =
"SELECT"
. " product.productid,"
. " product.title"
. "FROM"
. " product"
. "WHERE"
. " product.type='boardgame'";`

我发现添加这些点和引号是非常令人沮丧的，然而，即使是现在！这会使 SQL 在将来很难维护。

你如何处理代码中的多行字符串？你是把 SQL 写在一行，把它分成多个字符串，还是用多行字符串？

*注意:最初当我编译这个列表时，我写道 JEdit 不支持多行字符串。经过今天的测试，我发现确实如此。我已经改正了我的错误。*` 

## 分享这篇文章