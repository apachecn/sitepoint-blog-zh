# Sculpin 扩展:定制您的静态站点博客

> 原文：<https://www.sitepoint.com/sculpin-extended-customizing-your-static-site-blog/>

如果你是一名 PHP 开发人员，目前正在用 Octopress 或 Jekyll 之类的静态站点生成器运行一个博客，如果你能用你的主要语言来运行它，那不是很好吗？是的，对于我们开发人员来说，使用多种语言是健康的，但是说实话，我们经常想在博客中添加一些功能，但是在不熟悉的语法中很难完成。在本文中，我们将设置 Sculpin，一个 PHP 静态站点生成器。就像任何其他静态站点生成器一样，它使用 markdown 文件和 HTML 模板来生成您的博客，因此转换应该很容易。

![Sculpin logo](img/256dbb3dfff8d953619dcef857e8d589.png)

## 安装泄水孔

安装需要使用命令行和已安装的 PHP 现代版本(最好是 5.6 以上)。

```
wget https://download.sculpin.io/sculpin.phar 
```

下载完成后，我们会让它可执行:

```
chmod +x sculpin.phar 
```

然后，我们将它移到操作系统的可执行目录中:

```
sudo mv sculpin.phar /usr/local/bin/sculpin 
```

或者，您可以将它复制到您想要使用它的任何目录中。

执行`sculpin`将列出您可以使用的命令:

![sculpin commmands](img/f06aaa7374f3d3c992c5f0f1545a141c.png)

## 创建博客

