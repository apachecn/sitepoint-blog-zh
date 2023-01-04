# 介绍邻近 API

> 原文：<https://www.sitepoint.com/introducing-proximity-api/>

智能手机和移动设备在我们的生活中变得越来越重要，似乎我们越来越离不开它们。

如果使用得当，适度的话，它们是有价值的工具，可以帮助我们完成一些普通的任务，比如知道下一班车，找到最近的地铁站，寻找意大利餐馆等等。

他们也为网络开发者打开了一个全新的世界。事实上，由于使用移动设备的需求增加，专门为移动设备创建的一组新 API 已经标准化。

在过去的几个月里，我已经向您介绍了几个 API，比如 [Web 通知 API](https://www.sitepoint.com/introduction-web-notifications-api/) 和 [Web 语音 API](https://www.sitepoint.com/introducing-web-speech-api/) 。在本文中，我将描述一个简单而有用的 API，称为 Proximity API。

## 介绍

[接近 API](https://www.w3.org/TR/proximity/) 定义了提供设备和物体之间距离信息的事件，由接近传感器测量。这个 API 最初是传感器 API 的一部分，后来拆分成为一个独立的 API。Proximity API 的规范被认为是稳定的，因为它在 2013 年 10 月 1 日达到了 W3C 候选推荐标准的状态。

如果您曾经拥有或使用过智能手机，我想您已经拥有或使用过了，那么您已经看到了这个 API 的实际应用。

*想要一个例子吗？*想想你的最后一个电话和你做了什么。你解锁你的智能手机，输入你想打电话的号码，然后点击“通话”按钮。完成后，你将智能手机放在耳边，突然神奇的事情发生了，屏幕关闭了。

这是许多智能手机为保护电池而执行的一项操作，但推动这项功能的是邻近 API。当你将移动设备放在离耳朵足够近的地方时，接近传感器会触发一个事件，智能手机会听到这个事件，然后关闭屏幕。

在网络上，我们有其他有趣的使用案例。你是否曾经一边开车一边用网络服务听音乐，然后不得不停下来？

拿走你的设备然后手动停止播放器有多痛苦？我猜答案是“很多！”。多亏了这个 API，web 开发人员现在可以添加一个功能，这样如果一个物体(在这个例子中是手)靠近设备，玩家就会暂停。

现在我们知道了什么是 Proximity API 及其用例，我们可以深入研究它所公开的事件。

## 事件

[Proximity API](https://www.w3.org/TR/proximity/) 定义了两个事件，我们可以根据物体的接近程度来监听和做出反应。第一个事件是`deviceproximity`，它提供了主机设备和附近物体之间的距离信息。第二个事件是`userproximity`，指定设备是否感应到附近的物体。两者都在`window`对象上触发，所以为了监听它们，我们给它附加了一个处理程序。

如何为`deviceproximity`事件附加一个处理程序的例子如下:

```
window.addEventListener('deviceproximity', function(event) {
   console.log('An object is ' + event.value + ' centimeters far away');
});
```

附加的处理程序接收一个对象作为它的第一个参数，包含我们需要的信息。由`deviceproximity`事件传递的对象提供了三个属性:`value`、`min`和`max`。`value`是一个数字，以厘米为单位表示设备与物体的接近程度。`min`和`max`属性描述了传感器可以探测的最小和最大距离，单位为厘米。由`userproximity`事件传递的对象公开了`near`属性。它是一个布尔值，指定一个对象是否离设备足够近(`true`)或不够近(`false`)。在这种情况下*足够近*意味着物体在特定设备的可检测范围内。

对这个 API 感到兴奋？没那么快…

## 浏览器兼容性

目前对 Proximity API 的支持度非常低。唯一支持它的浏览器是火狐，无论是桌面还是移动，[从 15 版](https://developer.mozilla.org/en-US/Firefox/Releases/15)开始。考虑到它已经达到了 W3C 候选推荐的状态，这是很奇怪的，但这就是我们现在所拥有的。

因为这个 API 只在 Firefox 中实现，所以知道如何测试它的支持是至关重要的。我们可以使用一个众所周知的方法来做到这一点，您可能在处理其他 API 时遇到过这个方法。该方法如下所示:

```
if ('ondeviceproximity' in window) {
   // API supported. Don't get too close, I can feel you
} else {
   // API not supported
}
```

如上图，我们可以测试一下`userproximity`事件的支持度。至此，我们知道了什么是 Proximity API，以及它公开了什么事件。为了完成我们的旅程，我们将开发一个简单的演示来查看它的运行情况。

## 演示

我们将在本节中构建的演示将一个处理程序附加到`deviceproximity`和`userproximity`事件，并在屏幕上显示返回值。为了显示这些值，我们将使用一个无序列表。此外，我们将在页面的开头放置两个`span`标签，以指定给定的事件是否不受支持。默认情况下，它们是隐藏的，但是如果浏览器不支持某个事件，它们就会显示出来。

JavaScript 代码也很简单。首先，我们测试浏览器，看它是否支持邻近 API。因为后者由两个独立的事件组成，我们将一次测试一次。如果给定的事件不被支持，我们使用相关的向用户显示相关的消息。否则，我们会附加一个处理程序，以便我们可以检索并显示返回值。

下面是演示的源代码，不过你也可以用一个[现场演示](http://aurelio.audero.it/demo/proximity-api-demo.html)来玩。这个 API 是我的 [HTML5 API 演示库](https://github.com/AurelioDeRosa/HTML5-API-demos)的一部分，这是一个允许你使用 HTML5 和相关技术引入的几十个 API 的演示集合。

```
<!DOCTYPE html>
<html>
   <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
      <meta name="author" content="Aurelio De Rosa">
      <title>Proximity API Demo by Aurelio De Rosa</title>
      <style> *
         {
            -webkit-box-sizing: border-box;
            -moz-box-sizing: border-box;
            box-sizing: border-box;
         }

         body
         {
            max-width: 500px;
            margin: 2em auto;
            padding: 0 0.5em;
            font-size: 20px;
         }

         h1
         {
            text-align: center;
         }

         .api-support
         {
            display: block;
         }

         .hidden
         {
            display: none;
         }

         .value
         {
            font-weight: bold;
         }

         .author
         {
            display: block;
            margin-top: 1em;
         } </style>
   </head>
   <body>
      <h1>Proximity API</h1>

      <span id="dp-unsupported" class="api-support hidden">deviceproximity event not supported</span>
      <span id="up-unsupported" class="api-support hidden">userproximity event not supported</span>

      <ul>
         <li>
            An object is at a distance of <span id="dp-value" class="value">null</span> centimeters
            (within a detectable range of <span id="dp-min" class="value">null</span> -
            <span id="dp-max" class="value">null</span> centimeters).
         </li>
         <li>
            Object close to the device? <span id="up-value" class="value">unavailable</span>
         </li>
      </ul>

      
         Demo created by <a href="http://www.audero.it">Aurelio De Rosa</a>
         (<a href="https://twitter.com/AurelioDeRosa">@AurelioDeRosa</a>).<br />
         This demo is part of the <a href="https://github.com/AurelioDeRosa/HTML5-API-demos">HTML5 API demos repository</a>.
      

      <script> if (!('ondeviceproximity' in window)) {
            document.getElementById('dp-unsupported').classList.remove('hidden');
         } else {
            var proximityValue = document.getElementById('dp-value');
            var proximityMax = document.getElementById('dp-max');
            var proximityMin = document.getElementById('dp-min');

            window.addEventListener('deviceproximity', function(event) {
               proximityValue.innerHTML = event.value;
               proximityMax.innerHTML = event.max;
               proximityMin.innerHTML = event.min;
            });
         }

         if (!('onuserproximity' in window)) {
            document.getElementById('up-unsupported').classList.remove('hidden');
         } else {
            var inProximity = document.getElementById('up-value');

            window.addEventListener('userproximity', function(event) {
               inProximity.innerHTML = event.near;
            });
         } </script>
   </body>
</html>
```

## 结论

在本文中，我向您介绍了 Proximity API。我们已经看到了这个 API 的作用及其用例。我们已经讨论了所提供的事件，以及如何使用它们根据设备附近对象的存在来调整 web 应用程序的行为。不幸的是，这个 API 只有 Firefox 支持，所以现在还不是使用它的时候。

你觉得这个 API 怎么样？你觉得有用吗？你会在下一个项目中使用它吗？

## 分享这篇文章