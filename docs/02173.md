# 从头开始用 JWT 认证你的 Rails API

> 原文：<https://www.sitepoint.com/authenticate-your-rails-api-with-jwt-from-scratch/>

![](img/ccfbfa7d33f279e6d7aa6ab01747cf17.png)

身份验证是任何 web 应用程序的重要组成部分之一。有无数的库和框架提供了各种选项来以某种方式执行身份验证。这些库消除了设置身份验证系统所需的大量基础工作，为幕后发生的事情提供了“魔力”。对于 Rails，我们有许多认证系统，其中最突出的是[设计](https://github.com/plataformatec/devise/)。

Devise 是一个身份验证引擎，它作为我们的应用程序的一部分运行，并在涉及到身份验证时完成所有繁重的工作。然而，我们经常不需要它提供的许多部件。例如，device 不能很好地与基于 API 的系统一起工作，这就是为什么我们有了[device*令牌* auth gem](https://github.com/lynndylanhurley/devise_token_auth) 。device*token*auth 是一个库，它做了 device 所做的事情，但是用令牌代替了会话。

今天我们将从头开始探索构建我们自己的基于定制 [JWT](https://jwt.io) 的认证系统。让我们开始吧。

> 注意:本教程针对基于 API 的认证。

## 为什么是 JWT

JWT (JSON Web Token，读作“jot”)是一个独立的身份验证标准，旨在安全地在系统之间交换数据。因为它是独立的，所以不需要任何后备存储就能工作。此外，JWT 方法非常可靠和灵活，允许它用于任何客户端。它没有任何启动开销，而且几乎所有语言都有库，这使得使用 jwt 变得轻而易举。

我们已经有了一个优秀的关于 JWT 以及如何在 Rails 中使用它的教程。如果你是第一次来 JWT，我建议你先读一读，了解一下我们将要创造的东西。

## 模型

我们将首先为我们的应用程序构建一个模型。应用程序将验证用户，所以让我们创建一个`User`模型。

```
rails g model user 
```

该命令将在 **db/migrate** 下创建一个名为 **XXX *create* users** 的迁移文件，其中`XXX`是当前日期。将以下代码添加到该文件中，这将添加我们需要的列:

```
create_table :users do |t|
  t.string :email,              null: false
  t.string :password_digest,    null: false
  t.string   :confirmation_token
  t.datetime :confirmed_at
  t.datetime :confirmation_sent_at

  t.timestamps
end 
```

运行`rake db:migrate`运行迁移。

我们在这里添加了`email`和`password_digest`列，这是注册或验证用户所需的基本列。当然，如果您愿意，可以添加更多有意义的列。用户确认需要`confirmation_token`、`confirmed_at`和`confirmation_sent_at`栏。如果您不想确认电子邮件，可以跳过这一步。

### 确认

让我们将我们的验证添加到**模型/用户. rb** :

```
validates_presence_of :email
validates_uniqueness_of :email, case_sensitive: false
validates_format_of :email, with: /@/ 
```

我们正在验证电子邮件的唯一性，不区分大小写，并对电子邮件进行简单的格式检查，确保电子邮件字符串中有一个`@`。这可能不是一个可靠的验证，但没有一个标准，这就是为什么我们在注册时验证电子邮件。

### 回收

我们将利用 Rails 的 [has *secure* 密码](http://api.rubyonrails.org/classes/ActiveModel/SecurePassword/ClassMethods.html#method-i-has_secure_password)来处理密码散列。

首先，将宝石`bcrypt`(它可能在那里，但是被注释掉了)添加到你的**宝石文件**中，然后运行`bundle install`。一旦安装了 gem，在文件 **models/user.rb** 中的`User`类中添加下面一行代码:

```
class User < ApplicationRecord
  has_secure_password
  ... 
```

让我们也添加我们希望在用户创建之前执行的回调。首先，我们想小写的电子邮件，并删除任何空格。其次，我们必须生成一个确认令牌，它将通过电子邮件发送给用户。

将这些`before`回调添加到 **models/user.rb** 文件:

```
before_save :downcase_email
before_create :generate_confirmation_instructions 
```

在将电子邮件保存到数据库之前，我们先将其压缩。确认指示应仅在创建用户记录期间生成。让我们添加方法:

```
def downcase_email
  self.email = self.email.delete(' ').downcase
end

def generate_confirmation_instructions
  self.confirmation_token = SecureRandom.hex(10)
  self.confirmation_sent_at = Time.now.utc
end 
```

如果打算跳过确认，可以省略上面的方法和对应的回调。

## 登记

### 创造

我们现在已经有了模型设置，所以让我们为用户创建添加一个端点。创建我们的用户控制器:

```
rails g controller users 
```

将下面一行添加到 **config/routes.rb** :

```
resources :users, only: :create 
```

我们现在已经生成了我们的`UsersController`。转到控制器(**app/controllers/users _ controller . Rb**)并添加以下行:

```
def create
  user = User.new(user_params)

  if user.save
    render json: {status: 'User created successfully'}, status: :created
  else
    render json: { errors: user.errors.full_messages }, status: :bad_request
  end
end

private

def user_params
  params.require(:user).permit(:email, :password, :password_confirmation)
end 
```

我们现在有一个 API 端点来创建用户！您可以通过启动服务器并发送一个`POST`请求来尝试一下，请求的主体中包含 JSON 形式的用户详细信息。以下是您可以发布到`http://localhost:3000/users`的示例:

```
{
  user: {
    email: 'test@example.com',
    password: 'anewpassword',
    password_confirmation: 'anewpassword'
  }
} 
```

您应该会收到作为响应的`User created successfully`消息。具有相同数据的后续请求应该用错误消息来响应。这就是我们在游戏中的验证。对于那些跳过确认部分的人，你可以跳过下面的内容，直接进入`Login`部分。

### 确认

一件悬而未决的事情是用户确认。我们将在创建之前向用户发送一封电子邮件确认，并创建一个端点来验证令牌以确认用户。

首先，我们必须在成功创建记录后向用户发送一封电子邮件。我们不会讨论如何发送电子邮件，因为已经有很好的教程介绍了如何发送电子邮件。我们应该在`users_controller`中的`user.save`之后添加一行来发送电子邮件。

```
...
if user.save
  #Invoke send email method here
... 
```

确保你在邮件中包含了`user.confirmation_token`。理想情况下，URL 应该指向一个端点，该端点获取令牌并将其发送到我们的 API。让我们创建后 API 端点。

将确认端点的路由添加到我们的 **config/routes.rb** 文件中:

```
resources :users, only: :create do
  collection do
    post 'confirm'
  end
end 
```

现在，在`UsersController`中创建`confirm`动作:

```
def confirm
  token = params[:token].to_s

  user = User.find_by(confirmation_token: token)

  if user.present? && user.confirmation_token_valid?
    user.mark_as_confirmed!
    render json: {status: 'User confirmed successfully'}, status: :ok
  else
    render json: {status: 'Invalid token'}, status: :not_found
  end
end 
```

让我们看看我们在这里做什么。首先，我们从`params`获取令牌，调用`to_s`来处理请求中没有发送令牌的情况。接下来，根据确认令牌获取相应的用户。

如果用户在场，并且确认没有过期，调用模型方法`mark_as_confirmed!`并以成功消息响应。我们必须将`confirmation_token_valid?`和`mark_as_confirmed!`方法添加到我们的`User`模型中:

```
def confirmation_token_valid?
  (self.confirmation_sent_at + 30.days) > Time.now.utc
end

def mark_as_confirmed!
  self.confirmation_token = nil
  self.confirmed_at = Time.now.utc
  save
end 
```

`confirmation_token_valid?`方法检查确认是否是在最近 30 天内发送的，因此没有过期。您可以将其更改为您希望的任何值。

`mark_as_confirmed!`保存确认时间并取消确认令牌，这样同一封确认电子邮件就不能用于再次确认用户。

我们现在有了确认用户的端点。您可以通过向端点`users/confirm?token=<CONFIRMATION_TOKEN>`发送 post 请求来测试它，并为用户检查`confirmed_at`和`confirmation_token`值。你应该得到`User confirmed successfully`。具有相同令牌的后续请求应该返回`Invalid token`。

我们现在有一个工作注册作为我们的应用程序的一部分。让我们创建最后一部分:登录端点。

## 注册

对于登录，我们将创建一个端点，通过发送用户的凭证并使用 JWT 令牌进行响应来让用户登录。我们还将添加一个助手方法，该方法可用于保护我们希望仅向经过身份验证的用户公开的端点。

### 控制器

让我们将登录路由添加到`users`资源下的 **config/routes.rb** 文件中:

```
resources :users, only: :create do
    collection do
      post 'confirm'
      post 'login'
    end
end 
```

我们现在已经创建了一条`users/login`路线。控制器需要一些修改来引入`json_web_token`代码并在`UsersController`中创建相应的动作。

在**controllers/application _ controller . Rb**中，在类定义后添加下面一行:

```
require 'json_web_token' 
```

在**controllers/users _ controller . Rb**中添加以下代码片段:

```
def login
  user = User.find_by(email: params[:email].to_s.downcase)

  if user && user.authenticate(params[:password])
      auth_token = JsonWebToken.encode({user_id: user.id})
      render json: {auth_token: auth_token}, status: :ok
  else
    render json: {error: 'Invalid username / password'}, status: :unauthorized
  end
end 
```

让我们一步一步地复习这个方法。首先，我们从电子邮件中获取用户，如果存在的话，调用`authenticate`方法传递提供的密码。`authenticate`方法由`has_secure_password`助手提供。

一旦我们验证了电子邮件和密码，通过我们尚未创建的`JsonWebToken` lib 中的 encode 方法将用户的 id 编码成一个 JWT 令牌。然后，归还令牌。

对于那些正在确认电子邮件的用户，我们不得不拒绝那些没有被确认的用户。修改控制器操作，以包括一个更多的条件:

```
...
if user && user.authenticate(params[:password])
  if user.confirmed_at?
    auth_token = JsonWebToken.encode({user_id: user.id})
    render json: {auth_token: auth_token}, status: :ok
  else
    render json: {error: 'Email not verified' }, status: :unauthorized
  end
else
... 
```

检查`confirmed_at`字段是否不为空，这意味着用户在允许他们登录之前已经被确认。

### JWT 图书馆

现在，让我们添加 JWT 图书馆。首先将以下宝石添加到您的`Gemfile`中，然后执行`bundle install`:

```
gem 'jwt' 
```

完成后，在 **lib/** 下创建一个名为 **json *web* token.rb** 的文件，并添加以下几行:

```
require 'jwt'

class JsonWebToken
  # Encodes and signs JWT Payload with expiration
  def self.encode(payload)
    payload.reverse_merge!(meta)
    JWT.encode(payload, Rails.application.secrets.secret_key_base)
  end

  # Decodes the JWT with the signed secret
  def self.decode(token)
    JWT.decode(token, Rails.application.secrets.secret_key_base)
  end

  # Validates the payload hash for expiration and meta claims
  def self.valid_payload(payload)
    if expired(payload) || payload['iss'] != meta[:iss] || payload['aud'] != meta[:aud]
      return false
    else
      return true
    end
  end

  # Default options to be encoded in the token
  def self.meta
    {
      exp: 7.days.from_now.to_i,
      iss: 'issuer_name',
      aud: 'client',
    }
  end

  # Validates if the token is expired by exp parameter
  def self.expired(payload)
    Time.at(payload['exp']) < Time.now
  end
end 
```

让我们看一下代码。首先，在`encode`方法中，将有效负载(即用户 id)与元信息(如到期日、发布者和受众)合并。你可以从本文开头链接的 JWT 教程中了解这些“元”声明。一旦合并，使用`JWT.encode`方法和来自我们服务器的密钥对有效载荷进行编码。这个密钥保持安全是很重要的，因为这是我们的应用程序发布的所有令牌的主密钥。

然后是`decode`方法，它使用来自`jwt` gem 的`decode`方法来(你猜对了)使用秘密密钥解码有效载荷。我们有几个其他的助手方法，一个是`valid_payload`，它验证有效负载是否被篡改，另一个是`expired`方法，它验证令牌是否过期。在`meta`方法中设置的默认到期时间是 7 天，但是您可以根据自己的要求自由更改。

现在我们有了一个可以使用的登录端点来登录用户。尝试在请求正文中使用以下格式的数据调用端点`users/login`:

```
{
  "email": "test@example.com",
  "password": "anewpassword"
} 
```

您应该会看到类似这样的响应，

```
{
  "auth_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoxLCJleHAiOjE0NzUzMTM5OTQsImlzcyI6Imlzc3Vlcl9uYW1lIiwiYXVkIjoiY2xpZW50In0.5P3qJKelCdbTixnLyIrsLKSVnRLCv2lvHFpXqVKdPOs"
} 
```

就在那里。我们的用户身份验证令牌。我们现在可以使用这个令牌来验证用户的每个请求。

### 认证助手

好了，我们将创建一个 helper 方法，它从头部获取令牌，验证有效负载，并从 DB 中获取相应的用户。打开**/app/controllers/application _ controller . Rb**，添加以下内容:

```
protected
# Validates the token and user and sets the @current_user scope
def authenticate_request!
  if !payload || !JsonWebToken.valid_payload(payload.first)
    return invalid_authentication
  end

  load_current_user!
  invalid_authentication unless @current_user
end

# Returns 401 response. To handle malformed / invalid requests.
def invalid_authentication
  render json: {error: 'Invalid Request'}, status: :unauthorized
end

private
# Deconstructs the Authorization header and decodes the JWT token.
def payload
  auth_header = request.headers['Authorization']
  token = auth_header.split(' ').last
  JsonWebToken.decode(token)
rescue
  nil
end

# Sets the @current_user with the user_id from payload
def load_current_user!
  @current_user = User.find_by(id: payload[0]['user_id'])
end 
```

这里，`authenticate_request!`是我们将要用来认证控制器动作的助手方法。它从请求的`Authorization`头中获取有效载荷，然后使用我们之前见过的`valid_payload`方法验证有效载荷。一旦确认有效，它使用有效负载中的`user_id`获取用户，将用户记录加载到作用域中。

我们现在可以将`authenticate_request`方法作为一个`before_filter`添加到我们希望为用户验证的任何控制器动作中。

## 结论

至此，本教程到此结束。我们讨论了用户认证的重要方面——注册、确认和登录——所有这些都是通过使用 JWT 的 API 实现的。你可以继续这样做，也可以对它的某些部分做一些修改，比如添加单独的组件，比如密码重置、电子邮件更新、帐户锁定等等。

示例应用程序托管在 [github](https://github.com/avinoth/auth-scratch-demo) 上。随便叉，随便弄。

我感谢你阅读这篇教程，我希望它能为你服务。快乐学习。

## 分享这篇文章