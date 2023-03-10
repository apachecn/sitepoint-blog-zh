# 有用的 JavaScript 数学函数以及如何使用它们

> 原文：<https://www.sitepoint.com/javascript-math-functions/>

内置的 JavaScript Math 对象包括许多有用的函数，用于执行各种数学运算。让我们深入了解一下它们是如何工作的，以及您可能会用它们来做什么。

## Math.max 和 Math.min

这些函数几乎与您预期的一样:它们返回所提供的参数列表的最大值或最小值:

```
Math.max(1,2,3,4,5)
<< 5

Math.min(4,71,-7,2,1,0)
<< -7 
```

参数*所有的*都必须是`Number` [数据类型](https://www.sitepoint.com/beginners-guide-javascript-variables-and-datatypes/#datatypes)。否则，`NaN`将被返回:

```
Math.max('a','b','c')
<< NaN

Math.min(5,"hello",6)
<< NaN 
```

不过，要小心。JavaScript 将试图将*的值强制转换成一个数字:*

```
Math.min(5,true,6)
<< 1 
```

在本例中，布尔值`true`被强制转换为数字`1`，这就是为什么它作为最小值返回的原因。如果您不熟悉类型强制，当运算符的操作数属于不同类型时，就会发生这种情况。在这种情况下，JavaScript 将尝试将一个操作数转换为另一个操作数类型的等效值。你可以在第二章的 *[JavaScript:新手到忍者，第二版](https://www.sitepoint.com/premium/books/javascript-novice-to-ninja-2nd-edition/read/2/k01nwt77/)* 中阅读更多关于类型强制的内容。

需要提供一个数字列表作为参数，而不是一个数组，但是您可以使用扩展运算符(`...`)来解包一个数字数组:

```
Math.max(...[8,4,2,1])
<< 8 
```

`Math.max`函数有助于从保存在数组中的分数列表中找到高分:

```
const scores = [23,12,52,6,25,38,19,37,76,54,24]
const highScore = Math.max(...scores)
<< 76 
```

`Math.min`功能对于在比价网站上寻找最佳价格非常有用:

```
const prices = [19.99, 20.25, 18.57, 19,75, 25, 22.50]
const bestPrice = Math.min(...prices)
<< 18.57 
```

## 绝对值

一个**绝对值**就是数的大小，不管它的大小。这意味着正数保持不变，负数失去负号。`Math.abs`函数将计算其参数的绝对值:

```
Math.abs(5)
<< 5

Math.abs(-42)
<< 42

Math.abs(-3.14159)
<< 3.14159 
```

你为什么想这么做？嗯，有时你想计算两个值之间的*差*，你可以通过从最大值中减去最小值来计算，但通常你不会事先知道两个值中哪个是最小的。为了解决这个问题，你可以以任意顺序减去这些数字，然后取绝对值:

```
const x = 5
const y = 8

const difference = Math.abs(x - y)
<< 3 
```

一个实际的例子可能是在一个省钱的网站上，你想知道通过计算两个交易之间的差异你可以节省多少，因为你将处理实时价格数据，不会提前知道哪一个交易是最便宜的:

```
const dealA = 150
const dealB = 167

const saving = Math.abs(dealA - dealB)
<< 17 
```

## 数学. pow

`Math.pow`执行功率计算，如下所示:

```
3⁴ = 81 
```

在上面的例子中，3 是已知的基数，4 是 T2 指数。我们会把它理解为“3 的 4 次方是 81”。

该函数接受两个值—底数和指数—并返回底数的指数幂的结果:

```
Math.pow(2,3)
<< 8

Math.pow(8,0)
<< 1

Math.pow(-1,-1)
<< -1 
```

`Math.pow`几乎已经被 ES2016 中引入的中缀取幂运算符(`**`)所取代，它执行完全相同的运算:

```
2 ** 3
<< 8

8 ** 0
<< 1

(-1) ** (-1)
<< -1 
```

## 计算根

根是幂的逆运算。例如，由于 3 的平方是 9，所以 9 的平方根是 3。

`Math.sqrt`可用于返回作为参数提供的数字的平方根:

```
Math.sqrt(4)
<< 2

Math.sqrt(100)
<< 10

Math.sqrt(2)
<< 1.4142135623730951 
```

如果提供负数或非数值作为参数，此函数将返回`NaN`:

```
Math.sqrt(-1)
<< NaN

Math.sqrt("four")
<< NaN 
```

但是要小心，因为 JavaScript 会试图强制类型:

```
Math.sqrt('4') 
<< 2

Math.sqrt(true)
<< 1 
```

`Math.cbrt`返回一个数的立方根。这接受所有数字，包括负数。如果使用了非数字的值，它还会尝试强制类型。如果它不能将值强制为一个数字，它将返回`NaN`:

```
Math.cbrt(1000)
<< 10

Math.cbrt(-1000)
<< -10

Math.cbrt("10")
<< 2.154434690031884

Math.cbrt(false)
<< 0 
```

可以使用指数运算符和分数幂来计算其他根。例如，一个数的四次方根可以通过它的四分之一次方(或 0.25)来求出。因此，下面的代码将返回 625 的第四个根:

```
625 ** 0.25
<< 5 
```

要计算一个数的五次方根，你需要计算它的五分之一次方(或 0.2):

```
32 ** 0.2
<< 2 
```

一般来说，要找到一个数的 n 次方根，你需要将它提升到`1/n`的幂，所以要找到一个百万的六次方根，你需要将它提升到 1/6 的幂:

```
1000000 ** (1/6)
<< 9.999999999999998 
```

注意这里有一个舍入误差，因为答案应该正好是 10。这种情况经常发生在无法用二进制精确表达的分数幂上。(你可以在“[JavaScript](https://www.sitepoint.com/rounding-numbers-javascript/#problemswithroundingnumbersinjavascript)中的数字舍入指南”中了解更多关于这个舍入问题的信息。)

还要注意，如果根是偶数，就找不到负数的根。这将返回`NaN`。所以你不能试图找到-7 的 10 次方根，例如(因为 10 是偶数):

```
(-7) ** 0.1 // 0.1 is 1/10
<< NaN 
```

你可能想计算根的一个原因是计算增长率。例如，假设你想在年底前将利润提高 10 倍。你的利润每月需要增长多少？为了找出答案，你需要计算 10 的 12 次方根，或者 10 的 12 次方:

```
10 ** (1/12)
<< 1.2115276586285884 
```

这个结果告诉我们，为了在年底前实现 10 倍的利润，月增长系数必须在 1.21 左右。或者换句话说，你需要每月增加 21%的利润来实现你的目标。

## 对数和指数

对数可以用来计算指数。例如，假设您想要求解以下方程:

```
2ˣ = 100 
```

在上面的等式中，`x`肯定不是整数，因为 100 不是 2 的幂。这可以通过使用以 2 为底的对数来解决:

```
x = log²(100) = 6.64 (rounded to 2 d.p.) 
```

`Math`对象有一个`log2`方法来执行这个计算:

```
Math.log2(100)
<< 6.643856189774724 
```

它还有一个执行相同计算的`log10`方法，但是使用 10 作为基数:

```
Math.log10(100)
<< 2 
```

这个结果告诉我们，要得到 100，你需要把 10 提高到 2 的次方。

还有一种日志方法，就是`Math.log`。这就计算出了[的自然对数](https://en.wikipedia.org/wiki/Natural_logarithm)，它以[的欧拉数](https://en.wikipedia.org/wiki/E_(mathematical_constant)) — `e`(大约 2.7)——为底数。这可能看起来是一个奇怪的使用值，但实际上当指数增长发生时，它在自然界中经常发生，因此得名“自然对数”:

```
Math.log(10)
<< 4.605170185988092

Math.log(Math.E)
<< 1 
```

最后的计算表明，欧拉数(`e` ) —存储为常数`Math.E` —需要提高到 1 的幂才能获得自身。这是有意义的，因为任何数字的 1 次方实际上就是它本身。如果将 2 和 10 作为参数提供给`Math.log2`和`Math.log10`，可以获得相同的结果:

```
Math.log2(2)
<< 1

Math.log10(10)
<< 1 
```

为什么要用对数呢？在处理呈指数增长的数据时，通常会使用对数标度，以便更容易看到增长率。对数标度经常被用来衡量疫情期间每天新冠肺炎病例的数量，因为它们上升得如此之快。

如果你足够幸运，拥有一个人气增长迅速的网站(比如说，每天翻一番)，那么你可能要考虑在展示图表之前使用对数标度来显示你的人气是如何增长的。

## 斜边

你可能记得在学校学过毕达哥拉斯定理。这表明直角三角形最长边的长度(**斜边**)可以用下面的公式求出:

```
h² = x² + y² 
```

这里，x 和 y 是另外两条边的长度。

`Math`对象有一个`hypot`方法，当提供另外两个长度作为参数时，该方法将计算斜边的长度。例如，如果一边长度为 3，另一边长度为 4，我们可以使用以下代码计算斜边:

```
Math.hypot(3,4)
<< 5 
```

但是为什么这有用呢？斜边是两点间最短距离的度量。这意味着，如果您知道页面上两个元素的 x 和 y 坐标，您可以使用此函数来计算它们之间的距离:

```
const ship = {x: 220, y: 100}
const boat = {x: 340, y: 50}

const distance = Math.hypot(ship.x - boat.x,ship.y - boat.y) 
```

我希望这篇简短的综述对您有所帮助，并帮助您在项目中充分利用 JavaScript Math 对象的强大功能。

**相关阅读:**

*   [如何用 Math.random()](https://www.sitepoint.com/generate-random-numbers-javascript/) 在 JavaScript 中生成随机数
*   [JavaScript 中的数字舍入指南](https://www.sitepoint.com/rounding-numbers-javascript/)

## 分享这篇文章