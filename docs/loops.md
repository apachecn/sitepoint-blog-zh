# 学习循环

> 原文：<https://www.sitepoint.com/loops/>

计算机的一个显著优点是它们可以轻松有效地执行重复性任务。你可以不写重复的代码，而是写一组处理数据的语句，然后让计算机通过使用一种称为循环的结构来重复执行这些语句。PHP 中的循环有几种不同的风格:`for`、`while`、`do-while`和`foreach`。我将向您介绍他们中的每一个，并向您展示他们如何使重复的任务变得简单明了并且易于维护。

## `for`循环

让我们考虑为数字 12 生成乘法表的例子。您可以计算并显示 12 乘以 1、2、3 等得出的值。像这样:

```
<?php
echo "1 × 12 = " . (1 * 12) . "<br>";
echo "2 × 12 = " . (2 * 12) . "<br>";
echo "3 × 12 = " . (3 * 12) . "<br>";
...
```

…等等。也许将代码扩展到 12×12 会很快，只是简单的复制、粘贴和修改。但是如果计算需要增加到 50×12 呢？复制和粘贴的方法实际上是对时间的低效利用，并且会导致以后代码变得笨拙。

但是不要害怕！解决方案就在眼前——`for`循环。

```
<?php
for ($i = 1; $i <= 12; $i++) {
    echo $i . " × 12 = " . ($i * 12) . "<br>";
}
```

这个例子展示了为任意值创建乘法表只需要几行代码。

for 循环使用三个单独的表达式，用分号分隔并包含在括号内:

*   第一个表达式`$i = 1`初始化用作计数器的变量`$i`(`$i`是循环计数器常用的变量名)。计数器将用于记录循环已经执行了多少次。
*   第二个表达式`$i <= 12`决定是停止循环还是继续。在这种情况下，循环将执行 12 次。更准确地说，当`$i`的值小于或等于 12 时，循环将继续。一旦`$i`有了更高的值，循环就会停止。
*   第三个表达式`$i++`更新计数器。`$i`每循环一次，增量为 1。循环的周期也称为迭代。

从维护的角度来看，如果您需要更改代码以将范围扩展到 50×12，那么只需要更改第二个表达式中的值:

```
<?php
for ($i = 1; $i <= 50; $i++) {
    echo $i . " × 12 = " . ($i * 12) . "<br>";
}
```

这显然比多添加 38 行代码要好！

如果你想逆向处理，比如把 12 乘以 12，然后是 11，然后是 10，依此类推，会怎么样呢？如果只是简单的调整三个表情就很容易了。

```
<?php
for ($i = 12; $i > 0; $i--) {
    echo $i . " × 12 = " . ($i * 12) . "<br>";
}
```

`$i`被初始化为 12，这是循环需要开始的值。`$i > 0`确保循环仅在`$i`为 1 或更大时迭代。在每个周期之后，`$i`的值递减 1。

## `while`和`do-while`循环

当你必须执行有限数量的动作时，一个`for`循环是合适的。但是，有时您可能需要对不确定数量的迭代执行重复的操作。在这种情况下，while 或`do-while`循环都是理想的。

这个例子演示了一个读取文件并打印内容的`do-while`循环——您事先不知道这个文件中有多少行数据，因此使用 for 显然不合适。

```
<?php
$file = "employees.csv";
$employeeFile = fopen($file, "r");
do {
    $data = fgets($employeeFile);
    echo $data . "<br>"; 
}
while (!feof($employeeFile));
```

注意，`do`关键字在左括号之前，`while`子句在右括号之后。无论有 5 行还是 500 行，循环都将继续，直到到达文件的末尾。

与 while 循环相同的示例仅如下所示:

```
<?php
$file = "employees.csv";
$employeeFile = fopen($file, "r");
while (!feof($employeeFile)) {
    $data = fgets($employeeFile);
    echo $data . "<br>"; 
}
```

那么这和`do-while`有什么区别呢？有一个微妙但重要的区别——`do-while`将至少执行一次，因为只有在第一次迭代后才检查文件结束条件。只有当我们不在文件末尾时,`while`循环才开始执行。

## `foreach`循环

您已经看到了一个打印乘法表中数字 12 的简单例子。现在让我们看看另一个例子，这次使用的是一个数组。正如 PHP 数组简介一文所指出的，数组本质上是数据的索引表。假设这个数组保存一年中的月份:

==img1==

要访问该值，您需要相关的键。为了说明如何使用`$i`作为键和计数器，让我们从一个名为`$months`的数组中打印日历月份:

```
<?php
$months = array("January", "February", "March", ... "Dec");
for ($i = 0; $i < 12; $i++) {
    echo $months[$i] . "<br>";
}
```

像以前一样使用`for`循环，但是有两个细微的区别——首先，`$i`被初始化为 0，因为自动索引数组中的第一项是 0。第二，该条件检查计数器是否小于 12，因为一旦处理完 12 个项目(数组中的键值 0 到 11)，循环就必须停止。

您可能已经猜到了使用 for 的替代方法。是的，另一个选择是`foreach`。它不需要您预先知道有多少数组元素，也不需要您跟踪计数器变量。

`foreach`循环有两种变化，我将向您展示这两种变化。第一个只检索每个数组元素的值。

```
<?php
foreach ($months as $value) {
	echo $value . "<br>";
}
```

您可以看到这是一个比使用`for`更简单的遍历数组的解决方案。在括号内，您提供数组名，后跟关键字`as`和一个变量，当前元素的值将被赋给该变量。在这种情况下，`$months`是数组，`$value`是值变量。不需要计数器或检查循环是否应该继续，并且如果数组大小改变，也不需要改变任何代码。

在另一个`foreach`变体中，您可以访问键和值:

```
<?php
foreach ($months as $key => $mon) {
	echo $key . " - " . $mon . "<br>";
}
```

这里`$key =>`已经被添加到表达式中，它允许您访问键值。

## `break`和`continue`关键字

在整篇文章中，我假设您想从头到尾处理一个循环。当然，如果出现某种情况，您可能希望尽早退出循环。为此，您可以使用`break`关键字，如下面较早的`do-while`循环的修改示例所示:

```
<?php
$file = "employee.sql";
$employeeFile = fopen($file, "r");
do {
    $data = fgets($employeeFile);
    if (strlen($data) == 0) {
        break; 
    }
    else {
        echo $data; 
    }
}
while (!feof($employeeFile));
```

检索并检查文件的每一行——如果该行是空白的，使用`break`立即退出循环。只要循环还没有到达文件的末尾，子句就保持循环运行。

如果您不想退出循环，而是继续下一次迭代，请使用`continue`关键字。

## 摘要

在本文中，您已经看到了使用循环执行重复任务并使代码更易于维护的各种方法。我想告诉你一些使用循环的技巧:

*   当迭代次数有限且已知时，使用`for`。
*   当迭代次数未知但可由条件控制时(如到达文件末尾)，使用`while`或`do-while`。
*   当需要处理数组中的每个元素时，使用`foreach`。
*   如果需要完全脱离循环，可以使用`break`。
*   如果您想跳过迭代的剩余语句并继续下一个，您可以使用`continue`。

图片 via [马尔西奥](http://www.shutterstock.com/gallery-192436p1.html)/[Shutterstock](http://shutterstock.com)

## 分享这篇文章