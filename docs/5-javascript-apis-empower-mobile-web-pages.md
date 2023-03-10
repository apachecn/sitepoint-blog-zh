# 5 个 JavaScript APIs 支持您的移动网页

> 原文：<https://www.sitepoint.com/5-javascript-apis-empower-mobile-web-pages/>

像这样的陈述

> “移动市场正在增长”

和

> “人们通过移动设备(智能手机、平板电脑等)访问网络。)将超过那些通过台式机或笔记本电脑访问它的人”

不要再打动了。

今天，我们都意识到，至少我们应该意识到，在为网络开发任何东西时，移动市场都是非常重要的。

多年来，关于本地应用程序和 web 应用程序以及哪一个是最好的，一直存在着讨论和不同的观点。不管你的观点如何，过去的事实是，原生移动应用程序允许访问网页无法访问的硬件组件。但是这种差距在今天仍然有效吗？web 技术是否已经发展到我们作为开发人员可以只用 HTML、CSS 和 JavaScript 编码的程度？

在本文中，我将介绍一些 JavaScript APIs，它们允许您的 web 页面访问移动设备的硬件组件，或者支持您的 web 应用程序在移动设备上运行。

## 电池状态 API

[电池状态 API](https://www.w3.org/TR/battery-status/) 提供关于系统电池电量或状态的信息。多亏了这个 API，你可以知道电池是否正在充电，电池还有多长时间会完全放电，或者仅仅是它的电流水平。这些细节可以通过属于`window.navigator.battery`对象的四个属性来访问。该 API 还定义了当上述属性发生变化时可以触发的事件。

这个 API 在一些情况下很有用，比如您(或您的用户)在公共汽车上使用 web 应用程序努力处理一个文档，但忘记保存您所做的更改。突然，你的智能手机关机了，你发疯了，因为你失去了大量的时间和所有的工作。由于这个 API，我们可以开发能够检测电池当前水平的页面，并更频繁地保存更改，以防止数据丢失，以防电池电量低或严重。

在撰写本文时，电池状态 API 仅受 Firefox 支持，但检测对此 API 的支持很容易，如下所示:

```
if (window.navigator && window.navigator.battery) {
   // API supported
} else {
   // Not supported
}
```

下面是使用此 API 的一个简单示例:

```
// Print if battery is charging or not
console.log("The battery is " + (navigator.battery.charging ? "" : "not") + " charging");
```

如果你想尝试这个 API，[我们为你准备了一个演示](https://blogs.sitepointstatic.com/examples/tech/battery-api/index.html)。如果你想进一步调查，[我们已经在 SitePoint 这里覆盖了电池状态 API](https://www.sitepoint.com/html5-battery-status-api/) 。

## Web 通知 API

在移动设备上，我们熟悉通知的概念，它们是由我们安装在设备上的许多应用程序实现的。在网络上，网站以不同的方式实现它们。想想 Google+和 Twitter，它们都有通知机制，但实现方式不同。

[Web 通知 API](https://www.w3.org/TR/notifications/) 就是为了这个目的而创建的 API，用来标准化开发者通知用户的方式。通知允许警告网页上下文之外的用户事件，例如电子邮件的递送。虽然开发人员创建通知的方式是相同的，但是规范并没有描述 UI 应该如何以及在哪里显示它们。这意味着我们将在不同的浏览器上看到不同的风格。例如，在移动设备上，我们可能会在通知栏中看到它们。

Web 通知 API 通过`window`对象的`Notification`属性公开。它是一个允许我们创建通知实例的构造函数。要创建新的通知，我们可以编写如下代码:

```
var notification = new Notification('Email received', {
  body: 'You received an email. Read it. Now!'
});
```

目前，Chrome、Firefox 和 Safari 都支持该 API。支持 Web 通知 API 的移动浏览器有 Firefox、Android 4.4+和 Blackberry。你看到什么奇怪的东西了吗？Chrome mobile 不支持 API！悲伤却真实。

由于支持该 API 的浏览器，超过一半的市场被覆盖，但因为我们想确保我们的 JavaScript 代码不试图调用不支持的方法，我们必须测试支持。我们可以使用下面的代码片段来实现:

```
if ('Notification' in window) {
  // API supported
} else {
  // Not supported
}
```

被这个 API 激动了？太好了！你可以在文章“[Web 通知 API 简介](https://www.sitepoint.com/introduction-web-notifications-api/)”中了解更多信息，也可以在[中体验一下现场演示](http://aurelio.audero.it/demo/web-notifications-api-demo.html)。

## 邻近 API

[Proximity API](https://www.w3.org/TR/proximity/) 是一个 JavaScript API，我们可以用它来检测一个对象与运行网页的设备的接近程度。如果您的设备有近程传感器，则距离由近程传感器测量。Proximity API 不提供属性或方法，只有两个事件在`window`对象上触发。我们可以监听它们来执行操作。第一个事件`deviceproximity`提供设备和附近物体之间的实际距离信息，而第二个事件`userproximity`仅指定附近是否有物体。

唯一支持这个 API 的浏览器是 Firefox，从版本 15 开始，在桌面和移动上都支持。不幸的是，由于许多笔记本电脑和台式机没有接近传感器，我们不得不主要针对移动设备。

要检测对此 API 的支持:

```
if ('ondeviceproximity' in window) {
   // API supported
} else {
   // Not supported
}
```

下面是一个简单的使用示例:

```
window.addEventListener('userproximity', function(event) {
   console.log( (event.near ? '' : 'no ') + 'object detected nearby');
});
```

如果你想了解更多关于 Proximity API 的内容，我写了一篇名为“[介绍 Proximity API](https://www.sitepoint.com/introducing-proximity-api/) ”的文章。如果你想看它的运行，你可以[玩这个演示](http://aurelio.audero.it/demo/proximity-api-demo.html)。

## 振动 API

[振动 API](https://www.w3.org/TR/vibration/) 是一个非常简单的 API，由一个方法组成，该方法赋予我们振动设备的能力。一个明显的用途是在游戏中，我们可以重现十年前由一些游戏机引入的效果。然而，这并不是这个 API 唯一可能的用途。

正如我提到的，振动 API 只公开了一个名为`vibrate()`的方法。后者属于`window.navigator`对象，以最简单的形式接受一个整数，指定设备应该振动的毫秒数。

除了 Internet Explorer 和 Safari 之外，所有主流浏览器都支持该 API。尽管如此，这可能是在你的下一个项目中使用它的正确时机。事实上，如果它被支持，你将为你的用户提供更好的体验(当然，除非你滥用这个特性)。检测支持非常简单，如下所示:

```
if (window.navigator && window.navigator.vibrate) {
   // API supported
} else {
   // Not supported
}
```

API 的一个非常简单的用法如下:

```
// Vibrate for three seconds
navigator.vibrate(3000);
```

要了解关于这个 API 的更多信息，请阅读文章“[如何使用 HTML5 振动 API](https://www.sitepoint.com/use-html5-vibration-api/) ”，并且不要忘记[进行演示](https://blogs.sitepointstatic.com/examples/tech/vibration-api/index.html)。

## 设备定位 API

我要讨论的最后一个 API 是[设备定向 API](https://www.w3.org/TR/orientation-event/) 。从导航应用到游戏，检测设备的方向在很多情况下都很有用。这个 API 定义了几个事件，这些事件提供了关于设备的物理方向和运动的信息。这个 API 是 W3C 的工作草案，这意味着规范是不稳定的，我们可能会期待未来的一些变化。

API 公开了以下三个事件`deviceorientation`、`devicemotion`和`compassneedscalibration`。当加速度计检测到设备方向发生变化时，第一个触发。第二个是每次设备加速或减速时触发。当用户代理确定指南针需要校准时，触发最后一个事件。

几乎所有主流浏览器都支持这个 API(除了 Safari ),但是这种支持是部分的或者不一致的。例如，在撰写本文时，很少有浏览器支持`compassneedscalibration`事件。因此，我的建议是对这些事件进行测试，以了解它们是否受支持。要测试`deviceorientation`事件的存在，您可以编写:

```
if (window.DeviceOrientationEvent) {
   // Event supported
} else {
   // Not supported
}
```

或者:

```
if ('ondeviceorientation' in window) {
   // Event supported
} else {
   // Not supported
}
```

例如，如果您想要测试`devicemotion`事件，您可以编写:

```
if (window.DeviceMotionEvent) {
   // Event supported
} else {
   // Not supported
}
```

如果你想玩这个 API [，我们有一个演示，你可以使用](http://aurelio.audero.it/demo/device-orientation-api-demo.html)。如果你想研究它，我们有文章“[在 HTML5](https://www.sitepoint.com/using-device-orientation-html5/) 中使用设备定向”。

## 结论

在本文中，我向您展示了一些 API，它们可以增强您的 web 页面对移动访问者的支持。

这些 API 的用例是无穷无尽的，这完全取决于你的幻想和你正在开发的应用程序或网站的类型。我希望你喜欢这篇文章，让我知道任何其他你认为有用的 API。

## 分享这篇文章