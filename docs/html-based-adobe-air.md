# 基于 HTML 的 Adobe AIR 应用程序的新窗口

> 原文：<https://www.sitepoint.com/html-based-adobe-air/>

使用 Adobe AIR 创建桌面应用程序是一种增强能力的体验。无论您是 Flash、Flex 还是 JavaScript 开发人员，构建存在于单个窗口中的应用程序都是一个简单的过程。然而，一旦应用程序超出了单个屏幕的界限，您就会开始考虑如何最好地接近它。本文将研究可用的选项，并考虑每种情况的利弊。[在最后回答测验](https://www.sitepoint.com/quiz/adobe/html-based-adobe-air/)，有望赢得免费的 Adobe CS4 Web Premium 和 Flex Builder Pro 3！

##### 窗口选项

它可能是一个选项屏幕，或者是一个辅助视图，或者是一个文档窗口。应用程序可以有许多方向，也有几种不同的方法来解决问题:

*   切换当前视图
*   创建一个灯箱样式的对话框
*   使用本机窗口

##### 切换当前视图

切换视图是改变现有应用程序界面的最简单的方法之一。在 HTML 应用程序中，您可以将视图加载到当前的 DOM 中，或者将 URL 更改为指向另一个页面。

当我构建 Snitter 时，它将这两个特性用于不同的目的。大多数屏幕只是隐藏起来的 div 元素，使用一点 CSS 和 JavaScript 就可以将它们重新显示出来。使用这种方法，您可以像在任何 HTML 页面上一样轻松地制作过渡动画。

在更改当前文档的 URL 的情况下，Snitter 在应用新主题时使用它来刷新视图。这可能有点棘手，因为您需要确保应用程序初始化代码与屏幕重新加载分开。

Google Analytics 应用程序使用标签来跟踪新加载的视图。

视图切换解决了次要视图与主视图所提供的空间相适应的问题(Google Analytics 就是这种情况)。

##### 灯箱风格的对话框

lightbox 是 JavaScript 驱动的对话框，不需要新的浏览器窗口。它使用一个覆盖来隐藏页面内容，同时在顶部放置一个小的居中的内容框来吸引用户的注意力。这个名字来源于最先实现这个想法的库: [Lightbox](http://www.huddletogether.com/projects/lightbox2/) 。随着标准 web 浏览器中弹出窗口拦截器的出现，lightboxes 作为一种变通方法变得流行起来。他们看起来不错(一般来说)，他们提供了一些模态。换句话说，lightbox 中的内容具有优先权，在执行某个操作之前，会屏蔽掉原始页面内容。

这种趋势已经延续到基于 AIR 的应用程序中，lightboxes 提供了快速、风格化的警告，而没有实例化新窗口的开销。

但是在基于 HTML 的应用程序中，这也有它的缺点。特别是，键盘访问仍然允许用户切换到 lightbox 内容后面的其他控件。这可能会导致用户执行不必要的操作。

##### 本机窗口

可能最引人注目的方法是通过`NativeWindow`类在 Adobe AIR 中实例化新的本机窗口的能力。这里实例化一个新的 HTML 窗口最快、最有效的方法是使用`window.open`。这类似于在 web 浏览器中使用相同的命令:

```
window.open('newpage.html');
```

创建一个新窗口，并在该新窗口中加载一个 HTML 文档。可以通过`window.opener`访问父窗口。如果您需要促进两个窗口之间的通信，这尤其方便。

这种方法的主要缺点是缺少新窗口的选项；它将一直使用系统 chrome，即使你的应用程序没有它。

如果你的应用程序需要使用定制的 chrome，即使是新的窗口，你也需要创建自己的原生窗口。幸运的是，Adobe AIR 的 API 使这一点变得相对简单明了(只是稍微有点冗长)。`HTMLLoader`对象的`createRootWindow`方法创建新窗口，并允许在该窗口中加载 HTML 页面。

让我们来看一下实例化一个新窗口的步骤。第一步是通过`NativeWindowInitOptions`类定义窗口选项。例如，是否应该使用系统镶边将在此对象中定义:

```
var options = new air.NativeWindowInitOptions(); 
options.systemChrome = "none"; 
options.type = "lightweight";
```

`Type`属性有三个不同的选项:`normal`、`utility`和`lightweight`。一个`lightweight`窗口必须始终伴随着设置为`none`的`systemChrome`。一个`utility`窗口类似于一个`normal`窗口，但是有一个稍微窄一点的标题栏，并且没有出现在任务栏上(就此而言，一个`lightweight`窗口也是如此)。`Normal` windows *会*出现在 OS X 的任务栏或者 **Windows** 菜单中。

如果你正在创建一个设置面板，你可能会使用`utility`或`lightweight`选项。如果你正在创建一个更大的二级窗口，像一个文档编辑屏幕，你可能更喜欢让窗口显示在任务栏上。

下一步是通过创建一个新的`Rectangle`来定义窗口的尺寸。前两个参数设置屏幕左上角的 X 和 Y 坐标，后两个参数分别定义宽度和高度:

```
var windowBounds = new air.Rectangle(200,250,300,400);
```

定义好选项后，会创建一个新窗口，一个新的`URLRequest`对象会指定应用程序附带的本地文件的路径:

`var newHTMLLoader = air.HTMLLoader.createRootWindow(
true, options, true, windowBounds);
newHTMLLoader.load(new air.URLRequest("newwindow.html"));`

我们可以就此打住，但是`window.open`的工作方式和`createRootWindow`的工作方式有一点不同:对于后者，新窗口无法访问开启器窗口。打开窗口的`window`对象可以通过返回给变量`newHTMLLoader`的`HTMLLoader`对象的`window`属性来访问。因此，我们将创建一个新属性，恰当地命名为`opener`，并将其设置为当前窗口:

```
newHTMLLoader.window.opener = window;
```

现在，在我们新打开的窗口中，我们可以像使用 window.open 一样访问打开器窗口:

```
window.opener // accesses the opener window
```

创建多个窗口时，内存管理可能会成为一个问题。把每个新窗口想象成在你的浏览器中打开一个新标签。

创建单独的本机窗口的另一个好处是能够在`HTMLLoader`控件上使用基于 Flash 的效果。虽然通过 CSS 和 JavaScript 可以制作一些动画，但通过使用 Flash 10 的 pixel bender 功能来重塑整个 HTML 画布可以提供一些强大的效果。请参阅 Adobe 开发人员中心的 [BlackBookSafe 示例应用程序](http://www.adobe.com/devnet/air/ajax/articles/blackbooksafe_anatomy.html)了解更多信息。

##### 包装它

创建一个新窗口可能很简单，但是你有很多选择。确保为你创建的窗口类型选择正确的解决方案。视图切换、灯箱和原生窗口都是有用的工具，有助于将应用程序扩展到单个屏幕之外。

##### 测试你的知识

 **通过做小测验来测试自己对本文内容的理解。[提交您的答案](https://www.sitepoint.com/quiz/adobe/html-based-adobe-air/)有机会赢得免费的 Adobe CS4 Web Premium 和 Flex Builder 3 Pro。[现在参加测验](https://www.sitepoint.com/quiz/adobe/html-based-adobe-air/)！**

## 分享这篇文章