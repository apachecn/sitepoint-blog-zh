# forem——Rails 论坛引擎

> 原文：<https://www.sitepoint.com/forem-rails-forum-engine/>

![Roman Forum, Rome](img/c672e8cca937cad662fd5bc10d0a634b.png)

你曾经想要为你的网站建立一个论坛吗？有大量的解决方案可供你用来完成这项任务。然而，直接集成到你的 Rails 应用中的论坛呢？一个可以随意修改的？前阵子，我试图回答完全相同的问题，发现只有几个论坛宝石可用。

## 寻找一个完美的论坛

首先，有[变身兽](https://github.com/courtenay/altered_beast/)。它曾经有点流行，但是它最近一次更新是在 5 年前，它甚至不支持 Rails 3。

[论坛怪兽](https://github.com/codezomb/forum_monster)似乎是更好的选择。它提供了一些功能，最近一次更新是在 2013 年 7 月。但是，它并没有被积极地维护——2013 年只有三次提交，2014 年没有提交。

我还发现了[野蛮野兽](https://github.com/wbharding/savage-beast)(似乎论坛开发者喜欢怪物)，但它最后一次更新是在 2009 年，所以没有骰子。

我找到的最后一个解决方案是 [rBoard](https://github.com/radar/rboard) 但是它的主页上说这个项目已经被转移到 [Forem](https://github.com/radar/forem) 而不再维护。所以，我访问了 Forem 的页面，然后…找到了！Forem 似乎是一个成熟的论坛[引擎](http://guides.rubyonrails.org/engines.html)，有一个活跃的(嗯，不是**非常**活跃的社区)和许多有用的功能，包括:

*   基本操作(创建/更新/删除类别/论坛/主题)
*   锁定/锁定/隐藏主题
*   回复和引用帖子
*   管理用户组和分配版主
*   带有可插入格式化器的文本格式化，代码高亮，以及使用表情符号的能力
*   与 Gravatar 集成
*   主题化(能够创建[自定义主题](https://github.com/radar/forem/wiki/Theming)
*   [权限系统](https://github.com/radar/forem/wiki/Authorization-System)
*   翻译成多种语言
*   能够[与 RefineryCMS](https://github.com/radar/forem/wiki/Integration-with-Refinery-CMS) 整合。

Forem 是由 Ryan Bigg 创建的(并且主要是由他维护的)，并且(在撰写本文时)有超过 1300 个提交。

在本文中，我将演示如何设置 Forem，并指出一些问题。

工作演示可以在[http://sitepoint-forem.herokuapp.com](http://sitepoint-forem.herokuapp.com)找到。

源代码可以在 [GitHub](https://github.com/bodrovis/sitepoint-forem) 上获得。

## 一些陷阱(如承诺的)

嗯，我希望你对 Forem 提供的特性感到兴奋，并渴望开始将它集成到你的应用程序中。然而，在我们到达那里之前，我觉得有必要提醒你一下你可能会面临的问题。

首先明白 Forem 目前没有**没有**稳定版本。它正在积极地
开发中，所以新的功能可能会被添加，旧的功能可能会被删除或改变，恕不另行通知。有[路线图](https://github.com/radar/forem/wiki/1.0-Roadmap)可用，但是 1.0 版本什么时候出还真不好说。

此外，Forem 还有一些尚未解决的[问题](https://github.com/radar/forem/issues?state=open)(其中一些相当严重)，你也可能会发现新的问题。这可能意味着 Forem 不适合初学者(但是，我鼓励您尝试一下。)

此外，Forem 目前仅提供基于 Bootstrap 的单一主题。这个主题部分被破坏了，所以你肯定需要调整一些风格或者使用你自己的主题。

然而，所有这些问题都是可以解决的，Forem 仍然是一个非常酷的论坛引擎，有很多有用的特性。此外，在这一点上，它没有可行的替代方案。

## 准备应用程序

我将使用 Rails 4.1.1，但是你可以用 Rails 3 实现同样的解决方案

好了，聊够了，让我们开始编码吧。首先创建一个没有默认测试套件的新应用程序:

```
rails new forum -T
```

以下是我们将在本次演示中使用的宝石列表:

```
gem 'forem', github: "radar/forem", branch: "rails4"
gem 'forem-bootstrap', github: "radar/forem-bootstrap"
gem 'forem-redcarpet', github: "radar/forem-redcarpet"
gem 'devise'
```

我们还需要一个 gem 用于分页，您可以选择`will_paginate`或`kaminari`，因为 Forem 两者都支持:

```
gem 'will_paginate'
# or
gem 'kaminari'
```

如果您使用的是 Rails 3，请像这样指定`rails3`分支:

```
gem 'forem', github: "radar/forem", branch: "rails3"
```

首先，我们需要设置一些身份验证。Forem 的酷之处在于它允许您选择任何解决方案或从头开始编写自己的解决方案。对于本文，我们将使用 Devise，一个由 Plataformatec 创建的灵活的认证系统。如果您不熟悉，请查看[导轨](https://github.com/plataformatec/devise/wiki)。要开始，你只需要几个步骤。

运行设计生成器:

```
rails generate devise:install
```

你可能想要调整**initializer/device . Rb**中的一些设置(当你使用一个真正的应用程序时，你肯定会想要这样做)。出于本演示的目的，默认设置就可以了。完成后，运行另一个生成器来准备模型:

```
rails generate devise User
```

最后，运行迁移:

```
rake db:migrate
```

为登录、注册和其他您可能想要定制的表单设计一组视图。为此，发出以下命令:

```
rails g devise:views
```

这会将默认视图复制到您的应用程序中，您可以按照自己喜欢的方式调整它们。

## 安装 Forem

至此，我们已经准备好安装 Forem 了。它带有一个漂亮的生成器，会问你几个问题来创建初始化文件和迁移:

```
rails g forem:install
```

这些问题是:

*   你的用户类叫什么？(默认为`User`)
*   你 app 里的`current_user`帮手叫什么？(默认为`current_user`)

如果您选择“设计”并调用您的模型`User`，您可以安全地对这两个问题点击“回车”。Forem 的迁移将自动运行，所以我们已经完成了命令提示符。

打开您的 routes 文件(注意，已经有几个路由存在，它们是由 Devise 和 Forem 自动添加的),并添加一个根路由:

*routes.rb*

```
[...]
root to: 'pages#home'
```

然后创建`PagesController`和相应的`pages/home.html.erb`视图，其中没有内容。如果你使用 Rails 3，不要忘记删除`public/index.html`文件。

现在是时候调整我们的布局了。

*application.html.erb*

```
<% flash.each do |key, value| %>
  <%= content_tag(:div, value, class: "alert alert-#{key}") %>
<% end %>

<%= link_to 'Home', main_app.root_path %>
<%= link_to 'Forums', forem.forums_path %>

<header>
  <nav>
    <% if user_signed_in? %>
      <%= link_to current_user.email, main_app.edit_user_registration_path %>
      | <%= link_to "Sign out", main_app.destroy_user_session_path, :method => :delete %>
      <% if current_user.forem_admin %>
        | <%= link_to "Administrate", forem.admin_root_url %>
      <% end %>
    <% else %>
      <%= link_to "Sign up", main_app.new_user_registration_path %>
      | <%= link_to "Sign in", main_app.new_user_session_path %>
    <% end %>
  </nav>
</header>
```

注意，我没有只使用`root_path`，而是指定了`main_app.root_path`。这是因为 Forem 是一个 Rails 引擎，有自己独立的路由系统。否则，它可能会与应用程序的路线冲突。所以要访问 Forem 的路线，使用`forem.route_name`。

## 设置 Forem

下一步是向模型文件添加一个方法:

*user.rb*

```
[...]
def forem_name
  email
end
```

`forem_name`方法用于在他/她的文章旁边显示用户的名字。Devise 只提供了一个`email`列，所以我们用它来代替名称。在您的应用程序中，您可能希望在`users`表中添加一个`name`列，并为您的用户提供一种更改姓名的方法。

另外，记住 Forem 使用 [Gravatar](https://secure.gravatar.com/) 来显示用户头像，使用电子邮件来查找头像。如果用户表中没有电子邮件，可以向模型文件中添加另一个方法，并告诉 Forem 用户的电子邮件存储在哪里:

*user.rb*

```
[...]
def forem_email
  email_address
end
```

打开 **initializers/forem.rb** 文件。您可以在这里更改一些设置。现在，添加这段代码:

*initializer/forem . Rb*

```
Rails.application.config.to_prepare do
  Forem.layout = "application"
end
```

默认情况下，Forem 将使用自己的布局来显示论坛页面，但是我们希望指定自己的布局。请注意文档上说使用`Forem::ApplicationController.layout "application"`，但是我发现[在这个设置上有一个问题](https://github.com/radar/forem/issues/552)。因此，我们必须坚持使用`Forem.layout = "application"`。希望它能很快被修复。

Forem 还在供应商目录下创建了几个文件。如果你的 **application.js** 已经连接了 jQuery(默认情况下是这样的)，从**vendor/assets/JavaScript s/forem . coffee . js**中删除`//= require jquery`行。现在修改 **forem.css.scss** 文件:

*供应商/资产/样式表/forem.css.scss*

```
@import 'forem-bootstrap';
```

这一行为 Forem 导入引导主题(由`forem-bootstrap` gem 提供)。最后要做的任务是需要这两个文件:

*JavaScript s/application . js*

```
[...]
//= require forem
```

*样式表/application.css.scss*

```
@import 'forem/base';
```

此外，如果您使用的是 Rails 4，请添加 Forem 的资源进行预编译:

*initializer/asset.rb*

```
Rails.application.config.assets.precompile += %w( forem.css forem.js )
```

酷，我们已经完成了基本的设置。现在您可以启动您的服务器，注册一个新用户，并分配管理权限(打开`users`表并将`forem_admin`属性切换到`true`)。尝试创建一些类别，论坛，主题-一切都应该工作。请注意，默认情况下，所有创建的主题在对用户可见之前都被标记为“应被审核”。

## 格式化程序和表情符号

帖子的格式呢？多亏了`forem-redcarpet` gem，它应该工作得很好——甚至提供了代码高亮显示。目前，红地毯支持高亮显示，但我已经创建了一个 pull 请求，提议切换到[Coderay](http://coderay.rubychan.de/)——你可以在这里查看[。](https://github.com/radar/forem-redcarpet/pull/6)

还有[其他可用的格式化程序](https://github.com/radar/forem/wiki/Formatters)，但是(据我所知)其中一些在引用方面有问题——如果你想知道细节，请查看[这一期](https://github.com/radar/forem/issues/512)。

用户也可以在他们的帖子中使用表情符号。不幸的是，我在使用 Rails 4.1 时发现了一个问题——你可以在这里[读到这个问题。](https://github.com/radar/forem/issues/561)

## 结论

各位，今天就到这里吧！我们很快就成功地设置了 Forem 来使用我们的应用程序。顺便说一下，你可以在这里查看使用 Forem [的真实项目。](https://github.com/radar/forem#places-using-forem)

如果您在设置或使用 Forem 时遇到问题，请不要犹豫，在 GitHub 上提出[问题，提供所有相关信息。Bug 修复和新特性也非常受欢迎，所以您可以继续克隆 Forem repo，现在就开始调整它！](https://github.com/radar/forem/issues?state=open)

你的网站使用什么论坛解决方案？在评论里分享一下你的经历:)。

祝你在新创建的论坛上聊天愉快，并很快见到你！

## 分享这篇文章