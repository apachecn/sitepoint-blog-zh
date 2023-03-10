# 引入 TypeScript——类固醇上的 JavaScript

> 原文：<https://www.sitepoint.com/microsoft-typescript-javascript-precompiler/>

除非你整个星期都躲在灌木丛下，否则你肯定会遇到[打字稿](https://www.sitepoint.com/typescript-tutorial-for-beginners/)；微软的*应用级 JavaScript 开发的新语言*。有很多错误的信息在飞来飞去，所以我想我应该加入…

首先，TypeScript 不是 JavaScript 的替代品。TypeScript 对于 JavaScript 就像 C++对于 C 一样，它是语言的扩展，而不是一种新的语法。

TypeScript 被编译(*【预处理】*或*【trans compiled】*可能是更好的描述)成本地 JavaScript 代码。这是一个与 [CoffeeScript](http://coffeescript.org/) 类似的概念，除了 TypeScript 更接近 JavaScript 的现有语法；JavaScript++如果你喜欢的话。

## 打字稿功能

TypeScript 提供静态类型；您可以定义变量的数据类型。这种语法对于那些来自 C 语言的人来说有点不寻常，因为类型是在变量之后指定的，例如

```
 function Hello(name: string) {
	return "Hello " + name;
} 
```

许多开发人员会欣喜若狂地发现类声明和继承，例如

```
 // base class
class Language {
    private hasClasses: boolean;

	constructor(hasClasses: boolean) {
        this.hasClasses = hasClasses;
    }

    ClassSupported() {
		return (this.hasClasses ? "yes" : "no");
    }
}

// JavaScript language
class JavaScript extends Language {
    constructor(){
        super(false);
    }
}

// TypeScript language
class TypeScript extends Language {
    constructor(){
        super(true);
    }
}

var js = new JavaScript();
alert(js.ClassSupported()); // no

var ts = new TypeScript();
alert(ts.ClassSupported()); // yes 
```

我们也有接口:

```
 interface Website {
	name: string;
	url: string;
}

function ShowSite(site Website) {
	return site.name + " at http://" + site.url + "/";
}

alert(ShowSite({ name: "SitePoint", url: "www.sitepoint.com" })); 
```

有趣的是，一个对象不需要明确声明它正在实现一个接口；它只需要匹配预期的定义。

最后，TypeScript 实现模块，例如

```
 module Say {
	export function Hello(text: string) {
        return "Hello " + text + "!";
    }
}
alert(Say.Hello("world")); 
```

所有代码都直接翻译成 JavaScript，例如

```
 var Say;
(function (Say) {
    function Hello(text) {
        return "Hello " + text + "!";
    }
    Say.Hello = Hello;
})(Say || (Say = {}));

alert(Say.Hello("world")); 
```

微软提供了一个很好的在线平台来帮助你评估语法:[www.typescriptlang.org/Playground/](http://www.typescriptlang.org/Playground/)

## 我喜欢打字稿，因为…

让我们看看好东西。

不熟悉 JavaScript 的开发人员经常纠结于原型继承的概念。这与 C#和 Java 等语言中使用的传统继承概念有所不同。TypeScript 提供了令人放心的舒适。

显然，TypeScript 使用了提议的 ECMAScript Harmony 类语法。当这变得广泛可用时(不要屏住呼吸)，TypeScript 编译器将不需要转换类，您的代码仍将运行。

静态类型允许更好的 IDE 特性，比如自动完成、重构和调试。已经为 Visual Studio、Sublime Text、Vim 和 Emacs 发布了一个插件，更多插件正在发布中。换句话说，开发复杂的 JavaScript 应用程序应该更容易。

TypeScript 可以在 Node.js 中的客户端或服务器上使用。它是 JavaScript 的超集，可以编译成原始的、非混淆的代码。与谷歌的 Dart 语言不同，TypeScript 并不试图完全取代 JavaScript，不需要其他浏览器厂商添加功能，也不需要庞大的运行时库。它成功的机会要大得多。

这种语言是由安德斯·海尔斯伯格设计的，他给我们带来了优秀的 C#。TypeScript 是开源的，在 Apache 2.0 许可下于[typescript.codeplex.com](http://typescript.codeplex.com/)提供。

最后，用 TypeScript 编写 TypeScript 编译器。真是酷毙了。

## 但是…

我怀疑使用微软的开发者会采用 TypeScript。NET 和 Visual Studio。虽然它对其他人无疑是有用的，但 web 开发社区中的一些部门不会梦想接触微软的产品。下定决心——总会有 [*“微软！啊！！不要。！!"*](http://developers.slashdot.org/story/12/10/01/2011201/typescript-microsofts-replacement-for-javascript) 注释。

微软创建了 TypeScript，因为 JavaScript 仍然是有史以来最容易被误解的编程语言。近年来观点有所改进，但是你仍然会看到评论说 JavaScript 是一个失败，不应该被“合适的”开发者使用。除了他们自己的势利或对语言的误解，很少有人提供合理的论据。

我喜欢 JavaScript。它灵活、强大，并提供了被其他语言采用的特性。我可以理解 TypeScript 背后的基本原理，并且认为它已经很好地实现了，但是当我非常乐意编写更高效的本机 JavaScript 代码时，我不太可能使用它*(或者应该是)*。

但是如果你不是像我一样狂热的 JavaScript 爱好者，TypeScript 提供了一个令人信服的选择。

想了解更多关于 TypeScript 的信息，请前往[typescriptlang.org](http://www.typescriptlang.org/)。

在你问之前，微软没有赞助这篇文章，也没有让我写这篇文章。

## 分享这篇文章