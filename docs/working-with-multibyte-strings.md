# 使用多字节字符串

> 原文：<https://www.sitepoint.com/working-with-multibyte-strings/>

一种书面语言，无论是英语、日语还是其他语言，都由许多字符组成，因此，当以数字方式处理一种语言时，一个基本问题是找到一种以数字方式表示每个字符的方法。过去，我们只需要表示英文字符，但现在情况完全不同了，结果是出现了令人眼花缭乱的字符编码方案，用来表示许多不同语言的字符。PHP 如何联系和处理这些不同的方案？

## 基础知识

我们都知道，一个“位”可以是 0 或 1，也可以不是别的，而一个“字节”是一组八个连续的位。由于一个字节中有八个这样的双值点，因此一个字节可以配置成总共 256 种不同的模式(2 的 8 次方)。可以将不同的字符与每个可能的 8 位模式相关联。

把这些字节以不同的顺序放在一起，你就有了一些交流。不一定是智能的，那要看两端是谁，但它是交流。只要我们能够用 256 个或更少的独特字符来表达一种语言的字符，我们就万事大吉了。

但是如果我们不能用仅仅 256 个字符来表达一种语言呢？或者如果我们需要在同一个文档中表达多种语言呢？今天，当我们数字化我们能找到的所有东西时，256 个字符远远不够。幸运的是，更能应对挑战的角色设计已经被设计出来。这些新的超级字符集使用一到四个字节来定义字符。

当今字符编码领域的大狗是 Unicode，这是一种使用多个字节来表示字符的方案。它是由 Unicode Consortium 开发的，有几个版本:UTF-32 用于星际飞船的无畏级，UTF-16 用于星际迷航:进入黑暗企业，UTF-8 是我们在现实世界中大多数人应该用于我们的 web 应用程序。

正如我所说的，Unicode(包括 UTF-8)使用多字节配置来表示字符。UTF-8 使用 1 到 4 个字节来产生 1，112，064 个模式来代表不同的字符。这些“宽字符”占用更多的空间，但 UTF-8 确实比其他一些编码方案处理起来更快。

为什么每个人都对 UTF 8 号欢呼雀跃？部分原因是在 ESPN 和 TCM 上看到的支持 UTF-8 的商业广告中突出的热门型号，但主要是因为 UTF-8 模仿 ASCII，如果没有任何特殊字符，它会准确地跟踪 ASCII。

## 这对 PHP 有什么影响？

我知道你在想什么。我只需将我的元标签中的字符集设置为“UTF-8 ”,一切都会好的。但事实并非如此。

首先，一个简单的事实是，PHP 并不是真正为处理多字节字符而设计的，所以使用标准字符串函数处理这些字符可能会产生不确定的结果。当我们需要处理这些多字节字符时，我们需要使用一组特殊的函数:mbstring 函数。

第二，即使你控制住了 PHP，仍然会有问题。覆盖您的通信的 HTTP 头也包含一个字符集标识，它将覆盖您的页面的 meta 标签中的内容。

那么，PHP 如何处理多字节字符呢？有两个函数组会影响多字节字符串。

首先是 [iconv](http://us2.php.net/manual/en/ref.iconv.php "PHP: iconv Functions - Manual") 。在 5.0 中，这已经成为语言的默认部分，一种将一种字符集转换成另一种字符集表示的方法。这不是我们这篇文章要讲的内容。

第二个是多字节支持，一系列以“mb_”为前缀的命令。有许多这样的命令，快速回顾一下就会发现，其中一些命令与根据给定的编码方案确定字符是否合适有关，其他命令是面向搜索的函数，类似于 PHP 正则表达式的一部分，但是面向多字节函数。

## 打开 PHP 的多字节支持

多字节支持不是 PHP 的默认特性，但它也不要求我们下载任何额外的库或扩展；它只是需要一些重新配置。不幸的是，如果您使用的是 PHP 的托管版本，这可能不是您能做到的。

使用`phpinfo()`功能查看您的配置。向下滚动到输出的一半，会有一个标记为“mbstring”的部分。这将显示基本功能是否已启用。关于如何启用此功能的信息，您可以[参考手册](http://www.php.net/manual/en/mbstring.installation.php "PHP: Installation - Manual")。简而言之，通过使用`--enable-mbstring`编译时选项来启用 mb 功能，并设置运行时配置选项`mbstring-encoding_translation`。

当然，最终的解决方案是 PHP 6，因为它将使用 IBM(请大家去掉球帽)ICU 库来确保对多字节字符集的本机支持。我们要做的就是坐以待毙，好吗，巴迪·罗？但在此之前，请查看现在可用的多字节支持。

## 多字节字符串命令

可能有 53 种不同的多字节字符串命令。也有可能有 54 个。我有点记不清了，但你明白了。不用说，我们不会一一介绍，只是为了好玩，让我们快速浏览一下。

### mb _ 检查 _ 编码

`mb_check_encoding()`函数检查以确定特定编码序列对于编码方案是否有效。该函数不会告诉您字符串的编码方式(或者它将适用于什么方案)，但是它会告诉您它是否适用于指定的方案。

```
<?php
$string = 'u4F60u597Du4E16u754C';
$string = json_decode('"' . $string . '"');
$valid = mb_check_encoding($string, 'UTF-8');
echo ($valid) ? 'valid' : 'invalid';
```

您可以在 PHP 手册中找到受支持编码的[列表。](http://php.net/manual/en/mbstring.supported-encodings.php "PHP: Supported Character Encodings")

### mb _ 波束

`strlen()`函数返回一个字符串中的字节数。对于每个字符都是一个字节的 ASCII 码，这可以很好地找到字符的数量。对于多字节字符串，您需要使用`mb_strlen()`函数。

```
<?php
$string = 'u4F60u597Du4E16u754C';
$string = json_decode('"' . $string . '"');

echo strlen($string); // outputs 12 – wrong!
echo mb_strlen($string, 'UTF-8'); // outputs 4
```

### mb_ereg_search

`mb_ereg_search()`函数执行传统字符搜索的多字节版本。但是有一些注意事项——您需要使用`mb_regex_encoding()`函数指定编码方案，正则表达式没有分隔符(它只是模式部分),正则表达式和字符串都是使用`mb_ereg_search_init()`指定的。

```
<?php
// specify the encoding scheme
mb_regex_encoding('UTF-8');

// specify haystack and search
$string = 'u4F60u597Du4E16u754C';
$string = json_decode('"' . $string . '"');

$pattern = 'u754C';
$pattern = json_decode('"' . $pattern . '"');

mb_ereg_search_init($string, $pattern);

// finally we can perform the search 
$result = mb_ereg_search();
echo ($result) ? "found" : "not found";
```

## 够了吗？

我不知道你怎么想，但我认为这个世界真的需要更简单的东西。不幸的是，多字节处理无法满足这一需求。但是现在这是你不能忽视的事情。有时候，您将无法执行正常的 PHP 字符串处理(因为您正试图对超出正常 ASCII 范围(U+0000–U+00FF)的字符进行处理)。这意味着你必须使用面向 mb 的函数。

想了解更多？说真的，你知道？我真的以为这会把你吓跑。我对此没有准备。我的时间到了。你的最佳选择？查看 PHP 手册。哦，试试别的东西。使用某物的实际经验是无法替代的。

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章