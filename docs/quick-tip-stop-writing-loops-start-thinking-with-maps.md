# 快速提示:停止写循环，开始用地图思考

> 原文：<https://www.sitepoint.com/quick-tip-stop-writing-loops-start-thinking-with-maps/>

**在大多数程序员的学习道路上，有一段时间他们会发现一个叫做 [map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map "Array.prototype.map() documentation on MDN") 的函数。在发现`map`函数之前，每当你需要你的机器多次执行某个动作时，你可能会使用一个`for`循环。在一般情况下，该操作会转换一些数据。**

## 必要的

例如，您团队中的一名销售人员交给您一大串电子邮件地址。在电子邮件地址传入时，没有太多的精力去验证它们，所以有些是大写的，有些是小写的，有些是两者的混合。转换数据的`for`循环方法如下所示:

```
var mixedEmails = ['JOHN@ACME.COM', 'Mary@FooBar.com', 'monty@spam.eggs'];

function getEmailsInLowercase(emails) {
  var lowercaseEmails = [];

  for (var i = 0; i < emails.length; i++) {
    lowercaseEmails.push(emails[i].toLowerCase());
  }

  return lowercaseEmails;
}

var validData = getEmailsInLowercase(mixedEmails); 
```

这种方法是可行的，但是它包含了大量痛苦的仪式来实现实际上简单而常见的操作。我们使用`for`循环的函数编码了太多我们不想表达的细节。一些痛处:

*   我们告诉机器，它需要创建一个临时列表，将电子邮件地址复制到其中。
*   我们告诉机器首先计算我们想要转换多少个电子邮件地址，然后在电子邮件地址列表中移动该次数。
*   我们告诉机器创建一个计数器，这样它就知道它在电子邮件地址列表的哪个位置操作。
*   我们告诉机器它应该朝哪个方向计数，这意味着在这个阶段*排序*是很重要的——事实并非如此。

这是编程的*必要*方法。我们在命令机器如何工作。

## 困惑

我们想要清理前面的方法，所以我们使用了`map`函数。当我们通读任何关于`map`函数的文档时，我们会看到像“数组”、“每个”和“索引”这样的词。这意味着我们可以把`map`当作一个稍微不那么隆重的`for`环线，事实上我们可以做到。让我们改变我们原来的功能。

```
var mixedEmails = ['JOHN@ACME.COM', 'Mary@FooBar.com', 'monty@spam.eggs'];

function getEmailsInLowercase(emails) {
  var lowercaseEmails = [];

  emails.map(function(email) {
    lowercaseEmails.push(email.toLowerCase());
  });

  return lowercaseEmails;
}

var validData = getEmailsInLowercase(mixedEmails); 
```

这是可行的，并且比`for`循环方法更干净。除了在代码片段中有更少的字符之外，我们没有告诉机器如何跟踪索引或者它应该通过我们的列表向哪个方向工作。

然而，这还不够。这仍然是编程的*必要的*方法。我们仍然发号施令过多。我们关心的是我们不需要关心的细节，而且我们每走一步都握着电脑的手。

## 宣言的

我们需要的是改变我们思考数据转换的方式。我们不会认为“计算机，我需要你取这个列表的第一个元素，然后小写，然后推到这个另一个列表，然后返回这个列表”。相反，我们认为“计算机，我有一个大小写混合的电子邮件地址列表，我需要一个小写电子邮件地址列表。这是一个小写的函数。

```
var mixedEmails = ['JOHN@ACME.COM', 'Mary@FooBar.com', 'monty@spam.eggs'];

function downcase(str) {
  return str.toLowerCase();
}

var validData = mixedEmails.map(downcase); 
```

毫不夸张地说，这对人类来说更具可读性，这就是编程的意义所在:向其他*人*表达想法，无论他们是其他开发人员还是你未来的自己。上面的代码片段说“我们的有效数据是映射到`downcase`函数的混合邮件列表”。

像这样在如此高的层次上表达思想是函数式编程学校的核心原则，这也是我们正在做的事情。复杂的程序是由简单的组件组合而成的，这些组件具有单一的功能，并且易于理解。

这种方法还有几个优点。排名不分先后:

*   我们的小写函数提供了尽可能简单的接口；一个值输入，一个值输出。
*   运动的部分更少，所以我们的逻辑更容易理解，更容易测试，更不容易坏。
*   我们的逻辑只做一件事，所以很容易重用和与其他函数结合，以便表达更复杂的思想。
*   沿着这条声明性的道路前进时，代码库的规模急剧缩小并不罕见。

尽管使用匿名函数作为`map()`的第一个参数很常见，但我建议将函数抽出来，并给它们起一个有意义的名字。这有助于记录您使用该函数的意图，因此另一个开发人员稍后可以通过读取名称来理解该方法的作用，而不必在头脑中解析实现。

## 浏览器支持

原生的`map()`方法在 [ECMAScript 5 规范](https://people.mozilla.org/~jorendorff/es5.html#sec-15.4.4.19 "Array.prototype.map() in ES5 specification")中定义，并且具有良好的[浏览器支持](http://kangax.github.io/compat-table/es5/#test-Array.prototype.map "Support for Array.prototype.map")。如果你需要支持早于 9 的 Internet Explorer 版本，你可以引入一个 [polyfill](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map#Polyfill "Array.prototype.map() polyfill on MDN") 或者使用一个库，比如[下划线](http://underscorejs.org/ "Underscore documentation")或者 [Lodash](https://lodash.com/ "Lodash documentation") 。

## 表演

在绝大多数情况下，`map`函数和`for`循环之间的选择在真实世界的代码中没有性能影响。`for`循环*比* [稍微快一点](http://jsperf.com/for-loop-vs-array-prototype-map "for loop vs Array.prototype.map() on jsPerf")，但是这种差异不值得考虑，除非你正在编写某种形式的图形或物理引擎，即使这样，在剖析你的关键性能代码之前引入这种优化也是没有意义的，这样你就有一些硬数据要处理。

## 包扎

将逻辑分成简单的纯方法并将这些方法应用于数据结构的函数方法将使您的代码更简洁、更健壮、更容易理解。这个概念是通用的，更通用的概念允许更大的代码重用。学会以这种方式思考不仅会提高您的 JavaScript，还会提高您使用大多数其他编程语言的能力；您可以像在 Haskell 中一样在 Ruby 中应用这种方法。

所以，下一次你伸手去拿一个`for`环的时候，重新考虑一下。请记住，开始时的数据结构不一定是平面数组；您可以从一个对象开始，取出它的值，然后在其上映射一个函数，最后对结果数组进行排序。你甚至可以使用一个库，比如下划线来映射对象，保留关键字。

你能想到更多使用`map()`功能的创造性方法吗？尝试，看着你的代码收缩。

## 分享这篇文章