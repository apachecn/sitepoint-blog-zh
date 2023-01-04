# ES2017 的新功能:异步功能、改进的对象等

> 原文：<https://www.sitepoint.com/es2017-whats-new/>

**让我们来看看 ES2017 中最重要的 JavaScript 更新，并简要介绍这一更新过程实际上是如何发生的。**

## 更新过程

JavaScript (ECMAScript)是一个不断发展的标准，由许多供应商跨多个平台实现。ES6 (ECMAScript 2015)是一个耗时六年才最终完成的大型版本。制定了新的年度发布流程，以简化流程并快速添加新功能。

名副其实的技术委员会 39 (TC39)由包括浏览器供应商在内的各方组成，他们开会推动 JavaScript 提案沿着一条严格的发展道路前进:

第 0 阶段:strawman-
对新的或改进的 ECMAScript 特性的最初提交。

**第一阶段:提案—**
由 TC39 至少一名成员支持的正式提案文档，包括 API 示例、语言语义、算法、潜在障碍、多项填充和演示。

第二阶段:草案-
特性规范的初始版本。该特性需要两个实验实现，尽管其中一个可以在诸如 Babel 的 transpiler 中实现。

**第三阶段:候选人—**
评审提案规范，并从供应商处收集反馈。

**第 4 阶段:完成—**
该提案已准备好纳入 ECMAScript。一个特性一旦达到这个阶段就应该被认为是一个标准。但是，在浏览器和 Node.js 之类的运行时中，它可能需要更长的时间。

如果 ES2015 太大，ES2016 就故意做得很小，以证明标准化过程。增加了两个新功能:

1.  数组`.includes()`方法，当数组中包含一个值时，该方法返回 true 或 false，以及
2.  `a ** b`取幂运算符，与`Math.pow(a, b)`相同。

## ES2017 的新功能

ES2017(或以前的 ES8)的特性集被认为是对 ECMAScript 规范的第一次适当修改。它交付以下货物…

## 异步函数

与大多数语言不同，JavaScript 在默认情况下是异步的。花费大量时间的命令不会停止执行。这包括请求 URL、读取文件或更新数据库等操作。必须传递一个回调函数，该函数在操作结果已知时执行。

当一系列嵌套的异步函数必须按顺序执行时，这会导致回调地狱。例如:

```
function doSomething() {
  doSomething1((response1) => {
    doSomething2(response1, (response2) => {
      doSomething3(response2, (response3) => {
        // etc...
      };
    });
  });
} 
```

ES2015 (ES6)引入了 Promises，它提供了一种更简洁的方式来表达相同的功能。一旦您的函数被承诺，它们就可以使用:

```
function doSomething() {
  doSomething1()
  .then(doSomething2)
  .then(doSomething3)
} 
```

ES2017 异步函数承诺让异步调用更加清晰:

```
async function doSomething() {
  const
    response1 = await doSomething1(),
    response2 = await doSomething2(response1),
    response3 = await doSomething3(response2);
} 
```

有效地使每个调用看起来像是同步的，同时不会阻碍 JavaScript 的单个处理线程。

