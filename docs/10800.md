# Web 方向反思，第 3 部分:Bruce Lawson 和 Remy Sharp 的 HTML5

> 原文：<https://www.sitepoint.com/web-directions-reflections-3-html5/>

如果说[移动开发是上个月](https://www.sitepoint.com/web-directions-reflections-part-2-mobile-development-with-john-resig-and-jonathan-stark/) [Web Directions @media](http://atmedia.webdirections.org/) 的热门话题，HTML5 紧随其后…

## 布鲁斯·劳森和帕特里克·劳克:HTML5 结构、语义、风格和性感

Opera 的开放网络传播者 Bruce Lawson 为我们所有人受苦。他在会议的第一天得了喉炎，尽管做了几次勇敢的尝试，还是无法发言。我怀疑他们对微软的展台很满意——布鲁斯没有机会问一些关于 IE9 可疑的 100% HTML5 兼容表的尴尬问题。

帕特里克·劳克(Patrick Lauke)在最后一刻介入，解释为什么 HTML5 可能是我们所有灾难的解决方案。他解释说，像 Ajax 和 Web2.0 一样，营销部门已经取得了控制权。“HTML5”这个术语现在意味着你想要它是什么。(Remy Sharp 还认为 HTML5 对普通的非开发人员来说意味着一切。是标记、JavaScript、CSS、SVG、jQuery 和你的晚餐！)但是，如果扫掉 BS，所有主要厂商都在标准后面，它对 web 应用的支持更好，HTML5 是更 bling 的 HTML！

Patrick 描述了 28 个新结构元素中的一些，并特别关注了 webforms。HTML5 提供了特殊的输入类型，如日期，时间，电子邮件，电话，网址，甚至“模式”,允许您指定自己的正则表达式。复杂的 JavaScript 输入验证的日子屈指可数了。

也许最令人兴奋的 HTML5 元素是`video`和`audio`。视频作为原生对象是有意义的:它提供了键盘可访问性和一个 JavaScriptable-API，因此您可以控制媒体播放并创建自己的控件。不幸的是，对于视频格式仍有一些争论，供应商也有他们自己的偏好。可能有必要以多种格式提供同一视频，直到达成普遍共识:

```
 <video>
	<source src="video.webm" type="video/webm" />
	<source src="video.ogv" type="video/ogg" />
	<source src="video.mp4" type="video/mp4" />
	<!-- Flash-player fallback code -->
</video> 
```

Patrick 还讨论了可脚本化的图像标签 [canvas](https://www.sitepoint.com/canvas-vs-svg-how-to-choose/) 。Canvas 非常适合游戏和特效，但是存在可访问性问题，所以你应该对使用它来制作内容保持警惕。

最后，帕特里克回答了这个问题:HTML5 是一个 Flash 杀手吗？除非你用的是 iPhone/iPad，否则不存在 HTML5 取代 Flash 的问题。HTML5 给了开发者一个选择，在此之前，Flash 可能是唯一的选择。

帕特里克的幻灯片可以在网上找到，你也可以阅读 T2 的布鲁斯·劳森的网站访谈。

## Remy Sharp:带翅膀的浏览器—html 5 API

雷米是最后发言的人之一，他承认他已经调整了他的发言几次，以考虑到在早些时候的会议中提出的话题！这是一个很棒的展示，尽管我没有足够幸运地看到他扔向观众的巧克力蛋。

可脚本化的`video`是第一个话题。您只需要几行 JavaScript 就可以使用 play()和 pause()等方法以及 paused、currentTime、playbackRate 和 currentSrc 等属性来控制媒体回放。还可以将事件与媒体回放同步，这可能会带来非常令人兴奋的可能性。

在讨论了 canvas 和 SVG 的优点之后，Remy 透露了他对 cookies(浏览器的 cookies，而不是巧克力片)的厌恶。Cookie 代码很难记住，它使用一种特殊的日期格式，它们会跨会话泄漏，并且只能通过设置过去的到期日期来删除。网络存储是未来的趋势，浏览器技术，如 T2 的网络 SQL 数据库和 T4 的索引数据库也很有前景。

地理定位是另一个强大的工具，尤其是对于移动设备。对象可以用来获取你的纬度和经度。它还可以检查位置的准确性，如果您希望咖啡店搜索应用程序提供准确的结果，这一点很重要。

WebSockets 是下一个主题。与标准的 HTTP 请求/响应不同，WebSockets 为 JavaScript 提供了直接的服务器通信 API。服务器和客户端可以根据需要发送和接收数据

```
 var conn = new WebSocket("ws://server.com:8000/");

conn.onmessage = function(event) {
	alert("Received message: " + event.data);
}

conn.send("Hello Mr Server!"); 
```

WebSockets 的优点是它们是事件驱动的(没有持续的轮询)和轻量级的(没有 HTTP 头)。WebSockets 可能会彻底改变基于浏览器的应用和服务器，比如已经支持这项技术的 node.js。

尽管这些技术是新的并且支持有限，但是 Remy 提供了 shims 的链接，允许您在所有主流浏览器中使用这些特性。他的[幻灯片可以在网上](http://www.slideshare.net/remy.sharp/browsers-with-wings)或者以 [PDF 格式](http://remysharp.com/downloads/browsers-with-wings.pdf)获得。你也可以阅读[雷米的 SitePoint 访谈](https://www.sitepoint.com/web-directions-media-remy-sharp-interview/)。

在接下来的网络方向反思贴中:安迪·克拉克煮他的设计，道格·谢佩斯向 SVG 致敬…

## 分享这篇文章