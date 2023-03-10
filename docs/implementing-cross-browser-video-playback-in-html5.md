# 在 HTML5 中实现跨浏览器的视频播放

> 原文：<https://www.sitepoint.com/implementing-cross-browser-video-playback-in-html5/>

新 HTML5 规范中最著名的元素之一是本机处理视频回放的能力。一个简单的<video>标签是在许多现代浏览器上获得即时视频支持所需要的——是吗？并不是所有的浏览器版本都能处理这个 HTML5 特性；截至 2012 年 1 月，全球约三分之二的浏览器部分或完全支持<video>元素。下面是一些选项，可以确保<video>元素在现代和不那么现代的浏览器中都可以正常工作。</video></video></video>

但是，等等！当你可以上传到 YouTube 并嵌入视频时，为什么还要费心使用<video>？毕竟，YouTube 有移动支持，并使编码成为一个简单的过程。尽管人们普遍认为 YouTube 就是你所需要的一切，但它确实有一些明显的缺点。例如，在 YouTube 视频的末尾会显示一系列其他推荐视频，这可能会吸引用户离开你的网站。YouTube 还会给每个视频添加一个小水印，这并不总是被接受的。如果你的视频包含敏感的、受版权保护的、“仅限会员”或“仅限员工”的信息，自托管可能是你唯一的选择。在很多情况下，YouTube 并不是一个可行的选择。如果你发现自己处于这样的困境中，这里有一些如何在你的网站上设计可靠的、自托管的视频回放的方法。</video>

### <video>标签基础知识</video>

<video>元素适用于所有主流浏览器的最新版本(IE9+、Firefox 7+、Chrome 14+、Safari 4+和 Opera 11+)。<video>的典型实现如下所示:</video></video>

```
<video controls poster="linktoposter.jpg" width="640" height="360" controls="controls">
 <source src="linktovideo.mp4" type="video/mp4" />
 <source src="linktovideo.webm" type="video/webm" />
 <source src="linktovideo.ogv" type="video/ogg" />
 <p>Fallback text.</p>
 </video>
```

注意你是如何引用一个视频的几个版本的。这是因为并非所有的浏览器都支持 MP4 播放。那些没有的将跳到 WEBM 格式。如果浏览器不支持 MP4 或 WEBM，它将默认为 OGV 格式。在这三种格式之间，您应该可以兼容几乎所有常见的浏览器。

让我们仔细看看上面使用的<video>属性:</video>

*   **海报**–视频加载时或访客点击播放按钮前显示的图片链接
*   **控件**–告知访客是否可以看到视频播放控件
*   **source**–视频文件所在位置的链接
*   **源类型**–引用文件的视频格式
*   **回退文本**–如果浏览器不能播放三种视频格式中的任何一种，将显示< p >标签内的文本

如果你在网站上实现<video>有困难，请记住以下修正:</video>

*   视频的 MP4 版本必须首先列出，因为 iPad 有一个 bug。
*   如果视频不能在通用的最新浏览器上播放，您可能需要将适当的 MIME 类型添加到您的。htaccess 文件:

*   添加类型视频/ogg–ogv
*   添加类型视频/MP4–MP4，m4v
*   添加类型视频/网络媒体–网络媒体

### 转换视频

将你的视频转换成不同的文件类型可能听起来很乏味，但是很容易做到。用于将视频文件转换为多种格式的免费资源是 [Miro Video Converter](http://www.mirovideoconverter.com/) ，它可以让您将视频文件转换为不同的格式，同时保留原始尺寸。其他工具是可用的，但对于一个独立的，简单的过程，Miro 视频转换器是正确的选择。

### 向后兼容性

为了确保较旧的浏览器可以查看您的视频，您应该依靠 Adobe Flash 后备。因为这种与旧浏览器的兼容性问题是一个常见的问题，所以网上有几个库和脚本可以为您做很多繁重的工作。我们来看看其中的两个:

*   **media element . js**–一个工具包，让您可以选择只包含一个单独的. MP4 文件，并可以自由使用“多个带 Flash fallback 的编解码器”Drupal 和 WordPress(以及其他)的插件使得这成为一个很好的选择。请记住，这个选项是依赖于 javascript 的，您可以对播放器做很多选择(这是一件好事)。[点击这里查看 MediaElement.js](http://mediaelementjs.com) 。
*   **人人视频**–这是一个无 javascript 的解决方案，提供了一种非常简单的方法来确保与旧浏览器的兼容性。没有要下载或安装的内容；你唯一需要做的就是调用一个 Flash 容器。其中有一些:FlashFox，Flow Player 和 JW Player。[点击这里查看视频给大家](http://camendesign.co.uk/code/video_for_everybody)。

**代码示例:**

在这两种方法中，面向所有人的视频需要的设置最少，因此代码如下:

```
<video controls="controls" poster="linktoposter.jpg" width="640" height="360">
 <source src="linktomovie.mp4" type="video/mp4" />
 <source src="linktomovie.webm" type="video/webm" />
 <source src="linktomovie.ogv" type="video/ogg" />
 <!-- Fallback object using Flow Player -->
<object type="application/x-shockwave-flash" data="http://releases.flowplayer.org/swf/flowplayer-3.2.1.swf" width="640" height="360">
 <param name="movie" value="http://releases.flowplayer.org/swf/flowplayer-3.2.1.swf" />
 <param name="allowFullScreen" value="true" />
 <param name="wmode" value="transparent" />
 <param name="flashVars" value="config={'playlist':[ 'linktoposter.jpg',{'url':'linktomovie.mp4','autoPlay':false}]}" />
 <img alt="My Movie" src="linktoposter.jpg" width="640" height="360" title="No video playback capabilities, please download the video below." />
 </object>
 <!-- Fallback Text -->
 Your browser does not appear to support a browser. Please download the video below.
</video>
 <p>
 <strong>Download video:</strong> <a href="linktomovie.mp4">MP4 format</a> | <a href=" linktomovie.ogv">Ogg format</a> | <a href=" linktomovie.webm">WebM format</a>
 </p>
```

对每个人来说，视频的好处在于它只需要一个. MP4 文件就可以工作。但是，拥有 WEBM 和 OGV 格式仍然是一个好主意，因为许多浏览器都支持它们。

### 浏览器兼容性

如果你想知道哪些现代浏览器支持哪些格式，这里有一个简单的分类。

| **浏览器** | **MP4** | **OGV** | **网页** |
| *IE9* | 是 | 手动安装 | 手动安装 |
| *火狐(10)* | 不 | 是 | 是 |
| *铬(17)* | 不 | 是 | 是 |
| *安卓* | 是 | 是 | 是 |
| *Safari (5.1.3)* | 是 | 手动安装 | 手动安装 |
| *歌剧(11.61)* | 手动安装 | 是 | 是 |

### 总之…

所以，如果你有所有三种文件格式——MP4、WEBM 和 OGV——你应该有你的大部分基础，除了非常旧的浏览器和模糊的浏览器。这就是闪存回退发挥作用的地方。将几种 Flash 后退技术之一与 HTML5 一起使用，是尖端技术和适应旧浏览器的可靠结合。

## 分享这篇文章