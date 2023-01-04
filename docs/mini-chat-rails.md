# 使用 Rails 进行迷你聊天

> 原文：<https://www.sitepoint.com/mini-chat-rails/>

![Comments - Icon](img/9f86f398b3e2e33a911d453e2a7e1cae.png)

到处都是评论。您可能已经在您的一些项目中实现了某种注释系统(如果没有，那么您可能会对我的[“使用 Rails 的嵌套注释”](https://www.sitepoint.com/nested-comments-rails/)文章感兴趣)。

但是，如果我们想创建一个完全异步的评论系统，在这个系统中，用户在发送或接收新评论时不会经历任何页面重载，那该怎么办呢？我们可以使用什么工具来完成这项任务？我们可能会面临什么问题，如何解决这些问题？

在这个由两部分组成的系列文章中，我将向您展示如何通过六次迭代在 Rails 上创建一个全功能的迷你聊天。我们将讨论以下主题:

*   通过多个社交网络进行认证(OAuth 2，OmniAuth)
*   异步评论发布
*   无需重新加载页面就可以即时加载新的评论(我们将讨论两种可能的解决方案:AJAX 轮询和 Faye 的 web sockets)

我们还将讨论一些关于 AJAX 轮询和 Faye 安全性的注意事项。

源代码可以在 [GitHub](https://github.com/bodrovis/SitepointMiniChat) 获得。

在[http://sitepoint-minichat.herokuapp.com/](http://sitepoint-minichat.herokuapp.com/)查看工作演示。

## 一些计划

此演示将使用 Rails 4.1.5，但同样的解决方案也可以用 Rails 3 来实现。

好了，在开始我们的旅程之前，让我们写下对迷你聊天应用程序的要求:

*   只有经过身份验证的用户才能阅读和发表评论。
*   用户应该能够通过脸书或 Twitter 认证。
*   每个评论都应该提供用户的名字，头像，以及他在社交网络中的个人资料的链接。
*   评论应该立即发布和接收(嗯，几乎立即)，没有任何页面重新加载。
*   不允许空的和很长的注释。
*   当用户进入聊天时，他们应该能够阅读之前发布的一些评论。旧的评论应该自动删除。

很好，需求已经制定，是时候进行第一次(也可能是最大的一次)迭代了——抓紧！

## 证明

首先创建一个没有默认测试套件的新 Rails 应用程序:

```
$ rails new mini_chat -T
```

在深入研究代码之前，先停下来想一想我们要为这个应用程序实现的身份验证系统。因为我们希望我们的用户能够通过脸书和 Twitter 登录，所以将使用 [OAuth 2](http://tools.ietf.org/html/draft-ietf-oauth-v2-22) 协议。你可能听说过 OAuth2，但如果没有，不要担心，我会解释基本知识。

OAuth2 背后的主要思想是用户在平台的网站上输入自己的凭证(在我们的例子中是 Twitter 或脸书)。这样用户永远不会把自己的密码暴露给我们的 app 相反，网站只给出用户账户的一些信息(姓名、个人资料网址、头像等。)和一个用于发出 API 调用(例如，代表用户在脸书上发布消息)的密钥对(或单个密钥，具体取决于平台)。

关键的想法是，用户总是知道第三方资源能够执行什么操作，如果他不信任这个网站，他可以拒绝认证。此外，颁发给资源的密钥对不能用于执行任何操作——只允许资源最初请求的那些操作(这称为作用域)。该密钥对具有有限的生命周期，并且用户还可以随时手动撤销对任何先前已验证的资源的访问。

当身份验证阶段开始时，第三方网站会转发一个用于识别该网站的特殊应用程序密钥。资源想要执行的一系列动作也被发送。用户会收到一个对话框提示，说明该网站希望他进行身份验证，并要求获得执行一系列操作的权限。如果用户同意，他将被重定向回站点，其中包含刚刚通过身份验证的用户的信息，以及发出 API 调用的密钥对。

乍一看，这似乎很复杂。实际上，我们可以很容易地在 Rails 应用程序中实现这个场景。

首先，我们需要一个模型和一个表来存储关于认证用户的信息。实际上，我们甚至不需要 API 键，因为我们不打算发出任何 API 调用。在本演示中，我们将以下信息存储在`users`表中:

*   `provider`(`string`)–用于认证的社交网络的名称。在我们的例子中，它可能是“facebook”或“twitter”。
*   `name`(`string`)–用户的姓名(可选姓氏，但这取决于用户在创建个人资料时输入的内容)。
*   `profile_url`(`string`)–用户个人资料的链接。
*   `avatar_url`(`string`)——用户头像的链接。
*   `uid`(`string`)–用户在社交网络上的唯一标识符。稍后我们会看到为什么需要它。

继续创建所需的迁移:

```
$ rails g model User name:string avatar_url:string provider:string profile_url:string uid:string
```

现在打开 migration 的文件，在`change`方法的末尾添加以下几行:

*xxx_create_users.rb*

```
[...]
def change
  [...]
  add_index :users, :uid
  add_index :users, :provider
  add_index :users, [:uid, :provider], unique: true
end
[...]
```

正如您所看到的，我们在`uid`和`provider`列上创建了三个索引，以及这些列上的聚集索引，并要求它执行惟一性。这背后的想法是，不能有两个不同的用户具有相同的社交网络名称和唯一标识符。

应用迁移:

```
$ rake db:migrate
```

此时，我们可以转到身份验证本身。幸运的是，有一些宝石会让我们的生活变得更容易——它们是马克·多德维尔的 [omniauth-facebook](https://github.com/mkdynamic/omniauth-facebook/) 和阿伦·阿格拉瓦尔的 [omniauth-twitter](https://github.com/arunagw/omniauth-twitter) 。这个“omniauth”后缀是关于什么的？

OmniAuth 是 Intridea，Inc .创建的一个 gem，旨在标准化 web 应用程序的多提供商认证。OmniAuth 允许你使用尽可能多的不同认证策略，允许你的用户通过 OpenID、脸书、Twitter、Google 等进行认证。有许多策略可供选择，但是你可以[创造你自己的](https://github.com/intridea/omniauth/wiki/Strategy-Contribution-Guide)。对于这个应用程序，我们将使用两个策略:Twitter 和脸书，打包成这两个宝石。

将这些宝石放入*宝石文件*

*Gemfile*

```
[...]
gem 'omniauth-facebook'
gem 'omniauth-twitter'
```

然后跑

```
$ bundle install
```

现在我们必须在脸书和推特上注册我们的网站为应用程序。访问[https://developers.facebook.com/apps](https://developers.facebook.com/apps)并在此创建新应用。在设置部分给它起一个名字(我称之为“SitePoint 迷你聊天演示”)。不要忘记，这个名字将是可见的认证用户！接下来，点击“添加平台”，选择“网站”。填写“站点 URL”(在我的例子中，我输入了“http://Site point-mini chat . heroku App . com/”)和“应用程序域”(我输入了“Site point-mini chat . heroku App . com”)。请记住，该域必须从 URL 派生，否则您的设置将不会被保存。还要提供一个“联系电子邮件”并点击“保存”。导航到“状态&评论”并切换“你想让这个应用程序和它的所有实时功能对公众可用吗？”切换到“是”。

对于一个真正的应用程序，花一些时间调整其他应用程序的设置(如图片，描述，标语等。)我们将前往“仪表板”部分。这里，请注意“应用 ID”和“应用秘密”字段(后者被屏蔽)。点击“显示”,显示密码并输入您的密码——这些是我们设置身份验证所需的密钥。

在*配置/初始化器*中创建一个名为 *omniauth.rb* 的新文件，并粘贴以下代码:

*config/initializer/omni auth . Rb*

```
Rails.application.config.middleware.use OmniAuth::Builder do
  provider :facebook, ENV['FACEBOOK_KEY'], ENV['FACEBOOK_SECRET'],
           scope: 'public_profile', display: 'page', image_size: 'square'
end
```

为了存储密钥对，我使用了 [Heroku 环境变量](https://devcenter.heroku.com/articles/config-vars)。正如你可能已经猜到的那样，`FACEBOOK_KEY`是“应用 ID”,`FACEBOOK_SECRET`是“应用秘密”。

`scope`是我们的应用程序将请求的权限列表。因为我们不会访问任何脸书 API 函数，唯一请求的权限是`public_profile`，意思是“检索用户的基本数据”。点击这里阅读更多关于脸书[的权限。](https://developers.facebook.com/docs/facebook-login/permissions/v2.1)

`display`是在认证时发送给脸书的另一个参数，它控制认证页面的外观。例如，如果您在一个小弹出窗口中打开身份验证页面，那么您可以提供`popup`作为`display`值。在这里阅读更多。

最后一个选项是`image_size`，控制我们想要接收的用户头像的大小。`square`表示“50x50px 图像”。

现在我们需要为 Twitter 做同样的事情。导航到[https://apps.twitter.com/](https://apps.twitter.com/)和
创建一个新应用。给它一个名称、描述和 URL。另外，以下面的
格式输入回调 URL:“*apps 的 _URL* /auth/twitter/callback”。这种格式由 OmniAuth 定义，是用户在成功认证后将被重定向到的资源。我们一会儿会处理的。

接下来，接受条款并创建应用程序。你可能需要花一些时间来调整应用程序的其他设置，如组织名称、图标等。此外，请注意，在详细信息窗格中，您可以更改应用程序的访问级别。我们将保留默认的“只读”设置。

开放 API 密钥——这是 API 密钥和 API 秘密所在的位置。返回到您的 *omniauth.rb* 文件并添加一些代码:

*config/initializer/omni auth . Rb*

```
Rails.application.config.middleware.use OmniAuth::Builder do
  provider :twitter, ENV['TWITTER_KEY'], ENV['TWITTER_SECRET'], image_size: 'normal'
  [...]
```

将您的密钥直接粘贴到该文件中，或者使用其他方法来存储它们(这是推荐的方法，它们不应该对每个人都可见)。`image_size`选项以 48x48px 图像的形式提供用户的头像。有关可用选项的完整列表，请查看[文档](https://github.com/arunagw/omniauth-twitter#authentication-options)。

好了，我们已经完成了这个演示中可能是最乏味的任务，并准备继续。还记得回拨网址吗？我们现在需要创建相应的路线:

*config/routes.rb*

```
[...]
get '/auth/:provider/callback', to: 'sessions#create'
get '/auth/failure', to: 'sessions#auth_fail'
get '/sign_out', to: 'sessions#destroy', as: :sign_out
[...]
```

我还创建了`sign_out`和认证失败路径。

在控制器上:

*会话 _ 控制器. rb*

```
class SessionsController < ApplicationController
  def create
    user = User.from_omniauth(request.env['omniauth.auth'])
    cookies[:user_id] = user.id
    flash[:success] = "Hello, #{user.name}!"
    redirect_to root_url
  end

  def destroy
    cookies.delete(:user_id)
    flash[:success] = "See you!"
    redirect_to root_url
  end

  def auth_fail
    render text: "You've tried to authenticate via #{params[:strategy]}, but the following error occurred: #{params[:message]}", status: 500
  end
end
```

在`create`动作中，调用一个要创建的方法`from_omniauth`并传递`request.env['omniauth.auth']`作为参数。`request.env['omniauth.auth']`散列包含了关于被认证用户的所有信息(它被称为“认证散列”)。

创建用户后，将它的`id`存储在 cookie 中，这样我们就可以检查她是否通过了身份验证。然后，设置一条欢迎信息，将她重定向到`root_url`(也还不存在)。

`destroy`动作更简单。只需删除`user_id` cookie，设置再见消息，重定向到主页面。

如果您想看一下认证散列，您可以用下面这行代码替换`create`方法的代码:

```
render text: request.env['omniauth.auth'].to_yaml
```

然后导航到“ *your_app 的 _url* /auth/twitter/”(或“auth/facebook”)并观察结果。

我们需要`User.from_omniauth`方法。在这种方法中，用户应该被创建或找到(通过她唯一的 id 和社交网络名称)并被更新:

*型号/用户. rb*

```
class User < ActiveRecord::Base
  class << self
    def from_omniauth(auth)
      provider = auth.provider
      uid = auth.uid
      info = auth.info.symbolize_keys!
      user = User.find_or_initialize_by(uid: uid, provider: provider)
      user.name = info.name
      user.avatar_url = info.image
      user.profile_url = info.urls.send(provider.capitalize.to_sym)
      user.save!
      user
    end
  end
 end
```

这里我使用`symbolize_keys!`方法来调用类似`info.name`的方法。正如您所看到的，我们获取所有需要的用户信息，然后保存记录，并返回`user`作为结果。

您可能会注意到行`info.urls.send(provider.capitalize.to_sym)`。由于我们在这里与多个提供商合作，URL 通常以以下方式返回:

```
:urls => { :Twitter => "https://twitter.com/johnqpublic" }
```

我们只需要知道社交网络名称来获取用户的个人资料地址。点击查看
认证散列[的示例。](https://github.com/arunagw/omniauth-twitter#authentication-hash)

此时，我们已经完成了 95 %的身份验证。如果你需要添加更多的认证选项，比如 Open ID 或 Google，这很容易做到。只需通过添加另一个提供者来修改 *initializers/omniauth.rb* 文件，并测试认证哈希(一些社交网络可能会呈现与我们刚刚看到的略有不同的哈希)。

这里的最后一步是为我们的用户提供认证链接。在这样做之前，我们需要一个根路由和一个相应的视图，所以让我们在下一次迭代中完成它。

## 评论

在这个迭代中，我们需要切换到评论——毕竟我们正在构建一个消息应用程序。让我们称我们的模型为`Comment`，并让它包含以下字段(除了默认的`id`、`created_at`、`updated_at`):

*   `body`(`text`)–实际消息。
*   `user_id`(`integer`)–指向发布此消息的用户的外键。

如你所见，这是一个非常简单的模型。创建并应用迁移:

```
$ rails g model Comment body:text user:references
$ rake db:migrate
```

别忘了打开 *user.rb* 并添加这一行:

*型号/用户. rb*

```
[...]
has_many :comments, dependent: :delete_all
[...]
```

很好，现在我们准备添加一些路线:

*config/routes.rb*

```
[...]
resources :comments, only: [:new, :create]
root to: 'comments#new'
[...]
```

在这个演示中，我们只需要`new`和`create`动作。我们不打算建立一个后端来管理评论，但你可以自己添加这个功能。

相应的控制器(方法将在后面填充):

*comments_controller.rb*

```
class CommentsController < ApplicationController
  def new
    @comment = Comment.new
  end

  def create
  end
end
```

在创建视图之前，连接 [Bootstrap](http://getbootstrap.com/) 来帮助我们设计样式:

*Gemfile*

```
[...]
gem 'bootstrap-sass'
[...]
```

*application.css.scss*

```
@import 'bootstrap';
@import 'bootstrap/theme';
```

还要修改布局以利用 Bootstrap 的样式:

*layouts/application . html . erb*

```
[...]
<body>
  <div class="navbar navbar-default navbar-static-top">
    <div class="container">
      <div class="navbar-header">
        <%= link_to 'Mini-chat', root_path, class: 'navbar-brand' %>
      </div>
    </div>
  </div>

  <div class="container">
    <% flash.each do |key, value| %>
      <div class="alert alert-<%= key %>">
        <%= value %>
      </div>
    <% end %>

    <%= yield %>
  </div>
</body>
[...]
```

好了，现在我们可以转到唯一的观点了:

*comments/new.html.erb*

```
<div class="jumbotron">
  <h1>Welcome to our mini-chat!</h1>
  <p class="lead">Please sign in via one of the social networks to start chatting</p>
  <%= link_to '', '/auth/twitter', class: 'social twitter' %>
  <%= link_to '', '/auth/facebook', class: 'social facebook' %>
</div>
```

这是我们向用户展示身份验证链接的地方。 *auth* 部分由 OmniAuth 定义，后跟提供者的名称(在我们的例子中是 *facebook* 或 *twitter* )。让我们也对这些链接进行一些样式化处理。或许最简单的方法就是使用几乎任何互联网用户都能识别的社交网络图标。要下载官方图标，请访问以下资源:[https://www.facebookbrand.com/](https://www.facebookbrand.com/)和[https://about.twitter.com/press/brand-assets](https://about.twitter.com/press/brand-assets)。我已经创建了一个[精灵图像](https://github.com/bodrovis/SitepointMiniChat/blob/master/app/asseimg/logos.png)(在[http://csssprites.com/](http://csssprites.com/)的帮助下)你可以使用。

现在应用一些样式:

*application.css.scss*

```
[...]
.social {
  background: image-url('logos.png') no-repeat transparent;
  display: inline-block;
  &.twitter {
    width: 50px;
    height: 42px;
    background-position: 0 -52px;
  }

  &.facebook, &.facebook:active {
    width: 50px;
    height: 50px;
    background-position: 0 0;
  }
}
```

注意，我在这里使用`image-url`是因为，否则，我的背景图像将不会在 Heroku 上呈现。

在进入注释表单之前，我们需要检查用户是否经过身份验证。习惯上，这种方法被称为`current_user`:

*application _ controller . Rb*

```
def current_user
  @current_user ||= User.find_by(id: cookies[:user_id]) if cookies[:user_id]
end

helper_method :current_user
```

有了这个方法，我们可以为经过身份验证的用户提供一个“注销”链接:

*layouts/application . html . erb*

```
[...]
<div class="navbar navbar-default navbar-static-top">
  <div class="container">
    <div class="navbar-header">
      <%= link_to 'Mini-chat', root_path, class: 'navbar-brand' %>
    </div>

    <% if current_user %>
      <ul class="nav navbar-nav navbar-right">
        <li><%= image_tag current_user.avatar_url %></li>
        <li><%= link_to 'Sign out', sign_out_path %></li>
      </ul>
    <% end %>
  </div>
</div>
[...]
```

返回视图:

*comments/new.html.erb*

```
<% if current_user %>
  <div class="page-header">
    <h1>Join the discussion!</h1>
  </div>

  <%= render 'form' %>
<% else %>
  <%= render 'welcome' %>
<% end %>
```

我已经将欢迎消息和身份验证链接提取到一个单独的部分:

*comments/_ welcome . html . erb*

```
<div class="jumbotron">
  <h1>Welcome to our mini-chat!</h1>
  <p class="lead">Please sign in via one of the social networks to start chatting</p>
  <%= link_to '', '/auth/twitter', class: 'btn social twitter' %>
  <%= link_to '', '/auth/facebook', class: 'btn social facebook' %>
</div>
```

现在，创建*表单的*部分:

*comments/_form.html.erb*

```
<div class="well">
  <%= form_for @comment do |f| %>
    <div class="form-group">
      <%= f.label :body, 'Enter your comment:' %>
      <%= f.text_area :body, rows: 3, class: 'form-control', required: true, maxlength: 2000 %>
      Cannot be blank or contain more than 2000 symbols.
    </div>

    <%= f.submit 'Post', class: 'btn btn-primary btn-lg' %>
  <% end %>
</div>
```

这里没有什么特别的地方——只有一个输入消息的文本区和一个“发布”按钮。我们还需要一个验证来防止用户发送空的或很长的消息:

*models/comment.rb*

```
[...]
validates :body, presence: true, length: {maximum: 2000}
[...]
```

稍后，您还可以添加一些客户端验证(例如，借助于 [jQuery Validate 插件](http://jqueryvalidation.org))。

这里要做的最后一件事是充实`create`动作:

*comments_controller.rb*

```
[...]
def create
  if current_user
    @comment = current_user.comments.build(comment_params)
    if @comment.save
      flash[:success] = 'Your comment was successfully posted!'
    else
      flash[:error] = 'Your comment cannot be saved.'
    end
  end
  redirect_to root_url
end

private

def comment_params
  params.require(:comment).permit(:body)
end
[...]
```

我们的用户现在可以发表他们的评论。然而，他们不会看到它们。让我们解决这个问题！

*comments_controller.rb*

```
[...]
def new
  @comment = Comment.new
  @comments = Comment.order('created_at DESC')
end
[...]
```

*comments/new.html.erb*

```
<% if current_user %>
  <div class="page-header">
    <h1>Join the discussion!</h1>
  </div>

  <%= render 'form' %>
  <div class="panel panel-default" id="comments">
    <div class="panel-body">
      <ul class="media-list">
        <%= render @comments %>
      </ul>
    </div>
  </div>
<% else %>
  <%= render 'welcome' %>
<% end %>
```

我还使用了另一个将为`@comments`数组中的每个注释呈现的分部:

*comments/_ comment . html . erb*

```
<li class="media comment">
  <%= link_to image_tag(comment.user.avatar_url, alt: comment.user.name, class: "media-object"),
              comment.user.profile_url, target: '_blank', class: 'pull-left' %>
  <div class="media-body">
    <h4 class="media-heading"><%= link_to comment.user.name, comment.user.profile_url, target: '_blank' %> says
      [at <%= comment.created_at.strftime('%-d %B %Y, %H:%M:%S') %>]</h4>
    <p><%= comment.body %></p>
  </div>
</li>
```

最后，一点造型:

*application.css.scss*

```
[...]
#comments {
  max-height: 500px;
  overflow: auto;
}
```

此时，启动服务器，通过一个社交网络进行身份验证，并尝试发布一些评论。我们已经准备好进入下一个迭代了！

## 删除过多的注释

我们不想向用户展示自应用程序创建以来发布的所有消息。在这个迭代中，我们将创建一个预定的任务来删除过多的评论(显然，这个问题可以通过许多方法来解决)。

我将在这里使用 [Heroku Scheduler](https://devcenter.heroku.com/articles/scheduler) 插件，但是你可以使用另一个解决方案，比如 [Rufus Scheduler](https://github.com/jmettraux/rufus-scheduler) 。

可以使用以下命令安装该插件(请注意，您的应用程序应该已经在 Heroku 上创建好了):

```
$ heroku addons:add scheduler:standard
```

接下来创建实际任务:

*lib/tasks/scheduler.rake*

```
desc "This task is called by the Heroku scheduler add-on"

task remove_excessive_comments: :environment do
  puts "Removing excessive comments now..."
  Comment.remove_excessive!
end
```

和`remove_excessive!`方法:

*models/comment.rb*

```
[...]
class << self
  def remove_excessive!
    if all.count > 100
      order('created_at ASC').limit(all.count - 50).destroy_all
    end
  end
end
[...]
```

这里的数字是任意的，所以你可以随意调整。这里的大意是，如果有很多评论贴出来，除了最后 50 条之外，全部删除。

您可以通过运行以下命令来测试此任务

```
$ heroku run rake remove_excessive_comments
```

当你准备好跑的时候

```
$ heroku addons:open scheduler
```

并以所需的频率在此创建一个新任务(在“任务”字段中输入`rake remove_excessive_comments`)。

您还可以实现无限滚动，以便在用户向下滚动时逐渐加载旧的注释——查看我的[“Rails 中的无限滚动:基础知识”](https://www.sitepoint.com/infinite-scrolling-rails-basics/)文章以了解更多信息。

## 添加一点 AJAX

这将是今天的最后一次迭代。让我们添加一点 AJAX 来使评论发布过程异步。这样，页面就不会在每次用户发表评论时被重新加载。

如果你正在使用 [Turbolinks](https://github.com/rails/turbolinks/) ，我建议添加 [jquery-turbolinks](https://github.com/kossnocorp/jquery.turbolinks) gem 来带回默认页面加载事件(因为最初 Turbolinks 有自己的[事件](https://github.com/rails/turbolinks/#events))。

*Gemfile*

```
[...]
gem 'jquery-turbolinks'
[...]
```

*application.js*

```
[...]
//= require jquery.turbolinks
//= require turbolinks
```

要用 AJAX 装备我们的表单，只需要做一个修改:

*comments/_form.html.erb*

```
<%= form_for @comment, remote: true do |f| %>
```

控制器的`create`方法也应该被修改以处理 AJAX 请求:

*comments_controller.rb*

```
[...]
def create
  respond_to do |format|
    if current_user
      @comment = current_user.comments.build(comment_params)
      if @comment.save
        flash.now[:success] = 'Your comment was successfully posted!'
      else
        flash.now[:error] = 'Your comment cannot be saved.'
      end
      format.html {redirect_to root_url}
      format.js
    else
      format.html {redirect_to root_url}
      format.js {render nothing: true}
    end
  end
end
[...]
```

这里使用的`respond_to`方法以正确的格式响应:HTML(如果用户在浏览器中禁用了 JavaScript)或 JS。还需要创建一个新视图:

*comments/create.js.erb*

```
<% if @comment.new_record? %>
  alert('Your comment cannot be saved.');
<% else %>
  $('#comments').find('.media-list').prepend('<%= j render @comment %>');
  $('#comment_body').val('');
<% end %>
```

我们使用一个`if @comment.new_record?`条件来检查评论是否被保存。如果是——在注释列表中添加一个新的注释(通过渲染`_comment.html.erb`部分)并清除文本区域。如果注释没有保存，您可以通过警告(或以其他方式显示)发现的错误列表来扩展这个功能。

继续尝试新的基于 AJAX 的表单。的确，它运行得很好，但仍有许多工作要做。主要的问题是，用户仍然需要重新加载页面来查看其他人发布的内容，但是在真正的聊天中，一旦添加了新的评论，她就会看到。所以这是我们下次的任务！

## 结论

我们创建了一个消息应用程序，允许用户通过其中一个社交网络登录并发表评论。我们还添加了一个调度任务来删除过多的评论。

在下一部分中，我们将讨论为用户显示新评论的两种可能方式:
AJAX 轮询和 web 套接字。我们将讨论哪种解决方案更适合我们的需求，并指出一些问题。

## 分享这篇文章