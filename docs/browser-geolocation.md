# HTML5 浏览器地理定位浏览器支持

> 原文：<https://www.sitepoint.com/browser-geolocation/>

所以今天在我的空闲时间，我想看看不同的浏览器如何处理 HTML 地理定位和谷歌地图。到目前为止，我做过以下浏览器:Firefox 16、Chrome 23、IE9、IE8 和 Safari 5 for Windows。现代浏览器支持它。这是结果。声明一下，我在澳大利亚布里斯班附近。

![support](img/87fad7bcc2dfdca1c4bc7faee73067d3.png "support")

目前，W3C 地理定位 API 受到以下桌面浏览器的支持:

*   火狐 3.5 以上版本
*   Chrome 5.0 以上
*   Safari 5.0 以上版本
*   Opera 10.60+版
*   Internet Explorer 9.0 以上

[在 GitHub 上查看项目](https://github.com/sdeering/html5-browser-geolocation)

## Firefox HTML5 地理定位

![ff1](img/9cd69c3767cb2f36cda9ecfc685113f4.png "ff1")

![ff](img/2e73c83a6110512513b50d3d2edc69d6.png "ff")

## Chrome HTML5 地理定位

![chrome](img/2341b7b0c28f4c560a0327d6d2c99962.png "chrome")

## IE9 HTML5 地理定位

![ie91](img/88e44a7c47fa6d0d1e729d3ec03255d9.png "ie91")

![ie9](img/299d7bebb44e44a364615b5dd08465aa.png "ie9")

## IE8 HTML5 地理定位

![ie8](img/07bfda509fa98fcff51d4cf74155405a.png "ie8")

## Safari (windows) HTML5 地理定位

![](img/6b49794de7dd652bcd1c2d67ced3de81.png "safari1")

![safari(windows)](img/bd7a3a744e1dbae917d4fcb9b9a200c6.png "safari(windows)")

一些有趣的结果，尤其是 IE9 把城市搞错了。我想我以前见过俄罗斯作为一个视口的中心，所以没什么好惊讶的。IE8 永远不会支持这一点，但我认为 windows 版 Safari 会支持。我将很快在 Mac 上测试并公布结果。如果你想测试你的浏览器，你可以在下面这样做。地理定位可以用于其他事情，而不仅仅是在地图上绘制一个点:[参见 W3c 用例](https://www.w3.org/TR/geolocation-API/#usecases_section)获得更多的例子。

## 完整代码列表

```
 <title>Get Geolocation Using HTML5 Demo | jQuery4u</title> 

## 分享这篇文章

```