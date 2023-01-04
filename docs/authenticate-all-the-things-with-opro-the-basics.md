# 用 oPRO 认证所有的东西，基本的

> 原文：<https://www.sitepoint.com/authenticate-all-the-things-with-opro-the-basics/>

![o2](img/8cbf15074210bae4df7dcef76af6a628.png)

*这篇文章由[汤姆·帕金](https://www.sitepoint.com/author/tparkin)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

在过去的几个月里，我写了一堆主题，涵盖了从低级(AuthLogic)到成熟(Devise)的各种[认证解决方案](https://www.sitepoint.com/series/authentication-in-rails/)。今天我们将再次讨论 [OAuth 2](https://tools.ietf.org/html/draft-ietf-oauth-v2-22) ，不过这一次我将向您展示如何构建您自己的认证提供者并根据需要进行定制。

今天的嘉宾是理查德·施尼曼创作的[oPRO](https://github.com/opro/opro)(OAuth**Pro**vider 的简称)。这是一个 [Rails 引擎](http://guides.rubyonrails.org/engines.html)，它允许您快速、轻松地构建自己的身份验证提供者。oPRO 附带了一系列预定义的设置、控制器、视图和模型。这些项目中的大多数都可以进一步扩展或修改。

如果出于某种原因，您从未使用过 OAuth 2 协议，那么您可以阅读这篇介绍性教程，或者参考我关于使用 OAuth 2 和 Rails 的文章[。简而言之，这是一个允许一些第三方应用程序代表用户获得对服务的有限访问的协议。它最酷的一点是，用户可以控制应用程序可以执行的操作，而无需访问用户的密码。](https://www.sitepoint.com/rails-authentication-oauth-2-0-omniauth/)

oPRO 对您的设置只做了两个假设:

*   你有某种用于认证的用户模型
*   您正在使用 ActiveRecord

要查看它的运行情况并阅读一些文档，请查看这个示例应用程序。

本文将由三部分组成。我们将涵盖许多不同的内容:

*   准备身份验证提供者(姑且称之为“服务器应用程序”)
*   准备演示客户端应用程序(“客户端应用程序”)
*   在 server 应用程序中设置基本鉴定
*   集成 oPRO
*   提供基本的 OAuth 工作流
*   自定义默认视图和控制器
*   编写简单的 API 适配器
*   将自定义数据添加到身份验证哈希中
*   使用范围
*   添加刷新令牌的功能
*   限速
*   为 server 应用程序引入自定义身份验证解决方案
*   用用户凭证交换访问令牌

这似乎有很多工作要做，但我会在路上指引你，所以不要害怕！

服务器和客户端应用程序的源代码可以在 GitHub 上找到[。](https://github.com/bodrovis/Sitepoint-source/tree/master/Auth_Provider_with_Opro)

## 正在准备服务器应用程序

深呼吸，创建一个新的 Rails 应用程序。这将是我们的身份认证提供商:

```
$ rails new OproServer -T 
```

对于这个演示，我使用的是 Rails 4.2，但是 oPRO 也兼容 Rails 3.1 和 3.2。

我们现在需要两颗宝石:

*Gemfile*

```
[...]
gem 'opro'
gem 'devise'
[...] 
```

oPRO 建议将作为默认认证解决方案，因此，我们将坚持使用它(不过，我会给你一些关于使用不同认证机制的说明)。

奔跑

```
$ bundle install 
```

现在让我们设置设计。我不打算提供深入的解释，但如果你想了解更多，参考[这篇文章](https://www.sitepoint.com/devise-authentication-in-depth/)。

运行设计生成器以提供一些基本配置并创建一个`User`模型:

```
$ rails generate devise:install
$ rails generate devise User 
```

根据需要调整*config/initializer/device . Rb*文件。此外，修改布局以显示 Devise 依赖的 flash 消息:

*视图/布局/应用程序. html.erb*

```
[...]
<% flash.each do |key, value| %>
  <div class="alert alert-<%= key %>">
    <%= value %>
  </div>
<% end %>
[...] 
```

对于这个演示，我不打算使用任何样式，因为我们有很多其他的事情要做。现在运行另一个生成器，为 oPRO 创建一个初始化文件和挂载路径:

```
$ rails g opro:install 
```

最后，应用所有迁移:

```
$ rake db:migrate 
```

### 注册新的客户端应用程序

默认情况下，oPRO 附带一个文档页面，所以启动您的服务器并导航到[localhost:3000/oauth _ docs](http://localhost:3000/oauth_docs)来访问它。如果它没有出现，请确保您没有跳过前面部分的任何内容。

准备好之后，创建一个静态页面控制器、一个根路由和一个视图:

*pages_controller.rb*

```
class PagesController < ApplicationController
end 
```

*config/routes.rb*

```
[...]
root to: 'pages#index'
[...] 
```

*views/pages/index . html . erb*

```
<h1>Welcome to my Auth provider!</h1>
<%= link_to 'Register a new client app', new_oauth_client_app_path %> 
```

这个链接指向一个页面，客户可以在那里注册他们的应用程序，就像他们在 Twitter 或脸书上收到密钥对一样。访问此页面时会要求您进行身份验证，因此请注册一个示例用户。在“创建 OAuth 客户端应用程序”页面上，输入您的应用程序的名称，然后单击创建(该名称可以在以后更改)。您将看到客户端 id 和密钥对，所以暂时不要关闭这个页面。

我们需要做的下一件事是创建一个客户端应用程序，所以继续下一步！

## 准备客户端应用程序

创建另一个名为`OproClient`的 Rails 应用程序:

```
$ rails new OproClient -T 
```

我们需要一个地方来存储在上一步中收到的客户端 ID 和密钥。当然，我们可能会把它直接放在代码中，但这不是很安全，所以我将坚持使用环境变量。在开发模式下，将从 *config/local_env.yml* 文件中加载值:

*config/local_env.yml*

```
opro_client_id: 'your_id'
opro_client_secret: 'your_secret'
opro_base_url: 'http://localhost:3000' 
```

为了方便起见，我还包括了服务器的基本网址。

现在调整 *application.rb* 文件以正确设置`ENV`:

*配置/应用. rb*

```
[...]
if Rails.env.development?
  config.before_configuration do
    env_file = File.join(Rails.root, 'config', 'local_env.yml')
    YAML.load(File.open(env_file)).each do |key, value|
      ENV[key.to_s] = value
    end if File.exists?(env_file)
  end
end
[...] 
```

现在，您可以从代码中访问`ENV['opro_client_id']`、`ENV['opro_client_secret']`和`ENV['opro_base_url']`。

不要忘记从版本控制中排除 *local_env.yml* :

*。gitignore〔t1〕*

```
[...]
config/local_env.yml 
```

添加静态页面控制器和欢迎页面:

*pages_controller.rb*

```
class PagesController < ApplicationController
end 
```

*config/routes.rb*

```
[...]
root to: 'pages#index'
[...] 
```

*views/pages/index . html . erb*

```
<h1>Welcome!</h1>
<%= link_to 'Authenticate via oPRO', "#{ENV['opro_base_url']}/oauth/new?client_id=#{ENV['opro_client_id']}&client_secret=#{ENV['opro_client_secret']}&redirect_uri=%2F%2Flocalhost%3A3001%2Foauth%2Fcallback" %> 
```

有几件事需要注意:

*   `/oauth/new`是默认的 oPRO 通过 OAuth 验证用户的路由。
*   您必须通过您的`client_id`和`client_secret`才能正确地进行认证(稍后我们将了解到这不是唯一的方法)。
*   您还必须指定一个`redirect_uri`——这是在认证发生后用户将被重定向到的地方。

因此，如您所见，这与其他 OAuth 2 提供者的做法非常相似。

不确定你，但对我来说，这个网址似乎有点太长，所以我想把它移到其他地方:

*application _ controller . Rb*

```
[...]
private

def new_opro_token_path
  "#{ENV['opro_base_url']}/oauth/new?client_id=#{ENV['opro_client_id']}&client_secret=#{ENV['opro_client_secret']}&redirect_uri=%2F%2Flocalhost%3A3001%2Foauth%2Fcallback"
end

helper_method :new_opro_token_path
[...] 
```

我将它放在控制器内部，因为稍后我们也需要从其他动作中调用它。

*views/pages/index . html . erb*

```
<h1>Welcome!</h1>
<%= link_to 'Authenticate via oPRO', new_opro_token_path %> 
```

现在我们需要设置一个回调 URL。

### 回拨 URL

首先，为你的客户端 app 提供一个新的路线:

*config/routes.rb*

```
[...]
get '/oauth/callback', to: 'sessions#create'
[...] 
```

现在我们需要一个带有`create`动作的`SessionsController`。当用户被重定向到这个动作时，oPRO 将发送一个`code`参数，所以 URL 看起来像`http://localhost:3001/?code=123`(我们使用端口 3001，因为 3000 已经被服务器占用了——别忘了！).然后，通过向`http://localhost:3000/oauth/token.json`发送一个 HTTP POST 请求以及客户机 ID 和秘密，这个代码被用来获得实际的访问令牌。

因此，我们需要一个库来执行 HTTP 请求。有多种解决方案可供选择，当然你可以自由选择你最喜欢的，但我将坚持使用 [rest-client](https://github.com/rest-client/rest-client) ，因为它简单而强大。

*Gemfile*

```
[...]
gem 'rest-client'
[...] 
```

奔跑

```
$ bundle install 
```

现在创建一个新的控制器:

*会话 _ 控制器. rb*

```
class SessionsController < ApplicationController
  def create
    response = JSON.parse RestClient.post("#{ENV['opro_base_url']}/oauth/token.json",
                    {
                        client_id: ENV['opro_client_id'],
                        client_secret: ENV['opro_client_secret'],
                        code: params[:code]
                    },
                    accept: :json)
    session[:access_token] = response['access_token']
    session[:refresh_token] = response['refresh_token']
    redirect_to root_path
  end
end 
```

我们使用`RestClient.post`向`http://localhost:3000/oauth/token.json`发送一个 POST 请求，并设置三个必需的参数。oPRO 用 JSON 响应，JSON 包含访问和刷新令牌以及一个`expires_in`字段，说明令牌多长时间后会失效(默认情况下，令牌根本不会过期，所以我们暂时把它放在一边)。

然后，我们使用通用的 json gem 来解析响应并在用户会话中存储令牌，将它们重定向回主页。

## 示例 API 请求

到目前为止一切顺利，但是我们需要通过执行一些请求来测试令牌是否真的在工作。幸运的是，oPRO 提供了一个示例路由`http://localhost:3000/oauth_tests/show_me_the_money.json`,只有当访问令牌有效时，它才会响应一条成功消息。

因此，创建一个新的控制器:

*api_tests_controller.rb*

```
class ApiTestsController < ApplicationController
  def index
    redirect_to root_path and return unless session[:access_token]
    @response = JSON.parse RestClient.get("#{ENV['opro_base_url']}/oauth_tests/show_me_the_money.json",
                                          params: {
                                              access_token: session[:access_token]
                                          },
                                          accept: :json)
  end
end 
```

如果没有设置访问令牌，只需将用户重定向回主页。否则，向示例路由发送 GET 请求，提供访问令牌作为唯一的参数，然后解析响应。

在相应的视图中显示 server 应用程序返回的消息:

*views/API _ tests/index . html . erb*

```
<%= @response['message'] %> 
```

不要忘记添加路线:

*config/routes.rb*

```
[...]
resources :api_tests, only: [:index]
[...] 
```

最后，修改主页视图:

*views/pages/index . html . erb*

```
<h1>Welcome!</h1>
<% if session[:access_token] %>
  <%= link_to 'Show some money', api_tests_path %>
<% else %>
  <%= link_to 'Authenticate via oPRO', new_opro_token_path %>
<% end %> 
```

现在启动您的服务器(不要忘记端口 3000 已被占用):

```
$ rails s -p 3001 
```

看看这一切是如何运作的。当点击“显示一些钱”链接时，你会看到一个“OAuth 工作！”信息–这意味着我们走在正确的道路上。

如果您收到 401 错误消息，这意味着您没有发送令牌或者令牌无效。仔细检查你的代码——在执行前面的步骤时，你可能遗漏了一些东西。

## 自定义控制器和视图

您可能想要定制视图或者排除 oPRO 提供的一些默认控制器(例如，带有 docs 的控制器)。这可以通过向您的 *config/routes.rb* 文件中的`mount_opro_oauth`方法传递参数散列来实现。此方法支持以下选项:

*   `except`–传递一个符号或一组符号以排除某些路线。可能的选项:
    *   `docs`–带导轨的控制器
    *   `tests`–具有测试 API 方法的控制器
    *   `client_apps`–管理客户端 API 应用程序的控制器
*   `controllers`–传递带有控制器名称和新路径的散列。可能的选项:
    *   `oauth_docs`–与`docs`相同
    *   `oauth_tests`–与`tests`相同
    *   `oauth_client_apps`–与`client_apps`相同
    *   `oauth_new`–控制器向用户请求许可。请注意，您只能重新定义`new`动作。`create`动作[仍将从默认控制器调用](https://github.com/opro/opro/blob/master/lib/opro/rails/routes.rb#L11)。

这个方法的完整实现可以在[这里](https://github.com/opro/opro/blob/master/lib/opro/rails/routes.rb#L5)找到。

所以首先，我们先排除文档路线。为此，只需写下:

*config/routes.rb*

```
[...]
mount_opro_oauth except: :docs
[...] 
```

我想做的是调整用户通过我们的应用程序进行身份验证时看到的页面。当然，要做到这一点，我们需要一个自定义控制器。

*config/routes.rb*

```
[...]
mount_opro_oauth controllers: {oauth_new: 'oauth/auth'}, except: :docs
[...] 
```

我们在`Oauth`下命名空间`AuthController`，所以在*控制器*目录下创建一个 *oauth* 文件夹，里面有一个 *auth_controller.rb* 文件:

*控制器/oauth/auth_controller.rb*

```
class Oauth::AuthController < Opro::Oauth::AuthController
end 
```

该自定义控制器继承自 oPRO 定义的`Opro::Oauth::AuthController`。我在这里没有对任何行动进行修改，因为我们只需要改变一条路线。不过，如果需要的话，完全可以覆盖`new`动作。

在 *views/oauth/new.html.erb* 下创建一个视图，并随意调整。例如，您可以为应用程序请求的访问权限添加一些说明:

*views/oauth/new.html.erb*

```
[...]

<h2>Explanation</h2>

<ul>
  <li><strong>Read</strong> - the app will be able to read information about your account.
  This permission is required.</li>
  <li><strong>Write</strong> - the app will be able to modify your account.</li>
</ul> 
```

可能包括一些品牌和联系信息在这里。您也可以使用相同的方法定制其他视图。

## 结论

因此，在这一部分，我们为下一步奠定了基础。目前，用户可以验证和执行对示例 API 操作的请求。然而，仍有许多工作要做:我们需要将用户信息存储在数据库中，引入更多的 API 方法，并重构代码。此外，除了令牌之外，获取一些关于用户的其他信息也是很棒的。

因此，在本文的下一部分，我们将关注所有这些问题。回头见！

## 分享这篇文章