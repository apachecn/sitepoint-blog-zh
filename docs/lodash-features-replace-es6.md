# 您可以用 ES6 替换的 10 项 Lodash 功能

> 原文：<https://www.sitepoint.com/lodash-features-replace-es6/>

**现在， [Lodash](https://lodash.com/) 是[最依赖的 npm 包](https://www.npmjs.com/browse/depended)，但是如果你使用 ES6，你可能实际上并不需要它。**

在本文中，我们将看看如何使用带有[箭头函数](https://www.sitepoint.com/javascript-arrow-functions/)和其他新的 ES6 特性的原生集合方法，来帮助我们绕开许多流行的用例。

* * *

## 1.映射、过滤、减少

这些收集方法使转换数据变得轻而易举，并且几乎得到普遍支持。我们可以将它们与箭头函数配对，以帮助我们编写 Lodash 提供的实现的简洁替代方案:

```
_.map([1, 2, 3], function(n) { return n * 3; });
// [3, 6, 9]
_.reduce([1, 2, 3], function(total, n) { return total + n; }, 0);
// 6
_.filter([1, 2, 3], function(n) { return n <= 2; });
// [1, 2]

// becomes

[1, 2, 3].map(n => n * 3);
[1, 2, 3].reduce((total, n) => total + n);
[1, 2, 3].filter(n => n <= 2); 
```

它也不止于此。如果我们使用的是现代浏览器，我们也可以使用[查找](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find)、[一些](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some)、 [every](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every) 和 [reduceRight](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduceRight) 也可以。

## 2.头和尾

[析构语法](https://www.sitepoint.com/es6-destructuring-assignment/)允许我们在没有实用函数的情况下获得列表的头部和尾部:

```
_.head([1, 2, 3]);
// 1
_.tail([1, 2, 3]);
// [2, 3]

// becomes

const [head, ...tail] = [1, 2, 3]; 
```

也可以用类似的方式获得初始元素和最后一个元素:

```
_.initial([1, 2, 3]);
// -> [1, 2]
_.last([1, 2, 3]);
// 3

// becomes

const [last, ...initial] = [1, 2, 3].reverse(); 
```

如果您发现 [reverse](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reverse) 改变了数据结构，那么您可以在调用 reverse 之前使用 spread 操作符克隆数组:

```
const xs = [1, 2, 3];
const [last, ...initial] = [...xs].reverse(); 
```

## 3.休息和传播

[rest](https://lodash.com/docs#rest) 和 [spread](https://lodash.com/docs#spread) 函数允许我们定义和调用接受可变数量参数的函数。ES6 为这两种操作引入了专用语法:

```
var say = _.rest(function(what, names) {
  var last = _.last(names);
  var initial = _.initial(names);
  var finalSeparator = (_.size(names) > 1 ? ', & ' : '');
  return what + ' ' + initial.join(', ') +
    finalSeparator + _.last(names);
});

say('hello', 'fred', 'barney', 'pebbles');
// "hello fred, barney, & pebbles"

// becomes

const say = (what, ...names) => {
  const [last, ...initial] = names.reverse();
  const finalSeparator = (names.length > 1 ? ', &' : '');
  return `${what}  ${initial.join(', ')}  ${finalSeparator}  ${last}`;
};

say('hello', 'fred', 'barney', 'pebbles');
// "hello fred, barney, & pebbles" 
```

## 4.咖喱菜肴

如果没有更高级的语言，比如[TypeScript][5]或[Flow][6]，我们就不能给我们的函数类型签名，这使得[变得相当困难。当我们接收 curried 函数时，很难知道已经提供了多少个参数，接下来我们需要提供哪些参数。使用箭头函数，我们可以显式地定义 curried 函数，使其他程序员更容易理解它们:](https://www.sitepoint.com/currying-in-functional-javascript/)

```
function add(a, b) {
  return a + b;
}
var curriedAdd = _.curry(add);
var add2 = curriedAdd(2);
add2(1);
// 3

// becomes

const add = a => b => a + b;
const add2 = add(2);
add2(1);
// 3 
```

这些显式定制的箭头函数对于调试尤其重要:

```
var lodashAdd = _.curry(function(a, b) {
  return a + b;
});
var add3 = lodashAdd(3);
console.log(add3.length)
// 0
console.log(add3);
// function (a, b) {
// /* [wrapped with _.curry & _.partial] */
//   return a + b;
// }

// becomes

const es6Add = a => b => a + b;
const add3 = es6Add(3);
console.log(add3.length);
// 1
console.log(add3);
// function b => a + b 
```

如果我们使用像 [lodash/fp](https://github.com/lodash/lodash/wiki/FP-Guide) 或 [ramda](http://ramdajs.com) 这样的函数库，我们也可以使用箭头来消除对 auto-curry 风格的需求:

```
_.map(_.prop('name'))(people);

// becomes

people.map(person => person.name); 
```

## 5.部分的

与 currying 一样，我们可以使用箭头函数使部分应用变得简单明了:

```
var greet = function(greeting, name) {
  return greeting + ' ' + name;
};

var sayHelloTo = _.partial(greet, 'hello');
sayHelloTo('fred');
// "hello fred"

// becomes

const sayHelloTo = name => greet('hello', name);
sayHelloTo('fred');
// "hello fred" 
```

也可以在 spread 运算符中使用 rest 参数来部分应用可变函数:

```
const sayHelloTo = (name, ...args) => greet('hello', name, ...args);
sayHelloTo('fred', 1, 2, 3);
// "hello fred" 
```

## 6.经营者

Lodash 附带了许多函数，这些函数将语法操作符重新实现为函数，这样就可以将它们传递给集合方法。

在大多数情况下，箭头函数使它们足够简单和简短，我们可以改为内联定义它们:

```
_.eq(3, 3);
// true
_.add(10, 1);
// 11
_.map([1, 2, 3], function(n) {
  return _.multiply(n, 10);
});
// [10, 20, 30]
_.reduce([1, 2, 3], _.add);
// 6

// becomes

3 === 3
10 + 1
[1, 2, 3].map(n => n * 10);
[1, 2, 3].reduce((total, n) => total + n); 
```

## 7.小路

Lodash 的许多函数将路径作为字符串或数组。我们可以使用箭头函数来创建更多可重用的路径:

```
var object = { 'a': [{ 'b': { 'c': 3 } }, 4] };

_.at(object, ['a[0].b.c', 'a[1]']);
// [3, 4]
_.at(['a', 'b', 'c'], 0, 2);
// ['a', 'c']

// becomes

[
  obj => obj.a[0].b.c,
  obj => obj.a[1]
].map(path => path(object));

[
  arr => arr[0],
  arr => arr[2]
].map(path => path(['a', 'b', 'c'])); 
```

因为这些路径“只是函数”，所以我们也可以组合它们:

```
const getFirstPerson = people => people[0];
const getPostCode = person => person.address.postcode;
const getFirstPostCode = people => getPostCode(getFirstPerson(people)); 
```

我们甚至可以创建接受参数的高阶路径:

```
const getFirstNPeople = n => people => people.slice(0, n);

const getFirst5People = getFirstNPeople(5);
const getFirst5PostCodes = people => getFirst5People(people).map(getPostCode); 
```

## 8.挑选

[pick](https://lodash.com/docs#pick) 实用程序允许我们从目标对象中选择我们想要的属性。我们可以使用析构和速记对象文字来获得相同的结果:

```
var object = { 'a': 1, 'b': '2', 'c': 3 };

return _.pick(object, ['a', 'c']);
// { a: 1, c: 3 }

// becomes

const { a, c } = { a: 1, b: 2, c: 3 };

return { a, c }; 
```

## 9.常数，同一性，Noop

Lodash 提供了一些实用程序来创建具有特定行为的简单函数:

```
_.constant({ 'a': 1 })();
// { a: 1 }
_.identity({ user: 'fred' });
// { user: 'fred' }
_.noop();
// undefined 
```

我们可以使用箭头来定义所有这些函数:

```
const constant = x => () => x;
const identity = x => x;
const noop = () => undefined; 
```

或者我们可以将上面的例子改写如下:

```
(() => ({ a: 1 }))();
// { a: 1 }
(x => x)({ user: 'fred' });
// { user: 'fred' }
(() => undefined)();
// undefined 
```

## 10.链接和流程

Lodash 提供了一些帮助我们编写链式语句的函数。在许多情况下，内置的集合方法返回一个可以直接链接的数组实例，但是在某些情况下，当方法改变集合时，这是不可能的。

但是，我们可以将相同的转换定义为箭头函数数组:

```
_([1, 2, 3])
 .tap(function(array) {
   // Mutate input array.
   array.pop();
 })
 .reverse()
 .value();
// [2, 1]

// becomes

const pipeline = [
  array => { array.pop(); return array; },
  array => array.reverse()
];

pipeline.reduce((xs, f) => f(xs), [1, 2, 3]); 
```

这样，我们甚至不用去想[轻击](https://lodash.com/docs#tap)和[到](https://lodash.com/docs#thru)之间的区别。将这种简化封装在一个实用函数中，可以成为一个很好的通用工具:

```
const pipe = functions => data => {
  return functions.reduce(
    (value, func) => func(value),
    data
  );
};

const pipeline = pipe([
  x => x * 2,
  x => x / 3,
  x => x > 5,
  b => !b
]);

pipeline(5);
// true
pipeline(20);
// false 
```

## 结论

Lodash 仍然是一个很棒的库，这篇文章只是提供了一个新的视角，让我们了解 JavaScript 的演进版本是如何让我们在以前依赖实用程序模块的情况下解决一些问题的。

不要忽视它。相反，下一次你想抽象的时候，想想一个简单的函数是否可以代替它！

*这篇文章由[马克·布朗](https://www.sitepoint.com/author/mbrown)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

## 分享这篇文章