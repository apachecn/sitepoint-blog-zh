# Phunkie 函数式编程:PHP 中的解析器组合子

> 原文：<https://www.sitepoint.com/functional-programming-with-phunkie-parser-combinators-in-php/>

Phunkie 是一个为 PHP 提供函数结构的库。在本教程中，Phunkie 创建者 Marcello Duarte，Inviqa 的培训主管，解释了如何使用函数库创建解析器组合子。这篇文章最初出现在 Inviqa 博客上[，并在他们的允许下在这里重新发布。](https://inviqa.com/blog/functional-programming-php-developers-guide-parser-combinators-phunkie)

![Phunkie logo](img/964c55771b588e8fe522ab66012737f9.png)

## 学习函数式编程

函数式编程真的很重要。纯函数(或者没有副作用的函数)是完美的行为单位，因为我们可以依靠它们来构建更大更复杂的系统。函数式编程的伟大之处就在于这种可组合性。

这是我第一次相信的事情，那是在我获得管理计算的执照学位的时候，在人工智能和 Lisp 的一个学期里。我的课程主要集中在 C/C++上，因此我必须关注技能需求。

令人欣慰的是，我已经能够在 Inviqa 重新点燃我学习函数式编程的热情，在这里，我们正在交付越来越多的基于 Scala (通用编程语言)的项目。

《红宝书》我大概读过三遍(丘萨诺[比雅纳松](https://www.manning.com/books/functional-programming-in-scala)一部，不是沃恩·弗农的那部)。我上了马丁·奥德斯基的所有 Coursera 课程，周末花了几个小时看视频和看论文。

我给自己设定了一个挑战:用函数式编程做我在学习 TDD 时做的事情，并创建了 [PHPSpec](https://www.sitepoint.com/bdd-in-laravel-getting-started-with-behat-and-phpspec/) (一个[BDD](https://www.sitepoint.com/bdd-javascript-cucumber-gherkin/)PHP 开发人员的测试和设计工具)来帮助我学习；我决定用 PHP 编写自己的函数式编程方言。就这样 Phunkie 诞生了！

现在轮到你了。学习函数式编程意味着让自己沉浸在一种新的范式中，这可能很有挑战性。它需要一种完全不同的心态来处理问题。

所以，当你可以使用函数式编程来解决现实世界的问题时，你已经花了几个小时来掌握新的思维或了解理论。

在本教程中，我的目的是通过回顾我对 Hutton & Meijer 的一元解析器组合子的实现来帮助快速跟踪您的旅程。希望你能够看到函数式编程的思想和用途。

而且，通过使用 Phunkie，您可以消除学习 Haskell 来理解主题的需要。你所需要的只是对 PHP 有所了解。

所以让我们开始吧！

## 分析器

根据 Terence Parr 的说法，解析是计算机识别短语的行为。解析有很多策略。我们将使用递归下降解析，这是最基本的解析之一，但它足够强大，可以实现一些有用的语法结构，如排序、选择和重复。

## 组合子

如果有人问函数式编程是怎么回事，我会回答:组合。函数是完美的行为单元，你可以组合它们来创建更大的系统。组合子是一种组合模式，早在编写任何类或对象之前，它就已经出现在函数式编程中了。它们也很优雅。

我们将解析器实现为函数。只有三个非常基本的解析器就足以帮助我们创建本文中的所有其他解析器。

### 使用类型来表示函数

让我们想想解析器是做什么的。我们给它一个字符串。然后，它试图找出该字符串是否匹配任何语法定义。如果我们有一个匹配，我们保留结果以及字符串的剩余部分。如果我们失败了，我们就停止。

那么函数声明会是什么样子呢？

```
<?php

function parse(string $toParse): mixed
{
    // if it matches a grammar, it returns the match and the remaining string
    // if it doesn’t, it returns an empty set
} 
```

但是一个函数只能返回一个结果。那么我们如何返回一个匹配加上剩余的字符串呢？我们将使用一个 Phunkie 类型:`Pair`。pair 允许您将不同类型的值组合在一起，如下所示:

```
<?php

// We will use the Phunkie immutable pair for our pair
use Phunkie\Types\Pair;

function parse(string $toParse): Pair
{
    // if matches grammar
    return Pair($match, $remaining);
} 
```

所以一个解析器类型可以这样描述:`Parser = (String) -> Pair<Result, String>`，内容是:解析器是一个函数，它取一个字符串，返回一个由解析结果和剩余待解析字符串组成的对。

如果我们只对一个解析器感兴趣，这就足够了。但是如果我们在讨论合并解析器呢？结果`Pair<Result, String>`是不够的，所以我们将有一个这些对的列表，其中一个对将是每个解析器的结果。考虑到这一点，解析器类型应该是这样的:`Parser = (String) -> List<Pair<Result, String>>`。

这将用 PHP 编写，使用类语法，如下所示:

```
<?php

// We will use the Phunkie immutable list for our list
use Phunkie\Types\ImmList;

/**
 * Represents a function (String) -> List<Pair<Result, String>>
 */
class Parser
{
    /**
     * A function that takes a String and returns List of Pairs (Result, String)
     *
     * @var callable
     */
    private $run;

    public function __construct(callable $run)
    {
        $this->run = $run;
    }

    /**
     * @param string $toParse
     * @return ImmList of pairs of (results, remaining)
     */
    public function run(string $toParse): ImmList
    {
        return ($this->run)($toParse);
    }
} 
```

## 原始分析器

现在我们有了类型定义，我们可以开始创建非常基本的解析器了。第一个是一个解析器，它总是成功的，不消耗任何字符串。你可能会说这毫无用处。好吧，我们才刚刚开始，很快你就会看到我们能用这些原始的解析器做什么。

让我们从创建一个如何使用它的例子开始。我们将调用我们的解析器`result`。该测试将如下所示:

```
<?php

describe("Parser", function() {
    context("Basic parsers", function() {

        describe("result", function() {

            it("succeeds without consuming any of the string", function() {
                expect(result("hello")->run("world"))
                    ->toEqual(ImmList(Pair("hello", "world")));
            });
        });

    }
} 
```

实现非常简单:

```
<?php

function result(string $a): Parser
{
    return new Parser(function(string $s) use ($a): ImmList {
        return ImmList(Pair($a, $s));
    });
} 
```

那很容易。现在让我们构建另一个总是失败的原始浏览器，不管给它什么。失败由空列表表示。Phunkie 中的空列表是用`Nil()`构造的。我们可以把这个解析器叫做:`zero`。该测试将如下所示:

```
 describe("zero", function(){

            it("always return an empty list, which means failure", function(){
                expect(zero()->run("world"))->toEqual(Nil());
            });

        }); 
```

您大概可以猜到实现方式:

```
function zero(): Parser
{
    return new Parser(function($s): ImmList {
        return Nil();
    });
} 
```

让我们再介绍一个基本的解析器。这一次，更有用一点。这个解析器将使用字符串的第一个字符。如果字符串为空，它将失败。让我们看看它是什么样子的。下面是一个例子:

```
 describe("item", function() {

            it("returns an empty list for an empty string", function() {
                expect(item()->run(""))->toEqual(Nil());
            });

            it("parses a string and returns the first character", function() {
                expect(item()->run("hello"))->toEqual(ImmList(Pair('h', "ello")));
            });

        }); 
```

实现应该是简单的:

```
function item(): Parser
{
    return new Parser(function(string $s): ImmList {
        return strlen($s) == 0 ? Nil() : ImmList(Pair($s[0], substr($s, 1)));
    });
} 
```

这就是原始解析器。我们现在可以利用它们来构建组合子，组合多个解析器的能力来构建更大的解析器。

## 解析器组合子

假设我们想要获取字符串`"hello"`，应用`item`解析器，然后再次应用`item`解析器。如果有一个解析器可以让您应用两个解析器的序列并返回一对结果，那就太好了。让我们称这个解析器为`seq`。下面是一个例子:

```
 describe("seq", function() {

            it("applies parsers in sequence", function() {
                expect(seq(item(), item())->run("hello"))
                    ->toEqual(ImmList(Pair(Pair('h', 'e'), "llo")));
            });

        }); 
```

如果我们试图凭直觉实现这一点，我们可能会得出一些在函数式编程之外常见的命令式代码。第一次尝试应该是这样的:

```
function seq(Parser $p, Parser $q): Parser
{
    return new Parser(function($s) use ($p, $q) {
        // run the first parser, store the result
        $resP = $p->run($s);

        // run the second parser, using the remaining string from the first parser
        $resQ = $q->run($resP->head->_2);

        // return the pair with both results
        return ImmList(Pair(Pair($resP->head->_1, $resQ->head->_1), $resQ->head->_2));
    });
} 
```

看起来很困惑，对吧？那是因为它是！

这个命令式的例子过于简单了。我们甚至没有检查第一个解析器是否失败。更重要的是，如果我们对此应用另一个`seq`,我们将得到嵌套元组，这对这个解析器可能不是一个有用的结果。

您会记得我们将`Parser`类型定义为:具有解析字符串并返回结果和剩余字符串的函数的东西。更一般地说，我们的`Parser`类型代表一种计算。

当我们调用方法`run`时，可以触发计算。在现代函数式编程中，我们经常这样做，不仅使用类型来表示一系列值，还表示其他计算元素。

事实上，我们在这里追求的操作——从第一个(解析)上下文中获取值并将该值传递给另一个(解析)上下文——是函数式编程中众所周知的模式。

这种对上下文中的值的抽象称为单子。通过在我们的`Parser`类上实现单子组合方法`flatMap`,我们可以使解析器非常可组合——这就是使用单子的全部意义。

让我给你看一个`Parser`的`flatMap`的可能实现，然后我们可以一步一步来看。

```
class Parser
{
    private $run;
    public function __construct(callable $run) { $this->run = $run; }
    public function run(string $toParse): ImmList { return ($this->run)($toParse); }

    public function flatMap(callable $f): Parser
    {
        return new Parser(function(string $s) use ($f) {
            return $this->run($s)->flatMap(function(Pair $result) use ($f) {
                return $f($result->_1)->run($result->_2);
            });
        });
    }
} 
```

这个方法使用自己的解析器函数和传递给它的新解析器函数创建一个新的`Parser`。首先我们叫`run` : `$this->run("hello")`。如果`$this`是一个`item`解析器，它将返回类似`ImmList(Pair("h", "ello"))`的结果。

一旦我们获得了将解析器应用于输入字符串的结果，我们就可以将另一个解析器函数应用于这个结果。是的，结果在一个列表里！那么你如何从一个上下文中得到一些东西呢？哦耶，单子！是的，列表也是单子。所以我们在列表上的`flatMap`获取结果:`$this->run($s)->flatMap(function(Pair $result)`。现在我们可以将给定的函数应用于结果对。我们可以使用`_1`和`_2`只读访问器来访问对中的第一个和第二个元素:return`$f($result->_1)->run($result->_2)`；。

另一个重要的组合子是`map`。虽然赋予`flatMap`的函数必须返回一个`Parser`，但赋予`map`的函数可以返回任何值，并且`map`方法将确保它被返回到函数被映射的上下文中。

```
 public function map(callable $f)
    {
        return new Parser(function(string $s) use ($f) {
            return $this->run($s)->map(function(Pair $result) use ($f) {
                return Pair($f($result->_1), $result->_2);
            });
        });
    } 
```

请注意在代码示例中,`$f`的返回值是如何用于创建组成解析器的结果的。`map`通常用在一系列`flatMaps`的末尾，简单地组织你想要返回的值，确保它们在你需要的上下文中。

让我们考虑一个使用单子的`seq`实现。注意我们如何使用`map`来暴露我们期望看到的结果:

```
function seq(Parser $p, Parser $q): Parser
{
    return $p->flatMap(function($x) use ($q) {
        return $q->map(function($y) use ($x) {
            return Pair($x, $y);
        });
    });
} 
```

事实上，这种模式在函数式编程中非常普遍，以至于 FP 语言通常有一种特殊的语法。这是它在 Phunkie (0.6.0)中的样子:

```
function seq(Parser $p, Parser $q): Parser
{
    return for_(
        __($x)->_($p),
        __($y)->_($q)
    )->yields($x, $y);
} 
```

我知道您在想什么:这个`seq`解析器非常简洁，但是如果我们的解析器只能解析字符串的第一个字符，那么组合解析器又有什么用呢？好了，现在我们已经用例子讲述了基础知识，我们可以继续这样的问题了。

请继续关注本系列的第二部分，它将涵盖更多的排序和其他策略。

## 分享这篇文章