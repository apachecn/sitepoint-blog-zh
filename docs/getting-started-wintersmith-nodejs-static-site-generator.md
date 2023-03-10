# Wintersmith 入门:基于 Node.js 的静态站点生成器

> 原文：<https://www.sitepoint.com/getting-started-wintersmith-nodejs-static-site-generator/>

我最近经常谈论和写关于[静态站点生成器](https://www.sitepoint.com/static-site-generators/)的话题。一般来说，我倾向于推荐使用 Jekyll，它是目前构建静态站点最稳定、功能最丰富的选择。然而，Jekyll 基于 Ruby 的事实可能会成为一些开发人员的绊脚石，尤其是那些不熟悉 Ruby 的开发人员。我得到的一个最常见的问题是:“有没有一个基于 JavaScript 并通过 npm 可用的好选项？”

在本文中，这是两部分系列的第一部分，我们将讨论这样一个选项。Wintersmith 是一个健壮的静态站点生成器，它有一个最大的障碍(在我看来):缺乏详细的文档。希望这篇文章能帮助那些对通过 npm 获得的基于 JavaScript 的选项感兴趣的人开始用 Wintersmith 构建静态站点。

## 样本站点

对于本文，我们将使用我构建的示例站点，您可以在这里找到。这个样本是一个[的冒险时间！](http://www.cartoonnetwork.com/games/adventuretime/index.html)粉丝网站，看起来像下图。

![Sample Adventure Time! Site](img/d8d3d90398ea1c36fb3b3cec06ff275d.png)

该项目的目标是使用一些静态站点生成器来比较它们，从而重建这个站点。虽然该网站有意简单，但它包含了许多我们将在 [Wintersmith](http://wintersmith.io/) 中看到的关键特性:

*   自定义全局元数据——将自定义元数据设置为可在模板中访问和使用的站点全局元数据的能力；
*   自定义帖子元数据-能够在每个帖子的基础上设置自定义元数据，可以在列出帖子时或在帖子本身的显示中访问这些元数据；
*   数据集——能够添加不是文章或页面的内容，而是自定义数据类型(在本例中是字符数据)。

样本中使用的所有角色数据、内容和图像都来自于[冒险时间！维基](http://adventuretime.wikia.com/)。该设计基于来自 [HTML5UP](http://html5up.net/) 的免费模板。

## 如何设置 Wintersmith

Wintersmith 基于 Node.js 和 npm 的一个好处就是安装过程超级简单。你必须执行下面的命令来安装它(注意:在 Windows 上不需要`sudo`):

```
$ sudo npm install -g wintersmith
```

就这样——你可以走了！现在让我们创建一个新项目。

### 创建网站

要使用 Wintersmith 创建新站点，请输入以下命令:

```
$ wintersmith new [project name]
```

对于示例站点，我们将项目命名为“wintersmithsite”。所以要执行的命令是:

```
$ wintersmith new wintersmithsite
```

它将生成一个具有给定项目名称的文件夹，其中包含一堆文件，我们可以修改这些文件来开始构建我们的网站。

如果我们看一下生成的文件，我们会看到 Wintersmith 将配置、模板和插件放在根级别，而站点文件放在名为“contents”的文件夹中。

### 测试站点

要在本地服务器上运行项目，请更改目录并开始预览:

```
$ cd wintersmithsite
$ wintersmith preview
```

默认情况下，本地服务器运行在端口 8080 上，因此我们可以通过浏览到`http://localhost:8080`来打开站点。我们可以使用`-p`选项指定一个不同的端口。此外，默认情况下，服务器是冗余的，会向控制台输出详细的错误消息和加载的资源。我们可以通过输入以下命令了解服务器的许多其他选项:

```
$ wintersmith preview -help
```

这些选项也可以在名为`config.json`的站点配置文件中设置，但是现在，默认设置应该可以了。

## 模板基础

Wintersmith 默认使用 [Jade](http://jade-lang.com/) 作为它的模板语言。本教程将使用它，但是如果你喜欢不同的模板语言，Wintersmith 有很多[插件](https://github.com/jnordberg/wintersmith/wiki/Plugins)可用。

模板放在网站根目录的“模板”文件夹中。Jade 是一种非常简洁的模板语言——没有括号，没有结束标签，缩进也很重要。让我们看看如何使用 Jade 创建模板的一些基础知识。

### 输出数据

Jade 提供了多种从变量输出数据的方式。构建站点模板时最常见的是将标签设置为等于变量值。例如，下面来自`templates/article.jade`的例子将把一篇文章的标题放在开始和结束的`<h2>`标签中。

```
h2= page.title
```

默认情况下，变量的内容在输出前被转义。这意味着，如果它包含 HTML，标签将不会在输出中呈现，而是显示为纯文本。当我们需要它们不被转义时，我们必须添加一个感叹号，如`templates/article.jade`中的示例所示:

```
section.content!= typogr(page.html).typogrify()
```

我们可以对属性做同样的事情。下面来自`templates/partials/homepagemiddle.jade`的例子创建了一个`<a>`标签，其`href`属性等于文章的 URL。

```
a(href= article.url, class="image featured")
```

如果你想知道默认情况下页面对象上有哪些变量，那么[文档](https://github.com/jnordberg/wintersmith#the-page-plugin)会列出它们。我应该注意到上面的`article`变量不是默认的，而是一个循环的结果，我们将在后面讨论。

使用 Jade 输出变量的另一种方法是使用`#{ variableName }`。当我们这样做时，变量的内容被转义。在我们的示例中没有这种方法的例子。

如果想输出未转义变量的内容，可以使用的语法是`!{ variableName }`。例如，当我们输出文章主体的内容时，我们希望呈现其中的任何标签。一个例子摘自`templates/partials/homepagemiddle.jade`:

```
| !{ typogr(article.intro).typogrify() }
```

前一行代码前面的管道意味着内容将显示为[纯文本](http://jade-lang.com/reference/plain-text/)。

#### npm 模块

你可能想知道`typogrify()`电话是怎么回事。嗯，Wintersmith 的一个好处就是支持使用 npm 模块。生成的站点包括三个: [typogr](https://www.npmjs.com/package/typogr) (就是你看到上面用的)； [Moment.js](https://www.npmjs.com/package/moment) (要了解更多关于 Moment 的内容，可以阅读文章[使用 Moment.js](https://www.sitepoint.com/managing-dates-times-using-moment-js/) 管理日期和时间)；和[下划线](https://www.npmjs.com/package/underscore)。

让我们看看如何使用 Moment.js 在模板中格式化日期，如`templates/partials/homepagemiddle.jade`中的示例所示:

```
p= "Posted " + moment.utc(article.date).format('MMM DD, YYYY')
```

Moment.js 提供了比格式化更多的功能，所有这些都可以从我们的模板中获得。但是，我们不仅限于 Moment.js，因为我们可以将任何 npm 模块添加到我们站点的`config.json`、`npm install`的`require`部分，并在我们的模板中使用该模块。

### 包含

为了使模板更易于维护和重用，我们需要将它们分开。我们可以使用[包含](http://jade-lang.com/reference/includes/)来实现这一点。来自`templates/index.jade`的这段代码包含了`templates/partials/header.jade`文件(注意你不需要。jade 文件扩展名):

```
include ./partials/header
```

Jade 还支持继承，可以用来创建相似的、独立的、可重用的模板代码块。如果你想知道更多关于继承的细节，请查阅文档。

### 条件式

有些情况下，您可能希望根据特定条件显示模板的不同方面。这可以在 Jade 中使用[条件句来完成。Jade 支持`if`、`else if`、`else`和`unless`，实际上是对`if`的否定版本。](http://jade-lang.com/reference/conditionals/)

这个来自`templates/partials/header.jade`的例子只在我们不在页面上时显示横幅(我们站点中的每个帖子都是一个页面，所以这意味着只在主页上显示它，`index.html`):

```
if !page
section(id="banner")
header
h2 Explore the Land of Ooo...
p ...and its many kingdoms!
```

这个条件也可以写成`unless page`。

Jade 还支持`case`语句块。如果你想了解更多，[请参考官方文件](http://jade-lang.com/reference/case/)。

### 环

循环是我们在模板中经常要做的事情，不管我们是循环文章还是数据。针对这样的需求，Jade 同时支持 [`each`和`while`循环](http://jade-lang.com/reference/iteration/)。

下面来自`templates/partials/homepagemiddle.jade`的例子使用一个`each`循环输出我们所有的字符数据。在主页的中间，我们显示每个角色及其图像、名称和描述。`each`循环遍历数组中的每个对象，并将它赋给变量`character`，在这里我们可以访问它的属性。

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

不幸的是，不支持给循环添加限制或偏移量。相反，我们可以通过组合变量和条件来实现这一点。在下面的例子中，我们只显示了前两个帖子(类似于 limit)。请记住，设置变量(`i`和`articles`)的行前面有一个`-`，表示它们将在编译时在服务器上运行。这意味着在模板的输出中没有生成相应的代码。

```
- var i=0
- var articles = env.helpers.getArticles(contents);
each article in articles
- i++
if i<3
div(class="6u")
section(class="box")
a(href= article.url, class="image featured")
img(src= article.metadata.banner)
header
h3= article.title
p= "Posted " + moment.utc(article.date).format('MMM DD, YYYY')
| !{ typogr(article.intro).typogrify() }
footer
ul(class="actions")
li
a(href= article.url, class="button icon fa-file-text") Continue Reading
```

您会注意到我们使用`env.helpers.getArticles(contents);`来获取 contents/articles 文件夹中的文章数组。据我所知，这没有很好的文档记录，但是这个方法来自于[分页器插件](https://github.com/jnordberg/wintersmith/blob/718250eefdef08e9667650c350da0fb37c185936/examples/blog/plugins/paginator.coffee)，它可以在`config.json`中配置。

本文的下一个示例和最后一个示例使用偏移和限制进行复制，以显示前两个示例之后的五个示例:

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

## 结论

在本文中，我向您介绍了 Wintersmith，如果您正在寻找基于 Node.js 的静态站点生成器，这是一个不错的选择。我介绍了如何安装和开始使用 Wintersmith，还讨论了默认模板系统 Jade 的一些特性。在第二部分的[中，我将教你如何使用 Markdown 格式创建文章，如何设置自定义元数据，以及如何生成和部署你的静态网站。](https://www.sitepoint.com/creating-posts-custom-metadata-data-wintersmith/ "Creating Posts, Custom Metadata, and Data in Wintersmith")

正如您所看到的，Wintersmith 的一个有趣的方面是它能够利用 npm 模块。这为开发人员在定制他们的站点或添加他们可能需要的附加功能时提供了很多选择。

## 分享这篇文章