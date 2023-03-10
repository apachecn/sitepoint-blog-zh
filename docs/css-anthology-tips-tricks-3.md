# CSS 选集:101 个基本提示、技巧和窍门，第 3 章-CSS 和图像

> 原文：<https://www.sitepoint.com/css-anthology-tips-tricks-3/>

考虑到 CSS 纯粹主义者喜欢的许多设计，你可能会认为图像很快就会成为过去，而不是干净的、符合标准的、CSS 格式的、基于文本的设计。

然而，尽管与 CSS 布局“风格”的简洁相比，完全依赖切片图像的网站开始显得有点过时，但放置得当的图像可以使原本平凡的设计变得生动。而且，随着设计者开始拓展符合标准的语义标记所能达到的界限，成功地将语义和美感结合起来的网站变得越来越常见。

在 CSS 中处理图像需要一些简单的技巧，然后可以将它们结合起来创建有趣的效果。本章中的解决方案演示了处理图像的基本概念，同时回答了一些常见问题。然而，正如本书中的大多数解决方案一样，不要害怕尝试，看看你能创造出什么独特的效果。别忘了，如果你想离线阅读这些信息，你可以下载。pdf 版的前 4 章在这里。

##### 如何给图片添加边框？

照片图像，也许用来说明一篇文章，或者作为相册中的展示，当用细线镶边时看起来很整洁。然而，在图形程序中打开每个镜头来添加边框是一个耗时的过程，如果您需要改变边框的颜色或厚度，您需要再次经历相同的艰苦过程来创建所需的图像。

***解***

使用 CSS 为图像添加边框是一个简单的过程。图 3.1 显示了文档中的两个图像。

![1430_3.1](img/1034929d58a4fedaf52c38cbd01e73df.png)
图 3.1。图像显示在网络浏览器中。

```
img { 

  border-width: 1px; 

  border-style: solid; 

  border-color: #000000; 

}
```

这段代码也可以写成如下形式:

```
img { 

  border: 1px solid #000000; 

}
```

在图 3.2 中，您可以看到这对图像的影响。

![1430_3.2](img/556a9d114a679cd533bfdbdcc78281a0.png)
图 3.2。一旦应用了 CSS 边框，图像看起来会更整洁。

您的布局可能包含您不想应用永久黑色边框的图像。您可以为边框创建一个 CSS 类，并根据需要将其应用于选定的图像。

```
.imgborder { 

  border: 1px solid #000000; 

} 

<img src="myfish.jpg" alt="My Fish" class="imgborder" />
```

如果您在页面上有一个完整的图像选择，比如一个相册，您可以为一个容器中的所有图像设置边框。

例 3.1。imageborders.css(节选)

```
#album img { 

  border: 1px solid #000000; 

}
```

这种方法将使您不必在容器中为每个单独的图像添加类。

##### 我如何使用 CSS 来替换图片上不推荐使用的 HTML border 属性？

如果你和我一样，给链接到其他文档的图片添加`border="0"`可能是你几乎自动做的事情。使用`<img>`标签的 border 属性是我们都知道的确保导航按钮周围不会出现难看的蓝色边框的方法。但是，在 HTML 和 XHTML 的当前版本中，border 已经被弃用。

***解***

正如您可以创建边框一样，您也可以删除边框。将图像的 border 属性设置为 none 将删除那些难看的边框。

```
img { 

  border: none; 

}
```

##### 如何用 CSS 为我的页面设置背景图片？

在 CSS 之前，背景是使用`<body>`标签的背景属性添加的。此属性现在已被弃用，并被 CSS 属性所取代。

***解***

例 3.2。背景. css

```
body { 

  background-color: #ffffff; 

  background-image: url(peppers_bg.jpg); 

  background-repeat: no-repeat; 

}
```

上述规则将 peppers_bg.jpg 图像作为背景添加到任何附加了该样式表的页面，如图 3.3 所示。

![1430_3.3](img/df78ba3d3f46db7f3051a001594afb4a.png)
图 3.3。标准图像显示为背景图像。

***讨论***

CSS 属性 background-image 使您能够将背景图像的位置指定为 URL。默认情况下，背景将平铺显示，如图 3.4 所示。

由于我们不希望在这个例子中有多个辣椒，我们需要防止这个图像平铺。为此，我们将属性 background-repeat 设置为 no-repeat。背景重复的其他值有:

*   `repeat`–该默认值使图像在页面上下平铺，如图 3.4 所示。
*   `repeat-x`–图像在页面上平铺成单行图像，如图 3.5 所示。
*   `repeat-y`–图像在页面上向下平铺成一行，如图 3.6 所示。

![1430_3.4](img/123fab2cbd52756933482ba7e6758d97.png)
图 3.4。默认情况下，背景图像设置为重复。

