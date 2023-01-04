# TypeScript 中的编译时不变性

> 原文：<https://www.sitepoint.com/compile-time-immutability-in-typescript/>

**[TypeScript](https://www.sitepoint.com/typescript-tutorial-for-beginners/) 允许我们用类型信息来修饰[符合规范的 ECMAScript](https://www.ecma-international.org/ecma-262/) ，我们可以使用专用的编译器分析并输出普通的 JavaScript。在大规模项目中，这种静态分析可以在诉诸冗长的调试会话之前捕捉到潜在的错误，更不用说部署到生产环境中了。然而，TypeScript 中的引用类型仍然是可变的，这可能会在我们的软件中导致意想不到的副作用。**

在本文中，我们将探讨禁止引用变异可能带来的好处。

> 需要复习 JavaScript 中的不变性吗？阅读我们的指南，[JavaScript 中的不变性](https://www.sitepoint.com/immutability-javascript/)。

## 原语与引用类型

JavaScript 定义了两组最重要的数据类型*:*

 **   原语:不可变的低级值(如字符串、数字、布尔值等)。)
*   引用:属性的集合，代表可识别的堆内存，是**可变的**(例如，对象、数组、`Map`等)。)

假设我们声明了一个常量，并给它赋了一个字符串:

```
const message = 'hello'; 
```

假定字符串是原语，因此是不可变的，我们不能直接修改这个值。它只能用于产生新的值:

```
console.log(message.replace('h', 'sm')); // 'smello'
console.log(message); // 'hello' 
```

尽管调用了`message`上的`replace()`，我们并没有修改它的内存。我们只是创建了一个新的字符串，保留了`message`的原始内容。

默认情况下，改变`message`的索引是不可行的，但是在严格模式下会抛出一个`TypeError`:

```
'use strict';

const message = 'hello';
message[0] = 'j'; // TypeError: 0 is read-only 
```

注意，如果`message`的声明使用了`let`关键字，我们将能够替换它所解析的值:

```
let message = 'hello';
message = 'goodbye'; 
```

重要的是要强调这是*而不是*突变。相反，我们用一个不变的值替换另一个不变的值。

## 可变引用

让我们对比一下原语和引用的行为。让我们声明一个具有几个属性的对象:

```
const me = {
  name: 'James',
  age: 29,
}; 
```

鉴于 JavaScript 对象是可变的，我们可以更改它现有的属性并添加新的属性:

```
me.name = 'Rob';
me.isTall = true;

console.log(me); // Object { name: "Rob", age: 29, isTall: true }; 
```

与原语不同，对象可以直接变异，而不需要用新的引用来替换。我们可以通过在两个声明中共享一个对象来证明这一点:

```
const me = {
  name: 'James',
  age: 29,
};

const rob = me;

rob.name = 'Rob';

console.log(me); // { name: 'Rob', age: 29 } 
```

继承自`Object.prototype`的 JavaScript 数组也是可变的:

```
const names = ['James', 'Sarah', 'Rob'];

names[2] = 'Layla';

console.log(names); // Array(3) [ 'James', 'Sarah', 'Layla' ] 
```

## 可变引用有什么问题？

假设我们有一个由前五个斐波纳契数列组成的可变数组:

```
const fibonacci = [1, 2, 3, 5, 8];

log2(fibonacci); // replaces each item, n, with Math.log2(n);
appendFibonacci(fibonacci, 5, 5); // appends the next five Fibonacci numbers to the input array 
```

这段代码表面上看起来似乎无关紧要，但是由于`log2`改变了它接收的数组，我们的`fibonacci`数组将不再像名字所暗示的那样专门表示斐波那契数。相反，`fibonacci`会变成`[0, 1, 1.584962500721156, 2.321928094887362, 3, 13, 21, 34, 55, 89]`。因此，有人可能会认为这些声明的名称在语义上不准确，使得程序的流程更难理解。

## JavaScript 中的伪不可变对象

