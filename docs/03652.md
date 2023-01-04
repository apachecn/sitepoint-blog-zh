# 铁路凤凰城

> 原文：<https://www.sitepoint.com/phoenix-for-railsies/>

![phoenix](img/a681ad42c41b241cbff8069a281bbf9a.png)

我的上一篇文章讨论了 Ruby 生态系统中的 WebSockets，特别关注了 Rails 中对 WebSockets 的支持。当我在学习 WebSockets 和 Ruby 之间的复杂关系时，我发现了一个 web 框架，它使 WebSockets 变得更容易，同时仍然让我们以“Rails 的方式”做事。它叫凤凰。虽然 WebSockets 是我发现 Phoenix 的原因，但它提供的不仅仅是“更简单的 WebSockets”。

不幸的是(或者幸运的是，取决于你如何看待事物)，Phoenix 不是一个 Ruby 框架。相反，它是用一种叫做 [Elixir](http://elixir-lang.org) 的语言编写的，这种语言建立在 Erlang 虚拟机之上。在本文中，我们将看看为什么作为 ruby 专家考虑 Elixir+Phoenix 组合是有意义的，以及 Phoenix 框架的基础。

## 长生不老药

为了理解 Elixir 的特别之处，我们必须了解一些关于 Erlang 和 Erlang VM 的知识。Erlang 最初是由爱立信(或多或少是一家电信公司)开发的，旨在构建无论世界上发生什么样的疯狂都不会中断的系统。虽然 Erlang 没有像瘟疫一样流行，但是它已经被许多公司用于一些相当大的项目。一些例子:WhatsApp 由基于 Erlang 的架构提供支持，脸书最初将 Erlang 用于其聊天架构，Basho(Riak 背后的公司)大量使用 Erlang，[高度重视它](http://basho.com/posts/technical/erlang-at-basho-five-years-later/)。Erlang 非常适合构建高度可用的分布式系统，原因有很多(例如，管理器的概念在 Erlang 中执行得特别好)。然而，Erlang 是一种函数式语言，它有许多特性，对于许多人来说，使用起来有点不舒服。

Erlang 运行在 Erlang 虚拟机之上。如果您熟悉 Java，这类似于 Java 和 JVM 之间的关系。Elixir 是另一种运行在 Erlang 虚拟机之上的语言。因此，Elixir 用一种不同的语法提供了非常简单的 Erlang 互操作(我们可以调用 Erlang 函数，基本上没有运行时成本),在我看来，这种语法比 Erlang 语法更令人满意。

如果你想快速掌握长生不老药的基本知识，我们有几篇[文章](https://www.sitepoint.com/ruby-colored-glasses-elixir/)的[到](https://www.sitepoint.com/elixir-love-child-ruby-erlang/)，你可以快速浏览。即使你没有任何关于仙丹的经验，但是有一些 Ruby 背景，你会发现理解仙丹的方式并不太难。

## 凤凰

那么，[凤凰](http://www.phoenixframework.org)是什么？这是一个为 Elixir 构建的类似 Rails 的框架。你可能会问:学习另一种语言的另一个框架到底有什么意义呢？这种语言似乎还没有人真正使用。有几个原因。首先，Erlang VM 非常快，Elixir 继承了这种速度。有[基准](http://www.littlelines.com/blog/2014/07/08/elixir-vs-ruby-showdown-phoenix-vs-rails/)显示了这一点。但是当然，我们可以整天坐在那里戳洞，所以你必须用这两种技术建造一些东西来相信它。

其次，Elixir 是一种有趣的语言，因为它成功地结合了函数式编程、OOP 和类似 Lisp 的元编程概念(Elixir 的宏非常强大)。最后，当涉及到 WebSockets 这样的东西时，Rails 确实感觉有点落后。凤凰城有这些权利的方块。最重要的是，您可以获得大部分这些好处，而不必放弃 Rails 的细微之处。诚然，Phoenix 没有 Rails 那么多的包，但是框架本身的功能相当完整。我们来看看凤凰的基本情况。

## 装置

我们将快速完成安装步骤，主要是因为它们很无聊，还有其他资源可以更好地解释它们(我们将链接到这些资源)。首先，你需要安装[药剂](http://elixir-lang.org/)，幸运的是，它有一个非常连贯的[安装页面](http://elixir-lang.org/install.html)。RubyGems/Bundler 夫妇的长生不老药被称为“Mix”。我们可以用它来安装 Phoenix，如 [Phoenix 安装页面](http://www.phoenixframework.org/docs/installation)所述。

## 建造它

如果我扔给你一堆理论，当你读完这篇文章的时候，你可能会忘记几乎所有的理论。相反，我们将使用 Phoenix 构建一个小应用程序来查看香肠是如何制作的。如果你跟着做，这些步骤一开始可能看起来有点杂乱无章，但最终，事情应该会“一拍即合”

那么，我们要建造什么？我一整天都在不断地忘记一些小事，我希望当我在笔记本电脑上敲打一些代码时，有一种方法能提醒我。为了解决这个问题，我们将构建一个可以设置提醒的 webapp，它会通过电子邮件提醒我们。我们也可以使用 HTML5 通知 API 来实现这一点，但是我们将使用电子邮件来演示 Phoenix 中的一些重要概念。

### 基本结构

我们首先运行一个“空的”Phoenix 应用程序:

```
mix phoenix.new remynders
```

您应该会收到一条安装消息，以此结尾:

```
We are all set! Run your Phoenix application:

$ cd remynders
$ mix phoenix.server
```

您也可以在 IEx (Interactive Elixir)中运行它，如下所示:

```
$ iex -S mix phoenix.server
```

我们可以在 [http://127.0.0.1:4000](http://127.0.0.1:4000) 看看我们启动服务器后到目前为止都有什么。我们可以让服务器继续运行，因为就像 Rails 一样，Phoenix 服务器会实时重新加载代码。为了使用我们的“remynders”应用程序，我们必须修改路由，这样我们就可以有一条不在 Phoenix 默认页面结束的路由。打开 **web/router.ex** (或多或少类似于 Rails 的 **config/routes.rb** ):

```
defmodule Remynders.IndexView do
  use Remynders.Web, :view
end
```

最后，我们在**web/templates/index . html . eex**中有了模板:

```
<div class="headline">Set Quick Reminders Quickly</div>

<a href="#" class="button">Create a reminder</a>
```

如果你现在浏览根索引，你会看到一些我们的创造，但是你也会注意到两件事:

1.  我们的创作目前看起来像垃圾。
2.  页面上还有一堆“凤凰框架”营销材料。

为了解决这些问题，我们必须改变布局和一些 CSS。

### 布局

如果你打开**web/templates/layout/application . html . eex**，你会看到到处都是凤凰营销材料的标记。相反，我们将用我们的标记替换它:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="description" content="">
    <meta name="author" content="">

    <title>Remynders</title>
    <link rel="stylesheet" href="<%= static_path(@conn, "/css/app.css") %>">
  </head>

  <body>
    <div class="page_wrapper">
      <div id="logo">
      <h1>Remynders</h1>
      </div>

      <p class="alert alert-info" role="alert"><%= get_flash(@conn, :info) %></p>
      <p class="alert alert-danger" role="alert"><%= get_flash(@conn, :error) %></p>

      <%= @inner %>
    </div>
    <script src="<%= static_path(@conn, "/js/app.js") %>"></script>
    <script>require("web/static/js/app")</script>
  </body>
</html>
```

目前我们需要关心的唯一部分是`，在这里我们的渲染模板将被简单地“放入”(同样，与 Rails 的概念 **application.html.erb** )非常相似)。所以，我们已经解决了我们的部分问题，但是美学还不是 Remynders 最好的品质…到目前为止。`

 `## 静态文件

我们希望修改 CSS 的布局和给定的页面。快速浏览一下 **web/static/css/app.scss** 的顶部，我们会看到 Phoenix 附带了一个随时可用的 Bootstrap 副本。因为这是一个小应用程序，而且我们不太关心支持一大堆风格复杂的不同浏览器，所以我们会去掉 Bootstrap。我们将把 **app.css** 替换为:

```
body {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  margin: 0;
  padding: 0;
  border: none;
}

#logo h1 {
  font-size: 20px;
  margin: auto;
  text-align: center;
  padding-bottom: 20px;
  color: #fff;
  background-color: #cc0000;
  padding-top: 10px;
  margin-bottom: 40px;
  text-transform: uppercase;
  letter-spacing: 2px;
}

.headline {
  font-weight: bold;
  text-align: center;
  font-size: 40px;
}

.button {
  display: inline-block;
  border: 4px solid #000;
  padding: 20px;
  color: #000;
  text-transform: uppercase;
  font-weight: bold;
  text-decoration: none;
  font-size: 12px;
  letter-spacing: 1px;
  margin: 35px;
}

.button:hover {
  transition: background-color 0.3s ease;
  background-color: #cc0000;
  color: #fff;
  border: 4px solid #cc0000;
}

.center_block {
  margin: auto;
  text-align: center;
}
```

CSS 使索引页面看起来更好一些。显然， **app/static** 目录保存了我们的 web 应用程序的静态元素(例如 Javascript、CSS 等)。).

### 助手

现在，我们的索引页面或多或少是静态的。我们希望将它链接到一个表单，让我们可以创建一个提醒。首先，我们需要修复我们的路由，以便为“提醒”设置资源路由(就像在 Rails 中一样):

```
resources "/reminders", ReminderController
```

这就建立了资源路径，但是在东西开始工作之前我们需要一个`ReminderController`。同样，我们在**web/controllers/reminder _ controller . ex**中创建新的控制器:

```
defmodule Remynders.ReminderController do
  use Remynders.Web, :controller

  plug :action

  def new(conn, _params) do
    render conn, "new.html"
  end
end
```

现在，我们将修复索引页面中的链接。首先，使用 **web/views/index_view.ex** 中的 URL 帮助器:

```
defmodule Remynders.IndexView do
  use Remynders.Web, :view
  alias Remynders.Router.Helpers

  def new_reminder_path do
    Helpers.reminder_path(Remynders.Endpoint, :new)
  end
end
```

我们可以通过打开**web/templates/index . html . eex**并替换:

```
<a href="#" class="button">Create a reminder</a>
```

有了这个:

```
<a href="<%= new_reminder_path %>" class="button">
```

这就是我们所做的。首先，我们在视图中添加了一个别名:

```
alias Remynders.Router.Helpers
```

这就让我们把`Remynders.Router.Helpers`仅仅称为`Helpers`。纯粹是为了方便。然后，我们使用实际的 URL 助手:

```
Helpers.reminder_path(Remynders.Endpoint, :new)
```

将它定义为视图中的一个函数。这让我们可以从实际的模板中调用函数来设置链接。与 Rails 的`link_to`相比，这一切看起来真的很复杂，事实证明还有另一种方法。我们做这个视图函数主要是为了演示如何从模板中调用视图中的方法。我们实际上可以用一个 HTML 助手替换我们的标签:

```
<%= link "Create Reminder", to: reminder_path(@conn, :new), class: "button" %>
```

现在，如果你打开索引页面，点击按钮，你会看到…一个错误页面。我们还没有设置视图！

## 直到下次

到目前为止，我们似乎还没有走得很远。我们只通过 Phoenix 提供了一些非常简单的页面，几乎没有开发出 URL 帮助器。但是，我们已经建立了一个坚实的基础，在此基础上，我们可以快速构建表单助手、模型、后台处理和 HTML5 通知 API。我们将在下一篇文章中涉及很多内容！

## 进一步阅读

Phoenix 的某些部分还没有很好的文档，但是我们使用的大部分内容可以在 [Phoenix 指南](http://www.phoenixframework.org/docs/overview)中找到。Phoenix 还附带了大量的[参考文档](http://hexdocs.pm/phoenix/)，这对弄清楚如何使用一个特定的函数非常有用。模板中大多数可用的 HTML 生成方法(如`link`)都是 Phoenix.HTML 的一部分，在[中有单独的文档](http://hexdocs.pm/phoenix_html/)。

## 分享这篇文章`