# jQuery 延迟对象介绍

> 原文：<https://www.sitepoint.com/introduction-jquery-deferred-objects/>

长期以来，JavaScript 开发人员一直使用回调函数来执行多项任务。一个很常见的例子是通过`addEventListener()`函数添加一个回调，在事件被触发时执行各种操作，比如`click`或者`keypress`。回调函数很简单，可以完成简单情况下的工作。不幸的是，当您的 web 页面变得越来越复杂，并且您需要并行或顺序地执行许多异步操作时，它们就变得难以管理了。

ECMAScript 2015(又名 ECMAScript 6)引入了一种处理这种情况的本地方法:承诺。如果你不知道什么是承诺，你可以阅读文章[JavaScript 承诺概述](https://www.sitepoint.com/overview-javascript-promises/)。jQuery 提供了并且仍然提供它自己风格的承诺，称为[延迟对象](https://api.jquery.com/category/deferred-object/)。在 promises 被引入 ECMAScript 的几年前，它们就被引入了 jQuery。在本文中，我将讨论什么是`Deferred`对象，以及它们试图解决什么问题。

## 简史

在 jQuery 1.5 中引入了`Deferred`对象，作为一个可链接的实用程序，用于将多个回调注册到回调队列中，调用回调队列，以及传递任何同步或异步函数的成功或失败状态。从那以后，它一直是讨论的主题，一些批评，以及一路上的许多变化。批评的几个例子是[你错过了承诺的要点](https://blog.domenic.me/youre-missing-the-point-of-promises/)和 [JavaScript 承诺以及为什么 jQuery 实现被破坏](https://thewayofcode.wordpress.com/tag/jquery-deferred-broken/)。

与[承诺对象](http://api.jquery.com/Types/#Promise)，`Deferred`一起表示承诺的 jQuery 实现。在 jQuery 和 2.x 版本中,`Deferred`对象遵循[、CommonJS 承诺/提议](http://wiki.commonjs.org/wiki/Promises/A)。该提议被用作本地承诺所基于的[承诺/A+提议](https://promisesaplus.com/)的基础。正如在简介中提到的，jQuery 不遵守 Promises/A+提议的原因是因为它在这个提议被构思之前就实现了 Promises。

因为 jQuery 是一个前身，并且由于向后兼容的问题，在纯 JavaScript 和 jQuery 1.x 和 2.x 中使用 promises 的方式有所不同。此外，因为 jQuery 遵循不同的建议，所以这个库与其他实现 promises 的库不兼容，比如 Q 库。

在即将到来的 **jQuery 3** 中，与 native promises 的互操作性(如 ECMAScript 2015 中所实现的)[已经得到了改进](https://github.com/jquery/jquery/issues/1722)。main 方法(`then()`)的签名因为向后兼容的原因还是有点不同，但是行为更符合标准。

## jQuery 中的回调

为了理解为什么您可能需要使用`Deferred`对象，让我们讨论一个例子。在使用 jQuery 时，使用它的 Ajax 方法来执行异步请求是很常见的。为了便于举例，假设您正在开发一个向 GitHub API 发送 Ajax 请求的网页。您的目标是检索用户的存储库列表，找到最近更新的存储库，找到名称中包含字符串“README.md”的第一个文件，最后检索该文件的内容。根据这个描述，每个 Ajax 请求可能只有在前一步完成后才开始。换句话说，请求必须按照*序列*运行。

将这个描述转换成伪代码(请注意，我没有使用真正的 GitHub API)，我们得到:

```
var username = 'testuser';
var fileToSearch = 'README.md';

$.getJSON('https://api.github.com/user/' + username + '/repositories', function(repositories) {
  var lastUpdatedRepository = repositories[0].name;

  $.getJSON('https://api.github.com/user/' + username + '/repository/' + lastUpdatedRepository + '/files', function(files) {
    var README = null;

    for (var i = 0; i < files.length; i++) {
      if (files[i].name.indexOf(fileToSearch) >= 0) {
        README = files[i].path;

        break;
      }
    }

    $.getJSON('https://api.github.com/user/' + username + '/repository/' + lastUpdatedRepository + '/file/' + README + '/content', function(content) {
      console.log('The content of the file is: ' + content);
    });
  });
}); 
```

正如您在这个例子中看到的，使用回调，我们必须嵌套调用，以我们想要的顺序执行 Ajax 请求。这降低了代码的可读性。有许多嵌套回调或者必须同步的独立回调的情况通常被称为“回调地狱”。

为了稍微好一点，您可以从我创建的匿名内联函数中提取命名函数。然而，这个改变并没有多大帮助，我们仍然发现自己在回调地狱。输入`Deferred`和`Promise`对象。

## 延期对象和承诺对象

在执行异步操作时，可以使用`Deferred`对象，比如 Ajax 请求和动画。在 jQuery 中，`Promise`对象是从`Deferred`对象或`jQuery`对象创建的。它拥有`Deferred`对象的方法子集:`always()`、`done()`、`fail()`、`state()`和`then()`。我将在下一节介绍这些方法和其他方法。

如果您来自本地 JavaScript 世界，您可能会对这两个对象的存在感到困惑。为什么 JavaScript 有一个对象(`Promise`)却有两个对象(`Deferred`和`Promise`)？为了解释它们的区别和用例，我将采用我在我的书 [jQuery in Action，第三版](https://www.manning.com/books/jquery-in-action-third-edition)中使用的相同类比。

如果你编写一个处理异步操作的函数，并且它应该返回一个值(也可能是一个错误或者根本没有值)，那么通常使用对象。在这种情况下，您的函数是值的**生产者**，并且您希望阻止用户更改`Deferred`的状态。当你是函数的**消费者**时，使用 promise 对象。

为了澄清这个概念，假设您想要实现一个基于承诺的`timeout()`函数(我将在本文的下一节[中向您展示这个例子的代码)。您负责编写必须等待给定时间的函数(在这种情况下不返回值)。这使你成为制作人。您的函数的**消费者**并不关心解析或拒绝它。消费者只需要能够添加在`Deferred`完成、失败或进展时执行的功能。此外，你要确保消费者不能自行决定解决或拒绝`Deferred`。为了实现这个目标，您需要返回您在`timeout()`函数中创建的`Deferred`的`Promise`对象，而不是`Deferred`本身。通过这样做，您可以确保除了您的`timeout()`函数之外，没有人能够调用`resolve()`或`reject()`方法。](#creatingapromisebasedsettimeoutfunction)

你可以在这个 [StackOverflow 问题](http://stackoverflow.com/q/17308172/1136887)中读到更多关于 jQuery 的 Deferred 和 Promise 对象之间的区别。

现在您已经知道了这些对象是什么，让我们来看看可用的方法。

## 延迟方法

对象非常灵活，提供了满足您所有需求的方法。它可以通过调用`jQuery.Deferred()`方法来创建，如下所示:

```
var deferred = jQuery.Deferred(); 
```

或者，使用`$`快捷键:

```
var deferred = $.Deferred(); 
```

一旦创建，`Deferred`对象就公开了几个方法。忽略那些已弃用或已删除的，它们是:

*   `always(callbacks[, callbacks, ..., callbacks])`:添加当`Deferred`对象被解析或拒绝时调用的处理程序。
*   `done(callbacks[, callbacks, ..., callbacks])`:添加解析`Deferred`对象时调用的处理程序。
*   `fail(callbacks[, callbacks, ..., callbacks])`:添加当`Deferred`对象被拒绝时调用的处理程序。
*   `notify([argument, ..., argument])`:用给定的参数调用`Deferred`对象上的`progressCallbacks`。
*   `notifyWith(context[, argument, ..., argument])`:用给定的上下文和参数调用`Deferred`对象上的`progressCallbacks`。
*   `progress(callbacks[, callbacks, ..., callbacks])`:添加当`Deferred`对象生成进度通知时调用的处理程序。
*   `promise([target])`:返回一个`Deferred`的`Promise`对象。
*   `reject([argument, ..., argument])`:拒绝一个`Deferred`对象，用给定的参数调用任何一个`failCallbacks`。
*   `rejectWith(context[, argument, ..., argument])`:拒绝一个`Deferred`对象，用给定的上下文和参数调用任何一个`failCallbacks`。
*   `resolve([argument, ..., argument])`:解析一个`Deferred`对象，用给定的参数调用任何一个`doneCallbacks`。
*   `resolveWith(context[, argument, ..., argument])`:解析一个`Deferred`对象，用给定的上下文和参数调用任何一个`doneCallbacks`。
*   `state()`:确定`Deferred`对象的当前状态。
*   `then(resolvedCallback[, rejectedCallback[, progressCallback]])`:添加当`Deferred`对象被解析、拒绝或仍在处理中时要调用的处理程序。

对这些方法的描述让我有机会强调 jQuery 文档和 ECMAScript 规范中使用的术语之间的一个区别。在 ECMAScript 规范中，当一个承诺被履行或拒绝时，它就被认为得到了解决。然而，在 jQuery 的文档中，resolved 一词用于指代 ECMAScript 规范所称的已实现状态。

由于所提供方法的数量，不可能在本文中涵盖所有方法。然而，在接下来的部分中，我将展示几个使用`Deferred`和`Promise`的例子。在第一个例子中，我们将重写“jQuery 中的回调”一节中检查的代码片段，但是我们将使用这些对象，而不是使用回调。在第二个例子中，我将阐明所讨论的生产者-消费者类比。

## 延迟的顺序 Ajax 请求

在这一节中，我将展示如何使用`Deferred`对象及其一些方法来提高在“jQuery 中的回调”一节中开发的代码的可读性。在深入研究之前，我们必须了解我们需要哪些可用的方法。

根据我们的需求和提供的方法列表，很明显我们可以使用`done()`或`then()`方法来管理成功的案例。因为你们中的许多人可能已经习惯了 JavaScript 的`Promise`对象，所以在这个例子中，我将使用`then()`方法。这两种方法的一个重要区别是，`then()`能够将作为参数接收的值转发给在它之后定义的其他`then()`、`done()`、`fail()`或`progress()`调用。

最终结果如下所示:

```
var username = 'testuser';
var fileToSearch = 'README.md';

$.getJSON('https://api.github.com/user/' + username + '/repositories')
  .then(function(repositories) {
    return repositories[0].name;
  })
  .then(function(lastUpdatedRepository) {
    return $.getJSON('https://api.github.com/user/' + username + '/repository/' + lastUpdatedRepository + '/files');
  })
  .then(function(files) {
    var README = null;

    for (var i = 0; i < files.length; i++) {
      if (files[i].name.indexOf(fileToSearch) >= 0) {
        README = files[i].path;

        break;
      }
    }

    return README;
  })
  .then(function(README) {
    return $.getJSON('https://api.github.com/user/' + username + '/repository/' + lastUpdatedRepository + '/file/' + README + '/content');
  })
  .then(function(content) {
    console.log(content);
  }); 
```

正如你所看到的，代码可读性更好，因为我们能够将整个过程分解成小的步骤，这些步骤都在同一层次上(就缩进而言)。

## 创建基于承诺的 setTimeout 函数

您可能知道， [setTimeout()](https://www.sitepoint.com/jquery-settimeout-function-examples/) 是一个在给定时间后执行回调函数的函数。这两个元素(回调函数和时间)都应该作为参数提供。假设您想在一秒钟后将一条消息记录到控制台。通过使用`setTimeout()`函数，您可以用如下所示的代码实现这个目标:

```
setTimeout(
  function() {
    console.log('I waited for 1 second!');
  },
  1000
); 
```

如您所见，第一个参数是要执行的函数，而第二个参数是要等待的毫秒数。这个功能已经工作了很多年，但是如果您需要在您的`Deferred`链中引入延迟，该怎么办呢？

在下面的代码中，我将向您展示如何使用 jQuery 提供的`Promise`对象来开发基于承诺的`setTimeout()`函数。为此，我将使用`Deferred`对象的`promise()`方法。

最终结果如下所示:

```
function timeout(milliseconds) {
  // Create a new Deferred object
  var deferred = $.Deferred();

  // Resolve the Deferred after the amount of time specified by milliseconds
  setTimeout(deferred.resolve, milliseconds);

  // Return the Deferred's Promise object
  return deferred.promise();
}

timeout(1000).then(function() {
  console.log('I waited for 1 second!');
}); 
```

在这个清单中，我定义了一个名为`timeout()`的函数，它包装了 JavaScript 的本机`setTimeout()`函数。在`timeout()`内部，我创建了一个新的`Deferred`对象来管理一个异步任务，该任务包括在指定的毫秒数之后解析`Deferred`对象。在这种情况下，`timeout()`函数是值的生产者，因此它创建了`Deferred`对象并返回一个`Promise`对象。通过这样做，我确保了函数的调用者(消费者)不能随意解析或拒绝`Deferred`对象。实际上，调用者只能添加函数来执行，使用`done()`和`fail()`等方法。

## jQuery 1.x/2.x 和 jQuery 3 的区别

在第一个使用`Deferred`的例子中，我们开发了一个代码片段，它寻找一个名称中包含字符串“README.md”的文件，但是我们没有考虑没有找到这样一个文件的情况。这种情况可以看作是一种失败。当这种情况发生时，我们可能希望中断调用链，直接跳到它的结尾。要做到这一点，抛出一个异常并用`fail()`方法捕获它是很自然的，就像你用 JavaScript 的`catch()`方法一样。

在 Promises/A 和 Promises/A+兼容的库(例如，jQuery 3.x)中，抛出的异常被转换为拒绝，并调用失败回调，例如用`fail()`添加的回调。这将异常作为参数接收。

在 jQuery 1.x 和 2.x 中，一个未被捕获的异常会暂停程序的执行。这些版本允许抛出的异常冒泡，通常达到`window.onerror`。如果没有定义函数来处理这个异常，将显示异常消息，并中止程序的执行。

为了更好地理解不同的行为，请看一下我书中的这个例子:

```
var deferred = $.Deferred();
deferred
  .then(function() {
    throw new Error('An error message');
  })
  .then(
    function() {
      console.log('First success function');
    },
    function() {
      console.log('First failure function');
    }
  )
  .then(
    function() {
      console.log('Second success function');
    },
    function() {
      console.log('Second failure function');
    }
  );

deferred.resolve(); 
```

在 jQuery 3.x 中，这段代码会将消息“第一次失败函数”和“第二次成功函数”写入控制台。原因是，正如我之前提到的，规范规定抛出的异常应该被转换为拒绝，并且必须用异常调用失败回调。此外，一旦管理了异常(在我们的示例中是通过传递给第二个`then()`的失败回调)，应该执行下面的成功函数(在本例中，成功回调传递给第三个`then()`)。

在 jQuery 1.x 和 2.x 中，只执行第一个函数(抛出错误的函数),您只会在控制台上看到消息“未捕获的错误:错误消息”。

### jQuery 1.x/2.x

[jsbin.com](https://jsbin.com/nozege/embed?js,console)T2【上 JS 斌】

### jQuery 3

[jsbin.com](https://jsbin.com/kifazi/embed?js,console)T2【上 JS 斌】

为了进一步提高其与 ECMAScript 2015 的兼容性，jQuery 3 还为名为`catch()`的`Deferred`和`Promise`对象添加了一个新方法。这是一个定义当`Deferred`对象是`rejected`或者它的`Promise`对象处于拒绝状态时执行的处理程序的方法。其签名如下:

```
deferred.catch(rejectedCallback) 
```

这个方法不过是`then(null, rejectedCallback)`的一个捷径。

## 结论

在本文中，我向您介绍了 jQuery 的 promises 实现。承诺可以让你避免讨厌的同步并行异步函数的技巧，以及在回调中嵌套回调的需要…

除了展示几个例子之外，我还介绍了 jQuery 3 如何提高与原生承诺的互操作性。尽管旧版本的 jQuery 和 ECMAScript 2015 之间存在显著差异，但`Deferred`仍然是您工具箱中一个非常强大的工具。作为一名专业开发人员，随着项目难度的增加，你会发现自己经常使用它。

## 分享这篇文章