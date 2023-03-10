# 十月 CMS 静态页面入门

> 原文：<https://www.sitepoint.com/getting-started-with-october-cms-static-pages/>

*本文是与[十月 CMS](https://octobercms.com/?utm_source=sitepoint&utm_medium=content&utm_campaign=staticpages) 合作创作的。感谢您对使 SitePoint 成为可能的合作伙伴的支持。希望与 SitePoint 合作？在这里获得[更多信息。](https://www.sitepoint.com/advertise/)*

如今，对网站开发者来说可能很难，尤其是现在 WordPress 正在经历其历史上最大和最戏剧性的更新。在过去的几个月里，我们发现人们对十月 CMS 社区越来越感兴趣。

十月 CMS 是寻找基于 Laravel PHP 框架的现代可靠内容管理系统的开发人员的自然选择。today October CMS 于 2014 年推出，是一个拥有庞大生态系统的成熟平台。十月 CMS 以其可靠性和不间断更新而闻名，受到开发者及其客户的极大赞赏。“返璞归真”的哲学对于自由职业者和数字工作室来说非常重要，他们的业务依赖于他们使用的软件的质量。快速发展的[市场](https://octobercms.com/plugins?utm_source=sitepoint&utm_medium=content&utm_campaign=staticpages)和大约在十月份建成的[配套商业](https://1pilot.io/)是社区信任的最好证明。2018 年 10 月 CMS 还在 [CMS 评论家奖大赛](https://octobercms.com/blog/post/october-cms-voted-best-flat-file-cms-2018?utm_source=sitepoint&utm_medium=content&utm_campaign=staticpages)中被评为最佳平面文件 CMS。

十月 CMS 有一个简单的可扩展核心。核心提供基本的 CMS 功能，并且可以通过市场上的插件添加许多功能。插件市场包括许多产品类别，其中包括博客、电子商务、联系方式等等。

在本教程中，我们将演示如何创建一个网页可编辑的 WYSIWYG(所见即所得)模式的网站，以及博客功能。我们将使用[静态页面插件](https://octobercms.com/plugin/rainlab-pages?utm_source=sitepoint&utm_medium=content&utm_campaign=staticpages)，它允许你创建一个动态网站结构，页面和菜单可由非技术终端用户管理。同时，网站将包括动态博客页面，内容由[博客插件](https://octobercms.com/plugin/rainlab-blog?utm_source=sitepoint&utm_medium=content&utm_campaign=staticpages)管理。本教程中使用的所有插件都是免费的，任何人都可以安装。本教程的思想可以扩展到创建更复杂的网站。实现类似功能的主题可以在[主题市场](https://octobercms.com/themes?utm_source=sitepoint&utm_medium=content&utm_campaign=staticpages)上找到，但本教程的目标是展示从头开始制作一个十月网站是多么容易。

这是我们最终将会得到的:

![](img/f625973ae72fc44172b2c0f88a6204d4.png)

网站主题完全基于 Twitter Bootstrap 4.3。演示网站还包括一个简单的 CSS 文件，它管理一些灯光样式特性，如填充和颜色，但由于它与本教程的主题无关，所以在本指南中不做描述。

### 安装十月 CMS

要安装 10 月 CMS，您必须有一台满足最低要求的网络服务器。你可以使用 [MAMP](https://www.mamp.info/en/) 作为解决方案，在你的电脑上尝试十月。

有几种方法可以安装十月 CMS。我们将使用向导安装程序选项，它简单且用户友好。安装过程在[文档](https://octobercms.com/docs/setup/installation#wizard-installation?utm_source=sitepoint&utm_medium=content&utm_campaign=staticpages)中解释。创建一个新的 MySQL 数据库，[下载安装程序](https://octobercms.com/download?utm_source=sitepoint&utm_medium=content&utm_campaign=staticpages)，将其解压到你为十月份准备的目录下，并在浏览器中打开`install.php`脚本。

![](img/7712929e4169fd0d9062bfd6cbe542df.png)

安装过程相当简单，显示了输入数据库细节和设置管理员凭证的标准提示。在最后一步，单击从头开始。这将创建一个带有简单演示主题的空安装。

![](img/d67c051dcd070d7b39c6b85e4a8b522f.png)

安装完成后，点击**管理区域**链接，使用您在安装过程中提供的凭证登录到十月 CMS 后端。

### 安装静态页面和博客插件

在这一步，我们将安装我们的小项目所需的两个插件。在十月 CMS 管理区，导航到**设置**页面，点击侧边栏中的**更新&插件**链接。在搜索栏中输入“静态页面”，当插件列表加载完毕后，点击**静态页面**插件。

![](img/3b896dc5b5850391ba548aa5bef98796.png)

这将安装静态页面插件。重复安装博客插件的过程。

![](img/0f6138ac9b54b78176f2efa141a4aa16.png)

如果一切正常，您应该会看到安装了两个插件:

![](img/291bd9b732229fd3a4342652cbb33a93.png)

### 创建新主题

我们想从一个空白的主题开始我们的教程。要创建一个新主题，打开十月 CMS 管理区的**设置**页面，点击侧边栏的**前端**主题项。点击**创建新的空白主题**链接。输入新的主题名称，例如，“我的博客主题”。点击**创建**。

![](img/b7d5336071cafabe30456084bd81555a.png)

### 创建布局

在 10 月 CMS 中，[布局](https://octobercms.com/docs/cms/layouts?utm_source=sitepoint&utm_medium=content&utm_campaign=staticpages)定义了页面框架——通常，它们包括 HTML、HEAD、TITLE 和 BODY 标签。几乎每个 CMS 页面都有一个分配给它的布局。最简单的功能布局代码如下所示:

```
<html>
    <body>
        {% page %}
    </body>
</html> 
```

请注意`{% page %}`标记——当十月 CMS 请求并呈现页面时，它会被页面内容替换。

对于我们的教程，布局将定义 Twitter 引导页面所需的标记。我们基本上是从 [Bootstrap Starter 模板示例](https://getbootstrap.com/docs/4.3/examples/starter-template/)中复制它，并扩展十月份的 CMS 特性。

要创建布局，点击主菜单上的 **CMS** 打开 CMS 编辑器。请注意，使用内置的 CMS 编辑器不是必需的，因为所有的主题对象——布局、页面和部分——都只是主题目录中的 HTML 文件(尽管有一个使用数据库的选项)。这意味着您可以用您最喜欢的代码编辑器来编辑它们。但是，对于新用户来说，使用 CMS 编辑器更好。

![](img/ce24e52bf14cde0905dd54453bb5df1a.png)

点击工具条中的**布局**项，然后点击**添加**按钮。在布局编辑器中，在**文件名**字段中输入**默认**，点击**保存**(也可以在 Windows 上点击 **Ctrl+S** 或者在 Mac 上点击 **Cmd+S** )。将以下文本粘贴到标记代码编辑器中，并确保再次保存布局。

```
<!doctype html>
<html lang="en">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
        <meta name="description" content="">
        <title>My blog theme</title>

        <!-- Bootstrap core CSS -->
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
        <link href="{{ 'assets/css/theme.css'|theme }}" rel="stylesheet">
    </head>
    <body>
        <nav class="navbar navbar-expand-md navbar-dark bg-dark">
            <div class="container">
                <a class="navbar-brand" href="/">Static pages demo</a>

                <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarsExampleDefault" aria-controls="navbarsExampleDefault" aria-expanded="false" aria-label="Toggle navigation">
                    <span class="navbar-toggler-icon"></span>
                </button>

                <div class="collapse navbar-collapse">
                    <!-- Menu contents will go here -->
                </div>
            </div>
        </nav>

        <main role="main" class="container">
            <div class="row">
                <div class="col-8">
                    {% page %}
                </div>
                <div class="col">
                    <div class="blog-categories-sidebar">
                        <h4>Blog categories</h4>

                        <!-- Blog category list will go here -->
                    </div>
                </div>
            </div>
        </main><!-- /.container -->

        <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>
    </body>
</html> 
```

如上所述，标记是从 [Bootstrap Starter 模板](https://getbootstrap.com/docs/4.3/examples/starter-template/)复制而来的，增加了一些内容:

*   我们添加了对`theme.css`资产文件的引用。这个样式表与本教程无关。它定义了博客类别菜单的视觉样式，并管理一些文本样式。
*   主容器有两列。左栏包含`{% page %}`标签，当请求实际页面时，将由 10 月 CMS 用实际页面内容替换。右栏包含博客类别菜单。
*   顶部菜单元素已被删除。在本教程的后面，我们将用自动生成的内容替换它们。

![](img/e52e6796ccf5cc5c54c8a21700553217.png)

要使布局可用于静态页面，我们必须向布局添加静态页面组件。点击工具条中的**组件**项，点击**静态页面**组件。它位于**页面**部分。这将把组件添加到布局中。这个组件不需要任何配置，但是您可以点击它来查看**检查器**中的可用属性。您也可以选中**默认布局**复选框，使该布局成为所有新页面的默认布局。

![](img/10be8566aadd5b88f361b8711783853e.png)

离开页面前不要忘记保存布局！

### 创建静态页面

我们想象的网站将有以下页面结构:

*   主页(/)
*   项目(/项目)
    *   网站(/项目/网站)
    *   应用程序(/项目/应用程序)
*   大约
*   博客(/博客)
*   联系我们(/联系我们)

除了博客之外的所有页面都将是静态页面。没有任何 web 开发知识的最终用户也可以在 WYSIWYG 模式下创建静态页面。现在让我们创建静态页面。

点击十月 CMS 主菜单中的**页面**项，打开静态页面编辑器。

![](img/5988f991fb391c06072ff854109611e4.png)

点击**添加**按钮创建一个新的静态页面。在标题字段中输入**主页**，在**网址**字段中输入“/”(正斜杠)。将任何文本放入**内容**字段并保存页面。

![](img/989d7b5ac578a0435caceb80181ddd5f.png)

重复相同的过程创建项目，关于和联系我们页面。博客页面不是静态页面，现在跳过它。您可以为您创建的页面使用任何 URL。静态页面插件将尝试根据您提供的页面标题生成 URL。

您可能已经注意到，网站和应用程序是项目页面的子页面。要创建子页，将鼠标悬停在页面列表中的项目页面上，然后单击**添加子页**链接:

![](img/44f025e0bba5f93c333dcf62cf27a39e.png)

子页面自动生成的 URL 将反映它们的位置，例如，`/projects/websites`。

现在，您可以通过单击管理区域顶部菜单中的预览图标来测试您的网站。该图标位于用户头像左侧的右上角。如果你做的一切都是正确的，网站现在显示主页，你可以通过在浏览器地址栏中输入它们的 URL 来尝试打开其他页面。

![](img/75c9916c3e1b46592d371376db7c69d1.png)

网站顶部菜单和博客类别列表还不可见，我们将在以下部分解决这个问题。

### 创建菜单

现在我们已经创建了静态页面，我们可以创建主网站菜单。静态页面插件可以根据静态页面的结构、博客类别等等自动创建菜单结构。您可以手动添加菜单项，也可以将菜单配置为自动生成菜单项。

我们的网站顶部有两个菜单——左侧菜单和右侧菜单。让我们从创建左侧菜单开始。在**页面**屏幕上点击侧边栏中的**菜单**项，然后点击**添加**按钮创建您的第一个菜单。在名称字段中输入“顶部菜单–左侧”。在**菜单项**区域点击**添加项目**。

在**标题**字段中输入任意值——无论如何，该菜单项标题将不可见。在**类型**下拉菜单中选择**所有静态页面**。选中**用其生成的内容替换此项**复选框。保存菜单项和菜单本身。

![](img/f8448eeb75674dbd957e3ef60e3403d3.png)

现在，我们可以将菜单添加到我们的布局中，使其在所有网站页面上都可见。返回 **CMS** 区域，点击**布局**，在布局列表中点击 default.htm 布局，在编辑器中打开布局。现在点击工具条中的**组件**项，并点击**静态菜单**组件。在布局组件区域点击新组件，打开**检查器**，确保**菜单**属性显示“顶部菜单–左侧”。在**别名**属性中输入 **topMenuLeft** 。保存布局。

![](img/7af18c08e89af258704617027717b200.png)

现在我们要进行一些编码。Bootstrap 4 期望导航条有一个非常具体的[标记:](https://getbootstrap.com/docs/4.3/components/navbar/)

```
<ul class="navbar-nav">
    <li class="nav-item active"><a class="nav-link" href="...">Active item</a></li>
    <li class="nav-item"><a class="nav-link" href="...">Inactive item</a></li>
</ul> 
```

对于下拉菜单，它稍微复杂一些。

静态页面插件可以自动为菜单生成标记，但是默认的标记并不是特定于 Bootstrap 的——这是不可能有一个适合所有框架的通用标记格式的。我们将创建自己的[部分](https://octobercms.com/docs/cms/partials?utm_source=sitepoint&utm_medium=content&utm_campaign=staticpages)来呈现菜单项，这将与 Bootstrap 4 完全兼容。你可能知道，十月 CMS 使用了[树枝模板引擎](https://twig.symfony.com/)。

在 **CMS** 区域点击工具条中的 **Partials** ，然后点击 **Add** 创建一个新的 Partials。在**文件名**字段中输入**菜单项**，并将以下代码片段粘贴到**标记**字段中。完成后，请确保保存部分内容。

```
<ul class="navbar-nav {{ class }}">
  {% for item in items %}
    <li class="
      nav-item
      {{ item.isActive or item.isChildActive ? 'active' : '' }} 
      {{ item.items ? 'dropdown' : '' }}
    ">
      <a
        class="nav-link {% if item.items %}dropdown-toggle{% endif %}"
        {% if item.items %}data-toggle="dropdown"{% endif %} 
        href="{{ item.url }}"
      >{{ item.title }}</a>

      {% if item.items %}
        <div class="dropdown-menu">
          {% for subitem in item.items %}
            <a class="dropdown-item" href="{{ subitem.url }}">{{ subitem.title }}</a>
          {% endfor %}
        </div>
      {% endif %}
    </li>
  {% endfor %}
</ul> 
```

**项**和**类**变量应该从分部外部传递，我们将在下面解释。**项**变量是要呈现的菜单项列表(数组),而**类**变量指定了我们要添加到 navbar 元素的 CSS 类。我们将使用相同的部分来显示 3 个菜单:主菜单的左侧，主菜单的右侧和博客类别列表。

需要使用**类**变量将 **mr-auto** 类名添加到左上角的菜单中。这个类是[引导布局系统](https://getbootstrap.com/docs/4.3/layout/grid/)的一部分。基本上，它会把左上角的菜单推到左边，把右上角的菜单推到右边。

**项**数组包含一个菜单项列表。你可以在[插件的文档页面](https://octobercms.com/plugin/rainlab-pages?utm_source=sitepoint&utm_medium=content&utm_campaign=staticpages)上找到静态页面菜单的完整 API 文档。

现在我们终于可以在页面上显示菜单了。打开 **CMS** 编辑器，点击工具条中的**布局**，点击我们的默认布局。用下面一行替换`<!-- Menu contents will go here -->`占位符，然后**保存**模板。

```
{% partial 'menu-items' items=topMenuLeft.menuItems class='mr-auto' %} 
```

[部分标签](https://octobercms.com/docs/cms/partials#rendering-partials?utm_source=sitepoint&utm_medium=content&utm_campaign=staticpages)是 10 月 CMS 模板引擎的标准特性。它采用分部名称和可选变量来传递给分部。上面的代码呈现了我们刚刚创建的部分。**条目**变量从静态菜单组件加载菜单项，别名为 **topMenuLeft** ，这是我们之前创建的。**类**变量只是一个静态字符串，其中包含 Bootstrap 所需的值。

再次打开网站预览以查看渲染的菜单:

![](img/8b7edc64792946f7801bfbf6012ee860.png)

目前，菜单有两个问题——联系我们项目显示在左侧，尽管根据我们的计划，它必须在右侧。另一个问题是菜单上没有博客项目。我们将很快解决这两个问题。

首先，让我们修复“联系我们”菜单项。进入**页面**部分，点击侧边栏的**页面**项目，点击**联系我们**页面。点击编辑器右侧的小三角形图标进入**设置**表单:

![](img/eb4eee163c3c82ca72dfe7506076052e.png)

启用**设置**表单上的**隐藏导航**选项，保存静态页面。这将从自动生成的菜单中隐藏“联系我们”静态页面。

![](img/eae4cfdb15a0b4b98abf8e6aafc79900.png)

现在让我们创建右侧顶部菜单。点击工具条上的**菜单**，点击**添加**创建一个新菜单。在**名称**字段中输入**顶部菜单-右**。点击**添加项目**，在**标题**字段输入**联系我们**。在**类型**下拉菜单中选择**静态页面**，在**参考**下拉菜单中选择**联系我们**页面。点击**应用**保存菜单项，并确保保存菜单本身。

![](img/6e339cd7acd2a627d1a5cb7282f1dbd7.png)

返回到 **CMS** 区域，在编辑器中打开我们的默认布局。向布局添加另一个**静态菜单**组件。点击新组件以显示 Inspector，在**别名**字段中输入**顶部菜单右键**，并在下拉菜单中选择**顶部菜单–右侧**。

![](img/0a722ffd3577cf6b780722f4fa0e28d8.png)

关闭 Inspector，将下面一行粘贴到**标记**部分，位于您为呈现左上角菜单而添加的行的下方:

```
{% partial 'menu-items' items=topMenuRight.menuItems %} 
```

正如你所看到的，我们使用和以前一样的部分，但是这次我们从右上方的菜单中加载菜单项，并且不传递任何 CSS 类。保存布局并再次预览网站:

![](img/b7b4f98a9d840ac101e5d439e4b0e662.png)

恭喜，我们就快成功了！最后一个缺失的菜单项是博客。解释如何给网站添加博客功能超出了本教程的范围。你可以在[博客插件文档页面](https://octobercms.com/plugin/rainlab-blog?utm_source=sitepoint&utm_medium=content&utm_campaign=staticpages)上阅读关于创建博客页面的内容。假设您已经创建了博客文章页面，我们可以在左上方的菜单中添加一个菜单项。

打开**页面**部分，进入**菜单**区域。点击**顶部菜单–左侧**菜单，点击**添加项目**。在**标题**字段中输入**博客**，在**类型**下拉菜单中选择 **CMS 页面**。在**参考**下拉菜单中选择您的博客页面。

![](img/2c7af5fac3fc3dc71ef90865d8d9c845.png)

暂时不要关闭菜单项编辑器弹出窗口。我们将展示一个技巧，帮助我们在博客类别页面上以编程方式激活博客菜单项。在菜单项编辑器中点击**属性**标签，在**代码**字段中点击进入**博客**。保存菜单项，然后保存菜单。

您现在可以刷新网站预览，并在菜单上看到我们的新博客项目:

![](img/8fc6e3d42598e4638f20f2d0f1db5eea.png)

### 创建博客类别菜单

我们的演示网站唯一缺少的部分是博客类别菜单。我们假设您已经按照博客插件文档创建了博客类别 CMS 页面。让博客类别页面与静态页面插件兼容的一个重要因素是，类别页面上的**帖子列表**组件必须配置**类别过滤器**属性，以便从 URL 参数中读取其值:

![](img/de47160cef75c5bf5f732a1ae4e73e2a.png)

配置完博客类别 CMS 页面后，再次点击十月 CMS 主菜单中的**页面**项，创建一个新的**菜单**。在**名称**字段中输入**博客类别**，点击**添加项目**。在菜单项编辑器中，在**标题**字段中输入**类别**。这个值并不重要，因为这个菜单项将被替换为基于博客类别的菜单项。在**类型**下拉菜单中选择**所有博客类别**。在 **CMS 页面**下拉菜单中选择您的博客类别 CMS 页面。点击**用其生成的子菜单**替换此项复选框，保存菜单项并保存菜单。

![](img/a1b2eeb6ea375c2aeb5ec7b043881298.png)

我们已经向布局中添加了两个菜单，添加第三个菜单的过程是相同的。点击十月主菜单中的 **CMS** 项，打开默认布局，将**静态菜单**组件添加到布局中。点击新组件，并在**别名**字段中输入**博客类别**。在**菜单**下拉列表中选择新菜单**博客类别**。保存布局。

现在用**部分**标签替换注释`<!-- Blog category list will go here -->`:

```
{% partial 'menu-items' items=blogCategories.menuItems class='flex-column' %} 
```

再次保存布局并预览网站。如果你在博客类别菜单中没有看到任何条目，请确认你已经创建了博客类别！现在，网站看起来正是我们想要的。

![](img/b19fc97e2a515e4e641acba86ee28558.png)

然而，如果你点击任何博客类别，你会注意到博客主菜单项是不活跃的。为了解决这个问题，我们将使用静态页面 API 以编程方式激活菜单项。在 CMS 编辑器中打开博客类别页面，将这个小片段粘贴到**代码**编辑器中:

```
function onInit()
{
    $this['activeMenuItem'] = 'blog';
} 
```

![](img/74921bdd29b29a964c095c541e1ca904.png)

`blog`值是您为博客项目指定的菜单项代码。而 **activeMenuItem** 是一个变量，静态页面插件使用它来确定哪个菜单项当前是活动的。你可以在[文档页面](https://octobercms.com/plugin/rainlab-pages#documentation?utm_source=sitepoint&utm_medium=content&utm_campaign=staticpages)上阅读更多关于插件 API 的内容。保存页面并刷新网站预览—博客项目现在处于活动状态。

![](img/cef1c1fcb71a47079c9ad411fff209d5.png)

我们希望你喜欢这个教程！写这篇文章花了几个小时，但创建演示网站的过程只花了 15 分钟。现在你可以创建静态和动态混合页面的网站。您可以在市场上找到更多有用的插件，并在十月 CMS 博客上了解更多技巧！

另请参见:

*   [静态页面插件](https://octobercms.com/plugin/rainlab-pages#documentation?utm_source=sitepoint&utm_medium=content&utm_campaign=staticpages)
*   [博客插件](https://octobercms.com/plugin/rainlab-blog?utm_source=sitepoint&utm_medium=content&utm_campaign=staticpages)
*   [插件市场](https://octobercms.com/plugins?utm_source=sitepoint&utm_medium=content&utm_campaign=staticpages)
*   [主题市场](https://octobercms.com/themes?utm_source=sitepoint&utm_medium=content&utm_campaign=staticpages)
*   [10 月 CMS 文档](https://octobercms.com/docs/?utm_source=sitepoint&utm_medium=content&utm_campaign=staticpages)
*   [十月 CMS 博客](https://octobercms.com/blog?utm_source=sitepoint&utm_medium=content&utm_campaign=staticpages)
*   [脸书页面](https://www.facebook.com/octobercms/)
*   [推特](https://twitter.com/octobercms)

## 分享这篇文章