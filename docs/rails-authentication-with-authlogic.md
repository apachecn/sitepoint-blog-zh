# 使用 Authlogic 的 Rails 身份验证

> 原文：<https://www.sitepoint.com/rails-authentication-with-authlogic/>

![](img/88a0498a0bdc1d76d5222116575a1946.png)

今天，我想向大家介绍一下本·约翰逊为 Ruby 开发的一个简单的认证解决方案。

AuthLogic 并不引人注目，而且相当低级。它不依赖于生成的代码(例如，像[设计](https://www.sitepoint.com/devise-authentication-in-depth/))——相反，它只提供工具，你可以用它来以你喜欢的方式编写你的应用程序。某种意义上，AuthLogic 类似于[巫术](https://www.sitepoint.com/magical-authentication-sorcery/)。

让我们构建一个 Rails 应用程序，允许用户注册、登录和退出，以及重置他们的密码。在此过程中，我们将讨论 AuthLogic 的各种特性和设置。阅读完本教程后，您将可以在您的实际项目中使用 AuthLogic。

源代码可以在 [GitHub](https://github.com/bodrovis/Sitepoint-source/tree/master/Authentication_with_AuthLogic) 上找到。

演示应用可在[sitepoint-authlogic.herokuapp.com](https://sitepoint-authlogic.herokuapp.com/)获得。

## 入门指南

我将向您展示的演示应用程序是用 Rails 4.1 构建的，但 AuthLogic 支持 Rails 3，甚至 Rails 2(它有一个单独的分支)。

创建一个没有默认测试套件的新“逻辑”应用程序:

```
$ rails new Logical -T
```

投入以下宝石:

*Gemfile*

```
[...]
gem 'bootstrap-sass'
gem 'authlogic', '3.4.6'
[...]
```

然后跑

```
$ bundle install
```

如果您愿意，可以利用 Bootstrap 的风格:

*样式表/应用程序. scss*

```
@import 'bootstrap-sprockets';
@import 'bootstrap';
```

并修改布局:

*视图/布局/应用程序. html.erb*

```
[...]
<nav class="navbar navbar-inverse">
  <div class="container">
    <div class="navbar-header">
      <%= link_to 'Logical', root_path, class: 'navbar-brand' %>
    </div>
    <div id="navbar">
      <ul class="nav navbar-nav">
        <li><%= link_to 'Home', root_path %></li>
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

现在为静态页面创建控制器:

*pages_controller.rb*

```
class PagesController < ApplicationController
  def index
  end
end
```

然后相应的查看:

*views/pages/index . html . erb*

```
<div class="jumbotron">
<div class="container">
  <h1>Welcome!</h1>
  <p>Sign up to get started.</p>
  <p>
    <%= link_to '#', class: 'btn btn-primary btn-lg' do %>
      Sign Up &raquo;
    <% end %>
  </p>
</div>
```

和路线:

*config/routes.rb*

```
[...]
root to: 'pages#index'
[...]
```

完成后，进行下一步，让我们一起集成 AuthLogic！

## 设置 AuthLogic

### 模型

使用 AuthLogic 涉及两个模型:一个基本模型，我们称之为`User`和一个“特殊”模型，从`Authlogic::Session::Base`继承而来，称为`UserSession`(你可以阅读[这一节](https://github.com/binarylogic/authlogic#tell-me-quickly-how-authlogic-works)来快速了解这个解决方案是如何工作的)。

首先，创建一个新迁移:

```
$ rails g model User email:string crypted_password:string password_salt:string persistence_token:string
```

([这里的](https://github.com/binarylogic/authlogic#example-migration)是具有所有可能字段的示例迁移)

将以下行添加到生成的迁移中:

*migrations/XXX _ create _ users . Rb*

```
[...]
add_index :users, :email, unique: true
[...]
```

应用迁移:

```
$ rake db:migrate
```

`email`、`crypted_password`和`password_salt`字段实际上是可选的。AuthLogic 并不真正关心如何进行身份验证，例如使用 LDAP 或 OAuth 2。这里是受支持的身份验证方法的 AuthLogic“附加组件”的[列表，但是其中许多都没有得到积极维护。](https://github.com/binarylogic/authlogic#authlogic-add-ons)

顺便说一下，您还可以有一个 AuthLogic 将使用的`login`字段来代替`email`。

`persistence_token`字段是必需的。AuthLogic 使用它以安全的方式保持用户的会话。

现在调整模型:

*型号/用户. rb*

```
[...]
acts_as_authentic
[...]
```

这为`User`模型配备了 AuthLogic 功能。`acts_as_authentic`接受一个块来重新定义默认设置(比如密码验证规则——我们将在文章的最后一节讨论)。

现在创建一个新的特殊模型:

*models/user_session.rb*

```
class UserSession < Authlogic::Session::Base
end
```

#### Windows 用户注意事项

在 Ruby 2.1 或 2.2 中使用 AuthLogic 时，Windows 用户可能会遇到一个非常严重的错误(我没有用 Ruby 1.9 测试过)。完整的讨论可以在 [GitHub](https://github.com/binarylogic/authlogic/issues/461) 上找到，但是，简而言之，这个错误与 [SCrypt](https://github.com/pbhogan/scrypt) 有关，这是一个实现安全密码哈希算法的 gem。AuthLogic 使用 SCrypt 作为默认的加密提供程序，但是在 Windows 上，它不断返回分段错误，服务器崩溃。

最快的方法是使用另一个提供商——AuthLogic 提供了为数不多的几个提供商。

在本演示中，我将坚持使用 SHA512，因此调整模型:

*型号/用户. rb*

```
[...]
acts_as_authentic do |c|
    c.crypto_provider = Authlogic::CryptoProviders::Sha512
end
[...]
```

### 控制器和助手

让我们用控制器来管理用户:

*users_controller.rb*

```
class UsersController < ApplicationController
  def new
    @user = User.new
  end

  def create
    @user = User.new(users_params)
    if @user.save
      flash[:success] = "Account registered!"
      redirect_to root_path
    else
      render :new
    end
  end

  private

  def users_params
    params.require(:user).permit(:email, :password, :password_confirmation)
  end
end
```

这是一个非常基本的控制器，负责用户注册。确保允许使用
`password`和`password_confirmation`属性，因为默认情况下，AuthLogic 会检查这两个属性是否匹配。

这是管理登录和注销的控制器:

*user _ sessions _ controller . Rb*

```
class UserSessionsController < ApplicationController
  def new
    @user_session = UserSession.new
  end

  def create
    @user_session = UserSession.new(user_session_params)
    if @user_session.save
      flash[:success] = "Welcome back!"
      redirect_to root_path
    else
      render :new
    end
  end

  def destroy
    current_user_session.destroy
    flash[:success] = "Goodbye!"
    redirect_to root_path
  end

  private

  def user_session_params
    params.require(:user_session).permit(:email, :password, :remember_me)
  end
end
```

如您所见，我们使用`UserSession`模型来认证用户。它自动保存用户的会话，因此控制器非常干净简单。

那`current_user_session`呢？它是一个辅助方法:

*application _ controller . Rb*

```
[...]
private

def current_user_session
  return @current_user_session if defined?(@current_user_session)
  @current_user_session = UserSession.find
end

def current_user
  return @current_user if defined?(@current_user)
  @current_user = current_user_session && current_user_session.user
end

helper_method :current_user_session, :current_user
[...]
```

`UserSession.find`自动使用`persistence_token`查找当前会话。

我还添加了`current_user`来根据当前会话轻松获取当前用户的记录。

### 路线

我们必须建立一些路线:

*config/routes.rb*

```
[...]
resources :users, only: [:new, :create]

resources :user_sessions, only: [:create, :destroy]

delete '/sign_out', to: 'user_sessions#destroy', as: :sign_out
get '/sign_in', to: 'user_sessions#new', as: :sign_in
[...]
```

### 视图

最后，让我们处理一下视图。

首先，布局:

*视图/布局/应用程序. html.erb*

```
[...]
<nav class="navbar navbar-inverse">
  <div class="container">
    <div class="navbar-header">
      <%= link_to 'Logical', root_path, class: 'navbar-brand' %>
    </div>
    <div id="navbar">
      <ul class="nav navbar-nav">
        <li><%= link_to 'Home', root_path %></li>
      </ul>
      <ul class="nav navbar-nav pull-right">
        <% if current_user %>
          <li><span><%= current_user.email %></span></li>
          <li><%= link_to 'Sign Out', sign_out_path, method: :delete %></li>
        <% else %>
          <li><%= link_to 'Sign In', sign_in_path %></li>
        <% end %>
      </ul>
    </div>
  </div>
</nav>
[...]
```

在这里，我只是填充顶部菜单。

现在进入“注册”页面:

*views/users/new.html.erb*

```
<div class="page-header"><h1>Register</h1></div>

<%= form_for @user do |f| %>
  <%= render 'shared/errors', object: @user %>

  <div class="form-group">
    <%= f.label :email %>
    <%= f.email_field :email, class: 'form-control' %>
  </div>

  <div class="form-group">
    <%= f.label :password %>
    <%= f.password_field :password, class: 'form-control' %>
  </div>

  <div class="form-group">
    <%= f.label :password_confirmation %>
    <%= f.password_field :password_confirmation, class: 'form-control' %>
  </div>

  <%= f.submit 'Register', class: 'btn btn-primary btn-lg' %>
<% end %>
```

AuthLogic 会自动验证电子邮件是否有效，密码是否匹配，并且长度至少为 4 个字符。您可以在 *model/user.rb* 中轻松地重新定义这些设置——我们稍后将对此进行讨论。

添加共享部分以显示错误:

*views/shared/_ errors . html . erb*

```
<% if object.errors.any? %>
  <div class="panel panel-warning errors">
    <div class="panel-heading">
      <h5><i class="glyphicon glyphicon-exclamation-sign"></i> Found errors while saving</h5>
    </div>

    <ul class="panel-body">
      <% object.errors.full_messages.each do |msg| %>
        <li><%= msg %></li>
      <% end %>
    </ul>
  </div>
<% end %>
```

不要忘记在 *index.html.erb* 中添加一个指向“注册”页面的链接:

*views/pages/index . html . erb*

```
<div class="jumbotron">
  <div class="container">
    <h1>Welcome!</h1>
    <p>Sign up to get started.</p>
    <p>
      <%= link_to new_user_path, class: 'btn btn-primary btn-lg' do %>
        Sign Up &raquo;
      <% end %>
    </p>
  </div>
</div>
```

最后，是“登录”视图的代码:

*views/user _ sessions/new . html . erb*

```
<div class="page-header"><h1>Sign In</h1></div>

<%= form_for @user_session do |f| %>
  <%= render 'shared/errors', object: @user_session %>

  <div class="form-group">
    <%= f.label :email %>
    <%= f.email_field :email, class: 'form-control' %>
  </div>

  <div class="form-group">
    <%= f.label :password %>
    <%= f.password_field :password, class: 'form-control' %>
  </div>

  <div class="form-group">
    <%= f.label :remember_me %>
    <%= f.check_box :remember_me %>
  </div>

  <%= f.submit "Log in!", class: 'btn btn-primary btn-lg' %>
<% end %>
```

现在，您可以开始了——启动服务器并注册您的第一个用户！

## 存储附加信息

AuthLogic 支持一些自动填充的“神奇属性”。您可以使用它们来存储有关用户的附加信息，如上次登录日期或上次使用的 IP。

继续创建新的迁移:

```
$ rails g migration add_magic_columns_to_users
```

像这样调整它:

*XXX _ add _ magic _ columns _ to _ users . Rb*

```
class AddMagicColumnsToUsers < ActiveRecord::Migration
  def change
    add_column :users, :login_count, :integer, :null => false, :default => 0
    add_column :users, :failed_login_count, :integer, :null => false, :default => 0
    add_column :users, :last_request_at, :datetime
    add_column :users, :current_login_at, :datetime
    add_column :users, :last_login_at, :datetime
    add_column :users, :current_login_ip, :string
    add_column :users, :last_login_ip, :string
  end
end
```

并运行迁移:

```
$ rake db:migrate
```

现在，为了简单起见，让我们在用户登录后在主页上显示所有这些信息:

*views/pages/index . html . erb*

```
<% if current_user %>
  <div class="page-header"><h1>Welcome back!</h1></div>

  <h2>Some info about you...</h2>

  <div class="well well-lg">
    <ul>
      <li>Login count: <%= current_user.login_count %></li>
      <li>Failed login count: <%= current_user.failed_login_count %></li>
      <li>Last request: <%= current_user.last_request_at %></li>
      <li>Current login at: <%= current_user.current_login_at %></li>
      <li>Last login at: <%= current_user.last_login_at %></li>
      <li>Current login IP: <%= current_user.current_login_ip %></li>
      <li>Last login IP: <%= current_user.last_login_ip %></li>
    </ul>
  </div>
<% else %>
    [...]
<% end %>
```

登录并观察结果。例如，如果您希望检查用户访问网站的频率，这可能会很方便。

## 重置密码

用户容易忘记他们的密码，因此向他们提供重置密码的方法是至关重要的。AuthLogic 也为您提供了一个工具来添加这个功能。

AuthLogic 的作者建议使用一个简单的机制，用户首先输入一个电子邮件，然后接收一个更新密码的链接，然后按照该链接实际设置新密码。该链接包含一个必须重置的特殊“易腐”令牌。

因此，我们需要在`users`表中添加一个名为`perishable_token`的新字段。请注意，我们不把它叫做类似`reset_password_token`的东西。AuthLogic 没有规定此令牌只能用于密码重置，例如，您可以使用它来激活用户的帐户。

除了`perishable_token`，AuthLogic 还支持对于 API 来说非常理想的`single_access_token`——它提供访问，但不持久。在这里阅读更多。

好了，现在创建并应用一个新的迁移:

```
$ rails g migration add_perishable_token_to_users perishable_token:string
$ rake db:migrate
```

显然，我们需要一个特殊的控制器来管理密码重置和邮件程序。

从控制器开始:

*密码 _ 重置 _ 控制器. rb*

```
class PasswordResetsController < ApplicationController
  def new
  end

  def create
    @user = User.find_by_email(params[:email])
    if @user
      @user.deliver_password_reset_instructions!
      flash[:success] = "Instructions to reset your password have been emailed to you."
      redirect_to root_path
    else
      flash[:warning] = "No user was found with that email address"
      render :new
    end
  end

  def edit
    @user = User.find_by(perishable_token: params[:id])
  end

  def update
    @user = User.find_by(perishable_token: params[:id])
    if @user.update_attributes(password_reset_params)
      flash[:success] = "Password successfully updated!"
      redirect_to root_path
    else
      render :edit
    end
  end

  private

  def password_reset_params
    params.require(:user).permit(:password, :password_confirmation)
  end
end
```

`new`是当用户点击“忘记密码了？”时将调用的操作链接。

处理发送的数据，并尝试通过电子邮件获取用户。如果找到该用户，密码重置说明将发送到他们的电子邮件中。

当用户访问发送到所提供的电子邮件的链接时，调用`edit`。这个链接包含我们用来查找用户记录的易腐令牌。编辑页面包含另一个输入新密码的表单。

在`update`动作中，获取用户并更新他们的密码。

我们还需要路线:

*config/routes.rb*

```
...
resources :password_resets, only: [:new, :create, :edit, :update]
...
```

现在，向您的模型添加一个新方法:

*型号/用户. rb*

```
[...]
def deliver_password_reset_instructions!
  reset_perishable_token!
  PasswordResetMailer.reset_email(self).deliver_now
end
[...]
```

`reset_perishable_token!`是 AuthLogic 提供的一种方法——它只是简单地通过[将](https://github.com/binarylogic/authlogic/blob/1040e1026053d8347b4bbd5dce6e9ee8be3229b9/lib/authlogic/acts_as_authentic/perishable_token.rb#L92) `perishable_token`设置为一个新的随机值并保存记录。

我们还必须创建新的邮件:

*mailers/application _ mailer . Rb*

```
class ApplicationMailer < ActionMailer::Base
  default from: "from@example.com"
  layout 'mailer'
end
```

*mailers/password _ reset _ mailer . Rb*

```
class PasswordResetMailer < ApplicationMailer
  def reset_email(user)
    @user = user
    mail(to: @user.email, subject: 'Password reset instructions')
  end
end
```

*视图/布局/mailer.text.erb*

```
<%= yield %>
```

*views/password _ reset _ mailer/reset _ email . text . erb*

```
Here is your link to reset password: <%= edit_password_reset_url(@user.perishable_token) %>.
```

另外，不要忘记设置默认的 URL 选项:

*config/environments/development . Rb*

```
config.action_mailer.default_url_options = { host: '127.0.0.1:3000' }
```

请注意，在开发环境中，实际上不会发送电子邮件，但是您可以在控制台中看到它们的内容。还要注意，我的演示应用程序不会发送电子邮件，但它很容易在生产环境中设置。点击阅读更多[。](http://guides.rubyonrails.org/action_mailer_basics.html#example-action-mailer-configuration)

最后，更新视图以包含“忘记密码了？”链接:

*views/user _ sessions/new . html . erb*

```
<div class="page-header"><h1>Sign In</h1></div>

<%= form_for @user_session do |f| %>
  [...]
  <%= f.submit "Log in!", class: 'btn btn-primary btn-lg' %>
  <br/><br/>
  <%= link_to 'Forgot your password?', new_password_reset_path, class: 'btn btn-sm btn-default' %>
<% end %>
```

现在继续检查这是如何工作的！

## 调整默认设置

正如我已经告诉您的，AuthLogic 提供了一些默认设置，您可以通过将一个块传递给`acts_as_authentic`来轻松地更改这些设置。要了解关于这些设置的更多信息，您可以浏览[文档](http://www.rubydoc.info/github/binarylogic/authlogic/index)，但是让我突出显示其中的一些。

### 蛮力

默认情况下，AuthLogic 试图防止暴力攻击(当有人使用程序通过使用各种符号组合来“猜测”密码时)，但前提是您的表中存在`failed_login_count`字段。您可以更改两种设置:

*   `consecutive_failed_logins_limit`(默认值为 50)-允许的连续失败登录次数。设置`0`禁用暴力保护。
*   `failed_login_ban_for`(默认为 2 小时)-用户的账户将被锁定多长时间。设置为`0`永久锁定。

更多可以在这里阅读[。](http://www.rubydoc.info/github/binarylogic/authlogic/Authlogic/Session/BruteForceProtection/Config)

### HTTP 基本身份验证

AuthLogic 支持 [HTTP 基本认证](http://www.rubydoc.info/github/binarylogic/authlogic/Authlogic/Session/HttpAuth)，默认开启。

### 密码配置

有[各种设置](http://www.rubydoc.info/github/binarylogic/authlogic/Authlogic/Session/Password/Config)可用于更改存储登录和密码信息的默认字段，以及更改查找用户会话的方法。

还有一个[页面](http://www.rubydoc.info/github/binarylogic/authlogic/Authlogic/ActsAsAuthentic/Password/Config)，列出了与密码验证规则和密码确认相关的设置。

### 超时注销

您还可以指示 AuthLogic 在一段时间后将用户标记为已注销。只需将`logout_on_timeout`设置为`true`并使用`stale?`来检查用户是否需要重新登录。

点击阅读更多[。](http://www.rubydoc.info/github/binarylogic/authlogic/Authlogic/Session/Timeout/Config)

您还可以使用`logged_in_timeout`设置来确定用户是否登录。更多可以在[这里找到](http://www.rubydoc.info/github/binarylogic/authlogic/Authlogic/ActsAsAuthentic/LoggedInStatus/Config)。

### 电子邮件配置

访问[本页](http://www.rubydoc.info/github/binarylogic/authlogic/Authlogic/ActsAsAuthentic/Email/Config)了解与电子邮件相关的各种选项(存储电子邮件的字段、验证规则等)。

您可以使用更多选项进行 AuthLogic 定制，因此请务必浏览文档。

## 结论

在本文中，我们研究了 AuthLogic 并构建了一个简单的演示应用程序。观察各种身份验证解决方案并比较它们的原理确实很有趣，不是吗？

您更喜欢哪种身份认证解决方案，为什么？您是否遇到过要求您开发自己的身份验证系统的任何限制或特殊情况？分享一下你的经验！

一如既往，欢迎反馈。谢谢你和我呆在一起，再见。

## 分享这篇文章