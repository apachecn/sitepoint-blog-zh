# 使用 HTML5 构建 Windows 8 应用程序–第 2 部分

> 原文：<https://www.sitepoint.com/building-a-windows-8-app-with-html5-how-to-create-a-small-rss-reader/>

从零开始，我们将通过两个教程学习如何用微软 Windows 8 的 JavaScript 框架 **HTML5** 、 **CSS3** 和 **WinJS** 构建一个小型 RSS 阅读器。然后，我们将构建一个针对 Windows 商店的 WinRT 应用程序。我们还将尝试通过使用 **Expression Blend 5** 来遵循 Windows 8 UI 设计准则。如果一切顺利，你应该可以在 30 分钟内理解这篇文章。下周，我们将运行第二部分。

这第一篇文章将帮助您创建一个使用 WinJS ListView 控件的欢迎屏幕。这个控件将通过漂亮的缩略图显示最近发表的所有博客文章。第二个将在您单击其中一个项目时显示的详细视图上工作。最后，你会在最后找到最终的解决方案下载。如果您需要澄清本文的某些部分，请将其视为有用的补充资源。

**先决条件:** 要学习这些教程，您需要首先:

1–在你的机器上下载/购买并安装**Windows 8 RTM**:http://msdn.microsoft.com/en-US/windows/apps/br229516.aspx，那里也有 90 天的试用版。
2–下载&安装**Visual Studio 2012 Express RTM**for Windows 8:[http://msdn.microsoft.com/en-US/windows/apps/br229516.aspx](http://msdn.microsoft.com/en-US/windows/apps/br229516.aspx "http://msdn.microsoft.com/en-US/windows/apps/br229516.aspx")这是免费的，当然你也可以使用付费版本。

**注意:** 如果你有一台 Mac，它可以很好地工作，这要归功于 BootCamp 或由 Parallels 处理的虚拟机

**注 2:** 本文已于 2012 年 8 月 21 日更新，实现了 Windows 8 发布预览版与 RTM 之间代码中 UI &的变化。一般来说，如果您需要从 RP 迁移您的应用程序，您应该阅读这个文档:[突破性变化文档](http://go.microsoft.com/fwlink/p/?LinkID=258744)。在我们的例子中，唯一的影响与 Visual Studio 的新 UI &命名有关。

**注 3:** 我在这里添加了一个专门针对 **WordPress** 和**社区服务器**的补充帖子: [Windows 8 HTML5 Metro 风格 App:RSS reader in 30min——构建你的 WordPress 版本](http://blogs.msdn.com/b/davrous/archive/2012/07/06/windows-8-html5-metro-style-app-rss-reader-in-30min-the-wordpress-version.aspx "http://blogs.msdn.com/b/davrous/archive/2012/07/06/windows-8-html5-metro-style-app-rss-reader-in-30min-the-wordpress-version.aspx")

下面是我们将在本文中看到的内容的简要总结:

–第一步:创建一个空白应用程序
–第二步:创建我们主页
的 HTML & CSS 库–第三步:第一次接触 Blend
–第四步:用 XHR 加载数据并将它们绑定到 ListView 控件
–第五步:使用模板并用 Blend
修改设计–第六步:下载源代码

**注:** 这些教程基于 Chris Sell & Kieran Mockford 交付的[构建 Metro 风格应用的工具](http://channel9.msdn.com/events/BUILD/BUILD2011/BPS-1006)会话。我只是为 Windows 8 RTM 更新了它。

## 步骤 1:创建空白应用程序

首先，您需要启动 Visual Studio 2012，并通过“文件–>新建项目”创建一个新的 JavaScript –> Windows 应用商店空白应用项目:

![New Project screen](img/a95cb08e76a876d635e62300545ef378.png "figure1")

将其命名为“ **SimpleChannel9Reader** ”，因为我们将从这里下载第九频道 Coding4Fun 部分的 RSS 流:【http://channel9.msdn.com/coding4fun/articles/RSS】T2

## 步骤 2:创建主页的 HTML 和 CSS 基础

打开描述启动应用程序时将显示的第一页的`default.html`文件。而不是下面的 HTML 部分:

```
<p>Content goes here</p>
```

插入这个:

```
<div id="main"> <header id="banner"> <button id="backbutton"> </button> <h1 id="maintitle">Welcome to Channel 9!</h1> </header> <section id="content"> </section> </div>
```

我们现在有了一个全局`div`容器，id 为`main`，嵌入了两个子容器`banner`和`content`。标题将明显显示在页面的顶部，而内容部分将显示在页面的正下方。

让我们通过打开存储在`css`目录中的`default.css`文件来添加一点 CSS。由于**媒体查询**，你会看到已经有一些预定义的 CSS 来处理各种可用的 Windows 8 视图。

在这两篇文章中，我们将只关注“**全屏-横向**”状态。所以跳到这一节，插入下面这段 CSS 代码:

```
#main {
    width: 100%;
    height: 100%;
}

#banner {
    width: 100%;
    height: 100%;
}

#backbutton {
}

#maintitle {
}

#content {
    width: 100%;
    height: 100%;
}
```

这只是表明我们想要为我们的 3 个主容器占用所有的可用空间。

按 F5 键或点击以下按钮运行您的应用程序:

![Buton: Local Machine](img/939a5e0ca3ec5b5848e2cd12e3b0a790.png "figure2")

从逻辑上讲，您现在应该会看到这个屏幕:

![Welcome screen](img/b2e6e11f55532266e5de416d3b2c187a.png "figure3")

而且你也应该看到一个明显的设计问题:后退按钮和标题没有对齐。让我们用 Blend 5 来解决这个问题吧！

## 第三步:第一次接触混合物

启动 Blend 并导航到 SimpleChannel9Reader 项目所在的文件夹。Blend 将显示:

![Blend](img/f5e6df86bb92d741a6994f003c6eb4c1.png "figure4")

这里的目标是创建两个网格。第一个是主容器。它将由一个占据所有可用宽度的列&两行来定义。第二个将由一行两列定义，分别是后退按钮和标题。

让我们从使用“ **Live DOM** ”窗口选择`main`元素开始:

![Main element](img/44935e34ffc728226c02ba9b25834644.png "figure5")

跳转到“ **CSS 属性**部分，选择 **#main** 规则，在“**布局**窗口中，显示切换到“ ***-ms-grid*** ”:

![ms-grid](img/f33796fe33add7de299f45b678f5cee9.png "figure6")

我们将使用目前仅由 IE10 支持的 **CSS 网格布局**规范，但该规范将很快登陆其他浏览器。如果你想了解更多关于 Windows 8 应用模式支持的布局类型，你可以阅读这篇文章:[为你的应用选择 CSS3 布局](http://msdn.microsoft.com/en-us/library/windows/apps/hh465327.aspx)。

如果您只是想了解 CSS3 网格规范，可以随意玩这个 IE 试驾演示:[动手:CSS3 网格布局](http://ie.microsoft.com/testdrive/Graphics/hands-on-css3/hands-on_grid.htm "http://ie.microsoft.com/testdrive/Graphics/hands-on-css3/hands-on_grid.htm")

好了，现在显示已经正确地切换到网格中，我们需要定义我们的网格。为此，跳转到“**网格**部分，并声明以下属性:

![Grid](img/56f2ec6bb9af37d15ca52ef585f3c787.png "figure7")

我们将有一个横跨整个屏幕宽度的独特的列(无论分辨率如何)和 2 行。第一行的高度大小“固定”为 132 像素，另一行将占用剩余的空间。您可以在 Blend designer 图面中看到这一点:

![Blend designer surface](img/8ff38028adbd0571d4c14aa5b8de0d90.png "figure8")

现在，我们将把"**内容**"元素移到第二行。选择它进入“ **Live DOM** ，选择 **#content** 规则，移动到它的“ **Grid** 属性。将“ **-ms-grid-row** ”值改为 2。您也可以将“ **banner** ”元素移动到第 1 行，但默认情况下它会在那里。

我们现在将第一行分成两列，以便将每个元素移动到正确的位置。选择“ **banner** 元素，将其显示属性切换为“-ms-grid”并定义 1fr line &两列 120px 和 1fr:

![Banner element](img/f27248426ad4c3e61c6174e1c643c969.png "figure9")

由于“ **-ms-grid-row-align** ”属性更改为“**居中**”，将“ **maintitle** ”元素移动到第 2 列并垂直居中:

![maintitle element](img/43c676d3fe431557f95e2c9c98472174.png "figure10")

选择**返回按钮**，跳转到**布局**部分。设置一个 **54px** 上边距和一个 **40px** 左边距。如果您没有遗漏什么，您现在应该会在设计图面上看到:

![backbutton ](img/97a0adb5124d90aade7e58193d44915c.png "figure11")

通过*文件*->-*保存所有修改*，返回 Visual Studio。打开`default.css`，你会看到 Blend 已经按照正确的规则生成了一些“干净”的 CSS:

```
@media screen and (-ms-view-state: fullscreen-landscape)
{

    #main {
        width: 100%;
        height: 100%;
        display: -ms-grid;
        -ms-grid-columns: 1fr;
        -ms-grid-rows: 132px 1fr;
    }

    #banner {
        width: 100%;
        height: 100%;
        display: -ms-grid;
        -ms-grid-columns: 120px 1fr;
        -ms-grid-rows: 1fr;
    }

    #backbutton {
        margin-top: 54px;
        margin-left: 40px;
    }

    #maintitle {
        -ms-grid-column: 2;
        -ms-grid-row-align: center;
    }

    #content {
        width: 100%;
        height: 100%;
        -ms-grid-row: 2;
    }
}
```

只需按 F5 键检查应用程序是否工作正常。

## 步骤 4:用 XHR 加载数据，并将它们绑定到 ListView 控件

好了，现在让我们深入研究一下代码。

首先要做的是插入控件，该控件将负责在欢迎屏幕上显示我们文章的缩略图。为此，我们将使用 WinJS。

WinJS 库或“**Microsoft Window Library for JavaScript SDK**”旨在帮助 JavaScript 开发人员以简单的方式实现新的 Windows 8 UI 体验。它提供了一组控件、一个模板引擎、一个绑定引擎、处理异步调用的承诺、处理名称空间的助手等等。

例如，如果你想了解更多关于控件的内容，你可以阅读这篇文章:[快速入门:添加 WinJS 控件和样式](http://msdn.microsoft.com/en-us/library/windows/apps/hh465493)

在 Windows 应用商店项目中，您可以在“**解决方案资源管理器**”的“参考”部分找到此库:

![Library](img/368df0da043ffdec63e10e78f4a626ff.png "figure12")

在里面，你会发现提供了两个*暗* & *亮*主题的默认样式表以及 JavaScript 代码。请随意看一看。通过阅读代码来学习可能会很有趣。

在我们的例子中，我们将使用 ListView 控件创建一个网格布局来显示元素列表。

打开“***【default.html】***”并在 section 标签内，键入这段 HTML:

```
<div id="articlelist" data-win-control="WinJS.UI.ListView"></div>
```

目前只是简单的古典`div`。然而，它用`data-win-control`属性进行了注释，这表明我们希望使用 **WinJS** 库将这个简单的 div 转换成 JavaScript **ListView** 控件。

这个操作的完成要感谢一行神奇的 JavaScript 代码，您可以在`default.js`中找到。这是:

```
WinJS.UI.processAll();
```

这个异步操作将解析 DOM，找到所有标记有`data-win-control`属性的元素，将它们转换成真正的 WinJS 控件，为您实现新的 Windows 8 UI 体验。如果你错误地删除了这条线，你所有的`div`将再次变成一些简单的`div`。

我们现在需要向这个*列表视图*提供一些从 RSS 提要中抓取的数据。在绑定到`onactivated`事件的函数中，将这段代码添加到`processAll()`行的上方:

```
articlesList = new WinJS.Binding.List();
var publicMembers = { ItemList: articlesList };
WinJS.Namespace.define("C9Data", publicMembers);
```

然后你需要在函数的顶部声明`articlesList`变量，就在`app`变量的下面。

我们在这里声明一个**绑定。List()** 类型。它是用于将数据绑定到 WinJS 控件的类型。例如，它包含了一些方法，可以帮助你在后台添加一些数据，这要归功于绑定机制；它会自动反映到视图中。

此外，你可能已经注意到我们使用了一些干净的 JavaScript 代码，例如通过使用现代模式，如“**模块模式**”。为此，我们在`default.js`中有一个匿名的自执行 JS 函数。然后，我们需要找到一种方法向外部函数公开一些公共数据。这就是为什么我们在相关的 WinJS helper 中实现了**名称空间**的概念。这有助于我们轻松地定义我们想要公开的内容。在我们的例子中，我们将有一个公共的`C9Data`对象，它将有一个`ItemList`属性，包含我们要显示的未来元素。

我们现在需要一个函数来从 RSS 提要中提取数据，解析它们，并动态创建一些 JS 对象，将它们放入著名的绑定列表中。这是我的:

```
function downloadC9BlogFeed() {
    WinJS.xhr({ url: "http://channel9.msdn.com/coding4fun/articles/RSS" }).then(function (rss) {

    });
}
```

这个函数首先运行一个异步的 **XmlHttpRequest** 到指定的 URL。定义在**承诺**中的代码(如果你愿意，也可以定义在`.then()`中)只有在请求完成并接收到数据后才会被执行。这时，我们需要通过这段代码过滤数据，这段代码必须插入到匿名函数中:

```
var items = rss.responseXML.querySelectorAll("item");

for (var n = 0; n < items.length; n++) {
    var article = {};
    article.title = items[n].querySelector("title").textContent;
    var thumbs = items[n].querySelectorAll("thumbnail");
    if (thumbs.length > 1) {
        article.thumbnail = thumbs[1].attributes.getNamedItem("url").textContent;
        article.content = items[n].textContent;
        articlesList.push(article);
    }
}
```

我希望这段代码是不言自明的。它选择`item`节点，访问它们感兴趣的属性，将它们映射到一个“文章”对象上动态创建的`title`、`thumbs`、`content`属性。请记住这些属性的名称；我们以后会用到它们。最后，这个函数通过将这个新对象添加到绑定集合来完成。

我们现在需要在应用程序的启动阶段运行这个函数。一旦 DOM 解析完成，就应该运行这段代码来构建 WinJS 控件。为此，请使用这行代码:

```
WinJS.UI.processAll().then(downloadC9BlogFeed);
```

我们必须指定控件的数据源。跳回 HTML 代码，修改与 ListView 关联的 div 以更改其选项:

```
<div id="articlelist" data-win-control="WinJS.UI.ListView" 
 data-win-options="{ itemDataSource: C9Data.ItemList.dataSource }"></div>
```

最后，我们需要一些基本的 CSS 来帮助控件知道如何绘制它的每个项目。跳转到`default.css`文件并添加这两条规则:

```
#articlelist {
    width: 100%;
    height: 100%;
}

#articlelist .win-item {
    width: 150px;
    height: 150px;
}
```

这个 CSS 表明我们的 ListView 控件应该占用其容器的所有可用空间，并且它的每个项目(通过`.win-item`类”)应该占用 150 x 150 像素。

按 F5 运行解决方案。你会有这样丑陋的东西:

![ugly output](img/82c13869de68ae74d49882f09cc9c1dc.png "figure13")

但是不要慌，这种难看的输出是意料之中的行为！我们还有一些设计需要改进。但是您已经可以看到绑定工作正常，控件在触摸和鼠标体验下工作良好。此外，控件会自动缩放至各种分辨率。这样你就不会有和上面屏幕一样的布局(显示的行数和列数)。

## 第五步:使用模板和修改设计混合

我们现在需要改变每个元素的绘制方式。这正是模板引擎的目的。模板只是一段标有 WinJS 属性的 HTML。

导航到`default.html`页面，将这段 HTML 添加到`main`部分的正上方:

```
<div id="C9ItemTemplate" data-win-control="WinJS.Binding.Template" style="display: none;"> <div> <div> <img data-win-bind="src: thumbnail" /> </div> <div data-win-bind="innerText: title"> </div> </div> </div>
```

这是一个标有`WinJS.Binding.Template`值的 HTML 模板。这将有助于 WinJS 知道在执行完`processAll()`之后如何处理这段特殊的 HTML。你还会发现`data-win-bind`定义绑定表达式的用法。它将帮助绑定引擎知道数据源中的哪些 JavaScript 属性要映射到适当的 HTML 节点。除此之外，你可以使用一些经典的 HTML。

我们现在需要配置 WinJS 控件不再使用默认模板，而是使用上面的新模板。只需更改选项即可完成:

```
<div id="articlelist" data-win-control="WinJS.UI.ListView" 
 data-win-options="{ itemDataSource: C9Data.ItemList.dataSource, itemTemplate: C9ItemTemplate }"> 
</div>
```

如果您现在运行该应用程序，您应该看到以下屏幕:

![Welcome Channel 9](img/30a5f6ca226a713e4901b3f6ab8cce85.png "figure14")

好多了，但我们还没完成。为了在设计评审中走得更远，我们需要我们的朋友 Blend 的帮助。

所以，让我们回到 Blend。它会要求你重新加载你在 Visual Studio 中所做的所有修改。完成后，您将拥有:

![Into Blend](img/151c46cacd1d41f8453d7ef8afe50522.png "figure15")

你不惊讶吗？你应该害怕！事实上，我们在这里看到的视觉输出与您在 Visual Studio 中按 F5 时看到的是一样的。这意味着 **Blend 5 直接在设计器内部动态执行应用的 JavaScript** 部分！这是一个很棒的功能。

由于这一点，你将能够直接在真实数据上工作，而不必被迫实施我们所谓的“*嘲弄*”。这是 JavaScript 最酷的部分。Blend 能够执行启动 XHR 请求的 JS 代码并构建 WinJS 对象。

在`default.css`下，我们来添加两个新的 CSS 规则。点击主媒体查询上的“+”按钮:

![media query](img/f337331ec103c90652d9afa635006595.png "figure16")

添加这些新的选择器:

*T1。listitem templateT3*和**。listitem template img****

选择`#articlelist .win-item`规则，该规则将突出显示带有`articlelist` ID 的 ListView 控件的每个元素。

将这些元素的大小从 150 像素乘 150 像素更改为**250 像素乘 250 像素**。你只需要跳转到右边面板的“**尺寸**”部分。

布局应该动态更新。如果没有，您可以通过单击专用按钮来强制刷新设计图面:

![Dedicated button](img/c787b60942ca5dec85b2750c3ef874cb.png "figure17")

下面是您应该得到的结果:

![Result](img/115cbe06612bcd6948229a7695ee90f5.png "figure18")

我们现在要调整模板图像的大小。为此，使用“**选择**指针并点击其中一幅图像:

![select an image](img/f20e5dfbff1331946192190d7af0b9ce.png "figure19")

您可以在“**应用的规则**部分看到当前应用的 CSS 规则。点击`.listItemTemplate img`并用鼠标调整您刚刚选择的图像的大小。与同一选择器匹配的所有其他图像将动态地实时反映这些变化。

与其寻找合适的尺寸，我会帮助你。跳转到**尺寸**部分，确定如下尺寸: **234px 宽**和 **165px 高**。

为了增强我们的设计，我们需要每个元素之间有更多的空间，并使我们的 ListView 控件与标题对齐。

点击`.listItemTemplate`选择器，导航至“**布局**部分，点击“**边距**”区域右侧的“**锁定**图标。选择任意边距并键入 **8px** 。

最后，为了将 ListView 控件的网格与标题对齐，我们需要将它从左边移动我们刚刚设置的元素边距的 120 px–8px。

通过按“+”按钮添加一个新的选择器，并将其命名为`.win-surface`。固定 **112px** 的左边距。

返回 Visual Studio，接受所做的更改，然后按 F5。您现在应该有这样的布局:

![Layout](img/2e05812f29785c1fab73305afb8aadad.png "figure20")

## 步骤 6:下载源代码

到目前为止，我们已经取得了很大进展。我们现在需要显示每篇文章的细节，以继续发现 Blend 的强大功能以及几个很酷的 CSS3 新功能。你可以在这里下载与第一篇文章相关的解决方案:[简单频道 9 读者文章 1](https://david.blob.core.windows.net/win8/SimpleChannel9ReaderArticle1.zip "https://david.blob.core.windows.net/win8/SimpleChannel9ReaderArticle1.zip")

下周第二篇文章中再见！

## 分享这篇文章