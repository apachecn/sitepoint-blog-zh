# eZ publish 3.1:尝试一下

> 原文：<https://www.sitepoint.com/ez-publish-3-1-take-spin/>

**你已经听说过 [eZ publish 3.1](http://www.ez.no/) ，这是 eZ Systems 的开源内容管理系统(CMS)的最新版本。您还听说了它与 2.2 版有很大不同。有多不同？在本教程中我们会看到。**

让我们一步一步地使用 3.1 版本来构建一个站点，这样您就可以快速体验到新版本的强大功能。

##### 为什么 eZ publish？

如果你的客户想创建一个网站来帮助他们建立一个社区，包括新闻、论坛、邮件列表、时事头条和网上商店，那么你应该看看 eZ publish。点击查看一些由 eZ publish [支持的网站。eZ publish 允许您的客户维护网站内容，让您可以为他们开发更酷、更强大的功能。](http://www.ez.no/developer/reference_list/selected)

***eZ 发布的优势***

*   所有编辑和内容功能都是基于网络的。因此，分布式组可以处理网站的内容，而不必同步文件或进行协调。
*   复杂的基于时间的发布和收回，无需管理员干预。一些 CMS 产品会这样做，但只是在发布时。
*   基于 PHP 的开源软件——你可以随意修改程序——这既危险又强大。
*   支持内容和布局采用多种语言和格式的国际网站。
*   有很大的发展空间——它拥有完整 CMS 的所有功能——因此很难想象 eZ publish 的发展会超过它。
*   一个强大的面向对象的模板驱动的渲染系统可以让你轻松地按照你想要的方式定制你的站点。

***eZ 发布的弊端***

*   现实世界中的设置很复杂。除非你有网络服务器的根级访问权限，否则很难让主机提供商为你设置 eZ publish。一个例外是 Marlabs T1，他们以托管 eZ publish 为业——注意他们同时支持 2.2 和 3.1 版本。
*   定制你的站点需要一段时间，尤其是如果你不是 PHP 高手的话。
*   文档非常有限，尤其是 3.1 版。
*   相当陡峭的学习曲线。如果你不需要 eZ publish 的多用户、基于网络的编辑和管理，我强烈推荐 Fog Creek Software 的商业产品 [CityDesk](http://www.fogcreek.com/CityDesk/index.html) 。有一个免费的介绍版，允许你开发小型网站，更强大的软件版本价格适中。

##### 2.2 或 3.1:你的毒药是什么？

问题不是要不要选择 eZ publish，而是你会选择哪个版本:可信的 2.2 版，还是花哨的新版本 3.1？这是一个关键的选择，将影响你的网站多年。不幸的是，目前没有办法将你的 2.2 版网站迁移到 3.1 版。

***试过了&真实—2.2 版***

[Harry Fuecks 的文章， *eZ publish: PHP 的黑仔应用程序*](https://www.sitepoint.com/blog/) 对该产品的 2.2 版本进行了出色的介绍，它提供了以下好处:

*   它的功能是“开箱即用”
*   它有一个漂亮的默认布局，适合文章和标题、论坛、邮件列表、用户管理，甚至在线投票模块。
*   如果您不喜欢某样东西，编写自己的模块来增加功能是一个非常简单的过程。
*   它得到了用户社区的良好支持，文档也更加成熟。

***强大的&复杂—3.1 版本***

3.1 版本是最近才出来的，和 2.2 版本差别很大。事实上，就快速建立你的网站而言，它几乎是倒退了一步。eZ publish 3.1:

*   没有为普通用户提供一套通用的模板。
*   侧重于内容管理系统需要的基本框架，而不是“开箱即用”的功能。这里没有内置的在线投票。但是如果你想的话，你可以创建一个强大的。
*   基于出色的基于对象的呈现模型，支持灵活、动态的站点。

从长远来看，3.1 版本带来了重要的改进，这将使它成为功能全面、基于内容的网站的重要基础。不幸的是，由于几乎没有让 2.2 如此吸引人的“开箱即用”功能，它在短期内很难销售。但是如果你正在计划一个大型的、复杂的站点(或者你希望成为这样的站点)，3.1 版本有能力和灵活性来帮助你的站点在未来几年发展。

如果你想深入比较 eZ publish 2 和 3，可以看看 Sebastiaan van de Vliet 的文章*eZ publish Evaluation:Version 2 vs Version 3*。如果你想判断 eZ publish 3.1 是否准备好了，请继续阅读。

##### 建设网站

让我们开始吧。我假设你和我一样，对 PHP 了解不多，但是你不怕从头开始编码，也不怕修改现有的代码。我还假设您将在自己的计算机(自托管)上，在 Windows NT/2000/XP 下尝试 eZ publish，因为这是我使用的系统。如果你运行的是 95/98/Me，过程是类似的。

我还假设您的机器上没有运行任何其他 Web 服务器，比如 IIS。如果是，您应该禁用它。如果你不确定，打开浏览器并访问 http://localhost——如果你得到一个网络错误，那么你没有运行本地 Web 服务器。

令人欣慰的是，eZ Systems 的人员提供了一个安装程序，可以自动完成与 eZ publish 和支持它的各种开源技术(包括 Apache、MySQL 和 PHP)相关的设置和安装过程。这是天赐良机，你可以很快上手。如果你把事情搞砸了，你可以轻松快捷地卸载并重新安装产品。

还要注意，我已经为您提供了一个代码档案库[，其中包含了我们将在本教程中用到的所有文件。](https://www.sitepoint.com/examples/ezp3/ezp3.1samples.zip)

***为本地托管安装 eZ publish 3.1***

1.  进入 [eZ publish 3](http://ez.no/developer/download) 发布页面。点击“eZ publish 3.1 installers”下的“Windows 2000/NT/XP Installer”并下载。EXE 就在那里。注意是 20+ mg。

*   运行您下载的 EXE 文件。将运行一个相当标准的设置，带有相当标准的问题。这个安装程序将安装 Apache Web 服务器、PHP 4 和 eZ publish。*   打开 Web 浏览器，输入链接 http://localhost/index.php，您应该会看到 eZ publish 生成的默认演示站点。*   如果您希望 Apache 自动提供 eZ publish 站点的根目录，而不包含“/index.php”部分，请打开“C:Program FileseZ systemapacheconf”目录下的“httpd.conf”文件(假设您接受默认安装目录)。现在将“目录索引”行条目改为“目录索引 index.html 索引”。最后，停止并启动“Apache”服务，或者重启系统。*   eZ publish 现已启动并运行。您可以通过访问 http://localhost/index.php 来访问该页面，或者通过 http://localhost/index . PHP/admin 访问管理页面。管理用户是“admin”，密码是“publish”(无引号)。

本质上，每个浏览器都被支持，因为系统直接生成动态生成的 HTML。

查看内容方面的示例(http://localhost/index.php)，您会很快了解这个工具有多强大。有一个漂亮的三栏新闻标题页面(注意，“新闻”、“公司”、“内部网”本质上是一样的)、网上商店、画廊和论坛部分。如果您不喜欢示例站点的布局、组织或颜色选择，不要惊慌，您几乎可以更改所有内容。

我不打算在组织内容上花太多时间；相反，我们将专注于使网站看起来像你想要的样子。查看 eZ publish [用户指南](http://ez.no/manual)了解如何添加、修改和删除内容的详细信息。

##### 快速目录浏览

理解 eZ publish 的目录结构很重要。在安装 eZ publish 的目录下(通常是“C:Program FileseZ systemezpublish”)，您会发现许多目录。你将要工作的是“设计”和“设置”——远离其他的。

“设计”目录是最重要的——你的大部分工作都在这里进行。此目录包含指定网站外观的文件。

“设置”目录包含控制 eZ publish 行为的文件。你偶尔会去这个地区。

##### 关闭缓存

eZ publish 动态生成页面。换句话说，当您请求一个页面时，它会执行一系列数据库查询，并在访问者请求时生成 HTML 网页。这是 eZ publish 网页需要更长时间加载的原因之一——系统需要想出向你显示什么。为了加快速度，eZ 公布了它最近制作的“缓存”页面，以防其他人需要它们。毕竟一遍又一遍的动态渲染同一个首页没有太大意义。本质上，eZ publish 对它生成的页面进行快照，当有人下一次请求时，它就发送快照。

通常情况下，这样做很好——网站真的不会很快改变，缓存大大提高了性能。然而，当你建立网站的时候，你将会改变很多。所以最好禁用缓存，直到你对你的站点满意为止。

***关闭视图缓存***

1.  转到 eZ publish 的安装目录。通常，如果使用默认安装，这就是“C:Program FileseZ systems”。在“ezpublishsettings”子目录中查找一个名为“site.ini”的文件以供将来参考，在本文中，我将给出该文件的路径为“[Install]ezpublishsettings site . ini”

*   在文本编辑器中打开该文件，搜索条目“ViewCaching=enabled”，将其更改为“ViewCaching=disabled”(没有引号)。*   验证缓存是否关闭。请访问 http://localhost/index . PHP/admin 上的管理界面。以用户“admin”的身份登录，密码为“publish”(无引号)。单击管理界面顶部的“设置”选项卡，然后单击左侧的“缓存”。您应该会看到“视图缓存被禁用”在“内容视图缓存”下

##### 网站设计

为了定制网站的外观，您需要提供生成 HTML 的代码。eZ publish 称之为“网站设计”。网站设计就像是内容的“窗口”。想象一下从窗户望出去是一片山景。现在，把你心目中的窗框改成方形窗格，然后改成菱形窗格。场景保持不变，但前景发生了变化。你可以用 eZ publish 做同样的事情。

***通过不同的站点设计检查相同的内容***

1.  确保您以管理员身份登录。进入 http://localhost/index . PHP/admin 的 admin 界面，以用户“admin”密码“publish”(无引号)登录，点击登录。有些网站设计要求你以管理员身份登录，这就是为什么这一步很重要。

*   转到演示站点的开始页面，网址为 http://localhost/index . PHP/demo/content/view/full/24/。你现在正在通过“演示”网站设计查看内容。*   接下来，我们通过“标准”的 sitedesign 来看一下:http://localhost/index . PHP/standard/content/view/full/24/。突然间，一切都不同了。同样的内容出现了，但是所有的粉饰都没有了。此外，还出现了许多控件。您必须登录的唯一原因是标准站点设计要求您这样做。*   现在，浏览一下“用户”sitedesign(也是非常基础的):http://localhost/index . PHP/user/content/view/full/24/。

站点设计是通过 URL 选择的，位于“index.php”之后的项目中。我们不会改变现有的站点设计，而是创建我们自己的站点设计，名为“mydesign”。

***创建新的站点设计***

1.  首先，在创建 mydesign 站点设计之前，检查一下当您尝试访问它时会发生什么——请访问 http://localhost/index . PHP/mydesign。您将看到您最终到达登录界面(如果您没有登录)。这是 eZ publish 对不良网址的标准回答。

*   在“[Install]ezpublishsettingssiteaccess”目录中，您会看到每个不同站点设计的文件夹。创建一个新的名为“我的设计”。*   在“mydesign”目录中创建一个名为“site.ini.append”的文件，并插入以下内容:

```
#?ini charset="iso-8859-1"?   

# eZ publish configuration override file.   

#   

[SiteAccessSettings]   

RequireUserLogin=false   

[DesignSettings]   

SiteDesign=mydesign
```

当您使用“my design”site design 时，此文件将覆盖“[Install]ezpublishsettingssite . ini”文件。您已经告诉 eZ publish，您不希望用户必须登录，并且您将在“mydesign”目录中指定一个 sitedesign。

*   保存并关闭文件。*   返回并尝试访问 mydesign 站点 design。请访问 http://localhost/index . PHP/my design。你会看到现在你可以看到的内容，看起来可疑地像标准的网站设计。如果您不提供设计，默认情况下会替换标准的 sitedesign。

现在我们已经有了一个网站设计，让我们改变一些事情，使它看起来像我们想要的那样。为此，我们需要创建和修改模板。

##### 页面布局模板

模板定义了通过网站设计查看内容时的外观。eZ publish 的一半麻烦在于计算出要修改哪个模板，以便完成您的特定设计目标。我从未找到这些模板的完整列表。然而，我正试图编译一个，如果有人有，我很乐意看到它。

坦率地说，我不确定“覆盖”一个模板和简单地在您的目录中创建一个模板之间有什么区别。到目前为止，我已经通过覆盖完成了我想要的一切。如果我找到了，我会告诉你的。

现在我们将为 mydesign 重写“pagelayout.tpl ”,它控制基本的页面布局和设计。为此，我们需要做三件事:

1.  将“mydesign”添加到活动站点设计列表中，

*   告诉 eZ publish 复制默认模板，然后*   根据我们的新设计修改它。

我们还将修改第二个模板来完成我们的工作。

***将“my design”site design 添加到活动 Sitedesigns 列表中***

注意:您只需要在第一次创建 sitedesign 时这样做。您不需要在每次想要覆盖模板时都这样做。

1.  打开“[Install]ezpublishsettingssite . ini”文件，并查找一系列条目，内容如下:“AvailableSiteAccessList[]=demo”为您的新“my design”site design 添加一行，“AvailableSiteAccessList[]= my design”。保存并关闭文件。

*   转到“[Install]ezpublishdesign”目录，创建一个名为“mydesign”的目录。进入该目录，并创建一个名为“override”的子目录。进入该目录，并创建一个名为“templates”的子目录。完成后，您将拥有一个“[Install]ezpublishdesignmydesignoverride templates”目录。

***告诉 eZ publish 复制默认的 pagelayout.tpl 模板***

1.  进入管理界面:http://localhost/index . PHP/admin 以用户“admin”密码“publish”(无引号)登录(如果需要)。单击管理界面顶部的“设置”选项卡，然后单击左侧的“模板”选项卡。点击“最常用模板”下的“pagelayout.tpl”

*   在下一页上，从下拉列表中选择“我的设计”，然后单击“新建”。*   在下一页上，在文本框中输入“pagelayout”，选择“默认模板的副本”，然后单击“创建”。*   如果看到错误，很可能是模板的目录不存在。检查以确保您创建的“[Install]ezpublishdesignmydesignoverride templates”目录存在。查看您的新模板，它位于"[Install]ezpublishdesignmydesignoverridetemplatespagelayout . TPL "快速浏览一下，您可以看到它是 HTML 和模板命令的混合，用花括号{}括起来。

现在，让我们坦率地说。eZ publish 希望你创建漂亮的、模块化的布局，让所有的东西以一种逻辑的方式整齐地组合在一起。

不幸的是，当需要更新设计时，将您的设计分散在半打模板中会很烦人。我承认，那些试图创建许多具有可重用元素的站点的人可以很好地利用 eZ publish 的这一特性——但是对于那些想要创建相当简单的站点的人来说，这太难了。

因此，我建议，对于简单的网站，你应该把几乎所有的元素都放到 pagelayout.tpl 中，忽略子模板。它不仅使普通网站管理员的生活更加轻松，而且将每个页面的模板数量从 8 个减少到 1 个只能提高 eZ publish 的渲染性能。

说到这里，让我们修改 pagelayout.tpl 模板，使用一个简单的布局创建我们的页面，顶部有一个横幅，左侧有一个菜单，还有一些内容。我们将使用演示内容，只是为了让事情继续下去。

***修改 pagelayout.tpl 到我们的新设计***

您可以下载它，而不是将整个文件放入文章的正文中。从[代码档案](https://www.sitepoint.com/examples/ezp3/ezp3.1samples.zip)中复制文件“pagelayout.tpl ”,并用它替换“[Install]ezpublishdesignmydesignoverride templates”中的文件。现在，

1.  转到“[Install]ezpublishdesignmydesign”目录，创建一个名为“image”的目录。这是 eZ publish 期望找到您的。gif，。bmp 还有。jpg 图像文件。

*   将文件“mytopline.gif”从[代码归档文件](https://www.sitepoint.com/examples/ezp3/ezp3.1samples.zip)复制到“[Install]ezpublishdesignmydesignimage”——如果您想替换您自己的 gif 文件，请使用它——只需将其命名为“mytopline.gif”。

关于我的 pagelayout.tpl 的几点意见:

*   我试着把模板的东西减到最少，所以它大部分是简单的 HTML，有一些变化。
*   它实际上是 XHTML——这就是`<br />`的内容。
*   您会注意到顶部横幅的 img 标签不是标准的 HTML: `<img src={"mytopline.gif"|ezimage} />`奇怪的是 src 属性使用的引用。它被括在花括号中，这使得模板管理器检查它，并替换指令的结果。在本例中，我们使用 ezimage 模板操作符，它查找并返回“mytopline.gif”图像的位置。您也可以使用它的显式(但相对)路径:“/design/my desiimg/my topline . gif”——它在功能上是等效的。
*   我已经在左边的导航项目中放置了指向演示安装中的文件夹的明确链接。从技术上来说，这并不理想，但是，如果你试图使用你的目录结构来自动驱动布局，你总是会发现有你需要一个真正的网站，你不想显示在一个目录树的项目。真正的网站作者倾向于最终使用这种方法，使它看起来完全像他们想要的样子。
*   有对样式表的引用，但是我还没有定义一个(还没有)——我们会到的。
*   什么是`{$module_result.content}`？这将在主显示中呈现内容，我们还没有对其进行定制。但是我们一会儿就会知道。

当你现在访问 http://localhost/index . PHP/mydesign 的时候，你会看到大部分页面布局都是由 pagelayout.tpl 文件的内容驱动的。但是，内容的显示仍然使用标准设计。我们还没有定制用于查看各种对象的模板，所以当您查看内容时，您将获得默认视图。

##### 渲染对象

eZ publish 中的所有内容都包含在对象中。对象是文件夹、文章、文章、论坛、照片等东西。当我们显示一个对象时，比如一篇文章，eZ publish 会将其呈现为 HTML。它查看数据，如出版日期、摘要文本、文章文本等。，然后对它应用一个模板，使它以我们想要的方式出现。这里，我们将呈现两种类型的对象——一个文件夹和一篇文章——以提供一些基本功能。在一个成熟的网站中，你可能会渲染很多不同的对象。

请访问 http://localhost/index . PHP/my design/content/view/full/24。你会看到列出了两篇文章——“山里的周末”和“精神食粮”。这是一个非常基本的展示。在面向对象的世界中，每个对象负责将其自身呈现为 HTML。本质上，当文件夹开始列出它的内容时，它要求其中的对象显示它们自己。在这种情况下，他们被要求以“行”格式呈现自己，即每个对象都有多个简短的摘要行，单击这些摘要行可以查看完整版本。因此，我们需要做的是覆盖标准模板，这些模板控制着我们的站点设计中文章的行显示。

***覆盖文章对象线条模板***

1.  转到位于 http://localhost/index . PHP/admin 的管理界面，以用户“admin”的身份登录(如有必要)，密码为“publish”(无引号)。单击管理界面顶部的“设置”选项卡，然后单击左侧的“模板”。点击“最常用模板”下的“/node/view/line.tpl”

*   从下拉列表中选择“我的设计”，然后单击“新建”。*   将模板名称设置为“line_article”(这个名称没有任何魔力)，选择“article”类，保留“Any”部分，并将节点留空。选择“默认模板的副本”并单击“创建”。*   请在“[Install]ezpublishdesignmydesignoverridetemplatesline _ article . TPL”查看您的新模板，这里的代码只够呈现一篇文章的一行内容。从
    [代码档案](https://www.sitepoint.com/examples/ezp3/ezp3.1samples.zip)中复制模板文件“line_article.tpl”，替换“[Install]ezpublishdesignmydesignoverride templates”中的模板文件。*   再次访问 http://localhost/index . PHP/my design/content/view/full/24。正如你将看到的，这些文章被渲染得更加令人愉快，还有一个摘要和文章的一个小缩略图。

我们可以为主要文章的渲染重复这个过程(称为“完整”格式)。如果你点击“山里的一个周末”，你会看到这篇文章的糟糕呈现。您可以覆盖文章的完整模板，方法是重复文章对象行模板的步骤(如上)，根据需要更改几个模板:选择“/node/view/full.tpl”模板，并将模板名称设置为“article”。

您的新模板将显示为“[Install]ezpublishdesignmydesignoverridetemplatesarticle . TPL”。最后，下载模板文件://article.tpl，替换“[Install]ezpublishdesignmydesignoverride templates”中的模板。

刷新一下《山里的一个周末》的故事，你会发现好看多了。如果你访问各种文章，你会看到他们都使用这个新的模板。如果你访问“新闻”下的“体育”文件夹，可以看到一个更有趣的效果——http://localhost/index . PHP/my design/content/view/full/25。在那里，文章“我们又做了”没有缩略图。

然而，即使我们的模板明确地期望缩略图，系统也不会给我们带来任何麻烦。为什么不呢？因为 eZ publish 会自动删除与缩略图相关的代码——甚至没有残留的`<table>`标签！

我让你来决定如何渲染文件夹。作为提示，请记住您需要覆盖 folder 类的 line.tpl 和 full.tpl。你可以从 eZ 发布网站的[教程中了解更多。或者，如果你想，偷我的模板。在](http://ez.no/sdk/tutorials/view/templateissues)[代码档案](https://www.sitepoint.com/examples/ezp3/ezp3.1samples.zip)中，你会找到文件:file://line_folder.tpl 和 file://folder.tpl

##### 本地化您的站点

如果你在美国，你会注意到当你访问一篇文章时，比如 http://localhost/index . PHP/my design/content/view/full/62/“山里的一个周末”，日期会列出这个月的前一天，欧洲和英国风格。eZ publish 对多语言站点有详细的支持，这方面的讨论超出了本文的范围。

默认情况下，eZ publish 安装选择英国英语作为本地化设置的默认设置。要更改这一点，您需要更改 site.ini 文件，告诉它哪些本地化设置是合适的:

***设置默认语言***

1.  在文本编辑器中打开“[Install]ezpublishsettingssite . ini”，在“[RegionalSettings]”部分中搜索“Locale=eng-GB”条目。将其更改为“Locale=eng-US”(无引号)，然后保存并关闭它。

*   访问 http://localhost/index . PHP/my design/content/view/full/62/《山里的一个周末》。嘿——它没变！原来 eZ publish 有个 bug。他们把日期的美国本地化值搞错了。不过，这很容易解决…*   在文本编辑器中打开“[Install]ezpublishshareloaleeng-us . ini”，搜索一个显示为“ShortDateFormat=%d/%m/%Y”的条目。将此更改为“shortDateFormat = % m/% d/% Y”—您可能希望在这里对 date format、DateTimeFormat 和 ShortDateTimeFormat 进行类似的更改。*   访问 http://localhost/index . PHP/my design/content/view/full/62/《山里的一个周末》。现在这个日期以美国人期望的方式出现了。

##### 但是，我从哪里开始呢？

定制东西对我们来说很好，但我真的不希望访问者在访问我的网站时必须从根目录开始，你和我都不希望他们知道我们希望他们使用“我的设计”。我们可能希望他们能够输入“http://localhost/”并保持原样！这是个合理的要求。

幸运的是，我们可以很容易地在 eZ publish 中设置默认的站点设计和起始页。

***设置默认启动页面和站点设计***

1.  如果还没有，请回到本文的第一部分，修改 apache 设置，允许您跳过 URL 的“index.php”部分。

*   请访问 http://localhost/。您应该可以在“Frontpage”目录中看到演示站点设计。*   在文本编辑器中打开“[Install]ezpublishsettingssite . ini”，搜索“DefaultAccess=demo”条目——将其更改为“DefaultAccess=mydesign”(无引号)，保存并关闭它。*   请访问 http://localhost/。您应该看到 mydesign 站点设计，但是您会注意到它仍然指向“Frontpage”目录。*   打开“[Install]ezpublishsettingsmydesignsite . ini . append”，这是我们之前在文本编辑器中创建的。请记住，当我们使用“my design”site design 时，此文件会覆盖“[Install]ezpublishsettingssite . ini”文件。将以下内容添加到文件中:

```
[SiteSettings]    

IndexPage=/content/view/full/17
```

*   保存并关闭文件。您刚刚告诉 eZ publish，您希望它默认为 object #17(新闻目录)的完整视图。

请访问 http://localhost/。您应该看到 mydesign 站点设计，它指向“新闻”目录。

##### 样式表

之前，您可能已经注意到，虽然我们在布局模板中引用了样式表，但是我们从来没有真正定义过样式表。这给了我们的网站一个明显“平淡无奇”的外观和感觉。

您可以在整个网站中插入格式属性，但这使得维护成为一场噩梦。例如，假设您想在整个站点中使用 Web color # 99FFCC，但是在将它放入全局模板的 365 个不同的 bgcolor 属性中后，您会发现这是一个非常难看的颜色。你得回去把它们都修好！当你有全局搜索和替换时，这不是一件大事，但如果你想玩不同的选项，如果能在一个地方改变颜色设置，并让所有 365 个属性都改变，那就太好了。

输入:样式表。有很多宣传，但是样式表真正让你做的是命名一组特征，比如大小、对齐、颜色、字体等等。，并使用“class”属性在 HTML 中引用该名称。当你开发和维护你的站点时，这是一个很好的捷径。有很多地方可以让你学到更多关于层叠样式表(CSS)的知识，所以我就不再赘述了。访问 [SitePoint 的 CSS 类别](https://www.sitepoint.com/subcat/90)和 [HTML 帮助](http://www.htmlhelp.com/reference/css/)以获得关于 CSS 的优秀教程。在这里，我将向你展示如何将样式表附加到你的站点设计中。

***将样式表附加到你的站点设计***

1.  如果你只需要一个样式表(对于简单的网站，这可能就是你所需要的)，你可以在 pagelayout.tpl 文件中引用它。

*   转到“[Install]ezpublishdesignmydesign”目录，创建一个名为“样式表”的目录。这不是绝对必要的，但它确实让事情更有条理。*   在文本编辑器中打开“[Install]ezpublishdesignmydesignoverridetemplatespagelayout . TPL”，在`<head>`标签下添加以下 HTML。

```
<link rel="stylesheet" type="text/css" href={"stylesheets/mydesign.css"|ezdesign} />
```

*   下载样式表文件://mydesign.css 放入“[Install]ezpublishdesignmydesignoverridestylesheetsmydesign . CSS”

请访问 http://localhost/。您应该会看到样式表的效果。

##### 部分

房间里有一只大象，我们还没有谈到它。在 eZ publish 界面中，您可能会多次听到提到“sections”当你使用 eZ publish 进行开发时，这是一个需要理解的重要概念，尽管在你的第一个 eZ publish 站点中，你可能不会使用它们。

节将您的站点划分为不同的内容“区域”,这些区域具有不同于其他节的外观和行为。例如，演示站点中的“新闻”和“公司”区域是不同的部分，它们看起来彼此不同——不同的配色方案、横幅图形、页面布局等。内容可以分配给任何部分。不幸的是，将内容分配给各个部分的用户界面是晦涩难懂的。

***将内容分配给一个章节***

1.  我们将把“我的公司”重新分配到“新闻”部分，看看在演示站点上的效果(通过演示站点设计查看)。

*   请访问 http://localhost/index . PHP/demo/content/view/full/33/。这是“我的公司”部分，看起来与“新闻”部分不同。这是因为文件夹“我的公司”中的内容被分配到第 6 部分(我的公司)，而不是第 3 部分(新闻)。*   请访问 http://localhost/index . PHP/admin 上的管理界面。如果需要，以用户“admin”密码“publish”登录(无引号)。单击管理界面顶部的“设置”选项卡，然后单击左侧的“部分”。点击“新闻部分”右侧的回形针(分配)*   选择“我的公司”单选按钮，然后单击选择。这将“我的公司”目录分配给“新闻”部分。新闻模板现在将应用于“我的公司”的内容。所有子项目也会被重新指定。*   再次访问 http://localhost/index . PHP/demo/content/view/full/33/您将看到“新闻”部分模板已应用于“我的公司”文件夹的内容。这有点荒谬，因为新闻模板包含对“新闻”项目的硬编码引用。

请注意，此过程不会移动网站中的内容，这是由内容在文件夹中的位置决定的。它只是影响使用哪些模板。

嗯，我不知道你，但对于大多数我有兴趣创建的网站，我真的只需要一个单独的部分。构建包含多个部分的站点超出了本文的范围。

##### 把一切都包起来

有很多东西要出版。在这里，我们仅仅触及了表面——我们只关注了一个领域，那就是创建基于内容的网站，让它们看起来像你想要的那样。eZ publish 3 比 eZ publish 2.2 灵活和强大得多，但使用起来要困难得多，尤其是因为没有任何有用的默认模板来帮助您入门。

希望这篇介绍能帮助你创建令人敬畏的 eZ publish 3.1 网站！

## 分享这篇文章