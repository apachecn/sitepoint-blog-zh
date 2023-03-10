# 网络工作者终极指南

> 原文：<https://www.sitepoint.com/developing-faster-javascript-apps-the-ultimate-guide-to-web-workers/>

在本教程中，我们将介绍 web workers，并演示如何使用它们来解决执行速度问题。

**内容:**

1.  [JavaScript 非阻塞 I/O 事件循环](#javascriptnonblockingioeventloop)
2.  [长期运行的 JavaScript 函数](#longrunningjavascriptfunctions)
3.  [网络工作者](#webworkers)
4.  [浏览器工作者演示](#browserworkerdemonstration)
    *   [专职员工与共享员工](#dedicatedvssharedworkers)
    *   [客户端工作人员限制](#clientsideworkerlimitations)
    *   [如何使用客户端 web worker](#howtouseaclientsidewebworker)
    *   [Web worker 错误处理](#webworkererrorhandling)
    *   [客户端 web workers 和 ES 模块](#clientsidewebworkersandesmodules)
    *   [客户端服务人员](#clientsideserviceworkers)
5.  [服务器端 Web Worker 演示](#serversidewebworkerdemonstration)
    *   [如何使用服务器端 web worker](#howtouseaserversidewebworker)
    *   [内联工作者脚本](#inlineworkerscripts)
    *   [服务器端工作人员限制](#serversideworkerlimitations)
    *   [线程间共享数据](#sharingdatabetweenthreads)
6.  [node . js 工人的替代品](#alternativestonodejsworkers)
    *   [Node.js 子进程](#nodejschildprocesses)
    *   [Node.js 聚类](#nodejsclustering)
    *   [流程经理](#processmanagers)
    *   [集装箱经理](#containermanagers)
7.  [结论](#conclusion)

浏览器和服务器上的 JavaScript 程序运行在单个处理线程上。这意味着程序一次只能做一件事。简单地说，您的新 PC 可能有 32 个核心 CPU，但是当您的 JavaScript 应用程序运行时，其中 31 个处于空闲状态。

JavaScript 的单线程避免了复杂的并发情况。如果两个线程试图同时进行不兼容的更改，会发生什么？例如，一个浏览器可能正在更新 DOM，而另一个线程重定向到一个新的 URL 并从内存中清除该文档。 [Node.js](https://www.sitepoint.com/an-introduction-to-node-js/) 、 [Deno](https://www.sitepoint.com/learn-deno/) 和 Bun 从浏览器继承了同一个单线程引擎。

这不是特定于 JavaScript 的限制。大多数语言都是单线程的，但是像 PHP 和 Python 这样的 web 选项通常运行在 web 服务器上，它为每个用户请求在一个新的线程上启动单独的解释器实例。这是资源密集型的，因此 Node.js 应用程序通常定义自己的 web 服务器，它运行在单线程上，异步处理每个传入的请求。

Node.js 方法在处理较高的流量负载时更有效，但是长时间运行的 JavaScript 函数会抵消效率的提高。

在我们演示如何解决 web workers 的执行速度问题之前，我们将首先研究 JavaScript 如何运行，以及为什么长时间运行的函数会有问题。

## JavaScript 非阻塞 I/O 事件循环

您可能认为一次做一件事会导致性能瓶颈，但是 JavaScript 是异步的，这避免了大多数单线程处理问题，因为:

*   没有必要等待用户点击网页上的按钮。

    当点击发生时，浏览器引发事件[调用 JavaScript 函数](https://www.sitepoint.com/callbacks-javascript/)。

*   不需要*等待*对 Ajax 请求的响应。

    当服务器返回数据时，浏览器会引发一个调用 JavaScript 函数的事件。

*   Node.js 应用程序不需要等待数据库查询的结果。

    当数据可用时，运行时调用 JavaScript 函数。

JavaScript 引擎运行一个[事件循环](https://www.youtube.com/watch?v=8aGhZQkoFbQ)。一旦代码的最后一条语句执行完毕，运行时就会返回并检查未完成的计时器、挂起的回调和数据连接，然后根据需要执行回调。

其他操作系统处理线程负责调用输入/输出系统，如 HTTP 请求、文件处理程序和数据库连接。*它们不会阻塞事件循环*。它可以继续执行队列中等待的下一个 JavaScript 函数。

![Simplified JavaScript event loop](img/3e4e76dd86e6326b902f03325a50d179.png)

本质上，JavaScript 引擎只负责运行 JavaScript 代码。操作系统处理可能导致引擎的所有其他 I/O 操作，在发生某些情况时调用 JavaScript 函数。

## 长期运行的 JavaScript 函数

JavaScript 函数通常由事件触发。它们会做一些处理，输出一些数据，并且大多数情况下，会在几毫秒内完成，以便事件循环可以继续。

不幸的是，一些长时间运行的函数会阻塞事件循环。假设您正在开发自己的图像处理功能(比如锐化、模糊、灰度化等等)。异步代码可以从文件中读取(或写入)数百万字节的像素数据——这对 JavaScript 引擎几乎没有影响。然而，处理图像的 JavaScript 代码可能需要几秒钟来计算每个像素。该函数阻塞事件循环——并且在它完成之前，没有其他 JavaScript 代码可以运行。

*   在浏览器中，用户将无法与页面进行交互。他们将无法点击、滚动或键入，并可能会看到一个“无响应脚本”错误和一个停止处理的选项。

*   Node.js 服务器应用程序的情况更糟。当函数执行时，它不能响应其他请求。如果需要 10 秒钟才能完成，那么每个访问该点的*用户将不得不等待长达 10 秒钟——*，即使他们没有在处理图像*。*

你可以通过将计算分成更小的子任务来解决这个问题。下面的代码使用传递的`imageFn`函数处理不超过 1000 个像素(来自一个数组)。然后它以 1 毫秒的延迟调用自己。事件循环阻塞的时间较短，因此 JavaScript 引擎可以处理迭代之间的其他传入事件:

```
// pass callback function, image processing function, and input image
// return an output image to a callback function
function processImage( callback, imageFn = i => {}, imageIn = [] ) {

  const chunkSize = 1000; // pixels to process per iteration

  // process first chunk
  let
    imageOut = [],
    pointer = 0;

  processChunk();

  // process a chunk of data
  function processChunk() {

    const pointerEnd = pointer + chunkSize;

    // pass chunk to image processing function
    imageOut = imageOut.concat(
      imageFn( imageIn.slice( pointer, pointerEnd ) )
    );

    if (pointerEnd < imageIn.length) {

      // process next chunk after a short delay
      pointer = pointerEnd;
      setTimeout(processChunk, 1);

    }
    else if (callback) {

      // complete - return output image to callback function
      callback( null, imageOut );

    }

  }

} 
```

这可以防止脚本无响应，但并不总是实用的。即使 CPU 有能力做更多的工作，单个执行线程仍然完成所有的工作。为了解决这个问题，我们可以使用 web workers。

## 网络工作者

Web workers 允许脚本作为后台线程运行。工作线程运行时有自己的引擎实例和独立于主执行线程的事件循环。它并行执行，不会阻塞主事件循环和其他任务。

要使用工作脚本，请执行以下操作:

1.  主线程发布一条包含所有必要数据的消息。
2.  worker 中的事件处理程序执行并启动计算。
3.  完成后，工作线程向主线程回发一条消息，并返回数据。
4.  主线程中的事件处理程序执行、解析传入的数据，并采取必要的措施。

![worker processing](img/f2316525a165c38f79eda3923b2a86a6.png)

主线程*或任何工作线程*可以产生任意数量的工作线程。多线程可以并行处理不同的数据块，从而比单个后台线程更快地确定结果。也就是说，每个新线程都有启动开销，因此确定最佳平衡可能需要一些实验。

所有浏览器 Node.js 10+、Deno 和 Bun 都支持具有类似语法的 workers，尽管服务器运行时可以提供更高级的选项。

## 浏览器工作人员演示

下面的演示展示了一个以毫秒为单位的数字钟，每秒钟可以更新 60 次。同时，你可以启动一个骰子模拟器，它可以投掷任意数量的骰子任意次。默认情况下，它投掷十个六面骰子一千万次，并记录总数的频率。

[https://codesandbox.io/embed/web-worker-browser-861myz?fontsize=14&hidenavigation=1&theme=dark&view=preview](https://codesandbox.io/embed/web-worker-browser-861myz?fontsize=14&hidenavigation=1&theme=dark&view=preview)

在 CodeSandbox 上查看上面的演示:

*   [编辑视图](https://codesandbox.io/s/web-worker-browser-861myz?file=/index.html)
*   [全屏视图](https://861myz.csb.app/)

点击**开始投掷**看钟；它将在计算运行时暂停。较慢的设备和浏览器可能会抛出“无响应脚本”错误。

现在选中**使用 web worker** 复选框，再次开始投掷。在计算过程中，时钟继续运行。这个过程可能会花费更长的时间，因为 web worker 必须启动、接收数据、运行计算并返回结果。随着计算复杂度或迭代次数的增加，这一点将变得不那么明显。在某些时候，工作线程应该比主线程快。

### 专职员工与共享员工

浏览器提供了两种工作选项:

*   :由另一个脚本启动、使用和终止的单个脚本

*   **[共享工作器](https://caniuse.com/sharedworkers)** :不同窗口、iframes 或工作器中的多个脚本可以访问的单个脚本

与共享工作器通信的每个脚本都传递一个唯一的端口，共享工作器必须使用该端口将数据传回。然而，IE 或大多数移动浏览器不支持共享工作者，这使得它们在典型的 web 项目中不可用。

### 客户端工作人员的限制

一个工作线程独立于主线程和其他工作线程运行；它不能访问其他线程中的数据，除非该数据被显式传递给工作线程。数据的一个*副本*被传递给工人。在内部，JavaScript 使用其[结构化克隆算法](https://developer.mozilla.org/docs/Web/API/Web_Workers_API/Structured_clone_algorithm)将数据序列化为字符串。它可以包括诸如字符串、数字、布尔值、数组和对象等本机类型，但*不包括*函数或 DOM 节点。

浏览器工作人员可以使用 console、Fetch、XMLHttpRequest、WebSocket、IndexDB 等 API。它们不能访问文档对象、DOM 节点、localStorage 和窗口对象的某些部分，因为这可能导致 JavaScript 用单线程解决的并发冲突问题——比如在重定向的同时更改 DOM。

重要提示:工作者最适合用于 CPU 密集型任务。它们对密集的 I/O 工作没有好处，因为这些工作被卸载到浏览器并异步运行。

### 如何使用客户端 web worker

下面的演示将`src/index.js`定义为主脚本，当用户单击 **start** 按钮时，它启动时钟并启动 web worker。它在`src/worker.js`(相对于 HTML 文件)用工人脚本的名字定义了一个[工人对象](https://developer.mozilla.org/docs/Web/API/Worker/Worker):

```
// run on worker thread
const worker = new Worker("./src/worker.js"); 
```

接下来是一个`onmessage`事件处理程序。这在工作人员将数据发送回主脚本时运行——通常是在计算完成时。数据存在于事件对象的`data`属性中，并传递给`endDiceRun()`函数:

```
// receive data from worker
worker.onmessage = function(e) {
  endDiceRun(e.data);
}; 
```

主脚本使用它的`postMessage()`方法启动 worker 来发送数据(一个名为`cfg`的对象):

```
// post data to worker
worker.postMessage(cfg); 
```

`src/worker.js`定义工人代码。它使用 [`importScripts()`](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) 导入`src/dice.js`——一个全局工作器方法，它将一个或多个脚本同步导入到工作器中。文件引用相对于工作人员的位置:

```
importScripts('./dice.js'); 
```

`src/dice.js`定义一个`diceRun()`函数来计算投掷统计量:

```
// record dice throwing statistics
function diceRun(runs = 1, dice = 2, sides = 6) {
  const stat = [];

  while (runs > 0) {
    let sum = 0;

    for (let d = dice; d > 0; d--) {
      sum += Math.floor(Math.random() * sides) + 1;
    }
    stat[sum] = (stat[sum] || 0) + 1;
    runs--;
  }

  return stat;
} 
```

请注意，这是一个 ES 模块*而不是*([见下文](#clientsidewebworkersandesmodules))。

`src/worker.js`然后定义一个单独的`onmessage()`事件处理程序。当主调用脚本(`src/index.js`)将数据发送给 worker 时，就会运行这个脚本。事件对象有一个`.data`属性，它提供对消息数据的访问。在本例中，是具有属性`.throws`、`.dice`和`.sides`的`cfg`对象，它们作为参数传递给`diceRun()`:

```
onmessage = function(e) {

  // start calculation
  const cfg = e.data;
  const stat = diceRun(cfg.throws, cfg.dice, cfg.sides);

  // return to main thread
  postMessage(stat);

}; 
```

一个`postMessage()`函数将结果发送回主脚本。这调用了上面显示的`worker.onmessage`处理程序，它运行`endDiceRun()`。

总之，线程处理是通过在主脚本和工作线程之间发送消息来实现的:

1.  主脚本定义了一个`Worker`对象，并调用`postMessage()`发送数据。
2.  工作者脚本执行一个开始计算的`onmessage`处理程序。
3.  worker 调用`postMessage()`将数据发送回主脚本。
4.  主脚本执行一个`onmessage`处理程序来接收结果。

![worker message calls](img/5e89877b9bff60f45950e0671c9bb819.png)

### Web worker 错误处理

除非您使用的是旧的应用程序，否则现代浏览器中的开发人员工具支持 web worker 调试和控制台日志记录，就像任何标准脚本一样。

主脚本可以随时调用一个 [`.terminate()`](https://developer.mozilla.org/docs/Web/API/Worker/terminate) 方法来结束 worker。如果员工未能在特定时间内做出响应，这可能是必要的。例如，如果在十秒钟内没有收到响应，此代码将终止一个活动的工作线程:

```
// main script
const worker = new Worker('./src/worker.js');

// terminate worker after 10 seconds
const workerTimer = setTimeout(() => worker.terminate(), 10000);

// worker complete
worker.onmessage = function(e) {

  // cancel termination
  clearTimeout(workerTimer);

};

// start worker
worker.postMessage({ somedata: 1 }); 
```

工作脚本可以使用标准的错误处理技术，例如验证传入的数据、[、`catch`、`finally`、](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/try%E2%80%A6catch)[、`throw`、](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/throw)来优雅地处理出现的问题，并在需要时向主脚本报告。

您可以使用以下方法检测主脚本中未处理的工作线程错误:

*   [`onmessageerror`](https://developer.mozilla.org/docs/Web/API/Worker/messageerror_event) :当工作进程收到无法反序列化的数据时触发

*   [`onerror`](https://developer.mozilla.org/docs/Web/API/Worker/error_event) :工作者脚本中出现 JavaScript 错误时触发

返回的事件对象在`.filename`、`.lineno`和`.message`属性中提供错误细节:

```
// detect worker error
worker.onerror = function(err) {
  console.log(`${ err.filename }, line ${ err.lineno }: ${ err.message }`);
} 
```

### 客户端 web workers 和 ES 模块

默认情况下，浏览器 web 工作人员*不能*使用 ES 模块(使用`export`和`import`语法的模块)。

`src/dice.js`文件定义了一个导入到 worker 中的函数:

```
importScripts('./dice.js'); 
```

有点不寻常的是，`src/dice.js`代码也包含在主`src/index.js`脚本中，因此它可以启动与工作进程和非工作进程相同的功能。`src/index.js`作为 ES 模块加载。它不能`import`T4 代码，但是它可以将它作为 HTML `<script>`元素加载，这样它就可以在模块中使用了:

```
const diceScript = document.createElement('script');
diceScript.src = './src/dice.js';
document.head.appendChild(diceScript); 
```

这种情况在大多数应用程序中不太可能发生，除非您需要在主脚本和辅助脚本之间共享代码库。

通过向 worker 构造函数追加一个`{ type: "module" }`参数，可以在 worker 中支持 ES6 模块:

```
const worker = new Worker('./src/worker.js', { type: 'module' }); 
```

然后您可以在`src/dice.js`中`export`执行`diceRun()`功能:

```
export function diceRun(runs = 1, dice = 2, sides = 6) {
  //...
} 
```

然后在`worker.js`模块中使用完全限定或相对 URL 引用来`import`它:

```
import { diceRun } from './dice.js'; 
```

理论上，ES6 模块是一个很好的选择，但不幸的是，它们只在版本 80(2020 年发布)以后的基于 Chromium 的浏览器中受支持。您不能在 Firefox 或 Safari 中使用它们，这使得它们对于这里显示的示例代码来说不切实际。

更好的选择是使用捆绑器，例如 [esbuild](https://esbuild.github.io/) 或 [rollup.js](https://rollupjs.org/) 。这些可以解析 ES 模块引用，并将它们打包到一个单独的 worker(和 main) JavaScript 文件中。这简化了编码，并具有使工人明显更快的好处，因为他们不需要在执行前解析导入。

### 客户端服务人员

[服务工作者](https://developer.mozilla.org/docs/Web/API/Service_Worker_API)是渐进式 web 应用程序使用的特殊 web 工作者，用于提供离线功能、后台数据同步和 Web 通知。他们可以:

*   充当浏览器和网络之间的代理来管理缓存文件
*   即使没有加载浏览器或页面，也在后台运行以更新数据和接收传入的消息

像 web 工作者一样，服务工作者运行在单独的处理线程上，不能使用诸如 DOM 之类的 API。然而，相似之处也仅限于此:

*   主线程可以声明服务工作者的可用性，但是两者之间没有任何直接的通信。主线程不一定知道服务工作器正在运行。

*   服务人员通常不用于 CPU 密集型计算。它们可以通过缓存文件和进行其他网络优化来间接提高性能。

*   特定的域/路径可以使用许多 web workers 来完成不同的任务，但它只能注册一个服务 worker。

*   服务工作者必须在相同的 HTTPS 域和路径上，而 web 工作者可以通过 HTTP 从任何域或路径进行操作。

服务人员超出了本文的范围，但是您可以找到更多信息:

*   [服务人员入门](https://www.sitepoint.com/getting-started-with-service-workers/)
*   [服务人员概述](https://developer.chrome.com/docs/workbox/service-worker-overview/)
*   [MDN 使用服务人员](https://developer.mozilla.org/docs/Web/API/Service_Worker_API/Using_Service_Workers)
*   [渐进式网络应用](https://web.dev/progressive-web-apps/)

## 服务器端 Web Worker 演示

Node.js 是最常用的服务器 JavaScript 运行时，它从版本 10 开始就提供了 workers。

Node.js 不是唯一的服务器运行时:

*   Deno 复制了 [Web Worker API](https://deno.land/manual/runtime/workers) ，所以语法与浏览器代码相同。它还提供了一种兼容模式，如果您想使用那个运行时的[工作线程语法](https://doc.deno.land/https://deno.land/std@0.160.0/node/worker_threads.ts)，它会填充 Node.js APIs。

*   Bun 还在测试阶段，尽管其目的是同时支持浏览器和 Node.js 工作 API。

*   您可能正在使用 JavaScript 无服务器服务，如 AWS Lambda、Azure functions、Google Cloud functions、Cloudflare workers 或 Netlify edge functions 等。这些可能提供类似 web worker 的 API，尽管好处较少，因为每个用户请求启动一个单独的独立实例。

下面的演示展示了一个 Node.js 进程，它每秒将当前时间写入控制台:[在新的浏览器标签](https://codesandbox.io/s/web-worker-node-js-fs43s6?file=/src/index.js)中打开 Node.js 演示。

然后在主线程上启动掷骰子计算。这会暂停正在输出的当前时间:

```
 timer process 12:33:18 PM
  timer process 12:33:19 PM
  timer process 12:33:20 PM
NO THREAD CALCULATION STARTED...
┌─────────┬──────────┐
│ (index) │  Values  │
├─────────┼──────────┤
│    2    │ 2776134  │
│    3    │ 5556674  │
│    4    │ 8335819  │
│    5    │ 11110893 │
│    6    │ 13887045 │
│    7    │ 16669114 │
│    8    │ 13885068 │
│    9    │ 11112704 │
│   10    │ 8332503  │
│   11    │ 5556106  │
│   12    │ 2777940  │
└─────────┴──────────┘
processing time: 2961ms
NO THREAD CALCULATION COMPLETE

timer process 12:33:24 PM 
```

一旦完成，相同的计算将在一个工作线程上启动。在这种情况下，时钟在骰子处理发生时继续运行:

```
WORKER CALCULATION STARTED...
  timer process 12:33:27 PM
  timer process 12:33:28 PM
  timer process 12:33:29 PM
┌─────────┬──────────┐
│ (index) │  Values  │
├─────────┼──────────┤
│    2    │ 2778246  │
│    3    │ 5556129  │
│    4    │ 8335780  │
│    5    │ 11114930 │
│    6    │ 13889458 │
│    7    │ 16659456 │
│    8    │ 13889139 │
│    9    │ 11111219 │
│   10    │ 8331738  │
│   11    │ 5556788  │
│   12    │ 2777117  │
└─────────┴──────────┘
processing time: 2643ms
WORKER CALCULATION COMPLETE

  timer process 12:33:30 PM
  timer process 12:33:31 PM
  timer process 12:33:32 PM 
```

工作进程通常比主线程快一点。

### 如何使用服务器端 web worker

该演示将`src/index.js`定义为主脚本，当服务器接收到一个新的 HTTP 请求时，它将启动一个`timer`进程(如果它还没有运行的话):

```
// timer
timer = setInterval(() => {
  console.log(` timer process ${ intlTime.format(new Date()) }`);
}, 1000); 
```

`runWorker()`函数在`src/worker.js`(相对于项目根)用工人脚本的名字定义了一个[工人对象](https://nodejs.org/dist/latest/docs/api/worker_threads.html#class-worker)。它将一个`workerData`变量作为单个值传递，在本例中，这个值是一个具有三个属性的对象:

```
const worker = new Worker("./src/worker.js", {
  workerData: { throws, dice, sides }
}); 
```

与浏览器 web workers 不同，这将启动脚本。没有必要运行`worker.postMessage()`，尽管您可以使用它来运行 worker 中定义的`parentPort.on("message")`事件处理程序。

`src/worker.js`代码使用`workerData`值调用`diceRun()`，并使用`parentPort.postMessage()`将结果传回主线程:

```
// worker thread
import { workerData, parentPort } from "node:worker_threads";
import { diceRun } from "./dice.js";

// start calculation
const stat = diceRun(workerData.throws, workerData.dice, workerData.sides);

// post message to parent script
parentPort.postMessage(stat); 
```

这在主`src/index.js`脚本中引发了一个`"message"`事件，该脚本接收结果:

```
// result returned
worker.on("message", result => {
  console.table(result);
}); 
```

工作者在发送消息后终止，这引发了一个`"exit"`事件:

```
worker.on("exit", code => {
  //... clean up
}); 
```

您可以根据需要定义其他错误和事件处理程序:

*   [`messageerror`](https://nodejs.org/dist/latest/docs/api/worker_threads.html#event-messageerror_1) :当工作进程收到无法反序列化的数据时触发
*   [`online`](https://nodejs.org/dist/latest/docs/api/worker_threads.html#event-online) :工作线程开始执行时触发
*   [`error`](https://nodejs.org/dist/latest/docs/api/worker_threads.html#event-error) :工作者脚本中发生 JavaScript 错误时触发。

### 内联工作脚本

一个单独的脚本文件可以包含主代码和工作代码。代码可以使用`isMainThread`检查它是否在主线程上运行，然后调用自己作为一个工作者(在 ES 模块中使用`import.meta.url`作为文件引用，或者在 CommonJS 中使用`__filename`):

```
import { Worker, isMainThread, workerData, parentPort } from "node:worker_threads";

if (isMainThread) {

  // main thread
  // create a worker from this script
  const worker = new Worker(import.meta.url, {
    workerData: { throws, dice, sides }
  });

  worker.on("message", msg => {});
  worker.on("exit", code => {});

}
else {

  // worker thread
  const stat = diceRun(workerData.throws, workerData.dice, workerData.sides);
  parentPort.postMessage(stat);

} 
```

就个人而言，我更喜欢将文件分开，因为主线程和工作线程可能需要不同的模块。内联工作器可能是简单的单脚本项目的一个选项。

### 服务器端工作人员限制

服务器工作人员仍然独立运行，并像在浏览器中一样接收有限的数据副本。

Node.js、Deno 和 Bun 中的服务器端工作线程比浏览器工作线程具有更少的 API 限制，因为没有 DOM。当两个或更多的工作人员试图同时将数据写入同一个文件时，您可能会遇到问题，但这在大多数应用程序中不太可能发生。

您将不能传递和共享复杂的对象，如数据库连接，因为大多数对象都有无法克隆的方法和函数。但是，您可以执行下列操作之一:

*   在主线程中异步读取数据库数据，并将结果数据传递给工作线程。

*   在 worker 中创建另一个连接对象。这将有一个启动成本，但如果您的函数需要进一步的数据库查询作为计算的一部分，这可能是实用的。

重要提示:请记住，工作者最适合用于 CPU 密集型任务。它们对密集的 I/O 工作没有好处，因为这些工作被卸载到操作系统上并异步运行。

### 线程间共享数据

上面显示的主线程和工作线程之间的通信会在两端产生克隆的数据。可以使用一个代表固定长度原始二进制数据的 [SharedArrayBuffer](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) 对象在线程之间共享数据。下面的主线程定义了从 0 到 99 的 100 个数字元素，并将其发送给一个工作线程:

```
import { Worker } from "node:worker_threads";

const
  buffer = new SharedArrayBuffer(100 * Int32Array.BYTES_PER_ELEMENT),
  value = new Int32Array(buffer);

value.forEach((v,i) => value[i] = i);

const worker = new Worker("./worker.js");

worker.postMessage({ value }); 
```

工作者可以接收`value`对象:

```
import { parentPort } from 'node:worker_threads';

parentPort.on("message", value => {
  value[0] = 100;
}); 
```

此时，无论是主线程还是工作线程都可以改变`value`数组中的元素，并且它在两端都被改变了。

这种技术可以提高效率，因为不需要在任一线程中序列化数据。也有不利的一面:

*   只能共享整数。
*   仍然需要发送消息来表明数据已经改变。
*   存在两个线程可能同时更改相同的值并失去同步的风险。

也就是说，这一过程可能有利于需要处理大量图像或其他数据的高性能游戏。

## Node.js 工作线程的替代

并不是每个 Node.js 应用程序都需要或者可以使用一个 worker。简单的 web 服务器应用程序可能没有复杂的计算。它继续在单个处理线程上运行，随着活动用户数量的增加，响应速度会变慢。该设备可能具有更高的处理能力，有多个 CPU 内核处于闲置状态。

以下部分描述了通用多线程选项。

### Node.js 子进程

在 workers 之前，Node.js 支持子进程，Deno 和 Bun 也有类似的功能。

本质上，他们可以启动另一个应用程序(不一定是 JavaScript)，传递数据，并接收结果。他们的运作方式与工人类似，但效率一般较低，流程更密集。

当您运行复杂的 JavaScript 函数时——可能在同一个项目中——最好使用 Workers。当您启动另一个应用程序(如 Linux 或 Python 命令)时，子进程是必要的。

### Node.js 集群

[Node.js 集群](https://nodejs.org/dist/latest/docs/api/cluster.html)允许您派生任意数量的相同进程，以便更高效地处理负载。最初的主进程可以自己分叉——对于由 [`os.cpus()`](https://nodejs.org/dist/latest/docs/api/os.html#oscpus) 返回的每个 CPU 可能分叉一次。它还可以在实例失败时处理重启，并在分叉的进程之间代理通信消息。

`cluster`标准库提供的属性和方法包括:

*   `.isPrimary`:返回主进程的`true`(也支持旧的`.isMaster`)

*   `.fork()`:产生一个子工作进程

*   `.isWorker`:返回工作进程的`true`

此示例为设备上可用的每个 CPU/内核启动一个 web 服务器工作进程。一台 4 核机器将产生 4 个 web 服务器实例，因此它可以处理高达 4 倍的处理负载。它还会重新启动任何失败的进程，使应用程序更加健壮:

```
// app.js
import cluster from 'node:cluster';
import process from 'node:process';
import { cpus } from 'node:os';
import http from 'node:http';

const cpus = cpus().length;

if (cluster.isPrimary) {

  console.log(`Started primary process: ${ process.pid }`);

  // fork workers
  for (let i = 0; i < cpus; i++) {
    cluster.fork();
  }

  // worker failure event
  cluster.on('exit', (worker, code, signal) => {
    console.log(`worker ${ worker.process.pid } failed`);
    cluster.fork();
  });

}
else {

  // start HTTP server on worker
  http.createServer((req, res) => {

    res.writeHead(200);
    res.end('Hello!');

  }).listen(8080);

  console.log(`Started worker process: ${ process.pid }`);

} 
```

所有进程共享端口 8080，任何进程都可以处理传入的 HTTP 请求。运行应用程序时的日志显示如下:

```
$ node app.js
Started primary process: 1001
Started worker process:  1002
Started worker process:  1003
Started worker process:  1004
Started worker process:  1005

...etc...

worker 1002 failed
Started worker process:  1006 
```

很少有 Node.js 开发人员尝试集群。上面的例子很简单并且运行良好，但是当您试图处理消息、失败和重启时，代码会变得越来越复杂。

### 流程经理

Node.js 进程管理器可以帮助运行 Node.js 应用程序的多个实例，而不必手动编写集群代码。最广为人知的是 PM2。以下命令为每个 CPU/内核启动应用程序的一个实例，并在它们失败时重新启动任何一个实例:

```
pm2 start ./app.js -i max 
```

应用程序实例在后台启动，因此非常适合在实时服务器上使用。您可以通过输入`pm2 status`来检查哪些进程正在运行(显示了简短的输出):

```
$ pm2 status

┌────┬──────┬───────────┬─────────┬─────────┬──────┬────────┐
│ id │ name │ namespace │ version │ mode    │ pid  │ uptime │
├────┼──────┼───────────┼─────────┼─────────┼──────┼────────┤
│ 1  │ app  │ default   │ 1.0.0   │ cluster │ 1001 │ 4D     │
│ 2  │ app  │ default   │ 1.0.0   │ cluster │ 1002 │ 4D     │
└────┴──────┴───────────┴─────────┴─────────┴──────┴────────┘ 
```

PM2 还可以运行用 Deno、Bun、Python 或任何其他语言编写的非 Node.js 应用程序。

### 集装箱经理

集群和进程管理器将应用程序绑定到特定的设备。如果您的服务器或操作系统依赖失败，无论运行多少实例，您的应用程序都会失败。

容器是一个类似于虚拟机的概念，除了它们不是模拟一个完整的硬件设备，而是模拟一个操作系统。容器是单个应用程序的轻量级包装，包含所有必需的操作系统、库和可执行文件。它提供了 Node.js(或任何其他运行时)和您的应用程序的独立实例。一台设备可以运行许多容器，因此不太需要集群或流程管理。

容器超出了本文的范围，但是众所周知的解决方案包括 [Docker](https://www.sitepoint.com/premium/books/docker-for-web-developers/) 和 [Kubernetes](https://www.sitepoint.com/premium/books/start-kubernetes/) 。他们可以在任意数量的设备上启动和监控任意数量的容器，甚至在不同的位置，同时分发传入的流量。

## 结论

JavaScript 工作者可以通过在并行线程中运行 CPU 密集型计算来提高客户端和服务器端的应用程序性能。服务器端工作人员还可以通过在单独的线程中运行更危险的函数，并在处理时间超过一定限制时终止它们，从而使应用程序更加健壮。

在 JavaScript 中使用 workers 很简单，但是:

*   工作人员无法访问所有 API，如浏览器 DOM。它们最适合用于长时间运行的计算任务。

*   对于密集但异步的 I/O 任务，如 HTTP 请求和数据库查询，不太需要工作人员。

*   启动一个工作线程会产生开销，因此可能需要进行一些实验来确保它们能够提高性能。

*   进程和容器管理等选项可能比服务器端多线程更好。

也就是说，当您遇到性能问题时，workers 是一个有用的工具。

## 分享这篇文章