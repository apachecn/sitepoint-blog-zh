# 面向对象的 JavaScript:深入探究 ES6 类

> 原文：<https://www.sitepoint.com/object-oriented-javascript-deep-dive-es6-classes/>

通常我们需要在程序中表达一个想法或概念——可能是汽车引擎、计算机文件、路由器或温度读数。在代码中直接表示这些概念分为两部分:表示状态的数据和表示行为的函数。ES6 类为我们提供了一个方便的语法来定义代表我们概念的对象的状态和行为。

ES6 类通过保证调用初始化函数使我们的代码更加安全，并且它们使定义一组固定的函数来操作数据和维护有效状态变得更加容易。如果你可以把某样东西想象成一个独立的实体，那么很可能你应该在你的程序中定义一个类来代表这个“东西”。

考虑这个非类代码。你能发现多少错误？你会如何修理它们？

```
// set today to December 24
const today = {
  month: 24,
  day: 12,
};

const tomorrow = {
  year: today.year,
  month: today.month,
  day: today.day + 1,
};

const dayAfterTomorrow = {
  year: tomorrow.year,
  month: tomorrow.month,
  day: tomorrow.day + 1 <= 31 ? tomorrow.day + 1 : 1,
}; 
```

日期`today`无效:没有第 24 个月。另外，`today`没有完全初始化:它缺少年份。如果有一个不能忘记的初始化函数就更好了。还要注意，当添加一天时，如果超过 31 天，我们在一个地方检查，但在另一个地方错过了检查。如果我们只通过一组小而固定的函数与数据交互，并且每个函数都保持有效状态，那就更好了。

下面是使用类的正确版本。

```
class SimpleDate {
  constructor(year, month, day) {
    // Check that (year, month, day) is a valid date
    // ...

    // If it is, use it to initialize "this" date
    this._year = year;
    this._month = month;
    this._day = day;
  }

  addDays(nDays) {
    // Increase "this" date by n days
    // ...
  }

  getDay() {
    return this._day;
  }
}

// "today" is guaranteed to be valid and fully initialized
const today = new SimpleDate(2000, 2, 28);

// Manipulating data only through a fixed set of functions ensures we maintain valid state
today.addDays(1); 
```

**JARGON TIP:**

*   当一个函数与一个类或对象相关联时，我们称之为**方法**。
*   当从一个类中创建一个对象时，该对象被称为该类的一个**实例**。

## 构造器

`constructor`方法比较特殊，它解决了第一个问题。它的工作是初始化一个实例到一个有效的状态，它将被自动调用，所以我们不能忘记初始化我们的对象。

## 保持数据私密

我们试图设计我们的类，以保证它们的状态是有效的。我们提供了一个只创建有效值的构造函数，我们设计的方法也总是只留下有效值。但是只要我们让每个人都可以访问我们班级的数据，就会有人把它搞乱。我们通过保持数据不可访问来防止这种情况，除非通过我们提供的函数。

**行话提示:**保持数据私有以保护数据被称为**封装**。

### 有惯例的隐私

不幸的是，JavaScript 中不存在私有对象属性。我们必须伪造它们。最常见的方法是遵循一个简单的约定:如果属性名以下划线为前缀(或者，不太常见的是，以下划线为后缀)，那么它应该被视为非公共的。我们在前面的代码示例中使用了这种方法。一般来说，这个简单的约定是可行的，但是从技术上讲，每个人都可以访问这些数据，所以我们必须依靠自己的原则来做正确的事情。

### 使用特许方法的隐私

伪造私有对象属性的下一个最常见的方法是在构造函数中使用普通变量，并在闭包中捕获它们。这个技巧给了我们外部无法访问的真正的私有数据。但是要使它工作，我们的类的方法本身需要在构造函数中定义并附加到实例上:

```
class SimpleDate {
  constructor(year, month, day) {
    // Check that (year, month, day) is a valid date
    // ...

    // If it is, use it to initialize "this" date's ordinary variables
    let _year = year;
    let _month = month;
    let _day = day;

    // Methods defined in the constructor capture variables in a closure
    this.addDays = function(nDays) {
      // Increase "this" date by n days
      // ...
    }

    this.getDay = function() {
      return _day;
    }
  }
} 
```

### 符号隐私

从 ES6 开始，符号是 JavaScript 的一个新特性，它给了我们另一种伪造私有对象属性的方法。代替下划线属性名，我们可以使用唯一的符号对象键，我们的类可以在闭包中捕获这些键。但是有漏洞。JavaScript 的另一个新特性是`Object.getOwnPropertySymbols`，它允许外界访问我们试图保密的符号键:

