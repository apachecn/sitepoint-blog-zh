# 用石英 2D 制作图表:第 3 部分

> 原文：<https://www.sitepoint.com/creating-a-graph-with-quartz-2d-part-3/>

为了创建一个折线图，我将重用我们在本系列的前一部分中用于绘制条形图的同一个项目。我们不再需要绘制线条的逻辑，所以在`drawRect`方法的末尾注释掉十几行代码，这些代码处理线条。

我还将`kOffsetX`的值更改为 0，以便线图从视图的左边缘开始。我们需要向数据集添加几个值，您可以选择 0 到 1 之间的任何值。以下是我的选择:

```
float data[] = {0.7, 0.4, 0.9, 1.0, 0.2, 0.85, 0.11, 0.75, 0.53, 0.44, 0.88, 0.77, 0.99, 0.55}; 
```

创建一个新的空方法，确保它在`drawRect`之前:

```
- (void)drawLineGraphWithContext:(CGContextRef)ctx
{

} 
```

我们将逐步用代码填充这个方法。第一步是设置环境，在这种情况下定义我们将要使用的线条的宽度和颜色。以下两行代码应该不会让您感到惊讶:

```
CGContextSetLineWidth(ctx, 2.0); 
CGContextSetStrokeColorWithColor(ctx, [[UIColor colorWithRed:1.0 green:0.5 blue:0 alpha:1.0] CGColor]); 
```

您已经熟悉了绘制路径——我们已经将条绘制为路径——因此，下面几行代码虽然略有不同，但应该是完全可以理解的:

```
int maxGraphHeight = kGraphHeight - kOffsetY;
CGContextBeginPath(ctx);
CGContextMoveToPoint(ctx, kOffsetX, kGraphHeight - maxGraphHeight * data[0]);

for (int i = 1; i < sizeof(data); i++) 
{
    CGContextAddLineToPoint(ctx, kOffsetX + i * kStepX, kGraphHeight - maxGraphHeight * data[i]);
}

CGContextDrawPath(ctx, kCGPathStroke); 
```

这就是我们需要画一个简单的线图。运行该应用程序，您应该会看到以下内容:

