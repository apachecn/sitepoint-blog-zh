# 仔细查看 SVG 路径数据

> 原文：<https://www.sitepoint.com/closer-look-svg-path-data/>

SVG 路径表示形状的轮廓。该形状可以被填充、描边、用于导航文本、成为图案和/或用作剪辑路径。

当我们在 HTML 中手工编写 SVG 或者嵌入来自矢量图形软件的 XML 代码时，构成图形的路径的代码细节就会暴露出来。这种利用 SVG 的方式被称为“内联”,这篇在 CSS-Tricks 上发表的文章应该有助于你了解这一点和其他 SVG 概念。

根据所使用的形状，这些路径可能会变得相当复杂，尤其是当涉及到许多[曲线](https://www.w3.org/TR/SVG/paths.html#PathDataCurveCommands)时。理解“幕后”到底发生了什么，对于编辑这些图形和消除渲染时的任何意外是非常有益的。

在本文中，我将回顾 SVG 路径数据中可能存在的命令，特别关注一个香蕉图像和一些图表，以便进一步理解。

## `path`元素

`<svg>`中的`<path>`元素包含路径完成形状所需的命令细节，形状可以由直线和曲线组成。

虽然`<path>`元素接受了[个属性](https://www.w3.org/TR/SVG/paths.html#PathElement)，但在本文中，我将严格关注路径数据中的命令。

## 路径数据

路径数据构成了定义图形路径的特定信息。通过 moveto、lineto、closepath 和各种 curve 命令，这些数据可以包括起点和终点、子路径和曲线。

该数据包含在`<path>`元素的`d`属性中:

```
<svg>
    <path d="<path data details>" />
</svg>
```

让我们来看一个香蕉图。这个香蕉由两个`<path>`元素组成，每个元素包含一个起点和至少一个曲线命令。

```
<svg width="228px" height="253px" viewBox="0 0 228 253">
    <g class="banana">
        <path fill="none" stroke="#f7c562" stroke-width="8" stroke-linecap="round" stroke-linejoin="round" d="M8.64,223.948c0,0,143.468,3.431,185.777-181.808c2.673-11.702-1.23-20.154,1.316-33.146h16.287c0,0-3.14,17.248,1.095,30.848
  c21.392,68.692-4.179,242.343-204.227,196.59L8.64,223.948z" />
        <path fill="none" stroke="#f7c562" stroke-width="8" stroke-linecap="round" stroke-linejoin="round" d="M8.767,230.326c0,0,188.246,40.154,196.485-160.139" />
    </g>
</svg>
```

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [SVG 香蕉演示路径数据语法](http://codepen.io/SitePoint/pen/scIdq/)。