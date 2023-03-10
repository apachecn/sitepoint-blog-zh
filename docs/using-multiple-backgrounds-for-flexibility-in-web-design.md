# 在网页设计中灵活使用多种背景

> 原文：<https://www.sitepoint.com/using-multiple-backgrounds-for-flexibility-in-web-design/>

如果说我在过去几年的设计师生涯中学到了什么，那就是你总能学到新的技巧来解决常见的设计挑战。最常见的设计挑战之一是创建一个定制的背景，该背景可以扩展和收缩以适应不同的浏览器高度。在这种情况下，重复的背景元素，如条纹或平铺图案将无法满足需要。这项任务最具挑战性的方面是，每一页很可能是不同的高度。你可以设计你的背景来适应一个页面的高度，但是一个僵硬的、静态的背景图片不能适应更短的页面，比如你的联系人页面。

你可以简单地希望没有人会注意到页面中途中断或突然停止的背景，但是背景故障是很难被忽略的；尽管从技术上来说，他们处于幕后，但他们却非常显眼。一个可行的解决方案是为网站的顶部创建一个背景图片，并为页脚区域创建一个单独的背景图片。只要你保持你的文件很小，并且你不要把这些元素做的太高，这对于很多设计来说都可以很好的工作。请注意，两个背景图片的总高度不应高于最短的页面。

然而，将自己限制为两个背景图像意味着背景的可扩展中间背景必须是一种颜色、CSS3 渐变或重复的图像，这并不适用于所有的设计，在某些情况下会给你带来与开始时相同的问题。但是，只要掌握一点 CSS 知识，您就可以在前两个背景图像之间添加第三个背景图像，同时保持相同的可扩展性并保持整体设计不变。

## 步骤 1:创建一个无缝背景

在 Photoshop 中，我创建了一个宽度足以填满一个浏览器的文档，并用天蓝色填充它。

