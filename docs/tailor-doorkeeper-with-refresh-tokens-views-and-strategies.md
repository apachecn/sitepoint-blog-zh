# 用刷新令牌、视图和策略定制看门人

> 原文：<https://www.sitepoint.com/tailor-doorkeeper-with-refresh-tokens-views-and-strategies/>

![](img/eb9528092d154012457b7674a742dad0.png)

在本系列的前一部分中，我们遇到了[看门人](https://github.com/doorkeeper-gem/doorkeeper)，一个构建定制 OAuth 2 提供者的 Rails 引擎。我展示了如何将这个解决方案集成到您的应用程序中，以及如何使用它来保护 API 请求。

目前，用户能够注册他们的 OAuth 2 应用程序，接收访问令牌，执行 API 请求，以及使用作用域。但是，还有一些事情需要处理:

*   任何人都可以访问 OAuth 2 应用程序列表
*   用户无法获得刷新令牌并使用它们来获取新的访问令牌
*   最好能设计一个定制的 OmniAuth 策略，以后可以打包成宝石
*   我们需要一种方法来调整视图和路线

因此，在本文中，我们将关注这些问题。

源代码可以在 [GitHub](https://github.com/bodrovis/Sitepoint-source/tree/master/Auth_Provider_with_Doorkeeper) 上找到。

如果你没有按照上一部分的说明去做，你可以克隆这个分支。

## 保护 OAuth 2 应用程序

提醒您一下，此时我们有两个应用程序:一个所谓的“服务器”(实际的身份验证提供者)和一个“客户端”(用于测试目的的应用程序)。目前，即使用户没有通过身份验证，他们仍然可以访问[localhost:3000/OAuth/applications](http://localhost:3000/oauth/applications)并浏览已注册 OAuth 2 应用程序的列表。显然，这不好，所以我们需要一种方法来限制对该页面的访问。

幸运的是，看门人提供了一个[简便的方法](https://github.com/doorkeeper-gem/doorkeeper#applications-list)来解决这个问题。调整看门人的初始化文件:

*config/initializer/door keeper . Rb*

```
[...]
admin_authenticator do
  User.find_by_id(session[:user_id]) || redirect_to(new_session_url)
end
[...] 
```

这与我们在上一部分所做的非常相似，但是现在我们设置的是`admin_authenticator`而不是`resource_owner_authenticator`。对于真正的应用程序，你可能想使用一个更复杂的解决方案(比如要求用户有一个“管理员”角色，等等。)，但这对于演示来说是没问题的。

重新加载服务器并导航到[localhost:3000/oauth/applications](http://localhost:3000/oauth/applications)。如果您未通过身份验证，您将被重定向到登录页面。这意味着一切都在按计划进行。

## 引入刷新令牌

默认情况下，访问令牌在 2 小时后过期，此后用户必须再次重复身份验证过程。如果您需要调整这种行为，取消注释并修改看门人的初始化文件中的`access_token_expires_in 2.hours`行。顺便说一下，这里你也可以调整`authorization_code_expires_in 10.minutes`的设置。此代码在身份验证阶段用于接收访问令牌，其生命周期应该很短。

然而，为了方便用户，我们可能希望引入[刷新令牌](https://github.com/doorkeeper-gem/doorkeeper/wiki/Enable-Refresh-Token-Credentials)。默认情况下，看门人不签发它们，但这可以很容易地更改:

*config/initializer/door keeper . Rb*

```
[...]
use_refresh_token
[...] 
```

更改此文件后，不要忘记重新启动服务器。

现在打开您的客户端应用程序，修改`SessionsController`以存储刷新令牌和访问令牌到期时间:

*会话 _ 控制器. rb*

```
class SessionsController < ApplicationController
  def create
    req_params = "client_id=#{ENV['oauth_token']}&client_secret=#{ENV['oauth_secret']}&code=#{params[:code]}&grant_type=authorization_code&redirect_uri=#{ENV['oauth_redirect_uri']}"
    response = JSON.parse RestClient.post("#{ENV['server_base_url']}/oauth/token", req_params)
    session[:access_token] = response['access_token']
    session[:refresh_token] = response['refresh_token']
    session[:expires_at] = response['expires_in'].seconds.from_now
    redirect_to root_path
  end
end 
```

`response['expires_in']`包含表示令牌将过期的秒数，但是为了方便起见，我将它转换为一个`DateTime`对象。

现在介绍两条新路线:

*config/routes.rb*

```
[...]
get '/user', to: 'users#show', as: :user
get '/user/update', to: 'users#update', as: :update_user
[...] 
```

目前我们的主页有以下内容:

*views/pages/index . html . erb*

```
<h1>Welcome!</h1>

<% if session[:access_token] %>
  <%= link_to 'Get User', "http://localhost:3000/api/user?access_token=#{session[:access_token]}" %>
  <%= link_to 'Update User', "http://localhost:3000/api/user/update?access_token=#{session[:access_token]}" %>
<% else %>
  <%= link_to 'Authorize via Keepa', new_oauth_token_path %>
<% end %> 
```

我想将这些“获取用户”和“更新用户”URL 提取到控制器操作中，这就是我们创建新路由的原因:

*views/pages/index . html . erb*

```
[...]
<% if session[:access_token] %>
  <%= link_to 'Get User', user_path %>
  <%= link_to 'Update User', update_user_path %>
<% else %>
  <%= link_to 'Authorize via Keepa', '/auth/keepa' %>
<% end %> 
```

这是新的控制器:

*users_controller.rb*

```
class UsersController < ApplicationController
  def show
    redirect_to "http://localhost:3000/api/user?access_token=#{session[:access_token]}"
  end

  def update
    redirect_to "http://localhost:3000/api/user/update?access_token=#{session[:access_token]}"
  end
end 
```

我们现在需要做的是在执行 API 请求之前检查访问令牌是否已经过期。最简单的方法是雇佣一个`before_action`:

*users_controller.rb*

```
[...]
before_action :check_access_token
[...]
private

def check_access_token
  redirect_to root_path unless session[:access_token]
  if session[:expires_at] <= Time.current
    req_params = "client_id=#{ENV['oauth_token']}&client_secret=#{ENV['oauth_secret']}&refresh_token=#{session[:refresh_token]}&grant_type=refresh_token&redirect_uri=#{ENV['oauth_redirect_uri']}"
    response = JSON.parse RestClient.post("#{ENV['server_base_url']}/oauth/token", req_params)
    set_oauth_info_from response
  end
end 
```

如果没有设置访问令牌，我们只需将用户重定向回来。如果令牌已经过期，我们将生成一个新的字符串，其中包含请求参数`client_id`、`client_secret`、`refresh_token`、`redirect_uri`和`grant_type`(必须将[设置为](https://tools.ietf.org/html/rfc6749#section-6)到`refresh_token`)。你可能会问，范围呢？[默认情况下](https://tools.ietf.org/html/rfc6749#section-6)，将授予新的访问令牌来执行用户最初批准的相同操作。您可以缩小范围，但不能添加以前未提供的范围。

接下来，我们获取响应并将其传递给现在将要介绍的`set_oauth_info_from`方法:

*application _ controller . Rb*

```
[...]
private

def set_oauth_info_from(response)
  session[:access_token] = response['access_token']
  session[:refresh_token] = response['refresh_token']
  session[:expires_at] = response['expires_in'].seconds.from_now
end 
```

在`SessionsController`中使用这个方法来避免代码重复:

*会话 _ 控制器. rb*

```
def create
  req_params = "client_id=#{ENV['oauth_token']}&client_secret=#{ENV['oauth_secret']}&code=#{params[:code]}&grant_type=authorization_code&redirect_uri=#{ENV['oauth_redirect_uri']}"
  response = JSON.parse RestClient.post("#{ENV['server_base_url']}/oauth/token", req_params)
  set_oauth_info_from response
  redirect_to root_path
end 
```

就是这样！现在，我们的用户能够毫无问题地接收刷新令牌！

## 构建自定义 OmniAuth 提供程序

如果有一天你的应用程序变得足够受欢迎，你可能会想要制定一个定制的 [OmniAuth](https://github.com/intridea/omniauth) 策略，并将其作为一颗宝石来分发。看来这可以很容易地完成。当然，我们不会创建一个单独的 gem，相反，让我们将我们的新策略存储在客户端应用程序的 **lib** 文件夹中。

*lib/omni auth/strategies/keep a . Rb*

```
require 'omniauth-oauth2'

module OmniAuth
  module Strategies
    class Keepa < OmniAuth::Strategies::OAuth2
      option :name, :keepa

      option :client_options, {
          :site => "http://localhost:3000",
          :authorize_url => "/oauth/authorize"
      }

      uid { raw_info["id"] }

      info do
        {
            :email => raw_info["email"]
        }
      end

      def raw_info
        @raw_info ||= access_token.get('/api/user').parsed
      end
    end
  end
end 
```

我已经将我的策略命名为 Keepa，因此应该相应地设置类名和`option :name`。

`raw_info`方法用于获取通过我们的策略进行身份验证的用户的信息。`/api/user`是我们在上一篇文章中介绍过的路线。让我提醒您相应的操作是什么样子的:

*API/用户 _ 控制器. rb*

```
[...]
def show
  render json: current_resource_owner.as_json
end

private

def current_resource_owner
  User.find(doorkeeper_token.resource_owner_id) if doorkeeper_token
end
[...] 
```

我们基本上获取启动身份验证阶段的用户，并以 JSON 格式呈现他的信息。

`uid { raw_info["id"] }`解释使用什么字段作为唯一标识符。我们使用表中的标识符，但是您可以引入一个特殊的字段来代替。

```
info do
  {
      :email => raw_info["email"]
  }
end 
```

这表明在成功认证后，回调操作中哪些字段是可用的。您可以根据需要添加其他字段。

这段代码依赖于 [omniauth-oauth2](https://github.com/intridea/omniauth-oauth2) 抽象策略，所以把它连接起来:

*Gemfile*

```
[...]
gem 'omniauth-oauth2', '1.3.1'
[...] 
```

然后跑

```
$ bundle install 
```

因为[影响看门人](https://github.com/doorkeeper-gem/doorkeeper/issues/732)的[这个问题](https://github.com/intridea/omniauth-oauth2/issues/81)，我在这里使用 1.3.1 版本。

现在为客户端应用程序创建一个新的初始化文件:

*config/initializer/omni auth . Rb*

```
require File.expand_path('lib/omniauth/strategies/keepa', Rails.root)

Rails.application.config.middleware.use OmniAuth::Builder do
  provider :keepa, ENV['oauth_token'], ENV['oauth_secret'], scope: 'public write'
end 
```

如果您曾经在项目中使用过 OmniAuth，这应该非常熟悉。唯一需要注意的是，我们需要明确地要求策略文件，因为它不是自动加载的。

`public`和`write`是上一篇文章中介绍的作用域。应该使用空格作为分隔符。

OmniAuth 指示我们添加一个特殊的回调路由，因此替换旧的路由:

*config/routes.rb*

```
[...]
get '/oauth/callback', to: 'sessions#create'
[...] 
```

随着

*config/routes.rb*

```
[...]
get '/auth/:provider/callback', to: 'sessions#create'
[...] 
```

修改配置文件:

*config/local_env.yml*

```
[...]
oauth_redirect_uri: 'http%3A%2F%2Flocalhost%3A3001%2Fauth%2Fkeepa%2Fcallback' 
```

另外，不要忘记打开您的服务器应用程序，导航到[localhost:3000/oauth/applications](http://localhost:3000/oauth/applications)，并为您的应用程序编辑重定向 URI。

更改客户端应用程序的主页:

*views/pages/index . html . erb*

```
[...]
<% if session[:access_token] %>
  <%= link_to 'Get User', user_path %>
  <%= link_to 'Update User', update_user_path %>
<% else %>
  <%= link_to 'Authorize via Keepa', '/auth/keepa' %>
<% end %> 
```

下一步是改变回调操作:

*会话 _ 控制器. rb*

```
[...]
def create
  set_oauth_info_from request.env['omniauth.auth']['credentials']
  redirect_to root_path
end
[...] 
```

`request.env['omniauth.auth']`你应该很熟悉。它包含以下格式的身份验证哈希:

```
{"provider"=>:keepa,
 "uid"=>1,
 "info"=>{"email"=>"test@example.org"},
 "credentials"=>
  {"token"=>"8d8...",
   "refresh_token"=>"321...",
   "expires_at"=>1453301961,
   "expires"=>true},
 "extra"=>{}} 
```

请注意，该字段被称为`token`(不是`access_token`)，而不是`expires_in`，我们用的是`expires_at`。

因此,`set_oauth_info_from`方法应该有所改变:

*application _ controller . Rb*

```
[...]
def set_oauth_info_from(response)
  session[:access_token] = response['access_token'] || response['token']
  session[:refresh_token] = response['refresh_token']
  if response['expires_in']
    session[:expires_at] = response['expires_in'].seconds.from_now
  else
    session[:expires_at] = Time.at response['expires_at']
  end
end
[...] 
```

可以完全删除`new_oauth_token_path` helper 方法，您就可以通过新的闪亮策略进行身份验证了。太好了！

## 进一步定制

你可能想知道是否有可能修改看门人的观点和路线。是的，这是完全可能的。

要改变[视图](https://github.com/doorkeeper-gem/doorkeeper/wiki/Customizing-views)，运行这个简单的命令:

```
$ rails generate doorkeeper:views 
```

它将生成看门人使用的所有视图和布局。

通过调整 **application.rb** 文件中的这些设置，您可以自由使用自己的布局:

```
config.to_prepare do
  Doorkeeper::ApplicationsController.layout "my_layout"

  Doorkeeper::AuthorizationsController.layout "my_layout"

  Doorkeeper::AuthorizedApplicationsController.layout "my_layout"
end 
```

您的应用程序中的路线和助手方法可以在看门人的视图中使用，但是它们必须以`main_app`(例如，`main_app.login_path`)为前缀，因为这个解决方案是一个独立的引擎。

调整默认路由也很简单。`use_doorkeeper`方法接受一个具有所有必要配置的块。例如，您可以使用自己的控制器管理 OAuth 2 应用程序:

```
use_doorkeeper do
  controllers :applications => 'custom_applications'
end 
```

`controllers`接受`:authorizations`、`:tokens`、`:applications`和`:authorized_applications`的值。

你甚至可以跳过一些控制器，说:

```
use_doorkeeper do
  skip_controllers :applications
end 
```

`skip_controllers`接受相同的值。

更多信息请参考[本维基页面](https://github.com/doorkeeper-gem/doorkeeper/wiki/Customizing-routes)。

## 结论

在本文中，我们最终确定了我们的定制 OAuth 2 提供程序，对它进行了一些保护，引入了刷新令牌，并制定了一个定制 OmniAuth 策略。现在，您可以根据需要进一步定制和增强您的应用程序了！我真的鼓励你浏览[看门人维基](https://github.com/doorkeeper-gem/doorkeeper/wiki)，因为它提供了一些关于如何解决常见问题的很好的教程。

我希望这一系列文章对您有用并且有趣。如果你想让我谈一个特定的话题，请不要犹豫，我一直期待着你的反馈。快乐编码并保持认证！

## 分享这篇文章