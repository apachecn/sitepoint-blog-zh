# Illustrator 绘图工具，第 5 部分:钢笔工具–曲线

> 原文：<https://www.sitepoint.com/illustrator-drawing-tools-part-5-pen-tool-curves/>

##### 这是 Illustrator 绘图工具系列的最后一部分。在上一篇文章中，我们看了如何使用钢笔工具绘制直线段，以及如何使用选择工具编辑整个路径和单个点。作为这个系列的总结，今天的帖子是关于用钢笔画曲线的。这通常被认为是使用钢笔工具最难的部分，但它需要的只是一些练习和对曲线如何工作的理解。

**绘制曲线路径**

与直线路径不同，曲线路径是通过单击和拖动创建的。第一次单击并拖动时，您设置了曲线路径的起点，重要的是，您还确定了曲线的方向。继续拖移时，会在上一个点和当前点之间绘制一条曲线路径。

当您使用钢笔工具单击并拖动时，会发生两件事。

1.单击时，在画板上放置一个锚点。

2.拖动时会绘制方向线和方向点。

我们使用方向线和点来确定我们正在绘制的曲线路径的方向和形状。我们开始画画吧。

1.从工具箱中选择 [![image001](img/f0b47f6385c361ebce28e76922c6a68f.png)](https://www.sitepoint.com/wp-content/uploads/2009/06/image0015.png) 钢笔工具或者按 P 键作为快捷键。

2.单击并立即向上拖动钢笔工具指针。
[![image003](img/a77d22c2f4e2fd1513fda39041ac5492.png)](https://www.sitepoint.com/wp-content/uploads/2009/06/image0035.png) 
当您释放鼠标按钮时，您第一次单击的位置会出现一个锚点，锚点的上方和下方会延伸出两条方向线。

3.通过单击并向相反方向拖动来添加另一个点，继续绘制曲线。
[![image005](img/5c7085943dca0661cc4945629e4ef066.png)](https://www.sitepoint.com/wp-content/uploads/2009/06/image0054.png)

如果您在绘制时出错，请选取“编辑”>“撤销新锚点”( **Ctrl +Z / Cmd + Z** )来撤销您绘制的最后一个点，然后再试一次。

 *4.再次重复同样的操作，添加另一条曲线。

[![image007](img/cc0304011742be7881266b6a80830894.png)](https://www.sitepoint.com/wp-content/uploads/2009/06/image0074.png)

如果在单击并拖动的同时按住 Shift 键，则方向线的斜率限制为 45 度增量。

5.要完成你的路径，只需 **Ctrl + Click / Cmd + Click** 。

**画一条封闭的曲线路径**

下面是如何用钢笔画一个封闭的路径，在这个例子中是一个圆。请记住，您总是希望使用尽可能少的锚点。

1.单击并向上拖动(或向下-拖动的方向就是曲线的走向)。
[![image009](img/9abb03556f7d12b3b02ae94cef72030d.png)](https://www.sitepoint.com/wp-content/uploads/2009/06/image0094.png)

2.从起点正对面的新点向下拖动。

[![image011](img/a6365b17a637b2d12eca4911deda9f9b.png)](https://www.sitepoint.com/wp-content/uploads/2009/06/image0114.png) 
3。将指针放在起点上，单击并再次向上拖动，直到得到所需的曲线，从而闭合路径。

[![image013](img/70667f93260c23728e1495461f6fbe37.png)](https://www.sitepoint.com/wp-content/uploads/2009/06/image0134.png)

##### **组合曲线和直线路径段**

现在你已经学会了如何分别绘制直线和曲线，你需要能够把它们放在一起。您将组合包含拐角点和平滑点的路径。平滑锚点的方向线彼此相反，相隔 180 度。角点没有方向线，只有一条方向线，或者两条方向线的角度不是 180 度。(有点迷惑，我知道！)

可能你要做的最困难的组合是在一个角点上连接两条曲线。如果你能做到这一点，那么你就可以用这支笔画任何类型的画。假设你想创建一个看起来像这样的东西；

[![image015](img/f09759892b7b5e5c1309cc179352dd97.png)](https://www.sitepoint.com/wp-content/uploads/2009/06/image0152.png)

1.选择钢笔工具。

2.我们从直线段开始，这非常容易，所以单击一次作为起点(不要拖动)，再次单击放下第二个锚点。
[![image017](img/10efc295f0b2e35894dea1d82b06ad64.png)](https://www.sitepoint.com/wp-content/uploads/2009/06/image0172.png)

3.现在我们要进入一个弯道。当您将指针移到最后一个锚点上时，光标会发生变化，显示出看起来像倒置的“v”形。这表示您要将该点从一个直点转换为一个弯点。单击并一次向上拖动以开始曲线。然后单击并向下拖动，创建下一个锚点，并完成您开始的曲线。
[![image019](img/3ea509ebee72d50713941dccf177b23d.png)](https://www.sitepoint.com/wp-content/uploads/2009/06/image0193.png)

[![image021](img/8da99bf79e5a02c47faba6b64e97b050.png)](https://www.sitepoint.com/wp-content/uploads/2009/06/image0213.png)

现在我们需要另一条曲线。然而，你会发现，如果你只是简单地点击和拖动一个新的点，它不会去你想要的地方。你可能会得到这样的结果。

**[![image023](img/65f7eb59cf5b1ad640a950ed6e62f079.png)](https://www.sitepoint.com/wp-content/uploads/2009/06/image0233.png)**

**或者类似这样的东西，如果你向相反的方向拖动。**

[![image025](img/4fbc71cb266cb1ac8389bf5eab23468e.png)](https://www.sitepoint.com/wp-content/uploads/2009/06/image0253.png)

4.为了防止这种情况发生，我们需要在最后一个锚点上按住 **Alt / Option +单击**来开始一条新曲线。你会再次看到一个上下颠倒的小 v。按住 Alt/Option 键，向上拖动以创建第二条向上的曲线。然后单击并向下拖动下一个点来完成曲线。你应该有这样的东西:

[![image027](img/5bd587db5fa0e76358c1c81fb8b5fa65.png)](https://www.sitepoint.com/wp-content/uploads/2009/06/image0273.png)

5.重复步骤 4，创建第三个“向上”的曲线。

[![image029](img/ce38566ae43931756f65a766556de947.png)](https://www.sitepoint.com/wp-content/uploads/2009/06/image0292.png)

 **6.最后，我们需要将一个曲线点转换成一个直线点。再次按住 **Alt / Option** 并点击一次你的最后一个锚点，释放 Alt / Option 键，然后再次点击放下你的最后一个锚点。您现在应该已经创建了一个如下所示的路径；

 **[![image031](img/70a81722e995a6dbc61512649e685b36.png)](https://www.sitepoint.com/wp-content/uploads/2009/06/image0312.png)

7. **Ctrl / Cmd +点击任意位置**完成路径。

 ****

当然，关于钢笔工具还有很多要学，但是在这个阶段，大量的结合直线段和曲线的练习会让你很快成为一名钢笔骑师。我将在接下来的几周回到这个话题，并向你展示一些技巧和窍门来提高和改进你的钢笔工具的使用。同时，用钢笔工具练习组合曲线和直线点的一个很好的方法是尝试画出字母的轮廓。从简单的 I 和 Z 开始，然后尝试小写的 T，S，P 等等。这是我给我的班级做的一个练习，他们觉得很有用。
[![image033](img/9c73546d446c5e77b2dda963866d9683.png)](https://www.sitepoint.com/wp-content/uploads/2009/06/image0333.png)

我希望你已经发现这个关于 Illustrator 绘图工具的系列很有用。

你是钢笔工具的新手吗？这是你一直在逃避的事情吗？你还想进一步了解 Illustrator 中的哪些工具？

**相关阅读:**

[在 Illustrator 中绘图，第一部分:线条工具](https://www.sitepoint.com/drawing-in-illustrator-part-1-the-line-tools/)
[在 Illustrator 中绘图，第二部分:形状工具](https://www.sitepoint.com/drawing-in-illustrator-part-2-the-shape-tools/)
[在 Illustrator 中绘图，第三部分:铅笔&平滑工具](https://www.sitepoint.com/illustrator-drawing-tools-part-3-the-pencil-tool/)
[在 Illustrator 中绘图，第四部分:钢笔工具——直线](https://www.sitepoint.com/drawing-in-illustrator-part-4-pen-tool-straight-lines/)***** 

## *****分享这篇文章*****