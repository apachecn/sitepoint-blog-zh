# 剖析一个幽灵博客主题

> 原文：<https://www.sitepoint.com/anatomy-ghost-blog-theme/>

Ghost 是一个开源博客平台，由作家为作家而建。Ghost 旨在让您专注于您的内容，而不是使用哪个插件，它非常简洁。你不会找到 [jQuery](http://jquery.com/) 导航栏或滑块。你会发现这是一个简单易用的博客系统，把你的内容放在第一位。

Ghost 还使用 Markdown 来格式化你的文章，这也使得非开发者更新他们的博客变得简单。许多人想写博客，但是被像 [WordPress](https://wordpress.com/) 这样的产品范围所吓倒。如果你想相对快速地建立并运行一个博客，Ghost 可能适合你。

这种简单的设计也使得整合你喜欢的框架和库变得容易。想用[基础](http://foundation.zurb.com/index.html)还是[自举](http://getbootstrap.com/)组件？jQuery 或者[砖石](http://masonry.desandro.com/)怎么样？Ghost 的缩小设计使得在自定义主题中使用第三方工具变得毫不费力。

## 装置

如果您的系统上安装了 Node.js，Ghost 的安装就很简单。即使你不知道 Node.js，在你选择的平台上安装也没有问题。如果没有安装 Node.js，可以[在这里](http://nodejs.org/download/)获取。安装节点后，前往[幽灵](https://ghost.org/)下载。

我想花点时间强调一下 Node.js 的使用带来的挑战。因为 Node 本质上是服务器端 JavaScript，所以您只能在可以使用 Ghost 的主机上使用。你需要一个虚拟服务器来运行 Ghost 标准主机，如果你只是想开一个简单的博客，这是一个昂贵的提议。最经济的选择是免费的亚马逊网络服务(T1)、T2 数字海洋(T3)或 T4 Ghost Pro(T5)。亚马逊是免费的，但需要配置一台服务器。数字海洋每月 5 美元，提供完全控制，同样需要配置服务器。Ghost Pro 选项，十块钱一个月，完全由 Ghost 团队管理，最容易上手运行。

### 第一步

创建一个名为 Ghost 的文件夹，并将 zip 文件解压到其中。打开节点命令提示符，并导航到解压缩文件的目录。运行以下命令安装 Ghost:

```
npm install --production
```

安装后，转到 Ghost 文件夹，打开`config.js`文件，将下面一行改为您站点的 URL:

```
url: 'http://my-ghost-blog.com',
```

如果你没有域名，你可以使用你的 IP 地址。您还可以更改主机和端口，尽管这不是 Ghost 正常工作所必需的。更改这些设置后，键入以下命令:

```
npm start
```

只有当您在 Ghost 文件夹中时，这才会起作用。Ghost 将启动并让您知道它正在监听的域/IP 地址和端口(应该与您在`config.js`中设置的相同)。

## 创建主题

开箱即用，鬼来了基本卡斯帕主题。Casper 是为你自己的主题设计的一个起点。最终你会想要设计自己的主题。对您可以构建的内容的唯一限制是 Ghost 处理组成您的博客帖子的数据的方式。

导航到 Ghost 文件夹中的`\content\themes`文件夹。在那里你会看到一个 Casper 主题的文件夹。创建一个自定义主题就像创建一个以你的主题命名的文件夹一样简单。在你的主题文件夹中创建一个 post.hbs 和 index.hbs 文件，你就有了一个主题，尽管它在这一点上不是很有用。让我们更深入地看看组成主题的文件。

### package.json

```
{
    "name": "Theme",
    "version" : "1.0"
}
```

Ghost 最终将需要 package.json 文件。它基本上告诉鬼你的主题的名称和版本。

### 默认. hbs

```
<!DOCTYPE html>
<html>
<head>
    {{! Document Settings }}
    <meta http-equiv="Content-type" content="text/html" charset="UTF-8" />

    {{! Page Meta }}
    <title>{{meta_title}}</title>
    <meta name="description" content="{{meta_description}}" />
    <meta name="HandheldFriendly" content="True" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />

    {{! Stylesheets }}
    <link rel="stylesheet" type="text/css" href="/assets/css/pig.css" />
    <link href='http://fonts.googleapis.com/css?family=Changa+One' rel='stylesheet' type='text/css'>

    {{! Ghost outputs important style and meta data with this tag }}
    {{ghost_head}}
</head>
<body class="{{body_class}}">
    <div class="pagewrapper">
    {{>header}}
    <div>
    {{{body}}}
    </div>
    {{> footer}}
    {{! Ghost outputs important scripts and data with this tag }}
    {{ghost_foot}}
    </div>    
    {{! Javascript files here }}
</body>
</html>
```

尽管您只需要文件夹中的 post.hbs 和 index.hbs 文件就可以拥有一个主题，但是将主题结合在一起的粘合剂是 default.hbs。该文件是所有页面的主模板，所有页面都将作为 default.hbs 的一部分加载。hbs 是与[手柄](http://handlebarsjs.com/)模板引擎一起使用的文件扩展名。

```
{{! Document Settings }}
```

通常双花括号内的标签是把手标签。在这种情况下，感叹号使这成为一个注释。

```
<title>{{meta_title}}</title>
```

一个 Handlebars 标签，这次提供页面的标题。

```
{{ghost_head}},{{ghost_foot}}
```

这是一个特殊的 Ghost 标签，用于输出样式、元数据和脚本。

```
<body class="{{body_class}}">
```

这用于根据加载的内容输出类。

```
{{>header}}, {{> footer}}
```

用于输出部分文件，Handlebars 将在 partials 目录中查找这些文件。

```
{{{body}}}
```

页面上最重要的标签，这是内容将被加载的地方。还要注意三重花括号。这告诉把手不要转义 HTML 并按预期显示。

### index.hbs

```
{{!< default}}
{{#foreach posts}}
<article class="mbox">
    <div class="i-title">
        <h2><a href="{{{url}}}">{{title}}</a></h2>
        <time>
        {{date format='DD MMM YYYYY'}}
        </time>
    </div>
    <div class="list-post">
    <div class="i-pic">
        <p>{{content words="0"}}</p>
    </div>
    <div class="i-blurb">
        <p>{{excerpt}} ... <a href="{{{url}}}">Read More</a></p>
    </div>
    </div>
</article>
{{/foreach}}
```

index.hbs 是加载 Ghost 页面时显示的第一个页面，也是唯一接收所有帖子数据的页面。这意味着 index.hbs 是唯一一个不需要用 JavaScript 拼凑出一个解决方案就能获得帖子列表的页面。

```
{{!< default}}
```

上面的内容向 Handlebars 表明，该页面将从 default.hbs 加载到{{{body}}}标记中。

```
{{#foreach posts}}, {{/foreach}}
```

这是显示帖子列表的手柄块辅助对象。标签之间的内容将在每篇文章中重复。

```
{{{url}}}, {{title}},{{date format='DD MMM YYYYY'}}
```

URL、标题和发布日期的把手标签。

{ { content words = " 0 "}，{ {摘录}}

这些是为文章列表输出一篇文章的小片段的特殊标签。通常你只会使用其中的一个，但是我把两个都用来在文章列表中加入一张图片。“内容单词”标签将输出图片，但摘录不会。内容单词将输出您指定的任意数量的单词。当您选择 0 时，它只输出帖子附带的图片。“摘录”标签用于在文章中添加一个单独的文本区域，允许您单独设置图片和文本的样式。

### 后 hbs

```
{{!< default}}
{{#post}}
<article class="art-box">
    <div class="p-title">
        <h1><a href="/">&larr; Go back</a></h1>
        <h1>{{title}}</h1>
    </div>
    <div class="p-content">
        {{{content}}}
    </div>
        {{#if author}}
    <div class="p-writer">
        <p>Written by {{author}}</p>
    </div>
{{/if}}
</article>
{{/post}}
```

当你点击一篇文章时显示的页面，这个页面只能访问一篇文章的内容。

```
{{#post}}, {{/post}}
```

这告诉 Handlebars 该页面可以访问单个帖子。

```
{{#if author}},{{/if}}
```

这将检查文章是否有作者简介；如果是这样，它将与帖子一起显示。

### header.hbs

```
<div class="head-wrap" {{#if @blog.cover}}style="background: url({{@blog.cover}});"{{/if}}>
{{#if @blog.logo}}
    <div class="h-logo">
        <a class="h-logo-style" href="{{@blog.url}}">
        <img src="{{@blog.logo}}" alt="{{@blog.title}}">
        </a>
    </div>
    {{/if}}
    <div class="h-title">
        <h1 class="h-title-style">
        <a title="{{@blog.title}}" href='{{@blog.url}}'>{{{@blog.title}}}
        </a>
        </h1>
    </div>
    <div class="h-description">
        <h2 class="h-description-style">{{@blog.description}}</h2>
    </div>
    </div>
```

这是从 default.hbs 中的{{>header}}标记加载的文件。

```
<div class="head-wrap" 
     {{#if @blog.cover}}style="background: url({{@blog.cover}});"{{/if}}>
```

这个标签检查一个博客封面是否已经上传，如果是，它将它设置为背景。

```
{{#if @blog.logo}}, {{/if}}
```

这是用来输出博客标志，如果它已经上传。

```
<img src="{{@blog.logo}}" alt="{{@blog.title}}"
```

这将显示博客徽标。

```
<a title="{{@blog.title}}" href='{{@blog.url}}'>{{{@blog.title}}}
```

将博客标题显示为链接。

```
<h2 class="h-description-style">{{@blog.description}}</h2>
```

显示博客描述。

### footer.hbs

```
<div class="foot-box">
    <div class="f-social">
    <a href="http://www.facebook.com/sharer.php?u={{url absolute="true"}}" target="_blank">
        <img src="/asseimg/facebook.png">
    </a>
    <a href="https://plus.google.com/share?url={{url absolute="true"}}" target="_blank">
        <img src="/asseimg/google_plus.png">
    </a>
    <a href="http://twitter.com/share?text={{@blog.title}}&url={{url absolute="true"}}" target="_blank">
        <img src="/asseimg/twitter.png">
    </a>
    </div>
    <div class="f-cp">
    <p>© {{date format='YYYY'}} <a href="{{@blog.url}}">{{@blog.title}}</a></p>
    </div>
    <div class="f-ghst">
    <p>Runs on  <a href="http://ghost.org" target="_blank">Ghost</a></p>
    </div>
</div>
```

这个文件也可以在 partials 目录中找到，它从 default.hbs 中的{{> footer}}标签加载。没有你还没有见过的把手标签，这个页脚包含了用于共享的社交媒体图标。

## 设计主题

正如我提到的，幽灵是非常准系统的。我原本打算使用 Bootstrap 来设计页面，但是我决定使用我用 Sass 构建的一个小型网格系统。

```
@media screen and (max-width: 600px) {
    .mbox .list-post .i-pic {
        position: relative;
        min-height: 1px;
        padding-left: 0px;
        padding-right: 0px;
        float: left;
        width: 100%;
        background-color: #f2e7e7;
        border-bottom: 3px solid #090a0a;
    }
    .mbox .list-post .i-pic img {
        width: 100%;
        height: 100%;
    }
}

@media screen and (min-width: 601px) {
    .mbox .list-post .i-pic {
        position: relative;
        min-height: 1px;
        padding-left: 0px;
        padding-right: 0px;
        float: left;
        width: 33.33333%;
        left: 33.33333%;
        background-color: #f2e7e7;
        border-bottom: 3px solid #090a0a;
     }
}
```

在这个小片段中。css 文件中，我在本文中只包含了两个断点，但是在真实的应用程序中，我们会包含更多的断点。如您所见，一旦屏幕缩小到 600 像素，所有的列都将缩放到 100%。

## 结论

我只触及了 Ghost 的皮毛。如果你想开一个博客，并且有足够的资源，我建议你试一下 Ghost。记住它是由作家为作家建造的！

## 分享这篇文章