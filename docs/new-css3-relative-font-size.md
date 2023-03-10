# 新的 CSS3 相对字体大小单位

> 原文：<https://www.sitepoint.com/new-css3-relative-font-size/>

自从我上次写关于 CSS 字体大小的文章已经超过三年了。十年来，我们被不一致的关键词(小、中、大等)和有缺陷的固定单位(px、pt、mm)所困扰。虽然 Internet Explorer 中与 px 相关的问题已经消失，但%和 em 仍然是最佳选择——尤其是对于响应式设计。

幸运的是，CSS3 为我们提供了几个新的选择…

## 雷姆

百分比和 em 大小调整遇到的一个问题是复合级联难题！考虑这个基本的例子…

```
<style>
body  { font-size: 100%; }
p, li { font-size: 0.75em; }
</style>

<p> 12px text </p>

<ul>
<li> 12px text
<ul><li> 9px text </li></ul>
</li>
</ul> 
```

em 单位是相对于它们的父容器的，所以嵌套列表的字体大小是递减的。确定复杂页面上的字体大小可能很困难——我们大多数人都使用 force *(大量的试错)*。

rem 类似于 em，只是它相对于根元素而不是父元素。将`li`选择器更改为`0.75rem`将确保所有嵌套列表保持在 12px。

rem 在 Chrome、Firefox、Safari、Opera 和 IE9+中都有。旧的浏览器可以用后备来支持，你可能会发现使用绝对单位更容易，例如

```
p, li
{
  font-size: 12px;
  font-size: 0.75rem;
} 
```

## vw、vh 和 vmin

这些新属性允许您根据视窗尺寸缩放字体大小，即

*   1vw 是视口宽度的 1%
*   1vh 是视口高度的 1%
*   1vmin 是 1vw 和 1vh 中最小的

例如，假设您的浏览器视口设置为 1，000 x 1，200 像素:

*   1.5vw = 15px 字体大小
*   1.5vh = 18px 字体大小
*   1.5 vmin = min(1.5w、1.5vh) = 15px 字体大小

新设备将彻底改变响应式设计——移动设备上的文本通常会显得有点大，因为你拿着设备的距离比显示器更近。

浏览器支持还不完善，但很快就能支持了…

*   IE10 —完全支持
*   IE9 —支持，但 vmin 被命名为“vm”
*   Chrome 22+ —完全支持
*   Safari 6 和 iOS Safari 6 —完全支持
*   Firefox —将在版本 19 中实施(2013 年 2 月底)
*   黑莓浏览器 10 —全面支持

还没有 Opera 的消息，但我怀疑他们在调查这个案子。同样，建议使用回退几年，例如

```
p, li
{
  font-size: 12px;    /* old */
  font-size: 1.2vm;   /* IE9 */
  font-size: 1.2vmin;
} 
```

你会在你的页面中采用这些新的字体大小单位吗？

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如真实世界的 [HTML5 & CSS3。](https://learnable.com/courses/html5-css3-for-the-real-world-1484?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)

对本文的评论已经关闭。对 CSS3 有疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?53-CSS-amp-Page-Layout?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章