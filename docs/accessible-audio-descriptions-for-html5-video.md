# HTML5 视频的辅助音频描述

> 原文：<https://www.sitepoint.com/accessible-audio-descriptions-for-html5-video/>

一位客户最近要求我制作一个无障碍视频播放器，她非常希望拥有的功能之一是**音频描述**。音频描述适用于盲人或视力受损者，提供额外的语音信息来描述重要的视觉细节。

传统上，音频描述的视频必须特别制作，音频编码在单个视频文件的单独轨道中。对这些音轨进行编码需要非常专业的视频编辑设备，这给大多数内容制作者提出了超出实际水平的要求。

我在网上看到的所有音频描述的内容都是这样的。例如， [BBC iPlayer](http://www.bbc.co.uk/iplayer/) 有一个这样的内容的[选择，但是视频播放器不能让你控制相对音量，你也不能关闭音频描述——你只能观看节目的单独描述或未描述版本。](http://www.bbc.co.uk/iplayer/tv/categories/audiodescribed "Audio Described programs on BBC iPlayer (bbc.co.uk)")

## 输入 HTML5

HTML5 视频规范确实提供了一个`[audioTracks](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html#media-resources-with-multiple-media-tracks "Media resources with multiple media tracks (whatwg.org)")`对象，它可以实现一个开/关按钮，并分别控制音频和视频音量。但它的浏览器支持实际上是不存在的——在撰写本文时，只有 <abbr title="Internet Explorer 10">IE10</abbr> 支持这一功能。

无论如何，我的客户想要的是一个单独文件中的**音频描述，它可以被添加到视频中，而不需要创建一个单独的版本，并且不需要专门的软件也很容易制作。当然，它必须能在相当多的浏览器中工作。**

所以我的下一个想法是使用[媒体控制器](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html#synchronising-multiple-media-elements "Synchronising multiple media elements (whatwg.org)")，这是 <abbr title="HyperText Markup Language Version 5">HTML5</abbr> 音频和视频的一个功能，允许你同步多个来源。然而，浏览器对此的支持同样不足——在撰写本文时，只有 Chrome 支持这一功能。

但是你知道——即使没有这种支持，同时启动两个媒体文件显然不成问题，这只是让 T2 保持它们同步的问题。那么我们能使用现有的、广泛实现的特性来实现它吗？

## 视频事件

视频 <abbr title="Application Programming Interface">API</abbr> 提供了许多我们可以挂接的事件，这使得音频回放与视频事件同步成为可能:

*   `"play"`事件(播放视频时触发)。
*   `"pause"`事件(当视频暂停时触发)。
*   `"ended"`事件(当视频结束时触发)。
*   `"timeupdate"`事件(在视频播放时持续触发)。

真正至关重要的是`"timeupdate"`事件。它发射的*频率*没有规定，实践中变化很大——但作为一个粗略的整体平均值，它达到每秒 3-5 次，这对我们的目的来说足够了。

我见过有人尝试用类似的方法来同步两个视频文件，但这并不特别成功，因为即使是微小的差异也非常明显。但是音频描述通常不需要如此精确地同步——无论哪种方式的延迟都是可以接受的——而且播放音频文件对浏览器来说工作量也少得多。

因此，我们需要做的就是使用我们拥有的视频事件，将音频和视频回放锁定在一起:

*   播放视频时，播放音频。
*   当视频暂停时，暂停音频。
*   当视频结束时，同时暂停视频和音频。
*   当时间更新时，如果音频时间和视频时间不同，请将它们设定为匹配。

经过一些实验后，我发现通过比较整秒的时间可以得到最好的结果，就像这样:

```
if(Math.ceil(audio.currentTime) != Math.ceil(video.currentTime))
{
  audio.currentTime = video.currentTime;
}
```

这似乎有悖直觉，最初我认为我们需要尽可能多的数据精度，但事实似乎并非如此。通过使用视频原声的文字音频副本进行测试(即音频和视频都产生相同的声音)，很容易听到同步的好坏。在此基础上进行试验，我得到了更好的同步，当四舍五入的数字，比没有。

这是最终的剧本。如果浏览器支持`MediaController`，那么我们就使用它，否则我们执行手动同步，如下所述:

```
var video = document.getElementById('video');
var audio = document.getElementById('audio');

if(typeof(window.MediaController) === 'function')
{
  var controller = new MediaController();
  video.controller = controller;
  audio.controller = controller;
} 
else
{
  controller = null;
}

video.volume = 0.8;
audio.volume = 1;

video.addEventListener('play', function() 
{
  if(!controller && audio.paused)
  {
    audio.play();
  }
}, false);

video.addEventListener('pause', function()
{
  if(!controller && !audio.paused)
  {
    audio.pause();
  }
}, false);

video.addEventListener('ended', function()
{
  if(controller)
  {
    controller.pause();
  }
  else
  {
    video.pause();
    audio.pause();
  }
}, false);

video.addEventListener('timeupdate', function()
{
  if(!controller && audio.readyState >= 4)
  {
    if(Math.ceil(audio.currentTime) != Math.ceil(video.currentTime))
    {
      audio.currentTime = video.currentTime;
    }
  }
}, false);
```

请注意，`MediaController`本身仅通过脚本定义，而使用静态`"mediagroup"`属性定义控制器是可能的:

```
<video mediagroup="foo"> ... </video>
<audio mediagroup="foo"> ... </audio>
```

如果我们这样做了，那么在 Chrome 中没有 JavaScript 也能工作。它将同步媒体源，但用户将无法控制音频(包括无法关闭)，因为浏览器*不知道音频代表什么*。在这种情况下，将音频编码到视频中会更好，因为这样它就可以出现在`audioTracks`对象中，浏览器可以识别并提供本地控件。

但是因为我们没有数据，这是一个相当有争议的问题！因此，如果脚本不可用，音频就无法播放。

这是最后一个演示版本，它可以在任何最新版本的 Opera、Firefox、Chrome、Safari 或 <abbr title="Internet Explorer 9">IE9</abbr> 或更高版本中运行:

*   **[音频描述演示](http://jspro.brothercake.com/audio-descriptions/)**

当然，这只是一个简单的概念验证演示——没有初始特性检测，它只有本机`"controls"`属性提供的基本控件。对于一个正确的实现，它需要自定义控件，提供(除其他外)一个按钮来打开和关闭音频，以及单独的音量滑块。该接口还应该可以通过键盘访问，这在一些浏览器的本地控件中是做不到的。它还需要正确处理缓冲——事实上，如果您搜索超过视频预加载的点，音频将继续自由播放，直到视频加载足够多，使其恢复同步。

我可能还会提到，描述本身很难达到专业标准！这是我的声音，你可以听到，记录和转换使用 [Audacity](http://audacity.sourceforge.net/ "Audacity is a free audio editor and recorded") 。但尽管如此，我认为它有效地证明了这种方法的技术门槛有多低。视频不用剪辑，用免费软件一个小时就把音频做好了。

作为概念验证，我要说它相当成功——我确信我的客户会非常满意！

## 分享这篇文章