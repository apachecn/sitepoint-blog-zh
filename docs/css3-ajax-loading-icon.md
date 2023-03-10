# 如何创建一个没有图片的 CSS3 Ajax 加载图标

> 原文：<https://www.sitepoint.com/css3-ajax-loading-icon/>

每当您的 web 应用程序与服务器交互时，都会有不可避免的延迟。这可能是针对 Ajax 请求、上传文件或使用更新的 HTML5 APIs，如 web 套接字或服务器发送的事件。

理想情况下，您应该给用户一些反馈，表明他们的操作正在被处理。你会经常看到带有旋转图案的小动画 gif。有几个网站会为你创建它们，比如[preloaders.net](http://preloaders.net/)和 [ajaxload.info](http://www.ajaxload.info/) 。

图像是最好的跨浏览器选项，但是它们有许多问题:

*   gif 不支持 alpha 透明度。将图像放在彩色背景上时，你需要小心。
*   位图图像不会很好地缩放。如果你改变尺寸，你需要创建一个新的图像。
*   如果不小心，动画图形可能会有很大的文件大小。
*   图像招致额外的 HTTP 请求。虽然图像将被缓存，但初始下载可能比它所代表的后台进程花费更长的时间！您可以通过预加载图像或使用嵌入式数据 URL 来解决这个问题，但这需要更多的工作。

幸运的是，CSS3 允许我们创建没有图像的加载图标。它们易于创建、缩放、重新着色、在任何背景下使用，并且不会招致图像下载。您需要:

1.  单个 HTML 元素，例如`<div id="ajaxloader"></div>`。
2.  几个 CSS 背景，边界和阴影效果来创建一个图形图标。
3.  CSS3 转换和动画来旋转或移动元素。

[**查看演示页面……**](https://blogs.sitepointstatic.com/examples/tech/css3-ajax-icon/index.html)

## 浏览器兼容性

CSS3 转换和动画是实验性质的，需要厂商前缀— [，你知道它们会引起什么麻烦](https://www.sitepoint.com/w3c-css-webkit-prefix-crisis/)。示例代码实现了 final 属性以及-webkit (Chrome 和 Safari)、-moz (Firefox)、-ms (IE)和-o (Opera)的前缀，但是不能保证它们能够一致地工作。

在撰写本文时，Chrome、Safari 和 Firefox 的最新版本都提供了 CSS3 动画。IE9/8/7/6 和 Opera 显示静态图像，尽管 IE10 和 Opera 12 可能支持这些属性。

让事情更复杂的是，Firefox 允许你单独制作伪元素的动画。因此，您可以使用:before 和:after 添加一些元素，并向不同方向旋转或移动它们，以创建更复杂的动画。虽然我最初希望这样做，但它在 webkit 浏览器中不起作用。Chrome 和 Safari 只允许将*真实的*元素动画化。这似乎是一个错误或疏忽，但在当前或测试版中尚未修复。

## 创建图标

我们的 HTML `div`可以放在文档中的任何地方，尽管最好将其作为`body`的最后一个子元素添加。然后，它将出现在其他元素之上，并且可以相对于页面进行定位。

图标 CSS 简单地设置了宽的白色圆形边框。然后将右边框颜色设置为透明，并应用一点阴影:

```
 #ajaxloader
{
	position: absolute;
	width: 30px;
	height: 30px;
	border: 8px solid #fff;
	border-right-color: transparent;
	border-radius: 50%;
	box-shadow: 0 0 25px 2px #eee;
} 
```

结果是:

![CSS Ajax icon 1](img/7406a8c766bfbb8fb830996f3a03b746.png)

很容易调整不同效果的属性，例如添加`border-right: 0 none;`会产生:

![CSS Ajax icon 2](img/adebf5f47a5242ff5e7e41e594b3c0d3.png)

## 动画图标

为了使图标旋转和跳动，我们在 CSS3 动画中应用了旋转变换和不透明度变化。动画名称、持续时间、缓动类型和重复应用于元素:

```
 #ajaxloader
{
	animation: spin 1s linear infinite;
} 
```

后跟动画关键帧:

```
 @keyframes spin
{
	from { transform: rotate(0deg);   opacity: 0.2; }
	50%  { transform: rotate(180deg); opacity: 1.0; }
	to   { transform: rotate(360deg); opacity: 0.2; }
} 
```

没有一个浏览器支持不带厂商前缀的动画，所以当您 [**查看演示页面**](https://blogs.sitepointstatic.com/examples/tech/css3-ajax-icon/index.html) 时，您会在源代码中看到-webkit、-moz、-ms 和-o 选项。

现在，每当发起 Ajax 请求时，都可以使用一点 JavaScript 显示图标。这是一个很好的效果，可以很容易地定制，并且可以在当前 55%的浏览器上运行。

不幸的是，45%的网络用户看不到这个动画。当 IE10 发布，用户转向其他浏览器的最新版本时，这个数字会下降，但仍然占很大比例。你可以使用图片，但是如果你这样做了，你也可以在所有的浏览器上使用图片。

因此，我建议你看看自己的统计数据。如果你的访问者主要使用 Chrome、Safari 和 Firefox，你今天就可以采用这种技术。如果没有，现在坚持使用图像，并等待更一致的浏览器支持。

## 分享这篇文章