# 正则表达式的乐趣[4]

> 原文：<https://www.sitepoint.com/the-joy-of-regular-expressions-4/>

找到了更多的快乐，是时候中断你周五晚上的观看了，从我们上次[停止的地方继续这个传奇。](https://www.sitepoint.com/the-joy-of-regular-expressions-3/)

## 内容

*   那是约会吗？
    *   [d 元字符](#decimal_metachar)
    *   [更多子模式](#more_subpatterns)
*   [用户更友好的日期](#user_friendlier_dates)
    *   [PCRE 扩展模式修改器](#pcre_extended)
    *   [非捕获子模式](#non_capturing_subpatterns)
    *   [分支](#branching)
    *   [十六进制文字](#hex_literals)
*   [图案爆炸](#preg_split)
    *   [空白元字符](#whitespace_metachar)
*   [捕获分割分隔符](#capturing_split_delimiters)

## 那是约会吗？

您已经在这里第一次体验了子模式[，它们被用来捕获一个单词，并通过](https://www.sitepoint.com/the-joy-of-regular-expressions-2/#subpatterns) [preg_replace_callback()](http://www.php.net/preg_replace_callback) 将其包装在 HTML span 标签中。是时候进一步探索子模式了…

你有一个包含日期/时间戳的字符串，如“20061028134534”，即年(4 位数)、月(2 位数)、日(2 位数)、小时(2 位数，24 小时制)、分钟和秒(都是 2 位数)。你需要把它分解成它的组成部分，这样你就可以用它们来进行计算。

现在，您可以多次调用 [substr()](http://www.php.net/substr) ，但是另一个解决方案是正则表达式，例如；

```
 <php
$date = '20061028134534'; # The input date string

preg_match(
            '/^(d{4})(d{2})(d{2})(d{2})(d{2})(d{2})$/',
            $date,
            $matches
          );

print_r($matches); 

```

仔细观察图案；

```
/^(d{4})(d{2})(d{2})(d{2})(d{2})(d{2})$/

```

在模式的开始和结束是`^`和`$`断言，你已经[见过](https://www.sitepoint.com/the-joy-of-regular-expressions-1/#Exact_Match)，所以这里没有问题。

**d 元字符**

`d`是另一个元字符类，它匹配“任何十进制数字”。它类似于你在这里看到的的`w`元字符，但用于数字而不是单词字符。事实上，它是编写你自己的角色类的简写，比如`[0-9]`(你之前已经看过[这里](https://www.sitepoint.com/the-joy-of-regular-expressions-1/#Validating_a_username))

附加到每一次出现的`d`的是一个[长度量词](https://www.sitepoint.com/the-joy-of-regular-expressions-1/#Quantifying_Length)，例如`d{4}`表示*正好是*四位数(用于匹配年份–2006)或`d{2}`–*正好是*两位数。

**更多子模式**

到目前为止一切顺利，但是这里所有的[子模式](https://www.sitepoint.com/the-joy-of-regular-expressions-2/#subpatterns)的作用是什么？它们告诉 PCRE 引擎，我希望*捕获*每个子匹配，然后 PHP 将通过第三个参数`preg_match()`使其可用——在本例中是`$matches`变量——这是一个通过引用填充*的数组——稍后将详细介绍。*

上面的代码输出以下内容(`$matches`变量的内容)；

```
Array
(
    [0] => 20061028134534
    [1] => 2006
    [2] => 10
    [3] => 28
    [4] => 13
    [5] => 45
    [6] => 34
)

```

数组的第一个(`[0]`)元素是*完全匹配*，在本例中，它恰好与完整的输入字符串相同。同时，索引为`[1]`到`[6]`的元素是从年到秒的日期组成部分——它们被子模式捕获。

您已经[看到了](https://www.sitepoint.com/the-joy-of-regular-expressions-1/#preg_match_returns)*preg _ match()*返回匹配的次数(可能是 0 或 1)，但是它的第三个参数是一个数组，作为返回实际匹配值的媒介。不同于通常的从函数中得到结果的方式，比如:

```
 $result = myfunc(); 

```

…你给`preg_replace()`一个变量名作为函数*的参数*，它会为你填充值——就像；

```
 $result = somefunc($more_results_get_put_here_by_reference); 

```

该数组的第一个元素将总是包含整个模式的完全匹配(假设有一个)，而其他数组索引对应于子模式捕获的值，当从*左到右*读取整个模式时，元素的顺序由子模式的*左括号*’(’的相对位置决定；请注意，即使子模式中嵌套了子模式，也是如此。

现在我们已经将日期/时间戳转换成了一个有用的数组，我们可以用它来执行计算，并验证它的格式(当然不是实际值——2006 年 2 月 31 日将会过去！).

还有另一种(或许)更优雅的方法来处理这种格式的日期，稍后在查看`preg_split()`时，您将在中看到[。](#capturing_split_delimiters)

## 用户更友好的日期

上面的时间戳对于日志文件和类似的文件来说很方便，但是不容易阅读。我们倾向于以类似`28th Oct 2006`的格式向最终用户提供日期。那么，用一个正则表达式来验证格式(而不是值)怎么样呢？)并提取其中有趣的部分？

```
 <?php
$date = '28th Oct 2006';

preg_match(
                '/^

                 (d{1,2})  # Match the day of the month

                 (?:st|nd|rd|th) # Match English ordinal suffix

                 x20            # Match space character

                 # Match the month....
                 (Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)

                 x20            # Match another space character

                 (d{4})         # Match the year

                $/x',
                $date,
                $matches
                );

print_r($matches); 

```

**PCRE 扩展模式修改器**

第一件可能让你吃惊的事情——正则表达式充满了空白和注释——那是因为我在这里使用了我在[之前提到的`/x`模式修饰符](https://www.sitepoint.com/the-joy-of-regular-expressions-1/#Tactics)。从[手动](http://www.php.net/manual/en/reference.pcre.pattern.modifiers.php) …

> 如果设置了这个修饰符，*模式中的空白数据字符将被完全忽略*，除非被转义或在字符类内部，字符类外部的未转义#和下一个换行符之间的*字符也将被忽略*。这相当于 Perl 的/x 修饰符，使得在复杂的模式中包含注释成为可能。但是，请注意，这仅适用于数据字符。空白字符不能出现在模式中的特殊字符序列中，例如在序列(？(它引入了一个条件子模式。

…所以它允许我插入一些注释来帮助解释正则表达式在做什么。也就是说，它也有助于在一行中看到完整的表达式…

```
/^(d{1,2})(?:st|nd|rd|th)x20(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)x20(d{4})$/

```

所以，一点一点地检查这个图案…

开始时，我们有通常的“主题开始”断言`^`，后面跟着子模式`(d{1,2})`；这与一个月中某一天的数字相匹配——它可能是一个月的第一天(只有一位数字),或者在 28 号的情况下，它是两位数字，因此有了长度量词`{1,2}`。

**非捕获子模式**

表达式的下一部分立刻引入了两个新特性:`(?:st|nd|rd|th)`。乍一看，这也可能看起来像一个子模式，但更仔细地看:`(?:`将其含义改为“非捕获”。如果有帮助的话，你可以把它看作是一种“自定义断言”。就像你看到的[这里](https://www.sitepoint.com/the-joy-of-regular-expressions-1/#Exact_Match)的`^`和`$`断言以及`b`这里[讨论的字边界断言](https://www.sitepoint.com/the-joy-of-regular-expressions-2/#word_metacharacters)一样，它断言一个必须满足但不成为被捕获子模式的条件。

换句话说，`(?: )`形式的子模式意味着“这是一个非捕获子模式；无论匹配什么，都应该在结果中返回*而不是*。

那么为什么*不*我要捕捉我的`(?:st|nd|rd|th)`子模式呢？它旨在将英语序数后缀与数字匹配，例如 1 *st* ，2 *nd* ，3 *rd* 或 4 *th* 。我认为这应该包含在有效的日期格式中，但是我对这个值本身并不感兴趣，所以不需要捕获它。

关于内存和处理开销，非捕获子模式也值得注意，正如 Andrei 在这个 [Regex 诊所](http://www.gravitonic.com/do_download.php?download_file=talks/phpworks2004/regex_clinic_phpworks_2004.pdf) (pdf，第 99/100 页)中提到的那样 PCRE 引擎不需要为它们的内容分配内存。在这个例子中，影响将是微不足道的，但是对于较大的文档/更复杂的模式，性能和内存开销可能变得很关键。

**分支**

现在我们已经覆盖了非捕获子模式，另一个新出现的`(?:st|nd|rd|th)`是“竖线”字符`|`。这是“分支操作符”,允许您指定可能匹配的替代模式。

它有点像 PHP 中的“or”操作符——它允许你设置可选的条件，其中一个可以被满足。我在这里做的是断言一个月中的数字日后面必须跟有字符串“st”、“nd”、“rd”或“th”中的任何一个*一个*——这涵盖了一个月中任何一天的英语序数后缀。

当你使用一个分支操作符时，它的意思要么“局部地”存在于它所嵌入的括号中(就像我的序数后缀例子一样)，要么它可以用来在整个模式中放置一个分支。例如，考虑下面的模式；

```
 preg_match('#some [b]bold[/b] text|some [i]italic[/i] text#',$text, $m);

```

注意:我使用#作为[表达式分隔符](https://www.sitepoint.com/the-joy-of-regular-expressions-1/#Expression_Delimiters_and_Pattern_Modifiers)，因为模式本身包含正斜杠。我还必须对`[`和`]`字符进行转义，否则它们会被认为是一个字符类(见[这里](https://www.sitepoint.com/the-joy-of-regular-expressions-1/#Validating_a_username))。这可能是与匹配 [BBCode](http://en.wikipedia.org/wiki/Bbcode) 相关的模式。它可以匹配*或者*；

```
some [b]bold[/b] text

```

或者

```
some [i]italic[/i] text

```

…感谢模式中间的分支操作符。

**十六进制文字**

模式中的下一个是:`x20`——用字符的[十六进制代码](http://en.wikipedia.org/wiki/Hexadecimal)来表示字符。如果您跳到您的 [ASCII 表](http://www.lookuptables.com/)，您会看到十六进制代码为 20 的字符不是别的，正是空格字符。啊？当我可以使用一个真实的字符时，为什么要使用十六进制代码？如果你还记得上面的，我正在使用`/x`模式修饰符，它指示正则表达式引擎忽略空白，这样我们就可以得到一个格式良好的正则表达式。但是我希望“2006 年 10 月 28 日”中的空格成为模式的一部分，所以我需要十六进制表示来告诉 PCRE 引擎它应该匹配的空格字符。

下次你会看到更多由十六进制代码指定的字符。你也可以使用三位数的[八进制的](http://en.wikipedia.org/wiki/Octal)代码作为字符，但是一定要仔细阅读[手册中适用于它们的特殊情况的](http://www.php.net/manual/en/reference.pcre.pattern.syntax.php#regexp.reference.backslash)。注意[双引号字符串](http://www.php.net/manual/en/language.types.string.php#language.types.string.syntax.double)——PHP 对`x20`的意思也有自己的看法…

所以这个模式终于开始有意义了…这个部分`(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)`是另一个*捕获*子模式，再次包含分支操作符，允许我为月份指定三个字母的替代字符串，而模式的结尾捕获四位数的年份，如您之前所见。最后，给定输入“2006 年 10 月 28 日”，这个 PHP 脚本的输出如下所示:

```
Array
(
    [0] => 28th Oct 2006
    [1] => 28
    [2] => Oct
    [3] => 2006
)

```

同样，数组的零元素是完全匹配的，而接下来的三个元素分别给出了日、月和年——我现在只需要用一个数字替换“Oct ”,就可以开始计算了。

## 支持多种日期格式

将子模式和分支结合起来可以产生强大的表达式。那么，扩展前面的例子以接受另一种日期格式，例如 2006-10-28 (yyyy-mm-dd)怎么样？

匹配该格式本身需要一个表达式，如:

```
/^(d{4})-(d{1,2})-(d{1,2})$/

```

…没什么新鲜的。

但是我们怎么把它和之前的模式结合起来呢？事实上这并不难——我们只需要将每个模式嵌套在另一个*子模式中，然后在两者之间放置一个分支(注意下面的评论)；*

```
 <?php
function match_date ($date) {
    if ( preg_match(
                    '/^
     ( # First date format...

         (d{1,2})
         (?:st|nd|rd|th)
         x20
         (Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)
         x20
         (d{4})

     )

     | # branch

     ( # Second date format...

         (d{4})-(d{1,2})-(d{1,2})

     )
    $/x',
                    $date,
                    $matches
                    )
         )
        {
            return $matches;
        }
    return FALSE;
}

print_r(match_date('28th Oct 2006'));
print_r(match_date('2006-10-28')); 

```

这里真正发生的是我将两个日期格式模式进行了日期标注，并将它们嵌入到另一个形式为`/^( )|( )$/`的模式中。

但是有一个小问题——正如您还记得的第一个模式，当给定一个类似“2006 年 10 月 28 日”的日期时，以“10 月”的形式返回月份，而我的第二个模式，给定“2006-10-28”作为输入，以“10”的形式返回月份。我需要能够准确地确定匹配的日期格式，以便在需要时采取正确的步骤将月份转换为整数。

事实上这很容易做到，因为分配给每个子模式的索引`preg_match()`是固定的。通过检查输出可以看到这一点:`print_r(match_date('28th Oct 2006'));`产生；

```
Array
(
    [0] => 28th Oct 2006
    [1] => 28th Oct 2006
    [2] => 28
    [3] => Oct
    [4] => 2006
)

```

元素`[0]`通常是整个模式的完全匹配。同时，元素`[1]`是第一个主要子模式匹配的内容，包含第一个日期格式模式。元素`[2]`–`[4]`是日期的组成部分。

现在比较一下，当我把另一种日期格式`print_r(match_date('2006-10-28'));`输入模式时得到的输出；

```
Array
(
    [0] => 2006-10-28
    [1] => 
    [2] => 
    [3] => 
    [4] => 
    [5] => 2006-10-28
    [6] => 2006
    [7] => 10
    [8] => 28
)

```

现在，对应于第一个日期格式的元素`[1]`到`[4]`只是空值。第二个日期格式的匹配从元素`[5]`开始，它是第二个主要子模式的匹配，随后是元素`[6]`到`[8]`，它们也是日期的组成部分。

因此，通过检查返回数组中匹配的位置，我可以确定匹配了哪个子模式，并适当地处理结果。在这种情况下，我可以获取数组的零元素，并搜索数组的其余部分，以找到匹配的子模式的索引——类似于；

```
 printf(
       "Matched subpattern %d",
       (array_search($m[0],array_slice($m,1))/4)
       )."n"; 

```

我将在下一次使用 PCRE 进行(简单的)解析时回到这个想法。

## 图案爆炸

现在你已经习惯了`preg_match()`，是时候介绍另一个 PCRE 函数了——[preg _ split()](http://www.php.net/preg_split)。从概念上讲，它与 [explode()](http://www.php.net/explode) 函数做的事情是一样的，但是除了使用简单的字符串分隔符来分割字符串之外，还可以使用正则表达式来匹配分隔符。

例如，能够将一些包含 HTML
标签的文本拆分成行怎么样？可能在某些情况下你在和
打交道，而在其他情况下是和
(注意正斜杠)；

```
 <?php
$comment = "This is a comment<br>with mixed breaks<br/>in it";
print_r(preg_split('#<br/?>#',$comment)); 

```

我使用#作为替代的[模式分隔符](https://www.sitepoint.com/the-joy-of-regular-expressions-1/#Expression_Delimiters_and_Pattern_Modifiers)，因为模式本身包含正斜杠:`#<br/?>#`。

同时，模式中斜杠后面的`?`量词(在[这里](https://www.sitepoint.com/the-joy-of-regular-expressions-2/#Hunting_for_jpegs)之前你已经见过了)表示零或一，允许我匹配< br >和< br/ >。输出如下所示:

```
Array
(
    [0] => This is a comment
    [1] => with mixed breaks
    [2] => in it
)

```

如何应用同样的方法将文档按其包含的段落进行分割？如果输入如下所示:

```
<p>
    Paragraph one.
</p>

<p>
    Paragraph two.
</p>

<p>
    Paragraph three.
</p>

```

作为第一次尝试，让我们对它进行如下尝试(输入在`$doc`变量中)；

```
 print_r(preg_split('#</?p>#', $doc)); 

```

该模式非常类似于用于
标签的模式，除了正斜杠移动了位置，这允许我匹配开始和结束段落标签。这是输出结果。

```
Array
(
    [0] => 
    [1] => 
    Paragraph one.

    [2] => 

    [3] => 
    Paragraph two.

    [4] => 

    [5] => 
    Paragraph three.

    [6] => 
)

```

hmmm——这里有很多空白，所以我将更新模式，使开始或结束段落标记两边的空白成为分割分隔符的一部分；

```
 print_r(preg_split('#s*</?p>s*#', $doc)); 

```

**空白元字符**

记住`*`是零或更多的量词，你已经在这里看到过。

那么`s`是做什么的？它是另一个字符类元字符，匹配任何空白字符，比如空格或新行。

下面是现在的输出结果:

```
Array
(
    [0] => 
    [1] => Paragraph one.
    [2] => 
    [3] => Paragraph two.
    [4] => 
    [5] => Paragraph three.
    [6] => 
)

```

越来越好，但是空数组元素在那里做什么呢？它们是结束标签与下一个开始标签(如`</p><p>`)背靠背的结果。中间没有任何东西，但是由于它们是两个独立的分隔符，`preg_match()`为它们之间的“void”创建一个空值。

如果我们能去掉它们就好了，我们可以使用`PREG_SPLIT_NO_EMPTY`常量标志，它作为第四个参数*传递给`preg_split()`(第三个参数指定了我们希望返回的最大分割数或片段数，-1 表示“无限制”)。根据手册，使用`PREG_SPLIT_NO_EMPTY`旗表示；*

> 如果设置了这个标志，preg_split()将只返回非空的片段。

所以我的分离器变成了。

```
 print_r(preg_split('#s*</?p>s*#', $doc, -1, PREG_SPLIT_NO_EMPTY)); 

```

…产生以下输出…

```
Array
(
    [0] => Paragraph one.
    [1] => Paragraph two.
    [2] => Paragraph three.
)

```

…好多了。

## 捕获拆分分隔符

正如我在[第一个例子](#is_that_a_date)的结尾提到的，有另一种方法可以从类似‘20061028134534’的日期/时间戳中提取组件，这涉及(可以说)通过利用`PREG_SPLIT_DELIM_CAPTURE`标志盗用`preg_split()`。查阅[手册](http://www.php.net/preg_split) …

> PREG_SPLIT_DELIM_CAPTURE:如果设置了此标志，分隔符模式中的括号表达式也将被捕获并返回。

注意:并不是所有的正则表达式引擎都支持返回分隔符，正如我在 T1 之前抱怨的那样——共同点似乎是 T2 的企业级引擎，他们似乎不赞成让开发者的生活像 T4 的 T5 那样简单。反正…在 Perl，PHP，Ruby，Python 和(Mozilla！)Javascript，您应该会发现返回一个 regex 分割分隔符是受支持的(事实上在 [ICU](http://icu.sourceforge.net/userguide/regexp.html) 中也是如此)，这使得简单的记号赋予器[易于构造](http://simon.incutio.com/archive/2003/10/26/reSplit)。

所以这里有一个例子，做了或多或少与前面的`preg_match()`为基础的日期/时间戳提取器相同的事情；

```
 $date = '20061028134534';
print_r(
        preg_split(
                   '/^(d{4})|(d{2})/'
                   ,$date,
                   -1,
                   PREG_SPLIT_DELIM_CAPTURE |
                   PREG_SPLIT_NO_EMPTY
                   )
        ); 

```

输出…

```
Array
(
    [0] => 2006
    [1] => 10
    [2] => 28
    [3] => 13
    [4] => 45
    [5] => 34
)

```

它使用日期的组成部分作为分隔符来分割日期，然后返回这些分隔符。因为输入日期(在这种情况下)恰好是正确的格式，所以我得到了我想要的，但是值得注意的是，这种方法*无法*验证日期格式——您可以对它进行任何处理，并且会得到某种结果。但它带给我的是处理‘2006 10 28’、‘2006 10 281234567890’等变长时间戳的能力。

我将让您来弄清楚它在做什么——尝试移除`PREG_SPLIT_DELIM_CAPTURE`和`PREG_SPLIT_NO_EMPTY`标志，看看您会得到什么。我将回到`PREG_SPLIT_DELIM_CAPTURE`，另找时间解析/标记。

## 包裹

这对于一次射击来说已经足够了。这一轮的关键点是子模式、分支操作符和`preg_split()`。下次吧。

## 分享这篇文章