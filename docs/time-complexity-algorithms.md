# 算法的时间复杂度

> 原文：<https://www.sitepoint.com/time-complexity-algorithms/>

如果你是一名 web 开发人员或程序员，你很可能已经为各种任务编写了算法。例如:在一个表中搜索，按降序排列一组数字，计算到达一个位置的最短路径……但是怎样才能证明一个算法是好算法呢？

算法的一个规格是它的正确性。在测试了几次之后，你可能会认为你的算法是可行的。然而，如果你能[从数学上证明](http://en.wikipedia.org/wiki/Formal_verification)你的算法对于每一个可能的输入值都能如预期的那样工作，这将是一个巨大的奖励。在这篇文章中，我将不再深入探讨这个问题。

另一个规范是它的效率:计算时间和输入量有什么关系？是线性关系吗？输入翻倍，计算时间是指数级上升吗？这就是本文将要讨论的内容。

## 时间复杂度

如上所述，时间复杂度是计算时间和输入量的关系。这通常是一个数组或一个对象的大小。时间复杂度对于从数据库获取用户名、连接字符串或加密密码这样的简单功能也没有用。它更多地用于排序函数、递归计算和通常需要更多计算时间的事情。

这不是因为我们不关心函数的执行时间，而是因为这种差异可以忽略不计。我们不关心获取用户名是否需要 10ms 而不是 3ms。然而，如果我们有一个需要 400 毫秒的递归排序算法，我们可以将其减少到 50 毫秒，这将是一件有趣的事情。

正如你可能猜到的，计算时间越短，算法效率越高。接下来的问题是:“我们如何以一种通用的方式定义时间复杂性？”。这就是我们要使用“大 O 符号”的地方。

## 大 O 符号

大 O 符号是表示算法时间复杂度的符号。它是算法比例因子上限的数学表示。例如，如果我们将输入数组的大小增加一倍，计算时间会增加多少？这可以通过两个例子来说明:

```
$numbers = array(14,82,4,0,24,28);
foreach($numbers as $number)
{
    echo $number;
}
```

假设`$numbers`数组是函数的参数。我们有一个 foreach 循环贯穿其项目。如果我们计算代码运行所需的时间，如果我们将数组的大小加倍会发生什么？我们可以很容易地在这个例子中看到，它将运行时间增加一倍。我们看到数组的大小和计算时间之间存在线性关系。所以如果我们把数组的大小写成 n，我们就可以把时间复杂度写成 O(n)。
再比如:

```
$numbers = array(14,82,4,0,24,28);
foreach($numbers as $number1)
{
    foreach($numbers as $number2)
    {
        if($number1 >= $number2)
        {
            echo $number1 . " is greater than or equal to " . $number2;
        }
        else
        {
            echo $number1 . " is smaller than " . $number2;
        }
    }
}
```

在这段代码中，有一个 foreach 循环位于另一个 foreach 循环内。假设‘n’是`$numbers`的大小。然后我们通过 n 循环 n 次。你可能会猜到，我们把时间复杂度写成 O(n)。

大 O 符号表示计算时间的缩放比例，并使用该缩放比例的上限和极限之间的某种混合。例如:

```
$numbers = array(14,82,4,0,24,28);
foreach($numbers as $number1)
{
    foreach($numbers as $number2)
    {
        if($number1 >= $number2)
        {
            echo $number1 . " is greater than or equal to " . $number2;
        }
        else
        {
            echo $number1 . " is smaller than " . $number2;
        }
    }
}

foreach($numbers as $number)
{
    echo $number;
}
```

你可能会想把时间复杂度写成 O(n +n)。虽然从技术上来说，这没有错，但却毫无意义:你总是将时间复杂性定义为无限的数学极限。如果你把一个多项式的极限定为无穷大，最重要的总是指数最高的变量。因为时间复杂性适用于时间的变化率，所以因子永远不会写在变量之前。这意味着，例如，你可以用 O(n)代替 O(5n)。

## 高效算法

现在我们知道了如何表达时间复杂度，我们可以看看一些高效算法的例子。

对于第一个，我想介绍另一个特殊的符号:O(log(n))，它显示了一个[对数](http://en.wikipedia.org/wiki/Logarithm)关系。使用这种算法的一个例子是 T2 二分搜索法算法 T3。对于那些懒得阅读整篇文章的人:你想在一个按字母顺序排列的列表中找到一个名字，所以你去了中心。如果您搜索的名称在此之前，您将转到中间页面和开头之间的中间位置(即 1/4)。你继续这样，直到你找到正确的名字。该算法时间复杂度为 O(log(n))。

如果您通过遍历列表条目来查找名字，时间复杂度将是 O(n)。虽然这还不错，但 O(log(n))要好得多。它可以称得上是一种有效的算法。

## 低效的算法

正如有高效的算法一样，我们也有低效的算法。其中之一就是 [bogosort 算法](http://en.wikipedia.org/wiki/Bogosort)。虽然(幸运的是)没有人真的使用它，但它被用来示范你不应该这样做。当使用它对一个数字列表进行降序排序时，它会随机地为列表选择一个顺序。然后，它将检查列表的顺序是否正确，如果不正确，它将再次随机化。如您所见，该算法效率不高，时间复杂度为 O(n x n！)(带 n！作为 n 的[阶乘](http://en.wikipedia.org/wiki/Factorial)。如果你想以一种高效的方式对数组进行排序，寻找另一种算法，例如 [Heapsort](http://en.wikipedia.org/wiki/Heapsort) 。

## 编写算法并优化它

我现在将通过首先编写一个算法，然后编写一个更好的算法来演示我们如何应用时间复杂性。通过观察它的复杂性，你会明白为什么后者更好。我想写一个以数组作为参数的函数。该数组将由若干正整数组成。然后，该函数将返回一个新数组，包含这些整数，按大小递增排序。

我将使用的第一个算法叫做插入排序。简而言之:它将遍历数组，如果一个整数小于下一个整数，它将切换它们。更详细的描述可以在这里阅读[。](http://en.wikipedia.org/wiki/Insertion_sort)

我是这样实现算法的:

```
function insertionSort($array)
{
    $currentNumber;

    for($i = 1; $i < count($array); $i++)
    {
        $currentNumber = $array[$i];

        while (($i-1 >= 0) && ($currentNumber < $array[$i-1])) //While there is a smaller number to the left
        {
            $array[$i] = $array[$i-1]; //replace the current number by the number to its left
            $i--;
        }
        //there are no smaller numbers to the left anymore
        $array[$i] = $currentNumber; //set the current number to the number that originally had index i
    }

    return $array;
}

$array = array(4,29,9,2,9);
print_r(insertionSort($array));
```

您会看到在 for 循环中有一个 while 循环。最坏的情况是时间复杂度为 O(n)。虽然该算法在设计上做得很好，但如果处理更大的数组，O(n)就不好了。我现在将演示一个更好的算法:这个算法将首先找到作为参数传递的数组的最大值。然后它将创建一个名为`$counting`的关联数组，计算每个索引在原始数组中出现的次数。最后，它在计数数组中循环，并将每个索引“n”次添加到一个新数组中，其中“n”是索引的值。例如，如果$counting[23]的值为' 3 '，它会将 23 ^ 3 次添加到新数组中。

```
function findMax($array)
{
    $maximum = $array[0];
    for($i = 1; $i < count($array); $i++)
    {
        $maximum = ($array[$i] > $maximum ? $array[$i] : $maximum);  
    }

    return $maximum;
}

function increasingSort($array)
{
    $size = findMax($array);

    $counting = array();

    for($i = 0; $i <= $size; $i++)
    {
        $counting[$i] = 0;
    }

    for($i = 0; $i < count($array); $i++)
    {
        $counting[$array[$i]]++;
    }

    $ordered = array();

    for($i = 0; $i < count($counting); $i++)
    {
        for($j = 0; $j < $counting[$i];$j++)
        {
            $ordered[] = $i;
        }
    }

    return $ordered;
}

$array = array(29,1,2,2,2,28,98);
print_r(increasingSort($array));
```

该算法的时间复杂度为 O(n ),大大优于插入排序算法。然而，请注意，这种算法可能不适合变化很大的更高的数字，因为`$array`将具有巨大的大小。始终确保算法适合情况。

## 时间复杂性不是一切

既然我已经向您展示了什么是时间复杂性，请注意计算时间不应该是您唯一的关注点。虽然您应该始终尝试找出您的算法是否足够省时，但还有其他方面需要考虑。如果你需要对十个项目进行排序，计算时间并不重要，所以不要在这上面浪费时间。此外，对于大多数任务，如排序，搜索条目等。已经有各种有效的和经过测试的算法，等着你去谷歌它们。

## 分享这篇文章