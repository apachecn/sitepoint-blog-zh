# JavaScript 中猴子补丁的实用用法

> 原文：<https://www.sitepoint.com/pragmatic-monkey-patching/>

![Monkey in a spacesuit typing code into a terminal](img/0f8d72dd8155b188c0fa1f92a824e3d3.png)

这篇文章由[莫里茨·克罗格](https://www.sitepoint.com/author/morkro/)和[汤姆·格列柯](https://www.sitepoint.com/author/tgreco/)进行了同行评议。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

除了一件让你抓狂的小事之外，你曾经使用过运行良好的第三方代码吗？为什么创建者忘记删除那些讨厌的控制台日志？如果 API 调用程序可以多做一件事，那不是很好吗？如果是这样，那么你知道维护者很难(或者不可能)实现你的变更。但是自己改代码呢？如果你没有资源，又不想自己主持，你怎么能做到呢？欢迎来到 JavaScript 猴子补丁的世界！

在这篇文章中，我们将看看什么是 Monkey 补丁，并通过一些不同的例子，使用它来改变第三方部件的功能，以满足我们的需求。

## 什么是猴子打补丁？

[猴子打补丁](https://en.wikipedia.org/wiki/Monkey_patch)(以下简称 MP)是一种在不改变其原始源代码的情况下，覆盖、扩展甚至抑制代码段默认行为的技术。这是通过用一个*修正的*版本替换原来的行为来实现的。

本文将使用现有的[反馈框小部件](https://github.com/jwillmer/feedBackBox)，它显示一个简单的可滑动的弹出窗口，如下图所示，包含一个反馈表单。

![A feedback form widget, which is going to be monkey patched](img/4b906bcb095b0e7180feb181a64120bd.png)

源代码被修改以包括作为 MP 目标的用例。我说的目标指的是我们将要用*修补*的一个特定的功能、特性或者，在最低层次上，方法。

我做的另一个修改是删除代码周围立即调用的函数表达式(IIFE)。这样做是为了专注于 MP 的技术。

您可以找到完整的示例，包括本文中讨论的 monkey 补丁，[作为 Plunker](https://plnkr.co/edit/CgH6AQ40Zx9BrXXEXMVT?p=preview) 。

## 猴子打补丁不是不好的做法吗？

在进入正题之前，让我们先弄清楚一件事:**是的**，MP 被认为是一种**的不良做法**——邪恶的 *eval* ，命令式编程，可变数据结构，双向绑定等等也是如此。

如果你使用其中的任何一个，很可能会有一个相当大的群体告诉你，你做错了，应该改变这个或那个，以适应更好的条件。但是和往常一样，有不同的工具和技术可以使用，它们对特定场景的适用性各不相同。有时看起来极端、疯狂或简单糟糕的事情可能是特定案例的最后手段。不幸的是，因为一些实践被认为是不好的，你甚至找不到很多描述如何以正确的方式做错误的事情的文章。

这里描述的情况可能是一个不自然的情况，用一个假的小部件驱动到极端，以显示你的选择。作为读者，你必须决定你是否喜欢你所看到的。如果没有别的，读完这篇文章你会有更好的理解，以便反驳 MP。

## 猴子补丁的目标

在我们深入研究这些技术之前，让我们先检查一下我们想要达到的目标。修改后的小部件有一些我们想要解决的代码味道。

### 硬核背景色

第一个是名为`toggleError`的方法，它应该根据布尔参数改变元素的背景颜色

```
FeedbackBox.prototype.toggleError = function(obj, isError) {
  if(isError) {
    obj.css("background-color", "darkgrey");
  } else {
    obj.css("background-color", "");
  }
} 
```

如您所见，它通过 jQuery 方法`css`设置了背景色属性。这是一个问题，因为我们希望通过样式表规则指定这一点。

### 讨厌的控制台日志

在开发小部件时，控制台日志被用来提示开发者当前正在执行什么。在开发过程中可能是好的方法，在生产使用中肯定不是最好的方法。因此，我们需要找到一种方法来剥离所有这些调试语句。

### 拦截广告服务器调用

这个小部件很棒，但是它有一个奇怪的行为。每次初始化脚本时，它都会向一个陌生的广告服务器发出请求，并在我们的页面上显示不必要的膨胀。

```
FeedbackBox.prototype.init = function() {
  // call to an adserver we'd like to skip
  $.ajax('vendor/service.json', {
    method: 'GET'
  }).then(function(data) {
    console.log("FeedbackBox: AdServer contacted");
  });

  ... 
```

> **注意:**演示代码针对 Plunker 内部的一个 JSON 文件来模拟一个传出的 Ajax 请求，但是我希望您能理解这一点。

## 覆盖方法

MP 的一个关键概念是在调用原始代码之前或之后，获取一个现有的函数并用自定义行为来扩充它。但是调用最初的实现并不总是必要的，因为有时您只是想用您的自定义操作来替换它。这种方法是帮助我们解决硬编码背景颜色的理想方法。

您应用 MP 的位置需要在原始实现加载并可用之后。通常情况下，您应该努力使您的更改尽可能接近目标，但是请记住，目标的实现可能会随着时间的推移而改变。对于我们的例子，初始化和 MPs 一起将进入文件`main.js`。

查看小部件实现，我们可以看到有一个 FeedbackBox 对象作为小部件的根。稍后，toggleError 函数在其原型上实现。

```
function FeedbackBox(elem, options) {
  this.options = options;  
  this.element = elem;  
  this.isOpen = false;
}

FeedbackBox.prototype.toggleError = function(obj, isError) {
  ...
} 
```

由于 JavaScript 是一种动态语言，它的对象可以在运行时修改，所以我们最终要做的只是用我们的自定义方法替换 toggleError。唯一要记住的是保持签名(名称和传递的参数)不变。

```
FeedbackBox.prototype.toggleError = function(obj, isError) {
  if(isError) {
    obj.addClass("error");
  } else {
    obj.removeClass("error");
  }
}; 
```

新的实现现在简单地向给定元素添加了一个错误类，从而允许我们通过 css 来设置背景颜色的样式。

## 扩充方法

在前面的例子中，我们看到了如何通过提供我们自己的来覆盖原来的实现。另一方面，管理控制台日志本质上应该只是过滤掉特定的调用并抑制它们。成功的关键是检查你嵌入的代码，并试图理解它的工作流程。通常，这是通过在您选择的浏览器中启动开发人员控制台，查看加载的资源，添加断点和调试目标代码部分来实现的，以了解它的功能。不过，这一次，您需要做的只是在另一个选项卡中打开名为[vendor/jquery . feedback box . js](https://plnkr.co/edit/CgH6AQ40Zx9BrXXEXMVT?p=preview)的 Plunker 示例的实现。

通过查看调试消息，我们可以看到每个消息都以 *FeedbackBox:* 开始。因此，要达到我们的目的，一个简单的方法是截取原始调用，检查提供的要编写的文本，只有在原始方法不包含调试提示时才调用它。

为此，让我们首先将原始的`console.log`存储到一个变量中，以备后用。然后我们再次用我们的自定义实现覆盖原来的属性，首先检查提供的属性`text`是否是字符串类型，如果是，它是否包含子串`FeedbackBox:`。如果是这样，我们就什么都不做，否则我们将通过调用它的`apply`方法来执行原始的控制台代码。

注意，这个方法将上下文作为第一个参数，这意味着应该调用该方法的对象，以及一个神奇的`arguments`变量。后者是最初传递给原始控制台日志调用的所有参数的数组。

```
var originalConsoleLog = console.log;
console.log = function(text) {
  if (typeof text === "string" && text.indexOf("FeedbackBox:") === 0) {
    return;
  }

  originalConsoleLog.apply(console, arguments);
} 
```

> **注意:**你可能想知道为什么我们没有简单地转发`text`属性。Well console.log 实际上可以用无限的参数来调用，这些参数最终将连接成一个文本输出。因此，我们没有定义所有的可能性，这对于无限的可能性来说是非常困难的，而是简单地转发所有将要发生的事情。

## 拦截 Ajax 调用

最后但同样重要的是，让我们看看如何解决广告服务器的问题。让我们再来看看小部件的`init`功能:

```
$.ajax({
  url: './vendor/a-d-server.json',
  method: 'GET',
  success: function(data) {
    console.log(data);
    console.log("FeedbackBox: AdServer contacted");
  }
}); 
```

第一个想法可能是打开浏览器，搜索如何覆盖 jQuery 插件。根据你的搜索技巧，你可能会也可能不会找到合适的答案。但是让我们停下来想一想这里到底发生了什么。不管 jQuery 用它的`ajax`方法做什么，总有一天它会创建一个本地 XMLHttpRequest。

让我们看看[在引擎盖下是如何工作的](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)。在 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/Using_XMLHttpRequest) 上找到的最简单的例子向我们展示了这一点:

```
var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
  if (xhttp.readyState == 4 && xhttp.status == 200) {
      // Action to be performed when the document is read;
  }
};
xhttp.open("GET", "filename", true);
xhttp.send(); 
```

我们看到创建了一个新的`XMLHttpRequest`实例。它有一个我们实际上不关心的`onreadystatechange`方法，然后还有`open`和`send`方法。太好了。所以我们的想法是给 send 方法打个补丁，告诉它不要执行对特定 URL 的调用。

```
var originalSend = XMLHttpRequest.prototype.send;
XMLHttpRequest.prototype.send = function(data) {
  if ( URL DOES NOT POINT TO AD SERVER ) {
    return originalSend.apply(this, arguments);
  }

  return false;
}; 
```

事实证明，您无法从对象本身获得目标 URL。废话。那我们该怎么办？我们让它在对象上可用。寻找获得 URL 的第一个机会，我们可以看到`open`方法接受它作为第二个参数。为了使 URL 在对象本身上可用，让我们首先 MP open 方法。

和以前一样，我们将原始的 open 方法存储在一个变量中，以备后用。然后，我们用我们的自定义实现覆盖原始实现。因为我们喜欢使用 JavaScript 这种动态语言，所以我们可以简单地动态创建一个新属性，并将其命名为`_url`，它被设置为传入参数的值。

```
var originalOpen = XMLHttpRequest.prototype.open;
XMLHttpRequest.prototype.open = function (method, url) {
  this._url = url;
  return originalOpen.apply(this, arguments);
}; 
```

除此之外，我们调用原始的 open 方法，不再做任何事情。

重温我们的发送 MP，现在很明显如何解决条件检查。下面是修改后的版本:

```
var originalSend = XMLHttpRequest.prototype.send;
XMLHttpRequest.prototype.send = function(data) {
  if (this._url !== "./vendor/a-d-server.json") {
    return originalSend.apply(this, arguments);
  }

  return false;
}; 
```

## 结论

我们在这里看到的是使用猴子补丁来改变运行时代码行为的简短介绍。但是，更重要的是，我希望这篇文章能够让您知道应该如何处理猴子补丁的问题。虽然补丁本身通常很简单，但重要的是在运行时如何以及在哪里调整代码。

此外，我真的希望，不管您对 Monkey Patching 的感觉如何，您都有机会看到使用动态语言的美妙之处，允许您甚至在运行时动态地更改本机实现。

## 分享这篇文章