# 使用 Preact 作为反应选项

> 原文：<https://www.sitepoint.com/using-preact-react-alternative/>

Preact 是虚拟 DOM 组件范例的一个实现，就像 react 和许多其他类似的库一样。与 React 不同的是，它的大小只有 3KB，在速度上也超过了它。它是由杰森·米勒创建的，可以在众所周知的许可和开源的麻省理工许可下使用。

## 为什么使用 Preact？

Preact 是 react 的轻量级版本。如果您喜欢使用 react 构建视图，但性能、速度和大小是您的优先考虑事项，您可能更喜欢使用 Preact 作为轻量级替代方案，例如，在移动 web 应用程序或渐进式 web 应用程序的情况下。

无论您是开始一个新项目还是开发一个现有项目，Preact 都可以为您节省大量时间。你不需要重新发明轮子去学习一个新的库，因为它与 React 相似并且兼容——由于兼容层`preact-compat`，你可以使用现有的 React 包，只有一些别名。

## 利弊

React 和 Preact 之间有许多不同之处，我们可以总结为三点:

*   **特性和 API** : Preact 只包括 react API 的一个子集，而不是 React 中所有可用的特性。
*   **大小** : Preact 比 react 小很多。
*   **性能** : Preact 比 react 快。

每一个库都有自己的优缺点，只有你的优先顺序可以帮助你决定哪个库适合你的下一个项目。在这一节中，我将尝试列出这两个库的优缺点。

### 预先采取行动

