# JavaScript ES2015 中的对象继承模式

> 原文：<https://www.sitepoint.com/patterns-object-inheritance-javascript-es2015/>

![A child reading in bed with a torch, with scary silhouettes cast on the wall by toys](img/adf33be21744e358837829a71310dda3.png)

随着期待已久的 ES2015(以前称为 ES6)的到来，JavaScript 配备了专门定义类的语法。在这篇文章中，我将探索我们是否可以利用类语法将更小的部分组成类。

将层次结构的深度保持在最小对于保持代码的整洁是很重要的。明智地划分班级会有所帮助。对于大型代码库，一种选择是用较小的部分创建类；组成类。这也是避免重复代码的常用策略。

想象一下，我们正在构建一个游戏，玩家生活在一个动物的世界里。有些是朋友，有些是敌对的(像我这样的爱狗人士可能会说所有的猫都是敌对的生物)。我们可以创建一个类`HostileAnimal`，它扩展了`Animal`，作为`Cat`的基类。在某种程度上，我们决定加入旨在伤害人类的机器人。我们做的第一件事是创建`Robot`类。我们现在有两个具有相似属性的类。例如，`HostileAnimal`和`Robot`都能够`attack()`。

如果我们能以某种方式在一个单独的类或对象中定义敌意，比如说`Hostile`，我们就可以将这两个`Cat`都重用为`Robot`。我们可以用各种方式做到这一点。

多重继承是一些经典 OOP 语言支持的特性。顾名思义，它让我们能够创建一个从多个基类继承的类。在下面的 Python 代码中，查看`Cat`类如何扩展多个基类:

```
class Animal(object):
  def walk(self):
    # ...

class Hostile(object):
  def attack(self, target):
    # ...

class Dog(Animal):
  # ...

class Cat(Animal, Hostile):
  # ...

dave = Cat();
dave.walk();
dave.attack(target); 
```

一个*接口*是(类型化)经典 OOP 语言中的一个常见特征。它允许我们定义一个类应该包含什么方法(有时是属性)。如果这个类没有，编译器会抛出一个错误。如果`Cat`没有`attack()`或`walk()`方法，下面的类型脚本代码会引发错误:

```
interface Hostile {
  attack();
}

class Animal {
  walk();
}

class Dog extends Animal {
  // ...
}

class Cat extends Animal implements Hostile {
  attack() {
    // ...
  }
} 
```

多重继承遭遇了钻石问题(两个父类定义了相同的方法)。一些语言通过实现其他策略来回避这个问题，比如 *mixins* 。Mixins 是只包含方法的小类。mixins 不是扩展这些类，而是包含在另一个类中。例如，在 PHP 中，mixins 是使用 Traits 实现的。

```
class Animal {
  // ...
}

trait Hostile {
  // ...
}

class Dog extends Animal {
  // ...
}

class Cat extends Animal {
  use Hostile;
  // ...
}

class Robot {
  use Hostile;
  // ...
} 
```

## 概述:ES2015 类语法

