# 用巫术进行魔法认证

> 原文：<https://www.sitepoint.com/magical-authentication-sorcery/>

![Magician Hat](img/b76b14efa996ad1c10b06fa799a63594.png)

身份验证是许多 web 应用程序的重要组成部分。我不需要说服您，身份验证必须易于使用，同时受到良好的保护。你既可以从头开始写，也可以使用现有的许多宝石之一。

本文是即将推出的 Rails 认证系列文章的第一篇。我们将看看[巫术](https://github.com/NoamB/sorcery)，一个不太为人所知，但非常方便易用的宝石，由诺姆·本·阿里和其他人创造。与[设计](https://github.com/plataformatec/devise/)相比，巫术的层次要低一些，需要开发者执行一些额外的动作。然而，这是一件好事，因为您可以只挑选所需的功能。

在子模块的帮助下增加了额外的选项。最初，巫术只提供最少的[功能集](https://github.com/NoamB/sorcery#full-features-list-by-module)。有子模块来启用用户激活、暴力保护、OAuth 2 支持等等。决定你的应用程序需要什么真的取决于你自己。“少即是多”是巫术的主要[原则之一。](https://github.com/NoamB/sorcery#philosophy)

我希望你对更好地了解巫术感到兴奋。:)继续读下去，让我们一起构建一个演示应用程序！

源代码可以在 [GitHub](https://github.com/bodrovis/SitePoint-Sorcery) 上获得。

工作演示可在[sitepoint-sorcery.herokuapp.com](http://sitepoint-sorcery.herokuapp.com)获得。

## 准备应用程序

我将把我的演示应用程序命名为“magic”——毕竟我们集成了 magic 认证:

```
$ rails new Magical -T
```

将使用 Rails 4.2.0，但是可以用 Rails 3 实现相同的解决方案。这个演示应用程序除了认证和相关功能之外不会提供任何功能，但是我们需要至少一个页面用于测试目的，该页面应该只能由经过认证的用户访问，因此创建一个基本的`PagesController`:

*pages_controller.rb*

```
class PagesController < ApplicationController
  def index
  end
end
```

相应的观点:

*views/pages/index . html . erb*

```
<h1>Welcome!</h1>

<p>Restricted area for authorized users only.</p>
```

并添加路线:

*config/routes.rb*

```
[...]
get '/secret', to: 'pages#index', as: :secret
root to: 'pages#index'
[...]
```

我还将使用 Twitter Bootstrap 来对应用程序进行一些设计:

*Gemfile*

```
[...]
gem 'bootstrap-sass', '~> 3.3.3'
[...]
```

*application.scss*

```
@import "bootstrap-sprockets";
@import "bootstrap";
@import "bootstrap/theme";

.nav > li > span {
  display: block;
  padding-top: 15px;
  padding-bottom: 15px;
  color: #9d9d9d;
}
```

*视图/布局/应用程序. html.erb*

```
[...]
<nav class="navbar navbar-inverse">
  <div class="container">
    <div class="navbar-header">
      <%= link_to 'Magical', root_path, class: 'navbar-brand' %>
    </div>
    <div id="navbar">
      <ul class="nav navbar-nav">
        <li><%= link_to 'Secret Page', secret_path %></li>
      </ul>
    </div>
  </div>
</nav>

<div class="container">
  <% flash.each do |key, value| %>
    <div class="alert alert-<%= key %>">
      <%= value %>
    </div>
  <% end %>

  <%= yield %>
</div>
[...]
```

好的，准备工作都完成了…很快，不是吗？现在，让我们将巫术整合到应用程序中！

## 综合巫术

### 模型和迁移

首先，将宝石放入你的*宝石档案*:

*Gemfile*

```
[...]
gem 'sorcery'
[...]
```

然后跑

```
$ bundle install
```

巫术的整合应该不会引起任何困难，但是有几个[已知的不兼容性](https://github.com/NoamB/sorcery/wiki/Known-incompatibilities)你应该考虑。

我们需要生成巫术的配置和迁移。起初，巫术只提供最基本的功能——认证本身。没有暴力保护，没有“记住我”，没有“恢复密码”，甚至没有电子邮件有效性检查。然而，这意味着你可以只挑选你真正需要的特性。

继续运行命令:

```
$ rails g sorcery:install
```

这将创建*配置/初始化器/巫术. rb* 文件、`User`模型和迁移。魔法给了用户三个领域:

*   `crypted_password` ( `string`)
*   `salt` ( `string`)
*   `email` ( `string`)

如果您希望为模型指定一个不同的名称，请提供`--model`标志:

```
$ rails g sorcery:install --model Admin
```

打开新生成的迁移文件，在`create_table`方法中添加下面一行:

*XXX _ 巫术 _ 核心. rb*

```
[...]
t.string :name
[...]
```

通过这种方式，我们可以确保用户也可以提供他们的姓名。现在可以应用迁移了:

```
$ rake db:migrate
```

在模型上。如果您打开 *models/user.rb* 文件，您会注意到

*型号/用户. rb*

```
[...]
authenticates_with_sorcery!
[...]
```

线出现在那里。它在模型中加入了一些巫术的方法。我们在这里要做的是添加一些验证，因为最初是不存在的。这意味着可以提供任何电子邮件和密码(包括空白)。

*型号/用户. rb*

```
[...]
validates :password, length: { minimum: 3 }
validates :password, confirmation: true
validates :email, uniqueness: true
[...]
```

我还要求密码的最小长度，要求确认，并确保电子邮件是唯一的。

邮件格式怎么样？您可能会编写自己的正则表达式，但是这个正则表达式似乎太长了。相反，抓住一个现有的[宝石](https://github.com/validates-email-format-of/validates_email_format_of)来解决这个问题:

*Gemfile*

```
[...]
gem 'validates_email_format_of'
[...]
```

别忘了跑步

```
$ bundle install
```

然后像这样修改模型:

*型号/用户. rb*

```
[...]
validates :email, uniqueness: true, email_format: { message: 'has invalid format' }
[...]
```

现在我们可以确定电子邮件的格式是正确的。现在是着手控制器的时候了。

### 签约雇用

需要两个控制器:一个处理用户注册(可能还有配置文件更新或删除)，另一个处理登录和注销。

让我们从注册开始。调用这个控制器`UsersController`:

*users_controller.rb*

```
class UsersController < ApplicationController
  def new
    @user = User.new
  end

  def create
    @user = User.new(user_params)
    if @user.save
      flash[:success] = 'Welcome!'
      redirect_to root_path
    else
      render 'new'
    end
  end

  private

  def user_params
    params.require(:user).permit(:email, :password, :password_confirmation, :name)
  end
end
```

观点:

*views/users/new.html.erb*

```
<h1>Registration</h1>

<%= form_for @user do |f| %>
  <%= render 'shared/errors', object: @user %>
  <div class="form-group">
    <%= f.label :name %>
    <%= f.text_field :name, class: 'form-control', required: true %>
  </div>

  <div class="form-group">
    <%= f.label :email %>
    <%= f.email_field :email, class: 'form-control', required: true %>
  </div>
  <div class="form-group">
    <%= f.label :password %>
    <%= f.password_field :password, class: 'form-control', required: true %>
  </div>
  <div class="form-group">
    <%= f.label :password_confirmation %>
    <%= f.password_field :password_confirmation, class: 'form-control', required: true %>
  </div>
  <%= f.submit 'Register', class: 'btn btn-primary btn-lg' %>
<% end %>
```

巫术使用 [bcrypt-ruby](https://github.com/codahale/bcrypt-ruby) 宝石来保护密码。这意味着密码从不以纯文本形式存储，只有它们的摘要存在于数据库中(在`crypted_password`字段中)。因此，`password`和`password_confirmation`是没有对应表字段的虚拟属性。

您可能还注意到了`salt`字段——它是一个随机字符串，用于进一步保护密码。你看，由[哈希函数](https://en.wikipedia.org/wiki/Cryptographic_hash_function)生成的摘要几乎不可能反转并恢复到原始消息。然而，可以做的是生成一个单词字典和相应的摘要。只要相同的消息总是具有相同的摘要，获得数据库访问权限的攻击者就可以通过字典搜索摘要并查找相应的字符串。盐是为了防止这种攻击。它在首次创建用户时生成，并在哈希过程中使用，如下所示:

```
hash(salt + password)
```

不可能为每种独特的盐建立一个字典，因此密码变得真正受保护。你可以在这里阅读更多。

顺便说一句，你可以通过覆盖*config/initializer/巫术. rb* 文件中的`user.encryption_algorithm =`选项来使用其他的[加密算法](https://github.com/NoamB/sorcery/blob/940d77643d0f87ef915d8383e4f85ee8ce3166c0/lib/sorcery/model/config.rb#L77)。

让我们添加路线:

*config/routes.rb*

```
[...]
resources :users, only: [:new, :create]
get '/sign_up', to: 'users#new', as: :sign_up
[...]
```

好了，现在用户可以注册了，但是默认不会登录。为此，可以使用至少接受两个参数的`login`方法:电子邮件和密码。将其添加到控制器:

*users_controller.rb*

```
[...]
def create
  @user = User.new(user_params)
  if @user.save
    login(params[:user][:email], params[:user][:password])
[...]
```

尝试注册…一切都应该工作正常。

## 登录和注销

下一步是实现登录和注销功能。创建新的
`SessionsController`:

*会话 _ 控制器. rb*

```
class SessionsController < ApplicationController
  def new
  end

  def create
    if login(params[:email], params[:password])
      flash[:success] = 'Welcome back!'
      redirect_to root_path
    else
      flash.now[:warning] = 'E-mail and/or password is incorrect.'
      render 'new'
    end
  end

  def destroy
    logout
    flash[:success] = 'See you!'
    redirect_to log_in_path
  end
end
```

在`create`动作中使用了相同的`login`方法。如果用户提供了错误的电子邮件或密码，该方法返回`nil`，因此会显示一条错误消息。`destroy`动作中的`logout`方法做了它所说的事情——注销用户。

现在的观点是:

*views/sessions/new . html . erb*

```
<h1>Log In</h1>
<%= form_tag sessions_path, method: :post do %>
  <div class="form-group">
    <%= label_tag :email %>
    <%= email_field_tag :email, nil, class: 'form-control', required: true %>
  </div>

  <div class="form-group">
    <%= label_tag :password %>
    <%= password_field_tag :password, nil, class: 'form-control', required: true %>
  </div>

  <%= submit_tag 'Log In', class: 'btn btn-primary btn-lg' %>
<% end %>
```

路线是:

*config/routes.rb*

```
[...]
resources :sessions, only: [:new, :create, :destroy]
get '/log_in', to: 'sessions#new', as: :log_in
delete '/log_out', to: 'sessions#destroy', as: :log_out
[...]
```

准备就绪后，就该调整布局以显示所有必要的链接了。

### 显示链接

我们希望向当前未通过身份验证的用户显示“注册”和“登录”链接。“注销”和“秘密网页”链接应该显示一旦他们登录。要检查用户是否经过身份验证，可以使用`current_user`方法，该方法返回一条用户记录或`nil`:

*视图/布局/应用程序. html.erb*

```
[...]
<div id="navbar">
  <ul class="nav navbar-nav">
    <% if current_user %>
      <li><%= link_to 'Secret Page', secret_path %></li>
    <% else %>
      <li><%= link_to 'Sign Up', sign_up_path %></li>
      <li><%= link_to 'Log In', log_in_path %></li>
    <% end %>
  </ul>
  <% if current_user %>
    <ul class="nav navbar-nav pull-right">
      <li><span><%= current_user.name %></span></li>
      <li><%= link_to 'Log Out', log_out_path, method: :delete %></li>
    </ul>
  <% end %>
</div>
[...]
```

这很简单，不是吗？然而，仍然可以通过直接输入 URL 来访问这个秘密页面。我们需要做的是在页面控制器中添加某种检查。

### 限制访问

幸运的是，巫术提供了一种`require_login`方法来限制未授权用户访问某些页面。应该是这样作为`before_action`使用的:

*application _ controller . Rb*

```
[...]
before_action :require_login
[...]
```

然而，这将导致限制对所有 T2 页面的访问。显然，没有登录的用户应该能够访问登录和注册页面。因此，使用`skip_before_action`:

*users_controller.rb*

```
[...]
skip_before_action :require_login, only: [:new, :create]
[...]
```

*会话 _ 控制器. rb*

```
[...]
skip_before_action :require_login, except: [:destroy]
[...]
```

当一个未经验证的用户试图打开一个受限页面时会发生什么情况呢？打开巫术的初始化文件:

*config/initializer/巫术. rb*

```
[...]
# What controller action to call for non-authenticated users. You can also
# override the 'not_authenticated' method of course.
# Default: `:not_authenticated`
#
# config.not_authenticated_action =
[...]
```

我们需要的是`not_authenticated`方法。创建它:

*application _ controller . Rb*

```
[...]
private

def not_authenticated
  flash[:warning] = 'You have to authenticate to access this page.'
  redirect_to log_in_path
end
[...]
```

还有一个小的改进可以添加。目前，当未经验证的用户打开受限页面时，他们将被重定向到登录页面。登录后，他们被带到网站的主页。这样不太方便。将用户重定向到他们试图查看的页面会更好。满足`redirect_back_or_to`法。此方法会将用户重定向回他们来自的地方或指定的页面:

*会话 _ 控制器. rb*

```
[...]
def create
  if login(params[:email], params[:password])
    flash[:success] = 'Welcome back!'
    redirect_back_or_to root_path
[...]
```

## 你还记得我吗？

您可能已经习惯了登录页面上标有“记住我”的小复选框。把它也添加到我们的应用程序怎么样？

我们可以利用巫术的子模块来完成这项任务。每个子模块都提供自己的功能，可以独立连接。把它们想象成乐高积木。

我们目前感兴趣的子模块叫做`RememberMe`。运行以下命令来复制所需的迁移:

```
$ rails g sorcery:install remember_me  --only-submodules
```

在一些文档中，你可能会发现有`--migrations`标志而不是`--only-submodules`标志，但是后者更受欢迎，因为`--migrations`已被弃用。该迁移将向`users`表中添加两列:

*   `remember_me_token` ( `string`)
*   `remember_me_token_expires_at` ( `datetime`)

这个令牌用于“记住”用户，显然，它不应该永远有效。应用迁移:

```
$ rake db:migrate
```

现在，注册新的子模块:

*config/initializer/巫术. rb*

```
[...]
Rails.application.config.sorcery.submodules = [:remember_me]
[...]
```

如果您希望调整令牌的持续时间，请在*巫术. rb* 文件中查找`user.remember_me_for`选项。默认值为一周。

将复选框添加到登录表单:

*views/sessions/new . html . erb*

```
[...]
<%= form_tag sessions_path, method: :post do %>
  [...]
  <div class="form-group">
    <%= label_tag :remember_me %>
    <%= check_box_tag :remember_me %>
  </div>
  [...]
<% end %>
```

最后，调整相应控制器的方法:

*会话 _ 控制器. rb*

```
[...]
def create
  if login(params[:email], params[:password], params[:remember_me])
    flash[:success] = 'Welcome back!'
    redirect_back_or_to root_path
  else
    flash.now[:warning] = 'E-mail and/or password is incorrect.'
    render 'new'
  end
end
[...]
```

`login`方法也可以接受第三个可选参数，指定用户是否应该被记住。

## 激活自己！

在许多网站上，用户必须在实际登录之前，通过访问电子邮件发送给他们的链接来激活他们的帐户。让我们实现同样的特性。安装新的子模块:

```
$ rails g sorcery:install user_activation  --only-submodules
```

并应用迁移:

```
$ rake db:migrate
```

这将添加以下字段:

*   `activation_state` ( `string`)
*   `activation_token` ( `string`)
*   `activation_token_expires_at` ( `datetime`)

注册子模块:

*config/initializer/巫术. rb*

```
[...]
Rails.application.config.sorcery.submodules = [:user_activation]
[...]
```

此外，调整设置:

*config/initializer/巫术. rb*

```
[...]
user.user_activation_mailer = UserMailer
[...]
```

还有几个设置值得一提:

*   `user.activation_mailer_disabled`–如果设置为`true`，带有激活链接的电子邮件将不会自动发送，您可以自行决定何时发送。默认值是`false`。
*   `prevent_non_active_users_to_login`–未激活用户是否可以登录。默认为`false`。

生成邮件程序以处理电子邮件的发送:

```
$ rails g mailer UserMailer activation_needed_email activation_success_email
```

删除*布局*和*用户邮件*目录下的所有 *.text.erb* 文件。现在修改邮件，如下所示:

*mailers/user_mailer.rb*

```
class UserMailer < ApplicationMailer
  def activation_needed_email(user)
    @user = user
    mail(to: user.email, subject: "Account activation")
  end

  def activation_success_email(user)
    @user = user
    mail(to: user.email, subject: "Your account is now activated")
  end
end
```

如你所见，巫术需要两种方法:

*   `activation_needed_email`发送带有激活链接的电子邮件
*   `activation_success_email`发送一封确认邮件，告知帐户已成功激活。

实际上，你可以通过将*中的`activation_success_email_method_name`设置为`nil`来禁止发送“成功”邮件。*

观点:

*views/user _ mailer/activation _ needed _ email . html . erb*

```
<p>Welcome, <%= @user.name %>!</p>

<p>To login to the site, just follow <%= link_to 'this link', activate_user_url(@user.activation_token) %>.</p>

<p>Thanks for joining and have a great day!</p>
```

*views/user _ mailer/activation _ success _ email . html . erb*

```
<p>Congratulations, <%= @user.name %>!</p>

<p>You have successfully activated your account.</p>

<p>You may now proceed to <%= link_to 'log in page', log_in_url %>.</p>

<p>Thanks for joining and have a great day!</p>
```

为了让链接助手工作，我们需要做一些配置:

*config/environments/development . Rb*

```
[...]
config.action_mailer.default_url_options = { host: '127.0.0.1:3000' }
[...]
```

请注意，电子邮件不会在开发中实际发送，您只能在控制台中看到它们的内容和元信息。在 *development.rb* 中设置`config.action_mailer.perform_deliveries`到`true`来改变这种行为。

对于生产，您必须配置 SMTP 设置。一些例子可以在[这里](http://guides.rubyonrails.org/action_mailer_basics.html#example-action-mailer-configuration)找到。在我的演示应用程序中，我将禁用用户激活。

要完成这一步，我们必须添加一个控制器方法和一个路由:

*config/routes.rb*

```
[...]
resources :users, only: [:new, :create] do
  member do
    get :activate
  end
end
[...]
```

*users_controller.rb*

```
[...]
skip_before_action :require_login

def activate
  if @user = User.load_from_activation_token(params[:id])
    @user.activate!
    flash[:success] = 'User was successfully activated.'
    redirect_to log_in_path
  else
    flash[:warning] = 'Cannot activate this user.'
    redirect_to root_path
  end
end
[...]
```

`load_from_activation_token`是由巫术提出的一种方法，通过激活令牌来寻找资源。`activate!`实际激活账户并将结果保存到数据库。

厉害！你现在可以去检查这一切是如何工作的。

### 与 DelayedJob 集成

您可能已经注意到，发送一封电子邮件需要一些时间，并且在此操作完成之前，页面不会加载。这不是一个好的用户体验，因为发送电子邮件可能需要很长时间。要解决这个问题，电子邮件发送过程应该是异步的。

CollectiveIdea 的 DelayedJob 可以用来实现这个目标。

扔进一颗新宝石:

*Gemfile*

```
[...]
gem 'delayed_job_active_record'
# or
gem 'delayed_job_mongoid'
[...]
```

然后跑

```
$ bundle install
```

生成并应用 DelayedJob 的迁移问题:

```
$ rails generate delayed_job:active_record
$ rake db:migrate
```

另外，在应用程序配置中添加下面一行来设置队列后端:

*配置/应用. rb*

```
[...]
config.active_job.queue_adapter = :delayed_job
[...]
```

只有 Rails 4.2+才应该这样做。

最后，将下面的代码**放在巫术初始化文件的末尾**:

*config/initializer/巫术. rb*

```
[...]
module Sorcery
  module Model
    module InstanceMethods
      def generic_send_email(method, mailer)
        config = sorcery_config
        mail = config.send(mailer).delay.send(config.send(method), self)
      end
    end
  end
end
```

电子邮件发送现在是异步执行的，耶！

要在本地机器上测试这一点，运行

```
$ rake jobs:work
```

以便 DelayedJob 开始处理作业，并通过发出

```
$ rails s
```

在单独的控制台选项卡中。接下来注册一个新用户，并导航到 DelayedJob 的控制台选项卡。您将看到类似这样的内容

```
[Worker(host:xxx pid:7128)] Job UserMailer.send (id=1) RUNNING
[Worker(host:xxx pid:7128)] Job UserMailer.send (id=1) COMPLETED after 2.2951
[Worker(host:xxx pid:7128)] 1 jobs processed at 0.4057 j/s, 0 failed
```

这意味着集成已成功完成。

## 抵御暴力

[暴力攻击](https://en.wikipedia.org/wiki/Brute-force_attack)可能是最广为人知的攻击类型。基本上，攻击者试图通过尝试一个又一个符号组合来“猜测”密码。为了安全起见，在一些不成功的登录尝试之后，帐户应该被锁定。

添加另一个魔法子模块:

```
$ rails g sorcery:install brute_force_protection  --only-submodules
```

这将生成一个迁移，该迁移将添加这些列:

*   `failed_logins_count`(`integer`)–用户连续多少次尝试登录失败
*   `lock_expires_at`(`default`)–账户何时解锁(如果已锁定)
*   `unlock_token`(`string`)–解锁账户的随机令牌

应用迁移:

```
$ rake db:migrate
```

注册新模块:

*config/initializer/巫术. rb*

```
[...]
Rails.application.config.sorcery.submodules = [:brute_force_protection]
[...]
```

就是这样。巫术会照顾其余的，但你可能想调整一些设置，如:

*   `user.consecutive_login_retries_amount_limit`–允许多少次不成功的尝试。默认值为 50。
*   `user.login_lock_time_period`–用户应该被锁定多长时间。默认值为 3600 秒。提供 0 无限期锁定用户。
*   `user.unlock_token_mailer`和`user.unlock_token_email_method_name`–使用解锁令牌向用户发送电子邮件的类和方法(默认情况下未设置)。

如果您想让用户解锁他们的帐户，除了创建一个邮件程序之外，您还需要一个单独的控制器(`ResetPasswordsController`)，方法与此类似:

```
[...]
def create
  u = User.load_from_unlock_token(params[:token])
  u.unlock!
  flash[:success] = 'Your account was unlocked!'
  redirect_to root_url
end
[...]
```

`load_from_unlock_token`是由巫术提供的一种方法，通过提供的解锁令牌来搜索用户。`unlock!`依次解除锁定。`!`在方法名的末尾表示用户将立即被保存，所以你不必调用`u.save`。

## 记录活动

活动日志是一个子模块，帮助实现诸如“谁当前在线”之类的功能。你知道该怎么做:

```
$ rails g sorcery:install activity_logging  --only-submodules
```

这将添加以下字段:

*   `last_login_at` ( `datetime`)
*   `last_logout_at` ( `datetime`)
*   `last_activity_at` ( `datetime`)
*   `last_login_from_ip_address` ( `string`)

应用迁移:

```
$ rake db:migrate
```

注册子模块:

*config/initializer/巫术. rb*

```
[...]
Rails.application.config.sorcery.submodules = [:activity_logging]
[...]
```

我们需要一个返回当前活动用户的方法。巫术曾经提供了`current_users`方法，但是它被决定[移除它](https://github.com/NoamB/sorcery/issues/602)。因此，让我们写我们自己的版本:

*application _ controller . Rb*

```
[...]
private

def current_users
  User.current_users
end

helper_method :current_users
[...]
```

*user.rb*

```
[...]
class << self
  def current_users
    where("#{sorcery_config.last_activity_at_attribute_name} IS NOT NULL") \
.where("#{sorcery_config.last_logout_at_attribute_name} IS NULL
  OR #{sorcery_config.last_activity_at_attribute_name} > #{sorcery_config.last_logout_at_attribute_name}") \
.where("#{sorcery_config.last_activity_at_attribute_name} > ? ", sorcery_config.activity_timeout.seconds.ago.utc.to_s(:db))
  end
end
[...]
```

巫术维基上还有一些其他的[例子](https://github.com/NoamB/sorcery/wiki/Fetching-currently-active-users)。

现在，只需使用`current_users`方法来显示用户名:

*视图/布局/应用程序. html.erb*

```
[...]
<div class="col-sm-9">
  <%= yield %>
</div>
<%= render 'shared/current_users' %>
[...]
```

*views/shared/_ current _ users . html . erb*

```
<div class="col-sm-3 well well-sm">
  <h3>Currently active users:</h3>
  <ul>
    <% current_users.each do |user| %>
      <li><%= user.name %></li>
    <% end %>
  </ul>
</div>
```

太好了！

如果您感兴趣的话，这个模块简单地设置了一些[回调](https://github.com/NoamB/sorcery/blob/master/lib/sorcery/controller/submodules/activity_logging.rb#L37)，它们在登录之后、注销之前以及每一个其他请求之后触发。这些回调更新相应的字段。例如，[这里是在每个用户请求后运行的代码](https://github.com/NoamB/sorcery/blob/0c3b0ef0f23ac1f18b878c51e53e9f82dc61557e/lib/sorcery/model/submodules/activity_logging.rb#L44)。

## 更新 2015/04/01:忘记密码

这次更新再次受到一位读者的启发，他让我展示如何用巫术实现密码重置功能。感谢你们所有人这么好的反馈！

用户往往会忘记他们的密码，因此提供重置密码的选项对于任何身份验证系统都是绝对必要的。有了魔法，我们可以很快做到。

一如既往地安装新的`ResetPassword`模块:

```
$ rails g sorcery:install reset_password --only-submodules
```

这将生成一个迁移，向`users`表添加以下字段:

*   `reset_password_token` ( `string`，索引)
*   `reset_password_token_expires_at`，(`datetime`)
*   `reset_password_email_sent_at`，(`datetime`)

`reset_password_token`是一个随机字符串，将在请求密码重置指令时生成。该令牌仅在有限的时间内有效，一旦用户更改其密码，该令牌就会失效。稍后将使用它来获取用户记录并更改其密码，因此令牌应该保持安全。

确保模块已注册:

*config/initializer/巫术. rb*

```
[...]
    Rails.application.config.sorcery.submodules = [:reset_password]
    [...]
```

现在提供“忘记密码了？”链接:

*views/sessions/new . html . erb*

```
<p><%= link_to 'Forgot your password?', new_reset_password_>path %></p>
```

设置一些路线:

*config/routes.rb*

```
[...]
    resources :reset_passwords, only: [:new, :create, :update, :edit]
    [...]
```

并创建一个新控制器:

*reset _ passwords _ controller . Rb*

```
class ResetPasswordsController < ApplicationController
      skip_before_filter :require_login

      def new
      end
    end
```

创建第一个视图，用户将在其中输入接收密码重置说明的电子邮件:

*views/reset _ passwords/new . html . erb*

```
<h1>Reset Password</h1>

    <%= form_tag reset_passwords_path, method: :post do %>
      <div class="form-group">
        <%= label_tag :email %>
        <%= text_field_tag :email, nil, class: 'form-control', required: true %>
      </div>

      <%= submit_tag "Send reset instructions", class: 'btn btn-primary btn-lg' %>
    <% end %>
```

现在的`create`方法:

*reset _ passwords _ controller . Rb*

```
[...]
  def create
    @user = User.find_by_email(params[:email])
    @user.deliver_reset_password_instructions! if @user
    flash[:success] = 'Instructions have been sent to your email.'
    redirect_to log_in_path
  end
    [...]
```

`deliver_reset_password_instructions!`是由巫术提供的方法，该方法实际上将依赖您自己的邮件程序来发送密码重置指令。我们已经有了`UserMailer`,所以让我们在这里添加一个新方法:

*mailers/user_mailer.rb*

```
[...]
  def reset_password_email(user)
    @user = user
    @url  = edit_reset_password_url(@user.reset_password_token)
    mail(to: user.email,
         subject: "Your password has been reset")
  end
    [...]
```

添加相应的视图:

*views/user _ mailer/reset _ password _ email . html . erb*

```
<p>Hello, <%= @user.name %>!</p>

    <p>Someone (hopefully, you) have requested to reset your password.</p>

    <p>To choose a new password, follow this link: <%= link_to @url, @url %>.</p>
```

我们还必须在巫术的配置文件中提供邮件程序的名称:

*config/initializer/巫术. rb*

```
[...]
    user.reset_password_mailer = UserMailer
    [...]
```

如果您不喜欢默认的`reset_password_email`方法名，您可以覆盖`user.reset_password_email_method_name`。

现在添加当用户点击电子邮件中提供的链接时将被调用的`edit`动作:

*reset _ passwords _ controller . Rb*

```
[...]
  def edit
    @token = params[:id]
    @user = User.load_from_reset_password_token(@token)

    not_authenticated if @user.blank?
  end
    [...]
```

和观点:

*views/reset _ passwords/edit . html . erb*

```
<h1>Choose a new password</h1>

    <%= form_for @user, url: reset_password_path(@token), method: :patch do |f| %>
      <%= render 'shared/errors', object: @user %>

      <div class="form-group">
        <%= f.label :password %>
        <%= f.password_field :password, class: 'form-control', required: true %>
      </div>

      <div class="form-group">
        <%= f.label :password_confirmation %>
        <%= f.password_field :password_confirmation, class: 'form-control', required: true %>
      </div>

        <%= f.submit 'Set password', class: 'btn btn-primary btn-lg' %>
    <% end %>
```

最后，添加`update`动作来处理密码更改:

*reset _ passwords _ controller . Rb*

```
[...]
  def update
    @token = params[:id]
    @user = User.load_from_reset_password_token(@token)

    not_authenticated && return if @user.blank?

    @user.password_confirmation = params[:user][:password_confirmation]
    if @user.change_password!(params[:user][:password])
      flash[:success] = 'Password was successfully updated.'
      redirect_to log_in_path
    else
      render "edit"
    end
  end
    [...]
```

你完了！如果你有困难，请随意修改这段代码并提出你的问题。

## 结论

我们已经看了一下巫术的基本设置和它的子模块。还有更多可用的，所以看看[项目 wiki](https://github.com/NoamB/sorcery/wiki) 来了解更多。

你曾经尝试过在你的项目中使用巫术吗？你觉得方便吗？分享你的经历，不要犹豫，在评论中提出你的问题。

## 分享这篇文章