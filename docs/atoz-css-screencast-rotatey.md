# AtoZ CSS 截屏:rotateY CSS 转换

> 原文：<https://www.sitepoint.com/atoz-css-screencast-rotatey/>

这个截屏是我们 AtoZ CSS 系列的一部分。你可以在这里找到系列[的其他条目。](https://www.sitepoint.com/blog/)

## 副本

在上一集里，我们学习了属性`transform`和值`translateX()`。

在这一集里，我们将深入探讨 3D 中的变换和元素操作。

在这一集里，我们将了解:

*   CSS 中的 3D 空间
*   如何围绕垂直轴和水平轴旋转元素
*   以及透视是如何工作的

### 三维空间

浏览器有点像一个二维画布，我们在上面布置内容块和样式图像、文本和链接，以创建一个网站或 web 应用程序。

如果你还记得高中数学，当你看一个图形时，有一个水平的 x 轴和一个垂直的 y 轴。

还有直接从屏幕出来的第三个轴，称为 z 轴。在这一季的最后一集，当我们谈论`z-index`时，我们会对此进行更深入的探讨。现在，我们只需要知道有第三维度，我们可以使用 3D 变换将元素从 2D 平面转换到 3D 平面。

当处理 3D 空间中的元素时，我们可以通过将父容器上的`transform-style`属性设置为`preserve-3d`来确保它们看起来好像在第三个平面中。

这将允许所有子元素出现在 3D 空间中。如果未设置该属性，元素将显示为扁平状，效果不会那么明显。

### 辐状的

旋转在设计的许多方面都非常有用——从完整的 180 度到很小的几度。

默认旋转轴是 z 轴。

旋转量可以用度或弧度来指定。我依稀记得数学课上的弧度，但我总是用度数，因为它们让我感觉舒服多了。

我这里有一个图像，周围有白色边框和阴影。如果我想旋转图像，我可以通过使用`transform`属性和`rotate()`函数来实现。

顺时针旋转为正值，逆时针旋转为负值。

```
img {
  transform: rotate(10deg);
}
```

这与图像绕 z 轴旋转时使用`rotateZ()`的单轴旋转相同。

当使用`rotateY()`时，图像围绕 y 轴旋转，这是一个有用的技巧，可以实现类似卡片翻转效果或类似的效果。

```
img {
  transform: rotateY(45deg);
}
```

如果我添加一个动画，让图像围绕 y 轴连续旋转，那么事情会变得更加清晰。

```
img {
  animation: spin 2s infinite linear;
}
@keyframes spin {
  from {transform: rotateY(0deg);}
  to {transform: rotateY(360deg);}
}
```

您可能会注意到，当元素旋转大约 180 度时，图像看起来变成了镜像。默认情况下，图像的背面是可见的，即使在旋转图像时，图像的正面也是背向观察者的。

元素背面的可见性可以用`backface-visibility`属性来控制。它采用值`visible`或`hidden`，其中`visible`是默认值。

rotate 的一个稍微合理的用法是给页面上的元素添加微妙的效果。浏览器支持 IE9+,但因为这是一个相当不重要的外观设计，在旧 IE 中有非旋转元素对我来说不是一件大事。不过，为了安全起见，测试总是值得的——以防万一。

### 远景

当处理 3D 空间中的元素时，我们可以通过将透视引入等式来使视觉效果更加极端。

回到美术课上，你可能已经知道，要在一张 2D 纸上实现三维效果，你可以从一个消失点画出一系列直线，并确保所有远离观众的水平特征都会聚在该点。

这张照片很好地展示了透视效果。但是 CSS 中的这个特性是如何实现的呢？

这里我有一个立方体的标记。立方体的六个面包含在“cube-x”`<div>`中，而“cube-x”`<div>`又包含在另外两个面中，因此最终我们可以看到立方体的三维旋转。

```
<div class="container"> 
  <div class="cube-z"> 
    <div class="cube-y"> 
      <div class="cube-x"> 
        <div class="face-one">1</div> 
        <div class="face-two">2</div> 
        <div class="face-three">3</div> 
        <div class="face-four">4</div> 
        <div class="face-five">5</div> 
        <div class="face-six">6</div> 
      </div> 
    </div> 
  </div> 
</div>
```

```
.container {
  width: 400px;
  margin: 100px auto;
}
[class^="cube"] {
  position: relative;
  width: 200px;
  height: 200px;
  transform-style: preserve-3d;
}
[class^="face-"] {
  position: absolute;
  width: 200px;
  height: 200px;
  padding: 20px;
  color: rgba(0,0,0,0.75);
  border: 1px solid;

  font-size: 160px;
  font-family: Avenir;
  line-height: 1;
  text-align: center;
}

.face-one {
  transform: translateZ(100px);
  background-color: hsla(329,58%,52%,0.8);  
}
.face-two {
  transform: rotateX(90deg) translateZ(100px);
  background-color: hsla(0,0%,13%,0.8);
}
.face-three {
  transform: rotateY(90deg) translateZ(100px);
  background-color: hsla(54,70%,68%,0.8);
}
.face-four {
  transform: rotateY(-90deg) translateZ(100px);
  background-color: hsla(190,81%,67%,0.8);
}
.face-five {
  transform: rotateX(-90deg) translateZ(100px) rotate(180deg);
  background-color: hsla(261,100%,75%,1.0);
}
.face-six {
  transform: rotateY(180deg) translateZ(100px);
  background-color: hsla(84,76%,53%,0.8);
}
.cube-z {transform: rotateZ(0deg);}
.cube-y {transform: rotateY(0deg);}
.cube-x {transform: rotateX(0deg);}
```

此时，立方体的第一面在 2D 平面上是可见的，其他面都是不可见的。

如果我围绕 y 轴旋转“容器”,我们将能够依次看到每一面。

```
.cube-z {animation: spin 4s infinite linear;}

@keyframes spin {
  from {transform: rotateY(0deg);}
  to {transform: rotateY(360deg);}
}
```

这给了我们一种三维的感觉，但是如果我们增加一点透视，效果会变得更加清晰，从而实现真正的 3D 感。

```
.container {
  perspective: 500px;
  perspective-origin: 25% 50%;
}
```

我将停止动画，让我们看看这里发生了什么。

透视属性控制查看器和 z 轴零点之间的距离。较高的值会将查看器移得更远，较低的值会将查看器移得更近，这大大增加了 3D 定位的影响。

我们可以通过改变`perspective-origin`属性来控制消失点的位置。

该属性采用两个长度值，第一个用于水平位置，第二个用于垂直位置。操纵这些值会改变元素的视角，当动画重新打开时，效果会非常显著。

我们即将结束这个 AtoZ 系列，所以请关注我讨论使用`z-index`的最后一封信。

## 分享这篇文章