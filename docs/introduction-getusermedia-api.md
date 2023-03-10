# getUserMedia API 简介

> 原文：<https://www.sitepoint.com/introduction-getusermedia-api/>

在 90 年代中期，聊天是网络上最好的产品之一。如果你还年轻，并且认为开发自己的聊天应用程序是多么酷，请举手。他们最好的功能之一是能够从网络摄像头捕捉麦克风音频和/或视频，并通过互联网发送。为了实现这些功能，开发人员长期以来一直依赖 Flash 和 Silverlight 等插件。然而，如果您没有适当的权限或者您不精通技术，Flash 和 Silverlight 可能会是一个问题。今天，由于有了 <abbr title="Web Real-Time Communication">WebRTC</abbr> 项目及其相关的 API，不再需要这样的插件了。本文将介绍 getUserMedia API，它是从 WebRTC 项目派生的 API 之一。

## 什么是 getUserMedia API

getUserMedia API 提供从本地设备对多媒体流(视频、音频或两者)的访问。这个 API 有几个用例。第一个显然是实时交流，但我们也可以用它来记录在线课程的教程或课程。另一个有趣的用例是监视你的家或工作场所。就其本身而言，这个 API 只能获取音频和视频，而不能发送数据或将其存储在文件中。例如，为了进行完整的工作聊天，我们需要通过互联网发送数据。这可以使用 [RTCPeerConnection API](https://www.w3.org/TR/webrtc/#rtcpeerconnection-interface) 来完成。为了存储数据，我们可以使用 [MediaStreamRecorder API](http://dev.w3.org/2011/webrtc/editor/webrtc-20111004.html#mediastreamrecorder) 。

getUserMedia API 对于开发者和用户来说都是惊人的。开发人员现在可以通过一个函数调用来访问音频和视频源，而用户不需要安装额外的软件。从用户的角度来看，这也意味着开始使用该功能的时间减少了，同时也增加了非技术人员对该软件的使用。

尽管 getUserMedia API 已经存在了一段时间，但截至 2013 年 12 月 30 日，它仍然是 W3C 的工作草案。因此，规格可能会有一些变化。API 只公开了一个属于`window.navigator`对象的方法`getUserMedia()`。该方法接受约束对象、成功回调和失败回调作为其参数。`constraints`参数是具有属性`audio`和`video`之一或两者的对象。这些属性的值是一个布尔值，其中`true`表示请求流(音频或视频)，而`false`不请求流。因此，要请求音频和视频，请传递以下对象。

```
{
  video: true,
  audio: true
}
```

或者，该值可以是一个 [`Constraints`对象](http://dev.w3.org/2011/webrtc/editor/getusermedia.html#idl-def-Constraints)。这种类型的对象允许我们对请求的流有更多的控制。事实上，我们可以选择检索高分辨率的视频源，例如 1280×720，或者低分辨率的，例如 320×180。每个`Constraints`对象包含两个属性，`mandatory`和`optional`。`mandatory`是一个指定<q cite="http://dev.w3.org/2011/webrtc/editor/getusermedia.html#dictionary-constraints-members">约束集的对象，UA 必须满足该约束集，否则调用 errorCallback。</q> `optional`，是指定<q cite="http://dev.w3.org/2011/webrtc/editor/getusermedia.html#dictionary-constraints-members">约束集的对象数组，UA 应该尝试满足该约束集，但是如果不能满足，则可以忽略该约束集。</q>

假设我们想要用户的音频和视频，其中视频必须至少是高分辨率的，并且帧速率为 30。此外，如果可能的话，我们希望视频的帧率为 60。为了执行这个任务，我们必须传递下面的对象。

```
{
  video: {
    mandatory: {
      minWidth: 1280,
      minHeight: 720,
      minFrameRate: 30
    },
    optional: [
      { minFrameRate: 60 }
    ]
  },
  audio: true
}
```

您可以在[规范](http://dev.w3.org/2011/webrtc/editor/getusermedia.html#sec-track-properties)中找到更多关于可用属性的信息。

`getUserMedia()`的另外两个参数只是分别在成功或失败时调用的两个回调。成功时，检索到的流被传递给回调。错误回调被传递一个 [`MediaError`对象](http://dev.w3.org/2011/webrtc/editor/getusermedia.html#idl-def-MediaError)，该对象包含关于所发生错误的信息。

## 浏览器兼容性

对 getUserMedia API 的支持在桌面上相当不错，但在移动设备上却很差。此外，大多数支持它的浏览器仍然有厂商前缀版本。目前，实现该 API 的桌面浏览器有 Chrome 21+ (-webkit 前缀)、Firefox 17+ (-moz 前缀)和 Opera 12+(15 到 17 版本不支持)，在旧版本中存在一些问题。在移动浏览器上，只有 Chrome 21+ (-webkit 前缀)和 Opera 12+(16 版的-webkit 前缀)支持该 API。还要注意，如果在 Chrome 中通过`file://`协议打开一个包含使用该 API 的指令的页面，它将无法工作。

歌剧的例子非常有趣，值得一提。这个浏览器实现了 API，但是由于一个未知的原因，在版本 15 中切换到 Blink 渲染引擎后，他们不再支持它。最后，在版本 18 中恢复了 API 支持。似乎这还不够，Opera 18 是第一个支持音频流的版本。

也就是说，多亏了一个名为 [getUserMedia.js](https://github.com/addyosmani/getUserMedia.js) 的填充程序，我们可以忽略兼容性问题。后者将测试浏览器，如果 API 没有实现，它将退回到 Flash。

## 演示

在这一节中，我将向您展示一个基本的演示，以便您可以看到 getUserMedia API 是如何工作的，并具体地看到它的参数。这个演示的目标是创建一个“镜子”，从这个意义上来说，从网络摄像头和麦克风捕捉到的所有内容都将通过屏幕和音频扬声器传输。我们将请求用户允许访问这两个多媒体流，然后使用 HTML5 `video`元素输出它们。标记非常简单。除了`video`元素，我们还有两个按钮:一个开始执行，一个停止执行。

关于脚本部分，我们首先测试浏览器支持。如果不支持 API，我们会显示消息“不支持 API”，并禁用这两个按钮。如果浏览器支持 getUserMedia API，我们将为按钮的`click`事件附加一个监听器。如果点击了“Play demo”按钮，我们将测试我们是否在处理一个旧版本的 Opera，因为前面部分描述的问题。然后，我们从用户的设备请求音频和视频数据。如果请求成功，我们使用`video`元素传输数据；否则，我们将显示控制台上发生的错误。“停止演示”按钮会暂停视频并停止流。

以下代码的现场演示可从[这里](http://aurelio.audero.it/demo/getusermedia-api-demo.html)获得。

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>getUserMedia Demo</title>
    <style>
      body
      {
        max-width: 500px;
        margin: 2em auto;
        font-size: 20px;
      }

      h1
      {
        text-align: center;
      }

      .buttons-wrapper
      {
        text-align: center;
      }

      .hidden
      {
        display: none;
      }

      #video
      {
        display: block;
        width: 100%;
      }

      .button-demo
      {
        padding: 0.5em;
        display: inline-block;
        margin: 1em auto;
      }

      .author
      {
        display: block;
        margin-top: 1em;
      }
    </style>
  </head>
  <body>
    <h1>getUserMedia API</h1>
    <video id="video" autoplay="autoplay" controls="true"></video>
    <div class="buttons-wrapper">
      <button id="button-play-gum" class="button-demo" href="#">Play demo</button>
      <button id="button-stop-gum" class="button-demo" href="#">Stop demo</button>
    </div>
    <span id="gum-unsupported" class="hidden">API not supported</span>
    <span id="gum-partially-supported" class="hidden">API partially supported (video only)</span>
    <script>
      var videoStream = null;
      var video = document.getElementById("video");

      // Test browser support
      window.navigator = window.navigator || {};
      navigator.getUserMedia = navigator.getUserMedia       ||
                               navigator.webkitGetUserMedia ||
                               navigator.mozGetUserMedia    ||
                               null;

      if (navigator.getUserMedia === null) {
        document.getElementById('gum-unsupported').classList.remove('hidden');
        document.getElementById('button-play-gum').setAttribute('disabled', 'disabled');
        document.getElementById('button-stop-gum').setAttribute('disabled', 'disabled');
      } else {
        // Opera <= 12.16 accepts the direct stream.
        // More on this here: http://dev.opera.com/articles/view/playing-with-html5-video-and-getusermedia-support/
        var createSrc = window.URL ? window.URL.createObjectURL : function(stream) {return stream;};

        // Opera <= 12.16 support video only.
        var audioContext = window.AudioContext       ||
                           window.webkitAudioContext ||
                           null;
        if (audioContext === null) {
          document.getElementById('gum-partially-supported').classList.remove('hidden');
        }

        document.getElementById('button-play-gum').addEventListener('click', function() {
          // Capture user's audio and video source
          navigator.getUserMedia({
            video: true,
            audio: true
          },
          function(stream) {
            videoStream = stream;
            // Stream the data
            video.src = createSrc(stream);
            video.play();
          },
          function(error) {
            console.log("Video capture error: ", error.code);
          });
        });
        document.getElementById('button-stop-gum').addEventListener('click', function() {
          // Pause the video
          video.pause();
          // Stop the stream
          videoStream.stop();
        });
      }
    </script>
  </body>
</html>
```

## 结论

本文向您介绍了 WebRTC 项目，这是近年来最令人兴奋的 web 项目之一。特别是，本文讨论了 getUserMedia API。只用浏览器和很少的几行代码就能创建一个实时通信系统的可能性是非常好的，并带来了许多新的机会。

正如我们所看到的，getUserMedia API 简单但非常灵活。它只公开了一个方法，但是它的第一个参数`constraints`允许我们要求更适合我们应用程序需求的音频和视频流。浏览器之间的兼容性不是很广，但是正在增加，这是个好消息！为了更好地理解本文中的概念，不要忘记使用提供的演示。最后，我强烈建议您尝试更改代码来执行一些任务，例如应用 CSS 过滤器来更改视频流的显示方式。

## 分享这篇文章