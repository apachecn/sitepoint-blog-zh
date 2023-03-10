# 使用 PHP 正则表达式

> 原文：<https://www.sitepoint.com/regular-expressions/>

```
^[A-Za-z0-9-_.+%]+@[A-Za-z0-9-.]+.[A-Za-z]{2,4}$
```

它让你明白了古埃及象形文字的所有含义，尽管那些小图片至少看起来像是有意义的。但是这…这看起来像胡言乱语。这是什么意思？意思是*oleomarg32@hotmail.com*，*火热。Rebel@veneuser.info* 、*robust lamp+self mag @ Gmail . ca*、
或几乎任何其他简单的电子邮件地址，因为这是用描述如何匹配字符串中的文本的语言编写的模式。当您希望超越纯文本匹配时，比如在“Mustard”中查找“stud”(顺便说一下，这可能会失败)，并且您需要一种方法来“解释”您正在查找的内容，因为每个实例可能都不同，您需要正则表达式，亲切地称为 [regex](https://www.sitepoint.com/learn-regex/) 。

## 正则表达式符号介绍

为了让你的脚湿起来，让我们拿上面的例子来一点一点的分解。

`**^**[A-Za-z0-9-_.+%]+@[A-Za-z0-9-.]+.[A-Za-z]{2,4}$`
行首可以像回车符一样被检测到，尽管它并不是真正的隐形字符。使用`^`告诉正则表达式引擎匹配必须在行首开始。

我们没有详细说明字母表中的每一个字符，而是用速记法给出了一个范围。通常它是区分大小写的，所以你必须指定大写和小写的范围。

`^**[**A-Za-z**0-9**-_.+%**]**+@[A-Za-z0-9-.]+.[A-Za-z]{2,4}$`
数字也是如此；我们可以简单地将它们缩短成一个范围，而不是写下所有的 10 位数字。

这些是我们允许的特殊字符:破折号、下划线、点号、加号和百分号。

我们范围周围的括号有效地将您放在它们之间的所有内容创建成您自己的自定义通配符。我们的“通配符”能够匹配任何大写或小写字母 A-Z、数字 0-9 或我们的特殊标点符号之一。

`^[A-Za-z0-9-_.+%]**+**@[A-Za-z0-9-.]+.[A-Za-z]{2,4}$`
这是一个量词；它修改了前一个字符应该匹配的次数，在这种情况下，前一个字符是方括号内的集合。`+`的意思是“至少一个”，所以在我们的例子中，在匹配了字符串的开头之后，我们必须在括号中包含至少一个字符。

在这一点上，我们可以匹配(给定之前的示例电子邮件地址) *oleomarg23* ， *Fiery。叛逆*，还有*强韧+自拍*。类似于 *@SodaCanDrive.com* 的东西会失败，因为我们必须在文本的最开始至少有一个括号中的字符。

除了`+`作为量词，还有`*`除了完全不匹配也会匹配之外，几乎完全相同。如果我们用`*`替换样本中的第一个`+`量词，那么:

```
^[A-Za-z0-9-_.+%]*@[A-Za-z0-9-.]+.[A-Za-z]{2,4}
```

它会成功地匹配字符串 *@SodaCanDrive.com* ，因为我们有效地告诉 regex 引擎保持匹配，直到它遇到一个不在集合中的字符，即使没有任何字符。

回到我们最初的模式…

`^[A-Za-z0-9-_.+%]+**@**[A-Za-z0-9-.]+.[A-Za-z]{2,4}$`
`@`字面上匹配，所以我们现在我们已经匹配了 *oleomarg23@* ， *Fiery。Rebel@* 和 *robustlamp+selfmag@* 。文本*greencandelabra.com*失败，因为它没有 at 符号！

表达式的这一部分类似于我们在 at 符号前匹配的部分，只是这次我们不允许使用下划线、加号或百分号。现在我们到了 oleomarg23@hotmail.com*，*火热进行中。Rebel@veneuser.info* 和*robust lamp+self mag @ Gmail . ca*。*gnarly 3.1415 @ pie _ a _ la _ mode . com*最多只能匹配 *gnarly3.1415@pie* 。*

这里我们有一个转义点，以便与字面意思匹配。请注意，加号在括号内时字面上匹配，但在括号外作为量词有特殊的含义。在括号外，点必须转义，否则将被视为通配符；在括号内，点表示点。

啊哦！因为我们已经匹配了*。com* ，*。信息*和*。看起来这场比赛会失败，因为我们没有更多的点了。但是 regex 很聪明:匹配引擎尝试回溯来找到匹配。所以现在我们回到 *oleomarg23@hotmail。*，*火热。Rebel@veneuser。*和 *robustlamp+selfmag@gmail。*。*

此时，*gnargly 3.1415 @ pie _ a _ la _ mode . com*失败，因为目前匹配的后面的字符不是点。*drnddog @ chew wed . legs . onhair*延续为 *drnddog@chewwed.legs.* 。

还记得我们如何使用括号自定义通配符吗？我们可以用大括号做类似的事情来定制量词。`{2,4}`表示至少匹配两次，但不超过四次。如果我们只想精确匹配两次，我们会写`{2}`。我们可以用`{0,4}`处理任何数量，最多四个。`{2,}`最少匹配两个。

`{2,4}`是我们的特殊量词，它将最后一个通配符匹配限制为任何 2、3 或 4 个字母或点。我们几乎完全匹配*oleomarg23@hotmail.com*，*火热。Rebel@venuser.info* 和*robust lamp+self mag @ Gmail . ca*。*drnddog @ chew wed . legs . onhair*必须进一步后退到 *drnddog@chewwed.legs* 才能进行匹配。

我们只差一个了…

`^[A-Za-z0-9-_.+%]+@[A-Za-z0-9-.]+.[A-Za-z]{2,4}**$**`
`$`是`^`的反义词。就像`^`为行的开始所做的一样，`$`将匹配锚定到行的结束。我们的例子现在都匹配了，而*drnddog @ chew wed . legs . onhair*失败了，因为在字符串的末尾没有 2、3 或 4 个字母前面有一个点。

## PHP 中的正则表达式

对正则表达式使用的符号有这个基本的理解当然很好，但是我们仍然需要知道如何在 PHP 的上下文中应用它来实际做一些有成效的事情，所以让我们看看函数`preg_match()`、`preg_replace()`和`preg_match_all()`。

### preg_match()

要验证电子邮件地址的表单字段，我们可以使用`preg_match()`:

```
<?php
if (preg_match('/^[A-Za-z0-9-_.+%]+@[A-Za-z0-9-.]+.[A-Za-z]{2,4}$/',
    $_POST["emailAddy"])) {
    echo "Email address accepted";
}
else {
    echo "Email address is all broke.";
}
```

如果找到匹配项，`preg_match()`返回 1，否则返回 0。请注意，我们在正则表达式的开头和结尾添加了斜杠。它们被用作分隔符来显示正则表达式开始和结束的函数。你可能会问，“但是杰森，这不就是引号的作用吗？”让我向你保证还有更多，我很快会解释。

### preg_replace()

要查找电子邮件地址并添加格式，我们将使用`preg_replace()`:

```
<?php
$formattedBlock = preg_replace(
    '/([A-Za-z0-9-_.+%]+@[A-Za-z0-9-.]+.[A-Za-z]{2,4})/U',
    "<b>\1</b>", $blockOText);
```

下面是承诺的解释:我们在结束分隔符后放置了一个`U`作为修改正则表达式匹配方式的标志。我们已经看到正则表达式匹配是如何贪婪的，尽可能地吞噬尽可能多的字符，只有在必要时才进行回溯。u 使正则表达式“不贪婪”没有它，字符串*tweedle@dee.com-and-tweedle@dum.com*将匹配为一。但是通过让它变得不贪婪，我们告诉它去寻找最短的匹配模式……只是*tweedle@dee.com*。

你注意到我们也把整个表达式用括号括起来了吗？这使得 regex 引擎捕获与括号之间的表达式匹配的文本副本，我们可以用反向引用(`1`)来引用该文本。`preg_replace()`的第二个参数告诉函数用一个开始的粗体标记替换文本，即匹配第一组括号和结束的粗体标记之间的模式的内容。如果有其他括号，可以参考`2`、`3`等。取决于他们的位置。

### preg_match_all()

要扫描一些文本并提取其中所有电子邮件地址的数组，`preg_match_all()`是我们的最佳选择:

```
<?php
$matchesFound = preg_match_all(
    '/([a-z0-9-_.+%]+@[a-z0-9-.]+.[a-z]{2,4})/Ui',
    $articleWithEmailAddys, $listOfEmails);
if ($matchesFound) {
    foreach ($listOfEmails[0] as $foundEmail) {
        echo $foundEmail . "<br>";
    }
}
```

返回找到了多少个匹配，并将这些匹配粘贴到我们作为第三个参数提供的变量引用中。它实际上创建了一个多维数组，在这个数组中，我们要寻找的匹配在索引 0 处找到。

除了`U`修饰符，我们还提供了`i`，它指示 regex 引擎我们希望模式以不区分大小写的方式应用。也就是说，`/a/i`将匹配小写字母 A 和大写字母 A(或者`/A/i`也同样适用，因为修饰符要求引擎不区分大小写)。这允许我们现在在表达式中写类似于`[a-z0-9]`的东西，而不是`[A-Za-z0-9]`，这使得它更短更容易理解。

好了，事情差不多结束了。虽然使用正则表达式可以做更多的事情，包括前视、后视和更复杂的反向引用示例，所有这些都可以在 PHP 的在线文档中找到，但希望您有足够的东西可以使用，为本文中的许多脚本服务。

图片 via[Boris MRD ja](http://www.shutterstock.com/gallery-538708p1.html "Stock Photos | Shutterstock: Royalty-Free Subscription Stock Photography & Vector Art")/[Shutterstock](http://www.shutterstock.com "Stock Photos and Royalty-Free Images by Subscription")

## 分享这篇文章