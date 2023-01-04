# 有趣的函数式宏编程

> 原文：<https://www.sitepoint.com/functional-programming-in-php-with-macros/>

我对我之前关于 PHP 宏的文章感到非常兴奋，我认为探索宏和函数式编程的交集会很有趣。

PHP 已经有了很多功能，而面向对象的模式在其生命中出现得相对较晚。尽管如此，PHP 的函数可能很麻烦，尤其是当与可变范围规则结合使用时…

![Assembling lego blocks](img/cbb8c26b1455938c96a387c34b247157.png)

考虑以下 ES6 (JavaScript)代码:

```
let languages = [
    {"name": "JavaScript"},
    {"name": "PHP"},
    {"name": "Ruby"},
];

const prefix = "language: ";

console.log(
    languages.map(
        language => prefix + language.name
    )
); 
```

在这个例子中，我们看到`languages`被定义为一系列编程语言。每种编程语言都有一个常量前缀，并且[产生的数组](https://singlethread.io/post/find-the-intersection-of-two-arrays/)被记录到控制台。

这是我们将要看到的最多的 JavaScript。如果你想了解更多 ES6，查看 BabelJS 的[文档。](http://babeljs.io/docs/learn-es2015)

将此与类似的 PHP 代码进行比较:

```
$languages = [
    ["name" => "JavaScript"],
    ["name" => "PHP"],
    ["name" => "Ruby"],
];

$prefix = "language: ";

var_dump(
    array_map(function($language) use ($prefix) {
        return $prefix . $language;
    }, $languages);
); 
```

它的代码并不多，但也不像 JavaScript 那样清晰和习惯。当我在构建 PHP 的时候，我经常会想念 JavaScript 富于表现力的函数语法。我想试着赢回一些有表现力的语法！

## 入门指南

和上一篇文章一样，我们将使用 [Márcio Almada 的 Yay 库](https://github.com/marcioAlmada/yay)。我们可以安装它:

```
composer require yay/yay:* 
```

我们将把我们的 Yay 代码(非常类似于 PHP，但是有宏支持)放在以`.yphp`结尾的文件中，并用以下代码将它们编译成普通的 PHP:

```
vendor/bin/yay before.yphp > after.php 
```

## 简单的解决办法

我想尝试的第一件事是用更类似于 JavaScript 的语法映射一组事物。我想要这样的东西:

```
$list->map(function($item) {
    return strtoupper($item);
}); 
```

我们已经可以做到这一点，但它需要创建一个特殊的 PHP 类。我们不能在这个特殊的类上使用普通的数组函数，所以这个类还需要有方法在原生 PHP 数组之间进行转换。

![AIN'T NOBODY GOT TIME FO' THAT!](img/8826cc2196def0af15ec5bf4dad5ceff.png)

相反，我们来做一个宏:

```
macro {
    T_VARIABLE·A->map(···expression)
} >> {
    array_map(···expression, T_VARIABLE·A)
} 
```

这个宏将匹配任何看起来像变量的东西，后跟`->map(...)`；并将其转换为`array_map(...)`替代方案。所以，如果我们输入这个代码:

```
$languages = [
    ["name" => "JavaScript"],
    ["name" => "PHP"],
    ["name" => "Ruby"],
];

var_dump(
    $languages->map(function($language) {
        return strtoupper($language["name"]);
    })
); 
```

…它将编译成:

```
$languages = [
    ["name" => "JavaScript"],
    ["name" => "PHP"],
    ["name" => "Ruby"],
];

var_dump(
    array_map(function($language) {
        return strtoupper($language["name"]);
    }, $languages)
); 
```

*如果你还没有，现在是一个很好的时机来阅读前一篇文章，它解释了这段代码是如何工作的。如果你对目前所看到的不满意，你绝对不应该尝试下面的例子……*

## 复杂的解决方案

我们有了一个好的开始！JavaScript 和 PHP 之间仍然有一个显著的区别:变量范围。如果我们想使用这个前缀，我们必须将它绑定到给`array_map(...)`的回调中。让我们解决这个问题。

首先，我们将重新定义上次创建的 stringify 宏:

```
macro {
    →(···expression)
} >> {
    ··stringify(···expression)
} 
```

这将把`→(...)`替换为一个字符串版本，该字符串版本是 parens 之间匹配的任何内容。然后我们需要定义一个匹配箭头函数语法的模式:

```
macro {
    T_VARIABLE·A->map(
        T_VARIABLE·parameter1
        T_DOUBLE_ARROW·arrow
        ···expression
    )
} >> {
    // ...replacement definition
} 
```

这将匹配`$value => $return`。我们还需要匹配接受密钥的变体:

```
macro {
    T_VARIABLE·A->map(
        T_VARIABLE·parameter1, T_VARIABLE·parameter2
        T_DOUBLE_ARROW·arrow
        ···expression
    )
} >> {
    // ...replacement definition
} 
```

这将匹配`$key, $value => $return`。我们可以将这些组合成一个匹配器，但这将很快变得复杂。所以，我选择了两个宏。

下一步是捕获变量上下文和映射函数:

```
macro {
    // ...capture pattern
} >> {
    eval('
        $context = get_defined_vars();

        return array_map(
            function($key, $value) use ($context) {
                // ...give context to map function
            },
            array_keys(' . →(T_VARIABLE·A) . '),
            array_values(' . →(T_VARIABLE·A) . ')
        );
    ')
} 
```

我们使用`get_defined_vars()`函数将所有当前定义的变量存储在一个数组中。这些的范围与执行`eval`的范围相同。然后，我们将上下文作为绑定参数传递给闭包。我们还为源列表提供键和值。

这是`array_map`工作方式的一个奇怪的副作用。我们有点依赖于`array_keys`和`array_values`的返回值的顺序是相同的，但是这是一个非常安全的赌注。

最后，我们需要解决如何在对`array_map`的调用中使用上下文:

```
macro {
    // ...capture pattern
} >> {
    eval('
        $context = get_defined_vars();

        return array_map(
            function($key, $value) use ($context) {
                extract($context);
                ' . →(T_VARIABLE·parameter1) . ' = $value;
                return (' . →(···expression) . ');
            },
            array_keys(' . →(T_VARIABLE·A) . '),
            array_values(' . →(T_VARIABLE·A) . ')
        );
    ')
} 
```

我们使用`extract`函数为`$context`数组中的每个键和值创建局部变量。这意味着在`array_map`范围之外的每个变量在里面也是可用的。我们返回`···expression`的值，就在重新设置用户定义的值变量之后。

对于这个的键/值变量，我们需要设置这两个变量。两种变体的完整宏都是:

```
macro {
    T_VARIABLE·A->map(
        T_VARIABLE·parameter1
        T_DOUBLE_ARROW·arrow
        ···expression
    )
} >> {
    eval('
        $context = get_defined_vars();

        return array_map(
            function($key, $value) use ($context) {
                extract($context);

                ' . →(T_VARIABLE·parameter1) . ' = $value;

                return (' . →(···expression) . ');
            },
            array_keys(' . →(T_VARIABLE·A) . '),
            array_values(' . →(T_VARIABLE·A) . ')
        );
    ')
}

macro {
    T_VARIABLE·A->map(
        T_VARIABLE·parameter1, T_VARIABLE·parameter2
        T_DOUBLE_ARROW·arrow
        ···expression
    )
} >> {
    eval('
        $context = get_defined_vars();

        return array_map(
            function($key, $value) use ($context) {
                extract($context);

                ' . →(T_VARIABLE·parameter1) . ' = $key;
                ' . →(T_VARIABLE·parameter2) . ' = $value;

                return (' . →(···expression) . ');
            },
            array_keys(' . →(T_VARIABLE·A) . '),
            array_values(' . →(T_VARIABLE·A) . ')
        );
    ')
} 
```

这些宏将接受以下输入:

```
$languages = [
    ["name" => "JavaScript"],
    ["name" => "PHP"],
    ["name" => "Ruby"],
];

$prefix = "language: ";

var_dump(
    $languages->map(
        $language => $prefix . $language["name"]
    )
);

var_dump(
    $languages->map(
        $key, $value => ($key + 1) . ": " . $value["name"]
    )
); 
```

…并生成类似如下的代码:

```
$languages = [
    ["name" => "JavaScript"],
    ["name" => "PHP"],
    ["name" => "Ruby"],
];

$prefix = "language: ";

var_dump(
    eval('
        $context = get_defined_vars();

        return array_map(
            function($key, $value) use ($context) {
                extract($context);
                ' . '$language' . ' = $value;
                return (' . '$prefix . $language["name"]' . ');
            },
            array_keys(' . '$languages' . '),
            array_values(' . '$languages' . ')
        );
    ')
);

var_dump(
    eval('
        $context = get_defined_vars();

        return array_map(
            function($key, $value) use ($context) {
                extract($context);
                ' . '$key' . ' = $key;
                ' . '$value' . ' = $value;
                return (' . '($key + 1) . ": " . $value["name"] . ' . ');
            },
            array_keys(' . '$languages' . '),
            array_values(' . '$languages' . ')
        );
    ')
); 
```

再一次，我们可以看到生成的代码并不是最漂亮的。但是它确实克服了一个相当烦人的变量范围限制，并且允许传统的`array_map`方法的简写语法。

你印象深刻吗？反感？请在下面的评论中告诉我们！

## 分享这篇文章