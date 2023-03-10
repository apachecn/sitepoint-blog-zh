# 使用 Web 通知 API 显示动态消息

> 原文：<https://www.sitepoint.com/browser-notification-api/>

*使用 Web 通知 API 显示动态消息由 [Julian Motz](https://www.sitepoint.com/author/jmotz) 进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

![Shakespeare holding a caption card in front of a scene from Romeo and Juliet](img/714054d17f28b0cd30890d053cd2b800.png)

在我们生活的世界里，来自你最喜爱的网站或应用程序的通知不再存在于你的智能手机的范围内。现在直接从你的浏览器接收通知已经很平常了。例如，如果你有一个新的好友请求，或者有人评论了一个提到你的故事，脸书就会给你发送通知。Slack 是一款流行的消息应用，当你在对话中被提及时，它会向你发送通知。

作为一名前端开发人员，我很好奇如何为不处理大量信息流的网站利用浏览器通知。如何根据访问者对我的网站的兴趣添加与他们相关的浏览器通知？

在本文中，我们将在[简明 CSS 网站](http://concisecss.com)上实现一个通知系统，它将在框架的新版本发布时提醒访问者。我将向您展示我是如何通过使用带有浏览器[通知 API](https://developer.mozilla.org/en-US/docs/Web/API/notification) 的[本地存储](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)来实现这一点的。

![A browser notification displaying on the concisecss.com website, using the Notification API](img/1aebd8363e3eba3f59ad069c476166c5.png)

## 通知 API 的基础知识

首先，我们需要确定访问者的 web 浏览器是否支持通知。贯穿本教程的主要工作将由[通知对象](https://developer.mozilla.org/en-US/docs/Web/API/notification)完成。

```
(function() {
  if ("Notification" in window) {

  }
})(); 
```

到目前为止，我们只确定了访问者的浏览器是否支持通知。在我们建立之后，我们需要知道我们是否可以向访问者显示许可。

我们将属性的输出存储在一个变量中。如果许可被授予或拒绝，我们不返回任何内容。如果我们以前没有请求许可，我们用 [requestPermission](https://developer.mozilla.org/en-US/docs/Web/API/Notification/requestPermission) 方法来请求许可。

```
(function() {
  if ("Notification" in window) {
    var permission = Notification.permission;

    if (permission === "denied" || permission === "granted") {
      return;
    }

    Notification.requestPermission();
  }
})(); 
```

![Popup letting the user to allow or block browser notifications](img/cf415773a52c26de11693d25ca1b248e.png)

您应该会在 web 浏览器中看到类似于上图的通知。

既然我们已经请求了权限，那么让我们修改代码，以便在允许权限时显示通知:

```
(function() {
  if ("Notification" in window) {
    var permission = Notification.permission;

    if (permission === "denied" || permission === "granted") {
      return;
    }

    Notification
      .requestPermission()
      .then(function() {
        var notification = new Notification("Hello, world!");
      });
  }
})(); 
```

![JavaScript notification example](img/edf891ae247364e4131589b2308c3c4e.png)

没什么启发性，但很实用。

在这里，我们使用`requestPermission()`方法的基于承诺的语法来显示许可被授予后的通知。我们使用[通知构造器](https://developer.mozilla.org/en-US/docs/Web/API/Notification/Notification)来显示通知。这个构造函数有两个参数，一个用于通知标题，一个用于选项。按照文档的链接找到可以传递的选项的完整列表。

## 存储框架版本

在本文的前面，我提到我们将使用 localStorage 来帮助我们显示通知。使用 localStorage 是在 JavaScript 中存储持久客户端信息的首选方法。我们将创建一个名为`conciseVersion`的 localStorage 键，它将包含框架的当前版本(例如 1.0.0)。然后，我们可以使用这个密钥来检查框架的新版本。

我们如何用最新版本的框架更新我们的`conciseVersion`键的值？每当有人访问网站时，我们需要一种设置当前版本的方法。每当发布新版本时，我们还需要更新该值。每次`conciseVersion`值改变时，我们需要向访问者显示一个通知，宣布框架的新版本。

我们将通过在页面中添加一个隐藏元素来解决这个问题。这个元素将有一个名为`js-currentVersion`的类，并且只包含当前版本的框架。因为这个元素存在于 DOM 中，所以我们可以使用 JavaScript 轻松地与它交互。

这个隐藏元素将用于在我们的`conciseVersion`键中存储框架版本。每当发布新版本的框架时，我们也将使用这个元素来更新那个键。

```
<span class="js-currentVersion" aria-hidden="true">3.4.0</span> 
```

我们可以使用少量的 CSS 来隐藏这个元素:

```
[aria-hidden="true"] {
  display: none;
  visibility: hidden;
} 
```

> **注意:**因为这个元素不包含有意义的内容，所以屏幕阅读器没有必要访问这个元素。这就是为什么我将 aria-hidden 属性设置为`true`并使用`display: none`作为隐藏元素的手段。有关隐藏内容的更多信息，请参考这篇 [WebAIM 文章](http://webaim.org/techniques/css/invisiblecontent/)。

现在我们可以使用这个元素并在 JavaScript 中与之交互。我们需要编写一个函数来返回我们刚刚创建的隐藏元素中的文本。

```
function checkVersion() {
  var latestVersion = document.querySelector(".js-currentVersion").textContent;
} 
```

这个函数通过使用 [textContent](https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent) 属性来存储`.js-currentVersion`元素的内容。让我们添加另一个变量来存储我们的`conciseVersion` `localStorage`键的内容。

```
function checkVersion() {
  var latestVersion = document.querySelector(".js-currentVersion").textContent;
  var currentVersion = localStorage.getItem("conciseVersion");
} 
```

现在我们在一个变量中有了我们框架的最新版本，并且我们正在将我们的`localStorage`键存储到一个变量中。是时候添加决定是否有新版本框架可用的逻辑了。

我们首先检查`conciseVersion`键是否存在。如果没有，我们将向用户显示一个通知，因为这可能是他们第一次访问。如果密钥确实存在，我们检查它的值(存储在`currentVersion`变量中)是否大于当前版本的值(存储在`latestVersion`变量中)。如果框架的最新版本比访问者最后看到的版本更高，我们就知道新的版本已经发布了。

> **注意**:我们使用 [semver-compare](https://www.npmjs.com/package/semver-compare) 库来处理两个版本字符串的比较。

知道了这一点，我们向访问者显示一个通知，并适当地更新我们的`conciseVersion`键。

```
function checkVersion() {
  var latestVersion = document.querySelector(".js-currentVersion").textContent;
  var currentVersion = localStorage.getItem("conciseVersion");

  if (currentVersion === null || semverCompare(currentVersion, latestVersion) === -1) {      
    var notification = new Notification("Hello, world!");

    localStorage.setItem("conciseVersion", latestVersion);
  }
} 
```

要使用此功能，我们需要修改下面的权限代码。

```
(function() {
  if ("Notification" in window) {
    var permission = Notification.permission;

    if (permission === "denied") {
      return;
    } else if (permission === "granted") {
      return checkVersion();
    }

    Notification.requestPermission().then(function() {
      checkVersion();
    });
  }
})(); 
```

这允许我们显示通知，如果用户先前已经授予权限或者如果权限刚刚被授予。

## 显示通知

到目前为止，我们只向用户显示了不包含太多信息的简单通知。让我们编写一个函数，允许我们动态地创建浏览器通知，并控制通知的许多不同方面。

这个函数有正文、图标、标题以及可选链接和通知持续时间的参数。在内部，我们创建一个 options 对象来存储通知正文文本和图标。我们还创建了一个新的`Notification`对象实例，传递了通知的标题和选项对象。

接下来，如果我们想要链接到我们的通知，我们添加一个`onclick`处理程序。我们使用`setTimeout()`在指定时间后关闭通知。如果调用该函数时没有指定时间，则使用默认值 5 秒。

```
function displayNotification(body, icon, title, link, duration) {
  link = link || null; // Link is optional
  duration = duration || 5000; // Default duration is 5 seconds

  var options = {
    body: body,
    icon: icon
  };

  var n = new Notification(title, options);

  if (link) {
    n.onclick = function () {
      window.open(link);
    };
  }

  setTimeout(n.close.bind(n), duration);
} 
```

现在，让我们修改`checkVersion()`,向用户显示一个更具信息性的通知。

```
function checkVersion() {
  var latestVersion = document.querySelector(".js-currentVersion").textContent;
  var currentVersion = localStorage.getItem("conciseVersion");

  if (currentVersion === null || semverCompare(currentVersion, latestVersion) === -1) {      
    displayNotification(
      `Click to see what's new in v${latestVersion}`,
      "http://concisecss.cimg/logo.png",
      "A new version of Concise is available",
      `https://github.com/ConciseCSS/concise.css/releases/v${latestVersion}`
    );

    localStorage.setItem("conciseVersion", latestVersion);
  }
} 
```

我们使用我们的`displayNotification`函数为我们的通知提供描述、图像、标题和链接。

> **注意:**我们使用 ES6 [模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)在文本中嵌入表达式。

## 完整的代码和测试

下面你可以看到我们在本教程中编写的完整代码。

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )实现动态浏览器通知的笔[。](http://codepen.io/SitePoint/pen/QGBGPL/)