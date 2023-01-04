# jQuery 检测移动设备–iPhone iPod iPad

> 原文：<https://www.sitepoint.com/jquery-detect-mobile-devices-iphone-ipod-ipad/>

jQuery 代码片段，用于检测用户是否正在使用移动设备(特别是 iPhone、iPod 或 iPad)查看网站。您可能还希望[检测移动浏览器类型](http://jquery4u.com/mobile/detect-mobile-browser/)。

```
jQuery(document).ready(function($){
	var deviceAgent = navigator.userAgent.toLowerCase();
	var agentID = deviceAgent.match(/(iphone|ipod|ipad)/);
	if (agentID) {

        // mobile code here

	}
});
```

## 分享这篇文章