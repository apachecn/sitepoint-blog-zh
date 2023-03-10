# ES6 在行动:符号及其用途

> 原文：<https://www.sitepoint.com/es6-symbols-uses/>

虽然 ES2015 引入了许多开发人员期望已久的语言功能，但还有一些新功能不太为人所知和理解，其好处也不太清楚，例如符号。

该符号是一个新的基本类型，一个保证永远不会与另一个符号冲突的唯一标记。从这个意义上来说，你可以把符号看作是一种 UUID。让我们看看符号是如何工作的，以及我们能用它们做什么。

## 创建新符号

创建新符号非常简单，只需调用[符号](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Symbol)函数。请注意，这只是一个标准函数，而不是对象构造函数。试图用`new`操作符调用它将导致一个`TypeError`。每次你调用`Symbol`函数，你都会得到一个新的完全唯一的值。

```
const foo = Symbol();
const bar = Symbol();

foo === bar
// <-- false 
```

也可以通过传递一个字符串作为第一个参数来创建带有标签的符号。标签不影响符号的值，但对调试很有用，并且在调用符号的`toString()`方法时显示。可以用同一个标签创建多个符号，但是这样做没有好处，而且可能会导致混乱。

```
let foo = Symbol('baz');
let bar = Symbol('baz');

foo === bar
// <-- false
console.log(foo);
// <-- Symbol(baz) 
```

## 我能用符号做什么？

符号可以很好地代替字符串或整数作为类/模块常量:

```
class Application {
  constructor(mode) {
    switch (mode) {
      case Application.DEV:
        // Set up app for development environment
        break;
      case Application.PROD:
        // Set up app for production environment
        break;
      case default:
        throw new Error('Invalid application mode: ' + mode);
    }
  }
}

Application.DEV = Symbol('dev');
Application.PROD = Symbol('prod');

// Example use
const app = new Application(Application.DEV); 
```

字符串和整数不是唯一的值；例如，像数字`2`或字符串`development`这样的值也可以在程序的其他地方用于不同的目的。使用符号意味着我们可以对所提供的价值更有信心。

符号的另一个有趣用途是作为对象属性键。如果您曾经使用 JavaScript 对象作为[hashmap](https://en.wikipedia.org/wiki/Hash_table)(PHP 术语中的关联数组，或者 Python 中的字典)，您将会熟悉使用括号符号获取/设置属性:

```
const data = [];

data['name'] = 'Ted Mosby';
data['nickname'] = 'Teddy Westside';
data['city'] = 'New York'; 
```

使用括号符号，我们也可以使用符号作为属性键。这样做有几个好处。首先，您可以确保基于符号的键永远不会冲突，不像字符串键，字符串键可能会与对象的现有属性或方法的键冲突。二是不会在`for … in`循环中枚举，被`Object.keys()`、`Object.getOwnPropertyNames()`、`JSON.stringify()`等函数忽略。这使它们成为序列化对象时不希望包含的属性的理想选择。

```
const user = {};
const email = Symbol();

user.name = 'Fred';
user.age = 30;
user[email] = 'fred@example.com';

Object.keys(user);
// <-- Array [ "name", "age" ]

Object.getOwnPropertyNames(user);
// <-- Array [ "name", "age" ]

JSON.stringify(user);
// <-- "{"name":"Fred","age":30}" 
```

然而，值得注意的是，使用符号作为键并不能保证隐私。提供了一些新工具来允许您访问基于符号的属性键。`Object.getOwnPropertySymbols()`返回任何基于符号的键的数组，而`Reflect.ownKeys()`将返回所有键的数组，包括符号。

```
Object.getOwnPropertySymbols(user);
// <-- Array [ Symbol() ]

Reflect.ownKeys(user)
// <-- Array [ "name", "age", Symbol() ] 
```

## 著名的符号

因为符号键属性对于 ES6 之前的代码是不可见的，所以它们非常适合向 JavaScript 的现有类型添加新功能，而不会破坏向后兼容性。所谓的“众所周知的”符号是`Symbol`函数的预定义属性，用于定制某些语言特性的行为，并用于实现迭代器等新功能。

`Symbol.iterator`是一个众所周知的符号，用于为对象分配一个特殊的方法，这允许它们被迭代:

```
const band = ['Freddy', 'Brian', 'John', 'Roger'];
const iterator = band[Symbol.iterator]();

iterator.next().value;
// <-- { value: "Freddy", done: false }
iterator.next().value;
// <-- { value: "Brian", done: false }
iterator.next().value;
// <-- { value: "John", done: false }
iterator.next().value;
// <-- { value: "Roger", done: false }
iterator.next().value;
// <-- { value: undefined, done: true } 
```

内置类型`String`、`Array`、`TypedArray`、`Map`和`Set`都有一个默认的`Symbol.iterator`方法，当这些类型中的一个实例在`for … of`循环中使用时，或者与 spread 操作符一起使用时，就会调用这个方法。浏览器也开始使用`Symbol.iterator`键来允许像`NodeList`和`HTMLCollection`这样的 DOM 结构以同样的方式迭代。

## 全球登记册

该规范还定义了一个运行时范围的符号注册表，这意味着您可以跨不同的执行上下文存储和检索符号，例如在文档和嵌入式 iframe 或服务工作器之间。

从注册表中检索给定键的符号。如果键的符号不存在，则返回一个新的符号。如您所料，对同一按键的后续调用将返回相同的符号。

`Symbol.keyFor(symbol)`允许您检索给定符号的密钥。使用注册表中不存在的符号调用该方法会返回 undefined:

```
const debbie = Symbol.for('user');
const mike   = Symbol.for('user');

debbie === mike
// <-- true

Symbol.keyFor(debbie);
// <-- "user" 
```

## 用例

有几个使用符号提供优势的用例。一种是我在本文前面提到的，当您想要向对象添加“隐藏”属性时，这些属性在对象被序列化时不会包含在内。

库作者还可以使用符号安全地用属性或方法扩充客户机对象，而不必担心覆盖现有的键(或者让它们的键被其他代码覆盖)。例如，小部件组件(如日期选择器)通常用需要存储在某个地方的各种选项和状态进行初始化。将小部件实例分配给 DOM 元素对象的属性并不理想，因为该属性可能会与另一个键冲突。使用基于符号的键巧妙地避开了这个问题，并确保您的小部件实例不会被覆盖。参见 Mozilla Hacks 的博客文章 [ES6 in Depth: Symbols](https://hacks.mozilla.org/2015/06/es6-in-depth-symbols/) 以获得对这一想法的更详细的探索。

## 浏览器支持

如果你想尝试符号，主流浏览器支持[是相当不错的](https://kangax.github.io/compat-table/es6/)。如你所见，当前版本的 Chrome、Firefox、Microsoft Edge 和 Opera 原生支持符号类型，移动设备上的 Android 5.1 和 iOS 9 也是如此。如果你需要支持老版本的浏览器，也可以使用[聚合填充](https://github.com/medikoo/es6-symbol)。

## 结论

虽然引入符号的主要原因似乎是为了方便在不破坏现有代码的情况下向语言中添加新功能，但它们确实有一些有趣的用途。对于所有开发人员来说，至少对它们有一个基本的了解，并且熟悉最常用的、众所周知的符号及其用途是值得的。

## 分享这篇文章