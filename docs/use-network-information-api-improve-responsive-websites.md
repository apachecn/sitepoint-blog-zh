# 如何利用网络信息 API 改进响应型网站

> 原文：<https://www.sitepoint.com/use-network-information-api-improve-responsive-websites/>

响应式网页设计彻底改变了网页。当在一系列不同的设备和屏幕上查看时，单个站点可以调整其布局。所需要的只是一些媒体查询来检测屏幕大小和加载可选的样式或样式表。

然而，使用屏幕大小来检测浏览器的功能，就像通过查看收音机来判断汽车的速度一样。现代智能手机和平板电脑的分辨率越来越大，将愉快地显示典型的桌面视图。如果该视图添加了大量字体、图像或其他资产，移动用户可能会收到降级的— *和昂贵的* —体验，因为他们使用的是较慢或计量连接。

## 网络信息 API

网络信息 API 可能会有所帮助。它指示用户是否使用计量连接，如预付费，并提供对带宽的估计。利用这些信息，有条件地加载图像、视频、字体和其他资源是可能的。基本上，您可以覆盖媒体查询，以确保移动布局保留在有限的网络上。

## 浏览器支持

尽管 2011 年发布了网络信息 API 草案规范，但目前只有 Firefox 和 Chrome 提供实验性支持。在我们与供应商达成共识之前，API 可能会发生变化:

*   评估**带宽**很困难。当您四处移动或在移动网络和 wi-fi 网络之间切换时，它可能会频繁变化。网络容量可能是好的，但这并不意味着与特定服务器的连接就一定是好的。保持最新的带宽估计也可能是处理器和网络密集型的。
*   设备如何知道您的连接是否被计量？在我提到的一些酒店和机场，即使是快速的 wi-fi 也可能有高得离谱的费用。一种选择是，当建立新的连接时，设备会提示您。

幸运的是，我们可以使用对象检测来检测 API 的存在。

## API 基础

网络信息 API 对象由`navigator.connection`返回。为了确保跨浏览器兼容性，我们需要:

```
var connection = navigator.connection || navigator.mozConnection || navigator.webkitConnection;
```

虽然 IE 通常实现无前缀的 API，但是您可能希望将`navigator.msConnection`添加到这个列表中。

我们的`connection`对象提供了两个只读属性:

*   **带宽** —双精度值，表示当前带宽的估计值，单位为 MB/s(兆字节每秒)。如果用户离线，则该值为零，如果无法确定，则该值为无穷大。请注意，大多数网络提供商的报价单位是每秒兆位，典型的繁忙移动 3G 连接大约为 400Mbps ~= 400，000 bits/s ~= 50Kb/s ~= 0.05MB/s。
*   **计量** —一个布尔值，当`true`时，表示用户的连接受到限制，带宽使用应尽可能受到限制。

例如:

```
if (connection && !connection.metered && connection.bandwidth > 2) {
// load high-resolution image
var img = document.getElementById("kitten");

img.src =img/kitten_hd.jpg";
}
```

最后，当连接状态改变时，我们可以执行一个`change`事件处理程序，例如

```
// default bandwidth
var highBandwidth = false;

// bandwidth change handler
function BandwidthChange() {
highBandwidth = (!connection.metered && connection.bandwidth > 2);
console.log(
"switching to " +
(highBandwidth ? "high" : "low") +
" bandwidth mode"
);
}

// Network Information object
var connection = navigator.connection || navigator.mozConnection || navigator.webkitConnection;

// initialize
if (connection) {
connection.addEventListener("change", BandwidthChange);
BandwidthChange();
}
```

在这段代码中，当高带宽可用时，全局变量`highBandwidth`将被设置`true`。其他代码可以相应地做出反应，例如当`highBandwidth`为`false`时:

1.  不加载高分辨率图像
2.  不需要的字体不会被加载
3.  Ajax 轮询速度变慢
4.  Ajax 超时参数增加了

为了使事情变得简单一点，您可以在`BandwidthChange`函数中的`body`标签上附加一个类，例如

```
// bandwidth change handler
function BandwidthChange() {
highBandwidth = (!connection.metered && connection.bandwidth > 2);

var body = document.body;

if (highBandwidth) {
body.classList.add("hibw");
}
else {
body.classList.remove("hibw");
}

console.log(
"switching to " +
(highBandwidth ? "high" : "low") +
" bandwidth mode"
);
}
```

这允许我们有条件地在 CSS 中加载诸如背景图像的项目

```
/* low bandwidth plain-color background */
#myelement
{
background-color: #ccc;
}

/* high bandwidth image background */
body.hibw #myelement
{
background: url(image.jpg) 0 0 no-repeat;
}
```

在媒体查询加载的桌面布局中仍然可以检查到这种情况。

## 应该使用网络信息 API 吗？

在撰写本文时，网络信息 API 几乎不支持浏览器，并且可能会发生变化。也就是说，如果你正在创建一个必须在移动设备上运行的网站或应用程序，现在一个小小的计划就可以[阻止你的页面达到 1.7Mb](/average-page-weights-increase-32-2013/) 。如果 API 发生变化，您只需更新`BandwidthChange`函数，您的站点就会做出适当的反应。

我当然认为网络信息 API 值得考虑。你呢？

## 分享这篇文章