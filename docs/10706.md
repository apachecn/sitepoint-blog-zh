# W3C 支持 WOFF 网络字体标准

> 原文：<https://www.sitepoint.com/w3c-backs-the-woff-webfont-standard/>

W3C 今天宣布 Web 开放字体格式(WOFF)已经在他们的标准轨道上发布。假设所有感兴趣的团体都同意，该格式将成为免版税的标准和 W3C 推荐标准。

W3C WebFonts 工作组主席 Vladimir Levantovsky 表示:

> 作为 W3C 开发的一个重要的网络字体标准，WOFF 1.0 代表了一个在网络上实现高级排版的通用解决方案。
> 
> 在浏览器公司和字体供应商的支持下，他们正在使他们的字体在 WOFF 获得许可，这一新的 W3C 建议将为内容创作者、网络作者和品牌经理带来丰富的排版选择。对这一规范的需求帮助推动了我们在 Web 字体工作组内取得的快速进展，该工作组大约四个月前才成立。

## WOFF 是什么？

虽然 CSS2 中引入了`@font-face`规则，但是字体下载仍然是一个有争议的问题，因为它可能导致商业字体违反许可条款进行分发。设计了几种字体格式来解决这个问题，包括 WOFF、TrueDoc (PFR)和嵌入式 OpenType (EOT)。

WOFF 是一种相对较新的格式，由微软、Mozilla 和 Opera 提交给 W3C。它是在 2009 年开发的，W3C 在 2010 年 7 月 27 日发布了 WOFF 的工作草案。从技术上讲，WOFF 是一个包含 zlib 压缩的基于样条的字体的包装器。文件大小通常比同等的 TTF 文件小 40%，这是网络分发的一个重要因素。其他元数据，如许可细节或其他信息，也可以附加到文件中。

火狐 3.6 和 IE9(第三平台预览版及以上版本)已经支持 WOFF。Opera 将实现这种格式，并将其添加到 webkit 中，因此它最终会出现在 Safari 和 Chrome 中。

W3C 并不期望 WOFF 取代其他格式，如 TrueType、OpenType、Open Font 或 SVG 字体。简直是另类。例如，字体经销商可以为桌面/印刷应用提供相同的 OpenType 字体，为网络应用提供相同的 WOFF 字体。每个文件将被提供一个适当的许可证和使用限制。

## 我今天能使用 WOFF 吗？

是的。WOFF 字体可从几家商业铸造厂获得，如 [FontShop](http://www.fontshop.com/) 和 [FontFont](http://www.fontfont.com/) 。 [FontSquirrel](http://www.fontsquirrel.com/) 和[开放字体库](http://openfontlibrary.org/)也提供 WOFF 格式的免费字体。

如果你喜欢艺术，开源字体编辑器已经支持 WOFF。或者，您可以使用[sfnt 2 off](http://people.mozilla.com/%7Ejkew/woff/)实用程序将 OpenType 转换为 WOFF。

## WOFF 资源公司

有关 WOFF 的更多信息，请参考:

*   [万维网文档上的 W3C 字体](https://www.w3.org/Fonts/)
*   [web fonts 工作组](https://www.w3.org/Fonts/WG/)
*   [WOFF 文件格式规范 1.0](https://www.w3.org/TR/2010/WD-WOFF-20100727/)

## 分享这篇文章