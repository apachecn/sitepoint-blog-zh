# 介绍屏幕方向 API

> 原文：<https://www.sitepoint.com/introducing-screen-orientation-api/>

每个人都以不同的方式使用网络。有人用它聊天，有人用它看电影，还有人用它玩游戏。对于那些上网在移动设备上玩游戏的人，我有一个问题:你玩过要求你旋转屏幕才能开始的游戏吗？我打赌答案是肯定的。

你必须旋转屏幕的原因是用户界面是为特定模式开发的，纵向或横向。如果你的网络应用有同样的需求，或者你正在开发这些游戏中的一个，我有一个 JavaScript API 给你:屏幕方向 API。在这篇文章中，我将讨论它是什么，它有什么好处。

## 什么是屏幕方向 API？

[屏幕方向 API](https://www.w3.org/TR/screen-orientation/) <q cite="https://www.w3.org/TR/screen-orientation/">提供读取屏幕方向状态的能力，当该状态改变时被通知，并且能够将屏幕方向锁定到特定状态。</q>换句话说，您现在可以检测用户设备的方向(纵向和横向)并锁定您需要的模式。这样做，你就不需要向你的用户展示奇怪的动画和标签来指定需要的方向。事实上，你现在可以将设备的屏幕设置在需要的方向上，这样用户就会明白如何旋转设备。

屏幕方向 API 还处于非常早期的阶段，因为它是 W3C 的工作草案。当前的规范可能在几个月后被[正在开发的更新版本](https://w3c.github.io/screen-orientation/)所取代。新版本与旧版本略有不同，因为它为其中一个方法引入了新的属性和不同的返回类型。

同样值得注意的是，要锁定屏幕，网页必须处于全屏模式(可以使用[全屏 API](https://www.sitepoint.com/use-html5-full-screen-api/) 来实现)。

现在您已经知道了什么是屏幕方向 API，让我们来了解更多关于它的属性和方法。

## 属性和事件

屏幕方向 API 用属性`orientation`和两个方法`lockOrientation()`和`unlockOrientation()`扩展了`window`的`screen`对象。

属性返回一个代表屏幕方向的字符串。它的值可以是下列值之一:

*   `portrait-primary`:方向为主纵向模式。对于智能手机，这个值意味着它处于垂直位置，按钮在底部。
*   `portrait-secondary`:方向为第二纵向模式。对于智能手机，这个值意味着它处于垂直位置，按钮在顶部(设备在下方)
*   `landscape-primary`:方向为主横向模式。对于智能手机，这个值意味着它处于水平位置，按钮在右边。
*   `landscape-secondary`:方向为第二风景模式。对于智能手机，这个值意味着它处于水平位置，按钮在左侧。

`lockOrientation()`和`unlockOrientation()`方法是互补的。第一种方法锁定设备屏幕，就好像设备在某个位置进行了物理旋转，如下图所示。如果操作成功，它返回一个布尔值`true`，否则返回`false`。

![Device Orientation API in Action](img/1d9ff959bf5e9064ff2265ccae4e5526.png)

The Screen Orientation API in action. The device is physically in portrait mode but the screen acts as if it was in landscape.

`lockOrientation()`接受一个或多个字符串来指定我们想要锁定屏幕的方向。字符串必须是前面提到的`orientation`属性的值之一，`portrait`指定方向可以是`portrait-primary`和`portrait-secondary`，或者`landscape`指示方向可以是`landscape-primary`和`landscape-secondary`。

使用此方法并传递一个字符串的代码示例如下所示:

```
window.screen.lockOrientation('portrait');
```

下面是一个传递多个字符串的示例:

```
window.screen.lockOrientation('portrait-primary', 'landscape-primary');
```

`unlockOrientation()`方法用于释放先前设置的锁定，并允许屏幕在每个位置再次旋转。

该 API 还提供了一个名为`onorientationchange`的事件，每次屏幕方向改变时都会触发该事件。我们可以使用这个事件来检测方向的变化，并相应地更新 UI 或网站的业务逻辑。

## 浏览器兼容性

对屏幕方向 API 的支持非常好，尽管有些浏览器仍然使用带前缀的版本。Firefox 18+使用其供应商前缀(`moz`)、Internet Explorer 11+使用其供应商前缀(`ms`)、Chrome 38+(目前处于测试阶段)和 Opera 25+(目前处于测试阶段)都支持该 API。不幸的是，就像许多其他真正有趣和有用的 API 一样，Safari 没有任何支持。

正如你所看到的，随着 Chrome 和 Opera 的下一个版本，你将拥有几乎所有支持这个 API 的主流浏览器。所以，你真的可以在你的下一个项目中使用它。即使没有支持，我们也学会了基于特征检测来开发我们的项目。要测试 API 是否在您的浏览器中实现，您可以编写:

```
if ('orientation' in screen) {
   // API supported, yeah!
} else {
   // API not supported :(
}
```

到目前为止，我们已经讨论了 API 公开的属性和事件以及 API 的用例。在下一节中，我们将创建一个简单的 web 页面来查看屏幕方向 API 的运行情况。

## 演示

我们将要开发的演示由一个 HTML 页面组成，该页面显示指示屏幕当前方向的文本。然后，我们有一个选择框来指定我们想要锁定屏幕的方向。最后我们有两个按钮:一个锁屏，一个解锁。

在 JavaScript 代码中，我们检测浏览器是否支持这个 API，以及它使用什么前缀，如果有的话。如果浏览器中没有实现 API，我们会显示消息“不支持 API ”,并且按钮将被禁用。如果 API 受支持，我们将一个处理程序附加到正确的事件(名称因前缀而异)，在该事件中，我们更新段落的文本以显示当前方向。

最后，我们创建另外两个处理程序。首先，我们将页面设置为全屏模式，并将屏幕锁定在选择框指定的方向。在第二个例子中，我们解除锁定并退出全屏模式。

重要提示:在开发和测试这个演示时，我在 Firefox 中发现了几个错误。当任何值被传递到`lockOrientation()`(实际上是`mozLockOrientation()`)而不是`portrait-primary`和`portrait-secondary`时，浏览器崩溃。此外，当给出`portrait-secondary`时，Firefox 的行为就好像字符串是`landscape-primary`。根据我的推文，两个错误已经被归档([https://bugzil.la/1061372](https://bugzil.la/1061372)和[https://bugzil.la/1061373](https://bugzil.la/1061373))，希望它们能很快被修复。

您可以在下面找到演示的完整代码。你也可以[在线](http://aurelio.audero.it/demo/screen-orientation-api-demo.html)玩。

```
<!DOCTYPE html>
<html>
   <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
      <meta name="author" content="Aurelio De Rosa">
      <title>Screen Orientation API Demo by Aurelio De Rosa</title>
      <style>
         *
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

         .button-demo
         {
            padding: 0.5em;
            margin: 1em;
         }

         .author
         {
            display: block;
            margin-top: 1em;
         }
      </style>
   </head>
   <body>
      <h1>Screen Orientation API</h1>

      <span id="so-unsupported" class="api-support hidden">API not supported</span>

      <div id="so-results">
         <p>
            The orientation of the device is <span id="orientation" class="value">unavailable</span>
         </p>
         <form id="form-orientation">
            <label for="orientation">Lock the device in:</label>
            <select id="orientation-type">
               <option value="portrait">portrait</option>
               <option value="landscape">landscape</option>
               <option value="portrait-primary">portrait-primary</option>
               <option value="portrait-secondary">portrait-secondary</option>
               <option value="landscape-primary">landscape-primary</option>
               <option value="landscape-secondary">landscape-secondary</option>
            </select>
            <br />
            <input class="button-demo" id="lock-button" type="submit" value="Lock!" />
            <button class="button-demo" id="unlock-button">Unlock!</button>
         </form>
      </div>

      
         Demo created by <a href="http://www.audero.it">Aurelio De Rosa</a>
         (<a href="https://twitter.com/AurelioDeRosa">@AurelioDeRosa</a>).<br />
         This demo is part of the <a href="https://github.com/AurelioDeRosa/HTML5-API-demos">HTML5 API demos repository</a>.
      

      <script>
         var prefix = 'orientation' in screen ? '' :
                      'mozOrientation' in screen ? 'moz' :
                      'msOrientation' in screen ? 'ms' :
                      null;

         if (prefix === null) {
            document.getElementById('so-unsupported').classList.remove('hidden');

            ['lock-button', 'unlock-button'].forEach(function(elementId) {
               document.getElementById(elementId).setAttribute('disabled', 'disabled');
            });
         } else {
            var form = document.getElementById('form-orientation');
            var select = document.getElementById('orientation-type');

            // Function needed to see lock in action
            function launchFullscreen(element) {
               if(element.requestFullscreen) {
                  element.requestFullscreen();
               } else if(element.mozRequestFullScreen) {
                  element.mozRequestFullScreen();
               } else if(element.webkitRequestFullscreen) {
                  element.webkitRequestFullscreen();
               } else if(element.msRequestFullscreen) {
                  element.msRequestFullscreen();
               }
            }

            function exitFullscreen() {
               if(document.exitFullscreen) {
                  document.exitFullscreen();
               } else if(document.mozCancelFullScreen) {
                  document.mozCancelFullScreen();
               } else if(document.webkitExitFullscreen) {
                  document.webkitExitFullscreen();
               } else if (document.msExitFullscreen) {
                  document.msExitFullscreen();
               }
            }

            function orientationHandler() {
               var orientationProperty = prefix + (prefix === '' ? 'o' : 'O') + 'rientation';
               document.getElementById('orientation').textContent = screen[orientationProperty];
            }

            screen.addEventListener(prefix + 'orientationchange', orientationHandler);
            document.getElementById('lock-button').addEventListener('click', function(event) {
               event.preventDefault();
               launchFullscreen(document.documentElement);

               setTimeout(function() {
                  screen[prefix + (prefix === '' ? 'l' : 'L') + 'ockOrientation'](select.value);
               }, 1);
            });
            document.getElementById('unlock-button').addEventListener('click', function() {
               exitFullscreen();
               screen[prefix + (prefix === '' ? 'u' : 'U') + 'nlockOrientation']();
            });

            orientationHandler();
         }
      </script>
   </body>
</html>
```

## 结论

在本文中，我们讨论了屏幕方向 API。这个 API 公开了一个指定屏幕方向的属性和两个方法。第一种方法允许您将屏幕锁定在给定的方向，而第二种方法可以释放锁定。最后，它提供了一个事件来知道方向何时发生变化。

正如您所看到的，使用这个 API 非常简单，在未来的项目中使用它应该不会太难。最后，浏览器之间的支持非常好，所以这真的是你可以计划采用的东西。

## 分享这篇文章