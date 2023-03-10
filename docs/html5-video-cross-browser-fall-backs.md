# 带 Flash 或 Silverlight 后备的跨浏览器 HTML5 视频

> 原文：<https://www.sitepoint.com/html5-video-cross-browser-fall-backs/>

直到最近，在网页中播放视频文件还是出了名的复杂。用户需要 Flash 或 Silverlight 插件，即使是最简单的 HTML 也是一团乱麻:

```
 <object width="320" height="240">
<param name="movie" value="myvideo.swf" />
<embed src="myvideo.swf" width="320" height="240"></embed>
</object> 
```

很少有 HTML5 特性比原生音频和视频更让开发者兴奋。`<audio>`和`<video>`标签允许你在没有插件的情况下在支持 HTML5 的浏览器中播放媒体文件。这些元素也成为 DOM 的一部分，这样你就可以创建自己的播放器控件，添加字幕，并将 JavaScript 事件与媒体播放同步。另外，基本的 HTML5 代码更容易理解:

```
 <video src="myvideo.mp4" width="320" height="240" /> 
```

当然，web 开发人员的生活从来没有那么简单。尽管 HTML5 规范定义了`<audio>`和`<video>`标签，但它并没有指定特定的编解码器，供应商有自己的选择。对于 IE 和 Safari 来说，H.264 编码的 MP4 文件不会错。Firefox，Chrome 和 Opera 在 Ogg 容器中支持 Theora 或 Vorbis，WebM 将很快推出。

因此，您仍然需要为单个视频编码多个文件。幸运的是，不需要求助于脚本或浏览器检测，因为 HTML5 允许我们定义任意数量的源文件——浏览器将选择它找到的第一个兼容的格式，例如

```
 <video width="320" height="240">
	<source src="myvideo.mp4" type="video/mp4" />
	<source src="myvideo.ogv" type="video/ogg" />
	<source src="myvideo.webm" type="video/webm" />
</video> 
```

如果浏览器不支持`<video>`标签，我们可以回到 Flash 或 Silverlight 版本:

```
 <video width="320" height="240">
	<source src="myvideo.mp4" type="video/mp4" />
	<source src="myvideo.ogv" type="video/ogg" />
	<source src="myvideo.webm" type="video/webm" />

	<object width="320" height="240">
	<param name="movie" value="myvideo.swf" />
	<embed src="myvideo.swf" width="320" height="240"></embed>
	</object>
</video> 
```

或者，我们可以显示一个有用的错误消息或显示一个图像——也许是一个动画 PNG 或 GIF！

```
 <video width="320" height="240">
	<source src="myvideo.mp4" type="video/mp4" />
	<source src="myvideo.ogv" type="video/ogg" />
	<source src="myvideo.webm" type="video/webm" />

	<p>Sorry, your browser cannot play this video.</p>
	<img src="videofail.png" />

</video> 
```

这个工具是在 HTML5 中特别设计的，所以视频可以在带有插件的传统浏览器上显示。HTML5 与 Flash 或 Silverlight 相处得非常愉快— [它不会很快杀死它们](https://www.sitepoint.com/html5-kills-silverlight/)！

## 分享这篇文章