# WebRTC 的黎明

> 原文：<https://www.sitepoint.com/the-dawn-of-webrtc/>

Web 实时通信(WebRTC)旨在为开发人员提供使用简单的 JavaScript APIs 创建高清视频和音频通话的能力。这些 API 直接嵌入在浏览器中，不需要任何类型的插件、下载或安装就可以启动并运行。

谷歌花了大约 2 亿美元将这项技术开源给开发社区。WebRTC 使用多种视频和音频编解码器，使任何人都能够创建下一代通信应用程序，而无需支付许可或版税。

## 有哪些可能性？

对于 WebRTC 将如何改变通信行业，我们才刚刚开始触及皮毛。我们看到所有类型的应用程序都是用 WebRTC 创建的。最具标志性的例子之一就是[亚马逊的五月天按钮](https://vimeo.com/77009281)。它展示了大大小小的公司如何利用 WebRTC 的真正力量。

WebRTC 为您带来了许多增强应用程序的功能，例如:

*   **视频通信:**在浏览器之间创建安全的高清音频和视频流
*   **文件共享和消息传递:**在浏览器之间安全地连接和共享数据，无需将文件上传到云或网络服务器。数据在连接的对等体之间直接发送
*   **电话到浏览器:** WebRTC 允许公共交换电话网(PSTN)和浏览器之间的连接。使用 HTML5、SIP 网关和 WebRTC 中的新 API，您可以从一个位置拨打和接听电话
*   **Mobile to Mobile:** WebRTC 不仅仅用于 web，iOS 和 Android 都有利用 WebRTC 功能的本地库
*   **机器对机器:** WebRTC 可嵌入到需要机器对机器通信的系统中，如物联网。Google Chromecast 是在正常用例之外使用 WebRTC 的完美例子

## 了解 WebRTC APIs

WebRTC 依赖于直接嵌入到 web 浏览器中的三个 JavaScript APIs，不需要客户端或浏览器插件就可以与另一个支持 WebRTC 的浏览器直接通信。这些 API 是:

*   MediaStream(又名 getUserMedia)允许您访问用户使用的设备的摄像头、麦克风或屏幕。作为一个附加的安全层，在您被允许流式传输他们的媒体之前，用户将被授予访问权限。如果用户通过安全连接(HTTPS)进行连接，则用户只需为应用程序授予一次访问权限，但是如果用户通过非安全连接(HTTP)进行连接，则每次应用程序需要访问时都会提示用户
*   RTCPeerConnection(又名 PeerConnection)允许两个用户直接进行对等通信。它编码和解码发送到本地机器和从本地机器发送到接收媒体的远程对等机的媒体。
*   RTCDataChannel(又名 DataChannel)表示两个对等体之间的双向数据通道。它搭载在 RTCPeerConnection 之上，允许您在两个连接的对等体之间安全地直接发送数据。

## WebRTC 入门

我们将从一个简单的 photo booth 应用程序开始，它允许您使用网络摄像头捕捉图像，并对捕捉到的图像应用一些 CSS 滤镜。它将教您使用 MediaStream API 开始使用 WebRTC 的基础知识。这是谷歌团队创建的示例应用程序的略微修改版本

### 超文本标记语言

在下面的 HTML 代码中，您将看到创建第一个 WebRTC web 应用程序所需的基础知识。WebRTC 利用 HTML5 `video”元素来呈现本地和远程视频流。此外，我们将使用“canvas”元素制作本地视频流的快照，以应用过滤器:

```
<div class="m-content">
   <h1>getUserMedia + CSS filters demo</h1>

   <div class="photo-booth">
      <!-- local video stream will be rendered to the video tag -->
      <video autoplay></video>
      <!-- a copy of the stream will be made and css filters applied  -->
      <canvas></canvas>
   </div>
   <div class="buttons">
      <!-- call getUserMedia() to access webcam and give permission -->
      <button id="start">Access Webcam</button>
      <!-- take a snapshot from your webcam and render it to the canvas tag -->
      <button id="snapshot">Take a Snapshot</button>
      <!-- sort through the available css filters -->
      <button id="filter">Change Filter</button>
   </div>
</div>
```

### Java Script 语言

`navigator.getUserMedia()`方法是 getUserMedia API 提供的方法，它允许您从用户那里检索流。在撰写本文时，需要针对不同的供应商前缀进行定义，以使该应用程序能够在所有兼容 WebRTC 的浏览器上工作:Chrome、Firefox、Opera。我们可以用下面的代码实现这个目标:

```
navigator.getUserMedia = navigator.getUserMedia ||
                         navigator.webkitGetUserMedia ||
                         navigator.mozGetUserMedia;
```

我们需要用`navigator.getUserMedia()`定义我们所请求的约束，这将决定我们所请求的媒体类型。在本例中，我们仅通过设置`video: true`请求访问用户的网络摄像头。

```
var constraints = { audio: false, video: true };
```

下面我们在变量中定义和存储演示应用程序的 HTML 元素。

```
var start   = document.querySelector('#start');
var snapshot = document.querySelector('#snapshot');
var filter  = document.querySelector('#filter');
var video   = document.querySelector('video');
var canvas  = document.querySelector('canvas');
```

接下来，我们需要为将要应用于快照的过滤器创建一个数组。我们将在 CSS 代码中定义过滤器，在下一节中描述，使用相同的名称:

```
var filters = ['blur', 'brightness', 'contrast', 'grayscale',
               'hue', 'invert', 'saturate', 'sepia'];
```

真正有趣的时间到了！我们向开始按钮添加一个`click`事件来初始化`navigator.getUserMedia(constraints, success, error);`以访问我们的网络摄像头。必须获得许可才能访问我们的网络摄像头。每个浏览器供应商都以不同的方式处理显示允许访问用户的网络摄像头和麦克风的提示。

```
start.addEventListener('click', function() {
    navigator.getUserMedia(constraints, success, error);
});
```

在成功授予访问用户网络摄像头的权限后，我们将 stream 对象作为 HTML5 `video`标签的源进行传递。

```
function success(stream) {
   /* hide the start button*/
   start.style.display = 'none';

   /* show the snapshot button*/
   snapshot.style.display = 'block';

   /* show the filter button*/
   filter.style.display = 'block';
   if(window.URL) {
      video.src = window.URL.createObjectURL(stream);
   } else {
      video.src = stream;
   }
}
```

如果访问用户的网络摄像头时出现错误或权限被拒绝，您将收到一条错误消息，该消息将打印到控制台。

```
function error(e) {
   console.log('navigator.getUserMedia error: ', e);
}
```

接下来，我们创建一个简单的函数，将 CSS 过滤器应用于`canvas`和`video`元素。该函数将找到 CSS 类的名称，并将过滤器应用于画布。

```
filter.addEventListener('click', function() {	
   var index = (filters.indexOf(canvas.className) + 1) % filters.length;
   video.className = filters[index];
   canvas.className = filters[index];		
});
```

最后，我们拍摄本地视频流的快照，并将其呈现给`canvas`。

```
snapshot.addEventListener('click', function() {
   canvas.width = 360;
   canvas.height = 270;
   canvas.getContext('2d').drawImage(video, 0, 0, canvas.width, canvas.height);
});
```

### 半铸钢ˌ钢性铸铁(Cast Semi-Steel)

下面你会发现设计你的第一个 WebRTC 应用程序的基础。

```
body
{
   font-family: 'Open Sans', sans-serif;
   background-color: #e4e4e4;
}

h1
{
   width: 780px;
   margin-left: 20px;
   float: left;
}

.m-content
{
   width: 800px;
   height: 310px;
   margin: auto;
}

.photo-booth
{
   width: 800px;
   height: 310px;
   float: left;
}
```

WebRTC 允许两种方式来定义视频流的大小。您可以在传递给`navigator.getUserMedia(contraints, success, error);`的`contraints`变量中定义它，也可以在 CSS 中定义它。在这个例子中，我们使用 CSS 来定义本地视频流的`video`维度和`canvas`元素。

```
video
{
   width: 360px;
   height: 270px;
   float: left;
   margin: 20px;
   background-color: #333;
}

canvas
{
   width: 360px;
   height: 270px;
   float: left;
   margin: 20px;
   background-color: #777;
}
```

接下来，我们给我们的按钮一个小喇叭。我们将隐藏过滤器和快照按钮，直到我们可以使用`getUserMedia()`访问我们的麦克风和摄像头。

```
.buttons
{
   margin-left: 20px;
   float: left;
}

button
{
   background-color: #d84a38;
   border: none;
   border-radius: 2px;
   color: white;
   font-family: 'Open Sans', sans-serif;
   font-size: 0.8em;
   margin: 0 0 1em 0;
   padding: 0.5em 0.7em 0.6em 0.7em;
}

button:active
{
   background-color: #cf402f;
}

button:hover
{
   background-color: #cf402f;
   cursor: pointer;
}

#filter, #snapshot
{
   display: none;
   margin-right: 20px;
   float: left;
}
```

接下来，我将使用 CSS 定义照片亭的过滤器。您可以在相关 MDN 页面的[上找到支持的过滤器列表。](https://developer.mozilla.org/en-US/docs/Web/CSS/filter)

```
.blur
{
   filter: blur(2px);
   -webkit-filter: blur(2px);
}

.grayscale
{
   filter: grayscale(1);
   -webkit-filter: grayscale(1);
}

.sepia
{
   filter: sepia(1);
   -webkit-filter: sepia(1);
}

.brightness
{
   filter: brightness(2.2);
   -webkit-filter: brightness(2.2);
}

.contrast
{
   filter: contrast(3);
   -webkit-filter: contrast(3);
}

.hue
{
   filter: hue-rotate(120deg);
   -webkit-filter: hue-rotate(120deg);
}

.invert
{
   filter: invert(1);
   -webkit-filter: invert(1);
}

.saturate
{
   filter: staurate(5);
   -webkit-filter: staurate(5);
}
```

如果您熟悉 MailChimp，您可能已经注意到了使用网络摄像头添加个人资料图片的功能。MailChimp 为其用户添加了一个简单而有效的解决方案，使用 WebRTC 以类似于本演示的方式修改他们的个人资料图像。

[本文开发的代码可以在 GitHub](https://github.com/sitepoint-editors/The-Dawn-of-WebRTC) 上获得。您可以在 [WebRTC 挑战赛](http://webrtcchallenge.com/articles/sitepoint/the-dawn-of-webrtc/?utm_source=sitepoint.com&utm_medium=article&utm_campaign=article%3Athe%20dawn%20of%20webrtc)上观看照片应用的现场演示。

## 和睦相处

因此，您可能想知道 WebRTC 在浏览器供应商和移动设备上的可用性。目前，WebRTC 只兼容 Chrome、Firefox 和 Opera 的桌面版本以及 Android 上的移动浏览器。WebRTC 还不能在 iOS 上用于移动浏览器，但是您可以使用本地库来构建您的 iOS 和 Android 应用程序。微软正在积极推动对象实时通信(ORTC ),它目前计划成为 WebRTC 1.1 规范的一部分。在那之前，有一个解决办法是使用 Temasys 托管的开源插件[来支持 Internet Explorer 和 Safari。](https://temasys.atlassian.net/wiki/display/TWPP/WebRTC+Plugins)

爱立信目前正在用他们的“布瑟”应用支持 WebRTC，你可以从苹果应用商店下载。这是他们新框架 [OpenWebRTC](http://www.openwebrtc.io/) 的一部分，OpenWebRTC 是基于 GStreamer 的跨平台 WebRTC 客户端框架。

一个方便的工具，你可以用来检查你最喜欢的浏览器的状态是网站[iswebrtcreadyyet.com](http://iswebrtcreadyyet.com/)。

## WebRTC 资源

网络实时通信是一项激动人心的技术，它为创新打开了大门。开发人员现在可以增强用户体验，并在他们的应用程序中提供上下文信息。下面是一些资源，您可以从中找到更多关于 WebRTC 的信息。

*   Webrtc.org 主页，由谷歌 Chrome 团队维护
*   与 WebRTC 的实时通信: [Google I/O 2013 演示文稿](https://www.youtube.com/watch?v=p2HzZkd2A40)
*   [WebRTC GitHub 回购](https://github.com/webrtc/)

如果您想使用 WebRTC 与朋友进行简单的会议或对话，以下是您可以免费使用的资源列表:

*   [Tawk.com](https://tawk.com)
*   [Talky.io](https://talky.io)
*   [出镜](https://appear.in)

## WebRTC 挑战

如果您想了解更多关于 WebRTC 生态系统的信息，请参加 [WebRTC 挑战赛](http://www.webrtcchallenge.com/?utm_source=sitepoint.com&utm_medium=article&utm_campaign=article:the%20dawn%20of%20webrtc)。这是由 [Blacc Spot Media](http://www.blaccspot.com/?utm_source=sitepoint.com&utm_medium=article&utm_campaign=article:the%20dawn%20of%20webrtc) 团队发起的一项新计划，旨在向 web 和移动社区的开发人员介绍 WebRTC 的优势和功能，并对他们进行培训。

## 结论

这只是 Web 实时通信(WebRTC)的力量和能力的一瞥。随着本系列的继续，我们将深入探究 WebRTC 的来龙去脉，它在市场中的地位，以及大大小小的公司如何开始利用它的力量。重要的是要记住，WebRTC 是**而不是**一个开箱即用的解决方案，而是一个允许您增强应用程序的工具。敬请关注更多内容！

## 分享这篇文章