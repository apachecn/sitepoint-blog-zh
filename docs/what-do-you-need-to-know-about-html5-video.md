# 关于 HTML5 视频你需要知道什么

> 原文：<https://www.sitepoint.com/what-do-you-need-to-know-about-html5-video/>

HTML5 不断以革命性的网络功能震撼互联网社区。HTML5 的关键增强之一是高级视频支持。有了新的 HTML5 标准，用户不需要任何特殊的插件就可以在 PC 或任何便携式设备上的网络浏览器中播放视频。尽管 HTML5 标准越来越受欢迎，但大多数 web 开发人员和设计人员不愿意迁移到新的标记语言，可能是因为缺少完整的 HTML5 规范或缺少关于 HTML5 编码的实用信息。在这篇文章中，我想深入了解 HTML5 视频元素，并用简单的英语解释它的要点。

## <video>标签与<object>标签</object></video>

标签引用了网页中的嵌入元素:音频、视频、Java applet、ActiveX、PDF 或 Flash。这个标签的标记相当隐晦。因此，如果您想要将 Flash 视频嵌入到网页中，您必须插入以下代码:

```
<object id=0 type="application/x-shockwave-flash" data=player_flv_maxi.swf width=420 height=240
<param name="movie" value=player_flv_maxi.swf />
<param name="wmode" value="opaque" />
<param name="allowFullScreen" value="true" />
<param name="allowScriptAccess" value="sameDomain" />
<param name="quality" value="high" />
<param name="menu" value="true" />
<param name="autoplay" value="false" />
<param name="autoload" value="false" />
<param name="FlashVars" value="flv=Wildlife.flv&width=420&height=240&autoplay=0&autoload=0&buffer=5&buffermessage=&playercolor=464646&loadingcolor=999898&buttoncolor=ffffff&buttonovercolor=dddcdc&slidercolor=ffffff&sliderovercolor=dddcdc&showvolume=1&showfullscreen=1&playeralpha=100&title=Wildlife.flv&margin=0&buffershowbg=0" />

```

要玩这个游戏，用户不能没有一个 Flash player 插件，该插件可用于除谷歌 Chrome 之外的所有现代浏览器，因为播放器已经预装在浏览器中。

HTML5 引入了新的<video>标签，取代了以前的用于 Flash 视频嵌入的<object>标签。使用 HTML5 <video>标签的好处有很多。它不仅带来了视频代码的极简主义和简单性，还带来了搜索机器人正确索引你的视频文件的便利。网站开发人员可以像添加图片一样在页面上添加视频，因为 HTML5 中的</video> <video>所需的基本标记非常简单明了:</video></object></video>

```
</video><video width="320" height="240" controls autoplay poster="video.jpg">
 <source src="movie.mp4" type="video/mp4" />
 <source src="movie.ogg" type="video/ogg" />
 Your browser does not support the video tag.
</video>
```

这样的代码甚至可以由不精通技术的专家轻松编写。用最简单的方法，代码可以简化为:`<video src="movie.mp4" controls width="320" height="480"></video>`

## 视频属性

如您所见，<video>标签的使用就像 HTML 中的任何其他标签一样。在开始和结束标签之间，你可以放置不同的属性来得到你想要的视频播放器。</video>

一些属性是布尔型的(如控制、循环、静音)，不需要值。因此，通过包含或省略它们，可以分别设置值“真”或“假”。

