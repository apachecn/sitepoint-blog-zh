# 如何使用字体加载器提高页面性能

> 原文：<https://www.sitepoint.com/improve-page-performance-font-loader/>

非常感谢杰森·帕门塔尔给我写这篇文章的灵感。否则我绝不会考虑这个问题！

你上一次在网页中使用 Arial、Times New Roman、Helvetica 或… *战栗* …漫画字体是什么时候？网络字体花了太长时间才出现，但一旦出现，我们就再也不会回头。字体很有趣，(通常)免费且实现快速:

```
@import url(http://fonts.googleapis.com/css?family=Ubuntu:300,300italic,400,400italic,500,500italic,700,700italic);
```

然后，您可以在整个页面中使用该字体，例如

```
body {
	font-family: Ubunutu, sans-serif;
	font-weight: 400;
}
```

这些字体也适用于移动设备，因此用户可以在你的响应式网页设计中获得很好的体验。

或者他们有吗？

除了图像，字体通常是网页中最大的资产。上面的 Ubuntu 字体增加了近 250Kb 的页面重量，这在较慢的移动连接上很明显。Chrome，IE，Safari 和 Opera 在加载字体时会留下一个空格，所以页面不可用。Firefox 和旧版本的 Opera 以备用字体显示文本，然后切换——这被称为无样式文本闪烁(FOUT)。两种选择都不理想。

我们很少担心字体粗细问题，也很少找借口，比如*“这只是第一页的问题”*或者*“很多用户会把字体缓存起来”*。我们可以省略不常用的字体；例如，删除大多数 Ubuntu 斜体样式节省了近 40%。很少有人敢采取使用标准操作系统字体的明显解决方案——我们的客户和设计师永远不会原谅我们。

## JavaScript webfontloader

幸运的是，还有另一个选择:webfontloader。一旦页面加载完毕，这个 JavaScript 库可以从[谷歌](http://www.google.com/fonts/)、 [Typekit](http://www.typekit.com/) 、[Fonts.com](http://www.fonts.com/)、 [Fontdeck](http://fontdeck.com/) 或者你自己的服务器后台加载字体。库本身向页面增加了 17Kb，但它也作为后台进程下载。

为了加载上面的 Ubuntu 字体集，我们创建了一个名为`WebFontConfig`的全局对象，它定义了我们的字体和设置，然后加载 webfontloader 本身:

```
var WebFontConfig = {
	google: {
		families: [ 'Ubuntu:400,300,400italic,300italic,500italic,500,700,700italic:latin' ]
	},
	timeout: 2000
};

(function(){
	var wf = document.createElement("script");
	wf.src = ('https:' == document.location.protocol ? 'https' : 'http') +
		'://ajax.googleapis.com/ajax/libs/webfont/1/webfont.js';
	wf.async = 'true';
	document.head.appendChild(wf);
})();
```

因此，我们可以根据设备和带宽容量来确定是不加载字体、加载部分字体还是加载所有字体。理想情况下，我们可以使用[网络信息 API](/use-network-information-api-improve-responsive-websites/) ，但是浏览器支持仍然有限。或者，注意`WebFontConfig`中的`timeout`设置；如果字体文件需要超过两秒钟的时间来下载，请求将被放弃。

## css 回调

webfontloader 在操作期间将类名应用于`html`元素:

*   `.wf-loading` —已请求所有字体
*   `.wf-active` —所有字体都可用
*   `.wf-inactive` —无法加载任何字体

类别也适用于单个字体:

*   `.wf-<familyname>-<fvd>-loading` —已请求单一字体
*   `.wf-<familyname>-<fvd>-active` —单一字体可用
*   `.wf--<familyname>-<fvd>-inactive` —无法加载单一字体

其中`<familyname>`是字体名称的净化版本，而`<fvd>`是[变体描述](https://github.com/typekit/fvd)，例如用于 400 磅斜体的`i4`。

这允许我们在字体下载后进行切换——就像 Firefox 的操作方式一样，例如

```
/* default OS fonts */
body {
	font-family: arial, sans-serif;
}

/* fonts now loaded */
.wf-active body {
	font-family: 'Ubuntu';
}
```

## javascript 回调

类似的 JavaScript 回调函数可以在`WebFontConfig`中定义，尽管这在少数情况下有用，例如

```
var WebFontConfig = {
	google: {
		families: [ 'Ubuntu:400,300,400italic,300italic,500italic,500,700,700italic:latin' ]
	},
	timeout: 2000,
	loading: function() {},
	active: function() {},
	inactive: function() {},
	fontloading: function(familyName, fvd) {},
	fontactive: function(familyName, fvd) {},
	fontinactive: function(familyName, fvd) {}
};
```

有关更多信息，请参考 [webfontloader 文档](https://github.com/typekit/webfontloader)。

## 最小化 FOUT

如果你的备用字体在风格、粗细或间距上与你的网页字体有很大的不同，那么非样式文本的闪烁会很不和谐。但是，通过一点试验，你可以调整备用字体、粗细、行高和边距，以确保网页字体加载时，页面元素保持在大致相同的位置…

参见 [CodePen](http://codepen.io) 上 Craig Buckler([@ Craig Buckler](http://codepen.io/craigbuckler))的笔[如何使用字体加载器](http://codepen.io/craigbuckler/pen/kGCJr/)。