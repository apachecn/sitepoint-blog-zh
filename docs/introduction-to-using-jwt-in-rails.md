# 在 Rails 中使用 JWT 认证的介绍

> 原文：<https://www.sitepoint.com/introduction-to-using-jwt-in-rails/>

![](img/c252672c5c1884bc27d08581b107e243.png)

随着单页应用程序(SPA)和移动应用程序的出现，API 已经走到了 web 开发的前沿。随着我们开发 API 来支持我们的 SPA 和移动应用程序，保护 API 已经成为一个主要的棘手问题。基于令牌的身份验证是最受欢迎的身份验证机制之一，但是令牌容易受到各种攻击。为了缓解这种情况，人们必须实现修复问题的方法，这通常会导致一次性的解决方案，使令牌在不同的系统之间不可交换。JSON Web Tokens (JWT) 是为了实现基于标准的令牌处理和验证而创建的，这些令牌可以在不同的系统之间毫无问题地交换。

## 什么是 JWT？

jwt 通过 JSON 携带信息(称为“声明”)，因此得名 JSON Web 令牌。JWT 是一个标准，已经在几乎所有流行的编程语言中实现。因此，它们可以很容易地在不同平台上实现的系统中使用或交换。

jwt 是由普通字符串组成的，因此可以很容易地在 URL 或 HTTP 头中进行交换。它们也是自包含的，并携带诸如有效载荷和签名之类的信息。

## JWT 的解剖

JWT(读作“JOT”)由三个字符串组成，用“.”分隔：

```
aaaaa.bbbbbbb.ccccccc 
```

第一部分是报头，第二部分是有效载荷，第三部分是签名。

标题由两部分组成:

*   令牌的类型，即“JWT”
*   使用的哈希算法

例如:

```
{
  "typ": "JWT",
  "alg": "HS256"
} 
```

标头是 base64 编码的，其结果是:

```
aeyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9 
```

这是令牌的第一部分。

JWT 的第二部分是有效载荷。这部分携带令牌中的有趣信息，也称为 JWT 声明。索赔有三种类型——私人索赔、公共索赔和注册索赔。

*   已登记的权利要求是保留名称但不强制使用的权利要求。例如，iss、sub、aud 等。
*   私人债权是双方约定的名称，可能会与其他公共债权发生冲突。必须谨慎使用。
*   公开声明—我们可以根据我们的身份认证要求创建的声明，如用户名、用户信息等。

我们可以创建一个示例负载，如下所示:

```
{
  "iss": "sitepoint.com",
  "name": "Devdatta Kane",
  "admin": true
} 
```

这将被编码为-

```
ew0KICAiaXNzIjogInNpdGVwb2ludC5jb20iLA0KICAibmFtZSI6ICJEZXZkYXR0YSBLYW5lIiwNCiAgImFkbWluIjogdHJ1ZQ0KfQ 
```

这成为 token 的第二部分。

第三，也可能是最重要的部分是签名。它由三部分组成:头部、有效载荷和秘密。我们通过一个 HMACSHA256 函数运行头部和有效载荷的组合字符串，并将“secret”作为服务器端机密。像这样:

```
require "openssl"
require "base64"

var encodedString = Base64.encode64(header) + "." + Base64.encode64(payload);
hash  = OpenSSL::HMAC.digest("sha256", "secret", encodedString) 
```

因为只有服务器知道秘密，所以没有人能够篡改有效载荷，并且服务器能够使用签名检测任何篡改。

我们的签名如下:

```
2b3df5c199c0b31d58c3dc4562a6e1ccb4a33cced726f3901ae44a04c8176f37 
```

现在我们已经得到了 JWT 的三个部分。结合这三部分，我们得到:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.ew0KICAiaXNzIjogInNpdGVwb2ludC5jb20iLA0KICAibmFtZSI6ICJEZXZkYXR0YSBLYW5lIiwNCiAgImFkbWluIjogdHJ1ZQ0KfQ.2b3df5c199c0b31d58c3dc4562a6e1ccb4a33cced726f3901ae44a04c8176f37 
```

这是我们完整的 JWT，可用于进一步的要求。

## 在 Rails 中使用 JWT

JWT 有几乎所有平台的库，Ruby 也不例外。我们将创建一个简单的 Rails 应用程序，它使用优秀的 Devise gem 进行身份验证，使用 jwt gem 创建和验证 jwt 令牌。

让我们用联系模型和 CRUD 创建一个示例 Rails 应用程序。该应用程序使用 Rails 4.2 和 SQlite:

```
rails new jwt_on_rails 
```

生成应用程序后，创建一个家庭控制器，我们将使用它来检查我们的身份验证。这里是我们在`app/controllers`中的`home_controller.rb`。像这样:

```
class HomeController < ApplicationController
  def index

  end
end 
```

将`HomeController`映射到`config/routes.rb`中的`/home`:

```
Rails.application.routes.draw do
  get 'home' => 'home#index'
