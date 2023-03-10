# 在服务器呈现的 React 中处理异步 API

> 原文：<https://www.sitepoint.com/asynchronous-apis-server-rendered-react/>

如果你曾经制作过一个基本的 React 应用程序页面，它可能会在较慢的设备上遇到较差的 SEO 和性能问题。您可以添加回传统的 web 页面的服务器端呈现，通常使用 NodeJS，但是这不是一个简单的过程，尤其是使用异步 API。

在服务器上呈现代码的两个主要好处是:

*   提高加载时间的性能
*   提高搜索引擎优化的灵活性。

请记住，Google 确实会等待您的 JavaScript 加载，所以像标题内容这样简单的事情会毫无问题地改变。(不过，我不能代表其他搜索引擎，也不知道它有多可靠。)

在这篇文章中，我将讨论在使用服务器呈现的 React 代码时从异步 API 获取数据。React 代码将应用程序的整个结构构建在 JavaScript 中。这意味着，与带有控制器的传统 MVC 模式不同，在应用程序被渲染之前，你不知道你需要什么数据。使用像 Create React App 这样的框架，您可以快速创建一个高质量的工作应用程序，但它要求您只在客户端处理渲染。这是一个性能问题，也是一个 SEO/数据问题，传统的模板引擎可以改变你认为合适的头部。

## 问题是

React 在大多数情况下是同步渲染的，所以如果没有数据，就渲染一个加载屏幕，等待数据到来。这在服务器上不太好，因为在渲染之前你不知道你需要什么，或者你知道你需要什么但是你已经渲染了。

看看这个标准的渲染方法:

```
ReactDOM.render(
  <provider store={store}>  <browserrouter>  <app></app>  </browserrouter>  </provider>
, document.getElementById('root')
) 
```

问题:

1.  这是一个寻找根元素的 DOM 呈现。我的服务器上没有这个，所以我们必须把它分开。
2.  我们不能访问主根元素之外的任何东西。我们不能设置脸书标签、标题、描述、各种 SEO 标签，并且我们不能控制元素之外的 DOM 的其余部分，尤其是头部。
3.  我们提供了一些状态，但是服务器和客户端有不同的状态。我们需要考虑如何处理这种状态(在本例中是 Redux)。

所以我在这里使用了两个库，它们非常受欢迎，所以希望它也适用于您正在使用的其他库。

存储服务器和客户端同步的状态是一个噩梦般的问题。这非常昂贵，并且通常会导致复杂的错误。在服务器端，理想情况下，除了能够正常工作和渲染之外，你不想对 Redux 做任何事情。(还可以正常使用；只需设置足够的状态，使其看起来像客户端。)如果您想尝试，可以从查看各种分布式系统指南开始。

**React-Router** :仅供参考，这是默认安装的 v4 版本，但如果您有一个旧的现有项目，它会有很大的不同。你需要确保处理好你的路由服务器端*和*客户端以及 v4——它在这方面做得很好。

毕竟，如果需要进行数据库调用呢？突然，这变成了一个大问题，因为它是异步的，并且在你的组件内部。当然，这并不是一个新问题:请查看官方反应报告。

您必须进行渲染，以确定您需要哪些依赖项——这些依赖项需要在运行时确定——并在提供给客户端之前获取这些依赖项。

## 现有解决方案

下面，我将回顾一下目前提供的解决这个问题的方案。

### Next.js

