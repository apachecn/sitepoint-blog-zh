# 用 Choo 框架进行有趣的函数式编程

> 原文：<https://www.sitepoint.com/functional-programming-choo/>

这篇文章由[维尔丹·索蒂奇](https://www.sitepoint.com/author/vildansoftic)和[约舒瓦·乌茨](https://github.com/yoshuawuyts)进行了同行评议。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

![Train going over a bridge of iDevices](img/eb09fc5245db53d502ddb2f07e6abe92.png)

今天我们将探索由 [@yoshuawuyts](https://twitter.com/yoshuawuyts) 创作的[Choo](https://github.com/yoshuawuyts/choo)——这个小框架可以。

这是一个全新的框架，帮助您建立单页应用程序，包括状态管理，单向数据流，视图和路由器。使用 Choo，你可以编写类似风格的应用程序来进行反应和还原，但成本(文件大小)和 API 数量却很少。如果你喜欢最简单的框架，喜欢在前沿玩新技术，你会喜欢探索 Choo。因为它非常纤细，另一个很有意义的地方是用于*移动网络应用*，在那里你应该保持文件大小最小。

Choo 没有引入任何真正的新东西，它只是建立在来自 React、Redux、Elm、函数式编程范式和其他灵感的许多好想法的基础上。这是一个整洁的小 API，它将所有这些好东西包装到一个内聚的包中，您可以安装并开始构建单页面应用程序。

这篇文章将涉及 Choo v3。在我写这篇文章的时候，v4 还处于 alpha 版本，所以你需要留意变化——这列火车正在快速前进。

*注意*:如果你对像 [React](https://facebook.github.io/react/) 这样的声明式视图库和像 [Redux](http://redux.js.org/) 这样的状态管理库有所了解，那么这篇文章会很有意义。如果你还没有这方面的经验，你可能会发现[Choo Docs–Concepts](https://github.com/yoshuawuyts/choo#concepts)对重要概念提供了更深入的解释。

## 一定要在家里试试这个

接着拉下[演示报告](https://github.com/sitepoint-editors/choo-demo)并安装依赖项。

```
git clone https://github.com/sitepoint-editors/choo-demo
cd choo-demo
npm install 
```

有 npm 脚本来运行每个示例，例如

```
npm run example-1
npm run example-2 
```

## 你好，周

首先，我们需要 choo 包并创建一个应用程序。

在 GitHub 上查看文件: [1-hello-choo.js](https://github.com/sitepoint-editors/choo-demo/blob/master/1-hello-choo.js)

```
const choo = require('choo')
const app = choo() 
```

我们使用**模型**来容纳我们的状态和修改它的函数(reducers，effects & subscriptions)，这里我们用一个`title`属性初始化我们的状态。

```
app.model({
  state: {
    title: '🚂 Choo!'
  },
  reducers: {}
}) 
```

**视图**是将状态作为输入并返回单个 DOM 节点的函数。Choo 附带的`html`功能是一个包裹着[溜溜球](https://github.com/maxogden/yo-yo/)的包装器。

```
const html = require('choo/html')
const myView = (state, prev, send) => html` <div>
    <h1>Hello ${state.title}</h1>
    <p>It's a pleasure to meet you.</p>
  </div> ` 
```

这个`html`example``语法对你来说可能是新的，但是这里没有魔法，它是一个 ES6 标记的模板文字。参见[让我们和凯尔](https://www.youtube.com/watch?v=c9j0avG5L4c)一起写代码这一集，以获得对它们的详细解释。

**Routes** 将 URL 映射到视图，在这种情况下`/`匹配所有 URL。

```
app.router(route => [
  route('/', myView)
]) 
```

为了让这个火车头运行起来，我们调用`app.start`并将根节点添加到文档中。

```
const tree = app.start()
document.body.appendChild(tree) 
```

我们结束了。运行`npm run example-1`，您应该会看到以下文档:

```
<div>
  <h1>Hello 🚂 Choo!</h1>
  <p>It's a pleasure to meet you.</p>
</div> 
```

我们正在通过 Choo 的微型 API 取得稳步进展。我们已经有了基本的路由，并且正在使用来自模型的数据呈现视图。真的没有那么多要学的了。

在文档中阅读更多:[模型](https://github.com/yoshuawuyts/choo#models)，[视图](https://github.com/yoshuawuyts/choo#views)

### 在浏览器中运行 Choo

如果你在家学习，这些例子都是使用一个名为 [budo](https://github.com/mattdesl/budo) 的开发服务器，用 browserify 编译源代码，并在一个简单的 HTML 页面中运行脚本。这是使用 Choo 示例的最简单的方法，但是你也可以轻松地将 Choo 与其他捆绑器集成，或者看看[最小香草方法](https://github.com/yoshuawuyts/choo/blob/0e7c13a14dc0d47fe1724bbfe571384e7aa14950/examples/vanilla/index.html)，如果那是你的难题的话。

## 变来变去

现在，我敢肯定，在这一点上你的想法是惊人的，唉，使用 Choo 来呈现这样的静态内容是毫无意义的。当你有随时间变化的状态和动态视图时，Choo 变得有用:这意味着对事件、定时器、网络请求等做出响应。

视图中的**事件**可以用`onclick`等属性注册，参见[悠悠球事件属性](https://github.com/maxogden/yo-yo/blob/master/update-events.js)的完整列表。事件可以触发动作，函数`send`以一个缩减器和数据的名义传递。

查看 GitHub 上的文件: [2-state-changes.js](https://github.com/sitepoint-editors/choo-demo/blob/master/2-state-changes.js)

```
const myView = (state, prev, send) => {
  function onInput(event) {
    send('updateTitle', event.target.value)
  }

  return html` <div>
      <h1>Hello ${state.title}</h1>
      <p>It's a pleasure to meet you.</p>
      <label>May I ask your name?</label>
      <input value=${state.title} oninput=${onInput}>
    </div> `
} 
```

如果你使用过流行的 Redux 库，那么 reducer 看起来会很熟悉，它们是接受先前状态和有效载荷并返回新状态的函数。

```
app.model({
  state: {
    title: '🚂 Choo!'
  },
  reducers: {
    updateTitle: (data, state) => {
      return { title: data }
    }
  }
}) 
```

**视图更新**由 [morphdom](https://github.com/patrick-steele-idem/morphdom) 处理。与 React 一样，您不需要担心手动 DOM 操作，这个库处理状态变化之间的 DOM 转换。

运行示例:`npm run example-2`

## 组件树

将一个复杂的 UI 分解成易于管理的小块 UI 是有意义的。

**视图**可以包含传递所需数据的其他视图以及`send`函数，以便子组件可以触发动作。

我们的新视图将把一个`item`作为输入，输出一个`<li>`，它可以触发我们之前看到的相同的`updateTitle`动作。

查看 GitHub 上的文件: [3-component-tree.js](https://github.com/sitepoint-editors/choo-demo/blob/master/3-component-tree.js)

```
const itemView = (item, send) => html` <li>
    <span>Go ahead ${item.name},</span>
    <button onclick=${() => send('updateTitle', item.name)}>make my day</button>
  </li> ` 
```

视图只是函数，所以您可以在模板文字占位符`${}`内的任何表达式中调用它们。

```
const myView = (state, prev, send) => html` <div>
    <ul>
      ${state.items.map(item => itemView(item, send))}
    </ul>
  </div> ` 
```

这就是了，Choo 视图中的 Choo 视图。

运行示例:`npm run example-3`

## 效果

**效果**是可以触发其他动作的功能，不直接修改状态。它们与 Redux 中的动作创建者相同，可以处理异步流。

> 效果的例子包括:执行 XHR 请求(服务器请求)，调用多个 reducers，将状态保存到 localstorage。

查看 GitHub 上的文件: [4-effects.js](https://github.com/sitepoint-editors/choo-demo/blob/master/4-effects.js)

```
const http = require('choo/http')
app.model({
  state: {
    items: []
  },
  effects: {
    fetchItems: (data, state, send, done) => {
      send('updateItems', [], done)
      fetch('/api/items.json')
        .then(resp => resp.json())
        .then(body => send('updateItems', body.items, done))

    }
  },
  reducers: {
    updateItems: (items, state) => ({ items: items })
  }
}) 
```

可以用调用 reducers 的同一个`send`函数来调用效果。视图有两个重要的生命周期事件，因此当 DOM 节点被添加到 DOM 和从 DOM 中删除时，您可以触发操作。这些是`onload`和`onunload`。在这里，一旦视图被添加到 DOM 中，我们就触发我们的`fetchItems`效果。

```
const itemView = (item) => html`<li>${item.name}</li>`

const myView = (state, prev, send) => html` <div onload=${() => send('fetchItems')}>
    <ul>
      ${state.items.map(item => itemView(item))}
    </ul>
  </div> ` 
```

运行示例:`npm run example-4`

在文档中阅读更多:[效果](https://github.com/yoshuawuyts/choo#effects)

## 捐款

> 订阅是从数据源接收数据的一种方式。例如，当使用聊天应用程序的 [SSE 或 Websockets](https://www.sitepoint.com/real-time-apps-websockets-server-sent-events/) 监听来自服务器的事件时，或者当捕捉视频游戏的键盘输入时。

**订阅**在`app.start`注册。下面是一个使用订阅来监听按键并将按键存储在 state 中的示例。

查看 GitHub 上的文件: [5-subscriptions.js](https://github.com/sitepoint-editors/choo-demo/blob/master/5-subscriptions.js)

```
const keyMap = {
  37: 'left',
  38: 'up',
  39: 'right',
  40: 'down'
}

app.model({
  state: {
    pressedKeys: {
      left: false,
      up: false,
      right: false,
      down: false
    }
  },
  subscriptions: [
    (send, done) => {
      function keyChange(keyCode, value) {
        const key = keyMap[keyCode]
        if (!key) return

        const patch = {}
        patch[key] = value
        send('updatePressedKeys', patch, done)
      }
      window.addEventListener('keydown', (event) => {
        keyChange(event.keyCode, true)
      }, false)
      window.addEventListener('keyup', (event) => {
        keyChange(event.keyCode, false)
      }, false)
    }
  ],
  reducers: {
    updatePressedKeys: (patch, state) => ({
      pressedKeys: Object.assign(state.pressedKeys, patch)
    })
  }
}) 
```

运行示例:`npm run example-5`

阅读更多文档:[订阅](https://github.com/yoshuawuyts/choo#subscriptions)

## 按指定路线发送

下面你可以看到一个关于 Choo 中路由工作的更完整的例子。这里的`app.router`是围绕[片状路由器](https://github.com/yoshuawuyts/sheet-router)包的包装器，它支持默认和嵌套路由。您也可以使用位置缩减器`send('location:setLocation', { location: href })`编程更新路线。

查看 GitHub 上的文件: [6-routes.js](https://github.com/sitepoint-editors/choo-demo/blob/master/6-routes.js)

要从一个视图链接到另一个视图，只需使用链接即可。

```
const homeView = (state, prev, send) => html` <div>
    <h1>Welcome</h1>
    <p>Check out your <a href="/inbox">Inbox</a></p>
  </div> ` 
```

路由本身可以像这样注册。

```
app.router(route => [
  route('/', homeView),
  route('/inbox', inboxView, [
    route('/:id', mailView),
  ])
]) 
```

可以通过`state.params`访问 URL 的动态部分

```
const mailView = (state, prev, send) => {
  const email = state.items.find(item => item.id === state.params.id)
  return html` <div>
      ${navView(state)}
      <h2>${email.subject}</h2>
      <p>${email.to}</p>
    </div> `
} 
```

运行示例:`npm run example-6`

在文档中了解更多:[路由器](https://github.com/yoshuawuyts/choo#router)

## 组件状态和叶节点

Choo 视图被设计成接受数据并返回 DOM 节点的纯函数。React 已经表明，这是构建声明性 ui 的一种很好的方式，但是它也有缺点。如何将组件包含到 Choo 视图中，维护它们自己的状态并修改它们自己的 DOM 节点？如何在 Choo 中包含不纯的组件并利用大量的 DOM 库呢？

这里有一个天真的尝试，试图在 Choo 视图中包含一个 d3 数据可视化。向`onload`函数传递了对添加的 DOM 节点的引用，我们可以用 d3 成功地修改该元素，但是在重新渲染时，我们的 viz 就消失了，永远地…

```
const dataVizView = (state) => {
  function load(el) {
    d3.select(el)
      .selectAll('div')
      .data(state.data)
      .enter()
      .append('div')
      .style('height', (d)=> d + 'px')
  }

  return html` <div onload=${load}></div> `
} 
```

Choo 使用的 diffing 库(morphdom)在`isSameNode`中提供了一个 escape hatch，可以用来防止重新渲染。Choo 的 [cache-element](https://github.com/yoshuawuyts/cache-element/) 包含包装该行为的函数，以简化在 Choo 中缓存和制作小部件所需的代码。

在 GitHub 上查看文件: [7-friends.js](https://github.com/sitepoint-editors/choo-demo/blob/master/7-friends.js)

```
const widget = require('cache-element/widget')
const dataViz = widget(update => {
  update(onUpdate)

  const el = html`<div></div>`
  return el

  function onUpdate(state) {
    const bars = d3.select(el)
      .selectAll('div.bar')
      .data(state.data)

    bars.style('height', (d)=> d + 'px')

    bars.enter()
      .append('div')
      .attr('class', 'bar')
      .style('height', (d)=> d + 'px')
  }
})
const dataVizView = (state, prev, send) => dataViz(state) 
```

运行示例:`npm run example-7`

我们现在已经触及了 Choo API 的所有主要组件，我告诉过你它很小。

还有[应用，使用](https://github.com/yoshuawuyts/choo#appusehooks)来扩展 Choo 的工作方式，允许你在`onAction`和`onStateChange`等不同点拦截它的流程，并执行你自己的代码。这些钩子可以用来创建插件或中间件。

另外，**服务器端渲染**可以通过 [app.toString(route，state)](https://github.com/yoshuawuyts/choo#html--apptostringroute-state) 实现。

## 单元测试

函数式编程最受推崇的优点之一是可测试性，那么 Choo 的表现如何呢？

### 组件规格

Choo 视图是纯函数，将状态作为输入并返回一个 DOM 节点，因此很容易测试。下面是如何用 Mocha 和 Chai 渲染一个节点并在其上做出断言。

```
const html = require('choo/html')
const myView = (state) => html` <div class="my-view">
    ${JSON.stringify(state)}
  </div> `

describe('Component specs', () => {
  it('should return a DOM node', () => {
    const el = myView({hello: 'yep'})

    expect(el.innerHTML).to.contain('{"hello":"yep"}')
    expect(el.className).to.equal('my-view')
  })
}) 
```

### 减速器规格

测试 reducers 是类似的，它们是以状态和有效载荷作为输入并返回新状态的函数。您需要将每个 reducer 函数从模型中提取出来，以便能够独立地测试它们。

```
const myReducer = (data, state) => {
  return { title: data }
}

describe('Reducer specs', () => {
  it('should reduce state', () => {
    const prev = { title: 'hello!' }
    const state = myReducer(prev, "🚂 Choo!")

    expect(state.title).to.equal("🚂 Choo!")
  })
}) 
```

这些只是展示单元测试故事可以为 Choo 应用寻找什么的例子。每个概念都是用纯函数实现的，所以可以很容易地单独测试。

## 强项

很简单，很有凝聚力。路线、视图、动作和减速器之间的可预测流程使学习变得简单，使用起来也很有趣。微小的 API 意味着一旦你知道这些组件是如何协同工作的，你就可以开始构建而不需要查看详细的文档。

几乎不需要工具。不需要 JSX 或复杂的构建管道， [browserify](http://browserify.org/) 是所有被推荐的将依赖关系整合成一个包的工具。那可以简单到`browserify ./entry.js -o ./bundle.js`。

它是一次性的。在 Choo 中构建应用程序的一部分并不是终身监禁。视图只是返回 DOM 节点的函数，因此它们可以在任何使用 DOM 的地方使用。

最小的 **5kb** 内存意味着您可以放心地包含其他版本的 Choo 或其他框架。这是节食的框架。

## 弱点

它还不成熟，将会有突破性的变化。参见 [v4 变更日志](https://github.com/yoshuawuyts/choo/blob/4-changelog/CHANGELOG.md)中 API 如何成为移动目标的示例。虽然进步是一件好事，但是版本之间的迁移却是一个潜在的不利因素。

您可能需要手动优化。像 React 和 Angular 这样希望拥有整个应用程序的大型库可以做一些事情，比如在树的顶部使用委托来优化事件。如果你想要事件委托，你需要了解它是如何工作的，并通过在顶级组件注册事件来实现它。

它没有经过战斗考验。当您采用 React 这样的库时，您可以放心地这样做，因为它在一些最大的网站上使用。你知道它已经过彻底的测试，即使在旧的浏览器上也能正常工作。

这是新来的孩子。受欢迎意味着你可以利用知识网络和现成的组件。最后一个例子展示了如何在一个视图中包含操纵部分 DOM 的其他库，但是在这个阶段它仍然是非常原始的。在撰写本文时，实现这一点的补丁才出现了几天。

## 结论

我个人很喜欢 Choo。它用一个友好的 API 汇集了许多伟大的想法。我可以想象自己一开始在业余项目中使用它，看看它在各种场景中是如何工作的。我期望在某个时候达到极限，但当你在最前沿工作时，那就是突破。

如果这激起了你的兴趣，你可能想通读一下[自述文件](https://github.com/yoshuawuyts/choo/)，探索一下[演示文件](https://github.com/yoshuawuyts/choo/#demos)，或者阅读正在编写的[手册](https://yoshuawuyts.gitbooks.io/choo/content/)，以获得作者的更多示例。

你怎么想呢?试试吧，在下面的评论中告诉我们你的进展如何。

## 分享这篇文章