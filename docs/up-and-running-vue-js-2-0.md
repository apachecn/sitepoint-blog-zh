# 启动并运行 Vue.js 2.0 框架

> 原文：<https://www.sitepoint.com/up-and-running-vue-js-2-0/>

*想从头学起 Vue.js？使用 SitePoint Premium 获得涵盖基础知识、项目、技巧和工具&的 Vue 书籍全集。[现在就加入，每月仅需 14.99 美元](https://www.sitepoint.com/premium/products/Z2lkOi8vbGVhcm5hYmxlL1Byb2R1Y3QvMzE3?utm_source=blog&utm_medium=articles)。*

*本文已经更新，主要是为了更新工具部分。*

流行的 JavaScript 框架 [Vue.js](https://vuejs.org/) 一发布 [v2](https://medium.com/the-vue-point/vue-2-0-is-here-ef1f26acf4b8#.f6sth6njx) ，我就迫不及待地想尝试一下，看看使用起来是什么感觉。作为一个非常熟悉 Angular 和 React 的人，我很期待看到他们和 Vue 之间的相似之处和不同之处。

Vue 2 拥有出色的性能统计，相对较小的有效负载(Vue 的捆绑运行时版本在缩小和压缩后重达 30KB)，以及对 vue-router 和 Vuex 等配套库的更新，Vue 的状态管理库。在一篇文章中涵盖的内容太多了，但是请留意后面的文章，在这些文章中，我们将更仔细地研究与核心框架完美结合的各种库。

## 来自其他图书馆的启示

在我们阅读本教程的过程中，你会看到 Vue 的许多特性明显受到了其他框架的启发。这是好事；很高兴看到新的框架吸收了其他库的一些想法并加以改进。特别是，你会看到 Vue 的模板非常接近 Angular 的，但是它的组件和组件生命周期方法更接近 React 的(和 Angular 的)。

这方面的一个例子是，与 React 和当今 JavaScript 领域的几乎所有框架非常相似，Vue 使用虚拟 DOM 的概念来保持高效的渲染。Vue 使用了一个分支 [snabbdom](https://github.com/snabbdom/snabbdom) ，这是一个比较流行的虚拟 dom 库。Vue 网站包括关于其虚拟 DOM 渲染的[文档](https://vuejs.org/v2/guide/render-function.html#Nodes-Trees-and-the-Virtual-DOM)，但是作为一个用户，你需要知道的是 Vue 非常擅长保持你的渲染速度(事实上，它在许多情况下比 React 表现得更好)，这意味着你可以放心你正在一个坚实的平台上构建。

## 组件，组件，组件

就像现在的其他框架一样，Vue 的核心构件是组件。您的应用程序应该是一系列组件，它们构建在彼此之上，以生成最终的应用程序。Vue.js 更进一步，建议(尽管不是强制)在一个单独的`.vue`文件中定义你的组件，然后可以被构建工具解析(我们很快会谈到这些)。鉴于本文的目的是全面探索 Vue 以及使用它的感觉，我将在我的应用程序中使用这个约定。

Vue 文件看起来像这样:

```
<template>
  <p>This is my HTML for my component</p>
</template>

<script> export default {
    // all code for my component goes here
  } </script>

<style scoped> /* CSS here
   * by including `scoped`, we ensure that all CSS
   * is scoped to this component!
   */ </style> 
```

或者，如果您不喜欢将组件的所有部分放在一个文件中，您可以给每个元素一个`src`属性，并分别指向一个单独的 HTML、JS 或 CSS 文件。

## 设置项目

虽然优秀的[Vue CLI](https://cli.vuejs.org/)的存在使建立一个完整的项目变得容易，但当开始一个新的库时，我喜欢从头开始，这样我可以对工具有更多的了解。

这些天来， [webpack](https://webpack.js.org/) 是我首选的构建工具，我们可以将其与 [vue-loader 插件](https://github.com/vuejs/vue-loader)相结合，以支持我之前提到的 Vue.js 组件格式。我们还需要 Babel 和`env`预置，这样我们可以使用现代 JavaScript 语法编写所有代码，以及 [webpack-dev-server](https://github.com/webpack/webpack-dev-server) ，它将在检测到文件更改时更新浏览器。

让我们初始化一个项目并安装依赖项:

```
mkdir vue2-demo-project
cd vue2-demo-project
npm init -y
npm i vue
npm i webpack webpack-cli @babel/core @babel/preset-env babel-loader vue-loader vue-template-compiler webpack-dev-server html-webpack-plugin --save-dev 
```

然后创建初始文件夹和文件:

```
mkdir src
touch webpack.config.js src/index.html src/index.js 
```

项目结构应该如下所示:

```
.
├── package.json
├── package-lock.json
├── src
│   ├── index.html
│   └── index.js
└── webpack.config.js 
```

现在让我们来设置 webpack 配置。这可以归结为以下几点:

*   告诉 webpack 对任何`.vue`文件使用`vue-loader`
*   告诉 webpack 对任何`.js`文件使用 Babel 和`env`预设
*   告诉 webpack 使用`src/index.html`作为模板，为开发服务器生成一个 HTML 文件:

```
//webpack.config.js
const VueLoaderPlugin = require('vue-loader/lib/plugin')
const HtmlWebPackPlugin = require("html-webpack-plugin")

module.exports = {
  module: {
    rules: [
      {
        test: /\.vue$/,
        loader: 'vue-loader',
      },
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env']
          }
        }
      }
    ]
  },
  plugins: [
    new VueLoaderPlugin(),
    new HtmlWebPackPlugin({
      template: "./src/index.html"
    })
  ]
} 
```

最后，我们将在 HTML 文件中添加一些内容，这样就可以开始了！

```
<!-- src/index.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>My Vue App</title>
  </head>
  <body>
    <div id="app"></div>
  </body>
</html> 
```

我们创建一个 ID 为`app`的空的`div`，因为这是我们将要放置 Vue 应用程序的元素。我总是更喜欢使用`div`，而不仅仅是`body`元素，因为这让我可以控制页面的其余部分。

## 编写我们的第一个 Vue.js 应用

我们将忠实于每一个编程教程，编写一个 Vue 应用程序，把“你好，世界！”在我们深入到更复杂的东西之前。

每个 Vue 应用都是通过导入库，然后实例化一个新的`Vue`实例来创建的:

```
import Vue from 'vue'

const vm = new Vue({
  el: '#app',
}) 
```

我们给 Vue 一个元素来渲染到页面上，这样，我们就创建了一个 Vue 应用程序！我们为希望 Vue 用我们的应用程序替换的元素传递一个选择器。这意味着当 Vue 运行时，它将使用我们创建的`div#app`并用我们的应用程序替换它。

我们使用变量名`vm`的原因是因为它代表“视图模型”。尽管与[“模型-视图-模型”(MVVM)模式](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93viewmodel)没有严格的联系，但 Vue 在一定程度上受到了它的启发，而且 Vue 应用程序使用变量名`vm`的惯例一直沿用至今。当然，您可以随意命名这个变量！

到目前为止，我们的应用程序还没有做任何事情，所以让我们创建我们的第一个组件，`App.vue`，它实际上会在页面上呈现一些东西。

Vue 并没有规定你的应用程序是如何构建的，所以这取决于你。我最终为每个组件创建了一个文件夹，在本例中是`App`(我喜欢大写字母，表示一个组件)，其中有三个文件:

*   `index.vue`
*   `script.js`
*   `style.css`

```
mkdir src/App
touch src/App/{index.vue,script.js,style.css} 
```

文件结构现在应该是:

```
.
├── package.json
├── package-lock.json
├── src
│   ├── App
│   │   ├── index.vue
│   │   ├── srcipt.js
│   │   └── style.css
│   ├── index.html
│   └── index.js
└── webpack.config.js 
```

`App/index.vue`定义模板，然后导入其他文件。这与[中推荐的结构一致。关注点分离呢？](https://vuejs.org/v2/guide/single-file-components.html#What-About-Separation-of-Concerns)Vue 的文档部分。

```
<!-- src/App/index.vue -->
<template>
  <p>Hello, World!</p>
</template>
<script src="./script.js"></script>
<style scoped src="./style.css"></style> 
```

我喜欢叫它`index.vue`，但是你可能也想叫它`app.vue`，这样更容易搜索。我更喜欢在我的代码中导入`App/index.vue`而不是`App/app.vue`，但是你可能会不同意，所以请随意选择你和你的团队最喜欢的。

目前，我们的模板只是`<p>Hello, World!</p>`，我将 CSS 文件留空。主要工作进入`script.js`，看起来是这样的:

```
export default {
  name: 'App',
  data() {
    return {}
  },
} 
```

这样做就创建了一个组件，我们将其命名为`App`，主要是为了调试的目的，我稍后会谈到，然后定义这个组件拥有并负责的数据。目前，我们没有任何数据，所以我们可以通过返回一个空对象来告诉 Vue。稍后，我们将看到一个使用数据的组件示例。

现在我们可以返回到`src/index.js`并告诉 Vue 实例呈现我们的`App`组件:

```
import Vue from 'vue'

import AppComponent from './App/index.vue'

const vm = new Vue({
  el: '#app',
  components: {
    app: AppComponent,
  },
  render: h => h('app'),
}) 
```

首先，我们导入组件，信任 webpack 和 vue-loader 来负责解析它。然后我们声明组件。这是重要的一步:默认情况下，Vue 组件不是全球可用的。每个组件都必须有一个它们将要使用的所有组件的列表，以及它将被映射到的标签。在这种情况下，因为我们像这样注册我们的组件:

```
components: {
  app: AppComponent,
} 
```

这意味着在我们的模板中，我们将能够使用`app`元素来引用我们的组件。

最后，我们定义`render`函数。这个函数是用一个助手调用的，这个助手通常被称为`h`，它能够创建元素。这与 React 使用的`React.createElement`函数没有太大的不同。在这种情况下，我们给它一个字符串`'app'`，因为我们想要渲染的组件被注册为具有标签`app`。

通常情况下(以及本教程的其余部分),我们不会在其他组件上使用`render`函数，因为我们将定义 HTML 模板。但是如果你想了解更多信息，渲染函数的 [Vue.js 指南值得一读。](https://vuejs.org/guide/render-function)

完成后，最后一步是在`package.json`中创建一个 npm 脚本:

```
"scripts": {
  "start": "webpack-dev-server --mode development --open"
}, 
```

现在，运行`npm run start`。你的默认浏览器应该在[打开 http://localhost:8080/](http://localhost:8080/) ，你应该看到“你好，世界！”在屏幕上。

尝试编辑`src/index.vue`将消息更改为其他内容。如果一切正常，webpack-dev-server 应该刷新页面以反映您的更改。

耶！我们启动并运行了 Vue.js。

## Devtools 视图

在我们开始使用 Vue 开发一个稍微复杂一点的应用程序之前，现在是一个很好的时机来说明你肯定应该安装 Vue devtools。这些位于 Chrome 开发者工具中，给你一个很好的方式来浏览你的应用程序和所有正在传递的属性，陈述每个组件的状态，等等。

![Screenshot of the Vue devtools](img/7588681b8bc21664c3d664c05ee363dd.png)

## 构建应用程序

作为一个示例应用程序，我们将使用 GitHub API 来构建一个应用程序，该应用程序允许我们输入用户名并查看有关该用户的一些 GitHub 统计信息。我在这里选择了 GitHub API，因为它为大多数人所熟悉，无需认证即可使用，并为我们提供了大量信息。

在启动一个应用程序之前，我喜欢快速思考一下我们需要哪些组件，我认为我们的`App`组件将呈现另外两个组件:`GithubInput`，用于接受用户的输入，以及`GithubOutput`，它将在屏幕上显示用户的信息。我们将从输入开始。

*注:你可以在 GitHub 上找到所有的[代码，甚至可以查看在线运行的](https://github.com/sitepoint-editors/vue2-demo-project)[应用](http://sitepoint-vue2-demo.surge.sh/)。*

### 初始设置

在`src`目录下为`GithubOutput`和`GithubInput`组件创建文件夹:

```
mkdir src/{GithubInput,GithubOutput} 
```

向每个文件添加必要的文件:

```
touch src/GithubInput/{index.vue,script.js,style.css}
touch src/GithubOutput/{index.vue,script.js,style.css} 
```

`src`文件夹的结构现在应该是这样的:

```
.
├── App
│   ├── index.vue
│   ├── script.js
│   └── style.css
├── GithubInput
│   ├── index.vue
│   ├── script.js
│   └── style.css
├── GithubOutput
│   ├── index.vue
│   ├── script.js
│   └── style.css
├── index.html
└── index.js 
```

## Vue.js 中的表单

让我们从`GithubInput`组件开始。与`App`组件一样，`index.vue`文件应该包含模板，以及加载脚本和 CSS 文件。模板现在只包含`<p>github input</p>`。我们很快会正确填写。我喜欢放一些虚拟的 HTML，这样我就可以在创建新组件时检查我是否正确连接了模板:

```
<!-- src/GithubInput/index.vue -->
<template>
  <p>github input</p>
</template>

<script src="./script.js"></script>
<style scoped src="./style.css"></style> 
```

创建这个组件时，我们做的一件不同的事情是创建一个与组件相关联的数据。这与 React 的状态概念非常相似:

```
// src/GithubInput/script.js
export default {
  name: 'GithubInput',
  data() {
    return {
      username: '',
    }
  }
} 
```

这表示该组件拥有并负责一段数据`username`。我们很快会根据用户的输入进行更新。

最后，为了让这个组件出现在屏幕上，我需要向`App`组件注册它，因为是`App`组件将呈现它。

为此，我更新了`src/App/script.js`并告诉它关于`GithubInput`的信息:

```
// src/App/script.js
import GithubInput from '../GithubInput/index.vue'

export default {
  name: 'App',
  components: {
    'github-input': GithubInput,
  },
  data() {
    return {}
  },
} 
```

然后我可以更新`App`组件的模板:

```
<!-- src/App/index.vue -->
<div>
  <p>Hello World</p>
  <github-input></github-input>
</div> 
```

Vue 组件的一个限制(在 Angular 和 React 中也是如此)是每个组件必须有一个根节点，所以当一个组件必须呈现多个元素时，一定要记住将它们都包装在某个东西中，通常是一个`div`。

## 跟踪表单输入

我们的`GithubInput`组件需要做两件事:

*   跟踪输入的当前值
*   告知值已经更改，以便其他组件可以知道并更新它们的状态。

我们可以通过创建一个包含`input`元素的`form`来实现第一个版本。我们可以使用 Vue 的内置指令来跟踪表单值。`GithubInput`的模板看起来是这样的:

```
<form v-on:submit.prevent="onSubmit">
  <input type="text" v-model="username" placeholder="Enter a github username here" />
  <button type="submit">Go!</button>
</form> 
```

您会注意到两个重要的属性:`v-on`和`v-model`。

`v-on`就是我们在 Vue 中绑定 DOM 事件，调用函数的方式。例如，`<p v-on:click="foo">Click me!</p>`会在每次段落被点击时调用组件的`foo`方法。如果你想更详细地了解事件处理，我强烈推荐关于事件处理的 [Vue 文档。](https://vuejs.org/v2/guide/events.html)

`v-model`在表单输入和一段数据之间创建双向数据绑定。在后台，`v-model`有效地监听表单输入上的更改事件，并更新 Vue 组件中的数据以进行匹配。

考虑到我们上面的模板，下面是我们如何使用`v-on`和`v-model`来处理表单中的数据:

*   `v-on:submit.prevent="onSubmit"`绑定提交表单时要运行的方法`onSubmit`。通过添加`.prevent`，这意味着 Vue 将自动阻止默认动作的发生。(如果 Vue 不这样做，我们可以在代码中调用`event.preventDefault()`，但我们不妨利用 Vue 的特性。)
*   在我们的代码中，`v-model:username`将输入的值绑定到值`username`。对于那些熟悉 Angular 的人来说，你可能会认为这与`ng-model`非常相似。当我们创建`GithubInput`时，我们声明它有一段数据`username`，这里我们将这段数据绑定到输入字段。两者将自动保持同步。

现在，回到我们组件的 JavaScript 中，我们可以声明`onSubmit`方法。请注意，这里的名称完全是随意的，您可以选择自己喜欢的名称，但是我喜欢坚持按照触发事件命名函数的惯例:

```
export default {
  name: 'GithubInput',
  data() {
    return { username: '', }
  },
  methods: {
    onSubmit(event) {
      if (this.username && this.username !== '') {
      }
    }
  }
} 
```

我们可以直接在`this`上引用数据，所以`this.username`会给我们文本框的最新值。如果它不为空，我们希望让其他组件知道数据已经更改。为此，我们将使用消息总线。组件可以在这些对象上发出事件，并使用它们来侦听其他事件。当您的应用程序变得更大时，您可能想要寻找一种更结构化的方法，例如 [Vuex](https://github.com/vuejs/vuex) 。目前，消息总线完成了这项工作。

好消息是我们可以使用一个空的 Vue 实例作为消息总线。为此，我们将创建`src/bus.js`，它只是创建一个 Vue 实例并将其导出:

```
import Vue from 'vue'
const bus = new Vue()

export default bus 
```

在`GithubInput`组件中，我们可以导入该模块，并通过在用户名改变时发出一个事件来使用它:

```
import bus from '../bus'

export default {
  ...,
  methods: {
    onSubmit(event) {
      if (this.username && this.username !== '') {
        bus.$emit('new-username', this.username)
      }
    }
  },
  ...
} 
```

这样，我们的表单就完成了，我们准备好开始处理结果数据了。

## 显示来自 GitHub 的结果

`GithubOutput`组件与我们的其他两个组件具有相同的结构。在`GithubOutput/script.js`中，我们还导入了`bus`模块，因为我们需要它来知道用户名何时改变。该组件负责的数据将是一个对象，它将 GitHub 用户名映射到我们从 GitHub API 获得的数据。这意味着我们不必每次都向 API 发出请求；如果我们之前已经获取了数据，我们可以简单地重用它。我们还将存储上次给我们的用户名，这样我们就知道在屏幕上显示什么数据:

```
// src/GithubOutput/script.js
import bus from '../bus'
import Vue from 'vue'

export default {
  name: 'GithubOutput',
  data() {
    return {
      currentUsername: null,
      githubData: {}
    }
  }
} 
```

当创建组件时，我们希望监听消息总线上发出的任何`new-username`事件。令人欣慰的是，Vue 支持许多[生命周期挂钩](https://vuejs.org/api/#Options-Lifecycle-Hooks)，包括`created`。因为我们是负责任的开发人员，所以当组件被使用`destroyed`事件破坏时，让我们也停止监听事件:

```
export default {
  name: 'GithubOutput',
  data: { ... },
  created() {
    bus.$on('new-username', this.onUsernameChange)
  },
  destroyed() {
    bus.$off('new-username', this.onUsernameChange)
  }
} 
```

然后我们定义`onUsernameChange`方法，该方法将被调用并设置`currentUsername`属性:

```
methods: {
  onUsernameChange(name) {
    this.currentUsername = name
  }
}, 
```

注意，我们不必将`onUsernameChange`方法显式绑定到当前范围。当你在 Vue 组件上定义方法时，Vue 会自动调用它们的`myMethod.bind(this)`,所以它们总是绑定到组件上。这就是为什么你需要在`methods`对象上定义你的组件的方法的原因之一，所以 Vue 完全知道它们并且可以相应地设置它们。

### 条件渲染

如果我们没有用户名——当组件第一次被创建时我们没有——我们希望向用户显示一条消息。Vue 有[多种条件渲染技术](https://vuejs.org/guide/conditional.html)，但是最简单的是`v-if`指令，它接受一个条件，并且只渲染存在的元素。也可与`v-else`配对使用:

```
<!-- src/GithubOutput/index.vue-->
<template>
  <div>
    <p v-if="currentUsername == null">
      Enter a username above to see their GitHub data
    </p>
    <p v-else>
      Below are the results for {{ currentUsername }}
    </p>
  </div>
</template>
<script src="./script.js"></script>
<style scoped src="./style.css"></style> 
```

同样，这对于任何 Angular 开发人员来说都非常熟悉。我们在这里使用两倍等于而不是三倍等于，因为我们希望条件不仅在`currentUsername`为`null`时为真，而且在`null == undefined`为`true`时也为真。

### 从 GitHub 获取

Vue.js 没有内置的 HTTP 库，这是有原因的。如今，许多浏览器都自带了`fetch` API(尽管在撰写本文时，还没有 IE11、Safari 或 iOS Safari)。为了这个教程，我不打算使用聚合填充，但是如果你需要的话，你可以很容易地在浏览器中填充 API。如果你不喜欢 fetch API，有很多第三方的 HTTP 库，Vue 文档中提到的是 [Axios](https://github.com/mzabriskie/axios) 。

我非常支持像 Vue 这样不附带 HTTP 库的框架。它降低了框架的捆绑包大小，让开发人员选择最适合他们的库，并根据需要轻松定制请求以与他们的 API 对话。在本文中，我将坚持使用 fetch API，但是也可以用您更喜欢的 API 替换它。

如果你需要 fetch API 的介绍，可以看看 SitePoint 上 Ludovico Fischer 的帖子，这会让你快速上手。

为了发出 HTTP 请求，我们将为组件提供另一个方法`fetchGithubData`，该方法向 GitHub API 发出请求并存储结果。它还将首先检查我们是否已经有该用户的数据，如果有，则不发出请求:

```
methods: {
  ...
  fetchGithubData(name) {
    // if we have data already, don't request again
    if (this.githubData.hasOwnProperty(name)) return

    const url = `https://api.github.com/users/${name}`
    fetch(url)
      .then(r => r.json())
      .then(data => {
        // in here we need to update the githubData object
      })
  }
} 
```

最后，我们只需要在用户名改变时触发这个方法:

```
methods: {
  onUsernameChange(name) {
    this.currentUsername = name
    this.fetchGithubData(name)
  },
  ...
} 
```

还有一件事需要注意，因为 Vue 跟踪你正在处理的数据，所以它知道什么时候更新视图。有一个很棒的[反应指南](https://vuejs.org/guide/reactivity.html)详细解释了这一点，但本质上 Vue 不能神奇地知道你何时从对象中添加或删除了一个属性，所以如果我们这样做了:

```
this.githubData[name] = data 
```

Vue 不会意识到这一点，也不会更新我们的观点。相反，我们可以使用特殊的`Vue.set`方法，它明确地告诉 Vue 我们已经添加了一个键。上面的代码看起来应该是这样的:

```
Vue.set(this.githubData, name, data) 
```

这段代码将修改`this.githubData`，添加我们传递给它的键和值。它还通知 Vue 这一变化，以便它可以重新渲染。

现在我们的代码看起来像这样:

```
const url = `https://api.github.com/users/${name}`
fetch(url)
  .then(r => r.json())
  .then(data => {
    Vue.set(this.githubData, name, data)
  }) 
```

最后，我们需要用`App`组件注册`GitHubOutput`组件:

```
// src/App/script.js
import GithubInput from '../GithubInput/index.vue'
import GithubOutput from '../GithubOutput/index.vue'

export default {
  name: 'App',
  components: {
    'github-input': GithubInput,
    'github-output': GithubOutput,
  },
  data() {
    return {}
  },
} 
```

并将其包含在模板中:

```
<!-- src/App/index.vue -->
<template>
  <div>
    <github-input></github-input>
    <github-output></github-output>
  </div>
</template> 
```

虽然我们还没有编写视图代码来在屏幕上显示获取的数据，但是您应该能够用您的用户名填写表单，然后检查 Vue devtools 来查看从 GitHub 请求的数据。这表明这些 devtools 是多么有用和强大；您可以检查任何组件的本地状态，并了解到底发生了什么。

### 在视图中显示一些统计数据

我们现在可以更新模板来显示一些数据。让我们将这段代码包装在另一个`v-if`指令中，这样我们只在请求完成时才呈现数据:

```
<!-- src/GithubOutput/index.vue -->
<p v-if="currentUsername == null">
  Enter a username above to see their GitHub data
</p>
<p v-else>
  Below are the results for {{ currentUsername }}
  <div v-if="githubData[currentUsername]">
    <h4>{{ githubData[currentUsername].name }}</h4>
    <p>{{ githubData[currentUsername].company }}</p>
    <p>Number of repos: {{ githubData[currentUsername].public_repos }}</p>
  </div>
</p> 
```

这样，我们现在可以将 GitHub 的细节呈现在屏幕上，我们的应用程序就完成了！

## 反应器

我们肯定可以做出一些改进。上面这段呈现 GitHub 数据的 HTML 只需要其中的一小部分——当前用户的数据。这是另一个组件的完美案例，我们可以将用户的数据提供给它，它可以呈现它。

让我们创建一个`GithubUserData`组件，遵循与其他组件相同的结构:

```
mkdir src/GithubUserData
touch src/GithubUserData/{index.vue,script.js,style.css} 
```

这个组件只有一个微小的不同:它将接受一个属性`data`，这将是用户的数据。属性(或“道具”)是组件将由其父组件传递的数据，它们在 Vue 中的行为很像在 React 中的行为。在 Vue 中，你必须显式声明组件需要的每个属性，所以这里我说我们的组件将使用一个道具，`data`:

```
// src/GithubUserData/script.js
export default {
  name: 'GithubUserData',
  props: ['data'],
  data() {
    return {}
  }
} 
```

我非常喜欢 Vue 的一点是你必须非常明确；组件将使用的所有属性、数据和组件都是显式声明的。这使得代码更好用，我想，随着项目变得更大更复杂，也更容易。

在新模板中，我们有和以前完全一样的 HTML，尽管我们可以引用`data`而不是`githubData[currentUsername]`:

```
<!-- src/GithubUserData/index.vue -->
<template>
  <div v-if="data">
    <h4>{{ data.name }}</h4>
    <p>{{ data.company }}</p>
    <p>Number of repos: {{ data.public_repos }}</p>
  </div>
</template>
<script src="./script.js"></script>
<style scoped src="./style.css"></style> 
```

要使用这个组件，我们需要更新`GithubOutput`组件。首先，我们导入并注册`GithubUserData`:

```
// src/GithubOutput/script.js
import bus from '../bus'
import Vue from 'vue'
import GithubUserData from '../GithubUserData/index.vue'

export default {
  name: 'GithubOutput',
  components: {
    'github-user-data': GithubUserData,
  },
  ...
} 
```

在声明组件时，您可以使用任何名称，所以在我放置`github-user-data`的地方，您可以放置任何您想要的东西。建议您坚持使用带有破折号的组件。Vue 并不强制这样做，但是 W3C 规范规定定制元素必须包含破折号，以防止与 HTML 未来版本中添加的元素发生命名冲突。

一旦我们声明了组件，我们就可以在模板中使用它:

```
<!-- src/GithubOutput/index.vue -->
<p v-else>
  Below are the results for {{ currentUsername }}:
  <github-user-data :data="githubData[currentUsername]"></github-user-data>
</p> 
```

这里的关键部分是我如何将`data`属性传递给组件:

```
:data="githubData[currentUsername]" 
```

该属性开头的冒号至关重要；它告诉 Vue 我们传递的属性是动态的，并且组件应该在每次数据改变时更新。Vue 将评估`githubData[currentUsername]`的值，并确保`GithubUserData`组件在数据改变时保持最新。

如果你觉得`:data`有点短和不可思议，你也可以使用更长的`v-bind`语法:

```
v-bind:data="githubData[currentUsername]" 
```

这两个是等价的，所以用你喜欢的。

## 结论

这样，我们的 GitHub 应用程序就处于一个非常好的状态！你可以在 GitHub 上找到所有的[代码，甚至可以查看在线运行的](https://github.com/sitepoint-editors/vue2-demo-project)[应用](http://sitepoint-vue2-demo.surge.sh/)。

当我开始使用 Vue 的时候，我有很高的期望，因为我只听到了好的方面，我很高兴地说它真的符合我的期望。使用 Vue 的感觉就像是把 React 最好的部分和 Angular 最好的部分融合在一起。有些指令(像`v-if`、`v-else`、`v-model`等等)确实很容易上手(也比在 React 的 JSX 语法中做条件句更容易立刻理解)，但是 Vue 的组件系统感觉和 React 的很像。

你被鼓励将你的系统分解成小的组件，总的来说，我发现这是一个非常无缝的体验。我也不能高度赞扬 Vue 团队的文档:这绝对是辉煌的。这些指南非常好，API 参考也很全面，但是很容易找到您想要的东西。

如果你喜欢这篇文章，并想了解更多，那么最好的起点肯定是官方 Vue.js 网站。

## 分享这篇文章