[![](img/b30e65778329ec7bd25cb618aecab732.png)](https://www.sitepoint.com/wp-content/uploads/2012/02/Screen-shot-2012-02-12-at-11.25.28-PM.png)

## 第二步:选择渐变

为了增加效果，我在上面添加了一个新层，并选择白色作为我的前景色。

[![](img/1dce6fc63a134b7eadd1facc471b4bc8.png)](https://www.sitepoint.com/wp-content/uploads/2012/02/Screen-shot-2012-02-12-at-11.27.29-PM.png)

## 第三步:微妙是关键

我选择了渐变工具，选择了前景透明选项，在画布的中心绘制了一个反射渐变，并将这一层的不透明度降低到 15-20%。

[![](img/21f660a60c41841eb3f1e184152c8174.png)](https://www.sitepoint.com/wp-content/uploads/2012/02/Screen-shot-2012-02-12-at-11.28.38-PM.png)

## 步骤 4:合并和裁剪

我将两层合并在一起。然后，我选择了单行选框工具，并作出了选择。

[![](img/69dece408abc4b16af42ab3602f295e0.png)](https://www.sitepoint.com/wp-content/uploads/2012/02/Screen-shot-2012-02-12-at-11.31.44-PM.png)

## 步骤 5:保存你的重复图像

我去图像>裁剪创建一个 1 像素高的宽图像。我把它保存为一个名为“sky.jpg”的. jpg 文件

[![](img/3841ca155a175e2ffe671bb5d4cdf7d9.png)](https://www.sitepoint.com/wp-content/uploads/2012/02/Screen-shot-2012-02-12-at-11.37.21-PM.png)

[![](img/c7c04c9f0cfc5a718b4208a16906be91.png)](https://www.sitepoint.com/wp-content/uploads/2012/02/Screen-shot-2012-02-12-at-11.37.47-PM1.png)

## 第六步:找到你的意象

我上网找了一张草的图片。

[![](img/a68f63e07125ad50a5c7eafe55039e81.png)](https://www.sitepoint.com/wp-content/uploads/2012/02/Screen-shot-2012-02-12-at-11.44.34-PM.png)

## 第七步:创造性地裁剪你需要的东西

我将这张图片放入 Photoshop 中，并对其进行了裁剪，使其不显得太高。

[![](img/6696c8c4aef2e35cd2369c57e4b41e95.png)](https://www.sitepoint.com/wp-content/uploads/2012/02/Screen-shot-2012-02-12-at-11.44.48-PM.png)

## 第八步:用蒙版混合你的图像

我添加了一个图层蒙版，使用渐变工具将草的顶部渐变为透明。

[![](img/30e6c3140194bec315602191a070fd1b.png)](https://www.sitepoint.com/wp-content/uploads/2012/02/Screen-shot-2012-02-12-at-11.48.52-PM.png)

## 第九步:敷面膜

我右击了蒙版图标，选择“应用蒙版”将其与图像合并。

[![](img/b4ee7cfaad9438c0ade6416e98ca2609.png)](https://www.sitepoint.com/wp-content/uploads/2012/02/Screen-shot-2012-02-12-at-11.49.08-PM.png)

## 步骤 10:保存为透明

我再次为网页保存，但这一次我选择了 png-24 透明，这样顶部就可以逐渐消失。

[![](img/b43fa8814c1251905b0c331269625229.png)](https://www.sitepoint.com/wp-content/uploads/2012/02/Screen-shot-2012-02-09-at-10.46.09-PM.png)

## 步骤 11:设置对比背景

对于我的最后一张图片，我创建了一个新的 Photoshop 文档，使它大约 200 像素高，并用天蓝色填充背景层(只是为了视觉参考)。

[![](img/49285be7e360d55929e80254bcd88bbf.png)](https://www.sitepoint.com/wp-content/uploads/2012/02/Screen-shot-2012-02-12-at-11.56.54-PM.png)

## 步骤 12:使用自定义笔刷

网上有很多地方可以下载自定义笔刷，所以我找了一些自定义的云笔刷，选择白色作为我的前景色，新建一个图层，开始在新图层上画云。

[![](img/7f311444e4c417852dbf939fd7b49434.png)](https://www.sitepoint.com/wp-content/uploads/2012/02/Screen-shot-2012-02-12-at-11.56.14-PM.png)

## 第十三步:远离边缘

我确保不要太靠近底部边缘，这样就不会有粗糙的线条破坏云彩的效果。我删除了背景层，只留下了透明背景上的云彩。我选择了“文件”>“为网络和设备保存”,我将这个文件保存为 png-24 格式，并带有透明度。

[![](img/ef9229641ab74019f1807b19c556d9be.png)](https://www.sitepoint.com/wp-content/uploads/2012/02/Screen-shot-2012-02-09-at-10.47.57-PM.png)

我留下了蓝色背景层，这样你就可以看到云，但一定要删除蓝色背景层。

[![](img/d49cf037a8870f79fb96b84d0cf2210c.png)](https://www.sitepoint.com/wp-content/uploads/2012/02/Screen-shot-2012-02-13-at-12.08.20-AM.png)

## 第 14 步:把所有东西放在一起

现在，用一点 CSS 魔法，我可以使用这些图像创建一个绿草如茵，云层覆盖的背景，完美地适合我的每一个页面。我为一家草坪护理公司创建了一个虚拟网站，向您展示如何应用这项技术。我们可以使用 CSS 调用所有三个背景图像:底部居中的草，顶部居中的云，以及填充它们之间不同高度的蓝天。使用 background 方法像平常一样设置站点的背景属性，但是在这种情况下，要特别注意顺序。你对这三张背景图片的排序方式会感觉类似于 Photoshop 中的图层。您调用的第一个项目将是离您最近的图像，而您定义的最后一个图像将是最远的图像。在这个项目中，我首先定义了草，然后定义了云，因为在真实场景中，云会消失在地平线上，对于我们来说，地平线就是草。换句话说，云不应该有机会走在草的前面，因为那样看起来不真实。两个图像都被设置为不重复，草与底部对齐，而云与顶部对齐，并且都居中。

## 第十五步:秩序至关重要

```
body {
background: url(images/grass.png) center bottom no-repeat, url(images/clouds.png) center top no-repeat, url(images/sky.jpg) center top repeat;
font-family: Arial, Helvetica, sans-serif;
color: #060;
text-align: justify;
}
```

## 步骤 16:正确堆叠你的图像

定义的第三个也是最后一个图像是“sky.jpg”文件，该文件居中并在其 y 轴上重复。这就保证了无论一个内容页面有多高，“sky.jpg”都会起到桥梁的作用，而云和草则分别修饰顶部和底部。结果是一个无缝的，动态的背景设计，适合每一页。

[![](img/e95adab29bca981d4ee4f4b634ff172f.png)](https://www.sitepoint.com/wp-content/uploads/2012/02/Screen-shot-2012-02-09-at-10.39.20-PM.png)

## 要记住的重要事情

我不能给你看这个而不谈论这个方法的缺点。它为你的网页设计项目提供了另一种创造性的选择，但这种方法的一个主要缺点是，你使用三个独立的图像来创建你的背景，这可能会降低你的网站速度，因为增加了多个大图形。您可能使用一个. jpg 和两个。png 文件，或者三个。png 文件，通常比。jpg 文件。另一个要考虑的因素是你的三张图片的高度，因为如果它们在一个意想不到的短页面上重叠，它们会破坏你的网站的外观。获得正确的图像高度可能需要时间和多次尝试和错误，但如果你的客户或同事坚持要一个复杂的、可扩展的背景，他们应该会比一个标准的、简单的背景花费更长的时间。

## 分享这篇文章