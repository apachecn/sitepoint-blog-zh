# PHP 宏的乐趣和利润！

> 原文：<https://www.sitepoint.com/php-macros-for-fun-and-profit/>

*我原本打算把这个叫做“带有 Márcio 完美实用的预处理器库的微宏”，但我没想到 Bruno 会同意……*

当开发人员感到有能力创造新的工具，甚至新的语言来解决他们的问题时，我真的很兴奋。

你看，很多开发者是从其他语言来到 PHP 的。许多 PHP 开发人员可以用多种语言编写代码。这些语言中经常有一些东西——小语法糖——是我们在构建 PHP 时欣赏甚至错过的。

![Complex laboratory labyrinth](img/c757eca8e5cb4d741dad755a3c1510b8.png)

在编译器层面上，将这些添加到语言中是很难的，是吗？)。除非你构建了编译器和/或知道它们是如何工作的。我们不会做任何技术性的事情，但我们仍然会被赋予权力。

当我以前写 Ruby(或在这方面类似的语言)时，我习惯使用列表索引范围。它们看起来像这样:

```
few = many[1..3] 
```

我们可以期待这样的代码获取`many`列表的第二、第三和第四项，并将它们赋给`few`变量。我们可以做一些类似的事情，使用`array_slice`:

```
$few = array_slice($many, 1, 3); 
```

我认为 Ruby 的等价物要优雅得多。我们也可以使用变量和负值作为范围的下限和上限。太有才了！

## 宏入门

