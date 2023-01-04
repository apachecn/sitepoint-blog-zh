# 使用 Array.prototype.reduceRight 在 JavaScript 中组合函数

> 原文：<https://www.sitepoint.com/function-composition-in-javascript/>

在过去的几年里，JavaScript 中的函数式编程越来越受欢迎。虽然它的一些定期推广的原则，如不变性，需要运行时解决方法，但该语言对函数的一流处理已经证明了它对由这种基本原语驱动的可组合代码的支持。在讨论如何从其他函数动态组合函数之前，让我们先简单回顾一下。

## 什么是函数？

实际上，函数是一个*过程*,它允许用户执行一组命令性步骤来执行副作用或返回值。例如:

```
function getFullName(person) {
  return `${person.firstName}  ${person.surname}`;
} 
```

当使用具有`firstName`和`lastName`属性的对象调用该函数时，`getFullName`将返回包含两个对应值的字符串:

```
const character = {
  firstName: 'Homer',
  surname: 'Simpson',
};

const fullName = getFullName(character);

console.log(fullName); // => 'Homer Simpson' 
```

值得注意的是，从 ES2015 开始，JavaScript 现在支持 [*箭头函数*](https://www.sitepoint.com/es6-arrow-functions-new-fat-concise-syntax-javascript/) 语法:

```
const getFullName = (person) => {
  return `${person.firstName}  ${person.surname}`;
}; 
```

假设我们的`getFullName`函数有一个 arity 1(即一个参数)和一个 return 语句，我们可以简化这个表达式:

```
const getFullName = person => `${person.firstName}  ${person.surname}`; 
```

这三种表达方式尽管意思不同，但都达到了相同的目的:

*   创建一个名为`getFullName`的函数，可通过`name`属性访问
*   接受唯一参数，`person`
*   返回由空格分隔的计算字符串`person.firstName`和`person.lastName`

## 通过返回值组合函数

除了将函数返回值赋给声明(例如`const person = getPerson();`)，我们还可以用它们来填充其他函数的参数，或者，一般来说，在 JavaScript 允许的地方提供值。假设我们有各自的函数来执行日志记录和`sessionStorage`副作用:

```
const log = arg => {
  console.log(arg);
  return arg;
};

const store = arg => {
  sessionStorage.setItem('state', JSON.stringify(arg));
  return arg;
};

const getPerson = id => id === 'homer'
  ? ({ firstName: 'Homer', surname: 'Simpson' })
  : {}; 
```

我们可以通过嵌套调用对`getPerson`的返回值执行这些操作:

```
const person = store(log(getPerson('homer')));
// person.firstName === 'Homer' && person.surname === 'Simpson'; => true 
```

考虑到在调用函数时向它们提供所需参数的必要性，最内层的函数将首先被调用。因此，在上面的例子中，`getPerson`的返回值将被传递给`log`，而`log`的返回值将被转发给`store`。从组合的函数调用中构建语句使我们最终能够从原子构建块中构建复杂的算法，但是嵌套这些调用会变得笨拙；如果我们想组合 10 个功能，会是什么样子？

```
const f = x => g(h(i(j(k(l(m(n(o(p(x)))))))))); 
```

幸运的是，我们可以使用一个优雅的通用实现:将一组函数简化为一个高阶函数。

## 用`Array.prototype.reduce`累加数组

[`Array`原型的`reduce`方法](https://www.sitepoint.com/map-reduce-functional-javascript/)获取一个数组实例，*将其累加*成一个单一值。如果我们希望对一组数字求和，可以遵循以下方法:

```
const sum = numbers =>
  numbers.reduce((total, number) => total + number, 0);

sum([2, 3, 5, 7, 9]); // => 26 
```

在这个代码片段中，`numbers.reduce`有两个参数:每次迭代时调用的回调，以及传递给回调的`total`参数的初始值；回调返回的值将在下一次迭代中传递给`total`。通过研究上面对`sum`的调用来进一步分解这一点:

*   我们的回调将运行 5 次
*   因为我们提供了一个初始值，所以在第一次调用时，`total`将是`0`
*   第一次调用将返回`0 + 2`，导致`total`在第二次调用时解析为`2`
*   后续调用`2 + 3`返回的结果将在第三次调用时提供给`total`参数，依此类推。

虽然回调接受两个额外的参数，分别表示当前索引和调用`Array.prototype.reduce`的数组实例，但前两个参数是最关键的，通常称为:

*   `accumulator`–上一次迭代后回调返回的值。在第一次迭代中，这将解析为初始值或数组中的第一项(如果未指定)
*   `currentValue`–当前迭代的数组值；因为它是线性的，所以在调用`Array.prototype.reduce`的过程中，这将从`array[0]`进展到`array[array.length - 1]`

## 用`Array.prototype.reduce`构成函数

既然我们已经了解了如何将数组简化为单个值，我们就可以使用这种方法将现有的函数组合成新的函数:

```
const compose = (...funcs) =>
  initialArg => funcs.reduce((acc, func) => func(acc), initialArg); 
```

注意，我们使用 [rest params 语法(`...` )](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters) 将任意数量的参数强制放入一个数组中，这样消费者就不用为每个调用点显式创建新的数组实例了。`compose`也返回另一个函数，使`compose`成为一个[高阶函数](https://www.sitepoint.com/higher-order-functions-javascript/)，它接受一个初始值(`initialArg`)。这是至关重要的，因为我们可以因此组成新的，可重用的功能，而不需要调用它们，直到必要时；这就是所谓的 [*懒评*](https://www.sitepoint.com/java-in-praise-of-laziness/) 。

因此，我们如何将其他函数组合成一个单一的高阶函数呢？

```
const compose = (...funcs) =>
  initialArg => funcs.reduce((acc, func) => func(acc), initialArg);

const log = arg => {
  console.log(arg);
  return arg;
};

const store = key => arg => {
  sessionStorage.setItem(key, JSON.stringify(arg));
  return arg;
};

const getPerson = id => id === 'homer'
  ? ({ firstName: 'Homer', surname: 'Simpson' })
  : {};

const getPersonWithSideEffects = compose(
  getPerson,
  log,
  store('person'),
);

const person = getPersonWithSideEffects('homer'); 
```

在这段代码中:

*   `person`声明将解析为`{ firstName: 'Homer', surname: 'Simpson' }`
*   上面的`person`表示将被输出到浏览器的控制台
*   `person`将被序列化为 JSON，然后在`person`键下写入会话存储

## 调用顺序的重要性

用一个可组合的实用程序组合任意数量的函数的能力使我们的代码更加整洁和抽象。但是，我们可以通过回顾内联呼叫来强调重要的一点:

```
const g = x => x + 2;
const h = x => x / 2;
const i = x => x ** 2;

const fNested = x => g(h(i(x))); 
```

人们可能会发现用我们的`compose`函数复制这一点是很自然的:

```
const fComposed = compose(g, h, i); 
```

既然如此，为什么`fNested(4) === fComposed(4)`解析为`false`？你可能记得我首先强调了内部调用是如何解释的，因此`compose(g, h, i)`实际上相当于`x => i(h(g(x)))`，因此`fNested`返回`10`，而`fComposed`返回`9`。我们可以简单地颠倒`f`的嵌套或组合变体的调用顺序，但是考虑到`compose`被设计成反映嵌套调用的特殊性，我们需要一种从右到左减少函数的方法；幸运的是，JavaScript 提供了`Array.prototype.reduceRight`:

```
const compose = (...funcs) =>
  initialArg => funcs.reduceRight((acc, func) => func(acc), initialArg); 
```

在这个实现中，`fNested(4)`和`fComposed(4)`都解析为`10`。然而，我们的`getPersonWithSideEffects`函数现在被错误地定义了；尽管我们可以颠倒内部函数的顺序，但在某些情况下，从左向右阅读可以促进对过程步骤的心理分析。事实证明，我们之前的方法已经相当普遍，但通常被称为*管道*:

```
const pipe = (...funcs) =>
  initialArg => funcs.reduce((acc, func) => func(acc), initialArg);

const getPersonWithSideEffects = pipe(
  getPerson,
  log,
  store('person'),
); 
```

通过使用我们的`pipe`函数，我们将保持`getPersonWithSideEffects`所要求的从左到右的排序。[由于上述原因，管道已经成为 RxJS](https://github.com/ReactiveX/rxjs/blob/master/doc/pipeable-operators.md) 的主要产品；可以说，更直观的想法是，组合流中的数据流由操作符以这种顺序操作。

## 作为继承替代的函数组合

在前面的例子中，我们已经看到了如何将功能无限组合成更大的、可重用的、面向目标的单元。函数组合的另一个好处是将自己从继承图的僵化中解放出来。假设我们希望基于类的层次结构重用日志记录和存储行为；人们可以这样表述:

```
class Storable {
  constructor(key) {
    this.key = key;
  }

  store() {
    sessionStorage.setItem(
      this.key,
      JSON.stringify({ ...this, key: undefined }),
    );
  }
}

class Loggable extends Storable {
  log() {
    console.log(this);
  }
}

class Person extends Loggable {
  constructor(firstName, lastName) {
    super('person');
    this.firstName = firstName;
    this.lastName = lastName;
  }

  debug() {
    this.log();
    this.store();
  }
} 
```

除了冗长之外，这段代码的直接问题是我们滥用继承来实现重用；如果另一个类扩展了`Loggable`，它本身也是`Storable`的子类，即使我们不需要这个逻辑。一个潜在的更具灾难性的问题在于命名冲突:

```
class State extends Storable {
  store() {
    return fetch('/api/store', {
      method: 'POST',
    });
  }
}

class MyState extends State {} 
```

如果我们要实例化`MyState`并调用它的`store`方法，我们将不会调用`Storable`的`store`方法，除非我们在`MyState.prototype.store`内添加对`super.store()`的调用，但是这将在`State`和`Storable`之间创建一个紧密的、脆弱的耦合。这可以用[实体系统或策略模式来减轻，正如我在其他地方介绍过的](https://www.youtube.com/watch?v=HbbnTAHY7jg)，但是尽管继承在表达系统更广泛的分类方面有优势，函数组合提供了一种简单、简洁的共享代码的方式，它不依赖于方法名。

## 摘要

JavaScript 对函数作为值的处理，以及产生它们的表达式，有助于更大的、特定于上下文的工作的简单组合。将此任务视为函数数组的累积，可以减少对命令式嵌套调用的需求，并且高阶函数的使用会导致其定义和调用的分离。此外，我们可以摆脱面向对象编程强加的严格的等级约束。

## 分享这篇文章