![1430_3.5](img/a7e820b7282defe6c65408b627596d93.png)
图 3.5。背景图像设置为重复-x。

![1430_3.6](img/e3e0bf0389f12c9ccaf649fe9d2995a7.png)
图 3.6。背景图像设置为重复-y。

##### 我如何定位我的背景图片？

默认情况下，如果向页面添加单个不重复的背景图像，它将出现在视窗的左上角。如果您将背景设置为向任何方向平铺，第一个图像将出现在该位置，并从该点平铺。然而，图像也可以显示在页面上的任何其他地方。

***解***

为了定位图像，我们需要使用 CSS 属性 background-position。

例 3.3。backgroundposition.css(节选)

```
body { 

  background-color: #FFFFFF; 

  background-image: url(peppers_bg.jpg); 

  background-repeat: no-repeat; 

  background-position: center center; 

}
```

上面的 CSS 将图像在页面上居中，如图 3.7 所示。

![1430_3.7](img/b8278978c8578be4629d2f5a390959eb.png)
图 3.7。background-position 属性可用于使图像居中。

***讨论***

`background-position`属性可以将关键字、百分比值或单位值(如像素)作为值。

*关键词*

在上面的例子中，我们使用关键字来指定背景图像应该显示在页面的中央。

例 3.4。backgroundposition.css(节选)

```
 background-position: center center;
```

您可以使用的关键字组合有:

*   `top left`
*   `top center`
*   `top right`
*   `center left`
*   `center center`
*   `center right`
*   `bottom left`
*   `bottom center`
*   `bottom right`

如果只指定其中一个值，另一个值将默认为居中。

```
background-position: top;
```

上面的代码与下面的代码相同:

```
background-position: top center;
```

*百分比值*

为了确保更准确的图像放置，您可以将这些值指定为百分比。这在液体布局中特别有用，在液体布局中，其他页面元素以百分比指定，以便它们根据用户的屏幕分辨率和大小调整大小。

```
background-position: 30% 80%;
```

此处包含的两个百分比中的第一个是指水平位置；第二是垂直位置。百分比取自左上角，0% 0%将图像的左上角放在浏览器窗口的左上角，100% 100%将图像的右下角放在窗口的右下角。

与关键字一样，如果您只指定了一个值，默认值就会起作用。这个默认值是 50%。

```
background-position: 30%;
```

因此，上面的代码与下面的代码相同:

```
background-position: 30% 50%;
```

*单位值*

您可以使用任何 CSS 单位来设置这些值，例如像素或 ems。

```
background-position: 20px 20px;
```

正如百分比一样，第一个值表示水平位置，而第二个值表示垂直位置。但与百分比不同，度量直接控制背景图像左上角的位置。

您可以混合单位和百分比，如果您只指定一个值，第二个值将默认为 50%。

##### 当页面滚动时，如何制作一个文本移动而背景图像保持不动？

你可能见过这样的网站，当内容滚动时，背景图像保持静止。这个效果是使用 CSS 实现的。

***解***

例 3.5。backgroundfixed.html(节选)

```
body {  

  background-color: #FFFFFF;  

  background-image: url(peppers_bg.jpg);  

  background-repeat: no-repeat;  

  background-position: 20px 20px;  

  background-attachment: fixed;  

}
```

![1430_3.8](img/624234d5430b7845b89a7f84b10cc38f.png)
图 3.8。背景图像是固定的，不会随着内容一起滚动。

我们可以使用值为 fixed 的属性 background-attachment 来固定背景，使其不随内容移动，如图 3.8 所示。

***讨论***

在这个解决方案中，我们使用几个 CSS 属性将我们的图像添加到背景中，定位它，并详细说明它在文档滚动时的行为。也可以使用一种简化的方法来写出这些信息，应用 CSS 属性`background`。该属性允许您在一次财产申报中申报`background-color`、`background-image`、`background-repeat`、`background-attachment`和`background-position`。

以下面显示的 CSS 声明为例:

例 3.6。backgroundfixed.css(节选)

```
body {  

  background-color: #FFFFFF;  

  background-image: url(peppers_bg.jpg);  

  background-repeat: no-repeat;  

  background-attachment: fixed;  

  background-position: 20px 20px;  

}
```

这些也可以写成如下形式:

```
body {  

  background: #ffffff url(peppers_bg.jpg) no-repeat fixed 20px  

    20px;  

}
```

##### 如何为其他元素设置背景图片？

在这一章中，我们已经学习了为文档设置背景图片。然而，背景图像也可以用在其他元素上。

***解***

例 3.7。backgrounds2.css(节选)

