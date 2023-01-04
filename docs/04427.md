# 揭开 JavaScript 闭包、回调和生命的神秘面纱

> 原文：<https://www.sitepoint.com/demystifying-javascript-closures-callbacks-iifes/>

我们已经仔细研究了[可变范围](https://www.sitepoint.com/demystifying-javascript-variable-scope-hoisting/)和[提升](https://www.sitepoint.com/javascript-hoisting/)，所以今天我们将通过研究现代 JavaScript 开发中最重要和最常用的三个概念来结束我们的探索——闭包、回调和生命。

## 关闭

在 JavaScript 中，[闭包](http://en.wikipedia.org/wiki/Closure_(computer_programming))是任何保持从其父作用域*引用变量的函数，即使在父函数返回*之后。

这意味着实际上任何函数都可以被认为是闭包，因为正如我们在本教程第一部分的[变量作用域](https://www.sitepoint.com/demystifying-javascript-variable-scope-hoisting/#variable-scope)一节中所了解的，函数可以引用或访问——

*   自身函数范围内的任何变量和参数
*   外部(父)函数的任何变量和参数
*   全局范围内的任何变量。

所以，很有可能你已经在不知不觉中使用了闭包。但我们的目标不仅仅是使用它们，而是理解它们。如果我们不明白它们是如何工作的，我们就不能正确地使用它们。出于这个原因，我们将把上面的闭包定义分成三个容易理解的点。

**点 1:** *可以引用当前函数之外定义的变量。*

```
function setLocation(city) {
  var country = "France"; 

  function printLocation() {       
    console.log("You are in " + city + ", " + country);  
  }

  printLocation();
}

setLocation ("Paris");  // output: You are in Paris, France
```

[试用 JS Bin 中的示例](http://jsbin.com/sakiqepato/1/edit?js,console,output)

在这个代码示例中，`printLocation()`函数引用了`country`变量和封闭(父)`setLocation()`函数的`city`参数。而结果就是，当`setLocation()`被调用时，`printLocation()`成功利用前者的变量和参数输出“你在法国巴黎”。

**第二点:** *内部函数可以引用外部函数中定义的变量，即使后者已经返回。*

```
function setLocation(city) {
  var country = "France"; 

  function printLocation() {       
    console.log("You are in " + city + ", " + country);  
  }

  return printLocation;
}

var currentLocation = setLocation ("Paris");   

currentLocation();   // output: You are in Paris, France
```

[试用 JS Bin 中的示例](http://jsbin.com/sakiqepato/2/edit?js,console,output)

这几乎与第一个例子相同，除了这次`printLocation()` *在外部`setLocation()`函数中被返回*，而不是被立即调用。所以，`currentLocation`的值就是内部的`printLocation()`函数。

如果我们像这样警告`currentLocation`–`alert(currentLocation);`–我们将得到以下输出:

```
function printLocation () {       
  console.log("You are in " + city + ", " + country);  
}
```

正如我们所见，`printLocation()`是在其词法范围之外执行的。看起来`setLocation()`已经不在了，但是`printLocation()`仍然可以访问并“记住”它的变量(`country`)和参数(`city`)。

闭包(内部函数)能够记住它周围的作用域(外部函数),即使它是在词法作用域之外执行的。因此，您可以在程序中随时调用它。

**第三点:** *内部函数通过引用存储其外部函数的变量，而不是通过值。*

```
function cityLocation() {
  var city = "Paris";

  return {
    get: function() { console.log(city); },  
    set: function(newCity) { city = newCity; }
  };
}

var myLocation = cityLocation();

myLocation.get();           // output: Paris
myLocation.set('Sydney');
myLocation.get();           // output: Sydney
```

[试用 JS Bin 中的示例](http://jsbin.com/sakiqepato/3/edit?js,console,output)

这里`cityLocation()`返回一个包含两个闭包的对象——`get()`和`set()`——它们都引用外部变量`city`。`get()`获取`city`的当前值，而`set()`更新它。当第二次调用`myLocation.get()`时，它输出`city`–“Sydney”的更新(当前)值，而不是默认的“Paris”。

因此，闭包既可以读取也可以更新它们存储的变量，并且这些更新对任何可以访问它们的闭包都是可见的。这意味着闭包存储对外部变量的引用，而不是复制它们的值。这是需要记住的非常重要的一点，因为不知道这一点会导致一些难以发现的逻辑错误——我们将在“立即调用函数表达式(IIFEs)”一节中看到。

闭包的一个有趣的特性是闭包中的变量是自动隐藏的。闭包将数据存储在它们的封闭变量中，但不提供对它们的直接访问。改变这些变量的唯一方法是提供对它们的间接访问。例如，在最后一段代码中，我们看到我们只能通过使用`get()`和`set()`闭包来间接地修改变量`city`。

我们可以利用这种行为在对象中存储私有数据。我们可以将数据作为变量存储在构造函数中，然后使用闭包作为引用这些变量的方法，而不是将数据存储为对象的属性。

如您所见，闭包没有什么神秘或深奥的东西——只有三点需要记住。

## 回收

在 JavaScript 中，函数是一级对象。这个事实的结果之一是，函数可以作为参数传递给其他函数，也可以由其他
函数返回。

将其他函数作为参数或者返回函数作为结果的函数称为[高阶函数](http://en.wikipedia.org/wiki/Higher-order_function)，作为参数传递的函数称为[回调函数](http://en.wikipedia.org/wiki/Callback_%28computer_programming%29)。它被命名为“回调”,因为在某个时间点它被高阶函数“回调”。

回调有很多日常用法。其中之一是当我们使用浏览器的`window`对象的`setTimeout()`和`setInterval()`方法时——这些方法接受并执行回调:

```
function showMessage(message){
  setTimeout(function(){
    alert(message);
  }, 3000);  
}

showMessage('Function called 3 seconds ago');
```

[试用 JS Bin 中的示例](http://jsbin.com/sakiqepato/4/edit?js,console,output)

另一个例子是当我们将事件监听器附加到页面上的元素时。通过这样做，我们实际上提供了一个指向回调函数的指针，该函数将在事件发生时被调用。

```
// HTML

&lt;button id='btn'&gt;Click me&lt;/button&gt;

// JavaScript

function showMessage(){
  alert('Woohoo!');
}

var el = document.getElementById("btn");
el.addEventListener("click", showMessage);
```

[试用 JS Bin 中的示例](http://jsbin.com/sakiqepato/5/edit?html,js,console,output)

理解高阶函数和回调如何工作的最简单方法是创建自己的函数和回调。那么，让我们现在创建一个:

```
function fullName(firstName, lastName, callback){
  console.log("My name is " + firstName + " " + lastName);
  callback(lastName);
}

var greeting = function(ln){
  console.log('Welcome Mr. ' + ln);
};

fullName("Jackie", "Chan", greeting);
```

[试用 JS Bin 中的示例](http://jsbin.com/sakiqepato/6/edit?js,console,output)

这里我们创建一个函数`fullName()`,它有三个参数——两个用于名和姓，一个用于回调函数。然后，在`console.log()`语句之后，我们放置一个函数调用，它将触发实际的回调函数——在`fullName()`下面定义的`greeting()`函数。最后，我们调用`fullName()`，其中`greeting()`作为变量传递——*不带括号*——因为我们不希望它立即执行，而只是想指向它供`fullName()`以后使用。

我们传递的是函数定义，而不是函数调用。这阻止了回调被立即执行，这不是回调背后的想法。作为函数定义传递，它们可以在任何时间和包含函数中的任何点被执行。此外，因为回调的行为就好像它们实际上是放在函数内部一样，所以它们实际上是闭包:它们可以访问包含函数的变量和参数，甚至是全局范围内的变量。

回调可以是现有的函数，如前面的示例所示，也可以是匿名函数，这是我们在调用高阶函数时创建的，如下面的示例所示:

```
function fullName(firstName, lastName, callback){
  console.log("My name is " + firstName + " " + lastName);
  callback(lastName);
}

fullName("Jackie", "Chan", function(ln){console.log('Welcome Mr. ' + ln);});
```

[试用 JS Bin 中的示例](http://jsbin.com/sakiqepato/7/edit?js,console,output)

JavaScript 库中大量使用回调来提供通用性和可重用性。它们允许库方法被容易地定制和/或扩展。此外，代码更易于维护，并且更加简洁易读。每当您需要将不必要的重复代码模式转换成更抽象/通用的函数时，回调就来了。

假设我们需要两个函数——一个打印已发表文章的信息，另一个打印已发送消息的信息。我们创建它们，但是我们注意到我们的逻辑的某些部分在两个函数中重复。我们知道在不同的地方有一段相同的代码是不必要的，也很难维护。那么，解决办法是什么呢？让我们在下一个例子中进行说明:

```
function publish(item, author, callback){   // Generic function with common data
  console.log(item);
  var date = new Date();

  callback(author, date);
}

function messages(author, time){   // Callback function with specific data
  var sendTime = time.toLocaleTimeString();
  console.log("Sent from " + author + " at " + sendTime);
}

function articles(author, date){   // Callback function with specific data
  var pubDate = date.toDateString();
  console.log("Written by " + author);
  console.log("Published " + pubDate);
}

publish("How are you?", "Monique", messages);

publish("10 Tips for JavaScript Developers", "Jane Doe", articles);
```

[试用 JS Bin 中的示例](http://jsbin.com/sakiqepato/8/edit?js,console,output)

我们在这里所做的是将重复的代码模式(`console.log(item)`和`var date = new Date()`)放入一个单独的通用函数(`publish()`)中，只将特定的数据留在其他函数中——现在是回调函数。这样，通过同一个功能，我们可以打印各种相关的信息——消息、文章、书籍、杂志等等。您唯一需要做的就是为每种类型创建一个专门的回调函数，并将其作为参数传递给`publish()`函数。

## 立即调用函数表达式(IIFEs)

一个[立即调用的函数表达式](http://en.wikipedia.org/wiki/Immediately-invoked_function_expression)，或 IIFE(读作“iffy”)，是一个函数表达式(命名的或匿名的)，在创建后立即执行。

这种模式有两种稍微不同的语法变体:

```
// variant 1

(function () {
  alert('Woohoo!');
})();

// variant 2

(function () {
  alert('Woohoo!');
}());
```

要将常规功能变成生活，你需要执行两个步骤:

1.  你需要把整个函数用括号括起来。顾名思义，IIFE 必须是函数表达式，而不是函数定义。因此，括号的目的是将函数定义转换成表达式。这是因为，在 JavaScript 中，括号中的所有内容都被视为表达式。
2.  您需要在最后添加一对括号(变体 1)，或者在右大括号之后添加一对括号(变体 2)，这将导致函数立即执行。

还有三件事需要记住:

首先，如果你把函数赋给一个变量，你不需要把整个函数用括号括起来，因为它已经是一个表达式了:

```
var sayWoohoo = function () {
  alert('Woohoo!');
}();
```

第二，在一个生命的结尾需要一个分号，否则你的代码可能无法正常工作。

第三，您可以将参数传递给一个生命(毕竟它是一个函数)，如下例所示:

```
(function (name, profession) {
  console.log("My name is " + name + ". I'm an " + profession + ".");
})("Jackie Chan", "actor");   // output: My name is Jackie Chan. I'm an actor.
```

[试用 JS Bin 中的示例](http://jsbin.com/sakiqepato/9/edit?js,console,output)

将全局对象作为参数传递给 IIFE 是一种常见的模式，这样就可以在函数内部访问它，而不必使用`window`对象，这使得代码独立于浏览器环境。下面的代码创建了一个变量`global`，无论您在什么平台上工作，该变量都将引用全局对象:

```
(function (global) {
  // access the global object via 'global'
})(this);
</code></pre>

<p>This code will work both in the browser (where the global object is <code>window</code>), or in a Node.js environment (where we refer to the global object with the special variable <code>global</code>). </p>

<p>One of the great benefits of an IIFE is that, when using it, you don’t have to worry about polluting the global space with temporary variables. All the variables you define inside an IIFE will be local. Let’s check this out:</p>

[code language="javascript"](function(){

  var today = new Date();
  var currentTime = today.toLocaleTimeString();
  console.log(currentTime);   // output: the current local time (e.g. 7:08:52 PM)

})();

console.log(currentTime);   // output: undefined
```

[试用 JS Bin 中的示例](http://jsbin.com/sakiqepato/10/edit?js,console,output)

在这个例子中，第一个`console.log()`语句工作正常，但是第二个语句失败了，因为变量`today`和`currentTime`由于生命而成为局部变量。

我们已经知道闭包保存了对外部变量的引用，因此，它们返回最近/更新的值。那么，你认为下面这个例子的输出会是什么呢？

```
function printFruits(fruits){
  for (var i = 0; i &lt; fruits.length; i++) {
    setTimeout( function(){
      console.log( fruits[i] );
    }, i * 1000 );
  }
}

printFruits(["Lemon", "Orange", "Mango", "Banana"]);
```

[试用 JS Bin 中的示例](http://jsbin.com/sakiqepato/11/edit?js,console,output)

你可能已经预料到水果的名字会以一秒钟的间隔一个接一个地打印出来。但是，实际上，输出是“未定义”的四倍。那么，问题出在哪里？

问题是`console.log()`语句中`i`的值对于循环的每次迭代都等于 4。而且，由于我们在水果数组的索引 4 处什么也没有，所以输出是“未定义的”。(记住，在 JavaScript 中，数组的索引从 0 开始。)当`i < fruits.length`返回`false`时，循环终止。所以，在循环结束时,`i`的值是 4。该变量的最新版本用于循环产生的所有函数中。所有这些都是因为闭包与变量本身相关联，而不是与变量的值相关联。

为了解决这个问题，我们需要为循环创建的每个函数提供一个新的作用域，它将捕获`i`变量的当前状态。我们通过关闭生命中的`setTimeout()`方法，并定义一个私有变量来保存当前的`i`副本。

```
function printFruits(fruits){
  for (var i = 0; i &lt; fruits.length; i++) {
    (function(){
      var current = i;                    // define new variable that will hold the current value of "i"
      setTimeout( function(){
        console.log( fruits[current] );   // this time the value of "current" will be different for each iteration
      }, current * 1000 );
    })();
  }
}

printFruits(["Lemon", "Orange", "Mango", "Banana"]);
```

[试用 JS Bin 中的示例](http://jsbin.com/sakiqepato/12/edit?js,console,output)

我们也可以使用下面的变体，它做同样的工作:

```
function printFruits(fruits){
  for (var i = 0; i &lt; fruits.length; i++) {
    (function(current){
      setTimeout( function(){
        console.log( fruits[current] );
      }, current * 1000 );
    })( i );
  }
}

printFruits(["Lemon", "Orange", "Mango", "Banana"]);
```

[试用 JS Bin 中的示例](http://jsbin.com/sakiqepato/13/edit?js,console,output)

生命通常用于创建封装模块的范围。在该模块中，有一个独立的私有作用域，可以防止不必要的或意外的修改。这种技术被称为[模块模式](http://addyosmani.com/resources/essentialjsdesignpatterns/book/#modulepatternjavascript)，是使用闭包管理作用域的一个强有力的例子，它在许多现代 JavaScript 库中被大量使用(例如，jQuery 和下划线)。

## 结论

本教程的目的是尽可能清晰简明地呈现这些基本概念——作为一套简单的原则或规则。很好地理解它们是成为一名成功且高效的 JavaScript 开发人员的关键。

要想更详细、更深入地了解这里介绍的主题，我建议您看一下 Kyle Simpson 的《您不知道的 JS: Scope & Closures》。

## 分享这篇文章