如果你还没有机会深入 ES2015 类，或者觉得你对它们了解不够，请务必在继续之前阅读 Jeff Mott 的[面向对象的 JavaScript——深入 ES6 类](https://www.sitepoint.com/object-oriented-javascript-deep-dive-es6-classes/)。

简而言之:

*   `class Foo { ... }`描述了一个名为`Foo`的类
*   `class Foo extends Bar { ... }`描述了一个类`Foo`，它扩展了另一个类`Bar`

在类块中，我们可以定义该类的属性。对于本文，我们只需要理解构造函数和方法:

*   `constructor() { ... }`是创建时执行的保留功能(`new Foo()`
*   `foo() { ... }`创建一个名为`foo`的方法

类语法主要是 JavaScript 原型模型的语法糖。它没有创建类，而是创建了一个函数构造函数:

```
class Foo {}
console.log(typeof Foo); // "function" 
```

这里的要点是 JavaScript 不是基于类的 OOP 语言。有人甚至会认为语法是欺骗性的，给人的印象就是如此。

## 编写 ES2015 类

可以通过创建一个抛出错误的伪方法来模拟接口。继承后，必须覆盖该函数以避免错误:

```
class IAnimal {
  walk() {
    throw new Error('Not implemented');
  }
}

class Dog extends IAnimal {
  // ...
}

const robbie = new Dog();
robbie.walk(); // Throws an error 
```

如前所述，这种方法依赖于继承。要继承多个类，我们要么需要多重继承，要么需要混合。

另一种方法是编写一个实用函数，在类被定义后对其进行验证。这方面的例子可以在[中找到，等等，JavaScript 确实支持多重继承！安德烈亚·贾马尔奇。请参阅“基本对象.实现功能检查”一节](http://webreflection.blogspot.com/2009/06/wait-moment-javascript-does-support.html)

是时候探索应用多重继承和混合的各种方法了。下面所有被检查的策略都可以在 [GitHub](https://github.com/sitepoint-editors/es2015-class-composition) 上找到。

### `Object.assign(ChildClass.prototype, Mixin...)`

ES2015 之前，我们使用原型进行继承。所有函数都有一个`prototype`属性。当使用`new MyFunction()`创建一个实例时，`prototype`被复制到实例中的一个属性中。当您试图访问一个不在实例中的属性时，JavaScript 引擎会尝试在 prototype 对象中查找它。

为了演示，请看下面的代码:

```
function MyFunction () {
  this.myOwnProperty = 1;
}
MyFunction.prototype.myProtoProperty = 2;

const myInstance = new MyFunction();

// logs "1"
console.log(myInstance.myOwnProperty);
// logs "2"
console.log(myInstance.myProtoProperty);

// logs "true", because "myOwnProperty" is a property of "myInstance"
console.log(myInstance.hasOwnProperty('myOwnProperty'));
// logs "false", because "myProtoProperty" isn’t a property of "myInstance", but "myInstance.__proto__"
console.log(myInstance.hasOwnProperty('myProtoProperty')); 
```

这些原型对象可以在运行时创建和修改。最初，我试图为`Animal`和`Hostile`使用类:

```
class Animal {
  walk() {
    // ...
  }
}

class Dog {
  // ...
}

Object.assign(Dog.prototype, Animal.prototype); 
```

上面的方法不起作用，因为类方法是不可枚举的。实际上，这意味着`Object.assign(...)`不从类中复制方法。这也使得创建将方法从一个类复制到另一个类的函数变得困难。但是，我们可以手动复制每个方法:

```
Object.assign(Cat.prototype, {
  attack: Hostile.prototype.attack,
  walk: Animal.prototype.walk,
}); 
```

另一种方法是抛弃类，使用对象作为混合。一个积极的副作用是 mixin 对象不能用于创建实例，从而防止误用。

```
const Animal = {
  walk() {
    // ...
  },
};

const Hostile = {
  attack(target) {
    // ...
  },
};

class Cat {
  // ...
}

Object.assign(Cat.prototype, Animal, Hostile); 
```

**优点**

*   无法初始化 Mixins

**缺点**

*   需要一行额外的代码
*   Object.assign()有点晦涩
*   重塑原型继承以使用 ES2015 类

### 在构造函数中合成对象

对于 ES2015 类，您可以通过在构造函数中返回对象来覆盖实例:

```
class Answer {
  constructor(question) {
    return {
      answer: 42,
    };
  }
}

// { answer: 42 }
new Answer("Life, the universe, and everything"); 
```

我们可以利用该特性从一个子类中的多个类组合一个对象。注意`Object.assign(...)`仍然不能很好地与 mixin 类一起工作，所以我在这里也使用了对象:

```
const Animal = {
  walk() {
    // ...
  },
};

const Hostile = {
  attack(target) {
    // ...
  },
};

class Cat {
  constructor() {
    // Cat-specific properties and methods go here
    // ...

    return Object.assign(
      {},
      Animal,
      Hostile,
      this
    );
  }
} 
```

由于`this`在上面的上下文中引用了一个类(带有不可枚举的方法)，`Object.assign(..., this)`没有复制`Cat`的方法。相反，您必须在`this`上显式设置字段和方法，以便`Object.assign()`能够应用它们，如下所示:

```
class Cat {
  constructor() {
    this.purr = () => {
      // ...
    };

    return Object.assign(
      {},
      Animal,
      Hostile,
      this
    );
  }
} 
```

这种方法不实用。因为您返回的是一个新对象，而不是一个实例，所以它本质上相当于:

```
const createCat = () => Object.assign({}, Animal, Hostile, {
  purr() {
    // ...
  }
});

const thunder = createCat();
thunder.walk();
thunder.attack(); 
```

我想我们可以同意后者更具可读性。

**优点**

*   我想这行得通吧？

**缺点**

*   非常模糊
*   ES2015 类语法的零收益
*   ES2015 类的误用

### 类工厂函数

这种方法利用了 JavaScript 在运行时定义类的能力。

首先，我们需要基类。在我们的例子中，`Animal`和`Robot`作为基类。如果你想从头开始，空类也可以。

```
class Animal {
  // ...
}

class Robot {
  // ...
} 
```

接下来，我们必须创建一个工厂函数，它返回一个扩展类`Base`的新类，该类作为参数传递。这些是混音:

```
const Hostile = (Base) => class Hostile extends Base {
  // ...
}; 
```

现在我们可以将任何类传递给`Hostile`函数，该函数将返回一个新类，该新类结合了`Hostile`和我们传递给该函数的任何类:

```
class Dog extends Animal {
  // ...
}

class Cat extends Hostile(Animal) {
  // ...
}

class HostileRobot extends Hostile(Robot) {
  // ...
} 
```

我们可以通过几个类来应用多个混合:

```
class Cat extends Demonic(Hostile(Mammal(Animal))) {
  // ...
} 
```

也可以使用`Object`作为基类:

```
class Robot extends Hostile(Object) {
  // ...
} 
```

**优点**

*   更容易理解，因为所有信息都在类声明头中

**缺点**

*   在运行时创建类可能会影响启动性能和/或内存使用

## 结论

当我决定研究这个主题并写一篇关于它的文章时，我期望 JavaScript 的原型模型有助于生成类。因为类语法使得方法不可枚举，对象操作变得更加困难，几乎不切实际。

类语法可能会让人误以为 JavaScript 是基于类的 OOP 语言，但事实并非如此。对于大多数方法，您必须修改对象的原型来模仿多重继承。最后一种方法，使用类工厂函数，是使用 mixins 来组合类的一种可接受的策略。

如果你发现基于原型的编程有局限性，你可能需要考虑一下你的思维模式。原型提供了您可以利用的无与伦比的灵活性。

如果出于某种原因，您仍然喜欢经典编程，那么您可能想研究一下编译成 JavaScript 的语言。例如， [TypeScript](https://www.typescriptlang.org/) 是 JavaScript 的一个超集，它增加了(可选的)静态类型和其他经典 OOP 语言中常见的模式。

你会在你的项目中使用上述方法吗？你找到更好的方法了吗？请在评论中告诉我！

*这篇文章由[杰夫·莫特](https://www.sitepoint.com/author/jmott/)、[斯科特·莫利纳里](https://www.sitepoint.com/author/smolinari/)、[维尔丹·索维奇](https://www.sitepoint.com/author/vildansoftic/)和[琼·尹](https://github.com/newjs)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

## 分享这篇文章