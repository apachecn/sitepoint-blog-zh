# 表情混合行为

> 原文：<https://www.sitepoint.com/expression-blend-behaviors-2/>

在我的上一篇文章 [*用 Microsoft Expression Blend*](https://www.sitepoint.com/article/microsoft-expression-blend)设计中，我们学习了使用 Expression Blend 设计 Microsoft Silverlight 应用程序的基础知识。我们在我的第一篇文章中采用了 SketchFlow 中构建的原型( [*快速原型与 SketchFlow*](https://www.sitepoint.com/article/rapid-prototyping-sketchflow) )，我们创建了一个集成了浏览器历史的 Silverlight 3 应用程序。然后我们继续看 Blend 对样本数据的支持。最后，我们看了界面元素的风格和模板在 XAML 的使用。

在我的最后一篇文章中，我们将进一步介绍我们的样例项目组合应用程序。我们将学习故事板以及如何制作动画。然后，我们将回到行为，并学习如何使用它们在我们的应用程序中建立交互性，而无需编写任何代码。在这篇文章之后，我将交给 Tatham Oddie，他将带你了解 Expression Blend 的姐妹产品的功能: [Expression Web](http://www.microsoft.com/expression/products/Web_Overview.aspx) 。

一旦你读完了，一定要看看我们的[文章测验](https://www.sitepoint.com/quiz/microsoft/expression-blend-behaviors)来测试你自己。

## 爆炸性新闻:Silverlight 4、Expression Blend 4 和 Windows Phone

在我们开始之前，我们应该回顾一下自上一篇文章以来微软世界发生了什么。微软最近在拉斯维加斯举行了年度 MIX 大会，会上有很多关于 Expression 和 Silverlight 的公告。

首先发布了 Silverlight 4。Silverlight 4 引入了一系列新功能，不胜枚举，但我最喜欢的是:

*   *浏览器外和多点触控支持* 为专用体验带来了一些有趣的机会，如信息亭和基于平板电脑的应用

*   *网络摄像头和麦克风支持* 将允许大量新应用，如增强现实

*   *鼠标滚轮、右击和拖放支持* 对于像我这样的商业应用程序设计者来说尤其是个好消息

*   *【吐司】通知* 使用原生操作系统

*   *谷歌 Chrome 支持* 现已正式上线

Silverlight 4 提供了 Expression Blend 4 的候选版本。Blend 4 还有一些有趣的新功能，包括:

*   Silverlight 4 和 Windows 演示基础(WPF) 4 支持

*   一些期待已久的基本形状，如多边形、箭头和弧形

*   在 SketchFlow 中更好地管理草图样式

*   对 SketchFlow 动画的更多控制

*   许多新的行为，包括管理触摸用户界面的行为

*   用于存储影响 SketchFlow 原型的全局变量和其他状态的数据存储

对于这篇文章，我已经切换到使用表达式混合 4。

最后，MIX10 的另一个重大声明是 Silverlight 将成为设计 Windows Phone 用户体验的主要平台。这实际上意味着任何目前是 WPF 或 Silverlight 设计师的人现在也是 Windows Phone 设计师。

同时，我们的当务之急是完成我们的投资组合申请。第一站:真实数据。

## 向我们的应用添加数据

在上一篇文章中，我们使用 Blend 的样本数据特性来生成一些占位符数据，以表示我们的投资组合的项目列表。这就是我们如何创建椅子的拉丁文本和图片列表。现在是时候添加一些真实的项目数据了。

我们可以通过多种方式获取项目信息。我们可以:

1.  从 web 服务获取信息，可能是 XML 格式的

2.  检索 XML 文件并使用。NET 的基于 SQL 的“Linq”特性

3.  从底层 HTML 页面获取信息(这个选项有一些吸引力，因为它允许我们为没有 Silverlight 的浏览器提供一个页面的 HTML 后备版本，还允许搜索引擎索引我们的 Silverlight 应用程序的关键内容)

4.  只需将数据嵌入到我们的项目中(可以包含文本和图像，或者只包含文本，图像由 URL 引用)

虽然可能推荐第三个选项，但由于本文关注的是设计而不是开发，我们将使用更简单的第四个选项。

### 构建 XML 数据文件

第一步是构建一个 XML 文件，它将包含我们投资组合的样本项目数据。

对于每个项目，我们需要:

*   一个标题

*   简短的描述

*   两幅图像

Tatham 将在接下来的两篇文章中详细介绍 Expression Web，但是现在，Expression Web 为我提供了一个非常好的 XML 编辑器，可以用来输入我的项目信息，如图[图 1，“在 Expression Web 中查看的 XML 文件”](#fig_expression_xml "Figure 1. An XML file viewed in Expression Web")所示。

**图一。在 Expression Web 中查看的 XML 文件**

![An XML file viewed in Expression Web](img/d7a87cec30b521479d43f759cc296191.png)

现在我有了我的真实数据，我可以在 Expression Blend 中打开我们的原型并替换之前的样本数据。

### 从 XML 创建数据源

Blend 中的数据面板允许我们添加各种数据源，包括从 XML 导入一个数据源。面板如图[图 2 所示，“使用数据面板导入 XML 数据源”](#fig_data_panel "Figure 2. Use the Data panel to import an XML data source")。

**图二。使用数据面板导入 XML 数据源**

![Use the Data panel to import an XML data source](img/c117f28d68e1e84b5d6a3fc4c0829bb7.png)

请注意，Blend 仍然将这种数据称为“样本数据”，但是对于我们的应用程序所需的少量信息，它工作得很好。例如，如果我们有更多的数据，我们可能希望在主应用程序加载后更智能地检索这些数据。

选择在应用程序运行时启用样本数据以在运行时使用您的样本数据，如图[图 3，“导入样本数据”](#fig_sample_data "Figure 3. Importing sample data")所示。

**图 3。导入样本数据**

![Importing sample data](img/a4825e9cedeb93da4ac2dd9c1240d239.png)

一旦加载了新的样本数据，下一步就是更改我们的投资组合的主面板和细节面板的数据绑定。

### 调整导入数据的数据类型

如果我们在数据面板中展开导入数据的模式，我们可以看到 Blend 假设每个数据元素都是一个字符串。这对于所有元素都没问题，除了一个元素，即`Image`元素，它实际上包含了图像的文件名——所以我们需要改变它。

Blend 在数据面板中提供了一个下拉列表，如图[图 4 所示，“数据类型在数据面板中设置”](#fig_data_type "Figure 4. Data types are set in the Data panel")，让我们将数据类型调整为图像，并指向包含图像的目录。Blend 会将图像导入到项目中。

**图 4。数据类型在数据面板**中设置

![Data types are set in the Data panel](img/5c88deb9bda9aeef6fe3ae81c1ebc1c2.png)

### 将新数据绑定到主列表

正如我们在上一篇文章中看到的，您可以通过从数据面板中拖动数据定义，将像`ListBox` es 这样的元素绑定到样本(或活动)数据。如图 5[所示，“从数据面板拖动数据，将其绑定到目标元素”](#fig_data_binding "Figure 5. Drag data from the Data panel to bind it to the target element")。

**图 5。从数据面板拖动数据，将其绑定到目标元素**

![Drag data from the Data panel to bind it to the target element](img/bd685f2f2009e54a702876274eb22970.png)

将新的数据源拖到列表上确实会有重置列表使用的模板的副作用。在上一篇文章中，我们为单个的`ListItem`及其容器定义了模板，所以我们现在需要将`ListBox`的模板重新分配回这些值。

**图 6。给列表项分配模板**

![Assigning Templates to the ListItems](img/f5471ffb1da9c507d245a4ef277ca3f5.png)

通过在新的 XML 数据源中使用与我在原始示例数据中使用的相同的标记名(例如，`<ProjectName>`)，先前应用于`ListBox`的`ListItem`的模板仍然可以在新的数据源中使用(因为模板在其当前的数据上下文中查找`ProjectName`属性)。

随着新项目列表绑定到主列表，我们可以移动到细节面板。

### 将新数据绑定到详细信息面板

将新项目数据绑定到细节面板甚至更容易。在上一篇文章中，我们创建了一个`StackPanel`来包含细节视图中的所有项目。如果我们选择`StackPanel`并查看数据面板，我们可以看到`StackPanel`将其`DataContext`设置为我们制作的样本集合。我们需要更改这个`DataContext`,以指向我们新的 XML 数据源。

**图 7。数据面板显示了当前选中元素**的`DataContext`

![The Data panel shows us the DataContext of the currently selected element](img/34d55251d20ad7efcd5fa1a714763523.png)

我们只需要更改细节`StackPanel`的`DataContext`来指向我们的新数据源。Blend 让我们简单地将新的数据上下文从数据面板中拖出，放到`StackPanel`上。我们只需要记住先将数据面板切换到细节模式，这样我们就绑定到集合中的当前选择，而不是整个列表。

当页面填满时，使用对象和时间轴面板中的对象通常比使用画板上的对象更容易，如图[图 8 所示，“使用对象和时间轴面板”](#fig_objects_timeline_data "Figure 8. Working with the Objects and Timeline panel")。

**图 8。使用对象和时间轴面板**

![Working with the Objects and Timeline panel](img/375c544915be90c1170964a5ca13e72a.png)

### 调整细节面板的背景

在这一点上，对于我的投资组合，我想调整细节面板的背景，以便当描述文本足够长时，后面的徽标被部分覆盖。我将简单地选择包含细节面板的`StackPanel`,设置它的背景以匹配界面的背景，然后调整 alpha 通道，使徽标只显示一点点。

**图 9。调整阿尔法通道**

![Adjusting the alpha channel](img/a934e90350d7f0635c3c2183b07e0a7c.png)

现在，我们有一个功能完整的投资组合，带有一些静态的视觉风格。

**图 10。功能组合**

![The functional portfolio](img/2654795fd595a53dfd208eaca063c3ee.png)

下一步是添加动画和过渡，以使应用程序更流畅，并帮助访问者理解应用程序的机制。

## 分享这篇文章