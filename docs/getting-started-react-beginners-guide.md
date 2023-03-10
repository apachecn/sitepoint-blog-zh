# React 入门:初学者指南

> 原文：<https://www.sitepoint.com/getting-started-react-beginners-guide/>

React 是一个出色的 JavaScript 库，在开发社区掀起了一场风暴。简而言之，它让开发者更容易为网络、移动和桌面平台构建交互式用户界面。如今，全球有数千家公司在使用 React，包括网飞和 Airbnb 等知名公司。

在本指南中，我将向您介绍 React 及其几个基本概念。我们将快速启动并运行 Create React App 工具，然后我们将一步一步地构建一个简单的 React 应用程序。当您完成时，您将对基础知识有一个很好的概述，并准备好在您的 React 旅程中迈出下一步。

### 先决条件

在开始学习 React 之前，有必要对 [HTML、CSS](https://www.sitepoint.com/premium/courses/html-css-for-beginners-3009) 和 [JavaScript](https://www.sitepoint.com/premium/courses/javascript-fundamentals-3092) 有一个基本的了解。这也将有助于对 [Node.js](https://www.sitepoint.com/an-introduction-to-node-js/) 以及 [npm 包管理器](https://www.sitepoint.com/npm-node-package-manager/)有一个基本的了解。

为了跟随本教程，您需要在您的机器上安装 Node 和 npm。为此，请访问 Node.js [下载页面](https://nodejs.org/en/download/)并获取您需要的版本(npm 与 Node 捆绑在一起)。或者，你可以参考我们关于使用版本管理器安装节点的[教程。](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)

## 什么是反应？

React 是一个用于构建 UI 组件的 JavaScript 库。与 Angular 或 Vue 等更完整的框架不同，React 只处理视图层，因此您需要额外的库来处理路由、状态管理等事情。在本指南中，我们将重点关注 React 的开箱即用功能。

React 应用程序是使用可重用的 **UI 组件**构建的，这些组件可以相互交互。React 组件可以是基于类组件，也可以是所谓的函数组件。基于类的组件是使用 [ES6 类](https://www.sitepoint.com/object-oriented-javascript-deep-dive-es6-classes/)定义的，而函数组件是基本的 JavaScript 函数。这些倾向于使用[箭头函数](https://www.sitepoint.com/es6-arrow-functions-new-fat-concise-syntax-javascript/)来定义，但是它们也可以使用`function`关键字。基于类的组件将实现一个`render`函数，该函数返回一些 [JSX](https://www.sitepoint.com/an-introduction-to-jsx/) (React 对常规 JavaScript 的扩展，用于创建 React 元素)，而函数组件将直接返回 JSX。如果你从未听说过 JSX，请不要担心，因为我们稍后将对此进行更深入的探讨。

React 组件可以进一步分为**有状态**和**无状态**组件。无状态组件的工作只是显示从其父 React 组件接收的数据。如果它接收到任何事件或输入，它可以简单地将这些传递给它的父节点来处理。

另一方面，有状态组件负责维护某种应用程序状态。这可能涉及从外部源获取数据，或者跟踪用户是否登录。有状态组件可以响应事件和输入来更新其状态。

根据经验，您应该尽可能地编写无状态组件。无论是在应用程序中还是在其他项目中，这些都更容易重用。

## 了解虚拟 DOM

在我们开始编码之前，您需要知道 React 使用一个**虚拟 DOM** 来处理页面呈现。如果你熟悉 jQuery，你会知道它可以通过 **HTML DOM** 直接操纵网页。在很多情况下，这种直接的交互几乎不会带来任何问题。然而，在某些情况下，比如运行高度交互的实时 web 应用程序，性能会受到很大影响。

为了解决这个问题，虚拟 DOM(真实 DOM 的内存表示)的概念被发明出来，并且目前被包括 React 在内的许多现代 UI 框架所应用。与 HTML DOM 不同，虚拟 DOM 更容易操作，能够在几毫秒内处理大量操作，而不会影响页面性能。React 定期比较虚拟 DOM 和 HTML DOM。然后，它计算一个 diff，并将其应用于 HTML DOM，使其与虚拟 DOM 相匹配。通过这种方式，React 可以确保您的应用程序以每秒 60 帧的速度呈现，这意味着用户很少或没有延迟。

## 启动一个空白的 React 项目

根据先决条件，我假设您已经设置了一个节点环境，并安装了最新版本的 npm(或者可选地安装了 [Yarn](https://classic.yarnpkg.com/en/) )。

接下来，我们将使用 [Create React App](https://create-react-app.dev/) 构建我们的第一个 React 应用程序，这是一个用于创建单页 React 应用程序的官方实用程序脚本。

让我们现在安装它:

```
npm i -g create-react-app 
```

然后用它创建一个新的 React 应用程序。

```
create-react-app message-app 
```

根据您的互联网连接速度，如果这是您第一次运行`create-react-app`命令，可能需要一段时间才能完成。一路上会安装一些包，这些包是建立一个方便的开发环境所需要的——包括 web 服务器、编译器和测试工具。

如果您不想全局安装太多的软件包，您也可以`npx`，它允许您下载并运行一个软件包，而无需安装它:

```
npx i -g create-react-app 
```

运行这两个命令中的任何一个都应该会输出类似如下的内容:

```
...
Success! Created react-app at C:\Users\mike\projects\github\message-app
Inside that directory, you can run several commands:

  yarn start
    Starts the development server.

  yarn build
    Bundles the app into static files for production.

  yarn test
    Starts the test runner.

  yarn eject
    Removes this tool and copies build dependencies, configuration files
    and scripts into the app directory. If you do this, you can’t go back!

We suggest that you begin by typing:

  cd message-app
  yarn start

Happy hacking! 
```

项目设置过程完成后，执行以下命令启动 React 应用程序:

```
cd message-app
npm start 
```

您应该会看到以下输出:

```
....

Compiled successfully!

You can now view react-app in the browser.

  Local:            http://localhost:3000
  On Your Network:  http://192.168.56.1:3000

Note that the development build is not optimized.
To create a production build, use yarn build. 
```

您的默认浏览器应该会自动启动，您应该会看到如下屏幕:

![Create React App](img/6abd183eda576dc473bc63235e838899.png)

既然我们已经确认了我们的 starter React 项目运行无误，那么让我们来看一下幕后发生了什么。您可以使用您喜欢的代码编辑器打开文件夹`message-app`。让我们从`package.json`文件开始:

```
{
  "name": "message-app",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "@testing-library/jest-dom": "^4.2.4",
    "@testing-library/react": "^9.3.2",
    "@testing-library/user-event": "^7.1.2",
    "react": "^16.13.1",
    "react-dom": "^16.13.1",
    "react-scripts": "3.4.3"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  "eslintConfig": {
    "extends": "react-app"
  },
  "browserslist": {
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ],
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ]
  }
} 
```

如你所见，Create React App 已经为我们安装了几个依赖项。前三个与 [React 测试库](https://www.npmjs.com/package/@testing-library/react)相关，它(正如你可能猜到的)使我们能够测试我们的 React 代码。然后我们有`react`和`react-dom`，任何 React 应用程序的核心包，最后是`react-scripts`，它设置开发环境并启动一个服务器(你刚才已经看到了)。

接下来是四个 npm 脚本，用于自动化重复性任务:

*   `start`启动开发服务器
*   `build`创建应用的生产就绪版本
*   `test`运行上述测试
*   `eject`将暴露你的应用程序的开发环境

这个最后的命令值得详细说明。Create React App 工具在您的实际代码和开发环境之间提供了清晰的分离。如果您运行`npm run eject`，Create React App 将停止隐藏它的功能，并将所有内容转储到您项目的`package.json`文件中。虽然这让您可以更好地控制应用程序的依赖性，但我不建议您这样做，因为您必须管理构建和测试项目时使用的所有复杂代码。如果遇到这种情况，您可以使用 [customize-cra](https://github.com/arackaf/customize-cra) 来配置您的构建过程，而无需弹出。

Create React App 还支持 [ESLint](https://www.sitepoint.com/up-and-running-with-eslint-the-pluggable-javascript-linter/) (从`eslintConfig`属性可以看出)，并使用 [react-app](https://www.npmjs.com/package/eslint-config-react-app) ESLint 规则进行配置。

`package.json`文件的`browserslist`属性允许您指定您的应用程序将支持的浏览器列表。这种配置被 [PostCSS](https://www.sitepoint.com/an-introduction-to-postcss/) 工具和 [Babel](https://www.sitepoint.com/babel-beginners-guide/) 之类的 transpilers 使用。

Create React 应用程序最酷的特性之一是它提供开箱即用的热重装功能。这意味着我们对代码所做的任何更改都会导致浏览器自动刷新。对 JavaScript 代码的更改将重新加载页面，而对 CSS 的更改将更新 DOM 而无需重新加载。

现在，让我们首先通过按`Ctrl` + `C`来停止开发服务器。一旦服务器停止，删除除了`src`文件夹中的`serviceWorker.js`和`setupTests.js`文件之外的所有文件。如果你对服务人员的工作感兴趣，你可以在这里了解更多。

除此之外，我们将从头开始创建所有代码，这样您就可以理解`src`文件夹中的所有内容。

## JSX 语法简介

React 文档将 JSX 定义为“JavaScript 的语法扩展”，它让编写 React 组件变得简单。使用 JSX，我们可以传递 HTML 结构，或者像处理标准 JavaScript 值一样处理元素。

这里有一个简单的例子:

```
import React from 'react';

export default function App() {
  const message = <h1>I'm a heading</h1>;  //JSX FTW!
  return ( message );
} 
```

注意那行`const message = <h1>I'm a heading</h1>;`。那是 JSX。如果你试图在网络浏览器中运行，它会给你一个错误。然而，在 React 应用程序中，JSX 由 transpiler(如 Babel)解释，并呈现为 React 可以理解的 JavaScript 代码。

注:你可以在我们的教程[JSX 介绍](https://www.sitepoint.com/an-introduction-to-jsx/)中了解更多关于 JSX 的信息。

过去，React JSX 文件通常带有一个`.jsx`文件扩展名。现在，Create React App 工具生成带有`.js`文件扩展名的 React 文件。虽然仍然支持`.jsx`文件扩展名，但 React [的维护者推荐使用`.js`的](https://github.com/facebook/create-react-app/issues/87)。然而，有一群反对 React 开发人员，包括我自己，他们更喜欢使用 T4 扩展，原因如下:

*   在 VS 代码中， [Emmet](https://code.visualstudio.com/docs/editor/emmet) 为`.jsx`文件开箱即用。但是，您可以配置 VS 代码，将所有的`.js`文件视为`JavaScriptReact`，让 Emmet 处理这些文件。
*   标准 JavaScript 和 React JavaScript 代码有不同的林挺规则。

然而，对于本教程，我将遵守 Create React App 给我们的内容，坚持使用`.js`文件结尾。

## 你好，世界！在反应中

让我们开始写一些代码。在新创建的`message-app`的`src`文件夹中，创建一个`index.js`文件，并添加以下代码:

```
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(<h1>Hello World</h1>, document.getElementById('root')); 
```

使用`npm start`或`yarn start`再次启动开发服务器。您的浏览器应该显示以下内容:

![Hello React](img/fda248e80cc4cc90a6c83e0e5c9e67df.png)

这是最基本的“Hello World”React 例子。`index.js`文件是您的项目的根，React 组件将在这里呈现。让我解释一下代码是如何工作的:

*   第 1 行:导入 React 包来处理 JSX 处理。
*   第 2 行:导入 ReactDOM 包来呈现根 React 组件。
*   第 3 行:调用[渲染函数](https://reactjs.org/docs/react-dom.html#render)，传入:
    *   `<h1>Hello World</h1>`:JSX 元素
    *   `document.getElementById('root')`:一个 HTML 容器(JSX 元素将在这里呈现)。

HTML 容器位于`public/index.html`文件中。在第 31 行，您应该会看到`<div id="root"></div>`。这被称为**根 DOM 节点**，因为其中的一切都将由 **React 虚拟 DOM** 管理。

虽然 JSX 看起来很像 HTML，但还是有一些关键的区别。例如，您不能使用`class`属性，因为它是一个 JavaScript 关键字。取而代之的是用`className`代替。此外，像`onclick`这样的事件在 JSX 被拼写为`onClick`。现在让我们修改我们的 Hello World 代码:

```
const element = <div>Hello World</div>;
ReactDOM.render(element, document.getElementById('root')); 
```

我已经将 JSX 代码转移到一个名为`element`的常量变量中。我还用`div`标签替换了`h1`标签。为了让 JSX 工作，您需要将您的元素包装在一个父标签中。

看一下下面的例子:

```
const element = <span>Hello,</span> <span>Jane</span>; 
```

上面的代码不行。您将得到一个语法错误，指示您必须将相邻的 JSX 元素包含在封闭标记中。大概是这样的:

```
const element = <div>
    <span>Hello, </span>
    <span>Jane</span>
    </div>; 
```

如何评价 JSX 的 JavaScript 表达式？简单。就像这样用花括号:

```
const name = "Jane";
const element = <p>Hello, {name}</p> 
```

…或者像这样:

```
const user = {
  firstName: 'Jane',
  lastName: 'Doe'
}
const element = <p>Hello, {user.firstName} {user.lastName}</p> 
```

更新您的代码，并确认浏览器显示“Hello，Jane Doe”。尝试其他示例，如`{ 5 + 2 }`。现在您已经掌握了使用 JSX 的基本知识，让我们继续创建一个 React 组件。

## 声明 React 组件

上面的例子是向您展示`ReactDOM.render()`如何工作的简单方式。通常，我们将所有项目逻辑封装在 React 组件中，然后传递给`ReactDOM.render`函数。

在`src`文件夹中，创建一个名为`App.js`的文件，并键入以下代码:

```
import React, { Component } from 'react';

class App extends Component {

  render(){
    return (
      <div>
        Hello World Again!
      </div>
    )
  }
}

export default App; 
```

这里，我们通过定义一个 JavaScript 类创建了一个 React 组件，该类是`React.Component`的子类。我们还定义了一个返回 JSX 元素的渲染函数。您可以在`<div>`标签中放置额外的 JSX 代码。接下来，用下面的代码更新`src/index.js`,以便查看浏览器中反映的变化:

```
import React from 'react';
import ReactDOM from 'react-dom';

import App from './App';

ReactDOM.render(<App/>, document.getElementById('root')); 
```

首先，我们导入`App`组件。然后我们使用 JSX 格式渲染`App`，就像这样:`<App/>`。这是必需的，这样 JSX 就可以把它编译成一个可以推送到`React DOM`的元素。保存更改后，查看一下您的浏览器，确保它呈现了正确的信息。

接下来，我们将看看如何应用样式。

## 设计 JSX 元素

有各种各样的方法来设计 React 组件。我们将在本教程中讨论的两个问题是:

1.  JSX 嵌入式造型
2.  外部样式表

下面是一个我们如何实现 JSX 内嵌样式的例子:

```
// src/App.js

render() {
  const headerStyle = {
    color: '#ff0000',
    textDecoration: 'underline'
  }
  return (
    <div>
      <h1 style={headerStyle}>Hello World Again!</h1>
    </div>
  )
} 
```

React 样式看起来很像普通的 CSS，但是有一些关键的区别。例如，`headerStyle`是一个对象文字。我们不能像平常一样使用分号。此外，为了与 JavaScript 语法兼容，许多 CSS 声明也进行了修改。例如，我们用`textDecoration`代替`text-decoration`。基本上，所有 CSS 键都使用 camel case，除了供应商前缀，如`WebkitTransition`，它必须以大写字母开头。

我们也可以这样实现样式:

```
// src/App.js

return (
  <div>
    <h1 style={{color:'#ff0000', textDecoration: 'underline'}}>Hello World Again!</h1>
  </div>
) 
```

第二种方法是使用外部样式表。默认情况下，已经支持外部 CSS 样式表。如果你想使用像 Sass 这样的预处理器，请[查阅文档](https://create-react-app.dev/docs/adding-a-sass-stylesheet/)了解如何配置它。

在`src`文件夹中，创建一个名为`App.css`的文件，并键入以下代码:

```
h1 {
  font-size: 4rem;
} 
```

将以下导入语句添加到文件顶部的`src/App.js`:

```
import './App.css'; 
```

保存后，您应该会看到浏览器上的文本内容在大小上发生了巨大的变化。您也可以像这样使用 CSS 类:

```
.header-red {
  font-size: 4rem;
  color: #ff0000;
  text-decoration: underline;
} 
```

更新`src/App.js`如下:

```
<h1 className="header-red">Hello World Again!</h1> 
```

我们不能使用 HTML 的`class`属性，因为它是一个保留的 JavaScript 关键字。相反，我们使用`className`。下面应该是您的预期输出。

![Styling in React](img/66152d0142aecb4ca3980e5a8839699b.png)

既然您已经学习了如何向 React 项目添加样式，那么让我们继续学习无状态和有状态 React 组件。

## 无状态与有状态组件

无状态组件，也称为哑组件，只是一个显示信息的组件。它不包含任何操作数据的逻辑。它可以接收来自用户的事件，然后将这些事件传递给父容器进行处理。

创建文件`message-view.js`并将下面的示例代码复制到其中。这是一个哑组件的完美例子(尽管从技术上讲，它更像是一个静态组件):

```
import React from 'react';

class MessageView extends React.Component {
  render() {
    return(
      <div className="message">
        <div className="field">
          <span className="label">From: </span>
          <span className="value">John Doe</span>
        </div>
        <div className="field">
          <span className="label">Status: </span>
          <span className="value"> Unread</span>
        </div>
        <div className="field content">
          <span className="label">Message: </span>
          <span className="value">Have a great day!</span>
        </div>
      </div>
    )
  }
}

export default MessageView; 
```

接下来，用下面的代码给`src/App.css`添加一些基本的样式:

```
body {
  background-color: #EDF2F7;
  color: #2D3748;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
}

h1 {
  font-size: 2rem;
}

.container {
  width: 800px;
  margin: 0 auto;
}

.message {
  background-color: #F7FAFC;
  width: 400px;
  margin-top: 20px;
  border-top: solid 2px #fff;
  border-radius: 8px;
  padding: 12px;
  box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
}

.field{
  display: flex;
  justify-content: flex-start;
  margin-top: 2px;
}

.label {
  font-weight: bold;
  font-size: 1rem;
  width: 6rem;
}

.value {
  color: #4A5568;
}

.content .value {
  font-style: italic;
} 
```

最后，修改`src/App.js`,使整个文件如下所示:

```
import React, { Component } from 'react';

import './App.css';
import MessageView from './message-view';

class App extends Component {
  render(){
    return (
      <MessageView />
    )
  }
}

export default App; 
```

到目前为止，代码应该是非常不言自明的，因为我已经解释了到目前为止所涉及的概念。现在看一下您的浏览器，您应该会看到以下结果:

![Messages View](img/fb52f951e5fcc8897673546cf8072697.png)

我们之前提到过，React 既提供基于类的组件，也提供函数组件。我们可以使用函数语法重写`MessageView`,如下所示:

```
import React from 'react';

export default function MessageView() {
  return (
    <div className="message">
      <div className="field">
        <span className="label">From: </span>
        <span className="value">John Doe</span>
      </div>
      <div className="field">
        <span className="label">Status: </span>
        <span className="value"> Unread</span>
      </div>
      <div className="field content">
        <span className="label">Message: </span>
        <span className="value">Have a great day!</span>
      </div>
    </div>
  );
} 
```

请注意，我已经删除了`Component`导入，因为这在函数语法中并不需要。这种风格一开始可能会令人困惑，但是您很快就会发现用这种方式编写 React 组件会更快。

此外，随着 [React 钩子](https://www.sitepoint.com/react-hooks/)的出现，这种编写 React 组件的风格变得越来越流行。

## 通过 Props 传递数据

您已经成功创建了一个无状态 React 组件。不过，它还不完整，因为要将其与有状态组件或容器正确集成，还需要做更多的工作。目前，`MessageView`正在显示静态数据。我们需要修改它，以便它可以接受输入参数。我们使用一种叫做 [props](https://reactjs.org/docs/components-and-props.html) 的东西来做这件事——我们将从父组件传递下来的数据。

首先改变`MessageView`组件，如下所示:

```
import React from 'react';

class MessageView extends React.Component {
  render() {
    const message = this.props.message;

    return(
      <div className="message">
        <div className="field">
          <span className="label">From: </span>
          <span className="value">{message.from}</span>
        </div>
        <div className="field">
          <span className="label">Status: </span>
          <span className="value">{message.status}</span>
        </div>
        <div className="field content">
          <span className="label">Message: </span>
          <span className="value">{message.content}</span>
        </div>
      </div>
    )
  }
}

export default MessageView; 
```

这里需要注意的主要事情是我们如何定义`message`变量。我们赋予它一个值`this.props.message`，它是从有状态的父组件传下来的。在我们的 JSX 中，我们可以引用我们的`message`变量并将其输出到页面。

现在让我们为我们的`MessageView`创建一个父组件。创建一个新文件`message-list.js`并添加以下代码:

```
import React, { Component } from 'react';
import MessageView from './message-view';

class MessageList extends Component {
  state = {
    message: {
      from: 'Martha',
      content: 'I will be traveling soon',
      status: 'read'
    }
  }

  render() {
    return(
      <div className="container">
        <h1>List of Messages</h1>
        <MessageView message={this.state.message} />
      </div>
    )
  }
}

export default MessageList; 
```

这里，我们使用[状态](https://reactjs.org/docs/state-and-lifecycle.html)来存储包含消息的对象。React 的神奇之处在于，当状态对象改变时，组件会重新呈现(从而更新 UI)。

然后，在我们的 JSX 中，我们将把我们的`state`对象的`message`属性传递给`MessageView`组件。

最后一步是更新我们的`App`组件，以呈现我们新的有状态`MessageList`组件，而不是无状态`MessageView`组件:

```
import React, { Component } from 'react';
import MessageList from './message-list';

import './App.css';

class App extends Component {
  render(){
    return (
      <MessageList />
    )
  }
}

export default App; 
```

保存更改后，检查您的浏览器以查看结果。

![Message List](img/5f38e0aa676bfcdc6e38635906e44c1a.png)

花点时间确保你明白发生了什么。我们在(有状态的)`MessageList`组件中声明了一个`state`对象。该对象的一个`message`属性包含了我们的消息。在我们的`render`函数中，我们可以使用称为 props 的东西将消息传递给我们的(无状态)子组件。

在(无状态)`MessageView`组件中，我们可以使用`this.props.message`来访问消息。然后，我们可以将这个值传递给我们的 JSX 来呈现页面。

唷！

## 道具检查

随着应用程序的增长，数据作为道具被来回传递，验证组件正在接收它们期望的数据类型将会很有用。

幸运的是，我们可以用[道具类型包](https://www.npmjs.com/package/prop-types)做到这一点。要快速查看这个例子，将我们的`MessageView`组件修改如下:

```
import React from 'react';
import PropTypes from 'prop-types';

class MessageView extends Component {
  // This stays the same
]

MessageView.propTypes = {
  message: PropTypes.object.isRequired
}

export default MessageView; 
```

如果`message`道具丢失，这将导致您的 React 应用程序抱怨。如果组件接收的不是一个对象，它也会抱怨。

您可以通过更改父组件的状态来尝试一下，如下所示:

```
 state = {
  message: 'Not an object!'
} 
```

回到您的浏览器并打开控制台。您应该会看到控制台中记录了以下内容:

```
Warning: Failed prop type: Invalid prop `message` of type `string` supplied to `MessageView`, expected `object`.
    in MessageView (at message-list.js:13)
    in MessageList (at App.js:9)
    in App (at src/index.js:6) 
```

## 组件重用

现在让我们看看如何使用`MessageView`实例显示多条消息。这就是 React 开始大放异彩的地方，因为它使代码重用变得非常容易(正如您将看到的)。

首先，我们将把`state.message`改为一个数组，并将其重命名为`messages`。然后，我们将使用 JavaScript 的[映射函数](https://www.sitepoint.com/map-reduce-functional-javascript/)来生成`MessageView`组件的多个实例，每个实例对应于`state.messages`数组中的一条消息。

我们还需要填充一个名为 [key](https://facebook.github.io/react/docs/lists-and-keys.html) 的特殊属性，其中包含一个惟一的值，比如`id`。React 需要这样做，以便跟踪列表中的哪些项目被更改、添加或删除。

按如下方式更新`MessageList`代码:

```
class MessageList extends Component {
  state = {
    messages:  [
      {
        _id: 'd2504a54',
        from: 'John',
        content: 'The event will start next week',
        status: 'unread'
      },
      {
        _id: 'fc7cad74',
        from: 'Martha',
        content: 'I will be traveling soon',
        status: 'read'
      },
      {
        _id: '876ae642',
        from: 'Jacob',
        content: 'Talk later. Have a great day!',
        status: 'read'
      }
    ]
  }

  render() {
    const messageViews = this.state.messages.map(
      message => <MessageView key={message._id} message={message} />
    )

    return(
      <div className="container">
        <h1>List of Messages</h1>
        {messageViews}
      </div>
    )
  }
} 
```

检查您的浏览器以查看结果:

![Messages Loop](img/35f96bbd878241261975e0e521a59ef4.png)

如您所见，使用 React 很容易定义构建块来创建强大而复杂的 UI 界面。

## 重构以使用 React 挂钩

钩子是 React 的最新版本，但是它们正在席卷 React 世界。简单地说，它们使得获取一个 React 函数组件并向其添加状态(和其他特性)成为可能。

我将通过重构我们的`MessageView`组件使其成为一个函数组件来结束本教程，该函数组件使用 React 钩子来管理其状态。请注意，这仅在使用 React v16.8 和更高版本时才有可能。

```
import React, { useState } from 'react';
import MessageView from './message-view';

export default function MessageList () {
  const initialValues = [
    {
      _id: 'd2504a54',
      from: 'John',
      content: 'The event will start next week',
      status: 'unread'
    },
    {
      _id: 'fc7cad74',
      from: 'Martha',
      content: 'I will be traveling soon',
      status: 'read'
    },
    {
      _id: '876ae642',
      from: 'Jacob',
      content: 'Talk later. Have a great day!',
      status: 'read'
    }
  ];

  const [messages] = useState(initialValues);
  const messageViews = messages.map(
    message => <MessageView key={message._id} message={message} />
  );

  return (
    <div className="container">
      <h1>List of Messages</h1>
      {messageViews}
    </div>
  );
} 
```

在上面的例子中，我用 [useState React 钩子](https://reactjs.org/docs/hooks-state.html)替换了`state`对象。顾名思义，这允许您管理组件的状态。

在大型项目中使用钩子可以帮助你避免所谓的道具钻孔。 **Prop drilling** 看到您通过多个组件(最终不需要该数据)传递 Prop，只是为了到达一个深度嵌套的组件。

我们还可以将我们的`MessageView`组件转换成一个函数组件:

```
import React from 'react';
import PropTypes from 'prop-types';

const MessageView = ({ message }) => {
  const { from, status, content } = message;

  return(
    <div className="message">  <div className="field">  <span className="label">From: </span>  <span className="value">{from}</span>  </div>  <div className="field">  <span className="label">Status: </span>  <span className="value">{status}</span>  </div>  <div className="field content">  <span className="label">Message: </span>  <span className="value">{content}</span>  </div>  </div>
  );
};

MessageView.propTypes = {
  message: PropTypes.object.isRequired
}

export default MessageView; 
```

请注意我们现在如何在组件中接收消息 prop:

```
const MessageView = ({ message }) => {
  ...
} 
```

这利用了一种叫做[object destructing](https://www.sitepoint.com/es6-destructuring-assignment/)的技术，它允许你从数组或对象中提取单独的项，并使用一种简化的语法将它们放入变量中。

我们在这里使用相同的技术，从`message`对象中获取我们需要的值，并避免在所有内容前面加上`message`:

```
const { from, status, content } = message; 
```

这就是全部！

在本文中，我不想深入探讨 React 挂钩，只是想让您知道它们的存在，并且它们在 React 社区中越来越受欢迎。如果你想了解更多关于钩子的知识，请阅读我们的指南[开始使用 React 钩子](https://www.sitepoint.com/react-hooks/)。

## 演示

这里有一个你可以玩的现场演示:

在 [CodePen](https://codepen.io) 上通过 SitePoint([@ SitePoint](https://codepen.io/SitePoint))
看到笔 [React 消息 App](https://codepen.io/SitePoint/pen/PoNXyrK) 。