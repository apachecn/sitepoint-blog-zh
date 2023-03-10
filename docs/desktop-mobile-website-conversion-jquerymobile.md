# 用 jQueryMobile 实现桌面到移动网站的转换

> 原文：<https://www.sitepoint.com/desktop-mobile-website-conversion-jquerymobile/>

jQuery Mobile 是制作移动 web 应用程序的强大框架。但是我们可以用它将现有的桌面网站转换成移动表示吗？

可以使用 MobilizeToday.com 的在线服务。他们提供了基于窗口小部件的模型，将任何现有的 HTML 内容表示到移动视图中。

[](http://widgets-5-0.mobilizetoday.com "jQueryMobile Widgets by MobilizeToday.com")

[![jQueryMobile Widgets by MobilizeToday.com](img/e9d8099764757ecce05f0949eab3df3b.png "jquerymobile-widgets-list")](http://widgets-5-0.mobilizetoday.com "jQueryMobile Widgets by MobilizeToday.com") 

背后的技术是 XSLT 转换。在线工具 Mobile View Builder 简化了转换过程。

 [![Mobile View Builder](img/8b95958389bd926ee23caa51c961690f.png "builder")](http://www.mobilizetoday.com "Mobile View Builder") 

使用 Builder，您可以轻松选择想要在移动版本中看到的元素。一旦选择了内容，它就会自动为 jQueryMobile 小部件生成 XPaths 值。如果您了解 XPath / XSLT，您可以全面地使用它，但这不是必须的。例如，您可以通过单击鼠标从典型的项目列表中获得如下视图:

 [![jQueryMobile Widget Example](img/c63e382522b53ebf56ca607dda2b969f.png "list-view-widget")](http://widgets-5-0.mobilizetoday.com/widgets/5.0/list-view.html "jQueryMobile Widget Example") 

生成的移动网站与桌面网站同步。这意味着如果桌面网站内容发生变化，移动网站也会自动更新。该解决方案支持通过可选的安全 https 连接提交表单，如登录、联系和结账(这对电子商务网站来说绝对是个好消息)。

为了开始，你可以[创建新账户](http://www.mobilizetoday.com/user/register)或者在他们的[主页](http://www.mobilizetoday.com)上提交表格。要创建基于 jQueryMobile 框架的移动网站，您应该转到“我的网站”，单击“添加新网站”，键入桌面网站 URL 并选择 jQueryMobile 作为框架。如果您希望从头开始构建移动网站，请取消选中“自动生成移动版本”。之后点击“添加网站”。

 [![add-jquerymobile-website](img/676698f52272894b370ec0660a6f7bf0.png "add-jquerymobile-website")](http://www.mobilizetoday.com "Add New Website") 

如果你自己创建移动网站，每个网站每年只需 49 美元。第一个网站可以免费发布。你可以选择雇佣开发人员来为你做这件事。这个选项大约需要 390 美元。由此产生的移动网站可以被任何域访问。移动用户的重定向脚本可用。经销商可以使用自己的品牌(白标)。

尽管有缺点或限制。解决方案不支持 flash 网站，也不支持基于框架集的网站。如果你的网站包含大量的 JavaScript，特别是用于提交表单的，那么应该手动调整以适应移动浏览器。这使得优化过程非常昂贵。

总而言之，MobilizeToday.com 为任何网站所有者提供了一个平台，以最小的投资弥补移动网络的差距，并使他们的业务移动化。对于经销商来说，MobilizeToday.com 为他们提供了一个在现有产品组合中添加移动网络选项的机会，以更新与现有客户的关系和/或获得新客户。由此产生的移动网站由 jQueryMobile 框架构成，该框架保证了与所有现代智能手机和平板电脑的兼容性。

## 分享这篇文章