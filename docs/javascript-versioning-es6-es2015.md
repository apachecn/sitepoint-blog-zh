# ES6 (ES2015)及更高版本:了解 JavaScript 版本

> 原文：<https://www.sitepoint.com/javascript-versioning-es6-es2015/>

随着编程语言的发展，JavaScript 的发展在过去的几年里变得异常疯狂。现在每年都会看到 ECMAScript 规范的新版本，人们很容易对 JavaScript 版本化、哪个版本支持什么以及如何让代码经得起未来考验感到困惑。

为了更好地理解这些看似源源不断的新特性背后的方式和原因，让我们简要回顾一下 JavaScript 和 JavaScript 版本化的历史，并找出标准化过程如此重要的原因。

## JavaScript 版本控制的早期历史

JavaScript 的原型是由 Brendan Eich 在 1995 年 5 月只用了十天就完成的。他最初被招募来为 Netscape Navigator 实现一个 Scheme 运行时，但是管理团队要求开发一种 C 风格的语言来补充当时刚刚发布的 Java。

JavaScript 于 1995 年 12 月在 Netscape Navigator 的第 2 版中首次亮相。第二年，微软对 JavaScript 进行逆向工程，创建了他们自己的版本，称之为 JScript。JScript 随 Internet Explorer 浏览器的第 3 版一起提供，几乎与 JavaScript 相同——甚至包括所有相同的错误和怪癖——但它确实有一些额外的 Internet Explorer 独有的功能。

## ECMAScript 的诞生

确保 JScript(以及任何其他变体)与 JavaScript 保持兼容的必要性促使 Netscape 和 Sun Microsystems 对该语言进行标准化。他们在欧洲计算机制造商协会的帮助下完成了这项工作，该协会将主持这项标准。这种标准化语言被称为 ECMAScript，以避免侵犯 Sun 的 Java 商标——此举引起了相当大的混乱。最终 ECMAScript 被用来指代规范，JavaScript 被用来(现在仍然是)指代语言本身。

