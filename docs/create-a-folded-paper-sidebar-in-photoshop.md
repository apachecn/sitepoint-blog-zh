# 在 Photoshop 中创建一个折叠纸边栏

> 原文：<https://www.sitepoint.com/create-a-folded-paper-sidebar-in-photoshop/>

纸张折叠是一种很好的效果，经常用在有分层或纹理表面的网站上。它可以消除网站的平坦性，我们将使用的方法可以应用到其他对象，如剥离贴纸效果，这在网页设计中也很流行。

1.创建一个尺寸为 450 像素宽 400 像素高的新文档(注意这些只是建议的尺寸，显然每个边栏的尺寸会根据您想要放入的内容而有所不同)。用你的网站背景颜色填充背景，我用的是#333333(深灰色)。

2.选择圆角矩形工具，并确保该工具的选项设置为形状层(第一个图标)，半径 10 像素，并选择你希望你的边栏的颜色。我用的是#99000(深红色)

[![2-ToolOptionsBar](img/7904777b36abe68b0f1b4aa5b767cfc2.png)](https://www.sitepoint.com/wp-content/uploads/2010/11/2-ToolOptionsBar.jpg)

3.画一个 350 像素宽，300 像素高的矩形。(提示:通过选择“窗口”>“信息”打开“信息”调板，以便您可以看到正在绘制的尺寸)。

[![1-RoundedRectangle](img/af2c95bac66f464d7bc5376ae68d9eac.png)](https://www.sitepoint.com/wp-content/uploads/2010/11/1-RoundedRectangle.jpg)

4.选择钢笔工具，设置图层形状，颜色设置为#cccccc(浅灰色)。画一个矩形，如下图所示。新的三角形层应该在圆角矩形层之上。

[![3-TriangleWithPenTool](img/122f1c53de54e350f97c89f81217509d.png)](https://www.sitepoint.com/wp-content/uploads/2010/11/3-TriangleWithPenTool.jpg)

5.在工具箱中的钢笔工具下，选择添加锚点工具。

##### [![3-AddAnchorPoint](img/382daaed795c6ba44c26b1fe26879a03.png)](https://www.sitepoint.com/wp-content/uploads/2010/11/3-AddAnchorPoint.jpg)

点击你刚刚画的三角形路径，添加两个额外的点，如下图所示。

[![5-AddAnchorpoints](img/060de0fb83682d5703ac3d9c468678be.png)](https://www.sitepoint.com/wp-content/uploads/2010/11/5-AddAnchorpoints.jpg)

6.用直接选择工具(白色箭头)点击右下角的点，轻轻地弯曲一条曲线，如图所示。然后对左下方的第二个点做同样的事情。

[![6-CurvePoints](img/ba6b864b5b8cf8880d3520c947f8aa06.png)](https://www.sitepoint.com/wp-content/uploads/2010/11/6-CurvePoints.jpg)

7.好了，这是大部分折叠到位，但现在我们需要折叠我们的矩形向下，以满足三角形。再次选择添加锚点工具，并添加两个新点，如图所示。

[![7-AddingExtraPoints](img/ad8981a0e6a3c96cefabf88278b45d79.png)](https://www.sitepoint.com/wp-content/uploads/2010/11/7-AddingExtraPoints.jpg)

8.现在我们需要做一些折叠。在添加了新点的矩形图层上，使用方向选择工具，将其中一个角点向下拖动到三角形后面。

[![8-TuckingAndFolding](img/ecb6044643838bbcfd5b4f5770e37d66.png)](https://www.sitepoint.com/wp-content/uploads/2010/11/8-TuckingAndFolding.jpg)

按住 Alt 键并拖动三角形顶部正上方添加的新点的手柄。按住 Alt 键可防止锚点另一侧的线条移动。

[![8-Morefolding](img/e5e459b9d9df859996396e4a5dca7c4e.png)](https://www.sitepoint.com/wp-content/uploads/2010/11/8-Morefolding.jpg)

用剩下的圆角矩形角点和新添加的锚点重复这个过程，这样你会得到如下结果:

[![9-Allcornersfolded](img/ccdd6f787ed1fc67c51a7eff97c33a44.png)](https://www.sitepoint.com/wp-content/uploads/2010/11/9-Allcornersfolded.jpg)

要完成整理，使用直接选择工具拖动到三角形的顶角点，以满足矩形的边缘。在这一点上，你可能也需要重新调整三角形上的曲线。这个过程的这一部分是一点点的尝试和错误，归结到什么取悦眼睛，所以发挥周围的锚点。

[![10-trianglemeetsrectangle](img/adfe7f0d17be79138444afb136562331.png)](https://www.sitepoint.com/wp-content/uploads/2010/11/10-trianglemeetsrectangle.jpg)

我最终的矩形和三角形看起来像这样:

[![11-FinalCurves](img/73ca1f0df0f0ca303628e27ad2dee69d.png)](https://www.sitepoint.com/wp-content/uploads/2010/11/11-FinalCurves.jpg)

9.选择三角形图层，点击图层面板底部的图层效果按钮，添加阴影效果。

我设置阴影的颜色更深，暗红色#330000 而不是黑色，不透明度设置为 50%，光线角度 90%，距离 4 像素，蔓延 10%和大小 10 像素。单击“确定”添加投影。

[![12-DropShadown](img/2e7bb3f29ee7a12f071ed0dde53fca3a.png)](https://www.sitepoint.com/wp-content/uploads/2010/11/12-DropShadown.jpg)

10.我们将添加同样的效果到圆角矩形层。点击三角形图层上的图层效果，按住 Alt/Option，拖动下拉阴影到圆角矩形图层上。这完全复制了你在三角形层上的东西。确保你没有把样式从一层拖到另一层。

[![13-DuplicateLayerEffect](img/8857130bdd97361fa4ac9729eacc5dbd.png)](https://www.sitepoint.com/wp-content/uploads/2010/11/13-DuplicateLayerEffect.jpg)

[![14-WithLayerStylesApplied](img/636f2d5e53f9902335ab322d0f10fc98.png)](https://www.sitepoint.com/wp-content/uploads/2010/11/14-WithLayerStylesApplied.jpg)

现在，为了让它看起来更有质感，我们将添加一个图案叠加到矩形层。在图层面板中选择它，然后点击添加图层样式按钮，选择图案叠加。当图层样式对话框打开时，点击小三角形并从菜单中选择艺术家表面。这给了你一堆可以应用的新纹理。我选择了画布，将不透明度降低到 70%，混合模式为正片。

[![15-PatternOverlay](img/30c631120f0421fbdd257ba59f478583.png)](https://www.sitepoint.com/wp-content/uploads/2010/11/15-PatternOverlay.jpg)

这使得“纸”看起来非常干净

[![15-PatternOverlayAppied](img/4a606b3ec55464e87546a9690ce63f34.png)](https://www.sitepoint.com/wp-content/uploads/2010/11/15-PatternOverlayAppied.jpg)

12.最后的任务是将你的导航添加到侧边栏，瞧！

[![Finished](img/6a29a9bb93840cbd47dfd70fbe168003.png)](https://www.sitepoint.com/wp-content/uploads/2010/11/Finished.png)

## 分享这篇文章