# 在 Wintersmith 中创建帖子、自定义元数据和数据

> 原文：<https://www.sitepoint.com/creating-posts-custom-metadata-data-wintersmith/>

在这个迷你系列的第一部分中，我已经向你介绍了 Wintersmith，这是我认为最好的基于 Node.js 的静态站点生成器之一。我介绍了如何安装和开始使用 Wintersmith，还讨论了默认模板系统 Jade 的一些特性。

在第二部分中，我将教您如何使用 Markdown 格式创建文章，如何设置自定义元数据，以及如何生成和部署静态网站。

## 创建帖子

在本系列的第一部分，我们已经用 Jade 构建了模板。现在是时候为站点创建一些内容了。默认情况下，帖子写在由标有的[渲染的](https://github.com/chjj/marked) [Markdown](http://daringfireball.net/projects/markdown/) 中，尽管其他渲染也可以作为[插件](https://github.com/jnordberg/wintersmith/wiki/Plugins)获得。

大多数开发者似乎对 Markdown 很熟悉，但是如果你不熟悉的话，这很容易学会。许多代码编辑器要么默认支持它，要么通过免费扩展支持它。有很多独立的 Markdown 编辑器，比如我在 OSX 上用的是 [Mou](http://25.io/mou/) ，在 Windows 上有 [MarkdownPad](http://markdownpad.com/) 。这篇文章不会深入讨论降价的细节，但如果你需要一个开始的地方，我建议你看看[这一页](http://daringfireball.net/projects/markdown/syntax)。

帖子放在`contents/articles`文件夹中。每个帖子都有自己的目录，这将是其搜索引擎优化友好的网址。例如，我们在《冒险时间》第六季的帖子！名为“Breezy”被放在名为`articles/season-6-breezy`的目录下。在那个目录里有我们的名为`index.md`的帖子的降价文件。

### 发布元数据

每个降价帖子可以在顶部使用 [YAML 格式](http://www.yaml.org/start.html)的元数据，类似于 Jekyll 的[封面内容](http://jekyllrb.com/docs/frontmatter/)。除了`template`、`title`和`date`之外，不需要任何元数据。`template`是应该用来呈现文章的模板，`title`是文章的标题，`date`是文章发表的日期。如果没有指定`template`，文章将不会被呈现(这可能不是您想要的)。如果未指定`title`和`date`，它们的值将接受默认值。

我们可以随意添加我们想要的任何其他元数据。我们将在下面的自定义元数据部分进一步讨论这一点。

Wintersmith 还允许我们通过 contents 目录中的 JSON 文件为使用 JSON 格式的帖子指定元数据。有关更多细节，请查看[文档](https://github.com/jnordberg/wintersmith#model)。

### 定制摘录

Wintersmith 让我们指定文章中摘录的结束位置。这确保了摘录不会超过特定长度或在逻辑点结束。为了指定摘录的结尾，我们必须将以下代码放在我们的 Markdown 帖子中:

```
<span class="more"></span>
```

如果我们不指定这个`span`，Wintersmith 将寻找第一个`<h2>`或`<hr>`的实例。如果这些都不存在，摘录将包含文章的完整 HTML，这不是你想要的。摘录可以通过页面上的`intro`属性获得，如我们的`templates/partials/homepagemiddle.jade`文件:

```
| !{ typogr(article.intro).typogrify() }
```

如果我们想检查是否指定了摘录，我们可以使用页面的`hasMore`属性。你可以通过检查[页面插件](https://github.com/jnordberg/wintersmith/blob/718250eefdef08e9667650c350da0fb37c185936/src/plugins/page.coffee)的代码来了解这是如何工作的。

## 自定义元数据

通常，您需要设置自己的元数据，可以是站点的全局元数据，也可以是帖子/页面的本地元数据。让我们看看在 Wintersmith 中是如何做到这一点的。

### 全局元数据

我们可以在`locals`对象下的`config.json`文件中设置任意元数据。例如，我们可以在示例站点中设置站点名称、描述和横幅图像。

```
{
  "locals": {
    "url": "http://localhost:8080",
    "name": "Adventure Time!",
    "owner": "Brian Rinaldi",
    "description": "Adventure Time is an American animated television series created by Pendleton Ward for Cartoon Network. The series follows the adventures of Finn, a human boy, and his best friend and adoptive brother Jake, a dog with magical powers to change shape and grow and shrink at will. Finn and Jake live in the post-apocalyptic Land of Ooo. Along the way, they interact with the other main characters of the show: Princess Bubblegum, The Ice King, and Marceline the Vampire Queen.",
  "banner": "/asseimg/about.jpg"
},
...
```

这些值在我们的站点模板中的任何地方都可以在`locals`对象下找到。例如，我们在`templates/partials/footer.jade`中使用上面的值:

```
div(class="4u")
    section
        header
            h2 What's this all about?
        a(href="#",class="image featured")
            img(src= locals.banner)
        p= locals.description
```

应该注意的是，这些也可以被称为`banner`和`description`而没有`locals`，它也可以工作。

### 每篇文章元数据

正如我们之前讨论的，每个帖子都应该有元数据集合。这个元数据可以包含我们选择的任意值。例如，这里是我们的一篇文章(`contents/articles/season-6-breezy/index.md`)的元数据，其中我们设置了一个`shortdesc`和一个`banner`值。

```
---
title:  "Breezy (Season 6)"
date:   2014-06-05 10:33:56
template: article.jade
shortdesc: Finn decides to get back into the dating game to help his wilting arm flower.
banner: /asseimg/breezy.jpg
---
```

我们可以使用页面对象中的`metadata`对象来访问`shortdesc`值。这里我们在`templates/partials/footer.jade`的一个循环中使用它:

```
- var i=0
- var articles = env.helpers.getArticles(contents);
each article in articles
    -i++
    if (i>2) && (i<8)
        li
            span(class="date")
                !=moment.utc(article.date).format('MMM')
                strong= moment.utc(article.date).format('DD')
            h3
                a(href=article.url)= article.title
            p= article.metadata.shortdesc
```

当然，我们也可以在页面输出中访问这些，就像我们在`contents/templates/article.jade`中所做的那样:

```
header
    h2= page.title
    p= page.metadata.shortdesc
```

## 自定义数据

并非我们网站的所有内容都是帖子或元数据。通常我们会有其他类型的数据收集。例如，在我们的示例站点中，我们有一个冒险时间集合！带有名称、描述和图像的字符。与其将这些值硬编码在页面上，不如将它们创建为数据，这样我们就可以在站点中任何需要的地方重用它们，并使它们的显示更加灵活。让我们看看这是如何工作的。

Wintersmith 中的数据类似于 [Jekyll collections](http://jekyllrb.com/docs/collections/) ，因为它是基于文件的。不是将一个 JSON 文件放在一个预先确定的文件夹中，而是在您在`contents`文件夹下选择的任何文件夹中，每个对象都有自己的文件。

例如，在我们的示例站点中，`contents/characters`包含一系列文件，每个文件都有代表一个字符的 JSON 数据。下面是`contents/characters/lsp.json`的内容:

```
{
  "description": "Lumpy Space Princess (LSP) acts like a bratty, apathetic, sassy, attention-seeking and willfully ignorant teenager, often texting on her phone.",
  "image": "/asseimg/lsp.jpg",
  "name": "Lumpy Space Princess"
}
```

这些数据是通过基于文件夹名称的`contents`对象自动读取的，每个文件都成为数组中的一个对象。文件中的每个属性都可以在`metadata`对象下获得。在我们的示例中，我们使用它在主页上输出一个字符列表:

```
each character in contents.characters
    div(class="4u")
        section(class="box")
            span(class="image featured")
                img(src= character.metadata.image)
            header
                h3= character.metadata.name
            p= character.metadata.description
```

## 生成和部署

现在我们已经完成了我们的项目，是时候部署它了。但是首先我们需要生成静态文件，我们必须上传到我们的服务器。要构建我们的站点，请执行以下命令:

```
$ wintersmith build
```

默认情况下，该命令会将文件生成到一个`build`目录中。然而，我们可以通过使用`-o`选项来指定不同的路径。我们还可以在构建之前使用`-X`选项强制清理`build`目录。要获得构建选项的完整列表，你可以使用命令`wintersmith build --help`。

## 结论

我认为很明显 Wintersmith 为静态站点生成器提供了完整的功能集，允许我们在 Node.js/npm 生态系统中构建复杂的静态站点。不幸的是，这里详细讨论的许多主题在有限的项目文档中没有很好地涵盖。

好消息是源代码都是 CoffeeScript，如果你是 JavaScript 开发人员，这很容易理解。因此，项目如何工作的许多细节可以通过查看源代码来了解。此外，还有一个用 Wintersmith 构建的网站的[展示窗口](https://github.com/jnordberg/wintersmith/wiki/Showcase)，其中许多包括源代码，可以帮助你看到其他人是如何使用该工具解决常见任务的。虽然这些不能代替可靠的文档，但是它应该能够让您到达您需要去的地方。

对于流行的静态站点引擎的其他示例，请查看我在 GitHub 上的项目，该项目不仅包括 Wintersmith 示例，还包括用 Jekyll、Harp 和中间人构建的相同示例站点。

## 分享这篇文章