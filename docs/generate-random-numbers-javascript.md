# 如何用 Math.random()在 JavaScript 中生成随机数

> 原文：<https://www.sitepoint.com/generate-random-numbers-javascript/>

**在本文中，我们将看看如何使用`Math.random()`在 JavaScript 中生成随机数，构建一个可以在多种用途中重用的函数——比如加载随机图像、从数组中选取随机元素，以及生成随机颜色、字母、字符串、短语和密码。**

## JavaScript 中的随机性

能够给你的程序增加一点随机性总是有用的。你可能想通过添加一些随机的风格来增加你的网站的趣味，生成一个随机的短语，或者给一个游戏添加一点运气的元素(例如，它们在这个[数字游戏](https://www.sitepoint.com/wordle-for-numbers-numble/)中被广泛使用)。

不幸的是，创造一个真正随机的值实际上是非常困难的(除非你能接触到某种[放射性物质](https://www.bbc.co.uk/bitesize/guides/zcynv9q/revision/7) …或者一只带着键盘的[猴子](https://en.wikipedia.org/wiki/Infinite_monkey_theorem))。为了解决这个问题，编程语言使用确定性方法来产生**伪随机数**。这些数字*看起来*是随机的，但实际上是由接受*种子值*的函数根据时间或鼠标指针位置等事件生成的。

JavaScript 有`random`函数，是内置`Math`对象的一个方法。ECMAScript 标准没有规定这个函数应该如何生成一个随机数，所以它留给了浏览器供应商来实现。在撰写本文时，目前各大浏览器都在后台使用 [xorshift128+](https://en.wikipedia.org/wiki/Xoroshiro128%2B) 算法生成伪随机数。

要使用它，只需输入`Math.random()`，它将返回一个介于 0(含)和 1(不含)之间的伪随机浮点十进制数:

```
const x = Math.random(); 
```

这可以表示为以下不等式:

```
0 <= x < 1 
```

但是如果你想要一个大于 1 的随机数呢？简单:你所需要做的就是乘以一个比例因子来放大它，例如，将结果乘以 10 将得到一个介于 0(包括)和 10(不包括)之间的值:

```
const y = Math.random()*10 
```

如果我们将前面不等式的两边都乘以 10，就可以看出其中的原因:

```
0 <= y < 10 
```

但结果还是浮点十进制数。如果我们想要一个随机整数呢？简单:我们需要做的就是使用`Math.floor`函数来[将返回值](https://www.sitepoint.com/rounding-numbers-javascript/)向下舍入到下面的整数。下面的代码将把一个从 0 到 9 的随机整数赋给变量`z`:

```
const z = Math.floor(Math.random()*10) 
```

请注意，即使我们乘以 10，返回的值也只会增加到 9。

我们可以将此方法推广到创建一个函数，该函数将返回一个介于 0 到最大值之间的随机整数，*但不包括作为参数提供的数字*:

```
function randomInt(number){
  return Math.floor(Math.random()*(number))
} 
```

我们现在可以使用这个函数返回一个 0 到 9 之间的随机数字:

```
const randomDigit= randomInt(10) 
```

现在我们有了一种创建随机整数的方法。但是两个不同值之间的随机整数，不总是从零开始呢？我们所需要做的就是使用上面的代码，在我们希望范围开始的值上加上。例如，如果我们想生成一个介于 6 和 10 之间(包括 6 和 10)的随机整数，我们将首先使用上面的代码生成一个介于 0 和 4 之间的随机整数，然后将结果加上 6:

```
const betweenSixAnd10 = Math.floor(Math.random()*5) + 6 
```

注意，为了生成一个在`0`和`4`之间的随机整数，我们实际上必须乘以`5`。

我们可以将此方法推广到创建一个函数，该函数将返回两个值之间的随机整数:

```
function randomIntBetween(min,max){
  Math.floor(Math.random()*(max - min + 1)) + min
} 
```

这只是我们编写的代码的一般化形式，用来获得 6 到 10 之间的随机数，但是用参数`min`代替了 6，用参数`max`代替了 10。要使用它，只需输入两个参数来表示随机数的下限和上限(含)。因此，为了模拟滚动六面骰子，我们可以使用以下代码返回 1 到 6 之间的整数:

```
const dice = randomIntBetween(1,6) 
```

为了展示`randomIntBetween`函数是如何工作的，我在下面的演示中将其连接到一些 HTML，这样你就可以改变`min`和`max`的值，并通过点击按钮生成一个随机整数(这可以用来复制龙与地下城&和类似游戏中使用的不同大小的骰子)。

看笔 [随机整数-site point](https://codepen.io/SitePoint/pen/KKRmXWg)由 site point([@ site point](https://codepen.io/SitePoint))
在 [CodePen](https://codepen.io) 上。