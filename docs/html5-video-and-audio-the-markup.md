# HTML5 视频和音频:标记

> 原文：<https://www.sitepoint.com/html5-video-and-audio-the-markup/>

以下是我们的书的摘录，由 Alexis Goldstein、Louis Lazaris 和 Estelle Weyl 撰写的《真实世界的 CSS3，第二版。世界各地的商店都有出售，或者你可以在这里买到电子书。

### 加价

在围绕容器、编解码器和许可问题的必要业务之后，是时候检查`video`元素的标记及其相关属性了。

在网页中包含 HTML5 视频的最简单方法如下:

```
<video src="example.webm"></video>
```

正如您可能已经从前面几节中发现的那样，这只能在有限的几种浏览器中工作。然而，在某种程度上，这是 HTML5 视频工作所需的最少代码。在一个完美的世界中，它在任何地方都可以工作——就像`img`元素在任何地方都可以工作一样——但是这还有点遥远。

类似于`img`元素，`video`元素也可以包含`width`和`height`属性:

即使可以在标记中设置尺寸，它们也不会影响视频的纵横比。例如，如果前一个示例中的视频实际上是 375×240，并且标记如图所示，则视频将在 HTML 中指定的 280 像素空间内垂直居中。这可以防止视频出现不必要的拉伸和失真。

`width`和`height`属性只接受整数，它们的值总是以像素为单位。自然，这些值可以通过脚本或 CSS 来覆盖。

### 启用本机控件

如果不能让用户播放、暂停、停止、浏览视频或调节音量，任何嵌入式视频都是不完整的。HTML5 的`video`元素包括一个`controls`属性，它就是这样做的:

```
<video src="example.webm" width="375" height="280" controls></video>
```

`controls`是布尔属性，所以不需要值。它包含在标记中告诉浏览器使控件对用户可见和可访问。

每个浏览器负责内置视频控件的外观。图 5.1 和图 5.2 显示了这些控件在不同浏览器之间的外观差异。

![](img/3849e23515189853c0f87409880858f7.png)

图 5.1。Chrome 中的原生视频控件

![](img/c4c68cd80016a5c7e9bb4f48294fe70c.png)

图 5.2。…在 Firefox 中

![](img/cf9cebfe38d18f03a0258067c72afff8.png)

图 5.3。…在 Internet Explorer 中

![](img/134468726e5f8adaf984c50cadb312f5.png)

图 5.4。…在歌剧中

### `autoplay`属性

我们很想省略对这个特殊属性的引用，因为在大多数情况下使用它是不可取的；但是，在某些情况下，它可能是合适的。布尔属性`autoplay`正如它的名字所暗示的那样:它告诉网页尽可能快地立即播放视频。

通常，这是一种不好的做法；我们大多数人都非常清楚，如果一个网站一加载就开始播放视频或音频，会有多么不和谐——尤其是如果我们的扬声器开得很大。可用性最佳实践规定，只有当用户请求时，才应该触发网页上的声音和动作。但这并不意味着永远不应该使用`autoplay`属性。

例如，如果所讨论的页面只包含一个视频，也就是说，用户单击某个页面的链接只是为了查看特定的视频，那么根据视频的大小、周围的内容、查看平台和观众，它可以自动播放。

下面是使用该属性的方法:

```
<video src="example.webm" width="375" height="280" controls ↵autoplay></video>
```

#### 警告:手机浏览器忽略`autoplay`

许多(如果不是全部的话)移动浏览器会忽略`autoplay`属性，所以视频会一直等待用户按下播放按钮才开始播放。考虑到移动带宽通常有限且昂贵，这是明智的。

### `loop`属性

另一个在使用前应该三思的可用属性是布尔属性`loop`。同样，这是不言自明的:根据规范，这个属性将告诉浏览器“到达末尾时，返回到媒体资源的开始处。”

所以，如果你创建了一个网页，它的唯一目的就是让访问者感到厌烦，那么它可能包含这样的代码:

```
<video src="example.webm" width="375" height="280" controls autoplay ↵loop></video>
```

自动播放和无限循环！我们只需要移除本地控件，我们就会有三个最糟糕的实践。

当然，和`autoplay`一样，在某些情况下`loop`也是有用的:例如，一个基于浏览器的游戏，当页面打开时，环境声音和音乐应该连续播放。

### 预加载属性

