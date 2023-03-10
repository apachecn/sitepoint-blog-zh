# 如何用 React 钩子和上下文 API 替换 Redux

> 原文：<https://www.sitepoint.com/replace-redux-react-hooks-context-api/>

在 React 中处理共享应用程序状态的最流行的方法是使用一个框架，比如 Redux。最近，React 团队引入了几个新特性，包括 [React 钩子](https://reactjs.org/docs/hooks-intro.html)和[上下文 API](https://reactjs.org/docs/context.html) 。这两个特性有效地消除了大型 React 项目开发人员一直面临的许多挑战。最大的问题之一是“支柱钻孔”，这在嵌套组件中很常见。解决方案是使用像 Redux 这样的状态管理库。不幸的是，这是以编写样板代码为代价的。但是现在可以用 React 钩子和上下文 API 替换 Redux。

在本教程中，您将学习一种在 React 项目中处理状态的新方法，而无需编写过多的代码或安装一堆库 Redux 就是这种情况。React 挂钩允许您在函数组件内部使用本地状态，而上下文 API 允许您与其他组件共享状态。

## 先决条件

为了跟随本教程，您需要熟悉以下主题:

*   [反应](https://www.sitepoint.com/getting-started-react-beginners-guide/)
*   [反应钩子](https://www.sitepoint.com/react-hooks/)
*   [还原](https://www.sitepoint.com/getting-started-redux/)

您将在这里学习的技术基于 Redux 中引入的模式。这意味着您需要在继续之前对`reducers`和`actions`有一个牢固的理解。我目前使用的是 [Visual Studio 代码](https://code.visualstudio.com/)，这似乎是目前最流行的代码编辑器(尤其是对 JavaScript 开发人员而言)。如果你用的是 Windows，我会推荐你安装 [Git Bash](https://gitforwindows.org/) 。使用 Git Bash 终端执行本教程中提供的所有命令。Cmder 也是一个很好的终端，能够在 Windows 上执行大多数 Linux 命令。

您可以在这个 [GitHub 资源库](https://github.com/sitepoint-editors/react-hooks-context-app)访问本教程中使用的完整项目。

## 关于新的状态管理技术

在 React 项目中，我们需要处理两种类型的状态:

*   地方州
*   全球状态

局部状态只能在定义它们的组件中使用。全局状态可以在多个组件之间共享。以前，定义一个全局状态需要安装一个状态管理框架，如 [Redux](https://redux.js.org/) 或 [MobX](https://mobx.js.org/) 。随着 [React v16.3.0](https://reactjs.org/blog/2018/03/29/react-v-16-3.html) 发布了**上下文 API** ，开发者无需安装额外的库就可以实现全局状态。

截至 [React v16.8](https://reactjs.org/blog/2019/02/06/react-v16.8.0.html) ，**钩子**已经允许在一个组件中实现许多 React 特性，而无需编写一个类。钩子给 React 开发人员编写代码的方式带来了巨大的好处。这包括代码重用和组件间共享状态的更简单的方法。对于本教程，我们将关注以下 React 挂钩:

*   [使用状态](https://reactjs.org/docs/hooks-reference.html#usestate)
*   [useReducer](https://reactjs.org/docs/hooks-reference.html#usereducer)

建议使用`useState`来处理简单值，如数字或字符串。然而，当处理复杂的数据结构时，您将需要`useReducer`钩子。对于`useState`，你只需要一个`setValue()`函数来覆盖现有的状态值。

对于`useReducer`，您将处理一个状态对象，该对象包含树状结构中不同数据类型的多个值。您需要声明可以改变一个或多个状态值的函数。对于数组这样的数据类型，您需要声明多个不可变函数[来处理添加、更新和删除操作。在本教程的后面部分，您将看到一个这样的例子。](https://www.sitepoint.com/immutability-javascript/)

一旦使用`useState`或`useReducer`声明了状态，就需要使用 React 上下文将其提升为全局状态。这是通过使用 React 库提供的`createContext`函数创建一个**上下文对象**来完成的。上下文对象允许在不使用 props 的情况下在组件之间共享状态。

您还需要为您的上下文对象声明一个**上下文提供者**。这允许页面或容器组件订阅您的上下文对象进行更改。容器的任何子组件都可以使用`useContext`函数访问上下文对象。

现在让我们来看看运行中的代码。

## 设置项目

我们将使用 [create-react-app](https://github.com/facebook/create-react-app) 快速启动我们的项目:

```
$ npx create-react-app react-hooks-context-app 
```

接下来，让我们安装[语义 UI React](https://react.semantic-ui.com/) ，一个基于 React 的 CSS 框架。这不是一个要求；我只是喜欢在不编写定制 CSS 的情况下创建漂亮的用户界面:

```
yarn add semantic-ui-react fomantic-ui-css 
```

打开`src/index.js`并插入以下导入:

```
import 'fomantic-ui-css/semantic.min.css'; 
```

这就是我们的项目开始使用语义 UI 所需要做的一切。在下一节中，我们将看看如何使用`useState`钩子声明一个状态，并将其提升到全局状态。

## 反例:使用状态

对于这个例子，我们将构建一个简单的计数器演示程序，由一个两按钮组件和一个显示组件组成。我们将引入一个在两个组件之间全局共享的`count`状态。组件将是`CounterView`的子组件，它将作为容器。按钮组件将有增加或减少`count`状态值的按钮。

让我们从在名为`context/counter-context.js`的上下文文件中定义我们的`count`状态开始。在`src`文件夹中创建并插入以下代码:

```
import React, { useState, createContext } from "react";

// Create Context Object
export const CounterContext = createContext();

// Create a provider for components to consume and subscribe to changes
export const CounterContextProvider = props => {
  const [count, setCount] = useState(0);

  return (
    <CounterContext.Provider value={[count, setCount]}>  {props.children}  </CounterContext.Provider>
  );
}; 
```

我们已经定义了一个名为`count`的状态，并将默认值设置为`0`。所有使用`CounterContext.Provider`的组件都可以访问`count`状态和`setCount`功能。让我们定义在`src/components/counter-display.js`中显示`count`状态的组件:

```
import React, { useContext } from "react";
import { Statistic } from "semantic-ui-react";
import { CounterContext } from "../context/counter-context";

export default function CounterDisplay() {
  const [count] = useContext(CounterContext);

  return (
    <Statistic>  <Statistic.Value>{count}</Statistic.Value>  <Statistic.Label>Counter</Statistic.Label>  </Statistic>
  );
} 
```

接下来，让我们定义包含增加和减少`state`组件的按钮的组件。创建文件`src/components/counter-buttons.js`并插入以下代码:

```
import React, { useContext } from "react";
import { Button } from "semantic-ui-react";
import { CounterContext } from "../context/counter-context";

export default function CounterButtons() {
  const [count, setCount] = useContext(CounterContext);

  const increment = () => {
    setCount(count + 1);
  };

  const decrement = () => {
    setCount(count - 1);
  };

  return (
    <div>  <Button.Group>  <Button color="green" onClick={increment}> Add </Button>  <Button color="red" onClick={decrement}> Minus </Button>  </Button.Group>  </div>
  );
} 
```

实际上，`useContext`函数将不起作用，因为我们还没有指定**提供者**。现在让我们通过在`src/views/counter-view.js`中创建一个容器来完成这个任务。插入以下代码:

```
import React from "react";
import { Segment } from "semantic-ui-react";

import { CounterContextProvider } from "../context/counter-context";
import CounterDisplay from "../components/counter-display";
import CounterButtons from "../components/counter-buttons";

export default function CounterView() {
  return (
    <CounterContextProvider>  <h3>Counter</h3>  <Segment textAlign="center">  <CounterDisplay />  <CounterButtons />  </Segment>  </CounterContextProvider>
  );
} 
```

最后，让我们用以下代码替换`App.js`中的现有代码:

```
import React from "react";
import { Container } from "semantic-ui-react";

import CounterView from "./views/counter-view";

export default function App() {
  return (
    <Container>  <h1>React Hooks Context Demo</h1>  <CounterView />  </Container>
  );
} 
```

您现在可以使用`yarn start`命令启动`create-react-app`服务器。浏览器应该会启动并呈现您的计数器。点击按钮以确保`increment`和`decrement`功能正在工作。

您也可以在 CodePen 上测试这段代码。

见 [CodePen](https://codepen.io) 上 SitePoint([@ SitePoint](https://codepen.io/SitePoint))
的笔 [qBZYyqw](https://codepen.io/SitePoint/pen/qBZYyqw) 。