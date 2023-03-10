# 理解 ECMAScript 6:类和继承

> 原文：<https://www.sitepoint.com/understanding-ecmascript-6-class-inheritance/>

本文是微软网站开发技术系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

我想与您分享一系列关于 ECMAScript 6 的文章，分享我对它的热情，并解释它如何为您工作。我希望你喜欢读它们，就像我喜欢写它们一样。

首先，我在微软工作，为 Spartan 项目开发浏览器渲染引擎，这是对我们熟悉的 Internet Explorer 引擎的巨大改进。)这些年来。我个人最喜欢它的特点是它支持很多 ECMAScript 6。对我来说，这是为 web 编写大型应用程序的巨大好处。

根据 status.modern.IE 上的[兼容性表](http://kangax.github.io/compat-table/es6/?WT.mc_id=12833-DEV-sitepoint-othercontent)和 [ES6，到目前为止，我们在 Project Spartan 中已经拥有了将近 70%的 ECMAScript 6 特性。](http://status.modern.ie?utm_source=SitePoint&utm_medium=other&utm_campaign=SitePoint)

![ES6 Compatibility Table](img/ebd2edb5ae6bd571e9d132abc2793f17.png)

我喜欢 JavaScript，但当涉及到像 Babylon.js 这样的大型项目时，我更喜欢 [TypeScript](http://www.typescriptlang.org/?WT.mc_id=12833-DEV-sitepoint-othercontent) ，它现在是[为 Angular 2 btw](http://blogs.msdn.com/b/somasegar/archive/2015/03/05/typescript-lt-3-angular.aspx?WT.mc_id=12833-DEV-sitepoint-othercontent) 提供动力。原因是 JavaScript(或 ECMAScript 5)没有我用来编写大型项目的其他语言的所有语法特性。例如，我想念班级和继承。

所以，事不宜迟，让我们进入正题:

## 创建一个类

JavaScript 是一种面向原型的语言，可以用 ECMAScript 5 模拟类和继承。

JavaScript 中函数的灵活性允许我们模拟我们在处理类时习惯的封装。我们可以使用的技巧是扩展对象的原型:

```
var Animal = (function () {
    function Animal(name) {
        this.name = name;
    }
    // Methods
    Animal.prototype.doSomething = function () {
        console.log("I'm a " + this.name);
    };
    return Animal;
})();

var lion = new Animal("Lion");
lion.doSomething();
```

我们可以看到，我们用`properties`和`methods`定义了一个`class`。

构造函数是由函数本身(函数 Animal)定义的，在这里我们可以实例化属性。通过使用原型，我们可以定义类似实例方法的函数。

这是可行的，但是它假设你知道原型继承，对于来自基于类的语言的人来说，这看起来非常混乱。奇怪的是，JavaScript 有一个`class`关键字，但是它什么也不做。ECMAScript 6 现在实现了这一点，并允许使用更短的代码:

```
class AnimalES6 {
    constructor(name) {
        this.name = name;
    }

    doSomething() {
        console.log("I'm a " + this.name);
    }
}

var lionES6 = new AnimalES6("Lion");
lionES6.doSomething();
```

结果是一样的，但是对于习惯于编写类的开发人员来说，这更容易编写和阅读。不需要原型，您可以使用`constructor`关键字来定义构造函数。

此外，类引入了许多 ECMAScript 5 中没有的新语义。例如，没有`new`就不能调用构造函数，或者不能尝试用`new`构造方法。另一个变化是方法是不可枚举的。

有趣的一点是:两个版本可以并存。

在一天结束时，即使有了新的关键字，你也只能得到一个添加了函数的原型函数。这里的一个`method`只是你的对象的一个函数属性。

ES6 还支持基于类开发的另一个核心特性，getters 和 setters。这使得`method`应该做什么变得更加明显:

```
class AnimalES6 {
    constructor(name) {
        this.name = name;
        this._age = 0;
    }

    get age() {
        return this._age;
    }

    set age(value) {
        if (value < 0) {
            console.log("We do not support undead animals");
        }

        this._age = value;
    }

    doSomething() {
        console.log("I'm a " + this.name);
    }
}

var lionES6 = new AnimalES6("Lion");
lionES6.doSomething();
lionES6.age = 5;
```

很方便，对吧？

但是我们在这里可以看到 JavaScript 的一个常见的警告:私有成员(`_age`)。前段时间我写了一篇关于这个[话题](http://blogs.msdn.com/b/eternalcoding/archive/2014/08/05/javascript-using-closure-space-to-create-real-private-members.aspx?WT.mc_id=12833-DEV-sitepoint-othercontent)的文章。

幸运的是，我们现在有了一个更好的方法来完成这项工作，ECMAScript 6 的一个新特性是:符号:

```
var ageSymbol = Symbol();

class AnimalES6 {
    constructor(name) {
        this.name = name;
        this[ageSymbol] = 0;
    }

    get age() {
        return this[ageSymbol];
    }

    set age(value) {
        if (value < 0) {
            console.log("We do not support undead animals");
        }

        this[ageSymbol] = value;
    }

    doSomething() {
        console.log("I'm a " + this.name);
    }
}

var lionES6 = new AnimalES6("Lion");
lionES6.doSomething();
lionES6.age = 5;
```

那么什么是象征呢？这是一种唯一的、不可变的数据类型，可以用作对象属性的标识符。如果没有该符号，则无法访问该属性。

这导致更“私人”的成员访问。

或者至少不太容易接近。符号对于名称的唯一性很有用，但唯一性并不意味着隐私。唯一性仅仅意味着，如果你需要一个不能与任何其他键冲突的键，创建一个新的符号。

但是这还不是真正的私有，因为多亏了`Object.getOwnPropertySymbols`，下游消费者可以访问你的符号属性。

## 处理继承

一旦有了阶级，我们也想有传承。再一次,[在 ES5](http://blogs.msdn.com/b/eternalcoding/archive/2014/08/20/simple-inheritance-with-javascript.aspx?WT.mc_id=12833-DEV-sitepoint-othercontent) 中模拟继承是可能的，但是做起来相当复杂。

例如，下面是 TypeScript 模拟继承的结果:

```
var __extends = this.__extends || function (d, b) {
    for (var p in b) if (b.hasOwnProperty(p)) d[p] = b[p];
    function __() { this.constructor = d; }
    __.prototype = b.prototype;
    d.prototype = new __();
};
var SwitchBooleanAction = (function (_super) {
     __extends(SwitchBooleanAction, _super);
     function SwitchBooleanAction(triggerOptions, target, propertyPath, condition) {
        _super.call(this, triggerOptions, condition);
        this.propertyPath = propertyPath;
        this._target = target;
     }
     SwitchBooleanAction.prototype.execute = function () {
        this._target[this._property] = !this._target[this._property];
     };
     return SwitchBooleanAction;
})(BABYLON.Action);
```

不太容易读懂。

但是 ECMAScript 6 更好:

```
var legsCountSymbol = Symbol();
class InsectES6 extends AnimalES6 {
    constructor(name) {
        super(name);
        this[legsCountSymbol] = 0;
    }

    get legsCount() {
        return this[legsCountSymbol];
    }

    set legsCount(value) {
        if (value < 0) {
            console.log("We do not support nether or interstellar insects");
        }

        this[legsCountSymbol] = value;
    }

    doSomething() {
        super.doSomething();
        console.log("And I have " + this[legsCountSymbol] + " legs!");
    }
}

var spiderES6 = new InsectES6("Spider");
spiderES6.legsCount = 8;
spiderES6.doSomething();
```

由于有了`extends`关键字，您可以将一个类专门化为一个子类，同时用`super`关键字保持对根类的引用。

有了所有这些伟大的补充，现在可以创建类和继承工作，而不用处理原型巫毒魔法。

## 为什么使用 TypeScript 比以前更加重要…

随着所有这些新特性在我们的浏览器上可用，我认为使用 TypeScript 来生成 JavaScript 代码变得更加重要。

首先，所有最新版本的 TypeScript (1.4)都开始增加对 ECMAScript 6 代码的支持(使用`let`和`const`关键字)，因此您只需保留现有的 TypeScript 代码，并启用这个新选项来开始生成 ECMAScript 6 代码。

但是如果你仔细观察一些 TypeScript 代码，你会发现它看起来像没有类型的 ECMAScript 6。所以今天学习 TypeScript 是明天理解 ECMAScript 6 的好方法！

## 结论

使用 TypeScript，您现在就可以在各种浏览器之间拥有这一切，因为您的代码被转换为 ECMASCript 5。如果想直接在浏览器中使用 ECMAScript 6，可以升级到 Windows 10，在那里用 Project Spartan 的渲染引擎进行测试。如果你不想只是为了尝试一些新的浏览器功能而这样做，你也可以在这里使用 Project Spartan [访问 Windows 10 电脑。这也适用于你的 MacOS 或 Linux 系统。](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=content&utm_campaign=SitePoint)

当然，Project Spartan 并不是唯一支持开放标准 ES6 的浏览器。其他浏览器也在上面，你可以在这里跟踪支持水平[。](http://kangax.github.io/compat-table/es6/)

JavaScript 和 ECMAScript 6 的未来是光明的，老实说，我迫不及待地想看到它在所有现代浏览器上得到广泛支持！

本文是微软网站开发技术系列的一部分。我们很高兴与你分享斯巴达项目的[和它的](http://blogs.msdn.com/b/ie/archive/2015/01/22/project-spartan-and-the-windows-10-january-preview-build.aspx?WT.mc_id=12833-DEV-sitepoint-othercontent)[新渲染引擎](http://blogs.msdn.com/b/ie/archive/2015/02/26/a-break-from-the-past-the-birth-of-microsoft-s-new-web-rendering-engine.aspx?WT.mc_id=12833-DEV-sitepoint-othercontent)。在 [modern 获得免费虚拟机或在您的 Mac、iOS、Android 或 Windows 设备上进行远程测试。即](http://modern.ie?utm_source=SitePoint&utm_medium=other&utm_campaign=SitePoint)。

## 分享这篇文章