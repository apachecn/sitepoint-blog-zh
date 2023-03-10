# 深入了解基本的铁路路线

> 原文：<https://www.sitepoint.com/an-in-depth-look-at-basic-rails-routing/>

![](img/7e7575dba05c4d4aab03994026915804.png)

教程的目的是以初学者的口吻解释 URL 映射在 Rails 应用程序中是如何发生的。我不可能涵盖所有关于 Rails 路由的信息，但是我会尽我所能来谈谈基本的方面。教程的这一部分将集中在简单的路线，下一部分我们将谈论资源丰富的路线。

Ruby on Rails 路由系统以一种独特的方式处理请求。它检查传入请求的 URL，并确定负责处理每个请求的控制器操作。它使用路由文件 **config/routes.rb** 中指定的特殊语法进行映射。这个 routes 文件控制着你的 web 应用程序的每个 URL 方面，因此我们研究它是很重要的。

## 铁路路线文件

生成的文件包含如何使用它的说明。让我们离开这一点，去挖掘路线的创建。

### 创建常规路线

创建路由的基本方法包括将 URL 映射到控制器和动作。当 Rails 路由器看到一个请求时，它会将该请求分派给与 URL 匹配的控制器动作。

所以一个网址看起来像这样:

```
/books/2 
```

将被映射到管制员的动作，假设航路被定义为:

```
get 'books/:id' => 'books#show' 
```

这是以下内容的简写:

```
get 'books/:id' to: 'books/show' 
```

这里的控制器将是`BooksController`并且将有一个`show`方法。下一步行动由控制器执行。您应该注意到我们在调用控制器和动作方法时使用的模式

```
books#show 
```

这是您将在这种场景中使用的模式。控制器是复数形式，方法前面有一个`#`,这是 Ruby 的说法，说明你在谈论一个实例方法。你可能会对上面例子中的`:id`感到困惑，你可以肯定我不会让你一无所知:)

如何处理这件事没有什么诀窍。当向应用程序发出请求时，Rails 会获取所有附带的参数，并在一个名为`params`的特殊散列中提供这些参数，您可以在控制器中使用这些参数。如果一个请求 URL 看起来像这样:http://sitepointbooks.com/books/15`,处理它的路由看起来像这样:

```
get 'books/:id' => 'books#show' 
```

控制器和动作方法将如下所示:

```
class BooksController < ApplicationController
  def show
    @books = Book.find(params[:id])
  end
end 
```

这显然不是唯一的方法，只是出于必要，我将放弃另一种方法。

```
class BooksController < ApplicationController
  before_action :set_book, only: [:show]

  def show
  end

  private

  def set_book
    @books = Book.find(params[:id])
  end
end 
```

第二种模式帮助您在其他操作之间共享公共设置。如果你以后看到类似上面的东西，你应该理解它。确定这一点后，让我们来看看一些有趣的东西——根路由。不要忘记我们上面谈到的东西，在下一节中你将需要这些知识。你懂吗？

### 根路由

根据您在 routes 文件中声明的规则，当对 web 应用程序的域名发出请求时，将调用根路由。假设你的域名是`http://test.com`，当有人试图连接到它，这条规则得到执行。现在规则是什么？

路由文件中的规则可能如下所示:

```
#config/routes.rb

root to: 'welcome#index' 
```

就像我们上面看到的，这意味着有一个`WelcomesController`和一个`index`方法动作。以下是其他一些例子:

```
root to: 'pages#index'
root to: 'welcome#show'
root to: 'pages#home' 
```

您只需要注意命名根路由时使用的惯例，就可以了。有一种命名根路由的简写格式，如下所示:

```
root 'pages#index' 
```

当您生成一个新的 Rails 应用程序并将您的浏览器指向根时，您将看到一个根页面，即使您还没有定义根路由。这怎么可能呢？如果你还没有看到这个，在你继续阅读之前试一试。

打开终端，生成一个新的 Rails 应用程序。`bundle install`你的宝石，开始你的`rails server`。打开浏览器，指向`http://localhost:3000`，你会看到一个根页面。不，那不是魔术:)让我解释发生了什么。

当您还没有为您的应用程序定义一个根时，默认情况下，Rails 会路由到一个内部的`Rails::WelcomeController`并呈现一个欢迎页面。明白了吗？

