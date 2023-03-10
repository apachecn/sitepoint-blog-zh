# 火狐 19 的新功能

> 原文：<https://www.sitepoint.com/firefox-19-whats-new/>

又一个六周过去了，是时候发布新的火狐了。2013 年 2 月 19 日出现 19 版。如果它没有自动安装，看看关于火狐的帮助>或者去 getfirefox.com 的。让我们来了解一下这些新功能…

## 原生 PDF 查看器

我一直不太理解原生 PDF 查看的必要性。插件可能会很慢，不稳定，在某些平台上不可用，但我一般会尽可能下载或避免 PDF 文档。也就是说，Mozilla 在内置浏览器方面做得很好。它比 Adobe 的渲染速度更快，可以将文档转换成 HTML5，你可以查看这些文档*(不是说你会想看——它很乱)*。

## CSS3 视口百分比支持

Firefox 19 现在支持新的 vh、vw、vmin 和 vmax 元素。更多详情，请参考[新的 CSS3 相对字体大小单位](https://www.sitepoint.com/new-css3-relative-font-size/)。

这些设备现在完全支持 Firefox、Chrome、Safari 和 IE10，部分支持 IE9。Opera 最近转向 WebKit 将不可避免地包含这个特性。

## @对打印文档的页面支持

@page 允许您针对打印页面的页边距或分页符，例如

```
@page
{
	margin: 30mm 50mm;
} 
```

注意,@page 后面可以跟:first，:left 或者:right 来定位特定的页面，但是这在 Firefox 中还没有实现。

## 将画布内容导出到图像

现在可以使用 toBlob 方法将生成的画布元素的图形内容导出到图像 blob:

```
toBlob(callback [, imagetype]);
```

导出是异步进行的，因此它需要一个回调函数来处理结果图像。默认情况下，图像会导出为无损的 24 位 PNG 格式，但是如果需要，您可以指定 JPG，例如

```
// JPEG at 90% quality
toBlob(callback, "image/jpeg", 0.9);
```

这个简单的示例将画布复制到页面上的一个新图像元素:

```
var canvas = document.getElementById("canvas");
canvas.toBlob(function(blob) {
	var newImg = document.createElement("img"),
		url = URL.createObjectURL(blob);
	newImg.onload = function() {
		// revoke blob after use
		URL.revokeObjectURL(url);
	};
	newImg.src = url;
	document.body.appendChild(newImg);
}); 
```

## 更新的开发人员工具

虽然很难胜过 Firebug，但 Mozilla 已经为 Firefox 添加了一系列优秀的开发工具。JavaScript 调试器现在允许在异常时暂停，您可以隐藏不可枚举的属性。此外，Web 控制台中的 CSS 链接现在可以在样式编辑器中打开。

还增加了一个实验性的远程 web 控制台，可以连接到 Android 或 Firefox OS 上的 Firefox Mobile。它必须在 about:config 中启用—将**dev tools . debugger . remote-enabled**设置为 true。

## 杂项改进

在引擎盖下有许多较小的更新:

*   更快的启动性能
*   [主题支持](https://addons.mozilla.org/en-US/firefox/themes/)回来了
*   CSS calc()函数现在可以用于渐变颜色停止。
*   支持[文本转换:全角](https://developer.mozilla.org/en-US/docs/CSS/text-transform)，主要用于亚洲字符集
*   canvas `isPointInStroke`方法测试一个点是否位于路径的填充和描边区域内。
*   CSS [内部分页符](https://developer.mozilla.org/en-US/docs/CSS/page-break-inside)支持控制打印文档中的分页符
*   CSS Flexbox 已被取消固定，但默认情况下保持禁用状态
*   XForms 已经被移除，但是你已经不再使用它了。
*   最低 CPU 要求已降至 600MHz，并且
*   数百个 bug 已经被清除

火狐浏览器已经输给了 Chrome 浏览器，但这款浏览器比谷歌的产品更具竞争力。它在 Windows 上运行得很好，但是还有人在 Mac 或 Linux 上有问题吗？

## 分享这篇文章