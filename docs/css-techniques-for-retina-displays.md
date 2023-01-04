# 视网膜显示的 CSS 技术

> 原文：<https://www.sitepoint.com/css-techniques-for-retina-displays/>

随着许多人使用其高度便携的设备来工作和娱乐，设备制造商寻求增强他们的用户体验，使他们的设备同时更强大和更易于使用。

苹果给了我们一个全新的产品系列，引入了双密度像素屏幕，在更小的屏幕上创建更清晰的图像。其他设备制造商也纷纷效仿，推出自己版本的高像素密度屏幕。

虽然这对消费者和最终用户来说无疑是一个福音，但它也给网络开发者和设计师带来了挑战，使他们的网站和应用程序与这种视网膜显示器兼容。

就在那时，网络开发者意识到网络是多么的过时，网站在这样的显示器上看起来是多么的“没有生命力”。

## 这是否意味着网站在这样的设备上看起来是坏的？

不，网站在 Retina 设备上不会被破坏。它们看起来很模糊，像素开始出现在低分辨率图像上。网站的整体结构保持不变。

## 像素和屏幕密度

当我们谈到像素时，它们只不过是显示器中最小的物理单位。这是一个微小的照明区域，当它与许多其他像素结合在一起形成一个图像。按照操作系统的指示，每个像素都有自己的亮度级别和颜色标准。正是两个像素之间难以察觉的距离使图像看起来更清晰明亮。

另一方面，屏幕密度指的是实际出现在显示屏上的像素总数。它通常以每英寸像素格式来衡量，即众所周知的 PPI。

本文标题中提到的术语 **Retina** 是苹果公司用来强调其设备双密度像素屏幕的友好词汇。在 Retina Ipad 和 MacBook Pro 这样的高分辨率显示屏上，人眼几乎不可能从正常的观看距离看到像素。

## CSS 像素

CSS 像素是浏览器用来在网页上绘制图像和其他内容的抽象单位。CSS 像素是 dip，这意味着它们是与设备无关的像素。它们会根据呈现它们的屏幕的像素密度来重新调整自己。

如果我们有下面这段代码:

`<div style=”width:150px; height:200px”></div>`

上面的 HTML 组件在标准显示器上看起来是 150 像素乘 200 像素，而在 Retina 显示器上是 300 像素乘 400 像素，以保持相同的物理大小。

![Pixel Density](img/ebb9a5b1a55050cd61d564a3d5f3f530.png "Pixel Density")

## 调整图像大小

### 1)使用交替的高分辨率像素

假设我们有一个 200 像素乘 400 像素(CSS 像素)的图像，我们想在 Retina 显示屏上正确显示它。我们可以上传一个 400 像素乘 800 像素大小的替代图像到我们的服务器上，每当网页在 Retina 设备上打开时，我们就可以渲染它们。

但是为了让它们看起来有相同的尺寸，我们必须使用 CSS 来调整它们的大小。下面这段代码解释了这一点。

```
/* for low resolution display */

.image {

    background-image: url(/path/to/my/lowreslogo.png);

    background-size: 200px 300px;

    height: 300px;

    width: 200px;

}

/* for high resolution display */

@media only screen and (min--moz-device-pixel-ratio: 2),

only screen and (-o-min-device-pixel-ratio: 2/1),

only screen and (-webkit-min-device-pixel-ratio: 2),

only screen and (min-device-pixel-ratio: 2) {

.image {

    background: url(/path/to/my/highreslogo.png) no-repeat;

    background-size: 200px 400px;

/* rest of your styles... */

}

}
```

### 2)使用@face-fonts 代替图像图标

如果你在网页上显示了大量的图标，那么用双倍分辨率的图标来调整每个图标的大小将会是一件非常忙碌的工作。我们将尝试使用@font-faces 来代替图片。这些图像字体会像普通字体一样在高分辨率设备上自动调整大小。使用@face-fonts 是位图图标的另一种解决方案。

今天有很多质量很好的图标字体可以满足你对网站设计的要求。这种字体的例子有 Fontello 和 Inkscape。

以下代码显示了如何使用@font-faces 替换图像图标:

#### 在 HTML 中:

```
/* define span tag for letters and give them a class in HTML */

<span class=”myicon”>d</span>
```

#### 在 CSS 中:

```
/* First import your font */

@font-family: myFont;

src: url('Modern_Icons.ttf'),

     url('Modern_Icons.eot'); /* IE9 */

. myicon {

    font-family: ‘Modern Icons’;

}
```

### 3)使用 SVG 图像代替位图图像

位图图像是光栅图像，它在视网膜显示器中放大了像素。但是它们有一个限制，那就是可以无限放大。这就是 SVG 图像发挥作用的地方。他们也解决了我们上传双倍分辨率的交替图像的问题，另外他们也解决了带宽问题！

#### 在 HTML 中:

`<img src="example.svg" width="150" height="200"/>`

#### 在 CSS 中:

```
.image {

    background-image: url(example.svg);

    background-size: 150px 200px;

    height: 150px;

    width: 200px;

}
```

### 4)使用 JavaScript 将所有图像替换为两倍大小的图像

如前所述，用双倍分辨率的图像替换低分辨率的图像会消耗额外的带宽，网站加载会很慢。尽管如此，如果您想使用上面讨论的第一种方法，那么通过编写单独的代码来替换其中的每一种方法将是一项困难的任务。

我们可以用 JavaScript 替换网页中的所有图片。下面的代码解释了这一点。

```
$(document).ready(function(){

    if (window.devicePixelRatio > 1) {

        var lowresImages = $('img');

        images.each(function(i) {

            var lowres = $(this).attr('src');

            var highres = lowres.replace(".", "@2x.");

            $(this).attr('src', highres);

        });

    }

});
```

上面的代码假设您已经将低分辨率的图像命名为`myimage.png`，将高分辨率的图像命名为`myimage@2x.png`。它会找到图像名称中的第一个点，并将其替换为`myimage@2x`。

使用上述 JavaScript 方法的一个主要缺点是，每次页面加载时，Retina 显示设备都必须下载两个版本的图像。这肯定会影响你网站的加载时间。

在我看来，你必须使用矢量图像方法(SVG ),因为它们的尺寸很小。对于图标图像，您可以使用@font-faces 方法。这两种方法一定会让你的网站在 Retina 设备中熠熠生辉，活灵活现。

## 使用高分辨率图标

今天，在标准设备和视网膜显示器中，网站图标有多种用途。在苹果设备的主屏幕上，收藏夹图标被用作书签。所以他们必须有更好的质量。您可以导出 16px 和 32px 版本来制作 favicon Retina。使用苹果的图标编辑器，XCode 中的**图形工具，制作一个好的 Retina favicon。**

## 结论

网络正在经历一个转型阶段，在这个阶段，网站已经为这种视网膜显示器做好了准备。这种转变虽然缓慢，但却是让访问者对内容满意的有效方法。

作为网页设计师，我们必须使用上面的 CSS 技术来制作适合高分辨率显示屏的网站。

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如真实世界的 [HTML5 & CSS3。](https://learnable.com/courses/html5-css3-for-the-real-world-1484?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)

对本文的评论已经关闭。对 CSS 有疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?53-CSS-amp-Page-Layout?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章