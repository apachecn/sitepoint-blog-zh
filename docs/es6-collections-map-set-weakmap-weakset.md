# ES6 集合:使用地图、集合、WeakMap、WeakSet

> 原文：<https://www.sitepoint.com/es6-collections-map-set-weakmap-weakset/>

**这篇文章研究了四个新的 ES6 系列及其优势。**

大多数主流编程语言都有几种类型的数据集合。Python 有列表、元组和字典。Java 有列表、集合、映射、队列。Ruby 有哈希和数组。到目前为止，JavaScript 只有数组。对象和数组是 JavaScript 的主力。ES6 引入了四种新的数据结构，它们将为语言增添力量和表现力:`Map`、`Set`、`WeakSet`和`WeakMap`。

## 搜索 JavaScript 散列表

哈希表、字典和哈希表是各种编程语言存储键/值对的几种方式，这些数据结构为快速检索进行了优化。

在 ES5 中，JavaScript 对象——只是带有键和值的属性的任意集合——可以模拟散列，但是使用对象作为散列有几个缺点。

### 缺点 ES5 中的键必须是字符串

JavaScript 对象属性键必须是字符串，这限制了它们作为不同数据类型的键/值对集合的能力。当然，您可以将其他数据类型强制转换为字符串，但是这增加了额外的工作。

### 缺点 2:对象本质上不是可迭代的

