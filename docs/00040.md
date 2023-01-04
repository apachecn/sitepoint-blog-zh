# JavaScript 中缺失的数学方法

> 原文：<https://www.sitepoint.com/javascript-missing-math-methods/>

在本文中，我们将探索 JavaScript 中一些缺失的数学方法，以及如何为它们编写函数。

JavaScript [数学对象](https://www.sitepoint.com/javascript-math-functions/)包含了一些真正有用且强大的数学运算，可用于 web 开发，但它缺少大多数其他语言提供的许多重要运算(比如 Haskell，它有大量这样的运算)。

以下是每个项目的快速链接:

*   [总和](#sum)
*   [产品](#product)
*   [奇数和偶数](#oddandeven)
*   [三角形编号](#trianglenumber)
*   [阶乘](#factorial)
*   [因素](#factors)
*   [isPrime](#isprime)
*   [最大公约数](#gcd)
*   [最低公倍数](#lcm)

## JavaScript: Sum 中缺少数学方法

你可能还记得在学校时，“和”是“加”的同义词。比如我们把数字 1，2，3 相加，其实就是`1 + 2 + 3`的意思。

我们的`sum`函数将包括对一个数组中的所有值求和。

编写这个函数有两种方法:我们可以使用一个`for`循环，或者我们可以使用`reduce`函数。如果你想重新熟悉一下`reduce`函数，你可以阅读一下在 JavaScript 中使用 map()和 reduce()的[。](https://www.sitepoint.com/map-reduce-functional-javascript/)

使用`for`循环:

```
function sum(array){
    let total = 0
    for(let count = 0; count < array.length; count++){
        total = total + array[count]
    }
    return total
} 
```

使用`reduce`功能:

```
function sum(array){
    return array.reduce((sum, number) => sum + number, 0)
} 
```

这两个函数的工作方式完全相同(`reduce`函数只是一个内置的`for`循环)，并且将返回相同的数字(给定相同的数组)。但是`reduce`功能要简洁得多。

比如说:

```
sum([1,2,3,4]) === 10 // 1 + 2 + 3 + 4

sum([2,4,6,8]) === 20 // 2 + 4 + 6 + 8 
```

能够对一系列数字求和可能是 JavaScript `Math`对象中最有用和最需要的“缺失”数学运算。同样，`sum`函数是一个很好的检查工具。例如，在一个数独游戏中，我们可以通过检查列/行加起来是 45 (1 + 2 + 3 + 4 +…+ 9)来检查用户在该列或行中是否没有重复。如果我们想算出总账单，这个函数在网上购物应用程序中也非常好用——假设所有的价格都存储在一个数组中。

以购物应用程序为例，下面是一个我们如何在代码中使用它的例子:

```
const prices = [2.80, 6.10, 1.50, 1.00, 8.99, 2.99]

function totalCost(prices){
    return prices.reduce((sum, item) => sum + item, 0)
} 
```

## JavaScript: Product 中缺少数学方法

我们的`product`函数将以与`sum`函数相似的方式工作，除了不是*将列表中的所有数字相加*，而是*将它们乘以*。

同样，我们可以使用与第一个`sum`函数几乎相同的`for`循环:

```
function product(array){
    let total = 1
    for(let count = 0; count < array.length; count++){
        total = total * array[count]
    }
    return total
} 
```

注意，我们用`1`而不是`0`初始化`total`变量，否则我们将总是以 0 的`total`结束。

但是`reduce`函数在这种情况下仍然有效，并且仍然是编写函数的一种更简洁的方式:

```
function product(array){
    return array.reduce((total, num) => total*num, 1)
} 
```

以下是一些例子:

```
product([2,5,8,6]) === 480 // 2 x 5 x 8 x 6

product([3,7,10,2]) === 420 // 3 x 7 x 10 x 2 
```

这个函数的用途可能不太明显，但我发现当试图在一次计算中进行多次转换时，它们非常有用。例如，如果你想知道十包苹果的美元价格(每包 1.50 美元)，而不是一个巨大的乘法和，将所有的值存储在一个数组中并使用我们刚刚编写的`product`函数会更有效。

该数组的一个示例如下:

```
const pricePerKg = 1.50
const numberOfKg = 10
const conversionRate = 1.16
const conversion = [1.50, 10, 1.16]

const USprice = product([pricePerKg,numberOfKg,conversionRate]) 
```

## JavaScript 中缺失的数学方法:奇数和偶数

这些函数将接受一个数组长度形式的数字，并根据该数字是奇数还是偶数返回`true`或`false`。

对于一个偶数来说，它必须被 2 整除，对于一个奇数来说，它是相反的，不能被 2 整除。这将是函数的关键部分。

例如，Haskell 内置了这些函数，这使得事情变得简单多了，尤其是您可以这样写:

```
even 29
<< false

odd 29
<< true 
```

另一方面，Ruby 以方法的形式提供这些函数。这仍然更容易写:

```
29.even?
<< false

29.odd?
<< true 
```

用 JavaScript 编写这些函数最简单的方法是使用[余数运算符、`%`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Remainder) 。这将返回一个数除以另一个数的余数。例如:

```
11 % 3 === 2 // 11 divide 3 === 3 remainder 2 
```

下面是我们的`even`函数的一个例子:

```
function even(number){
    return number % 2 === 0
} 
```

正如我们所看到的，我们有一个`even`函数，它将一个数字作为其参数，并根据以下条件返回一个布尔值:

```
number % 2 === 0 
```

当数被二除时，如果余数等于零，我们知道它能被二整除，就会返回`true`。例如:

```
even(6) === true

even (9) === false 
```

下面是我们的`odd`函数的一个例子:

```
function odd(number){
    return number % 2 !== 0
} 
```

这两个函数非常相似:将一个数字作为参数，并根据条件返回一个布尔值:

```
number % 2 !== 0 
```

如果数除以 2 的余数不等于零，则该数是奇数，将返回`true`。例如:

```
odd(7) === true

odd(114) === false 
```

能够检查一个数字是奇数还是偶数是至关重要的，而且非常简单。乍一看，这似乎并不重要，但是它可以作为一种很好的输入验证技术——例如，使用数组长度，或者简单地通过检查两人游戏的获胜者。你可以记录已经玩了多少轮，如果数字是奇数，玩家 1 赢，如果是偶数，玩家 2 赢——假设第一轮算 1。

这些函数是可以互换的，我们很可能只需要使用一个。然而，拥有这两个函数可以更容易地跟踪`true`或`false`逻辑，尤其是在大块代码中。

我们可以这样编写上面的例子:

```
function checkWinner(gamesPlayed){
    let winner
    if(odd(gamesPlayed)){
        winner = "player1"
    }
    else{
        winner = "player2"
    }
    return winner
} 
```

## JavaScript 中缺少数学方法:triangleNumber

三角形数字听起来比它们实际上要花哨得多。它们只是所有整数的和，直到某个数字。

比如这是第五个三角形的数字:5 + 4 + 3 + 2 + 1 = 15。

这又回到了我们之前的数独例子。我们希望检查所有的数字都是唯一的，我们可以通过检查它们是否与 1 + 2 + 3 + 4 + 5 + 6 + 7 + 8 + 9 的结果相匹配来做到这一点。这当然是第九个三角数了！

当然，我们可以使用一个`for`循环来编写函数，就像这样:

```
function triangleNumber(number){
    let sum = 0
    for(let i=1; i < number + 1; i++){
        sum = sum + i
    }
    return sum
} 
```

然而，这将是一个非常低效的决定，因为有一个非常简单的公式来计算三角形数:`0.5 x (number) x (number + 1)`。

因此，我们函数的最有效版本应该是这样的:

```
function triangleNumber(number){
    return 0.5 * number * (number + 1)
} 
```

下面是一些我们如何使用它的例子:

```
triangleNumber(7) === 28 // 0.5 x 7 x 8

triangleNumber(123) === 7626 // 0.5 x 123 x 124 
```

## JavaScript 中缺少数学方法:阶乘

自然数(任何严格大于 0 的整数)的[阶乘](https://en.wikipedia.org/wiki/Factorial)是所有小于或等于该数的数的乘积。例如:3 阶乘(用`3!`表示)是`3 x 2 x 1 = 6`。

类似于`sum`和`product`函数，有两种方法可以创建我们的`factorial`函数:通过使用`for`循环，以及通过使用递归。如果你以前没有遇到过递归算法，它们本质上是反复调用自己的函数，直到它们到达一个“基本情况”。你可以在函数式 JavaScript 中的[递归中读到更多关于它们的内容。](https://www.sitepoint.com/recursion-functional-javascript/)

下面是我们如何使用一个`for`循环来创建我们的`factorial`函数:

```
function factorial(number){
  let total = 1
  for (let i = 1; i < number+1; i++){
    total = total * i
  }
  return total
} 
```

此函数循环遍历从 1 到数字的所有数字(每次循环递增)，并将总数乘以每个数字，然后返回最终总数(数字阶乘)。

下面是我们如何使用递归创建我们的`factorial`函数:

```
function factorial(number){
  if (number <= 0){
    return 1
  }
  else{
    return number * factorial(number - 1)
  }
} 
```

在这个函数中，我们的基本情况是零，因为`0!`出人意料地是一(对此的[证明](https://medium.com/math-simplified/why-is-zero-factorial-1-83cb340fe883)实际上非常有趣)。这意味着，当数字通过函数时，只要它不是零，它就会将自己乘以`factorial(number - 1)`。

为了帮助理解这个函数在每一次传递中到底在做什么，追踪算法可能会有所帮助。下面是用 3:

```
factorial(3) === 3*factorial(2) === 3*2*factorial(1) === 3*2*1*factorial(0) === 3*2*1*1 === 3*2*1 === 6 
```

无论哪种方式，两个函数都将返回相同的值。例如:

```
factorial(5) === 120 // 5 x 4 x 3 x 2 x 1 
```

## JavaScript 中缺少数学方法:因子

因子成对出现，每一对相乘在一起形成原始数。例如:

*   10 的因子是:1 和 10；2 和 5。
*   18 的因子是:1 和 18；2 和 9；3 和 6。

我们希望我们的`factors`函数接受一个数字，并返回其所有因子的数组。有许多方法可以编写这个函数，但最简单的方法是使用命令式方法，例如:

```
function factors(number){
    let factorsList = []
    for(let count = 1; count < number+1; count++){
        if(number % count === 0){
            factorsList.push(count)
        }
    }
    return factorsList
} 
```

首先，我们创建我们的数组——一开始就让它为空。然后，我们使用一个`for`循环来传递从 1 到数字本身的每个整数，并且在每次传递时，我们检查该数字是否能被该整数整除(或者在本例中是`count`)。

正如你所看到的，为了检查整除性，我们再次使用了`mod`符号。如果这个数能被整数整除，它就是一个因子，可以放入数组中。

然后返回数组，每次我们运行函数时，都会返回一个按升序排列的因子数组。例如:

```
factors(50) === [1,2,5,10,25,50] 
```

寻找一个数字的因子非常有用，特别是当你需要组织团队的时候——比如在网络游戏中，当你需要每个团队中有相同数量的用户时。例如，如果你有 20 个用户，每个队需要 10 名球员，你可以使用一个`factors`函数来匹配两个队的 10 名球员。类似地，如果每个团队需要四名球员，您可以使用`factors`函数将这四人匹配成五个团队。

实际上，它可能看起来像这样:

```
function createTeams(numberOfPlayers, numberOfTeams){
    let playersInEachTeam
    if(factors(numberOfPlayers).includes(numberOfTeams)){
        playersInEachTeam = numberOfPlayers / numberOfTeams
    }
    else{
        playersInEachTeam = "wait for more players"
    }
    return playersInEachTeam
} 
```

## JavaScript: isPrime 中缺少数学方法

这是你在学校学到的最早的条件之一，然而在日常生活中并不常用。简而言之，如果一个数有两个截然不同的因子，那么它就是质数。质数开始是:2，3，5，7，11，13，17，19 …以此类推，直到无穷。

它最初可能看起来像一个复杂的函数——如果我们没有编写一个非常有用的`factors`函数，它可能确实如此。如上所述，如果一个数有两个不同的因子，那么它就是质数，所以我们的函数就像这样简单:

```
function isPrime(number){
    return factors(number).length === 2
} 
```

这将根据其因子列表的长度是否为 2(换句话说，是否有两个因子)返回一个布尔值。

实际上，它看起来像这样:

```
isPrime(3) === true

isPrime(76) === false

isPrime(57) === true 
```

继续上面的“分组用户”的例子，如果用户的数量是质数，我们不能将他们平均分组(除非我们只有一个组，但这将破坏例子的目标)，这意味着我们必须等待另一个用户加入。所以，我们可以在这样的函数中使用它:

```
function addUsers(users){
    if(isPrime(users)){
        wait = true
    }
    else{
        wait = false
    }
} 
```

## JavaScript 中缺少数学方法:gcd(最大公约数)

**最大公约数**运算有时被称为“最高公因数”，它寻找两个数字共有的最大因数。

例如:

*   12 和 15 的 GCD 是 3。
*   8 和 4 的 GCD 是 4。

解决这个问题的一个简单方法是列出每个数字的所有因素(使用上面我们难以置信的函数)并比较这些列表。然而，比较列表需要一些非常漂亮但效率低下的数组操作。

但是这里有一个例子:

```
function gcd(number1, number2){
    let inCommon = []
    for(let i of factors(number1)){
        if(factors(number2).includes(i)){
            inCommon.push(i)
        }
    }
    return inCommon.sort((a,b)=> b - a)[0]
} 
```

这里，我们将一个空数组赋给变量`inCommon`，并遍历`number1`的因子数组(使用之前的函数)。如果`number2`的因子数组包含当前通道中的项目，我们将它推入我们的`inCommon`数组。

一旦我们有了两个数字共有的所有因子的数组，我们就返回数组中按降序排序的第一个值。换句话说，我们返回最大公约数。

可以想象，如果我们还没有创建`factors`函数，代码会非常庞大。

更简洁但更难的方法是使用递归。这是一个非常著名的算法，叫做[欧几里德算法](https://www.khanacademy.org/computing/computer-science/cryptography/modarithmetic/a/the-euclidean-algorithm#:~:text=The%20Algorithm,%3D%20B%E2%8B%85Q%20%2B%20R):

```
function gcd(number1, number2){
    if(number2 === 0){
        return number1
    }
    else{
        return gcd(number2, number1%number2)
    }
} 
```

我们这里的基本情况是`number2`等于 0，此时`number1`是最大公约数。否则，GCD 就是`number2`的 GCD 和`number1`的余数除以`number2`。

同样，两个函数将返回相同的东西。例如:

```
gcd(24, 16) === 8

gcd(75, 1) === 1 
```

## JavaScript 中缺少数学方法:lcm(最小公倍数)

最小公倍数在与最大公约数相似的波长上工作，但会找到两个数都是因数的最小整数。

例如:

*   2 和 6 的 LCM 是 6。
*   4 和 15 的 LCM 是 60。

不幸的是，对于这个函数，我们不能只创建每个数字的所有倍数的数组，因为这将是一个无限的列表。

然而，有一个非常有用的公式可以用来计算最低公倍数:

```
(number1 x number2) / the Greatest Common Divisor of the two numbers 
```

要检查公式，可以用上面的例子试试。2 和 6 的 LCM:

```
(2 x 6)/gcd(2,6) = 12/2 = 6 
```

幸运的是，我们刚刚创建了一个`gcd`函数，所以创建这个函数非常简单:

```
function lcm(number1, number2){
    return (number1*number2)/gcd(number1, number2)
} 
```

就是这样！我们需要做的就是返回上面的公式，它应该可以工作:

```
lcm(12, 9) === 36 // (12 x 9)/3 
```

这个函数可能没有任何明显的用途，但是我经常发现它非常适合两个事件以不同的间隔发生的情况，这意味着我们可以使用 LCM 来找出这两个事件何时同时发生。

例如，如果图像被编程为每六秒出现一次，文本段落被编程为每八秒出现一次，则图像和段落将在第 24 秒第一次同时出现。

## 结论

上面的所有函数都可以在下面的 CodePen 演示中找到，在这里你可以与函数进行交互，并看到它们在实践中的工作。

参见 [CodePen](https://codepen.io) 上 site point([@ site point](https://codepen.io/SitePoint))
的笔 [JavaScript 缺失的数学方法](https://codepen.io/SitePoint/pen/ExLLrLe)。