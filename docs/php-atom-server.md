# PHP Atom 服务器

> 原文：<https://www.sitepoint.com/php-atom-server/>

在寻找在使用 [XMLHttpReqeust](http://xulplanet.com/references/objref/XMLHttpRequest.html) 时实现了某种超时机制的人时，遇到了 [isolani 的](http://www.isolani.co.uk/blog/) [PHP Atom Server](http://www.isolani.co.uk/blog/atom/PhpAtomApiImplementation) (服务于 [ATOM](http://en.wikipedia.org/wiki/Atom_%28standard%29) XML)，我猜这是他正在工作的[isotopic](http://www.isolani.co.uk/projects/atom/isoTope/)框架的一部分，以及一个 [Javascript ATOM 客户端](http://www.isolani.co.uk/blog/atom/JavascriptAtomApiClientUsingXmlHttpRequest)(使用 XMLHttpRequest)。

很有兴趣看到他在使用 [php://input](http://www.php.net/wrappers.php) 流。事实上，在 PHP 中还有另一种访问原始 POST 数据的方法，它可以在旧版本的 PHP 上工作，使用全局变量$HTTP_RAW_POST_DATA。除非您在 php.ini 中打开了 always_populate_raw_post_data，否则这个全局变量只在 HTTP POST 是在没有内容类型“application/x-www-form-urlencoded”(普通表单的默认内容类型)的情况下*生成的情况下才存在。我相信你也需要声明它或者通过$GLOBALS 数组访问它，不像其他变量如$_GET 和$ _ SERVER*

 `function getRawPost() {
global $HTTP_RAW_POST_DATA;
return $HTTP_RAW_POST_DATA;`

`// or
# return $GLOBALS['HTTP_RAW_POST_DATA'];
}`

但是这并没有让我更接近用 XmlHttpRequest 实现超时；)现在发生了一些奇怪的事情——IE 为同步和异步请求调用 xmlhttprequest . onreadystatechange 回调，但是如果您调用 abort()方法，请准备好 kill-9(Windows 方式)。与此同时，在发出同步请求时，Mozilla 似乎完全忽略了 onreadystatechange，这没有给超时留下太多空间。

沿途一些其他有趣的链接；

[跨浏览器 XMLHttpRequest](http://www.scss.com.au/family/andrew/webdesign/xmlhttprequest/)–为 Opera 提供部分 XMLHttpRequest 实现

非常有趣的实现，使用了一个 XMLHttpRequest 对象池。但似乎仍然没有解答超时之谜。

同样来自 libXmlRequest 作者:[web 应用程序引擎](http://www.whitefrost.com/projects/engine/background.html)——与[脚本服务器](https://www.sitepoint.com/blog/)在同一个球场上。我个人对“太多”的抽象层和过多的 XML 感到厌烦，但也许那就是我——透明是相对的。

【更新】
找错地方了！[克里斯蒂安](http://blog.bitflux.ch/)用 [Livesearch](http://blog.bitflux.ch/livesearch.js) 有了答案——使用 [Window.setTimeout()](http://www.devguru.com/Technologies/ecmascript/quickref/win_settimeout.html)

## 分享这篇文章