end 
```

检查它是如何工作的:

```
rails s 
```

将你最喜欢的浏览器指向 [http://localhost:3000/](http://localhost:3000/) ，检查一切是否正常。

我们已经准备好了基础应用程序。现在，在我们的应用程序中添加 Devise。首先，我们将在我们的`Gemfile`中添加设计和智威汤逊宝石。像这样:

```
gem 'devise'
gem 'jwt' 
```

使用以下方式安装它们:

```
bundle install 
```

现在让我们创建设备配置文件:

```
rails g devise:install 
```

我们将创建 device`User`模型并迁移数据库:

```
rails g devise User
rake db:migrate 
```

我们的`User`模型已经就绪，我们将使用它进行身份验证。是时候将 jwt 集成到我们的应用程序中了。首先，我们将在`lib/json_web_token.rb`中创建一个名为`JsonWebToken`的类。这个类将封装 JWT 令牌编码和解码逻辑。像这样:

```
class JsonWebToken
  def self.encode(payload)
    JWT.encode(payload, Rails.application.secrets.secret_key_base)
  end

  def self.decode(token)
    return HashWithIndifferentAccess.new(JWT.decode(token, Rails.application.secrets.secret_key_base)[0])
  rescue
    nil
  end
end 
```

添加一个初始化器，用于将`JsonWebToken`类包含在`config/initializers/jwt.rb`中。像这样:

```
require 'json_web_token' 
```

我们现在将在`ApplicationController`类中添加一些我们将在`AuthenticationController`类中使用的助手方法。

在`app/controllers/application_controller.rb`中:

```
class ApplicationController < ActionController::Base
  attr_reader :current_user

  protected
  def authenticate_request!
    unless user_id_in_token?
      render json: { errors: ['Not Authenticated'] }, status: :unauthorized
      return
    end
    @current_user = User.find(auth_token[:user_id])
  rescue JWT::VerificationError, JWT::DecodeError
    render json: { errors: ['Not Authenticated'] }, status: :unauthorized
  end

  private
  def http_token
      @http_token ||= if request.headers['Authorization'].present?
        request.headers['Authorization'].split(' ').last
      end
  end

  def auth_token
    @auth_token ||= JsonWebToken.decode(http_token)
  end

  def user_id_in_token?
    http_token && auth_token && auth_token[:user_id].to_i
  end
end 
```

我们在这里所做的是添加一些像`authenticate_request!`这样的辅助方法，它们将作为一个`before_filter`来检查用户凭证。我们将创建一个`AuthenticationController`来处理所有对 API 的认证请求。像这样:

在`app/controllers/authentication_controller.rb`中:

```
class AuthenticationController < ApplicationController
  def authenticate_user
    user = User.find_for_database_authentication(email: params[:email])
    if user.valid_password?(params[:password])
      render json: payload(user)
    else
      render json: {errors: ['Invalid Username/Password']}, status: :unauthorized
    end
  end

  private

  def payload(user)
    return nil unless user and user.id
    {
      auth_token: JsonWebToken.encode({user_id: user.id}),
      user: {id: user.id, email: user.email}
    }
  end
end 
```

这里我们添加了`AuthenticationController`来实现认证端点。它使用 Devise 对用户进行身份验证，如果凭证有效，则颁发一个 JWT。

我们现在将更新我们的`routes.rb`来添加认证端点。像这样:

```
Rails.application.routes.draw do
  post 'auth_user' => 'authentication#authenticate_user'
  get 'home' => 'home#index'
end 
```

此外，修改`HomeController`以使用`before_filter`保护它，并在成功认证的情况下添加有意义的响应:

```
class HomeController < ApplicationController
  before_filter :authenticate_request!

  def index
    render json: {'logged_in' => true}
  end
end 
```

现在，创建一个示例用户来使用 Rails 控制台测试身份验证机制。

```
rails c
rails> User.create(email:'a@a.com', password:'changeme', password_confirmation:'changeme') 
```

启动服务器，了解 JWT 身份验证的工作原理:

```
rails s 
```

打开另一个终端，使用 cURL 测试 API。首先，尝试不使用任何电子邮件或密码进行身份验证:

```
curl http://localhost:3000/home 
```

响应应该是`{"errors":["Not Authenticated"]}`，因为我们没有提供任何凭证。

现在根据 API 进行身份验证，并接收一个 JWT，我们将在后续请求中使用它:

```
curl -X POST -d email="a@a.com" -d password="changeme" http://localhost:3000/auth_user 
```

您将收到一个成功的响应，以及一个 JSON Web 令牌和其他用户信息。像这样:

```
{"auth_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoxfQ.po9twTrX99V7XgAk5mVskkiq8aa0lpYOue62ehubRY4","user":{"id":1,"email":"a@a.com"}} 
```

在对`/home`的请求中使用我们的新鲜`auth_token`。像这样:

```
curl --header "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoxfQ.po9twTrX99V7XgAk5mVskkiq8aa0lpYOue62ehubRY4" http://localhost:3000/home 
```

我们应该会收到一个成功的登录响应，比如:

```
{"logged_in":true} 
```

# 结论

现在，我们可以在任何 Angular/React/Ember 应用程序中使用这个 API，方法是存储发出的 JWT(在 cookie 或本地存储中)并在后续请求中使用它。这就结束了我们的教程，其中我们学习了如何在 Rails 应用程序中实现 JWT 以及 Devise。虽然本教程只讲述了基础知识，但它是使用 jwt 进行 API 认证的基础。

希望你喜欢这个教程。一如既往地欢迎评论和反馈。

## 分享这篇文章