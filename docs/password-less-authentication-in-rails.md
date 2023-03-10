# Rails 中的无密码认证

> 原文：<https://www.sitepoint.com/password-less-authentication-in-rails/>

![](img/e52a5f110740822f376d2707cec7e0d5.png)

身份验证是许多 web 应用程序的关键元素之一。它是应用程序和其用户之间的石墙，因此身份验证方法的安全性和易用性非常重要。但是，这种“认证”是什么？这是一种确保只有我们的应用程序授权的用户才被允许使用该应用程序的方法。我相信您知道，有许多方法可以对用户进行身份验证，例如电子邮件/密码、OpenID Connect、SAML、SSO 等等。

今天，我们来看看另一种方法:无密码认证。

## 什么是无密码认证？

当用户注册网站时，应用程序允许用户选择他们的凭据，通常是用户名或电子邮件/密码。然后，用户可以随时输入这些凭据来登录应用程序。无密码认证基本上消除了密码部分，只使用电子邮件来注册和登录。

这是如何工作的，当用户注册到我们的应用程序，一封电子邮件被发送来激活他们的帐户。这允许我们验证该电子邮件是否属于该用户。现在我们已经有了一封经过验证的电子邮件，下次用户尝试登录时，我们将向他们发送一封电子邮件，其中包含用户用于登录应用程序的令牌。一旦用户点击带有令牌的链接，应用程序将对用户进行身份验证。

让我们开始吧。初始化 rails 应用程序:

```
$ rails new passwordless
$ cd passwordless 
```

> 为了更清楚地了解到底发生了什么，本教程中我们不会使用任何库或 gem。

## 创建模型

让我们从创建应用程序所需的模型开始。我们将把这个模型称为`User`,因为我们要验证的是用户。但是你可以随意使用任何有用的东西。

```
$ rails g scaffold user fullname username:uniq email:uniq login_token token_generated_at:datetime
$ rails db:create && rails db:migrate 
```

`fullname`和`username`是可选的。我们增加了`username`，让用户可以通过`email`或`username`登录。我们还有其他几列，`login_token`和`token_generated_at`，它们基本上是我们为用户生成的一次性密码，以及它是何时生成的。

在表级别有一个独特的约束，但是让我们也为模型添加 ActiveRecord 验证。将以下内容添加到 **app/models/user.rb** :

```
validates :email, :username, uniqueness: true, presence: true 
```

除此之外，让我们在保存记录之前添加一个 before 过滤器来格式化`username`和`email`。这些验证也应该在客户端进行，但是这是一个附加的措施。添加之前过滤器:

```
...
before_save :format_email_username

def format_email_username
  self.email = self.email.delete(' ').downcase
  self.username = self.username.delete(' ').downcase
end 
```

在这里，我们基本上去掉了`username`和`email`中的空格，并在保存到数据库之前将其变成小写。

因为我们将允许用户通过用户名和电子邮件登录，所以让我们添加一个助手方法，该方法根据以下任一项获取用户记录:

```
...

def self.find_user_by(value)
  where(["username = :value OR email = :value", {value: value}]).first
end 
```

## 登记

有了模型，让我们继续创建我们的控制器文件和必要的路线。在我们开始创建注册控制器之前，快速创建一个静态页面来显示消息:

```
$ rails g controller static home 
```

将以下路线添加到 **config/routes.rb** :

```
root 'static#home' 
```

除此之外，还要将下面一行添加到您的**app/views/layouts/application . html . erb**中，用于向用户显示消息:

```
...
<body>
  <p id="notice"><%= notice %></p>
... 
```

生成用户控制器:

```
$ rails g controller users 
```

在 **config/routes.rb** 中，添加以下我们将用于用户注册的路由:

```
resources :users, only: [:create]
get '/register', to: 'users#register' 
```

现在，让我们在**app/controllers/users _ controller . Rb**中添加与上面声明的路线相对应的代码:

```
def register
  @user = User.new
end

def create
  @user = User.new(user_params)

  if @user.save
    redirect_to root_path, notice: 'Welcome! We have sent you the link to login to our app'
  else
    render :register
  end
end

private

def user_params
  params.require(:user).permit(:fullname, :username, :email)
end 
```

现在，在**app/views/users/register . html . erb**下创建用于注册的视图文件，并将该表单添加到其中:

```
<h1>Register</h1>

<%= form_for(@user) do |f| %>
  <% if @user.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(@user.errors.count, "error") %> prohibited this @user from being saved:</h2>

      <ul>
      <% @user.errors.full_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
      </ul>
    </div>
  <% end %>

  <div class="field">
    <%= f.label :fullname %>
    <%= f.text_field :fullname %>
  </div>

  <div class="field">
    <%= f.label :username %>
    <%= f.text_field :username %>
  </div>

  <div class="field">
    <%= f.label :email %>
    <%= f.text_field :email %>
  </div>

  <div class="actions">
    <%= f.submit 'Register' %>
  </div>
<% end %> 
```

请注意本表格的特别之处。这是一个标准的 Rails、scaffole 生成的表单，它为用户捕获`fullname`、`username`和`email`，并将其发送到我们的`create`端点。启动 Rails 服务器，前往`/register`,看看注册已经开始了！

## 登录链接

