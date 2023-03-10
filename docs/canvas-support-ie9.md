# 画布正在走向 IE9

> 原文：<https://www.sitepoint.com/canvas-support-ie9/>

IE9 可能还需要一段时间才能成为蒸汽软件，但这并没有阻止微软大张旗鼓地宣布新功能。最新发售的是`canvas`；苹果推出的一项 HTML5 图像技术。开发者曾质疑 IE9 的第二个预览版中为何缺少它……尤其是当微软开始吹嘘[*【100% html 5 兼容性】*](https://www.sitepoint.com/really-ie9-100-compatible-with-html5/) 的时候。

Safari 是第一个实现`canvas`标签的浏览器，IE 的其他竞争对手在 2009 年中期也加入了支持。微软一直保持沉默，但他们在 IE9 中对`canvas`的承诺应该受到称赞。

## 什么是画布？

画布是可脚本化的图像。像标准的`img`标签一样定义它的尺寸，例如

```
 <canvas id="mycanvas" width="200" height="200">
Sorry, but your browser does not support canvas.
</canvas> 
```

然后使用 JavaScript API 在该区域内进行绘制。例如，要绘制一个蓝色正方形，其尺寸为 180 像素，点向下 10 个像素，横向 10 个像素:

```
 var canvas = document.getElementById("mycanvas");
if (canvas.getContext) {
	var ctx = mycanvas.getContext("2d");
	ctx.fillStyle = "rgb(0,0,255)";
	ctx.fillRect(10, 10, 180, 180);
} 
```

一旦绘制完成，浏览器就无法访问生成的形状。如果你想反弹`canvas`周围的方块，你需要反复清除和重画形状。但是，可以使用 ctx.toDataURL()保存当前图像，它返回 PNG 编码的表示形式。

随着浏览器支持的改善，画布处理库如 [CAKE](http://code.google.com/p/cakejs/) 和 [processing.js](http://processingjs.org/) 变得越来越普遍。我怀疑我们会看到`canvas`被用于动画、图表和游戏——有几个[极好的例子](https://www.sitepoint.com/3-great-javascript-canvas-games-examples/)说明了这项技术的潜力。

直到现在，很少有开发者尝试过`canvas`，因为它在 IE 中不受支持。基于 Silverlight 的垫片是可用的，但是有多少用户安装了 Silverlight 插件？现在微软已经在 IE9 中确认了这一功能，这种情况应该会有所改变。

这是一个令人兴奋的发展，但是我怀疑你们中的许多人在质疑为什么你们应该使用 canvas 而不是 [SVG](https://www.sitepoint.com/internet-explorer-svg/) ？这两种技术都可以用于图形处理项目，但两者之间有细微的差别。

即将推出— *Canvas vs SVG* …

## 分享这篇文章