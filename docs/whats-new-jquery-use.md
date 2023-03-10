# jQuery 3.0 的新特性以及如何使用它

> 原文：<https://www.sitepoint.com/whats-new-jquery-use/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

jQuery 是迄今为止世界上最流行的 JavaScript 库，对我们这些网络开发者来说是一个福音。从它在 2006 年首次发布到今天，许多美国 web 开发人员已经将这个美妙的库包含在我们的项目中，以使我们的生活更加轻松。

回到 2015 年 7 月，jQuery 公布了 jQuery 3.0 的首个 alpha 时隔许久的重大发布。让我们看看 jQuery 的新特性以及如何使用它。

*更新:本文现已更新，包括 2016 年 1 月 14 日发布的 jQuery 3.0 测试版。*

## *显示()*和*隐藏()*

更新:jQuery 在 alpha 中尝试了一个新的实现，但是在 3.0 beta 中又恢复了这个变化。

在 3.0 测试版中，隐藏许多元素的性能得到了提高。jQuery 团队在这里发布了这个演示。其中一项测试揭示了这一结果:

![Chrome speed test](img/c31245e66bb7f97f3eb960a73349de6a.png)

jQuery 2.0 版本的速度慢了 97%！

除此之外，另一种显示和隐藏元素的建议方式是使用 *addClass()* 和 *removeClass()* 来控制可见性。或者我们可以在 *ready()* 调用期间调用元素上的*。*

快速样本:

```
<!DOCTYPE HTML>
<html>
	<head>
	<style> .invisible{
			display: none;
		}
		.visible{
			background-color: deepskyblue;
			display:block;
		} </style>
	<script src="jquery-3.0.0-beta1.js"></script>
	<script> $(document).ready(function(){
			$("#div1").addClass("invisible");
			$("#toggle").click(function(){
				$("#div1").removeClass("visible");
				$("#div1").addClass("invisible");
			});
		}); </script>
	<title>Control Visibility</title>
	</head>
	<body>
		<p>Hello!</p>
		<div id="div1">Can you see this?</div>
		<button id="toggle">Click me</button>
	</body>	
</html>
```

![Control visibility](img/4890626b9cb9e3ebf211924045918df4.png)

![Control visibility](img/4675506eded55e4d3561872cef93467f.png)

## *的命名法。数据()*键名

jQuery 团队修改了`.data()`的实现，以紧密匹配 HTML5 数据集规范。如果 data-*属性中的键包含数字，这些数字将不再参与转换。考虑这个例子:

使用 jQuery 2.1.4:

![data attribute in jQuery 2.1.4](img/ce3e22e4a1bef2d2379f1cc07fae4afd.png)

控制台窗口不显示该对象。

使用 jQuery 3.0.0:

![data attribute in jQuery 3.0.0](img/2ae24bbf67fcca04d3b24bb030427a74.png)

密钥被转换为 foo-42Name，因为数字现在不参与转换为 camel 大小写。因此，我们在控制台中得到输出。这把小提琴在 https://jsfiddle.net/nWCKt/25/有售。您可以更改 jQuery 版本来查看更改。

类似地，如果我们想要使用没有参数的`data()`显示所有数据，如果 data-*属性的任何关键字名称在连字符(-)后面有一个数字，那么在两个版本的 jQuery 中参数的数量将会改变，就像上面的例子一样。

*HTML:*

```
<div data-foo-42-name="namebar" data-foo-42-yes="yesbar"></div>
```

*JavaScript:*

```
var div = $('div');
console.log(div.data());
```

在 jQuery 3.0 中，这将显示对象{foo-42Name: "namebar "，foo-42Yes: "yesbar"}。但是在早期版本中，它会显示一个空对象{}。

### *width()* 和 *height()* 返回小数值

一些浏览器返回宽度和高度的子像素值。现在，只要浏览器支持，jQuery 就会为`.width()`、`.height()`、`.css(“width”)`和`.css(“height”)`返回十进制值。对于寻求亚像素精度来设计网页的用户来说，这可能是个好消息。

### *。*、*载()。卸载()*，和*。*方法错误()(被删除)

jQuery 3.0.0 beta 中已经删除了这些以前不推荐使用的方法。推荐的方式是使用`.on()`来处理这些事件。简短示例:

HTML:

```
<img src="space-needle.png" alt="Space Needle" id="spacen">
```

JavaScript:

早期实施(现已失效)

```
$( "#spacen" ).load(function() {
  // Handler implementation
});
```

建议实施:

```
$( "#spacen" ).on( "load", function() {
  // Handler implementation
});
```

## jQuery 对象现在是可迭代的

使用 ES2015 for-of，现在可以迭代 jQuery 对象。所以，你可以用这样的东西:

```
for ( node of $( "<div id=spacen>" ) ) {
  console.log( node.id ); // Returns ‘spacen’
}
```

(来源[此处](https://github.com/jquery/jquery/commit/bb026fc12c3c2ad37f47f0919e484bddcdc3d291))

## jQuery 动画现在在后端使用*requestAnimationFrame*API

现在所有的现代浏览器都支持`requestAnimationFrame`(这里的状态:[http://caniuse.com/#search=requestAnimationFrame](http://caniuse.com/#search=requestAnimationFrame))。凭借其受欢迎的支持，jQuery 将在执行动画时使用该 API。优势包括更流畅的动画和更少的 CPU 密集型动画(因此，节省手机电池)。

## *的增强。unwrap()* 方法

`.unwrap()`方法，它允许您从 DOM 中删除匹配元素的父元素，以前它不接受参数。这可能是一个问题，如果有人想解开的基础上设置的父条件。

在 jQuery 3.0.0 beta 中，`.unwrap()`接受 jQuery 选择器参数来处理这个问题。

## jQuery。延期更新为承诺/A+兼容

一个`promise`是一个异步操作的最终结果——它是一个承诺在未来交付一个结果的对象。与 promise 交互的主要方式是通过注册回调的`then`方法。如今，在 JavaScript 中使用 Promise 进行异步工作变得越来越流行。Promises/A+是可互操作的 JavaScript promises 的开放标准。(更多信息，请点击此链接:【https://promisesaplus.com/】T2)

根据 jQuery 文档，延迟对象是通过调用`jQuery.Deferred()`方法创建的可链接实用程序对象。它可以将多个回调注册到回调队列中，调用回调队列，并传递任何同步或异步函数的成功或失败状态。在 jquery 3.0.0 beta 中，`jQuery.Deferred`对象被更新为兼容 Promises/A+和 ES2015 Promises。因此,`.then()`方法有了重大的改变。

## 更好地处理错误情况

这个版本的 jQuery 对错误情况有更好的处理——到目前为止一直被忽略的错误请求会抛出错误。

例如:考虑`offset`，它获取匹配元素集中第一个元素相对于文档的当前坐标。如果您试图用早期版本的 jQuery 找出一个窗口的偏移量，您将得到结果为`{top: 0, left: 0}`,而不是抛出一个错误，因为窗口的偏移量没有意义。在 3.0 测试版中，它现在会抛出一个错误。

另一个例子:`$("#")`现在抛出一个错误，而不是返回 0 长度的集合。

## 像*这样的自定义选择器的大规模加速:可见*

当像:visible 这样的选择器在一个文档中多次使用时，性能得到了很大的提高。在内部，这是通过缓存完成的——所以第一次使用选择器时，结果是一样的。但是，此后的每次调用都会更快地给出结果，因为缓存发挥了作用。来自 jQuery [的 Timmy Willison 向](https://github.com/jquery/sizzle/issues/315#issuecomment-74336936)报告说，它将`:visible`选择器的性能提高了 17 倍！

这些是一些主要的更新。整个名单可以在他们的官方博客上找到:[http://blog.jquery.com/2016/01/14/jquery-3-0-beta-released/](http://blog.jquery.com/2016/01/14/jquery-3-0-beta-released/)。

## 从哪里下载最新版本

有两个版本:

1.  你可以直接从:[https://code.jquery.com/jquery-3.0.0-beta1.js](https://code.jquery.com/jquery-3.0.0-beta1.js)获得最新版本
2.  缩小版可从:[https://code.jquery.com/jquery-3.0.0-beta1.min.js](https://code.jquery.com/jquery-3.0.0-beta1.min.js)获得

它也可以在 npm 上获得:

```
npm install jquery@3.0.0-beta1
```

随着微软不再支持 IE8、IE9 和 IE10，jQuery 3.0 也不再支持 IE8。如果需要 IE6-8 支持，应该使用最新的 1.12 版本。

请随意试用这个测试版，并在[https://github.com/jquery/jquery](https://github.com/jquery/jquery)上给出反馈。值得一试！

## 更多的 Web 开发实践

这篇文章是微软布道者和工程师关于实用 JavaScript 学习、开源项目和互操作性最佳实践的 web 开发系列的一部分，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?WT.mc_id=16544-DEV-sitepoint-article80) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=16544-DEV-sitepoint-article80)。

我们鼓励您在 dev.microsoftedge.com 使用免费工具进行跨浏览器和设备测试，包括 Windows 10 的默认浏览器 Microsoft Edge:

*   [扫描你的网站，寻找过时的库、布局问题和可访问性](http://dev.modern.ie/tools/staticscan/?utm_source=SitePoint&utm_medium=article80&utm_campaign=SitePoint)
*   [下载适用于 Mac、Linux 和 Windows 的免费虚拟机](http://dev.modern.ie/tools/vms/windows/?utm_source=SitePoint&utm_medium=article80&utm_campaign=SitePoint)
*   [跨浏览器检查网络平台状态，包括微软 Edge 路线图](https://dev.modern.ie/platform/status/?utm_source=SitePoint&utm_medium=article80&utm_campaign=SitePoint)
*   [在您自己的设备上远程测试 Microsoft Edge](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=article80&utm_campaign=SitePoint)

**向我们的工程师和传道者进行更深入的学习:**

*   **互操作性最佳实践** ( [系列](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-5-things-to-consider-as-a-web-developer/?WT.mc_id=16544-DEV-sitepoint-article80)):
    *   [如何避免浏览器检测](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-Avoiding-Browser-Detection/?WT.mc_id=16544-DEV-sitepoint-article80)
    *   [使用 CSS 前缀的最佳实践](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-CSS-Vendor-Prefixes?WT.mc_id=16544-DEV-sitepoint-article80)
    *   [保持你的 JS 框架&库更新](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-Dealing-with-JavaScript-Libraries?WT.mc_id=16544-DEV-sitepoint-article80)
    *   [构建插件免费网络体验](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-Dealing-with-Plugins?WT.mc_id=16544-DEV-sitepoint-article80)
*   [GitHub 编码实验室:跨浏览器测试和最佳实践](https://github.com/deltakosh/interoperable-web-development?WT.mc_id=16544-DEV-sitepoint-article80)
*   哇，我可以在 Mac 电脑上测试 Edge & IE 浏览器& Linux！(来自雷伊·班戈)
*   [在不破坏网络的情况下推进 JavaScript】(来自 Christian Heilmann)](http://channel9.msdn.com/Events/WebPlatformSummit/2015/Advancing-JavaScript-without-breaking-the-web?WT.mc_id=16544-DEV-sitepoint-article80)
*   [用 WebGL 释放 3D 渲染](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Unleash-3D-rendering-with-WebGL-and-Microsoft-Edge?WT.mc_id=16544-DEV-sitepoint-article80)(来自 David Catuhe)
*   托管网络应用和网络平台创新

**我们的社区开源项目:**

*   伏龙。JS (跨设备远程 JavaScript 测试)
*   [manifoldJS](http://manifoldjs.com/?WT.mc_id=16544-DEV-sitepoint-article80) (部署跨平台托管的 web 应用)
*   [babylonJS](http://babylonjs.com/?WT.mc_id=16544-DEV-sitepoint-article80) (轻松制作 3D 图形)

**更多免费工具和后端 web 开发工具:**

*   [Visual Studio 代码](https://code.visualstudio.com/?WT.mc_id=16544-DEV-sitepoint-article80)(用于 Mac、Linux 或 Windows 的轻量级代码编辑器)
*   [Visual Studio 开发基础知识](https://www.visualstudio.com/en-us/products/visual-studio-dev-essentials-vs.aspx?WT.mc_id=16544-DEV-sitepoint-article80)(基于订阅的免费培训和云优势
*   [用节点编码。JS](https://www.microsoftvirtualacademy.com/en-US/training-courses/building-apps-with-node-js-jump-start-8422?WT.mc_id=16544-DEV-sitepoint-article80) 与[在蔚蓝云上试用](https://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=16544-DEV-sitepoint-article80)

## 分享这篇文章