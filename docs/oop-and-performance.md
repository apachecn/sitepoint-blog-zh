# 面向对象和性能

> 原文：<https://www.sitepoint.com/oop-and-performance/>

与[过程化的](http://en.wikipedia.org/wiki/Procedural_programming)相比，面向对象编程通常被视为一种折衷:基于对象引入的额外运行时查找开销(与等价的函数+变量集合相比)，通过更好的模块化/重用与更慢的处理来提高“开发人员性能”。

在谷歌上搜索“php oop”和[这是第一个结果](http://www.webmasterstop.com/tutorials/efficiency-PHP-OOP.shtml)，这是相当好的平衡，但得出了安全的结论；

> 下一次开发 PHP 代码时，您应该考虑是想要更快的执行时间/更少的 CPU 负载，还是更容易维护代码

这个论点是基于基准代码的。然而，它忽略了编写代码的人的方面，而这正是 OOP 的副作用可以提高性能的地方。

记住我只是在一般意义上说，我认为程序代码的趋势是使用“蛮力”——逻辑的实际*意味着*和*行为*被意大利面条掩盖了。如果代码实际上做了什么对开发人员来说是透明的，他们可能会看到他们引入的一些“令人眼花缭乱”的低效。

有趣的是，不久前我被要求帮助一个用 PHP 编写的统计分析工具，该工具对一个巨大的数据集进行计算。PHP、服务器和教皇已经因其糟糕的性能而受到指责。

从程序上来说，没有人有时间去责备的是基本上可以归结为这一点的逻辑(但这并不容易看到，因为它分散在多个文件中)；

```
 $giantDataSet = getGiantDatasetFromSomewhere();

$totalX = calculateTotalX($giantDataSet);

$averageX = calculateAvergageX($giantDataSet);

$totalY = calculateTotalY($giantDataSet);

$averageY = calculateAverageY($giantDataSet);

// etc.

```

所有这些计算函数内部都是一个循环，所以每个计算都是对整个数据集的另一个遍历。与此同时，每个计算更像是一个过滤器，做基本的数学运算，并乐于一次处理一行。

我的感觉是，如果作者考虑有用的抽象，那么每次遍历整个数据集的低效率就会凸显出来。相反，他们在代码中陷得太深，无法看到更大的画面。

修改后的版本类似于这样，对单个循环执行完整的分析；

```
 class Analyser {

    // stuff here...

    // Process the data something like this...
    function analyse($data) {
        foreach ( $data as $row ) {

            foreach ( array_keys($this->filters) as $key ) {

                $this->filters[$key]->filter($row);

            }

        }
    }

}

// Usage something like this;
$A = & new Analyser();

$A->addFilter(new TotalXFilter());
$A->addFilter(new AverageXFilter());
$A->addFilter(new TotalYFilter());
$A->addFilter(new AverageYFilter());

$A->analyse(getGiantDatasetFromSomewhere());

```

你可能会认为这是开发人员的问题，但我最近遇到的另一个例子让我想到了其他类型的问题，即由人编写的过程代码(相对于生成的代码)总是会产生糟糕的执行结果。

我基本上开始考虑如何让 [Dokuwiki 的](http://wiki.splitbrain.org/wiki:dokuwiki)解析器能够处理 UTF-8 编码的文本。在这个过程中，我已经或多或少地使用来自[简单测试](http://www.lastcraft.com/simple_test.php)的[词法分析器](http://www.phppatterns.com/index.php/article/articleview/106/1/2/)重写了解析器(实际上是考虑到 UTF-8 的一个稍微修改的版本)。

简而言之，Simple Test 的 lexer 充当了一个工具，使正则表达式易于管理——而不是你编写许多小/简单的巨型正则表达式。lexer 负责有效地组合它们，然后给你一个类似于 [SAX 的](http://www.php.net/sax)回调 API，允许你编写代码来响应匹配的“事件”。

对我来说，令人惊讶的结果是性能显著提高。在我的机器上，用 Dokuwiki 的本地解析器解析 [wiki:syntax](http://wiki.splitbrain.org/wiki:syntax) 源代码需要 5 到 7 秒。使用基于 Simple Test 的 lexer 的解析器，需要 0.2 到 0.25 秒。

看起来*造成这种差异的原因是 Dokuwiki 的解析器多次扫描完整的原始文本，用 HTML 替换 wiki 语法。对整个源文档至少进行了 18 次扫描。同时，Simple Test 的 Lexer 只扫描整个文档一次。*

这绝不是对 Dokuwiki 作者的批评。使用类似的方法，我当然不会做得更好。作为一个普通人，对我来说最简单的方法是编写对源代码进行多次扫描的代码——我的大脑不可能将 Dokuwiki 的解析器所做的一切合并到一个正则表达式中。

进一步扩展解析讨论，类似的故事似乎由 [Piccolo](http://piccolo.sourceforge.net/) 讲述；

> Piccolo 是一个小型的、速度极快的 fast XML 解析器

> Piccolo 是使用解析器生成器工具 JFlex 和 BYACC/J 的修改版本开发的……我注意到几乎所有的 Java XML 解析器都是手写的

底线似乎是*而不是*“那些研究 OOP 的人是更好的程序员”。相反，有些情况下，对于一个人(任何人)来说，编写性能良好的代码，需要从原始过程中进行抽象*。OOP 是实现抽象的一种可能方式，它可以帮助你开发更有效的解决方案。当然，“这是面向对象的，所以一定要慢一些”的观点是肤浅的。*

 *无论如何——回到 Dokuwiki 解析器，还有一些工作要做。

Simple Test 的 Lexer 的一个缺点(除非我修改它)是不能在你提供的正则表达式中使用子模式(它会对它们进行转义)。现在这使得寻找一个列表的结尾变得很困难，我还没有找到“前导空格”[非解析块](http://wiki.splitbrain.org/wiki:syntax#nonparsed_blocks)。

我还需要检查是否真的是*在处理 UTF-8:目前我已经为它发出的 preg_match()调用添加了/u 修饰符，但是这里也使用了一些 str*函数，用于提取子字符串——我可以使用 preg_split()和/u 修饰符来解决这个问题。*

除了性能之外，它应该一切顺利，另一个积极的结果将是输出格式与解析很好地分离。这意味着应该可以呈现当前 HTML 的替代输出格式——“简单地”插入一个包含正确方法名称的类——类似于；

```
 class Plaintext_Renderer {

    var $indent = 0;

    // Called on dokuwiki headers
    function header($level, $text) {
        $this->indent = $level;

        // Never mind the UTF-8...
        fwrite(STDOUT, strtoupper($text));
    }

    // Normal text
    function cdata($text) {
        fwrite(
            STDOUT,
            str_pad($text, $this->indent, " ", STR_PAD_LEFT)
            );
    }

    // etc.
}

```

一个很好的巧合是[维基百科](http://wikipedia.sourceforge.net/)和 Dokuwiki 使用几乎完全相同的[标记](http://en.wikipedia.org/wiki/Wikipedia:Wiki_markup)(我相信 Dokuwiki 部分基于 Mediawiki)。

这里的和[测试](http://cvs.sourceforge.net/viewcvs.py/xmlrpccom/dokuwiki/dev/tests/php/)是[。](http://cvs.sourceforge.net/viewcvs.py/xmlrpccom/dokuwiki/lib/)* 

## *分享这篇文章*