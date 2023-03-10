# JavaScript 设计模式:单例

> 原文：<https://www.sitepoint.com/javascript-design-patterns-singleton/>

在本文中，我们将深入探讨用 JavaScript 实现 singleton 的最佳方式，看看这是如何随着 ES6 的兴起而发展的。

在广泛生产中使用的语言中，JavaScript 是迄今为止发展最快的，看起来不像它最早的版本，更像 Python，ECMA International 提出了每一个新规范。虽然这些变化有不少批评者，但新的 JavaScript 确实成功地使代码更容易阅读和推理，更容易以符合软件工程最佳实践(特别是模块化和坚实原则的概念)的方式编写，更容易组装成规范的软件设计模式。

## 解释 ES6

ES6(又名 ES2015)是自 2009 年 ES5 标准化以来该语言的第一次重大更新。[几乎所有现代浏览器都支持 ES6](https://kangax.github.io/compat-table/es6/) 。然而，如果你需要适应旧的浏览器，ES6 代码可以很容易地通过 Babel 之类的工具转换成 ES5。ES6 给了 JavaScript 大量新特性，包括[对类的高级语法](https://www.sitepoint.com/object-oriented-javascript-deep-dive-es6-classes/)，以及[对变量声明的新关键字](https://www.sitepoint.com/es6-let-const/)。您可以通过阅读主题为的 [SitePoint 文章来了解更多信息。](https://www.sitepoint.com/javascript/es6/)

## 什么是独生子女

![9 Puzzle pieces. 8 are gray, 1 is red. A visual representation of the singleton pattern](img/13bb5c29dde117356d6f78554ab6cd98.png)

如果你不熟悉[单例模式](https://en.wikipedia.org/wiki/Singleton_pattern)，它的核心是一种设计模式，将一个类的实例化限制到一个对象。通常，目标是管理全局应用程序状态。我自己看到或编写的一些例子包括使用 singleton 作为 web 应用程序的配置设置源，在客户端使用 API 键启动任何东西(例如，您通常不想冒发送多个分析跟踪调用的风险)，以及在客户端 web 应用程序的内存中存储数据(例如，在 Flux 中存储)。

单例应该是消费代码不可变的，并且应该没有实例化多个单例的危险。

注意:有些情况下，单例可能是不好的，而事实上，它们总是不好的。关于这个讨论，你可以看看[这篇关于这个主题的文章](https://www.sitepoint.com/whats-so-bad-about-the-singleton/)。

## 用 JavaScript 创建单例的老方法

用 JavaScript 编写单例的老方法包括利用[闭包和立即调用的函数表达式](https://www.sitepoint.com/demystifying-javascript-closures-callbacks-iifes/)。下面是我们如何用老方法为一个假设的 Flux 实现编写一个(非常简单的)存储:

```
var UserStore = (function(){
  var _data = [];

  function add(item){
    _data.push(item);
  }

  function get(id){
    return _data.find((d) => {
      return d.id === id;
    });
  }

  return {
    add: add,
    get: get
  };
}()); 
```

当解释该代码时，`UserStore`将被设置为立即调用的函数的结果——一个公开两个函数的对象，但不授予对数据集合的直接访问。

然而，这段代码比它需要的更冗长，并且也没有给我们提供我们在使用单例时所希望的不变性。稍后执行的代码可以修改任一个[公开的函数](https://www.sitepoint.com/javascript-arrow-functions/)，或者甚至完全重新定义`UserStore`。此外，修改/违规代码可能在任何地方！如果我们由于对`UsersStore`的意外修改而发现了 bug，那么在一个更大的项目中追踪它们可能会非常令人沮丧。

正如 Ben Cherry 的文章中所指出的，你可以采取更高级的措施来减轻这些负面影响。(他的目标是创建模块，这些模块恰好是单例的，但模式是相同的。)但是这些给代码增加了不必要的复杂性，同时仍然不能得到我们想要的东西。

## 新的方式

通过利用 ES6 的特性，主要是模块和新的`const`变量声明，我们不仅可以用更简洁的方式编写单例，还可以更好地满足我们的需求。

让我们从最基本的实现开始。下面是对上述例子的(更清晰、更强大的)现代解释:

```
const _data = [];

const UserStore = {
  add: item => _data.push(item),
  get: id => _data.find(d => d.id === id)
}

Object.freeze(UserStore);
export default UserStore; 
```

如您所见，这种方式提高了可读性。但是它真正的亮点在于对使用我们的小单例模块的代码的约束:使用代码不能重新分配`UserStore`，因为有`const`关键字。作为我们使用 [Object.freeze](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze) 的结果，它的方法不能被改变，也不能给它添加新的方法或属性。此外，因为我们正在利用 ES6 模块，我们确切地知道在哪里使用了`UserStore`。

现在，这里我们把`UserStore`变成了一个对象文字。大多数时候，使用对象文字是最易读、最简洁的选择。然而，有时候你可能想利用传统课程的优势。例如，不断变化的商店都有许多相同的基本功能。利用传统的面向对象继承是在保持代码干燥的同时获得重复功能的一种方式。

如果我们想利用 ES6 类，实现看起来会是这样的:

```
class UserStore {
  constructor(){
    this._data = [];
  }

  add(item){
    this._data.push(item);
  }

  get(id){
    return this._data.find(d => d.id === id);
  }
}

const instance = new UserStore();
Object.freeze(instance);

export default instance; 
```

这种方式比使用对象文字略显冗长，而且我们的例子非常简单，我们看不出使用类有什么好处(尽管在最后的例子中它会派上用场)。

类路线的一个可能不明显的好处是，如果这是您的前端代码，而您的后端是用 C#或 Java 编写的，您可以在客户端应用程序中采用许多与后端相同的设计模式，并提高您的团队的效率(如果您很小，人们都在全栈工作)。听起来很软，也很难衡量，但是我在使用 React 前端开发 C#应用程序时亲身经历过，这种好处是真实的。

应该注意的是，从技术上讲，使用这两种模式的单例的不变性和不可重写性可能会被有动机的挑衅者破坏。通过使用 [Object.assign](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) ，可以复制一个对象文字，即使它本身是`const`。当我们导出一个类的实例时，尽管我们没有直接将类本身暴露给消费代码，但是任何实例的构造函数在 JavaScript 中都是可用的，并且可以被调用来创建新的实例。显然，这至少需要一点点努力，希望你的开发伙伴不要坚持违反单例模式。

但是，假设你想确保没有人破坏你的 singleton 的单一性，并且你还想让它与面向对象世界中的 singleton 的实现更加匹配。你可以这样做:

```
class UserStore {
  constructor(){
   if(! UserStore.instance){
     this._data = [];
     UserStore.instance = this;
   }

   return UserStore.instance;
  }

 //rest is the same code as preceding example

}

const instance = new UserStore();
Object.freeze(instance);

export default instance; 
```

通过添加保存对实例的引用的额外步骤，我们可以检查我们是否已经实例化了一个`UserStore`，如果我们已经实例化了，我们将不会创建一个新的。如您所见，这也很好地利用了我们已经将`UserStore`变成了一个类的事实。

## 想法？仇恨邮件？

毫无疑问，许多开发人员已经在 JavaScript 中使用旧的单例/模块模式很多年了，他们发现这种模式非常适合他们。尽管如此，因为寻找更好的做事方法对开发人员来说是如此重要，希望我们能看到像这样更干净、更容易理解的模式获得越来越多的关注。尤其是当利用 ES6+功能变得更加容易和普遍时。

这是我在生产中使用的一种模式，在定制的 Flux 实现中构建商店(商店比我们这里的例子多一点)，它工作得很好。但是如果你能看到其中的漏洞，请告诉我。另外，请支持您喜欢的任何一种新模式，不管您是否认为对象文字是可行的，或者您是否喜欢类！

## 分享这篇文章