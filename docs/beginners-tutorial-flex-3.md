# 关于 Flex 3 的绝对初学者教程文章

> 原文：<https://www.sitepoint.com/beginners-tutorial-flex-3/>

随着网络用户期望更丰富、更复杂的界面，富互联网应用程序(RIA)正变得越来越流行。在构建 RIA 时，Adobe Flex 是许多 web 开发人员的首选工具。

**Flex 曾经是只有大型企业组织使用的技术之一——Flex 的第一版定价为每 CPU 15，000 美元左右(对大多数开发人员来说有点贵),从那以后，Flex 就作为开源软件发布了。谈一个彻底的翻盘！**

Flex 现在的版本是 3.0，在 Adobe Flash Player 9 运行时上运行。Flex 3.0 如此成功，以至于 Flex 4.0 已经宣布将于 2009 年发布。作为一名开发人员，尽早进入并尽可能多地学习是一个好主意——在 Flex 社区中脱颖而出将很快变得极其困难。

开发人员正以极快的速度使用 Flex。原因之一是，如果您已经熟悉 XHTML，那么 Flex 编程相对容易学习。当然，当你习惯一个新的环境时，总会有一个过渡期，但学习 Flex 不会花很长时间！

另一个吸引人的地方是，由于 Adobe AIR(Adobe Integrated Runtime)平台的奇迹，Flex 应用程序可以开发为在用户的桌面上运行。现在，您可以开发并启动一个 RIA，它可以在 web 浏览器中运行，也可以作为桌面应用程序运行。使用 Adobe AIR 将应用程序部署到用户的桌面很容易，用户只需单击 web 浏览器中的链接，在他们的计算机上安装 AIR 运行时和您的 RIA。这才叫快速部署！

这就够散漫的了；让我们了解一下 Flex 的基本知识。

##### 为什么使用 Flex？

如果你正在考虑构建一个 RIA，你有一些技术选择，包括 Flex、Ajax 和 Microsoft Silverlight。如果客观地看待这些选项，每个选项所需的开发工作(以及由此产生的用户体验)大致相同。Flex 的好处之一是它的覆盖范围——运行 Flex 应用程序的 Adobe Flash Player 已经安装在全世界大约 10 亿台计算机上！

当然，Ajax 还使用了安装在世界上几乎每台计算机上的技术——JavaScript、XHTML 和 CSS。然而，Ajax 的一个缺点是跨浏览器兼容性很难实现。在一种浏览器(例如 Firefox)中可能有效的东西在另一种浏览器(例如 Internet Explorer)中可能无效，因此调试过程可能会变得困难和冗长。

另一方面，微软 Silverlight 与 Flex 相似，它运行在浏览器插件上。然而，Silverlight 还没有到达 Flash player 的安装用户群。

如果您刚刚开始构建 RIA，那么您肯定应该尝试所有的 RIA，看看您最喜欢哪一个，并找到最容易使用的 Flex、Silverlight 和 Ajax 各有优缺点。不过，在我看来，Flex 绝对是最好的 RIA 开发技术。此外，如果您是 Adobe Creative Suite 的粉丝，那么了解这些工具(Flash、Photoshop、Illustrator、Fireworks 等)中的许多都内置了 Flex 组件集成是一件好事，无论您如何看待它，这都是一个额外的好处。

##### Flex 框架概述

许多人避开 Flex 框架，因为他们认为它很复杂。但一般来说，框架只是一组可重用的类，它们可以一起工作，为应用程序提供基础。

拿房子打个比方:地球上的每栋房子都有框架。每栋房子都有地基和墙，那些墙没有地基是立不住的。一旦奠定了基础和墙壁了，屋顶可以应用和内部设计和实施，而工作继续在最初的基础。

如果我们将这种类比应用于 Flex 框架，我们就有了一个逻辑堆栈—控制器逻辑—可用于与数据库通信、处理安全性、写入文件系统等。还有用户界面元素——按钮、画布、下拉列表等等。所有这些也构成了 Flex 应用程序的基础——混凝土板、木梁和建造房屋的砖块。

web 开发人员很容易学习 Flex，因为它的核心与(X)HTML、CSS 和 JavaScript 有很多共同之处。假设您想创建一个带有表单按钮的简单网页。在 XHTML 中，您可以键入以下内容:

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd"> 

<html > 

  <head> 

    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" /> 

    <title>Button Example</title> 

  </head> 

  <body> 

    <form method="post" id="example" action="http://www.example.com/"> 

      <input type="button" name="newButton" id="newButton" value="This is a button" onclick="checkForm()" /> 

    </form> 

  </body> 

</html>
```

当您在 web 浏览器中查看此标记时，您会看到一个带有标签“这是一个按钮”的按钮。

![The XHTML button](img/71a2ddbd256f963b1ea89564ce6e342e.png)

为了在 Flex 中显示类似的东西，我们使用了一种叫做 MXML 的标记形式。以下是我们之前示例的 MXML 标记:

```
<?xml version="1.0" encoding="utf-8"?> 

<mx:Application xmlns:mx="http://www.adobe.com/2006/mxml" 

    layout="absolute"> 

  <mx:Button x="10" y="10" label="This is a button" 

 id="newButton" 

      click="checkForm()"/> 

</mx:Application>
```

**Go to page:** [1](https://sitepoint.com/beginners-tutorial-flex-3) | [2](https://sitepoint.com/beginners-tutorial-flex-3-2/) | [3](https://sitepoint.com/beginners-tutorial-flex-3-3/)

## 分享这篇文章