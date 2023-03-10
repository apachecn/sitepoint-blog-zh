# Sinatra 中的 Twitter 认证

> 原文：<https://www.sitepoint.com/twitter-authentication-in-sinatra/>

最近在曼彻斯特举行的 [Summit Awesome Hackathon](http://summitawesome.co.uk/) 上，我的团队正在开发一个 web 应用程序，可以在一个地方更新不同社交网络上用户的个人资料。这意味着使用各种社交网络 API(我可以告诉你，这可不是一件好事)。我想我应该写一篇关于如何通过允许用户登录 Twitter 来验证 Sinatra 应用程序中的用户的文章。

## 建立网站

为了演示这种工作方式，首先，让我们建立一个简单的应用程序，它有两个 URL——public 和 private。将以下代码保存在名为“main.rb”的文件中:

```
require 'sinatra'

helpers do
  def admin?
    true
  end
end

get '/public' do
  "This is the public page - everybody is welcome!"
end

get '/private' do
  halt(401,'Not Authorized') unless admin?
  "This is the private page - members only"
end
```

这将设置一个名为`admin?`的助手方法，我们可以用它来检查用户是否登录。默认情况下，它被设置为 true，以使我们能够检查事情是否正常。我们还设置了两个路由处理器。第一个是针对路线'/public '，它向每个访问者显示一条消息。

第二个途径是一个私有页面，只有当`admin?`助手返回 true 时才会显示消息。如果没有，那么我们使用 Sinatra 的`halt`方法来停止应用程序，并显示“未授权”的消息。要检查是否发生了这种情况，请将`admin?`助手方法更改为 false，重新启动服务器(`ruby main.rb`)并转到“http://localhost:4567/private”。

## 使用会话登录和注销

我们可以添加一些路由处理程序来允许用户登录和注销，并使用会话来跟踪用户是否登录。将 main.rb 中的代码更改为以下内容:

```
require 'sinatra'

configure do
  enable :sessions
end

helpers do
  def admin?
    session[:admin]
  end
end

get '/public' do
  "This is the public page - everybody is welcome!"
end

get '/private' do
  halt(401,'Not Authorized') unless admin?
  "This is the private page - members only"
end

get '/login' do
  session[:admin] = true
  "You are now logged in"
end

get '/logout' do
  session[:admin] = nil
  "You are now logged out"
end
```

现在重新启动服务器，尝试转到“http://localhost:4567/login”，然后转到“http://localhost:4567/private”，您应该能够看到该页面。转到“http://localhost:4567/logout”，然后再次尝试“http://localhost:4567/private”，您应该会得到“未授权”的消息。

太好了——我们有一个有效的登录和注销系统。下一步是使用 Twitter 认证用户，而不是仅仅允许任何人使用登录 url 登录。

## 向 Twitter 注册您的应用程序

在开始使用 Twitter 登录之前，您需要向 Twitter 注册您的应用程序。这很容易做到——只需前往[https://dev.twitter.com/apps](https://dev.twitter.com/apps),使用你的 Twitter 证书登录。然后点击“创建新应用程序”按钮并填写表格。您可以为您的应用程序选择任何名称(只要它不包含“Twitter”这个词)，并且可以使用任何网站作为占位符，直到您的应用程序获得一个实际的域名。例如，我用 http://www.sitepoint.com。

在回调 URL 字段中，您需要将`/auth/twitter/callback`添加到您在网站字段中使用的任何 URL。例如，我使用了 https://www.sitepoint.com/auth/twitter/callback.，然后您将被带到一个包含应用程序 OAuth 设置的页面。记下“消费者密钥”和“消费者密码”，因为我们将在应用程序中使用它们。

## omniauth

我们将要用来认证的库叫做‘omni auth’。顾名思义，它实际上可以用来认证各种服务，如 GitHub 和脸书等。它根据所使用的服务被分成不同的模块，因此在这种情况下，我们需要安装 omniauth-twitter gem:

```
$ gem install omniauth-twitter
```

接下来，您需要设置 ominauth，这可以在 main.rb 的顶部完成:

```
require 'sinatra'
require 'omniauth-twitter'

use OmniAuth::Builder do
  provider :twitter, 'put your consumer key here', 'put your consumer secret here'
end
```

## 使用 Twitter 登录

我们需要创建带有 Twitter 功能的登录。首先，我们将编辑我们的“/login”路由处理程序，以便它重定向到由 Omniauth 自动设置的 URL:

```
get '/login' do
  redirect to("/auth/twitter")
end
```

请注意，如果您使用的是 GitHub，URL 应该是“/auth/github”。这将带用户进入 Twitter，并询问他们是否允许您的应用程序访问 Twitter。

如果登录成功，将触发对“/auth/twitter/callback”的回调。我们现在需要做的就是创建一个路由处理器来处理我们希望在登录成功时发生的事情。这与我们之前在登录路由处理程序中使用的代码相同:

```
get '/auth/twitter/callback' do
  env['omniauth.auth'] ? session[:admin] = true : halt(401,'Not Authorized')
  "You are now logged in"
end
```

还有一个处理不成功登录的路径，我们需要为它创建一个处理程序:

```
get '/auth/failure' do
  params[:message]
end
```

这将显示从 Twitter 发回的关于身份验证失败原因的消息。

现在我们有了一个全功能的登录系统，它使用 Twitter 进行身份验证。这是我喜欢 Sinatra 的原因之一——我们用大约 40 行代码就创建了一个使用 Twitter 进行身份验证的全功能登录系统！

## 使用 Twitter 凭证

现在我们已经有人用 Twitter 登录了，我们能做什么呢？

当用户用 Twitter 登录时，你实际上获得了关于他们的大量信息。这存储在一个名为`env['omniauth.auth']`的散列中。要查看这是什么样子，请将回调路由处理程序更改为以下内容:

```
get '/auth/twitter/callback' do
  session[:admin] = true
  env['omniauth.auth']
end
```

现在试着用 Twitter 登录，你会看到你的 Twitter 账户中所有的信息都保存在 hash 中。

例如，您可以像这样欢迎用户访问您的站点:

```
get '/auth/twitter/callback' do
  session[:admin] = true
  "<h1>Hi #{env['omniauth.auth']['info']['name']}!</h1><img src='#{env['omniauth.auth']['info']['image']}'>"
end
```

这将给予他们个性化的欢迎，并显示他们的 Twitter 个人资料图片。如果您想继续使用这些详细信息，那么您可以将它们保存在会话哈希中，如下所示:

```
get '/auth/twitter/callback' do
  session[:admin] = true
  session[:username] = env['omniauth.auth']['info']['name']
  "<h1>Hi #{session[:username]}!</h1>"
end
```

您还可以使用这种方法来验证存储在数据库中的用户。Twitter 为它的所有用户提供了一个惟一的 id，这个 id 可以是用户模型的一个属性。然后，您可以像这样查看用户(这个例子使用了 Mongoid 语法，但是您应该能够让它为您选择的 ORM 工作:

```
@user = User.where(twitter_id: env['omniauth.auth']['uid']
```

## 更新 Twitter

一旦你让一个用户通过 Twitter 认证，你就可以理论上继续操作他们的 Twitter 账户。允许您做什么取决于用户授予您对应用程序的访问权限(您可以在 Twitter 开发人员网站的设置选项卡中选择您是希望对应用程序拥有只读权限还是读写权限)。Omniauth 实际上并不允许你操作 twitter 账户。为此，你需要安装 twitter gem 。现在没有足够的空间去使用它，也许在将来的文章中…但是你可能想在此期间自己尝试一下。

## 分享这篇文章