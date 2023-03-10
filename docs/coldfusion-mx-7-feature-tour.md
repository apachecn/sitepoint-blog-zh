# ColdFusion MX 7 功能导览

> 原文：<https://www.sitepoint.com/coldfusion-mx-7-feature-tour/>

**本文将重点介绍 CFMX7 的新特性，以及这个新版本如何改变 Web 应用服务器市场。我不会试图教你如何使用所有新增加的东西；相反，我们将看看这个版本中令人激动的新特性。**

[ColdFusion MX7](http://www.macromedia.com/software/coldfusion/) 包含了许多新功能。这里我们将重点讨论的包括 Flash 表单、文档生成、事件网关和报告功能，当然还有很多其他功能。

##### Flash 表单

Macromedia 将其演示服务器 Flex 的一部分引入 ColdFusion，并添加了 Flash 表单。此功能使开发人员能够创建有吸引力的、用户友好的表单，这些表单可以跨平台交付给任何 Flash 兼容的媒体。

要开始在 CFMX7 中使用 Flash，您需要在`CFFORM`标签中指定表单的格式以及宽度和高度:

```
<cfform format="flash" width="400" height="300"> 

</cfform>
```

您可能会注意到 HTML 和 XML 格式也是可用的。现在，我们将坚持使用 Flash 格式。

如果您运行上面的代码，将创建一个具有指定宽度和高度的 Flash 电影，尽管其中没有显示任何内容。别担心:那只是因为我们还没有给电影添加任何内容！

Flash 表单中的信息被组织在由`CFFORMGROUP`标签标识的表单组中。这个标签包含 13 种不同类型的组，但是在本文中，我们将只探索其中的几种。

首先，让我们看看这个简单的表单:

```
<cfform format="flash" width="400" height="300"> 

  <cfformgroup type="tabnavigator" height="200"> 

    <cfformgroup type="page" label="First Tab"> 

      <cfformitem type="text">This tab hold just plain text.</cfformitem> 

    </cfformgroup> 

    <cfformgroup type="page" label="Second Tab"> 

      <cfformitem type="text">This is tab hold some form elements!</cfformitem> 

      <cfinput type="text" label="My name" width="190" name="myName"> 

      <cfinput type="radio" label="I'm using ColdFusion MX 7.0" name="CFver" value="70"> 

      <cfinput type="radio" label="I'm using ColdFusion MX 7.0.1" name="CFver" value="701"> 

      <cfinput type="checkbox" label="This is a SitePoint.com article" name="sitePoint" value="yes"> 

    </cfformgroup> 

  </cfformgroup> 

</cfform>
```

您可能已经注意到代码中的几个`CFFORMGROUP`标签。每当您想要对表单元素进行分组时，就会使用这些标签。在本例中，我们创建了一个带有两个选项卡(页面)的选项卡导航器。每个选项卡都有自己的表单组。如果您想要创建三个选项卡，您必须创建三个类型为“page”的子表单组。

表单中包含的所有其他标记在 ColdFusion 的早期版本中已经存在。确保您至少安装了 Flash Player 7。

当您运行上面的代码时，您将得到以下结果:

![1500_flashform](img/14452fd1fa9b12c27b4d4a8e5c25ef57.png)

您可以选择“第一个标签”和“第二个标签”来查看不同的内容。

##### 文档生成

让屏幕上的信息以相同的外观和感觉出现在其他地方一直是基于 Web 的应用程序的创建者面临的问题。嗯，不再是了！现在，您可以使用您想要的确切格式来指定 PDF 或 Flash 纸质文档的生成。只需在 CFMX7 中使用`CFDOCUMENT`标签——就这么简单！试试这个:

```
<cfdocument format="pdf"> 

  SitePoint.com - Introduction to ColdFusion 7 

  <h1>PDF Generation</h1> 

  <hr /> 

  This is an example of PDF generation using ColdFusion 7\. 

</cfdocument>
```

你最终会得到什么？包含您的内容的全功能 PDF，完全按照您想要的样式显示:

![1500_pdfgen](img/8cad295839d3aa986c426bef2be95523.png)

##### 事件网关

从版本 7 开始，ColdFusion 支持网关。网关是其他系统的接口；它们是事件触发 ColdFusion 动作的方式。CFMX 7 支持几种网关，包括异步处理、JMS、XMPP 和 SMS 网关。

网关是如何工作的？嗯，如果您的系统中有在线 SMS 网关，ColdFusion 可以侦听包含命令的传入 SMS 消息，然后触发对这些命令的处理(例如授权请求)。它甚至可以给你发一条短信，告诉你处理的结果。

要了解网关是如何工作的，请查看 Macromedia 的 Ben Forta 编写的用于即时消息实现的 CFDOCS。如果你有雅虎！Messenger，您可以添加一个名为“cflivedocs”的好友。然后，打开一段对话，写`CFDOCUMENT`。惊喜！服务器直接从 Macromedia 的产品参考手册返回标签信息。这适用于任何 ColdFusion 标签。

![1500_cfdocs](img/8bbc777f0204cb87b73379c81ff0d7d5.png)

##### 报告

报表生成器是 CFMX7 的另一个令人兴奋的功能。这是 Coldfusion 套件随附的独立应用程式，可让您建立动态报告范本(CFR 档案，适用於 ColdFusion 报告)。

![1500_report](img/917faf67ff8408c395e3ea14c4454171.png)

CF Report Builder 还包括一个可视化查询生成器，允许您指定布局，并在方便时格式化每个报告元素。

您可以使用 CFREPORT 标记在页面上显示这些报告。输出可以导出为 PDF、Flash Paper、Excel 和 RTF 格式，但是，要获得对最后一种格式的支持，您必须安装 ColdFusion 7.0.1。这是如何做到的:

```
<cfreport template="YourReport.cfr" format="pdf"> 

</cfreport>
```

##### 开发新的 ColdFusion

当使用 CFMX7 时，所有这些新功能都会使一些当前的 IDE 软件(如 Dreamweaver)过时。Dreamweaver 8 附带了完整的 CFMX7 支持，但是如果您使用的是以前的版本(如 MX 2004)，您可以从 Macromedia 网站下载 [CFML 7.0 支持插件](http://download.macromedia.com/pub/coldfusion/7/dwextend/dwmx2004_extensions_for_cfmx7.mxp)。还有[更新的代码和帮助文件可供 Homesite+](http://download.macromedia.com/pub/coldfusion/7/hsextend/hsplus_extensions_for_cfmx7.zip) 使用。如果您已经在使用 ColdFusion 7.0.1，ide 尚不支持该版本中引入的新功能。

如果你在寻找 Macromedia 自己软件的替代品，你可以为 Eclipse 3.x 平台使用优秀的 [CFEclipse 插件。当前版本(2.0)完全支持 CFML 7.0。习惯 Eclipse 环境，因为它将是即将推出的 Macromedia 的 Flex IDE 的基础。](http://www.cfeclipse.org/)

##### 进入 Java 世界

每个人都知道 ColdFusion 使 Java 类的使用变得非常容易。现在，有了 7.0.1 版，好处变成了双向的。

ColdFusion 现在有 CFCProxy，它使程序员能够直接从 Java 代码中调用 CFC 组件。这肯定会强化 ColdFusion 的企业层面。有了这项新功能，Macromedia 确保在 Java 或 ColdFusion 上的投资可以重复使用，为客户提供附加价值。

##### 包扎

关于 Macromedia ColdFusion 的新功能还有很多要说的，市场已经对这个功能丰富的新版本做出了反应。Macromedia 即将与 Adobe 合并，这将巩固 ColdFusion 在市场中的地位，我相信我们会在下一个版本中看到很棒的东西。这是 CF 社区激动人心的时刻！

## 分享这篇文章