# 如何组织一个大型的 React 应用程序并使其具有可伸缩性

> 原文：<https://www.sitepoint.com/organize-large-react-application/>

在本文中，我将讨论我在构建大型 React 应用程序时采用的方法。React 最好的特性之一是它如何避开您的方式，并且在涉及到文件结构时绝不是描述性的。因此，你会在 Stack Overflow 和类似的网站上发现很多关于如何构建应用程序的问题。这是一个非常固执己见的话题，没有一个正确的方法。在本文中，我将向您介绍我在构建 React 应用程序时所做的决定:选择工具、构建文件以及将组件分解成更小的部分。

![An astronaut constructing a space colony in the shape of the React logo](img/37fcca8a80a9e641239cc3db2f559693.png)

## 构建工具和林挺

对于你们中的一些人来说，我是 webpack 的超级粉丝并不奇怪。虽然这是一个复杂的工具，但是团队在第 5 版中所做的大量工作以及新的文档网站使它变得更加容易。一旦你进入 webpack 并在你的头脑中有了这些概念，你真的有难以置信的力量去驾驭。我使用 Babel 来编译我的代码，包括像 JSX 这样的 React 专用转换，以及本地服务我的站点的 [webpack-dev-server](https://github.com/webpack/webpack-dev-server) 。我个人并没有发现热重载给我带来那么多好处，所以我对 webpack-dev-server 及其页面自动刷新非常满意。

我使用 ES 模块，ES2015 中首次引入(通过 Babel 传输)来导入和导出依赖项。这种语法已经存在一段时间了，尽管 webpack 可以支持 CommonJS(也就是节点样式导入)，但对我来说，开始使用最新最棒的是有意义的。此外，webpack 可以[使用 ES2015 模块](https://webpack.js.org/guides/tree-shaking/)从捆绑包中删除死代码，这虽然不完美，但却是一个非常方便的功能，随着社区在 ES2015 中向 npm 发布代码，这一功能将变得更加有益。web 生态系统的大部分已经转向 ES 模块，所以这是我开始的每个新项目的一个显而易见的选择。这也是大多数工具期望支持的，包括其他捆绑器，如 [Rollup](https://rollupjs.org/guide/en/) ，如果你不想使用 webpack 的话。

## 文件夹结构

对于所有的 React 应用程序，没有一个正确的文件夹结构。(与本文的其余部分一样，您应该根据自己的喜好对其进行修改。)但以下是对我有效的方法。

### 代码存在于`src`

为了保持有序，我将所有应用程序代码放在一个名为`src`的文件夹中。这仅包含最终出现在您的最终包中的代码，仅此而已。这很有用，因为你可以告诉 Babel(或任何其他作用于你的应用程序代码的工具)只查看一个目录，并确保它不会处理任何它不需要的代码。其他代码，比如 webpack 配置文件，位于一个适当命名的文件夹中。例如，我的顶层文件夹结构通常包含:

```
- src => app code here
- webpack => webpack configs
- scripts => any build scripts
- tests => any test specific code (API mocks, etc.) 
```

通常，位于顶层的文件只有`index.html`、`package.json`和任何点文件，如`.babelrc`。有些人喜欢在`package.json`中包含 Babel 配置，但是我发现那些文件在有很多依赖项的大型项目中会变得很大，所以我喜欢使用`.eslintrc`、`.babelrc`等等。

## 反应组分

一旦你有了一个`src`文件夹，棘手的是决定如何组织你的组件。过去，我将所有组件放在一个大文件夹中，比如`src/components`，但是我发现在更大的项目中，这变得非常快。

一个常见的趋势是为“智能”和“非智能”组件创建文件夹([也称为“容器”和“表示”组件](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0))，但就我个人而言，我从未发现显式文件夹对我有用。虽然我确实有一些组件可以大致分为“智能”和“非智能”(我将在下面详细讨论)，但是我并没有为每一个组件准备特定的文件夹。

我们根据组件在应用程序中的使用领域对组件进行了分组，并为所有使用的通用组件(按钮、页眉、页脚——通用且可重用的组件)创建了一个`core`文件夹。其余的文件夹映射到应用程序的特定区域。例如，我们有一个名为`cart`的文件夹，其中包含与购物车视图相关的所有组件，还有一个名为`listings`的文件夹，其中包含列出用户可以在页面上购买的东西的代码。

分类到文件夹中也意味着你可以避免在组件前面加上它们所用的应用领域。举个例子，如果我们有一个呈现用户购物车总费用的组件，而不是称它为`CartTotal`，我可能更喜欢用`Total`，因为我是从`cart`文件夹中导入它的:

```
import Total from '../cart/total'
// vs
import CartTotal from '../cart/cart-total' 
```

这是我发现自己有时会打破的一条规则。额外的前缀可以澄清问题，特别是如果您有两到三个名称相似的组件，但是这种技术通常可以避免名称的额外重复。

## 比大写字母更喜欢`jsx`扩展名

很多人在文件中用大写字母命名 React 组件，以区别于普通的 JavaScript 文件。所以在上面的导入中，文件应该是`CartTotal.js`或`Total.js`。我倾向于坚持用破折号作为分隔符的小写文件，所以为了区分，我对 React 组件使用了`.jsx`扩展名。因此，我会坚持使用`cart-total.jsx`。

这有一个小小的额外好处，通过将搜索限制在使用`.jsx`的文件，可以轻松地搜索 React 文件，如果需要，甚至可以将特定的 webpack 插件应用到这些文件。

无论你选择哪种命名约定，重要的是你要坚持它。在你的代码库中有一个约定的组合会很快变成一个噩梦，因为它会越来越大，你必须在其中导航。您可以使用来自 eslint-plugin-react 的规则[来执行这个`.jsx`约定。](https://github.com/yannickcr/eslint-plugin-react/blob/HEAD/docs/rules/jsx-filename-extension.md)

## 每个文件一个 React 组件

遵循前面的规则，我们坚持一个 React 组件文件的惯例，并且组件应该总是默认的导出。

通常我们的 React 文件看起来像这样:

```
import React from 'react'

export default function Total(props) {
  …
} 
```

例如，在我们必须包装组件以便将其连接到 Redux 数据存储的情况下，完全包装的组件成为默认导出:

```
import React, { Component, PropTypes } from 'react'
import { connect } from 'react-redux'

export default function Total(props) {
  …
}

export default connect(() => {…})(Total) 
```

您会注意到我们仍然导出原始组件。这对于测试非常有用，您可以使用“普通”组件，而不必在单元测试中设置 Redux。

通过将组件保持为默认导出，很容易导入组件并知道如何获取它，而不必查找确切的名称。这种方法的一个缺点是，导入人员可以随意命名组件。同样，我们有一个约定:导入应该以文件命名。因此，如果您正在导入`total.jsx`，那么组件应该作为`Total`导入。`user-header.jsx`变成了`UserHeader`，以此类推。

值得注意的是，每个文件一个组件的规则并不总是得到遵守。如果您最终构建了一个小组件来帮助您呈现部分数据，并且它只在一个地方使用，那么将它与使用它的组件放在同一个文件中通常更容易。将组件保存在单独的文件中是有代价的:会有更多的文件、更多的导入，并且作为开发人员通常会有更多的东西要遵循，所以要考虑这样做是否值得。像本文中的大多数建议一样，它们是有例外的规则。

## “智能”和“非智能”反应元件

我简单地提到了“智能”和“非智能”组件的分离，这是我们在代码库中坚持的东西。虽然我们无法通过将它们拆分到文件夹中来识别它，但您可以将我们的应用程序大致分为两种类型的组件:

*   操纵数据、连接 Redux 和处理用户交互的“智能”组件
*   “哑”组件被给予一组道具，并将一些数据呈现到屏幕上

你可以在我的关于 React 中功能性无状态组件的博客文章[中读到更多关于我们如何瞄准“哑”组件的信息。这些组件构成了我们应用程序的大部分，如果可能的话，您应该总是首选这些组件。它们更容易使用，更少错误，更容易测试。](https://www.jackfranklin.co.uk/blog/functional-stateless-components-react/)

即使当我们必须创建“智能”组件时，我们也试图将所有 JavaScript 逻辑保存在自己的文件中。理想情况下，必须处理数据的组件应该将数据交给能够处理数据的 JavaScript。通过这样做，操作代码可以与 React 分开测试，并且在测试 React 组件时可以根据需要模拟它。

## 避免大的`render`方法

虽然这一点过去指的是 React 类组件上定义的`render`方法，但在讨论功能组件时，这一点仍然成立，因为您应该注意呈现异常大的 HTML 片段的组件。

我们努力的一件事是拥有许多小的 React 组件，而不是更少、更大的组件。当你的组件变得太大时，一个很好的指导就是渲染函数的大小。如果它变得笨拙，或者你需要把它分成许多更小的渲染函数，那可能是时候考虑抽象出一个函数了。

这不是一个硬性规定；在推出更多组件之前，您和您的团队需要了解您满意的组件的大小，但是组件的`render`函数的大小是一个很好的衡量标准。你也可以使用道具或物品的数量作为另一个很好的指标。如果一个组件使用了七个不同的道具，这可能是它做得太多的迹象。

## 始终使用`prop-type`

React 允许您使用组件的 [prop-types](https://www.npmjs.com/package/prop-types) 包记录您期望组件给出的属性的名称和类型。

通过声明预期属性的名称和类型，以及它们是否是可选的，您可以更有信心在使用组件时获得正确的属性，并且如果您忘记了属性名称或给定了错误的类型，您可以花费更少的时间进行调试。您可以使用[eslint-plugin-react prop types 规则](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/prop-types.md)来实现这一点。

尽管花时间添加这些组件可能会感觉毫无结果，但当您这样做时，当您开始重用六个月前编写的组件时，您会感谢自己。

## Redux

我们还在许多应用程序中使用 Redux 来管理应用程序中的数据，如何构建 Redux 应用程序是另一个非常常见的问题，有许多不同的观点。

我们的获胜者是 [Ducks](https://github.com/erikras/ducks-modular-redux) ，这是一个将应用程序每个部分的动作、缩减器和动作创建器放在一个文件中的提议。同样，虽然这是一个对我们有用的方法，但选择并坚持一个惯例是这里最重要的事情。

比起拥有`reducers.js`和`actions.js`，其中每一个都包含彼此相关的代码，Ducks 系统认为将相关的代码组合到一个文件中更有意义。假设您有一个带有两个顶级键的 Redux 商店，`user`和`posts`。您的文件夹结构如下所示:

```
ducks
- index.js
- user.js
- posts.js 
```

`index.js`将包含创建主减速器的代码——可能使用 Redux 中的`combineReducers`来实现——在`user.js`和`posts.js`中，您放置所有这些的代码，通常看起来像这样:

```
// user.js

const LOG_IN = 'LOG_IN'

export const logIn = name => ({ type: LOG_IN, name })

export default function reducer(state = {}, action) {
  …
} 
```

这使您不必从不同的文件中导入动作和动作创建者，并使存储中不同部分的代码保持相邻。

## 独立的 JavaScript 模块

虽然本文的重点是 React 组件，但是在构建 React 应用程序时，您会发现自己编写了大量完全独立于 React 的代码。这是我最喜欢这个框架的一点:许多代码完全与组件分离。

任何时候，当您发现您的组件充满了可以从组件中移出的业务逻辑时，我建议您这样做。根据我的经验，我们发现名为`lib`或`services`的文件夹在这里工作得很好。具体的名称并不重要，但是一个装满“非 React 组件”的文件夹才是您真正想要的。

这些服务有时会导出一组函数，有时会导出相关函数的对象。例如，我们有`services/local-storage.js`，它提供了一个围绕本机`window.localStorage` API 的小包装器:

```
// services/local-storage.js

const LocalStorage = {
  get() {},
  set() {},
  …
}

export default LocalStorage 
```

将您的逻辑放在这样的组件之外有一些非常大的好处:

1.  您可以单独测试这段代码，而不需要呈现任何 React 组件
2.  在您的 React 组件中，您可以存根服务行为并返回您想要用于特定测试的数据

## 试验

如上所述，我们非常广泛地测试我们的代码，并且已经开始依赖脸书的 [Jest framework](https://jestjs.io/) 作为这项工作的最佳工具。它非常快，擅长处理大量的测试，可以在观察模式下快速运行并给你快速的反馈，并且附带了一些方便的测试 React 的功能。我之前在 SitePoint】上已经[写了很多关于它的文章，所以这里就不赘述了，但是我会谈谈我们如何构建我们的测试。](https://www.sitepoint.com/test-react-components-jest/)

过去，我致力于拥有一个单独的`tests`文件夹来保存所有的测试。所以如果你有了`src/app/foo.jsx`，你也会有`tests/app/foo.test.jsx`。实际上，随着应用程序变得越来越大，找到正确的文件变得越来越困难，如果你在`src`中移动文件，你经常会忘记在`test`中移动它们，并且结构变得不同步。此外，如果您在`tests`中有一个文件需要导入到`src`中，那么您最终会得到非常长的导入时间。我相信我们都遇到过这种情况:

```
import Foo from '../../../src/app/foo' 
```

如果您更改目录结构，这些很难处理，也很难修复。

相反，将每个测试文件放在源文件旁边可以避免所有这些问题。为了区分它们，我们用`.spec`作为测试的后缀——尽管其他人使用`.test`或简单地使用`-test`——但是它们与源代码共存，在其他方面使用相同的名称:

```
- cart
  - total.jsx
  - total.spec.jsx
- services
  - local-storage.js
  - local-storage.spec.js 
```

随着文件夹结构的改变，很容易移动正确的测试文件，并且当一个文件没有任何测试时，这也是非常明显的，因此您可以发现那些问题并修复它们。

## 结论

剥一只猫的皮有很多种方法，React 也是一样。该框架最好的特性之一是它如何让您围绕工具、构建工具和文件夹结构做出大多数决定，您应该接受这一点。我希望这篇文章能给你一些关于如何开发大型 React 应用程序的想法，但是你应该采纳我的想法，并根据你自己和你的团队的偏好进行调整。

## 分享这篇文章