受到最近 SitePoint 帖子的启发，我决定尝试将这种语法添加到我的应用程序中。问题是我对 PHP 解释器的理解不够好，无法添加它。然后我想起了[马尔乔·阿尔马达的 Yay 图书馆](https://github.com/marcioAlmada/yay)。它通过预处理添加宏。下面是一个简单的例子:

```
macro {
    unless (···condition) { ···body }
} >> {
    if (!(···condition)) { ···body }
}

$condition = false;

unless ($condition) {
    print "look ma! macros...";
} 
```

这个例子完全是自述文件(有一些风格上的不同)。

为了运行它，我们需要下载预处理器:

```
$ composer require yay/yay:dev-master 
```

下载完成后，您已经将上面类似 PHP 的代码保存到一个文件中(姑且称之为`unless.yphp`)，我们可以将它编译成普通的 PHP:

```
$ vendor/bin/yay unless.yphp >> unless.php 
```

结果将类似于:

```
$condition = false;

if (!($condition)) { print "look ma! macros..."; } 
```

## 这一切是如何运作的

Yay 实现了一个解析器。它将一个代码串分解成标记，构建一个抽象语法树，并将 PHP 代码与所有替换为真实 PHP 代码的宏放在一起。

起初，这些概念可能很难理解。我建议你阅读[这一系列](https://medium.com/making-languages)，其中解释了一些使用你熟知的工具。

当 Yay 匹配我们定义的`unless`模式时，它实际上在`condition`和`body`周围做了一个字符串替换。对于我们想要添加的语言语法，我们可以很好地利用这一点

## 添加我们的语法

我们需要做的第一件事是确定匹配范围索引的表达式:

```
macro {
    T_VARIABLE·A[
        T_VARIABLE·B..T_VARIABLE·C
    ]
} >> {
    array_slice(
        T_VARIABLE·A,
        T_VARIABLE·B,
        T_VARIABLE·C - T_VARIABLE·B
    )
} 
```

这个宏表达式寻找一个变量，比如`$many`，并将其标记为`A`。只有当它看到等同于`$many[$lower..$upper]`的字符时，它才会匹配，它会用以下字符替换:

```
array_slice(
    $many,
    $lower,
    $upper - $lower
); 
```

*`array_slice`函数参数是源数组、起始偏移量和要获取的项数。这与范围语法略有不同，因为范围语法定义了起始偏移量和结束偏移量。我们通过从下界减去上界在两者之间进行转换。*

`T_VARIABLE`是一种 AST 标记类型，它标识代码字符串中定义的变量。有相当多不同的令牌类型，但幸运的是，我们不必记住它们…

我们可以试试这个，用一些不错的 PHP:

```
macro {
    T_VARIABLE·A[
        T_VARIABLE·B..T_VARIABLE·C
    ]
} >> {
    array_slice(
        T_VARIABLE·A,
        T_VARIABLE·B,
        T_VARIABLE·C - T_VARIABLE·B
    )
}

$many = [
    "She walks in beauty",
    "like the night",
    "of cloudless climes",
    "and starry skies",
    "And all that's best",
    "of dark and bright",
    "meet in her aspect",
    "and her eyes",
    "...",
];

$lower = 4;
$upper = 8;

$few = $many[$lower..$upper]; 
```

当我们在转换过程中运行它时，我们会得到如下结果:

```
$many = [
    "She walks in beauty",
    "like the night",
    "of cloudless climes",
    "and starry skies",
    "And all that's best",
    "of dark and bright",
    "meet in her aspect",
    "and her eyes",
    "...",
];

$lower = 4;
$upper = 8;

$few = array_slice(
    $many,
    $lower,
    $upper - $lower
); 
```

这是一个好的开始。我们仍然希望允许简单的整数边界，这需要稍微复杂一些的宏代码:

```
macro {
    →(···expression)
} >> {
    ··stringify(···expression)
}

macro {
    T_VARIABLE·A[
        ···range
    ]
} >> {
    eval(
        '$list = ' . →(T_VARIABLE·A) . ';' .
        '$lower = ' . explode('..', →(···range))[0] . ';' .
        '$upper = ' . explode('..', →(···range))[1] . ';' .
        'return array_slice($list, $lower, $upper - $lower);'
    )
} 
```

让我们一步一步来。我们首先定义一个宏来将模式匹配转换为字符串。`··stringify(...)`是一个扩展(这意味着它转换其他匹配)。它接受一个匹配，并将其直接转换为一个字符串。我们可以在下面的例子中看到这种情况:

```
macro {
    convert(···expression)
} >> {
    ··stringify(···expression)
}

convert($a, $b, $c); 
```

如果我们通过 Yay 运行这段代码，我们将看到以下内容:

```
'$a, $b, $c'; 
```

我选择创建一种简写宏，将`→(...)`转换为`··stringify(...)`，以使更多的宏更加简洁。

我在试图捕获索引范围语法中的数字时遇到了一些问题。当 Yay 得到 AST 时，`4..8`被标记为`4.`和`.8`；而不是`4`、`..`和`8`。这将在 Yay 应用宏之前生成一个解析器错误。一种解决方法是将索引范围转换为字符串，并从那里开始解析。

一种优雅的方法是将索引范围字符串和源数组一起传递给函数。不幸的是，PHP 范围规则意味着变量范围界限在函数内部不可用，这使得它有点没用。

解决这个问题最简单的方法是引用字符串中的变量，并`eval`它们。其工作方式是:

1.  `$many`与`T_VARIABLE·A`匹配
2.  `4..8`与`···expression`匹配
3.  在`eval`中，`$lower`被定义为字符串`4..8`被`..`分解后的第一个数组索引
4.  `$upper`被定义为本次爆炸的第二个数组索引
5.  这个`eval`字符串的返回值是我们之前使用的原始`array_slice`代码

该宏将匹配以下任何一行:

```
$lower = 4;
$upper = 8;

$few = $many[$lower..$upper];
$few = $many[4..8];
$few = $many[4..$upper];
$few = $many[$lower..8]; 
```

…它们将生成类似如下的代码:

```
$few = eval(
    '$list = ' . '$many' . ';'.
    '$lower = ' . explode('..', '$lower..$upper')[0] . ';' .
    '$upper = ' . explode('..', '$lower..$upper')[1] . ';' .
    'return array_slice($list, $lower, $upper - $lower);'
);

$few = eval(
    '$list = ' . '$many' . ';'.
    '$lower = ' . explode('..', '4..8')[0] . ';' .
    '$upper = ' . explode('..', '4..8')[1] . ';' .
    'return array_slice($list, $lower, $upper - $lower);'
);

$few = eval(
    '$list = ' . '$many' . ';'.
    '$lower = ' . explode('..', '4..$upper')[0] . ';' .
    '$upper = ' . explode('..', '4..$upper')[1] . ';' .
    'return array_slice($list, $lower, $upper - $lower);'
);

$few = eval(
    '$list = ' . '$many' . ';'.
    '$lower = ' . explode('..', '$lower..8')[0] . ';' .
    '$upper = ' . explode('..', '$lower..8')[1] . ';' .
    'return array_slice($list, $lower, $upper - $lower);'
); 
```

考虑到变量作用域和解析器的限制，这可能是我们能做到的最干净的了。这不是最好看的 PHP，但它能完成任务。与此同时，我们可以用`$many[4..8]` …

如果你觉得这很有趣，或者对如何使用这个库有有趣的想法，可以考虑在下面留下评论。

## 分享这篇文章