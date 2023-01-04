# 实践中的 ES6:增强的对象文字

> 原文：<https://www.sitepoint.com/es6-enhanced-object-literals/>

这篇文章着眼于 JavaScript 中对象文字的可能性，特别是根据最近的 ECMAScript 更新。

使用文字符号创建 JavaScript 对象的能力非常强大。ES2015 (ES6)引入的新功能使所有现代浏览器(非 IE)和 Node.js 中的对象处理变得更加简单。

在某些语言中创建对象在开发时间和处理能力方面是很昂贵的，因为在完成任何事情之前必须声明一个`class`。在 JavaScript 中，动态创建对象很容易。例如:

```
// ES5-compatible code
var myObject = {
  prop1: 'hello',
  prop2: 'world',
  output: function() {
    console.log(this.prop1 + ' ' + this.prop2);
  }
};

myObject.output(); // hello world 
```

一次性物品被广泛使用。示例包括配置设置、模块定义、方法参数、函数返回值等。ES2015 (ES6)增加了一系列功能来增强对象文字。

## 从变量初始化对象

对象的属性通常是由同名的变量创建的。例如:

```
// ES5 code
var
  a = 1, b = 2, c = 3;
  obj = {
    a: a,
    b: b,
    c: c
  };

// obj.a = 1, obj.b = 2, obj.c = 3 
```

ES6 里不需要讨厌的重复！…

```
// ES6 code
const
  a = 1, b = 2, c = 3;
  obj = {
    a
    b
    c
  };

// obj.a = 1, obj.b = 2, obj.c = 3 
```

