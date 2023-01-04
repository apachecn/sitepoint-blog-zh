# 如何使用 Jest 测试 React 组件

> 原文：<https://www.sitepoint.com/test-react-components-jest/>

在本文中，我们将看看如何使用脸书维护的测试框架 [Jest](https://facebook.github.io/jest/) 来测试我们的 [React](https://facebook.github.io/react/index.html) 组件。我们将先看看如何在普通的 JavaScript 函数上使用 Jest，然后再看看它提供的一些现成的特性，这些特性旨在使测试 React 应用程序更容易。

值得注意的是，Jest 并不是专门针对 React 的:您可以用它来测试任何 JavaScript 应用程序。然而，它提供的一些特性对于测试用户界面来说非常方便，这就是它非常适合 React 的原因。

![A Jester Juggling React Icons](img/aabf2381b018217c57013e3f8ba327f2.png)

## 示例应用程序

在我们测试任何东西之前，我们需要一个应用程序来测试！忠于 web 开发传统，我构建了一个小的 todo 应用程序，我们将使用它作为起点。你可以在 GitHub 上找到它，以及我们将要编写的所有测试。如果你想体验一下这个应用，你也可以在网上找到一个[的现场演示。](https://sitepoint-editors.github.io/testing-react-with-jest)

该应用程序是在 ES2015 中编写的，使用带有 Babel ES2015 和 React 预设的 webpack 进行编译。我不会进入构建设置的细节，但如果你想查看的话，这些都在 GitHub repo 中。你可以在自述文件中找到关于如何让应用程序在本地运行的完整说明。如果你想了解更多，这个应用程序是使用 [webpack](https://webpack.github.io/docs/tutorials/getting-started/) 构建的，我推荐“[web pack 初学者指南](https://medium.com/@dabit3/beginner-s-guide-to-webpack-b1f1a3638460#.f6vf3p9ag)”作为对这个工具的一个很好的介绍。

应用程序的入口点是`app/index.js`，它只是将`Todos`组件呈现到 HTML 中:

```
render(
  <Todos />,
  document.getElementById('app')
); 
```

组件是应用程序的主要中枢。它包含所有的状态(这个应用程序的硬编码数据，实际上可能来自 API 或类似的东西)，并有代码来呈现两个子组件:`Todo`，它为状态中的每个 todo 呈现一次，以及`AddTodo`，它呈现一次，并为用户添加新的 todo 提供表单。

因为`Todos`组件包含所有的状态，它需要`Todo`和`AddTodo`组件在任何事情发生变化时通知它。因此，它将函数传递给这些组件，当一些数据改变时，它们可以调用这些组件，并且`Todos`可以相应地更新状态。

最后，现在，您会注意到所有的业务逻辑都包含在`app/state-functions.js`中:

```
export function toggleDone(todos, id) {…}

export function addTodo(todos, todo) {…}

export function deleteTodo(todos, id) {…} 
```

这些都是纯粹的函数，接受状态(对于我们的示例应用程序，这是一个 todos 数组)和一些数据，并返回新的状态。如果你不熟悉纯函数，它们只是引用给定的数据，没有副作用。更多内容，你可以阅读我在*上的文章*关于纯函数的文章和[我在 SitePoint 上关于纯函数和反应的文章](https://www.sitepoint.com/react-higher-order-components/)。

如果你熟悉 Redux，它们与 Redux 所说的缩减器非常相似。事实上，如果这个应用程序变得更大，我会考虑迁移到 Redux，以获得更明确、更结构化的数据方法。但是对于这种规模的应用程序，您经常会发现本地组件状态和一些良好抽象的功能已经足够了。

## 去 TDD 还是不去 TDD？

已经有很多文章讨论了测试驱动开发的优缺点，在编写代码修复测试之前，开发人员应该先编写测试。这背后的想法是，通过首先编写测试，您必须考虑您正在编写的 API，它可以导致更好的设计。我发现这很大程度上取决于个人偏好，也取决于我正在测试的东西。我发现，对于 React 组件，我喜欢先编写组件，然后向最重要的功能添加测试。然而，如果您发现首先为您的组件编写测试符合您的工作流程，那么您应该这样做。这里没有硬性规定；做对你和你的团队最有利的事情。

## 介绍笑话

Jest 于 2014 年首次发布，尽管它最初引起了很多兴趣，但该项目在一段时间内处于休眠状态，并没有得到积极的开发。然而，脸书已经投入了大量的精力来改进 Jest，最近发布了几个版本，这些版本的变化令人印象深刻，值得重新考虑。与最初的开源版本相比，Jest 唯一的相似之处是名称和徽标。其他的都被修改重写了。如果你想了解更多这方面的信息，你可以阅读 [Christoph Pojer 的评论](https://github.com/facebookincubator/create-react-app/pull/250#issuecomment-237098619)，他在那里讨论了该项目的当前状态。

如果你已经因为使用另一个框架来设置 Babel、React 和 JSX 测试而感到沮丧，那么我绝对推荐试试 Jest。如果您发现您现有的测试设置很慢，我也强烈推荐 Jest。它自动地并行运行测试，并且它的监视模式能够只运行与被更改的文件相关的测试，这在您有一个大的测试套件时是非常宝贵的。它配置了 [JSDom](https://github.com/jsdom/jsdom) ，这意味着您可以编写浏览器测试，但通过 Node 运行它们。它可以处理异步测试，并具有内置的高级特性，如模仿、间谍和存根。

## 安装和配置 Jest

首先，我们需要安装 Jest。因为我们也使用巴别塔，我们将安装另外几个模块，使 Jest 和巴别塔开箱即可播放，以及巴别塔和所需的预设:

```
npm install --save-dev jest babel-jest @babel/core @babel/preset-env @babel/preset-react 
```

您还需要有一个配置了 Babel 的`babel.config.js`文件，以使用您需要的任何预设和插件。示例项目已经有了这个文件，如下所示:

```
module.exports = {
  presets: [
    '@babel/preset-env',
    '@babel/preset-react',
  ],
}; 
```

这篇文章不会深入探讨如何建立巴别塔。如果你想详细了解巴别塔，我推荐[巴别塔使用指南](https://babeljs.io/docs/en/usage)。

我们还不会安装任何 React 测试工具，因为我们不会从测试组件开始，而是从测试状态函数开始。

Jest 希望在一个`__tests__`文件夹中找到我们的测试，这已经成为 JavaScript 社区中一个流行的约定，也是我们在这里要坚持的。如果你不是`__tests__`设置的粉丝，开箱即用的 Jest 也支持查找任何`.test.js`和`.spec.js`文件。

因为我们将测试我们的状态函数，所以继续创建`__tests__/state-functions.test.js`。

我们将很快编写一个适当的测试，但现在，放入这个虚拟测试，它将让我们检查一切工作正常，我们已经配置了 Jest:

```
describe('Addition', () => {
  it('knows that 2 and 2 make 4', () => {
    expect(2 + 2).toBe(4);
  });
}); 
```

现在，进入你的`package.json`。我们需要设置`npm test`来运行 Jest，我们可以简单地通过设置`test`脚本来运行`jest`来实现:

```
"scripts": {
  "test": "jest"
} 
```

如果您现在在本地运行`npm test`，您应该会看到您的测试运行并通过！

```
PASS  __tests__/state-functions.test.js
  Addition
    ✓ knows that 2 and 2 make 4 (5ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 passed, 0 total
Time:        3.11s 
```

如果您曾经使用过 Jasmine 或大多数测试框架，那么上面的测试代码应该非常熟悉。Jest 允许我们根据需要使用`describe`和`it`来嵌套测试。使用多少嵌套由您决定。我喜欢嵌套我的字符串，这样传递给`describe`和`it`的所有描述性字符串读起来就像一个句子。

当进行实际的断言时，您将想要测试的东西包装在一个`expect()`调用中，然后在其上调用断言。在这种情况下，我们使用了`toBe`。您可以在 Jest 文档中找到所有可用断言[的列表。`toBe`使用`===`检查给定值是否与测试值匹配。通过本教程，我们将看到 Jest 的一些断言。](https://jestjs.io/docs/en/api)

## 测试业务逻辑

既然我们已经看到 Jest 在虚拟测试中的工作，让我们让它在真实测试中运行吧！我们将测试我们的第一个状态函数，`toggleDone`。`toggleDone`获取当前状态和我们想要切换的 todo 的 ID。每个 todo 都有一个`done`属性，`toggleDone`应该把它从`true`交换到`false`，反之亦然。

*注意:如果你一直这样做，请确保你已经克隆了[回购](https://github.com/sitepoint-editors/testing-react-with-jest)，并将`app`文件夹复制到包含`___tests__`文件夹的同一个目录中。您还需要安装所有应用程序的依赖项(如 React)。一旦你克隆了这个库，你就可以通过运行`npm install`来确保它已经安装好了。*

我将从从`app/state-functions.js`导入函数开始，并设置测试的结构。虽然 Jest 允许你使用`describe`和`it`来嵌套你想要的深度，你也可以使用`test`，它通常会读得更好。`test`只是 Jest 的`it`函数的别名，但有时可以使测试更容易阅读，嵌套更少。

例如，下面是我如何用嵌套的`describe`和`it`调用编写测试:

```
import { toggleDone } from '../app/state-functions';

describe('toggleDone', () => {
  describe('when given an incomplete todo', () => {
    it('marks the todo as completed', () => {
    });
  });
}); 
```

下面是我如何用`test`做到这一点:

```
import { toggleDone } from '../app/state-functions';

test('toggleDone completes an incomplete todo', () => {
}); 
```

测试读起来仍然很好，但是现在有更少的缩进妨碍了测试。这一条主要归结于个人喜好；选择你更喜欢的风格。

现在我们可以写断言了。首先，我们将创建开始状态，然后将它传递给`toggleDone`，以及我们想要切换的 todo 的 ID。`toggleDone`将返回我们的结束状态，然后我们可以断言:

```
import { toggleDone } from "../app/state-functions";

test("tooggleDone completes an incomplete todo", () => {
  const startState = [{ id: 1, done: false, text: "Buy Milk" }];
  const finState = toggleDone(startState, 1);

  expect(finState).toEqual([{ id: 1, done: true, text: "Buy Milk" }]);
}); 
```

请注意，现在我使用`toEqual`来进行断言。你应该在原始值上使用`toBe`,比如字符串和数字，但是在对象和数组上使用`toEqual`。`toEqual`是为了处理数组和对象而构建的，它会递归地检查给定对象中的每个字段或项目，以确保它们匹配。

这样，我们现在可以运行`npm test`并看到我们的状态函数测试通过:

```
PASS  __tests__/state-functions.test.js
  ✓ tooggleDone completes an incomplete todo (9ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 passed, 0 total
Time:        3.166s 
```

## 对更改重新运行测试

对测试文件进行修改，然后不得不再次手动运行`npm test`，这有点令人沮丧。Jest 最好的特性之一是它的监视模式，监视文件的变化并相应地运行测试。它甚至可以根据发生变化的文件来判断运行哪个测试子集。它非常强大和可靠，你可以在手表模式下运行 Jest，然后在你编写代码的时候离开一整天。

要在手表模式下运行它，可以运行`npm test -- --watch`。在第一个`--`之后传递给`npm test`的任何东西都将直接传递给底层命令。这意味着这两个命令实际上是等效的:

*   `npm test -- --watch`
*   `jest --watch`

在本教程的剩余部分，我建议您让 Jest 在另一个标签或终端窗口中运行。

在继续测试 React 组件之前，我们将对另一个状态函数再编写一个测试。在真实的应用程序中，我会编写更多的测试，但是为了本教程的缘故，我将跳过其中的一些。现在，让我们编写一个测试来确保我们的`deleteTodo`函数正常工作。在看到下面我是如何写的之前，试着自己写一下，看看你的测试相比如何。

请记住，您必须更新顶部的`import`语句，以便将`deleteTodo`和`toggleTodo`一起导入:

```
import { toggleDone, deleteTodo } from "../app/state-functions"; 
```

我是这样写测试的:

```
test('deleteTodo deletes the todo it is given', () => {
  const startState = [{ id: 1, done: false, text: 'Buy Milk' }];
  const finState = deleteTodo(startState, 1);

  expect(finState).toEqual([]);
}); 
```

测试与第一次没有太大的不同:我们设置初始状态，运行函数，然后在完成状态下断言。如果您让 Jest 在观察模式下运行，请注意它是如何获取您的新测试并运行它的，以及这样做有多快！这是一个在你写测试时获得即时反馈的好方法。

上面的测试还展示了测试的完美布局，即:

*   建立
*   执行测试中的功能
*   断言结果

通过以这种方式安排测试，您会发现它们更容易理解和使用。

既然我们很高兴测试我们的状态函数，让我们继续反应组件。

## 测试反应组件

值得注意的是，默认情况下，我实际上鼓励您不要在 React 组件上编写太多的测试。任何你想彻底测试的东西，比如业务逻辑，都应该从你的组件中提取出来，放在独立的[函数](https://www.sitepoint.com/javascript-arrow-functions/)中，就像我们之前测试的状态函数一样。也就是说，有时测试一些 React 交互是有用的(例如，确保当用户单击按钮时，使用正确的参数调用特定的函数)。我们将从测试 React 组件呈现正确的数据开始，然后看看测试交互。

为了编写我们的测试，我们将安装 [Enzyme](https://github.com/enzymejs/enzyme) ，这是一个由 Airbnb 编写的包装库，使测试 React 组件变得更加容易。

*注意:自从这篇文章第一次被写出来，React 团队就不再关注酶，而是推荐 [React 测试库(RTL)](https://testing-library.com/docs/react-testing-library/intro) 。这一页值得一读。如果你正在维护一个已经有酶测试的代码库，没有必要放下一切离开，但是对于一个新项目，我建议考虑 RTL。*

除了酶，我们还需要为我们正在使用的 React 的任何版本安装适配器。对于 React v16，这将是`enzyme-adapter-react-16`，但对于 React v17 目前没有官方适配器可用，所以我们将不得不使用[一个非官方版本](https://www.npmjs.com/package/@wojtekmaj/enzyme-adapter-react-17)。*请注意，在官方支持发布之前，该软件包只是权宜之计，届时将被弃用。*

你可以在本期 GitHub 的[中关注正式版的进展。](https://github.com/enzymejs/enzyme/issues/2429)

```
npm install --save-dev enzyme @wojtekmaj/enzyme-adapter-react-17 
```

对于酶，我们需要少量的设置。在项目的根目录下，创建`setup-tests.js`并将这段代码放在那里:

```
import { configure } from 'enzyme';
import Adapter from '@wojtekmaj/enzyme-adapter-react-17';

configure({ adapter: new Adapter() }); 
```

然后，我们需要告诉 Jest 在执行任何测试之前为我们运行这个文件。我们可以通过配置`setupFilesAfterEnv`选项来实现。你可以把 Jest 配置放在它自己的文件中，但是我喜欢使用`package.json`并把东西放在一个`jest`对象中，Jest 也会选择:

```
"jest": {
  "setupFilesAfterEnv": [
    "./setup-tests.js"
  ]
} 
```

现在我们准备写一些测试了！让我们测试一下`Todo`组件在段落中呈现其 todo 的文本。首先我们将创建`__tests__/todo.test.js`，并导入我们的组件:

```
import Todo from '../app/todo';
import React from 'react';
import { mount } from 'enzyme';

test('Todo component renders the text of the todo', () => {
}); 
```

我也从酵素进口`mount`。`mount`函数用于呈现我们的组件，然后允许我们检查输出并对其做出断言。即使我们在 Node 中运行测试，我们仍然可以编写需要 DOM 的测试。这是因为 Jest 配置了 [jsdom](https://github.com/jsdom/jsdom) ，一个在 Node 中实现 dom 的库。这很棒，因为我们可以编写基于 DOM 的测试，而不必每次都打开浏览器进行测试。

我们可以使用`mount`来创建我们的`Todo`:

```
const todo = { id: 1, done: false, name: 'Buy Milk' };
const wrapper = mount(
  <Todo todo={todo} />
); 
```

然后我们可以调用`wrapper.find`，给它一个 CSS 选择器，找到我们期望包含 Todo 文本的段落。这个 API 可能会让您想起 jQuery，这是特意设计的。这是一个非常直观的 API，用于搜索渲染输出以找到匹配的元素。

```
const p = wrapper.find('.toggle-todo'); 
```

最后，我们可以断言其中的文本是`Buy Milk`:

```
expect(p.text()).toBe('Buy Milk'); 
```

这使得我们的整个测试看起来像这样:

```
import Todo from '../app/todo';
import React from 'react';
import { mount } from 'enzyme';

test('TodoComponent renders the text inside it', () => {
  const todo = { id: 1, done: false, name: 'Buy Milk' };
  const wrapper = mount(
    <Todo todo={todo} />
  );
  const p = wrapper.find('.toggle-todo');
  expect(p.text()).toBe('Buy Milk');
}); 
```

现在我们有一个测试来检查我们是否能成功地渲染 todos。

接下来，让我们看看如何使用 Jest 的 spy 功能来断言函数是用特定的参数调用的。这在我们的例子中很有用，因为我们有了作为属性给出两个函数的`Todo`组件，当用户点击一个按钮或执行一个交互时，它应该调用这两个函数。

在这个测试中，我们将断言当 todo 被点击时，组件将调用它被赋予的`doneChange`属性:

```
test('Todo calls doneChange when todo is clicked', () => {
}); 
```

我们希望有一个函数，可以用来跟踪它的调用，以及调用它的参数。然后我们可以检查，当用户点击 todo 时，调用了`doneChange`函数，并且使用了正确的参数。幸运的是，Jest 提供了这种现成的间谍软件。一个**间谍**是一个你不关心其实现的函数；你只关心什么时候怎么叫。把它想象成你在监视这个函数。为了创建一个，我们调用`jest.fn()`:

```
const doneChange = jest.fn(); 
```

这给出了一个函数，我们可以监视它并确保它被正确调用。让我们先用合适的道具来渲染我们的`Todo`:

```
const todo = { id: 1, done: false, name: 'Buy Milk' };
const doneChange = jest.fn();
const wrapper = mount(
  <Todo todo={todo} doneChange={doneChange} />
); 
```

接下来，我们可以再次找到我们的段落，就像之前的测试一样:

```
const p = wrapper.find(".toggle-todo"); 
```

然后我们可以在它上面调用`simulate`来模拟一个用户事件，传递`click`作为参数:

```
p.simulate('click'); 
```

剩下要做的就是断言我们的 spy 函数已经被正确调用。在这种情况下，我们希望用 todo 的 ID 来调用它，即`1`。我们可以使用`expect(doneChange).toBeCalledWith(1)`来断言这一点——这样，我们就完成了测试！

```
test('TodoComponent calls doneChange when todo is clicked', () => {
  const todo = { id: 1, done: false, name: 'Buy Milk' };
  const doneChange = jest.fn();
  const wrapper = mount(
    <Todo todo={todo} doneChange={doneChange} />
  );

  const p = wrapper.find('.toggle-todo');
  p.simulate('click');
  expect(doneChange).toBeCalledWith(1);
}); 
```

## 结论

脸书很久以前发布了 Jest，但最近它被重新拾起并被过度改进。它很快成为 JavaScript 开发人员的最爱，而且只会越来越好。如果你过去尝试过 Jest，但不喜欢它，我不能鼓励你再试一次，因为它现在实际上是一个不同的框架。它很快，在重新运行规范方面很棒，给出了很棒的错误消息，并且有一个很好的表达 API 来编写好的测试。

**如果你喜欢这篇文章，你可能会发现下面的内容很有用:**

*   Cypress 测试:运行 Web 应用程序测试的指南
*   [使用 unittest 和 pytest 进行 Python 单元测试的介绍](https://www.sitepoint.com/python-unit-testing-unittest-pytest/)
*   [使用木偶师学习端到端测试](https://www.sitepoint.com/puppeteer-end-to-end-testing/)
*   [3 种免提连续测试方法](https://www.sitepoint.com/3-methods-for-hands-free-continuous-testing/)
*   [重新介绍 Jenkins:流水线自动化测试](https://www.sitepoint.com/re-introducing-jenkins-automated-testing-with-pipelines/)

## 分享这篇文章