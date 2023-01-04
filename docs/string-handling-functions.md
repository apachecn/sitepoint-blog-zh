# PHP 字符串处理函数

> 原文：<https://www.sitepoint.com/string-handling-functions/>

PHP 有大量的内置字符串处理函数可供选择，允许您以几乎任何可能的方式轻松操作字符串。然而，学习所有这些函数，记住它们做什么，以及它们什么时候会派上用场可能有点令人生畏，尤其是对新开发人员来说。我不可能在一篇文章中涵盖所有的字符串函数，而且，这也是 PHP 手册的目的！但是我要做的是展示如何使用一些你应该知道的最常用的字符串处理函数。在这之后，你将和任何音乐会小提琴手一样与弦乐打交道！

## 在这个案子上

PHP 提供了几个函数，使您能够操纵字符串中字符的大小写，而不必逐个字符地编辑字符串。你为什么会关心这个？好吧，也许你想确保某些文本都是大写的，比如首字母缩写词，标题，以示强调，或者只是为了确保名称的大写正确。或者，您可能只想比较两个字符串，并希望确保所比较的字母是相同的字符集。案例操作函数非常容易掌握；你只需将字符串作为参数传递给函数，返回值是经过处理的字符串。

如果您想确保特定字符串中的所有字母都是大写的，您可以使用如下的`strtoupper()`函数:

```
<?php
$str = "Like a puppet on a string.";
$cased = strtoupper($str);
// Displays: LIKE A PUPPET ON A STRING.
echo $cased;
```

可能显而易见但仍然值得注意的是，数字和其他非字母字符不会被转换。

正如您可能猜到的那样，`strtolower()`函数的作用与`strtoupper()`正好相反，它将一个字符串转换成全部小写字母:

```
<?php
$str = "LIKE A PUPPET ON A STRING.";
$cased = strtolower($str);
// Displays: like a puppet on a string.
echo $cased;
```

可能还有其他时候，你想确保某些词，如姓名或头衔，只是有每个词的第一个字母大写。为此，您可以使用`ucwords()`功能:

```
<?php
$str = "a knot";
$cased = ucwords($str);
// Displays: A Knot
echo $cased;
```

也可以使用`lcfirst()`和`ucfirst()`函数来处理字符串首字母的大小写。如果你想第一个字母是小写的，使用`lcfirst()`。如果希望首字母大写，使用`ucfirst()`。`ucfirst()`函数可能是最有用的，因为你可以用它来确保一个句子总是以大写字母开头。

```
<?php
$str = "how long is a piece of string?";
$cased = ucfirst($str);
//Displays: How long is a piece of string?
echo $cased;
```

## 快速修剪

有时候绳子的边缘需要修剪。它可能有空白或其他字符在开头或结尾，需要删除。空白可以是实际的空格字符，但也可以是制表符、回车等。可能需要这样做的一个例子是，当您处理用户输入时，您想在开始处理它之前清理它。PHP 中的`trim()`函数可以让你做到这一点；您可以将字符串作为参数传递，该字符串开头和结尾的所有空格都将被删除:

```
<?php
$str = "  A piece of string?  ";
// Displays: string(22) " A piece of string? "
var_dump($str);

$trimmed = trim($str);
// Displays: string(18) "A piece of string?"
var_dump($trimmed);
```

`trim()`也是多用途的，除了字符串之外，你还可以向它传递一组字符，它将删除任何从开头或结尾匹配的字符:

```
<?php
$str = "A piece of string?";
$trimmed = trim($str, "A?");
// Displays: string(16) " piece of string"
var_dump($trimmed);
```

当你使用这些额外的字符时，你需要小心，因为`trim()`只会移除空白，如果你特别提供它作为你想要移除的字符之一:

```
<?php
$str = "A piece of string?";
$trimmed = trim($str, "A ?");
// Displays: string(15) "piece of string"
var_dump($trimmed);
```

即使`trim()`只删除了字符串开头和结尾的字符，它也删除了“A”和空格，因为当“A”被删除时，空格变成了字符串的新开头，所以它也被删除了。

PHP 中还有`ltrim()`和`rtrim()`函数，它们类似于`trim()`函数，但只是分别从字符串的左边或右边移除空白(或其他指定的字符)。

## 一根绳子有多长？

很多时候，当你处理弦的时候，你会想知道它有多长。例如，在处理表单时，您可能需要一个字段来确保用户不能超过一定数量的字符。要计算字符串中的字符数，可以使用`strlen()`函数:

```
<?php
$str = "How long is a piece of string?";
$size = strlen($str);
// Displays: 30
echo $size;
```

## 将琴弦切割成合适的尺寸

另一种常见的情况是在给定的字符串中找到特定的文本，并将其“剪切”出来，这样就可以用它做其他事情。要将一个字符串剪短，你需要一把好剪刀，在 PHP 中，你的剪刀就是`substr()`函数。

要使用`substr()`函数，请将您想要处理的字符串作为一个参数和一个正整数或负整数一起传递。这个数字决定了你从哪里开始剪线；0 从字符串的第一个字符开始(请记住，当您对字符串进行计数时，左边的第一个字符从位置 0 开始，而不是从 1 开始)。

```
<?php
$str = "How to cut a string down to size";
$snip = substr($str, 13);
//Displays: string down to size
echo $snip;
```

当使用负数时，`substr()`将从字符串的末尾开始向后。

```
<?php
$str = "How to cut a string down to size";
$snip = substr($str, -7);
//Displays: to size
echo $snip;
```

`substr()`的第三个可选参数是长度，这是另一个整数值，允许您指定想要从字符串中提取的字符数。

```
<?php
$str = "How to cut a string down to size";
$snip = substr($str, 13, 6);
//Displays: string
echo $snip;
```

如果您只需要找到字符串中特定文本的位置，那么您可以使用`strpos()`函数，该函数返回您的选择从字符串开始的位置。一个有用的技巧是将这两个函数结合起来，尤其是当你不知道要从字符串中剪切的文本的起始位置时。您可以搜索一段特定的文本，然后提取它，而不是将起始位置指定为一个整数。

```
<?php
$str = "How to cut a string down to size";
$snip = substr($str, strpos($str, "string"), 6);
//Displays: string
echo $snip;
```

## 物物交换店

最后，让我们看看用其他东西替换字符串的一部分，为此您可以使用`str_replace()`函数。这对于您只想交换字符串中特定单词或一组字符的实例并用其他内容替换它们的情况非常理想:

```
<?php
$oldstr = "The cat was black";
$newstr = str_replace("black", "white", $oldstr);
// Displays: The cat was white
echo $newstr;
```

如果您想要替换多个值，您也可以向`str_replace()`提供数组:

```
<?php
$oldstr  = "The flag was red white and blue.";
$america = array("red", "white", "blue");
$germany = array("black", "red", "yellow");
$newstr = str_replace($america, $germany, $oldstr);
// Displays: The flag was black red and yellow.
echo $newstr;
```

`str_replace()`是区分大小写的，所以如果你不想担心这个问题，你可以使用它的不区分大小写的兄弟，`str_ireplace()`。

## 摘要

希望这篇文章已经让您体验了 PHP 中使用字符串可以做的一些事情，并让您渴望了解更多。我还真的勉强刮到了冰山一角！了解所有不同字符串函数的最佳途径是花些时间阅读 PHP 手册中的字符串函数页面。

我很想知道你最常使用的字符串函数，所以请在下面的评论中随意提及。

<small>图片经由[瓦西里斯](http://www.shutterstock.com/gallery-297484p1.html) / [快门架](http://www.shutterstock.com)</small>

## 分享这篇文章