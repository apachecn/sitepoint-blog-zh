# 如何从头开始构建 WordPress 主题:第一步

> 原文：<https://www.sitepoint.com/build-wordpress-theme-from-scratch-first-steps/>

WordPress 主题让 WordPress 用户能够完全改变 WP 网站的外观，并为其添加功能。在这个由三部分组成的系列中，我们将介绍 WordPress 主题，展示它们是如何工作的，它们是如何构造的，它们背后的 PHP 架构，以及其他相关信息。然后我们将开始构建 WordPress 主题的旅程。

第一篇文章通过讨论 WordPress 主题背后的理论为我们的旅程做准备。

## 简单说两句基础知识

WordPress 被认为是一个博客引擎，或者一个简单的、面向博客的内容管理系统。它最初于 2003 年由马特·莫楞威格和迈克·利特尔发行。从那时起，它的用户群就没有停止增长。WordPress 是一个 PHP 驱动的 web 应用程序，使用 MySQL 作为其数据库，通常运行在服务器程序之后，如 NGINX 或 Apache。

WordPress 基本上只是一堆 PHP 文件，作为一个应用程序一起工作。PHP 解释器使用这些文件为 web 访问者生成网页。更准确地说，它生成 HTML。

WordPress 模板引擎的作用是让我们能够编写(主要)表示性的指令——关于如何精确地构造和样式化 WordPress 将要输出的 HTML 内容的指令。这些指令被封装在主题中。

每个主题由一个文件夹组成，其中有 PHP、CSS，有时还有 JavaScript 文件。每个 WordPress 主题必须拥有的文件——最少——是`style.css`和`index.php`。这是主题运行的最低技术要求*，但是没有一个严肃的 WordPress 主题只保留这两个文件。*

## 基本模板和部分文件

最小的`index.php`文件捕获所有在主题中没有各自的*专用*模板文件的查询。`front-page.php`、`home.php`、`page.php`、`taxonomy.php`、`author.php`、`archive.php`是一些模板，我们可以在主题中使用它们来进一步构建主题中的特定页面或查询。

例如，当访问者请求一些显示文章列表的页面时，`archive.php`文件将指定 HTML 输出结构。`page.php`将指定如何显示单个页面，等等。

**片段文件**封装了 WordPress 网站中页面的可重复部分。例如，网站上所有页面的页眉和页脚通常是一致的，所以 WordPress 主题将这些页面部分分为`header.php`和`footer.php`。`comments.php`将用于显示适用的评论。

我们解释过的*模板文件*中需要这些文件。

这样就坚持了[干原则](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)，不在那些文件里重复代码。

## 模板层次结构

在 WordPress 模板系统中，有一个模板文件的层次结构，WordPress 会尝试为每个请求使用它。这种等级制度是基于特殊性的。WordPress 将尝试为每个请求使用最具体的文件，如果它存在的话。如果它不存在，它将查找下一个不太具体的文件，依此类推。

为了在*页面*请求上解释这一点——当访问者访问 WordPress 网站上的特定页面时——WordPress 将首先尝试在`wp-admin`后端找到页面作者分配给它的模板。这可以是一个完全自定义的模板文件，甚至是完全静态的。

如果没有这样的模板，或者它没有被分配，WordPress 将试图找到一个文件名中带有特定页面的*标记*的模板文件。那看起来像是`page-mypageslug.php`——不管我们的`mypageslug`可能是什么。

WordPress 将尝试使用的下一个文件将是文件名中带有特定页面 ID 的文件——比如`page-48.php`。

如果这些特定于页面的文件都不存在，WordPress 将尝试使用`page.php`——用于所有****页面**，除非另有说明。**

 **如果它没有找到`page.php`，它会尝试使用`singular.php`。当没有找到*帖子* — `single.php`时使用该模板文件，当没有找到`page.php`时使用*页面*。

现在，如果在我们的*页面*请求示例中没有找到这些，WordPress 将退回到`index.php`。

简单地说，这解释了 WordPress 模板的层次结构。我们提到的所有这些模板文件通常会包含(需要)像`header.php`、`footer.php`和其他需要的部分。他们还可以指定使用特定于*的*部分——例如，特定于页面的标题。

为了理解主题化，你需要熟悉的下一件事是 WordPress *post type* 。

## WordPress 文章类型

WordPress 中的内容以*帖子类型*的形式存在。内置类型有*帖子*和*页面*。这些是合乎逻辑的。WordPress 还内置了*附件*帖子类型、*导航菜单*和*修订*。从技术上讲，这些也是文章类型。

我们也可以指定我们自己的帖子类型，无论是在我们的主题还是插件中。我们需要使用以下内容:

```
register_post_type( $post_type, $args ) 
```

在这里，我们指定了文章类型名称、它的结构、它在`wp-admin`中的表示方式等等。

当我们定义了这个之后，我们还可以创建针对这个文章类型的模板文件。自定义帖子类型，如*页面*和*帖子*，有自己的模板层次结构。

