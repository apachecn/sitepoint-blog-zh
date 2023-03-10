# OAuth 2 oPRO 的所有东西:用户和 API

> 原文：<https://www.sitepoint.com/oauth-2-all-the-things-with-opro-users-and-api/>

![o2](img/8cbf15074210bae4df7dcef76af6a628.png)

在我的[上一篇文章](https://www.sitepoint.com/authenticate-all-the-things-with-opro-the-basics/)中，我们遇到了[oPRO](https://github.com/opro/opro)——一个构建成熟 OAuth 2 提供者的 Rails 引擎。我们已经创建了一个服务器(实际的提供者)和一个客户端应用程序。目前，该服务器有一个由[设计](https://github.com/plataformatec/devise)支持的基本认证系统。用户能够创建应用程序来接收客户端和密钥，通过 OAuth 2 进行身份验证，并执行示例 API 请求。然而，目前在客户端上没有用于用户数据的存储机制。此外，除了用户的令牌之外，我们不会获得关于用户的任何信息。本文将解决这些问题，并介绍更多的 API 操作和重构代码。

服务器和客户端应用程序的源代码可以在 GitHub 上找到[。](https://github.com/bodrovis/Sitepoint-source/tree/master/Auth_Provider_with_Opro)

## 介绍用户

在用户会话中存储令牌并不方便，因此我想为客户端应用程序引入一个新的`users`表，并将所有相关数据保存在那里。有一个合适的模型也将允许我们提取一些方法来整理控制器。

创建新迁移:

```
$ rails g model User email:string uid:string access_token:string refresh_token:string expires_at:string 
```

除了令牌和到期信息，我们还将存储用户的 uid 和电子邮件——就像您在使用 Twitter 或脸书等认证提供商时可能会做的那样。

修改迁移以包括一些索引:

*migrations/XXX _ create _ users . Rb*

```
class CreateUsers < ActiveRecord::Migration
  def change
    create_table :users do |t|
      t.string :email, index: true
      t.string :uid, index: true, unique: true
      t.string :access_token
      t.string :refresh_token
      t.string :expires_at

      t.timestamps null: false
    end
  end
end 
```

并应用它:

```
$ rake db:migrate 
```

此外，添加基本验证规则:

*型号/用户. rb*

```
[...]
validates :access_token, presence: true
validates :refresh_token, presence: true
validates :expires_at, presence: true
validates :uid, presence: true, uniqueness: true
[...] 
```

准备就绪后，我们可以调整`SessionsController`(现在，我将只编写它的框架代码):

*会话 _ 控制器. rb*

```
[...]
def create
  user = authenticate_and_save_user
  if user
    login_user
    flash[:success] = "Welcome, #{user.email}!"
  else
    flash[:warning] = "Can't authenticate you..."
  end
  redirect_to root_path
end
[...] 
```

有两件事我们必须注意:执行用户认证和实际登录。我们可能会留下上一次迭代的代码

```
JSON.parse RestClient.post( [...] ) 
```

就在控制器的动作中，但这不是一个好主意。在生产环境中，您可能会为您的应用程序创建一个单独的身份验证适配器，并将其作为一个瑰宝。然而，出于我们的目的，将代码提取到一个单独的文件中就足够了。我将坚持使用模型，但是您也可以将它放在 *lib* 目录中(只是不要忘记需要该文件)。

*型号/opro_api.rb*

```
class OproApi
  TOKEN_URL = "#{ENV['opro_base_url']}/oauth/token.json"
  API_URL = "#{ENV['opro_base_url']}/api"

  attr_reader :access_token, :refresh_token

  def initialize(access_token: nil, refresh_token: nil)
    @access_token = access_token
    @refresh_token = refresh_token
  end

  def authenticate!(code)
    return if access_token
    JSON.parse(RestClient.post(TOKEN_URL,
                               {
                                   client_id: ENV['opro_client_id'],
                                   client_secret: ENV['opro_client_secret'],
                                   code: code
                               },
                               accept: :json))
  end
end 
```

`TOKEN_URL`和`API_URL`只是方便常数。

`access_token`和`refresh_token`是将在该类的各种方法中使用的实例变量。

在定义`initialize`方法时，我以散列方式列出参数——这个很酷的特性在较新版本的 Ruby 中得到支持，并允许轻松传递参数(您不必记住它们的顺序)。

`authenticate!`包含前一次迭代的代码(我只添加了一个`return`语句)。它向
发送请求并返回解析后的 JSON。

现在，回到控制器:

*会话 _ 控制器. rb*

```
[...]
def create
  user = User.from_opro(OproApi.new.authenticate!(params[:code]))
  if user
    login_user
    flash[:success] = "Welcome, #{user.email}!"
  else
    flash[:warning] = "Can't authenticate you..."
  end
  redirect_to root_path
end
[...] 
```

这里我们利用新创建的`authenticate!`方法。下一步是编写`from_opro`类方法，该方法获取用户的 auth 散列并将其存储到数据库中。如果你读过我关于 OAuth 2 的文章，我在那里介绍了一个非常相似的方法，叫做`from_omniauth`。

*型号/用户. rb*

```
[...]
class << self
  def from_opro(auth = nil)
    return false unless auth
    user = User.find_or_initialize_by(uid: auth['uid'])
    user.access_token = auth['access_token']
    user.refresh_token = auth['refresh_token']
    user.email = auth['email']
    user.save
    user
  end
end
[...] 
```

如果由于某种原因，auth hash 没有值，只需返回`false`——这将使我们的控制器呈现一个错误。否则，通过 uid 查找或初始化一个用户，更新所有属性，并返回结果记录。

太好了！最后一步是执行登录。为此，我将介绍几个助手方法:

*application _ controller . Rb*

```
[...]
private
[...]

def login(user)
  session[:user_id] = user.id
  current_user = user
end

def current_user
  @current_user ||= User.find_by(id: session[:user_id])
end

def current_user=(user)
  @current_user = user
end

helper_method :new_opro_token_path, :current_user
[...] 
```

这些是非常基本的方法，你可能已经见过无数次了。将在视图中被使用，所以我将其标记为 helper。

让我们也修改一下视图:

*views/pages/index . html . erb*

```
<h1>Welcome!</h1>
<% if current_user %>
  <ul>
    <li><%= link_to 'Show some money', api_tests_path %></li>
  </ul>
<% else %>
  <%= link_to 'Authenticate via oPRO', new_opro_token_path %>
<% end %> 
```

我猜用户可能想注销，所以也要给出一个相应的动作:

*config/routes.rb*

```
[...]
delete '/logout', to: 'sessions#destroy', as: :logout
[...] 
```

*会话 _ 控制器. rb*

```
[...]
def destroy
  logout
  flash[:success] = "See you!"
  redirect_to root_path
end
[...] 
```

`logout`还有一种方法:

*application _ controller . Rb*

```
[...]
def logout
  session.delete(:user_id)
  current_user = nil
end
[...] 
```

呈现新链接:

*views/pages/index . html . erb*

```
<h1>Welcome!</h1>
<% if current_user %>
  <ul>
    <li><%= link_to 'Show some money', api_tests_path %></li>
  </ul>
  <%= link_to 'Logout', logout_path, method: :delete %>
<% else %>
  <%= link_to 'Authenticate via oPRO', new_opro_token_path %>
<% end %> 
```

## 调整认证散列

此时您可能会想，“如果默认情况下，oPRO 只列出令牌和到期信息，我们怎么能获取用户的电子邮件和 uid 呢？”嗯，你是对的，我们必须做点什么。然而，问题是当前的稳定版本没有办法重新定义`TokenController`。该控制器实际上负责生成所有令牌并创建认证散列。然而，我[对合并到`master`分支的 oPRO 源代码做了一些调整](https://github.com/opro/opro/issues/52#issuecomment-161819767)。现在，您必须直接指定它:

*Gemfile*

```
[...]
gem 'opro', github: 'opro/opro', branch: 'master'
[...] 
```

如果将来有什么变化，我会根据需要更新这篇文章。

首先，介绍一条新路线:

*config/routes.rb*

```
[...]
mount_opro_oauth controllers: {
    oauth_new: 'oauth/auth',
    oauth_token: 'oauth/token'
}, except: :docs
[...] 
```

创建一个从原始控制器继承的自定义控制器:

*控制器/oauth/token_controller.rb*

```
class Oauth::TokenController < Opro::Oauth::TokenController
end 
```

我们不需要修改任何动作，因为唯一需要做的是改变视图:

*views/oauth/token/create . JSON . jbuilder*

```
json.access_token @auth_grant.access_token
json.token_type Opro.token_type || 'bearer'
json.refresh_token @auth_grant.refresh_token
json.expires_in @auth_grant.expires_in
json.uid @auth_grant.user.id
json.email @auth_grant.user.email 
```

jbuilder 负责为我们创建合适的 JSON。

除了`uid`和`email`之外，一切都取自[的原始视图](https://github.com/bodrovis/opro/blob/master/app/views/opro/oauth/token/create.json.jbuilder)。如有必要，您可以在此添加任何其他字段。

你可以走了。启动服务器并尝试进行身份验证。所有的用户信息都应该被正确地存储。

## 使用 API

### 一点重构

我们做得很好，但是我仍然不喜欢我们的代码执行 API 请求的方式。让我们将其提取到 *opro_api.rb* 文件中:

*型号/opro_api.rb*

```
[...]
def test_api
  JSON.parse(RestClient.get("#{ENV['opro_base_url']}/oauth_tests/show_me_the_money.json",
                            params: {
                                access_token: access_token
                            },
                            accept: :json))

end
[...] 
```

现在可以从控制器的动作中调用它:

*api_tests_controller.rb*

```
class ApiTestsController < ApplicationController
  before_action :prepare_client

  def index
    @response = @client.test_api
  end

  private

  def prepare_client
    @client = OproApi.new(access_token: current_user.access_token)
  end
end 
```

我们将要求`@client`执行任何 API 请求，因此我将它放在了`before_action`中。

这很好，但是如果当前用户由于某种原因没有令牌呢？让我们先检查一下，然后再做其他事情:

*api_tests_controller.rb*

```
class ApiTestsController < ApplicationController
  before_action :check_token
  before_action :prepare_client
  [...]

  private
  [...]
  def check_token
    redirect_to new_opro_token_path and return if !current_user || current_user.token_missing?
  end
end 
```

*型号/用户. rb*

```
[...]
def token_missing?
  !self.access_token.present?
end
[...] 
```

现在，如果令牌不存在，用户将被要求再次进行身份验证。

### 引入更多操作

让我们再添加两个自定义 API 操作:获取用户信息和更新用户。第一步是在服务器应用程序上添加一个新的`Api::UsersController`:

*config/routes.rb*

```
[...]
namespace :api do
  resources :users, only: [:show, :update]
end
[...] 
```

*控制器/api/users_controller.rb*

```
class Api::UsersController < ApplicationController
  def show
    @user = User.find(params[:id])
  end

  def update
    @user = User.find(params[:id])
    @user.last_sign_in_ip = params[:ip]
    render json: {result: @user.save}
  end
end 
```

`update`做什么并不重要，所以出于演示的目的，让我们简单地修改由 Devise 引入的`last_sign_in_ip`列。

别忘了一个观点:

*views/API/users/show . JSON . jbuilder*

```
json.user do
  json.email @user.email
end 
```

我再次返回一些样本数据。

然而，有两件事需要注意。在实际执行这些操作之前，我们没有检查请求中是否有访问令牌，以及它是否有效。oPRO 提供了一个`allow_oauth!`方法，它像`before_action`一样接受`only`和`except`选项。默认情况下，不允许基于访问令牌执行任何操作，因此将这一行添加到控制器:

*控制器/api/users_controller.rb*

```
class Api::UsersController < ApplicationController
  allow_oauth!
  [...]
end 
```

另一件要考虑的事情是，我们在执行`update`动作时不会发送 CSRF 令牌，所以 Rails 会引发一个异常。要避免这种情况，请修改以下代码行:

*application _ controller . Rb*

```
[...]
protect_from_forgery
[...] 
```

现在[请求伪造保护](http://api.rubyonrails.org/classes/ActionController/RequestForgeryProtection/ClassMethods.html)将使用`null_session`，这意味着如果没有提供 CSRF 令牌，会话将被无效，但不会完全重置。只要我们依靠访问令牌来执行身份验证，一切都应该没问题。

回到客户端的应用程序，调整我们可怜的人的 API 适配器:

*型号/opro_api.rb*

```
[...]
def get_user(id)
  JSON.parse(RestClient.get("#{API_URL}/users/#{id}.json",
                            params: {
                                access_token: access_token
                            },
                            accept: :json))
end

def update_user(id)
  JSON.parse(RestClient.patch("#{API_URL}/users/#{id}.json",
                              {
                                  access_token: access_token,
                                  ip: "#{rand(100)}.1.1.1"
                              },
                              accept: :json))
end
[...] 
```

现在控制器:

*api_tests_controller.rb*

```
[...]
def show
  @response = @client.get_user(params[:id])
end

def update
  @response = @client.update_user(params[:id])
end
[...] 
```

视图将简单地呈现一个响应:

*views/API _ tests/show . html . erb*

```
<pre><%= @response.to_yaml %></pre> 
```

*views/API _ tests/update . html . erb*

```
<pre><%= @response.to_yaml %></pre> 
```

添加新路线:

*config/routes.rb*

```
[...]
resources :api_tests, only: [:index, :show, :update]
[...] 
```

提供这些新操作的链接:

*views/pages/index . html . erb*

```
[...]
<ul>
  <li><%= link_to 'Show some money', api_tests_path %></li>
  <li><%= link_to 'Get a user', api_test_path(1) %></li>
  <li><%= link_to 'Update a user', api_test_path(1), method: :patch %></li>
</ul>
[...] 
```

我只是在这里硬编码了用户的 id，但这对这个演示来说并不重要。继续使用 API 吧！但是，请注意，为了让`update`动作正确工作，您必须在认证时允许对应用程序的“写”访问。我将在后面的章节中详细介绍各种权限。

## 结论

我们的应用程序开始看起来相当不错，但是这还不够。我们还有一些事情要处理:

*   接入令牌应该具有有限的寿命，并且应该引入一些速率限制。
*   我们还没有讨论其他高级主题，比如使用 scope、介绍您自己的身份验证解决方案以及将用户凭证换成令牌。

因此，本文的最后但并非最不重要的部分将涵盖所有这些主题。抓紧了！

## 分享这篇文章