让我们进入应用程序的实质性部分:发送登录电子邮件。基本上，当一个新用户注册或者每当他们请求登录时，我们必须给他们发送一个带有令牌的登录链接。当链接被点击，应用程序将登录用户。首先将以下助手方法添加到我们的 **apps/models/user.rb** 中，用于发送电子邮件:

```
...

  def send_login_link
    generate_login_token

    template = 'login_link'
    UserMailer.send(template).deliver_now
  end

  def generate_login_token
    self.login_token = generate_token
    self.token_generated_at = Time.now.utc
    save!
  end

  def login_link
    "http://localhost:3000/auth?token=#{self.login_token}"
  end

  def login_token_expired?
    Time.now.utc > (self.token_generated_at + token_validity)
  end

  def expire_token!
    self.login_token = nil
    save!
  end

  private

  def generate_token
    SecureRandom.hex(10)
  end

  def token_validity
    2.hours
  end
end 
```

> 请不要纠结于发送邮件的代码在哪里。我正试图尽可能地降低噪音，专注于更高层次的概念。例如，我从来不会在模型中放一个`UserMailer`，但这只是为了演示的目的。

因此，我们有一个`send_login_link`方法，我们将很快使用它来为用户发送登录链接。在将其存储到我们的数据库之前，我们实际上使用 BCrypt 对其进行了哈希处理，这使得它在数据泄露的情况下更加安全。除此之外，还要将宝石“bcrypt”添加到你的 gem 文件中。

生成登录令牌后，使用 ActionMailer `UserMailer`通过电子邮件将其发送给用户。设置邮件功能在本教程中被跳过，因为有许多好的教程解释如何根据你的电子邮件提供商来做。请确保在您发送给用户的电子邮件模板中包含我们在“send_login_link”方法中传递给用户邮件程序的 link 参数。

`login_link`配置有一个`localhost` url，但是根据您的应用程序相应地更改它。此外，`token_validity`的持续时间被设置为 2 小时，但显然你可以自由更改。最后，将这一行添加到**app/controllers/users _ controller . Rb**`create`动作中，紧跟在`@user.save`行之后:

```
...
if @user.save
  @user.send_login_link
... 
```

现在我们已经有了必要的助手方法，让我们添加接收路由来处理我们在电子邮件中发送的登录链接。

### 会话控制器

首先为会话生成控制器。

```
$ rails g controller session auth 
```

在 **config/routes.rb** 文件中更新，将`get 'session/auth'`改为`get '/auth/:user_id/:token', to: 'session#auth'`。在生成的 **session_controller.rb** 文件中，添加以下代码:

```
def auth
  token = params[:token].to_s
  user_id = params[:user_id] 
  user = User.find_by(id: user_id)

  if !user || !user.valid_token?
    redirect_to root_path, notice: 'It seems your link is invalid. Try requesting for a new login link'
  elsif user.login_token_expired?
    redirect_to root_path, notice: 'Your login link has been expired. Try requesting for a new login link.'
  else
    sign_in_user(user)
    redirect_to root_path, notice: 'You have been signed in!'
  end
end 
```

使用 helper 方法，检查令牌是否有效或者是否过期。如果无效，用适当的消息重定向到主页。有一个我们用过的助手方法，`sign_in_user`，我们必须创建它。打开**app/controllers/application _ controller . Rb**，添加:

```
def sign_in_user(user)
  user.expire_token!
  session[:email] = user.email
end

def current_user
  User.find_by(email: session[:email])
end 
```

我们基本上是让用户的令牌过期，并将用户的电子邮件存储到会话中。此外，我们还添加了一个 helper 方法来从会话中检索用户。无密码功能已经准备好了，继续尝试注册一个新用户来测试登录功能。

## 注册

最后一步，我们将利用我们的助手方法进行用户登录。首先将这些路由添加到 **config/routes.rb** 文件中:

```
resources :session, only: [:new, :create] 
```

并将以下代码添加到**/app/controllers/session _ controller . Rb**文件中:

```
def new
end

def create
  value = params[:value].to_s
  user = User.find_user_by(value)

  if !user
    redirect_to new_session_path, notice: "Uh oh! We couldn't find the username / email. Please try again."
  else
    user.send_login_link
    redirect_to root_path, notice: 'We have sent you the link to login to our app'
  end
end 
```

我们刚刚使用了`send_login_link`来搬运重物。应用程序的最后一部分是登录表单。创建文件**app/views/session/new . html . erb**，并添加以下表单:

```
<%= form_tag "/session" do %>
  <label> Email / Username </label>
  <%= text_field_tag "value" %>
  <%= submit_tag "Login" %>
<% end %> 
```

这只是一个为我们完成工作的简单表单。

## 结论

至此，本教程到此结束。无密码登录现在真的越来越流行了，它为我们的用户提供了一种更少干扰、更方便的身份验证方式。哦，它还减少了每个相关人员的开销。我鼓励您为您的应用程序尝试无密码登录方法，至少作为登录的补充方法。这将是一个开始！

本教程中使用的所有示例代码都可以在 [Github](https://github.com/avinoth/passwordless-app-sample) 上获得，请随意使用。感谢您阅读本教程，我希望它能达到您的目的。

## 分享这篇文章