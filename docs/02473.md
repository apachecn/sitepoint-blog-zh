# CSS 图像替换的历史

> 原文：<https://www.sitepoint.com/css-image-replacement-text-indent-negative-margins-and-more/>

CSS 中的图像替换有着广泛而多样的历史。在当今时代，如果你还在寻找这样做，有许多图像替换技术仍然在今天工作。值得注意的是，这些*中的一些*事实上[可能很快就会被谷歌](https://support.google.com/webmasters/answer/66353)惩罚(如果还没有的话)，所以要谨慎使用。然而，你永远不知道什么时候 CMS 或项目会迫使你需要拿出图像替换套件！这是历史工具包，在网络完全超越它之前，它可能是你需要的最后一个图像替换技术列表。

![Image replacement magician](img/7e2602de5aa990d76a8198aa01dbdb72.png)

SitePoint/ [Natalia Balska 的作品](http://sf-lab.net/)

## 使用负文本缩进 Phark 方法

这是大多数 web 开发人员曾经使用过的最常见的技术。这里的想法是通过设置一个大的负数`text-indent`将文本移动到浏览器窗口之外:

```
.replace-indent {
  width: 264px;
  height: 106px;
  background: url("assets/logo.png");
  text-indent: -9999px;
}
```

下面是一个 CodePen 演示，展示了使用`text-indent`隐藏文本后标题的外观:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的笔[图像替换缩进](http://codepen.io/SitePoint/pen/OXywoW/)。