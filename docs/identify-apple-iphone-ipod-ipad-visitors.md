# 如何识别苹果 iPhone、iPod 或 iPad 访问者

> 原文：<https://www.sitepoint.com/identify-apple-iphone-ipod-ipad-visitors/>

你不能逃避他们。去任何科技爱好者聚集的地方，你都会发现大量的苹果电器。不管是微软会议还是谷歌聚会，iPhone 无处不在。(据报道，10%的微软员工拥有这些设备，这让高层管理人员非常反感！[iPad 最近推出](https://www.sitepoint.com/ipad-fever-hits-the-us/)，我预计它会变得几乎一样受欢迎。

## 苹果代理商

从您的网站请求内容的设备(通常)会传递一个用户代理字符串。其中包含名称、操作系统、浏览器版本和渲染引擎等信息。Apple 的小工具通过了以下用户代理，尽管您可能会发现版本号的细微变化:

**iPhone:**

```
 Mozilla/5.0 (iPhone; U; CPU like Mac OS X; en) AppleWebKit/420+ (KHTML, like Gecko) Version/3.0 Mobile/1A543a Safari/419.3 
```

**iPod:**

```
 Mozilla/5.0 (iPod; U; CPU like Mac OS X; en) AppleWebKit/420.1 (KHTML, like Gecko) Version/3.0 Mobile/3A101a Safari/419.3 
```

**iPad:**

```
 Mozilla/5.0 (iPad; U; CPU OS 3_2 like Mac OS X; en-us) AppleWebKit/531.21.10 (KHTML, like Gecko) version/4.0.4 Mobile/7B367 Safari/531.21.10 
```

## 探知苹果

可以在服务器或客户端上分析用户代理字符串，以确定哪个设备正在访问您的站点。也就是说，请记住[浏览器嗅探发臭](https://www.sitepoint.com/why-browser-sniffing-stinks/):创建一个可以在多种浏览器上运行的网站比维护多个不同版本要好。但是，您可以使用这些代码来整理统计数据，或者创建与 iPhone 界面相匹配的 web 应用程序的替代视图。

事不宜迟，下面是一些 JavaScript 检测代码:

```
 // Apple detection object
var Apple = {};
Apple.UA = navigator.userAgent;
Apple.Device = false;
Apple.Types = ["iPhone", "iPod", "iPad"];
for (var d = 0; d < Apple.Types.length; d++) {
	var t = Apple.Types[d];
	Apple[t] = !!Apple.UA.match(new RegExp(t, "i"));
	Apple.Device = Apple.Device || Apple[t];
}

// is this an Apple device?
alert(
	"Apple device? " + Apple.Device +
	"niPhone? " + Apple.iPhone +
	"niPod? " + Apple.iPod +
	"niPad? " + Apple.iPad
); 
```

可以用 PHP 开发类似的代码用于服务器端检测:

```
 // Apple detection array
$Apple = array();
$Apple['UA'] = $_SERVER['HTTP_USER_AGENT'];
$Apple['Device'] = false;
$Apple['Types'] = array('iPhone', 'iPod', 'iPad');
foreach ($Apple['Types'] as $d => $t) {
	$Apple[$t] = (strpos($Apple['UA'], $t) !== false);
	$Apple['Device'] |= $Apple[$t];
}

// is this an Apple device?
echo
	"<p>Apple device? ", ($Apple['Device'] ? 'true' : 'false'),
	"</p>n<p>iPhone? ", ($Apple['iPhone'] ? 'true' : 'false'),
	"</p>n<p>iPod? ", ($Apple['iPod'] ? 'true' : 'false'),
	"</p>n<p>iPad? ", ($Apple['iPad'] ? 'true' : 'false'),
	'</p>'; 
```

或者，您可以使用 Apache 的。htaccess 文件，以重定向到另一个 URL 或子域上的 Apple 网站的特定版本，例如

```
 RewriteCond %{HTTP_USER_AGENT} ^.*(iP.*$
RewriteRule ^(.*)$ http://apple.site.com [R=301] 
```

有用的东西，但一个重要的问题仍然存在:我们应该为这些流行的设备开发不同版本的网站吗？

## 分享这篇文章