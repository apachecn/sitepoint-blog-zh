# 使用 Internet Explorer 9 和 Windows 7 的固定网站

> 原文：<https://www.sitepoint.com/pinned-sites-with-internet-explorer-9-and-windows-7/>

~~Internet Explorer 9 的一个新功能是将网站固定在任务栏、开始菜单或桌面上。用户可以在 [Windows 7](https://clk.atdmt.com/MRT/go/305902202/direct;wi.1;hi.1/01/) 上用 IE9 浏览网站。~~

Windows 7 用户以前可以将应用程序固定在任务栏上，这将这一原理扩展到了网络。这意味着可以添加一层功能，这对网站所有者和网站用户以及开发人员来说都有很大的潜力。

钉住的网站乍一看可能类似于书签或快捷方式，但其中的一些功能达到了一个全新的水平——值得仔细观察。

当然，代表网站所有者的是网络开发者，他们将建立框架来支持和鼓励用户锁定网站。这主要包括向站点页面的`<head></head>`部分添加一些适当配置的代码，但也需要一些有趣的决定。

**在 Internet Explorer 9 中锁定网站**

钉住网站是实现微软[宣称的目标](https://windowsteamblog.com/ie/b/ie/archive/2011/01/07/huffington-post-reinventing-the-big-news-experience-with-ie9-pinned-sites.aspx)的一个重要手段，即把用户喜欢的网络体验放在“最前面和中心”。最小化浏览器 chrome，按照最常浏览的页面排序新标签页，合并地址栏和搜索框——这些都旨在将更多的注意力放在网站内容上，而不是呈现网站内容所需的技术上。

钉住一个网站也是这种心态的一部分。它让站点访问者能够以最高优先级轻松访问他们最想访问的网页，并主动提供有关这些网页的更新信息，以吸引他们返回站点。

固定网站不需要改变现有的内容。它们将在自己的浏览器窗口中打开，非常像定制品牌的桌面应用程序。

正是在选项的实现和配置中，特别是自定义浏览器品牌和网站驱动的用户行为调用等功能，钉住网站提供了网站所有者想要利用的营销潜力。

为在线消费者配置了固定网站的公司已经[报告了](https://news.cnet.com/8301-10805_3-20031845-75.html)流量和用户互动的增加。

让我们通过探索固定站点的一些不同的具体特性来看看开发人员的角色。

**工作原理**

用 IE9 锁定一个站点有三种方法:拖动**图标**(在网站 URL 旁边的地址栏中找到)，在页面上拖动一个特定用途的预先配置的**图像**或者从浏览器窗口中撕下**标签**并将其拖动到一个锁定的位置:在本文中，我们将关注任务栏。在每种情况下，最终锁定的站点的任务栏图标将与收藏夹图标相同:

![Taskbar icon](img/baa5d7ea52826487c87c46156277603e.png)

当一个被钉住的网站被启动时，**后退**和**前进**按钮将呈现出网站图标的主色，并稍微向右移动，为网站图标腾出空间，然后作为被钉住网站的**主页**按钮，创造出一种品牌化的功能体验。

![Customized Home Button](img/dbd4904be4a40a4b61b2a9673b9d326b.png)

你可能会注意到地址栏外面的图标比里面的大。这利用了 favicon 支持不同分辨率的能力。

如果你把你的 favicon 设为 64×64 或 32×32 像素——而不是更标准的 16×16——任何浏览器都会把它缩小以显示在你的地址栏中，但是 IE9 会使用一个 24×24 版本的指定 favicon 更大的 favicon 作为一个固定站点的站点 **Home** 按钮，使这个站点品牌更加占主导地位。

如果您将任务栏配置为使用小图标，您的固定站点图标将以 16×16 显示，否则将显示 32×32 版本。

开发者所要做的就是[创建一个 favicon](https://msdn.microsoft.com/en-us/library/gg491740%28v=VS.85%29.aspx) ，使用他们最喜欢的图像编辑软件或独立的应用程序，并将它上传到网站根目录。我用 Photoshop 的插件来处理。ico 文件。你也可以使用 [X 图标编辑器](https://ie.microsoft.com/testdrive/Browser/IconEditor/Default.html)工具。

如果你的网站还没有使用 favicon，你还需要在你的网站标题中插入一行代码:`<link rel="shortcut icon" href="/filename.ico" />`

**图标**

好了，现在你有了一个漂亮的高分辨率图标，可以拖动它成为一个任务栏图标，提供一个网站的快捷方式。

当您将鼠标悬停在固定在 Windows 7 任务栏上的应用程序图标上时，会显示链接到的程序的缩略图。点击缩略图或图标，该程序将接管主窗口。这也适用于固定站点的图标。

![Taskbar icon thumbnail display](img/6e99ded0d149e455bba7733ed15bbf0e.png)

然而，固定在任务栏上的网站图标提供了更多的功能。

如果锁定的站点有多个打开的选项卡，任务栏图标呈现分层效果，每一层代表一个打开的选项卡。

将鼠标悬停在多层图标上，每个选项卡上的网页缩略图都会显示出来。单击缩略图，转到固定网站的打开选项卡。

![Multiple taskbar icon thumbnails](img/0412a78346ddb7cd9bf8c371ca508959.png)

这不需要开发人员做任何事情，但是插入一些[特征检测代码](https://msdn.microsoft.com/en-us/library/gg491733%28v=VS.85%29.aspx)来检查用户的浏览器是否支持站点锁定是一个很好的做法。

这将完成工作:

```
if (window.external.msIsSiteMode) {

// Check if the website was launched from a pinned site.

if (window.external.msIsSiteMode())

{

// TRUE

}

else

{

// FALSE

}

}
```

我们的下一部分给了开发人员更多的工作。

**跳转列表**

开发者能够创建自定义的 [**跳转列表**](https://msdn.microsoft.com/library/gg491743%28v=VS.85%29.aspx)**，通过右击一个固定站点的任务栏图标即可访问。**跳转列表**允许用户执行操作或访问网站或 web 应用程序的特定区域，而无需首先启动浏览器窗口。**

 **为固定站点自动设置默认跳转列表，如下所示:

![Default jump list](img/8c8906ed515cf602252f71eac76db34e.png)

IE8 中引入了 InPrivate 浏览，通过清除浏览器活动的痕迹来增强用户安全性，尤其是在使用具有公共访问权限的浏览器时。如果你不熟悉这个，你可以在这里阅读一下。

除此之外，默认跳转列表上唯一可用的操作是在被锁定的站点上打开一个浏览器会话，或者从任务栏上取消锁定该站点。

然而，通过一点编码，大量的功能可以添加到跳转列表中。

网站页面 HTML 中的 Meta 元素可以使用以下语法自定义固定站点启动后的呈现方式:

`<meta name="name" content="content"/>`

`name`的值和`content`的准则如下:

如果你没有给你的快捷方式一个名字，页面标题会被使用。

`msapplication-tooltip`:当鼠标指针悬停在锁定的站点快捷方式图标上时，作为工具提示显示的可选文本。

`msapplication-starturl`:应用程序的根 URL。如果缺少，则使用当前页面的地址。只允许 HTTP、HTTPS 或 FTP 协议。

`msapplication-navbutton-color`:固定站点浏览器窗口中后退和前进按钮的颜色。级联样式表(CSS)第 3 级(CSS3)定义的任何命名颜色或十六进制颜色值都是有效的。有关更多信息，请参见颜色表。如果缺少此元元素，颜色将基于快捷方式图标。

`msapplication-window`:固定站点浏览器窗口的初始大小。内容子元素以数字 N 的形式提供大小，用分号分隔。请注意，用户操作会覆盖该值。当用户更改窗口大小时，Windows 会保留用户生成的窗口大小，然后关闭该实例。

下面的代码示例使用 **meta** 元素定制带有工具提示、开始 URL、初始窗口大小和导航按钮颜色的固定站点快捷方式。

```
<meta name="application-name" content="Site Start Page"/>
<meta name="msapplication-tooltip" content="Start here"/>
<meta name="msapplication-starturl" content="https://example.com/start.html"/>
<meta name="msapplication-window" content="width=800;height=600"/>
<name="msapplication-navbutton-color" content="blue"/>
```

元元素还用于定义可以添加到跳转列表中的特定于应用程序的动作。

```
<meta name="msapplication-task" content="name=[name];action-uri=[uri];icon-uri=[filename.ico]"/>
```

一个很好的例子是《赫芬顿邮报》的网站，这是一个在线实体，它费了很大的劲来充分利用网站定位及其为读者提供的服务。《赫芬顿邮报》的基本跳转列表是这样的:

![Huffington Post jump list](img/84657a15427e809a1ce0e22ea5e0a0df.png)

在任务列表中，不同颜色的图标被用来区分读者感兴趣的区域。请注意，附加到大新闻条目的图标对应于任务下列出的网页类别类型。

大新闻列表是一个自定义类别，当项目在网站上更新时，它们会在跳转列表上更新——我们接下来将看看如何做到这一点。

所有这些都有助于为《赫芬顿邮报》的读者制作一份有意义、有用和可操作的菜单，这最终也会让网站所有者受益。IE 博主 Ziad Ismail [将《赫芬顿邮报》](https://clk.atdmt.com/MRT/go/305902206/direct;wi.1;hi.1/01/)描述为一个使用网站定位的新闻服务案例:它绝对值得一读。

**自定义跳转列表类别**

像《赫芬顿邮报》的那些人一样，开发人员可以向跳转列表添加自定义类别，不仅可以提供网站或特定网页的直接链接，还可以向用户提供有关该页面的信息。

为此，我们将调用`msSiteModeCreateJumplist`方法来创建一个带有标签 *Alerts* 的定制类别。

在使用这些步骤中的任何其他方法之前，至少调用一次`msSiteModeCreateJumplist`。

```
window.external.msSiteModeCreateJumplist('Alerts');
```

类别标签不会出现在跳转列表中，直到类别中至少有一个项目，所以下一步是使用`msSiteModeAddJumpListItem`创建列表项目。第一个参数指定项目名称，第二个参数指定选择项目时使用的 URL，第三个参数指定在列表上显示项目时使用的图标。

```
window.external.msSiteModeAddJumpListItem('Item 1', 'https://example.com/Item1.html', 'https://example.cimg/item1.ico');

window.external.msSiteModeAddJumpListItem('Item 2', 'https://example.com/Item2.html', 'https://example.cimg/item2.ico');

window.external.msSiteModeAddJumpListItem('Item 3', 'Item3.html', ‘https://example.com/otherimages/item3.ico');
```

创建了列表及其条目之后，我们现在需要显示列表。使用`msSiteModeShowJumplist`方法，Windows 在跳转列表中显示当前列表项，同时也立即更新内存列表。

```
window.external.msSiteModeShowJumplist();
```

当您调用`msSiteModeClearJumplist`方法时，Windows 会从跳转列表中删除项目。

```
window.external.msSiteModeClearJumplist();
```

另一个很好地使用跳转列表的例子是在亚马逊网站:

![Amazon jump list](img/2fa32cd38d2b0c6669bd936a2f9543e0.png)

这段代码显示，定制的 Amazon Favorites 类别是在页面加载时创建的，因此不需要使用`msSiteModeShowJumpList`方法。

```
<script type='text/javascript'>

try {

window.external.msSiteModeCreateJumplist('Amazon Favorites');

window.external.msSiteModeAddJumpListItem

('Amazon Wish List' ,

'https://www.amazon.com/wishlist?tag=amzn-ie9-jl-wl-20',

'https://www.amazon.com/favicon.ico');

window.external.msSiteModeAddJumpListItem

('Amazon Prime' ,

'https://www.amazon.com/gp/prime?tag=amzn-ie9-jl-prm-20',

'https://www.amazon.com/favicon.ico');

...

} catch (ex) {

}

</script> 
```

请注意，项目的添加顺序与它们在跳转列表中出现的顺序相反。

还要注意的是，任务列表关注的是用户可以做的事情，而定制的 Amazon Favorites 类别列出了用户可以访问的页面。

在自定义类别中，一次最多可以显示 20 个项目。如果您在一个类别中添加的项目超过 20 个，最早添加的项目将被删除。尽管自定义跳转列表类别最多可以包含 20 个项目，但默认情况下，列表中只有最后 10 个项目可见。

您可以更改任务栏的默认设置，但请记住，这只会影响您的显示。其他人可能会继续看到默认。

1.右击任务栏，点击**属性**。

2.点击**开始菜单**标签，点击**定制**按钮。

3.在框底部的**开始菜单尺寸**下，将跳转列表中显示的最近项目的**数量**选项设置为您喜欢的数量。

4.点击**确定**和**确定**。

请注意，用户可以从自定义跳转列表中删除项目，尽管它们可能会在新的会话中恢复，但不能从预设的任务列表中删除。

**制作动态跳转列表**

设置好自定义类别后，我们将通过调用`msSiteModeAddJumpListItem`方法向其中动态添加项目。项目被添加到列表的顶部，顺序与它们在跳转列表中出现的顺序相反。

在这个例子中，我们定制的“Alerts”类别列表中的两个任务是为了响应 onload 事件而设置的。任务 2 在任务 1 之前定义，因此它们在跳转列表中以正确的顺序出现。

```
var g_ext = null;

window.onload = function() {

try {

if (window.external.msIsSiteMode()) {

g_ext = window.external;

g_ext.msSiteModeCreateJumpList("Alerts");

g_ext.msSiteModeAddJumpListItem(

"Second Task","https://example.com/task2"," https://example.com/img/icon2.ico");

g_ext.msSiteModeAddJumpListItem(

"First Task","https://example.com/task1"," https://example.com/img/icon1.ico");

}

}

catch(ex) {

// Fail silently.

}

}
```

跳转列表上的项目即使在您关闭锁定的站点后仍然可见。但是，如果动态项依赖于应用程序状态更改，则在站点关闭后它们将无效。因此，无论应用程序状态如何，跳转列表中的链接都应该包含足够的导航信息。

将我们的前两个任务设置为在 onload 期间加载，对原始页面的更改将不会自动显示。

现在，我们将向自定义列表中添加几个条目，这些条目将根据用户的点击进行更新。我们将保留上例中的全局变量集。

```
function buttonClick() {

if (g_ext) {

g_ext.msSiteModeClearJumpList();

g_ext.msSiteModeCreateJumpList("Alerts");

g_ext.msSiteModeAddJumpListItem("Fourth Task","https://example.com/task4","https://example.com/img/icon4.ico");

g_ext.msSiteModeAddJumpListItem("Third Task","https://example.com/task3","https://example.com/img/icon3.ico");

g_ext.msSiteModeShowJumplist();

}

}
```

还记得我说过用户可以从自定义类别跳转列表中删除项目吗？除了更新跳转列表，`msSiteModeShowJumpList`方法还会为自从上次调用`msSiteModeShowJumpList`方法以来用户移除的每个项目引发一次`onmssitemodejumplistitemremoved`事件。如果您使用`msSiteModeClearJumpList`清除列表，将不会引发此事件。

关于站点锁定还有很多要说的，特别是你可以用任务栏图标做的一些聪明的事情，包括使用覆盖图标通知用户锁定站点上的事件或状态变化，以及添加工具栏命令以允许用户在锁定站点缩略图窗口中访问应用程序功能。

我将把它们留到我的下一篇文章中，以及其他一些关于如何最大限度地利用站点锁定的技巧，以及开发人员在使其最大限度地为站点用户和站点所有者服务中所扮演的角色。

在那之前，微软的 [Pinned Sites 开发者文档](https://clk.atdmt.com/MRT/go/305902208/direct;wi.1;hi.1/01/)是关于站点锁定的关键资源，而 Ziad Ismail 在[探索 IE 博客](https://clk.atdmt.com/MRT/go/305902209/direct;wi.1;hi.1/01/)上的系列也非常有用。

现在，为什么不参加一个简短的[测验](https://www.sitepoint.com/quiz/microsoft/pinned-sites-with-internet-explorer-9-and-windows-7/)来看看到目前为止你对这个话题了解了多少？

**note:**SitePoint Content Partner

本教程是在微软的支持下完成的。我们与微软合作，由 SitePoint 独立编写，努力共同开发对您最有用、最相关的内容。

![](img/349123992a371e5ce388918ddaf875f1.png)** 

## **分享这篇文章**