# 2017 年成为更好的节点开发者的 10 个技巧

> 原文：<https://www.sitepoint.com/10-tips-to-become-a-better-node-developer/>

![A person in a traditional cross-legged meditation pose, focusing on becoming a better Node developer](img/f8562ce1a73964d0c0f6663cdc9f4208.png)

2017 年成为更好的节点开发者的 10 个技巧由客座作者 **Azat Mardan** 撰写。SitePoint 客座博文旨在为您带来来自网络社区的杰出作家和演讲者的精彩内容。

***注:**本文原标题是来自平台大师的最佳节点实践。这篇文章涵盖了真实的、经过检验和测试的模式，而不是 2017 年的新模式和最佳模式。尽管 Node gurus 的一些好的旧实践在 2017 年和 2018 年甚至 2019 年仍然适用，但 async/await 等新的前沿功能并没有在这里讨论。那是因为这些新特性不在 Node core 的代码里，也不在 npm、Express 等热门项目里。文章的第二部分将反映内容的本质。*

> 本文收录于我们的文集[现代 JavaScript](https://www.sitepoint.com/premium/books/modern-javascript) 。如果你想在一个地方获得所有现代 JavaScript 的速度，注册 SitePoint Premium 并下载一份。

我在 2012 年加入 Storify 时开始全职从事 Node 的工作。从那以后，我再也没有回头看或者觉得自己错过了 Python、Ruby、Java 或 PHP——这些语言是我在之前十年的 web 开发中使用过的。

Storify 对我来说是一份有趣的工作，因为与许多其他公司不同，Storify 在 JavaScript 上运行(也许现在仍然如此)一切。你看，大多数公司，尤其是像 PayPal、Walmart 或 Capital One 这样的大公司，只对其堆栈的某些部分使用 Node。通常他们将它用作 API 网关或编排层。太好了。但是对于软件工程师来说，没有什么比完全沉浸在节点环境中更好的了。

在这篇文章中，我将概述 10 个技巧，帮助你在 2017 年成为更好的节点开发者。这些技巧来自我，我在战壕里看到并学到了它们，也来自编写过最流行的节点和 npm 模块的人。以下是我们将要讨论的内容:

1.  [避免复杂性](#avoidcomplexity) —将你的代码组织成尽可能小的块，直到它们看起来太小，然后让它们更小。
2.  [使用异步代码](#useasynchronouscode) —像躲避瘟疫一样避开同步代码。
3.  [避免阻塞 require](#avoidblockingrequire) —将所有 require 语句放在文件的顶部，因为它们是同步的，会阻塞执行。
4.  [知道 require 被缓存了](#knowthatrequireiscached) —这可能是你代码中的一个特性或者一个 bug。
5.  [总是检查错误](#alwayscheckforerrors) —错误不是足球。永远不要抛出错误，永远不要跳过错误检查。
6.  [仅在同步代码中使用 try…catch](#alwayscheckforerrors)—`try...catch`对异步代码无用，加上 V8 无法像普通代码一样优化`try...catch`中的代码。
7.  [返回回调或使用 if … else](#returncallbacksoruseifelse) —只是为了确定，返回一个回调以防止执行继续。
8.  [监听错误事件](#listentotheerrorevents) —几乎所有的节点类/对象都扩展了事件发射器(观察者模式)并发出`error`事件。一定要听那个。
9.  [了解你的 npm](#knowyournpm) —用`-S`或`-D`代替`--save`或`--save-dev`安装模块
10.  [在 package.json](#knowyournpm) 中使用精确版本:npm 在使用`-S`时会愚蠢地默认添加一个插入符号，所以手动去掉它们以锁定版本。永远不要在你的应用中信任 semver，但是在开源模块中要这样做。
11.  *奖金*——使用不同的依赖关系。把你的项目只需要开发的东西放在`devDependencies`中，然后使用`npm i --production`。不需要的依赖项越多，易受攻击的风险就越大。

因此，让我们一分为二，分别看一看它们。我们走吧。

别忘了:如上所述，这是第一部分。你可以在第二部分中找到进一步的十条建议。

## 避免复杂性

看看 npm 的创始人 Isaac Z. Schlueter 写的一些模块。例如， [use-strict](https://www.npmjs.com/package/use-strict) 对模块强制执行 JavaScript strict 模式，而这仅仅是*的三行代码:*

```
var module = require('module')
module.wrapper[0] += '"use strict";'
Object.freeze(module.wrap) 
```

那么为什么要避免复杂性呢？根据其中一个传说，源自美国海军的一句名言是:保持简单，愚蠢(或者是*“保持简单，愚蠢”*？).这是有原因的。人脑在任何时候的工作记忆中只能容纳五到七个项目。这只是事实。

通过将代码模块化成更小的部分，您和其他开发人员可以更好地理解和思考它。也可以更好的测试一下。考虑这个例子，

```
app.use(function(req, res, next) {
  if (req.session.admin === true) return next()
  else return next(new Error('Not authorized'))
}, function(req, res, next) {
  req.db = db
  next()
}) 
```

或者这段代码:

```
const auth = require('./middleware/auth.js')
const db = require('./middleware/db.js')(db)

app.use(auth, db) 
```

我相信你们大多数人会更喜欢第二个例子，尤其是当名字不言自明的时候。当然，当你写代码的时候，你可能认为你理解它是如何工作的。也许你甚至想通过在一行中链接几个方法来炫耀你有多聪明。拜托，你这个笨蛋的代号。代码给六个月没看这个代码的你，或者一个尝试过或者喝醉了的你。如果你在心智能力最强的时候写代码，那么你以后就更难理解了，更不用说你那些连算法的错综复杂都不熟悉的同事了。保持简单对于使用异步方式的节点来说尤其如此。

是的，的确发生了 [left-pad 事件](http://www.theregister.co.uk/2016/03/23/npm_left_pad_chaos/)，但这只影响了依赖公共注册表的项目，替换版本在 11 分钟内就发布了。变小的好处远远大于坏处。此外，npm 已经[改变了它的不公开政策](http://blog.npmjs.org/post/141905368000/changes-to-npms-unpublish-policy)，任何严肃的项目都应该使用缓存策略或私有注册中心(作为临时解决方案)。

## 使用异步代码

同步代码*在 Node 中有(小)位置吗？它主要用于编写 CLI 命令或其他与 web 应用程序无关的脚本。节点开发人员大多构建 web 应用程序，因此他们使用异步代码来避免阻塞线程。*

 *例如，如果我们只是构建一个数据库脚本，而不是一个处理并行/并发任务的系统，这可能没问题:

```
let data = fs.readFileSync('./acconts.json')
db.collection('accounts').insert(data, (results))=>{
  fs.writeFileSync('./accountIDs.json', results, ()=>{process.exit(1)})
}) 
```

但是在构建 web 应用程序时，这样会更好:

```
app.use('/seed/:name', (req, res) => {
  let data = fs.readFile(`./${req.params.name}.json`, ()=>{
    db.collection(req.params.name).insert(data, (results))=>{
      fs.writeFile(`./${req.params.name}IDs.json`, results, ()={res.status(201).send()})
    })
  })
}) 
```

区别在于您是在编写并发(通常是长时间运行)还是非并发(短时间运行)系统。根据经验，总是在 Node 中编写异步代码。

## 避免阻塞要求

Node 有一个简单的模块加载系统，它使用 CommonJS 模块格式。它内置的`require`函数是包含存在于单独文件中的模块的简单方法。与 AMD/requirejs 不同，Node/CommonJS 的模块加载方式是同步的。`require`的工作方式是:*你把导出的东西导入一个模块，或者一个文件*。

```
const react = require('react') 
```

大多数开发者不知道的是`require`是缓存的。因此，只要解析后的文件名没有剧烈的变化(在 npm 模块的情况下没有)，那么来自该模块的代码将被执行并加载到变量中一次(对于该进程)。这是一个很好的优化。然而，即使有了缓存，最好还是把 require 语句放在前面。考虑这段代码，它只加载实际使用它的路由上的`axios`模块。`/connect`路径会比需要的慢，因为模块导入是在请求发出时进行的:

```
app.post('/connect', (req, res) => {
  const axios = require('axios')
  axios.post('/api/authorize', req.body.auth)
    .then((response)=>res.send(response))
}) 
```

一种更好、更高效的方法是在定义服务器之前加载模块，而不是按照以下方式:

```
const axios = require('axios')
const express = require('express')
app = express()
app.post('/connect', (req, res) => {
  axios.post('/api/authorize', req.body.auth)
    .then((response)=>res.send(response))
}) 
```

## 知道 require 被缓存

我在前面提到过`require`被缓存，但是有趣的是我们可以在`module.exports`的之外有代码*。举个例子，*

```
console.log('I will not be cached and only run once, the first time')

module.exports = () => {
  console.log('I will be cached and will run every time this module is invoked')
} 
```

知道有些代码可能只运行一次，您就可以利用这个特性。

## 总是检查错误

节点不是 Java。在 Java 中，你会抛出错误，因为大多数情况下，如果出现错误，你不希望应用程序继续运行。在 Java 中，你可以用一个`try...catch`在更高的层次上处理多个错误。

Node 就不是这样了。由于 Node 使用[事件循环](https://www.youtube.com/watch?v=8aGhZQkoFbQ)并异步执行，所以当错误发生时，任何错误都与任何错误处理程序(如`try...catch`)的上下文分离。这在节点中没有用:

```
try {
  request.get('/accounts', (error, response)=>{
    data = JSON.parse(response)
  })
} catch(error) {
  // Will NOT be called
  console.error(error)
} 
```

但是`try...catch`仍然可以用在同步节点代码中。所以这是对前一个片段的更好的重构:

```
request.get('/accounts', (error, response)=>{
  try {
    data = JSON.parse(response)
  } catch(error) {
    // Will be called
    console.error(error)
  }
}) 
```

如果我们不能在一个`try...catch`块中包装`request`调用，那就会给我们留下来自未处理请求的错误。节点开发人员通过向您提供`error`作为回调参数来解决这个问题。因此，在每次回调时，您都需要手动处理`error`。您可以通过检查错误(确保它不是`null`)然后或者向用户或客户端显示错误消息并记录它，或者通过用`error`调用回调将它传递回调用堆栈(如果您在调用堆栈中有回调和另一个函数)。

```
request.get('/accounts', (error, response)=>{
  if (error) return console.error(error)
  try {
    data = JSON.parse(response)
  } catch(error) {
    console.error(error)
  }
}) 
```

您可以使用的一个小技巧是 [okay](https://www.npmjs.com/package/okay) 库。你可以像这样应用它来避免对无数嵌套回调的手工错误检查(你好，[回调地狱](https://www.sitepoint.com/saved-from-callback-hell/))。

```
var ok = require('okay')

request.get('/accounts', ok(console.error, (response)=>{
  try {
    data = JSON.parse(response)
  } catch(error) {
    console.error(error)
  }
})) 
```

## 返回回调或使用 if … else

节点是并发的。所以如果你不小心的话，这个特性可能会变成一个 bug。为了安全起见，用 return 语句终止执行:

```
let error = true
if (error) return callback(error)
console.log('I will never run - good.') 
```

避免由于控制流处理不当而导致的一些意外并发(和失败)。

```
let error = true
if (error) callback(error)
console.log('I will run. Not good!') 
```

为了确保万无一失，`return`一个阻止执行继续的回调。

## 听听`error`事件

几乎所有的节点类/对象都扩展了事件发射器(观察者模式)并发射`error`事件。对于开发人员来说，这是一个捕捉那些讨厌的错误并在它们造成严重破坏之前处理它们的机会。

养成使用`.on()`为`error`创建事件监听器的好习惯:

```
var req = http.request(options, (res) => {
  if (('' + res.statusCode).match(/^2\d\d$/)) {
    // Success, process response
  } else if (('' + res.statusCode).match(/^5\d\d$/))
    // Server error, not the same as req error. Req was ok.
  }
})

req.on('error', (error) => {
  // Can't even make a request: general error, e.g. ECONNRESET, ECONNREFUSED, HPE_INVALID_VERSION
  console.log(error)
}) 
```

## 了解你的国家预防机制

很多节点和事件前端开发者都知道有`--save`(用于`npm install`)不仅会安装一个模块，还会用模块的版本在`package.json`中创建一个条目。嗯，还有`--save-dev`，代表`devDependencies`(生产中不需要的东西)。但是你知道你可以只用`-S`和`-D`来代替`--save`和`--save-dev`吗？是的，你可以。

当你处于模块安装模式时，继续删除那些`-S`和`-D`将为你创建的`^`标志。它们是危险的，因为它们将允许`npm install`(或其快捷方式`npm i`)从 npm 获取最新的次要(语义版本控制中的第二位)版本。例如，6.1.0 到 6.2.0 是次要版本。

npm 团队相信[永远](http://semver.org/)，但你不应该。我的意思是，他们加上了插入符号`^`,因为他们相信开源开发者不会在小版本中引入突破性的变化。任何理智的人都不应该相信它。锁定您的版本。更好的是，使用[包膜](https://nodejs.org/en/blog/npm/managing-node-js-dependencies-with-shrinkwrap/) : `npm shrinkwrap`来创建一个新文件，其中包含依赖项的确切版本。

## 结论

这篇文章是两篇文章的第一部分。我们已经介绍了很多内容，从使用回调和异步代码，到检查错误和锁定依赖关系。我希望你在这里找到了新的或者有用的东西。如果你喜欢，一定要看看第二部分: [10 Node.js 最佳实践:来自节点大师的启示](https://www.sitepoint.com/node-js-best-practices-from-the-node-gurus/)。

告诉我你的想法。我错过了什么吗？你的做法有所不同吗？请在下面的评论中告诉我。

## 分享这篇文章*