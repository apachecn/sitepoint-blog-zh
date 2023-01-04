# swing Renaissance–很酷的 Java UI 示例

> 原文：<https://www.sitepoint.com/swing-renaissance-cool-java-ui-examples/>

如今，有两种流行的用 Java 构建图形用户界面的工具包: [Swing](http://java.sun.com/docs/books/tutorial/uiswing/) (来自 Sun)和[标准小部件工具包](http://www.eclipse.org/swt/)(来自 SWT)(来自 IBM/Eclipse)。不同的开发人员通常对哪个最好有强烈的意见，但事实上他们都有自己的长处。

**摇摆**

Swing 是 Java 2 (J2SE 1.2)首次引入的 Java 基础类(T1)(JFC)的一部分，是 Java 的“官方”(如果你愿意的话)GUI 框架。它内置于 Java (J2SE)的桌面版本中。Swing 的方法是在不牺牲跨平台一致性的前提下，尽可能地让用户界面组件(widgets)具有可定制性和可扩展性。

为此，它避免使用操作系统的内置小部件，而是使用纯 Java 代码在空白窗口中绘制用户界面并响应界面事件。

这样做的结果是，它的所有组件都是完全可皮肤化的，并且可以扩展定制的外观和行为。不利的一面是，用户界面经常与本机操作系统的外观和感觉不太匹配(尽管 Sun 在这方面做得越来越好)。此外，由于 Swing 惊人的灵活性，一旦您想做一些不太标准的事情，API 就会变得非常复杂。

此外，由于 Swing 的复杂性，初学者很容易“以错误的方式”做事，生成的应用程序可以工作，但在响应用户请求时似乎很慢或没有响应。

因为 Swing 内置于 Java 中，所以它非常适合必须最小化应用程序大小和跨平台部署必须完全透明的情况。因此，对于需要构建通过 [Java Web Start](http://java.sun.com/products/javawebstart/) 启动的小程序或应用程序的 Web 开发人员来说，Swing 仍然是最佳选择。

**SWT** 的缩写形式

从应用程序开发人员的角度来看，SWT 是用 Java 构建 GUI 的一个简单得多的 API。它没有回避操作系统的本机用户界面特性，而是欣然接受了这些特性，利用本机小部件来提供最佳性能并简化编码。SWT 最初是为在 Eclipse 项目中使用而开发的，但是开发人员已经利用它来构建各种桌面应用程序。

SWT 的好处是显而易见的:它是一个容易编写的 API，这意味着你可以更快地产生令人印象深刻的结果，而且因为小部件都是由本机操作系统处理的，用户会发现界面外观和行为完全熟悉。使用 SWT 也更容易实现良好的接口性能(即响应时间)。

根据你所做的开发类型，SWT 的负面影响要么是微不足道的，要么是致命的。用自定义功能扩展 SWT 小部件是困难的，通常也是不切实际的。因为 SWT 使用本机操作系统功能，所以 SWT 应用程序必须与本机库捆绑在一起，这些库与运行程序的操作系统接口。这意味着您通常必须为 Windows、Linux with GTK、Linux with Motif 和 Mac OS X 分发不同版本的应用程序。由于库的大小，部署使用 SWT 的小程序通常是不切实际的，并且对 Java Web Start 的支持也是有问题的。

**斯温的复兴**

虽然在一段时间内，Swing 似乎停滞不前，而新成员 SWT 吸引了所有的注意力，但随着好奇的开发人员探索可以用 Swing UI 组件实现的定制范围和视觉效果，Swing 正在经历复兴。

以下是我最喜欢的几个例子:

*   为 JTabbedPane
    创建一个定制的 UI 委托实现了 Adobe 风格的“迷你标签”,展示了在不实现整个外观的情况下改变一个小部件的外观是多么容易。
*   [数据提示](https://datatips.dev.java.net/)
    Slicker 与大多数本机实现相比，当列表项被列表宽度剪切时，它会在鼠标悬停时显示列表项的完整标签。
*   [Help Your Shelf(waiting dialog for Swing)](http://jroller.com/page/gfx/?anchor=help_your_shelf_waiting_dialog)
    虽然有点不必要，但这个例子只是触及了通过将 Java2D 效果与 Swing 组件相结合所能达到的视觉效果的表面。
*   3D 挥杆
    如果只是为了证明没有限制，这个例子展示了当用 3D 眼镜观看时出现在 3D 中的挥杆外观和感觉！

观看最新的实用和不太实用的挥杆实验的绝佳地点是 ClientJava.com 大学的博客。

## 分享这篇文章