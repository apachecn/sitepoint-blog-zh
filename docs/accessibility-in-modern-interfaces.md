# 现代界面中的可访问性

> 原文：<https://www.sitepoint.com/accessibility-in-modern-interfaces/>

今天用 JavaScript 做的一些事情真的很神奇。互联网本身还很年轻，但在其短暂的历史中已经经历了几次演变——最重要的可能是 AJAX 带来的令人难以置信的 Web 应用的爆炸。然而有一个失败者，那就是可及性。但是，现在我们已经达到了一个可访问性正在赶上的点，这要感谢 Web Accessibility Initiative 的可访问富互联网应用程序(WAI ARIA)的开发。


## 咏叹调的原则

ARIA 规范的核心是一组**属性语义**，用于使交互式内容可被辅助技术访问。毕竟，screenreader 用户怎么知道下面的标记代表什么？

```
<label>
  <button>25%</button>
</label>
```

ARIA 添加了重要的角色和状态信息，使事情变得易于理解，如下所示。

```
<label>
  <button role="slider"
    aria-orientation="horizontal"
    aria-valuemin="0"
    aria-valuemax="100"
    aria-valuenow="25"
    aria-valuetext="25%">25%</button>
</label>
```

ARIA 使构建动态界面、定制小部件和现代 Web 应用程序的所有不同组件成为可能，以一种屏幕阅读器和其他访问技术可以访问的方式，使用属于以下三大类的数据属性。

*   描述元素的**角色**的属性，如`dialog`、`progressbar`或`slider`。
*   描述元素的**属性**的属性，如`aria-required`、`aria-multiselectable`或`aria-valuemax`。
*   描述元素的**状态**的属性，如`aria-selected`、`aria-hidden`或`aria-valuenow`。

## 艾瑞亚的角色

对于你们中的许多人来说，最熟悉 ARIA 的可能是**标志性角色**，它可以在任何风格的 HTML 中用来描述主要内容块:

```
<div role="main" id="content"></div>
<div role="complementary" id="sidebar"></div>
<div role="navigation" id="menu"></div>
<div role="contentinfo" id="footer"></div>
```

在 HTML5 中，地标角色映射到主要结构元素:

```
<article role="main" id="content"></article>
<aside role="complementary" id="sidebar"></aside>
<nav role="navigation" id="menu"></nav>
<footer role="contentinfo" id="footer"></footer>
```

除了 HTML5 结构元素之外，使用标志性角色是**桥接技术**的一个例子——这是一种有利于当代辅助技术的临时解决方案，它支持 ARIA 角色，但还不理解 HTML5 语义。ARIA 是专门为提供这种临时解决方案而设计的，任何当前的 ARIA 属性最终都可能被更好的东西所取代。

像 HTML5 这样的正式规范需要很长的时间来开发——比开发人员提出新想法的时间要长得多。标准只能对去年*做的事情*做出反应，而有远见的开发者对我们现在*做的事情*更感兴趣。艾瑞亚可以填补这个空白。

## 键盘辅助拖放