所有现代浏览器(不是 IE 或 Opera Mini)和 Node.js 7.6+都支持异步函数。它们将改变你编写 JavaScript 的方式，一整篇文章将致力于回调、承诺和异步函数。还好我们有一个！参考现代 JavaScript 中的[流量控制。](https://www.sitepoint.com/flow-control-callbacks-promises-async-await)

## Object.values()

`Object.values()`是一种从对象内的名称-值对中提取值数组的快速且更具声明性的方法。例如:

```
const myObject = {
  a: 1,
  b: 'Two',
  c: [3,3,3]
}

const values = Object.values(myObject);
// [ 1, 'Two', [3,3,3] ] 
```

您再也不需要编写`for … of`循环了！`Object.values`在所有现代浏览器(不是 IE 或 Opera Mini)和 Node.js 7.0+中都是原生支持的。

## Object.entries()

从包含名称-值对的对象中返回一个数组。返回数组中的每个值都是包含名称(索引 0)和值(索引 1)的子数组。例如:

```
const myObject = {
  a: 1,
  b: 'Two',
  c: [3,3,3]
}

const entries = Object.entries(myObject);
/*
[
  [ 'a', 1 ],
  [ 'b', 'Two' ],
  [ 'c', [3,3,3] ]
]
*/ 
```

这提供了另一种迭代对象属性的方法。它也可以用来定义一个[图](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map):

```
const map = new Map(Object.entries({
  a: 1,
  b: 2,
  c: 3
})); 
```

`Object.values`在大多数现代浏览器(但不包括 IE、Opera Mini 和 iOS Safari)和 Node.js 7.0+中被原生支持。

## Object.getOwnPropertyDescriptors()

`Object.getOwnPropertyDescriptors()`方法返回另一个包含所有属性描述符(`.value`、`.writable`、`.get`、`.set`、`.configurable`、`.enumerable`)的对象。

属性直接出现在对象上，而不在对象的原型链中。它类似于[object . getownpropertydescriptor(object，property)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptor)——除了返回所有属性，而不仅仅是一个。例如:

```
const myObject = {
  prop1: 'hello',
  prop2: 'world'
};

const descriptors = Object.getOwnPropertyDescriptors(myObject);

console.log(descriptors.prop1.writable); // true
console.log(descriptors.prop2.value);    // 'world' 
```

## padStart()和 padEnd()字符串填充

字符串填充在 JavaScript 中一直存在争议。广受欢迎的[左键盘](https://github.com/stevemao/left-pad/)库在吸引了代表同名即时通讯应用的律师的注意后，被从 npm 撤下。不幸的是，它已经被用作成千上万个项目的依赖，互联网崩溃了。npm 随后更改了操作程序，左心导管未公布。

ES2017 中添加了原生字符串填充，因此无需使用第三方模块。`.padStart()`和`.padEnd()`分别将字符添加到字符串的开头或结尾，直到它们达到所需的长度。两者都接受最小长度和可选的`'fill'`字符串(默认为空格)作为参数。示例:

```
'abc'.padStart(5);         // '  abc'
'abc'.padStart(5,'-');     // '--abc'
'abc'.padStart(10, '123'); // '1231231abc'
'abc'.padStart(1);         // 'abc'

'abc'.padEnd(5);           // 'abc  '
'abc'.padEnd(5,'-');       // 'abc--'
'abc'.padEnd(10, '123');   // 'abc1231231'
'abc'.padEnd(1);           // 'abc' 
```

所有现代浏览器(非 IE)和 Node.js 8.0+都支持`.padStart()`和`.padEnd()`。

## 允许尾随逗号

ES2017 的一个小更新:尾随逗号不再在对象定义、数组声明、函数参数列表等中引发语法错误:

```
// ES2017 is happy!
const a = [1, 2, 3,];

const b = {
  a: 1,
  b: 2,
  c: 3,
};

function c(one,two,three,) {}; 
```

在所有浏览器和 Node.js 中都启用了尾随逗号，但是在编写时只有 Chrome 58+和 Firefox 52+支持函数参数中的尾随逗号。

## SharedArrayBuffer 和原子

[SharedArrayBuffer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) 对象用于表示一个固定长度的原始二进制数据缓冲区，可以在 web workers 之间共享。[原子](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Atomics)对象提供了一种可预测的方式来读取和写入由`SharedArrayBuffer`定义的内存位置。

虽然这两个对象都在 Chrome 和 Firefox 中实现，但它在 2018 年 1 月被禁用，以应对 [Spectre 漏洞](https://meltdownattack.com/)。

ECMAScript 2017 语言规范全文可从 [ECMA 国际网站](https://www.ecma-international.org/publications/standards/Ecma-262.htm)获得。你还想吃更多吗？ES2018 中的[新功能已经公布！](https://www.sitepoint.com/es2018-whats-new)

## 分享这篇文章