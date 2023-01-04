# 2017 年需要关注的 3 个 JavaScript 库

> 原文：<https://www.sitepoint.com/3-javascript-libraries-2017/>

*2017 年需要关注的 3 个 JavaScript 库由[奥雷里奥·德·罗萨](https://www.sitepoint.com/author/aderosa)和[维尔丹·索蒂奇](https://www.sitepoint.com/author/vildansoftic)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

![Signpost pointing back to 2015/2016 and ahead into 2017](img/5d6253b7446703ca6e9d381bdc75b8b0.png)

唷，2016 年结束了！对于世界和 JavaScript 领域来说，这都是疯狂的一年。无数令人印象深刻的新库和框架出现了，[你可能不需要 JavaScript](http://youmightnotneedjs.com/) 展示了一些模式，让许多人质疑他们对 JavaScript 的使用(一点点)和[的一条推文诺兰·劳森关于前端的演讲的幻灯片](https://twitter.com/LocalSourceNL/status/783977032736772096)引起了一些骚动和像杰里米·基思和克里斯蒂安·海尔曼这样的名人的回应，所有这些都在诺兰的一篇帖子[中进行了总结。我开始觉得“疯狂”是一种轻描淡写。2016 年是*疯狂的*。](https://nolanlawson.com/2016/10/13/progressive-enhancement-isnt-dead-but-it-smells-funny/)

今年还包括 JavaScript 疲劳。如果您没有注意到，许多开发人员正在经历 JavaScript 生态系统的疲劳，因为建立一个“现代”JavaScript 项目需要大量的工具和配置。在某一点上，如此多的开发人员分享了他们的想法，以至于出现了更多关于“JavaScript 疲劳疲劳”的文章！

为了帮助你我晚上睡觉，我为前端开发整理了一个 3 个有前途的通用库/框架的列表。

## view . js-检视. js

如果你还没有留意 [Vue.js](https://vuejs.org/) 的话，你绝对应该留意一下。Vue.js 是一个微小的 JavaScript 框架。

不，不要跑！

Vue.js 似乎主要关注于视图，并且只给你少数几个工具来管理这些视图的数据。Vue.js 的最小化方法没有成为一个塞满编程设计模式和限制的框架，这是一个很好的改变。

Vue.js 有两种版本:包含模板编译器的独立版本和不包含模板编译器的运行时版本。几乎在所有情况下，您都希望使用 Webpack 或 Browserify 预编译模板，并且只在客户端加载运行时包。更多信息参见[vue . js’安装页面](https://vuejs.org/v2/guide/installation.html)。

为了演示其简单性，下面是一个组件示例，它显示一条消息，并为一个按钮添加交互性以反转消息。

```
<div id="app">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">Reverse Message</button>
</div> 
```

```
import Vue from 'vue'

new Vue({
  el: '#app',
  data: {
    message: 'Hello World!',
  },
  methods: {
    reverseMessage: function () {
      const reversedMessage = this.message
        .split('')
        .reverse()
        .join('');

      this.message = reversedMessage;
    },
  },
}); 
```

你想念其他图书馆里你真正喜欢的功能吗？Vue.js 有很多[插件，还有几个指南可以让](https://github.com/vuejs/awesome-vue#libraries--plugins)[使用和编写一个 Vue.js 插件](https://vuejs.org/v2/guide/plugins.html)。

如果你想快速提高工作效率，你绝对应该尝试这个框架。随着项目的增长，它可以很好地扩展。值得一提的是，这个图书馆是由一个人在慷慨的捐助者和赞助者的帮助下维护的。

不管你选择单机还是运行时风格，Vue.js 默认支持符合 [ES5 的浏览器](http://caniuse.com/#feat=es5)。虽然没有记录，但我确信你可以通过手动添加一个 [ES5 垫片](https://github.com/es-shims/es5-shim)来增加支持。

更多信息，请查看 Vue.js 网站或其 [GitHub 库](https://github.com/vuejs/vue)。如果你有兴趣，一定要看看[尼尔森·雅克关于 Vue.js 的社论](https://www.sitepoint.com/2016-year-in-re-vue/)和[杰克·富兰克林关于 Vue.js 2.0 的介绍](https://www.sitepoint.com/up-and-running-vue-js-2-0/)。

## 苗条的

刚刚在 2016 年 11 月中旬发布的[苗条](https://svelte.technology/)是*真正的*新品。它是一个类似于 Vue.js 的 JavaScript 框架，但是占用空间更小。“传统的”框架需要运行时代码来定义和执行模块、保持状态、更新视图和做框架做的任何事情。苗条融入干净的 JavaScript 代码，就好像你根本不用框架一样。这样做的主要好处是文件大小。

框架实际上是一个工具，它将你的源代码编译成没有依赖的普通 JavaScript。Svelte 有插件，所以你可以使用 Webpack、Browserify、Rollup 或 Gulp 编译源代码。查看[编译器的仓库](https://github.com/sveltejs/svelte/#svelte)中所有可用的工具。

为了比较，我用 Svelte 重新创建了 Vue.js 示例:

```
<p>{{ message }}</p>
<button on:click="reverseMessage()">Reverse Message</button>

<script> export default {
  data () {
    return {
      message: 'Hello World!',
    }
  },
  methods: {
    reverseMessage () {
      const reversedMessage = this.get('message')
          .split('')
          .reverse()
          .join('');

      this.set({
        message: reversedMessage,
      });
    }
  }
}; </script> 
```

用 Vue.js 创建的同一个模块产生了一个 7kb 的包。Svelte 产生一个 2kb 的文件。

> TodoMVC 的轻量级实现压缩后重 3.6kb。相比之下，不带任何 app 代码的 React plus ReactDOM 压缩后重约 45kb。

一项 js-framework-benchmark 测试证明了 Svelte 在性能方面与 Inferno 竞争。如果您关心您的应用程序的内存占用，您绝对应该尝试一下。

如果您正在考虑在生产中使用它，我建议您再等一会儿。这个框架真的是新的，没有宣布未来的计划，除了文档中的待办事项，似乎指的是文档本身和插件。尽管它是超级新的，还没有经过实战检验，但我预计它明年会获得一些关注，并可能影响即将到来的库和/或框架。

在撰写本文时，Svelte 要么没有其插件系统的文档，要么根本没有。TODO 表明 Svelte 将支持插件，并可能有一个 API 来挂钩到框架中。

编译代码的兼容性取决于您的构建工作流堆栈，因此很难说它的默认兼容性是什么。从技术上来说，你应该能够通过包含 ES5 垫片来实现预 ES5 支持。

更多信息，请查看 Svelte 的网站或 T2 的 GitHub 库。

## Conditioner.js

最后也是最重要的，[conditor . js](http://conditionerjs.com/)。使用 Conditioner.js，您可以有条件地加载和调用模块。与其他模块加载器的不同之处在于 Conditioner.js 允许您定义加载和/或显示模块的条件。这使您可以减少加载时间并节省带宽。

Conditioner.js 构建时考虑到了渐进增强，建议您应该已经准备好使用给定 JavaScript 模块增强的功能组件。如何定义这些模块完全取决于您。你甚至可以让它从你喜欢的框架中加载模块。

该库不公开全局变量，并建议使用 AMD 加载程序，如 [RequireJS](http://requirejs.org/) 。它与 Browserify、Webpack、Rollup 和其他 AMD bundlers 兼容，但你会希望创建微小的捆绑包，以便 Conditioner.js 可以只加载页面需要的模块。

要入门，可以通过 npm: `npm install conditioner-js`安装。更多信息可以在[项目的主页](http://conditionerjs.com/#requirements)上找到。

这个演示不同于以前的演示，是为了更好地说明 Conditioner.js 的特性。假设我们希望显示事件的剩余时间。一个模块可能是这样的:

```
import moment from 'moment';

export default class RelativeTime {
  /**
   * Enhance given element to show relative time.
   * @param {HTMLElement} element - The element to enhance.
   */
  constructor(element) {
    this.startTime = moment(element.datetime);

    // Update every second
    setInterval(() => this.update(), 1000);
    this.update();
  }

  /**
   * Update displayed relative time.
   */
  update() {
    element.innerHTML = this.startDate.toNow();
  }
} 
```

初始化该模块非常简单:

```
<time datetime="2017-01-01" data-module="ui/RelativeTime">2017</time> 
```

Conditioner 将在 DOM 中的这个位置加载`ui/RelativeTime`模块。请注意，内容已经存在，并且是可接受的格式，该模块只是增强了这一点。

如果希望模块仅在对用户可见时初始化，可以通过以下条件来实现:

```
<!-- Show RelativeTime only if it is visible to the user -->
<time datetime="2017-01-01" data-module="ui/RelativeTime" data-conditions="element:{visible}">2017</time>
<!-- Keep showing RelativeTime after it was visible to the user -->
<time datetime="2017-01-01" data-module="ui/RelativeTime" data-conditions="element:{was visible}">2017</time> 
```

Conditioner.js 有一个非常广泛的监视器列表，您可以使用它来定义条件。不要烦恼！您只需要包含您需要的代码，防止包含不必要的代码。

您还可以将选项作为 JSON 字符串或可读性稍强的 JSON 变体传递。

```
<!-- JSON variant -->
<div data-module="ui/RelativeTime" data-options='unit:"seconds"'>...</div>
<!-- Or good old JSON -->
<div data-module="ui/RelativeTime" data-options='{"unit":"seconds"}'>...</div> 
```

使用或避免 Conditioner.js 的理由与 Svelte 类似:如果你关心你的应用程序的内存占用，你肯定应该考虑使用这个库。另一方面，图书馆的未来还不明朗，因为还没有宣布未来的计划。Conditioner.js 允许您定义自定义监视器，允许您使其适用于所有复杂的模块加载。

默认情况下，Conditioner.js 与支持 ES5 的浏览器兼容。很像 Vue.js 和 Svelte，使用[特定的 ES5 垫片](https://github.com/rikschennink/conditioner/tree/gh-pages/js/shim)可以实现更好的兼容性。

更多信息，请查看[conditor . js’网站](http://conditionerjs.com/)或其 [GitHub 库](https://github.com/rikschennink/conditioner)。

## 结论

我认为这些框架和库在 2017 年会非常棒。虽然我不喜欢框架，但我相信 Vue.js 和 Svelte 都在朝着正确的方向采取措施来解决当前框架中存在的问题，这可能会导致行业转向构建或定义事物的新方式。任何改善都是我期待的改变。

我的印象是，以基于组件的方式构建应用程序被认为是一种可行的方式。虽然我不认为 Conditioner.js 会带来重大改变，但它确实解决了我认为在基于组件的应用程序中相当常见的问题，使它成为更复杂加载的一个很好的补充。

2017 年你期待哪些图书馆会很棒？请在评论区告诉我们你的想法！

## 分享这篇文章