当使用[显示模块模式](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#revealingmodulepatternjavascript)时，这对于返回的对象可能是有用的，该模式(有效地)对名称空间进行编码，以避免命名冲突。例如:

```
// ES6 code
const lib = (() => {

  function sum(a, b)  { return a + b; }
  function mult(a, b) { return a * b; }

  return {
    sum,
    mult
  };

}());

console.log( lib.sum(2, 3) );  // 5
console.log( lib.mult(2, 3) ); // 6 
```

您可能已经在 ES6 模块中见过它:

```
// lib.js
function sum(a, b)  { return a + b; }
function mult(a, b) { return a * b; }

export { sum, mult }; 
```

## 对象方法定义简写

ES5 中的对象方法需要`function`语句。例如:

```
// ES5 code
var lib = {
  sum:  function(a, b) { return a + b; },
  mult: function(a, b) { return a * b; }
};

console.log( lib.sum(2, 3) );  // 5
console.log( lib.mult(2, 3) ); // 6 
```

这在 ES6 中已经没有必要了；它允许以下速记语法:

```
// ES6 code
const lib = {
  sum(a, b)  { return a + b; },
  mult(a, b) { return a * b; }
};

console.log( lib.sum(2, 3) );  // 5
console.log( lib.mult(2, 3) ); // 6 
```

这里不可能使用 [ES6 胖箭头`=>`函数语法](https://www.sitepoint.com/javascript-arrow-functions/)，因为方法需要一个名字。也就是说，如果直接命名每个方法(比如 ES5)，可以使用箭头函数。例如:

```
// ES6 code
const lib = {
  sum:  (a, b) => a + b,
  mult: (a, b) => a * b
};

console.log( lib.sum(2, 3) );  // 5
console.log( lib.mult(2, 3) ); // 6 
```

## 动态属性键

在 ES5 中，不可能使用变量作为键名，尽管它可以被添加到对象被创建后的*之后。例如:*

```
// ES5 code
var
  key1 = 'one',
  obj = {
    two: 2,
    three: 3
  };

obj[key1] = 1;

// obj.one = 1, obj.two = 2, obj.three = 3 
```

在 ES6 中，可以通过在方括号`[`中放置一个表达式来动态分配对象键。例如:

```
// ES6 code
const
  key1 = 'one',
  obj = {
    [key1]: 1,
    two: 2,
    three: 3
  };

// obj.one = 1, obj.two = 2, obj.three = 3 
```

任何表达式都可以用来创建密钥。例如:

```
// ES6 code
const
  i = 1,
  obj = {
    ['i' + i]: i
  };

console.log(obj.i1); // 1 
```

动态键可以用于方法和属性。例如:

```
// ES6 code
const
  i = 2,
  obj = {
    ['mult' + i]: x => x * i
  };

console.log( obj.mult2(5) ); // 10 
```

你*是否应该*创建动态属性和方法是另一回事。代码可能难以阅读，最好创建对象工厂或类。

## 析构(对象属性中的变量)

通常需要将一个对象的属性值提取到另一个变量中。这必须在 ES5 中明确声明。例如:

```
// ES5 code
var myObject = {
  one:   'a',
  two:   'b',
  three: 'c'
};

var
  one   = myObject.one, // 'a'
  two   = myObject.two, // 'b'
  three = myObject.three; // 'c' 
```

ES6 支持析构:你可以创建一个与等价对象属性同名的变量。例如:

```
// ES6 code
const myObject = {
  one:   'a',
  two:   'b',
  three: 'c'
};

const { one, two, three } = myObject;
// one = 'a', two = 'b', three = 'c' 
```

也可以使用符号`{ propertyName: newVariable }`将属性分配给任何名称的变量。例如:

```
// ES6 code
const myObject = {
  one:   'a',
  two:   'b',
  three: 'c'
};

const { one: first, two: second, three: third } = myObject;
// first = 'a', second = 'b', third = 'c' 
```

具有嵌套数组和子对象的更复杂的对象也可以在析构赋值中引用。例如:

```
// ES6 code
const meta = {
  title: 'Enhanced Object Literals',
  pageinfo: {
    url: 'https://www.sitepoint.com/',
    description: 'How to use object literals in ES2015 (ES6).',
    keywords: 'javascript, object, literal'
  }
};

const {
  title   : doc,
  pageinfo: { keywords: topic }
} = meta;

/*
  doc   = 'Enhanced Object Literals'
  topic = 'javascript, object, literal'
*/ 
```

这最初看起来很复杂，但是记住在所有的析构赋值中:

*   赋值的左边是**析构源**——保存被提取数据的数组或对象
*   赋值的右边是**析构目标**——定义被赋值变量的模式。

有一些警告。你不能用花括号开始一个语句，因为它看起来像一个代码块。例如:

```
{ a, b, c } = myObject; // FAILS 
```

您必须声明变量，例如:

```
const { a, b, c } = myObject; // WORKS 
```

或者，如果已经声明了变量，请使用括号，例如:

```
let a, b, c;
({ a, b, c } = myObject); // WORKS 
```

因此，您应该小心不要混淆已声明和未声明的变量。

在许多情况下，对象析构是有用的。

### 默认函数参数

向函数传递单个对象通常比使用一长串参数更容易。例如:

```
prettyPrint( {
  title: 'Enhanced Object Literals',
  publisher: {
    name: 'SitePoint',
    url: 'https://www.sitepoint.com/'
  }
} ); 
```

在 ES5 中，有必要解析对象以确保设置了适当的默认值。例如:

```
// ES5 assign defaults
function prettyPrint(param) {

  param = param || {};
  var
    pubTitle = param.title || 'No title',
    pubName = (param.publisher && param.publisher.name) || 'No publisher';

  return pubTitle + ', ' + pubName;

} 
```

在 ES6 中，我们可以为任何参数分配默认值。例如:

```
// ES6 default value
function prettyPrint(param = {}) { ... } 
```

然后，我们可以使用析构来提取值，并在必要时分配默认值:

```
// ES6 destructured default value
function prettyPrint(
  {
    title: pubTitle = 'No title',
    publisher: { name: pubName = 'No publisher' }
  } = {}
) {

  return `${pubTitle}, ${pubName}`;

} 
```

你是否觉得这段代码更容易阅读是另一回事！

### 解析返回的对象

函数只能返回一个值，但这可能是一个具有数百个属性和/或方法的对象。在 ES5 中，需要获得返回的对象，然后相应地提取值。例如:

```
// ES5 code
var
  obj = getObject(),
  one = obj.one,
  two = obj.two,
  three = obj.three; 
```

ES6 析构使这个过程更简单，并且不需要将对象作为变量保留:

```
// ES6 code
const { one, two, three } = getObject(); 
```

您可能在 Node.js 代码中看到过类似的赋值。例如，如果你只需要文件系统(`fs`)方法`readFile`和`writeFile`，你可以直接引用它们。例如:

```
// ES6 Node.js
const { readFile, writeFile } = require('fs');

readFile('file.txt', (err, data) => {
  console.log(err || data);
});

writeFile('new.txt', 'new content', err => {
  console.log(err || 'file written');
}); 
```

## ES2018 (ES9)静止/展开属性

在 ES2015 中，rest 参数和扩展运算符三点(`...`)符号仅适用于数组。ES2018 为对象启用了类似的静止/扩散功能。一个基本的例子:

```
const myObject = {
  a: 1,
  b: 2,
  c: 3
};

const { a, ...x } = myObject;
// a = 1
// x = { b: 2, c: 3 } 
```

您可以使用该技术将值传递给函数:

```
restParam({
  a: 1,
  b: 2,
  c: 3
});

function restParam({ a, ...x }) {
  // a = 1
  // x = { b: 2, c: 3 }
} 
```

在声明的末尾只能使用一个 rest 属性。此外，它只在每个对象的顶层起作用，而不是子对象。

扩展运算符可以在其他对象中使用。例如:

```
const
  obj1 = { a: 1, b: 2, c: 3 },
  obj2 = { ...obj1, z: 26 };

// obj2 is { a: 1, b: 2, c: 3, z: 26 } 
```

您可以使用 spread 操作符来克隆对象(`obj2 = { ...obj1 };`)，但是请注意，您只能获得浅层副本。如果一个属性包含另一个对象，则克隆将引用同一个对象。

ES2018 (ES9) rest/spread 属性支持不完整，但在 Chrome、Firefox 和 Node.js 8.6+中可用。

对象文字总是有用的。ES2015 引入的新功能并没有从根本上改变 JavaScript 的工作方式，但它们节省了键入的工作量，并使代码更清晰、更简洁。

## 分享这篇文章