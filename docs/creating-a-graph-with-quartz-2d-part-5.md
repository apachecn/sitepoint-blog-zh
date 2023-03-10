# 用石英 2D 制作图表:第 5 部分

> 原文：<https://www.sitepoint.com/creating-a-graph-with-quartz-2d-part-5/>

在这一系列文章中，我讨论的是使用石英 2D 创建图表和图形，这是苹果公司创建的图形渲染 API，是核心图形的一部分。你可能希望跟上[第一部分](https://www.sitepoint.com/creating-a-graph-with-quartz-2d/)、[第二部分](https://www.sitepoint.com/creating-a-graph-with-quartz-2d-part-2/)、[第三部分](https://www.sitepoint.com/creating-a-graph-with-quartz-2d-part-3/)和[第四部分](https://www.sitepoint.com/creating-a-graph-with-quartz-2d-part-4/)。

### 绘图文本

最后，我们将在图表上写一些文字。你可能想知道为什么我把这么简单的任务推迟了这么久。事实上，在石英 2D 中画一根弦并不需要太多的努力。让我们来试试:在 GraphView 中，在`drawRect`的末尾添加下面的代码片段(在这个阶段显示哪种类型的图形并不重要):

```
// Drawing text
CGContextSelectFont(context, "Helvetica", 44, kCGEncodingMacRoman);
CGContextSetTextDrawingMode(context, kCGTextFill);
CGContextSetFillColorWithColor(context, [[UIColor colorWithRed:0 green:0 blue:0 alpha:1.0] CGColor]);
NSString *theText = @"Hi there!";
CGContextShowTextAtPoint(context, 100, 100, [theText cStringUsingEncoding:NSUTF8StringEncoding], [theText length]); 
```

这里没有什么复杂的:我们选择一种字体、一种颜色、一种绘制模式，然后在图形中的某个地方绘制一个随机的字符串。你认为结果会怎样？让我们看看:

[![Quartz 2D Part V Figure 1](img/02a9ddf567818c3ff16c07050cf0fe6d.png "Quartz 2D Part V Figure 1")](https://www.sitepoint.com/wp-content/uploads/2011/11/Graph23.png)

图 1

哎呀！这不完全是我们想要的:文本上下颠倒了。这是因为在 iOS 使用的坐标系统中，垂直坐标是从上到下的。基本上所有东西都是倒着画的。如果您试图使用或多或少复杂的图形背景，您可能已经注意到了这一点。

我们可以在绘制图形时记住这一特点，并相应地调整逻辑。我们可以使用不同的背景，但当涉及到绘制文本时，我们必须面对这个问题。

幸运的是，解决方案并不难。我们需要做的只是变换坐标系，使纵轴从图形底部开始向上，有几个函数可以让我们做到这一点。事实上，我们可以用一行代码解决我们的问题。在完成任何文本绘制之前插入它:

```
CGContextSetTextMatrix (context, CGAffineTransformMake(1.0, 0.0, 0.0, -1.0, 0.0, 0.0)); 
```

运行应用程序，这次文本应该看起来没问题:

[![Quartz 2D Part V Figure 2](img/7fbed44b928ca9d84e0b10416da5837a.png "Quartz 2D Part V Figure 2")](https://www.sitepoint.com/wp-content/uploads/2011/11/Graph24.png)

图 2

让我们试着理解拯救世界的咒语。整个魔术是在六个参数，我在这里只提到那些非零。你可以在 [CGAffineTransform](http://developer.apple.com/library/mac/#documentation/GraphicsImaging/Reference/CGAffineTransform/Reference/reference.html) 的文档中阅读该逻辑的详细解释。

所以我们在改变使用的坐标系统，对吗？这六个参数描述了新旧系统之间的关系。第一个参数描述了新系统的`x`坐标和旧系统的`x`坐标之间的关系。我们不想在这里做任何改变，所以参数的值是 1。第四个参数描述了新系统的`y`坐标和旧系统的`y`坐标之间的关系。我们只需要反转方向，因此值是-1。

如您所见，在这个特殊的案例中，一切都非常简单。然而，你可以自由地定义一个更复杂的变换，如你所愿地挤压和移动坐标。现在，让我们更好地考虑另一个有用的转换。代码如下:

```
CGContextSetTextMatrix(context, CGAffineTransformRotate(CGAffineTransformMake(1.0, 0.0, 0.0, -1.0, 0.0, 0.0), M_PI / 2)); 
```

你能猜出它是做什么的吗？用这个代替之前的魔法咒语，瞧:

[![Quartz 2D Part V Figure 3](img/4de540569c9e638fdaae1d0c81bbdf68.png "Quartz 2D Part V Figure 3")](https://www.sitepoint.com/wp-content/uploads/2011/11/Graph25.png)

图 3

嗯，我必须将 CGContextShowTextAtPoint 的第三个参数更改为 200，以确保文本保持在图形边界内。看，我们可以垂直绘制文本——这对于绘制标签非常有用。

总结一下，让我们做点实际的事情，给线图的数据点编号。下面是代码，您应该不会感到惊讶:

```
CGContextSetTextMatrix(context, CGAffineTransformMake(1.0, 0.0, 0.0, -1.0, 0.0, 0.0));
CGContextSelectFont(context, "Helvetica", 18, kCGEncodingMacRoman);
CGContextSetTextDrawingMode(context, kCGTextFill);
CGContextSetFillColorWithColor(context, [[UIColor colorWithRed:0 green:0 blue:0 alpha:1.0] CGColor]);
for (int i = 1; i < sizeof(data); i++) 
{
    NSString *theText = [NSString stringWithFormat:@"%d", i];
    CGContextShowTextAtPoint(context, kOffsetX + i * kStepX, kGraphBottom - 5, [theText cStringUsingEncoding:NSUTF8StringEncoding], [theText length]);
} 
```

这就是结果:

[![Quartz 2D Part V Figure 4](img/3080232c7cd67f618c65e8c4f0a87935.png "Quartz 2D Part V Figure 4")](https://www.sitepoint.com/wp-content/uploads/2011/11/Graph26.png)

图 4

看起来还可以，但是如果标签在数据点下面居中会好很多，你觉得呢？为了实现这一点，我们需要知道正在绘制的标签的宽度，然后针对标签宽度的一半对其 x 坐标进行校正。

这一行代码将给出标签的大小。显然，我们需要指定用于绘制文本的相同字体:

```
CGSize labelSize = [theText sizeWithFont:[UIFont fontWithName:@"Helvetica" size:18]]; 
```

最后，这是实际绘制标签的修改行:

```
CGContextShowTextAtPoint(context, kOffsetX + i * kStepX - labelSize.width/2, kGraphBottom - 5, [theText cStringUsingEncoding:NSUTF8StringEncoding], [theText length]); 
```

再次运行应用程序，现在标签将在数据点下很好地对齐:

[![Quartz 2D Part V Figure 5](img/a4bfe22c3b7c5f3067eca1056fd1727c.png "Quartz 2D Part V Figure 5")](https://www.sitepoint.com/wp-content/uploads/2011/11/Graph27.png)

图 5

正如我们在本系列开始时所说，使用石英 2D 并不难，你只需要知道如何做某些事情，一旦你学会了，你就可以快速前进。现在你可以使用石英 2D 做任何需要做的事情来绘制任何复杂程度的定制图。天空才是极限！

### 石英 2D 指数

亚历山大·科列斯尼科夫关于使用石英 2D 制作图表的系列文章分为 5 个部分。您可以使用[石英 2D 标签](https://www.sitepoint.com/creating-a-graph-with-quartz-2d/)参考该系列，并使用以下链接访问单篇文章。

*   [用石英 2D 制作图表:第一部分](https://www.sitepoint.com/creating-a-graph-with-quartz-2d/)
*   [用石英 2D 制作图表:第二部分](https://www.sitepoint.com/creating-a-graph-with-quartz-2d-part-2/)
*   [用石英 2D 制作图表:第三部分](https://www.sitepoint.com/creating-a-graph-with-quartz-2d-part-3/)
*   [用石英 2D 制作图表:第四部分](https://www.sitepoint.com/creating-a-graph-with-quartz-2d-part-4/)
*   用石英 2D 制作图表:第 5 部分

## 分享这篇文章