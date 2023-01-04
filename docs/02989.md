# 看门人和 OAuth 2.0 入门

> 原文：<https://www.sitepoint.com/getting-started-with-doorkeeper-and-oauth-2-0/>

![Bellboy thin line icon for web and mobile minimalistic flat design. Vector white icon inside the red circle](img/eb9528092d154012457b7674a742dad0.png)

在我之前的系列文章中，我展示了如何使用 Rails 引擎 T2 oPRO T3 建立一个定制的 OAuth 2 提供者。今天我们将解决同样的问题，但这次使用另一个更受欢迎的工具——[看门人宝石](https://github.com/doorkeeper-gem/doorkeeper),由 Applicake 于 2011 年创建。从那以后，这个项目有了很大的发展，现在呈现了一个成熟而方便的解决方案。看门人可以和基本的 Rails 应用一起使用，也可以和 [Grape](https://github.com/doorkeeper-gem/doorkeeper/wiki/Grape-Integration) 一起使用。

在本文中，我将向您展示如何在看门人的帮助下构建自己的 OAuth 2 提供者和安全 API。我们会做基本的准备，整合看门人，稍微定制一下，引入*范围*。在本系列的第二部分中，我们将讨论更高级的东西，比如定制视图、使用刷新令牌、制作定制 OmniAuth 提供程序以及保护看门人的默认路由。

客户端和服务器应用的源代码可以在 [GitHub](https://github.com/bodrovis/Sitepoint-source/tree/master/Auth_Provider_with_Doorkeeper) 上找到。

## 创建应用程序

在这个演示中，我将使用 Rails 4.2。我们将创建两个应用程序:实际的提供者(姑且称之为“服务器”)和一个用于测试目的的应用程序(“客户机”)。从服务器开始:

```
$ rails new Keepa -T 
```

我们将需要这个应用程序的某种身份验证，但看门人没有规定使用哪一种。最近我已经介绍了[很多选项](https://www.sitepoint.com/series/authentication-in-rails/)，但是，今天，让我们使用 [bcrypt](https://github.com/codahale/bcrypt-ruby) 来编写我们自己的简单解决方案。

*Gemfile*

```
[...]
gem 'bcrypt-ruby'
[...] 
```

安装 gem，生成并应用新的迁移:

```
$ bundle install
$ rails g model User email:string:index password_digest:string
$ rake db:migrate 
```

现在给`User`模型配备 bcrypt 的功能和验证逻辑:

*型号/用户. rb*

```
[...]
has_secure_password
validates :email, presence: true
[...] 
```

为注册创建新的控制器:

*users_controller.rb*

```
class UsersController < ApplicationController
  def new
    @user = User.new
  end

  def create
    @user = User.new(user_params)
    if @user.save
      session[:user_id] = @user.id
      flash[:success] = "Welcome!"
      redirect_to root_path
    else
      render :new
    end
  end

  private

  def user_params
    params.require(:user).permit(:email, :password, :password_confirmation)
  end
end 
```

下面是相应的视图:

*views/users/new.html.erb*

```
<h1>Register</h1>

<%= form_for @user do |f| %>
  <%= render 'shared/errors', object: @user %>
  <div>
    <%= f.label :email %>
    <%= f.email_field :email %>
  </div>

  <div>
    <%= f.label :password %>
    <%= f.password_field :password %>
  </div>

  <div>
    <%= f.label :password_confirmation %>
    <%= f.password_field :password_confirmation %>
  </div>

  <%= f.submit %>
<% end %>

<%= link_to 'Log In', new_session_path %> 
```

*views/shared/_ errors . html . erb*

```
<% if object.errors.any? %>
  <div>
    <h5>Some errors were found:</h5>

    <ul>
      <% object.errors.full_messages.each do |msg| %>
        <li><%= msg %></li>
      <% end %>
    </ul>
  </div>
<% end %> 
```

添加一些路线:

*config/routes.rb*

```
[...]
resources :users, only: [:new, :create]
[...] 
```

为了检查用户是否登录，我们将使用一个好的、古老的`current_user`方法:

*application _ controller . Rb*

```
[...]
private

def current_user
  @current_user ||= User.find(session[:user_id]) if session[:user_id]
end

helper_method :current_user
[...] 
```

还需要一个单独的控制器来管理用户会话:

*会话 _ 控制器. rb*

```
class SessionsController < ApplicationController
  def new
  end

  def create
    @user = User.find_by(email: params[:email])
    if @user && @user.authenticate(params[:password])
      session[:user_id] = @user.id
      flash[:success] = "Welcome back!"
      redirect_to root_path
    else
      flash[:warning] = "You have entered incorrect email and/or password."
      render :new
    end
  end

  def destroy
    session.delete(:user_id)
    redirect_to root_path
  end
end 
```

`authenticate`是 bcrypt 提供的一种方法，用于检查是否输入了正确的密码。

以下是视图和路线:

*views/sessions/new . html . erb*

```
<h1>Log In</h1>

<%= form_tag sessions_path, method: :post do %>
  <div>
    <%= label_tag :email %>
    <%= email_field_tag :email %>
  </div>

  <div>
    <%= label_tag :password %>
    <%= password_field_tag :password %>
  </div>

  <%= submit_tag 'Log In!' %>
<% end %>

<%= link_to 'Register', new_user_path %> 
```

*config/routes.rb*

```
[...]
resources :sessions, only: [:new, :create]
delete '/logout', to: 'sessions#destroy', as: :logout
[...] 
```

最后，添加一个静态页面控制器、根页面和路由:

*pages_controller.rb*

```
class PagesController < ApplicationController
  def index
  end
end 
```

*views/pages/index . html . erb*

```
<% if current_user %>
  You are logged in as <%= current_user.email %><br>
  <%= link_to 'Log Out', logout_path, method: :delete %>
<% else %>
  <%= link_to 'Log In', new_session_path %><br>
  <%= link_to 'Register', new_user_path %>
<% end %> 
```

*config/routes.rb*

```
[...]
root to: 'pages#index'
[...] 
```

到目前为止，一切都应该是熟悉的。服务器应用程序现在已经准备好了，我们可以开始集成看门人。

## 集成看门人

将新宝石添加到*宝石文件*中:

*Gemfile*

```
[...]
gem 'doorkeeper'
[...] 
```

安装它并运行看门人的发电机:

```
$ bundle install
$ rails generate doorkeeper:install 
```

这个生成器将创建一个新的初始化文件，并将`use_doorkeeper`行添加到 *routes.rb* 中。这一行提供了一些看门人的路由(注册一个新的 OAuth 2，请求访问令牌，等等。)这个我们以后再讨论。

下一步是生成迁移。默认情况下，Doorkeeper 使用 ActiveRecord，但是您可以使用 [doorkeeper-mongodb](https://github.com/doorkeeper-gem/doorkeeper-mongodb) 来获得 Mongo 支持。

```
$ rails generate doorkeeper:migration 
```

您可以添加一个外键，如这里的[所述](https://github.com/doorkeeper-gem/doorkeeper#active-record)，但是我将直接应用迁移:

```
$ rake db:migrate 
```

打开看门人的初始化文件，找到第`resource_owner_authenticator do`行。默认情况下，它会引发一个异常，因此请将块的内容替换为:

*config/initializer/door keeper . Rb*

```
[...]
User.find_by_id(session[:user_id]) || redirect_to(new_session_url)
[...] 
```

`User`模型现在被绑定到看门人。你可以启动服务器，注册并导航到[localhost:3000/oauth/applications](http://127.0.0.1:3000/oauth/applications)。这是创建新 OAuth 2 应用程序的页面。创建一个，同时提供“http://localhost:3001/oauth/callback”作为重定向 URL。请注意，我们将端口 3001 用于客户端应用程序(尚不存在)，因为端口 3000 已经被服务器应用程序使用。完成后，您将看到您的应用程序 Id 和密钥。暂时保持此页面打开，或者记下这些值。

下一步是创建客户端应用程序。

## 创建客户端应用程序

运行 Rails generator 创建一个应用程序:

```
$ rails new KeepaClient -T 
```

添加静态页面控制器、根页面和路由:

*pages_controller.rb*

```
class PagesController < ApplicationController
  def index
  end
end 
```

*views/pages/index . html . erb*

```
<h1>Welcome!</h1> 
```

*config/routes.rb*

```
[...]
root to: 'pages#index'
[...] 
```

现在，让我们创建一个 *local_env.yml* 文件来存储一些配置，特别是在前面的步骤中从服务器应用程序接收到的客户端 Id 和密码:

*config/local_env.yml*

```
server_base_url: 'http://localhost:3000'
oauth_token: <CLIENT ID>' 
oauth_secret: '<SECRET>'
oauth_redirect_uri: 'http%3A%2F%2Flocalhost%3A3001%2Foauth%2Fcallback' 
```

将它装入`ENV`:

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

排除*。版本控制中的 yml* 文件，如果需要:

*。gitignore〔t1〕*

```
[...]
config/local_env.yml
[...] 
```

### 获取访问令牌

好了，我们已经准备好获取将用于执行 API 请求的访问令牌。为此，你可以使用 [oauth2](https://github.com/intridea/oauth2) 宝石，如这里的[所述](https://github.com/doorkeeper-gem/doorkeeper/wiki/Testing-your-provider-with-OAuth2-gem)。然而，我将再次坚持使用更低级的解决方案，以便您理解整个过程是如何发生的。

我们将利用 [rest-client](https://github.com/rest-client/rest-client) 来发送请求。

添加新宝石并`bundle install`它:

*Gemfile*

```
[...]
gem 'rest-client'
[...] 
```

要获得访问令牌，用户首先需要访问“localhost:3000/oauth/authorize”URL，同时提供客户端 Id、重定向 URI 和响应类型。让我们引入一个助手方法来生成适当的 URL:

*application_helper.rb*

```
[...]
def new_oauth_token_path
  "#{ENV['server_base_url']}/oauth/authorize?client_id=#{ENV['oauth_token']}&redirect_uri=#{ENV['oauth_redirect_uri']}&response_type=code"
end
[...] 
```

将其显示在客户端应用程序的主页面上:

*views/pages/index . html . erb*

```
[...]
<%= link_to 'Authorize via Keepa', new_oauth_token_path %>
[...] 
```

现在，介绍回拨路线:

*config/routes.rb*

```
[...]
get '/oauth/callback', to: 'sessions#create'
[...] 
```

*会话 _ 控制器. rb*

```
class SessionsController < ApplicationController
  def create
    req_params = "client_id=#{ENV['oauth_token']}&client_secret=#{ENV['oauth_secret']}&code=#{params[:code]}&grant_type=authorization_code&redirect_uri=#{ENV['oauth_redirect_uri']}"
    response = JSON.parse RestClient.post("#{ENV['server_base_url']}/oauth/token", req_params)
    session[:access_token] = response['access_token']
    redirect_to root_path
  end
end 
```

在访问“localhost:3000/oauth/authorize”之后，用户将被重定向到设置了`code`参数的回调 URL。在`create`动作中，生成适当的 params 字符串(带有`client_id`、`client_secret`、`code`、`grant_type`和`redirect_uri`)，然后对“localhost:3000/oauth/token”URL 执行 POST 请求。如果一切正常，响应将包含带有访问令牌的 JSON 及其生命周期(默认设置为 2 小时)。否则，将返回 401 错误。

然后，我们解析响应并将访问令牌存储在用户的会话中。当然，你需要为一个真正的应用程序引入某种认证，但是我在这里保持事情简单。

如果你看过我关于 oPRO 的文章，这个过程应该很熟悉。如果没有，恭喜您，您已经编写了客户端应用程序，用户现在可以获得访问令牌了。

这一切都很棒，但是我们能用这些代币做什么呢？显然，为了保护我们的 API，现在让我们来介绍一下吧！

## 介绍一个简单的 API

返回 server 应用程序并创建一个新的控制器:

*控制器/api/users_controller.rb*

```
class Api::UsersController < ApplicationController
  before_action :doorkeeper_authorize!

  def show
    render json: current_resource_owner.as_json
  end

  private

  def current_resource_owner
    User.find(doorkeeper_token.resource_owner_id) if doorkeeper_token
  end
end 
```

让我们一步一步来。

`before_action :doorkeeper_authorize!`允许我们通过阻止未经授权的请求来保护控制者的行为。这意味着用户必须提供访问令牌才能执行操作。如果没有提供令牌，看门人将阻止他们的方式，说:“你不得通过！”就像甘道夫一样。实际上，在我们的例子中，将会返回一个 401 错误，但是你已经明白了。

`current_resource_owner`是一个返回已发送的访问令牌的所有者的方法。`doorkeeper_token.resource_owner_id`返回执行请求的用户的 id(因为，正如您所记得的，我们在看门人初始化器中相应地调整了`resource_owner_authenticator`)。

`as_json`将`User`对象转换成 JSON。您可以提供一个`except`选项来排除一些字段，例如:

```
current_resource_owner.as_json(except: :password_digest) 
```

添加新路线:

*config/routes.rb*

```
namespace :api do
  get 'user', to: 'users#show'
end 
```

现在，如果你尝试访问“localhost:3000/api/user”，你会看到一个空页面。打开控制台，您会注意到显示了一个 401 错误，这意味着该操作现在受到保护。

如果你想定制这个未授权的页面，你可以在`ApplicationController`中重新定义`doorkeeper_unauthorized_render_options`方法。例如，添加如下错误消息:

*application _ controller . Rb*

```
def doorkeeper_unauthorized_render_options(error: nil)
  { json: { error: "Not authorized" } }
end 
```

调整客户端应用程序的主页:

*views/pages/index . html . erb*

```
[...]
<% if session[:access_token] %>
  <%= link_to 'Get User', "http://localhost:3000/api/user?access_token=#{session[:access_token]}" %>
<% else %>
  <%= link_to 'Authorize via Keepa', new_oauth_token_path %>
<% end %> 
```

现在授权，点击“获取用户”链接，观察结果-你应该看到你的用户的详细资料！

## 使用范围

范围是确定客户端能够执行哪些操作的方法。让我们介绍两种新的示波器:

*   `public`–允许客户端获取用户信息
*   `write`–允许客户端更改用户的个人资料

首先，更改看门人的初始化文件，以包含这些新的范围:

*config/initializer/door keeper . Rb*

```
[...]
default_scopes :public
optional_scopes :write
[...] 
```

`default_scopes`表示如果在授权 URL 中没有指定任何内容，默认情况下请求哪些范围。

`optional_scopes`是可能被传递的所有其他作用域的列表。如果传递了某个未知的范围，将会引发一个错误。

更新 API 控制器:

*API/用户 _ 控制器. rb*

```
[...]
before_action -> { doorkeeper_authorize! :public }, only: :show
before_action -> { doorkeeper_authorize! :write }, only: :update

def show
  render json: current_resource_owner.as_json
end

def update
  render json: { result: current_resource_owner.touch(:updated_at) }
end
[...] 
```

我们将作用域的名称传递给`doorkeeper_authorize!`,说明执行一个动作需要哪个作用域。请注意，也可以传递多个作用域:

```
doorkeeper_authorize! :admin, :write 
```

这意味着，如果客户端具有`admin` **或** `write`权限，则可以执行操作。如果您想要要求**两个**权限都存在，使用下面的构造:

```
doorkeeper_authorize! :admin
doorkeeper_authorize! :write 
```

介绍一条新路线:

*config/routes.rb*

```
[...]
namespace :api do
  get 'user', to: 'users#show'
  get 'user/update', to: 'users#update'
end
[...] 
```

为了使事情尽可能简单，我使用 GET 动词，但对于真正的应用程序，使用 PATCH 会更好。现在，在客户端应用程序中，让我们修改 helper 方法来请求`public`和`write`权限:

*application_helper.rb*

```
[...]
def new_oauth_token_path
  "#{ENV['server_base_url']}/oauth/authorize?client_id=#{ENV['oauth_token']}&redirect_uri=#{ENV['oauth_redirect_uri']}&response_type=code&scope=public+write"
end
[...] 
```

我们在这里添加了`scope`参数。多个作用域应该用`+`符号分隔。

最后，访问“http://localhost:3000/oauth/applications”，打开您的应用程序进行编辑，并在“Scopes”字段中填入`public write`值(用空格分隔)。

重新加载服务器并访问授权 URL。您会注意到，现在一个用户被请求执行两个操作的权限，但是它们的名称没有多大帮助。比如“写”是什么意思？对你来说可能是显而易见的，但对我们的用户来说不是。因此，最好能提供更多关于每个作用域的信息。这可以通过编辑为您生成的看门人的 I18n 文件来完成:

*config/locales/door keeper . en . yml*

```
en:
  doorkeeper:
    scopes:
      public: 'Access your public data.'
      write: 'Make changes to your profile.'
[...] 
```

现在，用户将看到每个范围的详细描述。

最后，向主页添加一个新链接:

*views/pages/index . html . erb*

```
[...]
<% if session[:access_token] %>
  <%= link_to 'Get User', "http://localhost:3000/api/user?access_token=#{session[:access_token]}" %>
  <%= link_to 'Update User', "http://localhost:3000/api/user/update?access_token=#{session[:access_token]}" %>
<% else %>
  <%= link_to 'Authorize via Keepa', new_oauth_token_path %>
<% end %> 
```

去试试这个吧！

## 结论

在本文中，我们为我们的应用程序和集成看门人奠定了基础。目前，用户可以注册他们的应用程序，请求访问令牌，并使用范围。API 现在是安全的，并且防止了未授权的访问。

在下一篇文章中，我们将介绍刷新令牌、保护看门人的一般路线、定制视图，并为 OmniAuth 制作一个可以打包成宝石的定制提供程序。

一如既往，不要犹豫，张贴您的问题，并很快看到你！

## 分享这篇文章