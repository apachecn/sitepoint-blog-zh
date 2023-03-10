# 快速提示:向视图传递数据的便利技巧

> 原文：<https://www.sitepoint.com/quick-tip-convenience-hacks-for-passing-data-to-views/>

在基于 MVC 的架构中，使用模板引擎是开发程序不可避免的一部分。通常是这样的:我们准备好数据并传递给视图。在视图中，我们根据我们的布局设计打印它们。

![](img/f108345870c53e9c399e56bc7d1882bd.png)

下面是一个使用[树枝](http://twig.sensiolabs.org/)的基本例子。

```
<?php

// Twig initialization

// Preparing data

$user     = 'user data';
$posts    = 'posts';
$comments = 'comments';

$twig->render('author.page', [

    'user'     => $user,
    'posts'    => $posts,
    'comments' => $comments,

]);

// ... 
```

然而，有时变量的数量可能比这多得多:十个或更多。在这种情况下，我们将有一个很长的变量列表(作为一个关联数组)，传递给各自的模板。它很快变得凌乱和不可读。如果有一种方法可以按名称列出我们需要的东西，并让 PHP 为我们处理其余的事情就好了。嗯…有！

在这篇文章中，我们将讨论一个以某种更方便的方式将已定义的变量传递给视图的技巧:我们可以使用 PHP 自带的 [compact()](http://php.net/manual/en/function.compact.php) 函数，通过按名称引用变量来挑选我们需要的数据:

```
<?php

// Twig initialization

// Preparing data

$user     = 'user data';
$posts    = 'posts';
$comments = 'comments';

$twig->render('author.page', compact('user', 'posts'));

// ... 
```

`compact()`接受一个名称列表，查找具有这些名称的变量(在当前范围内),并将它们作为一个关联数组返回。

有时候，指定我们不需要的变量比指定我们需要的变量更方便。由于 PHP 中没有内置的解决方案来压缩所有的变量(在当前的范围内),其中一些被排除在外，所以我们必须分三步完成。

首先，我们需要用`get_defined_vars()`获取控制器范围内的所有变量:

```
<?php
// ...
$variables = get_defined_vars();
// ... 
```

然后，我们通过使用 PHP 的 [array_diff](http://php.net/manual/en/function.array-diff.php) 函数，从需要的变量名中过滤掉不需要的变量名。

`array_diff`将一个数组与**一个**或**多个**数组进行比较，并返回**第一个数组**中的条目，这些条目是**不存在于**任何其他数组中的。

```
<?php
// ...
$variables = get_defined_vars();
$including = array_diff(array_keys($variables), ['user', 'posts', 'comments']);
// ... 
```

最后，我们可以通过使用 [`array_intersect_key()`](http://php.net/manual/en/function.array-intersect-key.php) 从`$variables`中提取出我们想要的变量(列于`$including`)。

`array_intersect_key()`接受多个数组并返回第*个*数组的条目，这些条目的关键字存在于*所有的*其他数组中。

请注意，由于`array_intersect_key()`比较键以获得数组的[交集，我们需要通过使用](https://singlethread.io/post/find-the-intersection-of-two-arrays/) [array_flip()](http://php.net/manual/en/function.array-flip.php) 来切换键及其在`$including`中的相关值:

```
<?php
// ...
$variables = get_defined_vars();
$including = array_diff(array_keys($variables), ['user', 'posts', 'comments']);

$vars = array_intersect_key($variables, array_flip($including));
// ... 
```

现在，为了使过程可重用，我们可以将复杂性抽象成一个帮助函数:

```
<?php

//Helpers.php

// ...

if (!function_exists('only_compact')) {

    function only_compact($values, $keys) {
        $keys = array_diff(array_keys($values), $keys);
        return array_intersect_key($values, array_flip($keys));

} 
```

这将使事情变得更简单:

```
<?php

// Twig initialization...

// Preparing data

$user          = 'user data';
$posts         = 'posts';
$comments      = 'comments';
$anotherOne    = 'some value';
$yetAnotherOne = 0;
$andAnotherOne = 0;
$counter       = 0;
$test_1        = 1;
$test_2        = 2;
$test_3        = 3;
$test_4        = 4;
$test_5        = 5;
$test_6        = 6;

// Even more variables ...

$twig->render('author.page', only_compact(get_defined_vars(), ['counter', 'twig']));
);

// ... 
```

结果，我们得到了一个变量数组，所有不需要的变量**都被排除了**。

要使用 helper 文件，我们可以将其添加到`composer.json`中的`files`(T1)下:

```
// ...

    "autoload":{
        "files": [
            "helpers.php"
        ]
    }

// ... 
```

由于我们使用 PHP 的内置函数，性能影响并不明显。以下是在使用上述技术之前和之后，使用 [Blackfire](https://blackfire.io) 进行的快速剖面测试的结果:

**不使用功能:**

![Without the functions](img/c3512c68b5dd1d71eb8dc59bb0afcf13.png)

**使用功能:**

![with the functions](img/fee8ec273427649dcac432bd8ede9a7b.png)

正如您所看到的，两个概要文件运行的结果几乎是相同的。

希望这个技巧能帮你节省一些打字的时间！

关于模板和视图效率，还有其他的技巧可以分享吗？让我们知道！

## 分享这篇文章