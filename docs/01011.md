# 使用 ES6 默认参数和属性简化代码

> 原文：<https://www.sitepoint.com/es6-default-parameters/>

创建一个方法也意味着编写一个 API——无论是为你自己、你团队中的另一个开发人员，还是使用你项目的其他开发人员。根据函数的大小、复杂性和用途，您必须考虑默认设置和输入/输出的 API。

[默认函数参数](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Default_parameters)和[属性快捷键](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Object_initializer#New_notations_in_ECMAScript_2015)是 ES6 的两个便利特性，可以帮助你编写 API。

## ES6 默认参数

让我们快速刷新一下我们的知识，再看一下语法。默认参数允许我们用默认值初始化函数。当参数被省略或`undefined`时，使用默认值，这意味着`null`是一个有效值。默认参数可以是从数字到另一个函数的任何值。

```
// Basic syntax
function multiply (a, b = 2) {
  return a * b;
}
multiply(5); // 10

// Default parameters are also available to later default parameters
function foo (num = 1, multi = multiply(num)) {
  return [num, multi];
}
foo(); // [1, 2]
foo(6); // [6, 12] 
```

### 现实世界的例子

让我们用一个基本的函数来演示默认参数是如何加速开发并使代码更有条理的。

我们的示例方法叫做`createElement()`。它接受一些配置参数，并返回一个 HTML 元素。该 API 如下所示:

```
// We want a &lt;p&gt; element, with some text content and two classes attached.
// Returns &lt;p class="very-special-text super-big"&gt;Such unique text&lt;/p&gt;
createElement('p', {
  content: 'Such unique text',
  classNames: ['very-special-text', 'super-big']
});

// To make this method even more useful, it should always return a default
// element when any argument is left out or none are passed at all.
createElement(); // &lt;div class="module-text default"&gt;Very default&lt;/div&gt; 
```

这个实现不会有太多的逻辑，但是由于它的默认覆盖范围，它会变得很大。

```
// Without default parameters it looks quite bloated and unnecessary large.
function createElement (tag, config) {
  tag = tag || 'div';
  config = config || {};

  const element = document.createElement(tag);
  const content = config.content || 'Very default';
  const text = document.createTextNode(content);
  let classNames = config.classNames;

  if (classNames === undefined) {
    classNames = ['module-text', 'default'];
  }

  element.classList.add(...classNames);
  element.appendChild(text);

  return element;
} 
```

到目前为止，一切顺利。这里发生了什么事？我们正在做以下工作:

1.  为我们的参数`tag`和`config`设置默认值，以防它们没有通过*(注意有些棉绒[不喜欢参数重新分配](http://eslint.org/docs/rules/no-param-reassign) )*
2.  用实际内容(和默认值)创建常数
3.  检查是否定义了`classNames`,如果没有，则分配一个默认数组
4.  在返回之前创建和修改元素。

现在，让我们来优化这个函数，使它更简洁，写起来更快，这样它的目的就更明显了:

```
// Default all the things
function createElement (tag = 'div', {
  content = 'Very default',
  classNames = ['module-text', 'special']
} = {}) {
  const element = document.createElement(tag);
  const text = document.createTextNode(content);

  element.classList.add(...classNames);
  element.appendChild(text);

  return element;
} 
```

我们没有触及函数的逻辑，但是从函数体中移除了所有的默认处理。[函数签名](https://developer.mozilla.org/en-US/docs/Glossary/Signature/Function)现在包含所有默认值。

让我进一步解释一部分，这可能会有点混乱:

```
// What exactly happens here?
function createElement ({
  content = 'Very default',
  classNames = ['module-text', 'special']
} = {}) {
  // function body
} 
```

我们不仅声明了一个默认的`object`参数，还声明了默认的对象*属性*。这使得默认配置看起来更明显，而不是仅仅声明一个默认对象(例如`config = {}`)然后设置默认属性。它可能需要一些额外的时间来适应，但最终它会改善您的工作流程。

当然，我们仍然可以讨论更大的配置，因为它可能会产生更多的开销，在函数体中保持默认处理会更简单。

## ES6 酒店人手短缺

如果一个方法接受大的配置对象作为参数，你的代码会变得很大。通常会准备一些变量，并将它们添加到所述对象中。属性缩写为 *[语法糖](https://en.wikipedia.org/wiki/Syntactic_sugar)* 以使这一步更短，可读性更强:

```
const a = 'foo', b = 42, c = function () {};

// Previously we would use these constants like this.
const alphabet = {
  a: a,
  b: b,
  c: c
};

// But with the new shorthand we can actually do this now,
// which is equivalent to the above.
const alphabet = { a, b, c }; 
```

### 缩短您的 API

好吧，回到另一个更常见的例子。下面的函数获取一些数据，对其进行变异并调用另一个方法:

```
function updateSomething (data = {}) {
  const target = data.target;
  const veryLongProperty = data.veryLongProperty;
  let willChange = data.willChange;

  if (willChange === 'unwantedValue') {
    willChange = 'wayBetter';
  }

  // Do more.

  useDataSomewhereElse({
    target: target,
    property: veryLongProperty,
    willChange: willChange,
    // .. more
  });
} 
```

我们经常用相同的名字命名变量和对象属性。使用属性简写，结合[析构](https://www.sitepoint.com/es6-destructuring-assignment/)，我们实际上可以大大缩短我们的代码:

```
function updateSomething (data = {}) {
  // Here we use destructuring to store the constants from the data object.
  const { target, veryLongProperty: property } = data;
  let { willChange } = data;

  if (willChange === 'unwantedValue') {
    willChange = 'wayBetter';
  }

  // Do more.

  useDataSomewhereElse({ target, property, willChange });
} 
```

同样，这可能需要一段时间来适应。最后，这是 JavaScript 中的新特性之一，它帮助我更快地编写代码，并使用更干净的函数体。

但是等等，还有呢！属性简写也可以应用于对象内部的方法定义:

```
// Instead of writing the function keyword everytime,
const module = {
  foo: 42,
  bar: function (value) {
    // do something
  }
};

// we can just omit it and have shorter declarations
const module = {
  foo: 42,
  bar (value) {
    // do something
  }
}; 
```

## 结论

默认参数和属性缩写是使您的方法更有组织的好方法，在某些情况下甚至更短。总的来说，缺省的函数参数帮助我更加专注于方法的实际目的，而不会被大量的缺省准备和 if 语句分散注意力。

Property shorthands 实际上更多的是一个装饰性的特性，但是我发现自己更有效率，并且花更少的时间来编写所有的变量、配置对象和函数关键字。

您是否已经使用默认参数和属性缩写？

*这篇文章由[塞巴斯蒂安·塞茨](https://www.sitepoint.com/community/users/m3g4p0p)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

## 分享这篇文章