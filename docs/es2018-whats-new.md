# ES2018 的新功能

> 原文：<https://www.sitepoint.com/es2018-whats-new/>

在本文中，我将介绍 ES2018 (ES9)引入的 JavaScript 新特性，并举例说明它们的用途和使用方法。

JavaScript (ECMAScript)是一个不断发展的标准，由许多供应商跨多个平台实现。ES6 (ECMAScript 2015)是一个耗时六年才最终完成的大型版本。我们制定了新的年度发布流程，以简化流程并更快地添加功能。ES9 (ES2018)是撰写本文时的最新版本。

技术委员会 39 (TC39)由包括浏览器供应商在内的各方组成，他们开会推动 JavaScript 提案沿着一条严格的发展道路前进:

**阶段 0:稻草人—**
最初提交的想法。

第一阶段:提案—
由至少一名 TC39 成员支持的正式提案文件，其中包括 API 示例。

第二阶段:草案–
具有两个实验性实现的功能规范的初始版本。

**第三阶段:候选人—**
评审提案规范，并从供应商处收集反馈。

**第 4 阶段:完成—**
该提案已准备好包含在 ECMAScript 中，但可能需要更长时间才能在浏览器和 Node.js 中发布

## 现在是 2016 年

ES2016 通过增加两个小功能证明了标准化过程:

1.  数组[包含()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes)方法，当数组中包含值时，该方法返回 true 或 false，并且
2.  `a ** b` [取幂](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Exponentiation_%28**%29)运算符，与`Math.pow(a, b)`相同。

## 现在是 2017 年

ES2017 提供了更多新功能:

