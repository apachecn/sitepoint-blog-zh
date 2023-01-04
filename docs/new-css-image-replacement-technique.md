# 再见-9999px:一种新的 CSS 图像替换技术

> 原文：<https://www.sitepoint.com/new-css-image-replacement-technique/>

-9999px 图像替换技术已经流行了近十年。要用图像替换文本元素，请使用以下代码:

```
 <h1>This Text is Replaced</h1>

<style>
h1
{
	background: url("myimage") 0 0 no-repeat;
	text-indent: -9999px;
}
</style> 
```

元素的背景会显示出来，它的文本会移出屏幕，这样就不会碍事了。简单有效。它经常被用来显示图形标题——现在我们有了网页字体，这已经很少需要了，但是你仍然会发现它在整个网页上被使用。

直到现在。

Scott Kellum 发现了一项新技术，并在 Zeldman.com 的 T2 得到推广:

```
 #replace
{
	text-indent: 100%;
	white-space: nowrap;
	overflow: hidden;
} 
```

该代码将文本缩进到超出其容器的宽度，但不会换行，溢出部分被隐藏。

虽然它有点长，也更难记住，但性能可以得到提高，因为浏览器不再在幕后绘制 9，999px 的方框。它还可以防止你在使用隐藏文本的链接周围看到奇怪的向左延伸的轮廓。

我还没能发现任何缺点——只是我希望我能先发现它。你采用这种技术了吗？你遇到过什么问题吗？

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如真实世界的 [HTML5 & CSS3。](https://learnable.com/courses/html5-css3-for-the-real-world-1484?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)

对本文的评论已经关闭。对 CSS 有疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?53-CSS-amp-Page-Layout?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章