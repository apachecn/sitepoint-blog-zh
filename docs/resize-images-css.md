# 如何用 CSS“调整”图像的大小

> 原文：<https://www.sitepoint.com/resize-images-css/>

虽然您不能在 CSS3 中“调整”图像的大小，但是您可以使用媒体查询和响应式设计的原则使它们在浏览器中看起来像是调整了大小。

想法是让图像响应视口尺寸，以确保它不与边缘重叠。如果视口的高度大于宽度，则必须定义最大宽度，如果视口的宽度大于高度，则必须定义最大高度。

这里是一些示例代码，以及更多关于这个主题的文章的链接。注意`img.ri:empty` `empty`的使用是一个结构化的伪类，它只匹配没有子元素的元素(一个图像不应该有任何子元素)。这是一个 CSS3 选择器，因此 IE8 及以下版本不会解析该声明。

```
@media screen and (orientation: portrait) {
  img.ri { max-width: 90%; }
}

@media screen and (orientation: landscape) {
  img.ri { max-height: 90%; }
}
```

关于响应式图像的更多信息，这里有[一篇关于创建响应式居中图像的更多信息的文章](https://www.sitepoint.com/css3-responsive-centered-image/)、[一个使用 CSS3 保持图像纵横比的指南](https://www.sitepoint.com/maintain-image-aspect-ratios-responsive-web-design/)和[一个背景大小属性的完整指南](https://www.sitepoint.com/css3-background-size-property/)。

## 分享这篇文章