# 你好，世界！你的第一个 JavaScript 程序

> 原文：<https://www.sitepoint.com/hello-world-your-first-javascript-programs/>

![](img/d6a976a369d55eb138aee7375b5d594d.png)

以下是我们新书的一小段摘录， [JavaScript:新手到忍者，第二版](https://www.sitepoint.com/premium/books/javascript-novice-to-ninja-2nd-edition)，作者是达伦·琼斯。这是 JavaScript 的终极初学者指南。SitePoint Premium 会员可以通过他们的会员身份访问，或者您可以在世界各地的商店购买一份。

学习编程语言时，以“你好，世界！”开始是一个传统程序。这是一个简单的程序，输出短语“Hello world！”宣布你来到了编程的世界。我们将坚持这一传统，用 JavaScript 编写这类程序。这将是一个记录短语“你好，世界！”到控制台。

要开始，你需要打开你的首选控制台(节点 REPL、浏览器控制台或 web 上的 [ES6 控制台](http://es6console.co))。控制台打开后，您只需输入以下代码:

```
console.log('Hello world!'); 
```

然后按*键进入*。如果一切按计划进行，您应该会看到“Hello，world！”已显示；类似于下面的截图。

![](img/843e6dcaef7d22ef170a21776bf577e6.png)

祝贺你，你已经写了你的第一个 JavaScript 程序！这看起来可能没什么，但是一位智者曾经说过，每个忍者程序员的旅程都是从一行代码开始的(或者类似的东西！).

## 浏览器中的 JavaScript

JavaScript 是一种解释型语言，需要一个宿主环境才能运行。由于它的起源，JavaScript 运行的主要环境是浏览器，尽管它可以在其他环境中运行；例如，我们刚刚编写的第一个程序运行在节点 REPL 中。节点还可以用于在服务器上运行 JavaScript。到目前为止，JavaScript 最常见的用途仍然是使网页具有交互性。正因为如此，在我们继续深入之前，我们应该先看看网页是由什么组成的。

## 网络的三层

几乎所有的网页都是由三种关键成分组成的――HTML、CSS 和 JavaScript。HTML 用于标记内容。CSS 是表示层，JavaScript 增加了交互性。

每一层都建立在最后一层之上。一个网页应该只需要 HTML 层就能运行――事实上，许多网站庆祝“T0”裸奔日,他们从网站上移除了 CSS 层。一个只使用 HTML 层的网站将会是最纯粹的形式，看起来很老派，但仍然应该是功能齐全的。

#### 保持这些层分开

将每一层的关注点分开被广泛认为是最佳实践，因此每一层只负责一件事。将它们放在一起会导致非常复杂的页面，其中所有的代码都混在一个文件中，导致“标签汤”或“代码面条”。这曾经是制作网站的标准方式，现在网上还有很多这样做的例子。

## 不引人注目的 JavaScript

最初使用 JavaScript 时，它被设计为直接插入到 HTML 代码中，正如在这个示例中可以看到的，当单击按钮时会显示一条消息:

```
<button id='button' href='#' onclick='alert("Hello World")'>Click Me</a>
```

这使得很难看到发生了什么，因为 JavaScript 代码与 HTML 混淆了。这也意味着代码与 HTML 紧密耦合，因此 HTML 中的任何更改都需要 JavaScript 代码也进行更改以防止中断。

通过将 JavaScript 代码放在它自己的`<script>`标签中，可以使它远离 HTML 的其余部分。下面的代码将获得与上面相同的结果:

```
<script> const btn = document.getElementById('button');
btn.addEventListener('click', function() {
    alert('Hello World!');
}); </script>
```

这样更好，因为所有的 JavaScript 都在一个地方，在两个脚本标记之间，而不是与 HTML 标记混合在一起。

我们可以更进一步，将 JavaScript 代码与 HTML 和 CSS 完全分开，放在它自己的文件中。这可以链接到使用`script`标签中的`src`属性来指定要链接到的文件:

```
<script src='main.js'></script> 
```

JavaScript 代码将被放在与 HTML 文档相同目录下的一个名为`main.js`的文件中。保持 JavaScript 代码完全独立的概念是[不引人注目的 JavaScript](https://en.wikipedia.org/wiki/Unobtrusive_JavaScript) 的核心原则之一。

以类似的方式，CSS 也应该保存在一个单独的文件中，因此网页中唯一的代码就是链接到 CSS 和 JavaScript 文件的实际 HTML。这通常被认为是最佳实践，也是我们将在本书中使用的方法。

#### 自动结束标签

如果您使用过 XML 或 XHTML，您可能会遇到自结束标记，如以下脚本标记:

```
<script src='main.js' /> 
```

这些将无法在 HTML5 中工作，所以应该避免。

您可能会看到一些使用 language 属性的遗留代码:

```
<script src='main.js' language='javascript'></script>
```

在 HTML5 中这是不必要的，但它仍然可以工作。

## 优雅的退化和渐进的增强

**适度降级**是指构建一个网站的过程，使其在使用 JavaScript 的现代浏览器中工作得最好，但在较旧的浏览器中仍能达到合理的标准，或者 JavaScript 或其某些功能不可用。这方面的一个例子是以高清晰度(HD)播放的节目――它们在 HD 电视上效果最好，但在标准电视上仍然有效；只是画面质量会差一些。这些程序甚至可以在黑白电视上运行。

**渐进式增强**是指从底层开始构建一个具有基本功能的网页，然后添加额外的增强功能(如果浏览器中有这些功能的话)的过程。如果您遵循三层原则，这应该是很自然的，JavaScript 层增强了 web 页面，而不是作为页面不可或缺的基本元素。例如，电话公司提供基本的电话服务，但如果您的电话支持的话，还会提供呼叫等待和来电显示等额外服务。

每当你在网页上添加 JavaScript 时，你都应该考虑你想采用的方法。你想从大量突破界限的惊人效果开始，然后确保那些可能没有最新最好浏览器的人的体验优雅地降级吗？或者，您是想开始构建一个能在大多数浏览器上运行的功能性网站，然后使用 JavaScript 增强体验？这两种方法相似，但有细微的不同。

## 您的第二个 JavaScript 程序

我们将用第二个将在浏览器中运行的 JavaScript 程序来结束这一章。这个例子比上一个例子更复杂，并且包含了很多概念，这些概念将在后面的章节中更深入地讨论，所以如果您在这个阶段没有理解所有的内容，请不要担心！这个想法是向您展示 JavaScript 的能力，并介绍一些将在接下来的章节中涉及的重要概念。

我们将遵循前面提到的不引人注目的 JavaScript 实践，并将我们的 JavaScript 代码保存在一个单独的文件中。首先创建一个名为`rainbow`的文件夹。在这个文件夹中创建一个名为`rainbow.html`的文件和一个名为`main.js`的文件。

让我们从 HTML 开始。打开`rainbow.html`并输入以下代码:

```
<head>
<meta charset='utf-8'>
<title>I Can Click A Rainbow</title>
</head>
<body>
<button id='button'>click me</button>
<script src='main.js'></script>
</body>
</html> 
```

这个文件是一个相当标准的 HTML5 页面，包含一个 ID 为`button`的按钮。ID 属性对于 JavaScript 来说非常有用，可以作为钩子来访问页面的不同元素。底部是一个`script`标签，链接到我们的 JavaScript 文件。

现在来看看 JavaScript。打开`main.js`并输入以下代码:

```
const btn = document.getElementById('button');

const rainbow = ['red','orange','yellow','green','blue','rebeccapurple','violet'];

function change() {      
document.body.style.background = rainbow[Math.floor(7*Math.random())];
}
btn.addEventListener('click', change); 
```

我们在 JavaScript 代码中的第一个任务是创建一个名为`btn`的变量(我们将在第 2 章讨论变量)。

然后我们使用`document.getElementById`函数来查找 ID 为`btn`的 HTML 元素(查找 HTML 元素在第 6 章中介绍)。然后将其赋给`btn`变量。

我们现在创建另一个名为`rainbow`的变量。然后，一个包含不同颜色字符串列表的数组被分配给`rainbow`变量(我们在第 2 章讨论字符串和变量，在第 3 章讨论数组)。

然后我们创建一个名为`change`的函数(我们将在第 4 章介绍函数)。这将 body 元素的背景颜色设置为彩虹的颜色之一(改变页面的样式将在第 6 章中讨论)。这包括使用内置的`Math`对象选择一个随机数(在第 5 章中介绍)并从`rainbow`数组中选择相应的颜色。

最后，我们创建一个*事件处理程序*，它检查按钮何时被点击。当这发生时，它调用我们刚刚定义的`change`函数(事件处理程序在第 7 章中讨论)。

在你最喜欢的浏览器中打开`rainbow.html`，试着点击几次按钮。如果一切正常，背景应该变成彩虹的每一种颜色，如下图所示。

![](img/c8806c95d867cd4180e6cb00aba36fb9.png)

如果你想快速尝试一下，你可以在 [CodePen](http://codepen.io/daz4126/pen/VPRdGa) 上检查代码。为了获得一些实践经验，我建议您也花时间创建这些文件，手动编写代码，并尝试在您的浏览器中运行它。

## 不要破坏网页

JavaScript 语言开发中的一个重要概念是它必须向后兼容。也就是说，当运行新规范的引擎解释所有旧代码时，它们必须以相同的方式工作(这有点像说 PlayStation 4 必须仍然能够运行为 PlayStation 1、2 和 3 创建的游戏)。这是为了防止 JavaScript 通过剧烈的改变“破坏网络”,这意味着一些网站上的遗留代码不能在现代浏览器中正常运行。

所以新版本的 JavaScript 不能做以前版本语言不可能做的事情。所改变的只是用于实现特定特性的符号，以使其更易于编写。这被称为*语法糖*，因为它允许以更好、更简洁的方式编写现有代码。

事实上，所有版本的 JavaScript 都是向后兼容的，这意味着我们可以使用 **transpilers** 将代码从一个版本的 JavaScript 转换成另一个版本。例如，您可以使用最新版本的 JavaScript 编写代码，然后将其转换为第 5 版代码，这几乎可以在任何浏览器中工作。

每年一个新版本的 ECMAScript 意味着在实现最新功能时，浏览器可能总是会稍微慢一点(他们在这方面越来越快，但大多数浏览器仍然需要两年时间来支持 ES6 模块)。这意味着如果你想使用最新的编码技术，在某些时候你可能不得不依赖于使用 transpiler，比如 [Babel](https://babeljs.io) 。

如果您发现某些代码在浏览器中不起作用，您可以将以下链接添加到您的 HTML 文档中:

```
<script src='https://unpkg.com/babel-standalone@6/babel.min.js'></script>
```

注意，这个链接需要放在任何需要传输的 JavaScript 之前。

您还必须在 JavaScript 文件的任何链接中将`type`属性更改为`text/babel`。例如，上例中指向 JavaScript 文件的链接将变为:

```
<script type='text/babel' src='main.js'></script>
```

这不是最好的长期解决方案，因为它要求浏览器在运行时传输所有的代码，尽管这对于代码实验来说很好。一个更好的解决方案是作为构建过程的一部分来传输你的代码，这将在第 15 章中讨论。

一些在线编辑器如 [CodePen](http://codepen.io) 、[巴别塔 REPL](https://babeljs.io/repl) 和 [JS Fiddle](https://jsfiddle.net) 允许你在浏览器中传输代码。ECMAScript 6 兼容性表也包含了关于各种 transpilers 中已经实现的特性的最新信息。

## 分享这篇文章