# Fireworks CS3 功能:智能缩放

> 原文：<https://www.sitepoint.com/fireworks-cs3-features-intelligent-scaling/>

*从 SitePoint 设计视图#33* 重新发布

Adobe 对 Fireworks 的第一次尝试——CS3，或旧时代的版本 9——终于发布了，并得到了一些好评。由于我们上周才收到完整的审查版，我将在下一个设计视图中发布完整的总结，但目前我认为我应该看看该包最好的新功能之一:智能或“9 切片”缩放。

闪光灯或焰火中的符号一直是一个伟大的概念。如果你使用一点 CSS，有一些非常熟悉的东西，能够定义一个永久的参考图形，然后在你的作品中使用该图形的链接实例或副本。对元件的更改会自动传递到副本，就像 CSS 更改传递到文档一样。

我发现使用符号的最大缺点是缩放问题。虽然单选按钮的大小调整得不优雅并不重要，但我们在典型设计中可能使用的大多数按钮、文本字段、下拉菜单、滚动条、选项卡和其他屏幕小部件都需要调整大小以适应它们包含的内容。在过去，调整 Fireworks(或 Flash，就此而言)中的符号大小会扭曲图形的形状，就像游乐园的镜子一样。虽然你可以学会忍受小的扭曲，但是大的尺寸调整问题是一个真正的问题。

Fireworks CS3 通过引入 9 切片缩放解决了这个问题。这个过程控制起来相对简单，虽然新的符号只能在 CS3 中很好地缩放，但它们仍然会像在旧版 Fireworks 中一样工作。让我们浏览一遍。

1.  **Convert to Symbol:** Select your original graphic and convert it to a symbol *Modify/Symbols/Convert to Symbol*). A dialog box allows you to name your symbol, and you’ll see it has two extra checkboxes at the bottom — tick the box marked “Enable 9-slice scaling guides.”

    ![Covert to symbol dialog](img/c0d6eacbc82985a601d6d7a9125512e8.png)

2.  **符号编辑器:**你的新符号将出现在符号编辑器中，但是增加了一组蓝色、虚线、可移动的参考线——两个垂直，两个水平。
3.  **Setting the scaling guides:** The blue guides create a 3×3 grid that lets us control which bits scale, and how. As the illustration below shows:
    *   中上和中下部件只允许水平缩放
        。
    *   允许左中右部分垂直缩放
        。
    *   四个角块根本不允许缩放。
    *   中心部分向各个方向扩展。

    滑动导轨，直到将角块与可平铺的侧块、顶块和底块完全隔离。

    ![The Symbol Editor](img/b07617dc81c51957418d8e34db545016.png)

4.  **锁定您的指南:**当您满意时，点击左下角的“锁定”复选框，然后点击左上角的
    “完成”按钮。

    ![9-slice scaling in action (gif animation)](img/05f628b225faa36ee3d749672cf8588b.png)

5.  **试用:**将您的符号从库调色板拖到您的布局上，并尝试缩放它。您应该会得到如右图所示的结果。

在本例中，我将两个水平参考线都放在图形的外部，这意味着它只设计为水平缩放。类似地，您可以设计一个垂直滚动条，只沿垂直轴缩放。

当然，这种缩放技巧并不局限于按钮和小部件。整个布局、导航面板、圆形文本框或任何其他重复的设计元素都可以，而且毫无疑问将受益于这一新功能。

简而言之，这是一个让符号比以往任何时候都更有用的功能，所以我认为它是最有可能改变你工作方式的功能。

## 分享这篇文章