对象不是设计用来作为集合的，因此没有有效的方法来确定一个对象有多少属性。(参见例如 [Object.keys 慢](https://jsperf.com/object-keys-vs-for-in-with-closure/3))。当你循环一个对象的属性时，你也可以得到它的原型属性。您可以将`iterable`属性添加到所有对象中，但并不是所有对象都要用作集合。您可以使用`for … in`循环和`hasOwnProperty()`方法，但这只是一种变通方法。当你在一个对象的属性上循环时，这些属性不一定按照它们被插入的顺序被检索。

### 缺点 3:内置方法冲突带来的挑战

对象有内置的方法，如`constructor`、`toString`和`valueOf`。如果将其中一个作为属性添加，可能会导致冲突。您可以使用`Object.create(null)`创建一个裸对象(它不从`object.prototype`继承)，但是，同样，这只是一个变通方法。

ES6 包括新的集合数据类型，因此不再需要使用对象并忍受它们的缺点。

## 使用 ES6 地图集合

`Map`是我们要研究的第一个数据结构/集合。映射是任何类型的键和值的集合。很容易创建新的地图，添加/删除值，循环键/值，并有效地确定它们的大小。以下是重要的方法:

### 创建地图和使用常用方法

```
const map = new Map(); // Create a new Map
map.set('hobby', 'cycling'); // Sets a key value pair

const foods = { dinner: 'Curry', lunch: 'Sandwich', breakfast: 'Eggs' }; // New Object
const normalfoods = {}; // New Object

map.set(normalfoods, foods); // Sets two objects as key value pair

for (const [key, value] of map) {
  console.log(`${key} = ${value}`); // hobby = cycling  [object Object] = [object Object]
}

map.forEach((value, key) => {
  console.log(`${key} = ${value}`);
}, map); // hobby = cycling  [object Object] = [object Object]

map.clear(); // Clears key value pairs
console.log(map.size === 0); // True 
```

[在 JSBin 上运行这个例子](https://SitePointEditors.jsbin.com/buwecaz/edit?js,console)

## 使用集合集合

集合是不包含重复值的有序值列表。集合不是像数组那样被索引，而是使用键来访问。set 已经存在于 [Java](https://docs.oracle.com/javase/7/docs/api/java/util/Set.html) 、 [Ruby](http://ruby-doc.org/stdlib-2.3.0/libdoc/set/rdoc/Set.html) 、 [Python](https://docs.python.org/2/library/sets.html) 以及其他许多语言中。ES6 集合和其他语言中的集合的一个区别是顺序在 ES6 中很重要(在许多其他语言中不是这样)。以下是重要的 Set 方法:

```
const planetsOrderFromSun = new Set();
planetsOrderFromSun.add('Mercury');
planetsOrderFromSun.add('Venus').add('Earth').add('Mars'); // Chainable Method
console.log(planetsOrderFromSun.has('Earth')); // True

planetsOrderFromSun.delete('Mars');
console.log(planetsOrderFromSun.has('Mars')); // False

for (const x of planetsOrderFromSun) {
  console.log(x); // Same order in as out - Mercury Venus Earth
}
console.log(planetsOrderFromSun.size); // 3

planetsOrderFromSun.add('Venus'); // Trying to add a duplicate
console.log(planetsOrderFromSun.size); // Still 3, Did not add the duplicate

planetsOrderFromSun.clear();
console.log(planetsOrderFromSun.size); // 0 
```

[在 JSBin 上运行这个例子](https://jsbin.com/pepobiz/1/edit?js,console)

## 弱收集、内存和垃圾收集

JavaScript 垃圾收集是一种内存管理形式，不再被引用的对象会被自动删除，它们的资源会被回收。

`Map`和`Set`对对象的引用被强持有，不允许垃圾收集。如果地图/集合引用了不再需要的大型对象，比如已经从 DOM 中删除的 DOM 元素，那么这样做的代价会很高。

为了解决这个问题，ES6 还引入了两个新的弱集合，分别叫做`WeakMap`和`WeakSet`。这些 ES6 集合是“弱”的，因为它们允许不再需要从内存中清除的对象。

## WeakMap

WeakMap 是我们讨论的第三个新 ES6 系列。`WeakMaps`与普通的`Maps`相似，尽管方法更少，并且在垃圾收集方面存在上述差异。

```
const aboutAuthor = new WeakMap(); // Create New WeakMap
const currentAge = {}; // key must be an object
const currentCity = {}; // keys must be an object

aboutAuthor.set(currentAge, 30); // Set Key Values
aboutAuthor.set(currentCity, 'Denver'); // Key Values can be of different data types

console.log(aboutAuthor.has(currentCity)); // Test if WeakMap has a key

aboutAuthor.delete(currentAge); // Delete a key 
```

[在 JSBin 上运行这个例子](https://jsbin.com/yejoxig/edit?js,console)

### 用例

WeakMaps [有几个流行的用例](http://stackoverflow.com/questions/29413222/what-are-the-actual-uses-of-es6-weakmap)。它们可以用来保持对象私有数据的私有性，也可以用来跟踪 DOM 节点/对象。

### 私有数据用例

下面的例子来自 JavaScript 专家 Nicholas C. Zakas :

```
var Person = (function() {
  var privateData = new WeakMap();

  function Person(name) {
    privateData.set(this, { name: name });
  }

  Person.prototype.getName = function() {
    return privateData.get(this).name;
  };

  return Person;
}()); 
```

在这里使用`WeakMap`简化了保持对象数据私有的过程。可以引用`Person`对象，但是如果没有特定的`Person`实例，就不允许访问`privateDataWeakMap`。

### DOM 节点用例

谷歌聚合物项目在一段名为 PositionWalker 的代码中使用了`WeakMaps`。

> PositionWalker 跟踪 DOM 子树中的位置，作为当前节点和该节点中的偏移量。

[WeakMap 用于](https://github.com/Polymer/designer/blob/ae5df5d85a008dfc5bedba4f3cfc1f2ac656b011/src/text/PositionWalker.js#L65)跟踪 DOM 节点的编辑、删除和更改:

```
_makeClone() {
  this._containerClone = this.container.cloneNode(true);
  this._cloneToNodes = new WeakMap();
  this._nodesToClones = new WeakMap();

  ...

  let n = this.container;
  let c = this._containerClone;

  // find the currentNode's clone
  while (n !== null) {
    if (n === this.currentNode) {
    this._currentNodeClone = c;
    }
    this._cloneToNodes.set(c, n);
    this._nodesToClones.set(n, c);

    n = iterator.nextNode();
    c = cloneIterator.nextNode();
  }
} 
```

## WeakSet

是集合集合，当不再需要它们引用的对象时，它们的元素可以被垃圾收集。`WeakSets`不允许迭代。[他们的用例相当有限](https://esdiscuss.org/topic/actual-weakset-use-cases)(至少目前如此)。大多数早期采用者说`WeakSets`可以用来标记物体而不需要改变它们。[ES6-Features.org](http://es6-features.org/)有一个[的例子，从 WeakSet](http://es6-features.org/#WeakLinkDataStructures) 中添加和删除元素，以便跟踪对象是否已经被标记:

```
let isMarked     = new WeakSet()
let attachedData = new WeakMap()

export class Node {
    constructor (id)   { this.id = id                  }
    mark        ()     { isMarked.add(this)            }
    unmark      ()     { isMarked.delete(this)         }
    marked      ()     { return isMarked.has(this)     }
    set data    (data) { attachedData.set(this, data)  }
    get data    ()     { return attachedData.get(this) }
}

let foo = new Node("foo")

JSON.stringify(foo) === '{"id":"foo"}'
foo.mark()
foo.data = "bar"
foo.data === "bar"
JSON.stringify(foo) === '{"id":"foo"}'

isMarked.has(foo)     === true
attachedData.has(foo) === true
foo = null  /* remove only reference to foo */
attachedData.has(foo) === false
isMarked.has(foo)     === false 
```

## 映射所有事物？记录与 ES6 集合

映射和集合是漂亮的新 ES6 键/值对集合。也就是说，JavaScript 对象在许多情况下仍然可以用作集合。除非情况需要，否则不需要切换到新的 ES6 系列。

[MDN 有一个很好的问题列表](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)来确定何时使用对象或键控集合:

*   键通常直到运行时才知道吗，需要动态查找吗？
*   是否所有的值都具有相同的类型，并且可以互换使用？
*   需要不是字符串的键吗？
*   键值对经常被添加或删除吗？
*   你有任意(容易改变)数量的键值对吗？
*   集合是迭代的吗？

## 新的 ES6 集合产生了更有用的 JavaScript

JavaScript 集合以前非常有限，但是 ES6 弥补了这一点。这些新的 ES6 集合将为该语言增加功能和灵活性，并简化采用它们的 JavaScript 开发人员的任务。

* * *

*本文是微软技术传道者和 [DevelopIntelligence](http://www.developintelligence.com/) 关于实用 JavaScript 学习、开源项目和互操作性最佳实践的 web 开发系列的一部分，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?wt.mc_id=DX_838519) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?wt.mc_id=DX_838519)。DevelopIntelligence 通过他们的前端博客和课程网站 [appendTo](http://appendto.com/) 提供 [JavaScript 培训](http://appendto.com/courses/javascript/)和 [React 培训](http://appendto.com/courses/react-training/)课程。*

*我们鼓励您使用[dev.microsoftedge.com](https://dev.windows.com/en-us/?wt.mc_id=DX_838519)上的免费工具，包括 [EdgeHTML 问题跟踪器](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/?wt.mc_id=DX_838519)，在这里您可以报告或搜索 EdgeHTML 问题，如网站渲染或标准合规性问题，来测试跨浏览器和设备，包括 Windows 10 的默认浏览器 Microsoft Edge。此外，[访问 Edge 博客](https://blogs.windows.com/msedgedev/?wt.mc_id=DX_838519)了解微软开发人员和专家的最新消息。*

## 分享这篇文章