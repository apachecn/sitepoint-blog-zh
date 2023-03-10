# 如何在 CSS3 中创建多个边框

> 原文：<https://www.sitepoint.com/css3-multiple-borders/>

CSS2.1 `border`属性为我们提供了很好的服务，但是它有点基础。如果你需要两个或更多不同颜色的边框呢？ [CSS3 边框图像属性](https://www.w3.org/TR/css3-background/#border-images)是一个选项，但它仍然需要 Photoshopping 技巧，而且语法相当复杂。但是，如果您只是需要一系列纯色边框，有一个更简单的替代方法:`box-shadow`属性。

![CSS3 Multiple Borders](img/88eb0e24f1f847ed6d69687bad86622b.png)

[**查看多边框演示页面……**](https://blogs.sitepointstatic.com/examples/tech/css3-multiple-borders/index.html)

box-shadow 有六个参数:

1.  **插入** : *(可选)*如果定义，元素内部会出现阴影。
2.  **水平**:距离元素的 x 距离
3.  **垂直**:离元素的 y 距离
4.  **模糊** : *(可选)*模糊半径，即 0 表示无模糊
5.  **扩散** : *(可选)*阴影扩散的距离，即 1px 向所有方向延伸阴影 1 个像素，因此它比父元素宽和高 2px
6.  **颜色**:阴影颜色

很少使用的`spread`参数可以用来创建一个边框。例如，可以使用无模糊和 1 像素扩展创建 1 像素的纯黑色边框:

```
box-shadow: 0 0 0 1px #000;
```

与`border`属性不同，`box-shadow`允许用逗号分隔多个阴影。定义的最后一个阴影位于堆栈的底部，因此，为了创建上面显示的盒子，我们使用以下代码:

```
box-shadow: 
	0 0 0 2px #000,
	0 0 0 3px #999,
	0 0 0 9px #fa0,
	0 0 0 10px #666,
	0 0 0 16px #fd0,
	0 0 0 18px #000;
```

注意:

*   这种效果适用于所有最新的浏览器，包括 IE9。
*   它可以与`border-radius`结合使用，但是记住半径应用于内部元素，因此外部阴影相应地突出。
*   与真正的边框不同，`box-shadow`不需要空间，因此效果会在其他元素下流动。

有些人会认为这是一个黑客，但它是一个不需要图像的多边界快速解决方案。

## 分享这篇文章