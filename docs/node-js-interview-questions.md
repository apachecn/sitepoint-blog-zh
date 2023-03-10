# 21 Node.js 面试问题及解答

> 原文：<https://www.sitepoint.com/node-js-interview-questions/>

准备工作面试总是一件令人生畏的任务。最有可能的是，你不知道你会被问到什么，紧张情绪很容易控制你，让你甚至忘记自己的名字。我已经为工作面试准备了 21 个 Node.js 问题，从非常简单的问题到一些技术上更先进的话题，来帮助你。

Node.js 并不专门用于后端。我们还使用它来创建前端应用程序，这已经成为 Web 开发生态系统中非常重要的一部分。这意味着 Node.js 开发人员熟悉这项技术在不同 JavaScript 环境中的作用非常有用。出于这个原因，我已经包括了一些问题和答案。

## 指导方针

我建议在阅读答案之前，尝试自己回答问题。如果你没有全部拿到，明天再试一次，看看你还保留了多少。

你也有机会在这里为你的候选人寻找面试问题的例子。我相信这些应该是足够多样的，作为一个起点来帮助你评估他们的水平。

不仅仅是正确回答一个问题，我认为是细节显示了一个人知道多少。一个好的答案可能会引发一场对话，而这场对话可能会把一次紧张的经历变成与同事的闲聊。这对双方来说都是理想的结果。

## Node.js 问题

### Node.js 是什么？

Node.js 是一个基于 V8 引擎的 JavaScript 运行时环境。它允许我们在浏览器之外运行 JavaScript 通常是在 web 服务器中。

### Node.js 有什么用？

鉴于 Node.js 的单线程性质要求我们尽快释放事件循环，因此它非常擅长以低圈复杂度处理多个连接。这使得 Node.js 成为微服务和实时应用的理想选择。

### 什么是 npm？

[npm](https://www.npmjs.com/) 代表 Node.js 包管理器。它由一个命令行界面组成，我们可以用它来访问公共和私有包的在线注册表。

### 如何从头开始创建 Node.js app？

我们可以从创建项目文件夹开始。然后，我们在命令行中导航到该文件夹并运行`npm init`。最后，我们按照步骤填写我们的应用程序信息。

### “npm 安装”有什么作用？

它安装在`package.json`文件中找到的依赖项。

### 如何在 Node.js 中安装库？

`npm install name-of-the-library`将安装我们的库并将其作为`dependency`包含在内。如果我们添加`--save-dev`参数，它将作为`devDependency`包含在内。

### 如何创建自定义脚本？

我们需要进入`package.json`并在`scripts`字段中添加我们的自定义脚本。然后我们可以通过在终端运行`npm run name-of-script`来运行我们的脚本。

### 有没有可能用 Node.js 创建一个前端应用？

浏览器不能运行 Node.js 应用程序，但是您可以使用 webpack 或 package 之类的东西来捆绑代码，并将其转换为浏览器可以运行的东西。现在使用 Node.js 环境来构建前端应用程序非常普遍。Node.js 在前端的一个很好的例子是[electronic](https://www.electronjs.org/)框架，它利用 Node.js 和 chromium 来构建“原生”应用，例如 [VS Code](https://code.visualstudio.com/) 。

### 你能提到三个流行的 Node.js 框架吗？

Express.js 可能是迄今为止最流行的框架。 [Koajs](https://koajs.com/) 可能是最快的之一，而 [Sails.js](https://sailsjs.com/) 非常适合使用 socket.io 的实时双边通信应用

### Express.js 有什么用？

Express.js 使得为我们的 web 应用程序设置路线变得非常容易，这使得创建[REST API](https://www.sitepoint.com/developers-rest-api/)成为一个显而易见的选择。它非常灵活且易于使用，其中间件架构有助于保持系统的简单性和可伸缩性。

### 什么是加密？

Crypto 是一个 Node.js 内部库，它提供加密功能，例如加密和解密密码。

### 我们如何在 Node.js 中处理局部和全局范围？

与客户端 JavaScript 不同，在 Node.js 中，在最高作用域用`var`声明的变量不是全局的；它们位于所在模块的本地。在浏览器上，我们可以访问全局变量所在的`window`对象，Node.js 为此提供了一个名为`global`的对象。

### Node.js 可以访问文件系统吗？

是的。我们可以利用 [fs](https://nodejs.org/api/fs.html) 模块来读取、写入、复制和删除文件和文件夹。

### 不阻塞是什么意思？

这意味着一段代码，比如一个异步函数，被安排在事件循环的下一次迭代中运行，因此解除了对其余代码的阻塞，允许它继续运行。

### 什么是事件循环，它是如何工作的？

事件循环赋予了 Node.js 异步的本质。它在一个循环中调度一组五个阶段的执行。第一阶段运行预定的 [setTimeout](https://nodejs.org/api/timers.html#timers_settimeout_callback_delay_args) 和 [setInterval](https://nodejs.org/api/timers.html#timers_setinterval_callback_delay_args) 回调。第二个运行计划在当前迭代中运行的 IO 回调。第三个轮询将在下一次迭代中执行的事件。第四个运行 [setImmediate()](https://nodejs.org/api/timers.html#timers_setimmediate_callback_args) 回调。最后，第五个运行所有的“关闭”回调。

### 异步函数并行运行吗？

不会。异步函数将在下一次事件循环迭代中执行，而并行进程在其自己的进程或线程中运行。

### Node.js 是多线程的吗？

Node.js 进程在单线程中运行，但是我们可以使用`child_process`模块来并行运行多个进程，或者使用`Workers`来运行多个线程。

### 什么是 child_process 模块？

[child_process](https://nodejs.org/api/child_process.html) 模块让我们产生和派生子进程。这些都是独立的进程，在它们自己的 CPU 中运行，并让我们访问系统命令。

### web worker 和 worker thread 有什么区别？

[Web workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API) 在浏览器中实现， [worker threads](https://nodejs.org/api/worker_threads.html) 在 Node.js 中实现，它们都解决了同一个问题，就是提供并行处理。事实上，Worker Thread API 是基于 Web Workers 实现的。

### 与子进程相比，使用工作线程的优势是什么？

子进程使用自己的内存空间运行自己的进程，而工作线程是进程中可以与主线程共享内存的线程。这有助于避免昂贵的来回数据序列化。

### 您将使用什么来通过 HTTP 打开与客户端的双向实时连接？

我们可以使用 WebSockets 或长轮询。有像 soket.io 和 SignalR 这样的库为我们简化了这一点。如果 WebSockets 在浏览器中不可用，它们甚至提供回退到长轮询的客户端。

## 结论

我们已经走到了路的尽头。我希望这些问题对你有用。你能把它们都弄好吗？如果你不能，不要担心。除非你的目标是一个高级职位，否则你不需要知道所有的信息。只要确保你掌握了基础知识，无论你在哪里发现了知识差距，努力拓展你的界限。我向你保证它不会被忽视。

我祝你面试好运。保持冷静，相信你所知道的，对人友善——后者可能是最重要的。大多数人宁愿和一个善良、谦逊的人一起填补知识的空白，也不愿每天和一个傲慢、自私、难以共事的人呆在办公室，尽管他们是天才。

如果你是面试官，记住紧张可能会妨碍某人展示自己有多优秀。尽可能让他们感到舒适，让他们知道你站在他们一边，你希望他们搞定这件事！

这是所有的乡亲。我们将在未来的一篇文章中讨论常见的 Node.js 面试代码挑战，以及应对这些挑战所需的技能和思维模式。下一集再见！

## 分享这篇文章