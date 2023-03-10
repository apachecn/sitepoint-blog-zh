# 掌握 CSS3: 7 不容错过的酷文本阴影示例

> 原文：<https://www.sitepoint.com/mastering-css3-7-cool-text-shadow-samples-you-cant-miss/>

我们已经探索了如何使用 CSS3 阴影的基础知识:[框阴影](http://html5insight.com/post/15660461090/mastering-shadows-part-1-box-shadows-en)和[文本阴影](http://html5insight.com/post/15678300626/mastering-shadows-part-2-text-shadows-en)。今天，我们将提高我们的技能，看看如何建立一些惊人的文字阴影。

*我在 Photoshop 的各种教程中找到了一些关于阴影的想法——我感兴趣的是我是否可以通过使用`text-shadow`来实现类似的效果。*

## Internet Explorer 和 Windows 8

要亲自尝试所有这些示例并使用`text-shadow`表达您的创造力，请下载 [Internet Explorer 10](http://ie.microsoft.com/testdrive/Info/Downloads/Default.html) 。所有提到的技术也将适用于使用 HTML 和 JavaScript 构建的 Windows 8 的 [metro 风格应用。愿阴影与你同在！](http://msdn.microsoft.com/en-us/windows/apps/)

## 街机直播

在我们的第一个示例中，我们将尝试绘制一些很酷的浮雕文本。我们将从简单的浅灰色文本开始:

![](img/8602b025fcd8ac71bedceaf1985f0e5d.png)

```
color: hsl(80, 70%, 55%);
```

接下来，让我们通过添加一些具有一个像素对角线偏移的阴影来添加浮雕效果(注意与文本颜色相比，阴影的颜色是如何定义的):

![](img/4b12fac995323311d3a182cf3961bf6c.png)

```
text-shadow: -1px -1px 0 hsl(80, 70%, 35%),
             -2px -2px 1px hsl(80, 70%, 35%);
```

现在我们将添加一些漂亮的细节——在文本周围添加一个浅白色的模糊阴影，在底部添加一个带有柔和过渡的深色阴影:

![](img/d5dd80e91a04062eafe6c9c4c2a95c42.png)

```
text-shadow: 0 0 2px #fff,
             -1px -1px 0 hsl(80, 70%, 35%),
             -2px -2px 1px hsl(80, 70%, 35%),
             -2px -2px 2px hsl(80, 10%, 15%);
```

接下来，让我们为我们的文本添加一个衬底。要做到这一点，我们需要扩展我们的阴影(这里我们使用的是`text-shadow`规则的第四个参数——传播距离):

![](img/8a4f0a16ad1ef9cc69130bd4e310b458.png)

```
text-shadow: …
             -3px -3px 0 7px hsl(60, 10%, 65%),
             -4px -4px 0 7px hsl(60, 10%, 65%),
             -5px -5px 0 7px hsl(60, 10%, 65%),
             -6px -6px 0 7px hsl(60, 10%, 65%);
```

最后，为了将我们的文本放置在背景上，让我们在基底的底部添加一个黑色的模糊阴影:

![](img/bdb4ac9a9250ef17da23040a2400f04e.png)

```
text-shadow: …
             -7px -7px 4px 8px hsl(60, 10%, 40%),
             -8px -8px 6px 9px hsl(60, 10%, 55%);
```

### 决赛成绩

![](img/1e293bed0b9fae82719b0e5d1d33671a.png)

```
color: hsl(80, 70%, 55%);
text-shadow: 0 0 2px #fff,
             /* embossed text */
             -1px -1px 0 hsl(80, 70%, 35%),
             -2px -2px 1px hsl(80, 70%, 35%),
             /* transition to substrate */ 
             -2px -2px 2px hsl(80, 10%, 15%),
             /* substrate */ 
             -2px -2px 0 7px hsl(60, 80%, 95%),
             -3px -3px 0 7px hsl(60, 10%, 65%),
             -4px -4px 0 7px hsl(60, 10%, 65%),
             -5px -5px 0 7px hsl(60, 10%, 65%),
             -6px -6px 0 7px hsl(60, 10%, 65%),
             /* shadow for substrate */ 
             -7px -7px 4px 8px hsl(60, 10%, 40%),
             -8px -8px 6px 9px hsl(60, 10%, 55%);
```

## 彩色幸福

在第二个示例中，我们将重复使用第一个示例中的一些想法:我们将构建多个彩色的基底，并制作一个金字塔。我们将从非常简单的粉红色文本开始:

![](img/a96e6c130617628377004a8de731d94a.png)

```
color: hsl(330, 100%, 50%);
```

首先我们来做浮雕。这一次，我们的阴影看起来很小，所以我可以通过省略中间的一个像素的偏移来减少应用规则的数量，但在对角线阴影的情况下，这种方法会导致锯齿效果。我还将添加一些模糊，以柔化到下一个基底的过渡:

![](img/705269068e78a3e8b971f012b71fbfc4.png)

```
text-shadow: 0 2px 0 0px hsl(330, 100%, 25%),
             0 3px 2px 0px hsla(330, 100%, 15%, 0.5);
```

接下来，让我们添加一个具有不同色调值的扩展基底(注意，我只改变了垂直偏移、色调和扩散距离):

![](img/cc94c50ac4c8871c7c9ef38679f2ee25.png)

```
text-shadow: 0 2px 0 0px hsl(330, 100%, 25%),
             0 3px 2px 0px hsla(330, 100%, 15%, 0.5),
             0 3px 0 3px hsl(350, 100%, 50%),
             0 5px 0 3px hsl(350, 100%, 25%),
             0 6px 2px 3px hsla(350, 100%, 15%, 0.5);
```

现在我们只需要重复同样的技巧几次，增加基底的尺寸，并向正确的方向移动色调:

![](img/633195628a17379f28618ceb7505afcf.png)

```
text-shadow: …
             0 6px 0 9px hsl(20, 100%, 50%),
             0 8px 0 9px hsl(20, 100%, 25%),
             0 9px 2px 9px hsla(20, 100%, 15%, 0.5),
             …
             0 15px 0 45px hsl(90, 100%, 50%),
             0 17px 0 45px hsl(90, 100%, 25%),
             0 17px 2px 45px hsla(90, 100%, 15%, 0.5);
```

### 决赛成绩

![](img/bc9da2557b70135dfeaf9bd24ee0ef19.png)

```
color: hsl(330, 100%, 50%);
text-shadow: 0 2px 0 0px hsl(330, 100%, 25%),
             0 3px 2px 0px hsla(330, 100%, 15%, 0.5),
             /* next */
             0 3px 0 3px hsl(350, 100%, 50%),
             0 5px 0 3px hsl(350, 100%, 25%),
             0 6px 2px 3px hsla(350, 100%, 15%, 0.5),
             /* next */
             0 6px 0 9px hsl(20, 100%, 50%),
             0 8px 0 9px hsl(20, 100%, 25%),
             0 9px 2px 9px hsla(20, 100%, 15%, 0.5),
             /* next */
             0 9px 0 18px hsl(50, 100%, 50%)
             0 11px 0 18px hsl(50, 100%, 25%),
             0 12px 2px 18px hsla(50, 100%, 15%, 0.5),
             /* next */
             0 12px 0 30px hsl(70, 100%, 50%),
             0 14px 0 30px hsl(70, 100%, 25%),
             0 15px 2px 30px hsla(70, 100%, 15%, 0.5),
             /* next */
             0 15px 0 45px hsl(90, 100%, 50%),
             0 17px 0 45px hsl(90, 100%, 25%),
             0 17px 2px 45px hsla(90, 100%, 15%, 0.5);
```

## 绍科拉特

我在试验交替阴影时构建的第三个示例从简单的棕色文本开始:

![](img/1639d1349aa507a415f025ff3ed56fd0.png)

```
color: hsl(20, 100%, 20%);
```

第一步是实现经典的 3D 文本效果:

![](img/2c9a4b874016919152e29cc7e762e4c4.png)

```
text-shadow: -1px 1px 0 0 hsl(20, 100%, 16%),
              -2px 2px 0 0 hsl(20, 100%, 16%),
              -3px 3px 0 0 hsl(20, 100%, 16%),
              -4px 4px 0 0 hsl(20, 100%, 16%),
              -5px 5px 0 0 hsl(20, 100%, 16%),
              -6px 6px 0 0 hsl(20, 100%, 16%);
```

接下来，我决定通过降低亮度来加深阴影，并通过增加对角线偏移来增加阴影之间的空间:

![](img/d46a665789d1121106543cbe6f6a651b.png)

```
text-shadow: -0px 0px 0 0 hsl(20, 100%, 16%),
               -2px 2px 0 0 hsl(20, 100%, 14%),
               -4px 4px 0 0 hsl(20, 100%, 12%),
               -6px 6px 0 0 hsl(20, 100%, 10%),
               -8px 8px 0 0 hsl(20, 100%, 8%),
               -10px 10px 0 0 hsl(20, 100%, 6%);
```

下一步是收缩阴影。通过使用收缩，您可以将阴影减少到原始符号的某些部分(这也取决于字体、字体大小和其他属性)。因此，你会得到一个参差不齐的阴影效果。此外，请注意，由于每个阴影的对角线偏移和扩散距离不同，我们会得到灯光扭曲效果:

![](img/bcbdc0d883e23adb40fcdbec77337a0c.png)

```
text-shadow: -0px 0px 0 0 hsl(20, 100%, 16%),
             -2px 2px 0 -1px hsl(20, 100%, 14%),
             -4px 4px 0 -2px hsl(20, 100%, 12%),
             -6px 6px 0 -3px hsl(20, 100%, 10%),
             -8px 8px 0 -4px hsl(20, 100%, 8%),
             -10px 10px 0 -5px hsl(20, 100%, 6%);
```

让我们稍微柔化一下我们的阴影。通过使用模糊半径和颜色，我们可以添加一些中间线:

![](img/c5e02df87cdb490baca6d48c0a30d520.png)

```
text-shadow: -0px 0px 1px 0 hsl(20, 100%, 16%),
             -2px 2px 2px -1px hsl(20, 100%, 14%),
             -4px 4px 2px -2px hsl(20, 100%, 12%),
             -6px 6px 3px -3px hsl(20, 100%, 10%),
             -8px 8px 2px -4px hsl(20, 100%, 8%),
             -10px 10px 2px -5px hsl(20, 100%, 6%);
```

最后，在用这个样本玩了几分钟后，我得到了下面的结果…

### 决赛成绩

![](img/9b26ed48e992aa1c4e111217dbb4a0d3.png)

```
color: hsl(20, 100%, 20%);
text-shadow: 0 0 1px hsl(20, 100%, 18%),
             -1px 1px 0 hsl(20, 100%, 16%), 
             -2px 2px 2px -1px hsl(20, 100%, 14%), 
             -4px 4px 2px -2px hsl(20, 100%, 12%),
             -6px 6px 3px -3px hsl(20, 100%, 10%),
             -8px 8px 2px -4px hsl(20, 100%, 9%),
             -10px 10px 3px -5px hsl(20, 100%, 8%),
             -12px 12px 2px -6px hsl(20, 100%, 7%),
             -14px 14px 2px -7px hsl(20, 100%, 6%),
             -15px 15px 2px -8px hsl(20, 100%, 5%), 
             -15px 15px 0 -8px hsla(20, 50%, 10%, 0.25);
```

## 奶油蛋糕

在第四个示例中，我们为一些蛋糕创建了奶油文本效果。下面是正文:

![](img/fb34caab95a8921974a6c0758d3c4698.png)

```
color: hsl(35, 100%, 30%);
background: hsl(35, 60%, 80%);
```

先说模糊。我添加了两个阴影:第一个(上面的)和文本的色调值相同，但是饱和度更低，第二个(下面的)更模糊，更亮，半透明。我还将下阴影的色调值移向红色:

![](img/67894b681e0ff8d621d56cc735e31fb3.png)

```
text-shadow: 0 0 2px 1px hsl(35, 70%, 30%),
             0 0 4px 4px hsla(30, 100%, 55%, 0.5);
```

现在让我们添加一些奶油色基质(色调值向黄色移动，亮度增加):

![](img/4c669cebe6817afc8894e94cab43208a.png)

```
text-shadow: …
             -1px 1px 2px 7px hsl(45, 60%, 95%);
```

下一步，我们应该给基底添加一些体积:我添加了一个新的阴影，其对角线偏移与文本颜色相同，但饱和度较低。请注意，该阴影的扩展程度低于基板(4px 对 7px):

![](img/aac7980004db105c469dd1e36931e082.png)

```
text-shadow: …
             -3px 3px 1px 4px hsl(35, 70%, 30%);
```

最后一步:模糊基底，使背景变得柔和:

![](img/36467f36ca2b962796fb12d084a45f11.png)

```
text-shadow: …
             -3px 3px 4px 8px hsla(30, 90%, 55%, 0.5);
```

### 决赛成绩

![](img/905b154430f519df1463d7972d65805c.png)

```
color: hsl(35, 100%, 30%);
background: hsl(35, 60%, 80%);
text-shadow: 0 0 2px 1px hsl(35, 70%, 30%),
             /* transition to substrate */
              0 0 4px 4px hsla(30, 100%, 55%, 0.5),
             /* substrate */
             -1px 1px 2px 7px hsl(45, 60%, 95%),
             /* adding volume */
             -3px 3px 1px 4px hsl(35, 70%, 30%),
             /* transition to background */
             -3px 3px 4px 8px hsla(30, 90%, 55%, 0.5);
```

## 塑料的

我一边玩着最后一个，一边想，“我能在它上面做些什么…？”与许多其他示例一样，最终结果取决于文本本身(大小、字体等)和应用的阴影效果。在我的第五个示例中，我使用的是 CabinSketch 字体。这就是我们所拥有的——它只是没有任何特殊效果的文本:

![](img/6aa16859e2bed627bc958bd22f2feb4a.png)

```
color: hsl(65, 60%, 20%);
background: hsl(65, 60%,95%);
```

首先，我在文本周围添加了一些模糊(注意，阴影比文本更亮，结果文本看起来更亮、更饱和):

![](img/d9b0a393404be1caaa3939ab1dee555c.png)

```
text-shadow: 0 0 3px 2px hsl(65, 60%,75%);
```

接下来，让我们添加一些具有模糊效果的轮廓(注意，我使用了扩展和降低亮度):

![](img/22d419e07d1a83ba430f1e82e377d47c.png)

```
text-shadow: 0 0 3px 2px hsl(65, 60%,75%),
             0 0 1px 9px hsl(65, 60%, 20%);
```

是的，它看起来太暗了——我将添加一个中间阴影来使我的文本变亮:

![](img/5acb7e22150d818e28658944c2ddba0e.png)

```
text-shadow: 0 0 3px 2px hsl(65, 60%,75%),
             0 0 1px 5px hsl(65, 60%,95%),
             0 0 1px 9px hsl(65, 60%, 20%);
```

现在是最有趣的一步。事实上，我不需要完整的大纲(基板)，只是一些片段。为了隐藏多余的细节，我将在基底上画一些阴影。这些阴影尺寸较小，但对角线偏移较大:

![](img/daeaa539d9aa104c77687666d32e63cd.png)

```
text-shadow: 0 0 3px 2px hsl(65, 60%,75%),
             0 0 1px 5px hsl(65, 60%,95%),
             6px 6px 4px 7px hsl(65, 60%,95%),
             -4px -6px 4px 6px hsl(65, 60%,95%),
             0 0 1px 9px hsl(65, 60%, 20%);
```

你也可以尝试软化一些细节。

### 决赛成绩

![](img/38673a805d9b6c291cffbb89a408278e.png)

```
color: hsl(65, 60%, 20%);
background: hsl(65, 60%,95%);
text-shadow: 0 0 3px 2px hsl(65, 60%,75%),
            /* light substrate */
             0 0 1px 5px hsl(65, 60%,95%),
            /* blurring */
             0 0 4px 4px hsla(65, 100%, 30%, 0.4),
            /* cutting substrate pieces */
            6px 6px 4px 7px hsl(65, 60%,95%),
            -4px -6px 4px 6px hsl(65, 60%,95%),
            /* dark outlining */
             0 0 1px 9px hsl(65, 60%, 20%);
```

## 绘画

以下两个示例将为您提供一些使用透明度的技巧。想一想:你将如何使用`text-shadow`在文本内部绘图？实际上，你不能使用`text-shadow`来绘制内部阴影。应用于文本的所有阴影被组合成一个堆栈，并一个接一个地绘制。然后，文本被绘制在它们上面。所以你需要以某种方式让文本消失…要做到这一点，你可以让文本透明！

![](img/7a42fc9414310fca886aafa984661a15.png)

```
color: transparent;
background: hsl(0, 75%,45%);
```

现在道路是清楚的。我要用白色来画(所以唯一真正重要的是 100%的亮度)。要在文本内绘制内容，只需减小阴影的大小:

![](img/41ac0b0b0ecb2ef03513052e74c8ec14.png)

```
text-shadow: 3px 3px 1px -8px hsla(0, 60%, 100%, 0.75);
```

让我们通过改变透明度、偏移量和大小来添加更多的细节:

![](img/3548d1f0a5f72315b80332c157c5fa99.png)

```
text-shadow: 3px 3px 1px -8px hsla(0, 60%, 100%, 0.75),
             -1px -1px 1px -4px hsla(0, 60%, 100%, 0.65),
             1px 1px 1px -4px hsla(0, 60%, 100%, 0.65);
```

为了加强形状，您可以添加一个扩大的模糊阴影:

![](img/f42ebda21843962048d2800e2ecefa6f.png)

```
text-shadow: …
             0 0 1px 2px hsla(0, 60%, 100%, 0.65);
```

如果你愿意，你可以增加额外的细节。

### 决赛成绩

![](img/51bce4de73707f32732f31810a95c2b6.png)

```
color: transparent;
background: hsl(0, 75%,45%);
text-shadow: 3px 3px 1px -8px hsla(0, 60%, 100%, 0.75),
             -1px -1px 1px -4px hsla(0, 60%, 100%, 0.65),
             1px 1px 1px -4px hsla(0, 60%, 100%, 0.65),
             /* background */
             0 0 1px 2px hsla(0, 60%, 100%, 0.65),
             * additional details */
             -3px -3px 1px 2px hsla(0, 60%, 100%, 0.25),
             3px 3px 1px 2px hsla(0, 60%, 100%, 0.25);
```

## 向上和向下

还有最后的样品！我要继续玩透明。我将从白色文本开始(选择它只是为了让它可见):

![](img/9278d9e4d1e5bbee9d190e8746bc1dce.png)

```
color: transparent;
```

首先，我们来添加一个经典的 3D 文本效果(你可以在这里玩透明度)。注意中间阴影中增加的亮度值——我发现这是强调音量的好方法(尝试增加亮度以使其更明显):

![](img/c1fcdaf7937a6956406f5930234b4fa6.png)

```
text-shadow: 1px -1px hsla(0, 0%, 30%, .6),
             2px -2px hsla(0, 0%, 30%, .7),
             3px -3px hsla(0, 0%, 32%, .8),
             4px -4px hsla(0, 0%, 30%, .9),
             5px -5px hsla(0, 0%, 30%, 1.0);
```

现在，为了增加体积，我将在顶部添加一个阴影，重复原始文本的形式:

![](img/d4f026f24a0cd02c0c4f119b61bd128b.png)

```
text-shadow: 0px 0px hsla(0, 0%, 50%, .5),
             1px -1px hsla(0, 0%, 30%, .6),
             …
```

最后，与底部类似，我将添加上部 3D 阴影，但透明度和亮度更高(为了使这部分更清晰，最上面的阴影以更低的透明度绘制):

![](img/60cdd803ddec6279e12b4874a4d1d5ca.png)

```
text-shadow: -4px 4px hsla(0, 0%, 70%, .4),
             -3px 3px hsla(0, 0%, 60%, .2),
             -2px 2px hsla(0, 0%, 70%, .2),
             -1px 1px hsla(0, 0%, 70%, .2),
             …
```

### 决赛成绩

![](img/247f05efb99162f9250f07f07027611d.png)

```
color: transparent;
text-shadow: -4px 4px hsla(0, 0%, 70%, .4),
             -3px 3px hsla(0, 0%, 60%, .2),
             -2px 2px hsla(0, 0%, 70%, .2),
             -1px 1px hsla(0, 0%, 70%, .2),
             0px 0px hsla(0, 0%, 50%, .5),
             1px -1px hsla(0, 0%, 30%, .6),
             2px -2px hsla(0, 0%, 30%, .7),
             3px -3px hsla(0, 0%, 32%, .8),
             4px -4px hsla(0, 0%, 30%, .9),
             5px -5px hsla(0, 0%, 30%, 1.0);
```

## 注意

本文讨论的`text-shadow`属性是在目前处于工作草案状态的 [CSS3 文本](https://www.w3.org/TR/css3-text/)模块中定义的。虽然它看起来相当稳定，但它仍然可以在细节上发生变化。

文本阴影的扩散距离值目前在 CSS 文本级别 4 的[编辑器草稿中定义。](http://dev.w3.org/csswg/css4-text/#text-shadow0)。

## 分享这篇文章