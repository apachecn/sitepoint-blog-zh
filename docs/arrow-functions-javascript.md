# JavaScript 中的箭头函数:如何使用简洁的语法

> 原文：<https://www.sitepoint.com/arrow-functions-javascript/>

了解关于 JavaScript 箭头函数的所有信息。我们将向您展示如何使用 ES6 箭头语法，以及在代码中利用箭头函数时需要注意的一些常见错误。你会看到很多例子来说明它们是如何工作的。

JavaScript 箭头函数随着 ECMAScript 2015(也称为 ES6)的发布而出现。由于其简洁的语法和对 *[这个](https://www.sitepoint.com/inner-workings-javascripts-this-keyword/)* 关键字的处理，arrow 函数很快成为开发人员最喜欢的特性。

## 箭头函数语法:重写常规函数

函数就像食谱一样，你可以在其中存储有用的指令来完成你需要在程序中发生的事情，比如执行一个动作或者返回值。通过调用您的函数，您可以执行食谱中包含的步骤。您可以在每次调用该函数时这样做，而无需一次又一次地重写配方。

以下是声明一个函数，然后用 JavaScript 调用它的标准方法:

```
// function declaration
function sayHiStranger() {
  return 'Hi, stranger!'
}

// call the function
sayHiStranger() 
```

您也可以编写与[函数表达式](https://www.sitepoint.com/function-expressions-vs-declarations/)相同的函数，如下所示:

```
const sayHiStranger = function () {
  return 'Hi, stranger!'
} 
```

JavaScript 箭头函数总是表达式。下面是如何使用粗箭头符号重写上面的函数:

```
const sayHiStranger = () => 'Hi, stranger' 
```

这样做的好处包括:

*   只有一行代码
*   没有`function`关键字
*   没有`return`关键字
*   没有花括号{}

在 JavaScript 中，[函数是“一等公民”。](https://www.sitepoint.com/higher-order-functions-javascript/)可以将函数存储在变量中，作为参数传递给其他函数，作为值从其他函数返回。您可以使用 JavaScript 箭头函数完成所有这些工作。

## 无 Parens 语法

在上面的例子中，函数没有参数。在这种情况下，您必须在粗箭头(`=>`)符号前添加一组空括号`()`。当[有不止一个参数](http://jsbin.com/doporef/edit?html,js,console,output)时，情况也是如此:

```
const getNetflixSeries = (seriesName, releaseDate) => `The ${seriesName} series was released in ${releaseDate}`
// call the function
console.log(getNetflixSeries('Bridgerton', '2020') )
// output: The Bridgerton series was released in 2020 
```

然而，[只有一个参数](http://jsbin.com/fitubus/edit?html,js,console,output)，您可以省略括号(您不必这样做，但是您可以这样做):

```
const favoriteSeries = seriesName => seriesName === "Bridgerton" ? "Let's watch it" : "Let's go out"
// call the function
console.log(favoriteSeries("Bridgerton"))
// output: "Let's watch it" 
```

不过，要小心。例如，如果您决定使用一个[默认参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters)，您必须[将其括在括号](http://jsbin.com/cesehiv/edit?html,js,console,output)中:

```
// with parentheses: correct
const bestNetflixSeries = (seriesName = "Bridgerton") => `${seriesName} is the best`
// outputs: "Bridgerton is the best"
console.log(bestNetflixSeries())

// no parentheses: error
const bestNetflixSeries = seriesName = "Bridgerton" => `${seriesName} is the best`
// Uncaught SyntaxError: invalid arrow-function arguments (parentheses around the arrow-function may help) 
```

仅仅因为你能，并不意味着你应该。混合着一点轻松、善意的讽刺，凯尔·辛普森(因*你不知道 JS* 出名)已经把他的想法放在了省略[这个流程图](https://github.com/getify/You-Dont-Know-JS/blob/1st-ed/es6%20&%20beyond/fig1.png)中的括号上。

## 隐性回报

当函数体中只有一个表达式时，可以使 ES6 arrow 语法更加简洁。你可以把所有东西都放在一行，去掉花括号，去掉关键字`return`。

在上面的例子中，您已经看到了这些漂亮的一行程序是如何工作的。这里还有一个例子，只是为了更好的衡量。`orderByLikes()`函数执行 tin 上所说的操作:也就是说，它返回一个网飞系列对象的数组，这些对象按最高点赞数排序:

```
// using the JS sort() function to sort the titles in descending order 
// according to the number of likes (more likes at the top, fewer at the bottom
const orderByLikes = netflixSeries.sort( (a, b) => b.likes - a.likes )

// call the function 
// output:the titles and the n. of likes in descending order
console.log(orderByLikes) 
```

这很酷，但是要注意代码的可读性——特别是当使用一行程序和无括号的 ES6 arrow 语法对一堆 arrow 函数进行排序时，就像在[这个例子](http://jsbin.com/bicapuf/edit?html,js,console,output)中一样:

```
const greeter = greeting => name => `${greeting}, ${name}!` 
```

那里发生了什么事？尝试使用常规函数语法:

```
function greeter(greeting) {
  return function(name) {
    return `${greeting}, ${name}!` 
  }
} 
```

现在，您可以快速看到外部函数`greeter`如何拥有一个参数`greeting`，并返回一个匿名函数。这个内部函数本身有一个名为`name`的参数，并使用`greeting`和`name`的值返回一个字符串。下面是调用该函数的方法:

```
const myGreet = greeter('Good morning')
console.log( myGreet('Mary') )   

// output: 
"Good morning, Mary!" 
```

### 小心这些隐含的返回错误

当您的 JavaScript arrow 函数包含多个语句时，您需要用花括号将它们全部括起来，并使用`return`关键字。

在下面的[代码中，该函数构建了一个包含几个网飞系列的标题和摘要的对象(网飞评论来自](http://jsbin.com/nulexar/edit?html,js,console,output)[烂番茄](https://editorial.rottentomatoes.com/guide/best-netflix-shows-and-movies-to-binge-watch-now/)网站) :

```
const seriesList = netflixSeries.map( series => {
  const container = {}
  container.title = series.name 
  container.summary = series.summary

  // explicit return
  return container
} ) 
```

`.map()`函数中的 arrow 函数在一系列语句中发展，在语句结束时返回一个对象。这使得在函数体周围使用花括号变得不可避免。

此外，由于使用了花括号，隐式返回不是一个选项。您必须使用`return`关键字。

如果您的函数*使用隐式返回返回一个对象文字*，您需要将该对象放在圆括号内。不这样做将导致错误，因为 JavaScript 引擎错误地将对象文字的花括号解析为函数的花括号。正如你刚才注意到的，当你在一个箭头函数中使用花括号时，你不能省略 return 关键字。

前面代码的较短版本演示了以下语法:

```
// Uncaught SyntaxError: unexpected token: ':'
const seriesList = netflixSeries.map(series => { title: series.name });

// Works fine
const seriesList = netflixSeries.map(series => ({ title: series.name })); 
```

## 你不能命名箭头函数

在关键字`function`和参数列表之间没有名字标识符的函数被称为*匿名函数*。下面是常规匿名函数表达式的样子:

```
const anonymous = function() {
  return 'You can\'t identify me!' 
} 
```

**箭头函数都是匿名函数**:

```
const anonymousArrowFunc = () => 'You can\'t identify me!' 
```

从 ES6 开始，变量和方法可以使用匿名函数的`name`属性，从语法位置推断出匿名函数的名称。这使得在检查其值或报告错误时识别函数成为可能。

[使用`anonymousArrowFunc`查看](http://jsbin.com/menowoy/edit?html,js,console,output):

```
console.log(anonymousArrowFunc.name)
// output: "anonymousArrowFunc" 
```

请注意，这个推断的`name`属性仅在匿名函数被赋给变量时存在，如上面的例子所示。如果你使用匿名函数作为[回调](https://www.sitepoint.com/demystifying-javascript-closures-callbacks-iifes/)，你就失去了这个有用的特性。下面的中的[演示举例说明了这一点，其中`.setInterval()`方法中的匿名函数不能利用`name`属性:](http://jsbin.com/yofizis/edit?html,js,console,output)

```
let counter = 5
let countDown = setInterval(() => {
  console.log(counter)
  counter--
  if (counter === 0) {
    console.log("I have no name!!")
    clearInterval(countDown)
  }
}, 1000) 
```

这还不是全部。这个推断出的`name`属性仍然不能作为一个合适的标识符，您可以用它来从函数内部引用函数——比如递归、解除事件绑定等等。

arrow 函数的固有匿名性使得 Kyle Simpson 表达了他对这些函数的如下观点:

> 因为我不认为在你的程序中频繁使用匿名函数是个好主意，所以我不喜欢使用`=>`箭头函数形式。——*[你不知道的 JS](https://github.com/getify/You-Dont-Know-JS/blob/2nd-ed/get-started/apA.md)*

## 箭头函数如何处理`this`关键字

关于箭头函数，要记住的最重要的事情是它们处理`this`关键字的方式。特别是箭头函数内部的`this`关键字不会得到反弹。

为了说明这意味着什么，请看下面的演示:

[code Pen _ embed height = " 300 " default _ tab = " html，result " slug _ hash = " qBqgBmR " user = " site point "]参见箭头函数中的笔 [JS by site point(](https://codepen.io/SitePoint/pen/qBqgBmR)[@ site point](https://codepen.io/SitePoint))
上的 [CodePen](https://codepen.io) 。[/codepen_embed]