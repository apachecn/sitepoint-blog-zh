# HTML5 视频:片段、标题和动态缩略图

> 原文：<https://www.sitepoint.com/html5-video-fragments-captions-dynamic-thumbnails/>

希望利用在线视频做更多事情的 Web 和应用程序开发人员可能会发现，三个鲜为人知或至少很少讨论的 HTML5 视频功能可能会开启许多新的创造性技术，以新的方式集成视频。

在这篇文章中，我将描述:媒体片段，轨道元素，以及 HTML5 视频与其他元素轻松集成的能力。

## 媒体片段

媒体片段或媒体片段 URIs [是 W3C 推荐标准](https://www.w3.org/TR/media-frags/),旨在支持 web 浏览器中本地视频处理的某些方面。

目前，该功能可用于在特定时刻开始或结束视频播放。人们可以想象这个特性支持一种视频精灵，例如，允许 HTML 游戏开发人员加载单个视频文件，但可以轻松地播放不同的部分来响应一些玩家的动作。

在其最简单的形式中，媒体片段开始时间被添加到视频源 URL。请注意，在下面的示例中，源 URL 后面的“#t=20”表示临时媒体片段。

```
<video controls>
    <source src="102614-video-sample.mp4#t=20">
</video>
```

在上面的代码中，视频将在 00:20 开始回放(假设是 mm:ss)。让我们看另一个例子:

```
<video controls>
    <source src="102614-video-sample.mp4#t=6,20">
</video>
```

上面的例子将在 0:06 开始播放，并持续播放到 0:20。

`src` URI 中的时间值也可以以小时-分钟-秒的格式指定(hh:mm:ss):

```
<video controls>
    <source src="102614-video-sample.mp4#t=00:00:20">
</video>
```

为了演示媒体片段，我有一个 27 秒的浮潜视频，其中有三个相当明显的过渡。第一部分开始于视频的开头(00:00:00)，下一部分开始于大约 00:00:06，第三个过渡发生在大约 00:00:17。

在演示中，有一个按钮代表每个视频片段。我还包含了两个单独的源文件，以确保视频可以在大多数浏览器中播放。

以下是视频代码和导航:

```
<video id="frag1" controls preload="metadata" width="720px" height="540px">
    <source src="102614-video-sample.mp4"
            type='video/mp4;codecs="avc1.42E01E, mp4a.40.2"'
            data-original="102614-video-sample.mp4">
    <source src="102614-video-sample.webm"
            type='video/webm;codecs="vp8, vorbis"'
            data-original="102614-video-sample-webmhd.webm">
</video>

<div class="nav">
    <button data-start="0">Section One</button>
    <button data-start="6">Section Two</button>
    <button data-start="17">Section Three</button>
</div>
```

数据属性已经被添加到源元素和按钮中，使得用 JavaScript 插入基于时间的媒体片段变得更加容易。实际上，当单击按钮时，脚本会加载一个带有基于时间的媒体片段的新源。

```
function mediaFragOne() {
    var video, sources, nav, buttons;
    video = document.querySelector('video#frag1');
    sources = video.getElementsByTagName('source');
    nav = document.querySelector('video#frag1+nav');
    buttons = nav.getElementsByTagName('button');

    for (var i = buttons.length - 1; i >= 0; i--) {
        buttons[i].addEventListener('click', function() {
            for (var i = sources.length - 1; i >= 0; i--) {
                sources[i].setAttribute(
                    'src', (sources[i].getAttribute('data-original')
                    .concat('#t=' + this.getAttribute('data-start'))));
                    video.load();
                    video.play();
            };
        });
    };
}
mediaFragOne();
```

这是演示:

*(请注意，由于视频文件在外部托管，加载单个文件时会有一些延迟，因此给演示一些时间来显示视频。)*

在[码笔](http://codepen.io)上看到 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的笔[c 376d 7 feb 0826d 02d 244046 ed 0 e 7 BD 77](http://codepen.io/SitePoint/pen/c376d7feb0826d02d244046ed0e7bd77/)。