HTML5 专家还建议在视频标签中插入下面一行:
视频没有播放？`<a href="pics/video.mp4">` `Download the file` `取而代之。`

 `## 控制

支持 HTML5 视频的浏览器已经内置了视频播放器。它们都包括一套标准的控件:播放、暂停、搜索、音量。然而，每个浏览器都为视频播放器提供了自己的外观:从 Chrome 和 IE 的最小化方法到 Firefox、Opera 和 Safari 的更精细的控制。

如果你想在所有浏览器中保持相同的控件，或者将播放器与我们的网站设计集成，你可以从头开始创建我们自己的控件。以下是一些有用的资源:

*   [VideoJS](http://videojs.com/) 是用 Javascript 和 CSS 构建的 HTML5 视频播放器；*   Sublime Video 是一款时尚的基于云的 HTML5 视频播放器，具有先进的功能集，是一款 paidware*   [Projekktor](http://www.projekktor.com/index.php) 是一款开源的 HTML5 视频播放器，采用纯 JavaScript 构建；在没有原生 H.264 支持的情况下也使用 Flash*   [教程](http://dev.opera.com/articles/view/custom-html5-video-player-with-css3-and-jquery/)“如何用 jQuery 和 CSS3 构建自定义 HTML5 视频播放器”来自 Opera 开发者。

## 视频源

<video>标签允许多个<source>元素以不同格式链接到同一个视频，例如:</video>

```
       <source src='movie.webm' type='video/webm; codecs="vp8.0, vorbis"'/>
       <source src='movie.ogv' type='video/ogg; codecs="theora, vorbis"'/>
       <source src='movie.mp4' type='video/mp4; codecs="avc1.4D401E, mp4a.40.2"'/>
```

如您所见，源标记有两个属性，src 和 type。“type”属性指定 MIME 类型和可能的编解码器列表，这有助于浏览器确定它是否可以解码文件。默认情况下，浏览器将使用第一个识别的格式。所有现代浏览器都支持至少一种 HTML5 视频格式。

## 浏览器支持

有关浏览器支持的最新信息，您可以查看 YouTube HTML5 页面

## 编解码器

关于应该采用哪种视频格式/编解码器的争论由来已久，至今尚未达成共识。因此，在 HTML5 规范草案中，任何对特定编解码器的引用都被删除了。相反，对视频编解码器提出了以下标准:

*   它应该具有良好的压缩、良好的图像质量和低解码处理器使用；*   免版税；*   特色硬件视频解码器

为 HTML5 视频元素建议了三种编解码器:H.264、OGG Theora 和 WebM VP8。以下是它们的优缺点。

## 264

优点
它能提供高质量的视频和小文件。它为低带宽、低 CPU 的移动设备和高带宽、高 CPU 的现代电脑以及介于两者之间的任何设备输出出色的视频。它对互联网终端用户是免费的。

T2:h . 264 的基本压缩机制获得了专利，采用者必须为商业使用向一个名为 MPEG-LA 的授权联盟支付版税。

## OGG Theora

优点
这是一个免版税的编解码器，除了已经免版税许可的原始 VP3 专利之外，不受任何已知专利的限制。

缺点
Theora 生成的高质量视频文件相对较大。另外，很难找到工具来转换成 OGG Theora。

## WebM VP8

**优点**
2010 年，谷歌收购了 VP8 背后的公司 On2，并将视频编解码规范作为开源发布，所有专利免版税。

**缺点**

虽然谷歌声称 WebM 在所有网络编解码器中具有最高的视频质量，但大多数独立来源发现，要么 H.264 略好，要么两者之间的差异很小。问题是很难找到将视频编码到 WebM 的工具。

在视频编解码器问题上达成完全共识之前，web 开发人员必须将视频转换为所有这三种格式。有一些处理 HTML5 视频准备的有用软件:

*   Freemake.com 的免费视频转换器 3.0 是一个 Windows 软件，它可以将桌面和在线视频编码为 H.264、WebM 和 Theora OGG 格式，生成的视频带有 HTML5 视频嵌入示例；*   或者，您可以在此处列出的视频工具的帮助下，接收作为视频转换结果的 HTML5 源文件。

一些网站管理员还将 HTML5

毫无疑问，HTML5 视频如果和 Flash 相比还是有很多好处的。然而，H.264、WebM 和 Theora OGG 是否仍将是视频元素支持的默认视频格式仍有争议。由于 HTML5 视频格式的规范在很大程度上取决于苹果、微软和谷歌公司的决定，整个新标准的未来似乎很模糊，但仍有希望。

## 分享这篇文章`