现在我们来看一个使用拖放的例子，展示 ARIA 如何为常见的脚本行为添加可访问的语义。就在不久前，拖放还被认为是根本不可访问的——一种固有的基于鼠标的交互，无法转化为键盘。但是，像我的 [dbx 库](http://www.brothercake.com/site/resources/scripts/dbx/ "Docking boxes (dbx)")这样的早期创新表明，通过思考拖放动作实际上是为了什么，然后提供一个实现相同结果的键盘接口，就可以做到这一点。

我们所说的拖放实际上是两种不同的交互。第一个是内容重新排序，元素可以上下移动来排序。第二种是抓取和移动动作，比如在文件夹之间拖动文件。dbx 库在设计时考虑了第一种交互，而 ARIA 规范则侧重于第二种。

在下面的演示中，拖放以三种不同的方式实现——使用`draggable`属性及其相关事件(键盘无法访问),为不支持`draggable`的浏览器补充鼠标事件，然后添加按键事件以使键盘用户可以访问它。

*   [**支持 ARIA 的拖放演示**](https://github.com/jsprodotcom/source/blob/master/accessibility-in-modern-interfaces.zip)

键盘交互基于 [ARIA 创作实践](https://www.w3.org/WAI/PF/aria-practices/#dragdrop "WAI-ARIA 1.0 Authoring Practices: 9\. Drag-and-Drop Support")中推荐的那些:

1.  使用`Tab`在项目间移动。
2.  按下`Space`选择一个项目。
3.  使用`Tab`在拖放目标之间移动。
4.  按`Enter`或`Ctrl+M`将选中的项目移动到选中的拖放目标。
5.  或者，按`Esc`取消并取消选择该项目。

`Space`键确实有意义，因为它已经用于选择单选按钮和复选框。而且，`Ctrl+M`快捷键不直观，与 Mac OS X 中的<q>最小化到 Dock</q> 快捷键冲突，在我看来，按下`Enter`要明显得多，也更容易使用，所以为了折中，两者都实现了。该演示还包含了**漫游`tabindex`**——根据界面的当前状态，使用动态`tabindex`值将元素放入或移出本地 tab 键顺序。演示中的每个拖放目标都是一个列表，每个可拖动对象都是一个列表项。因此，在其默认状态下，只有项目按 tab 键顺序排列:

```
<ul>
  <li tabindex="0">1</li>
  <li tabindex="0">2</li>
  <li tabindex="0">3</li>
</ul>

<ul>
  <li tabindex="0">4</li>
</ul>
```

一旦你选择了一个项目，列表需要按 tab 键顺序排列，这样你就可以选择一个作为拖放目标。但是，此时其他项目不再需要按 tab 键顺序排列，因为在完成当前项目之前，您不能选择另一个项目。除了在父列表上设置`tabindex="0"`之外，我们还将它从单个项目中移除，这样它们就完全脱离了 tab 键顺序。这是一件简单的事情，但这意味着用户只需要按几个键就可以找到他们想要的目标，而不是必须先跳过所有其他项目！

```
<ul tabindex="0">
  <li>1</li>
  <li>2</li>
  <li>3</li>
</ul>

<ul tabindex="0">
  <li>4</li>
</ul>
```

漫游`tabindex`的原则几乎可以在任何地方使用，使得在任何给定的时间，标签顺序中唯一的元素是那些用户现在可以与**交互的元素。**

 **### 添加 ARIA 语义

一旦我们有了键盘支持，我们需要为辅助技术做的另一件事就是添加两个 ARIA 属性—`aria-grabbed`和`aria-droptarget`。第一个是布尔标志，指示是否已选择元素进行拖动，而第二个是布尔标志，指示元素现在是否是放置目标。因此，在其默认状态下，不会抓取项目，也没有活动的放置目标:

```
<ul aria-dropeffect="none">
  <li aria-grabbed="false">1</li>
  <li aria-grabbed="false">2</li>
  <li aria-grabbed="false">3</li>
</ul>

<ul aria-dropeffect="none">
  <li aria-grabbed="false" tabindex="0">4</li>
</ul>
```

一旦你选择了一个项目，它就会被抓取，父列表就会成为拖放目标。

```
<ul aria-dropeffect="move">
  <li aria-grabbed="true">1</li>
  <li aria-grabbed="false">2</li>
  <li aria-grabbed="false">3</li>
</ul>

<ul aria-dropeffect="move">
  <li aria-grabbed="false">4</li>
</ul>
```

为了说明这些变化，演示包括一个带有浮动工具提示的版本，它显示了属性如何随着每次交互而变化。在这种情况下，`aria-droptarget`值是`move`，这意味着所选择的项目将从一个目标移动到另一个目标。其他可能的值包括创建副本的`copy`，以及创建引用或快捷方式的`link`。

## 艾瑞亚什么都不做

尽管`aria-droptarget`属性有不同的值，但它们实际上提供的只是辅助技术的*信息*，因此它们可以告诉用户将会发生什么。属性本身根本不做任何事情。因此，ARIA 并没有改变我们构建 Web 应用程序的方式，它只是提供了描述它们的语义属性。

ARIA 可以(也应该)使用的另一个主要例子是通过 AJAX 动态更新页面，这是 ARIA 之前的屏幕阅读器根本无法理解的。屏幕阅读器使用一种**虚拟缓冲区**，这是设备实际读取的当前页面的临时快照。当 AJAX 用于加载新内容并将其添加到 DOM 时，虚拟缓冲区是*而不是*更新的，这就是为什么屏幕阅读器无法访问第一代 AJAX 应用程序的原因。

ARIA 用它的`aria-live`属性解决了这个问题，这个属性可以用来指示内容何时改变，以及改变有多显著。例如，如果您在一个元素上设置了`aria-live="assertive"`,然后更新了它的内容，那么内容的变化将会立即传达给用户。或者，如果您设置了`aria-live="polite"`，它将在用户方便时进行通信。这些优先级是基于用户当前正在做的事情，所以一个`assertive`变化会立即打断他们，而一个`polite`变化会等到他们不忙的时候。由 screenreader 来决定这些优先级如何在用户的工作流中表现出来。

从开发人员的角度来看，您只需考虑更新有多重要。`polite`值将用于背景中内容变化的大多数情况，例如直播新闻标题或体育比赛结果。`assertive`值将用于需要立即注意的事情，比如表单验证错误。

## 结论

ARIA 并不完美，还没有完全实现，但是我仍然建议您在任何合适的时候尝试使用它。所有定制的小部件和 Web 应用程序都应该至少有基本的 ARIA 支持，因为这决定了是否有可访问性。

如果你想了解更多关于使用 WAI ARIA 的信息，我向你推荐 WAI ARIA 简介。Gez Lemon 的[juice studio](http://juicystudio.com/)和 Steve Faulkner 的 [Paciello Group 博客](http://www.paciellogroup.com/blog/)也是有用的书签。最终，你会发现自己正在阅读 ARIA [规范](https://www.w3.org/TR/wai-aria/ "Accessible Rich Internet Applications (WAI-ARIA) 1.0")和[创作实践](https://www.w3.org/WAI/PF/aria-practices/ "WAI-ARIA Authoring Practices")(祝你好运！)

您也可以从本文下载演示:

*   [**下载支持 ARIA 的拖放演示**](https://github.com/jsprodotcom/source/blob/master/accessibility-in-modern-interfaces.zip)

## 分享这篇文章**