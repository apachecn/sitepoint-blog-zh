# 正则表达式的乐趣[2]

> 原文：<https://www.sitepoint.com/the-joy-of-regular-expressions-2/>

所以继续乐趣开始于[这里](https://www.sitepoint.com/the-joy-of-regular-expressions-1/) …

## 内容

**第二部分**

*   [到目前为止我们去过哪里…](#Where_weve_been_so_far)
*   [狩猎。jp(e)gs](#Hunting_for_jpegs)
    *   [转义元字符](#Escaping_meta_characters)
*   [搜索并替换](#Search_and_Replace)
    *   [preg_quote()](#preg_quote)
    *   [preg_replace()](#preg_replace)
    *   [单词边界、单词字符……以及其他一切](#word_metacharacters)
    *   [子模式](#subpatterns)
    *   [发现 XSS 洞](#xss)
    *   [eval()是恶！](#eval_is_evil)
    *   [孕 _ 替换 _ 回调()](#preg_replace_callback)

[第三部分在这里](https://www.sitepoint.com/the-joy-of-regular-expressions-3/)

## 到目前为止我们去过的地方…

首先快速总结一下我们在第一部分中所涉及的内容；

*   表达式分隔符，例如`/yes/`或`%yes%`
*   模式修饰符语法，例如`/yes/i`
*   元字符…
    *   开始和结束断言:`^`和`$`例如`/^yes$/`
    *   适用于模式中位于字符之前的*的长度量词:*
        *   “一个或多个”量词:`+`
        *   “最小/最大”量化花括号:例如`{5,20}`
    *   引入字符类别，例如`[a-zA-Z0-9]`

您还遇到了 [preg_match()](http://www.php.net/preg_match) 和 [preg_match_all()](http://www.php.net/preg_match_all) 函数。

举例来说，是时候学习更多的语法了…

## 寻找。jp(英)gs

一些应用程序用文件扩展名`.jpeg`保存[JPEG](http://en.wikipedia.org/wiki/JPEG)，而其他人都使用`.jpg`。现在，如果我有一个目录，我知道它包含一些 JPEGs 图片，这些图片的*可以用任何一个文件扩展名来命名，我如何识别它们呢？我如何同时过滤掉目录中的所有其他文件类型？*

```
 <?php
$dh = opendir('/home/harryf/gallery');

while ( ($file = readdir($dh)) !== FALSE ) {

    if ( preg_match('/^.*.jpe?g$/i', $file ) ) {
        print "$filen";
    }

}

closedir($dh); 

```

放大这个模式—`/^.*.jpe?g$/i`我得到了什么？好的，`^`和`$`元字符你以前见过，知道它们匹配行的开始和结束。你知道的/i 模式修饰符也意味着“不区分大小写”——文件名可以是大写或小写。我这里还有什么？

`?`是另一个元字符:另一个长度量词，类似于`+`和您已经看到的花括号。它的意思是“零或*正好是前面字符的一个*”。所以这部分的例子:`jpe?g`的意思是；

> 我正在寻找一个字符序列，以字母“j”开始，然后“p”，然后*可选*字母“e”，最后字母“g”

但是这并不是我在这个模式中引入的唯一长度量词。开始时，我还有一个量词`*`:

```
/^.**<u>*</u>**.jpe?g$/i

```

`*`量词表示“零个或多个前面的字符”——没有最大限制，也没有最小限制。

好的——但是什么是被*应用于*的量词呢——模式中的前一个字符是句点:`.`,也就是*和*,是一个元字符，但是更像你在第 1 部分中看到的字符类。它的意思是“任何字符”——它将匹配*任何东西*(有一个例外，我稍后会谈到)。所以，结合“零或更多”量词`*`的模式的开始是说…

> 我不关心文件名的开头是什么——任何长度的文件都是允许的(我只对文件的扩展名感兴趣)

这让我只需要解释一下`/^.***<u>.</u>**jpe?g$/i`中间的`.`是什么意思…

## 转义元字符

它指的是*文字*文件名分隔符，例如“我的图片<u>。</u> jpg "。因为句号*通常*是正则表达式中的元字符，但是因为我需要它来匹配文件名分隔符，所以我必须在它前面加一个反斜杠*来转义*它。在模式中放置一个反斜杠告诉 regex 引擎*而不是*将跟随字符的*视为元字符。*

还有 [preg_quote()](http://www.php.net/preg_quote) 函数，用于转义用户输入之类的东西，嵌入到一个模式中——稍后会详细介绍。

关于哪些字符需要转义还有一些细节。一个例子:*在*一个正则表达式*字符类*中，没有必要(尽管如果你这样做也无妨)对每个元字符进行转义:一些元字符，比如'+'和' * '，会自动假定它们的字面意思。与此同时，除了正常的元字符集之外，其他字符也需要进行转义*，如果它们打算具有字面意义的话，比如通常会在字符类中指定一个*范围*的“-”。当你开始记忆语法时，何时何地需要转义字符将变得显而易见——现在不要太担心。*

你应该知道，当涉及到过度转义时，生活会变得很有趣，因为 PHP 的字符串*和*也使用反斜杠来转义某些字符，例如；

```
 print 'Tuesday's Child'; # Just a normal string 

```

如果你使用双引号，会更有趣。在一个理想的世界里，我们应该拥有像 Perl 和 Javascript 一样的 PHP 特性*字面正则表达式*。但无论如何…大多数时候这不会打扰你，只有当它来的时候，它可能会让你发疯。

## 搜索和替换

目前为止我们只匹配了。一些替换怎么样？

添加到站点的一个相当受欢迎的特性，尽管自 AJAX 以来有点“不流行”，对于被搜索引擎推荐到您的站点的访问者来说是一个“亮点”。您可以通过查看 HTTP referrer 来识别他们使用的搜索词，并使用 span 标签之类的东西在 HTML 中突出显示相应的单词。

事实上，PHP 这样做可能不是最聪明的想法——使用 Javascript 要好得多，而且节省了一些服务器 CPU 周期，但是它确实是一个很好的例子来说明正则表达式搜索和替换，而且它突出了一些潜在的安全隐患。

这是一个幼稚的实现。我不会试图复制 HTTP referrer，而是保持它的简单，使用一个 URL 查询，它将被放在变量`$_GET['q']` …

**重要提示:**——这个例子是*没有*安全(故意)——把那些手指拿开`CTRL+C`！

```
 <?php
$text = 'The quick brown fox jumps over the lazy dog';

# Do we have a search term?
if ( isset($_GET['q']) ) {
    # Escape the input - make sure it won't contain
    # any regex meta-characters
    $q = preg_quote($_GET['q'], '/');

    # Replace and instances of the search term with the
    # same but nested in a span tag...
    $text = preg_replace(
            "/b($q)b/i",                    # Pattern
            '<span class="hilite">$1</span>', # Replacement
            $text                             # Subject
        );

}
?>
<html><head><title>Hilite</title>
<style type="text/css">.hilite { background-color: yellow }</style>
</head>
<body>
<?php print $text; ?>
</body>
</html> 

```

好的——让我先解释一下代码在做什么，然后继续解释为什么它不安全。放大有趣的部分…

```
 # Escape the input - make sure it won't contain
    # any regex meta-characters
    $q = preg_quote($_GET['q'], '/');

    # Replace and instances of the search term with the
    # same but nested in a span tag...
    $text = preg_replace(
            "/b($q)b/i",                    # Pattern
            '<span class="hilite">$1</span>', # Replacement
            $text                             # Subject
        ); 

```

## preg_quote()

我在这里做的第一件事是引用传入的查询参数，以便如果它包含任何看起来像 regex 元字符或任何其他 regex 语法的内容，它将被反斜杠转义(如果您插入一个 print 语句来举例说明`$q`，您将能够知道发生了什么)。

现在`preg_quote()`在以下任何字符前加一个反斜杠…

```
.  + * ? [ ^ ] $ ( ) { } = !  | :

```

这基本上解决了任何可能被误认为是正则表达式语法的问题…除了表达式分隔符。这就是`preg_quote()`的第二个参数在这里做的…

```
$q = preg_quote($_GET['q'], <u>'/'</u>);

```

第二个参数告诉`preg_quote()`您正在使用哪个表达式分隔符，因此也对其进行了转义。

一剂恐惧和厌恶的药:如果你*未能*逃脱用户输入，然后将其嵌入到正则表达式中，你就打开了[命令注入](http://www.owasp.org/index.php/Command_Injection)的大门——你的用户将能够告诉你的正则表达式引擎该做什么。在最好的情况下，这只会导致错误消息(你希望[对此保持沉默](http://www.php.net/manual/en/ref.errorfunc.php#ini.display-errors))，而最糟糕的情况可能会变得非常糟糕，这取决于你在做什么——别忘了。

## preg_replace()

那么这个脚本的下一部分在做什么呢？

```
 $text = preg_replace(
            "/b($q)b/i",
            '<span class="hilite">$1</span>',
            $text
        ); 

```

它使用 [preg_replace()](http://www.php.net/preg_replace) 函数用一个 span 标签包装输入搜索词的所有匹配项。你可能对 [str_replace()](http://www.php.net/str_replace) 很满意，对吗？Well `preg_replace()`本质上是一样的，但是它不仅仅是简单的字符串替换，而是包含了正则表达式的优点。

现在这个模式需要一些解释…

```
"/b($q)b/i"

```

/i 模式修饰符在你认识的末尾，意思是“不区分大小写”——这允许我突出显示更多的输入搜索词的“命中”。

## 单词边界、单词字符…以及其他一切

出现两次的`b`呢？这是一个元字符，意思是“断言单词边界”。这有点像你以前见过的`^`和`$`元字符，但是当它们断言一行的开始和结束时，`b`元字符断言“单词的边缘”,例如，有空白、标点等的点。在单词字符序列的旁边。[下面是](http://www.php.net/manual/en/reference.pcre.pattern.syntax.php)PHP 手册如何定义单词边界…

> 单词边界是主题字符串中当前字符和前一字符不都匹配 W 或 W(即一个匹配 W，另一个匹配 W)的位置，或者如果第一个或最后一个字符分别匹配 W，则是字符串的开头或结尾。

…所有的一切？手册用另外两个*元字符*来定义单词边界，我们还没有看到:`w`—“单词字符”和`W`。不要惊慌，这里没有什么新东西。这两个都是您以前见过的 regex *字符类*的有效简写，让您不必定义自己的字符类。这里是`w`的手工定义；

> “单词”字符是任何字母、数字或下划线字符

…推而广之，`W`是所有其他的东西——所有不是单词字符的东西(比如标点、换行符和空格字符)。

细节超载！:现在`w`其实是*而不是*与角色类`[a-zA-Z0-9_]`必然相同——手动定义的部分我省略了；

> 字母和数字的定义由 PCRE 的字符表控制，如果进行特定于语言环境的匹配，可能会有所不同。例如，在“fr”(法语)区域设置中，一些大于 128 的字符代码用于重音字母，这些字符代码由 w 匹配。

…换句话说，`w`可能会让那些厚颜无耻的外国人偷偷将非 ASCII 字符通过您的验证模式！这里有一个很长的故事，我将跳过，但你可能会得到进一步的洞察力提示[这里](http://www.phpwact.org/php/i18n/utf-8#utf-8_dangerous_php_functionality)——参见关于地区的说明，并注意这也适用于`b`。

我们说到哪了？试图弄清楚一个`b`“单词边界”意味着什么:基本上是任何字母或数字序列的开始或结束。换句话说，它是帮助识别单词的有用工具。所以让我们再来看看完整的模式…

## 子模式

```
"/b($q)b/i"

```

给定两个单词的边界，我们似乎在匹配单词。但是这中间是什么:`($q)`？嗯，`$q`只是一个普通的 PHP 变量——用户搜索词——由于使用了[双引号](http://www.php.net/manual/en/language.types.string.php#language.types.string.parsing)，它成为了模式的一部分。那么括号在它周围做什么呢？你猜对了，它们也是正则表达式元字符:一个*子模式* <sup>[2](#note_2)</sup> 两边的分隔符。

子模式是对模式的*部分*进行分组的一种方式。它们有许多用途(更多的我将在以后的某个时间探讨)，但是对于我们的搜索和替换操作来说，它们是一种将模式的一部分标记为“特别有趣”的方式——我们不希望用户提供的搜索词两边有空白——我们只想捕获单词本身，并替换它。要了解这是如何工作的，你需要看看`preg_replace()`的下一个参数；

```
 $text = preg_replace(
            "/b($q)b/i",
            '<span class="hilite">$1</span>',
            $text
        ); 

```

…`'<span class="hilite">$1</span>'`。这就是我们想要用替换搜索词*的原因。你可以看到 span 标签在那里，期待地等待着，但是匹配的单词是如何嵌入其中的呢？通过`$1`–a*反向参考*到子模式。您必须阅读文档[以获得替换反向引用语法的完整描述，但简而言之，它们是指向与您的模式匹配的内容的“指针”。反向引用`$0`将始终可用，并对应于*完全*匹配。如果您使用子模式，将会存在类似`$1`的进一步反向引用(子模式越多，可用的编号反向引用就越多)。](http://www.php.net/preg_replace)*

综上所述，如果我搜索“狐狸”这个词，`preg_replace()`将会在这里的`$text`中找到它:`brown <u>fox</u> jumped`并将其替换为`brown <span class="hilite">fox</span> jumped`。

## 发现 XSS 洞

不过，有一个小问题，您可能已经发现了。虽然我明智地使用了`preg_quote()`来转义任何看起来像 regex 语法的输入，但是如果搜索项包含 HTML / Javascript 呢？现在是*潜在的*向[跨站点脚本](http://en.wikipedia.org/wiki/XSS)开放。我需要首先通过 [htmlspecialchars()](http://www.php.net/htmlspecialchars) 运行子模式匹配，而不仅仅是替换。但是怎么做呢？

稍微浏览一下[模式修饰符](http://www.php.net/manual/en/reference.pcre.pattern.modifiers.php)，你会发现/e 修饰符；

> 如果设置了这个修饰符，preg_replace()会对替换字符串中的反向引用进行常规替换，将其作为 PHP 代码进行计算，并使用结果替换搜索字符串。在替换的反向引用中，单引号和双引号由反斜杠转义。

好了，问题解决了…

**警告:**这是*也是*潜在的不安全和更严重的后果！

```
 $text = preg_replace(
            "/b($q)b/ie",
            '"<span class="hilite">".htmlspecialchars("$1")."</span>"',
            $text
        ); 

```

## eval()是邪恶的！

将/e 修饰符附加到模式后，替换字符串不再是字符串，而是 PHP 代码，适合于 [eval()](http://www.php.net/eval) 。当你听到“邪恶”这个词的时候，它应该就在你的嘴边。这里有一个[可能发生的故事](http://www.hardened-php.net/advisory_042006.119.html)。

简单的规则是**不要使用/e 修饰符**。除了安全隐患之外，替换代码变成了必须被解析、解释的东西*和*在*上执行的每一次替换*(如果你想要真正的恐惧和厌恶，就说“糟糕的性能”)。嘿——为 PHP 的 eval 编写代码是一个用引号引起来的思维扭曲练习。

那么还有什么选择呢？

## 孕 _ 替换 _ 回调()

不使用`preg_replace()`，而使用`preg_replace_callback()`，避免评估并提供明显更好的性能。与其给它提供一串代码，不如给它一个要执行的函数的名称。每次它需要进行替换时，将使用匹配调用该函数。这是最终的解决方案。

```
 function highlight_search($matches) {
        return sprintf(
                '<span class="hilite">%s</span>',
                htmlspecialchars($matches[1])
            );
    }

    $text = preg_replace_callback(
            "/b($q)b/i",
            'highlight_search',
            $text
        ); 

```

`preg_replace_callback()`的第二个参数是我的`highlight_search()` [回调](http://en.wikipedia.org/wiki/Callback_(computer_science))函数的名称，每次有匹配被替换时都会被调用。

回调函数需要处理单个参数:`$matches`，这个参数永远是一个索引数组。与替换反向引用`preg_replace()`提供的方式非常相似，数组中的第一个元素将一直存在并对应于完全匹配——即`$matches[0]`。子模式在数组中被分配了更高的索引——在这种情况下，我的子模式在`$matches[1]`处，所以我需要做的就是通过 [htmlspecialchars()](http://www.php.net/htmlspecialchars) 运行它，在 [sprintf()](http://www.php.net/sprintf) 的帮助下将它嵌入 span 标签中，返回它，我就完成了。返回值被用作替换值。

请注意，从可用性的角度来看，我使用的将输入的搜索词直接放入模式(转义后)的方法可能不够理想。需要对搜索词中的标点符号之类的东西进行一些思考，将搜索词分成几个部分并先对其进行一点分析可能更聪明。所以这个例子是一个次优的解决方案，但是我认为它很好地说明了正则表达式搜索和替换。

好了——本期节目到此结束。更多的时间(我需要暂时脱离正则表达式)。

<sup>1</sup> 它实际上意味着*任何*长度——不包括任何内容—`.jpg`都将是使用该正则表达式的有效文件名——使用`+`量词并要求至少一个字符可能更聪明，但我需要一个借口来说明`*`。

<sup>2</sup> 事实上，我不需要这个例子中的子模式，因为`b`是一个*断言*——它实际上并不成为匹配的一部分，但是这是一个很好的机会来说明子模式，子模式在搜索和替换操作中经常使用。

## 分享这篇文章