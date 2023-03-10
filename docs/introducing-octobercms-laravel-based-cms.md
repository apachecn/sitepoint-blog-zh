# OctoberCMS 简介——基于 Laravel 的 CMS

> 原文：<https://www.sitepoint.com/introducing-octobercms-laravel-based-cms/>

十月 CMS 是一个轻量级的，回归基础的内容管理系统，建立在 Laravel 的基础上，旨在使你的 web 开发工作流程变得简单。它有一个非常简单快速的学习曲线，保证你马上就可以离开地面，开始运行。通过插件系统，它是可伸缩的和可扩展的，通过基于文件的系统，它很容易维护，并允许轻松创建管理后端接口。在我们更深入地挖掘这个有前途的 CMS 之前，让我们先看一下它的基础。

![october](img/e9296e2849afaf76fc164c58feebce02.png)

## 基于 Laravel PHP 框架构建

Laravel PHP 框架是轻量级的、健壮的、优雅的。根据他们的说法，这是一个以创造性用户为中心构建的框架，将平凡的任务转化为轻松愉快的任务。Laravel 和 October CMS 的理念非常一致，看到 October 已经采用这种类型的框架也就不足为奇了。这种结合提供了简单、快速、优雅和手工风格编程的完美结合。现在让我们来看看如何设置我们的第一个十月 CMS 项目。

## 立即开始

十月对我们来说安装变得轻而易举。以下步骤将使你走上发展之路:

