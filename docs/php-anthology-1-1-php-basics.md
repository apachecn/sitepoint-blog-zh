# PHP 选集第 1 卷，第 1 章-PHP 基础知识

> 原文：<https://www.sitepoint.com/php-anthology-1-1-php-basics/>

PHP 是一种专门为构建网站而设计的编程语言，它非常容易学习和使用，这既是一种福气，也是一种诅咒。入门极其简单。不久之后，典型的初学者可以建立一个简单的网站，并通过像 [HotScripts](http://www.hotscripts.com) 这样的资源尝试大量的开源项目。

不幸的是，基于 PHP 的网站开发起来很容易，这也意味着你会很快陷入麻烦。随着网站流量的增加，以及对更多功能和更高复杂性的需求，对 PHP 有更深入的了解，并研究在大型网站上证明成功的应用程序设计和技术是很重要的。当然，你不能一头扎进编程，然后期望马上就知道所有的事情。即使你可以，那有什么乐趣呢？

本系列开始讨论 PHP 开发人员今天面临的关键问题。它由 5 条组成:

摘自第一卷:基础:

**[第一章:PHP 基础知识](https://www.sitepoint.com/blog/)**
这一章总结了你快速掌握 PHP 所需的所有基本知识，从如何使用手册，到理解 PHP 错误信息，以及如何包含工作。还有一些编写可移植代码的技巧，我们将看看一些主要的 PHP 配置陷阱。

**[第二章:面向对象的 PHP](https://www.sitepoint.com/blog/)**
第二章包括 PHP 的类语法的概要，以及解释面向对象范例的所有关键元素如何应用于 PHP 的初级读本。这是这本选集后面几章必不可少的预备读物。

**[第三章:PHP 与 MySQL](https://www.sitepoint.com/blog/)**
本章为你提供了 PHP 最喜欢的数据库 MySQL 的所有要领。我们从基础开始，涵盖重要的主题，如如何避免 SQL 注入袭击。然后，我们更深入地研究许多鲜为人知的主题，比如 MySQL 全文搜索工具，如何修复损坏的表和备份数据库，以及如何避免使用`PEAR::DB_DataObject`编写 SQL。这一章也是设计一个类来处理连接和查询 MySQL 数据库的“案例研究”。

摘自第 2 卷:应用

**[第 1 章:访问控制](https://www.sitepoint.com/blog/)**
从基本的 HTTP 认证开始，然后转移到应用程序级认证，本章着眼于控制对站点访问的方法。后来的解决方案着眼于实现一个用户注册系统，并创建一个具有用户、组和权限的细粒度访问控制系统。

**[第 5 章:缓存](https://www.sitepoint.com/article/php-anthology-2-5-caching)**
本章从“HTML 是最快的”这一基本观点出发，向您展示了如何利用客户端和服务器端的缓存来减少带宽使用并显著提高性能。

##### 这本书

这一系列的 5 个章节是从我的新书中摘录的。PHP 选集，第一卷和第二卷不仅包含了上面的 5 章，还包含了另外 12 章，这些章节探讨了从文本操作、日期和时间、错误处理到 XML、替代内容类型、统计和跟踪等一系列主题。

如果您更喜欢阅读 Adobe Acrobat PDF 版本的信息，您可以[下载这 5 个章节，以及示例参考资料，免费](https://www.sitepoint.com/books/phpant1/signoff.php )。

##### 您的反馈

如果您对本系列中的任何信息有疑问，快速回复的最佳机会是在 SitePoint.com 论坛上发布您的问题。特别欢迎您提出改进建议以及您可能发现的任何错误(可能有一两个错误)通知——将这些提交给 [SitePoint 图书支持](https://www.sitepoint.com/books/contact.php)。

你准备好进入 PHP 的奇妙世界了吗？我们走吧！

##### 第 1 章:PHP 基础知识

在第一章中，我将假设你已经有了 PHP 的基础知识，比如凯文·扬克的[使用 PHP&MySQL](https://www.sitepoint.com/books/phpmysql1/)(ISBN 0-9579218-1-0)构建你自己的数据库驱动的网站的前几章中所提供的知识，而是专注于 PHP 中的基本知识。

在这一章中，你会发现在哪里可以得到帮助——这是对那些大喊“读手册”的人的一种防御以及如何处理代码中的错误。我们还将讨论一些保持代码可移植性的通用技巧，并为您的 PHP 食谱提供其他必要的营养。并非这里的一切都符合“基本”的标题——对于更有经验的 PHP 开发人员来说，可能还会有一些惊喜，所以请保持警惕！

不过，要注意的是，关于 PHP 语法的讨论并不是最令人兴奋的话题——尽管这是为后面的章节做准备所必需的。如果你开始挣扎，记住《空手道小子》中的台词:在你能表演飞踢之前，你必须学会“上蜡，下蜡”。

##### 我在哪里可以得到帮助？

PHP 是使用最广泛的 Web 脚本语言，在全世界超过一千万个域上运行。对于一项缺乏任何公司资助的开源技术来说，它的流行似乎令人费解。然而 PHP 的成功并不神秘；它拥有所有技术中最活跃、最有帮助的在线社区之一。根据最近的估计，全球 PHP 开发人员的数量约为 500，000 人，考虑到这种媒介的性质，可以合理地假设很大一部分人活跃在网上。换句话说，对于基于 PHP 的网站的开发者来说，只需点击几下鼠标就能获得帮助。

***阅读手册***

有一个众所周知的四个字母缩写，RTFM(我不认为它需要在这里解释)，它往往被用来骚扰所有计算领域的初学者。虽然我可以理解老兵们可能不愿意无休止地重复同样的、有详细记录的说明，但我认为基本的假设应该是我们首先都知道如何阅读手册。

PHP 文档非常优秀，由志愿者维护，他们的唯一目的就是保持文档的最新、易懂和相关。[在线版本](http://www.php.net/manual/en/)非常容易导航，并包含来自全球开发者的注释形式的更多知识。手册是 PHP 真正出类拔萃的地方之一；像 Sun 和微软这样的软件公司要向在其平台上工作的开发者提供这种质量的材料还有很长的路要走。

该手册也有 24 种不同的语言版本，但当你在读这本书时，我想你会对该手册的英文版感到满意。它分为五个主要部分和附录。有必要知道可以找到什么样的信息，以及在哪里——至少在前四个部分，这四个部分与典型的 PHP 开发人员最相关。

***第一节:入门***

[http://www.php.net/getting-started](http://www.php.net/getting-started)

本节提供了 PHP 的简短介绍和一些基本示例。然后解释如何安装 PHP(描述各种操作系统-Web 服务器组合)，以及如何根据修改 php.ini 文件来配置它。

不可忽视的是关于安全性的部分，它涵盖了 PHP 开发人员经常犯的错误，这些错误使他们的应用程序容易被滥用。再说一次，PHP 易用性的“代价”是它不会总是保护您免于最严重的错误，所以在您的 PHP 生涯中尽早开始安全性是值得的。您可以在附录 C《安全检查表》以及本书的讨论中找到关键安全问题的摘要。

***第二节:语言参考***

http://www . PHP . net/langref

本节涵盖了 PHP 作为编程语言的基础知识。其中一些对你用 PHP 实现任何事情都是必不可少的，而其他的在你寻找改进技术的方法时会变得有用。一口气读完整本书很可能就像读一本字典。幸运的是，通过阅读大量在线教程，并研究开源 PHP 应用程序中使用的代码，可以吸收《语言参考》中包含的大部分信息。当然，当你读这本书的时候，我希望你能学到一些关于如何最大限度地利用 PHP 的知识。但是，有必要熟悉本手册这一部分中包含的主题，并牢记在心以备将来参考。

***第三节:特色***

[http://www.php.net/features](http://www.php.net/features)

这里涵盖的是 PHP 的核心元素，通常集中于解决特定的 Web 相关问题。特性部分的大部分内容读起来像是“执行摘要”,从开发人员的角度来看，当您看到它的实际应用时，可能会更好地理解这里包含的信息——例如，在我们将在本书中看到的例子中。

***第四节:功能参考***

[http://www.php.net/manual/en/funcref.php](http://www.php.net/manual/en/funcref.php)

这一部分构成了手册的真正主体，涵盖了 PHP 中可用功能的所有方面。这是您在学习 PHP 的过程中花费大部分时间的地方，所以您会很高兴听到 PHP 团队已经做出了一致的努力来使这一部分变得容易理解。在空闲的时候，翻翻手册，惊讶于你可以用 PHP 做的所有事情，这甚至很有趣。是的，我确实把阅读手册描述为“有趣”！

function reference 分为几个小节，涵盖了不同类别的函数，每个类别对应一个 PHP 扩展。

**PHP 扩展**

一开始，扩展的概念可能有点混乱，因为许多扩展是随标准 PHP 安装一起分发的。字符串函数就是一个很好的例子，如果没有它，我们将很难生存。一般来说，作为默认 PHP 安装的一部分，PHP 团队分发了他们认为对开发人员来说必不可少的所有扩展。

被视为“非必要”功能的扩展(例如，有些开发人员需要它们，但不是所有开发人员都需要)必须单独添加。重要信息显示在每个扩展主页上的“安装”标题下。核心扩展用句子“使用这些功能不需要安装；它们是 PHP 核心的一部分。”非标准扩展在附录 B“主机提供商清单”中进行了检查。

通过搜索字段(右上角)和在“函数列表”中搜索，可以访问函数参考中的信息。请注意，在功能列表中搜索仅检查手册的功能参考部分。要搜索整个手册，您需要在“在线文档”中进行搜索

另一种简便的方法是通过 URL 传递您感兴趣的主题名称来“捷径”到函数。例如，尝试在浏览器的地址栏中输入以下内容:[http://www.php.net/strings](http://www.php.net/strings)。这将把你带到 http://www.php.net/manual/en/ref.strings.php 的 T2，这是字符串扩展的主页。查看该页面，您将看到该扩展提供的所有可用功能的列表；左手边的菜单中提供了相同的列表。

以 strpos 函数为例，输入 URL[http://www.php.net/strpos](http://www.php.net/strpos)(它会将您带到[http://www.php.net/manual/en/function.strpos.php](http://www.php.net/manual/en/function.strpos.php))。您将看到以下关于`strpos`功能的信息:

(PHP 3，PHP 4)

```
strpos -- Find position of first occurrence of a string
```

描述

```
int strpos (string haystack, string needle [, int offset])
```

返回针在干草堆字符串中第一次出现的数字位置。与`strrpos()`不同，该功能可以将一个完整的字符串作为针参数，并且将使用整个字符串。

如果没有找到`needle`，则返回`FALSE`。

第一行包含函数名，第二行列出函数可用的 PHP 版本。第三行告诉你这个函数实际上做了什么。在这种情况下，这是一个相当简洁的解释，但 strpos 真的不是一个让您兴奋的主题。

在描述标题下面可能是最重要的一行——函数的签名。这描述了这个函数接受的参数和它返回的响应值。从左向右读，你有 int，它告诉你函数返回的值是一个整数(在这种情况下，是一段文本在另一段文本中的位置)。接下来是函数本身的名称，然后在圆括号中是这个函数的参数，用逗号分隔。

我们来看论点`string haystack`。这表示第一个参数应该是一个字符串值，而`haystack`只是简单地命名参数，以便在详细描述中引用。请注意，第三个参数放在方括号中，这意味着它是可选的(即，您不必提供该参数)。

下面是你如何使用`strpos`:

例 1.1。1.php

```
<?php   

$haystack = 'Hello World!';   

$needle   = 'orld';   

// Use the strpos() function   

$position = strpos($haystack, $needle);   

echo 'The substring "' . $needle . '" in "' .   

     $haystack . '" begins at character ' . $position;   

?>
```

请注意，我使用的`strpos`与它在手册中出现的方式相似。我使用了变量名`$haystack`和`$needle`来说明它们与手册中解释的关系，但是你可以使用任何你喜欢的变量名。

函数签名约定在整个手册中被一致地使用，所以一旦你习惯了它，你将能够快速掌握如何使用你以前没有尝试过的函数。

*出现问题时寻求帮助*

如果在 PHP 4.3.0 中使用内置函数时出错，PHP 的默认错误报告机制将显示一条错误消息，并带有一个链接，直接将您带到手册。

如果你有任何疑问，请务必通读其他 PHP 开发人员提交的评论，这些评论出现在手册每页的底部。通常，您至少会看到一个如何使用该函数的示例，这可能会解决您遇到的特殊困境。在许多情况下，你还会发现一个函数的其他解释和用法，这有助于拓宽你的理解。

***进一步帮助***

除了手册之外，还有成千上万的在线资源，你可以从中获得进一步的帮助。我敢说，你在 PHP 中遇到的 99%的常见问题已经在某个地方得到了解答，并且可以在网上找到。这意味着最明显(但有时被遗忘)的起点是谷歌，在那里快速搜索“PHP strpos 问题”会让你明白我的意思。

也有一些优秀的网站，你可以直接从其他 PHP 开发者那里得到答案(当然是免费的——这是 PHP 道德的一部分)。也许英语中最大的三个单词是:

*   SitePoint 论坛:[http://www.sitepointforums.com/](http://www.sitepointforums.com/)
*   开发棚论坛:[http://forums.devshed.com/](http://forums.devshed.com/)
*   PHP builder:[http://www.phpbuilder.com/board/](http://www.phpbuilder.com/board/)

每一个都使用 vBulletin 来主持在线讨论，因此，都有非常友好和易于使用的界面。所有都有非常活跃的会员，你应该会发现大多数问题在 24 小时内得到回答。

注意，在论坛上求助时，“助人自助”的原则很重要。不要发布一条消息说，“这个脚本有问题”，然后粘贴你的整个 PHP 脚本。缩小问题范围-确定您遇到问题的区域，并发布此代码片段以及其他相关信息，如错误消息、代码目的、您的操作系统等。主动提供帮助的人通常不希望在你的问题上花费超过几分钟的时间(毕竟他们是免费的)，所以节省他们的时间会增加你获得有用答案的机会。

不太方便，但可能是最有效的最后手段是 [PHP 邮件列表](http://www.php.net/mailing-lists.php)，这里鼓励初学者使用 PHP 通用列表。这些列表可供[有限浏览](http://news.php.net/)，不过也可以使用[PHP 网站](http://www.php.net/)的搜索栏搜索其中一些，并选择你所选择的列表。

开发 PHP 引擎核心的 Zend 公司也为一般的 PHP 问题举办了一个相当活跃的论坛。

如果你想得到答案，那就值得调查一下 [PHP 帮助台](http://www.phphelpdesk.com/)，这是由 [Tap Internet](http://www.tapinternet.com/) 运营的一项服务，他们与 Zend 合作提供 PHP 培训。

##### 如何修复 PHP 在我的脚本中发现的错误？

你已经完成了最新最棒的脚本的一半，突然一个测试执行产生了这个错误:

```
Parse error: parse error, unexpected T_ECHO, expecting ',' or ';'    

in c:htdocssitepointphpbasics2.php on line 5
```

这里有问题的代码如下:

例 1.2。2.php

```
<?php    

echo 'This is some code<br />';    

echo 'Somewhere in here I've got a ';    

echo 'parse error!<br />'    

echo 'But where is it?<br />';    

?>
```

您在这里处理的是一个语法错误，虽然您是 PHP 新手，但您可能会发现自己花了很多时间来寻找这样的问题。随着您对 PHP 越来越有经验，跟踪语法错误将变得更加容易。您甚至会知道自己的坏习惯，并可能在开始搜索之前就能猜出自己犯了什么错误(我自己的典型错误是在 PHP 字符串中构建 SQL 语句时忘记了最后一个引号，在构建数组时省略了逗号)。不过，熟悉 PHP 的错误消息是个好主意。

一般来说，在 PHP 应用程序中会遇到四种基本类型的错误:

***语法错误***

如上例所示，当您违反 PHP 的语法规则时，就会出现语法错误。语法错误通常会导致来自 PHP 的解析错误消息。

在上面的例子中，问题本身发生在第 4 行:

```
echo 'parse error!<br />'
```

我忘了在行尾加上分号(；)来标记每个语句的结束。PHP 解析器在遇到另一个 echo 语句时才注意到第五行的问题，因为指令可能合法地跨越多行。这一点值得注意，因为它有时会使错误难以发现——错误可能实际上发生在 PHP 注意到问题的那一行之前。

在大型 if-else 或 while 语句的情况下，语法错误会变得特别混乱，例如，您忘记了右括号。也许你有一个很长的清单，其中穿插着 HTML 代码块；寻找丢失的花括号可能极其困难。然而，随着您的编码技术的提高，您开始利用类，将您的代码分解成离散的代码块，在这些代码块中，代码很短并且易于阅读，您会发现定位语法错误要容易得多。

需要注意的另一件事是 PHP 对标记的使用。在上面的错误消息中，PHP 抱怨了一个“意外的 T_ECHO”T_ECHO 是一个标记，代表 PHP 脚本中的 ECHO 语句。PHP 解析器将您的代码分解成标记，以便它可以分析和处理脚本。您将在解析错误中看到的一些标记不太明显，所以如果您不确定，值得看一下关于标记的[手册。](http://www.php.net/tokens)

如果你使用的是 PHP 4.3.0，你会发现它包含了所谓的 [tokenizer 扩展](http://www.php.net/tokenizer)，它允许你像 PHP 解析器那样查看你的脚本。出于兴趣，下面是查看标记器输出的方式:

例 1.3。3.php

```
<?php    

/* Note: This script will only work with PHP 4.3.0 or later */    

// Read a PHP script as a string    

$script = file_get_contents('2.php');    

// Fetch the tokens into an array    

$tokens = token_get_all($script);    

// Display    

echo '<pre>';    

print_r($tokens);    

echo '</pre>';    

?>
```

***语义错误***

当您编写的代码遵守 PHP 的语法规则，但在执行时违反了 PHP 的“运行时规则”时，就会出现语义错误。例如，foreach 语句希望您给它一个数组:

例 1.4。4.php

```
<?php    

$variable = 'This is not an array';    

foreach ($variable as $key => $value) {    

  echo $key . ' : ' . $value;    

}    

?>
```

因为`$variable`不是一个数组，所以这个脚本产生以下错误消息:

```
Warning: Invalid argument supplied for foreach() in    

c:htdocssitepointphpbasics3.php on line 4
```

语义错误通常会导致这样的警告错误消息。

***环境错误***

当 PHP 脚本外部的系统引起问题时，就会出现环境错误。例如，当 PHP 脚本试图连接 MySQL 服务器时，它可能已经关闭。也许你指定了一个不正确的文件路径，所以 PHP 找不到这个文件。

当我们将一个在一个系统上编写的 PHP 脚本放在另一个环境不同的系统上执行时，也会出现这些错误。问题可能只是 Web 服务器的底层目录结构或域名不同。通常通过创建一个存储所有这些环境变量的中央配置脚本来处理这类问题。

PHP 在 php.ini 中还有许多设置，这些设置会导致脚本在设置不同的另一个系统上失败。我将在“如何编写可移植的 PHP 代码”一节中讨论这些问题；在附录 A，PHP 配置中也有总结信息。

***逻辑错误***

就 PHP 引擎而言，当应用程序运行良好，但代码做了一些与您预期不同的事情时，就会出现逻辑错误。例如，假设您有一个邮件脚本，您想用它向您的在线论坛的一些成员发送相同的消息。令你惊恐的是，你发现在执行脚本时，你已经给整个论坛的成员发了…二十次邮件！

这类问题最难发现；Windows XP 的用户将非常熟悉 Windows 更新——即使是大公司也在为逻辑错误而挣扎。

发现逻辑错误的关键是在一个独立于“活动”Web 服务器的安全环境中严格测试代码的能力。幸运的是，PHP 和相关技术，如 Apache 和 MySQL(如果您正在使用它们)是跨平台的，这使得即使底层操作系统不同，构建有效的开发环境也很容易。

你也应该研究单元测试，极限编程(XP)的一个方面，你可以在第 6 章，开发技术中找到介绍。我还建议在本章末尾进一步阅读。

在第 10 章，错误处理中，我将着眼于处理错误本身的策略，尤其是环境错误。特别是，我们将讨论如何为您的分析记录(或捕获)错误，而不会向您的应用程序用户显示难看的消息。

##### 如何将一个 PHP 脚本包含在另一个中？

发现编写千行脚本可能不是保持组织性的最佳方式后，您可能正在寻找将代码分成单独文件的方法。也许，在使用别人的开源应用程序时，您发现自己在努力消除类似下面这样的错误消息:

```
Fatal error: Failed opening required 'script.php'
```

***相互包容***

PHP 提供了四个命令，允许你将一个 PHP 脚本的内容添加到另一个脚本中，即 include、require、`include_once`和`require_once`。在每种情况下，PHP 获取命令中指定的文件，然后执行其内容。include 和 require 之间的区别在于，当它们找不到被告知要获取的脚本时，它们的行为方式。

include 将生成如下 PHP 警告消息:

```
Warning: Failed opening 'script.php' for inclusion
```

这将允许调用 include 命令的脚本继续执行。

相比之下，require 会导致如上所示的致命错误，这意味着调用脚本将会终止，从而使一切都停止。如果所需的文件对您的应用程序至关重要，终止脚本是一件非常好的事情。

`include_once`和`require_once`命令的行为类似于它们各自的表亲，但是如果该脚本已经被包含或被其他任何地方需要(被四个命令中的任何一个)，该语句将被忽略。乍一看，如何使用这些命令可能并不明显；您肯定知道使用了多少次 include 命令，对吗？在更复杂的应用程序中，`_once`命令变得非常方便，在这些应用程序中，PHP 脚本包含其他 PHP 脚本，而其他 PHP 脚本又包含更多的 PHP 脚本。当你使用类库(我们将在第 2 章，面向对象的 PHP 中探讨)时，这一点尤其重要，这些类被许多脚本重复使用。一个类可能依赖于另一个类的可用性；使用一个`require_once`来包含所需的类可以确保它总是可用的，但是如果这个类碰巧已经在其他地方被使用了，也不会导致任何问题。

要查看所有这些，让我们制作一个名为 include_me.php 的脚本:

例 1.5。include_me.php

```
<?php     

// include_me.php     

echo 'I've been included!<br />';     

?>
```

每次包含这个脚本时，它都会显示消息“我被包含了！”所以我们知道它起作用了。

现在，让我们测试将该文件包含在另一个脚本中的各种方法:

例 1.6。5.php

```
<?php     

// This works fine     

echo '<br />Requiring Once: ';     

require_once 'include_me.php';     

// This works fine as well     

echo '<br />Including: ';     

include 'include_me.php';     

// Nothing happens as file is already included     

echo '<br />Including Once: ';     

include_once 'include_me.php';     

// This is fine     

echo '<br />Requiring: ';     

require 'include_me.php';     

// Again nothing happens - the file is included     

echo '<br />Requiring Once again: ';     

require_once 'include_me.php';     

// Produces a warning message as the file doesn't exist     

echo '<br />Include the wrong file: ';     

include 'include_wrong.php';     

// Produces a fatal error and script execution halts     

echo '<br />Requiring the wrong file: ';     

require 'include_wrong.php';     

// This will never be executed as we have a fatal error     

echo '<br />Including again: ';     

include 'include_me.php';     

?>
```

下面是它生成的输出(注意，我在最后简化了错误消息):

```
Requiring Once: I've been included!     

Including: I've been included!     

Including Once:     

Requiring: I've been included!     

Requiring Once again:     

Include the wrong file:     

Warning: Failed opening 'include_wrong.php' for inclusion     

Requiring the wrong file:Fatal error: Failed opening required     

'include_wrong.php'
```

注意这里第一次使用`include_once`什么都不做(文件已经被包含了)，后面使用`require_once`也一样。稍后，当我试图包含错误的文件(在本例中，一个不存在的文件)时，我会收到一条警告消息。然而，执行继续到下一行，我试图要求一个不存在的文件。这一次，PHP 产生了一个致命错误，脚本停止执行，这意味着包含文件的最终尝试将永远不会发生。

请注意，您包含的文件不一定只包含 PHP。包含的文件可以只包含 HTML 而不包含任何 PHP。

使用哪个命令？

一般来说，除非你有特殊情况，需要一些其他行为，总是使用`require_once`命令将一个文件包含在另一个文件中。当您将 PHP 类放在单独的文件中，并且一个类可能依赖于另一个类时，这一点尤其重要。关于类的完整故事，请看第 2 章，面向对象的 PHP。

PHP 的四个 include 命令不应该与各种文件相关的函数混淆(在第 4 章，文件中讨论)；这些是为了获取文件，而不是立即将它们解析为 PHP 脚本，从而允许您处理它们的内容。

请注意，在本书中，我将讨论“包含”一个文件，即使我正在使用 require 命令。这是谈论 PHP 的一个常见约定，它源于第一批 PHP 先驱使用的较老的编程语言。

***寻路***

到目前为止，我只研究了在包含 include 命令的脚本所在的目录中包含文件。实际上，您通常希望根据文件的工作将它们组织到子目录中。这可能会造成很多混乱，尤其是当您使用第三方代码时，因为在其他目录中有许多处理 includes 的替代方法。

首先要注意的是，所有包含都是相对于主脚本(开始执行的地方)所在的目录计算的。例如，假设我们在以下位置有三个文件:

```
/home/username/www/index.php     

/home/username/www/includes/script.php     

/home/username/www/another.php
```

首先，让我们考虑一下 index.php。命令 include ' includes/script . PHP '；将正确包括 script.php，假设 index.php 是实际要求的文件。

但是如果我们在 script.php 中使用下面的命令会怎么样呢:

```
include '../another.php'; // ???
```

如果 script.php 是我们正在查看的页面，该命令将正确包含 another.php。但是，如果 index.php 是我们正在查看的页面，并且它包括 script.php，此命令将失败，因为 another.php 的位置是相对于 index.php 的位置计算的，而不是相对于 script.php。

我们有两个选择。我们可以修改 script.php，使其包含 another.php，如下所示:

```
include 'another.php';
```

或者，我们可以输入 another.php 的完整路径，如下所示:

```
include '/home/username/www/another.php';
```

这使得 another.php 位于何处变得毫无疑问。

PHP 配置文件 php.ini 也包含指令`include_path`。这允许您指定可以包含文件的目录，而不需要在使用 include 命令时指定它们的位置。

这种方法需要谨慎使用，因为如果一个包含的同名文件存在于多个目录中，它可能会导致奇怪的结果，但它可能是解决包含相关问题的有效方法。例如，PHP 的 [PEAR](http://pear.php.net/) 类库依赖于将包含 PEAR 的包含文件的目录添加到包含路径中。还要注意，在包含路径中指定太多的位置并不是一个好主意，因为这将降低 PHP 在试图查找代码中包含的脚本时的速度。

如果您在共享托管环境中使用 Apache，您可能能够使用. htaccess 文件覆盖`include_path`的值。将文件放在您希望它应用到的目录中(它也将应用到所有子目录)，该文件应该包含如下内容:

```
php_value include_path ".:/usr/local/lib/php:/home/user/phplib/"
```

同样也可以用 PHP 函数`ini_set`来完成，例如:

```
ini_set('include_path', 'C:/phplib/');
```

这允许在运行时从 PHP 脚本中进行更改。

你可以在附录 A，php 配置中找到 php.ini 值的参考。

##### 如何编写可移植的 PHP 代码？

并非所有的 PHP 安装都是一样的。根据 php.ini 中的版本和配置设置，您的脚本可能会也可能不会在安装了 php 的另一台服务器上正确运行。但是，您可以采用一些通用的良好实践来简化工作，并最大限度地减少为其他服务器重写代码的需要。

***保留所有配置中央***

对于大多数 PHP 应用程序，有必要提供描述脚本运行环境的信息，包括数据库用户名和密码、目录位置等等。一般来说，尽量将这些信息的大部分保存在一个地方——甚至可以是一个文件——这样，当需要修改信息时，您可以在一个地方完成所有操作。也就是说，在构建模块化应用程序时，您可能希望将特定“模块”的本地配置元素与模块本身存储在一起，而不是集中存储。

具体选择如何存储这些信息是个人的选择。在某些情况下，可能值得考虑 XML 文件或将一些信息存储在数据库中。同样值得注意的是`parse_ini_file`函数，我将在第 4 章“文件”中探讨。

一种简单但有效的机制是将所有设置作为 PHP 常量放在一个文件中，这使得它们可以从应用程序中的任何函数或类中获得。例如:

例 1.7。6.php

```
<?php      

// Configuration settings      

define('DOMAIN', 'sitepoint.com');      

// In another script      

echo 'The domain is ' . DOMAIN;      

?>
```

不过，常量需要谨慎使用。为了使你的函数和类在其他应用程序中可重用，它们不应该依赖于固定名称的常量；相反，它们应该接受配置信息作为参数。在这种情况下，最好在中央配置文件中使用 PHP 变量，然后可以根据需要将其传递给函数和类。如果你看一下第 3 章，PHP 和 MySQL，当连接到 MySQL 时，我们可以确定一些我们需要在一个中心位置的变量:服务器主机名、用户名、密码和所选数据库的名称。

使用我们在前面的解决方案中看到的`require_once`命令，我们可以创建一个名为 config.php 的文件，并将其放在公共 Web 目录之外。这有助于确保没有人意外地浏览到包含这些关键信息的文件，这将使网站的安全性处于危险之中。

***使用完整的`<?php ?>`标签***

PHP 支持多种标记样式来标记 PHP 代码的各个部分，包括短标记(`<? ?>`)和 ASP 样式的标记(`<% %>`)。这些都是通过设置`short_open_tag`和`asp_tags`从 php.ini 中控制的。当您将这些设置设为打开时，其他人可能不会。例如，当 PHP 与 XML 文档混合使用时，短标记样式会导致问题，XML 文档使用如下处理指令:

```
<?xml version="1.0"?>
```

如果我们有一个包含 PHP 和 XML 的文档，并且我们打开了`short_open_tag`，PHP 将会把 XML 处理指令`<?xml`误认为是 PHP 开始标记。

您的代码可能需要在`short_open_tags`和`asp_tags`都关闭的环境中运行。确保它们是正确的最好方法是养成总是使用`<?php ?>`标签风格的习惯，否则在未来的黑暗日子里可能会有大量的代码重写工作要做。

***注册 _ 全局关闭***

PHP 能够将输入的数据转换成本地 PHP 变量。这个特性是由 php.ini 中的`register_globals`设置控制的。当`register_globals`打开时，如果我将浏览器指向一个像 http://www.mysite.com/index.php?logged_in=1,这样的地址，php 将自动创建一个变量`$logged_in`，并赋予它`1`的值。PHP 团队现在建议禁用该设置，因为它会给安全性带来风险，正如前面的例子所示。

因此，在 php.ini 中，确保下面的代码是正确的:

```
register_globals = Off
```

这将迫使你通过特殊的预定义超全局变量(例如`$_GET['username']`)访问输入数据，这意味着它们不会与你在脚本中创建的变量冲突。

在 Apache 中使用. htaccess 文件，使用下面的代码可以获得相同的结果:

```
php_flag register_globals off
```

更多信息可以在 PHP 手册和凯文·杨克的文章[中找到【用 PHP 4.2 编写安全脚本！](https://www.sitepoint.com/blog/)在 SitePoint。

***魔法语录***

Magic quotes 是一个旨在帮助防止 PHP 初学者开发的网站出现安全漏洞的特性。

在您的脚本能够访问任何这些值之前，它会自动向传入的 URL 查询字符串、表单帖子和 cookie 数据添加转义字符(参见第 5 章，文本操作了解更多信息)。如果您将数据直接插入数据库，只要打开 magic quotes 功能，就不会有任何人篡改数据库的风险。

对于初学者来说，这当然是一个预防灾难的有用方法。然而，一旦您理解了什么是 SQL 注入攻击，并且养成了在代码中处理它们的习惯，那么神奇的引用功能可能会变得比它的价值更成问题。

Magic quotes 功能由 PHP 配置设置`magic_quotes_gpc`控制，可以打开也可以关闭。

我自己的偏好是总是关闭神奇的引号，自己处理 SQL 语句的转义数据。不幸的是，这意味着我写的代码不能很好地移植到打开了 magic quotes 的 PHP 安装中(我将在我的内容中以反斜杠结束)。值得庆幸的是，为了处理这个问题，PHP 提供了函数`get_magic_quotes_gpc`，可以用它来发现魔术引号是否打开。为了保持本书中的代码的可移植性，我们将使用一个简单的文件来去掉神奇的引用，如果该功能被启用的话:

例 1.8。MagicQuotes/strip_quotes.php(在 SPLIB 中)

```
<?php       

/**       

 * Checks for magic_quotes_gpc = On and strips them from incoming       

 * requests if necessary       

 */       

if (get_magic_quotes_gpc()) {       

  $_GET    = array_map('stripslashes', $_GET);       

  $_POST   = array_map('stripslashes', $_POST);       

  $_COOKIE = array_map('stripslashes', $_COOKIE);       

}       

?>
```

如果我们在接受来自查询字符串、表单帖子或 cookie 的数据的任何文件的开头包含这一点，我们将删除任何由神奇引号添加的斜线，如果该功能打开的话。这有效地让我们回到了开始时的状态。

SQL 注入攻击的主题在“如何解决由引号/撇号引起的数据库错误”一节中有详细讨论。如果您还不确定是否能够保护自己免受 SQL 注入攻击，请使用神奇的引号。一旦你很高兴你已经完全掌握了所有的问题，关闭神奇的报价功能，并节省自己许多头痛。请注意，只有使用 php.ini 文件或 Apache 的文件才能打开或关闭魔术引号。htaccess 文件。有关更多信息，请参见附录 A，PHP 配置。

 *引用就像是变量值的“捷径”。当我们使用 PHP 函数和类时，经常需要引用，这个主题我们将在第 2 章面向对象 PHP 中进一步讨论。当您在调用函数或类方法时使用对变量的引用时，它被定义为调用时按引用传递。考虑以下示例:

```
$result = myFunction(&$myVariable);
```

这里的`&`操作符告诉 PHP 使用对变量$myVariable 的引用作为参数，而不是创建其值的副本。这现在被普遍认为是不好的做法，因为它会使理解别人的代码变得非常困难。

使用下面的命令在 php.ini 中关闭它:

```
allow_call_time_pass_reference = Off
```

或者，在. htaccess 文件中关闭它，如下所示:

```
php_flag allow_call_time_pass_reference off
```

***编写可重用代码***

我知道说起来容易，但是如果你发现自己写了不止一个 PHP 脚本，你需要开始考虑如何使你的代码可重用，在你过早脱发之前。从技术上来说，这并不完全是一个可移植性的问题，但是如果您最终从事其他网站或应用程序的工作，您将会很高兴有现成的代码可以简单地插入到您的新项目中。此外，如果您正在编写代码，其他人会将它与他们网站上现有的应用程序集成，您需要以一种不对他们已经在使用的代码提出要求的形式来打包它。

例如，如果您的应用程序有某种用户身份验证系统，它会与他们已经在使用的系统集成吗——一个已经有与之相关的大型用户数据库的系统？

最好的方法是编写面向对象的代码(第 2 章，面向对象 PHP 的重点)，并考虑创建可重用的“组件”有些人认为用 PHP 编写面向对象的代码会降低应用程序的性能，因此应该不惜一切代价避免。他们忘记提到的是面向对象编程带来的性能的大幅提升。毕竟快程序员比快微处理器贵！

在衡量代码重用的潜力时，要考虑的一些事情是:

*   当需求改变时会发生什么？
*   向代码中添加新特性有多容易？
*   时间长了，你还能理解代码吗？
*   你的代码能容易地与其他应用程序集成吗？
*   你代码中的假设会应用到你在其他网站的工作中吗？

尽管对 PHP 应用程序整体设计的深入分析超出了本书的范围，但你会发现本书提供了许多鼓励你编写可重用代码的提示和建议。当你读这本书时，你应该对作为进一步研究主题的一些关键因素有所感觉。作为一名有经验的 PHP 开发人员，您对自己有一个主要的责任:不断扩展您对软件开发的更深奥方面的一般知识，例如设计模式和企业应用程序架构，作为改进您的开发技术的一种手段，更重要的是，节省您自己的时间。你的知识面越广，当你着手一个大项目时，失败的风险就越低。

##### 进一步阅读

*   [Write Secure Scripts with PHP 4.2!](https://www.sitepoint.com/article/
    )
    A tutorial that explains the importance of writing scripts with register_globals switched off.
*   [毫不费力(或者更好！)用 PHP 断言检测 Bug](https://www.sitepoint.com/blog/)
*   [Using Strings](http://www.zend.com/zend/tut/using-strings.php)
    Zend provides a walk-through of the main functions available for working with strings.
*   弦理论
    DevShed 提供了对字符串的深入研究，远至 Posix 扩展正则表达式。

在接下来的几周里，请在 SitePoint 上关注 PHP 选集的更多章节！如果你等不及了，[下载样本章节](https://www.sitepoint.com/books/phpant1/signoff.php)，或者[现在就订购你自己的副本](https://www.sitepoint.com/blog/)！* 

## *分享这篇文章*