# 用继电器反应数据获取

> 原文：<https://www.sitepoint.com/react-data-fetching-with-relay/>

React 正在引领前端的新时代。由脸书出版，它迅速成为许多科技公司生产中使用的流行库。在本文中，我们将讨论一个新的补充 React 框架，称为 Relay。

## React 中数据获取的问题

随着 React 越来越受欢迎，用 React 构建的项目的规模和复杂性也随之增长。React 主要是一个视图层库。这导致一些团队在构建需要不同基础设施的项目时遇到未知领域或限制。在这些成长的痛苦中，脸书一直积极主动地提供支持和指导。

### 流量

对于使用 React 的开发人员来说，早期的一个棘手问题是事件处理。脸书通过发布 Flux 对这个问题做出了回应，Flux 是一种抽象模式，它鼓励单向数据流来处理 React 中的事件。

我假设您对 Flux 有一些了解，所以我不会在本文中讨论它的细节。如果你对这个话题不熟悉，[你可以阅读一下 Sandeep Panda](https://www.sitepoint.com/creating-note-taking-app-react-flux/) 或[的这篇文章，看看这个关于通量](https://facebook.github.io/react/blog/2014/05/06/flux.html)的页面。

Flux 将 React 生态系统带到了一个新的高度。随着开发人员开始越来越熟悉 Flux，一些问题出现了。Flux 对于将数据作为应用程序状态进行管理非常有用，但是将初始状态填充到应用程序中却是一个问题。

围绕 Flux 的数据初始化有几个挑战。商店会调用服务器并填充它们自己吗？我们使用调度员的复水方法吗？我们在服务器上调用一系列动作来填充商店吗？我们如何异步地做到这一点，并在返回响应之前将我们所有的数据加载到同构应用程序的服务器上？

## 什么是接力？

Relay 是脸书发布的一个新的 React 数据获取框架。Relay 旨在为所有这些数据获取问题提供一个清晰的解决方案。

Relay 有几个主要卖点:

*   声明性:这也是 React 的一个主要特性。Relay 利用声明性代码样式定义实现数据依赖，这与视图组件的定义方式非常相似。与传统的命令式数据获取 API 相比，这是一个令人耳目一新的变化。

*   搭配:数据依赖定义与组件定义共存，这使得推断 UI 组件需要呈现什么数据变得更加容易。这使得对项目代码进行故障排除变得更加容易。查看一个包含 React 组件定义的文件，它需要什么数据才能工作就一目了然了。

*   突变:突变支持对 React 视图订阅的数据进行无缝修改，并将这些修改填充到数据持久层。

### 继电器与通量

Flux 是一个抽象的概念，而 Relay 是受这个概念启发而实现的。Relay 建立在 Flux 概念的基础上，具有相同的 dispatcher、actions 和 stores 概念，但是它们的表示方式稍有不同。继电器有一个新概念，称为“高阶组件”，我们将在文章的剩余部分展开这个主题。

在这个阶段，还不清楚 Relay 是否会取代现有的 Flux 实现或与之一致。例如， [Redux](https://github.com/rackt/redux) ，一个流行的 Flux 实现，也利用了‘高阶组件’。试图同时使用 Redux 和 Relay 会导致关于哪个框架绑定到 UI 组件的冲突。目前[正在讨论 Redux 与 Relay](https://facebook.github.io/react/blog/2014/05/06/flux.html) 的关系。

## 高阶组件

高阶分量或 hoc 的定义方式与常规 React 分量相同。HOCs 包装子 UI 组件。HOC 将执行其查询，然后呈现子 UI 组件，将查询数据作为*道具*传入。通量流现在由特设委员会管理，后者将充当调度员。它有类似于`setQueryParams()`的方法，可以被认为是一个通量动作。调用`setQueryParams()`触发通量流。在特设中定义的查询被更新，新的数据被提取，并且数据被持久存储在特设中。通过保存这些数据，HOC 充当了一个流量存储器。

下面是一个`ProfilePicture`组件和一个补充特设的简化示例。想象一下，我们已经定义了`ProfilePicture`来渲染整个项目中的用户头像。我们需要获取数据来显示用户头像。然后，我们创建一个中继节点，从数据库中查询用户资料图片。特设将此查询数据传递给子 ProfilePicture 组件:

```
class ProfilePicture extends React.Component {
  // A standard Profile Picture component
}

// This is our Higher Order Component. It fetches the data to pass
// as props to Profile Picture
module.exports = Relay.createContainer(ProfilePicture, {
  fragments: {
    user: () => Relay.QL` fragment on User {
        profilePicture(size: $size) {
          uri,
        },
      } `,
  },
});
```

我们的`ProfilePicture`组件将获得一些新的本地函数作为道具传入。这实质上就是继电器触发磁通的方式。该组件调用这些继电器 prop 函数，这相当于 Flux 动作调用。这将导致中继获取请求的最新数据。一旦完成，它将填充其内部存储，并将其作为道具传递给 HOC 的子视图组件。

## GraphQL

上面的例子可能看起来有点奇怪，尤其是这一部分:

```
Relay.QL` fragment on User {
        profilePicture(size: $size) {
          uri,
        },
      } `,
```

Relay 背后的大部分魔力是由 GraphQL 提供的。GraphQL 是来自脸书的一种新的查询语言，专门处理图形数据结构。深入讨论 GraphQL 超出了本文的范围，但是[您可以通过阅读涵盖这个主题的 Relay 文档](https://facebook.github.io/react/blog/2015/02/20/introducing-relay-and-graphql.html)来深化这个主题。现有的项目不会被设置为开箱即用地使用 GraphQL。[开始使用继电器](https://facebook.github.io/relay/docs/getting-started.html#content)的第一个推荐步骤是:

1.  创建一个 GraphQL 模式
2.  创建一个 GraphQL 服务器

应该注意的是，将现有项目转换为 GraphQL 模式并设置或修改现有服务器以使其对 GraphQL 友好可能需要大量的工作。当从头开始一个项目时，立即使用 Relay 可能更有意义。脸书提供了一个 [Relay Starter Kit](https://github.com/facebook/relay-starter-kit) ，看起来是一个在新项目中使用 Relay 和 GraphQL 的很好的解决方案。

## 无图表的继电器

由于设置 GraphQL 需要额外的开销，脸书的 Relay 可能不适合现有项目。幸运的是，有另一个受 Relay 启发的库可能更适合这些名为 react-transmit 的项目。react-transmit 是一个开源项目，旨在成为一个“基于承诺而不是 GraphQL 的受继电器启发的库”

如果我们将上面的配置文件示例更新为使用`react-trasmit`,那么我们会得到以下代码:

```
// Import Transmit
import Transmit from "react-transmit";

class ProfilePicture extends React.Component {
  // A standard Profile Picture component
}

// This is our Higher Order Component. It fetches the data to pass
// as props to Profile Picture
Transmit.createContainer(ProfilePicture, {
  fragments: {
    user: (userId) => {
      return new Promise(function(resolve, reject) { 
          // Do some Ajax here and resolve the promise
      });
    }
  },
});
```

react-transmit 示例看起来非常类似于 Relay 示例。然而，在这个实例中,`user`片段现在是一个返回承诺而不是 GraphQL 查询的函数。

## 继电器的当前状态

脸书发布了中继的[开源“技术预览”。他们在资源库中有一些很棒的例子，展示了如何使用 Relay 和非常详细的](https://facebook.github.io/react/blog/2015/08/11/relay-technical-preview.html)[文档部分](https://facebook.github.io/relay/)。

现在看来 Relay 不太适合同构的应用。没有办法告诉 Relay 在呈现其子视图之前等待所有的数据依赖项都已加载，这是服务器上需要的。如果你感兴趣，有一个正在进行的关于中继如何在服务器上工作的讨论。目前，这是一种反应-传输在当前更适合的模式。

至于 Relay 的未来，它的路线图旨在很快提供一些关键功能:

*   其他数据存储类型的适配器，因此对 GraphQL 没有硬性依赖。
*   [更好的同构支持](https://github.com/facebook/relay/issues/136)，如前所述。

## 结论

在本文中，我们已经讨论了一个名为 Relay 的新的 React 补充框架。Relay 基于一些相同的通量概念，由 GraphQL 供电。正如我提到的，对于已经存在的项目，Relay 可能不是一个好的选择。然而，这个框架是相当新的，我希望它在每个版本中变得越来越好。

现在，轮到你了。你知道接力赛吗？你曾经在你的项目中采用过它吗？

## 分享这篇文章