# Javascript 继承

> 原文：<https://www.sitepoint.com/javascript-inheritance/>

在谷歌上搜索“JavaScript 继承”会返回一些有趣但在我看来也令人沮丧的结果，比如[道格拉斯·克洛克福特的](http://www.crockford.com/)各种 Javascript 的[经典继承](http://www.crockford.com/javascript/inheritance.html)或者[凯文·林赛的方法](http://www.kevlindev.com/tutorials/javascript/inheritance/)，前者并没有真正提供一个明确的答案，而后者依赖于给你的对象附加一个“超类”属性的惯例，实际上需要为你创建的每个实例将你的对象粘在一起。

与此同时，像 [prototype](http://prototype.conio.net/) 和 [MochKit](http://www.mochikit.com/about.html) 这样的库已经进化出了它们自己的惯例和策略，你可能不欣赏也可能不欣赏。我每次都脾气暴躁。尽管 prototype 中有一些非常酷的代码，但它扩展了内置的 Javascript 类型，比如我不喜欢的[对象](http://www.devguru.com/Technologies/ecmascript/quickref/object.html)——这让我对混合其他来源的其他 Javascript 库感到紧张。与此同时，看看 MochKit(它可能是最好的 AJAX 实现，很好地参考了 [twisted](http://twistedmatrix.com/) )在谈到它的“基础”时，受到了这里[总结的那种聪明的影响。](https://www.sitepoint.com/60000-lines-of-javascript/)

**另一种选择**

如果你来自像 Java 这样的语言，有另一种方法可能会给你大部分你期望的东西([ooops](https://www.sitepoint.com/design-patterns-in-dynamic-programming/#comment-12775)——似乎不能阻止它；)).这要感谢[Troels Knak-Nielsen](http://www.kyberfabrikken.dk/)([indite](http://www.kyberfabrikken.dk/opensource/indite/)——实时验证所见即所得控件的作者)很久以前在 [JPSpan](http://sourceforge.net/projects/jpspan) 邮件列表[这里](http://sourceforge.net/mailarchive/forum.php?thread_id=6198582&forum_id=43023)告诉我这件事。

```
 function copyPrototype(descendant, parent) {
    var sConstructor = parent.toString();
    var aMatch = sConstructor.match( /s*function (.*)(/ );
    if ( aMatch != null ) { descendant.prototype[aMatch[1]] = parent; }
    for (var m in parent.prototype) {
        descendant.prototype[m] = parent.prototype[m];
    }
}; 

```

*注意:*以上代码在最初发布后更新[此评论](https://www.sitepoint.com/javascript-inheritance/#comment-12808)

乍一看，这个正则表达式看起来像是一个黑客，但是如果你慢慢理解它(这花了我一年多的时间)，你就会意识到它不是。有必要记住特罗尔对此所说的话；

> 首先，我完全理解并同意不扩展语言功能的观点。也就是说，我认为与 javascript 和继承相关的一点经常被误解。javascript 的核心是它是一种“无类型的面向对象语言”。这同样适用于 ECMAscript3.0。人们最常犯的错误是当他们把原型误认为类的时候——他们不是。
> 
> 在类型化的 OO 语言中，对象代码存在于类中——对象只是实例。在 js 中，代码存在于对象中。因此，对象不依赖于原型。在 js 中操纵原型运行时是可以的，即使是在对象被实例化之后。考虑用一种类型化的语言来做这件事。
> 
> 总之。我最近一直在研究 js，发现使用原型一级继承足够简单，但是进一步的继承会带来麻烦。我猜你和 JPSpan 得出了同样的结论。我第一次在谷歌上搜索这个话题时，发现了很多奇怪的东西，我不喜欢。我最终发明了一种解决方案，我一直在使用，我对此非常满意。它可能看起来像一个黑客，但它工作得很好，我有点原谅它。
> 
> 为了进一步为自己辩护，我将指出它不是一个语言扩展，如果你能理解它的作用的话。它仅仅是一个扩展原型的工具——不要与类继承混淆(尽管结果是一样的)。

通过尝试，最容易发现 Troels 点…

**基本继承**

您期望的方式…

```
 function Animal() {
    this.species = "animal";
};

Animal.prototype.category = function() {
    alert(this.species);
};

function Dog() {
    // Call the parent constructor to setup
    // the parent object properties...
    this.Animal();
};

// Dog "inherits" from Animal
copyPrototype(Dog, Animal);

var d = new Dog();
d.category(); 

```

…我收到了“动物”警报

另一方面，这次不调用父构造函数…

```
 function Animal() {
    this.species = "animal";
};

Animal.prototype.category = function() {
    alert(this.species);
};

function Dog() {
    // No call to parent constructor
    this.species = "canine";
};

// Dog "inherits" from Animal
copyPrototype(Dog, Animal);

var d = new Dog();
d.category(); 

```

这次警报是“犬科动物”。

**多代**

Javascript 继承的不同方法经常会遇到一个问题，下面是 Troels 方法如何处理这个问题；

```
 function Animal() {
    this.species = "animal";
};

Animal.prototype.category = function() {
    alert(this.species);
};

function Dog() {
    this.Animal();
    this.species += ":dog";
};

// Dog "inherits" from Animal
copyPrototype(Dog, Animal);

function Poodle() {
    this.Dog();
    this.species += ":poodle";
};

// Poodle "inherits" from Dog
copyPrototype(Poodle, Dog);

var p = new Poodle();
p.category(); 

```

…警报:“动物:狗:狮子狗”

**覆盖父方法**

默认情况下，这种方法只是替换方法。这意味着你需要确保在分配任何方法给子类原型之前调用`copyPrototype` *，否则父方法将覆盖子方法；*

```
 function Animal() {
    this.species = "animal";
};

Animal.prototype.category = function() {
    alert(this.species);
};

function Dog() {
    this.Animal();
    this.species += ":canine";
};

// Dog "inherits" from Animal
copyPrototype(Dog, Animal);

// Override parent method _after_ calling copyPrototype
Dog.prototype.category = function() {
    alert(this.species.toUpperCase())

}; 

```

…显示“动物:犬科动物”

在 [apply](http://www.devguru.com/Technologies/ecmascript/quickref/apply.html) 的帮助下，调用被覆盖的父方法也是可能的，尽管有点痛苦。修改上面的`Dog.prototype.category`方法演示了这一点；

```
 Dog.prototype.category = function() {
    // Call overridden parent method...
    Animal.prototype.category.apply(this);
    alert(this.species.toUpperCase())
}; 

```

…这将导致两个警报。如果您需要传递方法参数，您需要传递 [arguments](http://www.devguru.com/Technologies/ecmascript/quickref/arguments.html) 数组作为要应用的第二个参数()。

**Mixins**

MixIns (基本上是多重继承)也以一种相当可预测的方式工作；

```
 function Animal() {
    this.species = "animal";
};

Animal.prototype.category = function() {
    alert(this.species);
};

function Quadruped() {};

Quadruped.prototype.run = function() {
    alert('Running...');
}

function Dog() {
    this.Animal();
    this.species += ":canine";
};

// Dog "inherits" from Animal
copyPrototype(Dog, Animal);

// Dog "inherits" from Quadruped
copyPrototype(Dog, Quadruped);

var d = new Dog();
d.category();
d.run(); 

```

…两个警报:“动物:犬类”和“奔跑…”

**注意事项**

这种方法有两个特别的警告…

1.*当*打电话给`copyPrototype`的时候很重要，就像已经提到的。实际上，这意味着最好在声明子构造函数后立即调用它。

2.不要为父类*函数对象*声明`toString`方法，因为这可能会破坏`copyPrototype`中的正则表达式。你仍然可以修改父*原型*而不会造成任何伤害。换句话说，这将打破`copyPrototype`；

```
 Animal.toString = function() {
    return "this is an animal";
} 

```

但这是可以的；

```
 Animal.prototype.toString = function() {
    return "this is an animal";
} 

```

**总之**

在我见过的所有方法中，对我来说这已经成为最有说服力的——保持你的代码[干燥](http://www.artima.com/intv/dry.html)而不必修改核心 Javascript 类型和引入奇怪副作用的最好方法。它也只有 8 行代码，所以花费不多。归功于 [Troels](http://www.kyberfabrikken.dk/) 高超的横向思维。

## 分享这篇文章