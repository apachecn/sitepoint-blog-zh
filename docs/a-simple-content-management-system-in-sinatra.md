# Sinatra 中的简单内容管理系统

> 原文：<https://www.sitepoint.com/a-simple-content-management-system-in-sinatra/>

在最近的[峰会 Awesome Manchester Hackathon](http://summitawesome.co.uk/) 上提出的一个想法是由 [Tom Oakley](https://twitter.com/tomoakley125) 提出的，用于一个简单的内容管理系统，该系统使用 [Markdown](http://daringfireball.net/projects/markdown/) 。这个想法没有被选为黑客马拉松项目之一，但我认为它听起来非常适合在 Sinatra 中编写(本身是轻量级的，并且已经支持 Markdown)。因此，在黑客马拉松结束后，我决定试着建立它。

我认为如果我记录我在 RubySource 上取得的进展会很有趣(并且非常有用)……所以这是这个系列的第一部分！

我的想法是构建一些 Sinatra 中间件，可以附加到一个项目上，以添加一些简单的内容管理支持。我也想有标签页和扩展的可能性(如导航列表模块，文件上传模块，电子邮件模块等)。

## MongoDB 和 Mongoid

我决定使用 [MongoDB](http://www.mongodb.org/) 来实现持久性。我过去用过一点，觉得它很适合这里。MongoDB 是一个 [NoSQL](https://en.wikipedia.org/wiki/NoSQL) 数据存储，速度快，可伸缩。它使用文档集合的概念，而不是表和行。一个很大的优势是它是无模式的，因此不需要任何迁移。此外，您可以动态地添加新字段(事实上，同一集合中的文档甚至不必有相同的字段)，这使得它非常灵活，适合敏捷开发。

我选择使用 Mongoid 作为 ORM，因为我喜欢它的语法，它有很好的文档记录，并且正在积极开发中。我发现在本地安装 MongoDB 有点痛苦，但是 [MongoDB 文档](http://docs.mongodb.org/manual/installation/)应该可以帮助你。一旦安装完毕，你需要启动`mongod`服务器。这是特定于操作系统的，所以请查阅文档了解如何完成。

## 安装

首先，我创建了以下 Gemfile:

```
source "https://rubygems.org"
    ruby "2.0.0"
    gem "sinatra"
    gem "slim"
    gem "sass"
    gem "mongoid"
    gem "redcarpet"
```

首先，Mongoid 至少需要 Ruby 1.9.3，所以将它放在 Gemfile 中会有所帮助(特别是当我们稍后在 Heroku 上部署时。)显然，我们需要辛纳屈。我将为视图逻辑使用 Slim，为样式使用 Sass。这纯粹是我的选择，也是我目前默认的发展选择。老实说，不会有太多的造型，但使用 Sass 总是更容易。我已经提到了 Mongoid，它离开了红地毯——由 GitHub 的好心人创建的 markdown 的一个实现。

要确保所有东西都已安装，请在命令提示符下运行`bundle install`。

Mongoid 也需要一点设置。为此，创建一个名为 *mongoid.yml* 的文件，并将其保存在同一个目录中。这些是我发现在 Mongoid 上运行良好的默认设置:

```
development:
      sessions:
        default:
          database: cms
          hosts:
            - localhost:27017
    production:
      sessions:
        default:
          uri: <%= ENV['MONGOHQ<em>URL'] %>
          options:
            skip_version_check: true
            safe: true
```

在同一个目录中，我们需要创建一个名为 main.rb 的文件，我们的主要功能将放在这个文件中。为了让事情有所进展，我决定深入研究并构建一个经典应用程序，稍后将把它转移到模块化。在 main.rb 开始的时候，我们需要要求相关的 gem 并配置 Mongoid:

```
require 'sinatra'
    require 'sinatra/reloader' if development?
    require 'mongoid'
    require 'slim'
    require 'redcarpet'

    configure do
      Mongoid.load!("./mongoid.yml")
    end
```

## 页面类

现在我们来看 CMS 的核心——页面模型。我们从简单开始，给它一个标题和内容字段:

```
class Page
      include Mongoid::Document

      field :title,   type: String
      field :content, type: String
    end
```

要在一个类中使用 Mongoid，您需要做的就是包含`Mongoid::Document`模型。之后，您可以定义要在数据库中使用的字段。我们的两个字段是“标题”和“内容”,它们都是字符串类型。现在我们可以试着创建一些页面了！

## 创建一些页面

我们还没有定义任何路线，但这并不意味着我们不能创建任何页面。能够通过控制台运行我们的程序总是很有用的。使用以下命令打开终端并启动 IRB:

```
$> irb
```

这应该会给你一个类似于下面的 ruby 提示，我们需要 main.rb 文件:

```
2.0.0-p0 :001 > require './main'
=> true
```

现在我们可以试着添加一些页面:

```
2.0.0-p0 :002 > hello = Page.new
 => #

2.0.0-p0 :003 > hello.title = "Hello World!"
 => "Hello World!"

2.0.0-p0 :004 > hello.content = "
This is our first page

"
 => "
This is our first page

"

2.0.0-p0 :005 > hello.save
 => true
```

我们刚刚创建了我们的第一页！这是使用`new`方法完成的。我们将页面分配给变量`hello`。这个有 Mongoid 提供的`title`和`content`的 setter 方法。一旦这些都设置好了，我们就使用`save`方法来提交对数据库的修改。

我们可以使用`create`方法在数据库中用一行创建一个文档，而不是一次写出一个属性，然后保存对象:

```
2.0.0-p0 :006 > Page.create(title: "Markdown Page", content: "This page uses markdown")
 => #
```

我们可以通过查询数据库来检查页面是否已经创建。首先，让我们检查使用`count`方法保存了多少页:

```
2.0.0p0 :007 > Page.count
 => 2
```

这是个好消息——如我们所料，数据库中保存了 2 页。使用 Mongoid 搜索页面的方法有很多。下面是我们可能会用到的一些查询:

```
2.0.0p0 :009 > Page.first
     => # Page.last
     => #, title: "Hello World!", content: "
This is our first page

">
```

`first`和`last`方法完全按照它们在 tin 上所说的那样，分别在数据库中找到第一个和最后一个文档。我们还可以使用`find`方法，以文档的 id 字符串作为参数来查找特定的文档:

```
2.0.0p0 :011 > Page.find("5173f574a39401776a000002")
     => #
```

我们可能不总是知道文档 id，所以我们也可以使用`find_by`和`where`方法通过不同的字段进行搜索:

```
2.0.0p0 :012 > Page.find_by(title: "Hello World!")
 => #This is our first page

">

2.0.0p0 :013 > Page.where(title: "Hello World!").first
 => #This is our first page

">
```

虽然这些看起来一样，但还是有细微的区别:`find_by`返回一个页面对象，wheras `where`返回一个 MongoDB 标准，这是一个可以链接在一起的代理对象。在所有条件都满足之前，它不会查询数据库。

## 查看页面

现在我们有了一些页面，让我们创建一些路线和视图，这样我们就可以看到它们了！

首先，我们将创建一个索引页面，列出数据库中的所有页面。将以下路径添加到 main.rb 的底部:

```
get '/pages' do
  @pages = Page.all
  @title = "Simple CMS: Page List"
  slim :index
end
```

这将找到数据库中的所有页面，并将它们作为数组存储在 instance variable`@pages`中。我们还使用实例变量`@title`来存储页面的标题(这将在布局视图中使用)。然后，我们使用 slim 呈现名为 index 的视图。
我们现在需要创建那个视图。创建一个名为“index.slim”的文件，并将其保存在根目录下名为 views 的文件夹中。将以下代码放入该文件中:

```
h1 Pages
  -if @pages.any?
    ul.pages
    - @pages.each do |page|
      == slim :page, :locals => {page: page}
  - else
    p No pages here!
```

这只是所有页面的列表，但是它使用了一个 partial 来显示关于行`== slim :page, :locals => {page: page}`中每个页面的实际信息。这意味着我们需要创建另一个名为“page.slim”的视图(也保存在“views”文件夹中)，包含以下代码:

```
li
  a href="/pages/#{page.id}" =page.title
```

这不是一个很大的部分，但是将它保存在一个单独的文件中是有意义的，因为我们可能希望在以后的内容中添加更多的信息，或者在其他视图中使用它。

页面部分有一个到每个单独页面的链接。我们需要编写一个路由处理程序来处理这个问题。这用于显示每个单独的页面–在下面放置以下代码:

```
get '/pages/:id' do
      @page = Page.find(params[:id])
      @title = @page.title
      slim :show
    end
```

它根据 URL 中给出的 id 找到页面，并创建一个名为`@page`的实例变量来存储`Page`对象。我们还将页面标题存储在`@title`实例变量中。

接下来，让我们创建一个页面来查看实际页面。将视图目录中的以下代码保存为“show.slim”:

```
h1= @page.title
- if @page.content
  == markdown @page.content
```

这是一个非常直观的视图，将标题显示为一级标题，并使用`markdown`方法显示内容(如果确实有一些内容)。注意，这两个都是在路由处理程序中创建的`@page`实例变量的方法。

在测试之前，我们需要为应用程序创建一个布局。下面是一个基本的 HTML5 布局，可以做得很好:

```
doctype html
html
  head
    title= @title || "Simple Sinatra CMS"
  body
    h1
      a href="/pages" Simple Sinatra CMS
    == yield
```

将上面的代码片段保存为 views 目录中的“layout.slim ”,然后通过在终端中键入`ruby main.rb`来启动服务器，然后在浏览器中转至[http://localhost:4567/pages](http://localhost:4567/pages),您应该会看到类似于以下内容的页面列表:

![Screenshot1](img/58eb4be711c919a97cc1998757e10a08.png)

尝试浏览所有页面！

![Screenshot2](img/16718cb5d6469eeb6ed832c226589920.png)

## 创建页面

这很有趣，但我们只有两个页面，所以我们需要创建更多的…并且在浏览器中。

首先，我们需要为将要添加新页面的页面创建一个路由处理程序。将以下代码添加到' main.rb '中，但要确保它位于'/pages/:id '路由的之前的*(这是因为 Sinatra 总是通过将 params[:id]设置为' new '来查找匹配 URL 和'/pages/:id '匹配'/pages/new '的第一个路由):*

```
get '/pages/new' do
  @page = Page.new
  slim :new
end
```

这只是创建了一个新的页面对象，并将其存储在实例变量`@page`中，该变量将在视图中可用。目前我们可能不需要这个对象，但是将来我们可能会使用相同的表单来编辑页面，就像我们创建新页面一样。编辑表单将引用正在编辑的页面，这将是`@page`实例变量。如果这不存在，那么我们会得到错误，所以我们只是创建一个新的`Page`对象来避免这种情况。

然后我们显示新视图，最好在“视图”文件夹中创建，代码如下:

```
h1 New Page
form action="/pages" method="POST"
  fieldset
    legend Create a new page
    == slim :form
  input type="submit" value="Create"
```

这使用了实际表单的一部分(因此我们可以在编辑页面中重用它)。我们还需要将以下内容保存为“form.slim”:

```
label for="title" Title:
input.title type="text" name="page[title]" value="#{@page.title}" size="32"
label for="content" Content:
textarea.content name="page[content]" rows="12" cols="72" ==@page.content
```

目前，我们只有两个字段是必需的，所以这是一个相当简单的表单。您可以看到对`@page`实例变量的引用——当它是一个新页面时，这些引用将显示为空白。

现在我们需要弄清楚当表单被提交时会发生什么。我们可以使用路由处理器来处理这个问题。我们使用 URL“/pages ”,因为这是表单的“action”属性中的内容。但是在这种情况下，我们使用 HTTP 动词‘post ’,因为这是表单提交时使用的方法:

```
post '/pages' do
   page = Page.create(params[:page])
   redirect to("/pages/#{page.id}")
end
```

在这个动作中，我们使用之前在 IRB 中使用的`create`方法创建了一个新的`Page`对象。`params[:page]`是表单中输入的所有值的散列。然后我们使用 Sinatra 有用的`redirect`和`to`方法重定向到新创建页面的 URL。

现在所有的路线都已经准备好了，我们只需要在索引页面上添加一个链接来创建一个新页面。在 index.slim 顶部附近的某个位置创建以下行:

```
a href='/pages/new' Add a new page
```

重启服务器(按住 Ctrl + C，然后再次键入`ruby main`)并尝试创建一些新页面。

![Screenshot3](img/705441291c2452d98d35b18bb88a163c.png)

找到了。我们已经有了简单的内容管理系统的雏形。我们可以使用 Markdown 创建新页面并查看它们。在第 2 部分中，我们将看看编辑和删除页面，以及如何根据页面标题创建带有“漂亮 URL”的永久链接。

我希望你喜欢这篇文章，请在评论区留下你的反馈。我特别想听听关于这个项目进展的任何想法。

## 分享这篇文章