你可能会问:"我机器上的欢迎页面到底在哪里？"你可以在 GitHub-[Rails 默认欢迎页面](https://github.com/rails/rails/blob/master/railties/lib/rails/templates/rails/welcome/index.html.erb)上找到`Rails::WelcomeController`的代码。

当您在 routes 文件中定义根路由时，Rails 会自动停止调用这个控制器(`Rails::WelcomeController`)。

说到这里，让我们来看看命名路由。

### 命名路线

Rails 允许您使用`:as`参数来命名路线。这里有一种方法:

```
get 'contact', to: 'pages#show', as: 'contact' 
```

这为您提供了`contact_url`和`contact_path`助手，您可以在应用程序中需要的地方调用它们。例如在你看来是这样的:

当链接被点击时，`PagesController`的`show`动作被调用。超链接中显示的路径是:

```
/contact 
```

另一个例子涉及需要认证的应用程序。您可以用一个`SessionsController`来处理认证，用`new`和`destroy`动作分别处理登录和注销。在这种情况下，命名路由会派上用场:

```
get 'login', to: 'sessions#new', as: 'login'
get 'logout', to: 'sessions#destroy', as: 'logout' 
```

现在，您可以在应用程序中需要的地方使用`login_path`或`logout_path`。超链接显示:

```
/login
/logout 
```

### 铁路路线范围界定

Rails 为您提供了对相关路由进行分组并为它们设置默认选项的能力。为此，我们使用了`scope`方法。让我们来看看这是如何工作的，假设您的 routes 文件中有这样的内容:

```
get 'articles/new', to: 'articles#new'
get 'articles/edit/:id', to: 'articles#edit'
post 'articles/draft/:id', to: 'articles#draft' 
```

在上面的代码中，所有路线都映射到`ArticlesController`。使用`scope`你可以整理东西，让它看起来像这样

```
scope controller: :articles do
  get 'articles/new' => :new
  get 'articles/edit/:id' => :edit
  post 'articles/draft/:id' => :draft
end 
```

从上面我们给出了应用程序的路由系统指令，以将块中的路由映射到`ArticlesController`。我们不必为每条路线单独说明这一点。我们还可以通过修改代码告诉它要使用的路径，如下所示:

```
scope path: '/articles', controller: :articles do
  get 'new' => :new
  get 'edit/:id' => :edit
  post 'post/:id' => :post
end 
```

正如我们在上面看到的，`scope`方法接受一个`:controller`和`:path`选项。它还接受一个`:as`选项，所以我们可以

```
scope :articles, as: 'posts' do
  get 'new' => :new
end 
```

要了解 scope 如何工作的更多信息，请查看以下链接:

*   [导轨](http://edgeguides.rubyonrails.org/routing.html#controller-namespaces-and-routing)
*   [范围-行动调度](http://api.rubyonrails.org/classes/ActionDispatch/Routing/Mapper/Scoping.html#method-i-scope)

### 耙路线

您如何知道您的应用程序中存在哪些路由？或者我应该先问一下；有可能知道您的应用程序中的现有路线吗？当您将浏览器指向尚未添加到 routes 文件中的路线时，您将看到如下图所示的 Rails Routing 错误页面:

![routes](img/c505b98949f23c32aa7f6d2c085dddff.png)

这是您将在开发环境中看到的默认`404 error`页面。它列出了您的应用程序中已经定义的所有路线。如图所示，您可以在`path_hlpers`和`url_helpers`之间切换，您可以像这样在您的应用程序中使用它们

```
<%= link_to "Login", login_path %> 
```

您还可以通过使用所有 Rails 应用程序中的标准 Rake 任务来查找应用程序中定义的路线。要了解这是如何工作的，请打开您之前生成的 Rails 应用程序的 routes 文件，并粘贴以下代码:

```
#config/routes.rb

Rails.application.routes.draw do
  resources :books
end 
```

在你的终端`rake routes`中输入这个命令，然后回车。这应该会产生下面的内容:

```
 Prefix Verb   URI Pattern               Controller#Action
    books GET    /books(.:format)          books#index
          POST   /books(.:format)          books#create
 new_book GET    /books/new(.:format)      books#new
edit_book GET    /books/:id/edit(.:format) books#edit
     book GET    /books/:id(.:format)      books#show
          PATCH  /books/:id(.:format)      books#update
          PUT    /books/:id(.:format)      books#update
          DELETE /books/:id(.:format)      books#destroy 
```

让我们更进一步，编辑您的 routes 文件，如下所示:

```
#config/routes.rb

Rails.application.routes.draw do
  resources :books

  root "welcome#home"

  get 'login', to: 'sessions#new', as: 'login'
  get 'logout', to: 'sessions#destroy', as: 'logout'

  scope path: '/articles', controller: :articles do
    get 'new' => :new
    get 'edit/:id' => :edit
    post 'post/:id' => :post
  end
end 
```

运行`rake routes`并检查输出。它应该是这样的:

```
 Prefix Verb   URI Pattern                  Controller#Action
    books GET    /books(.:format)             books#index
          POST   /books(.:format)             books#create
 new_book GET    /books/new(.:format)         books#new
edit_book GET    /books/:id/edit(.:format)    books#edit
     book GET    /books/:id(.:format)         books#show
          PATCH  /books/:id(.:format)         books#update
          PUT    /books/:id(.:format)         books#update
          DELETE /books/:id(.:format)         books#destroy
     root GET    /                            welcome#home
    login GET    /login(.:format)             sessions#new
   logout GET    /logout(.:format)            sessions#destroy
      new GET    /articles/new(.:format)      articles#new
          GET    /articles/edit/:id(.:format) articles#edit
          POST   /articles/post/:id(.:format) articles#post 
```

结果总是以这种格式列出:

*   路线名称。
*   使用的 HTTP 动词。
*   要匹配的 URL 模式。
*   路线的路线参数。

您可以将`rake routes`的结果限制在给定控制器的路径上。在您的终端中运行这个命令:`CONTROLLER=books rake routes`并查看结果。

## 结论

至此，您已经理解了 Rails 路由系统的基本方面。在下一部分，我们将讨论资源丰富的路线。感谢阅读:)

## 分享这篇文章