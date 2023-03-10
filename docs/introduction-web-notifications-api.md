# Web 通知 API 简介

> 原文：<https://www.sitepoint.com/introduction-web-notifications-api/>

对于很多人来说，56kb 连接的时间已经淡出了记忆(移动用户除外)。那是一个浏览器可以打开一个页面就足够了的时代，甚至没有人可以预测多标签浏览的未来。今天，我们被社交网络、帖子、回复、评论、照片、视频等等淹没了。已经创建了几个系统来克服查看每一个标签来检查新活动的需要。其中一种方法是通知，它在不同的网站上非常普遍。

直到不久前，每个开发人员都必须自己实现通知，导致了一整套不同的解决方案。W3C 已经定义了一个 API 来以标准的方式解决这个问题，称为 [Web 通知 API](https://www.w3.org/TR/notifications/) 。

在移动设备上，我们非常熟悉通知的概念。试着离线几个小时，当你连接到互联网时，你会被安装在你的设备上的不同应用程序的通知淹没。如果我们的网页能够在桌面和移动设备上无缝运行，那就太好了。如引言中所述，这样的系统已经被开发和标准化，所以今天我们有了 [Web 通知 API](https://www.w3.org/TR/notifications/) 。

![Notification Hell](img/f17b703ef16f4af3d75ccde813f0cb93.png)
形象恬不知耻地从布拉德·弗罗斯特的陈述中窃取，[死亡扯淡](http://www.slideshare.net/bradfrostweb/death-to-bullshit-now-with-80-more-bullshit)

## 什么是 Web 通知 API

Web 通知 API 被定义为<q cite="https://www.w3.org/TR/notifications/">一个用于终端用户通知的 API。通知允许向网页环境之外的用户警告某个事件，例如电子邮件的发送。</q>规范没有描述 UA 应该如何以及在哪里显示这些通知。因此，我们将在不同的浏览器上看到不同的样式，并且基于我们使用的设备，我们将在不同的地方看到通知(例如，在移动设备上，我们可能会在通知栏中看到它们)。值得注意的是，在显示通知之前，浏览器会向用户显示一个警告，用户必须明确表示同意。

![An example of a notification using the Web Notifications API on Firefox](img/a1e0f47106a4b9b0867c85729096a8a5.png)
在 Firefox 上使用 Web 通知 API 的通知示例

这个 API 已经存在了一段时间，以至于规范已经改变了几次。该规范有两个主要版本。第一个版本是在 Chrome 和 Firefox 的旧版本中实现的。现在，该规范看起来很稳定，尽管截至 2014 年 4 月 19 日，它仍然是 W3C 的工作草案。

思考这种 API 的一些用例并不困难。例如，您可能希望一收到电子邮件就收到通知。如果有人在推特上提到你，或者在脸书或 Google+上发布你的照片，你可能也想得到通知。

现在我们知道了这个 API 是什么，它有什么用，让我们深入研究它的方法、属性和事件的描述。

## 方法、属性和事件

Web 通知 API 通过`window`对象的`Notification`属性公开。这是一个允许我们创建通知实例的构造函数。它接受两个参数——一个包含通知标题的字符串和一个可选的设置对象。在了解如何创建实例之前，我们先来看看可以指定的设置:

*   `body`:用于进一步指定通知目的的字符串。
*   `lang`:指定通知的语言。其值必须符合 [BCP 47 标准](http://tools.ietf.org/html/bcp47)。有效字符串的例子有`en-US`和`it-IT`。
*   `dir`:定义消息文本的方向。它的值可以是`auto`表示方向基于浏览器的设置，`ltr`指定从左到右的方向(对于欧洲语言)，或者`rtl`指定从右到左的方向(对于某些亚洲语言)。
*   `tag`:用作 ID 的字符串，可用于检索、替换或删除通知。
*   `icon`:指定将用作通知图标的图像的 URL。

要创建一个`Notification`对象的实例，我们应该编写如下语句:

```
var notification = new Notification('Email received', {
  body: 'You have a total of 3 unread emails'
});
```

很简单，不是吗？

原来我们刚刚学习的设置也可以作为通知实例的只读属性。此外，`Notification`对象公开了一个名为`permission`的属性。`permission`包含代表显示通知的当前权限的字符串。其值可以是下列值之一:`denied`，表示用户已经拒绝通知；`granted`，表示用户已经给予许可；或者`default`，表示用户选择未知。

这个 API 公开了两个方法:`requestPermission()`和`close()`。顾名思义，前者用于请求向用户显示通知的权限，而后者以编程方式关闭通知。`requestPermission()`是`Notification`对象的一个方法，它接受一个可选的回调，当用户接受或拒绝权限时，这个回调就会被执行。选择作为参数传递给回调，值可以是`granted`、`denied`或`default`。
`close()`是实例方法，不接受任何参数。

有时，我们可能需要在通知状态改变时立即执行操作。例如，我们可能想知道用户是否点击了通知，或者通知是何时关闭的。为此，我们可以为公开的四个事件之一附加一个处理程序:

*   `onclick`:当用户点击通知时触发。
*   `onclose`:用户或浏览器关闭通知时触发。
*   `onerror`:通知出错时触发。
*   `onshow`:显示通知时触发。

使用这些事件的基本示例如下所示。

```
var notification = new Notification('Email received', {
  body: 'You have a total of 3 unread emails'
});

notification.onshow = function() {
  console.log('Notification shown');
};
```

## 浏览器兼容性

对 Web 通知 API 的支持在桌面和移动上都不是很好。在桌面上，Chrome 和 Firefox 不久前实现了这个 API 的第一个版本。然而，仅考虑 API 的新版本，实现从 Chrome 22 和 Firefox 22 开始(两者都没有厂商前缀)。Safari 6+还支持 Web 通知 API。在移动方面，只有 Firefox 和 Blackberry 提供全面支持。测试一个浏览器是否支持这个 API 就是写一个如下所示的检查。

```
if ('Notification' in window) {
  // API supported
} else {
  // API not supported
}
```

## 演示

在这一节中，我将向您展示一个简单的演示，让您看到这个 API 的实际应用。演示由两部分组成。在第一部分中，有一个表单让您编写一些将在显示的通知中使用的文本。在第二部分中，一个按钮允许您使用我在演示中包含的预定义参数快速查看这个 API 做了什么。这两个请求都将触发对用户的许可请求。一旦获得许可，通知将出现在桌面的屏幕上，在移动设备的通知栏中。

在演示的脚本部分，我们做的第一件事是测试浏览器，看看它是否支持 Web 通知 API。如果不支持 API，我们会显示消息“不支持 API”，并禁用触发通知的两个按钮。如果浏览器实现了这个 API，我们就给按钮的`click`事件附加一个监听器。从代码中可以看出，处理程序是相同的，不同的标题和显示的消息根据触发事件的元素而有所不同:

```
if (event.target.id === 'button-wn-show-preset') {
  // Uses the preset parameters
} else {
  // Uses the custom parameters
}
```

除了显示通知之外，我们还为通知实例公开的四个事件分别附加了一个处理程序。这个处理程序除了在一个特定的`textform`元素中记录触发的事件和触发它的按钮之外，什么也不做。

以下代码的现场演示可从[这里](http://aurelio.audero.it/demo/web-notifications-api-demo.html)获得。

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>Web Notifications API Demo</title>
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

      .hidden
      {
        display: none;
      }

      #custom-notification
      {
        margin-top: 1em;
      }

      label
      {
        display: block;
      }

      input[name="title"],
      textarea
      {
        width: 100%;
      }

      input[name="title"]
      {
        height: 2em;
      }

      textarea
      {
        height: 5em;
      }

      .buttons-wrapper
      {
        text-align: center;
      }

      .button-demo
      {
        padding: 0.5em;
        margin: 1em;
      }

      #log
      {
        height: 200px;
        width: 100%;
        overflow-y: scroll;
        border: 1px solid #333333;
        line-height: 1.3em;
      }

      .author
      {
        display: block;
        margin-top: 1em;
      }
    </style>
  </head>
  <body>
    <h1>Web Notifications API</h1>
    <span id="wn-unsupported" class="hidden">API not supported</span>

    <form id="custom-notification" action="">
      <label for="title">Title:</label>
      <input type="text" id="title" name="title" />

      <label for="body">Body:</label>
      <textarea id="body" name="body"></textarea>

      <div class="buttons-wrapper">
        <button id="button-wn-show-preset" class="button-demo">Show Preset Notification</button>
        <input type="submit" id="button-wn-show-custom" class="button-demo" value="Show Custom Notification" />
      </div>
    </form>

    <h3>Log</h3>
    <div id="log"></div>
    <button id="clear-log" class="button-demo">Clear log</button>

    <script>
      if (!('Notification' in window)) {
        document.getElementById('wn-unsupported').classList.remove('hidden');
        document.getElementById('button-wn-show-preset').setAttribute('disabled', 'disabled');
        document.getElementById('button-wn-show-custom').setAttribute('disabled', 'disabled');
      } else {
        var log = document.getElementById('log');
        var notificationEvents = ['onclick', 'onshow', 'onerror', 'onclose'];

        function notifyUser(event) {
          var title;
          var options;

          event.preventDefault();

          if (event.target.id === 'button-wn-show-preset') {
            title = 'Email received';
            options = {
              body: 'You have a total of 3 unread emails',
              tag: 'preset',
              icon: 'http://www.audero.it/favicon.ico'
            };
          } else {
            title = document.getElementById('title').value;
            options = {
              body: document.getElementById('body').value,
              tag: 'custom'
            };
          }

          Notification.requestPermission(function() {
            var notification = new Notification(title, options);

            notificationEvents.forEach(function(eventName) {
              notification[eventName] = function(event) {
                log.innerHTML = 'Event "' + event.type + '" triggered for notification "' + notification.tag + '"<br />' + log.innerHTML;
              };
            });
          });
        }

        document.getElementById('button-wn-show-preset').addEventListener('click', notifyUser);
        document.getElementById('button-wn-show-custom').addEventListener('click', notifyUser);
        document.getElementById('clear-log').addEventListener('click', function() {
          log.innerHTML = '';
        });
      }
    </script>
  </body>
</html>
```

## 结论

在本文中，我们讨论了 Web 通知 API 及其用例。我们还描述了 API 公开的方法、属性和事件。正如我们所看到的，使用这个 API 非常容易，所以在您的下一个项目中使用它应该不难。

不幸的是，它在浏览器中的支持不是很好，但因为 Chrome、Firefox 和 Safari 实现了它，平均来说我们应该能够覆盖很大一部分用户。为了巩固这些概念，不要忘记使用提供的演示，并且可能尝试更改代码。

## 分享这篇文章