负责 JavaScript 版本控制和维护 ECMAScript 的工作组被称为[技术委员会 39](https://ecma-international.org/memento/TC39.htm) ，或 TC39。它由苹果、谷歌、微软和 Mozilla 等主要浏览器厂商的代表，以及对网络开发感兴趣的其他公司的特邀专家和代表组成。他们定期开会决定语言的发展方向。

当 JavaScript 在 1997 年由 TC39 标准化时，该规范被称为 ECMAScript 版本 1。ECMAScript 的后续版本最初是每年发布一次，但最终由于缺乏共识和围绕 ECMAScript 4 的难以管理的大功能集而变得分散。这个版本因此被终止并缩减为 3.1，但并没有最终确定为 ECMAScript 5。这是在 ECMAScript 3 发布 10 年后的 2009 年 12 月发布的，引入了 [JSON 序列化 API](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON) 、 [Function.prototype.bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind) 和[严格模式](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode)以及其他功能。两年后发布了一个维护版本，澄清了最新版本 5.1 的一些模糊之处。

* * *

你想深入了解 JavaScript 的历史吗？然后查看第一章 *[JavaScript:新手到忍者，第二版](https://www.sitepoint.com/premium/books/javascript-novice-to-ninja-2nd-edition)* 。

* * *

## ECMAScript 2015 和年度版本的复兴

随着 TC39 对 ECMAScript 4 产生的分歧的解决，Brendan Eich 强调了短期内更小版本的需求。这些新规范中的第一个是 *ES2015* (最初命名为 ECMAScript 6，或 ES6)。这个版本是支持未来年度 JavaScript 版本的一个庞大但必要的基础。它包含了当今许多开发人员喜爱的许多特性，例如:

*   [类](https://www.sitepoint.com/object-oriented-javascript-deep-dive-es6-classes/)
*   [承诺](https://www.sitepoint.com/overview-javascript-promises/)
*   [箭头功能](https://www.sitepoint.com/es6-arrow-functions-new-fat-concise-syntax-javascript/)
*   [ES 模块](https://www.sitepoint.com/understanding-es6-modules/)
*   [生成器和迭代器](https://www.sitepoint.com/ecmascript-2015-generators-and-iterators/)

ES2015 是首款遵循 *TC39 流程*的产品，TC39 流程是一种基于提案的讨论和采用元素的模型。

## TC39 流程

在一个提议被 ECMAScript 的下一个版本接受之前，它必须通过五个阶段。

### 阶段 0:稻草人

这是一个方便的步骤，允许向规范提交想法。任何人都可以对特性提出建议——即 TC39 成员和注册为贡献者的非成员。

### 第一阶段:提案

提案正式化的第一个阶段。有必要:

*   描述通过该解决方案纠正的任何存在的问题
*   提供了 API 大纲、高级实现细节以及多种填充和/或演示
*   预先讨论了潜在的障碍。

必须选出一名*冠军*来采纳和推进提案。此人必须是 TC39 成员。

### 第二阶段:草稿

这是 ECMAScript 未来版本中可能包含的一个特性的里程碑。这里，使用规范描述的[形式语言](https://www.ecma-international.org/ecma-262/8.0/index.html#sec-notational-conventions)详细描述了提议的语法和语义。此时应该有一个实验性的实现。

### 第三阶段:候选人

在这里，大部分提议和支持技术已经开发出来，但是它需要来自用户和实现者(比如浏览器供应商)的进一步反馈。一旦这是可利用的，并采取行动，大纲和规格细节是最终确定的，并由指定的审查人员和任命的编辑签署。由于在这个阶段需要一个兼容的实现，因此只有关键的改变才会被接受。

### 第四阶段:完成

该建议已被接受，可以添加到 ECMAScript 中。因此，固有的是:

*   验收测试是 [*测试 262* 套件](https://github.com/tc39/test262)的一部分，用 JavaScript 编写，用来证明特性的一致性和行为
*   至少有两个兼容的实现是可用的，并且已经发布，所有这些都展示了健壮性和开发人员的可用性
*   拉取请求已提交给[官方 ECMA-262 回购](https://github.com/tc39/ecma262)，该请求已由规范编辑器签署。

上述资源库的[贡献文档](https://github.com/tc39/ecma262/blob/master/CONTRIBUTING.md)进一步详述了 GitHub 问题的使用和管理语言添加的拉请求。

## 走向

随着 ES2015 的完成和 TC39 JavaScript 版本控制和更新流程的建立，每年 6 月都会发布后续版本，其中包括一年的提案。在撰写本文时，已经有了三个新的规范。

### 现在是 2016 年

也被称为 ES7，这是 ECMAScript 的第一个较小的增量版本。除了漏洞修复，它只增加了两个功能。

#### [Array.prototype.includes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes)

这个实例方法简化了在`Array`中搜索值:

```
// pre-ES2016:
const hasBob = names.indexOf('bob') > -1;

// ES2016:
const hasBob = names.includes('bob'); 
```

#### 指数算子

在 ES2016 之前，人们可以使用`Math.pow(base, exponent)`执行幂运算。这个版本[引入了一个运算符(**)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Arithmetic_Operators#Exponentiation_(**)) ，它有自己的优先级:

```
// pre-ES2016
Math.pow(5, 3); // => 125

// ES2016
5 ** 3; // => 125 
```

### 现在是 2017 年

ES2017(又名 ES8)是一个稍大的版本，包含一些有用的方法和语法结构。

#### 异步函数

承诺把我们从回调地狱中拯救了出来，但是他们的 API 仍然显示了冗长。[异步函数](https://www.sitepoint.com/simplifying-asynchronous-coding-async-functions/)用一种非常类似同步代码的语法对它们进行抽象:

```
// promises
const getProfile = name => {
  return fetch(`https://some-api/people/${name}`)
    .then(res => res.json())
    .then(({ profile }) => profile); // destructuring `profile` from parsed object
};

// async/await
const getProfile = async name => {
  const res = await fetch(`https://some-api/people/${name}`);
  const { profile } = await res.json();
  return profile;
}; 
```

#### 字符串填充方法

[string . prototype . pad start(length，padder)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/padStart) 和 [padEnd(length，padder)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/padEnd) 将分别在一个字符串上反复前置和追加`padder`(这是可选的，默认为空格)直到到达`length`个字符:

```
'foo'.padStart(6);          // => '   foo';
'foo'.padEnd(6);            // => 'foo   ';
'foo'.padStart(10, 'bar');  // => 'barbarbfoo';
'foo'.padEnd(10, 'bar');    // => 'foobarbarb'; 
```

其他特性包括[尾随逗号](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Trailing_commas)、[共享内存和原子](http://2ality.com/2017/01/shared-array-buffer.html)，以及静态`Object`方法( [Object.entries()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/entries) 、 [Object.values()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/values) 和[object . getownpropertydescriptors()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptors))。)

如果您想了解有关 ES2017 完整功能集的更多信息，请参阅我们的文章，其中涵盖了 ES2017 的新功能。

### 现在是 2018 年

在撰写本文时，这个最新的版本引入了一小部分强大的新增功能。

#### 异步迭代器

虽然`Promise.all()`允许您等待多个承诺的解析，但在某些情况下，您可能需要顺序迭代异步检索的值。现在可以等待[异步迭代器](https://jakearchibald.com/2017/async-iterators-and-generators/)和承诺数组了:

```
(async () => {
  const personRequests = ['bob', 'sarah', 'laura'].map(
    n => fetch(`https://api/people/${n}`)
  );

  for await (const response of personRequests) {
    console.log(await response.json());
  }
})(); 
```

#### 对象扩散和静止属性

表面上看，这两个语法上的改进已经在 JavaScript 开发人员中很流行了，这要归功于像 [Babel](https://babeljs.io/) 这样的编译器的出现。[对象传播和静止属性](https://developers.google.com/web/updates/2017/06/object-rest-spread)类似于数组传播和静止属性，允许对对象属性进行浅层复制和分组析构:

```
const react = {
  name: 'React',
  vendor: 'Facebook',
  description: 'A JavaScript library for building user interfaces',
  npm: true,
  cdn: true,
};

/* Use spread syntax inside an object literal to create
 * a shallow copy, while overriding certain properties.
 */
const vue = {
  ...react,
  vendor: 'Evan You',
  description: 'A JavaScript framework for building UIs',
};

/* Use rest within destructuring syntax to create a
 * label for accessing additional object properties.
 */
const { name, vendor, ...rest } = vue;
console.log(rest.description); // => 'A JavaScript framework for building UIs' 
```

其他被接受的提议还有[promise . prototype . finally()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/finally)，以及对[正则表达式](https://mathiasbynens.be/notes/es-regexp-proposals)和[模板文字](http://2ality.com/2016/09/template-literal-revision.html)的增强。

如果您想了解有关 ES2018 完整功能集的更多信息，请参阅我们的文章，内容涵盖了 ES2018 中的[新增功能。](https://www.sitepoint.com/es2018-whats-new)

## 最后一句话

JavaScript 在很短的时间内有了很大的发展。虽然这要归功于 ECMAScript 标准和 TC39 的出色工作，但由于 JavaScript 版本控制和开发先前缺乏稳定性和内聚力，这最初是一个艰难的旅程。

由于相对成熟的提案流程，语言只能以务实和可管理的方式进行改进。这是一个做网页开发者的好时机！

## 分享这篇文章