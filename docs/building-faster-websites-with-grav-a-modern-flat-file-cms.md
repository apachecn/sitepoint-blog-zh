# 用现代平面文件 CMS Grav 构建更快的网站

> 原文：<https://www.sitepoint.com/building-faster-websites-with-grav-a-modern-flat-file-cms/>

*我们最近推出了一些内容管理系统(CMS)文章——关于 [Perch](https://www.sitepoint.com/building-lightning-fast-extensible-websites-with-perch-cms/) 和 [Craft](https://www.sitepoint.com/introduction-to-craft-cms/) (都是数据库驱动的选项)和 [Statamic](https://www.sitepoint.com/building-database-free-websites-with-statamic-cms/) ，一个平面文件 CMS。在本文中，Ivaylo Gerchev 介绍了 Grav，这是另一个令人印象深刻的平面文件 CMS。*

* * *

如今，在网络开发者中有一种有趣的返璞归真的趋势。一些现代的、同类最佳的技术——如 [Markdown](https://en.wikipedia.org/wiki/Markdown) 、 [Twig](http://twig.sensiolabs.org/) 和[YAML](https://en.wikipedia.org/wiki/YAML)——经常被一起用来生产许多新的、轻量级的 CMS，专注于速度、简单性和生产率。他们遵循的原则非常简单:当事情变得太复杂时，最好从头开始，而不是试图清理现有的混乱。

在这篇文章中，我将向您介绍这种从零开始的*平台的一个最好的例子，称为 [Grav](https://getgrav.org/) 。*

![Screenshot of the Grave website](img/28d0284844a59e700c26b63fc99b4d4c.png)

## 什么是 Grav，我为什么要使用它？

Grav 是一个现代的平面文件 CMS，由 RocketTheme 开发。为什么现代？因为它使用了现代 PHP 和最新的标准，如 Markdown、Twig、YAML、教义缓存等。**平面文件** CMS 意味着不涉及数据库，所有网站的内容和配置设置都存储在纯文本文件中。

为了更好地理解这个相对较新的概念，想象一个常规的、基于 HTML 的网站，就像过去一样，但是增加了动态创建和维护实际 HTML 文件的能力。当你描绘这个场景时，请记住两件事:

*   首先，Grav 不是一个[静态站点生成器](https://www.sitepoint.com/7-reasons-use-static-site-generator/)。内容写在 Markdown 文件中，这些文件被处理并根据需要动态地转换成 HTML 文件。你不会在你的站点目录中找到任何静态的 HTML 文件。
*   第二，即使 Grav 不使用数据库，它仍然给你一种动态操作内容的方法——就像 WordPress 这样的数据库驱动的 CMS 一样。

所以，你可以把 Grav 看作是一种混合体，结合了静态和动态世界的优点。此外，平面文件 CMS 为您提供了一些非常有吸引力的额外优势:

*   一个没有数据库的网站消除了处理数据库可能带来的所有麻烦和瓶颈。
*   因为你只处理文件，所以你可以通过使用 [Git](https://git-scm.com/) 和像 [GitHub](https://github.com/) 或 [BitBucket](https://bitbucket.org/) 这样的服务，轻松地为所有文件添加 100%的版本控制。
*   您可以复制您的所有内容和配置文件，并将其移动到您希望的任何地方，这使得您的站点完全可移植并易于备份。

无论你是开发人员、设计人员，还是普通用户，Grav 都有适合你的东西。

Grav 是开发人员友好的
它为开发人员提供了许多有用的工具，如 CLI 控制台、GPM (Grav 软件包管理器)和调试栏。所有这些工具使得开发、调试、安装和更新主题和插件变得更加容易。

Grav 对设计师友好
设计师通常不是经验丰富的程序员，他们需要一种简单的方法来实现他们的设计。凭借 Twig 模板引擎的强大和简单，这可以相当容易地完成。使用 Twig 输出 PHP 就像使用 CSS 预处理程序(如 Sass)输出 CSS 一样简单。

Grav 是用户友好的
由于有了管理面板插件，用户可以创建和操作他们网站的内容，并以方便的图形化方式管理整个网站。

## Grav 功能的快速入门

要运行 Grav，只需要一个 web 服务器和 PHP 5.5.9 或更高版本。就是这样。(如果在本地测试，您可以使用像 MAMP 和 WAMP 这样的测试环境。)

### 正在设置

1.  [下载](https://getgrav.org/downloads) Grav 核心+管理插件。
2.  在你的 webroot 中解压。
3.  将浏览器指向解压后的 Grav 文件夹。
4.  创建一个管理员帐户。
5.  您可以立即开始添加内容。

![Screen shot of the Grav admin page](img/944756bad8e04ab6c085c303b8d14636.png)

### 使用文件

现在，让我们动手看看 Markdown、Twig 和 YAML 是如何在 Grav 中一起工作的。

假设我们有一个简单的网站，我们想添加一个 **FAQ** 页面，在那里问题和答案以手风琴的形式呈现。我们将使用来自 [UIkit 框架](http://getuikit.com/index.html)的**手风琴**组件。出于这个原因，我们将安装`UIKitifier`插件，它将为我们站点的每个页面自动添加 UIkit 及其组件。

1.  在**管理**面板中，进入**插件**，点击右上角的**添加**按钮。一个包含所有可用插件的列表将会出现。
2.  向下滚动直到你找到 **UIKitifier** 插件，然后点击**安装**按钮。
3.  安装完成后，将插件配置页面滚动到**组件**部分，将**手风琴**切换到**是**。

现在，我们将离开**管理**面板，返回到我们的站点文件。

### 添加页面

在 Grav 中，你整个站点的主题、插件和配置都存储在`/user`目录中。你所有的网站内容都存储在`user/pages`中。在那个目录中已经有一个文件夹，叫做`01.home`。这个文件夹代表我们网站的**主页**页面。

为了添加一个 **FAQ** 页面，我们创建了一个新文件夹，并将其命名为`02.faq`。Grav 使用数字对页面进行排序，因此数字前缀告诉 Grav 将 FAQ 页面放在菜单中主页的后面。此外，Grav 使用页面的名称作为 slug — `/faq`。

在`02.faq`文件夹中，我们为页面创建了实际的文件，并将其命名为`faq.md`。这个名字用来告诉 Grav 使用一个同名的模板`faq.html.twig`，我们稍后会创建这个模板。

简而言之，Grav 中的页面由包含 Markdown 文件的文件夹表示。文件夹名称代表页面名称，而 Markdown 文件名反映用于显示页面的模板的名称。

在`faq.md`中我们放了以下内容:

```
---
title: FAQ
faq:
    -   question: What is Grav?
        answer: Grav is a modern, flat-file CMS.
    -   question: What does flat-file mean?
        answer: Flat-file means your content is stored in files instead of a database.
    -   question: Why should I use Grav?
        answer: You should use Grav because it's a simple, lightweight, flexible, and powerful CMS.
---

## Frequently Asked Questions
```

### 页面的结构

Grav 中的每个降价文件都有两个部分:

1.  标题部分，用两个`---`标记表示，通常称为 **YAML 锋物质**。在这里，我们将所有的[内置](http://learn.getgrav.org/content/headers)和定制配置设置放入页面。
2.  一个内容部分，在标题下面，我们把我们的 Markdown 格式的内容。

在我们的`faq.md`文件中，我们用内置的`title`变量定义了页面的标题，并用自定义的`faq`变量定义了一个问答列表。在标题下面，我们放了一个 Markdown 格式的`H2`标题。

先不要预览页面！为了正确显示，它需要一个模板。为了提供一个，我们在`user/themes/antimatter/templates`目录中创建一个空的`faq.html.twig`文件，并在其中放入以下内容:

```
{% extends 'partials/base.html.twig' %}

{% block content %}

{{ content }}

<div class="uk-accordion" data-uk-accordion>

    {% for faq in page.header.faq %}

        <h3 class="uk-accordion-title">{{ faq.question }}</h3>
        <div class="uk-accordion-content">{{ faq.answer }}</div>

    {% endfor %}

</div>

{% endblock %}
```

这里，我们使用`{% extends %}`和`{% block %}` Twig 标签来扩展`partials/base.html.twig`模板，并用我们的自定义内容替换其中的空内容块。`{{ content }}`将显示降价内容部分的内容。下面，我们使用`page.header.faq`变量遍历`faq.md`文件头部分的每个列表项。我们还为 **Accordion** 组件添加了 UIkit 类，以便将列表显示为 Accordion。

现在，回到**管理**，切换到**页面**。你应该会在**主页**的正下方看到一个**常见问题**页面。

![Screen shot of pages in the Grav admin panel](img/4218cc019e0c030419e2f663183ee01f.png)

单击它，然后点击带有眼睛图标的按钮来预览页面。它应该是这样的:

![Screen shot of the current FAQ page](img/d846129fda46223cee344bfd90c56e16.png)

恭喜你！您刚刚使用特定模板创建了一个页面。到目前为止，一切顺利。但是，当您需要向列表中添加更多问题时，您必须编辑`faq.md`文件中的标题部分。这对不熟悉这些技术概念的用户来说尤其不方便。如果有一种更简单的方法来编辑 FAQ 列表不是很好吗？其实有这么个办法。我们可以通过在**管理**面板中提供一个易于使用的 GUI 表单来添加这样的功能。为了创建这样的表格，Grav 使用 YAML 文件，称为**蓝图**。

为了构建我们的表单，我们在`user/themes/antimatter/blueprints`目录中创建了一个名为`faq.yaml`的新文件，并将以下内容放入其中:

```
title: FAQ
@extends:
    type: default
    context: blueprints://pages

form:
  fields:
    tabs:
      fields:
        faq:
          type: tab
          title: FAQ
          fields:
            header.faq:
              name: faq
              type: list
              label: Frequently Asked Questions

              fields:
                .question:
                  type: text
                  label: Question
                .answer:
                  type: text
                  label: Answer
```

回到**管理**面板，刷新页面，你会看到一个额外的标签**常见问题**。点击它，一个全新的表单出现了，允许您以方便的 GUI 方式对 FAQ 列表重新排序、删除和添加项目。去试试吧。

![Screen shot of the YAML for in the Admin panel](img/c385bc70de2bdf41f943539e062954af.png)

## 你能用 Grav 建立什么类型的网站？

WordPress、Joomla 和 Drupal 都是强大的、成熟的 CMS，但是它们的复杂性和陡峭的学习曲线对于希望建立小型或中型网站(如个人博客、作品集、图片库或商业机构)的人来说是多余的，完全不需要。

如果您想要构建简单、快速且易于管理的站点，那么值得考虑像 Grav 这样更简单的选项。基本上没有安装和初始配置，只是即插即用。

当然，也不需要担心数据库。(就个人而言，我认为一个数据库对于一个较小的站点来说是多余的。)请记住，即使 Grav 使用文件，也可以像在常规的数据库驱动的 CMS 中一样动态地操作它们。在任何情况下，当你不需要数据库的能力时，维护一个没有数据库的站点是真正的乐趣。

另一方面，用 Grav 运行一个又大又重的网站可能是一个具有挑战性的操作，至少在目前的状态下是这样。尽管 Grav 已经有了很多插件，但大多数都是做简单的工作。如果你的网站需要更复杂和/或更具体的行为或功能，你需要自己为它构建一个插件，或者雇人为你构建。此外，如果您的网站包含数千个页面，那么管理它们可能会有点困难。

## 格拉夫的优点和缺点

在这一部分，我将概述我认为 Grav 的主要优点和缺点。

### Grav 优势

*   用现代技术建造的。现代 PHP 标准，Markdown，Twig，YAML 等。
*   非常快。得益于其平面文件架构和巧妙的缓存机制。
*   **更安全。**没有要被黑的数据库。
*   **零配置即时安装。**打开包装，你就可以开始了。没有数据库创建或配置瓶颈。
*   **基于文件而不是基于数据库。这意味着，如果你编辑一个文件时出错，只有那个文件会被损坏；如果您使用数据库驱动的 CMS，整个数据库可能会损坏。**
*   **轻松安全的备份和恢复。**你可以压缩你的整个网站，并把它运到你想去的任何地方。这比备份和恢复数据库要容易得多。
*   **100%版本控制。给你的文件添加版本控制是小菜一碟。(对数据库进行版本控制可能会很麻烦。)**
*   **使用 Markdown 创建简单、干净的内容。**但是你也可以使用 HTML，甚至两者混合使用。
*   无限的分类法。您可以随心所欲地标记和过滤您的内容，无需额外的编码步骤。
*   **内置图像编辑。**您可以在添加图像时对其进行编辑。
*   **易于定制。你可以自由使用你喜欢的 CSS 框架。主题化轻而易举。不需要 PHP 知识。**
*   **易于扩展。得益于 Grav 灵活的模块化架构，您可以从现有插件中选择，也可以轻松编写自己的插件。**
*   CLI 工具。有几个控制台工具，比如 GPM，方便了开发者的工作。
*   **调试和记录功能。在开发主题和插件时，你可以使用一个方便的调试栏。**
*   **强大而简单的管理面板**，一键式安装和更新。
*   **多站点功能。**您可以用一个 Grav 实例运行多个站点。
*   **多语言支持**。将你的网站翻译成你想要的多种语言。
*   最小的学习曲线。几乎没什么新东西可学。PHP 的使用减少到最低限度。学习 Markdown、Twig 或 YAML 可以在一天或一个周末完成，如果你还不知道它们的话。
*   出色的文档。Grav 的每一部分都解释的很清楚。事实上，Grav 非常简单，一旦你掌握了基本概念，你就可以通过探索已经存在的主题和插件中的代码来进一步学习它。
*   许多免费和现成的主题、插件和框架。除了传统的主题和插件，Grav 还提供了**框架**——由 Grav 核心、主题、插件和样本内容组成的一体化主题包。
*   **在麻省理工学院许可下免费开源**。

### 严重的缺点

*   **不适合大型商业网站**，需要处理成千上万的页面，并且需要复杂的数据库关系。
*   建造一个复杂的网站可能很有挑战性。这是可能的，但你需要创建许多自定义功能。
*   **道路测试。**尚未在足够多的网站和不同条件下进行测试。
*   **降价依赖**。最终用户必须学会使用 Markdown(尽管这并不难学)。通常，终端用户和客户习惯于用 WYSIWYG 编辑器编写他们的内容。
*   不够形象化。与 WordPress 或 Joomla 相比，Grav 就像是与成熟的 IDE(如 WebStorm)相比的崇高文本。目前，仍有一些东西需要手动配置或操作。

## Grav 的未来计划

正如本帖所宣布的，上面探讨的许多缺点最终将在 Grav 的下一个版本中得到解决。根据路线图，Grav 的下一个版本将更加用户友好(增加了 Admin Pro 和 Forms Pro 插件)，设计者友好(增加了对 [Gantry](http://www.gantry-framework.org/) 主题框架的支持)，开发者友好(提供了一个全面的 API 来构建前端和后端应用程序)。看起来很有希望。所以，敬请关注。

## 比赛呢？与其他选项相比如何？

首先，我想澄清的是，将 Grav 与 Drupal、Joomla 或 WordPress 等 CMS 进行比较并没有多大用处。Grav 是不同的产品，来自不同的类别。它不是数据库驱动的 CMSs 的替代品，而是一个有其优点和缺点的可用替代方案。

Grav 只能与其他无数据库的平面文件 CMS(不包括静态站点生成器)进行真正公平的比较，其中大部分可以在 FlatPhile 的平面文件 CMS 或[列表中找到。](http://www.flatphile.co/)

在我选择 Grav 作为我最喜欢和最适合我需要的 CMS 之前，我探索了大多数可用的选项，我真的找不到另一个开源替代方案在一个包中提供简单性、功能性和功能性的最佳组合。当然，这是我个人的看法，所以建议你自己去研究，自己决定。

现在，有两种流行的平面文件 CMS——[Statamic](https://www.sitepoint.com/building-database-free-websites-with-statamic-cms/)和[Kirby](https://www.sitepoint.com/introduction-kirby-cms/)——它们可以被定义为真正的 Grav 竞争对手。它们与 Grav 有许多相似之处和共同的功能，但与 Grav 相比，它们有两个主要缺点。首先，它们都是商业产品，其次，它们没有提供 Grav 以主题、插件或框架的形式提供的现成的或扩展的功能。

## 摘要

对我来说，鉴于其简单性和功能性的最佳结合，Grav 是目前最好的现代平面文件 CMS 之一。我真的不喜欢数据库带来的额外的复杂性，所以像 Grav 这样的无数据库 CMS 完美地满足了我的需求。最后，这完全取决于你个人的要求和喜好。如果你需要一个现代的，轻量级的 CMS，有清晰易懂的架构，那么 Grav 可能也是你正确的选择。

## 分享这篇文章