在我们去任何地方之前，如果你想要生产、服务器端渲染的 React 代码或通用应用， [Next.js](https://github.com/zeit/next.js/) 是你想要去的地方。它有效，干净，而且有 Zeit 做后盾。

然而，这是固执己见的，你必须使用他们的工具链，他们处理异步数据加载的方式不一定那么灵活。

查看 Next.js 回购文档中的直接副本:

```
import React from 'react'
export default class extends React.Component {
  static async getInitialProps ({ req }) {
    return req
      ? { userAgent: req.headers['user-agent'] }
      : { userAgent: navigator.userAgent }
  }
  render () {
    return <div> Hello World {this.props.userAgent}  </div>
  }
} 
```

`getInitialProps`是那里的键，它返回一个承诺，该承诺解析为一个填充 props 的对象，并且只在一个页面上。最棒的是，这是他们的工具链中内置的:添加它，它就可以工作，不需要任何工作！

那么如何获取数据库数据呢？你调用一个 API。你不想吗？啊，那太糟糕了。(好吧，所以你可以添加自定义的东西，但你必须自己完全实现它。)但是，如果您考虑到这一点，这是一个非常合理的，并且一般来说是一个好的实践，因为否则，您的客户端将仍然进行相同的 API 调用，并且您的服务器上的延迟实际上可以忽略不计。

您还受限于您可以访问的内容——基本上只是请求对象；同样，这似乎是一个好的实践，因为您无法访问您的状态，无论如何，这在您的服务器和客户端上都是不同的。哦，如果你以前没注意到的话，它只对顶级页面组件有效。

### 冗余连接

Redux Connect 是一个非常固执的服务器端渲染器，有着不错的理念，但是如果你不使用他们描述的所有工具，这可能不适合你。这个包有很多东西，但它太复杂了，还没有升级到 React 路由器 v4。这里有很多设置，但让我们来看最重要的部分，只是为了吸取一些教训:

```
// 1\. Connect your data, similar to react-redux @connect
@asyncConnect([{
  key: 'lunch',
  promise: ({ params, helpers }) => Promise.resolve({ id: 1, name: 'Borsch' })
}])
class App extends React.Component {
  render() {
    // 2\. access data as props
    const lunch = this.props.lunch
    return (
      <div>{lunch.name}</div>
    )
  }
} 
```

装饰者在 JavaScript 中不是标准的。在写作的时候它们是第二阶段，所以你可以自由使用。这只是增加高阶分量的另一种方式。这个想法很简单:关键是传递什么给你的道具，然后你有一个承诺的列表，它决定并被传递。这看起来不错。或许另一种选择很简单:

```
@asyncConnect([{
  lunch: ({ params, helpers }) => Promise.resolve({ id: 1, name: 'Borsch' })
}]) 
```

用 JavaScript 这似乎没有太多问题。

### 反作用-前负荷

[react-front-load](https://www.npmjs.com/package/react-frontload)repo 没有很多文档或解释，但也许我能得到的最好理解是通过测试(如[这个](https://github.com/davnicwil/react-frontload/blob/master/tests/index.test.js) )
和阅读源代码。当某个东西被挂载时，它被添加到一个承诺队列中，当它被解析时，它就被服务了。它做得很好，尽管很难推荐没有很好记录、维护或使用的东西:

```
const App = () => (
  <frontload isServer >  <component1 entityId='1' store={store}></component1>  </frontload>
)

return frontloadServerRender(() => (
  render(<app></app>)
)).then((serverRenderedMarkup) => {
  console.log(serverRenderedMarkup)
}) 
```

## 寻找更好的解决方案

上面的解决方案没有一个真正与我所期望的库的灵活性和简单性产生共鸣，所以现在我将展示我自己的实现。目标不是编写一个包，而是让你理解如何为你的用例编写你自己的包。

本示例解决方案的回购是这里的[和](https://github.com/ButterCMS/ssr-cra-async)。

### 理论

这背后的想法相对简单，尽管它最终是相当多的代码。这是对我们正在讨论的观点的概述。

服务器必须渲染 React 代码两次，我们将使用`renderToString`来完成。我们希望在第一次和第二次渲染之间保持一个上下文。在我们的第一次渲染中，我们试图消除任何 API 调用、承诺和异步动作。在我们的第二次渲染中，我们希望获得我们获得的所有数据，并将其放回我们的上下文中，因此渲染出我们的工作页面以供分发。这也意味着应用程序代码需要根据上下文执行(或不执行)操作，例如是在服务器上还是在客户端上，无论在哪种情况下是否正在获取数据。

此外，我们可以根据自己的意愿进行定制。在这种情况下，我们根据上下文更改状态代码和 head。

### 首次渲染

在你的代码中，你需要知道你是在服务器或浏览器之外工作的，理想情况下，你希望对其进行复杂的控制。使用 [React Router](https://www.npmjs.com/package/react-router) ，您可以获得一个静态上下文属性，这很好，所以我们将使用它。目前，我们只是添加了一个数据对象和请求数据，正如我们从 Next.js 中了解到的那样。我们的 API 在服务器和客户端之间是不同的，因此您需要提供一个服务器 API，最好具有与您的客户端 API 类似的接口:

```
const context = {data: {}, head: [], req, api}
const store = configureStore()
renderToString(
  <provider store={store}>  <staticrouter location={req.url}
      context={context}
    >  <app></app>  </staticrouter>  </provider>
) 
```

### 第二次渲染

就在你第一次渲染之后，我们将获取那些未完成的承诺，并等待这些承诺完成，然后重新渲染，更新上下文:

```
const keys = Object.keys(context.data)
const promises = keys.map(k=>context.data[k])
try {
  const resolved = await Promise.all(promises)
  resolved.forEach((r,i)=>context.data[keys[i]]=r)
} catch (err) {
  // Render a better page than that? or just send the original markup, let the front end handle it. Many options here
  return res.status(400).json({message: "Uhhh, some thing didn't work"})
}
const markup = renderToString(
  <provider store={store}>  <staticrouter location={req.url}
      context={context}
    >  <app></app>  </staticrouter>  </provider>
) 
```

### 应用

从我们的服务器快速跳转到应用程序代码:在我们的任何具有路由器连接的组件中，我们现在可以获得:

```
class FirstPage extends Component {
  async componentWillMount(){
    this.state = {text: 'loading'}

    this._handleData('firstPage')
  }
  async _handleData(key){
    const {staticContext} = this.props

    if (staticContext && staticContext.data[key]){
      const {text, data} = staticContext.data[key]
      this.setState({text, data})
      staticContext.head.push(
        <meta name="description" content={"Some description: "+text}/>
      )
    } else if (staticContext){
      staticContext.data[key] = this._getData()
    } else if (!staticContext && window.DATA[key]){
      const {text, data} = window.DATA[key]
      this.state = {...this.state, text, data}
      window.DATA[key] = null
    } else if (!staticContext) {
      const {text, data} = await this._getData()
      this.setState({text, data})
    }
  }
  async _getData(){
    const {staticContext} = this.props
    const myApi = staticContext ? staticContext.api : api
    const resp = await butter.post.list()
    const {data} = resp.data
    const {text} = await myApi.getMain()
    return {text, data}
  }
  render() {
    const text = this.state.text
    return (
      <div className='FirstPage'>  {text}  </div>
    )
  }
} 
```

哇，这么多复杂的代码。在这个阶段，您可能希望采用一种更为中继的方法，将您的数据获取代码分离到另一个组件中。

这个组件由您可能很熟悉的东西——一个渲染步骤和一个`componentWillMount`步骤——来保护。四阶段`if`语句处理不同的状态——预取、后提取、保存器呈现、后服务器呈现。在我们的数据被加载后，我们也添加到头部。

最后，还有一个获取数据的步骤。理想情况下，您的 API 和数据库具有相同的 API，这使得执行是相同的。您可能希望将这些放入 Thunk 或 Saga 中的一个操作中，使其更具可扩展性。

查看文章“[服务器端 React 渲染](https://css-tricks.com/server-side-react-rendering/)”和 repo [React 服务器端渲染](https://github.com/ButterCMS/react-ssr-example)了解更多信息。请记住，您仍然需要处理数据未被加载的状态！您将只在第一次加载时进行服务器渲染，因此您将在后续页面上显示加载屏幕。

### 更改`index.html`以添加数据

我们需要发送任何预取的数据作为页面请求的一部分，因此我们将添加一个脚本标记:

```
<script> window.DATA = {data:{}} // It doesn't really matter what this is, just keep it valid and replaceable </script> 
```

### 服务

然后，我们需要将它添加到我们的搜索和替换。然而，HTML 使用一个非常基本的脚本标签查找器，所以如果你有脚本标签，你需要对它进行 base-64 编码。此外，不要忘记我们的头牌！

```
// earlier on
const headMarkup = context.head.map(h=>(
  renderToStaticMarkup(h)
)).join('')

// then render
const RenderedApp = htmlData.replace('{{SSR}}', markup)
  .replace('{{head}}', headMarkup)
  .replace('{data:{}}', JSON.stringify(new Buffer(JSON.stringify(context.data)).toString('base64')))
if (context.code)
  res.status(context.code)
res.send(RenderedApp) 
```

我们还处理状态代码更改，例如，对于 404，如果您有 404 页面，您可以这样做:

```
class NoMatch extends Component {
  componentWillMount(){
    const {staticContext} = this.props
    if (staticContext){
      staticContext.code = 404
    }
  }
  render() {
    return (
      <div> Sorry, page not found </div>
    )
  }
} 
```

## 摘要

如果你不确定你在做什么，用 **Next.js** 就可以了。它是为服务器端渲染和通用应用程序设计的，或者如果您想要手动完成所有事情的灵活性，就按照您想要的方式。例如，如果您在子组件中获取数据，而不是在页面级别。

希望这篇文章对你有所帮助！别忘了检查一下 GitHub repo 的工作实现。

## 分享这篇文章