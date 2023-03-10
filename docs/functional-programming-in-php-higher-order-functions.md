# PHP 中的函数式编程:高阶函数

> 原文：<https://www.sitepoint.com/functional-programming-in-php-higher-order-functions/>

如果你检查几个框架和大规模应用程序，你肯定会在某个时候看到一个高阶函数。许多语言都支持高阶函数的思想，包括 JavaScript、Java、.NET、Python 甚至 PHP 等等。

但是什么是高阶函数，我们为什么要使用它呢？它给我们带来了什么好处，我们可以用它来简化我们的代码吗？在本文中，我们将专门讨论 PHP 中的高阶函数，但是将展示它们在其他语言中是如何使用的，以便进行比较。

## 一流的功能

在我们进入什么是高阶函数之前，我们必须首先明白，我们必须有一种语言能够支持一种叫做[一级函数](https://en.wikipedia.org/wiki/First-class_function)(又名一阶函数)的特性。这意味着语言将函数视为一等公民。换句话说，这种语言像对待变量一样对待函数。你可以将一个函数存储在一个变量中，将它作为一个变量传递给其他函数，像变量一样从函数中返回它们，甚至像你对变量一样将它们存储在数据结构中，如数组或对象属性中。目前大多数现代语言默认都有这个特性。你真正需要知道的是函数可以像变量一样被传递和使用。

出于我们的目的，我们将主要关注将函数作为参数传递和将函数作为结果返回，并简要介绍非局部变量和闭包的概念。(你可以在前段链接的维基百科文章的第 [1.1](https://en.wikipedia.org/wiki/First-class_function#Higher-order_functions:_passing_functions_as_arguments) 、 [1.4](https://en.wikipedia.org/wiki/First-class_function#Higher-order_functions:_returning_functions_as_results) 和 [1.3](https://en.wikipedia.org/wiki/First-class_function#Non-local_variables_and_closures) 节中阅读更多关于这些概念的内容。)

## 什么是高阶函数？

识别高阶函数有两个主要特征。高阶函数可以只实现以下一种或两种思想:将一个或多个函数作为输入或返回一个函数作为输出的函数。在 PHP 中，有一个关键字可以清楚地表明函数是高阶的:关键字`callable`。虽然这个关键字不一定要出现，但是这个关键字很容易识别它们。如果你看到一个函数或方法有一个可调用的参数，这意味着它接受一个函数作为输入。另一个容易的迹象是，如果您看到一个函数使用它的`return`语句返回一个函数。return 语句可能只是函数名，甚至可能是匿名/内嵌函数。下面是每种类型的一些例子。

```
// Simple user-defined function we'll pass to our higher-order function
function echoHelloWorld() {
  echo "Hello World!";
}

// Higher-order function that takes a function as an input and calls it
function higherOrderFunction(callable $func) {
  $func();
}

// Call our higher-order function and give it our echoHelloWorld() function. 
// Notice we pass just the name as a string and no parenthesis.
// This echos "Hello World!"
higherOrderFunction('echoHelloWorld'); 
```

以下是一些返回函数的高阶函数的简单示例:

```
// Returns an existing function in PHP called trim(). Notice it's a simple string with no parentheses.
function trimMessage1() {
  return 'trim';
}

// Here's an example using an anonymous inline function
function trimMessage2() {
    return function($text) {
        return trim($text);
    };
}

// Returns the 'trim' function
$trim1 = trimMessage1(); 

// Call it with our string to trim
echo $trim1('  hello world  ');

// Returns the anonymous function that internally calls 'trim'
$trim2 = trimMessage1(); 

// Call it again with our string to trim
echo $trim2('  hello world  '); 
```

你可以想象，你可以接受一个函数，然后用它生成另一个返回的函数。很巧妙的把戏，对吧？但是你为什么要做这些呢？这听起来像是只会让事情变得更复杂的事情。

## 为什么要使用或创建高阶函数？

您可能希望在代码中创建高阶函数有几个原因。从代码灵活性、代码重用、代码扩展到模仿你在另一个程序中看到的代码解决方案。虽然原因很多，但我们在这里只涉及其中的几个。

### 增加代码灵活性

高阶函数增加了极大的灵活性。根据前面的例子，您可能会看到类似这样的一些用法。您可以编写一个函数，它接受一整套不同的函数并使用它们，而不必编写代码来单独执行它们。

可能高阶函数本身也不知道会收到什么类型的函数。谁说函数必须知道它接受的函数的任何信息？前一分钟输入函数可能是一个`add()`函数，下一分钟它可能是一个`divide()`函数。在这两种情况下，它只是工作。

### 轻松扩展您的代码

该功能还使得以后添加额外的输入功能变得更加容易。假设您有`add()`和`divide()`，但是接下来您需要添加一个`sum()`函数。您可以编写`sum()`函数，并通过管道将它传递给更高阶的函数，而不必修改它。在后面的例子中，我们将演示如何使用这个功能来创建我们自己的`calc()`函数。

### 模仿另一种语言的特征

您可能希望在 PHP 中使用高阶函数的另一个原因是模拟 Python 中类似装饰器的行为。您将一个函数“包装”在另一个函数中，以修改该函数的行为。例如，你可以写一个函数来计时其他函数。您编写一个高阶函数，它接收一个函数，启动一个计时器，调用该函数，然后结束计时器，以查看已经过了多长时间。

## PHP 中现有高阶函数的例子

在 PHP 中找高阶函数的例子真的很简单。查看 [PHP 文档](https://www.php.net/docs.php)，找到一个带`callable`输入参数的函数/方法，你已经找到了。下面是一些常用的高阶函数的例子。你甚至可能在不知道它们是高阶函数的情况下使用它们。

### 高阶动态二重奏:`array_map`和`array_filter`

```
$arrayOfNums = [1,2,3,4,5];

// array_map: example of taking an inline anonymous function
$doubledNums = array_map(function($num) {
  return $num * 2;
}, $arrayOfNums);

var_dump($doubledNums); // Prints out array containing [2, 4, 6, 8, 10]; 
```

让我们看看如何使用另一个函数，这次是使用我们单独定义的过滤函数:

```
$arrayOfNums = [1,2,3,4,5];

// Example of creating a function and giving it to a higher-order function array_filter()
function isEven($num) {
  return ($num % 2) === 0;
}

// array_filter is a higher-order function
$evenNums = array_filter($arrayOfNums, 'isEven');

var_dump($evenNums); // Prints out array containing [2, 4] 
```

`array_filter`和`array_map`是两个非常受欢迎的高阶函数，你可以在很多代码项目中找到。另一个你可能使用的是`call_user_func`，它接受一个函数和一个参数列表以及对该函数的调用。这本质上是一个定制的高阶函数。它的全部目的是调用用户定义的其他函数:

```
function printCustomMessage($message) {
  echo "$message";
}

call_user_func('printCustomMessage', 'Called custom message through the use of call_user_func!'); 
```

## 如何创建自己的高阶函数

我们已经展示了很多高阶函数如何在 PHP 中工作的例子，并且我们已经创建了一些自定义函数来演示它们的各种用途。但是让我们用一个叫做`calc()`的新函数来展示一下灵活性。这个函数将接受两个参数和一个对这两个参数进行操作的函数，以给出一个结果:

```
function add($a, $b) {
  return $a + $b;
}

function subtract($a, $b) {
  return $a - $b;
}

function multiply($a, $b) {
  return $a * $b;
}

// Higher-order function that passes along $n1 and $n2
function calc($n1, $n2, $math_func) {
  return $math_func($n1, $n2);
}

$addedNums = calc(1, 5, 'add');
$subtractedNums = calc(1, 5, 'subtract');
$multipliedNums = calc(1, 5, 'multiply');

// Prints 6
echo "Added numbers: $addedNums\n";

// Prints -4
echo "Subtracted numbers: $subtractedNums\n";

// Prints 5
echo "Multiplied numbers: $multipliedNums\n"; 
```

注意，我们的`calc()`函数被写得非常一般化，接受一组其他函数，并用参数`$n1`和`$n2`调用它们。在这种情况下，我们的计算函数不关心它接受的函数是做什么的。它只是将参数传递给函数并返回结果。

但是等一下:我们的老板刚刚要求我们在现有的系统中添加一个将两个字符串相加的功能。但是串联字符串不是典型的数学运算。然而，在我们的设置中，我们只需要添加字符串连接，并通过`calc()`:

```
function addTwoStrings($a, $b) {
  return $a . " " . $b;
}

// Prints "Hello World!"
$concatenatedStrings = calc('Hello', 'World!', 'addTwoStrings'); 
```

虽然这个例子很不自然，但它展示了如何在更大的项目中使用这个概念。也许高阶函数决定了事件的处理方式。也许，根据触发的事件，您可以通过传入的处理函数来路由它。可能性是无限的。

这与其他具有高阶函数的语言相比如何？让我们以 Python 中的一个简单示例 decorator 为例，将其与 JavaScript 进行比较，然后与 PHP 进行比较。这样，如果你懂 Python 或者 JS，你就能看出它是如何等价的。

### Python 和 JavaScript 的并行比较

```
def say_message(func):
    def wrapper():
        print('Print before function is called')
        func()
        print('Print after function is called')
    return wrapper

def say_hello_world():
    print("Hello World!")

# Pass our hello world function to the say_message function. 
# It returns a function that we can then call
# Note: This line could have been replaced with the @say_message (pie syntax) on the say_hello_world method
say_hello_world = say_message(say_hello_world)

# Call the created function
say_hello_world()

# Output...
# Print before function is called
# Hello World!
# Print after function is called 
```

现在让我们看看如何在 JavaScript 高阶函数中实现这一点:

```
// Takes a function, returns a function.
function say_message(func) {
  return function() {
      console.log("Print before function is called");
      func();
      console.log("Print after function is called");
  }
}

function say_hello_world() {
  console.log("Hello World!");
}

// Again pass our function to say_message as an input parameter
// say_message returns a function that is wrapped around the say_hello_world function
say_hello = say_message(say_hello_world);

// Call the function
say_hello();

// Output...
// Print before function is called
// Hello World!
// Print after function is called 
```

最后，让我们将它与 PHP 进行比较:

```
// Takes a function, returns a function.
function say_message($func) {
  // We use 'use' so that our anonymous function can see $func coming in
  return function() use ($func) {
      echo "Print before function is called";
      $func();
      echo "Print after function is called";
  };
}

function say_hello_world() {
  echo "Hello World!";
}

// Again pass our function to say_message as an input parameter
// say_message returns a function that is wrapped around the say_hello_world function
$say_hello = say_message('say_hello_world');

// Call the function
$say_hello();

// Output...
// Print before function is called
// Hello World!
// Print after function is called 
```

从并列比较中可以看出，PHP 版本与 JavaScript 语法非常相似。但是如果你对 Python 和 decorator 有所了解，你可以看到 decorator 是如何被翻译成另外两种编程语言的。

## 简单介绍一下 Lambdas/匿名函数

很多时候，当您使用高阶函数时，您可能会看到内联匿名函数(也称为 lambdas)的使用。在我们上面看到的一些例子中，我使用了这种格式。除此之外，我还使用了更显式的版本，首先定义函数，然后将它传递给我们的高阶函数。这是为了让你习惯于看到两个版本。通常，你会在大量使用高阶函数的框架中看到内联 lambda 版本。不要被这种形式所迷惑。他们只是创建了一个简单的函数，没有名字，用它来代替你给独立函数命名的地方。

## 结论

在本文中，我们讨论了使函数成为高阶函数的基本定义。任何接受函数或返回函数(或两者兼有)的函数都可以被认为是高阶函数。我们还讨论了为什么你可能想要创建这些类型的函数，以及它们的优点是什么。

然后，我们展示了 PHP 中现有的一些高阶函数的例子，如`array_map`和`array_filter`，然后继续创建我们自己的高阶函数`calc()`，它接受几个输入函数并对几个参数进行操作。然后，我们进行了并排比较，展示了 PHP 的解决方案与 Python 装饰器和 JavaScript 实现的比较。
我希望你学到了更多关于高阶函数的知识，以及为什么你可能想在下一个大规模解决方案中考虑它们。它们可以提供许多优势，并减少可能是多余的锅炉板代码。

如果你想学习更多关于 PHP 函数式编程的知识，你可以[查阅我们的教程](https://www.sitepoint.com/functional-programming-and-php/)。

感谢阅读！

## 分享这篇文章