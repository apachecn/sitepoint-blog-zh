# 你可能想关注的 3 个新的 JavaScript APIs

> 原文：<https://www.sitepoint.com/3-new-javascript-apis-may-want-follow/>

如果你经常阅读 SitePoint，并且可能是我的追随者，你会意识到我已经写了很多关于 HTML5 和 JavaScript APIs 的文章。到目前为止，我已经发表了讨论 API 的文章，即使使用了 polyfill，您也可以从现在开始使用。

今天我将打破这个习惯，给你一个仍然处于非常早期阶段的一些 API 的预览。为了让您了解这些 API 有多先进，考虑一下 3 个中的 2 个是几天前才发布的。因此，坏消息是**它们现在都不能用**。然而，如果你对他们做的事情感兴趣，你可能想跟踪规范的发展，并提供你的反馈。

事不宜迟，我们开始吧！

## 网络警报 API

[Web Alarms API](https://www.w3.org/TR/web-alarms/) 提供对设备警报设置的访问，可以安排通知或在特定时间启动应用程序。这种 API 的典型用例包括应用程序，如闹钟、日历或任何其他需要在特定时间执行操作的软件。

从去年开始，这个 API 就是 W3C 的工作草案。因此，这些规范正处于成为 W3C 推荐标准的第一阶段。这个 API 是通过`window.navigator`对象的`alarms`属性公开的。`alarms`属性提供了三种方法:

*   `getAll()`:检索添加到设备中的所有报警，作为一个`Alarm`对象数组。
*   `add()`:基于`Date`对象注册报警，返回`AlarmRequest`对象。
*   `remove()`:根据唯一 ID(在应用程序源中唯一)删除先前添加的警报。

为了让您了解如何理想地调用这些方法，这里有一个添加了警报的示例(请记住，此时这段代码在任何浏览器中都不工作):

```
var alarmId;
var request = navigator.alarms.add(
    new Date("June 29, 2012 07:30:00"),
    "respectTimezone",
);

request.onsuccess = function (e) {
    alarmId = e.target.result;
};

request.onerror = function (e) {
    alert(e.target.error.name);
};
```

然后，如果您想删除相同的警报，您可以写:

```
var request = navigator.alarms.remove(alarmId);

request.onsuccess = function (e) {
    alert("alarm removed");
};

request.onerror = function (e) {
    alert(e.target.error.name);
};
```

如果你想了解更多关于 Web Alarms API 的信息，请看一下规范。

## 演示 API

[演示 API](http://webscreens.github.io/presentation-api/) 的目的是使辅助显示器(如投影仪或连接的电视)可用于网络，并考虑到使用有线(HDMI、DVI 或类似)和无线技术(MiraCast、Chromecast、DLNA、AirPlay 或类似)连接的显示器。该 API 所做的是允许在请求页面和在辅助显示器中显示的呈现页面之间交换消息。

请注意，规范不是 W3C 标准，也不在 W3C 标准轨道上。这个 API 是通过`window.navigator`对象的`presentation`属性公开的。该属性提供了一个名为`requestSession()`的方法，以及两个事件`present`和`availablechange`。`requestSession()`用于在辅助屏幕上启动或恢复演示。它返回一个 session 对象，该对象代表当前演示会话的句柄。当由传递给`requestSession()`的 url 表示的内容被加载时，呈现屏幕上的页面接收到`present`事件。最后，当第一个辅助显示器变得可用或最后一个辅助显示器被移除时，触发`availablechange`。

下面显示了一个取自规范的使用该 API 的示例:

```
<button disabled>Show</button>

<script>
var presentation = navigator.presentation,
    showButton = document.querySelector('button');

presentation.onavailablechange = function(e) {
  showButton.disabled = !e.available;
  showButton.onclick = show;
};

function show() {
  var session = presentation.requestSession('http://example.org/');

  session.onstatechange = function() {
    switch (session.state) {
      case 'connected':
        session.postMessage(/*...*/);
        session.onmessage = function() { /*...*/ };
        break;
      case 'disconnected':
        console.log('Disconnected.');
        break;
    }
  };
}
</script>
```

如果您想了解更多关于演示 API 的信息，请看一下[最终报告](http://webscreens.github.io/presentation-api/)。

## 备用 API

[待机 API](http://boiler23.github.io/screen-wake/) 允许您在顶级浏览上下文中请求屏幕唤醒锁定。这将阻止设备进入节能状态(例如，关闭屏幕)。这种能力对于一些 web 应用程序来说至关重要。例如，假设你正在驾驶汽车，在智能手机上使用基于网络的导航仪(不是本地应用程序)。如果不与屏幕交互，除非设置中另有规定，否则设备的屏幕会关闭。然而，在这种情况下，你确实希望你的屏幕保持清醒。这正是这个 API 发挥作用的地方。

备用 API 通过`window.navigator`对象的`wakeLock`属性公开。它提供了两种方法:

*   `request`:使底层平台保持屏幕处于活动状态。
*   `release`:解除唤醒锁定，屏幕不再保持唤醒状态。

这两种方法都只接受一个参数，可以是`"screen"`或`"system"`。前者用于瞄准设备屏幕，而后者瞄准其他设备资源，如 CPU 或无线电(但不是屏幕)。

下面的示例展示了如何使用这个 API 来请求屏幕保持唤醒状态:

```
navigator.wakeLock.request("screen").then(
    function successFunction() {
        // do something
    },
    function errorFunction() {
        // do something else
    }
);
```

为了允许屏幕关闭，我们可以编写以下语句:

```
navigator.wakeLock.release("display");
```

如果想了解更多关于待机 API 的内容，可以看看[非官方草案](http://boiler23.github.io/screen-wake/)。

## 结论

在本文中，我向您介绍了一些全新的 JavaScript APIs。我想再次强调，因为它们都处于非常早期的阶段，没有浏览器支持它们。因此，我们不能和他们玩。然而，作为一个新手，你有机会跟踪他们的发展，甚至为规范做出贡献。

web 开发的未来是光明的，成为其中的一部分吧！

## 分享这篇文章