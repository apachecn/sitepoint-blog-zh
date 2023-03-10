# 用 Visual Basic(或者 JavaScript！)

> 原文：<https://www.sitepoint.com/write-java-web-apps-in-visual-basic-or-javascript/>

更多来自 [JavaOne 2006 主题演讲](http://java.sun.com/javaone/sf/sessions/general/index.jsp) …

Java 6 (Mustang)中的一个新的关键特性是支持在 JVM 上运行的替代语言，该特性现在可以在测试版和每周快照中获得，并计划在 10 月份发布。特别是，Java 6 将支持作为一等公民运行 JavaScript 代码，完全访问 Java 类库，并能够在单个应用程序中在 Java 和 JavaScript 代码之间来回调用。

在周二的 JavaOne 大会上，Sun 发布了两个新项目，它们以一种 web 开发人员感兴趣的方式利用了这种能力。第一个项目 Semplice 将 Visual Basic 语言引入 Java 平台。Semplice 不是为了将现有的 VB 应用程序移植到 Java，而是为了允许 Visual Basic 开发人员在利用他们现有技能的同时过渡到 Java 平台，它允许您使用 VB 语法编写代码(包括所有自动类型转换、对由方法实现的属性的支持，以及 VB 开发人员非常喜欢的其他细节)，这些代码可以编译成将在 Java 6 VM 上运行的 Java 类。正如 J#之于 Java，Semplice 之于 Visual Basic。

在主题演讲期间的演示中，Semplice 开发人员[Tor Norbye](http://blogs.sun.com/tor)([Java Posse 播客](http://javaposse.com/)的常客)演示了使用预发布版本 [Java Studio Creator](http://developers.sun.com/prodtech/javatools/jscreator/) 在 VB 中构建一个简单的 web 应用程序。从一个空白页开始，他将许多 JavaServer Faces (JSF)组件拖到页面上，然后双击其中一个组件，向其添加一个事件处理程序。弹出的编辑器包含一个用 Visual Basic 编写的新事件处理程序，他很快添加了一些简单的实现代码，然后编译并运行应用程序。

他指出，该应用程序使用了用 Java 编写的 JSF 组件，他直接从自己的 Visual Basic 代码中访问这些组件的属性。此外，JSF 组件从 Java 内部调用 Visual Basic 事件处理代码。Semplice 允许这两种语言透明地协同工作。

得益于 VB 语言的特性，他不必调用`farenheit.getText()`来获取`farenheit`表单字段的值，他可以将其称为`farenheit.Text`，或者仅仅称为`farenheit`，这得益于 Visual Basic 的灵活性。他还能够在算术计算中使用得到的`String`值(即`celsius = (farenheit - 32) * 5 / 9`，而不必将其转换为整数。

对于那些在编写服务器端业务逻辑时需要 Java 的强大功能，但在编写 web 表示逻辑时需要一种更灵活、类型更松散的语言的开发人员来说，在 Java VM 上运行 Visual Basic 可能是一个非常有吸引力的选择！

然而，甚至比 Visual Basic 更灵活，而且他们可能更熟悉的是 JavaScript。那么为什么不用 JavaScript 编写 web 应用程序的服务器端代码呢？这正是 T2 项目火卫一 T3 将要做的。使用 Project Phobos，您可以直接在 HTML 模板中用 JavaScript 编写服务器端应用程序逻辑(就像在 PHP 或 JSP 中一样)，也可以在单独的“助手”脚本中编写，将代码与标记分开。

随着许多其他项目致力于将其他语言引入 Java VM，当考虑将 Java 作为 web 开发平台时，这两个项目给了开发人员更多的选择。

这两个演示的视频可以在 JavaOne 2006 大会第一天的 [Sun 技术大会的第三部分:Java 平台路线图:重要的东西，今天&明天](http://java.sun.com/javaone/sf/sessions/general/index.jsp)中找到。

项目开发者“HerbertC”的博客上发布了 Semplice 在行动中的全部细节和截图。

## 分享这篇文章