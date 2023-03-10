# 使用 Webkit 和 Imagemagick 创建跨浏览器按钮和其他 Swag

> 原文：<https://www.sitepoint.com/use-webkit-and-imagemagick-to-create-cross-browser-buttons/>

你已经看到了漂亮的 [BonBon 按钮](http://lab.simurai.com/css/buttons/)和[超级棒的按钮](http://www.zurb.com/article/266/super-awesome-buttons-with-css3-and-rgba)，你真的想在你的网页设计中使用它们。只是，众所周知，大多数人都使用 Internet Explorer，这使得他们的渲染像垃圾一样。

在本教程中，我将展示如何将众所周知的超级棒的按钮渲染成可以在大多数当前浏览器中使用的精灵，并且看起来就像它们在 webkit 中一样好。你可以将这种技术用于很多东西，比如盒子、边框等等。

开始之前，你需要安装 [webkit2png](https://github.com/AdamN/python-webkit2png) 和 [imagemagick](http://www.imagemagick.org/) 。

现在，让我们从一个基本的牛逼按钮开始，并将其保存为`/tmp/awesome.html`。我喜欢蓝色的:

```
<style>
a {
  background: #222 url(http://www.zurb.cimg/alert-overlay.png) repeat-x;
  display: inline-block;
  padding: 5px 10px 6px;
  color: #fff;
  text-decoration: none;
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  -moz-box-shadow: 0 1px 3px rgba(0,0,0,0.5);
  -webkit-box-shadow: 0 1px 3px rgba(0,0,0,0.5);
  text-shadow: 0 -1px 1px rgba(0,0,0,0.25);
  border-bottom: 1px solid rgba(0,0,0,0.25);
  cursor: pointer;
  font-size: 13px;
  font-weight: bold;
  line-height: 1;
  background-color: #ff5c00;
}
</style>
<a href="#">&nbsp;</a> 
```

它将呈现如下:

![](img/c5ab81ab3bad87db695f9afac0a28876.png)

让我们把这个东西弄得更宽一些，因为我们稍后会把它切开:

```
a {
  ...
  width: 240px;
} 
```

这样更好:

![](img/391b808c25a5851b451928eb228533d7.png)

现在，为了把这个渲染成一个图像，我们当然可以截图。但这不会保留阴影中微妙的阿尔法通道。那么让我们开始吧:

```
webkit2png --transparent --output=./awesome.png file:///tmp/awesome.html
```

注意，您需要使用`file://`协议将输入文件的绝对路径指定为第二个参数。

这会给你一个 800×600 像素的屏幕截图。让我们把它剪短一点，好吗？为此，您需要安装 [imagemagick](http://www.imagemagick.org/) :

```
convert -trim +repage awesome.png awesome.png
```

瞧，图像现在是 260×26 像素。但是我们也需要一个悬停效果的图像。所以让我们稍微调整一下样式表。这应该可以:

```
a {
  ...
  background-color: #f90;
} 
```

用一个新名称保存它— `/tmp/awesome-hover.html`。现在重复上面的过程:

```
webkit2png --transparent --output=./awesome-hover.png file:///tmp/awesome-hover.html
convert -trim +repage awesome-hover.png awesome-hover.png
```

接下来，我们将这两部分粘在一起，创建我们的精灵。Imagemagick 再次派上用场:

```
montage awesome.png awesome-hover.png -background transparent -tile 1x2 -geometry +0+0 combined.png
```

所以现在我们将两种状态渲染到一个单独的 sprite 中。好的，但是我们仍然需要创建 css 来使用它。让我们在`awesome-sprite.html`中创建新文档:

```
<style>
body { font-family: sans-serif; }
a {
  background: url(combined.png) no-repeat;
  display: inline-block;
  padding: 6px 10px 6px;
  color: #fff;
  text-decoration: none;
  text-shadow: 0 -1px 1px rgba(0,0,0,0.25);
  cursor: pointer;
  font-size: 13px;
  font-weight: bold;
  line-height: 1;
  width: 240px;
}
a:hover {
  background-position: 0 -26px;
}
</style>
<a href="#">Awesome</a> 
```

请注意，我调整了填充以补偿底部边框。这样，组合高度将保持不变。

但是等等。这很好，但这个精灵被锁定为 260 像素的固定宽度。如果我们想让它流动呢？为此，我们可以使用[滑动门技术](http://www.alistapart.com/articles/slidingdoors/):

```
<style>
body { font-family: sans-serif; }
a {
  background: url(combined.png) no-repeat;
  background-position: right 0;
  display: inline-block;
  padding-right: 10px;
}
a span {
  background: url(combined.png) no-repeat;
  background-position: left 0;
  display: inline-block;
  padding-top: 6px;
  padding-bottom: 6px;
  padding-left: 10px;
  color: #fff;
  text-decoration: none;
  text-shadow: 0 -1px 1px rgba(0,0,0,0.25);
  cursor: pointer;
  font-size: 13px;
  font-weight: bold;
  line-height: 1;
}
a:hover { background-position: right -26px; }
a:hover span { background-position: left -26px; }
</style>
<a href="#"><span>Awesome</span></a> 
```

我把大部分的样式移到了内部`span`，并留下了一些填充来为精灵的末端腾出空间。已经很接近了，但是还有一个问题:

![](img/64f05e860e14920291e5c09a55a07293.png)

看看这东西的侧面！

因为我们的精灵是透明的，我们不能像这样简单地叠加图像。我们需要对精灵们做些手术。首先，让我们把两端切成一个临时图像:

```
convert sprite.png -gravity west -crop 250x52+0+0 +repage left.png
convert sprite.png -gravity east -crop 10x52+0+0 -background transparent -extent 250x52 right.png
```

这将子画面切割成左侧和右侧，其中连续的背景与左侧部分在一起。

最后，让我们将它们组合成一个精灵:

```
montage left.png right.png -background transparent -tile 1x2 -geometry +0+0 sprite_final.png
```

我们最终得到了这样一个精灵:

![](img/69dae95a33f25b6bb30caf6082662747.png)

然后简单地调整这些背景的偏移量:

```
<style>
body { font-family: sans-serif; }
a {
  background: url(sprite_final.png) no-repeat;
  background-position: right 0;
  display: inline-block;
  padding-right: 10px;
}
a span {
  background: url(sprite_final.png) no-repeat;
  background-position: left 0;
  display: inline-block;
  padding-top: 6px;
  padding-bottom: 6px;
  padding-left: 10px;
  color: #fff;
  text-decoration: none;
  text-shadow: 0 -1px 1px rgba(0,0,0,0.25);
  cursor: pointer;
  font-size: 13px;
  font-weight: bold;
  line-height: 1;
}
a:hover { background-position: right -26px; }
a:hover span { background-position: left -26px; }
</style>
<a href="#"><span>Awesome</span></a> 
```

我们到了。确实很棒。

![](img/6eb5e9b217e226a420ab43a2650d27e9.png)

您可以通过将这个公式填充到 shell 脚本中来重复这个公式，这样您就有了自己的小按钮工厂。事实上，作为圣诞节和所有，[我做了你的工作](https://gist.github.com/760049)。只需将代码片段保存为`webkit_sprite`并使其可执行(`chmod +x webkit_sprite`，您可以像这样调用它:

```
PADDING=10 HTML_NORMAL=normal.html HTML_HOVER=hover.html ./webkit_sprite
```

## 分享这篇文章