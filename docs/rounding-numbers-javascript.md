# JavaScript 中的数字舍入指南

> 原文：<https://www.sitepoint.com/rounding-numbers-javascript/>

在本文中，我们将探索 JavaScript 中舍入数字的各种方法。这将包括使用 JavaScript 数学函数，以及其他舍入到小数位的方法。我们还将讨论舍入数字时需要注意的问题。

## JavaScript 舍入

当处理数值时，我们有时会执行需要舍入到整数的小数部分的计算——比如当你计算平均价格时，或者处理[随机数](https://www.sitepoint.com/generate-random-numbers-javascript/)。幸运的是，JavaScript 的`Math`对象提供了许多将数字四舍五入为整数值的方法。

在我们的例子中，我们将使用两个最重要的数学常数来演示不同类型的舍入: [Pi](https://en.wikipedia.org/wiki/Pi) ，它是圆周与其直径的比率，以及 [e](https://en.wikipedia.org/wiki/E_(mathematical_constant)) ，它是自然对数的基数，也称为“欧拉数”。这两个值都是`Math`对象的属性，但是让我们将它们分配给一些变量，使它们更容易处理:

```
const PI = Math.PI
const E = Math.E 
```

*专业提示:你也可以使用[对象析构](https://www.sitepoint.com/es6-destructuring-assignment/) :* 在一行中完成这个赋值

```
const { PI,E } = Math 
```

现在我们已经定义了这些常量，让我们看看 JavaScript 中舍入数字的一些方法。

## 用 Math.round 在 JavaScript 中舍入数字

我们要看的第一个方法是`Math.round`。这是最直接的选项，简单地将任何带小数部分的数字四舍五入到最接近的整数。它使用这样的规则:如果一个数正好在两个整数的中间，它将被向上舍入*。例如，2.5 将向上舍入到 3。*

 *要使用这种方法，我们只需提供要舍入的数字作为参数:

```
Math.round(2.3) // rounds down because it's closer to 2
<< 2

Math.round(2.921) // rounds up because it's closer to 3
<< 3

Math.round(2.5) // rounds up because it's exactly halfway
<< 3

Math.round(PI)
<< 3

Math.round(E)
<< 3 
```

如果你想把一个数字四舍五入到最接近的整数值，这就很方便了。例如，如果你在计算三次测试的平均分，你应该把三次得分相加，然后除以三。这可能不会产生一个整数，所以您可以使用`Math.round()`将其四舍五入到最接近的值:

```
const test1 = 86;
const test2 = 93;
const test3 = 95;
const average = Math.round((test1+test2+test3)/3);
<< 91 
```

## 用 Math.floor 舍入数字

我们要看的下一个方法是`Math.floor`。这总是将值*向下*舍入到下面的整数(顾名思义，该数字被下推到*楼层*):

```
Math.floor(2.3) // rounds down to 2
<< 2

Math.floor(2.921) // rounds down to 2
<< 2

Math.floor(2.5) // rounds down to 2
<< 2

Math.floor(PI)
<< 3

Math.floor(E)
<< 2 
```

`Math.floor`的一个常见用途是创建随机整数。将*向下舍入*确保整数从零开始，并且每个整数都有相等的机会被返回。从零开始通常是有用的，因为 JavaScript 中的数组是零索引的，所以向下舍入将确保可以选择数组中的第一个元素。下面的例子显示了如何使用`Math.floor`从数组中选择随机元素:

```
const fruit = ["🍏","🍌","🍓","🍋","🍐"]

const randomFruit = fruit[Math.floor(Math.random()*fruit.length)]
<< "🍓" 
```

在上面的代码中使用`Math.floor`将*向下舍入*可以确保返回一个介于 0 和 4 之间的索引，因此数组中的每个元素都有均等的机会被选中。

## 用 Math.ceil 舍入数字

说到四舍五入，这正是`Math.ceil`所做的。这个名字来源于*天花板*，与*地板*相反，暗示价值正在*上升*。这种方法的工作方式和所有其他方法一样。只需提供您想要取整的数字作为参数:

```
Math.ceil(2.3) // rounds up to 3
<< 3

Math.ceil(2.921) // rounds up to 3
<< 3

Math.ceil(2.5) // rounds up to 3
<< 3

Math.ceil(PI)
<< 4

Math.ceil(E)
<< 3 
```

但是什么时候你需要取整一个数字呢？一个常见的用法是如果你需要计算出你需要多少个容器来装东西。例如，假设您有一个包含播放列表的音乐网站，每个播放列表上有十首歌曲。如果有人上传了 82 首歌曲，你需要计算出需要创建多少个播放列表。这是通过将歌曲数量除以`10`(每个播放列表上的歌曲数量)来实现的:

```
const songsPerPlaylist = 10;
const numberOfSongs = 82;
const numberOfPlaylists = numberOfSongs/songsPerPlaylist;
<< 8.2 
```

使用`Math.round`会将这个*向下*舍入到`8` …但是这样我们就没有最后两首歌曲的播放列表了！在这种情况下，我们总是需要将*聚集起来*，以便有一个额外的容器来容纳任何剩余物:

```
const numberOfPlaylists = Math.ceil(numberOfSongs/songsPerPlaylist);
<< 9 
```

## 用 Math.trunc 舍入数字

我们要看的下一个方法是`Math.trunc`。严格来说，这不是一个舍入函数；它实际上*截断了*作为参数提供的数字。它基本上只是删除了数字的小数部分，只留下了整数部分，如下例所示:

```
Math.trunc(2.3) // just leaves 2
<< 2

Math.trunc(2.921) // just leaves 2, even though it's closer to 3
<< 2

Math.trunc(2.5) // just leaves 2
<< 2

Math.trunc(PI)
<< 3

Math.trunc(E)
<< 2 
```

乍一看，`Math.trunc`似乎和`Math.floor`一模一样；当然，到目前为止给出的例子都给出了相同的结果。但是，当负值作为参数提供时，这两种方法的行为会有所不同，如下例所示:

```
Math.floor(-2.3) // rounds DOWN to -3
<< -3

Math.trunc(-2.3) // removes the decimal part, leaving just -2
<< -2 
```

出现这种差异是因为，当使用`Math.floor`向下舍入一个负数时，它会向下舍入到下一个最小的整数，而截断负值相当于向上舍入*。*

 *当参数为负数时，`Math.ceil`返回与`Math.trunc`相同的值:

```
Math.trunc(-2.3) // removes the decimal part, leaving just -2
<< -2

Math.ceil(-2.3) // rounds UP to -2
<< -2 
```

所有这些方法都非常有用，但是它们有一个限制，即它们总是返回整数值。如果我们想把一个数字四舍五入到一定的小数位数或者有效数字呢？

## 在 JavaScript 中将数字舍入到小数位

我们已经看到`Math.round`会将数字四舍五入到最接近的整数。不幸的是，`Math`对象没有提供任何方法来将数字更精确地四舍五入到特定的小数位数。幸运的是，`Number`类型有几个内置的方法，*可以让*做到这一点。让我们来看看它们。

### 用 Number.toFixed 舍入到小数位

这是一个数字方法，这意味着它是由数字本身调用的。它将十进制数舍入到给定的小数位数，这是作为参数提供的:

```
2.4387587.toFixed(2) // rounds to 2 decimal places
<< "2.44" 
```

需要注意的一点是，该值作为一个*字符串*返回。您可以通过将方法调用封装在`Number`函数中来解决这个问题，函数[会将结果转换回一个数字](https://www.sitepoint.com/convert-string-to-number-javascript/):

```
Number(2.4387587.toFixed(2))
<< 2.44 
```

其他需要注意的事情:如果您试图将这个方法应用于一个已经是整数的数字，如果您只使用一个点来调用这个方法，您将会得到一个错误:

```
2.toFixed(2)
<< SyntaxError 
```

你不能用一个点来调用整数上的方法，因为不清楚这个点是方法调用操作符还是小数点。为了解决这个问题，您可以将整数放在括号中，或者使用两个点，这样可以清楚地表明您正在调用一个方法，而不是写出一个带小数点的数字文字:

```
(2).toFixed(2)
<< "2.00"

2..toFixed(2)
<< "2.00" 
```

如果没有提供参数，数字将被四舍五入为最接近的整数(但作为字符串返回):

```
PI.toFixed()
<< "3"

E.toFixed()
<< "3" 
```

舍入到一组小数位数的一个常见用例是在处理货币时，例如，如果您想以美元为单位提供最接近美分的价格。比方说，你有一个电子商务网站，对购物车中的任何商品进行 15%的促销。折扣价格在显示前可能需要四舍五入:

```
const item1Price = 2.99
const item2Price = 4.99
const item3Price = 6.20

const totalPrice = item1Price + item2Price + item3Price
const discountedPrice = 0.85 * totalPrice
<< 12.052999999999999 
```

使用`Number.toFixed`可以很容易地解决这个问题:

```
const discountedPrice = (0.85 * totalPrice).toFixed(2)
<< "12.05" 
```

*注:有关您可能会遇到的与`toFixed()`有关的问题的更多信息，请参见[号码()。toFixed()舍入误差:损坏但可修复](https://www.sitepoint.com/number-tofixed-rounding-errors-broken-but-fixable/)。*

### 用 numbers to 精度将数字舍入到小数位

`Number.toPrecision`方法的工作方式类似于`Number.toFixed`方法，但是它将数字四舍五入为固定的[有效数字](https://en.wikipedia.org/wiki/Significant_figures)。

如果你需要一个有效数字的快速提示，它基本上意味着只使用第一个非零数字。对于较大的数字，最后的答案也会用零填充。例如，数字 53，863 四舍五入到两位有效数字将成为 54，000。这是因为 5 和 3 是前两个非零数字，它向上舍入是因为下一个数字是 8。我们需要在末尾添加零，以确保舍入后的值是原始数字的合理近似值。

你也可以用类似的方法舍入小数。例如，0.00000623978 将舍入到 0.0000062 的两位有效数字，因为 6 和 2 是第一个非零数字，它向下舍入，因为下一个数字是 3。

要使用这个方法，只需在数字上调用它，提供有效数字的数目作为参数(记住，在对整数调用方法之前，需要将整数放在括号中):

```
(53863).toPrecision(2)
<< "5.4e+4"

0.00000623978.toPrecision(2)
<< 0.0000062" 
```

请注意，所有值都以字符串形式返回，并且可以使用指数表示法—例如“5.4e+4”而不是“54000”。

和以前一样，我们可以通过在`Number`函数中包装方法调用来确保返回一个数字:

```
Number((53863).toPrecision(2))
<< 54000 
```

舍入到给定的有效数字的一个常见用法是当您处理大数字并且您不确定它们会有多大时。例如，假设你想报告你的最新帖子被“喜欢”了多少次，你是将其四舍五入到最接近的 10、100 还是 1000？在某种程度上，这取决于它有多受欢迎；如果它只有 8 个赞，你不会想把它四舍五入到最接近的 100，但是如果它有成千上万个赞，那么把它四舍五入到最接近的 10 就显得很愚蠢。解决方案是将其四舍五入到一个有效数字:

```
const unpopularPost = 8;
const quitePopularPost = 387;
const poplularPost = 79671;

Number(unpopularPost.toPrecision(1))
<< 8
Number(quitePopularPost.toPrecision(1))
<< 400
Number(poplularPost.toPrecision(1))
<< Number(poplularPost.toPrecision(1))
<< 80000 
```

## JavaScript 中舍入数字的问题

在 JavaScript(或任何编程语言，就此而言)中舍入数字时，有一些事情需要注意。您可能知道，计算机将所有数据(包括数字)存储为二进制表示。JavaScript 将数字存储为 [32 位单精度](https://en.wikipedia.org/wiki/Single-precision_floating-point_format)二进制值。

这样做的问题是，一些[基数为 10 的数字](https://en.wikipedia.org/wiki/Decimal)无法用[基数为 2 的数字](https://en.wikipedia.org/wiki/Binary_number)精确表示。这通常不会导致任何问题，但会导致一些奇怪的结果，例如:

```
0.1 + 0.2 === 0.3
<< false 
```

这是因为 0.1 和 0.2 不能用二进制的*精确表示*，加起来会有微小误差。

`Math`对象有另一个名为`fround`的方法，它返回可以用 32 位表示的最接近的数字。例如，0.6125 在二进制中可以精确地表示为 0.101，因此这将返回相同的值:

```
Math.fround(0.625)
<< 0.625 
```

但是，正如我们在上面看到的，0.1 不能精确地用 32 位表示。`Math.fround`向我们展示了最接近的数字:

```
Math.fround(0.1)
<< 0.10000000149011612 
```

正如你所看到的，它非常接近 0.1，但*非常*略高。在大多数实际情况下，这不会导致任何问题，但是当您试图舍入一些数字时，偶尔会导致一些奇怪的行为:

```
3.55.toFixed(1) // should round UP to 3.6
<< "3.5" 
```

这是因为小数 3.55 无法用 32 位精确表示。我们可以使用`Math.fround`来看看它实际上是如何表示的:

```
Math.fround(3.55)
<< 3.549999952316284 
```

如你所见，它实际上是由浮点数 3.549999952316284 表示的，它将*向下舍入*到 3.5。

JavaScript 中舍入数字的这些问题并不经常出现，但是如果您要进行大量舍入，它们绝对是您应该注意的事情——尤其是当结果的准确性非常重要时。

## 我应该使用哪些方法来舍入数字？

在这个四舍五入的总结中，所有的四舍五入方法都出现了，你可能会问哪一个是最好的。一如既往，答案是“视情况而定”。

如果你只是想用*将一个数字四舍五入到最接近的整数*，用`Math.round`不会错太多，但是如果你总是想向下或向上舍入，不管小数部分是什么，你也应该考虑使用`Math.floor`或`Math.ceil`。如果你也打算对负数进行舍入，可以考虑使用`Math.trunc`。

如果你需要*四舍五入到给定的小数位数或有效数字*，你将不得不使用`Number.toFixed`或`Number.toPrecision`。但是要注意这两个方法是由数字调用的，[返回一个字符串](https://www.sitepoint.com/convert-number-to-string-javascript/)。

在下面的 CodePen 演示中，您可以看到本文中涵盖的所有不同类型的舍入的示例。

在 [CodePen](https://codepen.io) 上看到笔 [site point Rounding](https://codepen.io/SitePoint/pen/yLjaoXo)by site point([@ site point](https://codepen.io/SitePoint))
。**