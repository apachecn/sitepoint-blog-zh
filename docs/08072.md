# JavaScript 闭包去神秘化

> 原文：<https://www.sitepoint.com/javascript-closures-demystified/>

闭包是 JavaScript 语言的一个有点高级但经常被误解的特性。简单地说，[闭包](https://developer.mozilla.org/en/JavaScript/Guide/Closures "Closures - MDN")是包含一个函数和对创建该函数的环境的引用的对象。然而，为了完全理解闭包，JavaScript 语言还有另外两个特性必须首先理解― *[一级函数](http://en.wikipedia.org/wiki/First-class_function "First-class function - Wikipedia")* 和 *[内部函数](http://en.wikipedia.org/wiki/Nested_function "Nested function - Wikipedia")* 。

## 一流的功能

在编程语言中，如果函数可以像其他数据类型一样被操作，那么它们就被认为是一等公民。例如，一级函数可以在运行时构造并赋给变量。它们也可以传递给其他函数，并由其他函数返回。除了满足前面提到的标准，JavaScript 函数还有自己的属性和方法。以下示例显示了一级函数的一些功能。在示例中，创建了两个函数，并将其分配给变量“foo”和“bar”。存储在“foo”中的函数显示一个对话框，而“bar”只是返回传递给它的任何参数。示例的最后一行做了几件事。首先，存储在“bar”中的函数以“foo”作为参数被调用。“bar”然后返回“foo”函数引用。最后，调用返回的“foo”引用，导致“Hello World！”要展示的。

```
var foo = function() {
  alert("Hello World!");
};

var bar = function(arg) {
  return arg;
};

bar(foo)();
```

## 内部函数

内部函数也称为嵌套函数，是在另一个函数(称为外部函数)内部定义的函数。每次调用外部函数时，都会创建内部函数的一个实例。以下示例显示了如何使用内部函数。在这种情况下，add()是外层函数。在 add()内部，定义并调用 doAdd()内部函数。

```
function add(value1, value2) {
  function doAdd(operand1, operand2) {
    return operand1 + operand2;
  }

  return doAdd(value1, value2);
}

var foo = add(1, 2);
// foo equals 3
```

内部函数的一个重要特征是它们可以隐式访问外部函数的范围。这意味着内部函数可以使用变量、参数等。外部函数的。在前面的示例中，add()的“value1”和“value2”参数作为“operand1”和“operand2”参数传递给 doAdd()。但是，这是不必要的，因为 doAdd()可以直接访问“value1”和“value2”。下面重写了前面的示例，以展示 doAdd()如何使用“value1”和“value2”。

```
function add(value1, value2) {
  function doAdd() {
    return value1 + value2;
  }

  return doAdd();
}

var foo = add(1, 2);
// foo equals 3
```

## 创建闭包

当内部函数可以从创建它的函数外部访问时，闭包就创建了。这通常发生在外部函数返回内部函数时。发生这种情况时，内部函数会维护一个对创建它的环境的引用。这意味着它记住了当时范围内的所有变量(及其值)。以下示例显示了如何创建和使用闭包。

```
function add(value1) {
  return function doAdd(value2) {
    return value1 + value2;
  };
}

var increment = add(1);
var foo = increment(2);
// foo equals 3
```

这个例子有很多值得注意的地方。

*   add()函数返回其内部函数 doAdd()。通过返回对内部函数的引用，创建了一个闭包。
*   “value1”是 add()的一个局部变量，是 doAdd()的一个 *[非局部变量](http://en.wikipedia.org/wiki/Non-local_variable "Non-local variable - Wikipedia")* 。非局部变量是指既不在局部范围内也不在全局范围内的变量。“value2”是 doAdd()的局部变量。
*   当调用 add(1)时，会创建一个闭包并存储在“increment”中。在闭包的引用环境中，“值 1”被*绑定到值 1。被绑定的变量也被称为是*上的*封闭变量。这就是闭包这个名字的由来。*
*   当增量(2)被调用时，闭包*进入*。这意味着调用 doAdd()，用“value1”变量保存值 1。闭包本质上可以被认为是创建了下面的函数。

```
function increment(value2) {
  return 1 + value2;
}
```

## 何时使用闭包

闭包可以用来完成很多事情。它们对于像用参数配置回调函数这样的事情非常有用。本节涵盖了两个场景，在这两个场景中，闭包可以使开发人员的生活更加简单。

### 使用计时器

闭包在与 [setTimeout()](https://developer.mozilla.org/en/DOM/window.setTimeout "window.setTimeout - MDN") 和 [setInterval()](https://developer.mozilla.org/en/DOM/window.setInterval "window.setInterval - Wikipedia") 函数结合使用时非常有用。更具体地说，闭包允许将参数传递给 setTimeout()和 setInterval()的回调函数。例如，下面的代码通过调用 showMessage()每秒打印一次字符串“some message”。

```
<!DOCTYPE html>
<html lang="en">
<head>
  <title>Closures</title>
  <meta charset="UTF-8" />
  <script>
    window.addEventListener("load", function() {
      window.setInterval(showMessage, 1000, "some message<br />");
    });

    function showMessage(message) {
      document.getElementById("message").innerHTML += message;
    }
  </script>
</head>
<body>
  <span id="message"></span>
</body>
</html>
```

不幸的是，Internet Explorer 不支持通过 setInterval()传递回调参数。Internet Explorer 不显示“某个消息”，而是显示“未定义”(因为实际上没有值传递给 showMessage())。要解决这个问题，可以创建一个闭包，将“message”参数绑定到所需的值。然后闭包可以用作 setInterval()的回调函数。为了说明这个概念，前面例子中的 JavaScript 代码被重写为使用闭包。

```
window.addEventListener("load", function() {
  var showMessage = getClosure("some message<br />");

  window.setInterval(showMessage, 1000);
});

function getClosure(message) {
  function showMessage() {
    document.getElementById("message").innerHTML += message;
  }

  return showMessage;
}
```

### 模拟私有数据

许多面向对象的语言支持私有成员数据的概念。但是，JavaScript 不是一种纯面向对象的语言，不支持私有数据。但是，使用闭包来模拟私有数据是可能的。回想一下，一个闭包包含了对最初创建它的环境的引用――这已经超出了范围。因为引用环境中的变量只能从闭包函数中访问，所以它们本质上是私有数据。

下面的示例显示了一个简单 Person 类的构造函数。当每个人被创建时，它通过“name”参数被赋予一个名称。在内部，Person 将其姓名存储在“_name”变量中。遵循良好的面向对象编程实践，还提供了 getName()方法来检索名称。

```
function Person(name) {
  this._name = name;

  this.getName = function() {
    return this._name;
  };
}
```

Person 类还有一个主要问题。因为 JavaScript 不支持私有数据，所以没有什么可以阻止其他人来更改名称。例如，下面的代码创建一个名为 Colin 的人，然后将其姓名更改为 Tom。

```
var person = new Person("Colin");

person._name = "Tom";
// person.getName() now returns "Tom"
```

就我个人而言，我不喜欢任何人来合法地更改我的名字。为了防止这种情况发生，可以使用闭包使“_name”变量成为私有变量。下面使用闭包重写了 Person 构造函数。注意“_name”现在是 Person 构造函数的一个局部变量，而不是一个对象属性。形成闭包是因为外部函数 Person()通过创建公共 getName()方法公开了内部函数。

```
function Person(name) {
  var _name = name;

  this.getName = function() {
    return _name;
  };
}
```

现在，当调用 getName()时，保证会返回最初传递给构造函数的值。仍然有可能有人向对象添加一个新的“_name”属性，但是对象的内部工作将不会受到影响，只要它们引用闭包所绑定的变量。下面的代码显示“_name”变量确实是私有的。

```
var person = new Person("Colin");

person._name = "Tom";
// person._name is "Tom" but person.getName() returns "Colin"
```

## 何时不使用闭包

理解闭包如何工作以及何时使用它们是很重要的。同样重要的是，要理解它们何时不适合手头的工作。过度使用闭包会导致脚本执行缓慢并消耗不必要的内存。因为闭包创建起来非常简单，所以有可能在不知道的情况下误用它们。本节涵盖了几个应该谨慎使用闭包的场景。

### 在循环中

在循环中创建闭包可能会产生误导性的结果。这方面的一个例子如下所示。在本例中，创建了三个按钮。当“按钮 1”被点击时，应该显示一个提示“点击按钮 1”。“按钮 2”和“按钮 3”应显示类似的消息。然而，当运行这段代码时，所有的按钮都显示“Clicked button 4”。这是因为，当单击其中一个按钮时，循环已经执行完毕，并且循环变量已经达到其最终值 4。

```
<!DOCTYPE html>
<html lang="en">
<head>
  <title>Closures</title>
  <meta charset="UTF-8" />
  <script>
    window.addEventListener("load", function() {
      for (var i = 1; i < 4; i++) {
        var button = document.getElementById("button" + i);

        button.addEventListener("click", function() {
          alert("Clicked button " + i);
        });
      }
    });
  </script>
</head>
<body>
  <input type="button" id="button1" value="One" />
  <input type="button" id="button2" value="Two" />
  <input type="button" id="button3" value="Three" />
</body>
</html>
```

为了解决这个问题，闭包必须与实际的循环变量解耦。这可以通过调用一个新函数来实现，这反过来会创建一个新的引用环境。下面的例子说明了这是如何做到的。循环变量被传递给 getHandler()函数。getHandler()然后返回一个独立于原始“for”循环的闭包。

```
function getHandler(i) {
  return function handler() {
    alert("Clicked button " + i);
  };
}

window.addEventListener("load", function() {
  for (var i = 1; i < 4; i++) {
    var button = document.getElementById("button" + i);

    button.addEventListener("click", getHandler(i));
  }
});
```

### 构造函数中不必要的使用

构造函数是闭包误用的另一个常见来源。我们已经看到了如何使用闭包来模拟私有数据。然而，如果方法实际上并不访问私有数据，那么将方法实现为闭包就太过分了。下面的示例重新访问了 Person 类，但是这次添加了一个 sayHello()方法，该方法不使用私有数据。

```
function Person(name) {
  var _name = name;

  this.getName = function() {
    return _name;
  };

  this.sayHello = function() {
    alert("Hello!");
  };
}
```

每次实例化一个人时，都要花费时间来创建 sayHello()方法。如果创建了许多个人对象，这就成了浪费时间。更好的方法是将 sayHello()添加到 Person 原型中。通过添加到原型中，所有的人对象可以共享相同的方法。这节省了构造函数的时间，因为不必为每个实例创建闭包。下面重写了前面的例子，将无关的闭包移到了原型中。

```
function Person(name) {
  var _name = name;

  this.getName = function() {
    return _name;
  };
}

Person.prototype.sayHello = function() {
  alert("Hello!");
};
```

## 要记住的事情

*   闭包包含一个函数和对创建该函数的环境的引用。
*   当外部函数公开内部函数时，就形成了闭包。
*   闭包可以用来轻松地将参数传递给回调函数。
*   私有数据可以通过使用闭包来模拟。这在面向对象编程和名称空间设计中很常见。
*   构造函数中不应该过度使用闭包。增加原型是一个更好的主意。

## 分享这篇文章