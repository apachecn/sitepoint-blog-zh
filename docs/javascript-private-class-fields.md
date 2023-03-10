# JavaScript 的新私有类字段，以及如何使用它们

> 原文：<https://www.sitepoint.com/javascript-private-class-fields/>

ES6 为 JavaScript 引入了类，但是对于复杂的应用程序来说，它们可能过于简单。类字段(也称为*类属性*)旨在提供更简单的带有私有和静态成员的构造函数。该提案目前是一个 [TC39 第三阶段:候选](https://github.com/tc39/proposal-class-fields)，并有可能被添加到 ES2019 (ES10)。目前 Node.js 12、Chrome 74 和 Babel 都支持私有字段。

在我们研究如何实现类字段之前，快速回顾一下 ES6 类是很有用的。

*本文更新于 2020 年。要获得更深入的 JavaScript 知识，请阅读我们的书， [JavaScript:新手到忍者，第二版](https://www.sitepoint.com/premium/books/javascript-novice-to-ninja-2nd-edition?utm_source=blog&utm_medium=articles)。*

## ES6 课程基础

JavaScript 的面向对象继承模型可能会让来自 C++、C#、Java 和 PHP 等语言的开发人员感到困惑。为此，ES6 引入了*类*。它们主要是语法上的糖，但提供了更熟悉的面向对象编程概念。

一个类是一个*对象模板*，它定义了该类型对象的行为方式。下面的`Animal`类定义了通用动物(类通常用首字母大写表示，以区别于对象和其他类型):

```
class Animal {

  constructor(name = 'anonymous', legs = 4, noise = 'nothing') {

    this.type = 'animal';
    this.name = name;
    this.legs = legs;
    this.noise = noise;

  }

  speak() {
    console.log(`${this.name} says "${this.noise}"`);
  }

  walk() {
    console.log(`${this.name} walks on ${this.legs} legs`);
  }

} 
```

类声明总是在严格模式下执行。没必要加`'use strict'`。

当创建一个**动物**类型的对象时，运行**构造器**方法。它通常设置初始属性并处理其他初始化。`speak()`和`walk()`是增加更多功能的实例方法。

现在可以用关键字`new`从这个类创建一个对象:

```
let rex = new Animal('Rex', 4, 'woof');
rex.speak();          // Rex says "woof"
rex.noise = 'growl';
rex.speak();          // Rex says "growl" 
```

## Getters 和 Setters

*设置器*是仅用于定义值的特殊方法。类似地，*getter*是仅用于返回值的特殊方法。例如:

```
class Animal {

  constructor(name = 'anonymous', legs = 4, noise = 'nothing') {

    this.type = 'animal';
    this.name = name;
    this.legs = legs;
    this.noise = noise;

  }

  speak() {
    console.log(`${this.name} says "${this.noise}"`);
  }

  walk() {
    console.log(`${this.name} walks on ${this.legs} legs`);
  }

  // setter
  set eats(food) {
    this.food = food;
  }

  // getter
  get dinner() {
    return `${this.name} eats ${this.food || 'nothing'} for dinner.`;
  }

}

let rex = new Animal('Rex', 4, 'woof');
rex.eats = 'anything';
console.log( rex.dinner );  // Rex eats anything for dinner. 
```

## 子或子类

使用一个类作为另一个类的基础通常是可行的。使用`extends`关键字，`Human`类可以继承`Animal`类的所有属性和方法。可以根据需要添加、删除或更改属性和方法，从而使人类对象的创建变得更加容易和更具可读性:

```
class Human extends Animal {

  constructor(name) {

    // call the Animal constructor
    super(name, 2, 'nothing of interest');
    this.type = 'human';

  }

  // override Animal.speak
  speak(to) {

    super.speak();
    if (to) console.log(`to ${to}`);

  }

} 
```

`super`指的是父类，所以它通常是在`constructor`中进行的第一次调用。在这个例子中，人类的`speak()`方法覆盖了在`Animal`中定义的方法。

现在可以创建`Human`的对象实例:

```
let don = new Human('Don');
don.speak('anyone');        // Don says "nothing of interest" to anyone

don.eats = 'burgers';
console.log( don.dinner );  // Don eats burgers for dinner. 
```

## 静态方法和属性

用关键字`static`定义一个方法允许在一个类上调用它，而不需要创建一个对象实例。考虑一下`Math.PI`常量:在访问`PI`属性之前，不需要创建一个`Math`对象。

ES6 不像其他语言那样支持静态属性，但是可以向类定义本身添加属性。例如，`Human`类可以用来记录已经创建了多少个人类对象:

```
class Human extends Animal {

  constructor(name) {

    // call the Animal constructor
    super(name, 2, 'nothing of interest');
    this.type = 'human';

    // update count of Human objects
    Human.count++;

  }

  // override Animal.speak
  speak(to) {

    super.speak();
    if (to) console.log(`to ${to}`);

  }

  // return number of human objects
  static get COUNT() {
    return Human.count;
  }

}

// static property of the class itself - not its objects
Human.count = 0; 
```

该类的静态`COUNT` getter 相应地返回人类的数量:

```
console.log(`Humans defined: ${Human.COUNT}`); // Humans defined: 0

let don = new Human('Don');

console.log(`Humans defined: ${Human.COUNT}`); // Humans defined: 1

let kim = new Human('Kim');

console.log(`Humans defined: ${Human.COUNT}`); // Humans defined: 2 
```

## ES2019 类字段*(新)*

新的类字段实现允许在任何构造函数之外的类的顶部初始化公共属性:

```
class MyClass {

  a = 1;
  b = 2;
  c = 3;

} 
```

这相当于:

```
class MyClass {

  constructor() {
    this.a = 1;
    this.b = 2;
    this.c = 3;
  }

} 
```

如果你仍然需要一个构造函数，初始化器将在它运行之前被执行。

### 静态类字段

在上面的[例子中，静态属性在被定义后被添加到类定义对象中。对于类字段，这是不必要的:](#static-methods-and-properties)

```
class MyClass {

  x = 1;
  y = 2;
  static z = 3;

}

console.log( MyClass.z ); // 3 
```

这相当于:

```
class MyClass {

  constructor() {
    this.x = 1;
    this.y = 2;
  }

}

MyClass.z = 3;

console.log( MyClass.z ); // 3 
```

### 私有类字段

默认情况下，ES6 类中的所有属性都是公共的，可以在类外检查或修改*。在上面的`Animal`示例中，如果不调用`eats`设置器，就无法阻止`food`属性被更改:*

```
class Animal {

  constructor(name = 'anonymous', legs = 4, noise = 'nothing') {

    this.type = 'animal';
    this.name = name;
    this.legs = legs;
    this.noise = noise;

  }

  set eats(food) {
    this.food = food;
  }

  get dinner() {
    return `${this.name} eats ${this.food || 'nothing'} for dinner.`;
  }

}

let rex = new Animal('Rex', 4, 'woof');
rex.eats = 'anything';      // standard setter
rex.food = 'tofu';          // bypass the eats setter altogether
console.log( rex.dinner );  // Rex eats tofu for dinner. 
```

其他语言通常允许声明`private`属性。这在 ES6 中是不可能的，所以开发人员经常使用下划线约定(`_propertyName`)、[闭包、符号或弱映射](https://curiosity-driven.org/private-properties-in-javascript)来解决这个问题。下划线向开发人员提供了提示，但是没有什么可以阻止他们访问该属性。

在 ES2019 中，私有类字段使用哈希`#`前缀定义:

```
class MyClass {

  a = 1;          // .a is public
  #b = 2;         // .#b is private
  static #c = 3;  // .#c is private and static

  incB() {
    this.#b++;
  }

}

let m = new MyClass();

m.incB(); // runs OK
m.#b = 0; // error - private property cannot be modified outside class 
```

注意，没有办法定义私有方法、getters 或 setters。TC39 第三阶段:提议草案建议在名字上使用散列前缀`#`，并且已经在 Babel 中实现。例如:

```
class MyClass {

  // private property
  #x = 0;

  // private method (can only be called within the class)
  #incX() {
    this.#x++;
  }

  // private setter (can only be used within the class)
  set #setX(x) {
    this.#x = x;
  }

  // private getter (can only be used within the class)
  get #getX() {
    return this.$x;
  }

} 
```

## 直接的好处:更干净的反应代码！

React 组件通常有绑定到 DOM 事件的方法。为了确保`this`解析到组件，有必要相应地`bind`每个方法。例如:

```
class App extends Component {

  constructor() {

    super();

    this.state = { count: 0 };

    // bind all methods
    this.incCount = this.incCount.bind(this);
  }

  incCount() {
    this.setState(ps => { count: ps.count + 1 })
  }

  render() {

    return (
      <div>
        <p>{ this.state.count }</p>
        <button onClick={this.incCount}>add one</button>
      </div>
    );

  }
} 
```

当`incCount`被定义为 ES2019 类字段时，可以使用自动绑定到定义对象的 [ES6 `=>`粗箭头](https://www.sitepoint.com/javascript-arrow-functions/)将其指定为函数。不再需要添加`bind`声明:

```
class App extends Component {

  state = { count: 0 };

  incCount = () => {
    this.setState(ps => { count: ps.count + 1 })
  };

  render() {

    return (
      <div>
        <p>{ this.state.count }</p>
        <button onClick={this.incCount}>add one</button>
      </div>
    );

  }
} 
```

## 类字段:一种改进？

ES6 类定义过于简单。ES2019 类字段需要更少的代码，有助于可读性，并支持一些有趣的面向对象的编程可能性。

使用`#`来表示隐私受到了一些批评，主要是因为它很丑，感觉像是黑客。大多数语言都实现了一个`private`关键字，所以试图在类外使用该成员将被编译器拒绝。

JavaScript 被解释。考虑以下代码:

```
class MyClass {
  private secret = 123;
}

const myObject = new MyClass();
myObject.secret = 'one-two-three'; 
```

这可能会在最后一行抛出运行时错误，但是对于仅仅试图设置属性来说，这是一个严重的后果。JavaScript 是有意宽容的，ES5 允许对任何对象进行属性修改。

尽管笨拙，`#`符号在类定义之外是无效的。试图访问`myObject.#secret`可能会引发语法错误。

争论将会继续，但是，不管喜欢与否，类字段已经在几个 JavaScript 引擎中被采用。他们会留在这里。

## 分享这篇文章