尽管 JavaScript 对象是可变的，但我们可以利用特定的构造来深度克隆引用，即 [spread 语法](https://www.sitepoint.com/shorthand-javascript-techniques/#15spreadoperatorshorthand):

```
const me = {
  name: 'James',
  age: 29,
  address: {
    house: '123',
    street: 'Fake Street',
    town: 'Fakesville',
    country: 'United States',
    zip: 12345,
  },
};

const rob = {
  ...me,
  name: 'Rob',
  address: {
    ...me.address,
    house: '125',
  },
};

console.log(me.name); // 'James'
console.log(rob.name); // 'Rob'
console.log(me === rob); // false 
```

扩展语法也与数组兼容:

```
const names = ['James', 'Sarah', 'Rob'];
const newNames = [...names.slice(0, 2), 'Layla'];

console.log(names); // Array(3) [ 'James', 'Sarah', 'Rob' ]
console.log(newNames); // Array(3) [ 'James', 'Sarah', 'Layla' ]
console.log(names === newNames); // false 
```

在处理引用类型时不变地思考可以使我们的代码行为更加清晰。回顾之前的可变 Fibonacci 示例，我们可以通过将`fibonacci`复制到一个新数组中来避免这种突变:

```
const fibonacci = [1, 2, 3, 5, 8];
const log2Fibonacci = [...fibonacci];

log2(log2Fibonacci);
appendFibonacci(fibonacci, 5, 5); 
```

与其将创建副本的负担放在消费者身上，不如让`log2`和`appendFibonacci`将它们的输入视为只读，基于它们创建新的输出:

```
const PHI = 1.618033988749895;

const log2 = (arr: number[]) => arr.map(n => Math.log2(2));
const fib = (n: number) => (PHI ** n - (-PHI) ** -n) / Math.sqrt(5);

const createFibSequence = (start = 0, length = 5) =>
  new Array(length).fill(0).map((_, i) => fib(start + i + 2));

const fibonacci = [1, 2, 3, 5, 8];
const log2Fibonacci = log2(fibonacci);
const extendedFibSequence = [...fibonacci, ...createFibSequence(5, 5)]; 
```

通过编写我们的函数来返回新的引用以支持改变它们的输入，由`fibonacci`声明标识的数组保持不变，并且它的名称仍然是有效的上下文源。最终，这段代码更具有确定性。

## 掩盖裂缝

通过一点训练，我们也许能够对引用采取行动，就好像它们是单独可读的一样，但是它们禁止在其他地方发生突变。怎样才能阻止我们在应用程序的远程部分引入流氓语句来改变`fibonacci`?

```
fibonacci.push(4); 
```

ECMAScript 5 引入了`Object.freeze()`，它提供了一些针对变异对象的防御措施:

```
'use strict';

const me = Object.freeze({
  name: 'James',
  age: 29,
  address: {
    // props from earlier example
  },
});

me.name = 'Rob'; // TypeError: 'name' is read-only
me.isTheBest = true; // TypeError: Object is not extensible 
```

不幸的是，它只是简单地禁止属性突变，因此嵌套的对象仍然可以被改变:

```
// No TypeErrors will be thrown
me.address.house = '666';
me.address.foo = 'bar'; 
```

可以对特定树中的所有对象调用这个方法，但是这很快被证明是不实用的。也许我们可以利用 TypeScript 的特性来实现编译时的不变性。

## 用常量断言深度冻结文字表达式

在 TypeScript 中，我们可以使用 *const assertions* ，一个[类型断言](https://www.typescriptlang.org/docs/handbook/basic-types.html#type-assertions)的扩展，来从文字表达式中计算一个深度的只读类型:

```
const sitepoint = {
  name: 'SitePoint',
  isRegistered: true,
  address: {
    line1: 'PO Box 1115',
    town: 'Collingwood',
    region: 'VIC',
    postcode: '3066',
    country: 'Australia',
  },
  contentTags: ['JavaScript', 'HTML', 'CSS', 'React'],
} as const; 
```

用`as const`注释这个对象文字表达式会导致 TypeScript 计算它能计算的最具体的只读类型:

```
{
  readonly name: 'SitePoint';
  readonly isRegistered: true;
  readonly address: {
    readonly line1: 'PO Box 1115';
    readonly town: 'Collingwood';
    readonly region: 'VIC';
    readonly postcode: '3066';
    readonly country: 'Australia';
  };
  readonly contentTags: readonly ['JavaScript', 'HTML', 'CSS', 'React'];
} 
```

换句话说:

*   开放原语将被缩小到精确的文字类型(例如`boolean` = > `true`)
*   用`readonly`修改对象文字的属性
*   数组文字将变成`readonly`元组(例如`string[]` = > `['foo', 'bar', 'baz']`)

尝试添加或替换任何值都会导致 TypeScript 编译器引发错误:

```
sitepoint.isCharity = true; // isCharity does not exist on inferred type
sitepoint.address.country = 'United Kingdom'; // Cannot assign to 'country' because it is a read-only property 
```

Const 断言导致只读类型，这本质上不允许调用任何会使对象变异的实例方法:

```
sitepoint.contentTags.push('Pascal'); // Property 'push' does not exist on type 'readonly ["JavaScript", "HTML"...] 
```

自然，使用不可变对象来反映不同值的唯一方法是从它们创建新对象:

```
const microsoft = {
  ...sitepoint,
  name: 'Microsoft',
} as const; 
```

## 不可变函数参数

因为 const 断言只是将特定声明作为一组具有文字值的只读属性的语法糖，所以仍然有可能在函数体内改变引用:

```
interface Person {
  name: string;
  address: {
    country: string;
  };
}

const me = {
  name: 'James',
  address: {
    country: 'United Kingdom',
  },
} as const;

const isJames = (person: Person) => {
  person.name = 'Sarah';
  return person.name === 'James';
};

console.log(isJames(me)); // false;
console.log(me.name); // 'Sarah'; 
```

可以通过用`Readonly<Person>`注释`person`参数来解决这个问题，但是这只会影响对象的根级属性:

```
const isJames = (person: Readonly<Person>) => {
  person.name = 'Sarah'; // Cannot assign to 'name' because it is a read-only property.
  person.address.country = 'Australia'; // valid
  return person.name === 'James';
};

console.log(isJames(me)); // false
console.log(me.address.country); // 'Australia' 
```

没有内置的实用程序类型来处理深度不变性，但是鉴于 TypeScript 3.7 通过推迟解析引入了对递归类型的[更好的支持，我们现在可以表达一个*无限递归类型*，以在对象的整个深度上将属性表示为`readonly`:](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-7.html#more-recursive-type-aliases)

```
type Immutable<T> = {
  readonly [K in keyof T]: Immutable<T[K]>;
}; 
```

如果我们将`isJames()`的`person`参数描述为`Immutable<Person>`，TypeScript 也将禁止我们改变嵌套对象:

```
const isJames = (person: Immutable<Person>) => {
  person.name = 'Sarah'; // Cannot assign to 'name' because it is a read-only property.
  person.address.country = 'Australia'; // Cannot assign to 'country' because it is a read-only property.
  return person.name === 'James';
}; 
```

此解决方案也适用于深度嵌套的数组:

```
const hasCell = (cells: Immutable<string[][]>) => {
  cells[0][0] = 'no'; // Index signature in type 'readonly string[]' only permits reading.
}; 
```

尽管`Immutable<T>`是手动定义的类型，但是[正在讨论将 DeepReadonly < T >引入到 TypeScript](https://github.com/microsoft/TypeScript/issues/13923) 中，后者具有更精炼的语义。

## 现实世界的例子

非常流行的状态管理库[Redux](https://redux.js.org/faq/immutable-data#what-are-the-benefits-of-immutability)要求状态被不变地对待，以便确定商店是否需要更新。我们可能有这样的应用程序状态和操作界面:

```
interface Action {
  type: string;
  name: string;
  isComplete: boolean;
}

interface Todo {
  name: string;
  isComplete: boolean;
}

interface State {
  todos: Todo[];
} 
```

如果状态已经更新，我们的 reducer 应该返回一个全新的引用，我们可以键入带有`Immutable<State>`的`state`参数来禁止任何修改:

```
const reducer = (
  state: Immutable<State>,
  action: Immutable<Action>,
): Immutable<State> => {
  switch (action.type) {
    case 'ADD_TODO':
      return {
        ...state,
        todos: [
          ...state.todos,
          {
            name: action.name,
            isComplete: false,
          },
        ],
      };

    default:
      return state;
  }
}; 
```

## 不变性的额外好处

在整篇文章中，我们观察到了不可变地处理对象是如何产生更清晰、更确定的代码的。尽管如此，还有几个额外的优势值得一提。

### 使用严格比较运算符检测更改

在 JavaScript 中，我们可以使用[严格比较运算符](https://www.sitepoint.com/javascript-truthy-falsy/#strictequalitycomparisonswith) ( `===`)来确定两个对象是否共享同一个引用。考虑前面例子中的减速器:

```
const reducer = (
  state: Immutable<State>,
  action: Immutable<TodoAction>,
): Immutable<State> => {
  switch (action.type) {
    case 'ADD_TODO':
      return {
        ...state,
        // deeply merge TODOs
      };

    default:
      return state;
  }
}; 
```

因为我们只在计算出状态改变时才创建一个新的引用，所以我们可以推断出严格引用相等表示一个未改变的对象:

```
const action = {
  ...addTodoAction,
  type: 'NOOP',
};

const newState = reducer(state, action);
const hasStateChanged = state !== newState; 
```

通过严格引用相等来检测更改比深入比较两个对象树更简单，后者通常涉及递归。

### 通过引用记忆计算

作为将引用和对象表达式视为一对一关系的必然结果(即单个引用代表一组精确的属性和值)，我们可以通过引用来记忆潜在的昂贵计算。如果我们想添加一个包含斐波那契数列的前 2000 个数字的数组，我们可以使用一个[高阶函数](https://www.sitepoint.com/higher-order-functions-javascript/)和一个 [`WeakMap`](https://www.sitepoint.com/es6-collections-map-set-weakmap-weakset/#weakmap) 来可预测地缓存特定引用的运算结果:

```
const memoise = <TArg extends object, TResult>(func: Function) => {
  const results = new WeakMap<TArg, TResult>();

  return (arg: TArg) =>
    results.has(arg) ? results.get(arg) : results.set(arg, func(arg)).get(arg);
};

const sum = (numbers: number[]) => numbers.reduce((total, x) => total + x, 0);

const memoisedSum = memoise<number[], number>(sum);
const numbers = createFibSequence(0, 2000);

console.log(memoisedSum(numbers)); // Cache miss
console.log(memoisedSum(numbers)); // Cache hit 
```

## 不变性不是银弹

像所有编程范例一样，不可变性也有其缺点:

*   使用 spread 语法复制深层对象可能会很冗长，尤其是在复杂树中只更改一个原始值的时候。
*   创建新的引用将导致许多短暂的内存分配，垃圾收集必须最终处理这些内存分配；。这可能会冲击主线程，尽管现代垃圾收集器如 [Orinoco](https://v8.dev/blog/trash-talk) 通过并行化减轻了这种影响。
*   使用不可变类型和常量断言需要纪律和跨团队的共识。特定的林挺规则正在被讨论，作为自动化此类实践的一种方式，但仍是非常早期的提议。
*   许多第一方和第三方 API，如 DOM 和分析库，都是基于对象的变化而建模的。虽然特定的摘要会有所帮助，但网络上无处不在的不变性是不可能的。

## 摘要

充满突变的代码可能具有不透明的意图，并导致我们的软件行为异常。操纵现代 JavaScript 语法可以鼓励开发人员不变地操作引用类型——从现有引用创建新对象，而不是直接修改它们——并用 TypeScript 结构补充它们，以实现编译时不变性。这当然不是一个万无一失的方法，但是通过一些训练，我们可以编写非常健壮和可预测的应用程序，从长远来看，这只会使我们的工作更容易。

## 分享这篇文章*