*关于模板层次结构的更多信息可在[这里](https://developer.wordpress.org/themes/basics/template-hierarchy/)找到。*

## style.css

`style.css`是每个 WordPress 主题中至关重要的文件，它的功能超越了样式。这个文件用于向 WordPress 提供基本的主题信息。没有这个，WordPress 将不能注册一个主题作为主题。

WordPress Codex 提供了关于这个文件所有细节的更多信息。为了简洁起见，我们只回顾其中的一部分。在这个 CSS 文件的标题注释中，我们提供了以下信息:

*   主题名称
*   作者
*   描述
*   主题 URI
*   版本
*   许可证
*   和其他细节

WordPress 使用这些细节在后端正确显示主题。

例如，2017 主题中的 meta 标头如下所示:

```
/*
Theme Name: Twenty Seventeen
Theme URI: https://wordpress.org/themes/twentyseventeen/
Author: the WordPress team
Author URI: https://wordpress.org/
Description: Twenty Seventeen brings your site to life with header video and immersive featured images. With a focus on business sites, it features multiple sections on the front page as well as widgets, navigation and social menus, a logo, and more. Personalize its asymmetrical grid with a custom color scheme and showcase your multimedia content with post formats. Our default theme for 2017 works great in many languages, for any abilities, and on any device.
Version: 1.7
License: GNU General Public License v2 or later
License URI: http://www.gnu.org/licenses/gpl-2.0.html
Text Domain: twentyseventeen
Tags: one-column, two-columns, right-sidebar, flexible-header, accessibility-ready, custom-colors, custom-header, custom-menu, custom-logo, editor-style, featured-images, footer-widgets, post-formats, rtl-language-support, sticky-post, theme-options, threaded-comments, translation-ready

This theme, like WordPress, is licensed under the GPL.
Use it to make something cool, have fun, and share what you’ve learned with others.
*/ 
```

也就是说，`style.css` file 显然是用来设计 WordPress 页面的样式的。

## WordPress 动作和过滤器挂钩

WordPress——以及 WordPress 主题和插件——严重依赖于*动作挂钩*和*过滤挂钩*。这些是[事件驱动架构](https://www.sitepoint.com/wordpress-hook-system/)的一部分。对此的一个简单解释是，在执行连接到访问者 web 请求的页面的过程中，存在某些注册点— *钩子*——使我们能够在这些点上触发操作。我们*将* PHP 函数与那些要执行的点挂钩。那些是**动作**挂钩。**过滤器**钩子关注于处理——改变——执行周期内的数据片段。因此，如果过滤器挂钩是为任何一段数据(PHP 变量)注册的，我们可以*挂钩*我们自己的函数，并更改或处理这段数据。

虽然与无处不在的 [MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) 软件模式相比，WordPress 对 hook 系统的依赖在 WordPress 的普及中发挥了很大的作用，因为它使得将新功能插入其中变得非常简单，不需要接触甚至深入理解`core`代码库本身。

钩子在这里[有更详细的解释，我们将在本指南的其他部分更深入地研究它。](https://www.sitepoint.com/wordpress-hook-system/)

## 循环

循环是 WordPress 请求周期的基本部分。正如 WordPress Codex 所说:

> **循环**是 WordPress 用来显示帖子的 PHP 代码。使用循环，WordPress 处理每一篇要在当前页面上显示的文章，并根据它与循环标签中指定标准的匹配程度对其进行格式化。

WordPress 循环基本上是一个 PHP `while`循环，根据访问者的请求显示帖子，并在*模板文件*中指定标准。

在这个循环中，不管它输出的是一篇文章还是一个页面，还是一个列表，我们都可以输出文章的各个部分，比如标题和内容。我们可以指定任何我们想要的 HTML 输出或结构。我们也可以使用条件标签，等等。

最基本的循环示例可能如下所示:

```
if ( have_posts() ) {
    while ( have_posts() ) {
        the_post();
        //
        // Post Content here
        //
    } // end while
} // end if 
```

我们可以在一个页面上使用多个循环(在这种情况下，我们需要重置它们)，并且有特定的 WordPress 函数可以在其中使用。

## 条件标签

[WordPress 条件标签](https://codex.wordpress.org/Conditional_Tags)是 PHP 代码片段，使我们能够仅在满足特定条件时显示内容片段。例如，我们有站点范围的`header.php`，它被用在网站的每个页面上，但是我们可能希望某些 HTML 片段只显示在主页或其他页面上。

因此，在第一个例子中，我们将使用`is_front_page()`，它看起来像这样:

```
if (is_front_page()){
    # do something
} 
```

提供了大量的条件标签，它们允许在 WordPress 开发中有很大的灵活性。

这些是 WordPress 的构建模块，但是还有[分类法](https://codex.wordpress.org/Taxonomies)——可以是内置的或者我们可以指定的自定义分类法——以及这些分类法中的*术语*。我们用它们对我们的帖子进行分类和排序。

## 主题结构

在这一部分的最后，在我们努力创建我们自己的主题之前，我们将使用 [WP-CLI](https://www.sitepoint.com/wp-cli-introduction/) 工具来搭建一个基于[下划线](https://underscores.me/)的最小主题——由[automatic](http://automattic.com/)维护——来看一下典型的最小 WordPress 主题结构:

<video class="wp-video-shortcode" id="video-170061-4" width="1280" height="720" preload="metadata" controls=""><source type="video/mp4" src="https://uploads.sitepoint.com/wp-content/uploads/2018/11/1542071979s.mp4?_=4">[https://uploads.sitepoint.com/wp-content/uploads/2018/11/1542071979s.mp4](https://uploads.sitepoint.com/wp-content/uploads/2018/11/1542071979s.mp4)</video>

由此，我们可以了解如何构建我们的主题文件、文件夹等。

## 结论

在 WordPress 主题指南的第一部分，我们介绍了 WordPress 主题的术语和构建模块，我们将在下面的教程中使用它们来构建我们自己的 WordPress 主题。

* * *

这个系列中有三篇文章是关于从头开始构建 WordPress 主题的:

*   **理解主题的结构**
*   [主题基础知识](https://www.sitepoint.com/build-wordpress-theme-from-scratch-basics)
*   [提炼主题](https://www.sitepoint.com/build-wordpress-theme-from-scratch-final)

## 分享这篇文章**