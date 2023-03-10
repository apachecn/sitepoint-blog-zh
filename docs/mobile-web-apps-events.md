# 移动 Web 应用程序:事件

> 原文：<https://www.sitepoint.com/mobile-web-apps-events/>

这是即将出版的新书《为智能设备建立移动网站和应用程序》的另一个摘录，作者是[厄尔·卡斯尔丁](http://twitter.com/#!/mrspeaker)、[迈尔斯·埃夫托斯](http://twitter.com/#!/madpilot)和[马克斯·惠勒](http://twitter.com/#!/makenosound)。BuildMobile 独家发布了这本书的完整章节，关于移动网络应用的章节，这是关于事件的第二部分。

## 2.事件

对于许多用户来说，网站和原生应用程序之间最显著的区别是它们在屏幕或页面之间转换的方式。本地应用程序会立即响应，有时会在屏幕上滑动或淡出内容，而网站会在下一页内容开始加载之前显示几秒钟的白屏。

有很多方法可以让我们的 web 应用程序感觉更灵敏。正如您可能已经猜到的，使用 Ajax 引入页面组件以避免刷新是我们策略的一个重要组成部分。这种方法将在第 7 章*介绍 PhoneGap* 中得到回报，当我们使用 PhoneGap 将我们的应用程序带入本地世界时。

在我们用 Ajax 加载内容，并使用过渡和动画来改变事物之前，我们需要捕捉一些事件。在很大程度上，在移动设备上处理事件与在桌面上没有什么不同，但是有几个要点值得学习，所以我们将从这里开始。

让我们从捕捉选项卡栏中链接的点击事件开始。作为快速复习，下面是导航的标记:

```
<ul id="tab-bar">
  <li id="tab-spots">
    <a href="#page-spots">
      Spots
    </a>
  </li>      
  <li id="tab-sighting">
    <a href="#page2">
      Add a sighting
    </a>
  </li>
  <li id="tab-stars">
    <a href="#page3">
      Stars
    </a>
  </li>
</ul><!-- #tab-bar -->
```

为了验证我们可以捕获每个列表项上的点击事件，我们将发出另一条警告消息:

```
$("#tab-bar li").click(function(e){
  e.preventDefault();
  alert("Coming soon!");
});
```

### 运行脚本

接下来的所有代码片段将假设代码要么在函数内部，要么从函数内部被调用。这样，我们可以确保在运行任何依赖于它的代码之前，我们的文档已经被加载了。

我们已经将函数附加到所有列表项的 click 事件上，并且我们已经阻止了事件的默认行为发生，所以浏览器不会导航到链接地址。在你的手机上启动它，确保它能正常工作。这可能是一个无聊的警告信息，但是看到你的代码在你的手掌中运行是相当酷的。图 4.2，“我们已经捕获了点击事件，并使用它来显示消息”，显示了 Android 浏览器上的输出。

[![fig_4_alert](img/e394cac20a844b2aaeb69bfb8bf06b6e.png)](https://www.sitepoint.com/wp-content/uploads/2011/06/fig_4_alert.png)

DOM `click`事件由两个独立的步骤组成:一个`mousedown`，接着是一个`mouseup`。显然，这是一个为鼠标设计的事件，点击只需要几分之一秒，并且是在用户将鼠标移动到正确的位置后完成的。这种互动并不总是在触摸屏上复制得很好，在一些设备上点击会感觉有点迟钝。

稍微改进一下，只针对`mouseup`事件；然而，`mouseup`事件独立于`click`事件，所以我们仍然需要阻止点击链接到另一个页面。一旦我们缩减了超链接，我们就可以在`mouseup`事件处理程序中运行我们的代码，我们使用 jQuery 的`bind`方法来附加它:

```
// Stop clicks from following links
$("#tab-bar li a").click(function(e){
  e.preventDefault();
});

// Do our magic on mouse-up!
$("#tab-bar li").bind("mouseup", function(){
  alert("Coming soon!");
});
```

### 2.1.简单触摸事件

虽然移动设备可以假装你的手指是鼠标指针，并触发常规的点击事件，但它们也生成 *触摸* 事件，这些事件更加准确和有用。然而，这些软件带有如此多的跨浏览器陷阱，以至于你会开始希望你能回到 IE6 的开发上来！

幸运的是，检测触摸非常简单:

```
$("#tab-bar li").bind("touchend", function(e){
  alert("Coming soon!");
});
```

大多数最新的移动触摸设备都会触发`touchend`事件。当用户触摸屏幕后将手指从屏幕上抬起时就会触发。正如书中所总结的，还有一些其他事件可以很好地映射到鼠标事件。

注意，移动设备上没有`mouseover`事件——毕竟，不可能知道你的手指停在哪里！这可能是一个小细节，但它对用户交互设计有一些有趣的影响，正如我们在第 2 章*移动*的设计中简要讨论的。没有悬停状态或工具提示意味着用户不能再在屏幕上移动鼠标来寻求帮助，所以你必须让你的界面美观且一目了然。

到目前为止，我们已经完成了鼠标事件的一个子集——没有什么特别的。在下一章，我们将探索移动设备的一些高级触摸功能，如滑动和手势。同时，让我们继续研究简单的触摸事件。

### 2.2.使用特征检测点击

触摸事件可以更加灵敏和准确，但并非所有设备都支持。我们可能会将注意力集中在最新一代的超级手机上，但为尽可能多的浏览器提供支持总是一个好主意。此外，仅仅依靠触摸事件无法在桌面浏览器上测试你的应用。看到你的努力工作显示在手机上是很有趣的，但当你测试功能时，不得不在桌面和手机之间切换很长时间就不那么有趣了。

您可以(也应该)使用各种设备模拟器，将您的鼠标点击解释为触摸事件，但是如果您习惯于桌面 web 开发，能够在您的桌面浏览器中测试您的应用程序是非常方便的。

为了让我们的代码既能在新旧移动浏览器上运行，也能在桌面浏览器上运行，我们将实现一部分*特性检测*。功能检测包括探测用户的浏览器，看看我们可以使用哪些很酷的功能，以及(我们希望)为缺失的功能提供足够的备用功能。

我们可以通过分配几个变量来做到这一点:

```
var hasTouch = "ontouchend" in document,
    touchEndEvent = "touchend";

// Default to mouse up, if there's no touching
if (!hasTouch) {
  touchEndEvent = "mouseup";
}
```

如果浏览器支持触摸事件，窗口的`document`对象将包含`ontouchend`事件。我们创建一个名为`touchEndEvent`的变量，它最初包含字符串`"touchend"`；如果我们的触摸检测一无所获，我们就用`"mouseup"`来代替它。

现在我们有了一个包含我们想要的目标事件的变量，我们可以绑定变量而不是静态字符串，正确的事件将根据设备的功能进行处理:

```
$("#tab-bar li").bind(touchEndEvent, function(){
  alert("Coming soon!");
});
```

### 三元运算符

JavaScript(以及许多其他编程语言)中有一个快捷方式，允许你像上面那样有条件地设置一个变量。它被称为*三元运算符*。它有语法`a ? b : c`，翻译过来就是:如果`a`为真，返回`b`；否则，返回`c`。

所以我们可以把上面的`touchend`赋值写成:

```
var touchEndEvent = "ontouchend" in document ? "touchend" : "mouseup";
```

这读作“如果`document`包含属性`ontouchend`，返回`"touchend"`；否则，返回`"mouseup"`。然后将结果赋给`touchEndEvent`变量。三元运算符是进行条件赋值的一种简洁(有些人会认为是“神秘的”)的方式，就像传统程序员的制表符与空格之战一样，你要么喜欢三元运算符，要么讨厌它！

不是想吓唬你，关于点击这个话题还有更多要说的。当从鼠标移到手指上时，即使是简单的点击也有许多新问题，其中之一就是双击的含义。您的移动设备需要等待相对较长的时间来查看您的单击是否会变成双击，这种延迟表现为微小但明显的滞后。在第 6 章，*润色我们的应用*，我们将看看如何通过“快速点击”移除它不过，对于本章的其余部分，我们将偏向于简单，依赖于过去的好事件`click`。

### 构建移动图书

您可以从 Sitepoint 购买《为智能设备构建移动网站和应用程序》一书。阅读完整的[第 4 章。移动网络应用](https://www.sitepoint.com/build-mobile-book/)，独家在 BuildMobile，在下面的部分是免费的。

*   [开店](https://www.sitepoint.com/mobile-web-apps-setting-up-shop/)
*   事件
*   [速赢](https://www.sitepoint.com/mobile-web-apps-quick-wins/)
*   [加载页面](https://www.sitepoint.com/mobile-web-apps-loading-pages/)
*   Ajax
*   [模板化](https://www.sitepoint.com/mobile-web-apps-templating/)
*   我们有一个应用程序！

## 分享这篇文章