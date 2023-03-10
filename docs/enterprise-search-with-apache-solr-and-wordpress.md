# 使用 Apache Solr 和 WordPress 进行企业搜索

> 原文：<https://www.sitepoint.com/enterprise-search-with-apache-solr-and-wordpress/>

在本教程中，我们将了解什么是 Apache Solr 以及它是如何工作的。我们将看看一些强大的 Solr 特性，以及它与 MySQL 有何不同。我们会看到将 Solr 整合到我们的 WordPress 网站的好处。我们还将介绍如何安装 WordPress Solr 插件，以及如何托管 Apache Solr。最后，我们将通过查看一些使用 Solr 的流行网站来总结。

![Solr Search with WordPress](img/a545ce3fab53dd5eeaa24ad41f24876a.png)

本教程面向 WordPress 开发者和用户。如果你不是开发者，只是用 WordPress 来建立网站，那么你应该只关心 Solr 的好处和设置。然而，如果你是一个 WordPress 开发者，那么学习 Solr 的内部原理和技术实现将会对你有所帮助。

## 什么是 Apache Solr？

Apache Solr 是一个开源的企业搜索服务器。它存储信息的方式使得搜索速度非常快。简而言之，它也是一个类似 SQL 和 NoSQL 的存储系统。

Solr 是用 Java 编写的，使用 Lucene 搜索库作为其核心功能。使用 Solr 不需要了解 Java。

## 它与 MySQL 有何不同？

如果您是 Solr 的新手，理解 Solr 内部的最好方法是将其与 MySQL 进行比较。

