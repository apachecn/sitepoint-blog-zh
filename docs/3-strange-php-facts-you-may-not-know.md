# 你可能不知道的 3 个奇怪的 PHP 事实

> 原文：<https://www.sitepoint.com/3-strange-php-facts-you-may-not-know/>

毫无疑问:PHP 是一种简单、灵活、宽容的语言。但它也会表现出一些令人惊讶的行为。在这篇文章中，我将提出一些“奇怪的事实”，并解释为什么 PHP 会给出这样的结果。

## 浮点不精确性

你们大多数人可能都知道，浮点数并不能真正代表所有的实数。此外，两个表示良好的数字之间的一些运算会导致令人惊讶的情况。这是因为计算机表示数字的有限精度，所以它不仅影响 PHP，而且影响所有的编程语言。浮点问题的不准确性从一开始就让程序员头疼。

关于这个问题已经说了很多话，但是其中最重要的一篇文章是[每个计算机科学家都应该知道的浮点运算](http://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html "What Every Computer Scientist Should Know About Floating-Point Arithmetic")。如果你从来没有机会阅读它，我强烈建议你这样做。

让我们来看看这个非常小的片段:

```
<?php
echo (int) ((0.1 + 0.7) * 10);
```

你认为结果会是什么？如果你猜 8，你就错了…对不起，你失去了奖品！这段代码实际上会打印 7！对于那些有 Zend 认证的人来说，这个例子已经知道了。其实你可以在 *Zend PHP 5 认证学习指南*里找到。

现在让我们看看为什么会发生这种情况。执行的第一个操作是:

`0.1 + 0.7 // result is 0.79999999999`

由于精度问题，算术表达式的结果在内部存储为 0.7999999，而不是 0.8，这就是问题的根源。

执行的第二个操作是:

`0.79999999 * 10 = 7.999999999`

该操作运行良好，但保留了错误。

执行的第三个也是最后一个操作是:

`(int) 7.9999999 // result is 7`

该表达式使用了显式强制转换。当一个值被转换为`int`时，PHP 会截断数字的小数部分，得到结果 7。

这里有两件有趣的事情需要注意:

*   如果将数字强制转换为`float`而不是`int`，或者根本不强制转换，那么结果将会是您预期的 8。
*   虽然 CPU 不知道，我们实际上由于它的不精确而得到了这个错误，但是它根据数学悖论工作，断言 [0.999…等于 1](http://en.wikipedia.org/wiki/0.999... "0.999... - Wikipedia, the free encyclopedia") 。

为了总结这一点，我想引用一下 Zend PHP 5 认证学习指南第二版:

> 只要计算的精度与应用程序的正常运行相关，就应该考虑使用 BCMath 扩展提供的任意精度函数(您可以在 PHP 手册中找到它),而不是 PHP 的内置数据类型。

## PHP 如何“增加”字符串

在我们的日常工作中，我们编写执行递增/递减操作的指令，如下所示:

```
<?php
$a = 5;
$b = 6;
$a++;
++$b;
```

每个人都很容易理解这些声明是怎么回事。但是根据下面的陈述，猜猜会有什么输出:

```
<?php
$a = 1;
$b = 3;
echo $a++ + $b;
echo $a + ++$b;
echo ++$a + $b++;
```

你写下你的答案了吗？很好。让我们都检查一下。

```
4
6
7
```

不太难吧？现在让我们把标准提高一点。你尝试过增加字符串吗？尝试猜测以下内容的输出是什么:

```
<?php
$a = 'fact_2';
echo ++$a;

$a = '2nd_fact';
echo ++$a;

$a = 'a_fact';
echo ++$a;

$a = 'a_fact?';
echo ++$a;
```

这次没那么容易了吧。让我们来揭开答案。

```
fact_3
2nd_facu
a_facu
a_fact?
```

惊讶吗？在以数字结尾的字符串上使用增量运算符会增加该字母(字母表中紧随其后的那个字母，例如 t -> u)。无论字符串是否以数字开头，最后一个字符都会被更改。但是如果字符串以非字母数字字符结尾，则没有任何效果。

这在关于[递增/递减操作符](http://php.net/manual/en/language.operators.increment.php "PHP: Incrementing/Decrementing Operators")的官方文档中有很好的记录，但是你们中的大多数人可能没有读过，因为你们可能认为那里没有什么特别的东西。我必须承认，直到不久前，我也是这么想的。

> PHP 在处理字符变量而不是 C 语言的算术运算时遵循 Perl 的惯例。比如在 PHP 和 Perl $ a = ' Z$ a++；把$a 变成‘AA’，而在 C 中 a =‘Z’；a++；将 a 转换为“[”(“Z”的 ASCII 值为 90，“[”的 ASCII 值为 91)。请注意，字符变量可以递增，但不能递减，即使如此，也只支持普通 ASCII 字符(a-z 和 A-Z)。增加/减少其他字符变量没有影响，原始字符串不变。

## 价值表象的奥秘

承认吧，你是 PHP 中真正的数组大师。不要害羞。您已经了解了如何创建、管理和删除阵列的所有内容。尽管如此，下一个例子可能会让你乍一看大吃一惊。

您经常使用数组，并且需要搜索给定值是否在数组中。PHP 为此提供了一个函数，`in_array()`。让我们来看看它的实际应用:

```
<?php
$array = array(
  'isReady' => false,
  'isPHP' => true,
  'isStrange' => true
);
var_dump(in_array('sitepoint.com', $array));
```

输出会是什么？下注吧，让我们检查一下。

```
true
```

这是不是有点奇怪？我们有一个关联数组，它的值都是布尔值，如果我们搜索一个字符串，我们得到`true`。这个值是不是神奇的出现了？让我们看另一个例子。

```
<?php
$array = array(
  'count' => 1,
  'references' => 0,
  'ghosts' => 1
);
var_dump(in_array('aurelio', $array));
```

我们得到的是…

```
true
```

`in_array()`函数再次返回 true。这怎么可能呢？

你正在体验一个最好的和最坏的，又爱又恨的 PHP 特性。PHP 不是强类型的，这是错误的开始。事实上，如果在 PHP 中进行比较，以下所有值都被认为是相同的，除非您使用了 identity 运算符:

*   Zero
*   错误的
*   ""
*   “0”
*   空
*   数组()

默认情况下，`in_array()`函数使用松散比较，一个非空(`""`)和非零填充(`"0"`)的字符串等于`true`和所有非零数字(如`1`)。因此，在第一个例子中，我们得到了 true，因为`'sitepoint.com' == true`，而在第二个例子中，我们得到了`'aurelio' == 0`。

为了解决这个问题，你必须使用第三个也是可选的参数`in_array()`，它允许你严格地比较这些值。所以，如果我们写:

```
<?php
$array = array(
  'count' => 1,
  'references' => 0,
  'ghosts' => 1
);
var_dump(in_array('aurelio', $array, true));
```

我们最终会如愿以偿！

## 结论

在整篇文章中，您已经看到了在使用 PHP 时可能会遇到的一些奇怪和意想不到的行为。从这些例子中可以吸取的教训是:

*   永远不要相信浮点数
*   在操作中使用它们之前，请仔细检查您的数据类型
*   注意松散比较和松散数据类型的问题

如果你是一个高级用户，你可能已经知道他们了，但是复习是有价值的。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章