```
const SimpleDate = (function() {
  const _yearKey = Symbol();
  const _monthKey = Symbol();
  const _dayKey = Symbol();

  class SimpleDate {
    constructor(year, month, day) {
      // Check that (year, month, day) is a valid date
      // ...

      // If it is, use it to initialize "this" date
      this[_yearKey] = year;
      this[_monthKey] = month;
      this[_dayKey] = day;
     }

    addDays(nDays) {
      // Increase "this" date by n days
      // ...
    }

    getDay() {
      return this[_dayKey];
    }
  }

  return SimpleDate;
}()); 
```

### 弱地图隐私

[弱映射](https://www.sitepoint.com/preparing-ecmascript-6-map-weakmap/)也是 JavaScript 的一个新特性。我们可以使用实例作为键，将私有对象属性存储在键/值对中，并且我们的类可以在闭包中捕获这些键/值映射:

```
const SimpleDate = (function() {
  const _years = new WeakMap();
  const _months = new WeakMap();
  const _days = new WeakMap();

  class SimpleDate {
    constructor(year, month, day) {
      // Check that (year, month, day) is a valid date
      // ...

      // If it is, use it to initialize "this" date
      _years.set(this, year);
      _months.set(this, month);
      _days.set(this, day);
    }

    addDays(nDays) {
      // Increase "this" date by n days
      // ...
    }

    getDay() {
      return _days.get(this);
    }
  }

  return SimpleDate;
}()); 
```

### 其他访问修饰符

除了“private”之外，在其他语言中还有其他级别的可见性，例如“protected”、“internal”、“package private”或“friend”。JavaScript 仍然没有给我们一种方法来加强其他级别的可见性。如果你需要它们，你将不得不依靠惯例和自律。

## 引用当前对象

再看`getDay()`。它没有指定任何参数，那么它如何知道它被调用的对象呢？当一个函数作为使用`object.function`符号的方法被调用时，有一个隐式参数用来标识对象，这个隐式参数被分配给一个名为`this`的隐式参数。举例来说，下面是我们如何显式地而不是隐式地发送对象参数:

```
// Get a reference to the "getDay" function
const getDay = SimpleDate.prototype.getDay;

getDay.call(today); // "this" will be "today"
getDay.call(tomorrow); // "this" will be "tomorrow"

tomorrow.getDay(); // same as last line, but "tomorrow" is passed implicitly 
```

## 静态属性和方法

我们可以选择定义属于该类的数据和函数，但不属于该类的任何实例。我们分别称这些为静态属性和静态方法。每个实例只有一个静态属性的副本，而不是一个新副本:

```
class SimpleDate {
  static setDefaultDate(year, month, day) {
    // A static property can be referred to without mentioning an instance
    // Instead, it's defined on the class
    SimpleDate._defaultDate = new SimpleDate(year, month, day);
  }

  constructor(year, month, day) {
    // If constructing without arguments,
    // then initialize "this" date by copying the static default date
    if (arguments.length === 0) {
      this._year = SimpleDate._defaultDate._year;
      this._month = SimpleDate._defaultDate._month;
      this._day = SimpleDate._defaultDate._day;

      return;
    }

    // Check that (year, month, day) is a valid date
    // ...

    // If it is, use it to initialize "this" date
    this._year = year;
    this._month = month;
    this._day = day;
  }

  addDays(nDays) {
    // Increase "this" date by n days
    // ...
  }

  getDay() {
    return this._day;
  }
}

SimpleDate.setDefaultDate(1970, 1, 1);
const defaultDate = new SimpleDate(); 
```

## 子类

我们经常会发现我们的类之间的共性——我们想要合并的重复代码。子类让我们将另一个类的状态和行为合并到我们自己的类中。这个过程通常被称为**继承**，我们的子类被称为从父类“继承”，也被称为**超类**。继承可以避免重复，并简化需要与另一个类相同的数据和函数的类的实现。继承也允许我们替换子类，仅仅依靠一个公共超类提供的[接口](http://stackoverflow.com/q/2866987/1136887)。

## 继承以避免重复

考虑这个非继承代码:

```
class Employee {
  constructor(firstName, familyName) {
    this._firstName = firstName;
    this._familyName = familyName;
  }

  getFullName() {
    return `${this._firstName}  ${this._familyName}`;
  }
}

class Manager {
  constructor(firstName, familyName) {
    this._firstName = firstName;
    this._familyName = familyName;
    this._managedEmployees = [];
  }

  getFullName() {
    return `${this._firstName}  ${this._familyName}`;
  }

  addEmployee(employee) {
    this._managedEmployees.push(employee);
  }
} 
```

数据属性`_firstName`和`_familyName`，以及方法`getFullName`，在我们的类之间重复。我们可以通过让我们的`Manager`类继承`Employee`类来消除这种重复。当我们这样做时，`Employee`类的状态和行为——它的数据和函数——将被合并到我们的`Manager`类中。

这是一个使用继承的版本。注意[超级](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/super)的使用:

```
// Manager still works same as before but without repeated code
class Manager extends Employee {
  constructor(firstName, familyName) {
    super(firstName, familyName);
    this._managedEmployees = [];
  }

  addEmployee(employee) {
    this._managedEmployees.push(employee);
  }
} 
```

### 是-A 并且像-A 一样工作

有一些设计原则可以帮助你决定什么时候继承是合适的。继承应该总是模拟一种“是-是”和“类似-是”的关系。也就是说，经理“是一个”,并且“像一个”特定类型的雇员“工作”,这样我们在超类实例上操作的任何地方，我们都应该能够在子类实例中进行替换，并且一切都应该仍然正常工作。违反和坚持这一原则之间的区别有时是微妙的。微妙违反的一个经典例子是一个`Rectangle`超类和一个`Square`子类:

```
class Rectangle {
  set width(w) {
    this._width = w;
  }

  get width() {
    return this._width;
  }

  set height(h) {
    this._height = h;
  }

  get height() {
    return this._height;
  }
}

// A function that operates on an instance of Rectangle
function f(rectangle) {
  rectangle.width = 5;
  rectangle.height = 4;

  // Verify expected result
  if (rectangle.width * rectangle.height !== 20) {
    throw new Error("Expected the rectangle's area (width * height) to be 20");
  }
}

// A square IS-A rectangle... right?
class Square extends Rectangle {
  set width(w) {
    super.width = w;

    // Maintain square-ness
    super.height = w;
  }

  set height(h) {
    super.height = h;

    // Maintain square-ness
    super.width = h;
  }
}

// But can a rectangle be substituted by a square?
f(new Square()); // error 
```

一个正方形在数学上可能是一个矩形，但是一个正方形在行为上不像矩形那样工作。

超类实例的任何使用都应该被子类实例替换的规则被称为 [Liskov 替换原则](https://en.wikipedia.org/wiki/Liskov_substitution_principle)，它是面向对象类设计的重要部分。

### 小心过度使用

在任何地方都很容易找到通用性，拥有一个提供完整功能的类的前景是很诱人的，即使对于有经验的开发人员来说也是如此。但是继承也有缺点。回想一下，我们只通过一组小而固定的函数来操作数据，从而确保有效状态。但是当我们继承时，我们增加了可以直接操作数据的函数列表，这些额外的函数也负责维护有效状态。如果有太多的函数可以直接操作数据，那么数据就会变得和全局变量一样糟糕。过多的继承会创建单一的类，这会削弱封装性，更难纠正，也更难重用。相反，最好设计只体现一个概念的最小类。

让我们再来看看代码重复的问题。没有遗传我们能解决吗？另一种方法是通过引用来连接对象，以表示部分-整体关系。我们把这个叫做**作文**。

这是一种使用组合而非继承的经理-员工关系:

```
class Employee {
  constructor(firstName, familyName) {
    this._firstName = firstName;
    this._familyName = familyName;
  }

  getFullName() {
    return `${this._firstName}  ${this._familyName}`;
  }
}

class Group {
  constructor(manager /* : Employee */ ) {
    this._manager = manager;
    this._managedEmployees = [];
  }

  addEmployee(employee) {
    this._managedEmployees.push(employee);
  }
} 
```

在这里，经理不是一个独立的阶层。相反，管理器是一个普通的`Employee`实例，一个`Group`实例持有对它的引用。如果继承模拟的是“是-是”关系，那么组合模拟的是“有-是”关系。也就是说，一个组“有一个”管理者。

如果继承或组合可以合理地表达我们的程序概念和关系，那么更喜欢组合。

## 继承以替换子类

继承还允许不同的子类通过公共超类提供的接口互换使用。一个期望一个超类实例作为参数的函数也可以被传递一个子类实例，而该函数不必知道任何子类。替换具有公共超类的类通常被称为**多态性**:

```
// This will be our common superclass
class Cache {
  get(key, defaultValue) {
    const value = this._doGet(key);
    if (value === undefined || value === null) {
      return defaultValue;
    }

    return value;
  }

  set(key, value) {
    if (key === undefined || key === null) {
      throw new Error('Invalid argument');
    }

    this._doSet(key, value);
  }

  // Must be overridden
  // _doGet()
  // _doSet()
}

// Subclasses define no new public methods
// The public interface is defined entirely in the superclass
class ArrayCache extends Cache {
  _doGet() {
    // ...
  }

  _doSet() {
    // ...
  }
}

class LocalStorageCache extends Cache {
  _doGet() {
    // ...
  }

  _doSet() {
    // ...
  }
}

// Functions can polymorphically operate on any cache by interacting through the superclass interface
function compute(cache) {
  const cached = cache.get('result');
  if (!cached) {
    const result = // ...
    cache.set('result', result);
  }

  // ...
}

compute(new ArrayCache()); // use array cache through superclass interface
compute(new LocalStorageCache()); // use local storage cache through superclass interface 
```

## 不仅仅是糖

JavaScript 的类语法经常被认为是语法糖，在很多方面确实如此，但也有真正的区别——我们可以用 ES6 类做在 ES5 中做不到的事情。

### 静态属性是继承的

ES5 没有让我们在构造函数之间创建真正的继承。`Object.create`可以创建普通对象，但不能创建函数对象。我们通过手工复制静态属性来伪造它们的继承。现在有了 ES6 类，我们在子类构造函数和超类构造函数之间获得了一个真正的原型链接:

```
// ES5
function B() {}
B.f = function () {};

function D() {}
D.prototype = Object.create(B.prototype);

D.f(); // error 
```

```
// ES6
class B {
  static f() {}
}

class D extends B {}

D.f(); // ok 
```

### 内置构造函数可以被子类化

一些对象是“奇异的”,其行为不像普通对象。例如，数组将它们的`length`属性调整为大于最大整数索引。在 ES5 中，当我们试图子类化`Array`时，`new`操作符会为我们的子类分配一个普通对象，而不是我们超类的外来对象:

```
// ES5
function D() {
  Array.apply(this, arguments);
}
D.prototype = Object.create(Array.prototype);

var d = new D();
d[0] = 42;

d.length; // 0 - bad, no array exotic behavior 
```

ES6 类通过改变对象的分配时间和分配者来解决这个问题。在 ES5 中，对象是在调用子类构造函数之前分配的，子类会将该对象传递给超类构造函数。现在有了 ES6 类，对象在调用*超类*构造函数之前就被分配了，超类使那个对象对子类构造函数可用。这让`Array`分配一个外来对象，即使我们在子类上调用`new`。

```
// ES6
class D extends Array {}

let d = new D();
d[0] = 42;

d.length; // 1 - good, array exotic behavior 
```

### 多方面的

还有一个小分类的其他，可能不太重要的差异。类构造函数不能被函数调用。这可以防止忘记用`new`调用构造函数。此外，类构造函数的`prototype`属性不能被重新分配。这可能有助于 JavaScript 引擎优化类对象。最后，类方法没有`prototype`属性。这可以通过消除不必要的对象来节省内存。

## 以富有想象力的方式使用新功能

这里和其他 SitePoint 文章中描述的许多特性对 JavaScript 来说都是新的，社区现在正在尝试以新的和富有想象力的方式使用这些特性。

### 代理多重继承

其中一个实验使用了[代理](https://www.sitepoint.com/es6-proxies/)，这是 JavaScript 实现多重继承的新特性。JavaScript 的原型链只允许单一继承。对象只能委托给一个其他对象。代理为我们提供了一种将属性访问委托给多个其他对象的方法:

```
const transmitter = {
  transmit() {}
};

const receiver = {
  receive() {}
};

// Create a proxy object that intercepts property accesses and forwards to each parent,
// returning the first defined value it finds
const inheritsFromMultiple = new Proxy([transmitter, receiver], {
  get: function(proxyTarget, propertyKey) {
    const foundParent = proxyTarget.find(parent => parent[propertyKey] !== undefined);
    return foundParent && foundParent[propertyKey];
  }
});

inheritsFromMultiple.transmit(); // works
inheritsFromMultiple.receive(); // works 
```

我们能把它扩展到 ES6 类吗？一个类的`prototype`可以是一个代理，它将属性访问转发给多个其他原型。JavaScript 社区正在致力于此。你能搞清楚吗？加入讨论，分享你的想法。

## 类工厂的多重继承

JavaScript 社区一直在尝试的另一种方法是按需生成扩展变量超类的类。每个类仍然只有一个父类，但是我们可以用有趣的方式链接这些父类:

```
function makeTransmitterClass(Superclass = Object) {
  return class Transmitter extends Superclass {
    transmit() {}
  };
}

function makeReceiverClass(Superclass = Object) {
  return class Receiver extends Superclass
    receive() {}
  };
}

class InheritsFromMultiple extends makeTransmitterClass(makeReceiverClass()) {}

const inheritsFromMultiple = new InheritsFromMultiple();

inheritsFromMultiple.transmit(); // works
inheritsFromMultiple.receive(); // works 
```

有没有其他富有想象力的方法来使用这些功能？现在是时候在 JavaScript 世界留下你的足迹了。

## 结论

如下图所示，[对类的支持相当好](https://kangax.github.io/compat-table/es6/#test-class)。

[我能用 es6 级吗？](http://caniuse.com/#feat=es6-class)caniuse.com 主要浏览器支持 es6 级功能的数据。