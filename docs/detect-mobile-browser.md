# JavaScript 检测移动浏览器类型

> 原文：<https://www.sitepoint.com/detect-mobile-browser/>

JavaScript 代码片段，用于检测用户是否正在使用移动设备查看网站以及他们使用的移动浏览器。检测浏览器版本很有帮助，因为现在有数百种移动设备，但浏览器的数量却没有这么多。

这里有一个 JavaScript 函数可以帮助你。

```
// Browser Detection Javascript
function whichBrs() {
	var agt=navigator.userAgent.toLowerCase();
	if (agt.indexOf("opera") != -1) return 'Opera';
	if (agt.indexOf("staroffice") != -1) return 'Star Office';
	if (agt.indexOf("webtv") != -1) return 'WebTV';
	if (agt.indexOf("beonex") != -1) return 'Beonex';
	if (agt.indexOf("chimera") != -1) return 'Chimera';
	if (agt.indexOf("netpositive") != -1) return 'NetPositive';
	if (agt.indexOf("phoenix") != -1) return 'Phoenix';
	if (agt.indexOf("firefox") != -1) return 'Firefox';
	if (agt.indexOf("safari") != -1) return 'Safari';
	if (agt.indexOf("skipstone") != -1) return 'SkipStone';
	if (agt.indexOf("msie") != -1) return 'Internet Explorer';
	if (agt.indexOf("netscape") != -1) return 'Netscape';
	if (agt.indexOf("mozilla/5.0") != -1) return 'Mozilla';
	if (agt.indexOf('/') != -1) {
		if (agt.substr(0,agt.indexOf('/')) != 'mozilla') {
			return navigator.userAgent.substr(0,agt.indexOf('/'));}
		else return 'Netscape';} else if (agt.indexOf(' ') != -1)
	return navigator.userAgent.substr(0,agt.indexOf(' '));
	else return navigator.userAgent;
}
```

## 分享这篇文章