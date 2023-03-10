# Photoshop vs. Sketch:最干净的 HTML/CSS 之战

> 原文：<https://www.sitepoint.com/exporting-code-sketch-vs-photoshop/>

![Code generation ](img/3051d6e57591f0af4e7e55d988e1d5f7.png)

早在三月份，我写了一篇文章，概述了使用 Sketch 的 3 个理由，并将“导出”功能与 Photoshop 的“提取资源”进行了比较。

在我看来，在那篇文章中，Sketch 略占优势。然而，图像资产并不是您可以从布局组合中获取的唯一资产类型。在本文中，我们将看看这两个应用程序如何处理 HTML/CSS 代码生成的微妙任务。

## 方法一:老办法，用 Photoshop

[![CSSHat](img/33bd086bdc8ec363bdd12c30ba2f11c4.png)](https://csshat.com/)

自 CS6 以来，Adobe 允许他们的社区为 Photoshop 构建自己的扩展。这导致了许多广受好评的代码提取工具的诞生，比如免费的 [CSS Hat](https://csshat.com/) 和 [CSS3Ps](http://css3ps.com/) 。

Photoshop 一直是一个非常通用的工具，但自从移动设计真正成为主流以来，这些代码提取扩展对于维护省时的设计/开发工作流变得更加重要。

## 方法 2:使用 Sketch App 导出代码

Sketch App 的图层样式非常以 CSS 为中心(它毕竟是一个用户界面设计工具)，所以右键点击“复制 CSS 属性”非常容易。例如，“阴影”接受四个值； *X，Y，模糊和扩散*，这直接翻译成 CSS 的`box-shadow: X Y Blur Spread`。在我看来，这是最简单的代码提取形式，而且效果很好。

![Sketch App - Copy CSS attributes](img/1dcd92a76105fd171f77c84b1cfe94b2.png)

然而，Sketch 应用程序肯定仍然是相当新的，它没有一个用于代码编辑的配套应用程序。我不知道 Bohemian Coding 是否打算纠正这一点(这肯定会很棒)，但在这个时候，Sketch 没有办法在编码方面扩展工作流。

这意味着我们总是需要在 Sketch 应用程序和我们选择的代码编辑器之间来回切换，以手动弥合我们的草图样式和代码之间的差距。

## 方法 3:用 Adobe 括号提取代码

在过去的 18 个月里，Adobe 在连续性方面取得了惊人的进步；有一系列移动应用程序可以使用设备摄像头捕捉现实生活中的矢量、画笔和颜色，这些捕捉会自动与您的 Creative Cloud 帐户同步。

但最令人印象深刻的合作是[括号](http://brackets.io/)和 Photoshop 的合作。括号是一个面向前端开发人员的开源代码编辑器，由 HTML、CSS 和 JavaScript 构建而成。尽管它有“实时预览”功能，并与 Edge Web 字体集成，但它的用户界面实际上很小，因为你只需要添加社区构建的扩展所需的功能。

### 将 Photoshop 文档导入到括号中

自从发布了括号 Adobe 放弃了他们的“Edge Code”分支并坚持使用“括号”这个名称)，web 开发人员现在可以直接将 Photoshop 文档导入括号中，并在编码时提取颜色、字体和其他 CSS 属性等设计信息，即使他们的计算机上没有安装 Photoshop。

事实上，Creative Cloud 用户可以向您发送一个“共享链接”,您可以将它复制到括号中，这样您甚至可以在没有文件的情况下启动提取。让我们试试看。

首先，找到右侧边栏，找到显示工具提示“提取括号(预览)”的图标-单击它，您会注意到一个样本 Photoshop 文档与所有图层保持不变，您可以在其中打开和关闭这些图层，就像您在 Photoshop 本身中一样。括号很好地帮助您了解了这一点，所以我会尽量简短。

### 测量两层之间的距离

你可以通过点击第一层，按住 *Shift* ，然后点击第二层，在 Sketch App 中测量两层之间的距离。我认为这是难以置信的直观(因为它是),直到我在*为括号*提取中做了同样的事情，并意识到我可以将值复制到剪贴板，并在我的样式表中使用它，而无需离开应用程序。实际上，你可以按住*键*，将任意值复制到剪贴板。

### 如何使用 CSS 提示快速编码

CSS 暗示是一个特殊的功能，它确实显示了许多括号正在成熟，尽管 Dreamweaver CC 仍在竞争中。在下一步中，我们将从 PSD 中选择一个层，并从中提取所有的样式。

所以首先，选择一个层，然后切换到一个样式表。当需要声明一个样式时，括号不仅会根据您的输入给出具体的代码提示，还会根据您选择的图层提供已经定义好的值。

![Photoshop / Brackets integration](img/af9e8f99932c4e8e225fb5c094de6083.png)

然而，一个缺点是，这个特性不能根据浏览器厂商自动为样式属性添加前缀，但是如果你需要的话，有一个扩展可以实现这个功能。

## 判决

素描 App 和 Photoshop 现在已经争了相当一段时间了。Photoshop 是一个巨大的多用途工具。虽然有些人肯定会说，对于一般的用户界面设计师来说，它有点太“功能快乐”了，但它确实有 Sketch App 没有的两样东西，而且可能永远也不会有——连续性和协作。

能够在分屏风格的工作流中将 PSD 转换为 CSS 代码，甚至不需要在你的计算机上安装 PSD，这是我最近几个月遇到的最好的协作功能。

最棒的是，**括号是免费的**，无论你是否有 Photoshop 或 Creative Cloud 订阅。

## 分享这篇文章