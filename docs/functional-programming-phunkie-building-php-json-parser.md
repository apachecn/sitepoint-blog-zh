# 使用 Phunkie 进行函数式编程:构建 PHP JSON 解析器

> 原文：<https://www.sitepoint.com/functional-programming-phunkie-building-php-json-parser/>

Phunkie 是一个为 PHP 提供函数结构的库。在这个由两部分组成的教程中，Phunkie 的创建者 Marcello Duarte，Inviqa 的培训主管，解释了如何使用函数库创建解析器组合子。这篇文章最初出现在 Inviqa 博客上[，并在他们的允许下在这里重新发布。为了探索 PHP 的“非奇异”特性，我们有很棒的](https://inviqa.com/blog/functional-programming-php-developers-guide-parser-combinators-phunkie-part-ii)[付费课程](https://www.sitepoint.com/premium/courses/exploring-php-2951?aref=bskvorc)。

在本系列的第一部分中，我们探索了解析器和组合器，以帮助您开始从 PHP 函数式编程中获得价值。我们已经通过例子介绍了基础知识，现在我们将讨论更多的排序和其他策略。

让我们从停止的地方继续吧！

![Phunkie logo](img/964c55771b588e8fe522ab66012737f9.png)

## 排序组合子

好了，现在让我们尝试一个更有用的解析器。一种解析器，给定一个谓词，如果它满足谓词，则保留该字符，否则不保留该字符。我们将把这个解析器称为`sat`，从满足。

```
 describe("sat", function() {

            it("parses one character when it satisfies the predicate", function(){
                expect(sat("is_numeric")->run("4L"))->toEqual(ImmList(Pair('4', 'L')));
            });

            it("returns Nil when the character does not satisfy the predicate", function(){
                expect(sat("is_numeric")->run("L4"))->toEqual(Nil());
            });

        }); 
```

使用原语解析器`item`、`result`和`zero`的实现如下所示:

```
function sat(callable $predicate): Parser
{
    return item()->flatMap(function($x) use ($predicate) {
        return $predicate($x) ? result($x) : zero();
    });
} 
```

你可以看到积木现在是如何工作的。我们调用`item`解析器，对其结果调用`flatMap`，并应用谓词。如果谓词成功，我们返回`result`解析器，它基本上将`$x`提升到解析器上下文中。否则，`zero`会做同样的事情，但是用`Nil`代替任何结果。

我们可以通过创建一些其他的排序组合子来快速利用`sat`。

```
 context("Sequencing combinators", function() {
        describe("char", function() {
            it("parses a character", function() {
                expect(char('h')->run("hello"))->toEqual(ImmList(Pair('h', "ello")));
            });
        });

        describe("digit", function() {
            it("parses a digit", function() {
                expect(digit()->run("42"))->toEqual(ImmList(Pair('4', '2')));
            });
        });

        describe("lower", function() {
            it("parses a lowercase character", function() {
                expect(lower()->run("hello"))->toEqual(ImmList(Pair('h', "ello")));
            });
        });

        describe("upper", function() {
            it("parses an upper case character", function() {
                expect(upper()->run("Hello"))->toEqual(ImmList(Pair('H', "ello")));
            });
        });
    }); 
```

你会笑实现有多简单！

```
function char($c): Parser
{
    return sat(function($input) use ($c) { return $input === $c; });
}

function digit(): Parser
{
    return sat('is_numeric');
}

function lower(): Parser
{
    return sat(function($c) { return ctype_lower($c); });
}

function upper(): Parser
{
    return sat(function($c) { return ctype_upper($c); });
} 
```

## 选择组合子

在真实的语法世界中，如果我们必须在第一个解析器失败后立即返回一个空列表，我们将无法生存。解析器必须处理语法选择结构。一个非常常见的场景是匹配一个模式或另一个模式。我们通过将`plus`组合子添加到我们的解析器库中来做到这一点。

```
use function concat;

function plus(Parser $p, Parser $q)): Parser
{
    return new Parser(function(string $s) use ($p, $q) {
        return concat($p->run($s), $q->run($s));
    });
} 
```

我喜欢将一些组合子实现移入`Parser`类本身。例如，`plus`组合子变成了类中的`or`方法，为解析器创建了一些语法糖。

```
function plus(Parser $p, Parser $q)): Parser
{
    return $p->or($q);
} 
```

选择分析器的一个例子可以是接受小写或大写字符的分析器。我们可以将这个解析器命名为`letter`。我们还可以创建另一个接受数字或字母的解析器，我们可以将其命名为`alphanum`。

```
 context("Choice combinators", function() {
        describe("letter", function() {
            it("can combine parsers to parse letters", function() {
                expect(letter()->run("hello"))->toEqual(ImmList(Pair('h', "ello")));
                expect(letter()->run("Hello"))->toEqual(ImmList(Pair('H', "ello")));
                expect(letter()->run("5ello"))->toEqual(Nil());
            });
        });

        describe("alphanum", function() {
            it("can combine parsers to parse alphanum", function() {
                expect(alphanum()->run("hello"))->toEqual(ImmList(Pair('h', "ello")));
                expect(alphanum()->run("Hello"))->toEqual(ImmList(Pair('H', "ello")));
                expect(alphanum()->run("5ello"))->toEqual(ImmList(Pair('5', "ello")));
                expect(alphanum()->run("#ello"))->toEqual(Nil());
            });
        }); 
```

实现的优雅不言自明:

```
function letter(): Parser
{
    return plus(lower(), upper());
}

function alphanum(): Parser
{
    return plus(letter(), digit());
} 
```

## 递归组合子

我们可以使用的一个好技巧是将`result`解析器传递给`plus`来创建非确定性解析器。为了说明这一点，让我们构建一个从字符串中识别整个单词的`word`解析器。结果可能会让你吃惊:

```
 context("Recursive combinators", function() {
        describe("word", function() {
            it("recognises entire words out of a string", function() {
                expect(word()->run("Yes!"))
                    ->toEqual(ImmList(
                        Pair("Yes", '!'),
                        Pair("Ye", "s!"),
                        Pair('Y', "es!"),
                        Pair("", "Yes!")
                    ));
            });
        });

    //...
    }); 
```

在深入解释为什么我们有这么多对结果之前，让我们先来看看实现:

```
function word(): Parser
{
    $nonEmptyWord = letter()->flatMap(function($x) {
        return word()->map(function($xs) use ($x) {
            return $x . $xs;
        });
    });

    return plus($nonEmptyWord, result(''));
} 
```

正如我们所看到的，`letter`的使用意味着我们正在使用一个可用的字母，然而我们可能使用了一个字母，但没有到达解析的结尾。这种选择组合子是不确定的。我们得到了某种结果。我们达成了一封信。现在让我们看看是否还有更多。直到我们找到一个不是字母的东西，然后停下来。

另外，请注意我们如何在这个实现中使用[递归](https://en.wikipedia.org/wiki/Recursion)来继续解析、连接结果。顺便说一下，这就是我没有在这里使用`for notation`的原因。PHP 不是一种懒惰的语言，因此用符号实现递归会导致堆栈溢出。

一个非常有用的解析器可以识别另一个字符串中的整个字符串(或标记)。我们称之为`string`并递归实现它。

```
 describe("string", function() {

            it("parses a string", function() {
                expect(string("hello")->run("hello world"))
                    ->toEqual(ImmList(Pair("hello", " world")));
            });

               expect(string("helicopter")->run("hello world"))
                   ->toEqual(Nil());

        }); 
```

实现如下:

```
function string($s): Parser
{
    return strlen($s) ?

        for_(
            __($c)->_(char($s[0])),
            __($cs)->_(string(substr($s, 1)))
        )->call(concat, $c, $cs) :

        result ('');
} 
```

## 简单的重复

您可能会想到，word 和 string 中使用的重复模式是解析器经常遇到的。我们或许也可以这样概括。以这种方式创建解析器的美妙之处在于，它们可以很容易地组合起来创建新的解析器。

我们现在将定义简单的重复解析器`many`。我们将做出许多不确定的选择，这意味着它永远不会失败，但将返回一个空字符串。

```
 context("Simple repetitions", function() {
        describe("many", function() {

            it("generalises repetition", function() {
                expect(many(char('t'))->run("ttthat's all folks"))->toEqual(ImmList(
                    Pair("ttt", "hat's all folks"),
                    Pair("tt", "that's all folks"),
                    Pair('t', "tthat's all folks"),
                    Pair("", "ttthat's all folks")
                ));
            });

            it ("never produces errors", function() {
                expect(many(char('x'))->run("ttthat's all folks"))->toEqual(ImmList(
                    Pair("", "ttthat's all folks")
                ));
            });

        });
    //...
    }); 
```

这个实现看起来非常类似于`word`实现，只是我们要求一个解析器，而不是使用`letter`，所以我们可以表示任何类型的重复。

```
function many(Parser $p): Parser
{
    return plus($p->flatMap(function($x) use ($p) {
        return many($p)->map(function($xs) use ($x) {
            return $x . $xs;
        });
    }), result(''));
} 
```

我们现在可以简单地将`word`定义为`many(letter())`。同样，我们可以尝试用`many(digit())`实现一个数字解析器，但是由于`many`是不确定的，我们需要一个至少匹配一个字符的`many`版本。我们就称之为`many1`。

```
 describe("many1", function() {
            it("does not have the empty result of many", function() {
                expect(many1(char('t'))->run("ttthat's all folks"))->toEqual(ImmList(
                    Pair("ttt", "hat's all folks"),
                    Pair("tt", "that's all folks"),
                    Pair('t', "tthat's all folks")
                ));
            });
            it("may produce an error", function() {
                expect(many1(char('x'))->run("ttthat's all folks"))->toEqual(Nil());
            });
        }); 
```

用`for notation`实现:

```
function many1(Parser $p): Parser
{
    return for_(
        __($x)->_($p),
        __($xs)->_(many($p))
    )->call(concat, $x, $xs);
} 
```

然后我们可以用它来实现我们的自然数解析器`nat`。这一次我们将更进一步，通过将结果转换为整数来评估它。我们可以通过取链表的`head`得到结果，它是一对，然后使用`_1`评估器得到结果。下面是一个例子:

```
 describe("nat", function() {
            it("can be defined with repetition", function() {
                expect(nat()->run("34578748fff"))->toEqual(ImmList(
                    Pair(34578748, "fff"),
                    Pair(3457874, "8fff"),
                    Pair(345787, "48fff"),
                    Pair(34578, "748fff"),
                    Pair(3457, "8748fff"),
                    Pair(345, "78748fff"),
                    Pair(34, "578748fff"),
                    Pair(3, "4578748fff")
                ));
                expect(nat()->run("34578748fff")->head->_1)->toEqual(34578748);
            });
        }); 
```

下面是转换的实现:

```
function nat(): Parser
{
    return many1(digit())->map(function($xs) {
        return (int) $xs;
    });
} 
```

如果我们也想要负数，我们可以使用`nat`来构建一个`int`解析器。

```
 describe("int", function() {
            it("can be defined from char('-') and nat", function() {
                expect(int()->run("-251")->head->_1)->toEqual(-251);
                expect(int()->run("251")->head->_1)->toEqual(251);
            });
        }); 
```

实现简单，看起来像:

```
function int()
{
    return plus(for_(
        __($_)->_(char('-')),
        __($n)->_(nat())
    )->call(negate, $n), nat());
} 
```

落入`$_`的内容会被忽略，但会被匹配。Phunkie 库中的求反函数会将该数字转换为负整数。`nat()`确保我们也获得正数。

## 带分隔符的重复

至此，我们已经可以创建非常有趣的解析器了。例如，我们可以解析用 PHP 数组符号写成的整数列表:`[1,-42,500]`。根据我们已经知道的，我们可以这样写:

```
for_(
    __($open) ->_ (char('[')),
    __($n   ) ->_ (int()    ),
    __($ns  ) ->_ (many(
        for_(
            __($comma ) ->_ (char(',')),
            __($m )     ->_ (int()    )
        ) -> call (concat, $comma, $m))),
    __($close) ->_ (char(']'))
) -> call (concat, $open , $n , $ns , $close); 
```

事实上，我们可以通过概括分隔符的用法来简化这一点，在本例中，分隔符是逗号(，)。接下来我们将实现`sepBy1`，一个被多次应用的解析器，被另一个解析器的应用分开。这里有一个例子:

```
 describe("sepby1", function() {
            it("applies a parser several times separated by another parser", function() {
                expect(sepby1(letter(), digit())->run("a1b2c3d4"))
                    ->toEqual(ImmList(
                        Pair("abcd", '4'),
                        Pair("abc", "3d4"),
                        Pair("ab", "2c3d4"),
                        Pair('a', "1b2c3d4"))
                );
            });
        }); 
```

我们可以像这样快速实现它:

```
function sepBy1(Parser $p, Parser $sep)
{
    return for_(
        __($x)->_($p),
        __($xs)->_(many(for_(
            __($_)->_($sep),
            __($y)->_($p)
        )->yields($y)))
    )->call(concat, $x, $xs);
} 
```

在将`sepBy1`的实现转移到`Parser`类之后，我们现在可以使用中缀运算符`sepBy1`重新实现我们的整数列表解析器，这使得它更具可读性。

```
function ints()
{
   return for_(                                 __ ($_) ->_ (char('[') )                  ,__ ($ns  ) ->_ (int()->sepBy1(char(','))) ,__ ($_) ->_ (char(']'))
    ) -> yields ($ns);
} 
```

让我们在这里再应用一个概括，提取周围的模式。

```
function surrounded(Parser $open, Parser $p, Parser $close)
{
    return for_(
        __($_)->_($open),
        __($ns)->_($p),
        __($_)->_($close)
    )->yields($ns);
} 
```

这样我们就可以重写`ints`:

```
function ints()
{
    return surrounded(
        char('['),
        int()->sepBy1(char(',')),
        char(']')
    );
} 
```

## JSON 解析器

使用我们现有的资源，让我们构建一个 JSON 解析器。我不认为我们需要一步一步的描述，我将从帖子中链接 github repo。在这里，我也将告诉你一些不太直观的方面。

解析器基本上应该基于[规范](http://www.json.org/)构造一个`JsonObject`对象。这几乎是一个完整的实现。为了简单和简洁，我将省去空格、花哨的数字和字符串问题。

这是一个例子:

```
 context("Json parser", function() {
        describe("json_object", function() {

            it("parses json objects with no space or fancy digits", function() {
                expect((string)json_object()->run(

                    '{a:1,b:null,c:true,d:{x:"a",b:[1,2,3]}}'
                )->head->_1)->toEqual((string)

                new JsonObject(
                    ImmMap([
                        "a" => new JsonNumber(1),
                        "b" => new JsonNull(),
                        "c" => new JsonBoolean(true),
                        "d" => new JsonObject(
                            ImmMap([
                                "x" => new JsonString("a"),
                                "b" => new JsonArray([
                                    new JsonNumber(1),
                                    new JsonNumber(2),
                                    new JsonNumber(3)
                                ])
                            ])
                        )
                    ])
                ));
            });
        });
    }); 
```

`json_value`解析器是一个位于顶层的选择解析器。它的实现非常简单:

```
function json_value(): Parser
{
    return json_string()
        ->or(json_boolean())
        ->or(json_null())
        ->or(json_number())
        ->or(json_array())
        ->or(json_object());
} 
```

值得一提的是从解析值到 JSON 对象的转换。这通常是通过解析器组合子末尾的`map`来实现的。例如，参见`json_null`解析器:

```
function json_null(): Parser
{
    return string("null")->map(function($_) {
        return new JsonNull();
    });
} 
```

我创建了一个`sepBy1array`组合子，它不是返回最后连接的值，而是返回一个解析后的值的数组。这对`json_array`解析器来说非常方便。

```
function json_array(): Parser
{
    return char('[')->flatMap(function($_) {
        return sepBy1array(json_value(), char(','))->flatMap(function($elements) {
            return char(']')->map(function($_) use ($elements) {
                return new JsonArray(
                    array_filter($elements, function($a){ return $a != ''; })
                );
            });
        });
    });
} 
```

这里有一个类似的安排，使用 Phunkie 的对象的不可变映射。

```
function json_object(): Parser
{
    return char('{')->flatMap(function($_) {
        return sepBy1Map(word()->flatMap(function($key) {
            return char(':')->flatMap(function($colon) use ($key) {
                return json_value()->map(function($value) use ($key) {
                    return ImmMap($key , $value);
                });
            });
        }), char(','))->flatMap(function($pairs) {
            return char('}')->map(function() use ($pairs) {
                return new JsonObject($pairs);
            });
        });
    });
} 
```

暂时就这样吧！我希望这已经向您展示了函数式编程是多么强大，解析器组合子是多么可组合。我希望你喜欢这个教程，并受到启发，尝试自己的一些 Phunkie！

有问题吗？评论？在 Twitter 上通过 [@_md](https://twitter.com/_md) 找到我，或者在下面留言！

## 有用的链接

[1]–庞基库 https://github.com/phunkie/phunkie
[2]–马塞罗·杜阿尔特的解析器组合器库 https://github.com/MarcelloDuarte/ParserCombinators

## 分享这篇文章