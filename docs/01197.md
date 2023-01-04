# 使用 Next.js 编写服务器渲染的 React 应用程序

> 原文：<https://www.sitepoint.com/writing-server-rendered-react-apps-next-js/>

就 JavaScript 前端生态系统而言，尘埃落定。在这一点上，React 可以说拥有最大的思想份额，但是它有许多你需要熟悉的附加功能。Vue 提供了一个简单得多的选择。然后还有 Angular 和 Ember——虽然它们仍然很受欢迎，但并不是开始新项目的首选。

因此，尽管 React 是最受欢迎的选择，但它仍然需要大量工具来编写生产就绪的应用程序。创建 React 应用解决了许多启动时的痛点，但在没有[驱逐](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#npm-run-eject)的情况下，你还能坚持多久还不得而知。当您开始研究前端单页应用程序(SPAs)的当前最佳实践(如服务器端渲染、代码分割和 CSS-in-JS)时，您会发现有很多问题需要解决。

这就是下一个出现的地方。

## 为什么是下一个？

Next 提供了一个简单但可定制的解决方案来构建生产就绪型水疗中心。还记得 web 应用程序是如何用 PHP 构建的吗(在“web 应用程序”成为一个术语之前)？您在一个目录中创建一些文件，编写脚本，然后就可以部署了。对于 JavaScript 生态系统来说，这就是 Next 的目标。

Next 本身并不是一个全新的框架。它完全支持 React，但在此基础上提供了一个框架，用于在遵循最佳实践的同时构建 spa。您仍然编写 React 组件。你能用 Next 做的任何事情，你都可以用 React、Webpack、Babel、17 个 CSS-in-JS 替代方案之一、lazy imports 等等的组合来做。但是在使用 Next 构建时，您不会考虑使用哪种 CSS-in-JS 替代方案，或者如何设置热模块替换(HMR)，或者选择许多路由选项中的哪一个。你只是在使用 Next——而它*只是在*工作。

> 我愿意认为我对 JavaScript 略知一二，但是接下来。JS 节省了我大量的时间。— [埃里克·埃利奥特](https://twitter.com/_ericelliott/status/867160902184845312)

## 入门指南

Next 需要最少的设置。这为您提供了启动所需的所有依赖项:

```
$ npm install next react react-dom --save 
```

为您的应用程序创建一个目录，并在其中创建一个名为`pages`的目录。文件系统就是 API。每一个`.js`文件都变成了自动处理和渲染的路径。

在您的项目中创建一个包含以下内容的文件`./pages/index.js`:

```
export default () => (
  <div>Hello, Next!</div>
) 
```

在您的项目中填充以下内容:

```
{
  "scripts": {
    "dev": "next",
    "build": "next build",
    "start": "next start"
  }
} 
```

然后只需运行项目根目录下的`npm run dev`。转到 [http://localhost:3000](http://localhost:3000) ，你应该能看到你的应用程序，运行在其所有的荣耀！

就凭这一点你就能得到:

*   自动传输和捆绑(使用 Webpack 和 Babel)
*   热模块更换
*   `./pages`的服务器端渲染
*   静态文件服务:`./static/`映射到`/static/`。

祝你好运，香草反应有这么多的设置！

## 特征

让我们深入了解现代水疗应用的一些功能，为什么它们很重要，以及它们如何*与 Next*配合工作。

### 自动代码分割

**为什么很重要？**
代码拆分对于保证快速[第一次有意义的绘制](https://developers.google.com/web/tools/lighthouse/audits/first-meaningful-paint)很重要。如今，JavaScript 包的大小达到几兆字节并不罕见。通过网络为每个页面发送所有的 JavaScript 是对带宽的巨大浪费。

**如何用 Next**
用 Next，*只有*申报的进口才配每页。因此，假设您的`package.json`中有 10 个依赖项，但是`./pages/index.js`只使用其中的一个。

**pages/login.js**

```
import times from 'lodash.times'
export default () => (
  return <div>times(5, <h2> Hello, there! </h2>)</div>;
) 
```

现在，当用户打开登录页面时，它不会加载所有的 JavaScript，而只加载该页面所需的模块。

所以某个页面可能有胖进口，像这样:

```
import React from 'react'
import d3 from 'd3'
import jQuery from 'jquery' 
```

但这不会影响其余页面的性能。更快的装载时间。

### 作用域 CSS

**为什么很重要？**
默认情况下，CSS 规则是全局的。假设您有这样一个 CSS 规则:

```
.title {
  font-size: 40px;
} 
```

现在，您可能有两个组件，`Post`和`Profile`，这两个组件都可能有一个带有类`title`的 div。您定义的 CSS 规则将适用于这两者。因此，您现在定义了两个规则，一个用于选择器`.post .title`，另一个用于`.profile .title`。对于小应用来说是可以管理的，但是你只能想到这么多的类名。

作用域 CSS 允许您用组件定义 CSS，并且那些规则只应用于那些组件，确保您每次接触 CSS 时都不会担心意外的影响。

**下一个**
下一个是 [styled-jsx](https://github.com/zeit/styled-jsx) ，它提供了对隔离作用域 CSS 的支持。因此，您的 React 组件渲染函数中只有一个`<style>`组件:

```
export default () => (
  <div> Hello world <p>These colors are scoped!</p>  <style jsx>{\
      p {
        color: blue;
      }
      div {
        background: red;
      }
    `}</style>  </div>
) 
```

样式(CSS)、行为(JS)和模板(JSX)都在一个地方，这也是主机代管的好处。不再需要搜索相关的类名来查看应用了什么样式。

### 动态导入

**为什么很重要？**
动态导入让你在运行时动态加载 JavaScript 应用程序的一部分。这有几个动机，如[提案](https://github.com/tc39/proposal-dynamic-import)中所列:

> 这可能是因为只有在运行时才知道的因素(例如用户的语言)，出于性能原因(直到可能要使用代码时才加载代码)，或者出于健壮性原因(在加载非关键模块失败后仍然存在)。

**With Next**
Next 支持动态导入提议，让你将代码分割成可管理的块。因此，您可以编写这样的代码，在初始加载后动态加载 React 组件:

```
import dynamic from 'next/dynamic'

const DynamicComponentWithCustomLoading = dynamic(
  import('../components/hello2'),
  {
    loading: () => <p>The component is loading...</p>
  }
)

export default () =>
  <div>  <Header />  <DynamicComponentWithCustomLoading />  <p>Main content.</p>  </div> 
```

### 按指定路线发送

**为什么很重要？**
通过 JavaScript 改变页面的一个问题是路径不会随之改变。在最初的日子里，水疗中心因破坏网络而受到批评。如今，大多数框架都有一些健壮的路由机制。React 有广泛使用的`react-router`包。然而，使用 Next，您不需要安装单独的软件包。

**与下一个**可以通过`next/link`组件启用客户端路由。考虑这两个页面:

```
// pages/index.js
import Link from 'next/link'

export default () =>
  <div> Click{' '}  <Link href="/contact">  <a>here</a>  </Link>{' '} to find contact information. </div> 
```

```
// pages/contact.js
export default () => <p>The Contact Page.</p> 
```

不仅如此，您还可以在`Link`组件中添加`prefetch` prop，甚至在链接被点击之前预取页面。这实现了路由之间的超快速转换。

### 服务器渲染

如果不禁用 JavaScript，大多数基于 JavaScript 的 spa 就无法运行。然而，事情并不一定是那样的。接下来在服务器上呈现页面，当 JavaScript 被禁用时，它们可以像旧的呈现网页一样被加载。目录中的每个组件都被服务器自动渲染，它们的脚本被内联。这具有非常快的首次加载的额外性能优势，因为您可以只发送一个呈现的页面，而不需要对 JavaScript 文件发出额外的 HTTP 请求。

## 后续步骤

这应该足以让你对 Next 感兴趣，如果你正在开发一个 web 应用程序，或者甚至是一个基于电子的应用程序，Next 提供了一些有价值的抽象和缺省值。

要了解更多关于 Next 的信息，学习 Next.js 是一个很好的起点，也许这就是你所需要的。

## 分享这篇文章