# 使用 Microsoft App Studio 轻松运行 Windows Phone 应用程序

> 原文：<https://www.sitepoint.com/easy-windows-phone-apps-with-microsoft-app-studio/>

非编码人员创建基本移动应用的选择越来越多。在 SitePoint 上，我们有一篇文章介绍了在不编码的情况下创建移动应用的 3 个选项。

现在微软有了自己的替代产品，名为 [App Studio](http://appstudio.windows.com/en-us) ，我将展示如何使用它为 SitePoint 创建一个移动应用程序。使用 App Studio 构建应用程序所需要的只是一个[微软账户](https://signup.live.com/)，你就可以开始了。

## 入门指南

App Studio 允许您使用 Windows 8.1 和新的 Windows 10 操作系统为手机和平板电脑创建应用程序。您可以嵌入 RSS 源、YouTube 频道、脸书和 Twitter 源以及 HTML 页面，或者选择一个可用的模板。

要使用 App Studio 创建您的第一个项目，请登录您的 Microsoft 帐户，然后转到 [App Studio 仪表盘](http://appstudio.windows.com/projects)。

我们将创建一个 Windows 8.1 应用程序，单击*开始新项目*按钮，并确保选择 Windows 8.1 选项卡。

选择*空 App* 模板，将 <q>SitePoint</q> 设为 App 名称。

点击*从这个开始！*。这将把你重定向到一个*编辑你的应用程序内容*页面，在这里你可以更改你的应用程序的内容。您可以在这里插入内容，通过选择主题或修改主题来修改外观，设置图标和闪屏，以及更改其他设置。

![Empty app](img/411b569b06d570e5697b143df703c555.png)

## 第一个屏幕

我们构建的第一个屏幕将是一个菜单。点击*菜单*按钮，将标题设置为<q>站点点</q>。它将创建一个蓝色瓷砖来标识刚刚构建的菜单屏幕。

![Menu screen](img/e20cee3a4e340fc6d950387af27b38cd.png)

在该区域中，您可以看到三个图标。第一个类似汉堡图标，向菜单中添加内容。单击它并添加 9 个菜单项来表示 SitePoint 类别。

第一个 URL 将是用于*菜单动作标题*的 <q>HTML 和 CSS</q> 以及用于*菜单项动作值*的[https://www.sitepoint.com/html-css/](https://www.sitepoint.com/html-css/)。要保存更改，点击*保存*按钮(如果您忘记保存，切换前会提示您保存)。您可以用同样的方式继续其他菜单项。

![Menu items](img/4d49b4b2aec26983d4a402aebcb8b8c2.png)

现在做一些造型。我想把这些项目列成一个清单，一个叠一个。为此，点击菜单平铺的编辑按钮(第二个看起来像铅笔的按钮)并选择*照片左侧*布局，这将显示项目和名称的缩略图。你可以在这里找到我用的[的图片](https://uploads.sitepoint.com/wp-content/uploads/2015/10/1444311095resources.zip)。

![List style](img/5e6b21ecb7e2854eb1245a4a02c13d3e.png)

要为菜单项添加图标，单击每个菜单项的编辑按钮，单击每个菜单项的默认图标，并选择列表项的图像。

我们需要一个应用程序的图标。进入 *Tiles* 菜单，点击空白图像添加所需图标。

![Icons](img/49389807e66b7ba4a749f012d399fa4b.png)

对于主题，您可以从 Microsoft 提供的列表中选择一个。打开*主题*标签，从列表中选择一个，随意改变颜色。我用白色主题。

## 添加更多视图

现在我们有了第一个视图，我们可以添加更多。我想听听这些观点:

*   关于我们
*   新闻(RSS 源)
*   SitePoint 的 Twitter 页面
*   SitePoint 的 YouTube 频道
*   SitePoint 的脸书页面

对于“关于我们”页面，点击 *HTML* 按钮，并将标题设置为*关于我们*。内容摘自 SitePoint 的[关于我们的页面](https://www.sitepoint.com/about-us/)，内容如下:

```
<p>
SitePoint provides cutting-edge content for web professionals — developers, designers, programmers, freelancers and site owners.
</p>

<p>
Founded by <a href="https://twitter.com/daxatron">Mark Harbottle</a> and <a href="https://twitter.com/MattMickiewicz">Matt Mickiewicz</a>; our mission is to deliver new ideas, emerging concepts, and teach state-of-the-art technology to our readers. Whether it’s via our practical tutorials, books, articles, courses, if it’s got anything to do with building the web - we've got you covered.
</p>
```

点击*保存*按钮，视图被添加到应用程序中。通过左右滑动，你可以改变应用程序的视图。

对于 RSS 提要，点击 *RSS* 按钮，将标题设置为*提要*，将提要 url 设置为【https://www.sitepoint.com/feed/】的。通过点击*编辑*按钮，您可以设置 RSS 提要的样式，包括列表页面和单个项目(也称为详细信息)页面。我选择*没有照片*的列表布局，因为不是所有的文章都有照片，我希望提要保持一致。我为单品布局选择了*通用布局*。

对于 Twitter 页面，点击 *Twitter* 按钮，您需要创建一个 API 密匙。点击*创建新的 API 密匙*，这将引导您创建添加 Twitter 支持所需的 API 密匙。创建 API 密钥后，从下拉列表中选择*用户时间线*，并将用户名设置为`sitepointdotcom`。在这里，您可以像在 Feed 示例中那样更改布局，但我将它保留为默认布局，用户照片放在左侧。

这同样适用于 YouTube 频道。点击 *YouTube* 按钮，创建 API 密钥。在数据配置阶段，从下拉菜单中选择*用户*，并将用户名设置为 *<q>站点点</q>* 。你可以改变布局，但我会保持默认，视频缩略图在左边。

对于脸书页面，做与 Twitter 和 YouTube 相同的事情。选择 API 键(或者创建一个)并搜索名为`sitepoint`的脸书页面。从结果中选择并确认所有更改。

## 包扎

在构建了这个应用程序之后，最后的步骤是让每个人都可以使用它。首先，确保您保存了任何更改，并切换到*设置*选项卡。添加应用程序描述，例如:

> SitePoint 为网络专业人士——开发者、设计师、程序员、自由职业者和网站所有者提供前沿内容。

然后点击*完成*按钮。App Studio 然后生成三个下载:

*   一个可安装包，您可以将它安装在您的移动设备上
*   发布包，您可以将其上载并发布到 Windows Phone 应用商店
*   源代码

对于发布包，您必须填写“设置”选项卡中“商店关联”部分的字段。

![Install the app](img/9fb8a68d10259447d424c829d7394981.png)

恭喜你！您刚刚为 SitePoint 构建了一个 Windows 8.1 应用程序。让我知道你用 App Studio 尝试的其他实验。

## 分享这篇文章