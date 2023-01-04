# AtoZ CSS 截屏:CSS 不透明度属性

> 原文：<https://www.sitepoint.com/atoz-css-screencast-opacity/>

这个截屏是我们 AtoZ CSS 系列的一部分。你可以在这里找到系列[的其他条目。](https://www.sitepoint.com/blog/)

## 副本

属性指定了一个元素的不透明程度。

它的取值范围从 0 到 1，其中 0 表示完全透明，1 表示完全不透明。

在这一集的实践中，我们将看看`opacity`属性是如何工作的，包括它的一些缺点——然后使用`opacity`和我们在*“[第 11 集:`keyframe`动画](https://www.sitepoint.com/atoz-css-screencast-keyframe)中学到的知识创建一个仅 CSS 淡入淡出的幻灯片。*

### `opacity`

```
a img {
  opacity:1;
}
```

我这里有一个链接的图像，它的`opacity`设置为 1。这是默认设置，使图像完全不透明。将值设置为 0 会使其完全透明，但会保持其在文档中的位置。设置 0 到 1 之间的任何值都会使其半透明。

当将`opacity`设置为 1 以外的任何值时，会创建一个新的堆叠上下文，将半透明元素放置在新层上。因此，下面元素上的`background`是部分可见的。

我喜欢用这种效果给悬停在链接图片上的人一些视觉反馈。是一个可以动画化的属性，通过给图像添加一个过渡，效果是一个很好的微妙的淡入淡出。

```
a img {transition:0.3s;}
a:hover img {opacity:0.75;}
```

### 不透明度和内容

当将`opacity`应用到包含其他内容的元素时，子元素也是半透明的，不管它们上设置了什么`opacity`值。

如果我想创建一个半透明背景的盒子，`opacity`会让这个盒子和它的所有内容都是半透明的。在这种情况下，最好使用在`rgba`中设置的背景色，我们在 *" [第三集:CSS 颜色语法](https://www.sitepoint.com/atoz-css-screencast-color/) "* 中看到过。

### 幻灯片

由于`opacity`可以制作动画，让我们看看如何使用一系列的`keyframes`制作幻灯片，这些幻灯片只是操纵一组图像的`opacity`。

我有一个里面有 5 张图片的容器。每个都有一个数字类，用于创建 5 个不同的动画计时。

```
<div class="slide-container"> 
  <img class="slide1" src="http://www.placekitten.com/800/400"> 
  <img class="slide2" src="http://www.placekitten.com/800/500">
  <img class="slide3" src="http://www.placekitten.com/700/600"> 
  <img class="slide4" src="http://www.placekitten.com/800/400"> 
  <img class="slide5" src="http://www.placekitten.com/800/500"> 
</div>
```

通过在`slide-container`上设置`position:relative`并在幻灯片内的图像上设置`position:absolute`，幻灯片被堆叠在彼此之上。

```
.slide-container {
  position: relative;
  height: 400px;
  overflow: hidden;
}
.slide-container img {position: absolute; top:0; left:0; opacity:0;}
.slide1 {animation: fade 20s infinite;}
.slide2 {animation: fade 20s 4s infinite;}
.slide3 {animation: fade 20s 8s infinite;}
.slide4 {animation: fade 20s 12s infinite;}
.slide5 {animation: fade 20s 16s infinite;} 

@keyframes fade {
  0%   {opacity: 0;}
  10%  {opacity: 1;}
  20%  {opacity: 1;}
  30%  {opacity: 0;}
  40%  {opacity: 0;}
  50%  {opacity: 0;} 
  60%  {opacity: 0;}
  70%  {opacity: 0;}
  80%  {opacity: 0;}
  90%  {opacity: 0;}
  100% {opacity: 0;}
}
```

对于`keyframes`，我们希望每张图像在 1/5 的时间内可见。对于第一个图像，这可以通过让它在动画的前 0%从`opacity: 0`渐变到`opacity: 1`来实现，然后在再渐变到 0 之前保持 10%的不透明。

要为其他图像设置`keyframes`,我们可以复制并粘贴我们已经创建的代码块，但更整洁的方法是使用`animation-delay`属性来偏移每个动画开始时总持续时间的 1/5——这是第一张幻灯片开始淡出之前可见的时间。

复制幻灯片 2-5 的动画声明，并以 4 秒的增量增加`animation-delay`值，完成效果。

对于几行 CSS 来说还不错吧？

## 分享这篇文章