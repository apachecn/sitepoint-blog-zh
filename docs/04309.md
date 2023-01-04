# 构建一个基于 JavaScript 的移动音频播放器

> 原文：<https://www.sitepoint.com/building-mobile-javascript-powered-audio-player/>

你们中的一些人可能知道，我沉迷于 HTML5 和 JavaScript APIs。

我写过很多讨论 API 的教程，比如 [getUserMedia API](https://www.sitepoint.com/introduction-getusermedia-api/) 、 [Web Speech API](https://www.sitepoint.com/introducing-web-speech-api/) 、[屏幕定向 API](https://www.sitepoint.com/introducing-screen-orientation-api/) ，我在 GitHub 上还有一个专门的资源库[。](https://github.com/AurelioDeRosa/HTML5-API-demos)

在本文中，我将向您展示如何创建一个简单的基于 JavaScript API 的音频播放器，它使用几个 API 来改善人们使用移动设备的体验。

## 一个 JavaScript API 驱动的音频播放器

在本教程中，我将使用以下 API:

*   [环境光 API](http://modernweb.com/2014/05/27/introduction-to-the-ambient-light-api/) 根据周围环境的光照水平改变网页的主题。
*   [接近 API](https://www.sitepoint.com/introducing-proximity-api/) 根据物体的接近程度播放/暂停音频。
*   [电池状态 API](http://code.tutsplus.com/tutorials/html5-battery-status-api--mobile-22795) 检测电池电量，并在电池电量极低时自动暂停音频。
*   [Web Notifications API](https://www.sitepoint.com/introduction-web-notifications-api/) 在电池电量不足时通知用户，音频因此暂停。
*   [振动 API](http://code.tutsplus.com/tutorials/html5-vibration-api--mobile-22585) 提供触觉反馈，强化上述通知消息。

如果您需要复习这些 API 中的一个或多个，可以看看链接的文章，因为本教程假设您知道如何使用它们。

这个演示将使用原生 HTML5 `audio`元素来播放音频，没有任何库作为后备。但是，如果浏览器不支持音频`element`，则会显示一条消息。

整个应用程序的开发将考虑到逐步增强。如果浏览器不支持前面提到的一个或多个 API，应用程序将继续正常工作。唯一的区别是浏览器不会利用使用不支持的 API 的特性。

## 创建标记

这个演示是由一个带有简单标记的 HTML 页面组成的。它由实验的简短摘要和启用了本地控件的`audio`元素组成(`controls`属性)。该页面有一个`link`和一个`script`元素。前者指的是 CSS 文件(将在下一节讨论),其中包含定义三个不同主题的声明块(下一节将详细介绍)。后者指向包含实验业务逻辑的 JavaScript 文件。

如您在下面的代码中所见，`body`元素有一个预定义的`class`属性值`normal-theme`。它代表在正常光线条件下使用的默认主题。

HTML 页面的完整代码如下所示:

```
<!DOCTYPE html>
<html lang="en">
   <head>
      <meta charset="utf-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <title>Mobile Audio Player</title>
      <meta name="description" content="APIs-powered Audio Player">
      <meta name="viewport" content="width=device-width, initial-scale=1">

      <link rel="stylesheet" href="css/main.css">
   </head>
   <body class="normal-theme">
      <h1>APIs-powered Audio Player</h1>
      <p>
         This demo shows how to create a simple APIs-powered audio player. In particular this page
         uses the Proximity API, the Battery Status API, the Vibration API, the Web Notifications API,
         and the Ambient Light API.
      </p>
      <audio id="audio" src="http://freshly-ground.com/data/audio/mpc/20090119%20-%20Untitled%20Groove.mp3" controls>
         <p>I'm sorry but your browser doesn't support the <code>audio</code> element, so you can't run the demo.</p>
      </audio>

      <script src="js/main.js" async></script>
   </body>
</html>
```

## 玩家主题

实验的 CSS 文件非常简单简短。它为`body`元素定义了几个规则和三个主题:`dark-theme`、`normal-theme`和`light-theme`。这些主题中的每一个都定义了一种背景颜色和一种页面文本颜色。

在黑暗的环境中，为了避免给用户的眼睛造成压力，我们将为文本设置一个浅色的深色背景。相反，在明亮的环境中，我们将采用浅色背景和深色文本。在正常光线条件下(默认)，我们使用我们最喜欢的组合，前提是我们的用户仍然能够使用页面(例如，红色背景上的红色文本不是一个好的选择)。

完整的 CSS 代码如下:

```
body
{
   max-width: 600px;
   margin: 0 auto;
   font-size: 20px;
   padding: 0 1em;
}

.dark-theme
{
   background-color: #000000;
   color: #FFFFFF;
}

.normal-theme
{
   background-color: #B8FFF7;
   color: #C53131;
}

.light-theme
{
   background-color: #FFFFFF;
   color: #000000;
}
```

## 商业逻辑

业务逻辑是这个实验中最令人兴奋的部分。在这里，我们将介绍驱动音频播放器的代码，以及如何构建本文开头描述的功能。

我们需要执行的第一步是测试对我们计划使用的 API 的支持，并将结果存储为文字对象的属性:

```
var tests = {
   proximity: 'onuserproximity' in window,
   light: 'ondevicelight' in window,
   vibration: 'vibrate' in window.navigator,
   notification: 'Notification' in window
};
```

如您所见，电池状态 API 的测试丢失了。Firefox 实现了一个旧版本的规范，该规范不是基于承诺的，因此我们将把这个 API 单独作为一个案例来对待。在这个演示中，我想支持两个版本，因为 Firefox 是唯一一个实现了本实验中所有 API 的浏览器。我认为至少有一个浏览器能够展示演示的所有特性是很重要的。

除了`test`变量，我们还需要一个`config`变量，定义如下:

```
var config = {
   battery: {
      lowThreshold: 0.15,
      criticalThreshold: 0.05
   },
   vibration: {
      lowThreshold: [500, 200, 500],
      criticalThreshold: [1000]
   },
   notification: {
      lowThreshold: {
         tTitle: 'Battery level: low',
         message: 'Please charge your device to avoid the audio to be automatically paused.'
      },
      criticalThreshold: {
         title: 'Battery level: critical',
         message: 'The audio has been stopped to avoid the shutdown of your device.'
      }
   },
   light: {
      darkThreshold: 50,
      normalThreshold: 10000
   }
};
```

它包含我们将与 JavaScript APIs 结合使用的数据。例如，我们定义了与电池状态 API 一起使用的阈值(在`battery`属性下),以指定我们的应用程序何时认为电池电量低或严重。我们还定义了当电池电量低(`lowThreshold`)或临界(`criticalThreshold`)时使用的振动模式(在`vibration`属性下)。最后，我们定义了 Web 通知 API ( `notification`属性)和环境光 API ( `light`属性)使用的属性，以指定何时我们将认为光线水平是低的和正常的。

我们需要执行的最后一步准备工作是检索`audio`元素。这是通过以下语句实现的:

```
var audio = document.getElementById('audio');
```

至此，我们已经准备好使用 JavaScript APIs 为我们的音频播放器赋予超能力。我们要实现的第一个特性是播放/暂停音频的手势。准确地说，我们不会实现一个真正的手势。将手指、手或任何其他物体放在距离接近传感器足够近的地方就足以播放/暂停音频，但称之为“手势”听起来更好。

此功能通过以下代码实现:

```
if (tests.proximity) {
   window.addEventListener('userproximity', function (event) {
      if (event.near) {
         audio.paused ? audio.play() : audio.pause();
      }
   });
}
```

很简单，不是吗？我们可以创建的另一个简单的功能是根据环境光级别来切换应用的主题。倾听光线水平的变化，我们可以检测它是否低于定义的黑暗阈值(`darkThreshold`)、在后者和正常阈值(`normalThreshold`)之间，或者高于正常阈值。一旦完成，我们可以相应地改变主题。将此描述转换成代码会产生以下代码片段:

```
if (tests.light) {
   window.addEventListener('devicelight', function(event) {
      var light = Math.round(event.value);

      if (light < config.light.darkThreshold) {
         document.body.className = 'dark-theme';
      } else if (light < config.light.normalThreshold) {
         document.body.className = 'normal-theme';
      } else {
         document.body.className = 'light-theme';
      }
   });
}
```

现在我们已经有了检测光线水平变化和检测播放/暂停音频的“手势”的代码，我们必须实现与电池水平相关的功能。为此，我们必须为`levelchange`事件附加一个处理程序，并在应用程序启动时运行相同的处理程序。这样，如果应用程序启动时电池电量处于低水平或临界状态，我们将能够采取相应的行动。为此，我们将定义一个`manageBattery()`函数。我们还将检测浏览器支持的电池状态 API 的版本，以了解我们是否可以直接附加处理程序，或者何时解决承诺。

生成的代码如下所示:

```
function manageBattery(battery) {
   // Code here...
}

if (window.navigator.getBattery) {
   window.navigator.getBattery().then(function(battery){
      battery.addEventListener('levelchange', manageBattery.bind(window, battery));
      manageBattery(battery);
   });
} else if (window.navigator.battery) {
   window.navigator.battery.addEventListener('levelchange', manageBattery.bind(window, window.navigator.battery));
   manageBattery(window.navigator.battery);
}
```

要执行的最后一步是创建`manageBattery()`函数的主体。在这个函数中，我们必须执行以下操作:

1.  检测电池电量(好、低或临界)
2.  如果电池电量不足，暂停音频
3.  根据电池电量(低或临界)使用不同的模式振动设备
4.  根据电池电量显示不同的电池状态通知消息(电量低或严重)

基于该列表，生成的代码如下:

```
function manageBattery(battery) {
   if(!battery.charging && audio.duration > 0 && !audio.paused) {
      if (battery.level > config.battery.lowThreshold) {
         return;
      }

      var isCritical = battery.level <= config.battery.criticalThreshold;
      if (isCritical) {
         audio.pause();
      }

      if (tests.vibration) {
         window.navigator.vibrate(
            isCritical ? config.vibration.criticalThreshold : config.vibration.lowThreshold
         );
      }

      if (tests.notification) {
         Notification.requestPermission(function(permission) {
            if (permission !== 'denied') {
               new Notification(
                  isCritical ?  config.notification.criticalThreshold.title : config.notification.lowThreshold.title,
                  {
                     body: isCritical ?
                        config.notification.criticalThreshold.message :
                        config.notification.lowThreshold.message
                  }
               );
            }
         });
      }
   }
}
```

有了这最后一个片段，我们就完成了我们的演示，现在可以开始测试了。

## 代码和现场演示

这个实验的完整和工作代码可以在 [GitHub](https://github.com/sitepoint-editors/JavaScript-APIs-powered-audio-player) 上找到。如果你想看看我们开发的代码的运行情况，一个[现场演示](http://htmlpreview.github.io/?https://github.com/AurelioDeRosa/JavaScript-APIs-powered-audio-player/blob/master/index.html)也是可用的。

## 结论

在本教程中，我们开发了一个简单但功能强大的音频播放器，它使用了几个新的 JavaScript APIs。通过这个实验，我证明了使用 JavaScript APIs，您可以创建功能强大的应用程序，这些应用程序具有以移动设备为中心的特性，并且可以改善您的用户体验。我希望你喜欢它，你发现这个演示很有趣。

## 分享这篇文章