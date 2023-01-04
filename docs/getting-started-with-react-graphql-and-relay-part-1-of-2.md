# React、GraphQL 和 Relay 入门(第 1 部分，共 2 部分)

> 原文：<https://www.sitepoint.com/getting-started-with-react-graphql-and-relay-part-1-of-2/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

## 介绍

与 AngularJS 和 Ember 等框架不同，React 是一个提供有限数量功能的库，对于用于应用程序其他方面的库，React 在很大程度上是不可知的。从根本上说，React 提供了 UI 组件功能，从而提供了一种机制来创建组件、管理组件中的数据、呈现组件以及组合组件以构建更大的组件。无论数据来自哪里，如何检索，或者作为大型应用程序的一部分如何管理，都可以使用 React。为了解决这些问题，使用了其他库和模式。React 应用程序使用的一个常见模式是 Flux。

Flux 是作为 MVC(模型-视图-控制器)模式的替代方案开发的，用于管理响应动作的数据流。与 MVC 的双向数据流不同，Flux 依赖于系统各部分之间的单向数据流。Flux 是由脸书创建的，因为他们的开发人员发现很难对使用 MVC 的大型应用程序中的数据移动进行推理。

与 MVC 的多回路数据流不同，Flux 使用单回路数据流。流程在动作->调度器->存储(或多个存储)->组件(又名视图)->动作的连续循环中运行。

动作代表进入系统的某种事件。它可以是用户生成的，如请求刷新数据的按钮点击，也可以是通过 web 套接字接收的消息。该动作被传递给调度员，调度员将该动作发送给所有商店。调度程序实际上只不过是一种转发机制。它对动作、动作传递的数据以及每个商店对动作的责任或兴趣一无所知。它只是将动作分派给所有商店，然后每个商店选择是否应该处理该动作。存储负责维护数据的本地副本，强制执行业务规则，并通知组件新数据，以便它们可以被刷新。存储可以被认为是维护应用程序状态，整个流程本质上是一个状态机。React 组件利用状态机模式。从某种意义上说，Flux 在整个应用程序的架构中使用了相同的状态机模式。

虽然 Flux 是解决数据流问题的一种模式，但它本身并不提供这种模式的实现。为了使用 Flux 模式，开发人员被迫创建系统的所有组件，除了由脸书提供的调度程序。创建一个 Flux 系统相对容易，但是需要很多样板代码。在这方面，它遇到了与 Backbone.js 相同的问题。它很容易启动并运行，但最终需要大量的编码。

## 通量演变

随着开发人员使用 Flux，他们开始寻找将样板代码重构为可重用库的方法。此外，他们确定了 Flux 的子模式，这些子模式使对应用程序数据流的推理变得更容易，并在不牺牲 Flux 的一般好处的情况下降低了应用程序的复杂性。这些子模式包括将应用程序从许多商店减少到一个，将 dispatcher 和 store 合并到同一个组件中(当只有一个商店时这是有意义的)，并将组件包装在一个容器中，该容器在一个黑盒中处理动作创建、调度和商店管理。Flux 衍生物不是纯粹的 Flux，但是它们保留了 Flux 的基本元素，没有 Flux 通常的缺点，即大量的样板代码。

虽然 Flux 有很多衍生产品，但 Redux 是其中比较流行的一种。Redux 完全是围绕状态机和不可变数据的概念构建的，其中动作由单个 dispatcher-store 处理，该 dispatcher-store 使用 reducer 函数(它们本身是可组合的)从一种状态转换到另一种状态。它极大地简化了 Flux，同时引入了函数式编程的许多方面，一旦掌握了这些方面，编写 React 应用程序就会容易得多。

