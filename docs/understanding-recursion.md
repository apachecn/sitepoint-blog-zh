# 理解递归

> 原文：<https://www.sitepoint.com/understanding-recursion/>

在我以前的一篇文章[中，我写了迭代器](https://www.sitepoint.com/using-spl-iterators-1/ "Using SPL Iterators, Part 1")以及如何使用它们。今天我想看看迭代的孪生兄弟:递归。

不过，在我们讨论递归之前，让我们先来看看这段代码:

```
<?php
function factorial($number) {
    if ($number < 0) {
        throw new InvalidArgumentException('Number cannot be less than zero');
    }
    $factorial = 1;	
    while ($number > 0) {
        $factorial *= $number;
        $number --;
    }
    return $factorial;
}
```

阶乘是一个数乘以所有小于该数的正整数的结果，上面的函数使用一个简单的循环计算任何给定数的阶乘。现在让我们这样重写这个例子:

```
<?php
function factorial_recursive($number) {
    if ($number < 0) {
        throw new InvalidArgumentException('Number cannot be less than zero');
    }
    if ($number == 0) {
        return 1;
    }
    return $number * factorial_recursive($number – 1);
}
```

当我们调用这两个函数时，会得到相同的结果，但是请注意，第二个函数是通过调用自身来计算阶乘的。这就是所谓的递归。

## 什么是递归？

递归函数是一种调用自身的函数，可以直接调用，也可以在函数调用的循环中调用。

递归也可以指一种解决问题的方法，首先解决一个较小版本的问题，然后使用该结果加上一些其他计算来制定原始问题的答案。通常，在解决较小版本的过程中，该方法将解决问题的更小版本，等等，直到它达到解决起来很简单的“基本情况”。

要编写一个递归函数，你需要为它提供一些返回的方法，否则它会一直调用自己(或者直到调用栈崩溃、脚本超时或者内存耗尽)。这被称为保护条款或基础案例。

递归函数的最简单形式如下:

```
<?php
function my_recursive_func (args) {
    if (simplest case) {
        // The Base Case/Guard Clause that stops the
        // function from running forever
        return simple value;
    }
    else {
        //call function again with simpler args
        my_recursive_func(argsSimplified);
    }
}
```

## 递归的类型

当一个函数直接调用自己时，它被称为直接递归。在函数调用循环中最终调用自身的函数称为间接递归。看看下面间接递归的例子:

```
<?php
function A($num) {
    $num -= 1;
    if($num > 0) {	
        echo "A is Calling B($num)n";
        $num = B($num);
    }
    return $num;
}

function B($num) {
    $num -= 2;
    if($num > 0) {
        echo "B is Calling A($num)n";
        $num = A($num);
    }
    return $num;
}

$num = 4;
echo "Calling A($num)n";
echo 'Result: ' . A($num);
```

```
Calling A(4) 
A is Calling B(3) 
B is Calling A(1) 
Result: 0
```

上面的例子实际上是无用的代码，只是想告诉你一个函数如何通过另一个函数间接调用自己。调用`A(n>4)`或`B(n>4)`会导致被调用的函数被另一个函数调用。

知道一个函数可以像这样间接调用自己是很重要的，但是在本文中我们只讨论直接递归。

## 实际例子

为了向您展示递归有多强大，我们将编写一个函数来搜索数组中的一个键并返回结果。

```
<?php
function find_in_arr($key, $arr) {
    foreach ($arr as $k => $v) {
        if ($k == $key) {
            return $v;
        }		
        if (is_array($v)) {
            foreach ($v as $_k => $_v) {
	        if ($_k == $key) {
                    return $_v;
                }
            }
        }
    }
    return false;
}

$arr = [
    'name' => 'Php Master',
    'subject' => 'Php',
    'type' => 'Articles',
    'items' => [
        'one' => 'Iteration',
        'two' => 'Recursion',
        'methods' => [
            'factorial' => 'Recursion',
            'fibonacci' => 'Recursion',
        ],
    ],
    'parent' => 'Sitepoint',
];

var_dump(
    find_in_arr('two', $arr),
    find_in_arr('parent', $arr),
    find_in_arr('fibonacci', $arr)
);
```

```
string 'Recursion' (length=9)
string 'Sitepoint' (length=9)
boolean false
```

一切都很好，但是请注意，我们只在数组中迭代了两层，所以在第三层中搜索“Fibonacci”失败了。如果我们想搜索一个深度不确定的数组，这是不够的。我们可以将搜索重写为一个递归函数:

```
<?php
function find_in_arr($key, $arr) {
    foreach ($arr as $k => $v) {
        if ($k == $key) {
            return $v;
        }
        if (is_array($v)) {
            $result = find_in_arr($key, $v);
            if ($result != false) {
                return $result;
            }
        }
    }	
    return false;
}
```

使用递归函数，我们可以搜索几级深度的数组，因为我们没有硬编码函数的深度。它会一直运行，直到遍历完数组中的所有值。

## 头尾递归

到目前为止，在所有的例子中，我们都在使用头部递归。当函数调用自身时，它在返回自己的值之前等待调用的结果。可以这样编写函数，它们不对返回值进行操作，而是将所有需要的值作为参数传递。这被称为尾部调用(或尾部递归)。这种方法通常是首选的，因为语言的运行时有时可以优化调用，这样就不会有破坏调用堆栈的危险，但 PHP 不会这样做。

下面是我们的阶乘的例子，修改了尾部调用。注意，递归调用的结果被返回，而不是被进一步操作。

```
<?php
function factorial ($number, $factorial = 1) {
    if ($number < 0) {
        throw new InvalidArgumentException('Number cannot be less than zero (0)');
    }
    if ($number == 0) {
        return $factorial;
    }
    else {
        return factorial($number - 1, $factorial * $number);
    }
}
```

## 一般建议

任何可以迭代编写的代码也可以递归编写。然而，这并不总是容易做到的(甚至也不明智)。递归在遍历树和列表或执行大多数 O(n log n)排序时大放异彩。当你需要分解一个重复的问题时，递归比迭代方法更适合，比如在文件系统中搜索，你也需要进入子目录进行搜索。在不确定深度的遍历中，递归非常有效。

请记住，PHP 不会优化递归函数，即使您编写它们来进行尾部调用，而且递归函数通常比迭代函数效率更低、速度更慢，尽管它们有时会做得更好，如上面的代码示例所示。在函数式编程中，递归通常是迭代的首选，因此大多数函数式语言都优化了递归函数。

如果您使用 XDebug，请务必检查您的系统配置。默认情况下，您将有 100 个递归调用的限制，如果超过这个限制，您的脚本将抛出一个“达到最大嵌套限制”错误。如果需要更改，您可以更新`debug.max_nesting_level`配置值。

最后，阅读一篇关于栈堆和导致栈溢出的递归的[解释来理解在递归过程中调用栈发生了什么是一个好主意。](http://joel.inpointform.net/software-development/explanation-of-stack-heap-and-recursion-causing-stack-overflow/ "The stack, the heap, and recursion leading to stack overflow Joel")

## 结论

在这篇文章中，我给了你一个关于递归的大概的介绍，以及它与迭代的比较。我还向您展示了如何编写递归函数，何时编写以及为什么编写。我还试图警告你在使用递归时可能会陷入的一些陷阱。

递归是这样的，甚至许多有经验的程序员可以几年不使用它，而许多其他人甚至从未听说过它，这很可悲，因为它是一个真正强大的概念。我希望通过这篇文章，我可以给你足够的知识去开始编写你自己的递归函数。但是请记住，就像火一样，你必须始终小心谨慎，明智地使用这一工具。

图片由 [Alexandre Duret-Lutz](http://www.flickr.com/photos/24183489@N00/268729905) 通过 Flickr

## 分享这篇文章