# HyperApp:用于构建前端应用程序的 1 KB JavaScript 库

> 原文：<https://www.sitepoint.com/hyperapp-1-kb-javascript-library/>

Hyperapp 是一个 JavaScript 库，用于构建功能丰富的 web 应用程序。它将务实的 [Elm](https://guide.elm-lang.org) 启发的状态管理方法与支持键控更新&生命周期事件的 VDOM 引擎相结合——所有这些都没有依赖性。给予或采取几个字节，整个源代码缩小和 gzipped 坐在大约 1 KB。

在本教程中，我将向您介绍 Hyperapp，并带您浏览几个代码示例，以帮助您立即入门。我假设你熟悉 HTML 和 JavaScript，但是不要求你有其他框架的经验。

## 你好世界

我们将从一个简单的演示开始，展示所有移动部件的协同工作。

你也可以[在线](https://codepen.io/hyperapp/pen/zNxZLP?editors=0010)试代码。

```
import { h, app } from "hyperapp"
// @jsx h

const state = {
  count: 0
}

const actions = {
  down: () => state => ({ count: state.count - 1 }),
  up: () => state => ({ count: state.count + 1 })
}

const view = (state, actions) => (
  <div>  <h1>{state.count}</h1>  <button onclick={actions.down}>-</button>  <button onclick={actions.up}>+</button>  </div>
)

app(state, actions, view, document.body) 
```

这或多或少是每个 Hyperapp 应用程序的样子。单个状态对象、填充状态的动作以及将状态和动作转换成用户界面的视图。

在 app 函数中，我们复制了你的状态和动作(改变不属于我们的对象是不礼貌的),并将它们传递给视图。我们还包装了您的操作，以便它们在每次状态改变时重新呈现应用程序。

```
app(state, actions, view, document.body) 
```

状态是描述应用程序数据模型的普通 JavaScript 对象。也是不可改变的。要改变它，你需要定义动作并调用它们。

```
const state = {
  count: 0
} 
```

在视图中，您可以显示状态的属性，用它来决定您的 UI 应该显示或隐藏哪些部分，等等。

```
<h1>{state.count}</h1> 
```

您还可以将操作附加到 DOM 事件，或者在您自己的内联事件处理程序中调用操作。

```
<button onclick={actions.down}>-</button>
<button onclick={actions.up}>+</button> 
```

动作不会直接改变状态，而是返回状态的一个新片段。如果您试图改变一个动作中的状态，然后返回它，视图将不会像您预期的那样重新呈现。

```
const actions = {
  down: () => state => ({ count: state.count - 1 }),
  up: () => state => ({ count: state.count + 1 })
} 
```

app 调用返回连接到状态-更新视图-渲染循环的 actions 对象。您还可以在视图函数和操作中接收这个对象。向外界公开这个对象是有用的，因为它允许你从另一个程序、框架或普通的 JavaScript 与你的应用程序对话。

```
const main = app(state, actions, view, document.body)

setTimeout(main.up, 1000) 
```

### 关于 JSX 的一个笔记

为了熟悉起见，我将在本文的其余部分使用 [JSX](https://facebook.github.io/react/docs/introducing-jsx.html) ，但不要求你在 Hyperapp 中使用 JSX。备选方案包括内置的`h`函数、 [@hyperapp/html](https://github.com/hyperapp/html) 、 [hyperx](https://github.com/substack/hyperx) 和 [t7](https://github.com/trueadm/t7) 。

这是上面使用 [@hyperapp/html](https://github.com/hyperapp/html) 的同一个例子。

```
import { app } from "hyperapp"
import { div, h1, button } from "@hyperapp/html"

const state = { count: 0 }

const actions = {
  down: () => state => ({ count: state.count - 1 }),
  up: () => state => ({ count: state.count + 1 })
}

const view = (state, actions) =>
  div([
    h1(state.count),
    button({ onclick: actions.down }, "–"),
    button({ onclick: actions.up }, "+")
  ])

app(state, actions, view, document.body) 
```

### 虚拟 DOM

虚拟 DOM 是对 DOM 应该是什么样子的描述，使用一个嵌套的 JavaScript 对象树，称为虚拟节点。

```
{
  name: "div",
  props: {
    id: "app"
  },
  children: [{
    name: "h1",
    props: null,
    children: ["Hi."]
  }]
} 
```

应用程序的虚拟 DOM 树是在每个渲染周期从头开始创建的。这意味着我们在每次状态改变时调用视图函数，并使用新计算的树来更新实际的 DOM。

我们通过比较新的虚拟 DOM 和以前的虚拟 DOM，尝试用尽可能少的 DOM 操作来完成。这导致了高效率，因为通常只有一小部分节点需要改变，并且与重新计算虚拟 DOM 相比，改变真实 DOM 节点的成本很高。

为了帮助您以更紧凑的方式创建虚拟节点，Hyperapp 提供了`h`函数。

```
import { h } from "hyperapp"

const node = h(
  "div",
  {
    id: "app"
  },
  [h("h1", null, "Hi.")]
) 
```

另一种创建虚拟节点的方法是使用 [JSX](https://reactjs.org/docs/introducing-jsx.html) 。JSX 是一种 JavaScript 语言扩展，用于表示动态 HTML。

```
import { h } from "hyperapp"

const node = (
  <div id="app">  <h1>Hi.</h1>  </div>
) 
```

浏览器不理解 JSX，所以我们需要把它编译成`h`函数调用，因此有了 import `h`语句。让我们使用[巴别塔](https://github.com/babel/babel)来看看这个过程是如何工作的。

首先，安装依赖项:

```
npm i babel-cli babel-plugin-transform-react-jsx 
```

然后创建一个`.babelrc`文件:

```
{
  "plugins": [
    [
      "transform-react-jsx",
      {
        "pragma": "h"
      }
    ]
  ]
} 
```

并从命令行编译代码:

```
npm run babel src/index.js > index.js 
```

如果你不喜欢使用构建系统，你也可以从 CDN 加载 Hyperapp，比如 [unpkg](https://unpkg.com/hyperapp) ，它将通过`window.hyperapp`对象在全球可用。

## 例子

### Gif 搜索框

在这个例子中，我将向您展示如何使用 [Giphy API](https://api.giphy.com/) 构建一个 Gif 搜索框来异步更新状态

为了产生副作用，我们在回调中或者当一个承诺实现时，在其他动作中调用动作。

返回`null`、`undefined`或`Promise`对象的动作不会触发视图重新渲染。如果一个动作返回一个承诺，我们将把承诺传递给调用者，允许您创建[异步](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)动作，如下例所示。

[实例](https://codepen.io/hyperapp/pen/ZeByKv)

```
import { h, app } from "hyperapp"
// @jsx h

const GIPHY_API_KEY = "dc6zaTOxFJmzC"

const state = {
  url: "",
  query: "",
  isFetching: false
}

const actions = {
  downloadGif: query => async (state, actions) => {
    actions.toggleFetching(true)
    actions.setUrl(
      await fetch(
        `//api.giphy.com/v1/gifs/search?q=${query}&api_key=${GIPHY_API_KEY}`
      )
        .then(data => data.json())
        .then(({ data }) => (data[0] ? data[0].images.original.url : ""))
    )
    actions.toggleFetching(false)
  },
  setUrl: url => ({ url }),
  setQuery: query => ({ query }),
  toggleFetching: isFetching => ({ isFetching })
}

const view = (state, actions) => (
  <div> <input type="text"
      placeholder="Type here..."
      autofocus
      onkeyup={({ target: { value } }) =/> {
        if (value !== state.query) {
          actions.setQuery(value)
          if (!state.isFetching) {
            actions.downloadGif(value)
          }
        }
      }} /> <div class="container">  <img src={state.url}
        style={{
          display: state.isFetching || state.url === "" ? "none" : "block"
        }}
      />  </div>  </div>
)

app(state, actions, view, document.body) 
```

该状态存储 Gif URL 的字符串、搜索查询和一个布尔标志，以便知道浏览器何时获取新的 Gif。

```
const state = {
  url: "",
  query: "",
  isFetching: false
} 
```

`isFetching`标志用于在浏览器繁忙时隐藏 Gif。如果没有它，最后下载的 Gif 将会显示为另一个请求。

```
<img src={state.url}
  style={{
    display: state.isFetching || state.url === "" ? "none" : "block"
  }}
/> 
```

该视图由一个文本输入和一个显示 Gif 的`img`元素组成。

为了处理用户输入，使用了`onkeyup`事件，但是`onkeydown`或者`oninput`也可以。

在每次击键时，调用`actions.downloadGif`并请求一个新的 Gif，但前提是提取尚未挂起并且文本输入不为空。

```
if (value !== state.query) {
  actions.setQuery(value)
  if (!state.isFetching) {
    actions.downloadGif(value)
  }
} 
```

在`actions.downloadGif`中，我们使用[获取](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) API 向 Giphy 请求 Gif URL。

当`fetch`完成时，我们收到有效载荷，其中包含 Gif 信息和[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)。

```
actions.toggleFetching(true)
actions.setUrl(
  await fetch(
    `//api.giphy.com/v1/gifs/search?q=${query}&api_key=${GIPHY_API_KEY}`
  )
    .then(data => data.json())
    .then(({ data }) => (data[0] ? data[0].images.original.url : ""))
)
actions.toggleFetching(false) 
```

一旦接收到数据，就调用`actions.toggleFetching`(这允许进行进一步的获取请求)，并通过将获取的 Gif URL 传递给`actions.setUrl`来更新状态。

### TweetBox 克隆

在这个例子中，我将向您展示如何创建定制组件来将您的 UI 组织成可重用的标记，并构建一个简单的 TweetBox 克隆。

[实例](https://codepen.io/hyperapp/pen/bgWBdV)

```
import { h, app } from "hyperapp"
// @jsx h

const MAX_LENGTH = 140
const OFFSET = 10

const OverflowWidget = ({ text, offset, count }) => (
  <div class="overflow">  <h1>Whoops! Too long.</h1>  <p> ...{text.slice(0, offset)}  <span class="overflow-text">{text.slice(count)}</span>  </p>  </div>
)

const Tweetbox = ({ count, text, update }) => (
  <div>  <div class="container">  <ul class="flex-outer">  <li>  <textarea placeholder="What's up?" value={text} oninput={update}></textarea>  </li>  <li class="flex-inner">  <span class={count > OFFSET ? "overflow-count" : "overflow-count-alert"}
          >  {count}  </span>  <button onclick={() => alert(text)}
            disabled={count >= MAX_LENGTH || count < 0}
          > Tweet </button>  </li>  </ul>  {count < 0 && (
        <OverflowWidget
          text={text.slice(count - OFFSET)}
          offset={OFFSET}
          count={count}
        />
      )}  </div>  </div>
)

const state = {
  text: "",
  count: MAX_LENGTH
}

const view = (state, actions) => (
  <tweetbox text={state.text}
    count={state.count}
    update={e => actions.update(e.target.value)}
  />
)

const actions = {
  update: text => state => ({
    text,
    count: state.count + state.text.length - text.length
  })
}

app(state, actions, view, document.body) 
```

该状态存储消息的文本和剩余字符数`count`，初始化为`MAX_LENGTH`。

```
const state = {
  text: "",
  count: MAX_LENGTH
} 
```

该视图由我们的 TweetBox 组件组成。我们使用属性/道具将数据传递到小部件中。

```
const view = (state, actions) => (
  </tweetbox><tweetbox text={state.text}
    count={state.count}
    update={e => actions.update(e.target.value)}
  />
) 
```

当用户输入时，我们调用`actions.update()`来更新当前文本并计算剩余的字符。

```
update: text => state => ({
  text,
  count: state.count + state.text.length - text.length
}) 
```

从先前文本的长度中减去当前文本的长度告诉我们剩余字符的数量是如何变化的。因此，剩余字符的新计数是旧计数加上上述差值。

当输入为空时，该操作等于`(MAX_LENGTH - text.length)`。

当`state.count`小于 0 时，我们知道`state.text`一定比`MAX_LENGTH`长，所以我们可以禁用 tweet 按钮并显示 OverflowWidget 组件。

```
<button onclick={() => alert(text)} disabled={count >= MAX_LENGTH || count < 0}> Tweet </button> 
```

当`state.count === MAX_LENGTH`时，tweet 按钮也被禁用，因为这意味着我们没有输入任何字符。

OverflowWidget 标记显示消息中不允许的部分和几个相邻的上下文字符。常量`OFFSET`告诉我们要切掉多少额外的字符`state.text`。

```
<overflowwidget text={text.slice(count - OFFSET)}
  offset={OFFSET}
  count={count}></overflowwidget> 
```

通过将`OFFSET`传递给 OverflowWidget，我们能够进一步分割`text`,并将`overflow-text`类应用到特定的溢出部分。

```
<span class="overflow-text">{text.slice(count)}</span> 
```

## 与 React 的比较

在概念层面上，Hyperapp 和 React 有很多共同之处。两个库都使用虚拟 DOM、生命周期事件和基于键的协调。Hyperapp 看起来和感觉上很像 React 和 Redux，但样板文件更少。

React 普及了视图作为状态函数的概念。Hyperapp 通过内置的、受 Elm 启发的状态管理解决方案将这一想法向前推进了一步。

Hyperapp 拒绝局部组件状态只依赖纯功能组件的想法。这转化为高重用性、廉价的内存和简单的测试。

## 最后的想法

因为 Hyperapp 非常小，所以它在网络上的传输速度和解析速度都比其他任何替代产品都要快。这意味着需要学习的概念更少，bug 更少，框架更稳定。

我从来都不是大框架的粉丝。不是因为它们不好，而是因为我想写自己的 JavaScript，而不是框架希望我使用的 JavaScript。最重要的是我想要可转移的技能。我想增长 JavaScript 的技能，而不是框架的技能。

* * *

要了解更多关于 Hyperapp 的信息，请查看官方文档并在[推特](https://twitter.com/hyperappjs)上关注我们的更新和公告。

## 分享这篇文章