1.  要么[在 GitHub 上克隆他们的 repo](https://github.com/octobercms/october) ，要么[下载安装程序](https://github.com/octobercms/install/archive/master.zip)。推荐的方法是下载安装程序，因为它简单快捷，并允许您在一个易于遵循的逐步界面中在浏览器内进行一些配置。
2.  将文件解压缩到所需的本地目录，并配置本地服务器以匹配本地路径。
3.  建立一个新的数据库，并记下数据库名称。
4.  在浏览器中导航到`install.php`文件，按照屏幕上的说明操作，并输入您的配置变量。

按照提示，成功安装后删除安装文件，您就可以开始了。你现在可以导航到你的网站的前端，并探索管理区。让我们深入了解十月 CMS 的内部运作。

## 它是如何工作的–主题和模板

主题目录是任何新的 10 月 CMS 安装的最终输出的基础。该目录包含 CMS 运行所需的所有子目录和文件。默认情况下，十月 CMS 会给你一个演示主题。创建自己的目录就像创建一个新目录一样简单(想怎么命名就怎么命名)，在这个目录的根目录下创建一个名为`theme.yaml`的主题配置文件，以及一个包含页面文件的 pages 目录。现在，让我们探索演示主题，以掌握内部工作原理。

每个主题都由页面、部分、布局、内容文件和资产的子目录组成。这些目录中的每一个都可以包含子目录，从而简化大型项目。演示目录结构如下所示:

```
themes/
  demo/
    assets/
      css/
      images/
      javascript/
      ...
    content/
    layouts/
    pages/
    partials/
    theme.yaml
```

模板文件采用简单的结构，并利用了[分支标记](http://twig.sensiolabs.org/)。Twig 是一个 PHP 模板引擎，增强和加速了模板，并将常见的冗长任务简化为简单的代码片段。任何页面、布局和部分模板文件都有三个部分:

1.  配置
2.  PHP 代码
3.  小枝标记

配置部分的结构类似于 php.ini 文件，由两个`=`符号标记为完成，为 php 部分让路。PHP 部分对于任何模板文件都是可选的，通过再键入两个`=`符号也可以标记为完成。最后，Twig 标记包含模板文件的实际内容。这是一个模板文件可能的样子，取自他们的网站:

```
url = "/blog"
layout = "default"
==
function onStart()
{
  $this['posts'] = ...;
}
==
<h3>Blog archive</h3>
{% for post in posts %}
  <h4>{{ post.title }}</h4>
  {{ post.content }}
{% endfor %}
```

让我们更深入地了解一下每个模板文件和部分的用途。

### 页

页面文件有一个不言自明的标题——它们描述了你的站点页面。它们在配置部分采用三个参数:

1.  `url`–页面 url(必填)
2.  `title`–页面标题(必填)
3.  `layout`–页面布局，可能指向一个布局文件(可选)
4.  `description`–后端页面的描述(可选)

页面 url 可以接受参数，这取决于所讨论的页面。也可以指定 404 和错误页。下面是一个简单的页面布局示例:

```
url = "/"
title = "Welcome"
description = "The home page."
==
<h1>Welcome Home</h1>
```

你可以在第[页这里](https://octobercms.com/docs/cms/pages)阅读更多内容。

### 部分的

片段还有一个不言自明的名字——它们指的是包含部分代码的文件。分部文件非常强大，因为它们可以在页面、布局或其他分部中被调用和重用。您可以使用`{% partial %}`标签从另一个文件内部调用一个片段。部分仅支持一个配置参数:

1.  `description`–后端的部分描述(可选)

下面是一个部分(右边栏)调用另一部分(最近的帖子)的基本示例:

```
description = "Right sidebar."
==
<div class="sidebar">
  <aside>
    {% partial "recent-posts" %}
  </aside>
</div>
```

你可以在这里阅读片段[。](https://octobercms.com/docs/cms/partials)

### 布局

布局文件指定了页面的布局，这与我们所看到的前两种模板文件类型的自解释性质是一致的。它们是任何 10 月 CMS 项目的主要组成部分。它们有两个配置选项，都在后端接口中使用:

1.  `name`–后端布局文件的名称(可选)
2.  `description`–后端的布局文件描述(可选)

布局页面可能看起来像这样:

```
name = "Default"
description = "The default layout for our template"
==
<!doctype html>
<html>
  <body>
    {% page %}
  </body>
</html>
```

假设我们将这个布局文件命名为`default.htm`，并将其放在我们的`layouts`目录中。然后，页面可以像这样调用此布局:

```
title = "Welcome"
url = "/"
layout = "default"
description = "Welcome home!"
==
<h2>Welcome Home!</h2>
<p>...</p>
```

相当整洁！更多关于布局的信息请点击。

### 内容块

内容文件包含内容部分，可以从布局、页面或部分文件内部调用。内容文件支持三种扩展名:

1.  。htm(用于 HTML 标记)
2.  。txt(用于纯文本)
3.  。md(用于降价)

通过运行`{% content %}`标签，可以使用 twig 标记调用内容文件。下面是一个呈现几个内容块的页面示例:

```
<div class="sidebar">
  <aside class="recent-posts">
    {% content "einstein-quote.htm" %}
  </aside>
  <aside class="newsletter">
    {% content "newsletter.htm" %}
  </aside>
</div>
```

点击查看更多内容文件[。](https://octobercms.com/docs/cms/content)

## AJAX 模块

10 月 CMS 内置了一个非常健壮的 AJAX 框架，允许您非常容易地发出 AJAX 请求并与许多数据属性进行交互。要添加 AJAX 框架，只需将`{% framework %}`标签放在页面或布局中的任意位置。它需要 jQuery(应该在框架之前加载)，所以您的页面可能如下所示:

```
<script src="{{ [
    'assets/js/jquery.min.js',
]|theme }}"></script> 

{% framework %}
```

AJAX 请求通过调用服务器上的事件处理程序来工作。然后，该事件处理程序可以使用片段更新页面元素。我们可以使用两个 API 来执行 AJAX 任务:

### 数据属性 API

这个 API 允许您在没有任何 JavaScript 的情况下发出 AJAX 请求(没有 JavaScript API 那么冗长)。这是他们网站上使用数据属性 API 的一个例子。请求成功后，它会将用户重定向到另一个页面:

```
<form data-request="onLogin" data-request-redirect="/admin">
```

简单高效。到目前为止，我在这里看到的唯一缺点是可供我们使用的数据属性列表有限。然而，JavaScript API 提供了一个更强大的选项。

### JavaScript API

这个 API 比数据属性 API 更强大，因为它允许我们使用与 jQuery AJAX 函数兼容的任何选项和方法。我们可以对表单内部或表单本身的任何元素使用`request()`方法。`request()`方法只有一个必需的参数——处理程序名。这里有一个例子:

```
<form onsubmit="$(this).request('onProcess'); return false;">
```

通过这种方法，我们获得了更多的灵活性和控制力。关于 AJAX 模块的更多信息，请查看这里的文档。

## 易扩展性

十月 CMS 很容易通过插件和组件扩展。根据文件:

> 插件是通过扩展为 CMS 添加新功能的基础…组件是可以附加到任何页面或布局的构建模块。

让我们来详细了解一下这些特性。

### 插件

插件是扩展十月 CMS 的基础。他们可以定义组件，添加后端页面，交互和编辑其他插件的功能，等等。它们很容易描述和设置，并且位于`/plugins`目录中。要开始插件开发，请访问[插件文档](https://octobercms.com/docs/plugin/registration)，或者关注一两天后 SitePoint 上即将发布的教程。

### 成分

组件为我们提供了可配置的构建块，它们可以附加到任何页面、部分页面或布局上。它们通过以下方式扩展了前端页面的行为:

1.  通过参与页面执行周期来注入变量
2.  处理页面触发的 AJAX 事件
3.  使用片段提供基本标记

要了解更多关于组件的信息，请查看文档[此处](https://octobercms.com/docs/cms/components)。

## 包裹

十月 CMS 自己的哲学贯穿于他们的文档中。开始使用是一个非常简单的过程，让你立刻对 CMS 感到舒适。它背后的两个家伙，Alexey Bobkov 和 Samuel Georges，致力于简化 web 开发和部署，对我来说，这是这个项目的一个巨大贡献。

如果我能找到一件不喜欢它的事情，那就是 AJAX 框架依赖于 jQuery。这并不是说它不健壮、功能不强大——事实上，我个人认为 jQuery AJAX 功能非常棒。无论如何，如果你愿意的话，实现你自己的一套 JS 应该不会有太大的问题。除此之外，我真的对这个 CMS 感到兴奋，并已经计划在即将到来的项目中使用它。

## 分享这篇文章