# 25+ JavaScript 速记编码技术

> 原文：<https://www.sitepoint.com/shorthand-javascript-techniques/>

这真的是任何 JavaScript 开发者的必读之作。我们写了这篇指南，介绍我们多年来学到的速记 JavaScript 编码技术。为了帮助你理解正在发生的事情，我们包括了手写版本，以给出一些编码观点。

如果你想了解更多关于 ES6 的知识，请查看 [JavaScript:忍者新手，第二版](https://www.sitepoint.com/premium/books/javascript-novice-to-ninja-2nd-edition?utm_source=blog&utm_medium=articles)。

## 1.三元运算符

当您想用一行代码编写一个`if..else`语句时，这是一个很好的代码节省器。

手写:

```
const x = 20;
let answer;

if (x > 10) {
    answer = "greater than 10";
} else {
    answer =  "less than 10";
} 
```

速记:

```
const answer = x > 10 ? "greater than 10" : "less than 10"; 
```

您也可以像这样嵌套您的`if`语句:

```
const answer = x > 10 ? "greater than 10" : x < 5 ? "less than 5" : "between 5 and 10"; 
```

## 2.短路评估速记

当将一个变量值赋给另一个变量时，您可能希望确保源变量不为 null、未定义或空。你可以用多个条件写一个长的`if`语句，或者使用一个短路评估。

手写:

```
if (variable1 !== null || variable1 !== undefined || variable1 !== '') {
     let variable2 = variable1;
} 
```

速记:

```
const variable2 = variable1  || 'new'; 
```

不相信我？自己测试(将以下代码粘贴到【Test 控制台):

```
let variable1;
let variable2 = variable1  || 'bar';
console.log(variable2 === 'bar'); // prints true

variable1 = 'foo';
variable2 = variable1  || 'bar';
console.log(variable2); // prints foo 
```

请注意，如果将`variable1`设置为`false`或`0`，将会分配值`bar`。

## 3.声明变量速记

在函数的开始声明变量赋值是一个好习惯。当同时声明多个变量时，这种简化的方法可以节省大量的时间和空间。

手写:

```
let x;
let y;
let z = 3; 
```

速记:

```
let x, y, z=3; 
```

## 4.如果在场速记

这可能是微不足道的，但值得一提。进行“`if`检查”时，赋值运算符有时可以省略。

手写:

```
if (likeJavaScript === true) 
```

速记:

```
if (likeJavaScript) 
```

> **注意:**这两个例子并不完全相等，因为只要`likeJavaScript`是一个[真值](https://developer.mozilla.org/en-US/docs/Glossary/Truthy)，速记检查就会通过。

这是另一个例子。如果`a`不等于真，那就做点什么。

手写:

```
let a;
if ( a !== true ) {
// do something...
} 
```

速记:

```
let a;
if ( !a ) {
// do something...
} 
```

## 5.用于循环速记的 JavaScript

如果您想要普通的 JavaScript，并且不想依赖 jQuery 或 lodash 等外部库，这个小技巧真的很有用。

手写:

```
const fruits = ['mango', 'peach', 'banana'];
for (let i = 0; i < fruits.length; i++) 
```

速记:

```
for (let fruit of fruits) 
```

如果您只想访问索引，请执行以下操作:

```
for (let index in fruits) 
```

如果您想访问文字对象中的键，这也是可行的:

```
const obj = {continent: 'Africa', country: 'Kenya', city: 'Nairobi'}
for (let key in obj)
  console.log(key) // output: continent, country, city 
```

Array.forEach 的简写:

```
function logArrayElements(element, index, array) {
  console.log("a[" + index + "] = " + element);
}
[2, 5, 9].forEach(logArrayElements);
// a[0] = 2
// a[1] = 5
// a[2] = 9 
```

## 6.短路评估

如果预期的参数为空或未定义，我们可以简单地使用一个短路逻辑操作符，只用一行代码就可以完成同样的事情，而不是编写六行代码来指定默认值。

手写:

```
let dbHost;
if (process.env.DB_HOST) {
  dbHost = process.env.DB_HOST;
} else {
  dbHost = 'localhost';
} 
```

速记:

```
const dbHost = process.env.DB_HOST || 'localhost'; 
```

## 7.十进制基数指数

你可能在附近见过这个。这本质上是一种不用尾随零来书写数字的奇特方式。例如，1e7 本质上意味着 1 后面跟着 7 个零。它表示十进制基数(JavaScript 将其解释为浮点类型)等于 10，000，000。

手写:

```
for (let i = 0; i < 10000; i++) {} 
```

速记:

```
for (let i = 0; i < 1e7; i++) {}

// All the below will evaluate to true
1e0 === 1;
1e1 === 10;
1e2 === 100;
1e3 === 1000;
1e4 === 10000;
1e5 === 100000; 
```

## 8.对象属性速记

在 JavaScript 中定义对象文字使生活变得容易多了。ES6 提供了一种更简单的方法来为对象分配属性。如果变量名与对象键相同，您可以利用简写符号。

手写:

```
const x = 1920, y = 1080;
const obj = { x:x, y:y }; 
```

速记:

```
const obj = { x, y }; 
```

## 9.箭头功能速记

经典函数以其简单的形式易于阅读和编写，但是一旦您开始将它们嵌套在其他函数调用中，它们就会变得有点冗长和混乱。

手写:

```
function sayHello(name) {
  console.log('Hello', name);
}

setTimeout(function() {
  console.log('Loaded')
}, 2000);

list.forEach(function(item) {
  console.log(item);
}); 
```

速记:

```
sayHello = name => console.log('Hello', name);

setTimeout(() => console.log('Loaded'), 2000);

list.forEach(item => console.log(item)); 
```

需要注意的是，arrow 函数中的`this`值的确定不同于 longhand 函数，所以这两个例子并不完全相同。更多细节参见[这篇关于箭头函数语法](https://www.sitepoint.com/es6-arrow-functions-new-fat-concise-syntax-javascript/)的文章。

## 10.隐式返回速记

Return 是我们经常用来返回函数最终结果的关键字。带有单个语句的 arrow 函数将隐式返回其求值结果(该函数必须省略大括号(`{}`)以省略 return 关键字)。

要返回一个多行语句(比如一个对象文字)，需要用`()`而不是`{}`来包装你的函数体。这确保代码作为单个语句进行评估。

手写:

```
function calcCircumference(diameter) {
  return Math.PI * diameter
} 
```

速记:

```
calcCircumference = diameter => (
  Math.PI * diameter;
) 
```

## 11.默认参数值

您可以使用`if`语句来定义函数参数的默认值。在 ES6 中，您可以在函数声明本身中定义默认值。

手写:

```
function volume(l, w, h) {
  if (w === undefined)
    w = 3;
  if (h === undefined)
    h = 4;
  return l * w * h;
} 
```

速记:

```
volume = (l, w = 3, h = 4 ) => (l * w * h);

volume(2) //output: 24 
```

## 12.模板文字

用`' + '`把多个变量串联成一个字符串不累吗？难道没有更简单的方法吗？如果你能使用 ES6，那么你是幸运的。你需要做的就是使用反勾号和`${}`来括住你的变量。

手写:

```
const welcome = 'You have logged in as ' + first + ' ' + last + '.'

const db = 'http://' + host + ':' + port + '/' + database; 
```

速记:

```
const welcome = `You have logged in as ${first}  ${last}`;

const db = `http://${host}:${port}/${database}`; 
```

## 13.解构赋值速记

如果您正在使用任何流行的 web 框架，很有可能会使用数组或对象文字形式的数据在组件和 API 之间传递信息。一旦数据对象到达一个组件，您就需要解包它。

手写:

```
const observable = require('mobx/observable');
const action = require('mobx/action');
const runInAction = require('mobx/runInAction');

const store = this.props.store;
const form = this.props.form;
const loading = this.props.loading;
const errors = this.props.errors;
const entity = this.props.entity; 
```

速记:

```
import { observable, action, runInAction } from 'mobx';

const { store, form, loading, errors, entity } = this.props; 
```

您甚至可以指定自己的变量名:

```
const { store, form, loading, errors, entity:contact } = this.props; 
```

## 14.多行字符串速记

如果您曾经发现自己需要在代码中编写多行字符串，您应该这样写:

手写:

```
const lorem = 'Lorem ipsum dolor sit amet, consectetur\n\t'
    + 'adipisicing elit, sed do eiusmod tempor incididunt\n\t'
    + 'ut labore et dolore magna aliqua. Ut enim ad minim\n\t'
    + 'veniam, quis nostrud exercitation ullamco laboris\n\t'
    + 'nisi ut aliquip ex ea commodo consequat. Duis aute\n\t'
    + 'irure dolor in reprehenderit in voluptate velit esse.\n\t' 
```

但是有一个更简单的方法。就用反斜线。

速记:

```
const lorem = `Lorem ipsum dolor sit amet, consectetur
    adipisicing elit, sed do eiusmod tempor incididunt
    ut labore et dolore magna aliqua. Ut enim ad minim
    veniam, quis nostrud exercitation ullamco laboris
    nisi ut aliquip ex ea commodo consequat. Duis aute
    irure dolor in reprehenderit in voluptate velit esse.` 
```

## 15.扩展运算符速记

在 ES6 中引入的 **spread 操作符**，有几个用例使得 JavaScript 代码使用起来更加高效和有趣。它可以用来替换某些数组函数。扩展操作符只是一系列的三个点。

普通写法

```
// joining arrays
const odd = [1, 3, 5];
const nums = [2 ,4 , 6].concat(odd);

// cloning arrays
const arr = [1, 2, 3, 4];
const arr2 = arr.slice() 
```

速记:

```
// joining arrays
const odd = [1, 3, 5 ];
const nums = [2 ,4 , 6, ...odd];
console.log(nums); // [ 2, 4, 6, 1, 3, 5 ]

// cloning arrays
const arr = [1, 2, 3, 4];
const arr2 = [...arr]; 
```

与`concat()`函数不同，您可以使用 spread 运算符将一个数组插入到另一个数组中的任意位置。

```
const odd = [1, 3, 5 ];
const nums = [2, ...odd, 4 , 6]; 
```

您还可以将 spread 运算符与 ES6 析构符号结合使用:

```
const { a, b, ...z } = { a: 1, b: 2, c: 3, d: 4 };
console.log(a) // 1
console.log(b) // 2
console.log(z) // { c: 3, d: 4 } 
```

## 16.强制参数速记

默认情况下，如果没有传递值，JavaScript 会将函数参数设置为`undefined`。其他一些语言会抛出警告或错误。为了强制参数赋值，您可以使用一个`if`语句来抛出一个错误，如果`undefined`，或者您可以利用“强制参数速记”。

手写:

```
function foo(bar) {
  if(bar === undefined) {
    throw new Error('Missing parameter!');
  }
  return bar;
} 
```

速记:

```
mandatory = () => {
  throw new Error('Missing parameter!');
}

foo = (bar = mandatory()) => {
  return bar;
} 
```

## 17.Array.find 速记

如果你曾经被要求用普通的 JavaScript 编写一个 find 函数，你可能会使用一个`for`循环。在 ES6 中，引入了一个名为`find()`的新数组函数。

手写:

```
const pets = [
  { type: 'Dog', name: 'Max'},
  { type: 'Cat', name: 'Karl'},
  { type: 'Dog', name: 'Tommy'},
]

function findDog(name) {
  for(let i = 0; i<pets.length; ++i) {
    if(pets[i].type === 'Dog' && pets[i].name === name) {
      return pets[i];
    }
  }
} 
```

速记:

```
pet = pets.find(pet => pet.type ==='Dog' && pet.name === 'Tommy');
console.log(pet); // { type: 'Dog', name: 'Tommy' } 
```

## 18.对象[关键]速记

你知道`Foo.bar`也可以写成`Foo['bar']`吗？起初，你似乎没有理由这样写。然而，这种符号为您提供了编写可重用代码的基础。

考虑这个验证函数的简化示例:

```
function validate(values) {
  if(!values.first)
    return false;
  if(!values.last)
    return false;
  return true;
}

console.log(validate({first:'Bruce',last:'Wayne'})); // true 
```

这个函数完美地完成了它的工作。但是，请考虑这样一个场景，您有很多需要应用验证的表单，但是这些表单具有不同的字段和规则。构建一个可以在运行时配置的通用验证函数不是很好吗？

速记:

```
// object validation rules
const schema = {
  first: {
    required:true
  },
  last: {
    required:true
  }
}

// universal validation function
const validate = (schema, values) => {
  for(field in schema) {
    if(schema[field].required) {
      if(!values[field]) {
        return false;
      }
    }
  }
  return true;
}

console.log(validate(schema, {first:'Bruce'})); // false
console.log(validate(schema, {first:'Bruce',last:'Wayne'})); // true 
```

现在我们有了一个可以在所有表单中重用的验证函数，而不需要为每个表单编写自定义的验证函数。

## 19.双位 NOT 速记

按位运算符是您在初级 JavaScript 教程中学习到的特性之一，但您永远无法在任何地方实现它们。此外，如果你不处理二进制，谁会愿意处理 1 和 0 呢？

然而，双位 NOT 运算符有一个非常实用的用例。你可以用它来代替`Math.floor()`。双位 NOT 运算符的优点是，它执行相同的运算要快得多。你可以在这里阅读更多关于位运算符[的内容。](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators)

手写:

```
Math.floor(4.9) === 4  //true 
```

速记:

```
~~4.9 === 4  //true 
```

## 20.指数幂速记

数学指数幂函数的简写:

手写:

```
Math.pow(2,3); // 8
Math.pow(2,2); // 4
Math.pow(4,3); // 64 
```

速记:

```
2**3 // 8
2**4 // 4
4**3 // 64 
```

## 21.将字符串转换为数字

有时，您的代码会接收到字符串格式的数据，但需要以数字格式进行处理。没什么大不了的，我们可以进行快速转换。

手写:

```
const num1 = parseInt("100");
const num2 =  parseFloat("100.01"); 
```

速记:

```
const num1 = +"100"; // converts to int data type
const num2 =  +"100.01"; // converts to float data type 
```

## 22.对象属性分配

考虑下面这段代码:

```
let fname = { firstName : 'Black' };
let lname = { lastName : 'Panther'} 
```

你如何将它们合并成一个对象？一种方法是编写一个函数，将数据从第二个对象复制到第一个对象上。不幸的是，这可能不是您想要的—您可能需要在不改变任何现有对象的情况下创建一个全新的对象。最简单的方法是使用 ES6 中引入的`Object.assign`函数:

```
let full_names = Object.assign(fname, lname); 
```

您还可以使用 ES8 中引入的对象销毁符号:

```
let full_names = {...fname, ...lname}; 
```

可以合并的对象特性的数量没有限制。如果对象具有相同的特性名称，则值将按照合并的顺序被覆盖。

## 23.速记的按位索引

当使用数组执行查找时，`indexOf()`函数用于检索您正在查找的项目的位置。如果没有找到该项，则返回值`-1`。在 JavaScript 中，`0`被认为是“虚假的”，而大于或小于`0`的数字被认为是“真实的”。因此，必须像这样编写正确的代码。

手写:

```
if(arr.indexOf(item) > -1) { // Confirm item IS found

}

if(arr.indexOf(item) === -1) { // Confirm item IS NOT found

} 
```

速记:

```
if(~arr.indexOf(item)) { // Confirm item IS found

}

if(!~arr.indexOf(item)) { // Confirm item IS NOT found

} 
```

除了`-1`之外，`bitwise(~)`操作符将返回一个真值。否定它就像做`!~`一样简单。或者，我们也可以使用`includes()`功能:

```
if(arr.includes(item)) { // Returns true if the item exists, false if it doesn't

} 
```

## 24.Object.entries()

这是 ES8 中引入的一个特性，允许您将文字对象转换为键/值对数组。请参见下面的示例:

```
const credits = { producer: 'John', director: 'Jane', assistant: 'Peter' };
const arr = Object.entries(credits);
console.log(arr);

/** Output:
[ [ 'producer', 'John' ],
  [ 'director', 'Jane' ],
  [ 'assistant', 'Peter' ]
]
**/ 
```

## 25.Object.values()

这也是 ES8 中引入的新功能，它执行与`Object.entries()`类似的功能，但没有关键部分:

```
const credits = { producer: 'John', director: 'Jane', assistant: 'Peter' };
const arr = Object.values(credits);
console.log(arr);

/** Output:
[ 'John', 'Jane', 'Peter' ]
**/ 
```

## 26.建议一个？

我真的很喜欢这些，并希望找到更多，所以请留下评论，如果你知道的话！

## 分享这篇文章