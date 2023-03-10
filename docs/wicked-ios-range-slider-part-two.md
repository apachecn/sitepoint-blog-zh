# 邪恶的 iOS 范围滑块:第二部分

> 原文：<https://www.sitepoint.com/wicked-ios-range-slider-part-two/>

在这个迷你系列的第一部分中，我们解决了当您想要创建一个范围的值，而不仅仅是一个值时，实现滑块的问题。如果你没有读过第一部分，你会感到有点失落，所以我建议你去看看 [iOS 范围滑块:第一部分](https://www.sitepoint.com/wicked-ios-range-slider-part-one/)。

还和我们在一起吗？很好。

还记得我们的范围滑块吗？今天我们要完成它。我们将在第一阶段添加代码，以允许我们的代码响应滑块值的变化。我们还将更新滑块，以便蓝色条仅显示值之间的内容。

最后，我们将看看滑块拇指图像的点击区域的大小，我指的是你实际触摸以移动它们的部分的大小，并确保它们符合苹果人机界面指南。基本上，最后一点的意思是，我们的胖手指实际上能够抓住滑块拇指！

### 求 X 的值

首先，我们需要写一个与我们的`xForValue`方法相反的方法。当给定一个 X 点时，这个函数需要返回值。我们称这个为`valueForX`。看到我做了什么吗？下面是完整的方法。

```
-(float) valueForX:(float)x{
    return minimumValue + (x-_padding) / (self.frame.size.width-(_padding*2)) * (maximumValue - minimumValue);
} 
```

在我们的新方法中，我们正在进行完全相同的计算，但方向相反。除了添加新方法之外，您还需要将这个新方法添加到主文件顶部的私有方法定义中。

现在我们有了这个方法，我们将在定位拇指后更新我们的`selectedMinimumValue`和`selectedMaximumValue`。在`continueTrackingWithTouch`的 if 语句中，我们应该更新适当的选择值。

```
// For min
selectedMinimumValue = [self valueForX:_minThumb.center.x];
NSLog(@"Lower value is now %f", selectedMinimumValue);

// For max
selectedMaximumValue = [self valueForX:_maxThumb.center.x];
NSLog(@"Upper value is now %f", selectedMaximumValue); 
```

保存包含新代码的文件。构建并运行项目，当它加载时，您应该看到当您拖动任一选择器时，调试日志显示新的值。很可爱吧？

### 激发更新值事件

当您使用范围滑块时，您将绑定一个动作到`UIControlEventValueChanged`事件，所以我们需要告诉我们的滑块在值改变时触发该事件。这是在`continueTrackingWithTouch`方法内部完成的。

```
// Below the if statements
[self sendActionsForControlEvents:UIControlEventValueChanged]; 
```

现在，`UIControlEventValueChanged`事件的任何观察者都会在每次值改变时得到通知。完美。

### 如此接近，我可以品尝它

现在是抛光的时候了。我们还剩下一些东西，比如只在滑块拇指之间把轨道变成蓝色，而不是整个轨道。然后我们将对照苹果的人机界面指南检查拇指的可用性。

### 更新可见轨迹

为了更新轨迹的蓝色部分，我们将创建一个名为`updateTrackHighlight`的方法来重新定位包含`bar-highlight.png`图像(蓝色位)的`_track` `UIImage`的边界。

首先在`RangeSlider.m`顶部的 PrivateMethods 接口中定义方法。

```
-(void)updateTrackHighlight; 
```

接下来我们将完整地定义这个方法。这个方法唯一能做的就是调整`_track` `UIImage`的边界。我们不需要调整垂直边界，只需要将水平点调整到起始和结束拇指的水平点即可。

```
-(void)updateTrackHighlight{
    _track.frame = CGRectMake(
        _minThumb.center.x,
        _track.center.y - (_track.frame.size.height/2),
        _maxThumb.center.x - _minThumb.center.x,
        _track.frame.size.height
    );
} 
```

新帧的`rect`从`minThumb`的水平位置开始，延伸到`maxThumb`位置减去`minThumb`位置的宽度。因此，如果`minThumb`在 50 度，而`maxThumb`在 140 度，那么宽度就是 140–50 = 90 度。

帧的垂直部分是从轨迹中心计算的 Y 位置，去掉一半高度，然后与当前帧的高度相同。

现在，如果我们在两个地方添加对这个新方法的调用，即`initWithFrame`和`continueTrackingWithTouch`方法，那么我们有一个滑块，它只为滑块拇指之间的轨道显示蓝色。

```
// The end of initWithFrame, inside the if statement
[self updateTrackHighlight];

// Inside continueTrackingWithThumb, above [self setNeedsDisplay];
[self updateTrackHighlight]; 
```

[![iOS Range Slider Part Two Figure 1](img/a7e83c204de80709377c2892171aa728.png)](https://www.sitepoint.com/wp-content/uploads/2011/08/slider-with-correct-blue-track.png)

正确的蓝色轨道

### 最后，让我们检查一下可用性

在我们自我表扬之前，我们需要做一些事情来确保这在现实生活中有效，而不仅仅是在模拟器中。苹果的[人机界面指南](http://developer.apple.com/library/safari/#documentation/UserExperience/Conceptual/MobileHIG/Characteristics/Characteristics.html)将可触摸 UI 元素的最小舒适尺寸描述为 44×44 磅。

不幸的是，我们的拇指不够大:它们只有 25 分，这还不够好。正如您在这里看到的，缩略图的点击区域与图像大小相同。

[![iOS Range Slider Part Two Figure 2](img/abaa2957e1c0f9e219b68aba38765afe.png)](https://www.sitepoint.com/wp-content/uploads/2011/08/Inspecting-the-hit-area.png)

检查袭击区域

幸运的是，对我们来说，更新图像的框架很容易，不需要改变图像或任何类似的东西。

在`initWithFrame`中定义`_minThumb`和`_maxThumb` `UIImage`的地方，我们将调整`UIImage`的框架大小，以匹配我们的人机界面指南。幸运的是，我们的单元格框架恰好是 44 点；)

```
_minThumb.frame = CGRectMake(0,0, self.frame.size.height,self.frame.size.height); 
```

最后，我们将确保`UIImage`不会放大图像来匹配框架，因为那样看起来很愚蠢。代码更改后，现在您可以看到框架的触摸区域更适合手指。

```
// Just place the image in the middle, don't scale
_minThumb.contentMode = UIViewContentModeCenter; 
```

[![iOS Range Slider Part Two Figure 3](img/867f11ea695f7cebed28feeb5062d7df.png)](https://www.sitepoint.com/wp-content/uploads/2011/08/More-appropriate-hit-area.png)

合适的打击区域

### 观察变化

为了证明我们的范围滑块的动作观察器工作正常，让我们在表视图中添加第二个单元格，它将显示我们的滑块的范围，并且将在范围滑块的值改变时更新。所有这些代码都将在我们的`RootViewController`中。

**步骤 1** :在`RootViewController.h`文件中导入`RangeSlider.h`，定义一个方法如下:

```
-(void)updateRangeLabel:(RangeSlider *)slider; 
```

**第二步**:实现`RootViewController.m`中的方法。这个方法将接收`RangeSlider`作为变量`slider`。此时，我所做的就是记录当前范围，从 X-y。

```
-(void)updateRangeLabel:(RangeSlider *)slider{
    NSLog(@"Slider Range: %f - %f", slider.selectedMinimumValue, slider.selectedMaximumValue);
} 
```

**第三步**:告诉我们的滑块在动作改变时调用动作！这将我们在滑块的`continueTrackingWithTouch`方法中调用的变更事件链接到我们的`RootViewController`中的方法。在我们`RangeSlider`完成`alloc`后，这个进入`cellForRowAtIndexPath`内部。

```
[slider addTarget:self action:@selector(updateRangeLabel:) forControlEvents:UIControlEventValueChanged]; 
```

**步骤 4** :运行并观察日志更新滑块范围:x . XXX–x . XXX，同时滑动滑块！

[![iOS Range Slider Part Two Figure 4](img/a3c719e84352bb3dc4a49b0e0e943aed.png)](https://www.sitepoint.com/wp-content/uploads/2011/08/Screen-Shot-2011-08-27-at-5.19.49-PM.png)

记录滑块范围更新

### 瞧啊。

恭喜，现在您的项目有了一个可用的 iOS 范围滑块。出于礼貌，我们创建了 [GitHub iOS 范围滑块](https://github.com/buildmobile/iosrangeslider) repo，并将代码发布在那里，以帮助您将这些代码应用到您的项目中。请在评论中告诉我们你的进展。

## 分享这篇文章