*   MySQL 以表格和行的形式存储信息。而 Solr 以模式和 XML 文档的形式存储信息。模式定义了文档的结构。
*   MySQL 中可以有多个表，同样，Solr 中也可以有多个模式。
*   表中的列定义了表的结构，类似地，Solr 字段定义了模式的结构。
*   在 MySQL 中，你以行的形式存储，而在 Solr 中，你以文档的形式存储。
*   在 MySQL 中，当列被索引时，行被排列成树状结构。而在 Solr 中，当一个字段被索引时，它被安排到一个[反向索引](http://en.wikipedia.org/wiki/Inverted_index)数据结构中。

## 是什么让搜索变得如此之快？

Solr 使用倒排索引数据结构在文档中搜索单词，并与最终结果相交。没有其他存储系统使用这种数据结构。

## Solr 的其他特性是什么？

Solr 提供了许多其他功能，如拼写纠正、刻面、高亮、结果分组、自动完成等。在你的 WordPress 站点中实现这些功能会让它脱颖而出。这些功能提供了更好的用户体验和访问 WordPress 网站内容的新方法。

## 为什么你应该整合 WordPress 和 Solr？

当你的站点上的帖子数量增加时，当用户在你的站点上搜索时，MySQL 开始运行缓慢。这是因为 MySQL 循环遍历每个帖子，并使用正则表达式来匹配搜索词。这是一个非常耗费 CPU 资源的任务。有时，由于 PHP 脚本执行时间限制，用户会收到请求超时错误。如果有 10，000 个帖子，那么对于每个搜索查询，MySQL 将访问文件系统 10，000 次，这是一个非常庞大的任务，会降低网站的速度。

而 Solr 可以在几秒钟内搜索 10，000 个文档。如果你有一个中等规模的博客，那么一个 Solr 实例就足以支持所有的帖子。

## 如何整合 Solr 和 WordPress？

要在 WordPress 中集成 Solr，你需要两样东西:Solr 插件和 Solr 主机。Solr 插件的基本工作是[拦截 WordPress 搜索请求](http://codex.wordpress.org/Plugin_API/Action_Reference/pre_get_posts)并提供来自 Solr 服务器的结果，而不是 WordPress 自然地从 MySQL 获取结果。

Solr 插件还提供了自动建议、拼写纠正、高亮显示搜索词、标记和分类等功能。然而，要实现这些额外的特性，WordPress 主题必须与特定 Solr 插件的 API 兼容。一些插件允许你添加一个定制的`search.php`文件到主题中，然后插件[拦截模板层次](http://codex.wordpress.org/Plugin_API/Action_Reference/template_redirect)并执行定制的搜索文件，它具有所有这些高级特性。

Solr 插件只复制文章和页面到 Solr 服务器。Solr 仅在搜索请求期间使用，例如`http://example.com/?s=search_query`。对于所有其他操作和请求，WordPress 遵循正常流程，例如它使用 MySQL。

## WordPress 的 Solr 插件

有两个流行的 WordPress 插件可以集成 Solr: [我的 Solr 服务器的高级搜索](https://wordpress.org/plugins/advanced-search-by-my-solr-server/)和 [WPSOLR 搜索引擎](https://wordpress.org/plugins/wpsolr-search-engine/installation/)。

在本教程中，我将使用 WPSOLR 搜索引擎插件将 SOLR 集成到 WordPress 中。

## Solr 托管

您可以在专用服务器上托管 Solr，但是这需要一些维护。所以首选 Solr 云托管服务。有两个流行的 Solr 云托管服务: [OpenSolr](https://opensolr.com/) 和 [GotoSolr](http://www.gotosolr.com/en/) 。

在本教程中，我将使用 GotoSolr 来托管我们的 Solr 服务器。

## 安装 WPSOLR 搜索引擎插件

在 WordPress Admin Dashboard 中导航到“插件”,然后导航到“添加新插件”。然后搜索 WPSOLR 搜索引擎’。你会看到插件列在最上面。安装并激活它。

![WPSOLR Search Engine Plugin](img/eedfc9b329e6221c18754763986fc055.png)

## 在 GotoSolr 上托管 Solr

下面是在 GotoSolr 上托管 Solr 的步骤:

*   [创建新的 GotoSolr 账户](https://admin.gotosolr.com/#/signup?lng=en)。第一个月是免费的，所以最好先在你的 WordPress 网站上试用一下。这样，不喜欢也没有风险。
*   创建帐户后，您将可以访问控制面板。

![GotoSolr Dashboard](img/37b48bfbc726a98003f38de59019061a.png)

*   索引是模式、文档及其配置集合。您需要创建一个索引。点击索引标签上的 **+** 按钮。

![Solr Indexes](img/070c1f07d7c92a13ed51b98d2d664008.png)

*   现在你需要[为你的索引下载 WPSOLR 插件的配置文件](http://wpsolr.com/releases/)并上传它们。
*   单击“schema.xml”选项卡，并使用上传按钮上传之前下载的 schema.xml 文件。然后点击保存按钮。

![Solr Schema](img/220f8b09f4fc76e35a8872d64114b49c.png)

*   单击“访问密钥”选项卡，通过单击“添加新密钥/密码”创建新的安全密钥/密码。稍后，您将使用这些键，通过在所有 Solr https 基本身份验证调用中使用的 user/password 字段中设置它的值，让插件(并且只有它)连接到您的 Solr 索引。

![Solr Access Keys](img/83cb39e397d3bb0b9b89535659aa47b7.png)

*   点击“本索引的网址”标签。将索引的 URL 粘贴到文档中以备后用。注意这个 URL 有多复杂，而且它使用了 HTTPS。这一点，加上您的访问键，确保您的索引是安全的。

![Solr Index](img/804c33d0ef368ec649955a840a073610.png)

## 配置 WPSOLR 搜索引擎并将其连接到 GotoSolr 服务器

以下是连接 GotoSolr 服务器的步骤:

*   打开 WordPress 管理仪表板上的 WPSOLR 设置页面。点击“我将我的 2 个兼容配置文件上传到我的 Solr 核心”按钮。

![WPSOLR Settings](img/7b3dd66fb666ce5c7f6747ea9181f616.png)

*   Now select “Cloud Hosting” radio button. And then copy server access information from GotoSolr dashboard.

    ![WPSOLR/GotoSolr Dashboard](img/374433b84fbe05e065668a2039d257be.png)

    点击“检查 Solr 状态，然后保存”。这将验证您的设置，并测试与 Solr 索引的连接。如果有任何错误，您将会收到一条警告消息。如果(且仅当)连接通过验证，您的设置将被保存。

*   Under ‘Solr Options’ tab select what you want to be indexed and also what extra features you need.

    ![WPSOLR Solr Indexing Options](img/bb8424edd8ff374048f7f32e1dcb0383.png)

![WPSOLR Solr Result Options](img/20315912ed815bb7e5ce04cc6a2a69f5.png)

*   现在转到“Solr Operations”标签，点击“Load data”按钮，这将把你所有的 WordPress 内容复制到 Solr 服务器。

![Copying WordPress data to Solr](img/3848545558502e84f7c2027c82b435b3.png)

*   从现在开始，如果你做了任何改变或者创建了新的页面和帖子，这个插件会自动在 Solr 索引中复制一份。

这个插件覆盖了 WordPress 搜索表单来实现自动完成功能。如果你正在使用这个插件，那么你不需要创建一个自定义的 search.php 文件，因为这个插件会自动创建它。下面是一些截图，展示了一个 WordPress 站点上 Solr 的自动完成和其他特性。

![Example of Solr WordPress 1](img/53f3ad4da4c68a2aed1977a666beca82.png)

![Example of Solr WordPress 2](img/858f7db834d92e62f503faf6b1f2dc99.png)

## 使用 Solr 的网站

有很多流行的网站都在使用 Solr 来支持他们的搜索。这里只是一个简短的列表:

*   DuckDuckGo 使用 Solr 来提供拼写检查，存储网页等。
*   Drupal.org 使用 Solr 来增强他们所有的网站功能。
*   Last.fm 的所有搜索功能都使用 Solr。
*   其他一些网站包括美国在线，雅虎，Instagram，黄页等。

## 最后的想法

如果你是一名开发人员，那么你肯定会从学习 Solr 中受益。如果你是 WordPress 用户，那么集成 Solr 肯定会帮助你提供一个强大的搜索引擎。

这里有一些资源，您可以从中获得关于 Apache Solr 的更多信息:

*   [Solr 5 分钟](http://www.solrtutorial.com/solr-in-5-minutes.html):这个在线资源涵盖了 Solr 的基础知识，可以让你快速掌握。
*   Solr in Action :如果你喜欢通过 a 来学习，那么这是一个很好的选择。
*   [用大数据和云计算学习 Apache Solr】:这是我个人的视频课程，这是开始学习 Solr 的好方法。](http://qscutter.com/course/learn-apache-solr-with-big-data-and-cloud-computing/)

如您所见，在 WordPress 中实现 Solr 非常简单，没有太多麻烦。请在下面分享你的经历和你遇到的任何 Solr 和 WordPress 实现。

## 分享这篇文章