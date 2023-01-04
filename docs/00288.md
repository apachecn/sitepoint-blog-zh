# 如何使用 SVG 图像精灵

> 原文：<https://www.sitepoint.com/use-svg-image-sprites/>

本教程描述了两种创建包含许多图像的*精灵*的方法。然而，它使用的是 [SVG](https://www.sitepoint.com/svg-101-what-is-svg/) ，而不是更广为人知的位图技术。

SVG 是徽标、图表和图标的理想选择，原因如下:

*   在客户机或服务器上创建和操作 SVG 很容易。
*   与位图不同，SVG 可以缩放到任何大小而不会损失质量。
*   与网络字体图标不同，SVG 保持清晰，可以应用多种颜色、渐变，甚至复杂的滤镜。

## 什么是图像精灵？

多年来，图像精灵一直是一种很好的实践技术。如果您需要一些经常使用的图形，您可以将它们放在单个图像中，而不是单独的文件中。例如:

![image sprite](img/a21cdc6c5be4eb567696dafdbcaf80c9.png)

本示例在一个 192 像素×24 像素的文件中包含八个 24 像素×24 像素的图标。该文件可以提供稍微好一点的压缩，并且它只需要一个 HTTP 请求就可以显示所有的图标。通过 HTTP/2 加载八个独立的图标不太可能花费很长时间，但是图像可能在不同的时间出现，并且只在第一次使用时被缓存。

如果您想显示最右边的打印机图标，CSS 可以通过定位背景来显示正确的图像:

```
#print
{
  width: 24px;
  height: 24px;
  background: url('sprite.png') -168px 0;
} 
```

可以使用各种工具来计算像素位置并生成 CSS 代码:

*   [CSS 精灵生成器](https://www.toptal.com/developers/css/sprite-generator/)
*   [cssspritestool.com](https://cssspritestool.com/)
*   [刁难牛](http://www.spritecow.com/)

## SVG 图像精灵

多个 SVG 图像也可以放在一个 SVG 文件中，每个图像都可以通过 ID 而不是像素位置来引用。

最常见的技术是在一个 [SVG <符号>](https://developer.mozilla.org/docs/Web/SVG/Element/symbol) 中定义单独的图像。例如，这个 SVG 包含一个绿色圆圈、红色正方形和蓝色三角形:

```
<svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">

  <symbol id="circle">
    <circle cx="50" cy="50" r="45" stroke-width="5" stroke="#0f0" fill="#0f0" fill-opacity="0.5" />
  </symbol>

  <symbol id="square">
    <rect y="5" x="5" width="90" height="90" stroke-width="5" stroke="#f00" fill="#f00" fill-opacity="0.5" />
  </symbol>

  <symbol id="triangle">
    <path d="M20,7 L92,50 L6,93 z" stroke-width="5" stroke="#00f" fill="#00f" fill-opacity="0.5" />
  </symbol>

</svg> 
```

使用 [SVG `<use>`](https://developer.mozilla.org/docs/Web/SVG/Element/use) ，单个 sprite 可以在整个页面中使用任意次:

```
<svg width="100" height="100">
  <use xlink:href="./spriteuse.svg#circle" />
</svg>

<svg width="100" height="100">
  <use xlink:href="./spriteuse.svg#square" />
</svg>

<svg width="100" height="100">
  <use xlink:href="./spriteuse.svg#triangle" />
</svg> 
```

不幸的是，IE11 及更低版本的浏览器无法加载外部图像。最好的解决方法是将完整的 SVG 嵌入到 HTML 标记中，并使用其目标引用每个 sprite。例如:

```
<svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">

  <symbol id="circle">
    <circle cx="50" cy="50" r="45" stroke-width="5" stroke="#0f0" fill="#0f0" fill-opacity="0.5" />
  </symbol>

  <!-- further images -->

</svg>

<!-- display circle -->
<svg width="100" height="100">
  <use xlink:href="#circle" />
</svg> 
```

## SVG Sprite 堆栈

`<use>`元素有点冗长，只能在`<svg>`中使用(无论是独立的图像还是嵌入在 HTML 中)。它不能用于`<img>`、`<iframe>`、`<object>`，也不能作为 CSS 背景。

由 [@simurai](http://twitter.com/simurai) 和 [@erikdahlstrom](http://twitter.com/erikdahlstrom) 在 2012 年首次记录的 SVG 堆栈技术提供了一种绕过这种限制的方法。单个精灵被分配一个类别`"sprite"`，默认情况下，嵌入式 CSS 将它们设置为`display:none`。但是，当 sprite 是目标元素时，会应用`display:inline`:

```
<svg viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">

  <defs>
    <style><![CDATA[ .sprite { display: none; }
      .sprite:target { display: inline; } ]]></style>
  </defs>

  <g class="sprite" id="circle">
    <circle cx="50" cy="50" r="45" stroke-width="5" stroke="#0f0" fill="#0f0" fill-opacity="0.5" />
  </g>

  <g class="sprite" id="square">
    <rect y="5" x="5" width="90" height="90" stroke-width="5" stroke="#f00" fill="#f00" fill-opacity="0.5" />
  </g>

  <g class="sprite" id="triangle">
    <path d="M20,7 L92,50 L6,93 z" stroke-width="5" stroke="#00f" fill="#00f" fill-opacity="0.5" />
  </g>

</svg> 
```

如果这个 SVG 被命名为`sprite.svg`，您可以在 URL 中添加一个`#target-name`来显示一个特定的 sprite。例如，URL `sprite.svg#circle`以您选择的任何维度显示 ID 为`"circle"`的层。因此，它可以用于`<img>`:

```
<img src="./sprite.svg#circle" width="100" height="100" /> 
```

或者作为 CSS 背景:

```
.myelement {
  background: url('./sprite.svg#circle');
} 
```

或者在一个`<iframe>`:

```
<iframe src="./sprite.svg#circle">
  Your browser does not support iframes.
</iframe> 
```

或者在一个`<object>`标签中:

```
<object type="image/svg+xml" data="./sprite.svg#circle">
  <img src="./fallback-image.png" />
</object> 
```

该方法适用于所有浏览器，包括 Internet Explorer 9 和更高版本。

SVG 栈现在不太流行了，因为将 SVG 直接嵌入 HTML 已经成为一种最佳实践技术。然而，当您需要大量 SVG 图标并且不需要使用 CSS 或 JavaScript 直接操作它们时，它可能是理想的。

## 分享这篇文章