*   更清晰的 Promise 语法的异步函数
*   `Object.values()`从包含名称-值对的对象中提取一个值数组
*   `Object.entries()`，返回包含对象中名称和值的子数组的数组
*   `Object.getOwnPropertyDescriptors()`返回为另一个对象的自身属性定义属性描述符的对象(`.value`、`.writable`、`.get`、`.set`、`.configurable`、`.enumerable`)
*   `padStart()`和`padEnd()`，字符串填充的两个元素
*   对象定义、数组声明和函数参数列表中的尾随逗号
*   `SharedArrayBuffer`和`Atomics`用于读取和写入共享内存位置(响应 [Spectre 漏洞](https://meltdownattack.com/)而禁用)。

请参考[es 2017 的新功能](https://www.sitepoint.com/es2017-whats-new)了解更多信息。

## 现在是 2018 年

ECMAScript 2018(或者 ES9，如果你更喜欢旧的符号)现在可用。下面的特性已经达到了第 4 阶段，尽管在撰写本文时，工作实现在不同的浏览器和运行时环境下还不完善。

## 异步迭代

在 async/await 旅程中的某个时刻，您将尝试在同步循环中调用内的异步函数*。例如:*

```
async function process(array) {
  for (let i of array) {
    await doSomething(i);
  }
} 
```

没用的。这也不会:

```
async function process(array) {
  array.forEach(async i => {
    await doSomething(i);
  });
} 
```

循环本身保持同步，并且总是在其内部异步操作之前完成。

ES2018 引入了异步迭代器，除了`next()`方法返回一个承诺之外，它就像常规迭代器一样。因此，`await`关键字可以和`for … of`循环一起使用，以连续运行异步操作。例如:

```
async function process(array) {
  for await (let i of array) {
    doSomething(i);
  }
} 
```

## 无极. finally()

承诺链可以成功并到达最后的`.then()`，也可以失败并触发`.catch()`阻塞。在某些情况下，无论结果如何，您都希望运行相同的代码—例如，清理、删除对话框、关闭数据库连接等。

`.finally()`原型允许您在一个地方指定最终逻辑，而不是在最后的`.then()`和`.catch()`中复制它:

```
function doSomething() {
  doSomething1()
  .then(doSomething2)
  .then(doSomething3)
  .catch(err => {
    console.log(err);
  })
  .finally(() => {
    // finish here!
  });
} 
```

## 休息/传播属性

ES2015 引入了休止符参数和扩展运算符。三点(`...`)符号仅适用于数组运算。Rest 参数将传递给函数的最后一个参数转换成一个数组:

```
restParam(1, 2, 3, 4, 5);

function restParam(p1, p2, ...p3) {
  // p1 = 1
  // p2 = 2
  // p3 = [3, 4, 5]
} 
```

spread 操作符以相反的方式工作，将数组转换成可以传递给函数的独立参数。例如，给定任意数量的参数，`Math.max()`返回最大值:

```
const values = [99, 100, -1, 48, 16];
console.log( Math.max(...values) ); // 100 
```

ES2018 为对象析构和数组启用了类似的静止/扩散功能。一个基本的例子:

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

或者您可以使用它将值传递给函数:

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

像数组一样，在声明的末尾只能使用一个 rest 参数。此外，它只在每个对象的顶层起作用，而不是子对象。

扩展运算符可以在其他对象中使用。例如:

```
const obj1 = { a: 1, b: 2, c: 3 };
const obj2 = { ...obj1, z: 26 };
// obj2 is { a: 1, b: 2, c: 3, z: 26 } 
```

您可以使用 spread 操作符来克隆对象(`obj2 = { ...obj1 };`)，但是请注意，您只能获得浅层副本。如果一个属性包含另一个对象，则克隆将引用同一个对象。

## 命名为捕获组的正则表达式

JavaScript 正则表达式可以返回一个 match 对象——一个包含匹配字符串的类似数组的值。例如，要解析 YYYY-MM-DD 格式的日期:

```
const
  reDate = /([0-9]{4})-([0-9]{2})-([0-9]{2})/,
  match  = reDate.exec('2018-04-30'),
  year   = match[1], // 2018
  month  = match[2], // 04
  day    = match[3]; // 30 
```

它很难阅读，并且改变正则表达式也可能改变匹配对象索引。

ES2018 允许在开始捕捉括号`(`后立即使用符号`?<name>`来命名群组。例如:

```
const
  reDate = /(?<year>[0-9]{4})-(?<month>[0-9]{2})-(?<day>[0-9]{2})/,
  match  = reDate.exec('2018-04-30'),
  year   = match.groups.year,  // 2018
  month  = match.groups.month, // 04
  day    = match.groups.day;   // 30 
```

任何不匹配的命名组都将其属性设置为`undefined`。

命名捕获也可以在`replace()`方法中使用。例如，将日期转换为美国 MM-DD-YYYY 格式:

```
const
  reDate = /(?<year>[0-9]{4})-(?<month>[0-9]{2})-(?<day>[0-9]{2})/,
  d      = '2018-04-30',
  usDate = d.replace(reDate, '$<month>-$<day>-$<year>'); 
```

## 正则表达式回溯断言

JavaScript 目前支持正则表达式中的*前瞻*断言。这意味着匹配必须发生，但什么也没有捕获，断言不包括在整个匹配的字符串中。例如，要从任何价格中获取货币符号:

```
const
  reLookahead = /\D(?=\d+)/,
  match       = reLookahead.exec('$123.89');

console.log( match[0] ); // $ 
```

ES2018 引入了以相同方式工作的 *lookbehind* 断言，但用于之前的匹配。因此，我们可以捕捉价格数字，忽略货币字符:

```
const
  reLookbehind = /(?<=\D)\d+/,
  match        = reLookbehind.exec('$123.89');

console.log( match[0] ); // 123.89 
```

这是一个积极的回顾断言；非数字`\D`必须存在。还有一个负的 look back 断言，它设置一个值不能存在。例如:

```
const
  reLookbehindNeg = /(?<!\D)\d+/,
  match           = reLookbehind.exec('$123.89');

console.log( match[0] ); // null 
```

## 正则表达式(dotAll)标志

正则表达式点`.`匹配任何单个字符*，除了*回车。`s`标志改变了这种行为，因此允许使用行终止符。例如:

```
/hello.world/s.test('hello\nworld'); // true 
```

## 正则表达式 Unicode 属性转义

到目前为止，还不可能在正则表达式中本地访问 Unicode 字符属性。ES2018 在设置了`u` (unicode)标志的正则表达式中以`\p{...}`和`\P{...}`的形式添加了 Unicode 属性转义。例如:

```
const reGreekSymbol = /\p{Script=Greek}/u;
reGreekSymbol.test('π'); // true 
```

## 模板文字调整

最后，模板文本中所有与转义序列相关的语法限制都被删除了。

以前，`\u`开始 unicode 转义，`\x`开始十六进制转义，`\`后跟一个数字开始八进制转义。这使得不可能创建某些字符串，例如 Windows 文件路径`C:\uuu\xxx\111`。更多细节，请参考 [MDN 模板文字文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)。

这就是 ES2018，但 ES2019 的工作已经开始。明年你有什么特别想看的功能吗？

## 分享这篇文章