# 通过历史了解 ES6 模块

> 原文：<https://www.sitepoint.com/understanding-es6-modules-via-their-history/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

ES6 给 JavaScript 带来了长期以来最大的变化，包括管理大型复杂代码库的几个新特性。这些功能，主要是导入和导出关键字，统称为模块。

如果您是 JavaScript 新手，尤其是如果您来自另一种已经内置了模块化支持(不同名称的模块、包或单元)的语言，ES6 模块的设计可能看起来很奇怪。很多设计来自 JavaScript 社区多年来设计的解决方案，以弥补内置支持的不足。

我们将看看 JavaScript 社区通过每个解决方案克服了哪些挑战，以及哪些挑战仍未解决。最后，我们将了解这些解决方案如何影响 ES6 模块设计，以及 ES6 模块如何面向未来定位自己。

## 先是标签，然后是争议

起初，HTML 将自己局限于面向文本的元素，这些元素以非常静态的方式进行处理。Mosaic 是早期最流行的浏览器之一，在所有 HTML 完成下载之前不会显示任何内容。在 90 年代早期的拨号连接上，这可能会让用户盯着一个空白屏幕看几分钟。

Netscape Navigator 几乎在 90 年代中后期一出现就大受欢迎。像许多目前的颠覆性创新者一样，网景公司通过改变不受普遍欢迎的界限

在 1995 年著名的 10 天时间里，Brendan Eich 为 Netscape 创建了 JavaScript。他们并没有发明动态网页脚本的想法——violaww 比他们早了 5 年——但是就像艾萨克·辛格的缝纫机一样，他们的流行使他们成为这个概念的同义词。

起初，很少有程序员做任何实质性的 JS 工作。甚至这个名字都暗示了 JavaScript 与其服务器端的亲戚如 Java 和 ASP 相比是一个次要的公民。世纪之交的大多数 JavaScript 都将自己局限于服务器无法影响的客户端条件——通常是简单的表单活动，比如将焦点放在第一个字段，或者在提交之前验证表单输入。AJAX 最常见的意思仍然是指腐蚀性的家用清洁剂，几乎所有重要的操作都需要与服务器进行完整的 HTTP 往返，所以几乎所有的 web 开发人员都是后端人员，他们瞧不起这种“玩具”语言。

你明白最后一段的意思了吗？验证一个表单输入可能很简单，但是在多个表单上验证多个输入就变得复杂了——毫无疑问，JS 代码库也是如此。就在客户端脚本具有不可否认的可用性优势变得显而易见的同时，普通脚本标签的问题也出现了:DOM 就绪通知的不可预测性；文件连接中的变量冲突；依赖性管理；你说吧。