与前面讨论的两个属性相比，`preload`在许多情况下确实很方便。`preload`属性接受三个值之一:

*   `auto:`表示视频及其相关元数据将在视频播放前开始加载。这样，当用户请求时，浏览器可以更快地开始播放视频。
*   `none:`表示在用户按下播放之前，视频不应在后台加载。
*   `metadata:`与 none 类似，只是与视频相关的任何元数据(例如，其尺寸、持续时间等)都可以预加载，即使视频本身不会预加载。

在被省略的情况下，`preload`属性没有规范定义的默认值；每个浏览器决定这三个值中的哪一个应该是默认状态。这是有意义的，因为它允许连接良好的桌面浏览器自动预加载视频和/或元数据，没有真正的负面影响；然而，它允许移动浏览器默认使用`metadata`或
`none`，因为许多移动用户的带宽有限，他们更愿意选择是否下载视频。

### `poster`属性

当您试图在网上观看视频时，通常会显示视频的单个帧，以提供其内容的预告。`poster`属性使得选择这样一个玩笑变得很容易。这个属性类似于`src`，会通过 URL 的方式指向服务器上的一个图像文件。

下面是定义了`poster`属性的`video`元素的外观:

```
<video src="example.webm" width="375" height="280" controls ↵poster="teaser.jpg"></video>
```

如果省略了`poster`属性，默认的“海报”将是视频的第一帧，一旦加载就会显示出来。

### `muted`属性

`muted`属性是一个布尔值，它控制`video`元素的音轨的默认状态。

添加此属性将导致视频的音频轨道默认为静音，可能会覆盖任何用户偏好设置。这将
只控制元素的默认状态——与
控件或 JavaScript 交互的用户可以改变它。

这里它被添加到我们的`video`元素中:

```
<video src="example.webm" width="375" height="280" poster= ↵"teaser.jpg" muted></video>
```

在以前版本的 HTML5 规范中，有一个名为`audio`的属性，其值为`muted`。新的`muted`属性取代了现在已经过时的`audio`属性。

### 增加对多种视频格式的支持

正如我们已经讨论过的，目前没有使用单一容器格式来提供视频的选项，尽管这确实是拥有`video`元素背后的想法，并且我们希望在不久的将来能够实现。为了包含多种视频格式，`video`元素允许定义`source`元素，这样您就可以允许每个浏览器使用自己选择的格式显示视频。这些元素的功能与`video`元素上的`src`属性相同，所以如果您提供的是`source`元素，就没有必要为`video`元素指定一个`src`。

为了获得完整的浏览器支持，我们将在这里声明我们的`source`元素:

```
<source src="example.mp4" type="video/mp4"> 
<source src="example.webm" type="video/webm"> 
<source src="example.ogv" type="video/ogg">
```

`source`元素(奇怪的是)接受一个指定视频文件位置的`src`属性。它还接受一个`type`属性，指定被请求资源的容器格式。后一个属性使浏览器能够确定它是否可以播放所讨论的文件，从而防止它不必要地下载不支持的格式。

`type`属性还允许指定编解码器参数，该参数为所请求的文件定义视频和音频编解码器。下面是我们的`source`元素在指定编解码器下的外观:

您会注意到,`type`属性的语法已经稍微做了修改，以适应容器和编解码器值。值周围的双引号已更改为单引号，并且专门为编解码器包含了另一组嵌套的双引号。

乍一看，这可能有点令人困惑，但是在大多数情况下，一旦你有了一套对视频进行编码的方法(我们将在本章后面讲到)，你就只是复制和粘贴那些值。重要的一点是，您为指定的文件定义了正确的值，以确保浏览器可以确定它可以播放哪个(如果有的话)文件。

#### 注意:你需要哪种格式？

