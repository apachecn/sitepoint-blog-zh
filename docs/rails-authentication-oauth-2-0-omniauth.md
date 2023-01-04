# 使用 OAuth 2.0 和 OmniAuth 的 Rails 身份验证

> 原文：<https://www.sitepoint.com/rails-authentication-oauth-2-0-omniauth/>

![](img/799faca5757371e96e232fe61bee70f8.png)

这是 Rails 系列的[认证的第三篇文章。我们已经建立了一个经典的登录/密码认证系统，具有记住我、重置密码、确认电子邮件等功能。](https://www.sitepoint.com/series/authentication-in-rails/)

今天我们将讨论借助于 [OAuth 2 协议](http://oauth.net/2/)通过社交网络进行认证。我们将讨论 OmniAuth 及其四种策略:Twitter、脸书、Google+和 LinkedIn，允许用户登录他们喜欢的任何网络。我将指导你如何整合每个策略，设置它，并处理错误。

本文的源代码可以在 [GitHub](https://github.com/bodrovis/Sitepoint-source/tree/master/Authentication_with_OAuth2) 上找到。

在 sitepoint-oauth2.herokuapp.com 可以找到一个工作演示。

## OAuth 2 和 OmniAuth

[OAuth 2](https://tools.ietf.org/html/draft-ietf-oauth-v2-22) 是一种授权协议，使第三方应用程序能够获得对 HTTP 服务的有限访问。该协议的一个主要方面是颁发给应用程序的**访问令牌**。应用程序使用这个令牌代表用户执行各种操作。但是，它不能执行未经批准的事情(例如，用户可能只允许某个应用程序获取朋友的信息，但不能在用户的墙上发布)。

这很酷，因为在任何情况下，第三方应用程序都不应该访问用户的密码。它只获得一个特殊的令牌，该令牌在一段时间后过期(通常，用户也可以手动撤销访问权限)，并且只能用于执行一系列批准的任务，称为*范围*。

如何识别第三方 app？嗯，说到社交网络，一个应用必须先*注册*。开发者提供应用程序的 URL、名称、联系数据和其他对用户可见的信息。*授权提供商* (Twitter 或脸书)随后发布一个*密钥对*，为社交网络/提供商唯一标识该应用。

以下是当用户访问应用程序并试图通过社交网络进行身份验证时会发生什么的简单概述:

*   用户点击“登录”链接
*   用户被重定向到社交网络的网站。应用程序的数据(`client_id`)被一起发送用于识别
*   用户看到应用程序的细节(如名称，标志，描述等。)以及它想代表他执行哪些操作(范围)。想象一下，你走到用户面前说:“嘿，我叫杰克，我想得到你所有朋友的名单！如果你同意，我会给你看一些有趣的统计数据
*   如果用户不信任这个应用程序，他们只需取消授权
*   如果用户信任该应用，则授权被批准，并且用户被重定向回该应用(通过*回调 URL* )。关于经过身份验证的用户和生成的令牌(有时带有秘密密钥)的信息被一起发送。

有许多服务支持 OAuth 2 身份验证，因此为了标准化创建身份验证解决方案的过程，Intridea，Inc .创建了 OmniAuth 。它支持从 OAuth 2 到 LDAP 的任何内容。在本文中，我们将关注 [omniauth-oauth2](https://github.com/intridea/omniauth-oauth2) ，一个抽象的 oauth2 策略。基本上，它被用作一个构建模块，以方便[在其上设计](https://github.com/intridea/omniauth-oauth2#creating-an-oauth2-strategy)认证策略。[这里的](https://github.com/intridea/omniauth/wiki/List-of-Strategies)是 OmniAuth 可用的所有策略的庞大列表(也包括与 OAuth 2 无关的策略)。

拥有一个标准的策略方法是非常好的，因为你可以毫无问题地集成你想要的任何东西。尽管如此，你必须记住，一些社交网络可能会返回关于认证用户的不同数据，所以测试你的应用程序如何工作是绝对必要的(我将向你展示一些这方面的例子。)

如果您从未将 OAuth 2 集成到应用程序中，这可能看起来很复杂，但不要担心，很快就会变得非常清楚！通过例子来学习是很好的，所以让我们深入代码并创建一些有趣的东西。

## 准备演示应用程序

好了，创建一个简单的应用程序，允许用户通过其中一个社交网络进行身份验证。我把我的叫做 SocialFreak:

```
$ rails new SocialFreak -T
```

这个演示将使用 Rails 4，但是 Rails 3 应该也可以。

连接 Bootstrap 以设计应用程序(可选):

*Gemfile*

```
[...]
gem 'bootstrap-sass'
[...]
```

然后跑

```
$ bundle install
```

导入必要的文件:

*样式表/application.css.scss*

```
@import "bootstrap-sprockets";
@import "bootstrap";
@import 'bootstrap/theme';
```

修改布局:

*layouts/application . html . erb*

```
[...]
<nav class="navbar navbar-inverse">
  <div class="container">
    <div class="navbar-header">
      <%= link_to 'Social Freak', root_path, class: 'navbar-brand' %>
    </div>
    <div id="navbar">
      <ul class="nav navbar-nav">

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

这将设置主菜单(还没有项目)和一个区域来呈现 flash 消息。

创建根页面:

*config/routes.rb*

```
[...]
root to: 'pages#index'
[...]
```

*pages_controller.rb*

```
class PagesController < ApplicationController
  def index

  end
end
```

*视图/页面/html.erb*

```
<div class="jumbotron">
  <h1>Welcome!</h1>
  <p>Authenticate via one of the social networks to get started.</p>
</div>
```

## 通过 Twitter 认证

### 添加策略

让我们用 Arun Agrawal 创造的 [omniauth-twitter](https://github.com/arunagw/omniauth-twitter) gem。这是 OmniAuth 的众多策略之一。将其放入 gem 文件中:

*Gemfile*

```
[...]
gem 'omniauth-twitter'
[...]
```

然后跑

```
$ bundle install
```

所有 OmniAuth 策略的配置存在于 **omniauth.rb** 初始化文件中，所以现在创建它:

*config/initializer/omni auth . Rb*

```
Rails.application.config.middleware.use OmniAuth::Builder do
  provider :twitter, ENV['TWITTER_KEY'], ENV['TWITTER_SECRET']
end
```

注册新策略(提供者)就是向提供者提供一个密钥对来识别应用。当然，我们需要获得这两个密钥，所以请执行以下操作:

*   导航到[apps.twitter.com](https://apps.twitter.com)
*   点击“创建新应用”
*   填写表格。对于回拨 URL，提供您站点的地址加上“/auth/twitter/callback”。如果您在本地机器上，请提供“http://localhost:3000/auth/Twitter/callback”。我们将很快讨论这个回调 URL。
*   点击“创建”。
*   你将被重定向到 Twitter 上的应用信息页面。导航到“密钥和访问令牌”选项卡。
*   复制**消费者密钥**和**消费者秘密**并将它们粘贴到初始化文件中。
*   您也可以浏览其他选项卡，但对于本演示，我们不会更改任何其他内容。例如，在“权限”中，您可以设置用户通过身份验证后应用程序能够执行的操作。例如，如果你需要能够代表用户发布推文，你必须相应地改变权限。这控制了上述授权的“范围”。

请注意，您可以按照此处的[所述进一步设置该策略。对于这个演示，默认值就可以了。](https://github.com/arunagw/omniauth-twitter#authentication-options)

现在让我们回到回调 URL。这是在成功认证和批准授权后，用户将被重定向到应用程序内部的 URL(该请求还将包含用户的数据和令牌)。所有 OmniAuth 策略都期望回调 URL 等于*<your_app_url>/auth/:provider/callback "</your_app_url>*。`:provider`取策略的名字(“twitter”、“facebook”、“linkedin”等)。)如初始化器中所列。

有了这些知识，让我们相应地设置路线:

*config/routes.rb*

```
[...]
get '/auth/:provider/callback', to: 'sessions#create'
[...]
```

并将第一个链接添加到我们的主菜单:

*layouts/application . html . erb*

```
[...]
<ul class="nav navbar-nav">
  <li><%= link_to 'Twitter', '/auth/twitter' %></li>
</ul>
[...]
```

注意，`/auth/twitter`路由是由策略提供的，并重定向到 Twitter 登录页面。

## 认证散列

`SessionsController`中的`create`方法将解析用户数据，将其保存到数据库中，并执行应用程序的登录。然而，用户数据是什么样的呢？这很容易检查:

*会话 _ 控制器. rb*

```
class SessionsController < ApplicationController
  def create
    render text: request.env['omniauth.auth'].to_yaml
  end
end
```

`request.env['omniauth.auth']`包含带有用户所有数据的认证散列。

重新加载服务器，并尝试通过 Twitter 认证。你会看到类似于[这个](https://github.com/arunagw/omniauth-twitter#authentication-hash)的东西。请注意，除了您的姓名、位置、头像和其他基本信息之外，还有一些特殊数据，如关注者和 tweets 计数。当然，这些数据会因提供商而异(默认情况下，一些社交网络只发送基本信息)。

## 保存用户数据

既然您已经知道了身份验证哈希是什么样子，以及可以获取哪些数据，那么是时候决定存储哪些信息了。因为我们想要创建多提供商认证，所以保存 tweets count 之类的东西并不是最优先考虑的事情(但是，您可以只通过[序列化](http://apidock.com/rails/ActiveRecord/Base/serialize/class)并将用户认证散列的“额外”部分保存在一个单独的字段中)。

对于本演示，我们存储以下内容:

*   提供商的名称。这是绝对必要的，因为我们将有多个供应商。
*   用户的唯一标识符。该标识符由社交网络生成，并且可以包含各种符号。一些网络仅使用数字，而其他网络也使用字母。提供商名称和 uID 的组合将在我们的应用程序中唯一识别用户。
*   用户的全名。一些网络也分别提供名字和姓氏(还有一些，像 Twitter，也分开名字和昵称)，但是我们不需要这么复杂。
*   用户的位置。并不是所有的网络都提供这种功能，但是让我们研究一下哪些网络提供这种功能，以及以何种格式提供。
*   用户的头像 URL。我们将在主菜单中显示用户的头像，所以它必须非常小。幸运的是，大多数社交网络允许你从几个可用的尺寸中选择一个。使用 Twitter 的`image_size` [选项](https://github.com/arunagw/omniauth-twitter#authentication-options)来控制它(默认为 48x48px，非常适合我们)。另外，请注意，默认情况下，头像 URL 使用`http`作为协议。如果你想让你页面上的所有东西都使用`https`，许多社交网络都允许这样做。例如，使用 Twitter，你必须将`secure_image_url`设置为`true`。
*   用户的配置文件 URL。每个社交网络都提供这一点。然而，在许多情况下，`urls`键有一个嵌套散列，如下所示:

 `:urls => {
:Website => 'http://example.com',
:Twitter => "https://twitter.com/xxx"
}` 

我们不会存储用户的令牌，因为我们实际上不会代表他们执行任何操作。如果你需要这样做，用`string`数据类型创建`token`和`secret`字段(一些社交网络也提供 secret)。在向服务发送 API 请求时使用它们。

好了，现在我们可以生成适当的迁移:

```
$ rails g model User provider:string uid:string name:string location:string image_url:string url:string
```

打开迁移文件，并按如下方式修改它:

*xxx_create_users.rb*

```
[...]
t.string :provider, null: false
t.string :uid, null: false
add_index :users, :provider
add_index :users, :uid
add_index :users, [:provider, :uid], unique: true
[...]
```

`provider`和`uid`不能为空，应编入索引。`add_index :users, [:provider, :uid], unique: true`在这两个字段上添加聚集索引，并确保它们的组合是唯一的。

应用迁移:

```
$ rake db:migrate
```

现在重写`create`动作

*会话 _ 控制器. rb*

```
[...]
def create
  begin
    @user = User.from_omniauth(request.env['omniauth.auth'])
    session[:user_id] = @user.id
    flash[:success] = "Welcome, #{@user.name}!"
  rescue
    flash[:warning] = "There was an error while trying to authenticate you..."
  end
  redirect_to root_path
end
[...]
```

`from_omniauth`是一个尚不存在的方法，它将解析认证散列并返回用户记录。接下来，只需将用户 id 保存在会话中，并重定向到主页。

是时候关心一下`from_omniauth`类的方法了:

*user.rb*

```
[...]
class << self
  def from_omniauth(auth_hash)
    user = find_or_create_by(uid: auth_hash['uid'], provider: auth_hash['provider'])
    user.name = auth_hash['info']['name']
    user.location = auth_hash['info']['location']
    user.image_url = auth_hash['info']['image']
    user.url = auth_hash['info']['urls']['Twitter']
    user.save!
    user
  end
end
[...]
```

`find_or_create_by`确保我们不会多次创建同一个用户。该方法存储所有需要的数据，保存用户并返回它。如果你有兴趣，用户的令牌可以正常访问为`auth_hash['credentials']['token']` ( `auth_hash['credentials']['secret']`为秘密)。

## 当前用户和注销

我们需要一种方法来确定用户是否登录。`current_user`是一个方法，按照惯例，要么返回用户记录，要么返回`nil`:

*application _ controller . Rb*

```
[...]
private

def current_user
  @current_user ||= User.find_by(id: session[:user_id])
end

helper_method :current_user
[...]
```

确保它也可以从视图中被调用。

太好了，现在用户可以登录，但不能注销。添加注销链接:

*layouts/application . html . erb*

```
[...]
<nav class="navbar navbar-inverse">
  <div class="container">
    <div class="navbar-header">
      <%= link_to 'Social Freak', root_path, class: 'navbar-brand' %>
    </div>
    <div id="navbar">
      <% if current_user %>
        <ul class="nav navbar-nav pull-right">
          <li><%= image_tag current_user.image_url, alt: current_user.name %></li>
          <li><%= link_to 'Log Out', logout_path, method: :delete %></li>
        </ul>
      <% else %>
        <ul class="nav navbar-nav">
          <li><%= link_to 'Twitter', '/auth/twitter' %></li>
        </ul>
      <% end %>
    </div>
  </div>
</nav>
[...]
```

请注意，我还将用户的头像添加到了主菜单中。

相应的路线:

*config/routes.rb*

```
[...]
delete '/logout', to: 'sessions#destroy'
[...]
```

控制器动作:

*会话 _ 控制器. rb*

```
[...]
def destroy
  if current_user
    session.delete(:user_id)
    flash[:success] = 'See you!'
  end
  redirect_to root_path
end
[...]
```

最后，修改主页以显示当前登录用户的信息:

*views/pages/index . html . erb*

```
<% if current_user %>
  <div class="page-header">
    <h1>Here is some info about you...</h1>
  </div>
  <div class="panel panel-default">
    <div class="panel-body">
      <ul>
        <li><strong>Name:</strong> <%= current_user.name %></li>
        <li><strong>Provider:</strong> <%= current_user.provider %></li>
        <li><strong>uID:</strong> <%= current_user.uid %></li>
        <li><strong>Location:</strong> <%= current_user.location %></li>
        <li><strong>Avatar URL:</strong> <%= current_user.image_url %></li>
        <li><strong>URL:</strong> <%= current_user.url %></li>
      </ul>
    </div>
  </div>
<% else %>
  <%= render 'welcome' %>
<% end %>
```

我把欢迎词移到了单独的部分:

*views/pages/_ welcome . html . erb*

```
<div class="jumbotron">
  <h1>Welcome!</h1>
  <p>Authenticate via one of the social networks to get started.</p>
</div>
```

这需要涵盖很多内容，但现在我们已经建立了基础，添加新的提供商将变得非常容易(主要是)！

## 通过脸书认证

我们将使用 Mark Dodwell 的 omni auth-Facebook gem。

把它放到 gem 文件里

*Gemfile*

```
[...]
gem 'omniauth-facebook'
[...]
```

然后跑

```
$ bundle install
```

想法是一样的:我们必须设置新的提供者，并确保身份验证散列被正确解析。

*config/initializer/omni auth . Rb*

```
[...]
provider :facebook, ENV['FACEBOOK_KEY'], ENV['FACEBOOK_SECRET']
[...]
```

要得到脸书的钥匙和秘密:

*   导航到[developers.facebook.com](https://developers.facebook.com/)
*   点击“我的应用”菜单项下的“添加新应用”。
*   点击对话框中的“网站”。
*   单击“跳过并创建 ID”。
*   输入应用程序的名称并选择类别，然后单击“创建”。
*   您将被重定向到该应用程序的页面。点击“App Secret”旁边的“Show ”,输入您的密码以显示密钥。将这些键复制并粘贴到初始化文件中。

暂时不要离开这一页。你必须做一些设置来激活应用程序:

*   打开“设置”部分。
*   点击“添加平台”，选择“网站”。
*   填写“站点 URL”(本地机器为“http://localhost:3000”)和“应用程序域”(必须从站点 URL 或移动站点 URL 派生)。
*   填写“联系人电子邮件”(这是激活应用程序所必需的)，然后单击“保存更改”
*   导航到“状态和评论”部分，并设置“您是否要将此应用程序及其所有实时功能提供给公众？”切换到“是”。

这是最基本的设置——你可以用你的应用程序做更多的事情，所以也可以随意访问其他部分。

对于脸书提供商，我们还必须在 Rails 应用程序中进行一些设置:

*config/initializer/omni auth . Rb*

```
[...]
provider :facebook, ENV['FACEBOOK_KEY'], ENV['FACEBOOK_SECRET'],
  scope: 'public_profile', info_fields: 'id,name,link'
[...]
```

`scope`是授权后我们向用户请求的权限列表。`public_profile`表示我们的应用程序只能使用基本信息，不能代表用户执行任何操作。用户可能会请求大量的权限，包括允许你访问敏感数据和代表用户执行某些操作的扩展权限。

`info_fields`是我们希望包含在认证散列中的字段列表。请注意，一些字段将始终存在。[这里的](https://developers.facebook.com/docs/graph-api/reference/user/)是其他可用字段的列表。

和 Twitter 一样，脸书支持各种头像大小(`image_size`，默认为适合我们的 50×50)，支持`https`访问头像(`secure_image_url`)。[这里的](https://github.com/mkdynamic/omniauth-facebook#configuring)是您可以使用的所有设置。

很好，现在给主菜单添加一个新链接:

*layouts/application . html . erb*

```
[...]
<li><%= link_to 'Facebook', '/auth/facebook' %></li>
[...]
```

是时候调整一下`from_omniauth`方法了(这里的是认证散列的一个例子)。实际上，只有一行需要修改:

*型号/用户. rb*

```
[...]
user.url = auth_hash['info']['urls'][user.provider.capitalize]
[...]
```

现在你可以重启服务器并检查它了！

## 通过 Google+认证

我们将采用乔希·埃利索普的宝石。

将其放入 gem 文件中:

*Gemfile*

```
[...]
gem "omniauth-google-oauth2"
[...]
```

然后跑

```
$ bundle install
```

你知道规则。将新的提供程序添加到初始值设定项文件中:

*config/initializer/omni auth . Rb*

```
[...]
provider :google_oauth2, ENV["GOOGLE_CLIENT_ID"], ENV["GOOGLE_SECRET"]
[...]
```

要获取 Google key 和 secret:

*   导航到[console.developers.google.com](https://console.developers.google.com)
*   单击“创建项目”并为其命名。
*   从右侧菜单打开“API”部分，并确保 Google+ API 已启用。
*   打开“同意屏幕”并填写“产品名称”(如果您愿意，还可以填写其他字段)。
*   打开“凭据”并单击“创建新的客户端 ID”。
*   选择“Web 应用程序”。
*   在“授权的 JavaScript 源”中输入应用程序的 URL(本地机器为“http://localhost:3000”)。
*   在“授权重定向 URIs”中输入您的应用程序的 URL 加上“/auth/google_oauth2/callback”。

复制客户端 ID 和客户端密码，并将其粘贴到初始化文件中。

现在我们的 Rails 应用程序中的一些设置:

*config/initializer/omni auth . Rb*

```
[...]
provider :google_oauth2, ENV["GOOGLE_CLIENT_ID"], ENV["GOOGLE_SECRET"],
  scope: 'profile', image_aspect_ratio: 'square', image_size: 48, access_type: 'online'
[...]
```

`scope: 'profile'`表示我们只想接收基本的用户信息。谷歌提供了一大堆 API，所以请访问[游乐场](https://developers.google.com/oauthplayground/)查看可用范围的完整列表。

`image_aspect_ratio: 'square'`表示我们希望得到用户的头像，宽度和高度相等(`original`为默认值)。

`image_size: 48`表示用户头像的宽度和高度将为 48px。

`access_type: 'online'`表示我们不想接收刷新令牌。这意味着当 Google 给我们的令牌过期时，我们将无法刷新它(使用这个特殊的刷新令牌),因此用户必须再次登录。当您希望代表用户执行一些定期操作而不要求持续登录时，脱机访问类型非常有用。即使用户的令牌过期，你的程序将能够自动刷新它。这有点超出了本帖的范围。

您可能想利用一些其他的[配置选项](https://github.com/zquestz/omniauth-google-oauth2#configuration)。

返回到`from_omniauth`方法，看看这一行:

*型号/用户. rb*

```
[...]
user.url = auth_hash['info']['urls'][user.provider.capitalize]
[...]
```

对于 Google+，提供者的名称将是“google_oauth2”，但是在`urls`嵌套散列中找不到匹配的键。大概是这样的:

```
:urls => {
  :Google => "https://plus.google.com/xxx"
}
```

我们可以在这里添加一个基本的条件语句，但是最好重命名我们的提供者，就像这样:

*config/initializer/omni auth . Rb*

```
[...]
provider :google_oauth2, ENV["GOOGLE_CLIENT_ID"], ENV["GOOGLE_SECRET"],
         scope: 'profile', image_aspect_ratio: 'square', image_size: 48, access_type: 'online', name: 'google'
[...]
```

别忘了把 Google 开发者控制台里的回调 URL 改成“ <your_app_url>/auth/google/callback”！</your_app_url>

现在向主菜单添加另一个链接:

*layouts/application . html . erb*

```
[...]
<li><%= link_to 'Google+', '/auth/google' %></li>
[...]
```

重新加载服务器并尝试认证！

## LinkedIn 认证

这将是今天的最后一个社交网络，并将使用 Decio Ferreira 的[omni auth-LinkedIn-oauth 2](https://github.com/decioferreira/omniauth-linkedin-oauth2)gem:

*Gemfile*

```
[...]
gem 'omniauth-linkedin-oauth2'
[...]
```

别忘了跑步

```
$ bundle install
```

注册新的提供商:

```
[...]
provider :linkedin, ENV['LINKEDIN_KEY'], ENV['LINKEDIN_SECRET']
[...]
```

要获取 LinkedIn 密钥和密码:

*   导航到[linkedin.com/secure/developer](https://www.linkedin.com/secure/developer)
*   单击“添加新应用程序”。
*   填写必填字段。这种形式比你在其他社交网络注册应用时看到的要大得多(也有一点错误)，所以要有耐心。
*   用格式为“ <your_app_url>/auth/linkedin/callback”的 URL 填充“OAuth 2.0 重定向 URL”。</your_app_url>
*   提交表单后，打开应用程序页面，将消费者密钥和消费者秘密复制到初始化文件中。

添加一些配置选项:

```
[...]
provider :linkedin, ENV['LINKEDIN_KEY'], ENV['LINKEDIN_SECRET'],
  scope: 'r_basicprofile',
  fields: ['id', 'first-name', 'last-name', 'location', 'picture-url', 'public-profile-url']
[...]
```

`scope: 'r_basicprofile'`表示我们只想获得基本的用户数据。

`fields`包含应该在认证散列中返回的所有字段的数组。默认字段列表可以在[这里](https://github.com/decioferreira/omniauth-linkedin-oauth2#profile-fields)找到。

向主菜单添加另一个链接:

*layouts/application . html . erb*

```
[...]
<li><%= link_to 'LinkedIn', '/auth/linkedin' %></li>
[...]
```

这就是事情开始变得稍微复杂的地方，因为 LinkedIn 的认证哈希与我们到目前为止看到的略有不同:

*   `location`是包含国家代码和名称的嵌套散列。
*   `urls`也是嵌套 hash，但是没有`LinkedIn`键。相反，有一个`public_profile`键，可能还有一堆其他键(存储个人网站之类的东西)。

这给解析方法带来了一些复杂性:

*型号/用户. rb*

```
class << self
  def from_omniauth(auth_hash)
    user = find_or_create_by(uid: auth_hash['uid'], provider: auth_hash['provider'])
    user.name = auth_hash['info']['name']
    user.location = get_social_location_for user.provider, auth_hash['info']['location']
    user.image_url = auth_hash['info']['image']
    user.url = get_social_url_for user.provider, auth_hash['info']['urls']
    user.save!
    user
  end

  private

  def get_social_location_for(provider, location_hash)
    case provider
      when 'linkedin'
        location_hash['name']
      else
        location_hash
    end
  end

  def get_social_url_for(provider, urls_hash)
    case provider
      when 'linkedin'
        urls_hash['public_profile']
      else
        urls_hash[provider.capitalize]
    end
  end
end
```

我使用了`location_hash['name']`来获取用户所在国家的名称，使用`urls_hash['public_profile']`来获取用户的个人资料 URL。当然，我们可能会使用一个`if`条件来代替，但是在将来的某个时候，你可能会添加更多的提供者，谁知道这会带来哪些不一致。请随意进一步重构这段代码。

不幸的是，LinkedIn 不允许指定 avatar 的尺寸(至少，我还没有找到方法——如果你知道，请与我分享:)，所以将这个简单的样式添加到样式表中:

*样式表/应用程序. scss*

```
[...]
nav {
  img {
    max-width: 48px;
  }
}
```

现在您可以重新加载您的服务器并通过 LinkedIn 登录了！

## 从错误中拯救

我们今天要讨论的最后一件事是从身份验证阶段发生的错误中恢复过来。您可能还记得，在`create`方法中，我们有:

*会话 _ 控制器. rb*

```
[...]
begin
  @user = User.from_omniauth(request.env['omniauth.auth'])
  session[:user_id] = @user.id
  flash[:success] = "Welcome, #{@user.name}!"
rescue
 flash[:warning] = "There was an error while trying to authenticate you..."
end
[...]
```

这确实挽救了任何在`from_omniauth`方法中出现的错误。然而，这并不能保护我们免受认证过程中发生的错误的影响。例如，如果您禁用 cookies 并试图通过某个社交网络进行身份验证，您将会得到一个`SessionExpired`错误。

挽救这样的错误应该在初始化文件中完成:

*config/initializer/omni auth . Rb*

```
[...]
OmniAuth.config.on_failure = Proc.new do |env|
  SessionsController.action(:auth_failure).call(env)
end
```

我们简单地在`SessionsController`中调用`auth_failure`动作。它可能看起来像这样:

*会话 _ 控制器. rb*

```
[...]
def auth_failure
  redirect_to root_path
end
[...]
```

这里有另一个(更丑陋的)可能的选择:

*config/initializer/omni auth . Rb*

```
[...]
OmniAuth.config.on_failure do |env|
  error_type = env['omniauth.error.type']
  new_path = "#{env['SCRIPT_NAME']}#{OmniAuth.config.path_prefix}/failure?message=#{error_type}"
  ,[301, {'Location' => new_path, 'Content-Type' => 'text/html'}, []]
end
```

这将执行重定向到" <app_url>/failure？消息=xxx”。不要忘记设置相应的路由和控制器动作来处理请求。</app_url>

## 结论

我们已经创建了一个多提供商认证系统，可以按照您喜欢的任何方式进一步扩展。希望你喜欢这篇文章！你曾经在你的项目中通过社交网络集成认证吗？分享你的经验，不要犹豫，张贴你的问题。如果你想让我报道一个特定的话题，请发电子邮件，我会尽快回复。

快乐编码，保持社交！