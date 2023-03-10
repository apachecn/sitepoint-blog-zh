# 创建你自己的 CSS3 进度条

> 原文：<https://www.sitepoint.com/create-your-own-css3-progress-bars/>

CSS3 预示着网站功能和设计的一场革命。CSS3 允许 web 开发人员创建动态、灵活且易于修改的 web 内容，同时绕过繁琐的图像和 JavaScript 插件。本教程将向您展示如何使用纯 CSS3 样式从头开始编写一个令人印象深刻的进度条；没有图像，没有 JS。

在我们开始之前，为了演示的目的，我建议你安装最新版本的 Firefox、Chrome 或 Safari。Internet Explorer 或 Opera 目前都不支持我在下面使用的 CSS3 属性。

我们的教程将创建类似于下图所示的进度条效果:

![](img/946b7d5814ff0a619d57acebb13fcb1b.png "css3_progress_bars")

## 第一步:HTML

这些进度条的 HTML 看似简单。首先创建一个带有“progress”类的`<div>`元素。在这个元素中，设置一个子元素`<span>`来包含条形图的当前进度区域。代码应该如下所示:

```
<div class="progress">
   <span></span>
</div>
```

一旦标记就绪，您就可以开始有趣的部分了；陷入 CSS3！进度条上的视觉效果都需要一个单独的 CSS 类。比如上图所示的渐变效果，有“渐变”这个类；条纹效果由“stripe”类表示。这同样适用于动画和光泽效果。

## 第二步:基本的 CSS

编写基本的 CSS 进度条非常简单。本质上你必须创造两种不同的元素。“progress”类本身和一个子元素`<span>`。类似的规则也适用于为其他类设置样式。一旦您建立了基础，您就可以编写应用于子元素`<span>`的所需效果。您的基本 CSS 代码应该如下所示:

```
.progress {
  margin: 5px 0 3px;
  border: 6px solid #333;
  background: #333;
  overflow: hidden;
  border-radius: 50px;
  -moz-border-radius: 50px;
  -webkit-border-radius: 50px;
  box-shadow: 1px 1px 1px #777;
  -moz-box-shadow: 1px 1px 1px #777;
  -webkit-box-shadow: 1px 1px 1px #777;
}
.progress > span {
  display: block;
  height: 20px;
  width: 20%;
  border-radius: 5px;
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
}
```

可以修改颜色、边距、元素高度和其他属性，以适应您自己网站的布局。

## 第三步:渐变效果

比起使用 CSS3 渐变效果，我发现利用`box-shadow`属性来创建所需的效果更有效。属性通常不仅仅是创建一个渐变，所以在编码更复杂的效果时会更好的利用。另一个问题是，Internet Explorer 8 不支持许多更复杂的 CSS3 样式，包括渐变背景。作为一种解决方法，你会在下面的代码中注意到我已经应用了`filter`属性作为后备。至少，使用 IE8 的浏览者会看到某种渐变效果，而不是简单的颜色。

创建渐变效果进度条的代码如下所示。

```
 .gradient > span {
  box-shadow: 0 5px 5px rgba(255,255,255,0.6) inset, 0 -5px 7px rgba(0, 0, 0, 0.4) inset;
  -moz-box-shadow: 0 5px 5px rgba(255,255,255,0.6) inset, 0 -5px 7px rgba(0, 0, 0, 0.4) inset;
  -webkit-box-shadow: 0 5px 5px rgba(255,255,255,0.6) inset, 0 -5px 7px rgba(0, 0, 0, 0.4) inset;
  filter: progid:DXImageTransform.Microsoft.gradient(
    startColorstr='#33ffffff',
    endColorstr='#33000000',
    GradientType=0 );
} 
```

## 第四步:光泽效果

CSS3 背景`gradient`属性可以有效地用来创建一个光滑的进度条。这样做的目的是在进度条本身的顶部施加一个部分透明的光泽效果，这意味着你可以使用它在任何底层颜色上应用一个光泽效果。当创建渐变效果时，我创建了一个“gloss”类来确定所需的样式。下面是 CSS:

```
.gloss > span {
  background-image: -moz-linear-gradient(top,
    rgba(255,255,255,0.2) 0%,
    rgba(255,255,255,0.1) 45%,
    rgba(0,0,0,0.2) 55%,
    rgba(0,0,0,0.1) 100%);
  background-image: -webkit-gradient(linear, left top, left bottom,
    color-stop(0%,rgba(255,255,255,0.2)),
    color-stop(45%,rgba(255,255,255,0.1)),
    color-stop(55%,rgba(0,0,0,0.2)),
    color-stop(100%,rgba(0,0,0,0.1)));
  background-image: -webkit-linear-gradient(top,
    rgba(255,255,255,0.5) 0%,
    rgba(255,255,255,0.1) 45%,
    rgba(0,0,0,0.2) 55%,
    rgba(0,0,0,0.1) 100%);
  background-image: -o-linear-gradient(top,
    rgba(255,255,255,0.2) 0%,
    rgba(255,255,255,0.1) 45%,
    rgba(0,0,0,0.2) 55%,
    rgba(0,0,0,0.1) 100%);
  background-image: -ms-linear-gradient(top,
    rgba(255,255,255,0.2) 0%,
    rgba(255,255,255,0.1) 45%,
    rgba(0,0,0,0.2) 55%,
    rgba(0,0,0,0.1) 100%);
  background-image: linear-gradient(to bottom,
    rgba(255,255,255,0.2) 0%,
    rgba(255,255,255,0.1) 45%,
    rgba(0,0,0,0.2) 55%,
    rgba(0,0,0,0.1) 100%);
}
```

