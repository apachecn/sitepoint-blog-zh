# 全面了解 HTML5 的 track 元素

> 原文：<https://www.sitepoint.com/comprehensive-look-html5-track-element/>

如今，视频和音频在网络上已经司空见惯。互联网不再仅仅是基于文本的了。视频贡献了[几乎 40%的搜索结果](https://www.youtube.com/watch?v=ZHMKMv213f0#t=42)。从用户的角度来看，多媒体是一种互动和有趣的会话。另一方面，对于开发者来说，增强体验是一件艰巨的事情，直到 HTML5 的`<track>`元素来拯救。

读完这篇文章后，你会明白如何给你的媒体文件添加像*字幕*这样的定时媒体轨道。此外，您将了解这些如何增加网页的 SEO 价值。在继续之前，你可能想浏览一下[关于 HTML 视频和音频的介绍](https://www.sitepoint.com/web-foundations/introduction-audio-video-html5/)，解释容器格式、编解码器、标记和基础知识。

## `<track>`元素

`<track>`元素定义了你想和播放的媒体文件一起显示的任何文本。文本可能包括`subtitles`、`captions`、`descriptions`、`chapters`或`metadata`。

换句话说，`<track>`元素允许您指定额外的时间同步文本资源，这些资源与`audio`元素的音频文件时间轴或`video`元素的视频文件时间轴对齐。

track 元素是一个空元素，即它不能有结束标记(意味着它是一个 void 元素)。它必须包含在`<video>`或`<audio>`标签中。此外，如果在视频或音频标签中有任何`<source>`元素，那么`<track>`元素应该出现在`<source>`元素之后。

例如:

```
<video width="640" height="320" controls>
  <source src="some_video.mp4" type="video/mp4">
  <source src="some_video.ogg" type="video/ogg">
  <track src="some_video_subtitles.srt"
         kind="subtitles"
         srclang="en"
         label="English_subs">
</video>
```

## `src`属性

该属性指定包含轨道数据的文本文件的源地址，自然是一个必需的属性。该值应该是绝对或相对 URL。这意味着文件需要放在网络服务器上；不能从*文件://* URL 使用 track 元素。

例如:

```
<track src="video_captions.srt">
```

## `srclang`属性

`srclang`定义时间跟踪数据的语言。如果`kind`属性被设置为值`subtitles`，则必须包含该属性(更多内容见下文)。属性的值必须是有效的 BCP 语言标签。例如，值`hi`表示*印地语*，而`en`用于表示*英语*。大约有 8000 个可用的语言子标签。

```
<track src="video_subtitles.srt" kind="subtitles" srclang="en">
```

上面的示例代码将文本定时轨道文件的语言指定为英语。

## `kind`属性

这个属性定义了我们想要添加的轨迹类型。它可能包含多个值中的一个，下面将对每个值进行解释。

**字幕**
这些通常是视频或音频文件中正在播放的对话的翻译。当用户无法理解音轨中正在播放的语言，但用户可以阅读以首选语言编写的对话时，这些就派上了用场。必须指定源的语言。这是通过向`srclang`属性添加适当的值来实现的:

```
<track src="video_subtitles.srt" kind="subtitles" srclang="en">
```

**字幕** :
字幕是伴随正在播放的视频的简短描述。在我们想要通知用户一些相关信息的情况下，或者甚至当声音不清楚或听不见时，这些是合适的。一个简单的例子。

```
<track src="video_captions.srt" kind="captions">
```

**描述** :
顾名思义，这些用来*描述*媒体内容。当用户是盲人或视频不可用或模糊时，这是合适的。标记为描述的定时轨道通常被合成为单独的音频轨道。例如:

```
<track src="video_descriptions.srt" kind="descriptions">
```

**元数据**
这是为提供元数据内容的定时曲目设计的。用户代理通常不显示它。元数据值应该由 JavaScript 之类的脚本使用。这里有一个例子:

```
<track src="video_metadata.srt" kind="metadata">
```

**章节**
这代表章节标题，当用户导航媒体资源时使用。标记为章节的音轨通常与用户代理界面上的交互式列表一起显示。

```
<track src="video_chapters.srt" kind="chapters">
```

## `label`属性

该属性用于定义视频/音频文件中包含的文本轨道的标题。它由浏览器在列出可用的文本轨道时使用，并且是文本轨道的用户可读标签。

如果将`label`属性添加到`<track>`元素中，`label`属性的值不能为空值，否则代码将无法验证。该值必须是字符串。如果该属性不存在，浏览器将分配一个默认值，如“无标题”。

```
<track src="video_subtitles.srt"
       kind="subtitles"
       srclang="en"
       label="English_subtitles">
```

在上面的例子中，我们可以看到,`label`属性包含一个值“English _ subtitles”。

## `default`属性

这是一个布尔属性，用于将一个轨迹标识为默认轨迹。显然，`default`只能包含一个`<track>`元素。如果用户的偏好没有指示另一个轨道更合适，则可以启用它。

以下示例说明了在以三种语言(印地语、英语和日语)指定的字幕中，印地语字幕被指示作为默认曲目播放:

```
<track kind="subtitles" src="video_subtitles_hi.srt" srclang="hi" default>
<track kind="subtitles" src="video_subtitles_en.srt" srclang="en">
<track kind="subtitles" src="video_subtitles_ja.srt" srclang="ja">
```

现在我们已经检查了大多数可以与`<track>`元素一起使用的相关属性，下面显示了一个总结了`<track>`以及`video`和`source`元素用法的完整示例:

```
<video src="sample.ogv">
  <source src="some_video.mp4" type="video/mp4">
  <source src="some_video.ogg" type="video/ogg">
   <track kind="captions" src="video_captions.srt" srclang="en">
   <track kind="descriptions" src="video_desciptions.srt" srclang="en">
   <track kind="chapters" src="video_chapters.srt" srclang="en">
   <track kind="subtitles" src="video_subtitles_en.srt" srclang="en" default>
   <track kind="subtitles" src="video_subtitles_oz.srt" srclang="oz">
   <track kind="metadata" src="video_metadata1.srt" srclang="en" label="Metadata 1">
   <track kind="metadata" src="video_metadata2.srt" srclang="en" label="Metadata 2">
</video>
```

## 媒体跟踪的 SEO 好处

`<track>`元素已经完全打开了视频搜索引擎优化(SEO)的大门，并允许以更具成本效益的方式让搜索引擎更好地理解您的视频文件。

搜索引擎变得越来越多媒体意识和更多的信息，你可以附加到你的文件意味着更好的针对性和更高的流量，将转化为你的收入。

一些关键的 SEO 优势是:

*   **改善您的搜索状态**:搜索引擎在搜索时会抓取与视频相关的任何文本内容。
*   深度链接:搜索引擎返回的搜索结果指向与时间代码相关的视频的特定部分。
*   **关联内容**:文本文件可以很容易地合并到同一页面的关联文本内容中。
*   **可访问性和 UX** :字幕和说明文字提高了残疾人的可用性和可访问性。
*   **搜索结果中的缩略图**:一个视频页面的优势是在搜索结果中显示为一个丰富的片段，带有缩略图，可以提高点击率。

更多关于视频文字和字幕的 SEO 好处的信息可以在这篇文章中找到。

## 对`<track>`的浏览器支持

总的来说，`<track>`元素[拥有出色的浏览器支持](http://caniuse.com/#search=track):

*   铬
*   Firefox 31+
*   IE10+
*   Safari 6+版本
*   歌剧 15+

## 演示和总结

要查看`<track>`元素的作用，这里有两个链接:

*   [IE 的视频字幕演示页面](http://ie.microsoft.com/testdrive/Graphics/VideoCaptions/)
*   HTML5 演示中的跟踪示例

`<track>`元素标准化向媒体文件添加轨道数据。它支持使用链接到媒体播放的动态内容，这反过来增加了音频和视频元素的价值，并有可能带来 SEO 好处。

## 分享这篇文章