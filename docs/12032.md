# Mozilla Jetpack:增加推力的 Firefox 扩展

> 原文：<https://www.sitepoint.com/mozilla-jetpack-firefox-extensions/>

Jetpack 是 Mozilla 的一个新实验项目，它可能会彻底改变 Firefox 浏览器扩展的构建和部署方式。

自从 Firefox 诞生以来，扩展就一直是它的重要组成部分。Mozilla 让开发人员可以轻松地连接到 chrome API，并以以前没有设想过的方式增强浏览器。还记得在引入第三方工具如 [Firebug](https://addons.mozilla.org/en-US/firefox/addon/1843) 和 [Web Developer Toolbar](https://addons.mozilla.org/en-US/firefox/addon/60) 之前，客户端编码有多难吗？

今天，热情的开发人员社区提供了超过 12，000 个扩展。随着超过 10 亿次安装，火狐插件的力量无疑是浏览器成功的最大因素之一。

Jetpack 的主要目标是使扩展开发和交付更加容易:

*   HTML、CSS 和 JavaScript 将是唯一需要的工具。如果您可以为 web 编写代码，您可以创建一个 Jetpack 小部件。
*   扩展将被流式传输并从 URL 访问。
*   您可以使用 [Bespin](http://bespin.mozilla.com/) 直接在浏览器中开发代码。
*   安装和调试在不重启的情况下进行**，使用 Firebug 等常用工具。**
*   提供了轻量级和版本化的 API。
*   将提供一组可扩展的工具包和 API 库，如 jQuery、Google Maps、Twitter 和 Delicious。
*   一个 about:jetpack 页面显示了已安装的扩展、源代码、内存使用和其他有用的工具。

Jetpack 是一个标准的 Firefox 插件，可以从[项目页面](https://jetpack.mozillalabs.com/)安装。然后，您可以导航到包含 Jetpack 扩展的`link`的页面:

```
 <link rel="jetpack" href="extension-name.js" name="Jetpack Extension Name" /> 
```

将出现一个通知栏，要求确认安装，这是即时的，不需要重新启动浏览器。下面的小部件添加了一个“Boom！”链接到状态栏，当点击时，显示一个“蓬勃发展”的通知，并将活动标签的页面背景淡入红色(使用 jQuery):

```
 jetpack.statusBar.append({
	html: "Boom<i>!</i>",
		width: 45,
		onReady: function(widget){
			$(widget).click(function(){
				jetpack.notifications.show( "Booming!" );
				$(jetpack.tabs.focused.contentDocument)
					.find("body")
					.css({backgroundColor:"red"})
					.animate({opacity:.5});
		});
	}
}); 
```

喷气背包项目负责人阿萨·拉斯金提供了更多关于喷气背包和项目目标的信息:

<object width="400" height="290"><param name="allowfullscreen" value="true"><param name="allowscriptaccess" value="always"><param name="movie" value="https://vimeo.com/moogaloop.swf?clip_id=4752576&amp;server=vimeo.com&amp;show_title=1&amp;show_byline=1&amp;show_portrait=0&amp;color=&amp;fullscreen=1"><embed src="https://vimeo.com/moogaloop.swf?clip_id=4752576&amp;server=vimeo.com&amp;show_title=1&amp;show_byline=1&amp;show_portrait=0&amp;color=&amp;fullscreen=1" type="application/x-shockwave-flash" allowfullscreen="" allowscriptaccess="always" width="400" height="290"></object>

[Mozilla 实验室 Jetpack 简介和教程](https://vimeo.com/4752576)

另请参见:

*   [Mozilla 实验室 Jetpack 项目页面和扩展下载](https://jetpack.mozillalabs.com/)
*   [第三方 Jetpack 驱动的扩展](https://wiki.mozilla.org/Labs/Jetpack/In_The_Wild)
*   [Jetpack 开发教程](https://jetpack.mozillalabs.com/tutorial.html)
*   [Jetpack API 参考](https://jetpack.mozillalabs.com/api.html)

该项目仍处于早期测试阶段，所以通常的安装警告适用。

无法预测 Jetpack 会变得多流行或多强大。然而，开发和安装的便利性有可能使 Firefox 插件的数量呈指数级增长。它可能最终取代当前的扩展系统。

你觉得 Jetpack 怎么样？这是 Firefox 扩展的未来吗？还是会将开发分成两个系统？

## 分享这篇文章