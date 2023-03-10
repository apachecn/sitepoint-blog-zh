# 如何用性能 API 让你的站点更快

> 原文：<https://www.sitepoint.com/performance-api-faster-websites-apps/>

本教程解释了如何使用 Performance API 记录真实用户访问应用程序时的类似 DevTool 的统计数据。

使用浏览器 DevTools 评估 web 应用程序的性能是有用的，但是要复制真实世界的使用情况并不容易。不同地点的人使用不同的设备、浏览器和网络都会有不同的体验。

## 性能 API 简介

[Performance API](https://developer.mozilla.org/docs/Web/API/Performance_API) 使用一个缓冲区来记录 web 页面生命周期中某些点的对象属性中类似 DevTool 的指标。这些要点包括:

1.  [页面导航](#pagenavigationtiming):记录页面加载重定向、连接、握手、DOM 事件等等。
2.  [资源加载](#pageresourcetiming):记录图片、CSS、脚本、Ajax 调用等资产加载。
3.  [画图度量](#browserpainttiming):记录浏览器渲染信息。
4.  [自定义性能](#usertiming):记录任意应用处理时间，查找速度慢的功能。

所有的 API 都可以在客户端 JavaScript 中获得，包括 [Web Workers](https://developer.mozilla.org/docs/Web/API/Web_Workers_API/Using_web_workers) 。您可以使用以下方法检测 API 支持:

```
if ('performance' in window) {

  // call Performance APIs

} 
```

注意:尽管实现了大部分 API，Safari 并不支持所有的方法。

自定义(用户)性能 API 也复制在:

*   Node.js 内置的 [`performance_hook`模块](https://nodejs.org/dist/latest/docs/api/perf_hooks.html)，以及
*   Deno [性能 API](https://doc.deno.land/builtin/stable#Performance) (使用它的脚本必须使用`--allow-hrtime`权限运行)。

## `Date()`还不够好吗？

您可能已经看到了使用`Date()`函数记录运行时间的例子。例如:

```
const start = new Date();

// ... run code ...

const elapsed = new Date() - start; 
```

然而，`Date()`计算被限制到最接近的毫秒，并且基于系统时间，该系统时间可以由操作系统随时更新。

Performance API 使用一个单独的、分辨率更高的计时器，可以在几分之一毫秒内进行记录。它还提供了否则不可能记录的指标，例如重定向和 DNS 查找时间。

## 记录绩效指标

如果您可以在某个地方记录，那么在客户端代码中计算性能指标是非常有用的。您可以使用 Ajax[Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API)/[XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest)请求或 [Beacon API](https://developer.mozilla.org/en-US/docs/Web/API/Beacon_API) 将统计数据发送到您的服务器进行分析。

或者，大多数分析系统提供定制的类似事件的 API 来记录时间。例如，Google Analytics [用户计时 API](https://developers.google.com/analytics/devguides/collection/analyticsjs/user-timings) 可以通过传递类别(`'pageload'`)、变量名(`"DOMready"`)和值来记录到`DOMContentLoaded`的时间:

```
const pageload = performance.getEntriesByType( 'navigation' )[0];

ga('send', 'timing', 'pageload', 'DOMready', pageload.domContentLoadedEventStart); 
```

这个例子使用了页面导航计时 API。所以让我们从这里开始…

## 页面导航计时

在快速连接上测试你的网站不太可能代表用户体验。浏览器的 DevTools **Network** 选项卡允许你调节速度，但它不能模拟差的或断断续续的 3G 信号。

导航计时 API 将单个 [`PerformanceNavigationTiming`](https://developer.mozilla.org/docs/Web/API/PerformanceNavigationTiming) 对象推送到性能缓冲区。它包含由真实用户观察到的关于重定向、加载时间、文件大小、DOM 事件等等的信息。

通过运行以下命令来访问对象:

```
const pagePerf = performance.getEntriesByType( 'navigation' ); 
```

或者通过将页面 URL ( `window.location`)传递给 [`getEntriesByName() method`](https://developer.mozilla.org/docs/Web/API/Performance/getEntriesByName) 来访问它:

```
const pagePerf = performance.getEntriesByName( window.location ); 
```

两者都返回包含单个元素的数组，该元素包含一个具有只读属性的对象。例如:

```
[
  {
    name: "https://site.com/",
    initiatorType: "navigation",
    entryType: "navigation",
    initiatorType: "navigation",
    type: "navigate",
    nextHopProtocol: "h2",
    startTime: 0
    ...
  }
] 
```

该对象包括**资源标识属性**:

| 财产 | 描述 |
| --- | --- |
| **名称** | 资源 URL |
| **条目类型** | 性能类型— `"navigation"`用于页面，`"resource"`用于资产 |
| **启动器类型** | 启动页面下载的资源— `"navigation"` |
| **下一跳协议** | 网络协议 |
| **服务器计时** | [PerformanceServerTiming](https://developer.mozilla.org/docs/Web/API/PerformanceServerTiming) 对象的数组 |

*注意:performanceservertimeting`name`、`description`和`duration`指标由服务器响应写入 HTTP [`Server-Timing`头](https://developer.mozilla.org/docs/Web/HTTP/Headers/Server-Timing)。*

该对象包括**资源计时属性**，以相对于页面加载开始的毫秒为单位。计时通常按以下顺序进行:

| 财产 | 描述 |
| --- | --- |
| **开始时间** | 开始获取时的时间戳—页面的`0` |
| **工人开始** | 启动服务工作程序之前的时间戳 |
| **重定向开始** | 第一次重定向的时间戳 |
| **再定向** | 收到最后一次重定向的最后一个字节后的时间戳 |
| **fetchStart** | 获取资源之前的时间戳 |
| **domainLookupStart** | DNS 查找之前的时间戳 |
| **domainLookupEnd** | DNS 查找后的时间戳 |
| **连接开始** | 建立服务器连接之前的时间戳 |
| **连接完毕** | 建立服务器连接后的时间戳 |
| **secureConnectionStart** | SSL 握手之前的时间戳 |
| **请求开始** | 浏览器请求之前的时间戳 |
| **响应开始** | 浏览器接收数据的第一个字节时的时间戳 |
| **响应和** | 收到最后一个字节数据后的时间戳 |
| **持续时间** | 在**开始时间**和**响应和**之间经过的时间 |

该对象包括**下载大小属性**，以字节为单位:

| 财产 | 描述 |
| --- | --- |
| **传输大小** | 资源大小，包括标题和正文 |
| **编码器体尺寸** | 解压缩前的资源体大小 |
| **decodedBodySize** | 解压缩后的资源体大小 |

最后，该对象还包括**导航和 DOM 事件属性**(在 Safari 中不可用):

| 财产 | 描述 |
| --- | --- |
| **类型** | `"navigate"`、`"reload"`、`"back_forward"`或`"prerender"` |
| **重定向计数** | 重定向次数 |
| **unloadEventStart** | 前一个文档的`unload`事件之前的时间戳 |
| **卸载事件结束** | 前一个文档的`unload`事件之后的时间戳 |
| **支配型** | HTML 解析和 DOM 构造完成时的时间戳 |
| **domContentLoadedEventStart** | 运行`DOMContentLoaded`事件处理程序之前的时间戳 |
| **domContentLoadedEventEnd** | 运行`DOMContentLoaded`事件处理程序后的时间戳 |
| **domComplete** | DOM 构造和`DOMContentLoaded`事件完成时的时间戳 |
| **loadEventStart** | 页面`load`事件触发前的时间戳 |
| **loadEventEnd** | 页面`load`事件后的时间戳。所有资产都已下载 |

页面完全加载后记录页面加载度量的示例:

```
'performance' in window && window.addEventListener('load', () => {

  const
    pagePerf        = performance.getEntriesByName( window.location )[0],
    pageDownload    = pagePerf.duration,
    pageDomComplete = pagePerf.domComplete;

}); 
```

## 页面资源计时

每当页面加载图像、字体、CSS 文件、JavaScript 文件或任何其他项目时，资源计时 API 都会将一个 [`PerformanceResourceTiming`](https://developer.mozilla.org/docs/Web/API/PerformanceResourceTiming) 对象推送到性能缓冲区。运行:

```
const resPerf = performance.getEntriesByType( 'resource' ); 
```

这将返回一个资源计时对象数组。这些具有与上面的所示的[页面计时相同的属性，但是没有导航和 DOM 事件信息。](#pagenavigationtiming)

以下是一个示例结果:

```
[
  {
    name: "https://site.com/style.css",
    entryType: "resource",
    initiatorType: "link",
    fetchStart: 150,
    duration: 300
    ...
  },
  {
    name: "https://site.com/script.js",
    entryType: "resource",
    initiatorType: "script",
    fetchStart: 302,
    duration: 112
    ...
  },
  ...
] 
```

可以通过将资源的 URL 传递给 [`.getEntriesByName()`方法](https://developer.mozilla.org/docs/Web/API/Performance/getEntriesByName)来检查单个资源:

```
const resourceTime = performance.getEntriesByName('https://site.com/style.css'); 
```

这将返回一个只有一个元素的数组:

```
[
  {
    name: "https://site.com/style.css",
    entryType: "resource",
    initiatorType: "link",
    fetchStart: 150,
    duration: 300
    ...
  }
] 
```

您可以使用 API 来报告每个 CSS 文件的加载时间和解压缩大小:

```
// array of CSS files, load times, and file sizes
const css = performance.getEntriesByType('resource')
  .filter( r => r.initiatorType === 'link' && r.name.includes('.css'))
  .map( r => ({

      name: r.name,
      load: r.duration + 'ms',
      size: r.decodedBodySize + ' bytes'

  }) ); 
```

`css`数组现在为每个 CSS 文件包含一个对象。例如:

```
[
  {
    name: "https://site.com/main.css",
    load: "155ms",
    size: "14304 bytes"
  },
  {
    name: "https://site.com/grid.css",
    load: "203ms",
    size: "5696 bytes"
  }
] 
```

*注意:负载和大小为零表示资产已经被缓存。*

至少 150 个资源度量对象将被记录到性能缓冲区。您可以用 [`.setResourceTimingBufferSize(N)`方法](https://developer.mozilla.org/docs/Web/API/Performance/setResourceTimingBufferSize)定义一个具体的数字。例如:

```
// record 500 resources
performance.setResourceTimingBufferSize(500); 
```

已有的指标可以用 [`.clearResourceTimings() method`](https://developer.mozilla.org/docs/Web/API/Performance/clearResourceTimings) 清除。

## 浏览器绘画计时

[First Contentful Paint(FCP)](https://web.dev/first-contentful-paint/)测量用户导航到您的页面后呈现内容所需的时间。Chrome 的 DevTool Lighthouse 面板的**性能**部分显示了该指标。谷歌认为少于两秒的 FCP 时间是好的 T4 时间，你的页面会比 75%的网页显示得更快。

绘画计时 API 在以下情况下将两个记录两个 [PerformancePaintTiming](https://developer.mozilla.org/docs/Web/API/PerformancePaintTiming) 对象推送到性能缓冲区:

*   **首次绘制**发生:浏览器绘制第一个像素，并且
*   **first-contentful-paint** 发生:浏览器绘制 DOM 内容的第一项

运行时，两个对象都以数组形式返回:

```
const paintPerf = performance.getEntriesByType( 'paint' ); 
```

示例结果:

```
[
  {
    "name": "first-paint",
    "entryType": "paint",
    "startTime": 125
  },
  {
    "name": "first-contentful-paint",
    "entryType": "paint",
    "startTime": 127
  }
] 
```

**开始时间**与初始页面加载相关。

## 用户计时

Performance API 可用于为您自己的应用程序函数计时。所有用户计时方法在客户端 JavaScript、Web Workers、Deno 和 Node.js 中都可用。

*注意 Node.js 脚本必须加载[性能挂钩(`perf_hooks`)模块](https://nodejs.org/dist/latest/docs/api/perf_hooks.html)。*

*CommonJS `require`语法:*

```
const { performance } = require('perf_hooks'); 
```

*或 ES 模块`import`语法:*

```
import { performance } from 'perf_hooks'; 
```

最简单的选项是 [`performance.now()`](https://developer.mozilla.org/docs/Web/API/Performance/now) ，它从流程生命周期的开始返回一个高分辨率的时间戳。

简单的定时器可以用`performance.now()`。例如:

```
const start = performance.now();

// ... run code ...

const elapsed = performance.now() - start; 
```

*注意:一个非标准的 [`timeOrigin`属性](https://developer.mozilla.org/docs/Web/API/Performance/timeOrigin)返回一个 Unix 时间的时间戳。可以在 Node.js 和浏览器 JavaScript 中使用，不能在 IE 和 Safari 中使用。*

管理多个定时器很快变得不切实际。 [`.mark()`方法](https://developer.mozilla.org/docs/Web/API/Performance/mark)将一个名为[的 PerformanceMark 对象](https://developer.mozilla.org/docs/Web/API/PerformanceMark)对象添加到性能缓冲区。例如:

```
performance.mark('script:start');

performance.mark('p1:start');
// ... run process 1 ...
performance.mark('p1:end');

performance.mark('p2:start');
// ... run process 2 ...
performance.mark('p2:end');

performance.mark('script:end'); 
```

下面的代码返回一个 mark 对象数组:

```
const marks = performance.getEntriesByType( 'mark' ); 
```

具有`entryType`、`name`和`startTime`属性:

```
[
  {
    entryType: "mark",
    name: "script:start",
    startTime: 100
  },
  {
    entryType: "mark",
    name: "p1:start",
    startTime: 200
  },
  {
    entryType: "mark",
    name: "p1:end",
    startTime: 300
  },
  ...
] 
```

可以使用 [`.measure()`方法](https://developer.mozilla.org/docs/Web/API/Performance/measure)计算两个标记之间经过的时间。它传递了一个度量名、开始标记名(或使用零的`null`)和结束标记名(或使用当前时间的`null`):

```
performance.measure('p1', 'p1:start', 'p1:end');
performance.measure('script', null, 'script:end'); 
```

每个调用将一个具有计算持续时间的 [PerformanceMeasure](https://developer.mozilla.org/docs/Web/API/PerformanceMeasure) 对象推送到性能缓冲区。可以通过运行以下命令来访问一组度量值:

```
const measures = performance.getEntriesByType( 'measure' ); 
```

示例:

```
[
  {
    entryType: "measure",
    name: "p1",
    startTime: 200,
    duration: 100
  },
  {

    entryType: "measure",
    name: "script",
    startTime: 0,
    duration: 500
  }
] 
```

可使用 [`.getEntriesByName()`方法](https://developer.mozilla.org/docs/Web/API/Performance/getEntriesByName)通过名称检索标记或测量对象:

```
performance.getEntriesByName( 'p1' ); 
```

其他方法:

*   [`.getEntries()`](https://developer.mozilla.org/docs/Web/API/Performance/getEntries) :返回所有性能条目的数组。
*   [`.clearMarks( [name] )`](https://developer.mozilla.org/docs/Web/API/Performance/clearMarks) :清除已命名的标记(不带名称运行清除所有标记)
*   [`.clearMeasures( [name] )`](https://developer.mozilla.org/docs/Web/API/Performance/clearMeasures) :清除已命名的度量(不带名称运行，清除所有度量)

一个 [PerformanceObserver](https://developer.mozilla.org/docs/Web/API/PerformanceObserver) 可以监视缓冲区的变化，并在特定对象出现时运行一个函数。观察器函数由两个参数定义:

1.  `list`:观察者条目
2.  `observer`(可选)[观察者对象](https://developer.mozilla.org/docs/Web/API/PerformanceObserver)

```
function performanceHandler(list, observer) {

  list.getEntries().forEach(entry => {

    console.log(`name    : ${ entry.name }`);
    console.log(`type    : ${ entry.type }`);
    console.log(`duration: ${ entry.duration }`);

    // other code, e.g.
    // send data via an Ajax request

  });

} 
```

这个函数被传递给一个新的`PerformanceObserver`对象。 [`.observe()`方法](https://developer.mozilla.org/docs/Web/API/PerformanceObserver/observe)然后设置可观察的`entryTypes`(通常为`"mark"`、`"measure"`和/或`"resource"`):

```
let observer = new PerformanceObserver( performanceHandler );
observer.observe( { entryTypes: [ 'mark', 'measure' ] } ); 
```

每当新的标记或测量对象被推送到性能缓冲区时，`performanceHandler()`函数就会运行。

## 自我剖析 API

[自分析 API](https://wicg.github.io/js-self-profiling/) 与性能 API 相关，可以帮助找到低效或不必要的后台函数，而不必手动设置标记和度量。

示例代码:

```
// new profiler, 10ms sample rate
const profile = await performance.profile({ sampleInterval: 10 });

// ... run code ...

// stop profiler, get trace
const trace = await profile.stop(); 
```

该跟踪返回有关在每个采样间隔执行的脚本、函数和行号的数据。对相同代码的重复引用可能表明进一步优化是可能的。

该 API 目前正在开发中(见 [Chrome 状态](https://chromestatus.com/features/5170190448852992))，可能会发生变化。

## 调整应用程序性能

Performance API 提供了一种方法来测量网站和应用程序在实际设备上的速度，这些设备由不同位置的真实用户在一系列连接上使用。它使得为每个人整理类似 DevTool 的度量并识别潜在的瓶颈变得容易。

解决这些性能问题是另一回事，但是[site point Jump Start Web Performance book](https://www.sitepoint.com/premium/books/jump-start-web-performance/)会有所帮助。它提供了一系列快餐、简单的食谱和改变生活的饮食，让你的网站更快、更有反应。

## 分享这篇文章