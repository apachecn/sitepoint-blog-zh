# WAI-ARIA 简介

> 原文：<https://www.sitepoint.com/introduction-wai-aria/>

这可能令人震惊，但我告诉你:网络已经改变了！过去八年见证了 Ajax、JavaScript、HTML5 和无数前端框架的崛起。因此，互联网不再是一个静态 HTML 页面的地方，尽管有时它们很有趣。相反，它已经成为复杂的、几乎像桌面一样的 web 应用程序的游乐场，每个应用程序都有自己的小部件、控件和行为。

我们大多数人认为这是理所当然的，并将 web 开发推到了极限。然而，也有不可忽视的数量的残疾人仍然在与这些新技术作斗争。这不是因为禁用了 JavaScript 或当前辅助技术(AT)的功能不足。相反，2012 年 [WebAIM](http://webaim.org/) 发现[超过 98%的屏幕阅读器用户启用了 JavaScript。此外，像屏幕阅读器或](http://webaim.org/projects/screenreadersurvey4/#javascript "WebAIM's Screen Reader User Survey #4 Results")[可刷新盲文显示器](https://en.wikipedia.org/wiki/Refreshable_braille_display)这样的自动测试技术一年比一年好。

问题在于 HTML 标记大量使用 JavaScript 并产生大量动态内容的 web 应用程序的能力有限。当 ATs 处理 JavaScript 应用程序时，可以发现四个主要障碍:

1.  组件的未知功能
2.  组件的未知状态和属性
3.  动态内容的未报告更改
4.  键盘可访问性差

输入 [WAI-ARIA](https://en.wikipedia.org/wiki/WAI-ARIA) 。

## 什么？

ARIA，有时被称为，代表*可访问的富互联网应用*。它是由 W3C(万维网联盟)的[网页可访问性倡议](https://www.w3.org/WAI/)(WAI 部分完成了首字母缩略词)设计的技术规范，现在[是 W3C 的推荐标准](https://www.w3.org/TR/wai-aria/ "Accessible Rich Internet Applications (WAI-ARIA) 1.0 - W3C Recommendation 20 March 2014")。

您可以将 ARIA 看作是包含在您常用的 HTML 代码中的一组属性。这些额外的语义帮助 ATs 识别用户界面中的属性、关系和状态。通过这种方式，ARIA 解决了上面提到的可访问性挑战，并允许开发人员使高级 web 应用程序对残疾人更有用。

现在让我们看看 [Bootstrap 的标签面板小部件](http://getbootstrap.com/javascript/#tabs "Bootstrap Tabs")，并详细探索 ARIA 如何帮助我们扩充给定的 HTML 结构，使其更易于访问。我们将一路解决上述每个关键问题。

## 1.通过角色实现功能

ARIA 提供了丰富的*角色分类法*,使开发人员能够对没有意义的标签进行分类。这通过揭示 ATs 的功能或它们在整个 web 文档中所起的作用来为 ATs 准备标签。

Bootstrap 使用一个无序列表来标记它的选项卡式导航。该框架还正确地使用了 ARIA 角色`tablist`和`tab`。

```
<ul id="myTab" class="nav nav-tabs" role="tablist">
    <li class="active">
        <a href="#home" role="tab" data-toggle="tab">Home</a>
    </li>
    <li>
        <a href="#profile" role="tab" data-toggle="tab">Profile</a>
    </li>
    <li>
        <a href="#articles" role="tab" data-toggle="tab">Articles</a>
    </li>
</ul>
```

这些角色覆盖了通常的列表和列表元素类型，并公开这个 HTML 片段来表示一个选项卡列表。我们应该比 Bootstrap 更进一步。以下是面板的 HTML，我们将在其中添加正确的 ARIA 角色:

```
<div id="myTabContent" class="tab-content">
    <div class="tab-pane fade in active" id="home" role="tabpanel">
        <p>Welcome!</p>
    </div>
    <div class="tab-pane fade" id="profile" role="tabpanel">
        <p>My profile.</p>
    </div>
    <div class="tab-pane fade" id="articles" role="tabpanel">
        <p>My articles.</p>
    </div>
</div>
```

*注意:请忽略`active`和`tab-pane`这样的类名；它们属于 Bootstrap，仅用于演示目的。*

角色有助于奠定坚实的基础，但这不是全部。

## 2.状态和属性

ARIA 状态和属性为用户提供了关于如何与特定小部件交互的更多信息。这一切都是为了在键盘和屏幕阅读器上向用户展示我们的应用程序的关系和状态。幸运的是，W3C 为我们提供了一些详细的 WAI-ARIA 最佳实践。

到目前为止，我们已经有了一个选项卡列表和一些面板。然而，它们之间的联系只是因为 Bootstrap 的 JavaScript 将它们联系在一起而存在。换句话说，没有内在的关系。ARIA 用`aria-controls`和`aria-labelledby`属性来拯救我们。

属性用于将一个控件与其控制的区域关联起来。`aria-labelledby`属性用于指示作为元素标签的元素。

以下是选项卡的改进标记:

```
<li class="active">
    <a id="tab1" href="#home" role="tab" aria-controls="home" data-toggle="tab">Home</a>
</li>
```

下面是单个面板的新标记:

```
<div class="tab-pane fade in active" id="home" role="tabpanel" aria-labelledby="tab1">
    <p>Welcome!</p>
</div>
```

注意这些元素是如何通过使用各自的`id`属性的值连接起来的。

我们还应该明确描述什么？我们可以说，在任何时刻，只有一个选项卡*被选中*，只有相应的面板*可见*。幸好`aria-selected`和`aria-hidden`来了，化险为夷！它们是布尔型的，可以设置为`true`或`false`。这些可以使用 JavaScript 在适当的选项卡上更改，默认值可以在标记中设置。

## 3.动态内容的实时区域

在不重新加载页面的情况下发现动态更新的内容是屏幕阅读器的最大障碍之一，尤其是在 Ajax、Node 和单页面应用程序时代。这些应该如何处理？整页都要讲一遍吗？或者 AT 应该什么都不做，冒着用户将错过重要更新的风险？

甚至像我们的小标签列表这样简单的事情也会变得棘手。想象一下，我们使用一个定时器函数来循环我们的选项卡式界面(类似于图像滑块)。这将导致根据当前选择的选项卡显示不同的内容。

为了填补这个空白，ARIA 建立了活动区域(live regions)的概念，只要文档的特定部分发生变化，就可以通知 ATs。

让我们在`div`中实现一个包含当前可见选项卡面板的活动区域:

```
<div id="myTabContent" class="tab-content" aria-live="polite">
    ...
</div>
```

将`aria-live`属性设置为`polite`意味着只有当用户不忙于页面上的其他任务时，屏幕阅读器才会等待通知更改。值为`assertive`会立即通知用户。

## 4.增强的键盘导航

在 HTML4 的早期，可以通过 TAB 键获得键盘焦点的元素只有链接和表单元素。这对于那些被迫不用鼠标操作网页的人来说有一些严重的影响。他们应该如何导航到可能作为选项卡或下拉菜单的`span`或`div`元素？

因此，ARIA 通过扩展`tabindex`属性使每个 HTML 元素都能接收键盘焦点。今天，这是 HTML5 规范的一部分。

添加一些 JavaScript 来结束我们的示例，并使 Bootstrap 的选项卡面板小部件可以通过键盘访问。查看 CodePen 演示，并尝试只使用 TAB 和箭头键来操作这个小部件。

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看到笔 [BeplL](http://codepen.io/SitePoint/pen/BeplL/) 。