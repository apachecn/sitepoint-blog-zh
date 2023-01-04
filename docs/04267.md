# 设计深度认证

> 原文：<https://www.sitepoint.com/devise-authentication-in-depth/>

![deviselogo](img/0614afa5e5f813e0c9b195fbef287277.png)

这是“使用 Rails 进行身份验证”系列的第二篇文章。我们将讨论由[platform tec](http://plataformatec.com.br/)开发的[design](https://github.com/plataformatec/devise/)，这是一个流行的、成熟的认证解决方案。

与[魔法](https://github.com/NoamB/sorcery)(我上次看了)相比，design 是一个更高层次的解决方案，可以为你照顾到许多不同的方面。它显示控制器、视图、邮件程序和路线。虽然发出几个已经设计好并正在运行的命令很简单，但它是高度可定制的。Devise 有非常全面的文档和一个大型社区，可以提供大量有用的扩展。难怪《设计》如此受欢迎。

Devise 附带了一些模块，允许您只选择需要的模块。有一个模块支持密码恢复、电子邮件确认、帐户锁定和许多其他功能。

在这篇文章中，我将回顾:

*   将设备集成到演示应用程序中
*   设置设备并启用特定模块
*   定制设备
*   限制对某些页面的访问
*   设置异步电子邮件传递
*   集成一个设备扩展来评估密码强度
*   向客户端添加密码强度评估

听起来不错？我们开始吧！

应用程序的演示在
上，源代码在 [Github](https://github.com/bodrovis/Sitepoint-source/tree/master/Authentication_with_Devise) 上。

## 基础

正如在本系列的前一篇文章中，我们将创建一个除了身份验证之外不提供任何功能的应用程序(带有一些相关的特性)。这次想不出什么花里胡哨的名字，就干脆叫“Devise Demo”:

```
$ rails new DeviseDemo -T
```

使用的是 Rails 4.2.0，但是 Devise 也兼容 Rails 3。

丢下一些宝石:

*Gemfile*

```
[...]
gem 'devise', '3.4.1'
gem 'bootstrap-sass'
[...]
```

与教程无关，但我喜欢它的造型。

下次运行

```
$ bundle install
```

勾搭 Bootstrap 的风格:

*application.scss*

```
@import "bootstrap-sprockets";
@import "bootstrap";
@import 'bootstrap/theme';
```

和修改布局:

*视图/布局/应用程序. html.erb*

```
[...]
<div class="container">
  <% flash.each do |key, value| %>
    <div class="alert alert-<%= key %>">
      <%= value %>
    </div>
  <% end %>
</div>

<%= yield :top_content %>

<div class="container">
    <h1><%= yield :header %></h1>
  <%= yield %>
</div>
[...]
```

请注意，您必须以某种方式添加 flash 渲染功能，因为 Devise 依赖它来显示各种消息。当您第一次使用 Devise 运行`bundle`时，您可能会看到一条关于它的消息。

如你所见，我在这里使用了`yield`来放置一些额外的内容，叫做`top-content`和`header`。此外，我添加了这个帮助器方法来提供标题:

*application_helper.rb*

```
[...]
def header(text)
  content_for(:header) { text.to_s }
end
[...]
```

让我们也建立我们的主页。创建`PagesController`

*pages_controller.rb*

```
class PagesController < ApplicationController
end
```

然后是观点:

*views/pages/index . html . erb*

```
<% content_for :top_content do %>
  <div class="jumbotron">
    <div class="container">
      <h1>Welcome!</h1>
      <p>Register to get started.</p>
      <p>
        <%= link_to 'Register', new_user_registration_path, class: 'btn btn-primary btn-lg' %>
      </p>
    </div>
  </div>
<% end %>
```

将这些联系在一起的方法是:

*config/routes.rb*

```
[...]
root to: 'pages#index'
[...]
```

最后，为开发配置默认的 URL 选项:

*config/environments/development . Rb*

```
[...]
config.action_mailer.default_url_options = { host: '127.0.0.1', port: 3000 }
[...]
```

这是在电子邮件视图中正确生成链接所必需的。

才华横溢，现在是时候整合定计了！

## 集成设备

首先，运行以下命令来生成 Devise 的配置文件和翻译:

```
$ rails generate devise:install
```

config/initializer/device . Rb 包含许多不同的配置选项，这些选项都有很好的文档记录。我们将在一会儿调整这个文件。*config/locales/device . en . yml*用英语呈现特定于设备的翻译。这里有大量其他语言的翻译[。](https://github.com/plataformatec/devise/wiki/I18n)

接下来，生成一个包含 Devise 所需的附加列的模型:

```
$ rails generate devise User
```

您可以用任何其他名称替换`User`。这个命令将创建一个 *user.rb* 模型文件和一个添加所有必要字段的迁移。如果用户模型已经存在，它将被更新。

打开模型文件，查看它包含的内容。最重要的一行是:

*型号/用户. rb*

```
[...]
devise :database_authenticatable, :registerable,
       :recoverable, :rememberable, :trackable, :validatable
[...]
```

这是该型号激活的设计模块列表。

*   `database_authenticatable`–用户将能够使用存储在数据库中的登录名和密码进行身份验证。(密码以[摘要](https://en.wikipedia.org/wiki/Cryptographic_hash_function)的形式存储)。
*   `registerable`–用户可以注册、更新和销毁他们的个人资料。
*   `recoverable`–提供重置遗忘密码的机制。
*   `rememberable`–启用包含 cookies 的“记住我”功能。
*   `trackable`–跟踪登录次数、时间戳和 IP 地址。
*   `validatable`–验证电子邮件和密码(可以使用自定义验证器)。

看到了吗？Devise 会为您考虑许多不同的方面，只需选择所需的模块即可！

在本演示中，我们还将使用另外两个模块:

*   `confirmable`–用户必须在注册后确认他们的电子邮件，然后才能被允许登录。
*   `lockable`–在多次不成功的验证尝试后，用户的帐户将被锁定。

像这样修改模型:

*user.rb*

```
[...]
devise :database_authenticatable, :registerable,
       :recoverable, :rememberable, :trackable, :validatable, :confirmable, :lockable
[...]
```

您还必须编辑迁移文件，因此打开它并取消对以下行的注释:

*db/migrations/XXX _ device _ create _ users . Rb*

```
[...]
t.string   :confirmation_token
t.datetime :confirmed_at
t.datetime :confirmation_sent_at
t.string   :unconfirmed_email
t.integer  :failed_attempts, default: 0, null: false
t.string   :unlock_token
t.datetime :locked_at
add_index :users, :confirmation_token,   unique: true
add_index :users, :unlock_token,         unique: true
[...]
```

这些字段是可确认和可锁定模块正确运行所必需的。允许用户提供他们的名字也是一个不错的主意，所以再添加一行:

*db/migrations/XXX _ device _ create _ users . Rb*

```
[...]
t.string :name
[...]
```

现在，运行迁移:

```
$ rake db:migrate
```

现在已经创建了用户表，Devise 已经启动并运行。然而，我们仍然有一些事情要做。

## 设置设备

如前所述，Devise 设置存在于*device . Rb*初始化文件中。打开它，找到“配置:可锁定”部分。这里的大多数设置都将被注释掉，因此取消注释并提供以下值:

*   `config.lock_strategy = :failed_attempts`–这意味着在多次登录尝试失败后，帐户将被锁定。实际上，这是唯一可用的策略，但是您可以将该设置设置为`none`并自己处理锁定机制。
*   `config.unlock_strategy = :both`–可以通过电子邮件(通过访问 Devise 发送的电子邮件中提供的链接)或等待一定时间来解锁帐户。提供`:email`或`:time`，仅启用这两个选项中的一个。提供`:none`自行处理解锁过程。
*   `config.maximum_attempts = 20`–在帐户被锁定之前，用户连续输入错误密码的次数。
*   `config.unlock_in = 1.hour`–一个帐户将被解锁多长时间。仅当您使用`:time`或`:both`解锁策略时，才提供此设置。
*   `config.last_attempt_warning = true`–当用户还剩一次登录尝试时发出警告。该警告将以闪烁消息的形式显示。

很好，现在查找“配置:可确认”部分，取消对这些设置的注释:

*   `config.confirm_within = 3.days`–用户通过电子邮件中发送的链接激活账户的时间(基本上这意味着由 Devise 生成的激活令牌将不再有效)。如果帐户未激活，可以请求新的令牌。如果您不想激活令牌过期，请将其设置为`nil`。
*   `config.reconfirmable = true`–当通过个人资料更新更改电子邮件时，用户是否应该重新确认电子邮件。这个过程和报名后的确认过程一样。这个新的、未确认的电子邮件被存储在`unconfirmed_email`字段中，直到用户访问激活链接。在此期间，旧的电子邮件用于登录。

如果您正在构建一个真正的应用程序，也不要忘记调整这些设置:

*   `config.mailer_sender`–在此提供一封电子邮件，该邮件将被放入“发件人”字段。
*   `config.secret_key`–生成各种令牌的密钥。修改此项将使所有以前生成的令牌无效。

该文件中还有许多设置可能需要更改。例如，`config.password_length`设置最小和最大密码长度(默认为`8..128`)。

## 定制设备

### 生成视图

好了，现在我们要按照我们喜欢的方式来设计。启动服务器，导航到主页，然后单击“注册”链接。您将看到一个基本表单，但有两件事需要注意:

*   此表单不允许您提供名称，即使我们在迁移中添加了名称。
*   如果你正在使用 Bootstrap 进行造型，表单看起来会不好看(好吧，这不是一个巨大的问题，但我们仍然要考虑到它。)

幸运的是，有一种方法可以定制 Devise 提供的所有视图。奔跑

```
$ rails generate devise:views
```

将默认的设备视图直接复制到应用程序文件夹中。在*视图*目录下将创建一个名为*设计*的新文件夹。让我简单介绍一下里面的所有文件夹:

*   *确认*——当用户请求重新发送确认电子邮件时，它有一个单独的 *new.html.erb* 视图。
*   邮件程序(mailer)–所有电子邮件的模板都存储在这里。
*   *密码*–请求密码、重置电子邮件和实际更改密码的表单视图。
*   *注册*—*new . html . erb*视图在用户在网站上注册时呈现。 *edit.html.erb* 包含一个更新个人资料的表单。
*   *sessions*–只有一个视图，即站点的登录表单。
*   *共享*–这里只有一个部分，其中包含显示在每个设备页面上的链接(比如“忘记密码了？”、“重新发送确认邮件”等。)
*   *解锁*–只有一个带有表单的视图可以请求带有解锁链接的电子邮件。

如果您只想为某些模块(例如，`registerable`和`confirmable`)定制视图，请运行以下命令:

```
$ rails generate devise:views -v registrations confirmations
```

您甚至可以为不同的型号提供单独的视图(如果您的应用中有多个型号配备了 Devise):

```
$ rails generate devise:views users
```

点击阅读更多[。](https://github.com/plataformatec/devise/#configuring-views)

### 登记表

现在，让我们通过添加一个字段并对其进行样式化来更改提供注册表单的视图:

*views/device/registrations/new . html . erb*

```
<% header "Sign Up" %>

<%= form_for(resource, as: resource_name, url: registration_path(resource_name)) do |f| %>
  <%= devise_error_messages! %>

  <div class="form-group">
    <%= f.label :name %>
    <%= f.text_field :name, class: 'form-control' %>
  </div>

  <div class="form-group">
    <%= f.label :email %>
    <%= f.email_field :email, class: 'form-control' %>
  </div>

  <div class="form-group">
    <%= f.label :password %>
    <%= f.password_field :password, autocomplete: "off", class: 'form-control' %>
    <% if @validatable %>
      <span class="help-block"><%= @minimum_password_length %> characters minimum</span>
    <% end %>
  </div>

  <div class="form-group">
    <%= f.label :password_confirmation %>
    <%= f.password_field :password_confirmation, autocomplete: "off", class: 'form-control' %>
  </div>

  <%= f.submit "Sign up", class: 'btn btn-primary' %>
<% end %>

<%= render "devise/shared/links" %>
```

是我们显示页面标题的帮助方法。`devise_error_messages!`呈现试图保存记录时发现的任何错误。

重启服务器并注册一个新用户。你现在可以提供你的名字了。

### 主菜单和 Flash 消息

重定向回主页后，您会注意到更多问题:

*   没有办法识别哪个用户当前登录了。
*   没有办法注销。
*   欢迎 flash 消息的样式不正确(如果您坚持使用 Bootstrap)。

前两个问题很容易通过添加如下主菜单来解决:

*视图/布局/应用程序. html.erb*

```
<nav class="navbar navbar-inverse">
  <div class="container">
    <div class="navbar-header">
      <%= link_to 'Devise Demo', root_path, class: 'navbar-brand' %>
    </div>
    <div id="navbar">
      <ul class="nav navbar-nav">
        <li><%= link_to 'Home', root_path %></li>
      </ul>
      <ul class="nav navbar-nav pull-right">
        <% if user_signed_in? %>
          <li class="dropdown">
            <a class="dropdown-toggle" data-toggle="dropdown" href="#">
              <%= current_user.name %>
              <span class="caret"></span>
            </a>
            <ul class="dropdown-menu" role="menu">
              <li><%= link_to 'Profile', edit_user_registration_path %></li>
              <li><%= link_to 'Log out', destroy_user_session_path, method: :delete %></li>
            </ul>
          </li>
        <% else %>
          <li><%= link_to 'Log In', new_user_session_path %></li>
          <li><%= link_to 'Sign Up', new_user_registration_path %></li>
        <% end %>
      </ul>
    </div>
  </div>
</nav>
```

`user_signed_in?`是判断用户是否登录的 Devise ' helper 方法。如果没有人登录，则`current_user`返回用户记录或`nil`。请注意，如果您在生成 Devise ' migration 时不同地调用了您的模型，那么那些帮助器方法也将有不同的名称！对于一款`Admin`车型，应该是`admin_signed_in?`和`current_admin`。元编程很酷。

所有路线(当然除了`root_path`)也由 Devise 呈现。注意`destroy_user_session_path`(注销)默认需要 DELETE HTTP 方法。如果你不喜欢，修改*device . Rb*初始化器中的`config.sign_out_via`。

出于样式的目的，我利用 Bootstrap 的下拉菜单。如果你也在使用它，就需要更多的动作。Dropdown 依赖于 JavaScript 代码，使用 [Turbolinks](https://github.com/rails/turbolinks) ，在页面间导航时不会正确执行。使用 [jquery-turbolinks](https://github.com/kossnocorp/jquery.turbolinks) 来解决这个问题:

*Gemfile*

```
[...]
gem 'jquery-turbolinks'
[...]
```

别忘了跑步

```
$ bundle install
```

现在修改 *application.js* 文件:

*application.js*

```
[...]
//= require jquery
//= require jquery.turbolinks
//= require jquery_ujs
//= require bootstrap/dropdown
//= require turbolinks
[...]
```

要设计特定于设备的 flash 消息的样式，使用 Sass `@extend`方法是一个简单的方法(不要过度使用它，因为这个指令有一些缺点):

*application.scss*

```
[...]
.alert-alert {
  @extend .alert-warning;
}

.alert-notice {
  @extend .alert-info;
}
[...]
```

重新加载服务器，看看下拉菜单。为什么没有显示用户名？

### Strong_params 和编辑配置文件页面

如果您使用带有 strong_params 的 Rails(默认情况下在 Rails 4 中启用)，还需要一个步骤:
`:name`属性必须被列入白名单。

*application _ controller . Rb*

```
[...]
before_action :configure_permitted_parameters, if: :devise_controller?

protected

def configure_permitted_parameters
  devise_parameter_sanitizer.for(:sign_up) << :name
  devise_parameter_sanitizer.for(:account_update) << :name
end
[...]
```

我们向所有将`:name`属性列入白名单的 Devise controllers 添加了一个`before_action`，用于注册和帐户更新操作(还有一个`:sign_in`选项可用)。如果你有一个稍微复杂一点的嵌套散列或数组的场景，请参考文档的[这一节](https://github.com/plataformatec/devise/#strong-parameters)。

在转到个人资料页面更改您的姓名之前，应更新相应的视图:

*views/device/registrations/edit . html . erb*

```
<% header "Edit #{resource_name.to_s.humanize}" %>

<%= form_for(resource, as: resource_name, url: registration_path(resource_name), html: { method: :put }) do |f| %>
  <%= devise_error_messages! %>

  <div class="form-group">
    <%= f.label :name %>
    <%= f.text_field :name, class: 'form-control' %>
  </div>

  <div class="form-group">
    <%= f.label :email %>
    <%= f.email_field :email, class: 'form-control' %>
    <% if devise_mapping.confirmable? && resource.pending_reconfirmation? %>
      <span class="label label-info">Currently waiting confirmation for: <%= resource.unconfirmed_email %></span>
    <% end %>
  </div>

  <div class="form-group">
    <%= f.label :password %>
    <%= f.password_field :password, autocomplete: "off", class: 'form-control' %>
    <span class="help-block">leave blank if you don't want to change it</span>
  </div>

  <div class="form-group">
    <%= f.label :password_confirmation %><br />
    <%= f.password_field :password_confirmation, autocomplete: "off", class: 'form-control' %>
  </div>

  <div class="form-group">
    <%= f.label :current_password %>
    <%= f.password_field :current_password, autocomplete: "off", class: 'form-control' %>
    <span class="help-block">we need your current password to confirm your changes</span>
  </div>

  <%= f.submit "Update", class: 'btn btn-primary' %>
<% end %>

<h3>Cancel my account</h3>

<p>Unhappy?
  <%= button_to "Cancel my account", registration_path(resource_name), class: 'btn btn-danger',
                data: { confirm: "Are you sure?" }, method: :delete %>
</p>

<%= link_to "Back", :back, class: 'btn btn-default btn-sm' %>
```

请注意，用户还可以更改他们的电子邮件地址(如我们之前所讨论的，应该确认)、密码，甚至完全删除配置文件。

我不会展示其他设备的视图是如何设计的——如果你感兴趣，可以浏览我的 GitHub repo。

## 发送电子邮件和延迟作业

下面简单讨论一下邮件发送流程。首先，您应该记住，在开发环境中，默认情况下不会发送电子邮件。尽管如此，您仍然可以在控制台中看到它们的内容。要启用发送:

*config/environments/development . Rb*

```
[...]
config.action_mailer.perform_deliveries = true
[...]
```

并配置 ActionMailer(见示例[此处](http://guides.rubyonrails.org/action_mailer_basics.html#example-action-mailer-configuration))。

接下来，对于生产，您还必须添加类似的配置。以下是我对演示应用程序的配置:

*配置/环境/生产. rb*

```
[...]
config.action_mailer.delivery_method = :smtp
config.action_mailer.default_url_options = { host: 'sitepoint-devise.herokuapp.com' }

ActionMailer::Base.smtp_settings = {
    :address        => 'smtp.sendgrid.net',
    :port           => '587',
    :authentication => :plain,
    :user_name      => ENV['SENDGRID_USERNAME'],
    :password       => ENV['SENDGRID_PASSWORD'],
    :domain         => 'heroku.com',
    :enable_starttls_auto => true
}
[...]
```

由于这个应用程序运行在 Heroku 上，我不得不使用它的一个插件来发送邮件(你将无法直接发送电子邮件)。在我的设置中，使用了 [Sendgrid](https://addons.heroku.com/sendgrid) 。请注意，它是运行在一个免费计划，所以传递邮件可能需要一些时间。

顺便说一句，如果你想使用自定义邮件程序，改变*device . Rb*中的`config.mailer`设置。

最后，在后台执行电子邮件发送可能是个好主意，否则用户将不得不等到邮件发送后才能被重定向到新页面。让我们以 [DelayedJob](https://github.com/collectiveidea/delayed_job) 集成 Devise 为例。

扔进一颗新宝石:

*Gemfile*

```
[...]
gem 'delayed_job_active_record'
[...]
```

然后跑

```
$ bundle install
$ rails generate delayed_job:active_record
$ rake db:migrate
```

安装 gem 并生成和应用所需的迁移(将创建一个新表来存储计划任务)。

对于 Rails 4.2，修改 *application.rb* 文件以设置队列后端:

*配置/应用. rb*

```
[...]
config.active_job.queue_adapter = :delayed_job
[...]
```

现在，只需覆盖模型中的 Devise '方法:

*型号/用户. rb*

```
[...]
def send_devise_notification(notification, *args)
  devise_mailer.send(notification, self, *args).deliver_later
end
[...]
```

`deliver_later`表示发送将排队。太好了！

如果您打算在 Heroku 上部署该应用程序，请在项目的根目录下创建一个 *Procfile* ，并添加以下代码行:

procfile

```
worker: rake jobs:work
```

请注意，您必须启用至少一个工作进程来处理作业，这将花费您每月大约 35 美元:

```
$ heroku ps:scale worker=1
```

关于在 Heroku 上使用 DelayedJob，有一个相当全面的[指南](https://devcenter.heroku.com/articles/delayed-job)，所以请参考它以了解更多细节。

请注意，我不会在我的演示应用程序上启用后台发送，但相应的代码在 GitHub repo 中。

## 限制访问

到目前为止，我们做得很好，但是限制对特定页面的访问怎么办？如何做到这一点？

似乎 Devise 也注意到了这一点:您只需使用相应的方法作为`before_action`。首先，我们需要一个特殊的页面来限制。为简单起见，我们称之为“秘密”:

*config/routes.rb*

```
[...]
get '/secret', to: 'pages#secret', as: :secret
[...]
```

一个非常简单的观点:

*views/pages/secret . html . erb*

```
<% header "Secret!" %>
```

您甚至不需要将`secret`方法添加到`PagesController`中——Rails 会隐式定义它。

稍微修改一下菜单:

*视图/布局/应用程序. html.erb*

```
[...]
<ul class="nav navbar-nav">
  <li><%= link_to 'Home', root_path %></li>
  <% if user_signed_in? %>
    <li><%= link_to 'Secret', secret_path %></li>
  <% end %>
</ul>
[...]
```

现在调整控制器:

*pages_controller.rb*

```
[...]
before_action :authenticate_user!, only: [:secret]
[...]
```

这个`before_action`将在调用`secret`方法之前检查用户是否被认证。未经身份验证的用户将被重定向到带有“请进行身份验证”提示消息集的登录页面。再次注意，对于不同命名的模型，这个方法将有不同的名称(`authenticate_admin!`代表`Admin`模型)。

试试看！

## 使用设计扩展

大型的 Devise 社区已经开发了许多优秀的扩展来增加更多的功能。让我们以 Bit Zesty 整合[设计 Zxcvbn](https://github.com/bitzesty/devise_zxcvbn) 为例。

此扩展用于测量密码强度并拒绝弱密码。它依赖于 [zxcvbn-ruby](https://github.com/envato/zxcvbn-ruby) ，而后者又是 Dropbox 的 [zxcvbn.js](https://github.com/dropbox/zxcvbn) 的 ruby 端口。阅读[这篇](https://blogs.dropbox.com/tech/2012/04/zxcvbn-realistic-password-strength-estimation/)官方博客文章，了解这个解决方案是如何运作的。

扔进一颗新宝石:

*Gemfile*

```
[...]
gem 'devise_zxcvbn'
[...]
```

然后跑

```
$ bundle install
```

接下来为您的模型注册一个新模块:

*型号/用户. rb*

```
[...]
devise :database_authenticatable, :registerable,
       :recoverable, :rememberable, :trackable, :validatable, :confirmable, :lockable, :zxcvbnable
[...]
```

现在`User`是 zxcvbnable(试着发音)。此扩展只有一个设置:

*config/initializer/device . Rb*

```
[...]
config.min_password_score = 0
[...]
```

这基本上意味着密码应该有多强(越高越强)。强度被解释为估计的开裂时间:

*   `0`–预计破解时间小于`10 ** 2`秒。
*   `1`–`10 ** 4`秒。
*   `2`–`10 ** 6`秒。
*   `3`–`10 ** 9`秒。
*   无限(嗯，不完全是，但在这种情况下，我们可以认为几个世纪是无限的)。

我在这里设置了`0`,这样用户就不必考虑复杂的密码，但不建议在真正的应用程序中使用这样的弱密码。

最后，定制错误消息:

*config/locales/currency . en . yml*

```
[...]
en:
  errors:
    messages:
      weak_password: "is not strong enough. Consider adding a number, symbols or more letters to make it stronger."
[...]
```

尝试各种密码并检查它们的强度。

### 在客户端测量密码的强度

最后，让我向您展示如何将 zxcvbn 添加到客户端。下载并连接[这个文件](https://github.com/dropbox/zxcvbn/blob/master/zxcvbn.js)到你的项目中。用以下内容创建一个新的 CoffeeScript 文件:

*global.coffee*

```
jQuery ->
  displayHint = (strength, crack_time) ->
    msg = 'Password is ' + strength + ' (time to break it: ' + crack_time + ')'

    estimate_message = this.next('.estimate-message')
    if estimate_message.length > 0
      estimate_message.text msg
    else
      this.after '<span class="help-block estimate-message">' + msg + '</span>'

  $('form').on 'keyup', '.estimate-password', ->
    $this = $(this)
    estimation = zxcvbn($this.val())
    crack_time = estimation.crack_time_display

    switch estimation.score
      when 0 then displayHint.call($this, "very weak", crack_time)
      when 1 then displayHint.call($this, "weak", crack_time)
      when 2 then displayHint.call($this, "okay", crack_time)
      when 3 then displayHint.call($this, "strong", crack_time)
      when 4 then displayHint.call($this, "very strong", crack_time)

  return
```

把它挂起来:

*application.js*

```
[...]
//= require global
[...]
```

返回从 0 到 4 的数字——我们刚才已经讨论过了——所以这似乎是衡量力量最方便的方法。`crack_time_display`以友好的格式返回破解密码所需的估计时间(“3 天”、“4 年”、“世纪”等)。

现在，将`estimate-password`类分配给任何您想要的密码字段，例如:

*views/device/registrations/new . html . erb*

```
[...]
<div class="form-group">
  <%= f.label :password %>
  <%= f.password_field :password, autocomplete: "off", class: 'form-control estimate-password' %>
  <% if @validatable %>
    <span class="help-block"><%= @minimum_password_length %> characters minimum</span>
  <% end %>
</div>

<div class="form-group">
  <%= f.label :password_confirmation %>
  <%= f.password_field :password_confirmation, autocomplete: "off", class: 'form-control estimate-password' %>
</div>
[...]
```

当然，这是最简单的解决方案，所以可以随意进一步扩展。

## 结论

这就把我们带到了本文的结尾。我们已经将 Devise 集成到应用程序中，查看了它的模块和设置，并添加了一个扩展。还有更多可用的功能和扩展，所以我鼓励你看看[官方维基](https://github.com/plataformatec/devise/wiki)，特别是[的操作指南](https://github.com/plataformatec/devise/wiki/How-Tos)——它有 90 多个小但有用的教程。

我希望您喜欢 Rails 认证系列的第二部分！在下一篇文章中，我们将讨论 OAuth 2 的认证。

编码快乐，再见！

## 分享这篇文章