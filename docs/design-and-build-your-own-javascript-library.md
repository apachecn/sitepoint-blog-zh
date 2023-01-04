# 设计和构建你自己的 JavaScript 库:提示和技巧

> 原文：<https://www.sitepoint.com/design-and-build-your-own-javascript-library/>

*这篇文章由[阿德里安·三都](https://www.sitepoint.com/author/asandu)、[维尔丹·索维奇](https://www.sitepoint.com/author/vildansoftic)和[丹·普林斯](https://www.sitepoint.com/author/dprince/)进行了同行评议。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

图书馆:我们一直在使用它们。库是开发人员可以在他们的项目中使用的打包代码，这总是节省工作并防止重复发明轮子。拥有可重用的包，不管是开源的还是闭源的，都比重新构建相同的特性，或者从过去的项目中手工复制粘贴要好。

但是除了打包的代码，库到底是什么？除了少数例外，库应该总是一个文件，或者几个文件放在一个文件夹中。它的代码应该单独维护，并且在您的项目中实现它时应该保持原样。库应该允许您设置特定于项目的配置和/或行为。可以把它想象成一个只允许通过 USB 端口进行通信的 USB 设备。一些设备，如鼠标和键盘，允许通过设备提供的接口进行配置。

在本文中，我将解释库是如何构建的。尽管所涉及的大多数主题也适用于其他语言，但本文主要关注构建 JavaScript 库。

## 为什么要构建自己的 Javascript 库？

首先，库使得现有代码的重用非常方便。不用去挖一个老项目，复制一些文件，直接把库拉进来就行了。这也使你的应用程序碎片化，使应用程序代码库更小，更容易维护。

![Christ Church Library, Oxford](img/8a80a32297056b53022e039adec418e3.png)

Christ Church Library ([source](https://commons.wikimedia.org/wiki/File:Christ_Church_Library,_Oxford-17189963462.jpg))

任何使实现某个目标变得更容易并且可以重用的代码，就像抽象一样，都是被捆绑到库中的候选者。一个有趣的例子是 jQuery。虽然 jQuery 的 API 不仅仅是一个简化的 DOM API，但在几年前，当跨浏览器 DOM 操作相当困难时，它就意味着很多。

如果一个开源项目变得流行，越来越多的开发人员使用它，很可能人们会通过提交问题或贡献代码库来加入并帮助这个项目。无论哪种方式，它都将有益于该库和所有依赖于它的项目。

一个受欢迎的开源项目也能带来巨大的机会。一家公司可能会对你的工作质量印象深刻，并给你一份工作。也许一家公司会要求你帮助将你的项目集成到他们的应用程序中。毕竟没有人比你更了解你的图书馆。

对许多人来说，这仅仅是一种爱好——喜欢写代码，帮助他人，并在这个过程中学习和成长。你可以挑战自己的极限，尝试新事物。

## 范围和目标

在写第一行代码之前，应该清楚你的库的目的是什么——你必须设定目标。有了它们，你可以保持专注于你希望用你的图书馆解决什么问题。请记住，你的库应该比原始形式的问题更容易使用和记忆。API 越简单，用户就越容易学会使用你的库。引用 Unix 哲学:

> 做一件事并把它做好

问问你自己:你的图书馆解决了什么问题？你打算如何解决它？你会自己写所有的东西吗，或者你可以利用别人的库吗？

不管库的大小，尽量做一个路线图。列出你想要的每一个特性，然后尽可能多地删除，直到你拥有一个微小但功能强大的库，很像一个最小可行产品。那将是你的第一次释放。从那里，您可以为每个新特性创建里程碑。本质上，你正在把你的项目分解成小块，让每一个功能都更有成就感，更令人愉快。相信我，这会让你保持清醒。

## API 设计

就个人而言，我真的喜欢从最终用户的角度来看待我的图书馆。你可以称之为以用户为中心的设计。本质上，你正在创建一个你的图书馆的轮廓，希望给它更多的思考，让它对任何选择使用它的人来说更方便。同时，您可以考虑哪些方面应该是可定制的，这将在本文后面讨论。

最终的 API 质量测试是吃你自己的狗粮，在你自己的项目中使用你的库。试着用你的库替换应用程序代码，看看它是否覆盖了你想要的所有特性。通过定制(如本文后面所述)，尽可能保持库的简洁，同时保持足够的灵活性，使其也适用于他们的边缘情况。

下面是一个用户代理字符串库的实现或轮廓的例子:

```
// Start with empty UserAgent string
var userAgent = new UserAgent;

// Create and add first product: EvilCorpBrowser/1.2 (X11; Linux; en-us)
var application = new UserAgent.Product('EvilCorpBrowser', '1.2');
application.setComment('X11', 'Linux', 'en-us');
userAgent.addProduct(application);

// Create and add second product: Blink/20420101
var engine = new UserAgent.Product('Blink', '20420101');
userAgent.addProduct(engine);

// EvilCorpBrowser/1.2 (X11; Linux; en-us) Blink/20420101
userAgent.toString();

// Make some more changes to engine product
engine.setComment('Hello World');

// EvilCorpBrowser/1.2 (X11; Linux; en-us) Blink/20420101 (Hello World)
userAgent.toString(); 
```

根据库的复杂程度，您可能还需要考虑结构化。利用设计模式是构建库，甚至是克服一些技术问题的好方法。它还降低了在添加新功能时重构大型部件的风险。

## 灵活性和定制

让库变得伟大的是灵活性，然而也很难在你能定制什么和不能定制什么之间划一条线。一个完美的例子就是 [chart.js](http://www.chartjs.org/) vs [D3.js](https://d3js.org/) 。两者都是可视化数据的优秀库。Chart.js 使得*真正*容易创建和样式化不同类型的内置图表。但是如果你需要对图形有更多的控制，D3.js 就是你需要的。

将控制权交给用户有多种方式:配置、公开公共方法以及通过回调和事件。

配置库通常是在初始化期间完成的，但是有些库允许您在运行时修改选项。选项通常被限制在很小的范围内，改变这些选项除了更新这些值以备后用之外，不应该做任何事情。

```
// Configure at initialization
var userAgent = new UserAgent({
  commentSeparator: ';'
});

// Run-time configuration using a public method
userAgent.setOption('commentSeparator', '-');

// Run-time configuration using a public property
userAgent.commentSeparator = '-'; 
```

可以公开方法来与实例交互，例如从实例中检索数据(getters)，将数据放入实例中(setters)，以及执行操作。

```
var userAgent = new UserAgent;

// A getter to retrieve comments from all products
userAgent.getComments();

// An action to shuffle the order of all products
userAgent.shuffleProducts(); 
```

回调有时通过公共方法传递，通常是为了在异步任务后运行用户代码。

```
var userAgent = new UserAgent;

userAgent.doAsyncThing(function asyncThingDone() {
  // Run code after async thing is done
}); 
```

事件有很大的潜力。它们类似于回调，除了添加事件处理程序不应该触发动作。事件通常用来表示，你可能已经猜到了，事件！与回调非常相似，您可以提供附加信息并返回一个值供库使用。

```
var userAgent = new UserAgent;

// Validate a product on addition
userAgent.on('product.add', function onProductAdd(e, product) {
  var shouldAddProduct = product.toString().length < 5;

  // Tell the library to add the product or not
  return shouldAddProduct;
}); 
```

在某些情况下，您可能希望允许用户扩展您的库。为此，您可以公开一个用户可以填充的公共方法或属性，就像 Angular modules ( `angular.module('myModule')`)和 jQuery 的`fn` ( `jQuery.fn.myPlugin`)一样，或者什么都不做，只是让用户访问您的库的名称空间:

```
// AngryUserAgent module
// Has access to UserAgent namespace
(function AngryUserAgent(UserAgent) {

  // Create new method .toAngryString()
  UserAgent.prototype.toAngryString = function() {
    return this.toString().toUpperCase();
  };

})(UserAgent);

// Application code
var userAgent = new UserAgent;
// ...

// EVILCORPBROWSER/1.2 (X11; LINUX; EN-US) BLINK/20420101
userAgent.toAngryString(); 
```

类似地，这也允许您覆盖方法。

```
// AngryUserAgent module
(function AngryUserAgent(UserAgent) {

  // Store old .toString() method for later use
  var _toString = UserAgent.prototype.toString;

  // Overwrite .toString()
  UserAgent.prototype.toString = function() {
    return _toString.call(this).toUpperCase();
  };

})(UserAgent);

var userAgent = new UserAgent;
// ...

// EVILCORPBROWSER/1.2 (X11; LINUX; EN-US) BLINK/20420101
userAgent.toString(); 
```

在后者的情况下，让用户访问你的库的名称空间，让你对如何定义扩展/插件的控制更少。为了确保扩展遵循某种约定，您可以(也应该)编写文档。

## 测试

写大纲为[测试驱动开发](https://en.wikipedia.org/wiki/Test-driven_development)开了一个好头。简而言之，就是在编写实际的库之前，以测试的形式写下标准。如果这些测试检查一个特性的行为是否像它应该的那样，并且你在写你的库之前写这些测试，这个策略被称为[行为驱动开发](https://en.wikipedia.org/wiki/Behavior-driven_development)。无论哪种方式，如果您的测试覆盖了库中的每一个特性，并且您的代码通过了所有的测试，那么您就可以放心地假设您的库是有效的。

Jani Hartikainen 在[使用 Mocha 和 Chai](https://www.sitepoint.com/unit-test-javascript-mocha-chai/) 单元测试您的 JavaScript 中解释了如何使用 Mocha 编写单元测试。在[用 Jasmine、Travis 和 Karma](https://www.sitepoint.com/testing-javascript-jasmine-travis-karma/) 测试 JavaScript 中，Tim Evko 展示了如何用另一个叫做 Jasmine 的框架建立一个甜蜜的测试管道。这两个测试框架非常受欢迎，但是还有更多不同风格的测试框架。

我在本文前面创建的大纲已经对预期的输出做了注释。这是所有测试开始的地方:带着期望。我的库的 Jasmine 测试应该是这样的:

```
describe('Basic usage', function () {
  it('should generate a single product', function () {
    // Create a single product
    var product = new UserAgent.Product('EvilCorpBrowser', '1.2');
    product.setComment('X11', 'Linux', 'en-us');

    expect(product.toString())
      .toBe('EvilCorpBrowser/1.2 (X11; Linux; en-us)');
  });

  it('should combine several products', function () {
    var userAgent = new UserAgent;

    // Create and add first product
    var application = new UserAgent.Product('EvilCorpBrowser', '1.2');
    application.setComment('X11', 'Linux', 'en-us');
    userAgent.addProduct(application);

    // Create and add second product
    var engine = new UserAgent.Product('Blink', '20420101');
    userAgent.addProduct(engine);

    expect(userAgent.toString())
      .toBe('EvilCorpBrowser/1.2 (X11; Linux; en-us) Blink/20420101');
  });

  it('should update products correctly', function () {
    var userAgent = new UserAgent;

    // Create and add first product
    var application = new UserAgent.Product('EvilCorpBrowser', '1.2');
    application.setComment('X11', 'Linux', 'en-us');
    userAgent.addProduct(application);

    // Update first product
    application.setComment('X11', 'Linux', 'nl-nl');

    expect(userAgent.toString())
      .toBe('EvilCorpBrowser/1.2 (X11; Linux; nl-nl)');
  });
}); 
```

一旦您对第一个版本的 API 设计完全满意，就该开始考虑架构以及如何使用您的库了。

## 模块加载程序兼容性

您可以使用也可以不使用模块加载器。但是，选择实现您的库的开发人员可能会这样做，所以您需要使您的库与模块加载器兼容。但是哪一个呢？如何在 CommonJS、RequireJS、AMD 和其他之间选择？

其实不用！通用模块定义(UMD)是另一个旨在支持多模块加载器的策略。你可以在网上找到不同风格的代码片段，但你也可以在 UMD GitHub 库上找到变体，使你的库与 UMD 兼容。使用其中一个模板启动你的库，或者[用你最喜欢的构建工具](https://github.com/umdjs/umd#build-tools)添加 UMD，你就不用担心模块加载器了。

如果你希望使用 ES2015 `import` / `export`语法，我强烈建议使用 Babel 结合 [Babel 的 UMD 插件](http://babeljs.io/docs/plugins/transform-es2015-modules-umd/)编译成 ES5。这样，您就可以在项目中使用 ES2015，同时仍能制作适合所有人的资源库。

## 证明文件

我完全赞成对所有项目进行全面的文档记录，但这通常被认为是大量的工作，被推迟并最终被遗忘。

### 基本信息

文档应该总是从基本信息开始，比如项目名称和描述。这将有助于其他人了解您的图书馆是做什么的，以及它对他们来说是否是一个好的选择。

您可以提供额外的信息，如范围和目标，以更好地通知用户，并提供路线图，以便他们知道未来会发生什么或知道如何做出贡献。

### API、教程和示例

当然，你需要让用户知道如何使用你的库。这从 API 文档开始。教程和例子是很好的补充，但是写这些可能是很大的工作量。然而，内联文档却不是。这些注释可以用 [JSDoc](https://github.com/jsdoc3/jsdoc) 解析并转换成文档页面。

### 元任务

一些用户可能想要对您的库进行更改。在大多数情况下，这将是贡献，但有些人可能想创建一个私人使用的自定义构建。对于这些用户来说，包含元任务的文档是很有用的，比如构建库、运行测试、生成、转换或下载数据等的命令列表。

### 贡献

当你开源你的图书馆时，贡献是巨大的。为了指导贡献者，您可以添加文档，在其中解释做出贡献的步骤以及应该满足的标准。这将使你更容易审查和接受贡献，并让他们做得更好。

### 许可证

最后但同样重要的是，包括一个许可证。从技术上来说，如果你选择不包括一个，它仍然是有版权的，但不是每个人都知道这一点。

我发现[ChooseALicense.com](http://choosealicense.com/)是一个不需要成为法律专家就能选择执照的伟大资源。选择许可证后，只需将文本保存在项目根目录下的一个`LICENSE.txt`文件中。

## 包起来加个蝴蝶结

对于一个好的库来说，版本控制是必不可少的。如果你选择做出突破性的改变，用户可能希望继续使用适合他们的版本。

当前版本命名的事实标准是[语义版本化](http://semver.org/)，或 SemVer。SemVer 版本由三个数字组成，每个数字表示一个不同的变化:主要，次要和补丁。

### 向您的 Git 存储库添加版本/发布

如果您有一个 git 存储库，您可以将版本号添加到您的存储库中。您可以将它们视为您的存储库的快照。[标签](https://git-scm.com/book/en/Git-Basics-Tagging)，我们称之为。要创建标签，请打开终端并键入:

```
# git tag -a [version] -m [version message]
git tag -a v1.2.0 -m "Awesome Library v1.2.0" 
```

许多服务，比如 GitHub，会提供你所有版本的概述和下载链接。

### 发布到公共存储库

#### npm

许多编程语言都有包管理器，或者有第三方包管理器。这些允许我们引入专门针对这些语言的库。例如 PHP 的 [Composer](https://www.sitepoint.com/mastering-composer-tips-tricks/) 和 Ruby 的 [RubyGems](https://rubygems.org/) 。

Node.js 是一种独立的 JavaScript 引擎，附带了 [npm](https://www.npmjs.com) 。如果你不熟悉 npm，我们有一个很棒的[初学者指南](https://www.sitepoint.com/beginners-guide-node-package-manager/)。

默认情况下，您的 npm 包将公开发布。不要害怕！还可以发布[私有包](https://www.npmjs.com/npm/private-packages)、[建立私有注册表](https://docs.npmjs.com/misc/registry#can-i-run-my-own-private-registry)或者[完全避免发布](https://docs.npmjs.com/misc/registry#i-dont-want-my-package-published-in-the-official-registry-its-private)。

要发布您的包，您的项目将需要一个`package.json`文件。您可以手动操作，也可以使用交互式向导。要启动向导，请键入:

```
npm init 
```

属性应该匹配你的 git 标签。还有，一定要有一个`README.md`文件。就像 GitHub 一样，npm 将它用于呈现您的包的页面。

之后，您可以通过键入以下命令来发布您的包:

```
npm publish 
```

就是这样！您已经发布了您的 npm 包。

#### 凉亭

几年前，另一个名为 Bower 的软件包管理器浮出水面。然而，这个包管理器不是为特定的语言设计的，而是为特定的平台设计的 web。您可以在那里找到所有主要的前端资产。只有当你的库兼容浏览器时，在 Bower 上发布你的包才是有趣的。

如果你不熟悉 Bower，我们也有一个[初学者指南](https://www.sitepoint.com/package-management-for-the-browser-with-bower/)。

与 npm 非常相似，[您也可以建立一个私有存储库](https://github.com/bower/registry#installation)。您也可以阻止它在向导中完全发布。

有趣的是，在过去的一两年中，许多人似乎正在转换为 npm 的前端资产。虽然 npm 包主要是 JavaScript，但是许多前端包也发布在 npm 上。不管怎样，Bower 仍然很受欢迎，所以我强烈建议您也在 Bower 上发布您的软件包。

我有没有提到 Bower 实际上是一个 npm 模块，最初就是受它的启发？这些命令与 T2 的命令非常相似。要生成一个`bower.json`文件，请键入:

```
bower init 
```

就像`npm init`一样，说明不言自明。最后，要发布您的包:

```
bower register awesomelib https://github.com/you/awesomelib 
```

就像你把你的库放在野外，让每个人在他们的节点项目和/或网络上使用！

## 结论

核心产品是库。确保它解决了一个问题，易用且稳定，你会让你的团队或者很多开发者非常开心。

我提到的许多任务都很容易自动化，例如:运行测试、创建标签、在`package.json`中更新您的版本以及将您的包重新发布到 npm 和 bower。这是您进入持续集成领域并使用 Travis CI 或 Jenkins 等工具的地方。我之前提到的蒂姆·埃夫科的文章就涉及到了这一点。

你建立并出版了一个图书馆吗？请在下面的评论区分享吧！

## 分享这篇文章