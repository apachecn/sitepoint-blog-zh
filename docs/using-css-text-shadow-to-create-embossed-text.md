# 使用 CSS 文本阴影创建浮雕文本

> 原文：<https://www.sitepoint.com/using-css-text-shadow-to-create-embossed-text/>

text-shadow 属性在文本下面创建了一个阴影，这一点从它的名字就可以看出来！它在语法和功能上类似于 [`box-shadow`](https://developer.mozilla.org/En/CSS/-moz-box-shadow) ，但是它遵循单个字母的形状，而不是围绕元素的边界框进行渲染。以下是定义 x 偏移、y 偏移、模糊半径和颜色的通用语法示例:

```
text-shadow:3px 3px 1px #999;
```

…和它对应的图像:![This text has a shadow!](img/8d8bca4a06e78434be62849948acc3b9.png)我不知道你怎么想，但是在任何情况下我都不希望我的文本看起来是那样的！我从未从事过需要这种效果的设计，我不认为它看起来很好。这很俗气——就像你在垃圾邮件或 Geocities 网站上看到的一样。

## 一丝优雅

然而`text-shadow` *对于某项任务来说*非常有用，这似乎是它在野外唯一的用途——**创建浮雕文本**就像这样:

![This text has a shadow too!](img/a53b4c0762f4a83307f13044bbf83925.png)

它有这样的影子属性:

```
text-shadow:0 -1px 1px rgba(0,0,0,0.5);
```

小心翼翼而又不太露骨地做，效果可以很吸引人；它给像按钮和标题这样的功能性文本增加了一点档次。你可以在本页看到一些例子:

*   橙色按钮，例如顶部的[搜索表单中的“搜索”按钮，或者右边栏中的](#search)[时事通讯注册的“订阅”按钮](#newsletters)
*   海军头的盒子，就像我们刚刚提到的时事通讯注册盒，上面写着“在你的收件箱里获得专家提示”，或者靠近顶部的[类别标签](#categories)

那么它是如何工作的呢？

## 明暗对比

阴影和深度效果是通过模拟光线和阴影来创建的，因此我们有两种基本的方法来应用`text-shadow`来创建浮雕效果:

*   一个**正面的白色阴影**在字母的底部产生光的印象，本质上:

    ```
    text-shadow:1px 1px 0 white;
    ```

*   一个**负黑色阴影**在字母:

    ```
    text-shadow:-1px -1px 0 black;
    ```

    的顶部造成阴影的印象

但是出现的问题是**很难找到阴影的正确平衡**，为了达到一个真实的效果，要应用多少阴影。太细微了，肉眼几乎看不见；太明显，它开始看起来像一个阴影。不幸的是，最难得到的效果之一是浅色背景上的深色文本*，尤其是因为文本阴影被渲染在字体之外的*，而不是在字体上面。**

 *## 提示和技巧

那么，我们能做些什么来使这个效果更真实——让它看起来更像浮雕或雕刻——而不是又大又丑的投影呢？下面是我的小技巧:首先，**使用 <abbr title="Red Green Blue Alpha">RGBA</abbr> 颜色值**，这样效果就有部分不透明。这使它更好地与背景融合，并让您更好地控制效果的强度。(尽管 RGBA 颜色在 <abbr title="Internet Explorer">IE</abbr> 中缺乏支持，但它是学术性的，因为它无论如何都不支持该属性。)不要在 *x* 和 *y* 两个方向都偏移阴影，因为这样看起来太多了。如果你只在 *y* 方向偏移，看起来会更好，就好像光线在正上方一样。然后根据您正在使用的颜色组合使用不同的阴影:

*   对于彩色(但不是很暗)背景上的浅色文本，使用不透明度从`0.25`向上的深色负片阴影(值越高，效果越明显)。这里我也用`1px` 模糊半径

    ```
    #light-on-color{    background:#f60;    color:#fff;    text-shadow:0 -1px 1px rgba(0,0,0,0.5);}
    ```

    稍微柔化了效果
*   对于浅色(而非白色)背景上的彩色文本，使用不透明度相当高的浅色阴影:

    ```
    #color-on-light{    background:#eea;    color:#229;    text-shadow:0 1px 0 rgba(255,255,255,0.75);}
    ```

*   Other combinations are more tricky: a dark shadow will be ineffective against a very dark background or with very dark text, and the same for a light shadow with a light background or text. But after a whole bunch of experiments, I reckon the best effect is achieved with a **subtle combination of both light and dark shadows**. Add a low opacity on the dark shadow, high opacity on the light shadow, and a slight bias toward the background shade (so for dark text on a light background, use a bit more light shadow and a bit less dark; and vice versa). Similar to this:

    ```
    #dark-on-light{    background:#eee;    color:#223;    text-shadow:0 -1px 0 rgba(0,0,0,0.15),                0 1px 0 rgba(255,255,255,0.8)}#light-on-dark{    background:#223;    color:#eee;    text-shadow:0 -1px 0 rgba(0,0,0,0.3),                0 1px 0 rgba(255,255,255,0.4)}
    ```

    离两个极端越远(也就是说，越接近浅色的深色，或者深色的浅色)，最终效果应该越好。

这些最后的例子远非完美，其效果只是略微明显(这就是我们如何阻止它看起来太像一个投影)。但我认为它们很好地利用了尴尬的颜色组合，所以综合考虑所有因素，它们看起来还不错。我用所有四个例子制作了一个[演示页面，在我看来，它们是按照好看程度排序的。](https://www.sitepoint.com/examples/embossed/)

## 你的想法？

我很想听听你的想法，如何更好地达到这种效果，尤其是那些困难的颜色组合。或者您有一个完全不同的任务，而`text-shadow`属性很适合这个任务？*缩略图鸣谢:[达韦克纳皮克](http://www.flickr.com/photos/daveknapik/3026383777/)*

## 分享这篇文章*