*   Preact 是轻量级的，更小(gzip 压缩后只有 3KB 大小)，比 react 更快(参见这些[测试](https://developit.github.io/preact-perf/))。您也可以通过[链接](https://developit.github.io/preact-perf/)在您的浏览器中运行性能测试。
*   p React 在很大程度上与 react 兼容，并具有与 React 相同的 ES6 API，这使得采用 Preact 作为在项目中构建用户界面的新库或者出于性能原因用 Preact 替换现有项目变得非常容易。
*   官方网站上有很好的文档和示例。
*   它有一个强大的官方 CLI，可以快速创建新的 Preact 项目，没有 Webpack 和 Babel 配置的麻烦。
*   许多功能都是从 React 上已经完成的所有工作中获得的灵感。
*   它还有一套独立于 React 的高级特性，比如[链接状态](https://preactjs.com/guide/linked-state)。

### 反应积极

*   React 支持单向数据绑定。
*   它是由一家大公司脸书支持的。
*   官方网站和网络上的好文档、例子和教程。
*   大型社区。
*   在脸书的网站上使用，该网站在全球拥有数百万的访问者。
*   Chrome 有自己的官方开发者调试工具扩展。
*   它有 Create React App 项目样板，用于快速创建零配置的项目。
*   它有一个良好的架构和复杂的代码库。

### 反对意见

*   与 PReact 或其他现有的类似库相比，react 的大小相对较大。(React 缩小后的源文件大小约为 136KB，或者在缩小和压缩后约为 42KB。)
*   比 Preact 慢。
*   由于其复杂的代码库，新手开发者很难做出贡献。

*注意:我在撰写本文时列出的另一个缺点是，React 有一个与 BSD 许可证配对的授权专利条款，这使得它在法律上不适合某些用例。然而，【2017 年 9 月，React 许可切换 MIT，解决了这些许可问题。*

### 预防冲突

*   Preact 只支持无状态的功能组件和基于 ES6 类的组件定义，所以没有 **createClass** 。
*   不支持[上下文](https://facebook.github.io/react/docs/context.html)。
*   不支持 React 属性类型。
*   比 React 小的社区。

## Preact CLI 入门

Preact CLI 是由 Preact 的作者杰森·米勒创建的命令行工具。它使得创建一个新的 Preact 项目变得非常容易，而不会陷入复杂的配置中，所以让我们从安装它开始。

打开您的终端(Linux 或 macOS)或命令提示符(Windows)，然后运行以下命令:

```
npm i -g preact-cli@latest 
```

这将安装 Preact CLI 的最新版本，假设您已经在本地开发机器上安装了[节点和](https://www.sitepoint.com/beginners-guide-node-package-manager/)NPM。

现在，您可以使用以下内容创建您的项目:

```
preact create my-app 
```

或者，如果你想交互式地创建你的应用程序:

```
preact init 
```

接下来，在应用程序的根文件夹中导航并运行:

```
npm start 
```

这将启动一个实时重载的开发服务器。

最后，当您完成应用程序的开发后，您可以使用以下代码构建一个生产版本:

```
npm run build 
```

## 揭开你的第一个 Preact 应用的神秘面纱

成功安装 Preact CLI 并生成 app 后，让我们试着了解一下用 Preact CLI 生成的简单 app。

Preact CLI 生成以下目录结构

```
├── node_modules
├── package.json
├── package-lock.json
└── src
    ├── assets
    ├── components
    │   ├── app.js
    │   └── header
    ├── index.js
    ├── lib
    ├── manifest.json
    ├── routes
    │   ├── home
    │   └── profile
    └── style
        └── index.css 
```

`components`文件夹保存 Preact 组件，`routes`文件夹保存用于每个应用程序路线的页面组件。你可以使用`lib`文件夹保存任何外部库，使用`style`文件夹保存 CSS 样式，使用`assets`保存图标和其他图形。

请注意`manifest.json`文件，它类似于`package.json`,但用于 PWAs(渐进式 web 应用程序)。由于 Preact CLI，您可以开箱获得满分 PWA。

现在，如果您打开项目的`package.json`文件，您会看到主入口点被设置为`src/index.js`。以下是该文件的内容:

```
import './style';
import App from './components/app';

export default App; 
```

如您所见，`index.js`从`./components/app**`导入了样式和`App`组件，然后将其作为默认值导出。

现在，让我们看看里面有什么:

```
import { h, Component } from 'preact';
import { Router } from 'preact-router';

import Header from './header';
import Home from '../routes/home';
import Profile from '../routes/profile';

export default class App extends Component {
    handleRoute = e => {
        this.currentUrl = e.url;
    };

    render() {
        return (
            <div id="app">  <Header />  <Router onChange={this.handleRoute}>  <Home path="/" />  <Profile path="/profile/" user="me" />  <Profile path="/profile/:user" />  </Router>  </div>
        );
    }
} 
```

这个文件导出一个默认的类`App`，它扩展了从`preact`包导入的`Component`类。每个 Preact 组件都需要扩展`Component`类。

`App`定义了一个`render`方法，该方法返回一堆呈现应用程序主用户界面的 HTML 元素和 Preact 组件。

在`div`元素中，我们有两个 Preact 组件，`Header`——呈现应用程序的标题——和一个`Router`组件。

Preact 路由器类似于最新版本的 [React 路由器(版本 4)](https://www.sitepoint.com/react-router-v4-complete-guide/) 。您只需要用一个`<Router>`组件包装子组件，然后为每个组件指定`path`属性。然后，路由器将负责呈现组件，该组件具有与当前浏览器的 URL 相匹配的路径属性。

值得一提的是，Preact 路由器非常简单，与 react 路由器不同，它不支持嵌套路由和视图合成等高级功能。如果您需要这些功能，您必须通过别名`preact-compat`使用 React 路由器 v3，或者更好地使用最新的 React 路由器(版本 4 ),它比 v3 更强大，不需要任何兼容层，因为它直接与 Preact 一起工作。(请看这个 [CodePen](https://codepen.io/developit/pen/BWxepY?editors=0010) 演示的例子。)

## 预兼容层

`preact-compat`模块允许开发人员从 react 切换到 Preact，而无需将 React 和 ReactDOM 的导入更改为 Preact，或者将现有的 React 包用于 Preact。

使用`preact-compat`很容易。您所要做的就是首先通过 npm 安装它:

```
npm i -S preact preact-compat 
```

然后设置您的构建系统，将`react`或`react-dom`的导入或需求重定向到`preact-compat`。例如，在 Webpack 的情况下，您只需要向`webpack.config.js`添加以下配置:

```
{
  "resolve": {
    "alias": {
      "react": "preact-compat",
      "react-dom": "preact-compat"
    }
  }
} 
```

## 结论

Preact 是反应的一个很好的选择。它的社区正在稳步增长，越来越多的网络应用程序正在使用它。因此，如果您正在构建一个具有高性能要求的 web 应用程序，或者一个针对慢速 2G 网络的移动应用程序，那么您应该考虑 Preact——要么作为您项目的第一候选视图库，要么作为 react 的替代产品。

## 分享这篇文章