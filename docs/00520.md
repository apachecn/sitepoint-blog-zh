# JavaScript 中的延迟、睡眠、暂停和等待

> 原文：<https://www.sitepoint.com/delay-sleep-pause-wait/>

**许多编程语言都有一个`sleep`功能，可以将程序的执行延迟给定的秒数。然而，由于 JavaScript 的异步特性，它缺少这种功能。在本文中，我们将简要地看一下为什么会这样，然后我们如何自己实现一个`sleep`函数。**

## 理解 JavaScript 的执行模型

在开始之前，确保我们正确理解 JavaScript 的执行模型是很重要的。

考虑下面的 Ruby 代码:

```
require 'net/http'
require 'json'

url = 'https://api.github.com/users/jameshibbard'
uri = URI(url)
response = JSON.parse(Net::HTTP.get(uri))
puts response['public_repos']
puts "Hello!" 
```

正如所料，这段代码请求 GitHub API 获取我的用户数据。然后它解析响应，输出归属于我的 GitHub 帐户的公开转发数，最后打印“Hello！”对着屏幕。执行自上而下。

与之相比，等效的 JavaScript 版本:

```
fetch('https://api.github.com/users/jameshibbard')
  .then(res => res.json())
  .then(json => console.log(json.public_repos));
console.log("Hello!"); 
```

如果运行这段代码，它将输出“Hello！”屏幕上，*然后是*归于我的 GitHub 账户的公开回购数量。

这是因为从 API 获取数据是 JavaScript 中的异步操作。JavaScript 解释器将遇到`fetch`命令并分派请求。然而，它将*而不是*等待请求完成。相反，它会继续前进，输出“你好！”到控制台，然后当几百毫秒后请求返回时，它将输出 repos 的数量。

如果这些对你来说是新闻，你应该看看这个精彩的会议演讲:[到底什么是事件循环？](https://www.youtube.com/watch?v=8aGhZQkoFbQ)。

## 您可能实际上不需要 JS 睡眠功能

现在我们对 JavaScript 的执行模型有了更好的理解，让我们看看 JavaScript 是如何处理延迟和异步操作的。

### 使用`setTimeout`创建一个简单的延迟

在 JavaScript 中创建延迟的标准方式是使用它的 [`setTimeout`方法](https://www.sitepoint.com/javascript-settimeout-function-examples/)。例如:

```
console.log("Hello");
setTimeout(() => {  console.log("World!"); }, 5000); 
```

这会将“Hello”记录到控制台，然后让 JavaScript 等待 5 秒钟，然后记录“World！”到控制台。在很多情况下，这就足够了:做一些事情，等待，然后做其他事情。分类了！

但是，请注意`setTimeout`是一个异步方法。尝试修改前面的代码，如下所示:

```
console.log("Hello");
setTimeout(() => { console.log("World!"); }, 5000);
console.log("Goodbye!"); 
```

它将记录:

```
Hello
Goodbye!
World! 
```

### 等待带有 setTimeout 的东西

也可以使用`setTimeout`(或者它的表亲`setInterval`)让 JavaScript 或 TypeScript 等待，直到满足一个条件。例如，下面是如何使用`setTimeout`来等待某个元素出现在网页上:

```
function pollDOM () {
  const el = document.querySelector('my-element');

  if (el.length) {
    // Do something with el
  } else {
    setTimeout(pollDOM, 300); // try again in 300 milliseconds
  }
}

pollDOM(); 
```

这假设元素会在某个点出现。如果你不确定是不是这样，你需要考虑取消定时器(使用`clearTimeout`或`clearInterval`)。

如果你想了解更多关于 JavaScript 的`setTimeout`方法，请查阅我们的教程中的[，里面有很多例子可以帮助你。](https://www.sitepoint.com/jquery-settimeout-function-examples/)

### 现代 JavaScript 中的流控制

在编写 JavaScript 时，我们经常需要让 JS 等待某件事情发生(例如，从 API 获取数据)，然后做出响应(例如更新 UI 以显示数据)。

上面的例子使用了一个匿名回调函数来实现这个目的，但是如果你需要等待多件事情发生，语法很快就会变得非常复杂，你最终会陷入[回调地狱](https://www.sitepoint.com/saved-from-callback-hell/)。

幸运的是，这种语言在过去的几年里有了很大的发展，现在为我们提供了新的构造来避免这种情况。

例如，使用 [async await](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Async_await) 我们可以重写初始代码来从 GitHub API 获取信息:

```
(async () => {
  const res = await fetch(`https://api.github.com/users/jameshibbard`);
  const json = await res.json();
  console.log(json.public_repos);
  console.log("Hello!");
})(); 
```

现在代码从上到下执行。JavaScript 解释器等待网络请求完成，首先记录公共回复的数量，然后是“Hello！”消息。

如果这是你想要完成的事情，我鼓励你阅读我们的文章[现代 JS 中的流控制:对异步/等待承诺的回调](https://www.sitepoint.com/flow-control-callbacks-promises-async-await/)。

## 给原生 JavaScript 带来睡眠

如果你还和我在一起，那么我猜你已经准备好阻塞那个执行线程，让 JavaScript 等待它结束。

你可以这样做:

```
function sleep(milliseconds) {
  const date = Date.now();
  let currentDate = null;
  do {
    currentDate = Date.now();
  } while (currentDate - date < milliseconds);
}

console.log("Hello");
sleep(2000);
console.log("World!"); 
```

正如所料，这将记录“你好”，暂停两秒钟，然后记录“世界！”

它通过使用 [Date.now](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/now) 方法获得自 1970 年 1 月 1 日以来经过的毫秒数，并将该值赋给一个`date`变量。然后，在进入`do ... while`循环之前，它创建一个空的`currentDate`变量。在循环中，它反复获取自 1970 年 1 月 1 日以来经过的毫秒数，并将该值赋给先前声明的`currentDate`变量。当`date`和`currentDate`之间的差值小于以毫秒为单位的期望 JS 延迟时，循环将继续进行。

任务完成了，对吧？嗯，不完全是…

## 更好的睡眠功能

也许这段代码确实如您所愿，但是请注意，它有一个很大的缺点:循环将阻塞 JavaScript 的执行线程，并确保在它完成之前没有人可以与您的程序交互。如果你需要一个大的延迟，有可能会导致整个系统崩溃。

那怎么办呢？

嗯，也可以将本文前面学到的技术结合起来，实现一个更少干扰的睡眠功能:

```
function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

console.log("Hello");
sleep(2000).then(() => { console.log("World!"); }); 
```

这段代码将记录“Hello”，等待两秒钟，然后记录“World！”我们使用`setTimeout`方法在给定的毫秒数后解析[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)。

注意，我们需要使用一个`then`回调来确保第二条消息被延迟记录。我们还可以将更多的回调链接到第一个回调上:

```
console.log('Hello')
sleep(2000)
  .then(() => console.log('world!'))
  .then(() => sleep(2000))
  .then(() => console.log('Goodbye!')) 
```

这是可行的，但我不是所有`.then()`的最大粉丝。我们可以使用`async ... await`来美化它:

```
function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

async function delayedGreeting() {
  console.log("Hello");
  await sleep(2000);
  console.log("World!");
  await sleep(2000);
  console.log("Goodbye!");
}

delayedGreeting(); 
```

这看起来更好，但意味着无论什么代码使用了`sleep`函数，都需要标记为`async`。

看笔 [yLLQJrX](https://codepen.io/SitePoint/pen/yLLQJrX)by SitePoint([@ SitePoint](https://codepen.io/SitePoint))
上[码笔](https://codepen.io)。