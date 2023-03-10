# 解开意大利面条式代码:如何编写可维护的 JavaScript

> 原文：<https://www.sitepoint.com/write-maintainable-javascript/>

*这篇文章由[汤姆·格列柯](https://www.sitepoint.com/author/tgreco/)、[丹·普林斯](https://www.sitepoint.com/author/dprince/)和[亚菲·伯哈努](https://www.sitepoint.com/author/yberhanu/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

几乎每个开发人员都有维护或接管遗留项目的经历。或者，也许这是一个被重新拾起的老项目。常见的第一想法是扔掉代码库，从头开始。代码可能很乱，没有文档记录，而且要花几天时间才能完全理解所有的东西。但是，有了适当的计划、分析和良好的工作流程，就有可能将一个意大利面条式的代码库变成一个干净的、有组织的、可伸缩的代码库。

我不得不接管和清理许多项目。我白手起家的还不多。事实上，我现在正在这么做。我学到了很多关于 JavaScript 的知识，保持代码库有条理，最重要的是，不要对以前的开发者生气。在这篇文章中，我想告诉你我的步骤，告诉你我的经历。

## 分析项目

第一步是对正在发生的事情有一个总体的了解。如果是一个网站，点击所有的功能:打开模态，发送表单等等。在这样做的时候，打开开发人员工具，看看是否有任何错误出现或者记录了什么。如果是 Node.js 项目，打开[命令行界面](https://en.wikipedia.org/wiki/Command-line_interface)，通过 API。在最好的情况下，项目有一个入口点(例如，`main.js`、`index.js`、`app.js`、…)，在这里所有的模块被初始化，或者在最坏的情况下，整个业务逻辑被定位。

找出正在使用的工具。 [jQuery](https://jquery.com/) ？[反应过来](https://facebook.github.io/react/)？[快递](http://expressjs.com/)？列出所有重要的事情。假设这个项目是用 [Angular 2](https://angular.io/) 编写的，而你没有使用过它，那么直接进入文档，获得一个基本的理解。搜索最佳实践。

### 在更高的层次上理解项目

了解这些技术是一个好的开始，但是要获得真正的感受和理解，是时候研究一下 [**单元测试**](https://en.wikipedia.org/wiki/Unit_testing) 了。单元测试是测试功能和代码方法的一种方式，以确保代码的行为符合预期。阅读——和运行——单元测试比仅仅阅读代码给你更深刻的理解。如果你的项目中没有单元测试，不要担心，我们将会谈到这一点。

## 创建基线

这一切都是为了建立**一致性**。既然您已经有了关于项目工具链的所有信息，您知道了结构以及逻辑是如何连接的，那么是时候创建基线了。我建议添加一个 [`.editorconfig`](http://editorconfig.org/) 文件，以保持不同编辑器、IDE 和开发人员之间的编码风格指南一致。

### 连贯压痕

著名的问题(这是一场*战争*)，是否应该使用空格或制表符，并不重要。代码库是用空格写的吗？继续空格。带标签？使用它们。只有当代码库有混合缩进时，才需要决定使用哪一种。观点是好的，但是一个好的项目可以确保所有的开发人员都可以毫无争议地工作。

为什么这很重要？每个人都有自己使用编辑器或 IDE 的方式。例如，我是[代码折叠](https://en.wikipedia.org/wiki/Code_folding)的超级粉丝。如果没有这个功能，我真的会迷失在文件中。当缩进不一致时，这个特性就失效了。所以每次我打开一个文件，我都必须在开始工作之前修复缩进。这是对时间的巨大浪费。

```
// While this is valid JavaScript, the block can't
// be properly folded due to its mixed indentation.
 function foo (data) {
  let property = String(data);

if (property === 'bar') {
   property = doSomething(property);
  }
  //... more logic.
 }

// Correct indentation makes the code block foldable,
// enabling a better experience and clean codebase.
function foo (data) {
 let property = String(data);

 if (property === 'bar') {
  property = doSomething(property);
 }
 //... more logic.
} 
```

### 命名

确保遵守项目中使用的命名约定。 [CamelCase](https://en.wikipedia.org/wiki/CamelCase) 常用于 JavaScript 代码中，但我见过很多混合约定。例如，jQuery 项目经常混合命名 jQuery 对象变量和其他变量。

```
// Inconsistent naming makes it harder
// to scan and understand the code. It can also
// lead to false expectations.
const $element = $('.element');

function _privateMethod () {
  const self = $(this);
  const _internalElement = $('.internal-element');
  let $data = element.data('foo');
  //... more logic.
}

// This is much easier and faster to understand.
const $element = $('.element');

function _privateMethod () {
  const $this = $(this);
  const $internalElement = $('.internal-element');
  let elementData = $element.data('foo');
  //... more logic.
} 
```

### 林挺一切

虽然前面的步骤是装饰性的，主要是为了帮助更快地扫描代码，但是这里我们介绍并确保公共的最佳实践以及代码质量。ESLint 、 [JSLint](http://www.jslint.com/) 和 [JSHint](http://jshint.com/) 是目前最流行的 JavaScript linters。就我个人而言，我以前经常使用 JSHint，但 ESLint 已经开始成为我的最爱，主要是因为它的自定义规则和早期的 ES2015 支持。

当你启动林挺的时候，如果弹出很多错误，修复它们！在你的棉绒高兴之前，不要继续做别的事情！

### 更新相关性

更新依赖关系应该小心谨慎。如果没有注意到依赖关系所经历的变化，很容易引入更多的错误。一些项目可能使用固定版本(例如`v1.12.5`)，而其他项目使用通配符版本(例如`v1.12.x`)。如果您需要快速更新，版本号的构造如下:`MAJOR.MINOR.PATCH`。如果你不熟悉[语义版本](http://semver.org/)是如何工作的，我推荐你阅读 Tim Oxley 的[这篇文章](https://nodesource.com/blog/semver-a-primer/)。

没有更新依赖关系的通用规则。每个项目都是不同的，应该这样处理。更新您的依赖项的`PATCH`数量根本不应该是一个问题，并且`MINOR`通常也很好。只有当您增加了依赖项的`MAJOR`数量时，您才应该查找到底发生了什么变化。也许 API 已经完全改变了，你需要重写应用程序的大部分。如果这不值得努力，我会避免更新到下一个主要版本。

如果你的项目使用 [npm](https://www.npmjs.com/) 作为依赖项管理器(并且没有任何竞争对手)，你可以从你的 CLI 使用方便的`npm outdated`命令检查任何过时的依赖项。让我用我的一个名为 [FrontBook](https://github.com/morkro/FrontBook) 的项目中的一个例子来说明这一点，在那里我经常更新所有的依赖项:

![Image of npm outdated](img/f05c74b18e5fffff617c0697a9c82735.png)

正如你所看到的，我这里有很多重大更新。我不会一次更新所有内容，而是一次更新一个。当然，这将花费大量的时间，但是这是确保没有任何事情中断的唯一方法(如果项目没有任何测试的话)。

## 让我们把手弄脏吧

我想让你记住的主要信息是，清理并不一定意味着删除和重写大部分代码。当然，这有时是唯一的解决办法，但它不应该是你的第一步，也是唯一的一步。JavaScript 可能是一种奇怪的语言，因此给出通用的建议通常是不可能的。你总是要评估你的具体情况，找出一个可行的解决方案。

### 建立单元测试

进行单元测试可以确保您理解代码是如何工作的，并且您不会意外地破坏任何东西。JavaScript 单元测试值得单独撰写文章，所以我在这里就不多赘述了。广泛使用的框架有[卡玛](http://karma-runner.github.io/0.13/index.html)、[茉莉](http://jasmine.github.io/)、[摩卡](http://mochajs.org/)或[艾娃](https://github.com/sindresorhus/ava)。如果你也想测试你的用户界面， [Nightwatch.js](http://nightwatchjs.org/) 和 [DalekJS](http://dalekjs.com/) 是推荐的浏览器自动化工具。

单元测试和浏览器自动化的区别在于，前者测试的是 JavaScript 代码本身。它确保您的所有模块和一般逻辑按预期工作。另一方面，浏览器自动化测试项目的表面(用户界面)，确保元素在正确的位置并按预期工作。

在开始重构任何东西之前，先处理好单元测试。你的项目稳定性会提高，你甚至还没有考虑到可扩展性！一个很大的副作用是不要一直担心你可能打碎了什么东西而没有注意到。

丽贝卡·墨菲写了一篇关于为现有的 JavaScript 编写单元测试的优秀文章。

### 体系结构

JavaScript 架构是另一个巨大的话题。重构和清理架构归结于你有多少这样做的经验。在软件开发中，我们有许多不同的设计模式，但就可伸缩性而言，并不是所有的模式都适合。不幸的是，我无法涵盖本文中的所有情况，但至少可以给你一些一般性的建议。

首先，您应该弄清楚哪些设计模式已经在您的项目中使用了。仔细阅读这个模式，并确保它是一致的。可伸缩性的关键之一是坚持模式，而不是混合方法。当然，你可以在你的项目中为不同的目的使用不同的设计模式(例如，使用[单例模式](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#singletonpatternjavascript)用于数据结构或短命名空间助手函数，使用[观察者模式](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#observerpatternjavascript)用于你的模块),但是永远不要用一种模式写一个模块，用不同的模式写另一个模块。

如果你的项目中没有真正的架构(也许所有的东西都在一个巨大的`app.js`中)，是时候改变这种情况了。不要一蹴而就，要一点一点来。同样，没有通用的做事方法，每个项目设置都是不同的。文件夹结构因项目而异，取决于项目的大小和复杂程度。通常，在一个非常基本的层面上，这个结构被分成第三方库、模块、数据和一个入口点(例如`index.js`、`main.js`)，所有的模块和逻辑都在这个入口点被初始化。

这让我想到了**模块化**。

### 模块化一切？

模块化并不是 JavaScript 可伸缩性这个大问题的答案。它增加了开发人员必须熟悉的另一层 API。尽管如此，这也是值得的。原则是把你所有的功能分成小模块。通过这样做，解决代码中的问题和在同一个代码库的团队中工作变得更加容易。每个模块都应该有一个明确的目的和任务。模块不知道应用程序的外部逻辑，可以在不同的位置和情况下重用。

如何将一个大的功能分解成许多紧密相连的逻辑？让我们一起努力。

```
// This example uses the Fetch API to request an API. Let's assume
// that it returns a JSON file with some basic content. We then create a
// new element, count all characters from some fictional content
// and insert it somewhere in your UI.
fetch('https://api.somewebsite.io/post/61454e0126ebb8a2e85d', { method: 'GET' })
  .then(response => {
    if (response.status === 200) {
      return response.json();
    }
  })
  .then(json => {
    if (json) {
      Object.keys(json).forEach(key => {
        const item = json[key];
        const count = item.content.trim().replace(/\s+/gi, '').length;
        const el = ` <div class="foo-${item.className}">
            <p>Total characters: ${count}</p>
          </div> `;
        const wrapper = document.querySelector('.info-element');

        wrapper.innerHTML = el;
      });
    }
  })
  .catch(error => console.error(error)); 
```

这不是很模块化。一切都紧密相连，并依赖于其他部分。想象一下更大、更复杂的函数，你必须调试它，因为有东西坏了。也许 API 没有响应，或者 JSON 内部发生了变化。一场噩梦，不是吗？

让我们划分不同的职责:

```
// In the previous example we had a function that counted
// the characters of a string. Let's turn that into a module.
function countCharacters (text) {
  const removeWhitespace = /\s+/gi;
  return text.trim().replace(removeWhitespace, '').length;
}

// The part where we had a string with some markup in it,
// is also a proper module now. We use the DOM API to create
// the HTML, instead of inserting it with a string.
function createWrapperElement (cssClass, content) {
  const className = cssClass || 'default';
  const wrapperElement = document.createElement('div');
  const textElement = document.createElement('p');
  const textNode = document.createTextNode(`Total characters: ${content}`);

  wrapperElement.classList.add(className);
  textElement.appendChild(textNode);
  wrapperElement.appendChild(textElement);

  return wrapperElement;
}

// The anonymous function from the .forEach() method,
// should also be its own module.
function appendCharacterCount (config) {
  const wordCount = countCharacters(config.content);
  const wrapperElement = createWrapperElement(config.className, wordCount);
  const infoElement = document.querySelector('.info-element');

  infoElement.appendChild(wrapperElement);
} 
```

好了，我们现在有三个新模块。让我们看看重构后的`fetch`调用。

```
fetch('https://api.somewebsite.io/post/61454e0126ebb8a2e85d', { method: 'GET' })
  .then(response => {
    if (response.status === 200) {
      return response.json();
    }
  })
  .then(json => {
    if (json) {
      Object.keys(json).forEach(key => appendCharacterCount(json[key]))
    }
  })
  .catch(error => console.error(error)); 
```

我们也可以从`.then()`方法中提取逻辑并分离出来，但是我想我已经展示了模块化的含义。

### 如果`!modularization`还有什么？

正如我已经提到的，将你的代码库转换成微小的模块增加了另一层 API。如果您不希望这样，但是希望让其他开发人员更容易地使用您的代码，那么保持函数更大是绝对没问题的。您仍然可以将代码分解成更简单的部分，并将更多的精力放在可测试的代码上。

### 记录您的代码

文档是一个经常讨论的话题。编程社区的一部分人主张记录一切，而另一部分人认为[自记录](http://stackoverflow.com/questions/209015/what-is-self-documenting-code-and-can-it-replace-well-documented-code)代码是正确的做法。就像生活中的大多数事情一样，我认为两者的良好平衡使得代码具有可读性和可伸缩性。使用 [JSDoc](http://usejsdoc.org/) 作为你的文档。

JSDoc 是 JavaScript 的 API 文档生成器。它通常作为插件提供给所有知名的编辑器和 IDE。让我们看一个例子:

```
function properties (name, obj = {}) {
  if (!name) return;
  const arr = [];

  Object.keys(obj).forEach(key => {
    if (arr.indexOf(obj[key][name]) <= -1) {
      arr.push(obj[key][name]);
    }
  });

  return arr;
} 
```

这个函数接受两个参数并遍历一个对象，然后返回一个数组。这可能不是一个过于复杂的方法，但对于没有编写过代码的人来说，可能需要一段时间才能弄清楚发生了什么。此外，这个方法做什么并不明显。让我们开始记录:

```
/**
 * Iterates over an object, pushes all properties matching 'name' into
 * a new array, but only once per occurance.
 * @param  {String}  propertyName - Name of the property you want
 * @param  {Object}  obj          - The object you want to iterate over
 * @return {Array}
 */
function getArrayOfProperties (propertyName, obj = {}) {
  if (!propertyName) return;
  const properties = [];
  Object.keys(obj).forEach(child => {
    if (properties.indexOf(obj[child][propertyName]) <= -1) {
      properties.push(obj[child][propertyName]);
    }
  });
  return properties;
} 
```

代码本身我没怎么接触过。仅仅通过重命名函数和添加一个简短而详细的注释块，我们就提高了可读性。

### 拥有有组织的提交工作流

重构本身就是一项巨大的任务。为了能够总是回滚您的更改(以防您破坏了某些东西，后来才注意到)，我建议提交您所做的每个更新。重写了一个方法？`git commit`(或者`svn commit`，如果你和 SVN 一起工作的话)。重命名一个名称空间、文件夹或一些图像？`git commit`。你明白了。对一些人来说，这可能很乏味，但它确实能帮助你适当地清理并变得有条理。

为整个重构工作创建一个新的分支。永远不要在主人身上工作！您可能必须进行快速更改或者将错误修复上传到生产环境中，并且您不想在测试和完成之前部署您的(可能未测试的)代码。因此，建议总是在不同的分支上工作。

如果你需要简短的更新所有这些是如何工作的，GitHub 有一个关于版本控制工作流程的[有趣的指南](https://guides.github.com/introduction/flow/)。

## 如何不失去理智

除了清理所需的所有技术步骤外，还有一个我很少在任何地方看到提及的重要步骤:不要生前任开发人员的气。当然，这并不适用于所有人，但我知道有些人会经历这种情况。我花了很多年才真正理解这一点并克服它。我曾经对以前的开发人员的代码、他们的解决方案以及为什么一切如此混乱非常恼火。

最终，所有的消极都没能让我有所作为。这只会导致你进行不必要的重构，浪费你的时间，甚至可能破坏东西。这只会让你越来越烦。你可能会花费额外的时间，但是没有人会感谢你重写一个已经工作的模块。不值得。做需要做的事情，分析形势。每次回到一个模块，你总是可以重构微小的部分。

代码以这种方式编写总是有原因的。可能之前的开发者只是没有足够的时间做好，没有更好的了解，或者别的什么。我们都经历过。

## 包装它

让我们再回顾一遍所有的步骤，为你的下一个项目创建一个清单。

1.  **分析项目**

*   暂时放下你的开发者帽子，做一个用户来看看它到底是怎么回事。
*   浏览代码库，列出正在使用的工具。
*   阅读文档和工具的最佳实践。
*   通过单元测试，在更高的层次上感受项目。

1.  **创建基线**

*   引入 [`.editorconfig`](http://editorconfig.org/) 来保持不同 ide 之间编码风格指南的一致。
*   使缩进一致；制表符或空格都没关系。
*   实施命名约定。
*   如果还没有，添加一个 linter，如 [ESLint](http://eslint.org/) 、 [JSLint](http://www.jslint.com/) 或 [JSHint](http://jshint.com/) 。
*   更新依赖项，但要明智地做，并注意到底更新了什么。

1.  **清理**

*   使用类似于 [Karma](http://karma-runner.github.io/0.13/index.html) 、 [Jasmine](http://jasmine.github.io/) 或 [Nightwatch.js](http://nightwatchjs.org/) 的工具建立单元测试和浏览器自动化。
*   确保架构和设计模式是一致的。
*   不要混淆[设计模式](https://addyosmani.com/resources/essentialjsdesignpatterns/book/)，坚持已经存在的模式。
*   决定是否要将代码库分成模块。每个都应该只有一个目的，并且不知道你的代码库逻辑的其余部分。
*   如果你不想这么做，那就多关注于可测试的代码，并把它分解成更简单的块。
*   用恰当命名的函数以平衡的方式记录你的函数和代码。
*   使用 [JSDoc](http://usejsdoc.org/) 为您的 JavaScript 生成文档。
*   定期提交并在重要变更后提交。如果有东西坏了，就更容易回去了。

1.  不要失去理智

*   不要对之前的开发者发火；消极只会导致不必要的重构和浪费时间。
*   代码被写成这样是有原因的。记住，我们都经历过。

真的希望这篇文章对你有帮助。如果你在任何步骤上有困难，请告诉我，或者给我一些我没有提到的好建议！

## 分享这篇文章