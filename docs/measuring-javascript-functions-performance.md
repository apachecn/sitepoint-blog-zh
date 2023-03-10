# 测量 JavaScript 函数的性能

> 原文：<https://www.sitepoint.com/measuring-javascript-functions-performance/>

性能在软件中一直扮演着至关重要的角色。在网络上，性能甚至更重要，因为如果我们提供慢速网页，我们的用户可以很容易地改变网站并访问我们的竞争对手。作为专业的网站开发者，我们必须考虑这个问题。许多旧的 web 性能优化最佳实践，如最小化请求、使用 CDN 和不编写渲染阻塞代码，今天仍然适用。然而，随着越来越多的 web 应用程序使用 JavaScript，验证我们的代码速度很重要。

假设你有一个正常工作的函数，但是你怀疑它没有想象中的那么快，并且你有一个改进它的计划。你如何证明这个假设？今天测试 JavaScript 函数性能的最佳实践是什么？一般来说，完成这项任务的最佳方式是使用内置的`performance.now()`函数，并测量函数执行前后的时间。

在本文中，我们将讨论如何测量代码执行时间和技术，以避免一些常见的陷阱。

## Performance.now()

[高分辨率时间 API](https://www.w3.org/TR/hr-time/) 提供了一个名为`now()`的函数，该函数返回一个 [`DOMHighResTimeStamp`](https://www.w3.org/TR/hr-time/#sec-DOMHighResTimeStamp) 对象。它是一个浮点数，以毫秒<q cite="https://www.w3.org/TR/hr-time/#sec-DOMHighResTimeStamp">反映当前时间，精确到千分之一毫秒</q>。单独来看，这个数字不会给你的分析增加多少价值，但是两个数字之间的差异可以准确地描述过去了多少时间。

除了比内置的`Date`对象更精确之外，它还是“单调”的。简而言之，这意味着它不受系统(如您的笔记本电脑操作系统)定期校正系统时间的影响。更简单地说，定义两个`Date`实例并计算差异并不代表已经过去的时间。

“单调”的数学定义是<q>(指函数或量)以这样一种方式变化，即它要么从不减少，要么从不增加</q>。

另一种解释方式是，试着想象在一年中时钟前进或后退的时候使用它。例如，当你们国家的时钟都同意为了最大化白天的阳光而跳过一个小时。如果您要在时钟返回一小时之前创建一个`Date`实例，然后创建另一个`Date`实例，查看差异，它会显示类似“1 小时 3 秒 123 毫秒”的内容。对于`performance.now()`的两个实例，差别将是“3 秒 123 毫秒和 456789 千分之一毫秒”。

在这一节中，我不会详细介绍这个 API。因此，如果您想了解更多信息并查看一些使用示例，我建议您阅读文章[发现高分辨率时间 API](https://www.sitepoint.com/discovering-the-high-resolution-time-api/) 。

现在您已经知道了什么是高分辨率时间 API 以及如何使用它，让我们来深入研究一些潜在的陷阱。但在此之前，让我们定义一个名为`makeHash()`的函数，我们将在本文的剩余部分使用它。

```
function makeHash(source) {
  var hash = 0;
  if (source.length === 0) return hash;
  for (var i = 0; i < source.length; i++) {
    var char = source.charCodeAt(i);
    hash = ((hash<<5)-hash)+char;
    hash = hash & hash; // Convert to 32bit integer
  }
  return hash;
}
```

此类功能的执行可以按如下所示进行测量:

```
var t0 = performance.now();
var result = makeHash('Peter');
var t1 = performance.now();
console.log('Took', (t1 - t0).toFixed(4), 'milliseconds to generate:', result);
```

如果您在浏览器中运行这段代码，您应该会看到类似这样的内容:

```
Took 0.2730 milliseconds to generate: 77005292
```

这段代码的现场演示如下所示:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [YXmdNJ](http://codepen.io/SitePoint/pen/YXmdNJ/) 。

记住这个例子，让我们开始讨论。

### 陷阱 1——意外测量不重要的事情

在上面的例子中，你可以注意到我们在一个`performance.now()`和另一个`performance.now()`之间做的唯一的事情就是调用函数`makeHash()`并将其值赋给变量`result`。这给了我们执行那个函数所花费的时间，没有其他的了。这种测量也可以如下所述进行:

```
var t0 = performance.now();
console.log(makeHash('Peter'));  // bad idea!
var t1 = performance.now();
console.log('Took', (t1 - t0).toFixed(4), 'milliseconds');
```

下面显示了该片段的现场演示:

在[码笔](http://codepen.io)上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [PqMXWv](http://codepen.io/SitePoint/pen/PqMXWv/) 。

但是在这种情况下，我们将测量调用函数`makeHash('Peter')` *所需的时间，以及*在控制台上发送和打印输出所需的时间。我们不知道这两次手术各花了多长时间。你只知道组合时间。此外，发送和打印输出所需的时间会因浏览器而异，甚至会因当时浏览器中正在进行的操作而异。

也许你完全意识到`console.log`慢得不可预测。但是，执行多个函数同样是错误的，即使每个函数都不涉及任何 I/O。例如:

```
var t0 = performance.now();
var name = 'Peter';
var result = makeHash(name.toLowerCase()).toString();
var t1 = performance.now();
console.log('Took', (t1 - t0).toFixed(4), 'milliseconds to generate:', result);
```

同样，我们也不知道执行时间是如何分配的。是变量赋值、`toLowerCase()`调用还是`toString()`调用？

### 缺陷 2——只测量一次

另一个常见的错误是只进行一次测量，总结所用的时间，并在此基础上得出结论。在不同的时间可能会完全不同。执行时间在很大程度上取决于各种因素:

*   编译器预热的时间(例如将代码编译成字节码的时间)
*   主线程忙于做其他我们没有意识到的事情
*   你的电脑的 CPU 正忙于一些事情，使你的整个浏览器变慢

一个渐进的改进是重复执行函数，就像这样:

```
var t0 = performance.now();
for (var i = 0; i < 10; i++) {
  makeHash('Peter');
}
var t1 = performance.now();
console.log('Took', ((t1 - t0) / 10).toFixed(4), 'milliseconds to generate');
```

此示例的现场演示如下所示:

在[码笔](http://codepen.io)上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [Qbezpj](http://codepen.io/SitePoint/pen/Qbezpj/) 。

这种方法的风险是，我们浏览器的 JavaScript 引擎可能会进行次优化，这意味着第二次使用相同的输入调用函数时，它可以从记住第一次输出中受益，并简单地再次使用它。要解决这个问题，您可以使用许多不同的输入字符串，而不是重复发送相同的输入字符串(例如`'Peter'`)。显然，使用不同输入进行测试的问题在于，我们测量的函数自然会花费不同的时间。也许有些输入比其他输入导致更长的执行时间。

### 陷阱 3——过于依赖平均值

在上一节中，我们了解到重复运行某些东西是一个很好的实践，最好是使用不同的输入。然而，我们必须记住，不同输入的问题是，执行时间可能比所有其他输入长得多。所以让我们后退一步，输入同样的信息。假设我们将相同的输入发送 10 次，并打印出每一次花费的时间。输出可能如下所示:

```
Took 0.2730 milliseconds to generate: 77005292
Took 0.0234 milliseconds to generate: 77005292
Took 0.0200 milliseconds to generate: 77005292
Took 0.0281 milliseconds to generate: 77005292
Took 0.0162 milliseconds to generate: 77005292
Took 0.0245 milliseconds to generate: 77005292
Took 0.0677 milliseconds to generate: 77005292
Took 0.0289 milliseconds to generate: 77005292
Took 0.0240 milliseconds to generate: 77005292
Took 0.0311 milliseconds to generate: 77005292
```

注意第一次，这个数字和其他九次完全不同。最有可能的是，这是因为我们浏览器中的 JavaScript 引擎进行了一些子优化，需要一些预热。我们几乎无法避免这种情况，但我们可以考虑一些好的补救措施来防止错误的结论。

一种方法是计算最后九次的平均值。另一个更实际的方法是收集所有结果，并计算一个[中位数](https://en.wikipedia.org/wiki/Median)。基本上就是把所有的结果排好队，按顺序排序，挑中间的。这就是`performance.now()`如此有用的地方，因为你得到一个你可以做任何事情的数字。

让我们再试一次，但这次我们将使用中值函数:

```
var numbers = [];
for (var i=0; i < 10; i++) {
  var t0 = performance.now();
  makeHash('Peter');
  var t1 = performance.now();
  numbers.push(t1 - t0);
}

function median(sequence) {
  sequence.sort();  // note that direction doesn't matter
  return sequence[Math.ceil(sequence.length / 2)];
}

console.log('Median time', median(numbers).toFixed(4), 'milliseconds');
```

### 陷阱 4——以可预测的顺序比较函数

我们知道多次测量某样东西并取平均值总是一个好主意。此外，上一个例子告诉我们，最好使用中间值而不是平均值。

现在，实际上，测量函数执行时间的一个很好的用途是了解几个函数中哪一个更快。假设我们有两个函数，它们接受相同类型的输入并产生相同的结果，但是它们内部的工作方式不同。

假设我们想要一个函数，如果某个字符串在其他字符串的数组中，它返回`true`或`false`，但是这个*不区分大小写*。换句话说，我们不能使用`Array.prototype.indexOf`，因为它不是不区分大小写的*。这里有一个这样的实现:*

```
function isIn(haystack, needle) {
  var found = false;
  haystack.forEach(function(element) {
    if (element.toLowerCase() === needle.toLowerCase()) {
      found = true;
    }
  });
  return found;
}

console.log(isIn(['a','b','c'], 'B'));  // true
console.log(isIn(['a','b','c'], 'd'));  // false
```

我们立即注意到这可以改进，因为即使我们有一个早期的匹配，循环也总是遍历所有的元素。让我们试着用一个好的旧的`for`循环写一个更好的版本。

```
function isIn(haystack, needle) {
  for (var i = 0, len = haystack.length; i < len; i++) {
    if (haystack[i].toLowerCase() === needle.toLowerCase()) {
      return true;
    }
  }
  return false;
}

console.log(isIn(['a','b','c'], 'B'));  // true
console.log(isIn(['a','b','c'], 'd'));  // false
```

现在让我们看看哪一个最快。我们通过运行每个函数 10 次并收集所有测量值来实现这一点:

```
function isIn1(haystack, needle) {
  var found = false;
  haystack.forEach(function(element) {
    if (element.toLowerCase() === needle.toLowerCase()) {
      found = true;
    }
  });
  return found;
}

function isIn2(haystack, needle) {
  for (var i = 0, len = haystack.length; i < len; i++) {
    if (haystack[i].toLowerCase() === needle.toLowerCase()) {
      return true;
    }
  }
  return false;
}

console.log(isIn1(['a','b','c'], 'B'));  // true
console.log(isIn1(['a','b','c'], 'd'));  // false
console.log(isIn2(['a','b','c'], 'B'));  // true
console.log(isIn2(['a','b','c'], 'd'));  // false

function median(sequence) {
  sequence.sort();  // note that direction doesn't matter
  return sequence[Math.ceil(sequence.length / 2)];
}

function measureFunction(func) {
  var letters = 'a,b,c,d,e,f,g,h,i,j,k,l,m,n,o,p,q,r,s,t,u,v,w,x,y,z'.split(',');
  var numbers = [];
  for (var i = 0; i < letters.length; i++) {
    var t0 = performance.now();
    func(letters, letters[i]);
    var t1 = performance.now();
    numbers.push(t1 - t0);
  }
  console.log(func.name, 'took', median(numbers).toFixed(4));
}

measureFunction(isIn1);
measureFunction(isIn2);
```

我们运行它并得到以下输出:

```
true
false
true
false
isIn1 took 0.0050
isIn2 took 0.0150
```

此示例的现场演示如下所示:

通过[码笔](http://codepen.io)上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [YXmdZJ](http://codepen.io/SitePoint/pen/YXmdZJ/) 。