# 构建静态站点生成器 Grunt 插件

> 原文：<https://www.sitepoint.com/building-static-site-generator-grunt-plugin/>

你可能听说过[静态站点发生器](https://www.sitepoint.com/static-site-generators/)像[杰基尔](http://jekyllrb.com/)和[温特史密斯](http://wintersmith.io/)，你甚至可能使用过它们。但是您可能会惊讶地发现，编写自己的静态站点生成器并不难。

在本教程中，我将向你展示如何构建你自己的 [Grunt](http://gruntjs.com/) 插件，它将从模板和 Markdown 文件中为你生成一个静态站点。然后，您可以将它与您希望创建静态站点的任何其他 Grunt 插件结合使用。

## 为什么使用 Grunt？

你可能会问，*为什么要用 Grunt 来做这个？*

*   如果没有别的，这将是一个学习如何创建自己的繁重任务的好方法。
*   它提供了对 Grunt 的 API 的访问，简化了很多任务。
*   将它构建为 Grunt 插件提供了很大的灵活性——您可以将它与其他 Grunt 插件一起使用，以获得您想要的工作流。例如，您可以选择任何您想要的 CSS 预处理器，或者您可以通过 Rsync 或 Github 页面进行部署，方法是更改您使用的其他插件并修改配置。我们的插件将只需要采取降价文件和模板，并生成 HTML。
*   你可以很容易地添加额外的功能作为插件——例如，我使用一个现有的 Grunt 插件来生成我的站点地图。
*   您可以对其进行编辑，以便与不同的模板系统一起工作。例如，我将使用[手柄](http://handlebarsjs.com/)作为我的模板系统，但是使用[翡翠](http://jade-lang.com/)来代替就太琐碎了。

## 设置事物

我们的第一步是安装创建插件框架所需的所有东西。我假设你已经安装了 [Git](http://git-scm.com/) 、 [Node.js](https://nodejs.org/) 和 [grunt-cli](http://gruntjs.com/using-the-cli) 。首先，我们需要安装`grunt-init`:

```
npm install -g grunt-init
```

接下来，安装`gruntplugin`模板:

```
git clone git://github.com/gruntjs/grunt-init-gruntplugin.git ~/.grunt-init/gruntplugin
```

现在，为你的插件创建一个文件夹，我称之为`grunt-mini-static-blog`。导航到该文件夹并运行以下命令:

```
grunt-init gruntplugin
```

你会被问到一些关于你的插件的问题，这些问题会被用来生成你的`package.json`文件。如果你还不知道回答什么，不要着急，用默认的就好；您可以稍后更新该文件。这个命令将为你的插件生成一个样板文件。

接下来，安装您的依赖项:

```
npm install
```

您还需要一些额外的节点模块来帮您完成一些繁重的工作:

```
npm install handlebars highlight.js meta-marked moment rss lodash --save-dev
```

## 生成帖子

我们的第一个任务是生成个人博客帖子。首先，让我们设置默认配置。打开`Gruntfile.js`并修改`mini_static_blog`的配置:

```
// Configuration to be run (and then tested).
mini_static_blog: {
default: {
options: {
data: {
author: "My Name",
url: "http://www.example.com",
disqus: "",
title: 'My blog',
description: 'A blog'
},
template: {
post: 'templates/post.hbs',
page: 'templates/page.hbs',
index: 'templates/index.hbs',
header: 'templates/partials/header.hbs',
footer: 'templates/partials/footer.hbs',
notfound: 'templates/404.hbs'
},
src: {
posts: 'content/posts/',
pages: 'content/pages/'
},
www: {
dest: 'build'
}
}
}
}
```

这里我们为将要传递给插件的变量定义默认值。`data`对象定义了我们将要传递的各种数据，而`template`对象定义了我们将用来组装静态站点的各种模板。`src`对象定义了插件应该在哪里寻找实际内容，而`www`对象定义了输出应该保存在哪里。

这些只是我们插件的默认值——当在生产中使用它时，您可以在项目的 Gruntfile 中覆盖这些值，并使用您自己的自定义模板。您可能还想删除`nodeunit`任务及其配置，以及整个`test`文件夹。

注意，`disqus`的值默认为空，意味着注释关闭。如果用户想要使用 Disqus，他们可以在适当的字段中指定用户名。如果你喜欢使用另一个评论系统，比如脸书评论，实现它应该很简单。

我们还将创建一些基本模板，以便我们可以看到它的实际效果:

### templates/partial/header . HBS

```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=0">
<meta name="description" content="{{ data.description }}">
<link rel="alternate" type="application/rss+xml" title="{{data.title}} - feed" href="/atom.xml" />
<title>{{#if meta.title}}{{meta.title}} - {{/if}}{{data.title}}</title>
</head>
<body>

<header>
<h1><a href="/">{{data.title}}</a></h1>
<h2>{{ data.description }}</h2>
</header>
```

### templates/partial/footer . HBS

```
<footer>
<p>Copyright &amp;copy; {{ data.author }} {{ year }}.</p>
</footer>
</body>
</html>
```

### 模板/404.hbs

```
{{> header }}

<div class="container">
<h1>Whoops, that page doesn't seem to exist</h1>
<p>You might want to go back to <a href="/">the home page</a></p>
</div>
{{> footer }}
```

### 模板.索引. hbs

```
{{> header }}

{{#each posts}}
<article>
<p>{{ this.meta.formattedDate }}</p>
<h1><a href="{{ this.path }}">{{this.meta.title}}</a></h1>
{{{ this.post.content }}}
</article>
{{/each}}
{{#if prevChunk}}
<a href="/posts/{{ prevChunk }}/">Newer</a>
{{/if}}

{{#if nextChunk}}
<a href="/posts/{{ nextChunk }}/">Older</a>
{{/if}}
{{> footer }}
```

### templates/page.hbs

```
{{> header }}

<article class="post">
<h1>{{meta.title}}</h1>
{{{ post.content }}}
</article>

{{> footer }}
```

### templates/post.hbs

```
{{> header }}

<article class="post">
<p class="date">{{ this.meta.formattedDate }}</p>
<h1>{{meta.title}}</h1>
{{{ post.content }}}
<section class="comments">
{{#if data.disqus }}
<div id="disqus_thread"></div>
<script type="text/javascript"> window.disqus_identifier="";
window.disqus_url="{{ data.url }}{{ path }}/";
window.disqus_title="{{meta.title}}"; </script>
<script type="text/javascript" src="http://disqus.com/forums/{{ data.disqus }}/embed.js"></script>
<noscript><a href="http://{{ data.disqus }}.disqus.com/?url=ref">View the discussion thread.</a></noscript>
{{/if}}
</section>
</article>

{{#if next}}
<a href="{{ next.path }}">{{next.title}}</a>
{{/if}}
{{#if prev}}
<a href="{{ prev.path }}">{{prev.title}}</a>
{{/if}}

{{> footer }}
```

有了这些，我们就可以开始插件的工作了。生成的样板文件将包含一个名为`tasks`的文件夹，并且这里将有一个名为`mini_static_blog.js`的文件。找到以`grunt.registerMultiTask`开头的部分——我们所有的代码都需要放在函数体内。在顶部添加以下内容:

```
// Import external libraries
var Handlebars = require('handlebars'),
Moment = require('moment'),
RSS = require('rss'),
hljs = require('highlight.js'),
MarkedMetadata = require('meta-marked'),
_ = require('lodash'),
parseUrl = require('url');

// Declare variables
var output, path;

// Import options
var options = this.options({
year: new Date().getFullYear(),
size: 5
});
options.domain = parseUrl.parse(options.data.url).hostname;
```

这里，我们导入将要使用的外部库，并声明几个变量。我们还获取每一页的年份和大小，并从 Gruntfile 中定义的主机名中获取域名。

接下来，我们将页眉和页脚模板注册为分部模板，以便它们可以被其他模板使用:

```
// Register partials
Handlebars.registerPartial({
header: grunt.file.read(options.template.header),
footer: grunt.file.read(options.template.footer)
});
```

注意使用`grunt.file.read`实际获取模板文件内容。

然后，我们配置我们的 Markdown 解析器，以支持 GitHub 风格的 Markdown 和使用 [Highlight.js](https://highlightjs.org/) 的语法高亮显示(请注意，您需要包含 Highlight.js 的 CSS，以便实际看到它高亮显示)。

```
// Get languages
var langs = hljs.listLanguages();

// Get Marked Metadata
MarkedMetadata.setOptions({
gfm: true,
tables: true,
smartLists: true,
smartypants: true,
langPrefix: 'hljs lang-',
highlight: function (code, lang) {
if (typeof lang !== "undefined" &amp;&amp; langs.indexOf(lang) > 0) {
return hljs.highlight(lang, code).value;
} else {
return hljs.highlightAuto(code).value;
}
}
});
```

注意，我们首先获得一个可用语言的列表，然后在 highlight 函数中，我们检查是否已经检测到该语言，如果是，则显式选择该语言。

然后，我们获取包含页面和发布源的 Markdown 文件:

```
// Get matching files
var posts = grunt.file.expand(options.src.posts + '*.md', options.src.posts + '*.markdown');
var pages = grunt.file.expand(options.src.pages + '*.md', options.src.pages + '*.markdown');
```

注意这里我们再次使用了 Grunt file API 这里我们使用`expand`来获取 posts 和 pages 目录中的所有文件。

我们还编制了车把模板:

```
// Get Handlebars templates
var postTemplate = Handlebars.compile(grunt.file.read(options.template.post));
var pageTemplate = Handlebars.compile(grunt.file.read(options.template.page));
var indexTemplate = Handlebars.compile(grunt.file.read(options.template.index));
var notFoundTemplate = Handlebars.compile(grunt.file.read(options.template.notfound));
```

和以前一样，我们使用`grunt.file.read`获取模板文件的内容，并用 Handlebars 编译它们。

我们的下一步是生成帖子:

```
// Generate posts
var post_items = [];
posts.forEach(function (file) {
// Convert it to Markdown
var content = grunt.file.read(file);
var md = new MarkedMetadata(content);
var mdcontent = md.html;
var meta = md.meta;

// Get path
var permalink = '/blog/' + (file.replace(options.src.posts, '').replace(/(\d{4})-(\d{2})-(\d{2})-/, '$1/$2/$3/').replace('.markdown', '').replace('.md', ''));
var path = options.www.dest + permalink;

// Render the Handlebars template with the content
var data = {
year: options.year,
data: options.data,
domain: options.domain,
path: permalink + '/',
meta: {
title: meta.title.replace(/"/g, ''),
date: meta.date,
formattedDate: new Moment(new Date(meta.date)).format('Do MMMM YYYY h:mm a'),
categories: meta.categories
},
post: {
content: mdcontent,
rawcontent: content
}
};
post_items.push(data);
});

// Sort posts
post_items = _.sortBy(post_items, function (item) {
return item.meta.date;
});

// Get recent posts
var recent_posts = post_items.slice(Math.max(post_items.length - 5, 1)).reverse();

// Output them
post_items.forEach(function (data, index, list) {
// Get next and previous
if (index < (list.length - 1)) {
data.next = {
title: list[index + 1].meta.title,
path: list[index + 1].path
};
}
if (index > 0) {
data.prev = {
title: list[index - 1].meta.title,
path: list[index - 1].path
};
}

// Get recent posts
data.recent_posts = recent_posts;

// Render template
var output = postTemplate(data);

// Write post to destination
grunt.file.mkdir(options.www.dest + data.path);
grunt.file.write(options.www.dest + data.path + '/index.html', output);
```

我们遍历帖子，读取每个帖子的内容，并提取内容和元数据。然后，我们根据文件名为每个文件定义一个文件路径。每篇文章应该被命名为类似于`2015-04-06-my-post.md`的东西，生成的文件的路径将类似于`/blog/2015/04/05/my-post/`。如果你愿意，可以通过修改`permalink`变量的值来改变 URL。

接下来，我们将数据存储在一个对象中，并将其添加到`post_items`数组中。然后我们按日期对它们进行排序，并取出最近的五个。然后，我们再次遍历这些帖子，并获取每个帖子的下一篇和上一篇。最后，我们为每篇文章创建一个目录，呈现模板，并将内容写入其中的一个`index.html`文件。请注意，这意味着我们可以只通过目录来引用每个文件，从而得到干净漂亮的 URL。

让我们来测试一下。将以下内容保存到`content/posts/2015-04-12-my-post.md`:

```
---
title: "My blog post"
date: 2015-02-15 18:11:22 +0000
---

This is my blog post.
```

如果你运行`grunt`，你应该在`build/blog/2015/04/12/my-post/index.html`找到一个全新的 HTML 文件。

## 生成页面

生成页面稍微简单一些，因为我们不必担心日期:

```
// Generate pages
pages.forEach(function (file) {
// Convert it to Markdown
var content = grunt.file.read(file);
var md = new MarkedMetadata(content);
var mdcontent = md.html;
var meta = md.meta;
var permalink = '/' + (file.replace(options.src.pages, '').replace('.markdown', '').replace('.md', ''));
var path = options.www.dest + permalink;

// Render the Handlebars template with the content
var data = {
year: options.year,
data: options.data,
domain: options.domain,
path: path,
meta: {
title: meta.title.replace(/"/g, ''),
date: meta.date
},
post: {
content: mdcontent,
rawcontent: content
},
recent_posts: recent_posts
};
var output = pageTemplate(data);

// Write page to destination
grunt.file.mkdir(path);
grunt.file.write(path + '/index.html', output);
});
```

基本原理是一样的——我们遍历 pages 文件夹中的 Markdown 文件，并用适当的模板呈现每个文件。如果将以下内容保存到`content/pages/about.md`:

```
---
title: "About me"
---

All about me
```

然后，您应该会发现再次运行 Grunt 会在`build/about/index.html`生成一个新文件。

## 实现 RSS 提要和 404 页面

我们的下一个任务是生成一个 RSS 提要和一个 404 页面。我们可以使用之前安装的 RSS 模块创建提要:

```
// Generate RSS feed
var feed = new RSS({
title: options.data.title,
description: options.data.description,
url: options.data.url
});

// Get the posts
for (var post in post_items.reverse().slice(0, 20)) {
// Add to feed
feed.item({
title: post_items[post].meta.title,
description: post_items[post].post.content,
url: options.data.url + post_items[post].path,
date: post_items[post].meta.date
});
}

// Write the content to the file
path = options.www.dest + '/atom.xml';
grunt.file.write(path, feed.xml({indent: true}));

// Create 404 page
var newObj = {
data: options.data,
year: options.year,
domain: options.domain
};
output = notFoundTemplate(newObj);
path = options.www.dest;
grunt.file.mkdir(path);
grunt.file.write(path + '/404.html', output);
```

我们首先从 Gruntfile 传递的数据中定义提要的标题、URL 和描述。然后，我们获取最近的 20 篇文章，遍历它们，并将每篇文章作为一个条目添加，然后将结果保存在`atom.xml`中。

为了生成 404 页面，我们将一些参数传递给模板，并将输出保存在`404.html`中。

## 创建分页索引页

我们还想创建一个文章的分页列表:

```
// Generate index
// First, break it into chunks
var postChunks = [];
while (post_items.length > 0) {
postChunks.push(post_items.splice(0, options.size));
}

// Then, loop through each chunk and write the content to the file
for (var chunk in postChunks) {
var data = {
year: options.year,
data: options.data,
domain: options.domain,
posts: []
};

// Get the posts
for (post in postChunks[chunk]) {
data.posts.push(postChunks[chunk][post]);
}

// Generate content
if (Number(chunk) + 1 < postChunks.length) {
data.nextChunk = Number(chunk) + 2;
}
if (Number(chunk) + 1 > 1) {
data.prevChunk = Number(chunk);
}
data.recent_posts = recent_posts;
output = indexTemplate(data);

// If this is the first page, also write it as the index
if (chunk === "0") {
grunt.file.write(options.www.dest + '/index.html', output);
}

// Write the content to the file
path = options.www.dest + '/posts/' + (Number(chunk) + 1);
grunt.file.mkdir(path);
grunt.file.write(path + '/index.html', output);
}
```

首先，我们将帖子列表分成 5 个块。然后，我们为每个块生成 HTML，并将其写入文件。我选择的路径格式意味着一个典型的路径类似于`/posts/1/index.html`。我们还将第一页保存为网站的主页。

## 进一步发展的想法

实际上，这个插件只是你生成和部署博客的工具链的一部分。您需要将它与其他 Grunt 插件结合起来，并覆盖模板，以创建一个创建和部署工作静态博客的有用方法。但是只要你愿意花时间配置和安装你需要的其他插件，这可能是一个非常强大和灵活的维护博客的方法。你可以[在这里](https://github.com/matthewbdaly/grunt-mini-static-blog)找到出处。

这方面还有很大的发展空间。您可能想探索的一些想法包括:

*   用 [Lunr.js](http://lunrjs.com/) 实现搜索
*   实施类别
*   更改模板或注释系统

你可能想看看这个插件的更完整的版本, [grunt-blogbuilder](https://github.com/matthewbdaly/grunt-blogbuilder) ,了解如何实现这些。

我希望这篇教程能让你对通过利用 Grunt 来构建一个静态站点生成器有所了解，我期待着看到你的成果。

## 分享这篇文章