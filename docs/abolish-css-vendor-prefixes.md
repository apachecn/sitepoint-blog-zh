# 我们应该废除 CSS 厂商前缀吗？

> 原文：<https://www.sitepoint.com/abolish-css-vendor-prefixes/>

前缀是标记在 CSS 属性开头的特定于供应商的名称。例如，您需要以下代码将元素旋转 10 度:

```
 -moz-transform: rotate(10deg);		/* Firefox 3.5+ */
-o-transform: rotate(10deg);		/* Opera 10.5+ */
-webkit-transform: rotate(10deg);	/* Chrome and Safari3.1+ */
-ms-transform: rotate(10deg);		/* IE9 */
transform: rotate(10deg); 
```

(你可以选择使用微软的 DXImageTransform。支持 IE6 和 IE7 的微软矩阵滤波器)。

## 为什么我们需要前缀？

首先，您应该理解 web 标准不是由 W3C 规定的。该联盟不对创新负责，创新是浏览器厂商的事。

例如，假设 Opera 发明了一个名为“polygon”的新 CSS 属性，它神奇地将一个矩形元素转换成标准形状

```
 polygon: 6; /* a hexagon */ 
```

现在考虑一下，纯属偶然，Mozilla 也在开发自己的 polygon 属性实现。然而，Mozilla 更喜欢一个命名的值，而不是多个边:

```
 polygon: hexagon; 
```

这让 web 开发人员非常头疼。如果他们想使用 polygon 属性，就不可能同时支持两种浏览器。数值在 Firefox 中失败，命名值在 Opera 中失败。

供应商前缀解决了这个问题，例如

```
 -o-polygon: 6; /* Opera support */
-moz-polygon: hexagon; /* Firefox support */ 
```

微软和/或 webkit 团队可以创建他们自己的多边形实现。如果两个或更多的供应商同意，polygon 属性最终将成为 W3C CSS 规范的一部分。

相对较新的背景渐变语法突出了兼容性问题。webkit 团队已经实现了一个相当复杂但是通用的解决方案。Mozilla 的实现更接近于 W3C 规范草案(T1)——但这仍然是一个草案文档，可能会改变:

```
 background-image: -webkit-gradient(linear,left top,left bottom,color-stop(0,#000000),color-stop(1,#ffffff));
background-image: -moz-linear-gradient(top,#000000,#ffffff); 
```

## 前缀还有必要吗？

上面的旋转示例突出了几个问题:

*   代码很冗长
*   忘记前缀太容易了，而且
*   每个供应商都支持相同的语法。

前缀是对很少发生的问题的解决方案。浏览器厂商并不是孤立工作的:微软、苹果、Mozilla、谷歌和 Opera 都是 W3C 成员，没有一个厂商会在不参考其他厂商做法的情况下实现自己的功能。

考虑一下-webkit-transition 属性。webkit 团队正在领导 CSS 过渡开发，其他人也在跟进。那么为什么前缀是必要的呢？供应商不太可能偏离 webkit 的实现。严重的技术问题或有缺陷的想法很可能首先被 webkit 团队修复。

但不仅仅是较新的房产。Border-radius 已经推出好几年了，每个供应商都实现了相同的语法。然而，为了确保良好的浏览器兼容性，开发人员仍然需要使用:

```
 -moz-border-radius: 10px;
-webkit-border-radius: 10px;
border-radius: 10px; 
```

对于什么时候一个属性是足够“标准”的，供应商有他们自己的看法。特定于供应商的边框半径属性将在许多年后消亡。

CSS 大师彼得-保罗·科赫在他的博客上对前缀特别严厉:

> 是时候废除所有厂商前缀了。它们已经成为没有问题的解决方案，并且它们正在积极地损害网络标准。

## CSS 前缀该不该废除？

在一个浏览器合作日益增多的时代，厂商前缀仍然是必需的吗？我怀疑有哪个开发人员想使用前缀，但它们是必要之恶吗？如果供应商添加不带前缀的属性，但明确表示它们是实验性的，而(还)不是标准，会不会更好？

这是本周 SitePoint 投票的主题。请[投下您的一票](/),并在下面留下您的评论…

## 分享这篇文章