```
#smallbox {  

  background-image: url(mini_chilli.jpg);  

  background-repeat: repeat-y;  

  float: left;  

  padding-left: 60px;  

  margin-right: 20px;  

  width: 220px;  

  border: 1px solid #F5C9C9;  

}
```

图 3.9 中出现在方框左侧的红辣椒构成了一个背景，它是通过将背景图像平铺在 y 轴上而创建的。此处显示的背景图像应用于一个`<div>`，连同其他规则，以创建该框。

![1430_3.9](img/29433e7f2607d5f1cb65cca0c71c1ced.png)
图 3.9。辣椒图像是平铺的背景图像。

***讨论***

背景图像可以用在任何页面元素上。您可以对标题应用背景，如图 3.10 所示:

例 3.8。backgrounds2.html(节选)

```
<h1 class="header">Chinese style stuffed peppers</h1>
```

例 3.9。backgrounds2.css(节选)

```
.header {  

  background-image: url(dotty.gif);  

  width: 400px;  

  height: 30px;  

  padding: 6px 6px 4px 8px;  

  color: #B22222;  

  background-color: transparent;  

}
```

![1430_3.10](img/9751b9d735c281cccfee9ca0be5dd4ab.png)
图 3.10。标题有背景图像。

你甚至可以在链接上应用背景，这可以帮助你制造一些有趣的效果，如图 3.11 所示。

例 3.10。backgrounds2.css(节选)

```
a:link, a:visited {  

  color: #B22222;  

  background-color: transparent;  

  font-weight: bold;  

}  

a:hover {  

  background-image: url(dotty.gif);  

  text-decoration: none;  

}
```

![1430_3.11](img/5ccb2c934e76e3984a41ccfbda8a71ac.png)
图 3.11。悬停时，背景图像应用于链接。

##### 如何在图片上放置文字？

在 CSS 出现之前的糟糕日子里，向图像添加文本的唯一方法是在图形程序中添加文本！CSS 提供了更好的方法来达到这个效果。

***解***

将文本层叠在图像上最简单的方法是将图像作为背景图像。图 3.12 中显示的配料列表下方的辣椒图像是使用以下属性添加的:

例 3.11。backgrounds3.css(摘录)

```
 background-image: url(chilli2.jpg);  

  background-repeat: no-repeat;
```

![1430_3.12](img/0170c58b2f9af262fdcf6290cc3f0f3b.png)
图 3.12。辣椒图像是背景图像。

***讨论***

与在图形程序中简单地将文本添加到图像中相比，使用 CSS 在图像上放置文本有许多优点。

首先，更改属于图形一部分的文本更加困难。为此，您需要找到原始图形，在图形程序中编辑它，并在每次需要更改文本时上传它。

其次，文本作为文本内容比作为图像的一部分更容易访问。不支持图像的浏览器仍然可以阅读以 CSS 格式添加的文本。这也有利于你的搜索引擎排名——搜索引擎不能索引图片中的文本，但会将图片上的文本视为普通文本，并相应地进行索引。

##### 如何在我的文档中添加多个背景图像？

不可能在您的文档中添加多个背景图像。然而，通过将背景应用于嵌套的元素，例如`<html>`标签和`<body>`标签，可以给出多个背景图像的效果。

例 3.12。backgrounds4.css(摘录)

```
html {  

  background-image: url(mini_chilli.jpg);  

  background-repeat: repeat-y;  

  background-position: 2px 2px;  

  background-attachment: fixed;  

}  

body {  

  background-image: url(peppers_bg.jpg);  

  background-repeat: no-repeat;  

  background-position: 80px 20px;  

}
```

这种影响可以在图 3.13 中看到。

![1430_3.13](img/3453d6de8d7df77f94df5a822b192f83.png)图 3.13
。背景图像应用于`<html>`和`<body>`元素。

***讨论***

这个简单的例子可以形成使用多个背景图像的更复杂效果的基础。正如你在本章的例子中看到的，背景图像可以应用于页面上的任何元素。以这种方式仔细和创造性地使用图像可以创建许多有趣的视觉效果，同时保持文档的可访问性(因为背景图像不会干扰文档的结构)。

CSS Zen garden 中的许多条目都依赖于对背景图像的精心使用来实现它们的布局。

##### 摘要

本章解释了一些常见图像相关问题的答案。当然，整本书都会有与图像相关的问题。具体来说，处理定位的章节将探讨图像以及页面上其他元素的定位。

注意下一个——也是最后一个！—[章*CSS 选集:101 个基本技巧、窍门&黑掉*](https://www.sitepoint.com/books/cssant1)——很快就会在 SitePoint 上发布。或者，如果你等不及了，[现在就免费下载前 4 章](https://www.sitepoint.com/show-modal-popup-after-time-delay/)。

## 分享这篇文章