## 第五步:应用条纹

到目前为止，您应该已经熟悉了基本流程。要创建条纹效果，使用下面的代码建立一个“stripe”类。这个类的工作原理和 gloss 一样，在进度条上应用一个半透明的饰面，可以根据需要适应任何颜色。

```
.stripe > span {
  background-size: 30px 30px;
  background-image: -moz-linear-gradient(-45deg,
    rgba(255,255,255,0.15) 0%,
    rgba(255,255,255,0.15) 25%,
    rgba(255,255,255,0) 25%,
    rgba(255,255,255,0) 50%,
    rgba(255,255,255,0.15) 50%,
    rgba(255,255,255,0.15) 75%,
    rgba(255,255,255,0) 75%,
    rgba(255,255,255,0) 100%);
  background-image: -webkit-gradient(linear, left top, right bottom,
    color-stop(0%,rgba(255,255,255,0.2)),
    color-stop(25%,rgba(255,255,255,0.2)),
    color-stop(25%,rgba(255,255,255,0)),
    color-stop(50%,rgba(255,255,255,0)),
    color-stop(50%,rgba(255,255,255,0.2)),
    color-stop(75%,rgba(255,255,255,0.2)),
    color-stop(75%,rgba(255,255,255,0)),
    color-stop(100%,rgba(255,255,255,0)));
  background-image: -webkit-linear-gradient(-45deg,
    rgba(255,255,255,0.3) 0%,
    rgba(255,255,255,0.3) 25%,
    rgba(255,255,255,0) 25%,
    rgba(255,255,255,0) 50%,
    rgba(255,255,255,0.3) 50%,
    rgba(255,255,255,0.3) 75%,
    rgba(255,255,255,0) 75%,
    rgba(255,255,255,0) 100%);
  background-image: -o-linear-gradient(-45deg,
    rgba(255,255,255,0.15) 0%,
    rgba(255,255,255,0.15) 25%,
    rgba(255,255,255,0) 25%,
    rgba(255,255,255,0) 50%,
    rgba(255,255,255,0.15) 50%,
    rgba(255,255,255,0.15) 75%,
    rgba(255,255,255,0) 75%,
    rgba(255,255,255,0) 100%);
  background-image: -ms-linear-gradient(-45deg,
    rgba(255,255,255,0.15) 0%,
    rgba(255,255,255,0.15) 25%,
    rgba(255,255,255,0) 25%,
    rgba(255,255,255,0) 50%,
    rgba(255,255,255,0.15) 50%,
    rgba(255,255,255,0.15) 75%,
    rgba(255,255,255,0) 75%,
    rgba(255,255,255,0) 100%);
  background-image: linear-gradient(135deg,
    rgba(255,255,255,0.15) 0%,
    rgba(255,255,255,0.15) 25%,
    rgba(255,255,255,0) 25%,
    rgba(255,255,255,0) 50%,
    rgba(255,255,255,0.15) 50%,
    rgba(255,255,255,0.15) 75%,
    rgba(255,255,255,0) 75%,
    rgba(255,255,255,0) 100%);
}
```

## 第六步:动画进度条

如果你已经完成了这些步骤，那么你应该已经认识到 CSS3 在不借助图像和 JavaScript 的情况下呈现复杂图形效果的潜力。CSS3 也可以简单有效地用于创建令人印象深刻的动画风格。下面的代码展示了如何显示一个可以与最昂贵的 JS 动画相媲美的动画进度条。

您可以根据需要修改关键帧和动画值，然后将`animate`类应用于任何`<span>`以应用动画进度条效果。玩得开心，享受吧！

```
 .animate {
  animation: progress 2s linear infinite;
  -moz-animation: progress 2s linear infinite;
  -webkit-animation: progress 2s linear infinite;
  -ms-animation: progress 2s linear infinite;
  -o-animation: progress 2s linear infinite;
}

@-webkit-keyframes progress {
  from {
    background-position: 0 0;
  }
  to {
    background-position: -60px -60px;
  }
}

@-moz-keyframes progress {
  from {
    background-position: 0 0;
  }
  to {
    background-position: -60px -60px;
  }
}

@-ms-keyframes progress {
  from {
    background-position: 0 0;
  }
  to {
    background-position: -60px -60px;
  }
}

@-o-keyframes progress {
  from {
    background-position: 0 0;
  }
  to {
    background-position: -60px -60px;
  }
}

@keyframes progress {
  from {
    background-position: 0 0;
  }
  to {
    background-position: -60px -60px;
  }
}
```

按照这些步骤，你将会有一个动态的、专业的进度条，可以适应许多情况和网站主题。这只是在对 CSS3 特性进行一些实验后可以实现的许多设计特性中的一个例子。该栏的大多数特定属性，如配色方案、元素大小，甚至渐变阴影的角度，都可以很容易地适应您的设计要求。

## 分享这篇文章