根据您网站的目标受众，您可能不需要三个`source`元素来获得完整的浏览器支持。对视频和音频编解码器和容器的支持非常好，您可能只需要一两个组合。为了帮助您决定使用哪种格式，请务必查看我可以使用的[上的最新浏览器支持信息。](http://caniuse.com/)

### 来源订单

三个`source`元素作为`video`元素的子元素，用于呈现 HTML 的浏览器将选择它识别的任何容器/编解码器格式——只下载它需要的资源，忽略其他的。声明了三种文件格式后，我们的代码将如下所示:

```
<video width="375" height="280" poster="teaser.jpg" audio="muted"> <source src="example.mp4" type='video/mp4; codecs="avc1.42E01E, ↵mp4a.40.2"'> <source src="example.webm" type='video/webm; codecs="vp8, ↵vorbis"'> <source src="example.ogv" type='video/ogg; codecs="theora, ↵vorbis"'> </video>
```

您会注意到我们的代码现在在`video`元素上没有了`src`属性。如上所述，包含它是多余的，会覆盖任何在`source`元素中定义的视频文件，所以在这种情况下有必要省略它。

### 不支持 HTML5 视频的浏览器怎么办？

我们在`video`元素中包含的三个`source`元素将覆盖所有现代浏览器，但我们还不能确保我们的视频可以在旧浏览器上播放。如前所述，可能仍有很大比例的用户使用不支持 HTML5 视频的浏览器。这些用户中的大多数使用的是版本 9 之前的 Internet Explorer。

为了与优雅降级的原则保持一致，
`video`元素的设计使得老式的
浏览器可以通过其他方式访问视频。任何浏览器
不能识别`video`元素将会简单地
忽略它，连同它的`source`子元素。但是如果
`video`元素包含浏览器
识别为有效 HTML 的内容，它将读取并显示该内容
。

我们能为那些不支持的浏览器提供什么样的内容？[据 Adobe 称，](http://www.adobe.com/ca/products/flashruntimes/statistics.html)超过 10 亿的桌面用户在他们的系统上安装了 Flash Player 插件。这些 Flash 插件的大多数实例都是版本 9 或更高版本，支持 MPEG-4 视频容器格式。考虑到这一点，为了允许 Internet Explorer 8 和更早版本(以及其他不支持 HTML5 视频的旧浏览器)播放我们的视频，我们可以声明一个嵌入式 Flash 视频作为后备。以下是*html 5 先驱报*上视频的完整代码，包括 Flash 回退代码:

```
<video width="375" height="280" poster="teaser.jpg" audio="muted">
  <source src="example.mp4" type='video/mp4; codecs="avc1.42E01E, 
↵mp4a.40.2"'>
  <source src="example.webm" type='video/webm; codecs="vp8, 
↵vorbis"'>
  <source src="example.ogv" type='video/ogg; codecs="theora, 
↵vorbis"'>
  <!-- fallback to Flash: -->
  <object width="375" height="280" type="application/x-shockwave-
↵flash" data="mediaplayer-5.5/player.swf">
    <param name="movie" value="mediaplayer-5.5/player.swf">
    <param name="allowFullScreen" value="true">
    <param name="wmode" value="transparent">
    <param name="flashvars" value="controlbar=over&amp;image=images/
↵teaser.jpg&amp;file=example.mp4">
    <!-- fallback image -->
    <img src="teaser.jpg" width="375" height="280" alt="" title="No 
↵video playback capabilities">
  </object>
</video>
```

我们将跳过这个新添加的代码如何工作的所有细节(毕竟，这不是一本 Flash book！)，但是对于我们的标记中的这一附加内容，有几点需要注意:

*   `object`元素上的`width`和`height`属性应该与`video`元素上的属性相同。
*   为了播放这个文件，我们使用了 LongTail Video 的开源的 JW 播放器[，](http://www.longtailvideo.com/players/jw-flv-player/)，这是一个非商业用途的免费播放器，但是你可以使用任何你喜欢的视频播放器。
*   Flash 视频代码有自己的后备，即当 Flash 视频代码无法工作时显示的图像文件。
*   第四个 param 元素定义了要使用的文件(example.mp4)。如前所述，Flash player 的大多数实例现在都支持使用 MPEG-4 容器格式的视频回放，因此没有必要编码另一种视频格式。
*   规范指示支持 HTML5 视频的 HTML5 浏览器忽略视频元素中不是源标签的任何内容，因此在所有浏览器中回退都是安全的。

除了 Flash 后退内容之外，您还可以提供一个可选的下载视频链接，允许用户访问视频的本地副本并在闲暇时观看。这将确保没有人没有办法观看视频。

这里要提到的最后一点是，就像额外的`source`元素一样，你的网站上可能没有来自没有 HTML5 视频支持的浏览器的访问者，或者你可能不关心使用旧浏览器的小比例。在这两种情况下，您可以很容易地省略 Flash 回退内容，从而简化代码。

## 分享这篇文章