现在你已经安装了 Sculpin，我们可以创建一个新的博客。我们将使用由 Sculpin 团队提供的 [Sculpin 博客框架。克隆存储库并将其下载到`myblog`中。](https://github.com/sculpin/sculpin-blog-skeleton)

```
git clone https://github.com/sculpin/sculpin-blog-skeleton.git myblog 
```

一旦克隆完成，在`myblog`中导航并执行`sculpin install`。这将安装 Sculpin 运行所需的所有依赖项。

```
cd myblog
sculpin install 
```

这个命令在幕后使用了[作曲](https://getcomposer.org/)(你可以打开`sculpin.json`文件查看相似之处)。Sculpin 实际上捆绑了 Composer 的一个替代版本，因此在它之外不需要 Composer。如果你需要安装第三方软件包，只需将它们添加到`require`对象中，然后执行`sculpin install`来安装它们。默认情况下，软件包安装在`source/components`目录中。

一旦 Sculpin 完成了包的安装，执行下面的命令来生成站点。`watch`选项意味着一旦您更改了任何配置文件或`source`目录中的文件，站点将自动重新生成。`server`选项运行一个服务于生成的站点的服务器。默认情况下，这可以在`http://localhost:8000`访问，但是如果您使用的是一个好的 VM 环境，设置将会根据您打开的端口而有所不同。看[这篇关于 Spress 的帖子，一个雕塑替代品](https://www.sitepoint.com/building-an-spress-svbtle-theme-responsive-static-blogs/)来学习如何在 VM 中设置它。

```
sculpin generate --watch --server 
```

生成的站点将存储在`output_dev`文件夹中。这仅用于开发目的。如果你已经打算将你的博客部署到类似于 [Github 页面](https://pages.github.com/)的地方，你必须指定`env`选项:

```
sculpin generate --env prod 
```

这将创建一个`output_prod`目录，然后您可以将它推送到 Github 页面。我们将在后面的部分中讨论这一点。

## 配置 Sculpin

我们可以通过查看`app/config`目录中的两个文件来配置 Sculpin:

*   允许我们编辑全局选项，如博客标题和我们的 disqus 用户名。
*   允许我们指定 Sculpin 使用的主题和永久链接格式。

其他配置文件，比如 Amazon S3 bucket deployment 的配置文件，位于这个目录的根目录下。在本教程中，我们不会真的使用亚马逊 S3，所以我们可以让它保持原样。

您可以通过查看[配置文档](https://sculpin.io/documentation/configuration/)找到更多关于如何配置 Sculpin 的信息。

## 博客基础

如果你来自 Octopress 或 Jekyll，你会觉得 Sculpin 就像在家里一样，因为它使用 markdown 文件来发布博客文章，只是你必须使用更标准的`.md`作为文件扩展名，而不是`.markdown`。另一件需要注意的事情是，Sculpin CLI 没有生成新的 markdown 文件来存放博客文章的命令。这意味着你必须使用`touch`命令来创建一个新文件:

```
touch 2016-05-17-getting-started-with-sculpin.md 
```

不过，把它打出来真的很痛苦，所以这里有一个`touch.php`文件，你可以把它放在你的博客目录的根目录下:

```
<?php
$date = date('Y-m-d');
if(!empty($argv[2])){
    $date = $argv[2];
}
$file = 'source/_posts/' . $date . '-' . str_replace('_', '-', $argv[1]) . '.md';

$title = ucwords(str_replace('_', ' ', $argv[1]));
$handle = fopen($file, 'w');
$data = "---
title: {$title}
tags: []
categories: []

---

";
fwrite($handle, $data); 
```

用这个。您可以执行以下命令来创建新的博客文章:

```
php touch.php getting_started_with_sculpin 
```

它将创建一个`source/_posts/2016-05-17-getting-started-with-sculpin.md`文件。

如果您想要一篇过期的博客文章，可以指定一个日期作为第二个参数:

```
php touch.php getting_started_with_sculpin 2017-01-01 
```

这也将为您添加样板首页内容:

```
---
title: Getting Started With Sculpin
tags: []
categories: []

--- 
```

## 自定义网站

现在是时候定制网站，使它看起来正是我们想要的。下面是最终输出的样子:

![Sculpin blog sample output](img/ef4d472126b32407c491ef41552d737c.png)

我们将主要在`source`目录中工作，所以继续并输入它。在那里，打开`index.html`文件。这是 Sculpin 为网站主页提供的文件。在前几行中，您将看到 meta 块，它用于指定布局、页面标题、页面中使用的生成器以及每页显示多少文章。

```
---
layout: default
title: Home
generator: pagination
pagination:
    max_per_page: 3
--- 
```

如果你以前使用过 Twig、Blade 或任何其他模板引擎，布局应该非常熟悉。`index.html`继承自`default`布局。布局存储在`_layouts`文件夹中。模板使用 html 文件，所以实际的文件应该是`_layouts/default.html`。打开该文件，清除其内容并粘贴以下内容:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">

    <title>{% block title %}{{ page.title }}{% endblock %} &mdash; {{ site.title }} &mdash; {{ site.subtitle }}</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/picnic/4.1.2/picnic.min.css">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/picnic/4.1.2/plugins.min.css">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/9.4.0/styles/solarized-dark.min.css">
    <link href="{{ site.url }}/css/style.css" rel="stylesheet" type="text/css" />

    <script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/9.4.0/highlight.min.js"></script>
    <script>hljs.initHighlightingOnLoad();</script>
</head>
<body>
    <header>
        <nav>
          <a href="/" class="brand">
            <img class="logo" src="{{ site.url img/jackson/76x76.png" />
            <span>{{ site.title }}</span>
          </a>

          <input id="bmenub" type="checkbox" class="show">
          <label for="bmenub" class="burger pseudo button">menu</label>

          <div class="menu">
            <a href="{{ site.url }}/blog">Archives</a>
            <a href="{{ site.url }}/about">About</a>
          </div>
        </nav>
    </header>

    <div id="wrapper">    
        <main>
        {% block content_wrapper %}
            {% block content %}{% endblock %}
        {% endblock %}       
        </main>

        <footer class="container">
            &copy; {{ "now"|date("Y") }} {{ site.title }}
        </footer>
    </div>
</body>
</html> 
```

这是我们将使用的主要布局。现在只需关注`<main>`标签内的代码。剩下的我们以后再去除雾。

只有`<main>`标签内的内容会改变。它的意思是:渲染名为`content_wrapper`的块，在其内部渲染名为`content`的块。这是 Twig 真正出彩的地方，因为如果我们定义一个外部块(`content_wrapper`)，我们可以覆盖内部块(`content`)的内容。当我们稍后查看`_views/post.html`文件时，我们将看到这一点。现在，只要记住`content_wrapper`或`content`块应该在将从该模板继承的模板中定义。

```
<main>
{% block content_wrapper %}
    {% block content %}{% endblock %}
{% endblock %}     
</main> 
```

我们之前已经确定`index.html`继承自`layouts/default.html`。但是如果你打开`index.html`文件，你会发现我们实际上并没有定义一个`content`块。这是因为 Sculpin 认为您在 meta 块之后添加的任何内容都是内容。那里有默认的内容，由框架站点添加，但是我们将使用下面的内容。让我们用以下内容替换默认设置:

```
{% for post in page.pagination.items %}
    <article>
        <header>
            <h2><a href="{{ site.url }}{{ post.url }}">{{ post.title }}</a></h2>
            <span class="date">{{ post.date|date('M dS, Y') }}</span>
        </header>
        <div>

        {% set break_array = post.blocks.content|split('<!-- more -->', 2) %}

        {{ break_array[0]|raw }}

        {% if break_array|length > 1 %}
        <p>
            <a href="{{ site.url }}{{ post.url }}">
                Read more
            </a>
        </p>
        {% endif %}

        </div>
    </article>
{% endfor %}

{% if page.pagination.previous_page or page.pagination.next_page %}
    <div class="nav-container"> 
        {% if page.pagination.previous_page %}
        <a href="{{ site.url }}{{ page.pagination.previous_page.url }}" class="right">Newer Posts</a>
        {% endif %}
        {% if page.pagination.next_page %}
        <a href="{{ site.url }}{{ page.pagination.next_page.url }}" class="left">Older Posts</a>
        {% endif %}
    </div>
{% endif %} 
```

这就把我们带到下一个话题:[发电机](https://sculpin.io/documentation/generators/)。Sculpin 有一个现成的名为 pagination 的生成器，它允许我们轻松地对集合进行分页。在这种情况下，集合由我们当前在`source/_posts`目录中的文章组成。下面的代码根据当前页码遍历文章。在索引页面上，这将只返回最后三篇文章(文章从最新到最早排序)。

```
{% for post in page.pagination.items %}
<article>
    <!-- contents for each post here -->
</article>
{% endfor %} 
```

要显示分页链接:

```
{% if page.pagination.previous_page or page.pagination.next_page %}
    <div class="nav-container"> 
        <!-- pagination links here -->
    </div>
{% endif %} 
```

每页显示的文章数量可以通过更新当前使用的模板中的`max_per_page`字段的值来指定。

```
pagination:
    max_per_page: 3 
```

回到我们为分页循环的每次迭代显示的代码，我们有以下内容:

```
<header>
    <h2><a href="{{ site.url }}{{ post.url }}">{{ post.title }}</a></h2>
    <span class="date">{{ post.date|date('M dS, Y') }}</span>
</header>
<div>

{% set break_array = post.blocks.content|split('<!-- more -->', 2) %}

{{ break_array[0]|raw }}

{% if break_array|length > 1 %}
<p>
    <a href="{{ site.url }}{{ post.url }}">
        Read more
    </a>
</p>
{% endif %}

</div> 
```

这将显示文章的标题、内容和一个*阅读更多*链接。*阅读更多内容*链接是通过创建一个名为`break_array`的变量来实现的，该变量将文章的前半部分和后半部分存储为两个独立的数组项。这将寻找作为分隔符的`<!-- more -->`注释。HTML 注释在页面中是不可见的，所以它是完美的分隔符。

```
{% set break_array = post.blocks.content|split('<!-- more -->', 2) %} 
```

接下来，我们渲染`break_array`的第一个索引中的内容。使用`raw`过滤器是为了让 HTML 被呈现而不是被转义。

```
{{ break_array[0]|raw }} 
```

然后我们检查在`break_array`中是否存储了不止一个条目，在这种情况下，我们提供一个链接来查看整篇文章。

```
{% if break_array|length > 1 %}
<p>
    <a href="{{ site.url }}{{ post.url }}">
        Read more
    </a>
</p>
{% endif %} 
```

既然我们已经弄清楚了索引页面是如何呈现的，让我们回到`_layouts/default.html`文件。对于主样式表，我们使用 [Picnic.css](http://picnicss.com) 。我们从 CDN 加载它，但是你也可以下载文件到你的`source/css`目录并链接到它们。

```
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/picnic/4.1.2/picnic.min.css">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/picnic/4.1.2/plugins.min.css"> 
```

作为开发人员，我们的博客文章中大多有代码片段，所以我们为 [highlight.js](https://highlightjs.org) 链接了日光化的深色主题，以增加语法高亮显示。

```
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/9.4.0/styles/solarized-dark.min.css"> 
```

最后，我们将自定义样式表保存在`source/css`下。

```
<link href="{{ site.url }}/css/style.css" rel="stylesheet" type="text/css" /> 
```

以下是`style.css`文件的内容:

```
#wrapper {
    width: 900px;
    margin: 0 auto;
}

nav .menu {
    float: left;
    margin-left: 20px;
}

nav .menu a {
    margin-left: 20px;
}

main {
    margin-top: 80px;
}

article {
    margin-top: 50px;
    border-bottom: 1px dashed #ccc;
    color: #1D1D1D;
}

footer {
    margin: 20px 0;
    text-align: center;
    color: #525252;
}

.right {
    float: right;
}

.left {
    float: left;
}

.nav-container {
    overflow: auto;
    padding: 30px 0;
}

article h2 {
    margin-bottom: 0;
    padding-bottom: 0;
}

.date {
    color: #565656;
    font-size: 15px;
}

pre {
    background: none;
}

@media screen and (max-width: 780px){
    #wrapper {
        width: 100%;
        padding: 10px;
    }
} 
```

在自定义样式表(`css/style.css`)链接的正下方，我们有`highlight.js`脚本和初始化代码，它们将应用语法高亮显示。

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/highlight.js/9.4.0/highlight.min.js"></script>
<script>hljs.initHighlightingOnLoad();</script> 
```

### 个人帖子页面

既然我们已经完成了索引页面，那么是时候继续处理个人文章页面的模板了。有问题的文件是`_views/post.html`。让我们输入以下内容:

```
{% extends "default" %}

{% block content_wrapper %}
    <article>
        <header>
            <h2>{{ page.title }}</h2>
            <span class="date">{{ post.date|date('M dS, Y') }}</span>
        </header>
        <div class="contents">
            {{ page.blocks.content|raw }}
        </div>
        {% if page.tags %}
            <p class="tags">
            Tags:
            {% for tag in page.tags %}
            <a href="{{ site.url }}/blog/tags/{{ tag|url_encode(true) }}">{{ tag }}</a>{% if not loop.last %}, {% endif %}
            {% endfor %}
            </p>
        {% endif %}
    </article>
{% endblock %} 
```

这主要基于 Sculpin 博客框架中的个人帖子模板，添加了日期，删除了类别和分页链接。

您可能已经注意到，我们使用了不同的语法来扩展默认模板:

```
{% extends "default" %} 
```

这是因为`post`被认为是布局。在 Sculpin 中，你放在这些目录中的任何东西:`_views`、`_layouts`、`_partials`、`includes`都被认为是布局。这意味着任何模板都可以从这些文件继承，或者包含部分内容。

我们提到了`content_wrapper`；我们试图在`_layout/default.html`文件中渲染的块。里面是`content`区块:

```
<main>
{% block content_wrapper %}
    {% block content %}{% endblock %}
{% endblock %}     
</main> 
```

我们已经知道`content`块不需要显式定义，因为 Twig 假设元块下面的任何内容都是内容。这意味着我们实际上只能在`_views/post.html`中使用下面的代码，并且帖子仍然会被呈现:

```
{% extends "default" %} 
```

但这只会呈现文章的内容，所以标题、日期和任何其他我们想要包含的关于文章的信息都不会呈现。

然而，如果我们试图像这样覆盖`content`块的内容:

```
{% extends "default" %}

{% block content %}
<h1>I'm trying to override the content!</h1>
{% endblock %} 
```

…我们仍然可以看到帖子的内容。

这意味着我们不能真正覆盖`content`块中的内容。这就是`content_wrapper`块发挥作用的地方。通过定义这个块，我们可以覆盖`content`块。这是因为`content_wrapper`块包裹着`content`块。现在我们可以输出标题和出版日期。

```
{% block content_wrapper %}
    <article>
        <header>
            <h2>{{ page.title }}</h2>
            <span class="date">{{ post.date|date('M dS, Y') }}</span>
        </header>
        <div class="contents">
            {{ page.blocks.content|raw }}
        </div>
        {% if page.tags %}
            <p class="tags">
            Tags:
            {% for tag in page.tags %}
            <a href="{{ site.url }}/blog/tags/{{ tag|url_encode(true) }}">{{ tag }}</a>{% if not loop.last %}, {% endif %}
            {% endfor %}
            </p>
        {% endif %}
    </article>
{% endblock %} 
```

### 添加问题

添加用于处理注释的 [Disqus](https://disqus.com/) 非常容易，因为它内置于框架中:

```
{% if site.disqus.shortname and site.disqus.shortname != '' %}
<div id="disqus_thread"></div>
<script type="text/javascript"> /* * * CONFIGURATION VARIABLES: EDIT BEFORE PASTING INTO YOUR WEBPAGE * * */
    var disqus_shortname = '{{site.disqus.shortname}}'; // required: replace example with your forum shortname

    {% if page.disqus.identifier  %}var disqus_identifier = '{{page.disqus.identifier}}'; {% endif %}

    {% if page.disqus.title %}var disqus_title = '{{page.disqus.title}}';{% endif %}

    {% if page.disqus.url %}var disqus_url = '{{page.disqus.url}}';{% endif %}

    {% if page.disqus.category_id %}var disqus_category_id = '{{page.disqus.category_id}}';{% endif %}

    /* * * DON'T EDIT BELOW THIS LINE * * */
    (function () {
        var dsq = document.createElement('script');
        dsq.type = 'text/javascript';
        dsq.async = true;
        dsq.src = '//' + disqus_shortname + '.disqus.com/embed.js';
        (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq);
    })(); </script>
<noscript>Please enable JavaScript to view the
    <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by Disqus.</a>
</noscript>
{% endif %} 
```

为此，我们必须用 Disqus 短名称更新`app/config/sculpin_site.yml`文件:

```
# Insert your disqus shortname
disqus:
   shortname: YOUR_DISQUS_USERNAME 
```

### 博客档案

就像每个博客一样，我们的博客也需要一个存档页面，读者可以在这里看到所有以前发表的文章。在`blog.html`中，添加以下内容:

```
---
layout: default
title: Posts Archive
generator: pagination
use:
    - posts

---
{% set year = '0' %}
<h2>Posts Archive</h2>
{% for post in page.pagination.items %}
{% set this_year %}{{ post.date | date("Y") }}{% endset %}
{% if year != this_year %}
  {% set month = '0' %}
  {% set year = this_year %}
{% endif %}
{% set this_month %}{{ post.date | date("F") }}{% endset %}
{% if month != this_month %}
  {% set month = this_month %}
  <h3>{{ month }} {{ year }}</h3>
{% endif %}
  <div>
    <a href="{{ site.url }}{{ post.url }}">{{ post.title }}</a>
  </div>
{% endfor %}

<div>
{% if page.pagination.previous_page or page.pagination.next_page %}
    <div class="nav-container"> 
    {% if page.pagination.previous_page %}
    <a class="previous" href="{{ site.url }}{{ page.pagination.previous_page.url }}" title="Previous Page"><span class="title">Previous Page</span></a>
    {% endif %}
    {% if page.pagination.next_page %}
    <a class="next" href="{{ site.url }}{{ page.pagination.next_page.url }}" title="Next Page"><span class="title">Next Page</span></a>
    {% endif %}
    </div>
{% endif %}
</div> 
```

我们再次使用熟悉的语法。只是这一次我们没有指定每页需要多少项目。Sculpin 将使用默认值:10 个项目。

```
---
layout: default
title: Posts Archive
generator: pagination
use:
    - posts
--- 
```

接下来，我们分解以下代码:

```
{% for post in page.pagination.items %}
{% set this_year %}{{ post.date | date("Y") }}{% endset %}
{% if year != this_year %}
  {% set month = '0' %}
  {% set year = this_year %}
{% endif %}
{% set this_month %}{{ post.date | date("F") }}{% endset %}
{% if month != this_month %}
  {% set month = this_month %}
  <h3>{{ month }} {{ year }}</h3>
{% endif %}
  <div>
    <a href="{{ site.url }}{{ post.url }}">{{ post.title }}</a>
  </div>
{% endfor %} 
```

上面的代码做了和我们之前在`index.html`文件中做的一样的事情。也就是说，它遍历当前页面中的所有条目，只是这次我们根据月份和年份对每篇文章进行分组。

## 部署到 Github 页面

要部署到 Github 页面，您将需要一个 [Github 帐户](https://github.com/),如果您还没有，请继续注册。然后，[设置 Git](https://help.github.com/articles/set-up-git/) ，[生成 SSH 密钥](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)，然后[将 SSH 密钥添加到你的 Github 账户](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)。如果你对这一切都不熟悉，我推荐你看看 Shaumik 关于初学者 Git 的文章。

一旦你建立了一个 Github 帐户，[使用下面的名称格式创建一个新的存储库](https://github.com/new):

```
your_github_username.github.io 
```

如果你的用户名是 **captain** ，那么库名应该是 **captain.github.io** 。这几乎是 Github 页面的标准，这也意味着每个帐户只能创建一个 Github 页面。

接下来，转到博客的根目录，执行以下操作:

```
sculpin generate --env prod 
```

这将生成一个用于生产的站点。生成的站点将存储在`output_prod`目录中。一旦生成，您可以继续删除`.git`目录。如果你还记得以前，我们已经克隆了杜父鱼的博客骨架。我们不会真的向存储库贡献代码，这样我们就可以删除它的本地存储库。

我们现在需要在`output_prod`文件夹中初始化一个新的 git 存储库。在目录中导航并执行`git init`。然后，执行以下命令提交更改:

```
git add -A
git commit -m "create new blog" 
```

将您之前创建的 Github 存储库添加为远程存储库:

```
git remote add origin git@github.com:your_github_username/your_github_username.github.io.git 
```

最后，推送至存储库:

```
git push origin master 
```

等待几分钟，你的 Sculpin 博客就会在`http://your_github_username.github.io`开始运行。恭喜你。

## 结论

在本教程中，我们研究了 Sculpin，一个基于 PHP 的静态站点生成器。如您所见，Sculpin 对于您的静态站点生成需求来说是一个非常好的选择。你可以在这个 [Github repo](https://github.com/sitepoint-editors/hello-sculpin) 中访问本教程使用的代码。

你在你的 Sculpin 博客上做了大量定制吗？有没有其他静态站点生成器解决方案是你更喜欢的？让我们知道！

## 分享这篇文章