继电器是脸书的另一个通量衍生物，越来越受欢迎。有关脸书如何使用继电器的信息，以及他们对继电器与通量关系的看法，[点击此处](https://facebook.github.io/react/blog/2015/02/20/introducing-relay-and-graphql.html)。

## 继电器，包罗万象的解决方案

虽然 Redux 简化了应用程序的管理，但它不知道实际数据的位置。它可以与任何数据存储系统一起工作，再次导致更多的样板代码(尽管比 Flux 少)。现在来了 Relay(脸书的另一个创造——他们一直忙于 JavaScript 领域——React、Relay、Immutable.js、GraphQL、Jest、Flow 等。)，它试图重构数据访问的样板代码，并引入一种新的数据服务——graph QL。GraphQL 不同于传统的 REST 服务，它将数据视为一个图形，并试图以分层的形式表示该图形，允许数据的消费者指定他们需要的数据，这与传统的 REST 服务相反，传统的 REST 服务提供一组固定的数据，而不考虑消费者的需求。

那么 Relay 是做什么的呢？Relay 是一个框架，它通过一个实现动作的容器、一个调度程序和一个存储将 React 组件连接到 GraphQL 服务。开发者不需要编码动作、调度器或存储，而是可以触发动作并通过中继 API 访问结果。为了配置容器，开发人员必须提供 GraphQL 查询和变异片段，以向容器描述数据的图形结构，但除此之外，Relay 负责管理数据的所有细节。

Relay 真的是框架(比如 Angular)，不是库。它不是与实现无关的——它需要用 React 实现 UI 组件，并由 GraphQL 提供数据服务。一旦 GraphQL 服务器和 React 组件的配置就绪，Relay 就会接管并执行所有需要的操作。所以使用 Relay 的关键是掌握配置流程。

此外，与 Angular 等框架不同 Angular 只对客户端提出特定要求——Relay 还需要 GraphQL 服务器接口，该接口为中继容器提供数据查询和变异操作。Relay 不关心数据是如何存储的，只要数据是通过特定的 GraphQL 接口提供的。

因此，Relay 要求后端和前端开发团队都理解它是如何工作的，以及他们的每个部分需要如何编码和配置。

## 中继和反应

这篇文章的目的是从反应的角度来研究接力。GraphQL 服务器可以用任意数量的语言进行编码和配置，并部署在多种平台上。对于 Node.js 中的 GraphQL 实现，名为 **[graphql-relay](https://www.npmjs.com/package/graphql-relay)** 的包可以用来简化 graphql 服务器的编码和配置需求。在 React 端，另一个名为 **[的包 relay-react](https://www.npmjs.com/package/react-relay) 将用于配置中继容器和路由，以及触发数据变异等操作。**

## 入门指南

接力入门很难。因为这项技术太新了，而且有很多竞争者，所以关于如何使用接力赛的资源有限。有资源的地方，例子是有限的，开发人员最终被迫通读博客文章、GitHub 问题和正式规范，以便创建一个简单的 CRUD 应用程序。再加上相当复杂的开发环境，以及对正确构建的 GraphQL 服务器的需求，这项任务可能会非常艰巨，尤其是对于新的 JavaScript/前端开发人员。

首先，将下面的 GitHub [库](https://github.com/DevelopIntelligenceBoulder/react-flux-blog)克隆到你的电脑上，打开 **blog-post-5+6** 的文件夹。该文件夹包含一个完整的 GraphQL/React/Relay 应用程序。要启动并运行这个应用程序，打开一个终端，切换到 **blog-post-5+6** 文件夹，运行下面的 Gulp 命令。

```
 $ npm i  

$ npm i -g gulp eslint eslint-config-airbnb eslint-plugin-react@^4.3.0 webpack babel-cli babel-eslint eslint-plugin-jsx-a11y@^0.6.2 

$ gulp 

$ npm run update-schema 

$ gulp 

$ gulp server 
```

打开微软 Edge，然后导航到以下网址: [http://localhost:3000](http://localhost:3000/) 。

应该会出现一个样式为 [Bootstrap 4](http://v4-alpha.getbootstrap.com/) 的小部件列表，看起来如下所示:

项目的基本开发结构是典型的文件夹组织，源代码文件在`src`文件夹中编辑，然后复制到分发文件夹`dist`，应用程序从该文件夹执行。复制过程是由 Gulp 通过简单复制文件、处理 SASS 文件的任务和 JavaScript 的 WebPack 处理的组合来完成的。WebPack 处理机制使用 Babel transpiler 将 RelayQL、JSX 和 ES2015 代码转换为符合 ES5.1 的 JavaScript，以便在任何浏览器中执行。ES2015 和 JSX transpilation 在以前的帖子中有所涉及，但是 RelayQL 的 transpilation 是一个新话题。

## RelayQL 和 Babel-Relay 插件

GraphQL 服务器能够通过使用自省来生成模式。该模式是特定 GraphQL 服务器使用的所有类型的 JSON 文件。它包括自定义类型和内置类型。 [Babel-Relay 插件](https://www.npmjs.com/package/babel-relay-plugin)使用这个模式来验证用 [RelayQL](https://facebook.github.io/relay/docs/api-reference-relay-ql.html) 编码的 GraphQL 片段。片段使用 ES2015 字符串模板进行编码，一旦根据模式进行验证，就会转换为 JavaScript。这种验证有助于在 GraphQL 错误发生之前阻止它们。

配置 Babel-Relay 插件以及生成模式的最简单方法是使用来自 [Relay 网站](https://facebook.github.io/relay/docs/guides-babel-plugin.html)或 [Relay Starter Kit](https://github.com/relayjs/relay-starter-kit) 项目之一的示例。这些是这篇博文的 Github 库使用的文件，并遵循 Relay 网站上的模式。

在中继启动工具包中，需要两个文件:[build/babelrelayplugin . js](https://github.com/relayjs/relay-starter-kit/blob/master/build/babelRelayPlugin.js)和 [scripts/updateSchema.js](https://github.com/relayjs/relay-starter-kit/blob/master/scripts/updateSchema.js) 。 **updateSchema.js** 文件将用于生成模式，而 **babelRelayPlugin.js** 将使用模式文件来验证 GraphQL 片段并转换 RelayQL 代码。

## 配置 GraphQL 以使用中继

通常，标准的 GraphQL 服务器实现需要进行修改才能使用 Relay。名为 **[graphql-relay](https://www.npmjs.com/package/graphql-relay)** 的包用于帮助配置基于 Node.js 的 graphql 服务器，使其符合 relay 标准。GraphQL 服务器有三个主要方面需要特定于中继的配置:对象标识、类型连接和变异。

使用全局唯一的 ID，对象标识允许中继从 GraphQL 服务器查询实现节点接口的任何类型。全局 ID 是一个 base64 编码的值，包括用冒号连接在一起的类型名和本地 ID 值。 **graphql-relay** 库分别使用名为`toGlobalID`和`fromGlobalID`的函数提供了与全局 ID 相互转换的函数。类型名来自类型配置中指定的 GraphQL 自定义类型名。通常，本地 ID 值来自数据存储机制，例如关系数据库身份。

```
 import { nodeInterface } from './../node-definitions'; 

export const widgetType = new GraphQLObjectType({ 
  name: 'Widget', 
  description: 'A widget object', 
  fields: () => ({ 
    id: globalIdField('Widget'), 
    // more fields 
  }), 
  interfaces: () => [nodeInterface] 
}); 
```

文件 **node-definitions.js** (及其附带文件 **type-registry** )用于提供配置和类型注册表，以便通过节点接口使对象可用。

第二个特定于中继的配置，即类型连接，是父类型和它们的子类型之间的连接，它们之间有多对一的关系。这些是使用特殊的连接类型结构来管理的，该结构支持图边和游标的概念，用于限制结果集和生成结果页。连接和边类型可配置为支持附加属性，允许关于连接或边的性质的元数据，例如加权边。

```
 import { widgetType } from './types/widget-type'; 
import { connectionDefinitions } from 'graphql-relay'; 

export const { connectionType: widgetConnection, edgeType: WidgetEdge } = 
  connectionDefinitions({name: 'Widget', nodeType: widgetType}); 
```

`connectionDefinitions`函数用于在 Relay 期望的结构中创建连接类型。

```
 import { widgetConnection } from '../connections/widget-connection'; 

// inside of fields function of viewer type declaration 
widgets: { 
  type: widgetConnection, 
  description: 'A list of widgets', 
  args: connectionArgs, 
  resolve: (_, args) => connectionFromPromisedArray(getWidgets(), args) 
} 
```

`widgetConnection`类型从 **widget-connection.js** 文件导入，用于配置查看器类型的 widgets 字段。包`graphql-relay`还提供了一个名为`connectionArgs`的对象，该对象包含 Relay 传递的用于连接的标准参数。这些参数包含游标操作所需的值。

第三个也是最后一个特定于继电器的配置是突变的配置。`graphql-relay`包提供了一个名为`mutationWithClientMutationId`的特殊助手方法，用于轻松配置突变。需要四个字段:变异名称、输入字段、输出字段，最后是 mutate 和 get 有效载荷字段。在 GraphQL 中，所有的突变都伴随着一个查询，以获取任何可能已经更改的数据。Relay 通过智能地决定在发生突变后需要刷新哪些数据，进一步增强了这种能力。

变异名称是 React-Relay 应用程序在访问 GraphQL 服务器时用来调用变异的名称。输入字段对应于 GraphQL 突变的`args`。输出字段表示从变异中返回的类型的字段。mutate 和 get payload 字段将执行实际的数据库操作，并可以返回一个承诺，该承诺将延迟 GraphQL 对应用程序的响应，直到该承诺得到解决。

## 结论

React 和 GraphQL 结合 Relay 为构建 web 应用程序提供了一个有前途的框架。虽然需要进行大量的设置，但是一旦设置完成，开发就会顺利进行，消除样板代码，并智能地处理数据管理。Relay 框架可能会成为构建下一代 web 应用程序的游戏规则改变者。在下一篇文章中，我们将探索 React 使用 Relay 消耗 GraphQL 资源的过程。

这篇文章是微软技术传道者和 [DevelopIntelligence](http://www.developintelligence.com/) 关于实用 JavaScript 学习、开源项目和互操作性最佳实践的 web 开发系列的一部分，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?wt.mc_id=DX_838517) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?wt.mc_id=DX_838517)。DevelopIntelligence 通过他们的前端博客和课程网站 [appendTo](http://appendto.com/) 提供 [JavaScript 培训](http://appendto.com/courses/javascript/)和 [React 培训](http://appendto.com/courses/react-training/)课程。

我们鼓励你在包括微软 Edge(Windows 10 的默认浏览器)在内的各种浏览器和设备上进行测试，在[dev.microsoftedge.com](https://dev.windows.com/en-us/?wt.mc_id=DX_838517)上使用免费工具，包括[虚拟机](https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/?wt.mc_id=DX_838517)来测试微软 Edge 和 IE6 到 IE11 的版本。此外，[访问 Edge 博客](https://blogs.windows.com/msedgedev/?wt.mc_id=DX_838517)，从微软开发者和专家那里获得最新信息。

## 分享这篇文章