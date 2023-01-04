# 使用 Ramda.js 进行实用函数式编程

> 原文：<https://www.sitepoint.com/functional-programming-with-ramda/>

*这篇文章由[亚菲·伯哈努](https://www.sitepoint.com/author/yberhanu/)、[维尔丹·索蒂奇](https://www.sitepoint.com/author/vildansoftic/)、[贾尼·哈蒂凯宁](https://www.sitepoint.com/author/jhartikainen/)和[丹·普林斯](https://www.sitepoint.com/author/dprince/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

对我来说，JavaScript 如此有趣的原因之一是它的功能性。从一开始，函数就是 JavaScript 世界中的一等公民。这使得编写优雅且富于表现力的代码成为可能，这些代码可以很容易地以多种方式组合在一起。

然而，仅仅拥有进行一些函数式编程的能力并不会自动神奇地导致函数式编程。 [Ramda.js](http://ramdajs.com/) 是一个非常受欢迎的库(GitHub 上有超过 4k 的 stars ),我们可以用它来帮助我们开始使用 JavaScript 进行函数式编程。

## 入门指南

为了充分利用 Ramda.js，我们应该通过创建一个小的 Node.js 项目来习惯它的好处。我们可以简单地通过[节点包管理器](https://www.npmjs.com/) (npm)来安装它。

```
npm install ramda 
```

通常，我们会简单地将库的功能导入到名称空间`R`中。这样，所有对 Ramda 方法的调用都将有一个`R.`前缀。

```
var R = require('ramda'); 
```

当然，没有什么可以阻止我们在前端代码中使用 Ramda.js。在浏览器中，我们只需要包含库副本的正确路径。这可能就像下面的 HTML 代码片段一样简单。

```
<script src="ramda.min.js"></script> 
```

Ramda.js 不使用任何特定于 DOM 或 Node.js 的特性。它只是一个语言库/扩展，建立在 JavaScript 运行时已经公开的结构和算法之上(如 ECMAScript 5 中的标准)。

准备好开始了吗？让我们看看一些能力在行动！

## 概念

函数式编程中最重要的概念是[纯函数](https://www.sitepoint.com/an-introduction-to-reasonably-pure-functional-programming/)。一个纯函数是[幂等的](https://en.wikipedia.org/wiki/Idempotence)，不会改变任何状态。从数学上讲，这是有意义的，因为像`sin(x)`这样的函数看起来很自然，不依赖于任何外部状态。

除了有纯函数，我们还想有一个单参数函数。他们是最原始的。零变元函数通常表示外部状态会改变，因此不是纯的。但是在像 JavaScript 这样的语言中，我们通常会有接受不止一个参数的函数。

### 携带

将高阶函数(即，可以接受函数作为输入并发出函数作为输出的函数)与闭包(捕获局部变量)相结合的能力给了我们一个很好的出路: [currying](https://www.sitepoint.com/currying-in-functional-javascript/) 。Currying 是一个过程，在这个过程中，一个具有多个(比如说`n`)参数的函数被转换为一个具有单个参数的函数，返回另一个具有单个参数的函数。这种情况一直持续到收集到所有需要的参数。

假设我们想使用 Ramda.js 助手`is`编写一个单参数包装器，测试它的参数是否是一个`string`。下面的代码将完成这项工作。

```
function isString (test) {
    return R.is(String, test);
}

var result = isString('foo'); //=> true 
```

同样的事情可以用 currying 更容易地完成。由于`R.is`是 Ramda.js 的一部分，如果我们提供的参数少于函数的参数，库将自动返回一个 curried 函数:

```
var isString = R.is(String);
var result = isString('foo'); //=> true 
```

这就更有表现力了。因为我们使用了带有单个参数的`R.is`,所以我们收到了一个函数。在第二次调用时(记住，最初的函数调用需要两个参数),我们获得了结果。

但是如果我们一开始没有从 Ramda.js 的助手开始呢？假设我们已经在代码中定义了以下函数:

```
var quadratic = (a, b, c, x) => x * x * a + x * b + c;
quadratic(1, 0, 0, 2); //=> 4
quadratic(1, 0, 0)(2); //=> TypeError: quadratic(..) is not a function 
```

这是完整的二阶多项式。它有四个参数允许所有可能的值。但是通常，我们只想改变一组固定参数`a`、`b`和`c`的`x`。让我们看看如何用 Ramda.js 来改变这一点:

```
var quadratic = R.curry((a, b, c, x) => x * x * a + x * b + c);
quadratic(1, 0, 0, 2); //=> 4
quadratic(1, 0, 0)(2); //=> 4 
```

同样，我们能够简单地使用参数评估来别名特定的子集。例如，等式`x - 1`可以通过下式获得:

```
var xOffset = quadratic(0, 1, -1);
xOffset(0); //=> -1
xOffset(1); //=> 0 
```

如果函数的参数没有给出参数的数量，我们需要使用`curryN`并明确指定参数的数量。

Currying 是 Ramda.js 的核心，但是如果没有它，这个库就没那么有趣了。函数式编程中另一个重要的概念是不变性。

### 不可变结构

防止函数改变状态的最简单方法是只处理那些*不能*改变的数据结构。对于简单对象，我们需要只读访问器，例如

```
var position = {
    x: 5,
    y: 9
};
position.x = 10; // works! 
```

不会被允许。除了将属性声明为只读，我们还可以将它们转换为 getter 函数:

```
var position = (function (x, y) {
    return {
        getX: () => { return x; },
        getY: () => { return y; }
    };
})(5, 9);
position.getX() = 10; // does not work! 
```

现在这个已经好一点了，但是，对象还是可以改变的。这意味着某人可以添加一个自定义的`getX`函数定义，例如:

```
position.getX = function () {
  return 10;
}; 
```

实现不变性的最好方法是使用`Object.freeze`。与关键字`const`一起，我们可以引入一个不能改变的不可变变量。

```
const position = Object.freeze({ x: 5, y: 9 }); 
```

另一个例子是列表。将一个元素添加到一个不可变的列表中需要你复制一个原始列表，并在末尾添加新元素。当然，我们也可以在原对象上使用不变性的知识来优化实现。这样我们可以用一个简单的引用来代替副本。本质上，这可能成为一种链表。我们应该意识到，标准的 JavaScript 数组是可变的，因此需要复制以确保正确性。

像`append()`这样的方法处理 JavaScript 数组并返回这样的数组。运算是幂等的；如果我们用相同的参数多次调用这个函数，我们将总是得到相同的结果。

```
R.append('tests', ['write', 'more']); //=> ['write', 'more', 'tests']
R.append('tests', ['write', 'more']); //=> ['write', 'more', 'tests']
R.append('tests', ['write', 'more']); //=> ['write', 'more', 'tests'] 
```

还有一个`remove`方法，它返回没有指定条目的给定数组。它的工作原理如下:

```
R.remove('write', 'tests', ['write', 'more', 'tests']); //=> ['more'] 
```

因为它有大量灵活的参数，我们需要前面提到的`curryN`函数来应用 currying。还有一组有用的通用助手可用。

## 实用方法

对于所有帮助函数来说，最重要的概念是对参数进行排序以便于奉承。一个参数被修改的越频繁，它就越不可能被放在其他参数之前。

### 总和()和范围()

常见的疑点如 [sum](http://ramdajs.com/docs/#sum) 和 [range](http://ramdajs.com/docs/#range) 当然可以在 Ramda.js 中找到:

```
R.sum(R.range(1, 5)); //=> 10 
```

因此，对于`range()`助手，我们可以使用 currying 创建一个包装器:

```
var from10ToExclusive = R.range(10);
from10ToExclusive(15); //=> [10, 11, 12, 13, 14] 
```

如果我们想用一个固定的(独占的)最大值来包装它呢？价值？Ramda.js 通过使用一个由`R.__`表示的特殊参数来覆盖我们:

```
var to14FromInclusive = R.range(R.__, 15);
to14FromInclusive(10); //=> [10, 11, 12, 13, 14] 
```

### 地图()

此外，Ramda.js 试图为 JavaScript 的核心函数(如`Array.prototype.map`)提供“更好”的解决方案。这些选择有不同的参数顺序和现成的奉承。

对于[映射](http://ramdajs.com/docs/#map)功能，如下所示:

```
R.map(x => 2 * x, [1, 2, 3]); //=> [2, 4, 6] 
```

### 道具()

另一个有用的实用程序是 [prop](http://ramdajs.com/docs/#prop) 函数，它试图获取指定属性的值。如果给定的属性不存在，则返回`undefined`。如果值真的是`undefined`，这可能会有歧义，但实际上我们很少会在意。

```
R.prop('x', { x: 100 }); //=> 100
R.prop('x', { y: 50 }); //=> undefined 
```

### zipWith()

如果前面介绍的方法没有让您相信 Ramda.js 可能提供一些有用的东西，那么下面的这些方法可能会更有趣。这一次，我们不会查看一个具体的示例，而是查看任意选择的场景。

假设我们有两个列表，我们想加入它们。这实际上使用`zip`函数相当简单。然而，通常的结果(元素的数组，它们本身是二值数组)可能不是我们想要的。这就是 [zipWith](http://ramdajs.com/docs/#zipWith) 功能发挥作用的地方。它使用任意函数将这些值映射到一个值。

```
var letters = ["A", "B", "C", "D", "E"];
var numbers = [1, 2, 3];
var zipper = R.zipWith((x, y) => x + y);
zipper(letters, numbers); // ["A1", "B2", "C3"] 
```

类似地，我们可以引入向量的点积:

```
var dot = R.pipe(R.zipWith((x, y) => x * y), R.sum);
dot([1, 2, 3], [1, 2, 3]) // 14 
```

我们通过乘法压缩两个数组(产生`[1, 4, 9]`)并将结果传递给 sum 函数。

无论如何，使用可枚举数是一个大主题。Ramda.js 带来了许多有用的助手，这并不奇怪。我们已经引入了`R.map`来为每个元素应用一个函数。同样，也有帮助者减少元素的数量。要么通过最通用的`filter`函数(产生另一个数组)，要么通过`reduce`函数转换成单个值。

### 链条()

对数组进行操作附带了一些有用的辅助函数。例如，使用[链](http://ramdajs.com/docs/#chain)我们可以很容易地合并数组。假设我们有一个函数`primeFactorization`，它使用一个数字作为输入，并给出一个带有质因数的数组作为输出，我们可以将应用该函数的结果与一组数字组合起来，如下所示:

```
R.chain(primeFactorization, [4, 7, 21]); //=> [2, 2, 7, 3, 7] 
```

### 实际例子

到目前为止一切顺利。现在很大的问题是:使用 Ramda.js 介绍的这些概念，我们在日常工作中有什么好处？让我们假设我们有下面的(已经很好看了)代码片段。

```
fetchFromServer()
  .then(JSON.parse)
  .then(function (data){ return data.posts })
  .then(function (posts){ 
    return posts.map(function (post){ return post.title }) 
  }); 
```

如何使用 Ramda.js 使其更具可读性？好吧，第一行已经尽善尽美了。第二个已经很乱了。我们真正想要的是提取所提供参数的`posts`属性。

最后，我们有一种混乱的第三线。这里我们尝试迭代所有的文章(由参数提供)。同样，它的唯一目的是提取一个特定的属性。以下解决方案如何:

```
fetchFromServer()
  .then(JSON.parse)
  .then(R.prop('posts'))
  .then(R.map(R.prop('title'))); 
```

由于 Ramda.js 支持的函数式编程，这可能接近于可读性方面的最佳解决方案。但是，我们应该注意到，ECMAScript 6 中引入的“胖箭头”语法也可以产生非常简洁、可读的代码:

```
fetchFromServer()
  .then(JSON.parse)
  .then(json => json.posts)
  .then(posts => posts.map(p => p.title)); 
```

这几乎是可读的，不需要任何关于 Ramda.js 的知识。此外，我们减少了抽象的数量——这只会有利于性能和可维护性。

## 镜头

最后，我们还应该谈谈有用的对象助手。这里[镜头](http://ramdajs.com/docs/#lens)功能值得一提。

镜头是一种特殊的对象，可以和对象或数组一起传递给某些 Ramda.js 函数。它允许这些函数分别从对象或数组的特定属性或索引中检索或转换数据。

假设我们有一个带有两个键`x`和`y`的对象——就像本文开头给出的不变性例子一样。我们可以创建一个透镜来“聚焦”感兴趣的属性，而不是用 getter 和 setter 方法将一个对象包装在另一个对象中。

要创建访问对象属性`x`的镜头，我们可以执行以下操作:

```
var x = R.lens(R.prop('x'), R.assoc('x')); 
```

而`prop`是一个标准的 getter(这个已经介绍过了)，而 [assoc](http://ramdajs.com/docs/#assoc) 是一个 setter 函数(三值语法:key，value，object)。

现在我们可以使用 Ramda.js 中的函数来访问这个镜头定义的属性。

```
var xCoordinate = R.view(x, position);
var newPosition = R.set(x, 7, position); 
```

注意，该操作保持给定的`position`对象不变(与我们是否冻结它无关)。

需要注意的是， [set](http://ramdajs.com/docs/#over) 只是[对](http://ramdajs.com/docs/#over)的特化，两者类似但取的是函数而不是任意值。然后，该函数将用于转换该值。例如，以下调用将 x 坐标乘以 3:

```
var newPosition = R.over(x, R.multiply(3), position); 
```

## Ramda.js，lodash，还是别的？

一个合理的问题当然是为什么选择 ramda . js——为什么我们不应该使用 lodash 或其他任何东西来代替？当然，有人可能会说 Ramda.js 更新了，因此一定更好，但这与事实相去甚远。事实是，Ramda.js 是按照函数原则构建的——在参数放置和选择方面采用了新的方法(对于 JavaScript 库来说)。

例如，Ramda.js 中的列表迭代器默认只传递项目，而不传递列表。另一方面，其他库(如 lodash)的标准是将条目和索引传递给回调函数。这似乎是一个微妙的问题，但是它阻止了您使用像`parseInt()`(它接受一个可选的第二个参数)这样方便的内置函数，而使用 Ramda.js 这就很好了。

最终，选择什么的决定可能是由特定的需求或团队的经验和/或知识决定的，但肯定有一些好的理由给予 Ramda.js 应有的关注。

## 进一步阅读

*   [高阶函数](https://www.sitepoint.com/higher-order-functions-javascript/)
*   [为什么库里有帮助](https://hughfdjackson.com/javascript/why-curry-helps/)
*   [不变性](https://www.sitepoint.com/immutability-javascript/)
*   [为什么是拉姆达？](http://fr.umio.us/why-ramda/)
*   [Ramda 文档](http://ramdajs.com/docs/)
*   [用 Ramda.js 进行函数式编程](http://neiro.io/2015/09/05/functional-programming-with-ramda-js.html)

## 结论

函数式编程不应被视为灵丹妙药。相反，它应该被视为对我们现有工具箱的自然补充，为我们提供更高的可组合性、更大的灵活性和更好的容错性/健壮性。现代 JavaScript 库已经尝试采用一些函数概念来利用这些优势。js 是一个强大的工具，可以用功能实用程序扩展您自己的库。

你对函数式编程有什么看法？你看到它在哪里发光？请在评论中告诉我！

## 分享这篇文章