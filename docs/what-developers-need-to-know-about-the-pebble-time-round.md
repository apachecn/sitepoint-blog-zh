# 开发人员需要了解的关于 Pebble 的知识

> 原文：<https://www.sitepoint.com/what-developers-need-to-know-about-the-pebble-time-round/>

早在 3 月份，Pebble 发布了 Pebble Time 的消息，我整理了一份对 Pebble 开发者来说意味着什么的摘要。上周，他们发布了他们最新的大新闻——[卵石时间回合](https://blog.getpebble.com/2015/09/23/timeround/)。以下是 Pebble 开发者和修补者需要了解的新设备。

## 外形尺寸

新款 Pebble Time Round 不仅是圆形的，而且超级轻薄。它拥有与 Pebble Time 相同的 CPU、最大资源大小、支持的颜色数量、传感器、麦克风和智能配件端口。唯一不同的是，现在屏幕分辨率是 180×180 像素，(当然)是圆形的。

圆形实际上可以容纳略大的表盘:

![Pebble Time 144 compared to 180x180](img/44a575c956cf4ad8f1b5a49fea79c54a.png)

尺寸减小的一个缺点是电池寿命缩短。Pebble Time Round 的电池续航时间为两天，而不是专注于超级快速充电-15 分钟的充电时间可带来一天的电池续航时间。

遗憾的是，它也不太防水。它可以承受风雨，但带着鹅卵石游泳不会有好结果。

## 圆形平台被称为“粉笔”

之前的两个平台是“细晶岩”和“玄武岩”。“细晶岩”是原始的卵石台地，“玄武岩”是卵石时代的台地。为 round 格式开发需要第三种平台类型，称为“Chalk”。

## 模拟器是可用的

CloudPebble 的测试版已经准备好了一个模拟器，等待你在“Chalk”平台上进行实验。你可以现在就去试试你现有的应用程序，看看它们看起来怎么样。

## 你需要在运行时计算坐标

这是我在研究新变化时发现的最重要的提示。避免设置和使用像`SCREEN_WIDTH`和`SCREEN_HEIGHT`这样的常量值——不同版本的设备会有所不同！您可以在运行时检查应用程序窗口根层的大小:

```
Layer *window_layer = window_get_root_layer(s_window);
GRect bounds = layer_get_bounds(window_layer);
```

避免这种情况:

```
Layer *layer = layer_create(GRect(0, 0, 144, 168));
```

相反，请这样做:

```
GRect bounds = layer_get_bounds(parent_layer);
Layer *layer = layer_create(bounds);
```

阻止现有应用程序在新设备上工作的主要原因之一就是像这样的硬编码值，所以如果你要跨现有应用程序移植，首先检查硬编码的屏幕尺寸，并将其删除。

## 画圆很容易

结果是，当你有一个圆形的表盘时，你最终会画出更多的圆形。Pebble SDK 现在有一些新功能来帮助管理循环界面。

### 画一条圆形线

`graphics_draw_arc()`–在特定的`GRect`区域的两个角之间顺时针画一条弧线。这个功能比旧的`graphics_draw_circle()`更适合在应用程序中正确居中圆圈。这个功能使得你的圆很难在半个像素之间居中。新功能使这变得容易多了。

下面是一个代码示例:

```
graphics_draw_arc(ctx, inset_bounds, GOvalScaleModeFillCircle, start_angle, end_angle);
```

### 画一个实心圆

`graphics_fill_radial()`–顺时针填充两个角之间的圆。您也可以调整它的内侧嵌入半径来创建圆环形状。这对于老的`graphics_fill_circle()`来说是有利的，原因与上面的弧线功能相似，但也因为它能够画出环形形状。

以下是 Pebble 的 [Time Dots"](http://developer.getpebble.com/sdk/round-getting-started/#watchfaces) 示例中的一个实例:

```
int minute_angle = get_angle_for_minute(s_minutes);
GRect frame = grect_inset(bounds, GEdgeInsets(10));
graphics_context_set_fill_color(ctx, MINUTES_COLOR);
graphics_fill_radial(ctx, frame, GOvalScaleModeFitCircle, 20, 0, DEG_TO_TRIGANGLE(minute_angle));
```

您可以在这里的应用程序中看到这一点:

![The "Time Dots" watchface](img/221563d8103e9040cea185b39cc39c72.png)

### 围绕一个圆放置元素

`gpoint_from_polar()`–返回一个`GRect`内指定角度内的一个`GPoint`。基本上，提供圆上的一个点，而不是一个完整的圆。非常适合在中心点周围等距放置元素。例如，代表 12 小时的点序列就是这样包含在上面的“时间点”示例中的:

```
for (int i = 0; i Pretty handy!

        Enhance Your UI For Each Platform
Just because there's a round design does not mean all of your Pebble app designs should be circular from this point on. It may actually be more beneficial to adapt and adjust your design for the different form factors. Porting your existing apps from the rectangular platforms might really benefit from a shuffle of elements. A great example is Katharine Berry's CalTrain app which adjusts its interface for the round screen in a really nice way:

Image courtesy of Pebble

The app features a lot of curves which looks quite good in action as you can see from Katharine's wrist on her way to work:

Image courtesy of Katharine from Pebble

Don't Detect Platform Specifically
The following ways to detect the platform your app is running on are bad:

  PBL_PLATFORM_APLITE
  PBL_PLATFORM_BASALT
  PBL_PLATFORM_CHALK

Instead, use:

  PBL_COLOR
  PBL_BW
  PBL_ROUND
  PBL_RECT

        New Macros Are Available
Want to detect if the system is round or rectangular? You can do PBL_IF_ROUND_ELSE and PBL_IF_RECT_ELSE.

How about detecting if we've got a color platform or a black and white one? You can use PBL_IF_COLOR_ELSE and PBL_IF_BW_ELSE.

Setting Platform Specific Resources
It is possible to set up platform specific resources for the different capabilities of each device by tagging them. The tag options include:

  rect - rectangular devices
  round - round devices
  bw - black and white devices
  color - color devices
  aplite - targets the aplite platform (avoid when possible just like PBL_PLATFORM_APLITE)
  basalt - targets the basalt platform (avoid too)
  chalk - targets the chalk platform (avoid too)

You can use them within their filenames and include multiple tags, for example grumpy-cat~bw.png, grumpy-cat~color.png, grumpy-cat~color~rect.png and grumpy-cat~color~round.png.

These tags do not need to be mentioned in your appinfo.json file, you can just define one single image like so:

[code language="js"]
"resources": {
  "media": [
    {
      "type": "png",
      "name": "GRUMPY_CAT",
      "file": "images/grumpy-cat.png"
    }
  ]
}
```

如果您想单独为一个平台定义一个图像，您也可以在没有标签的情况下在`appinfo.json`中完成:

```
"resources": {
  "media": [
    {
      "type": "png",
      "name": "GRUMPY_CAT",
      "file": "images/grumpy-cat.png",
      "targetPlatforms": [
        "chalk"
      ]
    }
  ]
}
```

## 最小化边框

Pebble Time Round 周围已经有了相当大的边框区域。甚至更多的边界层是没有必要的。尽量减少应用程序周围基于软件的边框和边框。如果你的边框有意义，试着让它在视觉上不同于一个平的、实心的边框。使用图案和颜色来直观地表示该区域的信息。避免真正的厚和坚实的边界！

## 两个像素的边距

Pebble Time Round 屏幕的边缘有两个像素的空白，会与手表的边框稍微重叠:

*   避免在此空间内放置任何文本或视觉提示。
*   确保背景颜色一直延伸到应用程序的边缘。
*   Pebble 指出，尽量不要在显示器周围使用非常薄的边框，“制造差异可能会导致它们明显偏离中心”。

## 菜单

使用 [MenuLayer](http://developer.getpebble.com/docs/c/User_Interface/Layers/MenuLayer/) 组件的菜单在 Pebble Time Round 上的工作方式会有所不同。由于我们显示器顶部和底部的空间变小了，菜单会自动在当前选择的选项上垂直居中。

Pebble 指出，当菜单元素在中心区域突出显示时，您可以显示它的附加信息，但当该项目不是焦点选项时，您可以隐藏这些信息。

如果你是那些有想象力的开发人员之一，已经把他们自己的菜单系统放在一起，你需要记住这一点。功能`menu_layer_set_center_focused()`和`menu_layer_is_index_selected()`就得心应手了！

## 滚动文本不好

试图在圆形空间内滚动和重排文本看起来很混乱。Pebble 建议将这些信息分页，以便您点击“指示器”来查看屏幕上尚未显示的更多文本。

Pebble 的 SDK 有一个`ContentIndicator`组件，你可以在他们的文档中关于[在圆形应用](http://developer.getpebble.com/guides/pebble-apps/display-and-animations/circular-graphics/#displaying-more-content)中显示更多内容的部分了解更多信息。

## 3D 图纸尚未发布

Pebble Time Round 的 3D 图纸尚未发布，但他们已经被要求，肯定会很快出现。其他人的图纸都在他们的 3D 图纸 Github 上。

## 需要灵感？

我已经发现了一些为 Pebble Time Round 开发的应用程序。我之前展示的 Caltrain 是目前为止我最喜欢的！另一个我从 Pebble 开发人员 robisodd 那里看到的是一个看起来非常整洁的飞镖表盘:

![Pebble Dartboard Watchface](img/11dd7c2b1f963596810b10bc74f9ef12.png)

这款放大的格雷瓜尔·塞奇表盘在卵石纹腕表上熠熠生辉:

![The Zoomed In Pebble App](img/d0bd543c00d2ae64eccb755ea349f4fd.png)

另一个奇妙的表盘是 Le Fauve 的“月相”表盘，它根据你的位置倾斜:

![Moon Pebble Watch App](img/e7e002cd5a15ad2823ce883a5efef2c4.png)

其中一个名为[“同心度”](https://github.com/pebble-examples/concentricity)的 Pebble 应用程序看起来也很整洁:

![Concentricity Pebble Watch App](img/0b267d2d6c2d06a6febe620779bb9c6a.png)

## 结论

新设备开启了一些有趣的新设计挑战和机遇。希望这篇文章能帮助您成功使用您的第一个 Pebble Time Round 应用程序！走出去，在 CloudPebble 模拟器中体验新功能吧！

如果您开发了任何很酷的 Pebble Time Round 应用程序，请在评论中分享或在 Twitter 上与我联系( [@thatpatrickguy](http://www.twitter.com/thatpatrickguy) )。我很想知道开发者会把新的外形带到哪里。

我有一组关于 Pebble 开发的精选链接(包括 Pebble Time Round 的特定链接),供那些寻求快速参考的人使用。前往 Dev Diner 并查看我的 [Dev Diner Pebble 开发人员指南](http://devdiner.com/guides/pebble/)，其中包含本文中使用的全部参考链接、其他优秀的 SitePoint 文章等等。如果你有我没有列出的其他好资源，也请告诉我！

## 分享这篇文章