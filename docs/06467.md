# 如何在你的页面上添加无脂肪社交按钮

> 原文：<https://www.sitepoint.com/social-media-button-links/>

抱歉，脸书，但是在我的页面上增加 270Kb 的代码来显示一个小小的“喜欢”按钮似乎有点过分了。正如我在最近的文章中所讨论的，社交分享的隐藏成本，添加脸书、Twitter、Google+和 LinkedIn 按钮会使你的页面膨胀，降低网站的响应速度，并带来不必要的安全风险。

不需要那样。大多数社交网络都提供基于 URL 的分享按钮，这些按钮不会增加页面的权重，也不需要第三方 JavaScript。它们没有被广泛宣传，但使用起来并不困难。

## 编码您的 URL

所有这些链接都将你的网页地址的 URL 编码表示传递给社交网络。例如，使用 PHP:

```
$sURL = urlencode(
			'http' .
			($_SERVER['SERVER_PORT'] == 443 ? 's' : '') . '://' .
			$_SERVER['HTTP_HOST'] .
			$_SERVER['PHP_SELF']
		);
```

根据社交网络的不同，你可能还需要 URL 编码的页面标题、描述和标签列表(用逗号分隔，没有前导散列)。

## 脸谱网

脸书共享很简单。你提供网址，脸书获取标题，描述和缩略图:

网址:https://www.facebook.com/sharer/sharer.php

基本参数:

| **u** | :页面 URL |

示例:

```
https://www.facebook.com/sharer/sharer.php?u=http%3A%2F%2Fsitepoint.com%2F
```

更多资源请访问[https://developers . Facebook . com/docs/reference/plugins/share-links/](https://developers.facebook.com/docs/reference/plugins/share-links/)

## 推特

公平地说，Twitter 一直提供一个完整记录的 [Web Intents](https://dev.twitter.com/docs/intents) 系统，允许你发布页面链接。

网址:https://twitter.com/intent/tweet

基本参数:

| **网址** | :页面 URL |
| **正文** | : optional text |
| **标签** | :一组以逗号分隔的 hashtags |

示例:

```
https://twitter.com/intent/tweet?url=http%3A%2F%2Fsitepoint.com%2F&text=SitePoint&hashtags=web,development
```

更多资源可在[https://dev.twitter.com/docs/intents](https://dev.twitter.com/docs/intents)获得

## 谷歌+

谷歌分享链接类似于脸书:

网址:https://plus.google.com/share

基本参数:

| **网址** | :页面 URL |

示例:

```
https://plus.google.com/share?url=http%3A%2F%2Fsitepoint.com%2F
```

更多资源可在[https://developers.google.com/+/web/share/](https://developers.google.com/+/web/share/)获得

## 商务化人际关系网

LinkedIn 分享链接与脸书类似:

网址:http://www.linkedin.com/shareArticle

基本参数:

| **迷你** | :必须设置为“真” |
| **网址** | :页面 URL |
| **来源** | :公司/命名来源(最多 200 个字符) |
| **title** | :文章标题(最多 200 个字符) |
| **总结** | :简短描述(最多 256 个字符) |

示例:

```
http://www.linkedin.com/shareArticle?mini=true&url=http%3A%2F%2Fsitepoint.com%2F&title=SitePoint
```

更多资源可在[https://developer.linkedin.com/documents/share-linkedin](https://developer.linkedin.com/documents/share-linkedin)获得

## 渐进式弹出窗口

这些链接可以在所有浏览器中使用。然而，他们会把你从页面上带走，这对最热切的社交媒体经理来说太远了。

让我们给我们的社交链接一个“share”类，然后在页面末尾添加一个小的自运行 JavaScript 函数，它拦截所有的点击，并在一个小的弹出窗口中打开它们。(我同意弹出窗口是邪恶的，这感觉像是 1999 年的编码，但这是大多数社交按钮的做法。)

```
<script>
// create social networking pop-ups
(function() {
	// link selector and pop-up window size
	var Config = {
		Link: "a.share",
		Width: 500,
		Height: 500
	};

	// add handler links
	var slink = document.querySelectorAll(Config.Link);
	for (var a = 0; a < slink.length; a++) {
		slink[a].onclick = PopupHandler;
	}

	// create popup
	function PopupHandler(e) {

		e = (e ? e : window.event);
		var t = (e.target ? e.target : e.srcElement);

		// popup position
		var
			px = Math.floor(((screen.availWidth || 1024) - Config.Width) / 2),
			py = Math.floor(((screen.availHeight || 700) - Config.Height) / 2);

		// open popup
		var popup = window.open(t.href, "social", 
			"width="+Config.Width+",height="+Config.Height+
			",left="+px+",top="+py+
			",location=0,menubar=0,toolbar=0,status=0,scrollbars=1,resizable=1");
		if (popup) {
			popup.focus();
			if (e.preventDefault) e.preventDefault();
			e.returnValue = false;
		}

		return !!popup;
	}

}());
</script>
```

该脚本可以在所有浏览器中运行，包括 IE8 和更高版本。如果失败，浏览器将简单地在主窗口中打开社交网络页面。

[**查看演示页面……**](https://blogs.sitepointstatic.com/examples/tech/social-buttons/index.html)

结果是几个链接和一个少于 900 个字符的可选脚本来处理弹出窗口。谁需要 [580Kb 的社交网络代码](/social-sharing-hidden-costs/)？

## 分享这篇文章