[![Quartz 2D Part III Figure 1](img/a9b62b14cc4eebd1fd59b3a7c79dd0fa.png "Quartz 2D Part III Figure 1")](https://www.sitepoint.com/wp-content/uploads/2011/10/Graph16.png)

图 1

对于一些简单的情况，这个图表可能是可以接受的，但是有很多方法可以增强它。其中一个就是让数据点更加明显。

### 强调数据点

一个流行的解决方案是在每个数据点的顶部画一个小圆圈，我们就这么做吧。首先，我们希望用颜色填充圆圈，所以将下面一行代码添加到`drawLineGraphWithContext`方法的最后:

```
CGContextSetFillColorWithColor(ctx, [[UIColor colorWithRed:1.0 green:0.5 blue:0 alpha:1.0] CGColor]); 
```

接下来，让我们在头文件中定义圆半径:

```
#define kCircleRadius 3 
```

在石英 2D 中，要画一个圆或椭圆，我们首先需要创建一个矩形(`CGRect`)来定义这个圆的尺寸。创建这样一个矩形最简单的方法是使用带有四个参数的`CGRectMake`函数:矩形左上角的 x 和 y 坐标，然后是它的宽度和高度。

我们将以这样的方式创建矩形，它们的中心与我们刚刚绘制的线图所经过的点重合。然后，我们将在每个矩形内画一个圆，最后，我们将描边并填充这些圆。总而言之，代码看起来像这样:

```
for (int i = 1; i < sizeof(data) - 1; i++) 
{
    float x = kOffsetX + i * kStepX;
    float y = kGraphHeight - maxGraphHeight * data[i];
    CGRect rect = CGRectMake(x - kCircleRadius, y - kCircleRadius, 2 * kCircleRadius, 2 * kCircleRadius);
    CGContextAddEllipseInRect(ctx, rect);
}
CGContextDrawPath(ctx, kCGPathFillStroke); 
```

将它添加到`drawLineGraphWithContext`方法的末尾，这就是您应该看到的结果:

[![Quartz 2D Part III Figure 2](img/c1495ea8c12a3353b2cdc3857370dc68.png "Quartz 2D Part III Figure 2")](https://www.sitepoint.com/wp-content/uploads/2011/10/Graph17.png)

图 2

我们可能想要做的下一个增强是用一种颜色填充图形下面的空间。

### 填充图表

创建一个充满颜色的图形的步骤与创建图形线条的步骤非常相似，唯一的区别是我们需要创建一个封闭的路径。让我们看看如何做到这一点。

在`drawLineGraphWithContext`方法的开始，在定义了`maxGraphHeight`之后，让我们指定一种填充颜色——与我们用来绘制图形本身的颜色相同，但是半透明:

```
CGContextSetFillColorWithColor(ctx, [[UIColor colorWithRed:1.0 green:0.5 blue:0 alpha:0.5] CGColor]); 
```

然后我们基本上是把图形画出来，但是加几条线围出一个封闭的空间。最后，我们填充生成的路径，但不描边。跟着代码走，你会看到发生了什么:

```
CGContextBeginPath(ctx);
CGContextMoveToPoint(ctx, kOffsetX, kGraphHeight);
CGContextAddLineToPoint(ctx, kOffsetX, kGraphHeight - maxGraphHeight * data[0]);
for (int i = 1; i < sizeof(data); i++) 
{
    CGContextAddLineToPoint(ctx, kOffsetX + i * kStepX, kGraphHeight - maxGraphHeight * data[i]);
}
CGContextAddLineToPoint(ctx, kOffsetX + (sizeof(data) - 1) * kStepX, kGraphHeight);
CGContextClosePath(ctx);

CGContextDrawPath(ctx, kCGPathFill); 
```

填充完成后，我们绘制图形线本身，并在其上画圆，如前所述。如果你现在运行这个应用程序，你会发现图表变得更好看了:

[![Quartz 2D Part III Figure 3](img/6228ca5bd41ef7e5c0b85049529f7955.png "Quartz 2D Part III Figure 3")](https://www.sitepoint.com/wp-content/uploads/2011/10/Graph18.png)

图 3

为了使它更好，我们可能想用渐变填充图形。你已经知道如何创建一个渐变，所以第二次应该会更容易。

### 用渐变填充图形

下面是准备渐变的代码，这次没有注释。将其插入到我们在上一节中编写的用于创建实心填充的代码之前:

```
CGGradientRef gradient;
CGColorSpaceRef colorspace;
size_t num_locations = 2;
CGFloat locations[2] = {0.0, 1.0};
CGFloat components[8] = {1.0, 0.5, 0.0, 0.2,  // Start color
        1.0, 0.5, 0.0, 1.0}; // End color
colorspace = CGColorSpaceCreateDeviceRGB(); 
gradient = CGGradientCreateWithColorComponents(colorspace, components, locations, num_locations);

CGPoint startPoint, endPoint;
startPoint.x = kOffsetX;
startPoint.y = kGraphHeight;
endPoint.x = kOffsetX;
endPoint.y = kOffsetY; 
```

现在，我们将重用我们在上一节中创建的闭合路径。这一次，它将作为一个剪辑路径。注释掉这一行:

```
CGContextDrawPath(ctx, kCGPathFill); 
```

用另外两个来代替:

```
CGContextSaveGState(ctx);
CGContextClip(ctx); 
```

我们现在准备将渐变注入图形中。请继续:

```
CGContextDrawLinearGradient(ctx, gradient, startPoint, endPoint, 0); 
```

最后，进行清理:

```
CGContextRestoreGState(ctx);
CGColorSpaceRelease(colorspace);
CGGradientRelease(gradient); 
```

下面是您应该看到的结果:

[![Quartz 2D Part III Figure 4](img/948c44d3aef6db65bf217a0d86b3c461.png "Quartz 2D Part III Figure 4")](https://www.sitepoint.com/wp-content/uploads/2011/10/Graph19.png)

图 4

不错，是吗？当然，如果你愿意，你可以调整梯度。

现在我们知道如何绘制条形图和线图。它们看起来不错，但是还不能交互。比如说，如果当用户点击一个工具条时，图形会显示一条适合该工具条的消息，那就太好了。这正是我们在本系列的下一部分要处理的问题。

### 石英 2D 指数

亚历山大·科列斯尼科夫关于使用石英 2D 制作图表的系列文章分为 5 个部分。您可以使用[石英 2D 标签](https://www.sitepoint.com/creating-a-graph-with-quartz-2d/)参考该系列，并使用以下链接访问单篇文章。

*   [用石英 2D 制作图表:第一部分](https://www.sitepoint.com/creating-a-graph-with-quartz-2d/)
*   [用石英 2D 制作图表:第二部分](https://www.sitepoint.com/creating-a-graph-with-quartz-2d-part-2/)
*   用石英 2D 制作图表:第 3 部分
*   [用石英 2D 制作图表:第四部分](https://www.sitepoint.com/creating-a-graph-with-quartz-2d-part-4/)
*   [用石英 2D 制作图表:第五部分](https://www.sitepoint.com/creating-a-graph-with-quartz-2d-part-5/)

## 分享这篇文章