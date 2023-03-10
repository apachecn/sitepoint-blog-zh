# 快速提示:从头开始重新实现闭包来掌握它们

> 原文：<https://www.sitepoint.com/quick-tip-master-closures-by-reimplementing-them-from-scratch/>

*这篇文章由[蒂姆·塞韦里恩](https://www.sitepoint.com/author/tseverien/)和[米凯拉·莱尔](https://www.sitepoint.com/author/mlehr/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

说有很多关于闭包的文章是一种保守的说法。大多数人会解释闭包的定义，这通常可以归结为一句简单的话:闭包是一个函数，它记住了它被创建的环境。但是它是如何记住的呢？为什么闭包可以在局部变量超出作用域很久之后使用这些变量？为了揭开围绕闭包的魔法面纱，我将假装 JavaScript *没有*有闭包，*不能*嵌套函数，然后我们将从头开始重新实现闭包。在这样做的时候，我们将发现闭包到底是什么，以及它们是如何工作的。

在这个练习中，我还需要假设 JavaScript 有一个它实际上没有的特性。我需要假设一个普通的对象可以像一个函数一样被调用。您可能已经在其他语言中看到过这个特性。Python 让你定义一个`__call__`方法，PHP 有一个特殊的`__invoke`方法，当调用一个对象时，就像调用一个函数一样执行这些方法。如果我们假设 JavaScript 也有这个特性，可能会是这样:

```
// An otherwise ordinary object with a "__call__" method
let o = {
  n: 42,
  __call__() {
    return this.n;
  }
};

// Call object as if it were a function
o(); // 42 
```

这里我们有一个普通的对象，我们假装可以像调用函数一样调用它，当我们这样做时，特殊的`__call__`方法被执行，就像我们写了`o.__call__()`一样。

现在，让我们看一个简单的闭包例子。

```
function f() {
  // This variable is local to "f"
  // Normally it would be destroyed when we leave "f"'s scope
  let n = 42;

  // An inner function that references "n"
  function g() {
    return n;
  }

  return g;
}

// Get the "g" function created by "f"
let g = f();

// The variable "n" should be destroyed by now, right?
// After all, "f" is done executing and we've left its scope
// So how can "g" still reference a freed variable?
g(); // 42 
```

这里我们有一个带局部变量的外部函数`f`，和一个引用`f`的局部变量的内部函数`g`。然后我们返回内部函数`g`，并在`f`的范围之外执行它。但是如果`f`执行完了，那么`g`怎么还能使用超出范围的变量呢？

魔术是这样的:闭包不仅仅是一个函数。它是一个*对象*，带有一个构造函数和私有数据，我们可以像调用函数一样调用*。如果 JavaScript 没有闭包，我们必须自己实现它们，下面是它的样子。*

```
class G {
  // An instance of "G" will be constructed with a value "n",
  // and it stores that value in its private data
  constructor(n) {
    this._n = n;
  }

  // When we call an instance of "G", it returns the value from its private data
  __call__() {
    return this._n;
  }
}

function f() {
  let n = 42;

  // This is the closure
  // Our inner function isn't really a function
  // It's a callable object, and we pass "n" to its constructor
  let g = new G(n);

  return g;
}

// Get the "g" callable object created by "f"
let g = f();

// It's okay if the original variable "n" from "f"'s scope is destroyed now
// The callable object "g" is actually referencing its own private data
g(); // 42 
```

这里我们用类`G`的一个实例替换了内部函数`g`，并通过将`f`的局部变量传递给`G`的构造函数来捕获该变量，然后构造函数将该值存储在新实例的私有数据中。女士们先生们，这是一个终结。真的就这么简单。闭包是一个可调用的对象，它从实例化的环境中私有地存储通过构造函数传递的值。

## 更进一步

敏锐的读者会注意到有些行为我们还没有考虑到。让我们看另一个闭包例子。

```
function f() {
  let n = 42;

  // An inner function that references "n"
  function get() {
    return n;
  }

  // Another inner function that also references "n"
  function next() {
    n++;
  }

  return {get, next};
}

let o = f();

o.get(); // 42
o.next();
o.get(); // 43 
```

在这个例子中，我们有两个闭包，它们都引用了同一个变量`n`。一个函数对该变量的操作会影响另一个函数的值。但是如果 JavaScript 没有闭包，我们必须自己实现它们，那么我们就不会有同样的行为。

```
class Get {
  constructor(n) {
    this._n = n;
  }

  __call__() {
    return this._n;
  }
}

class Next {
  constructor(n) {
    this._n = n;
  }

  __call__() {
    this._n++;
  }
}

function f() {
  let n = 42;

  // These are the closures
  // They're callable objects that privately store the values
  // passed through their constructors
  let get = new Get(n);
  let next = new Next(n);

  return {get, next};
}

let o = f();

o.get(); // 42
o.next();
o.get(); // 42 
```

像以前一样，我们用类`Get`和`Next`的实例替换了内部函数`get`和`next`，它们通过将`f`的局部变量传递给构造函数并将该值存储在每个实例的私有数据中来捕获该变量。但是请注意，一个可调用对象对`n` *的操作并没有影响另一个可调用对象的值。发生这种情况是因为他们没有捕获一个*引用*到`n`；他们捕获了`n`值的一个*副本*。*

为了解释为什么 JavaScript 的闭包会引用同一个`n`，我们需要解释变量本身。事实上，JavaScript 的局部变量在传统意义上并不是真正的局部变量。相反，它们是动态分配和引用计数对象的属性，称为“LexicalEnvironment”对象，JavaScript 的闭包捕获对整个环境的引用，而不是对任何一个特定变量的引用。

让我们改变我们的可调用对象实现来捕获词法环境，而不是专门的`n`。

```
class Get {
  constructor(lexicalEnvironment) {
    this._lexicalEnvironment = lexicalEnvironment;
  }

  __call__() {
    return this._lexicalEnvironment.n;
  }
}

class Next {
  constructor(lexicalEnvironment) {
    this._lexicalEnvironment = lexicalEnvironment;
  }

  __call__() {
    this._lexicalEnvironment.n++;
  }
}

function f() {
  let lexicalEnvironment = {
    n: 42
  };

  // These callable objects capture a reference to the lexical environment,
  // so they will share a reference to the same "n"
  let get = new Get(lexicalEnvironment);
  let next = new Next(lexicalEnvironment);

  return {get, next};
}

let o = f();

// Now our callable objects exhibit the same behavior as JavaScript's functions
o.get(); // 42
o.next();
o.get(); // 43 
```

这里我们用一个具有属性`n`的`lexicalEnvironment`对象替换了局部变量`n`。闭包——类`Get`和`Next`的可调用实例——捕获对词汇环境对象的引用，而不是`n`的值。因为它们现在共享对同一个`n`的引用，一个可调用对象对`n`的操作会影响另一个可调用对象的值。

## 结论

闭包是我们可以像调用函数一样调用的对象。JavaScript 中的每个函数实际上都是一个可调用的对象，也称为“函数对象”或“仿函数”，它用一个词法环境对象实例化并私有存储，即使它是最外层的全局词法环境。在 JavaScript 中，函数不会创建闭包；函数*是*的闭包。

这篇文章对你理解闭包有帮助吗？我很高兴在下面的评论中听到你的想法或问题。

## 分享这篇文章