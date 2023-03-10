# Photoshop 入门:为 Web 保存

> 原文：<https://www.sitepoint.com/getting-started-photoshop-2/>

***为网页保存文件***

Photoshop 文件本身不能嵌入网页。您需要导出您的文件，并将其保存为网络友好的格式。web 图形有三种格式:gif、JPEGs 和 png。

**GIF**

GIF 格式(发音为“jiff”或“giff ”,取决于您是在轨道的哪一侧长大的)最多可以有 256 种颜色。GIF 文件支持透明和动画，最适合处理具有大面积相同颜色的图形，如下图所示。

**JPEG**

JPEG 格式(读作“jay-peg”)最适用于照片图像或具有超过 256 种颜色和渐变的图像，如反面页面上的花。以 JPEG 格式保存的图像被压缩，这意味着图像信息实际上会丢失，导致图像质量下降。

**PNG**

![Example of an image that should be saved as GIF or PNG](img/25fd4acdf1b84a9c4f81d9055b6d4be3.png)

PNG 格式(发音为“ping”)类似于 GIF 格式，因为它支持透明度，并且最适合纯色图像，如右图所示的徽标，但它优于 GIF 格式，因为它能够支持彩色区域的真实透明度。透明 png 目前在 Web 上没有广泛使用，因为旧版本的 Microsoft Internet Explorer 不支持它们；但是，它们经常在 Macromedia Flash 电影中使用。与 gif 相比，png 可以以更小的文件大小生成质量更好的图像。Photoshop 允许您将图像保存为 PNG-8 文件(其工作方式与 256 色的 GIF 文件相同)或 PNG-24 文件(允许数百万种颜色和可变透明度)。

*双击电源*
好像键盘快捷键还不够快，Windows 用户有了更多的方法来打开和保存文件， 如:按住 Ctrl 双击工作区新建文档双击工作区拉起打开对话框打开文件按住 Alt 双击工作区打开现有文件作为新文档按住 Ctrl-Shift 双击工作区保存文档按住 Shift 双击工作区访问 Adobe Bridge — Adobe 的“控制中心”和文件浏览器工作区是文档窗口后面的灰色区域。 如果你的快捷键不起作用，检查一下你点击的是工作区的一个空白点，而不是文档窗口或 Photoshop 工具！唉，Mac 上的 Photoshop 没有工作区，所以 Mac 用户无法享受双击快捷方式的好处。

![Example of an image that should be saved as JPEG](img/f322c96aefb7cb6e1f6eb0d623d34de2.png)

要在 Photoshop 中存储为 Web 格式，请选择“文件”>“存储为 Web 和设备格式”,或者按 Ctrl-Alt-Shift-S(在 Mac 上按 Command-Option-Shift-S)。这将打开背页所示的“存储为 Web 所用格式”对话框，该对话框将向您显示将要导出的图像的预览，其优化的大小显示在左下角。您可以使用右侧面板中的选项来调整图像的设置。选择是否要将文件保存为 GIF、JPEG、PNG-8 或 PNG-24，并尝试其他设置，同时注意优化的文件大小。尝试在图像的质量和文件大小之间取得平衡。当你对结果满意时，点击保存并给你的图像一个文件名。

如果你尝试了上面的练习，你可能会为自己保存了一个质量相当好的图片，而且文件比原来的要小很多而感到满意。您可以通过更改右侧窗格中的设置来实现这一点，但是这些设置实际上是做什么的呢？

**GIF/PNG-8**

*   **颜色**–调整此设置可减少图像中使用的颜色数量。这通常会对最终图像产生最大的影响。
*   **抖动量和类型(无抖动、扩散、图案、噪声)**–此设置与紧张或激动无关(尽管很有可能你刚才已经紧张或激动过了！).抖动指的是一种压缩技术，其中点的图案是变化的，以给出颜色渐变的错觉。更改抖动会导致大量颜色混合在一起的图像质量明显下降。
*   **透明**–如果你想要图形中的透明区域，勾选此框。我们将在第 2 章更仔细地研究透明性。
*   **哑光色**–对于透明图像，哑光色用于将图像边缘融入网页背景。对于不透明的图像，无光颜色定义图像的背景颜色。在第 2 章中会更详细地介绍在透明图像中使用无光色。

**JPEG**

*   **质量**–更改质量下拉框中的值会改变图像的压缩级别。降低质量可能会导致模糊或像素化，但过高的设置会产生一个大文件，用户需要很长时间才能下载。一个好的方法是逐渐降低质量值，直到您注意到图像质量下降到不可接受的程度。一个合理的妥协将在这一点上。

![The Save for Web dialog box](img/35c7ac0eb9f779828b3da90b49db9fa8.png)

**在 Fireworks 中保存网页文件**

![The Optimize palette in Fireworks](img/38e4865b68f4f1ee65493e5ad3838fbc.png)

您可以使用优化调色板在 Fireworks 中优化 Web 图像，如右图所示。预先在“优化”调板中设置文件类型和选项，当您准备好导出 web 图像时，请选择“文件”>“导出”或按 Ctrl-Shift-R(在 Mac 上为 Command-Shift-R)。Fireworks 将根据您定义的设置保存图像。

*为什么是两种工具？*
考虑到在 Photoshop 中可以保存网页文件，你想知道为什么需要 Fireworks 是完全合理的。虽然这两个程序确实可以执行许多相同的任务，但有些事情 Fireworks 可以做，而 Photoshop 不能。Fireworks 还使特定于 web 的任务变得更容易，因为它是一个较小的程序，不包含 Photoshop 的全套效果，所以加载速度比 Photoshop 更快。当你阅读这本书时，你会发现哪种工具更适合特定的任务。

##### Photoshop 图层

![A layered Photoshop document](img/56101fea642f99c423acee6f10dd552a.png)

图层是 Photoshop 的一项强大功能，允许您在不干扰图像其余部分的情况下处理图像的一部分。虽然层的概念可能看起来令人生畏，但是一旦你掌握了使用层的窍门，你就会想知道没有它们你是怎么活下来的！下一页的例子展示了 Photoshop 文档中右边的图层是如何堆叠在一起的。

任何层的透明部分，由方格网格显示，允许该层下面的层显示出来。

您可以通过在图层面板中单击相应的眼睛图标来显示和隐藏图像中的每个图层，如下页底部所示。

![The layers in a layered Photoshop document](img/d1f384508c0f50a7508cb6474bb74af9.png)

要组织您的层，您可以通过进入层>新建>组…将它们排列成层组。每个图层组的显示方式与图层面板上任何未分组的图层相同。图层组由文件夹图标表示。您可以通过单击文件夹图标左侧的三角形来折叠或展开层组，并通过将一个文件夹图标拖动到另一个文件夹图标来嵌套层组。

![Hiding a layer](img/f132d96573bf05e0d04f42b0fa8816b9.png)

**进入页面:**[1](/getting-started-photoshop)|[2](/getting-started-photoshop-2/)|[3](/getting-started-photoshop-3/)|[4](/getting-started-photoshop-4/)|[5](/getting-started-photoshop-5/)|[6](/getting-started-photoshop-6/)

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和互动在线课程[Photoshop 基础](https://learnable.com/courses/foundations-of-photoshop-2712?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)。

对本文的评论已经关闭。对 Photoshop 有疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?8-Graphics?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章