# Bolt CMS 中的记录检索和分页

> 原文：<https://www.sitepoint.com/record-retrieval-pagination-bolt-cms/>

Bolt 是一个轻量级的 CMS，构建在 Silex 上，带有 Symfony 组件，开发起来快速、简单、有趣。我最近对 Bolt 的喜爱已经使它成为我的 CMS 选择，因为我有意识地做出明智的选择，远离臃肿的框架。[在之前的](https://www.sitepoint.com/using-boltcms-build-small-business-website/)中，我非常详细地介绍了使用 Bolt 进行开发的情况。

![Bolt CMS](img/e4ab54df94494fd58127e40b0b4bcbd4.png)

今天，我们将把一个非常流行的任务分解成几个步骤，以便轻松完成它。我们要做的是:

1.  我们将为新闻文章创建一个内容类型，包括标题、摘录、特色图片和正文。
2.  我们将检索新闻文章，并以“博客”风格显示它们，包括分页。
3.  提要中的每篇新闻文章都将链接到其专用页面，在该页面上将显示整篇文章。
4.  在完整的文章页面上，我们将生成上一篇和下一篇文章的链接。

在我们深入讨论之前，让我们概述一下实现这一目标的途径。首先，我们将通过 Git 和 Composer 重新安装 Bolt。我们将使用一个基于 SQLite 文件的数据库，因为它已准备就绪，无需配置。为了快速制作前端模板，我们将从 [Bootswatch](http://bootswatch.com/) 引入一个免费的[boost trap](http://getbootstrap.com/css/)主题。Bolt 利用了[tweet](http://twig.sensiolabs.org/)，这是一个现代的 PHP 模板引擎，所以我们将使用 tweet 标记从头开始构建一个快速的主题。一旦我们都设置好了，我们将创建我们的内容类型，添加一些虚拟数据，并将其输出到页面上。我们将继续添加虚拟数据，直到我们有足够的数据来应用分页，然后我们将链接到单独的文章，并生成上一个和下一个链接。我们开始吧！

## 装置

作为一个环境，我们将使用我们自己的[家园改进版](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)，但是你可以自由使用你自己的本地配置。本文将在假设您已经全局安装了 Composer，并且已经将一个虚拟主机指向由下面的命令生成的适当文件夹的情况下继续。

为了安装，我们执行:

```
git clone git://github.com/bolt/bolt bolt
cd bolt
git checkout v2.0.6
composer install
```

在撰写本文时，版本 2.0.6 是稳定的分支。如果您看到任何权限错误，您需要通过运行以下命令来重置几个目录:

```
chmod -R 777 files/ app/database/ app/cache/ app/config/ theme/ extensions/
```

如果您现在在浏览器中访问 CMS 的界面，您应该会看到“创建用户”屏幕。这将在数据库中注册第一个用户，并授予您访问管理门户的权限，在这里我们可以编辑配置文件、设置内容类型、添加内容和切换主题。创建该用户，并通过转到`/bolt`登录到管理门户。查看前端——您应该看到 Bolt 的默认主题正在显示。让我们继续设置和构建我们自己的主题。

## 快速主题设置

因为我们不会太关注测试站点的整体视觉外观，所以我不会深入讨论前端的东西。然而，我们要做的是使用来自 Bootswatch 的 [Cosmo Bootstrap 主题快速创建我们自己的主题，并将我们的一些文件分成可重用的部分。在`theme`目录中，创建一个名为`my-theme`的新目录，并创建一个名为`index.twig`的新文件。让我们暂时把它留为空白，直接进入管理端。](https://bootswatch.com/cosmo/)

导航到`Configuration -> Main configuration`，四处看看。你网站的很多选项都可以在这里进行调整。如前所述，我们使用的是现成的 SQLite，所以不需要更改数据库设置。你可以随意编辑网站名称和收益，但是改变主题名称现在对我们来说更重要。向下滚动到主题部分，切换到`my-theme`。这就是我们在主配置文件中需要做的，所以保存它，让我们回到我们的`index.twig`文件。

使用 Bootstrap 的一些类，下面是我想出的让我们开始的方法(暂时不要使用这个代码，我们需要分解它):

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>My Site</title>
  <link rel="stylesheet" href="https://bootswatch.com/cosmo/bootstrap.min.css">
</head>
<body>

<header>
  <nav class="navbar navbar-default">
    <div class="container-fluid">
      <div class="navbar-header">
        <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1">
          <span class="sr-only">Toggle navigation</span>
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
        </button>
        <a class="navbar-brand" href="#">My Site</a>
      </div>
      <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
        <ul class="nav navbar-nav navbar-right">
          <li><a href="#">Home</a></li>
          <li><a href="#">News</a></li>
        </ul>
      </div>
    </div>
  </nav><!-- /nav -->
</header><!-- /header -->

<main>
  <div class="container">
    <h1>Welcome to my site</h1>
    <p class="lead">All the latest news, tips, and tricks for Bolt CMS.</p>
    <hr>
    <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Quae ratione veniam esse earum, voluptatem itaque in aut reprehenderit nostrum aspernatur minus. Iste iusto, id expedita esse? Dolorem, iusto, ipsam. Incidunt.</p>
  </div>
</main><!-- /main -->

<hr>

<footer>
  <div class="container">
    &copy; 2015, My Site
  </div>
</footer><!-- /footer -->

</body>
</html>
```

当然，这不是真正的可重用。我们要把它分成几个部分，这样我们就可以在其他模板中包含主要的块。因为我们的站点很简单，所以我们只需要两个:

*   `_header.twig`
*   `_footer.twig`

您可以随意命名您的分音，但是 Bolt 惯例是给名称加下划线来表示分音。下面是我们的`_header.twig`片段的样子:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>My Site</title>
  <link rel="stylesheet" href="https://bootswatch.com/cosmo/bootstrap.min.css">
</head>
<body>

<header>
  <nav class="navbar navbar-default">
    <div class="container-fluid">
      <div class="navbar-header">
        <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1">
          <span class="sr-only">Toggle navigation</span>
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
        </button>
        <a class="navbar-brand" href="#">My Site</a>
      </div>
      <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
        <ul class="nav navbar-nav navbar-right">
          <li><a href="#">Home</a></li>
          <li><a href="#">News</a></li>
        </ul>
      </div>
    </div>
  </nav><!-- /nav -->
</header><!-- /header -->

<main>
<div class="container">
```

这里是`footer.twig`部分:

```
</div>
</main><!-- /main -->

<hr>

<footer>
  <div class="container">
    &copy; 2015, My Site
  </div>
</footer><!-- /footer -->

</body>
</html>
```

现在，我们可以重新构建我们的`index.twig`文件和任何未来的模板，以包括如下的部分:

```
{% include '_header.twig' %}

<h1>Welcome to my site</h1>
<p class="lead">All the latest news, tips, and tricks for Bolt CMS.</p>
<hr>
<p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Quae ratione veniam esse earum, voluptatem itaque in aut reprehenderit nostrum aspernatur minus. Iste iusto, id expedita esse? Dolorem, iusto, ipsam. Incidunt.</p>

{% include '_footer.twig' %}
```

刷新你的浏览器，你会发现所有的都是完整的——只是我们现在使用了部分。在我们继续之前，您应该注意到有两种使用 Twig 生成模板的方法:

1.  第一种是使用 includes，就像我们上面做的那样。这是螺栓文档中的默认方法。
2.  第二种是使用[模板继承](http://twig.sensiolabs.org/doc/templates.html#template-inheritance)，它允许你构建基础框架，并让其他模板文件扩展它，将内容注入到各种块中。

对于本教程，我们将坚持使用默认的 includes 方法，但是我建议阅读和学习关于模板继承的内容，因为它非常强大。让我们继续创建内容类型。

## 创建内容类型(内容类型)

内容类型是任何 Bolt 网站或应用程序的核心。它们允许我们按照我们想要的方式构建内容结构，添加任意多的条目，将它们存储在我们的数据库中，对它们进行检索、排序、搜索等等。在我们的应用程序中，我们只需要一种内容类型:新闻。这将允许我们添加遵循特定格式的多个新闻文章。

在`Configuartion -> Contenttypes`下，您会注意到 Bolt 已经打包了三种内容类型来帮助您入门。现在让我们把这三个都去掉，这样我们就可以专注于构建我们自己的了。现在，请注意，我们希望以下内容与每篇新闻文章相关联:

*   一个标题
*   摘录
*   特色图片
*   一具尸体

我们还希望在查看单个新闻文章时使用特定的模板，而另一个模板用于检索所有的新闻文章。这些设置分别由以下两个参数定义:

*   `record_template`
*   `listing_template`

下面是我们的内容类型配置现在的样子:

```
# News

news:
    name: News
    singular_name: News Item
    slug: news
    singular_slug: news
    listing_template: news.twig
    record_template: newsitem.twig
    default_status: published
    fields:
        title:
            type: text
            class: large
        slug:
            type: slug
            uses: title
        image:
            type: image
        excerpt:
            type: textarea
            height: 150px
        body:
            type: html
            height: 300px
```

在这一点上，我们需要更新数据库来反映这些变化，所以转到`Configuration -> Check database`并点击更新按钮。现在你应该在侧边栏中看到`News`。继续之前，先补充一堆伪文章。我添加了 20 个——我知道这是一个过程，但是对于分页来说会很方便。我刚刚使用了 lorem ipsum 文本和一个 120×120 像素的虚拟图像。

## 检索所有记录

正如我们上面定义的，我们将使用`news.twig`模板文件来检索我们所有的新闻条目。在主题目录中创建文件，并像前面一样包含部分内容:

```
{% include '_header.twig' %}

<h1>News</h1>
<p class="lead">View all the news articles here.</p>
<hr>

{% include '_footer.twig' %}
```

这个模板现在可以通过 URL `/news`访问，所以我们可以把它硬编码到我们的导航中(菜单不在本教程的讨论范围之内)。在 Bolt 中，用于列表的页面总是有可用的`{{ records }}`变量。但是，因为我们将对内容进行分页，所以我们希望有更多的控制权。相反，我们将利用超级重要的 [`setcontent`标签](https://docs.bolt.cm/content-fetching)，它允许我们向想要检索的内容添加一些查询和规则。让我们循环所有的新闻文章，并用一些引导类输出它们:

```
{% include '_header.twig' %}

<h1>News</h1>
<p class="lead">View all the news articles here.</p>
<hr>

{% setcontent news = 'news' %}

{% for newsitem in news %}

<article class="media">
  <div class="media-left">
    <a href="#">
      <img src="{{ image(newsitem.image) }}" alt="{{ newsitem.title }}">
    </a>
  </div>
  <div class="media-body">
    <h2 class="media-heading">{{ newsitem.title }}</h2>
    <p class="media-excerpt">{{ newsitem.excerpt }}</p>
    <a href="#" class="btn btn-primary btn-sm">Read Full Article</a>
  </div>
</article>

{% endfor %}

<hr>

pagination

{% include '_footer.twig' %}
```

注意我们是如何根据 contenttype 配置中定义的字段名获取内容的。没有比这更简单的了。不过，我们还没有包含任何指向单个新闻条目的链接。让我们现在做那件事。Bolt 提供了一个助手函数来获取 contenttype 的单个记录的链接。我们可以像这样使用和实现它:

```
<a href="{{ newsitem.link }}">
  <img src="{{ image(newsitem.image) }}" alt="{{ newsitem.title }}">
</a>
```

我们现在可以在循环中更新所有的`href`属性，使每个记录都有工作链接。这里有一个清单现在应该是什么样子的截图。

![Listing of records](img/3dcef7ed86ad832aa6bf2cf68169c67a.png)

在我们开发单个新闻条目模板之前，让我们对记录进行分页。

## 翻阅记录

没有比这更简单的分页记录了。不再有依赖于你的 URL 结构而中断的令人困惑的函数，一点也没有。在我们的模板文件中，我们首先希望将每页的新闻条目数限制为 5。然后，我们希望输出分页链接。首先，我们需要更新`setcontent`标签来反映我们的需求:

```
{% setcontent news = 'news/latest/5' allowpaging %}
```

在下面的模板中，我们可以使用`pager`标签来输出分页链接。随着更改的实现，我们的模板现在应该如下所示:

```
{% setcontent news = 'news/latest/5' allowpaging %}

{% for newsitem in news %}

<!-- output loop here -->

{% endfor %}

{{ pager() }}
```

幸运的是，Bootstrap 的分页样式与 Bolt 的`pager`函数输出的 HTML 配合得很好，所以如果一切顺利，您应该有一个简单、有效的分页输出。这里有一个分页的截图。

![Pagination in action](img/cdca5c7948a6599a8090e84b1b7d902b.png)

简单又漂亮。我们继续吧。

## 检索和输出单个记录

到目前为止，您应该已经掌握了 Bolt 的运行方式。您可能会认为我们需要在我们的主题目录中创建一个`newsitem.twig`文件。如果没记错的话，这是我们在 contenttype 配置中指定的文件。让我们创建它，像以前一样拉我们的分音:

```
{% include '_header.twig' %}

{% include '_footer.twig' %}
```

之前，我提到过某些变量在记录或列表模板中是默认可用的。因为我们在这里查看的是单个记录，所以我们可以使用`{{ record }}`变量。记住这一点，我们不需要使用`setcontent`标签，因为它已经可以访问了。我们可以很容易地提取我们需要的数据，并将其输出到我们的模板中。为了提高效率，我使用了一些引导类和一点内联 CSS，下面是我想到的:

```
{% include '_header.twig' %}

<article>
  <header class="article__header">
    <h1>{{ record.title }}</h1>
    <p class="lead">{{ record.excerpt }}</p>
    <hr>
  </header>
  <div class="article__body">
    <img src="{{ image(record.image) }}" alt="{{ record.title }}" class="pull-left" width="120" height="120" style="margin-right: 12px;">
    {{ record.body }}
  </div>
</article>

{% include '_footer.twig' %}
```

打开浏览器，浏览你的任何新闻文章。现在，您应该会看到它们按照您的模板以一致的方式显示出来。厉害！有多简单？请查看下面的截图，查看单个记录列表。

![Single record listing](img/d3bbd142f56790502a453c2a8551c815.png)

我们还有最后一块拼图需要解决，你猜对了，这轻而易举。

## 添加上一个和下一个链接

给你的新闻文章添加上一个和下一个链接可能是你想要做的事情。它鼓励用户继续点击你的网站，最终让他们更容易地接触到更多的内容，并让他们停留更长的时间。在我们开始之前，让我们在文章下面设置一个快速导航部分，用于上一个和下一个链接。同样，我将使用一些引导样式来使它看起来更好。以下是编辑过的模板:

```
{% include '_header.twig' %}

<article>
  <!-- article loop here -->
</article>

<hr>

<nav class="row">
  <div class="col-md-6">
    <a href="#">Previous</a>
  </div>
  <div class="col-md-6 text-right">
    <a href="#">Next</a>
  </div>
</nav>

{% include '_footer.twig' %}
```

现在我们需要获取这些链接。正如您可能已经猜到的，Bolt 提供了从盒子中取出上一条和下一条记录的函数。我们要做的就是这个:

```
{% set prev = record.previous() %}
{% set next = record.next() %}
```

现在，我们可以完全访问上一条和下一条记录的内容。因此，我们可以更新我们的导航，输出如下所示的链接:

```
<nav class="row">
  <div class="col-md-6">
    <a href="{{ prev.link }}">&laquo; {{ prev.title }}</a>
  </div>
  <div class="col-md-6 text-right">
    <a href="{{ next.link }}">{{ next.title }} &raquo;</a>
  </div>
</nav>
```

我们就要完成了，但是我们有一件重要的事情要考虑。在第一个新闻项目页面上，没有“下一个”记录。同样，在最后一项中，将没有先前的记录。让我们运行一个条件来检查这一点:

```
<nav class="row">
  <div class="col-md-6">
    {% if (prev) %}
      <a href="{{ prev.link }}">&laquo; {{ prev.title }}</a>
    {% endif %}
  </div>
  <div class="col-md-6 text-right">
    {% if (next) %}
      <a href="{{ next.link }}">{{ next.title }} &raquo;</a>
    {% endif %}
  </div>
</nav>
```

为了完整起见，并展示我们使用 Bolt 开发模板和输出内容的简易性，下面是最终的`newsitem.twig`模板:

```
{% include '_header.twig' %}

<article>
  <header class="article__header">
    <h1>{{ record.title }}</h1>
    <p class="lead">{{ record.excerpt }}</p>
    <hr>
  </header>
  <div class="article__body">
    <img src="{{ image(record.image) }}" alt="{{ record.title }}" class="pull-left" width="120" height="120" style="margin-right: 12px;">
    {{ record.body }}
  </div>
</article>

<hr>

{% set prev = record.previous() %}
{% set next = record.next() %}

<nav class="row">
  <div class="col-md-6">
    {% if (prev) %}
      <a href="{{ prev.link }}">&laquo; {{ prev.title }}</a>
    {% endif %}
  </div>
  <div class="col-md-6 text-right">
    {% if (next) %}
      <a href="{{ next.link }}">{{ next.title }} &raquo;</a>
    {% endif %}
  </div>
</nav>

{% include '_footer.twig' %}
```

很好。现在，我们应该能够使用“上一篇”和“下一篇”链接浏览所有的新闻文章。下面是上一个和下一个链接的截图。

![Prev next links](img/fa4539b60c0d11883c25532cab897ed2.png)

## 包扎

随着网络在规模、范围、复杂性和观众数量上的不断发展，效率和速度扮演着越来越重要的角色。为了保持应用程序的精简，我们必须尽可能地去除多余的臃肿部分。除了是一个轻量级的和可靠的 CMS，让你决定你的网站的流程和操作，Bolt 还为前端和后端开发人员提供了一个流畅，简单的工作流程，当开发主题，内容类型，等等。在本教程中，我们看到了如何快速、轻松地按照我们想要的方式设置和访问内容。在开始下一个项目之前，认真问问自己，是否真的需要一个功能强大、功能丰富的 CMS，或者是否可以用 Bolt 来简化它。就我个人而言，我高度支持 Bolt，并在当前所有需要 CMS 的个人项目中使用它。反馈？评论？让我们知道！

## 分享这篇文章