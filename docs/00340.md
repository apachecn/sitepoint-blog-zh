# 如何从 DOM 坐标转换到 SVG 坐标，然后再转换回来

> 原文：<https://www.sitepoint.com/how-to-translate-from-dom-to-svg-coordinates-and-back-again/>

使用 SVGs 相对简单——除非你想混合 DOM 和矢量交互。

SVG 在`viewBox`属性中定义了自己的坐标系。例如:

```
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 800 600"> 
```

这将从`0,0`开始设置 800 个单位的宽度和 600 个单位的高度。这些*单位*是出于绘图目的的任意度量，并且可以使用一个单位的分数。如果你把这个 SVG 放在一个`800`乘`600`的像素区域，每个 SVG 单元*应该*直接映射到一个屏幕像素。

但是，矢量图像可以缩放到任意大小，尤其是在响应式设计中。您的 SVG 可以通过`300`缩小到`400`，或者甚至通过`1000`在`10`空间中拉伸得面目全非。如果希望根据光标位置放置元素，那么向这个 SVG 添加更多的元素会变得更加困难。

*注意:参见 Sara Soueidan 的 [viewport、viewBox 和 preserve spectra ratio](https://sarasoueidan.com/blog/svg-coordinate-systems/)文章，了解更多关于 SVG 坐标的信息。*

## 避免坐标转换

你也许可以完全避免在坐标系之间转换。

嵌入在 HTML 页面中的 SVG(而不是图像或 CSS 背景)成为 DOM 的一部分，可以像处理其他元素一样进行处理。例如，一个基本的 SVG 只有一个圆:

```
<svg id="mysvg" xmlns="https://www.w3.org/2000/svg" viewBox="0 0 800 600" preserveAspectRatio="xMidYMid meet">
  <circle id="mycircle" cx="400" cy="300" r="50" />
<svg> 
```

您可以将 CSS 效果应用于此:

```
circle {
  stroke-width: 5;
  stroke: #f00;
  fill: #ff0;
}

circle:hover {
  stroke: #090;
  fill: #fff;
} 
```

您还可以附加事件处理程序来修改属性:

```
const mycircle = document.getElementById('mycircle');

mycircle.addEventListener('click', (e) => {
  console.log('circle clicked - enlarging');
  mycircle.setAttribute('r', 60);
}); 
```

以下示例向 SVG 图像中添加三十个随机圆，在 CSS 中应用悬停效果，并使用 JavaScript 在单击圆时将半径增加十个单位。

在 [CodePen](https://codepen.io) 上通过 site point([@ site point](https://codepen.io/SitePoint))
查看笔 [SVG 交互](https://codepen.io/SitePoint/pen/ALAakG)。