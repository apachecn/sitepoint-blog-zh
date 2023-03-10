# 在辛纳屈创造一个延伸的宝石

> 原文：<https://www.sitepoint.com/jump-start-sinatra-sample-chapter/>

RubySource 撰稿人达伦·琼斯(Darren Jones)写了一本关于 Sinatra 的书，作为 SitePoint 最新 [*Jump Start*](https://www.sitepoint.com/jumpstart) 系列的一部分。有了这些“中篇小说大小”的书籍，时间紧迫的开发人员可以在仅仅一个周末的时间里掌握新的语言或框架。

在这段摘录中，Darren 解释了如何在 Sinatra 中创建一个管理扩展。把它拿走，达伦。

## 创建管理扩展

在第 4 章中，我们使用会话来创建允许用户登录和退出应用程序的路由处理程序。我们现在要创建一些有用的帮助器方法来保护页面并检查用户是否登录。我们还将以另一种方式注册 helper 方法——作为 Sinatra 扩展。扩展放在单独的文件中，包括助手方法、设置和路由处理程序。要使用该扩展名，您需要在应用程序中包含该文件。这使得它们可以重复使用，也可以包装成宝石进行分发。要生成扩展，在根目录下创建一个名为 **sinatra** 的新文件夹。在这个文件夹中，创建一个名为 **auth.rb** 的新文件。该扩展的完整代码如下所示:

```
require 'sinatra/base'
require 'sinatra/flash'

module Sinatra
  module Auth
    module Helpers
      def authorized?
        session[:admin]
      end

      def protected!
        halt 401,slim(:unauthorized) unless authorized?
      end
    end

    def self.registered(app)
      app.helpers Helpers

      app.enable :sessions
      app.set :username => 'frank', :password => 'sinatra'

      app.get '/login' do
        slim :login
      end

      app.post '/login' do
        if params[:username] == settings.username && params[:password] == settings.password
          session[:admin] = true
          flash[:notice] = "You are now logged in as #{settings.username}"
          redirect to('/')
        else
          flash[:notice] = "The username or password you entered are incorrect"
          redirect to('/login')
        end
      end

      app.get '/logout' do
        session[:admin] = nil
        flash[:notice] = "You have now logged out"
        redirect to('/')
      end
    end
  end

  register Auth

end
```

在文件的顶部，我们需要`'sinatra/base'`。每一个 Sinatra 扩展都需要这个文件——它是 Sinatra 的核心，只是缺少实际应用所需的代码。

我们还需要 Sinatra::Flash，因为该扩展将使用 Flash 在用户登录和注销后显示消息。

然后，我们的扩展被创建为嵌套在`Sinatra`模块中的模块。这是所有 Sinatra 扩展的标准结构。

我们将扩展将使用的助手方法放在模块的开头。这些都是在它们自己的名为`Helpers`的模块中创建的(你可以随便叫它什么，但是`Helpers`似乎很有意义)。

在`Helpers`模块中，我们添加了两个助手方法。第一个是`authorized?`，它通过检查`session[:admin]`的值是否为真来查看用户是否已经登录。这是在路由处理程序和视图中使用的好方法。

第二种方法是`protected!`。它指定路由处理程序只能由登录的用户访问(注意，它利用了`authorized?` helper 方法来检查这一点)。

下一个使用 Sinatra 的`halt`方法，该方法立即停止一个请求并返回一个指定的 HTTP 代码(本例中为 401)。它还显示了一个名为`unauthorized`的视图，我们将很快生成并保存在我们的**视图**目录中。

在助手之后，我们将设计一个叫做`self.registered(app)`的特殊方法。这包含扩展和所有路由处理程序的所有设置。它还指定了我们刚刚创建的助手模块的名称。这个方法中包含的所有方法都必须是`app`对象的方法，这是`self.registered`方法的参数，实际上是使用扩展的应用程序。

下一行告诉助手使用名为`Helpers`的模块。

之后，我们启用`sessions`并创建一些设置。这些设置可以在 **main.rb** 中被覆盖，这意味着你可以改变扩展使用的所有设置，而不必接触扩展文件。它们只是默认设置。

然后是路线处理程序。这些与我们在上一章中创建的基本相同，尽管我们现在使用 Sinatra::Flash 在用户登录和退出时添加一些漂亮的消息。

最后，在文件的末尾，我们注册了扩展名。

保存文件后，将下面一行添加到需求列表中的 **main.rb** 的顶部:

```
require './sinatra/auth'
```

最后，如上所述，我们需要一个`unauthorized`视图:

```
h1 Unauthorized
p You need to be logged in to view this page.
```

重启服务器，你应该能够使用我们的新扩展登录和退出。为了使事情变得更简单，让我们添加一个链接，以便用户可以在页脚中这样做。我们希望它出现在每一页上，所以让我们将下面的代码添加到 **layout.slim** 的底部:

```
footer
  - if authorized?
    a href="/logout" log out
  - else
    a href="/login" log in
```

它使用`authorized?`助手检查用户是否登录，然后显示相关链接(登录或注销)。

我们的最后一个任务是利用`protected!`方法要求用户登录某些页面。例如，创建新歌曲的页面应该只对登录的用户可用，因此它应该将`protected!`方法放在路由处理程序的开始处。打开 **song.rb** 并修改它，使它看起来像
下面这样:

```
get '/songs/new' do
protected!
```

我强烈建议您也将`protected!`方法添加到创建、编辑、更新和
删除路由处理程序中！

扩展`Auth`的目的是为了让它可以在其他应用程序中重用，但是其他应用程序不太可能想要使用相同的用户名和密码(更不用说安全风险了)。不过，改变这些很简单；你甚至不用编辑 **auth.rb** 文件；你可以在 **main.rb** 中设置它们，就像这样:

```
set :username, 'daz'
set :password, 'secret'
```

主应用程序文件中的设置将总是取代扩展文件中的默认设置，这使得定制扩展的设置变得容易，而不必在扩展的代码中四处摸索。

—

谢谢你，达斯。现在你有了，当你购买达伦的新书 [*Jump Start Sinatra*](https://www.sitepoint.com/store/jump-start-sinatra/) 时，你可以期待的一些专业知识。

## 分享这篇文章