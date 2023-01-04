# 在 Aurelia 中管理状态:如何在 Redux 中使用 Aurelia

> 原文：<https://www.sitepoint.com/managing-state-aurelia-with-redux/>

![A monitor with two dinosaurs, a rapidly approaching meteor and undo/redo buttons](img/196613b57e5d139c9f5ba2233b470f23.png)

这篇文章由[莫里茨·克罗格](https://www.sitepoint.com/author/morkro)和[杰德·阿扬](https://www.sitepoint.com/author/jahyoung/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！也感谢[西蒙·科灵顿](https://www.sitepoint.com/author/scodrington/)设计了这个演示。

如今，当开发一个 web 应用程序时，很多焦点都放在状态容器上——尤其是各种各样的[流模式](https://facebook.github.io/flux/docs/overview.html)。Flux 最突出的实现之一是 [Redux](http://redux.js.org/) 。对于那些还没有赶上宣传列车的人来说，Redux 是一个帮助您保持状态变化可预测性的库。它将应用程序的整个状态存储在一个对象树中。

在本文中，我们将介绍如何将 Redux 与下一代开源 JavaScript 客户端框架 [Aurelia](http://aurelia.io) 一起使用的基础知识。但是我们不会建立另一个反例，我们会做一些更有趣的事情。我们将构建一个简单的具有撤销和重做功能的 markdown 编辑器。本教程的代码是 GitHub 上的[，这里有一个完成项目的](https://github.com/sitepoint-editors/aurelia-redux)[演示](https://sitepoint-editors.github.io/aurelia-redux/)。

***注**:当学习新的东西时，我更喜欢回到源头，在 Redux 的情况下，有这个[由 Redux 创作者(丹·阿布拉莫夫)制作的令人敬畏的 Egghead 视频系列](https://egghead.io/courses/getting-started-with-redux)。由于我们不会详细介绍 Redux 的工作方式，如果您需要复习，并且有几个小时的空闲时间，我强烈建议尝试一下这个系列。*

## 本教程的结构

在本文中，我将构建同一个组件的三个版本。

第一个版本将使用纯粹的 Aurelia 方法。在这里，您将学习如何设置一个 Aurelia 应用程序，配置依赖关系，并创建必要的视图和视图模型。我们将研究使用双向数据绑定的经典 Aurelia 方式构建示例。

第二个版本将引入 Redux 来处理应用程序状态。我们将使用普通的方法，这意味着没有额外的插件来处理互操作。这样你将学会如何使用 Aurelia 的开箱即用特性来适应 Redux 开发过程。

最终版本将实现撤销/重做功能。任何从零开始构建这种功能的人都知道，这种功能非常容易上手，但事情可能会很快失控。这就是为什么我们将使用 [redux-undo](https://github.com/omnidan/redux-undo) 插件来处理这个问题。

在整篇文章中，你会看到一些官方 Aurelia 文档的参考，帮助你找到更多的信息。所有的代码清单也链接回它们的原始源文件。

所以事不宜迟，让我们开始吧。

## 搭建新的 Aurelia 应用程序

因为我们关注的是与 Aurelia 的交互，所以这个例子是基于 Aurelia 构建应用程序的新的首选方式，Aurelia CLI。

按照 [CLI 文档](http://aurelia.io/hub.html#/doc/article/aurelia/framework/latest/the-aurelia-cli)中说明的步骤，我们使用以下命令全局安装 CLI:

```
npm install aurelia-cli -g 
```

接下来，我们将使用以下内容创建新应用程序:

```
au new aurelia-redux 
```

这将启动一个对话框，询问您是想使用默认设置还是自定义您的选择。选择默认值(ESNext)并选择创建项目并安装依赖项。然后将目录切换到新项目的文件夹中(使用`cd aurelia-redux`)，并使用以下命令启动开发服务器:

```
au run --watch 
```

如果一切都按计划进行，这将启动一个 [BrowserSync](https://www.browsersync.io/) 开发服务器实例，默认监听端口 9000。此外，它将跟踪对您的应用程序所做的更改，并在需要时进行刷新。

## 向捆绑器添加依赖关系

下一步是为我们即将到来的项目安装必要的依赖项。由于 Aurelia CLI 构建在 npm 模块之上，因此我们可以使用以下命令来实现这一点:

```
npm install --save marked redux redux-undo 
```

好的，让我们逐一检查一下。标记的是一个功能齐全、易于使用的 markdown 解析器和编译器，我们将使用它来……嗯，正如它在 tin 上所说的那样。 [Redux](https://www.npmjs.com/package/redux) 是库本身的包， [redux-undo](https://www.npmjs.com/package/redux-undo) 是一个简单的插件，为我们的应用程序的状态容器添加撤销/重做特性。

在幕后，Aurelia CLI 使用 RequireJS，因此所有依赖项都通过异步模块定义(AMD)格式引用。现在剩下的就是告诉 Aurelia 应用程序如何以及在哪里可以找到这些依赖项。

为此，请打开应用程序的`aurelia-project`子文件夹中的`aurelia.json`文件。如果你向下滚动到`bundles`部分，你会看到两个物体。一个用于`app-bundle`，包含您自己的应用程序代码，随后是`vendor-bundle`，用于将您的应用程序的所有依赖项捆绑到一个单独的捆绑文件中。那个对象包含一个名为`dependencies`的属性，你猜对了，这就是我们要添加额外属性的地方。

> 手动操作文件`aurelia.json`,目前是一个必要的步骤，但是在未来的版本中会自动进行。

注册自定义依赖项有多种方法，最好通过遵循各自的[官方 Aurelia 文档](http://aurelia.io/hub.html#/doc/article/aurelia/framework/latest/the-aurelia-cli/6)来理解。我们要添加的是下面的代码:

```
// file: aurelia_project/aurelia.json

...
{
  "name": "text",
  "path": "../scripts/text"
},
// START OF NEW DEPENDENCIES, DON'T COPY THIS LINE
{
  "name": "marked",
  "path": "../node_modules/marked",
  "main": "marked.min"
},
{
  "name": "redux",
  "path": "../node_modules/redux/dist",
  "main": "redux.min"
},
{
  "name": "redux-undo",
  "path": "../node_modules/redux-undo/lib",
  "main": "index"
},
// END OF NEW DEPENDENCIES, DON'T COPY THIS LINE
{
  "name": "aurelia-templating-resources",
  "path": "../node_modules/aurelia-templating-resources/dist/amd",
  "main": "aurelia-templating-resources"
},
... 
```

[连接应用依赖关系](https://github.com/sitepoint-editors/aurelia-redux/blob/master/aurelia_project/aurelia.json)

现在一切都设置好了，您应该继续并重新启动 CLI 观察器，以正确捆绑您新安装的供应商依赖项。请记住，我们使用以下命令来完成此操作:

```
au run --watch 
```

就这样，现在我们准备用一些代码来弄脏我们的手。

## 添加一些样式

没有像样的样式，任何 markdown 编辑器都是不完整的。我们将从在根文件夹的`index.html`中包含一个外观时尚的字体开始。

```
<head>
  <title>Aurelia MarkDown Editor</title>
  <link href="https://fonts.googleapis.com/css?family=Passion+One:400,700|Roboto:300,400,500,700"
        rel="stylesheet" type="text/css">
</head> 
```

之后我们会给`/src/styles.css`添加一些样式。与其在这里列出所有的 CSS，我建议你看看 GitHub 上的 [CSS 文件，并在你自己的项目中使用这些样式。](https://github.com/sitepoint-editors/aurelia-redux/blob/master/src/styles.css)

## 用奥雷利亚的方式

我们将首先创建一个名为`<markdown-aurelia>`的[新定制元素](http://aurelia.io/hub.html#/doc/article/aurelia/framework/latest/creating-components)，作为我们的逻辑容器。我们遵循 Aurelia 的默认惯例，在`src`文件夹中创建一个视图模型`markdown-aurelia.js`和一个视图`markdown-aurelia.html`。

> 约定是强大的，但有时可能不适合您的应用程序。请注意，您可以根据需要按照这些指令覆盖它们

现在让我们来看看新组件的视图。Aurelia 组件视图包含在一个`<template>`标签中，因此我们所有的标记都应该嵌套在这个标签中。

我们首先需要我们的 CSS 文件。然后，在标题之后，我们使用一个`<div>`来容纳一个`<textarea>`，它将作为我们的编辑器面板，以及第二个`<div>`，它将显示编译后的结果。这些元素的`value`和`innerHTML`属性使用 [Aurelia 的 bind 命令](http://aurelia.io/hub.html#/doc/article/aurelia/binding/latest/binding-basics)绑定到 ViewModel 上的两个属性。

对于编辑器窗格，我们绑定到 ViewModel 上的`raw`属性。Aurelia 将默认使用双向绑定，因为它是一个表单控件。

对于预览`<div>`，我们绑定到`innerHTML`属性。我们这样做(而不是简单的`${html}`插值)是为了使结果 HTML 呈现为 HTML 而不是字符串。在这种情况下，Aurelia 将选择使用单向绑定，因为它在元素上没有看到 [contenteditable 属性](http://aurelia.io/hub.html#/doc/article/aurelia/binding/latest/binding-basics/6)，因此不希望用户在这里输入。

```
// file: src/markdown-aurelia.html

<template>
  <require from="./styles.css"></require>

  <h1>Aurelia Markdown Redux</h1>

  <div class="markdown-editor">
    <textarea class="editor" value.bind="raw"></textarea>
    <div class="preview" innerHTML.bind="html"></div>
  </div>
</template> 
```

[Markdown View，奥瑞利亚之路](https://github.com/sitepoint-editors/aurelia-redux/blob/master/src/markdown-aurelia.html)

> 哇…不亚于/Sass/Compass/无论什么…当然在 Aurelia 中有很多方式来设计组件。[看看这里](http://aurelia.io/hub.html#/doc/article/aurelia/framework/latest/the-aurelia-cli/8)看看你有哪些选择。

实际上没有更多的东西，所以让我们看看 ViewModel，老实说，它也一样短。这里，我们从导入`marked`依赖关系开始。你还记得我们之前用`aurelia.json`做的接线过程吗？所有这些都是为了允许这种 ES6 风格的外部模块导入。另外，我们导入了`bindable`装饰器。

按照 Aurelia 的约定，ViewModel 是一个简单的 ES6 类，使用文件名的大写字母命名。现在我们将使用 ES7 风格的装饰器将这个类(`raw`)上的一个属性声明为[可绑定的](http://aurelia.io/hub.html#/doc/article/aurelia/templating/latest/templating-html-behaviors-introduction/6)。我们需要这样做，因为我们使用这个属性将信息传递到组件中(通过`<textarea>`)。

之后，我们定义一个`html`属性来保存编译后的降价。最后，我们定义了一个`rawChanged`函数，每当`raw`绑定的值改变时，这个函数就会被触发。它接受`newValue`作为参数，该参数可以用作之前导入的`marked`函数的输入。这个函数的返回值被分配给组件的`html`属性。

```
// file: src/markdown-aurelia.js

import marked from 'marked';
import { bindable } from 'aurelia-framework';

export class MarkdownAurelia {
  @bindable raw;
  html = '';

  rawChanged(newValue) {
    this.html = marked(newValue);
  }
} 
```

[Markdown ViewModel，奥瑞利亚方式](https://github.com/sitepoint-editors/aurelia-redux/blob/master/src/markdown-aurelia.js)

在我们可以使用新组件之前，唯一要做的就是在某个地方渲染它。我们将在应用程序的`root`组件中执行此操作，因此打开文件`src/app.html`并用以下内容替换其内容:

```
// file: src/app.html

<template>
  <require from="./markdown-aurelia"></require>
  <markdown-aurelia raw.bind="data"></markdown-aurelia>
</template> 
```

[使用降价组件](https://github.com/sitepoint-editors/aurelia-redux/blob/master/src/app.html)

我们在这里做的是[通过使用`<require>`标签将组件](http://aurelia.io/hub.html#/doc/article/aurelia/templating/latest/templating-html-behaviors-introduction/4)导入到视图中。属性指定 Aurelia 应该在哪里寻找组件。

之后，我们渲染`<markdown-aurelia>`组件并将`data`属性绑定到我们的`raw`属性，这将作为组件的初始值。

我们在`app.js`文件中定义这个`data`属性，对应于`App`组件视图的视图模型。

```
// file: src/app.js

export class App {
  constructor() {
    this.data = 'Hello World!';
  }
} 
```

[设置默认降价数据](https://github.com/sitepoint-editors/aurelia-redux/blob/master/src/app.js)

瞧啊！我们有一个工作的 markdown 编辑器！

## 将 Redux 引入堆栈

Redux 可以用[三个关键原则](http://redux.js.org/docs/introduction/ThreePrinciples.html)来描述。第一个原则是*真理的单一来源*。这就是用一个地方来存储应用程序状态，即一个 JavaScript 对象(也称为状态树)。第二个原则是状态*是只读的*。这保证了国家本身不能被修改，而必须被完全取代。第三个原则是，这些变化应该使用纯函数进行*。这意味着没有副作用，我们应该总是能够以同样的方式重建一个状态。*

还有三个基本的实体，贯穿于每个 Redux 应用程序:*动作*、*减速器*和*商店*。动作是你在任何时候想要改变状态时调度的东西。它是一个普通的 JavaScript 对象，用尽可能少的术语描述变化。Reducers 是纯粹的函数，它获取应用程序的状态和正在调度的动作，并返回应用程序的下一个状态。最后，存储保存状态对象，它允许您调度操作。当你创建它的时候，你需要给它传递一个 reducer，指定状态如何被更新。

这就是我想给你的概述。如果你需要复习，请参考 egghead.io 上的官方 Redux 文档或丹·阿布拉莫夫的视频课程。我也可以在 SitePoint 上强烈推荐莫里茨·克鲁格的视频课程。

现在，事不宜迟，让我们看看 Redux 方式的 Markdown 视图模型。

### Redux 方式

让我们从在我们的`src`文件夹中创建新文件`markdown-redux.html`和`markdown-redux.js`开始。在这两个文件中，我们可以简单地复制现有的 Aurelia 代码，并在接下来的步骤中向它们添加额外的 Redux 部分。

从 ViewModel 开始，我们首先导入`createStore`函数，然后在我们的类声明中使用它来初始化存储。我们向存储传递对我们的 reducer 函数(`textUpdater`)的引用，并将其分配给我们的类的`store`属性。请注意，为了简单起见，这个例子将 reducer 和 action creator 保存在与 ViewModel 相同的文件中。

下一个变化发生在构造函数内部，我们使用`subscribe`函数注册一个`update`回调，Redux store 将在任何时候调用一个动作。您可以看到，我们利用了[绑定方法](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)将正确的执行上下文传递给回调。这个回调将负责呈现所有未来状态。

`update`方法本身只是使用 Redux 的`getState`方法从商店请求最新状态，并将结果值赋给我们的`html`和`raw`属性。

为了响应用户输入，我们创建了一个`keyupHandler`方法，该方法将`newValue`作为单个参数接受。这里我们来到 Redux 哲学的一个关键部分——触发状态变化的唯一方法是分派一个动作。因此，这是我们的处理程序唯一要做的事情:分派一个新的`updateText`动作，该动作接收`newValue`作为参数。

到目前为止还好吗？我们快到了。但是由于组件将使用一些默认文本进行初始化—还记得 raw 属性吗？—我们还需要确保初始值得到渲染。为此，一旦组件被附加到 DOM，我们可以利用 Aurelia 的[生命周期挂钩附加的](http://aurelia.io/hub.html#/doc/article/aurelia/framework/latest/creating-components/3)来调用`keyupHandler`。

```
// file: src/markdown-redux.js

import marked from 'marked';
import { bindable } from 'aurelia-framework';
import { createStore } from 'redux';

export class MarkdownRedux {
  @bindable raw;
  html = '';
  store = createStore(textUpdater);

  constructor() {
    this.store.subscribe(this.update.bind(this));
  }

  update() {
    const state = this.store.getState();
    this.html = state.html;
    this.raw = state.raw;
  }

  keyupHandler(newValue) {
    this.store.dispatch(updateText(newValue));
  }

  attached() {
    this.keyupHandler(this.raw);
  }
} 
```

[降价组件 Redux Way-ViewModel](https://github.com/sitepoint-editors/aurelia-redux/blob/master/src/markdown-redux.js)

## 添加动作创建者和缩减者

除了 ViewModel 更新之外，我们还需要看看 action 和 reducer。请记住，Redux 本质上就是一组函数，因此，我们唯一的动作将由一个`updateText`函数创建。它接受要转换成 HTML 的`text`，根据 Redux 原理，它封装在一个具有`TEXT_UPDATE`的`type`属性的对象中。使用 ES6 [简写属性名语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer)来指定`text`属性。

因为我们的例子需要一个缩减器，`textUpdater`作为根缩减器。如果没有提供，默认状态是一个具有空的`raw`和`html`属性的对象，使用 [ES6 默认值语法](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Functions/Default_parameters)指定。然后，缩减器检查`action`类型，如果没有找到匹配，或者返回新的状态，这是一个好的做法。

```
// file: src/markdown-redux.js

const TEXT_UPDATE = 'UPDATE';

// action creator
const updateText = (text) => {
  return {
    type: TEXT_UPDATE,
    text
  };
};

// reducer
function textUpdater(state = { raw: '', html: '' }, action) {
  switch (action.type) {
  case TEXT_UPDATE:
    return {
      raw: action.text,
      html: marked(action.text)
    };
  default:
    return state;
  }
} 
```

[减速组件-动作/减速器](https://github.com/sitepoint-editors/aurelia-redux/blob/master/src/markdown-redux.js)

## 更新视图

现在，如果我们看看 ViewModel 的变化带来了什么，我们会注意到对组件的更新要么局限于初始化器(为`raw`属性提供初始值的`App`组件)，要么局限于`update`方法。这与 Aurelia 的双向绑定相反，后者允许您在标记内以声明的方式更改值。

下面是我们如何修改视图以符合新的范例。我们将对文本区域的`value`属性使用`one-way`绑定，而不是利用 Aurelia 的`bind`关键字。通过这种方式，我们覆盖了默认的双向绑定行为，并强制执行从 ViewModel 到视图的单向更新过程。

为了捕获用户输入，我们还需要挂接`keyup`事件，这可以通过`trigger`绑定来实现。无论何时按下一个键，都应该调用`keyupHandler`并传递`<textarea>`的值。我们使用特殊的`$event`属性来访问原生 DOM 事件，并从那里获得`target`的值。最后但同样重要的是，我们不希望在每次击键时重新呈现，而是在用户停止输入后。我们可以通过使用 Aurelia 的[去抖绑定行为](http://aurelia.io/hub.html#/doc/article/aurelia/binding/latest/binding-binding-behaviors/3)来做到这一点。

> 我们也可以使用`delegate`来代替`trigger`。想了解区别吗？看一看[这里](http://aurelia.io/hub.html#/doc/article/aurelia/binding/latest/binding-delegate-vs-trigger/1)

```
// file: src/markdown-redux.html

<template>
  <require from="./styles.css"></require>

  <h1>Aurelia Markdown Redux</h1>

  <div class="markdown-editor cf">
    <textarea class="editor"
              keyup.trigger="keyupHandler($event.target.value) & debounce"
              value.one-way="raw"></textarea>
    <div class="preview" innerHTML.bind="html"></div>
  </div>
</template> 
```

[Redux 方式的降价组件-视图](https://github.com/sitepoint-editors/aurelia-redux/blob/master/src/markdown-redux.html)

最后，不要忘记更新`app.html`来实例化新组件

```
// file: src/app.html

<template>
  <require from="./markdown-redux"></require>
  <markdown-redux raw.bind="data"></markdown-redux>
</template> 
```

[更新 App.html 以渲染 Redux-Component](https://github.com/sitepoint-editors/aurelia-redux/blob/master/src/app.html)

## 实施撤消/重做

到目前为止，我们刚刚修改了我们最初的 Aurelia 组件，以利用 Redux 工作流。说实话，现在还没有太大的好处。我们为什么要做这一切？使用纯 Aurelia 方法也可以实现单点更新。事实证明，再一次，它的所有功能，使这种方法有意义。在下一步中，我们将了解如何为组件添加撤销和重做功能，以处理状态随时间的变化，并在这些变化之间来回导航。

让我们从在我们的`src`文件夹中创建新文件`markdown.html`和`markdown.js`开始。同样，在这两个文件中，我们可以简单地复制现有的 Aurelia 代码，并在接下来的步骤中向它们添加额外的代码。

这一次，我们将反其道而行之，先看看风景。这里，我们在我们的`markdown-editor`部分之上添加了一个新的`<div>`元素。在这个元素中，我们放置了两个按钮作为撤销和重做触发器。我们还想在相应的按钮中呈现先前状态(`pastCount`)和未来状态(`futureCount`)的数量。我们将使用简单的插值来实现这一点。

```
// file: src/markdown.html
<template>
  <require from="./styles.css"></require>

  <h1>Aurelia Markdown Redux</h1>

  <div class="toolbar">
    <button click.trigger="undo()">(${pastCount}) Undo</button>
    <button click.trigger="redo()">Redo (${futureCount})</button>
  </div>

  <div class="markdown-editor cf">
    ...
  </div>
</template> 
```

[带撤销/重做功能的降价组件–视图](https://github.com/sitepoint-editors/aurelia-redux/blob/master/src/markdown.html)

现在是时候看看 ViewModel 中的变化了。动作创建器和缩减器保持不变，但是新的是从 redux-undo 模块引入了`undoable`函数和`ActionCreators`函数。注意，`undoable`函数是默认导出的，所以我们可以去掉花括号。我们使用这个函数来包装我们的`textUpdater`减速器函数，并将其传递给`createStore`。这是使我们的存储能够处理撤销和重做功能所需要的。

除此之外，我们还引入了`pastCount`和`futureCount`属性，我们将其初始化为零。查看`update`方法，我们现在可以看到默认的`getState`方法不是返回状态，而是返回一个具有`present`、`past`和`future`状态的对象。我们使用`present`状态为我们的`html`和`raw`属性分配新值。由于`past`和`future`是状态数组，我们可以简单地利用它们的`length`属性来更新我们的计数。最后但同样重要的是，`undo`和`redo`方法现在分派新的动作，由`ActionCreators`对象自动添加。

```
// file: src/markdown.js

import marked from 'marked';
import { bindable } from 'aurelia-framework';
import { createStore } from 'redux';
import undoable from 'redux-undo';
import { ActionCreators } from 'redux-undo';

export class Markdown {
  @bindable raw;
  html = '';
  store = createStore(undoable(textUpdater));
  pastCount = 0;
  futureCount = 0;

  constructor() {
    ...
  }

  update() {
    const state = this.store.getState().present;
    this.html = state.html;
    this.raw = state.raw;
    this.pastCount = this.store.getState().past.length;
    this.futureCount = this.store.getState().future.length;
  }

  keyupHandler(newValue) {
    ...
  }

  undo() {
    this.store.dispatch(ActionCreators.undo());
  }

  redo() {
    this.store.dispatch(ActionCreators.redo());
  }

  attached() {
    ...
  }
} 
```

[带撤销/恢复的降价组件–视图模型](https://github.com/sitepoint-editors/aurelia-redux/blob/master/src/markdown.js)

再次更新`app.html`来实例化组件的最终版本。

```
// file: src/app.html
<template>
  <require from="./markdown"></require>
  <markdown raw.bind="data"></markdown>
</template> 
```

[更新 App.html 以渲染 Redux-Component](https://github.com/sitepoint-editors/aurelia-redux/blob/master/src/app.html)

这正是我们所需要的。这一切之所以如此容易，是因为我们遵循了 Redux 提出的标准工作流程。

## 结论

Redux 架构围绕着一个严格的单向数据流。这有很多好处，但也有代价。如果您将最初的 Aurelia 方式与第一次 Redux 重写进行比较，您会发现其中包含了更多的样板文件。当然，有抽象和更好的集成可用，如 aurelia-redux-plugin (它添加了另一种带有调度器和选择器装饰器的很酷的方法)，但最终这要么是更多代码的问题，要么是需要学习更多东西的问题。

我发现在评估新概念时，最重要的是真正理解它们是如何工作的。只有到那时，你才能真正决定复杂性和稳定性之间的权衡是否适合你。就我个人而言，我喜欢将我的应用程序看作一组状态的想法，我非常高兴看到一个简单的开箱即用的集成(甚至更深入的集成，如上面提到的插件)。

我希望您喜欢这个例子，并且现在有了一个更好的想法，如何将您现有的 Redux 技能应用到 Aurelia，或者借用一些想法并将其应用到您的默认开发方法中。让我们通过官方 [Aurelia Gitter 频道](https://gitter.im/aurelia/Discuss)或者在下面的评论中了解它。

## 分享这篇文章