# 如何用 basket.js 提高加载时间

> 原文：<https://www.sitepoint.com/how-to-improve-loading-time-with-basket-js/>

对于现代网页来说，脚本显然是一个至关重要的元素。就重要性和字节数量而言，它们的权重正在增加。例如，想想单页应用程序，以及它们为我们提供所需的所有功能和动画所需的大量代码，而不必点击浏览器上的重新加载按钮。页面中脚本的总大小与视频和音频文件相差甚远，但与图像的大小越来越接近。

在这篇文章中，我将测试两个访问量最大的网站，脸书和 Twitter 的性能，突出他们的一些主要问题。然后，我将重点介绍如何通过向您介绍一个名为 [basket.js](http://addyosmani.github.io/basket.js/) 的库来改善页面脚本的加载时间。

## 衡量脸书和 Twitter 的表现

在介绍 basket.js 是什么以及它解决什么问题之前，我们先来看一个真实世界的例子。让我们使用 Chrome 开发工具或 F12 开发工具来测量传输的字节数和脸书墙的加载时间。当然，为了以可靠的方式执行这项任务，您必须在浏览器中重新加载页面并清空缓存。如果你需要更多的指导，[Addy Osmani 的这条推文将帮助你](https://twitter.com/addyosmani/status/563130061231235072)。

话说回来，平均每个脸书墙加载的脚本总大小是多少？图像/脚本的比例是多少？关于 CSS 和 JavaScript 的挑战，哪一个会更重？猜一猜，写下你的答案，但是…不要作弊！

我们来看看结果:
![Facebook scripts](img/a7d20bfd5275488a14ce88ed53cf18e4.png)

![Facebook css](img/06c2abb247ab834cb41e2cb97e367f81.png)

![Facebook images](img/07bb1fc974210d47a1641c8d8c386485.png)

你可以在这里找到它们的摘要:

*   **脚本**:总大小:785 KB，请求数:49，总加载时间:~9s
*   CSS :总大小:125 KB，请求数:18，总加载时间:~2.2s
*   **图片**:总大小:251 KB，请求数:68，总加载时间:~8s

请记住，文件是并行加载的(达到给定的限制)，因此页面的加载时间小于下载单个文件的时间总和。

让我们通过查看 Twitter 时间轴来看另一个例子:

*   **脚本**:总大小:417 KB，请求数:4，总加载时间:~650ms
*   CSS :总大小:114 KB，请求数:3，总加载时间:~200ms
*   **图片**:总大小:647 KB，请求数:36，总加载时间:~3.5s

尽管 Twitter 的缩小方法看起来有所不同，但脚本的大小仍然接近所有加载图像大小的总和。

此时，你可能会想:“你在说什么？它只是不到 1 MB，我们甚至不应该担心这个！”。毫无疑问，在宽带连接(甚至 4G 连接)上，加载脚本的延迟可以(几乎)忽略不计。然而，情况并不是在所有的国家都一样。其中许多城市在市区以外没有宽带连接。例如，在意大利的乡下，你可能会发现自己被一个 56K 的调制解调器卡住了，上一代移动连接只是在最近才成为现实。虽然意大利并没有覆盖很大一部分市场(“只有”大约 6000 万潜在用户)，但一些较大的国家也受到同样问题的影响。[根据 Akamai 的“互联网状况”报告](https://www.akamai.com/us/en/our-thinking/state-of-the-internet-report/index.jsp)，在印度，绝大多数人都无法获得快速连接。此外，根据同一份报告，巴西是平均连接速度最低的国家之一。

基于这个讨论，你可以理解缓存脚本是一个好主意。

[basket.js](http://addyosmani.github.io/basket.js/) 为静态和动态加载的脚本解决了这个问题，将它们存储到[浏览器的本地存储中](https://www.sitepoint.com/an-overview-of-the-web-storage-api/)。它还允许对缓存及其过期时间进行细粒度控制。

你可能会反对浏览器缓存已经解决了这个问题，你是对的。然而，[本地存储速度更快，这在移动设备上尤为重要](https://www.sitepoint.com/html5-local-storage-revisited/)。我们将在接下来的章节中深化这个主题，但是 Google 和微软正在使用这项技术的事实可能已经给了你一个阅读这篇文章的好理由。

## Basket.js 是什么

正如其网站所说， <q>basket.js 是一个小型 JavaScript 库，支持脚本的本地存储缓存。</q>这句话很好地概括了该项目的目标。一旦库被加载到内存中，它就会异步发送请求来检索页面所需的其他脚本。它将它们注入到文档中，然后缓存到浏览器的本地存储中。这样，下次加载页面时，脚本将在本地加载，而不执行任何 HTTP 请求。

回想一下上面的脸书例子，这意味着您将节省 49 个 HTTP 请求，将近 800 KB，总加载时间大约为 9 秒(在宽带连接上！你可以合理地预期这在 56K 上要慢得多)。

### 本地存储与浏览器缓存和索引数据库

如前所述，谷歌和微软的研究一致认为`localStorage`比浏览器缓存快得多。在 SitePoint 上，我们最近在文章 [HTML5 本地存储再探](https://www.sitepoint.com/html5-local-storage-revisited/)中讨论了这个话题，其中 Luis Vieira 也讨论了`localStorage`的一些限制。此外，IndexedDB 的读写速度(令人惊讶地)比`localStorage`慢。

获得精确的测量值是非常具有挑战性的，目前还没有广泛的研究可用——尽管这是项目的重点之一。

## 如何使用 basket.js

使用这个库真的很简单。它提供了四种主要方法:

*   `basket.require()`:需要远程脚本并将它们注入页面(缓存或不缓存)
*   `basket.get()`:检查`localStorage`的脚本
*   `basket.remove()`:删除缓存的脚本
*   `basket.clear()`:删除所有缓存的脚本

### 需要脚本

要需要脚本，我们可以编写如下语句:

```
basket.require({ url: 'jquery.js' });
```

此方法可用于在一次调用中要求一个或多个脚本。它接受可变数量的参数，每个脚本一个对象。您还可以为脚本的 URL 传递字段，并为每个脚本传递一些选项。打电话总是有回报的。一旦脚本被加载，这个承诺就实现了，或者错误地被拒绝。这很方便，原因有几个:

*   使用承诺链来设置加载顺序，处理依赖关系变得很容易
*   可以处理脚本无法加载的情况，因此可以优雅地失败
*   另外，您可以缓存一个文件而不在加载时执行它——如果您真的需要它，您可以在以后用`.get()`检索它

可以传递给脚本的选项允许设置:

*   引用它的别名
*   如果脚本必须在加载后执行
*   脚本将在多少小时后过期或…
*   …如果它必须完全跳过缓存。

### 处理依赖关系

如果您的脚本都没有依赖项，您可以简单地一次要求它们:

```
basket.require(
    { url: 'jquery.js' },
    { url: 'underscore.js' },
    { url: 'backbone.js' }
);
```

否则，`basket.js`'面向承诺的 API 让您的生活变得轻松:

```
basket
    .require({ url: 'jquery.js' })
    .then(function () {
        basket.require({ url: 'jquery-ui.js' });
    });
```

### 细粒度脚本缓存到期管理

如上所述，脚本可以单独保存在缓存之外，或者可以为每个脚本单独设置到期时间。

```
basket.require(
    // Expires in 2 hours
    { url: 'jquery.js', expire: 2 },
    // Expires in 3 days
    { url: 'underscore.js', expire: 72 },
    // It's not cached at all
    { url: 'backbone.js', skipCache: true },
    // If you later change this value the older version from cache will be ignored
    { url: 'd3.js', unique: 'v1.1.0' }
);
```

### 手动清除缓存

您可以从缓存中删除单个项目:

```
basket
    .remove('jquery.js')
    .remove('modernizr');
```

或者，您可以一次只删除过期的项目，而不明确列出它们

```
remove basket.clear(true);
```

最后，还可以清除页面的所有脚本:

```
remove basket.clear();
```

### 手动验证缓存中的项目

您甚至可以提供自己的自定义函数来验证缓存中的项目，并决定何时将它们标记为陈旧。您可以用一个函数覆盖`basket.isValidateItem`,当缓存项有效时返回`true`,当脚本必须再次从源代码加载时返回`false`。

这不会覆盖现有的对`expiry`和`unique`选项的检查，而是在此基础上添加。此外，即使覆盖`isValidateItem`是一个强有力的选择，你也不太可能真的需要它。

## 动手操作:让我们构建一个示例

我用`basket.js`来重构 [TubeHound](http://thound.org) 的脚本加载，取代 [RequireJS](http://requirejs.org/) 作为脚本管理器。

这是主脚本标题之前的样子:

```
requirejs.config({
    "baseUrl”: "js/",
    "paths": {
      "jquery": "./lib/jquery-2.0.3.min",
      "Ractive": "./lib/Ractive",
      "utility": "utility",
      "fly": "./lib/Ractive-transitions-fly",
      "fade": "./lib/Ractive-transitions-fade",
      "bootstrap": "./lib/bootstrap.min",
      "jquery-ui": "./lib/jquery-ui-1.10.4.custom.min",
      "jquery-contextmenu": "./lib/jquery.contextmenu"
    },
    "shim": {
      "jquery": {
        exports: 'jquery'
      },
      "Ractive": {
            exports: 'Ractive'
      },
      "utility": {
            deps: ['jquery'],
            exports: 'utility'
      },
      "bootstrap": {
            deps: ['jquery'],
            exports: 'bootstrap'
      },
      "jquery-ui": {
            deps: ['jquery'],
            exports: 'jquery-ui'
      },
      "jquery-contextmenu": {
            deps: ['jquery'],
            exports: 'jquery-contextmenu'
      }
    }
});

require([
  'jquery',
  'Ractive',
  'utility',
  'bootstrap',
  'fly',
  'jquery-ui',
  'jquery-contextmenu',
  'fade'
], function ($, Ractive, utility) {
  ...
});
```

现在我去掉了除函数声明之外的所有内容，去掉了所有的参数。然后我加了一个新的小脚本叫做`loading.js`:

```
(function () {
  function requireScriptsDependingOnJQueryAndRactive () {
    return basket.require(
      { url: 'js/lib/bootstrap.min.js'},
      { url: 'js/lib/Ractive-transitions-fly.js', key: 'fly' },
      { url: 'js/lib/Ractive-transitions-fade.js', key: 'fade' },
      { url: 'js/lib/jquery-ui-1.10.4.custom.min.js', key: 'jquery-ui' },
      { url: 'js/lib/jquery.contextmenu.js', key: 'jquery-contextmenu' },
      { url: 'js/utility.min.js', key: 'utility', unique: 1 }
    );
  }

basket.require(
    { url: 'js/lib/jquery-2.0.3.min.js', key: 'jquery' },
    { url: 'js/lib/Ractive.js', key: 'Ractive' }
  ).then(requireScriptsDependingOnJQueryAndRactive)
  .then(function () {
    basket.require({ url: 'js/thound.min.js', unique: 1 });  //unique is to make sure we can force a reload, in case of bugs
  });
}());
```

这是通过 HTML 页面中的`<script>`标签加载的(就在`basket.js`之后):

```
<script src="js/lib/basket.min.js"></script>
<script src="js/loader.js"></script>
```

我为`utility.js`执行了类似的重构。以前，RequireJS 需要一些管道:

```
requirejs.config({
    "baseUrl": "js/",
    "paths": {
      "jquery": "./lib/jquery-2.0.3.min"
    },
    "shim": {
      "jquery": {
        exports: 'jquery'
      }
    }
});

define([
  'jquery'
], function ($) {
        "use strict";
        ...
});
```

之后，我使用一个全局变量“导出”模块，如下所示:

```
var utility = (function () {
    "use strict";
    ...
}());
```

### 表演

让我们来看看本质:我得到了多大的改进？这是基线，现有页面的硬重载:

![before basket hard reload](img/16a7427babe095371bc58be8de57f0f4.png)

28 次请求下载 904KB 用时 6.06 秒。接下来，我硬加载了新版本的页面，并再次测量:

![after basket first load](img/4128bda34cb60ff9d85c666cadcbbdd6.png)

因为它是从头开始加载页面，所以所有的脚本都是通过 HTTP 请求加载的。27 次请求下载 899KB 用了 4.01s(require js 被遗漏，被 basket.js 代替)。

此时，当您再次硬加载页面时，所有内容都从浏览器的缓存中刷新，但脚本保留在`localStorage`中:增量将测量缓存脚本提供的实际增益。

![after basket hard reload](img/2368ea7b2f7b67a2a968782e3dc3dca2.png)

结果是:下载 18 个请求所需的 352KB 需要 2.01 秒。所以对于一个 JS 密集型的页面来说，你实际上节省了很多。

最后，让我们看看正常访问主页的最终加载时间:

![after basket normal page load](img/2f7b2cfe9eff0eeb3c5f7ad7f847e787.png)

使用浏览器缓存和`basket.js`可以在 771ms 内加载页面，实际只加载了 5.3KB 个请求，大部分由缓存提供服务)。

## 结论

这个库是一个好主意，有一个缺点是依赖于不太完美的数据 API。选择`localStorage`的考虑是完全可以理解的。它试图提高性能，经验表明`localStorage`是最快的解决方案。

另一方面，正如 Donald Knuth 喜欢说的，“过早优化是万恶之源”！如果没有广泛而严格的性能比较，就很难衡量配额限制带来的限制。不幸的是，`localStorage`的问题不会很快消失，至少对 Chrome 来说不会，在 Chrome 中，增加配额将需要一些[非平凡的重写](https://code.google.com/p/chromium/issues/detail?id=58985)。

好消息是`basket.js`的作者正在考虑几个替代方案，包括一个[分层解决方案](https://github.com/addyosmani/basket.js/issues/131)，它将尝试使用浏览器上可用的最佳持久性 API:服务工人、[缓存 API(在 Chrome 上)](https://chromium.googlesource.com/chromium/blink/+/53d80dc622ba866d563d4934c98777a42d4c3e96)或文件系统 API。

我有点惊讶地看到，服务人员最初没有被考虑，但显然这将很快改变。更好的消息是，有许多新兴的图书馆正从不同的角度研究类似的想法。例如，Shed 看起来很有前途:一个更广泛的解决方案，让服务人员超级容易使用。

当我试图在一个真实的项目中使用它时，我可能会直接接触到一些问题(并被其灼伤):

1.  反馈可以在很大程度上得到改善:当它无法加载您的脚本时，很难判断发生了什么。如果幸运的话，您可能会看到一些错误，但是这些消息毫无意义。例如，我将一个实际的数组传递给`require()`方法:我得到的只是来自 lib 代码的一个泛型`TypeError`,所以我经历了很多尝试和错误才意识到我的错误。
2.  现在，如果你运气不好:一个脚本可能根本不会被加载，因为在承诺链的回调中有一个错别字(f.i. `basker.require`)。因为你的错误信息被咽下去了，你需要一段时间才能意识到。
3.  如果您的脚本存在依赖关系，那么您将失去使用 RequireJS 时所拥有的清晰的声明性语法，在 require js 中您可以列出每个脚本的依赖关系。
4.  另一方面，您在一个地方列出并排序了所有的依赖项。而且，让我们面对现实吧，RequireJS 有点啰嗦和多余。
5.  一旦它们被缓存，异步加载的脚本就不会出现在 Chrome 开发工具的*网络*面板中(也不会出现在 Firefox 的面板中)。此外，即使它们是从远程源代码加载的，您也不会在源代码中看到它们。这使得调试有点困难，但是如果你只在生产中需要优化的时候使用`basket.js`,可以在开发过程中解决。

说实话，这个项目还没有达到第 1 版，它明确表示，这只是一个实验，现在。的确，`basket.js`是一个非常有前途的想法，结果看起来非常好——但我认为它需要一点额外的步骤来准备用于一个复杂项目的开发——或者用于一个巨大页面的生产。(由于接口/结构的可能变化，这对于任何没有达到版本 1 的项目都是如此)。

相反，对于中小型项目，这可能是减少用户加载时间和挫折感的好方法。我会密切关注它，一旦项目成熟，我会很乐意支持它的采用。

## 分享这篇文章