JS 开发人员很容易找到工作，但却很难享受工作。当 jQuery 在 2006 年出现时，开发人员热情地采用了它。如今，排名前 1000 万的网站中有 [65](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-CSS-Vendor-Prefixes/?wt.mc_id=DX_838516) 到 [70%](http://libscore.com/#libs) 安装了 jQuery。但是它从来没有打算也不能提供什么来解决架构问题:“玩具”语言已经成功了，并且需要很好的技巧。

## 我们到底需要什么？

幸运的是，其他语言已经突破了这一复杂性障碍，并找到了解决方案:[模块化编程](https://en.wikipedia.org/wiki/Modular_programming)。模块鼓励许多最佳实践:

1.  **分离**:代码需要被[分离](https://en.wikipedia.org/wiki/Separation_of_concerns)成更小的块以便于理解。最佳实践建议这些块应该采用文件的形式。
2.  可组合性:您希望代码在一个文件中，但在许多其他文件中重用。这提高了代码库的灵活性。
3.  **依赖管理** : 65%的站点可能安装了 jQuery，但是如果你的产品是一个站点插件，并且需要一个特定的版本呢？如果合适，您希望重用已安装的版本，如果不合适，则加载它。
4.  **名称空间管理**:类似于依赖管理——你能在不重写核心代码的情况下移动文件的位置吗？
5.  **一致执行**:每个人都不应该对同样的问题提出自己的解决方案。

## 早期解决方案

JavaScript 开发人员针对这些问题提出的每个解决方案都对 ES6 模块的结构产生了影响。我们将回顾它们发展过程中的主要里程碑，以及社区在每一步中学到了什么，最终以今天的 ES6 模块的形式展示结果。

1.  **对象文字模式**
2.  **生命/启示模块模式**
3.  **CommonJS**
4.  **AMD**
5.  **UMD**

### 对象文字模式

![Object Literal Pattern](img/1183579d806802573fb30ac0060f62d6.png)

JavaScript 已经为 organization 内置了一个结构:object。对象文字语法被用作组织代码的早期模式:

![Example code 1](img/5252bbb5c6c1c04b83e54a62c2a2b901.png)

```
 <!DOCTYPE html>
<html>
	<head>
		<script src="person.js"></script>
		<script src="author.js"></script>
	</head>
	<body>
		<script>
		    person.author.doJob('ES6 module history');
		</script>
	</body>
	<script>
		// shared scope means other code can inadvertently destroy ours
		var person ='all gone!';
	</script>
</html> 
```

#### 它提供了什么

这种方法的主要好处是易于理解和实现。许多其他方面一点也不容易。

#### 是什么阻碍了它

它依赖于全局范围内的一个变量(person)作为它的根；如果页面上的其他 JS 声明了一个同名的变量，你的代码将会消失得无影无踪！此外，没有可重用性——如果我们想支持一只敲打字机的猴子，我们必须复制 author.js 文件:

![Example code 2](img/c8f2225421d80e9ec69ff52d444333ad.png)

最后，文件加载的顺序非常关键。如果人(或猴子)不存在，所有版本的作者都会出错。

### 生活/启示模块模式

![IIFE revealing module pattern](img/cf0dd0bb029ac573091ed46e9c955f4f.png)

一个 IIFE(根据这个术语的创造者 Ben Alman 的说法，读作“iffy ”)是一个被立即调用的函数表达式。函数表达式是包装在第一组括号中的函数关键字和函数体。第二组参数调用函数，将函数内部的任何内容作为参数传递给函数的参数。通过从函数表达式返回一个对象，我们得到了启示性的模块模式:

![Example code 3](img/1f0408025fb34af99565c5ff71530715.png)

```
 // IIFEPage.html
<!DOCTYPE html>
<html>
	<head>
		<script>
			// set init values
			var outerName ='Ross';
		</script>
		<script src="person.js"></script>
		<script src="monkey.js"></script>
		<script src="author.js"></script>
		<script>
			// we can get rid of person after author has loaded....
			person =undefined;
		</script>
	</head>
	<body>
		<script>
			// ...and author will still work here
			author.doJob('ES6 Module History');
		</script>
	</body>
</html> 
```

#### 它提供了什么

由于封闭，我们在生活中有很多控制权。例如，`innerName`本质上是私有的，因为外部没有任何东西可以访问它——我们选择*通过名为 name 的对象属性来显示*对它的访问。我们有类似构造函数的功能，比如为 person 内部的 name 设置一个默认值。我们可以控制依赖项的导入——如果我们需要重命名外部的东西(通常是 jQuery 的“$”别名),我们可以在参数中使用外部名称，并在内部给它任意命名。最后，结合这些技术允许我们在 author 中实现 decorator 模式，这开始将代码从对传递给它的生物的依赖中分离出来。

#### 是什么阻碍了它

生活/启示模块提供了许多功能，正如我们将看到的，对 AMD 有很大的影响。但是语法很难看，它是一个没有标准的临时黑客，并且它仍然严重依赖于全局范围。

### CommonJS

![CommonJS](img/c2257824ea850f7af7e5737d41349b42.png)

在进化的一个同步分支中，JavaScript 转移到了服务器端。剧透:虽然 Node 赢得了这场战斗，但早期的竞争者很多，包括 Ringo、Narwhal 和 Wakanda。这些开发人员知道客户端 JS 所经历的模块化问题，并希望跳过早期的混乱和低效。所以他们成立了一个工作组来开发服务器端模块的标准。

关于骆驼是由委员会设计的马的老笑话很有趣，因为它是痛苦的事实:委员会做了一些很好的事情，但设计不是其中之一，CommonJS 组不可避免地陷入了 [bikeshedding](https://github.com/deltakosh/interoperable-web-development/) 和其他组反模式的[中。在此期间，Node 使用 CommonJS 的一些思想创建了自己的模块实现。由于 Node 在服务器端的广泛流行，Node 格式被错误地称为 CommonJS，并在今天蓬勃发展。](http://bikeshed.com/#issuecomment-15432598)

作为一个服务器端解决方案，CommonJS 假设一个兼容的脚本加载器是先决条件。这个脚本加载器必须支持名为 require 和 module.export 的函数，这两个函数相互传输模块(还有一个名为“export”的 module.export 的语法快捷方式，我们不在这里讨论)。尽管它从未在浏览器中流行起来，但有几个工具确实支持在浏览器中加载它，所以我们将通过 Browserify 作为一个例子来看预编译。

![Example code 4 - CommonJS](img/6e4c9515d481f297e9852cb6609f3fff.png)

```
 // commonJSPage.html
<!DOCTYPE html>
<html>
	<head>
		<script src="out/bundle.js"></script>
	</head>
	<body>
		<script>
		</script>
	</body>
</html> 
```

#### 它提供了什么

给定加载程序外部需求，语法清晰简洁，并直接影响 ES6 模块语法。此外，模块将变量范围限制在它们自己内部；甚至不再可能声明一个全局的。

#### 是什么阻碍了它

不利的一面是，CJS 在异步环境中表现不佳。所有必需调用都必须在代码继续执行之前执行。这解释了前面的预编译步骤，但它也使“惰性加载”变得非常困难——所有代码都需要在任何执行开始之前加载。在慢速连接或动力不足的设备上表现不佳。

### 超微半导体公司

![AMD](img/ce547e14a48c4513a45e67bc6ea37dd0.png)

具有极大讽刺意味的是，虽然 CommonJS 工作组未能就服务器端标准达成一致，但讨论确实就客户端格式达成了共识。AMD，即异步模块定义，出现在 CommonJS 的讨论中。像 IBM 和 BBC 这样的主要玩家支持 AMD，并且考虑到他们的影响(你开始看到一个主题了吗？)它很快成为被称为前端开发的全新学科的从业者的主导格式。

AMD 对脚本加载器的要求与 CJS 相似，尽管 AMD 只支持名为 define 的方法。define 方法有三个参数:正在定义的模块的名称、正在定义的模块运行所需的依赖项的数组，以及在所有依赖项都可用时执行的函数(该函数按照依赖项声明的顺序接收依赖项作为参数)。只有函数参数是必需的。

同样值得注意的是，虽然官方不鼓励命名模块，并且命名模块本身不会增加任何好处，但不幸的是，它们在实践中很常见。一旦使用了命名模块，就不得不为每个模块设置 baseUrl 和路径，失去了在这个过程中改变代码位置的自由。下面的例子展示了命名模块，但是如果你是从零开始，除非万不得已，不要使用它们(在 AMD 语法中包装 jQuery 和下划线等非 AMD 库可能是有效使用命名模块的候选——更多信息请参见上面的链接)。

AMD 的 *define 方法*对应于 CJS 的 export，但是 AMD 没有指定 import 或 require 方法的等价物。模块内部的依赖关系由*定义*的第二个参数处理，模块外部的加载属于脚本加载器。例如，CurlJS 将他们的加载方法命名为 *curl* 而不是 *require* ，并且两种方法采用不同的参数。

这种格式利用了这样一个事实，即 JavaScript 分两步运行:解析(当代码被解释时，以及当发现语法错误时)，然后执行(当代码运行时，以及当遇到运行时错误时)。解析过程中，变量的目标(比如 author.js 第 2 行的 returnCreature)还不一定要存在；代码只需要语法正确。等待执行函数直到所有依赖项都已加载完毕的责任落在了脚本加载器身上。

![Example code 4 - AMD](img/c4eb279186da9414c18f9546e53cf702.png)

```
 // AMDPage.html
<!DOCTYPE html>
<html>
	<head>
		<script src="script/require.js"></script>
	</head>
	<body>
		<script>
		require.config({
		    baseUrl:"script",
		    paths: {
				"person":"person"
		    }
		});
		require(["author"],function(author) {
		    author.doJob('ES6 Module History');
		});
		</script>
	</body>
</html> 
```

#### 利弊

AMD 统治了很多年，但是严重依赖于一种丑陋的语法，这种语法需要大量的样板标点符号。浏览器所要求的它的异步本质意味着它不能被静态分析，还有其他类似的小原因，它不是所有人的解决方案。

### UMD

![UMD_art_12](img/ccfe991a20250e9823df23fdde60b46a.png)

他们说一个好的妥协是当没有人快乐的时候。通用模块定义试图将 AMD 和 CJS 融合在一起，通常是通过将 CJS 语法包装在 AMD 兼容的包装器中。

#### 利弊

像大多数既有蛋糕又能吃蛋糕的尝试一样，它遭受了父母双方的缺点，却没有提供很多好处。为了历史的完整性，这里提到了它，它确实向同构 JavaScript 的圣杯迈出了第一步，同构 JavaScript 可以在服务器和客户机上运行。

### ES6 模块

![ES6 Modules](img/a7393ac644f9e292150a9d0ab0b8a012.png)

负责 ES6 设计的 TC39 委员会很好地吸取了 AMD 和 CJS 的教训，这是 15 年来语言的最大变化。ES6 模块将最终带来其他语言已经享受多年的对模块化的内置支持，并包括让前端和后端开发人员都满意的建议功能。这是我们利用 ES6 模块的例子。

![Example code 6](img/8735170c90495a86f683db47e2a0b5eb.png)

```
 <!DOCTYPE html>
<html>
	<head>
		<script src="./out/bundle.js"></script>
	</head>
	<body>
		<script>
			console.log('page loaded');
		</script>
	</body>
</html> 
```

只剩下一个小问题:前端生态系统几乎不支持 ES6 模块。没有浏览器本身支持新的导入和导出关键字，或者提议的 HTML5 模块元素。被称为 transpilers 的工具，像 Babel 和 Traceur，可以将 ES6 模块预编译成有效的 ES5 代码，今天的浏览器可以处理这些代码；但是 ES5 必须用异步语法包装，然后由 RequireJS、Browserify 或 SystemJS 之类的脚本加载器处理。

试图通过这两个抽象层(传输和异步加载)传递哪怕是微不足道的 ES6 模块，都会带来实现上的挑战。在编写这个例子时，我遇到了一个运行时(浏览器)错误，require 语句找不到它的依赖项。我知道 Browserify 有时需要点斜杠目录前缀来标识模块('。/author’)，但这破坏了我的 Babel 构建，因为我将它设置为运行 Browserify 包之上的目录。我在一个生产应用程序中遇到过类似的 Babel 和 Webpack 问题。

重点是，这是最前沿的东西。给定时间，实现并不是不可能的，但与更成熟的技术(如 AMD + RequireJS)相比，预计在配置和故障排除上花费更多的时间。

## 结论

JavaScript 模块技术的历史反映了互联网本身的爆炸和演变。正如大型机不再控制无助的终端一样，像标准委员会这样的集中控制组织也不再能发布命令并期望大众默默地服从。个人贡献者为他们认为最重要的问题设计了他们自己的解决方案，而大众的采纳代表了对这些解决方案的投票。

在不同的实现探索了一个想法的排列之后，标准随之而来，以捕捉学习。这种态度——被史蒂夫·乔布斯精辟地总结为“真正的艺术家之船”——体现了平等主义和务实的态度，使现代网络开发如此成功和令人兴奋。

这篇文章是微软技术传道者和 [DevelopIntelligence](http://www.developintelligence.com/) 关于实用 JavaScript 学习、开源项目和互操作性最佳实践的 web 开发系列的一部分，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?wt.mc_id=DX_838516) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?wt.mc_id=DX_838516)。 [DevelopIntelligence](http://www.developintelligence.com/) 为技术团队和组织提供讲师指导的 [JavaScript 培训](http://www.developintelligence.com/catalog/javascript-training)和 [React 培训](http://www.developintelligence.com/catalog/web-development-training/react)。

我们鼓励你在包括微软 Edge(Windows 10 的默认浏览器)在内的各种浏览器和设备上进行测试，在[dev.microsoftedge.com](https://dev.windows.com/en-us/?wt.mc_id=DX_838516)上使用免费工具，包括 [F12 开发者工具](https://developer.microsoft.com/en-us/microsoft-edge/platform/documentation/f12-devtools-guide/?wt.mc_id=DX_838516)——七种不同的、完整记录的工具，帮助你调试、测试和加速你的网页。此外，[访问 Edge 博客](https://blogs.windows.com/msedgedev/?wt.mc_id=DX_838516)，从微软开发者和专家那里获得最新信息。

## 分享这篇文章