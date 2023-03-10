# 手指一挥，控制你的安卓手机

> 原文：<https://www.sitepoint.com/controlling-android-phone-wave-finger/>

这些天来，我对乐趣的定义包括摆弄不同的技术，看看我如何让它们互相利用。就像一个疯狂的指挥家疯狂地挥舞着手臂让管弦乐队一起演奏一样，我疯狂地打字，直到我脑海中的一个想法最终奏效。

在本文中，我将向您展示我如何创建一个 Android 呼叫控制器，它可以通过手指的旋转来静音来电铃声。我们将使用以下技术:

*   安卓设备
*   一个[跳跃运动控制器](https://www.leapmotion.com/)
*   [于{X}日](http://onx.ms/)
*   [Node.js](http://nodejs.org/)
*   [快递](http://expressjs.com/)
*   一点 [jQuery](http://jquery.com/)
*   [Heroku](https://www.heroku.com/)

上述技术构成了拼图的三个重要部分:

*   **节点服务器**–我们将运行一个节点服务器，它是 Android 设备和一个非常简单的网页之间的桥梁。它会跟踪我们是否希望手机静音。
*   **on { X }**–当我们接到来电时，我们会让手机轮询我们的服务器，看看是否有静音请求。如果是这样，它将自己静音并发送一条忙音。
*   **带有 Leap Motion 控制器的网页**–当我们打开网页，用手指在 Leap Motion 上画圈时，我们会向服务器发送静音请求。

## 什么是飞跃运动控制器？

[Leap 运动控制器](https://www.leapmotion.com/)是一个有趣的输入设备，可以检测你的手和手指的运动。这是一项革命性的技术，只是在等待一些不可思议的开发人员出现，并创造出纯粹的奇迹。我们将使用来自 Leap Motion 团队的 [LeapJS API](http://js.leapmotion.com/) 来控制 JavaScript 应用程序。

## 于{X}

我发现 JavaScript 的一个重要优势是，你可以很容易地使用这种语言来配对完全不相关的设备。在本演示中，我们将在{X} 上将 Leap Motion 与[配对。on{X}是一款带有 JavaScript API 的 Android 应用程序，允许您控制和/或响应 Android 设备上的事件。如果你以前没有在{X}上使用过，我已经在我的文章](http://onx.ms/)[“在{X}上用 JavaScript 控制网页”](https://www.sitepoint.com/controlling-web-pages-javascript-onx/)中介绍了它的基础知识。查看一下快速纲要。

## Node.js 服务器

`index.js`文件包含了我们所有的服务器端节点应用程序代码。我们网页的所有代码都在公共文件夹中。这是我们的目标目录结构:

![Project File Structure](img/43676d89522e5878a488bb74dbef8e6f.png)

对于这个演示，您的`package.json`文件(如下所示)中没有太多需要声明的内容。我们将使用它来声明我们的服务器的依赖性。在这种情况下，我们的服务器的主要依赖项是 Express。

当你将应用程序部署到 Heroku 这样的主机服务上时，最好包含`engines`字段。他们会指出您希望使用哪个版本的 Node 和 npm。

```
{
  "name": "LeapCallController",
  "version": "0.0.1",
  "dependencies": {
    "express": "3.1.x"
  },
  "engines": {
    "node": "0.10.x",
    "npm": "1.2.x"
  }
}
```

要在 Heroku 上运行我们的应用程序，我们需要一个`Procfile`，其内容如下所示。

```
web: node index.js
```

### 服务器代码

节点服务器是我们带有 Leap Motion 控制器的网页和我们的 Android 设备之间的桥梁。服务器代码如下所示。

```
var http = require('http'),
    express = require('express'),
    app = express(),
    server = require('http').createServer(app),
    port = process.env.PORT || 5000,
    call = {};
    call.sound = true;

app.use(express.bodyParser());

app.get('/', function(request, response) {
  response.sendfile('public/index.html');
});

app.post('/shouldibesilent', function(request, response) {
  console.log('That phone wants to know if it should be silent...', request);
  response.json({callSound: call.sound});
});

app.post('/call', function(request, response) {
  console.log('Something is setting the call to ' + request.body.action);

  switch (request.body.action) {
    case 'mute':
      call.sound = false;
      break;
    case 'reset':
      call.sound = true;
      break;
  }

  response.json({success: true, actionReceived: request.body.action});
});

app.get(/^(.+)$/, function(req, res) {
  res.sendfile('public/' + req.params[0]);
});

server.listen(port, function() {
  console.log('Listening on ' + port);
});
```

我们创建一个名为`call`的对象来存储关于调用状态的信息。`call.sound`是一个布尔值，表示我们是否收到了关闭声音的请求(使手机静音)。在我们的演示中，我们将只使用`call.sound`，但是我已经将它放在一个对象中，这样将来扩展应用程序的功能将会很简单。

### 与 Android 通信

下面的路线将被用来告诉我们的 Android 设备`call.sound`的值是什么。我使用了 JSON 响应，因为我发现它似乎最适合{X} Ajax 请求。在调试时，我发现使用`console.log()`在服务器上记录这些请求非常方便。

```
app.post('/shouldibesilent', function(request, response) {
  console.log('That phone wants to know if it should be silent...', request);
  response.json({callSound: call.sound});
});
```

### 与 Leap Motion 接口

`/call`处的`POST`路由负责处理电话采取行动的请求。我们将发送一个静音电话的请求，从而将`call.sound`设置为`false`。负责处理这个问题的代码如下所示。

```
app.post('/call', function(request, response) {
  switch (request.body.action) {
    case 'mute':
      call.sound = false;
      break;
    case 'reset':
      call.sound = true;
      break;
  }

  response.json({success: true, actionReceived: request.body.action});
});
```

## 客户端 JavaScript 代码

我们的`public`目录中的`index.html`页面对于目前访问的任何人来说都是乏味无趣的。你可以扩展它来显示一个关于来电信息的仪表板，或者当你做不同手势时，给用户一点关于他们是否成功做手势的反馈。在本演示中，我们不会深入探讨这一点。

在我们今天的演示中，我们将重点关注提供 Leap Motion 输入的 JavaScript。我包含 jQuery 只是为了使用 Ajax 功能，但是您也可以用普通的 JavaScript 来达到同样的效果。

```
<script src="jquery-1.7.2.min.js"></script>
```

我还在 LeapJS 中包含了下划线，因为我发现 LeapJS API 的一些版本需要它:

```
<script src="js/underscore.min.js"></script>
<script src="js/leap.min.js"></script>
```

前端 JavaScript 代码如下所示。为了使本教程简单，JavaScript 被放在 HTML 中。

```
var controller = new Leap.Controller({enableGestures: true}),
    callMuteRequestMade = false;

controller.loop(function(frame) {
  var gesture  = frame.gestures[0],
      type = gesture ? gesture.type : '';

  if (type == 'circle') {
    console.log('Circle');

    if (!callMuteRequestMade) {
      // Only ask it to mute once!
      callMuteRequestMade = true;

      $.ajax({
        url: '/call',
        type: 'POST',
        data: {
          action: 'mute'
        }
      });
    }
  }
});

controller.on('ready', function() {
  console.log('ready');
});
controller.on('connect', function() {
  console.log('connect');
});
controller.on('disconnect', function() {
  console.log('disconnect');
});
controller.on('focus', function() {
  console.log('focus');
});
controller.on('blur', function() {
  console.log('blur');
});
controller.on('deviceConnected', function() {
  console.log('deviceConnected');
});
controller.on('deviceDisconnected', function() {
  console.log('deviceDisconnected');
});
```

下面的代码行设置了我们的 Leap 运动控制器并启用了手势，这样我们就可以检测手指的旋转。您也可以使用手势来检测滑动和手指点击。从现在开始，我们将使用`controller`变量与跳跃动作进行交互:

```
var controller = new Leap.Controller({enableGestures: true})
```

JavaScript 末尾附近的各种`controller.on()`函数用于调试目的。每一个都让我们知道我们的 Leap Motion 设备的状态何时改变。

我们主要对在 Leap Motion 检测到的每一帧上重复运行的`controller.loop()`函数感兴趣。根据 API 文档，这大约是每秒 60 次。如果您正在做任何资源过于密集的事情，请记住这一点，因为它将频繁运行！

在我们的代码中，`controller.loop()`中的每一帧都检查 Leap Motion 拾取的任何手势。`frame.gestures`包含每手牌的数据数组。`frame.gestures[0]`意味着我们只是拿起第一手。`gesture.type`然后让我们知道拿起了什么手势:

```
var gesture  = frame.gestures[0],
    type = gesture ? gesture.type : '';
```

如果手势是一个圆圈，我们会看到是否已经发出了将手机静音的请求。一旦完成，我们将`callMuteRequestMade`设置为`true`。这样，我们就不会用一个盘旋的手指为每一帧发送数百个这样的消息。完成此任务的代码如下所示。

```
if (type == 'circle') {
  console.log('Circle');

  if (!callMuteRequestMade) {
    // Only ask it to mute once!
    callMuteRequestMade = true;
...
```

最后，如果这是第一次发出静音电话的请求，我们向我们在服务器上设置的`/call`路由发出 Ajax `POST`请求。

## {X}上的代码

我们的服务器已经准备好拦截来自我们网页和 Android 设备的呼叫。我们也有网页准备发送电话静音。还有最后一点需要设置——我们的 Android 设备。我们需要在{X}上创建一个规则，并将其上传到手机。

对于 Android 设备，我们将关注 on{X} API 中的两个事件处理程序，`device.telephony.on('incomingCall')`和`device.telephony.on('idle')`。每当 on{X}检测到您的设备上有来电时，就会触发第一次。每当设备的电话功能空闲时(例如，电话停止响铃，我们不再拨出电话，等等)，就会触发第二个。

{X}上的完整代码如下所示。

```
var originalCallVolume,
    originalRingerMode,
    currentPhoneNumber,
    textMessageRequested = false;

device.telephony.on('incomingCall', function(signal) {
  originalCallVolume = device.audio.ringerVolume,
  originalRingerMode = device.audio.ringerMode;
  currentPhoneNumber = signal.phoneNumber;

  device.scheduler.setTimer({
    name: 'checkingForInCallInputs', 
    time: 0,
    interval: 5 * 1000,
    exact: false
  }, function() {
    checkIfPhoneShouldBeSilent();
  });
});

device.telephony.on('idle', function() {
  device.scheduler.removeTimer('checkingForInCallInputs');
  returnToPhoneDefaults();
});

function checkIfPhoneShouldBeSilent() {
  device.ajax({
    url: 'http://yourappurlhere.com/shouldibesilent',
    type: 'POST',
    dataType: 'json',
    data: '{"call":"incoming"}',
    headers: {'Content-Type': 'application/json'}
  }, function onSuccess(body, textStatus, response) {
    var JSONResponse = JSON.parse(body);

    console.info('successfully received http response!');
    console.info(JSON.stringify(JSONResponse));

    if (JSONResponse.callSound === false) {
      device.audio.ringerVolume = 0;

      if (!textMessageRequested) {
        textMessageRequested = true;
        device.messaging.sendSms({
          to: currentPhoneNumber,
          body: 'Sorry! In the middle of a technological breakthrough. I\'ll call you back!'
        }, function(err) {
          console.log(err || 'sms was sent successfully');
        });
      }
    }
  }, function onError(textStatus, response) {
    var error = {};

    error.message = textStatus;
    error.statusCode = response.status;
    console.error('error: ',error);
  });
}

function returnToPhoneDefaults() {
  device.audio.ringerVolume = originalCallVolume;
  device.audio.ringerMode = originalRingerMode;
  textMessageRequested = false;

  device.ajax({
    url: 'http://yourappurlhere.com/call',
    type: 'POST',
    dataType: 'json',
    data: '{"action":"reset"}',
    headers: {'Content-Type': 'application/json'}
  }, function onSuccess(body, textStatus, response) {
    var JSONResponse = JSON.parse(body);

    console.info('Successfully got a response after asking to reset the call state');
    console.info(JSON.stringify(JSONResponse));
  }, function onError(textStatus, response) {
    var error = {};

    error.message = textStatus;
    error.statusCode = response.status;
    console.error('error: ',error);
  });
}
```

### 来电检测

每当我们有一个来电，我们存储电话的当前通话量和铃声模式。这样，我们可以在呼叫结束振铃后将它们设置回这些设置，以便将来的呼叫仍然振铃。我们还会存储来电者的电话号码，这样我们可以在将他们设为静音后向他们发送短信:

```
device.telephony.on('incomingCall', function(signal) {
  originalCallVolume = device.audio.ringerVolume,
  originalRingerMode = device.audio.ringerMode;
  currentPhoneNumber = signal.phoneNumber;
...
```

然后我们运行`device.scheduler.setTimer()`，这非常类似于 JavaScript 中的原生`setTimeout()`函数。在这里，我们每五秒检查一次手机是否应该静音。其他字段执行以下操作:

*   **名称**:我们将它设置为`"checkingForInCallInputs"`，这样我们在稍后移除计时器时就有了一个名称。
*   **time** :自 1970 年 1 月 1 日起( [Unix time](http://en.wikipedia.org/wiki/Unix_time) 从该日期开始)您希望计时器开始计时的时间，以毫秒为单位。我们把它设置为零，因为我们从这一刻开始计算时间。
*   **interval** :我们希望计时器以多少毫秒的间隔调用我们的函数。我把它设置为每 5 秒一次。
*   **精确**:将此设置为`false`可为重复定时器启用一种功率优化。我不确定它在多大程度上产生了显著的差异，但我认为有这一套不会有什么坏处。

包含这些字段的`device.scheduler.setTimer()`函数如下所示:

```
device.scheduler.setTimer({
  name: 'checkingForInCallInputs', 
  time: 0,
  interval: 5 * 1000,
  exact: false
}, function() {
  checkIfPhoneShouldBeSilent();
});
```

`checkIfPhoneShouldBeSilent()`函数很长，但它是典型的 Ajax 请求。它用一个简单的 JSON 字符串向`http://yourappurlhere.com/shouldibesilent`发出`POST`请求，让我们的服务器知道有一个来电。您需要将 URL 更改为您自己的服务器的 URL。

确保您已经将`dataType`和`headers`设置为 JSON，以便 on{X}以正确的格式发送请求:

```
dataType: 'json'
headers: {'Content-Type': 'application/json'}
```

当我们成功地从服务器获得响应时，我们使用`JSON.parse(body)`解析数据:

```
onSuccess(body, textStatus, response) {
  var JSONResponse = JSON.parse(body);
```

然后，我们检查来自服务器的 JSON 响应是否要求手机静音。如果是这样，我们使用 on{X} API 的`device.audio.ringerVolume`将铃声的音量设置为 0:

```
if (JSONResponse.callSound === false) {
  device.audio.ringerVolume = 0;
```

我们不想太粗鲁，完全忽略这个人，所以我们可以选择使用 on{X} API 中的`device.messaging.sendSms`函数给他们发送短信。回想一下，我们将他们的电话号码存储在`currentPhoneNumber`变量中。我们还通过将`textMessageRequested`设置为`true`来确保我们只发送一条短信:

```
if (!textMessageRequested) {
  textMessageRequested = true;
  device.messaging.sendSms({
    to: currentPhoneNumber,
    body: 'Sorry! In the middle of a technological breakthrough. I\'ll call you back!'
  }, function(err) {
    console.log(err || 'sms was sent successfully');
  });
}
```

### 检测电话何时再次空闲

当手机再次空闲时，我们移除`checkingForInCallInputs`计时器:

```
device.telephony.on('idle', function() {
  device.scheduler.removeTimer('checkingForInCallInputs');
```

然后，我们运行`returnToPhoneDefaults()`函数将呼叫音量、振铃模式和`textMessageRequested`设置回它们的初始值:

```
function returnToPhoneDefaults() {
  device.audio.ringerVolume = originalCallVolume;
  device.audio.ringerMode = originalRingerMode;
  textMessageRequested = false;
```

我们还重置了我们的服务器，不再通过向`/call`发出另一个`POST`请求来告诉我们的手机在下一次通话时静音。目前，除了调试之外，我们不使用成功响应:

```
device.ajax({
  url: 'http://yourappurlhere.com/call',
  type: 'POST',
  dataType: 'json',
  data: '{"action":"reset"}',
  headers: {'Content-Type': 'application/json'}
}, function onSuccess(body, textStatus, response) {
  var JSONResponse = JSON.parse(body);

  console.info('Successfully got a response after asking to reset the call state');
  console.info(JSON.stringify(JSONResponse));
}, function onError(textStatus, response) {
  var error = {};

  error.message = textStatus;
  error.statusCode = response.status;
  console.error('error: ',error);
});
```

## 在{X}上调试

如果在任何阶段您想检查某些东西是否工作正常，有两种方法可以调试 on{X}代码:

*   **记录你的 JSON 回复**–`console.info(JSON.stringify(JSONResponse));`。您可以通过转到{X}上的“规则”页面，单击您的规则并选择“日志”选项卡来查看这些信息。
*   **在你的手机上创建通知来显示什么时候工作了**–`device.notifications.createNotification("No longer in a call, I'll stop asking.").show();`

## 最后的结果

当您的服务器在网上运行，您的网页在浏览器中运行，并且您的电话连接到网上时，您现在应该能够将来电静音。为了测试我们的代码，我让我可爱的女朋友给我的手机打了这样一个电话:

![Incoming Call Made to Phone](img/803a4ebc29d9e45295a5d3af2c8a4273.png)

当我的电话响起时，我会像这样将手指在空中盘旋，让来电静音:

![Circling my finger above the Leap Motion to mute the incoming call](img/18767a1b966c243e776a5f21248db04c.png)

这确实导致了一个静音的电话和一条发给我女朋友的短信，让她知道我现在还不能接电话:

![The SMS Response Received](img/de8544531c1de48cd7500d8d4ea151fb.png)

## 结论

通过连接各种设备的 JavaScript APIs，可以做很多有趣的事情。每一个发布 JavaScript API 的新设备，都有可能发展成一个更加激动人心的互联未来！走出去，集成几个 API，做一些以前没有做过的新东西。如果有人用电话打断了你的注意力，就用手指圈住，然后继续工作。

本教程的所有代码都可以在 [GitHub](https://github.com/patcat/Leap-Motion-Call-Controller) 上获得。鼓励读者把它拉下来，进行实验。例如，您可以调整 on{X}代码以保持手机静音，或者添加对不同手势和动作的支持。你也可以加入插座。IO 来提高这里显示的标准 Ajax 请求的性能。

## 分享这篇文章