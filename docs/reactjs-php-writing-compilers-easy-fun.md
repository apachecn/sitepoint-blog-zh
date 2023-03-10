# PHP 中的 ReactJS:编写编译器既简单又有趣！

> 原文：<https://www.sitepoint.com/reactjs-php-writing-compilers-easy-fun/>

我以前用的分机叫 XHP。它支持 HTML-in-PHP 语法来生成前端标记。我最近接触了它，惊讶地发现现代 PHP 版本不再正式支持它。

因此，我决定使用一个基本的状态机编译器来实现它的用户域版本。看起来和你一起做这个项目会很有趣！

> 本教程的代码可以在 [Github](https://github.com/assertchris-tutorials/sitepoint-jsx-in-php) 上找到。

![Abstract image of blocks coming together](img/603bf0a11ceb78519cc429b989672ec4.png)

## 创建编译器

许多开发人员避免编写他们自己的编译器或解释器，认为这个主题太复杂或太难正确探索。我以前也有这种感觉。编译器可能很难做好，主题*可能*非常复杂和困难。但是，这并不意味着你不能做一个编译器。

做编译器就像做三明治。任何人都可以得到原料并把它们放在一起。你可以做一个三明治。你也可以去厨师学校学习如何做世界上最好吃的三明治。你可以学习多年的三明治制作艺术，人们可以在其他地方谈论你的三明治。你不会让三明治制作的广度和复杂性阻止你做你的第一个三明治吧？

编译器(和解释器)从简单的字符串操作和临时变量开始。当它们足够受欢迎(或足够慢)时，专家就可以介入；用独角兽的眼泪和冷嘲热讽代替字符串操作和临时变量。

在基本层面上，编译器获取一串代码，并通过几个步骤运行它:

1.  代码被分割成符号——有意义的字符和子字符串——编译器将使用它们来获得意义。语句`if (isEmergency) alert("there is an emergency")`可以被认为包含像`if`、`isEmergency`、`alert`和`"there is an emergency"`这样的标记；这些对编译器来说都有意义。

    第一步是将整个源代码分割成这些有意义的部分，这样编译器就可以开始按照逻辑层次来组织它们，从而知道如何处理代码。

2.  这些标记被安排到逻辑层次结构(有时称为抽象语法树)中，它表示程序中需要做什么。前面的语句可以理解为“如果条件(`isEmergency`)评估为真，则进行计算”。如果是，运行带有参数(`"there is an emergency"`)”的函数(`alert`)。

使用这种层次结构，代码可以立即执行(在解释器或虚拟机的情况下)或翻译成其他语言(在 CoffeeScript 和 TypeScript 等语言的情况下，它们都是编译到 Javascript 的语言)。

在我们的例子中，我们希望保持大部分 PHP 语法，但是我们也希望在上面添加我们自己的一点语法。我们可以创建一个全新的解释器……或者我们可以预处理新的语法，将其编译成语法上有效的 PHP 代码。

> 我曾经在之前写过关于[预处理 PHP 的文章，这是我最喜欢的添加新语法的方法。在这种情况下，我们需要编写一个更复杂的脚本；因此，我们将偏离之前添加新语法的方式。](https://www.sitepoint.com/how-to-make-modern-php-more-modern-with-preprocessing/)

### 生成令牌

让我们创建一个函数来将代码分割成令牌。它是这样开始的:

```
function tokens($code) {
    $tokens = [];

    $length = strlen($code);
    $cursor = 0;

    while ($cursor < $length) {
        if ($code[$cursor] === "{") {
            print "ATTRIBUTE STARTED ({$cursor})" . PHP_EOL;
        }

        if ($code[$cursor] === "}") {
            print "ATTRIBUTE ENDED ({$cursor})" . PHP_EOL;
        }

        if ($code[$cursor] === "<") {
            print "ELEMENT STARTED ({$cursor})" . PHP_EOL;
        }

        if ($code[$cursor] === ">") {
            print "ELEMENT ENDED ({$cursor})" . PHP_EOL;
        }

        $cursor++;
    }
}

$code = '
    <?php

    $classNames = "foo bar";
    $message = "hello world";

    $thing = (
        <div
            className={() => { return "outer-div"; }}
            nested={<span className={"nested-span"}>with text</span>}
        >
            a bit of text before
            <span>
                {$message} with a bit of extra text
            </span>
            a bit of text after
        </div>
    );
';

tokens($code);

// ELEMENT STARTED (5)
// ELEMENT STARTED (95)
// ATTRIBUTE STARTED (122)
// ELEMENT ENDED (127)
// ATTRIBUTE STARTED (129)
// ATTRIBUTE ENDED (151)
// ATTRIBUTE ENDED (152)
// ATTRIBUTE STARTED (173)
// ELEMENT STARTED (174)
// ATTRIBUTE STARTED (190)
// ATTRIBUTE ENDED (204)
// ELEMENT ENDED (205)
// ELEMENT STARTED (215)
// ELEMENT ENDED (221)
// ATTRIBUTE ENDED (222)
// ELEMENT ENDED (232)
// ELEMENT STARTED (279)
// ELEMENT ENDED (284)
// ATTRIBUTE STARTED (302)
// ATTRIBUTE ENDED (311)
// ELEMENT STARTED (350)
// ELEMENT ENDED (356)
// ELEMENT STARTED (398)
// ELEMENT ENDED (403) 
```

> 这是来自`tokens-1.php`

我们有了一个好的开始。通过单步执行代码，我们可以检查每个字符是什么(并识别对我们重要的字符)。例如，我们看到，当我们在索引 5 处遇到一个`<`字符时，第一个元素打开。第一个元素在索引 210 处结束。

不幸的是，第一个开口与`<?php`的匹配不正确。在我们的新语法中，这不是一个元素，所以我们必须阻止代码挑选出它:

```
preg_match("#^</?[a-zA-Z]#", substr($code, $cursor, 3), $matchesStart);

if (count($matchesStart)) {
    print "ELEMENT STARTED ({$cursor})" . PHP_EOL;
}

// ...

// ELEMENT STARTED (95)
// ATTRIBUTE STARTED (122)
// ELEMENT ENDED (127)
// ATTRIBUTE STARTED (129)
// ATTRIBUTE ENDED (151)
// ATTRIBUTE ENDED (152)
// ATTRIBUTE STARTED (173)
// ELEMENT STARTED (174)
// ... 
```

> 这是来自`tokens-2.php`

我们的新代码不是只检查当前字符，而是检查三个字符:如果它们匹配模式`<div`或`</div`，但不匹配`<?php`或`$num1 < $num2`。

还有另一个问题:我们的例子使用了 arrow 函数语法，所以`=>`被匹配为一个元素结束序列。让我们优化匹配元素结束序列的方式:

```
preg_match("#^=>#", substr($code, $cursor - 1, 2), $matchesEqualBefore);
preg_match("#^>=#", substr($code, $cursor, 2), $matchesEqualAfter);

if ($code[$cursor] === ">" && !$matchesEqualBefore && !$matchesEqualAfter) {
    print "ELEMENT ENDED ({$cursor})" . PHP_EOL;
}

// ...

// ELEMENT STARTED (95)
// ATTRIBUTE STARTED (122)
// ATTRIBUTE STARTED (129)
// ATTRIBUTE ENDED (151)
// ATTRIBUTE ENDED (152)
// ATTRIBUTE STARTED (173)
// ELEMENT STARTED (174)
// ... 
```

> 这是来自`tokens-3.php`

与 JSX 一样，属性允许动态值(即使这些值是嵌套的 JSX 元素)是有好处的。有几种方法可以做到这一点，但我更喜欢将所有属性视为文本，并递归地标记它们。要做到这一点，我们需要一种状态机来跟踪我们在一个元素和属性中有多少层。如果我们在一个元素标签中，我们应该把顶层的`{…}`作为一个字符串属性值，忽略后面的括号。类似地，如果我们在属性内部，我们应该忽略嵌套元素的开始和结束序列:

```
function tokens($code) {
    $tokens = [];

    $length = strlen($code);
    $cursor = 0;

    $elementLevel = 0;
    $elementStarted = null;
    $elementEnded = null;

    $attributes = [];
    $attributeLevel = 0;
    $attributeStarted = null;
    $attributeEnded = null;

    while ($cursor < $length) {
        $extract = trim(substr($code, $cursor, 5)) . "...";

        if ($code[$cursor] === "{" && $elementStarted !== null) {
            if ($attributeLevel === 0) {
                print "ATTRIBUTE STARTED ({$cursor}, {$extract})" . PHP_EOL;
                $attributeStarted = $cursor;
            }

            $attributeLevel++;
        }

        if ($code[$cursor] === "}" && $elementStarted !== null) {
            $attributeLevel--;

            if ($attributeLevel === 0) {
                print "ATTRIBUTE ENDED ({$cursor})" . PHP_EOL;
                $attributeEnded = $cursor;
            }
        }

        preg_match("#^</?[a-zA-Z]#", substr($code, $cursor, 3), $matchesStart);

        if (count($matchesStart) && $attributeLevel < 1) {
            print "ELEMENT STARTED ({$cursor}, {$extract})" . PHP_EOL;

            $elementLevel++;
            $elementStarted = $cursor;
        }

        preg_match("#^=>#", substr($code, $cursor - 1, 2), $matchesEqualBefore);
        preg_match("#^>=#", substr($code, $cursor, 2), $matchesEqualAfter);

        if (
            $code[$cursor] === ">"
            && !$matchesEqualBefore && !$matchesEqualAfter
            && $attributeLevel < 1
        ) {
            print "ELEMENT ENDED ({$cursor})" . PHP_EOL;

            $elementLevel--;
            $elementEnded = $cursor;
        }

        if ($elementStarted && $elementEnded) {
            // TODO

            $elementStarted = null;
            $elementEnded = null;
        }

        $cursor++;
    }
}

// ...

// ELEMENT STARTED (95, <div...)
// ATTRIBUTE STARTED (122, {() =...)
// ATTRIBUTE ENDED (152)
// ATTRIBUTE STARTED (173, {<spa...)
// ATTRIBUTE ENDED (222)
// ELEMENT ENDED (232)
// ELEMENT STARTED (279, <span...)
// ELEMENT ENDED (284)
// ELEMENT STARTED (350, </spa...)
// ELEMENT ENDED (356)
// ELEMENT STARTED (398, </div...)
// ELEMENT ENDED (403) 
```

> 这是来自`tokens-4.php`

我们添加了新的`$attributeLevel`、`$attributeStarted`和`$attributeEnded`变量；跟踪我们在属性嵌套中的深度，以及顶级的开始和结束位置。具体来说，如果我们在顶层，当属性值开始或结束时，我们捕获当前的光标位置。稍后，我们将使用它来提取字符串属性值并用占位符替换它。

我们也开始捕获`$elementStarted`和`$elementEnded`(其中`$elementLevel`扮演类似于`$attributeLevel`的角色)，这样我们就可以捕获一个完整的元素开始或结束标签。在这种情况下，`$elementEnded`指的不是结束标记，而是开始标记的结束字符序列。结束标记被视为完全独立的标记…

在当前光标位置后提取一小段子字符串后，我们可以看到元素和属性在我们期望的位置开始和结束。嵌套的控件结构和元素被捕获为字符串，只留下顶级元素、非属性嵌套元素和属性值。

让我们将这些标记打包，将属性与定义它们的标签相关联:

```
function tokens($code) {
    $tokens = [];

    $length = strlen($code);
    $cursor = 0;

    $elementLevel = 0;
    $elementStarted = null;
    $elementEnded = null;

    $attributes = [];
    $attributeLevel = 0;
    $attributeStarted = null;
    $attributeEnded = null;

    $carry = 0;

    while ($cursor < $length) {
        if ($code[$cursor] === "{" && $elementStarted !== null) {
            if ($attributeLevel === 0) {
                $attributeStarted = $cursor;
            }

            $attributeLevel++;
        }

        if ($code[$cursor] === "}" && $elementStarted !== null) {
            $attributeLevel--;

            if ($attributeLevel === 0) {
                $attributeEnded = $cursor;
            }
        }

        if ($attributeStarted && $attributeEnded) {
            $position = (string) count($attributes);
            $positionLength = strlen($position);

            $attribute = substr(
                $code, $attributeStarted + 1, $attributeEnded - $attributeStarted - 1
            );

            $attributes[$position] = $attribute;

            $before = substr($code, 0, $attributeStarted + 1);
            $after = substr($code, $attributeEnded);

            $code = $before . $position . $after;

            $cursor = $attributeStarted + $positionLength + 2 /* curlies */;
            $length = strlen($code);

            $attributeStarted = null;
            $attributeEnded = null;

            continue;
        }

        preg_match("#^</?[a-zA-Z]#", substr($code, $cursor, 3), $matchesStart);

        if (count($matchesStart) && $attributeLevel < 1) {
            $elementLevel++;
            $elementStarted = $cursor;
        }

        preg_match("#^=>#", substr($code, $cursor - 1, 2), $matchesEqualBefore);
        preg_match("#^>=#", substr($code, $cursor, 2), $matchesEqualAfter);

        if (
            $code[$cursor] === ">"
            && !$matchesEqualBefore && !$matchesEqualAfter
            && $attributeLevel < 1
        ) {
            $elementLevel--;
            $elementEnded = $cursor;
        }

        if ($elementStarted !== null && $elementEnded !== null) {
            $distance = $elementEnded - $elementStarted;

            $carry += $cursor;

            $before = trim(substr($code, 0, $elementStarted));
            $tag = trim(substr($code, $elementStarted, $distance + 1));
            $after = trim(substr($code, $elementEnded + 1));

            $token = ["tag" => $tag, "started" => $carry];

            if (count($attributes)) {
                $token["attributes"] = $attributes;
            }

            $tokens[] = $before;
            $tokens[] = $token;

            $attributes = [];

            $code = $after;
            $length = strlen($code);
            $cursor = 0;

            $elementStarted = null;
            $elementEnded = null;

            continue;
        }

        $cursor++;
    }

    return $tokens;
}

$code = '
    <?php

    $classNames = "foo bar";
    $message = "hello world";

    $thing = (
        <div
            className={() => { return "outer-div"; }}
            nested={<span className={"nested-span"}>with text</span>}
        >
            a bit of text before
            <span>
                {$message} with a bit of extra text
            </span>
            a bit of text after
        </div>
    );
';

tokens($code);

// Array
// (
//     [0] => <?php
//
//     $classNames = "foo bar";
//     $message = "hello world";
//
//     $thing = (
//     [1] => Array
//         (
//             [tag] => <div className={0} nested={1}>
//             [started] => 157
//             [attributes] => Array
//                 (
//                     [0] => () => { return "outer-div"; }
//                     [1] => <span className={"nested-span"}>with text</span>
//                 )
//
//         )
//
//     [2] => a bit of text before
//     [3] => Array
//         (
//             [tag] => <span>
//             [started] => 195
//         )
//
//     [4] => {$message} with a bit of extra text
//     [5] => Array
//         (
//             [tag] => </span>
//             [started] => 249
//         )
//
//     [6] => a bit of text after
//     [7] => Array
//         (
//             [tag] => </div>
//             [started] => 282
//         )
//
// ) 
```

> 这是来自`tokens-5.php`

这里发生了很多事情，但都只是前一版本的自然发展。我们使用捕获的属性开始和结束位置来提取整个属性值作为一个大字符串。然后，我们用数字占位符替换每个捕获的属性，并重置代码字符串和光标位置。

当每个元素关闭时，我们将该元素打开后的所有属性关联起来，并根据标记(及其占位符)、属性和起始位置创建一个单独的数组标记。结果可能有点难以阅读，但它在捕捉代码意图方面是准确的。

那么，我们该如何处理这些嵌套的元素属性呢？

```
function tokens($code) {
    // ...

    while ($cursor < $length) {
        // ...

        if ($elementStarted !== null && $elementEnded !== null) {
            // ...

            foreach ($attributes as $key => $value) {
                $attributes[$key] = tokens($value);
            }

            if (count($attributes)) {
                $token["attributes"] = $attributes;
            }

            // ...
        }

        $cursor++;
    }

    $tokens[] = trim($code);

    return $tokens;
}

// ...

// Array
// (
//     [0] => <?php
//
//     $classNames = "foo bar";
//     $message = "hello world";
//
//     $thing = (
//     [1] => Array
//         (
//             [tag] => <div className={0} nested={1}>
//             [started] => 157
//             [attributes] => Array
//                 (
//                     [0] => Array
//                         (
//                             [0] => () => { return "outer-div"; }
//                         )
//
//                     [1] => Array
//                         (
//                             [1] => Array
//                                 (
//                                     [tag] => <span className={0}>
//                                     [started] => 19
//                                     [attributes] => Array
//                                         (
//                                             [0] => Array
//                                                 (
//                                                     [0] => "nested-span"
//                                                 )
//
//                                         )
//
//                                 )
//
//                            [2] => with text
//                            [3] => Array
//                                (
//                                    [tag] => </span>
//                                    [started] => 34
//                                )
//                         )
//
//                 )
//
//         )
//
// ... 
```

> 这是来自`tokens-5.php`(修改)

在关联属性之前，我们循环遍历它们，并用递归函数调用来标记它们的值。我们还需要将任何剩余的文本(不在属性或元素标记中)添加到 tokens 数组中，否则它将被忽略。

结果是一个令牌列表，其中可以有嵌套的令牌列表。几乎已经是 AST 了。

### 组织令牌

让我们将这个令牌列表转换成更像 AST 的东西。第一步是排除与开始标记匹配的结束标记。我们需要识别哪些令牌是标签:

```
function nodes($tokens) {
    $cursor = 0;
    $length = count($tokens);

    while ($cursor < $length) {
        $token = $tokens[$cursor];

        if (is_array($token)) {
            print $token["tag"] . PHP_EOL;
        }

        $cursor++;
    }
}

$tokens = [
    0 => '<?php

    $classNames = "foo bar";
    $message = "hello world";

    $thing = (',
    1 => [
        'tag' => '<div className={0} nested={1}>',
        'started' => 157,
        'attributes' => [
            0 => [
                0 => '() => { return "outer-div"; }',
            ],
            1 => [
                1 => [
                    'tag' => '<span className={0}>',
                    'started' => 19,
                    'attributes' => [
                        0 => [
                            0 => '"nested-span"',
                        ],
                    ],
                ],
                2 => 'with text</span>',
            ],
        ],
    ],
    2 => 'a bit of text before',
    3 => [
        'tag' => '<span>',
        'started' => 195,
    ],
    4 => '{$message} with a bit of extra text',
    5 => [
        'tag' => '</span>',
        'started' => 249,
    ],
    6 => 'a bit of text after',
    7 => [
        'tag' => '</div>',
        'started' => 282,
    ],
    8 => ');',
];

nodes($tokens);

// <div className={0} nested={1}>
// <span>
// </span>
// </div> 
```

> 这是来自`nodes-1.php`

我已经从上一个令牌脚本中提取了一个令牌列表，这样我就不必再运行和调试那个函数了。在一个循环中，类似于我们在标记化过程中使用的循环，我们只打印非属性元素标记。让我们弄清楚它们是开始标记还是结束标记，以及结束标记是否与开始标记匹配:

```
function nodes($tokens) {
    $cursor = 0;
    $length = count($tokens);

    while ($cursor < $length) {
        $token = $tokens[$cursor];

        if (is_array($token) && $token["tag"][1] !== "/") {
            preg_match("#^<([a-zA-Z]+)#", $token["tag"], $matches);

            print "OPENING {$matches[1]}" . PHP_EOL;
        }

        if (is_array($token) && $token["tag"][1] === "/") {
            preg_match("#^</([a-zA-Z]+)#", $token["tag"], $matches);

            print "CLOSING {$matches[1]}" . PHP_EOL;
        }

        $cursor++;
    }

    return $tokens;
}

// ...

// OPENING div
// OPENING span
// CLOSING span
// CLOSING div 
```

> 这是来自`nodes-1.php`(修改)

现在我们知道了哪些标签是开始标签，哪些是结束标签；我们可以使用引用变量来构建一棵树:

```
function nodes($tokens) {
    $nodes = [];
    $current = null;

    $cursor = 0;
    $length = count($tokens);

    while ($cursor < $length) {
        $token =& $tokens[$cursor];

        if (is_array($token) && $token["tag"][1] !== "/") {
            preg_match("#^<([a-zA-Z]+)#", $token["tag"], $matches);

            if ($current !== null) {
                $token["parent"] =& $current;
                $current["children"][] =& $token;
            } else {
                $token["parent"] = null;
                $nodes[] =& $token;
            }

            $current =& $token;
            $current["name"] = $matches[1];
            $current["children"] = [];

            if (isset($current["attributes"])) {
                foreach ($current["attributes"] as $key => $value) {
                    $current["attributes"][$key] = nodes($value);
                }

                $current["attributes"] = array_map(function($item) {

                    foreach ($item as $value) {
                        if (isset($value["tag"])) {
                            return $value;
                        }
                    }

                    foreach ($item as $value) {
                        if (!empty($value["token"])) {
                            return $value;
                        }
                    }

                    return null;

                }, $current["attributes"]);
            }
        }

        else if (is_array($token) && $token["tag"][1] === "/") {
            preg_match("#^</([a-zA-Z]+)#", $token["tag"], $matches);

            if ($current === null) {
                throw new Exception("no open tag");
            }

            if ($matches[1] !== $current["name"]) {
                throw new Exception("no matching open tag");
            }

            if ($current !== null) {
                $current =& $current["parent"];
            }
        }

        else if ($current !== null) {
            array_push($current["children"], [
                "parent" => &$current,
                "token" => &$token,
            ]);
        }

        else {
            array_push($nodes, [
                "token" => $token,
            ]);
        }

        $cursor++;
    }

    return $nodes;
}

// ...

// Array
// (
//     [0] => Array
//         (
//             [token] => <?php
//
//     $classNames = "foo bar";
//     $message = "hello world";
//
//     $thing = (
//         )
//
//     [1] => Array
//         (
//             [tag] => <div className={0} nested={1}>
//             [started] => 157
//             [attributes] => Array
//                 (
//                     [0] => Array
//                         (
//                             [token] => () => { return "outer-div"; }
//                         )
//
//                     [1] => Array
//                         (
//                             [tag] => <span className={0}>
//                             [started] => 19
//                             [attributes] => Array
//                                 (
//                                     [0] => Array
//                                         (
//                                             [token] => "nested-span"
//                                         )
//
//                                 )
//
//                             [parent] =>
//                             [name] => span
//                             [children] => Array
//                                 (
//                                     [0] => Array
//                                         (
//                                             [parent] => *RECURSION*
//                                             [token] => with text
//                                         )
//
//                                 )
//
//                         )
//
//                 )
//
//             [parent] =>
//             [name] => div
//             [children] => Array
//                 (
//                     [0] => Array
//                         (
//                             [parent] => *RECURSION*
//                             [token] => a bit of text before
//                         )
//
//                     [1] => Array
//                         (
//                             [tag] => <span>
//                             [started] => 195
//                             [parent] => *RECURSION*
//                             [name] => span
//                             [children] => Array
//                                 (
//                                     [0] => Array
//                                         (
//                                             [parent] => *RECURSION*
//                                             [token] => {$message} with ...
//                                         )
//
//                                 )
//
//                         )
//
//                     [2] => Array
//                         (
//                             [parent] => *RECURSION*
//                             [token] => a bit of text after
//                         )
//
//                 )
//
//         )
//
//     [2] => Array
//         (
//             [token] => );
//         )
//
// ) 
```

> 这是来自`nodes-2.php`

花点时间研究一下这里发生的事情。我们创建一个`$nodes`数组，在其中存储新的、有组织的节点结构。我们还有一个`$current`变量，我们通过引用将每个开始标记节点分配给它。这样，我们可以逐步进入每个元素(开始标记、结束标记以及它们之间的标记)；以及在遇到结束标记时后退一步。

> 引用是最棘手的部分，但是它们对于保持代码相对简单是必不可少的。我是说，没那么简单。但是比非参考版本简单多了。

就如何递归工作而言，我们没有最干净的函数。因此，当我们通过`nodes`函数传递属性时，我们有时会在嵌套的标签属性旁边得到空的“令牌”属性。因此，我们需要过滤属性，以便在返回非空令牌属性值之前首先尝试返回嵌套标记。这可能需要清理一下…

### 重写代码

现在代码已经整齐地排列在一个层次结构或 AST 中，我们可以将其重写为有效的 PHP 代码。让我们从编写字符串标记(不嵌套在元素中)开始，然后格式化生成的代码:

```
function parse($nodes) {
    $code = "";

    foreach ($nodes as $node) {
        if (isset($node["token"])) {
            $code .= $node["token"] . PHP_EOL;
        }
    }

    return $code;
}

$nodes = [
    0 => [
        'token' => '<?php

        $classNames = "foo bar";
        $message = "hello world";

        $thing = (',
    ],
    1 => [
        'tag' => '<div className={0} nested={1}>',
        'started' => 157,
        'attributes' => [
            0 => [
                'token' => '() => { return "outer-div"; }',
            ],
            1 => [
                'tag' => '<span className={0}>',
                'started' => 19,
                'attributes' => [
                    0 => [
                        'token' => '"nested-span"',
                    ],
                ],
                'name' => 'span',
                'children' => [
                    0 => [
                        'token' => 'with text',
                    ],
                ],
            ],
        ],
        'name' => 'div',
        'children' => [
            0 => [
                'token' => 'a bit of text before',
            ],
            1 => [
                'tag' => '<span>',
                'started' => 195,
                'name' => 'span',
                'children' => [
                    0 => [
                        'token' => '{$message} with a bit of extra text',
                    ],
                ],
            ],
            2 => [
                'token' => 'a bit of text after',
            ],
        ],
    ],
    2 => [
        'token' => ');',
    ],
];

parse($nodes);

// <?php
//
// $classNames = "foo bar";
// $message = "hello world";
//
// $thing = (
// ); 
```

> 这是来自`parser-1.php`

我已经复制了从前一个脚本中提取的节点，所以我们不必再次调试或重用那个函数。让我们也处理一下元素:

```
require __DIR__ . "/vendor/autoload.php";

function parse($nodes) {
    $code = "";

    foreach ($nodes as $node) {
        if (isset($node["token"])) {
            $code .= $node["token"] . PHP_EOL;
        }

        if (isset($node["tag"])) {
            $props = [];
            $attributes = [];
            $elements = [];

            if (isset($node["attributes"])) {
                foreach ($node["attributes"] as $key => $value) {
                    if (isset($value["token"])) {
                        $attributes["attr_{$key}"] = $value["token"];
                    }

                    if (isset($value["tag"])) {
                        $elements[$key] = true;
                        $attributes["attr_{$key}"] = parse([$value]);
                    }
                }
            }

            preg_match_all("#([a-zA-Z]+)={([^}]+)}#", $node["tag"], $dynamic);
            preg_match_all("#([a-zA-Z]+)=[']([^']+)[']#", $node["tag"], $static);

            if (count($dynamic[0])) {
                foreach($dynamic[1] as $key => $value) {
                    $props["{$value}"] = $attributes["attr_{$key}"];
                }
            }

            if (count($static[1])) {
                foreach($static[1] as $key => $value) {
                    $props["{$value}"] = $static[2][$key];
                }
            }

            $code .= "pre_" . $node["name"] . "([" . PHP_EOL;

            foreach ($props as $key => $value) {
                $code .= "'{$key}' => {$value}," . PHP_EOL;
            }

            $code .= "])" . PHP_EOL;
        }
    }

    $code = Pre\Plugin\expand($code);
    $code = Pre\Plugin\formatCode($code);

    return $code;
}

// ...

// <?php
//
// $classNames = "foo bar";
// $message = "hello world";
//
// $thing = (
//     pre_div([
//         'className' => function () {
//             return "outer-div";
//         },
//         'nested' => pre_span([
//             'className' => "nested-span",
//         ]),
//     ])
// ); 
```

> 这是来自`parser-2.php`

当我们找到一个标记节点时，我们循环遍历属性并构建一个新的属性数组，该数组要么是来自标记节点的文本，要么是来自标记节点的已解析标记。这种递归处理了属性是嵌套元素的可能性。我们的正则表达式只处理用单引号引起的属性(为了简单起见)。您可以随意创建更全面的表达式，处理更复杂的属性语法和值。

我安装了`pre/short-closures`，这样箭头函数将扩展为一个常规函数:

```
composer require pre/short-closures 
```

这里还有一个句柄 PSR-2 格式化函数，所以我们的代码是按照标准格式化的。

最后，我们需要解决儿童问题:

```
require __DIR__ . "/vendor/autoload.php";

function parse($nodes) {
    $code = "";

    foreach ($nodes as $node) {
        if (isset($node["token"])) {
            $code .= $node["token"] . PHP_EOL;
        }

        if (isset($node["tag"])) {
            // ...

            $children = [];

            foreach ($node["children"] as $child) {
                if (isset($child["tag"])) {
                    $children[] = parse([$child]);
                }

                else {
                    $children[] = "\"" . addslashes($child["token"]) . "\"";
                }
            }

            $props["children"] = $children;

            $code .= "pre_" . $node["name"] . "([" . PHP_EOL;

            foreach ($props as $key => $value) {
                if ($key === "children") {
                    $code .= "\"children\" => [" . PHP_EOL;

                    foreach ($children as $child) {
                        $code .= "{$child}," . PHP_EOL;
                    }

                    $code .= "]," . PHP_EOL;
                }

                else {
                    $code .= "\"{$key}\" => {$value}," . PHP_EOL;
                }
            }

            $code .= "])" . PHP_EOL;
        }
    }

    $code = Pre\Plugin\expand($code);
    $code = Pre\Plugin\formatCode($code);

    return $code;
}

// ...

// <?php
//
// $classNames = "foo bar";
// $message = "hello world";
//
// $thing = (
//     pre_div([
//         "className" => function () {
//             return "outer-div";
//         },
//         "nested" => pre_span([
//             "className" => "nested-span",
//             "children" => [
//                 "with text",
//             ],
//         ]),
//         "children" => [
//             "a bit of text before",
//             pre_span([
//                 "children" => [
//                     "{$message} with a bit of extra text",
//                 ],
//             ]),
//             "a bit of text after",
//         ],
//     ])
// ); 
```

> 这是来自`parser-3.php`

我们解析每个标记子代，并直接引用每个标记子代(添加斜杠以说明嵌套的引用)。当我们构建参数数组时。我们循环遍历这些子元素，并将每个子元素添加到我们的`parse`函数最终返回的代码串中。

每个标签被转换成一个等价的`pre_div`或`pre_span`函数。这是一个更大的底层原始元素系统的占位符机制。我们可以通过删除这些函数来证明这一点:

```
require __DIR__ . "/vendor/autoload.php";

function pre_div($props) {
    $code = "<div";

    if (isset($props["className"])) {
        if (is_callable($props["className"])) {
            $class = $props["className"]();
        }

        else {
            $class = $props["className"];
        }

        $code .= " class='{$class}'";
    }

    $code .= ">";

    foreach ($props["children"] as $child) {
        $code .= $child;
    }

    $code .= "</div>";

    return trim($code);
}

function pre_span($props) {
    $code = pre_div($props);
    $code = preg_replace("#^<div#", "<span", $code);
    $code = preg_replace("#div>$#", "span>", $code);

    return $code;
}

function parse($nodes) {
    // ...
}

$nodes = [
    0 => [
        'token' => '<?php

        $classNames = "foo bar";
        $message = "hello world";

        $thing = (',
    ],
    1 => [
        'tag' => '<div className={0} nested={1}>',
        'started' => 157,
        'attributes' => [
            0 => [
                'token' => '() => { return $classNames; }',
            ],
            1 => [
                'tag' => '<span className={0}>',
                'started' => 19,
                'attributes' => [
                    0 => [
                        'token' => '"nested-span"',
                    ],
                ],
                'name' => 'span',
                'children' => [
                    0 => [
                        'token' => 'with text',
                    ],
                ],
            ],
        ],
        'name' => 'div',
        'children' => [
            0 => [
                'token' => 'a bit of text before',
            ],
            1 => [
                'tag' => '<span>',
                'started' => 195,
                'name' => 'span',
                'children' => [
                    0 => [
                        'token' => '{$message} with a bit of extra text',
                    ],
                ],
            ],
            2 => [
                'token' => 'a bit of text after',
            ],
        ],
    ],
    2 => [
        'token' => ');',
    ],
    3 => [
        'token' => 'print $thing;',
    ],
];

eval(substr(parse($nodes), 5));

// <div class='foo bar'>
//     a bit of text before
//     <span>
//         hello world with a bit of extra text
//     </span>
//     a bit of text after
// </div> 
```

> 这是来自`parser-4.php`

我已经修改了输入节点，所以会打印出`$thing`。如果我们实现了一个简单版本的`pre_div`和`pre_span`，那么这段代码就会成功执行。考虑到我们实际上只写了这么少的代码，这真的很难相信…

## 与 Pre 集成

问题是:我们拿这个做什么？

这是一个有趣的实验，但不是很有用。更好的办法是将它放到现有的项目中，并在现实世界中试验基于组件的设计。为此，我扩展了 Pre 以允许定制编译器(以及它已经允许的定制宏定义)。

然后，我将`tokens`、`nodes`和`parse`函数打包成一个可重用的库。做这件事花了很长时间，在我第一次创建函数和使用它们构建一个示例应用程序之间，我对它们做了一些改进。有些改进是小的(比如创建一组 HTML 组件原语)，有些是大的(比如重构表达式和允许定制组件类)。

我不打算回顾所有这些变化，但是我想向您展示一下这个示例应用程序是什么样子的。它以一个服务器脚本开始:

```
use Silex\Application;
use Silex\Provider\SessionServiceProvider;
use Symfony\Component\HttpFoundation\Request;

use App\Component\AddTask;
use App\Component\Page;
use App\Component\TaskList;

$app = new Application();
$app->register(new SessionServiceProvider());

$app->get("/", (Request $request) => {
    $session = $request->getSession();

    $tasks = $session->get("tasks", []);

    return (
        <Page>
            <TaskList>{$tasks}</TaskList>
            <AddTask></AddTask>
        </Page>
    );
});

$app->post("/add", (Request $request) => {
    $session = $request->getSession();

    $id = $session->get("id", 0);
    $tasks = $session->get("tasks", []);

    $tasks[] = [
        "id" => $id++,
        "text" => $request->get("text"),
    ];

    $session->set("id", $id);
    $session->set("tasks", $tasks);

    return $app->redirect("/");
});

$app->get("/remove/{id}", (Request $request, $id) => {
    $session = $request->getSession();

    $tasks = $session->get("tasks", []);

    $tasks = array_filter($tasks, ($task) => {
        return $task["id"] !== (int) $id;
    });

    $session->set("tasks", $tasks);

    return $app->redirect("/");
});

$app->run(); 
```

> 这是来自`server.pre`

该应用程序构建在 Silex 之上，这是一个简洁的微框架。为了加载这个服务器脚本，我有一个索引文件:

```
require __DIR__ . "/../vendor/autoload.php";

Pre\Plugin\process(__DIR__ . "/../server.pre"); 
```

> 这是来自`public/index.php`

…我的这道菜配有:

```
php -S localhost:8080 -t public public/index.php 
```

> 我还没有尝试过在 web 服务器上运行，比如 Apache 或 Nginx。我相信它会以大致相同的方式运行。

服务器脚本从我设置 Silex 服务器开始。我定义了几条路线，第一条路线从当前会话中获取一组任务。如果这个数组没有被定义，我默认它为一个空数组。

我直接传递这些，作为`TaskList`组件的子组件。我已经将这个组件和`AddTask`组件包装在一个`Page`组件中。`Page`组件看起来像这样:

```
namespace App\Component;

use InvalidArgumentException;

class Page
{
    public function render($props)
    {
        assert($this->hasValid($props));

        { $children } = $props;

        return (
            "<!doctype html>".
            <html lang="en">
                <body>
                    {$children}
                </body>
            </html>
        );
    }

    private function hasValid($props)
    {
        if (empty($props["children"])) {
            throw new InvalidArgumentException("page needs content (children)");
        }

        return true;
    }
} 
```

> 这是来自`app/Component/Page.pre`

这个组件并不是绝对必要的，但是我想声明 doctype 并为将来的头部内容(比如样式表和 meta 标记)腾出空间。我析构了`$props`关联数组(使用一些`pre/collections`语法),并将其传递给`<body>`元素。

然后是`TaskList`组件:

```
namespace App\Component;

class TaskList
{
    public function render($props)
    {
        { $children } = $props;

        return (
            <ul className={"task-list"}>
                {$this->children($children)}
            </ul>
        );
    }

    private function children($children)
    {
        if (count($children)) {
            return {$children}->map(($task) => {
                return (
                    <Task id={$task["id"]}>{$task["text"]}</Task>
                );
            });
        }

        return (
            <span>No tasks</span>
        );
    }
} 
```

> 这是来自`app/Component/TaskList.pre`

元素可以有动态属性。事实上，这个库不支持它们有文字(带引号的)属性值。除了这些动态属性值之外，它们的支持也很复杂。我正在定义`className`属性；它支持几种不同的格式:

1.  文字值表达式，如`"task-list"`
2.  一个数组(或者无键的`pre/collection`对象)，比如`["first", "second"]`
3.  关联数组(或键控`pre/collection`对象)，如`["first" => true, "second" => false]`

> 这类似于 ReactJS 中的`className`属性。keyed 或 object 形式使用值的真值来确定键是否附加到元素的`class`属性。

所有默认元素都支持在 Mozilla 开发人员网络文档中定义的非废弃的和非实验性的属性。所有元素都支持它们的`style`属性的关联数组，该数组使用 CSS 样式键的 kebab-case 形式。

最后，所有元素都支持`data-`和`aria-`属性，所有属性值都可以是返回其真值的函数(作为一种惰性加载的形式)。

让我们看看`Task`组件:

```
namespace App\Component;

use InvalidArgumentException;

class Task
{
    public function render($props)
    {
        assert($this->hasValid($props));

        { $children, $id } = $props;

        return (
            <li className={"task"}>
                {$children}
                <a href={"/remove/{$id}"}>remove</a>
            </li>
        );
    }

    private function hasValid($props)
    {
        if (!isset($props["id"])) {
            throw new InvalidArgumentException("task needs id (attribute)");
        }

        if (empty($props["children"])) {
            throw new InvalidArgumentException("task needs text (children)");
        }

        return true;
    }
} 
```

> 这是来自`app/Component/Task.pre`

每个任务都需要一个为每个任务定义的`id`(由`server.pre`定义)，以及一些子任务。子项用于任务的文本表示，并在`TaskList`组件中创建任务的地方定义。

最后，让我们看看`AddTask`组件:

```
namespace App\Component;

class AddTask
{
    public function render($props)
    {
        return (
            <form method={"post"} action={"/add"} className={"add-task"}>
                <input name={"text"} type={"text"} />
                <button type={"submit"}>add</button>
            </form>
        );
    }
} 
```

> 这是来自`app/Component/AddTask.pre`

这个组件演示了一个自关闭的`input`组件，除此之外别无其他。当然，需要定义添加和删除功能(在服务器脚本中):

```
$app->post("/add", (Request $request) => {
    $session = $request->getSession();

    $id = $session->get("id", 1);
    $tasks = $session->get("tasks", []);

    $tasks[] = [
        "id" => $id++,
        "text" => $request->get("text"),
    ];

    $session->set("id", $id);
    $session->set("tasks", $tasks);

    return $app->redirect("/");
});

$app->get("/remove/{id}", (Request $request, $id) => {
    $session = $request->getSession();

    $tasks = $session->get("tasks", []);

    $tasks = array_filter($tasks, ($task) => {
        return $task["id"] !== (int) $id;
    });

    $session->set("tasks", $tasks);

    return $app->redirect("/");
}); 
```

> 这是来自`server.pre`

我们没有在数据库中存储任何东西，但我们可以。这些组件和脚本是示例应用程序的全部内容。这不是一个很大的例子，但是它演示了各种重要的事情，比如组件嵌套和迭代组件呈现。

这也是一个很好的例子，说明了一些不同的前置宏是如何很好地协同工作的；特别是短闭包、集合，以及某些情况下的 async/await。

这里有一张它的 gif 图片。

![Gif of this in action](img/1af662d0df902d8ad38cf9fbc60385b7.png)

## 法克

当我在做这个项目的时候，我重新发现了一个叫做 [Phack](https://github.com/phplang/phack) 的项目，作者是[萨拉·戈莱蒙](https://twitter.com/SaraMG)。这是一个类似于 Pre 的项目，Pre 试图将一个 PHP 超集语言(在这里是 Hack)转换成普通的 PHP。

自述文件列出了 Phack 旨在支持的 Hack 特性及其状态。其中一个特征是 XHP。如果你一直想写黑客代码，但仍然使用标准的 PHP 工具；我建议去看看。我是 Sara 和她的工作的超级粉丝，所以我一定会关注 Phack。

## 摘要

这是一次简单编译器创建的旋风之旅。我们学习了如何构建一个基本的状态机编译器，以及如何让它在常规 PHP 语法中支持类似 HTML 的语法。我们还研究了这在示例应用程序中是如何工作的。

我想鼓励你试试这个。也许你想在 PHP 中加入自己的语法–[,你可以用 Pre](https://www.sitepoint.com/how-to-make-modern-php-more-modern-with-preprocessing/) 来完成。也许你想彻底改变 PHP。我希望本教程已经演示了一种方法，足够好，你觉得可以接受挑战。记住:创建编译器不需要大量的知识或培训。只是简单的字符串操作，和一些试错。

让我们知道你想出了什么！

## 分享这篇文章