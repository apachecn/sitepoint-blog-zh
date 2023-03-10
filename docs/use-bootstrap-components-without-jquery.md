# 本地引导:在没有 jQuery 的情况下使用引导组件

> 原文：<https://www.sitepoint.com/use-bootstrap-components-without-jquery/>

**你用 Bootstrap 的 JavaScript 组件吗？你喜欢[香草 JavaScript](http://vanilla-js.com/) 吗？那么您可能会对用于 Bootstrap 项目的[原生 JavaScript(Bootstrap Native)感兴趣，该项目旨在通过将组件移植到普通 JavaScript 来移除组件所需的 jQuery 依赖。](http://thednp.github.io/bootstrap.native/)**

## 为什么使用 Bootstrap Native？

这种端口的动机主要与性能有关。

一个好处是潜在的性能提升，这来自于普通 JavaScript 比 jQuery 更快的执行速度，正如许多基准测试所报告的那样。

另一个性能优势是减少了页面重量。我们来快速对比一下。以下所有数字指的是缩小的 gzipped 文件，用 KBs 表示。`bootstrap.js`指的是原始的引导脚本，`bsn.js`指的是引导本地脚本，`jq`指的是 jQuery。这里我们看到的是集合了所有组件的捆绑文件，但是应该注意的是，这两个库都有一个模块化的结构，只允许加载所需的组件及其依赖项。

**Bootstrap.js:**

*   jQuery 3 . 3 . 1+bootstrap . js = 30.0+12.9 = 42.9
*   jQuery 3 . 1 . 0 slim+bootstrap . js = 23.6+12.9 = 36.5
*   jQuery 2 . 2 . 4+bootstrap . js = 34.3+11.2 = 45.5
*   jQuery 1 . 12 . 4+bootstrap . js = 38.8+11.2 = 50.0

**引导本地 JavaScript** :

*   minifill + bsn.js = 2.4 + 7.8 = 10.2
*   polyfill.io(在 chrome 54 上)+ bsn.js = 1.1 + 7.8 = 8.9
*   polyfill.io(在 IE 8 上)+ bsn.js = 12.1 + 7.8 = 19.9

(*I E8 的 polyfill.io 大小取自[这里的](https://cdn.polyfill.io/v2/docs/features/#understanding-polyfill-sizes)。这些聚合填充物将在下一节*中讨论。)

因此，对于引导组件，大小在 36.5 到 50.0 KB 的范围内变化，而对于引导本机，大小范围缩小到 8.9 到 19.9 KB。

## 引导本机浏览器支持

关于浏览器支持，它可以与最初的基于 jQuery 的脚本相媲美。即支持各大移动和桌面平台上的[最新浏览器和 IE8+](https://github.com/thednp/bootstrap.native/wiki/Browser-support) 。这是通过两种多填充策略实现的。

第一个围绕着 [Polyfill.io](https://polyfill.io) 服务的使用。您所要做的就是在文档中插入相关的脚本标记，以获得一组为每个浏览器定制的聚合填充:

```
<script src="https://cdn.polyfill.io/v2/polyfill.js"></script> 
```

该服务可以被配置为基于站点上实际使用的特性来定制其响应。详见[Polly fill . io 文档](https://polyfill.io/v2/docs/)。

或者，也可以使用 [minifill](https://github.com/thednp/minifill) ，这是一种由项目作者自己提供的可能更轻的定制 polyfill。

## 引导本机用法

用法类似于原始的引导脚本，只是我们将删除 jQuery，用引导本地项目提供的脚本替换引导脚本，并且如果需要，包括 polyfills。

因此，在 end `</body>`标记之前，我们包含了用于引导本地组件的脚本:

```
<script src="https://cdn.jsdelivr.net/npm/bootstrap.native@2.0.15/dist/bootstrap-native-v4.min.js"></script>
```

其他 CDN URLs 可用，并在[引导本地文档页面](https://github.com/thednp/bootstrap.native/wiki/How-to-use)中列出。或者，可以下载该文件并在本地提供。

如果需要聚合填充，它们应包含在`<head>`标签中:

```
<script src="text/javascript" src="https://cdn.jsdelivr.net/gh/thednp/minifill@0.0.4/dist/minifill.min.js">  </script>
<!--[if IE]>
    <script src="https://oss.maxcdn.com/html5shiv/3.7.2/html5shiv.min.js"></script>
<![endif]-->
```

这个片段使用了`minifill` polyfill。

更多详细的使用说明，请参见 [Bootstrap 本地项目文档页面](http://thednp.github.io/bootstrap.native/)。

## 港口？

准确地说，它不是一个复制原始脚本所有特性的文字端口。Bootstrap 原生作者故意选择省略一些细微的功能，尤其是很少使用的功能，主要是出于性能原因和简化开发。

让我们来看看其中的一些问题。

### 自定义事件

这些是许多引导组件在其生命周期中触发的事件。例如，一个模态触发两个事件——一个在它打开时，另一个在它关闭时。(实际上，在每种情况下都会触发两个事件，一个在动作之前(`'show'`)，另一个在动作之后(`'shown'`)。)
当有选项卡切换时，选项卡使用类似的事件来通知它的观察者:为当前选项卡调度 hide 事件，为必须显示的选项卡调度 show 事件。

相反，Bootstrap Native 只为转盘和按钮提供这些事件。当两张幻灯片之间有过渡时，原始的旋转木马会触发几个自定义事件。第一个事件`'slide'`在过渡开始前触发，而另一个事件`'slid'`在过渡完成后触发。传递给处理程序的事件对象有两个属性，提供关于转换的信息，`direction`和`relatedTarget`。

以下 jQuery 代码片段说明了:

```
$carousel
  .on('slide.bs.carousel', function(e) {
    var caption = $(e.relatedTarget).find('.carousel-caption').text();
    console.log('About to slide to the ' + e.direction + ' to slide ' +  caption);
  })
  .on('slid.bs.carousel', function(e) {
    var caption = $(e.relatedTarget).find('.carousel-caption').text();
    console.log('Slid to the ' + e.direction + ' to slide ' + caption);
  });
```

Bootstrap Native 支持这两种事件，但是事件对象没有`direction`和`relatedTarget`属性。我们可以这样将前面的代码片段翻译成普通的 JS:

```
carousel.addEventListener('slide.bs.carousel', function(e) {
  console.log('About to slide');
});

carousel.addEventListener('slid.bs.carousel', function(e) {
  console.log('Slid');
});
```

如果我们需要一些其他组件的定制事件呢？我们自己实现它们并不太难。我们可以参考 Bootstrap 原生 Carousel 代码，使用 [CustomEvent API](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent) 。

首先创建事件对象:

```
if (('CustomEvent' in window) && window.dispatchEvent) {
  slid =  new CustomEvent("slid.bs.carousel");
  slide = new CustomEvent("slide.bs.carousel");
}
```

当幻灯片过渡即将开始时，触发`'slide'`事件:

```
if (slide) {
  this.carousel.dispatchEvent(slide);
}
```

最后，当转换完成时，触发`'slid'`事件:

```
if (slid) {
  self.carousel.dispatchEvent(slid);
}
```

基于这个模型，类似的代码可以很容易地添加到其他组件中。

CustomEvent API[并不容易在每个浏览器](http://caniuse.com/#feat=customevent)上获得，但是前面提到的 polyfills 涵盖了它。

### 编程 API

这是 [API](http://getbootstrap.com/docs/4.0/getting-started/javascript/) Bootstrap 组件，允许用 JavaScript 进行初始化和控制。例如，在模态元素上，可以调用三种方法来控制它的可见性:

```
$('#mymodal').modal('show')
$('#mymodal').modal('hide')
$('#mymodal').modal('toggle')
```

相反，Bootstrap Native 通常不提供这种类型的编程控制。上述方法不可用于模式，也不可用于下拉菜单、选项卡、警告或转盘。

### 其他差异

如果我们对组件进行并排比较，就会清楚 Bootstrap Native 并不是一个字面意义上的端口，在某些情况下会删除功能，而在其他情况下会添加一些新内容。

例如，在工具提示中，对于 Bootstrap，我们必须显式地初始化它们，因为出于性能原因，[相对的 data-api 是选择加入的](http://getbootstrap.com/javascript/#tooltips)。在 Bootstrap Native 中，只要正确设置了数据 API 属性，初始化就是自动的。此外，本机版本可以自动放置工具提示，无需任何附加选项。但是它没有涵盖像 Bootstrap 提供的那种模板系统。

下拉列表提供了另一个由于[有意的实施选择](https://github.com/thednp/bootstrap.native/issues/72)而导致相对引导组件略有不同的例子。单击菜单项后，jQuery 下拉菜单关闭，而本地菜单保持打开。

键盘输入处理也不完整。选项卡导航有效，但其他操作[部分实现](https://github.com/thednp/bootstrap.native/issues/82)。

关于轮播，默认情况下，jQuery 组件响应键盘输入，而在普通版本中，这种行为必须使用`data-keyboard`属性来启用:

```
<div id="carousel" class="carousel slide" ... data-keyboard="true">
```

该组件的另一个不同之处在于如何定制持续时间选项。该值用于确定现有幻灯片和进入幻灯片之间的过渡时间间隔必须有多长。两个库都定义了相同的默认值`600ms`，这是一个合理的可接受值，对于大多数用例来说应该没问题。

如果我们想改变这个值，假设在两个库中动画都是用 CSS 执行的，首先我们必须添加一些 CSS 规则来覆盖默认的持续时间值。

在 Bootstrap 中，我们需要使用一些 jQuery 代码来修改 JavaScript 中硬编码的值:

```
$carousel.data()['bs.carousel'].constructor.TRANSITION_DURATION = 2000;
```

另一方面，Bootstrap Native 在组件根元素上公开了一个`data-duration`属性，因此这个过程更容易:

```
<div id="carousel" class="carousel slide" data-ride="carousel" data-interval="false" data-duration="2000">
```

其他组件(如模式组件和工具提示组件)也可以使用相同的选项来更改过渡持续时间。

其他问题在[文档页面](http://thednp.github.io/bootstrap.native/)和项目[问题跟踪器](https://github.com/thednp/bootstrap.native/issues)上列出并解释。

## 结论

在我看来，这个项目非常有趣，但我不会这么快就扔掉最初的 jQuery 版本。事实上，就像在其他“jQuery Vs Vanilla JS”的比较中一样，获胜者通常取决于具体的用例。

前一节中讨论的问题不应该是主要障碍，所以如果您没有在寻找 Bootstrap JavaScript 组件的完美文字转换，并且已经准备好处理一些细微的差异，这可能是正确的解决方案。

此外，必须提到的是，该项目正在积极开发中，并提供了对 [GitHub tracker](https://github.com/thednp/bootstrap.native/issues) 上公开的问题的快速反馈。

那么，你打算在你的下一个 Bootstrap 项目中尝试一下吗？欢迎在评论中分享你的想法。

*本文由 [Joan Yin](https://github.com/newjs) 同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

如果你已经掌握了 Bootstrap 的基础知识，但想知道如何将 Bootstrap 技能提升到一个新的水平，请查看我们的[用 Bootstrap 4](https://www.sitepoint.com/premium/courses/building-your-first-website-with-bootstrap-4-2995) 建立你的第一个网站课程，快速而有趣地了解 Bootstrap 的强大功能。

## 分享这篇文章