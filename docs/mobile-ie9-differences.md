# IE9 移动版与 IE9 桌面版浏览器

> 原文：<https://www.sitepoint.com/mobile-ie9-differences/>

移动 HTML5 开发变得越来越重要——尤其是现在 [Abobe 已经停止为设备](https://www.sitepoint.com/adobe-abandons-mobile-flash/)开发 Flash。苹果和安卓手机提供 webkit 和 Opera 驱动的浏览器；在桌面版中进行测试可以帮助您修复大多数问题。

但是 Windows Phone 7 呢？新的操作系统得到了积极的评价，来自诺基亚、LG、HTC 和三星的新设备已经上市。对开发者来说幸运的是，移动网页浏览器是桌面版 IE9 的直接端口。然而，有一些细微的差别可能会让你措手不及…

## IE9 移动版增加的功能

以下功能出现在移动设备上的 IE9 中，但不会出现在桌面上:

1.  GPS 支持。HTML5 地理定位将使用手机的 GPS 系统，如果它可用的话。桌面版求助于 IP 查找和其他不太准确的方法。
2.  Support for the viewport tag, e.g.
    `<meta name="viewport" content="width=device-width">`

    但是请注意，目前不支持最小比例、最大比例和初始比例属性。

3.  支持`–ms-text-size-adjust`(自动|无|百分比)。这将调整移动设备的文本大小，例如，`–ms-text-size-adjust: 50%;`使其大小减半。

## IE9 Mobile 中删除的功能

以下功能出现在 IE9 的桌面版中，但不会出现在移动设备上:

1.  可下载的字体。仍然支持字体，但是移动浏览器不会下载文件。参考 [Windows Phone 7 支持的字体列表](http://msdn.microsoft.com/en-us/library/ff806365%28v=VS.95%29.aspx)。
2.  跨窗口通信—脚本不能以浏览器窗口为目标。
3.  CMYK 图像*(有人用这些吗？)*
4.  多流 HTML5 音频。
5.  JavaScript 中的 JIT 支持(应该只影响性能而不影响功能)。

微软还删除了:

*   VBScript
*   微软倡导的 ActiveX 网络化多媒体对象技术
*   VML
*   兼容性视图

老实说，如果这些从桌面版消失了，我会很高兴的！我相信这只是时间问题…

总的来说，IE9 的移动版看起来非常好。事实上，为移动 Windows 开发比多个桌面版本更容易！

## 分享这篇文章