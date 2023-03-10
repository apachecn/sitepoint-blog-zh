# 如何使用 CSS 将绝对定位的元素居中

> 原文：<https://www.sitepoint.com/css-center-position-absolute-div/>

这篇文章写于 2010 年，至今仍是我们最受欢迎的帖子之一。如果你热衷于学习更多关于 CSS 技术的知识，你可能会对这篇[关于 CSS](https://www.sitepoint.com/future-generation-css-selectors-level-4/) 中第 4 级选择器的文章很感兴趣。

将一个绝对定位的元素居中是 CSS 经常遇到的挑战。一旦我解决了这个问题，解决方案似乎就显而易见了，但我仍然发现自己每隔几个月就要用谷歌搜索一下这个问题。

CSS 中静态元素的水平居中通常通过将左右边距设置为`auto`来处理，例如:

```
.myelement {
  margin: 0 auto;
}
```

然而，这对绝对定位的元素不起作用。它的位置是相对于具有`absolute`、`relative`或`fixed`的`position`值的最直接的父元素来确定的。

在下面的例子中，相对的红色方块的`width`被设置为 40%的可用空间。绝对定位的蓝色正方形的左上角的位置是 30px 宽，10px 低:

```
.outer {
  position: relative;
  width: 40%;
  height: 120px;
  margin: 20px auto;
  border: 2px solid #c00;
}

.inner {	
  position: absolute;
  width: 100px;
  height: 100px;
  top: 10px;
  left: 30px;
  background-color: #00c;
}
```

如果我们不关心蓝框的精确尺寸，我们可以省略`width`设置，设置相同的`left`和`right`值。这将有效地使我们的蓝盒子居中:

```
.outer {
  position: relative;
  width: 40%;
  height: 120px;
  margin: 20px auto;
  border: 2px solid #c00;
}

.inner {
  position: absolute;
  height: 100px;
  left: 30px;
  top: 10px;
  right: 30px;
  background-color: #00c;
}
```

那么，如果盒子有固定的尺寸，我们如何使它居中呢？答案需要一点横向思考:

1.  首先，我们用`left: 50%`。与背景图像位置不同，这将把蓝框的左边移到中间。
2.  因为我们的盒子太靠右了，所以我们使用了一个负的左边距，它是盒子宽度的一半。在我们的例子中，我们必须将`margin-left`设置为`-50px`来将盒子移回正确的位置。

代码如下:

```
.outer {
  position: relative;
  width: 40%;
  height: 120px;
  margin: 20px auto;
  border: 2px solid #c00;
}

.inner {
  position: absolute;
  width: 100px;
  height: 100px;
  top: 10px;
  left: 50%;
  margin-left: -50px;
  background-color: #00c;
}
```

无论外部元素的宽度如何变化，蓝色框都将保持居中。

*如果你喜欢阅读这篇文章，你会爱上[site point Premium](https://www.sitepoint.com/premium/?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向有经验的开发人员学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如 [CSS Master](https://www.sitepoint.com/premium/books/css-master?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink) 。*

**本文评论关闭。对 CSS 有疑问？为什么不在我们的[论坛](https://www.sitepoint.com/community/)上问呢？**

## *分享这篇文章*