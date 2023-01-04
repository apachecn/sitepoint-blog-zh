# 让您的应用为 Rails 4 做好准备

> 原文：<https://www.sitepoint.com/get-your-app-ready-for-rails-4/>

一个新的 Rails 版本即将发布，它带来了许多变化、弃用和新特性。

让我们来看看你需要做些什么来让你的应用为 Rails 4 做好准备。

## 升级之路

为 Rails 4 做好准备的最简单的方法是将你的应用安装到 Rails 3.2 上。
如果不是 3.2，升级应该是次要版本。即从 3.0 到 3.1，然后从 3.1 到 3.2。

## 贬值

Rails 4 将完全删除一些内容。我们应该了解他们，并为升级做好准备。

### 仅限 Ruby 1.9.3+版本

Rails 4 将只针对 Ruby 1.9.3 和未来版本。所以要确保你的应用能在上面运行。

如果您使用的是 Ruby 1.9.x，升级应该非常简单。

如果你还在使用 Ruby 1.8.7，事情可能会更复杂一些。

现在大多数 gems 应该运行在 1.9 或者有一个替代版本。像 [rvm](https://rvm.io/) 或 [rbenv](https://github.com/sstephenson/rbenv) 这样的工具可以让你运行多个版本的 Ruby，帮了大忙。

在 gem 文件中使用条件语句对于在每个环境中使用不同的 gem 非常方便，直到在 1.9 上全部运行。

它应该是这样的:

```
gem 'ruby18-only-gem', :platforms => :ruby_18
gem 'ruby19-only-gem', :platforms => :ruby_19

#More on this can be seen in the Bundler manpages:
#http://gembundler.com/man/gemfile.5.html#PLATFORMS-platforms-
```

关于 1.8 和 1.9 的变化的一个很好的参考资料是 Peter Cooper 的 Ruby 1.9 演练。强烈推荐！

### 不再有供应商/插件

Rails 4 将移除 Rails::Plugins 类。因此，不会加载供应商/插件目录中的任何代码。

大多数应用已经依赖 gems，而不是供应商提供的插件。但是，如果您在供应商/插件中还有任何代码，您有两个选择:

*   把它移到宝石上。大多数插件应该已经有 gem 版本了。如果没有，您可以通过`:git`或`:path`选项从 Gemfile 中引用它。
*   将它移动到`lib/your_plugin`并从`config/initializers`上的初始化器中请求它。

这是不推荐使用的[提交](https://github.com/rails/rails/commit/dad7fdc5734a3813246f238ac5760b2076932216)。

### 路线匹配

在路线上，`match`方法将不再作为一个包罗万象的选项。现在，您应该用选项`:via`指定要响应哪个 HTTP 动词

```
#Rails 3.2
match "/users/:id" => "users#show"
```

```
#Rails 4.0
match "/users/:id" => "users#show", via: :get

#or specify multiple verbs
match "/users" => "users#index", via: [:get, :post]
```

更好的 Rails 3.2 兼容性的另一个选择是用显式的`get`、`post`或任何其他 HTTP 动词来指定您的动作。有了这个选项，您仍然可以让您的代码在今天运行，并为将来的升级做好准备。

```
#Rails 3.2 and 4.0 compatible

get "/users/:id" => "users#show"

# multiple verbs

get "/users" => "users#index"
post "/users" => "users#index"
```

### ActiveRecord 范围需要一个可调用的对象

在 Rails 4 中，所有的 ActiveRecord 作用域都必须用一个可调用的对象来定义(比如`Proc`或`lambda`):

```
#Rails 3.2
scope :recent, where(created_at: Time.now - 2.weeks)
```

```
#Rails 4
scope :recent, -> { where("created_at > ?", Time.now - 2.weeks).order("created_at desc") }
scope :active, -> { where(status: 'active') }
```

这有助于避免日期或时间对象只被评估一次而不是被动态评估的微妙错误。

### 删除的代码

Rails 4 代码库中也删除了大量代码。不过，不用担心，大多数仍然作为独立的宝石存在。这有助于升级者平稳过渡。

删除的代码:

*   [ActiveResource](https://github.com/rails/activeresource) (这里有一篇[的博文](http://yetimedia.tumblr.com/post/35233051627/activeresource-is-dead-long-live-activeresource)解释这一变化。)
*   [活动记录观察器和动作控制器清扫器](https://github.com/rails/rails-observers)
*   [active record::session store](https://github.com/rails/activerecord-session_store)
*   [ActiveModel 批量分配杀毒软件](https://github.com/rails/protected_attributes)
*   [动作缓存](https://github.com/rails/actionpack-action_caching)
*   [页面缓存](https://github.com/rails/actionpack-page_caching)
*   [基于哈希的动态查找器](https://github.com/rails/activerecord-deprecated_finders)

对于后者，值得一提的是:

active record-deprecated-finders 将成为 Rails 4.0 上的默认依赖项，以提供不推荐使用的功能。但是它将在 4.1 中被删除。因此，您应该密切关注所有警告，并开始修复它们！

[Rails guides](http://edgeguides.rubyonrails.org/4_0_release_notes.html#active-record-deprecations) 提供了关于更改动态查找器上最常见情况的有用解释:

> 除了通过查找*和通过*查找*之外的所有动态方法…已被否决。下面是重写代码的方法:*
> 
> *   `find_all_by_...`可以使用`where(...).`重写
> *   使用`where(...).last`可以重写`find_last_by_...`。
> *   使用`where(...)`可以重写`scoped_by_...`。
> *   使用`where(...).first_or_initialize`可以重写`find_or_initialize_by_...`。
> *   使用`find_or_create_by(...)`或`where(...).first_or_create`可以重写`find_or_create_by_...`。
> *   使用`find_or_create_by!(...)`或`where(...).first_or_create!`可以重写`find_or_create_by_...!`。

所有这些宝石将有助于平稳过渡。我建议，为了获得完整的 Rails 4 体验，只需在开发中使用它们，允许警告消息帮助您将所有代码升级到最新的语法。

## 新功能

现在是有趣的部分了！

Rails 4 中加入了很多东西。好消息是，它们中的大多数现在也可以通过 gems for rails 3.2 应用程序获得。我们可以进行预览，同时为升级做好代码准备！

让我们深入研究一下。

### 强参数([宝石](https://github.com/rails/strong_parameters))

围绕批量分配保护产生了很多争议。从那时起，探索了各种不同的方法。

Rails 3.2 在模型上使用了`attr_accessible`和`attr_protected`。

Rails 4 采用了完全不同的观点，让控制器负责将什么属性分配给模型。

```
#Rails 3.2
class User < ActiveRecord::Base
  attr_protected :name, :email
end

class UsersController < ApplicationController
  def create
    @user = User.new params[:user]
    if @user.save
      redirect_to @user
    else
      render :new
    end
  end
end
```

```
#Rails 4.0

class User < ActiveRecord::Base
  include ActiveModel::ForbiddenAttributesProtection
end

class UsersController < ApplicationController
  def create
    @user = User.new params.require(:user).permit(:name, :email)
    if @user.save
      redirect_to @user
    else
      render :new
    end
  end
end
```

这也可以简化为

```
#Rails 4

class UsersController < ApplicationController
  def create
    @user = User.new user_params
    #  ...
  end

  private
  def user_params
    params.require(:user).permit(:name, :email)
  end
end
```

这种方法的一个优点是，它允许我们在面向用户的操作中过滤不同于在管理区域中的参数。此外，不同的动作可以允许不同的允许属性集。让控制器负责模型接收的内容是有意义的。

使用这种宝石时还要记住一件事，你需要在`config/application.rb`上加上这一行:

```
config.active_record.whitelist_attributes = false
```

[gem](https://github.com/rails/strong_parameters) 还提供了非常清晰的自述，值得一读。幸运的是，有一个 [Railscast](http://railscasts.com/episodes/371-strong-parameters) 供更好奇的人使用。

### 缓存摘要和俄罗斯娃娃缓存( [gem](https://github.com/rails/cache_digests) )

随着 Rails 4 中动作和页面缓存的移除，引入了一种新的存储缓存的方式。这是通过基于[密钥的到期](http://37signals.com/svn/posts/3113-how-key-based-cache-expiration-works)。
主要思想是将`updated_at`属性附加到缓存键上。当一个记录改变时，它的缓存键也会改变，它会获取一个新的版本。

这种方法会产生大量的缓存垃圾，因此它更适合类似 memcache 的存储，当它耗尽空间时，首先删除最老的键。

此外，对于视图，它在缓存密钥上生成文件的 MD5 校验和。如果文件中的任何内容发生变化，缓存键也会发生变化，它会再次获取所有内容。

```
#Rails 3.2
<% cache ['v1', @user] do %>
...
<% end %>
```

```
#Rails 4.0
<% cache @user do %>
...
<% end %>
#this will generate a key like
# views/users/1-201222172130/1a79a4d60de6718e8e5b326e338ae533
```

这对于嵌套视图特别有效，因为它知道嵌套模板。

这里有一个关于这个话题的很棒的 [Railscast](http://railscasts.com/episodes/387-cache-digests) 。

### 声明性标签( [gem](https://github.com/rails/etagger) )

在 Rails 4 中，您将能够设置控制器范围的 ETag 后缀。当一个操作依赖于多个记录或一个登录用户时，这很有帮助。

要获得更好的 HTTP 缓存，这是一件非常容易的事情。

一个简单的例子:

```
class TodosController < ApplicationController
  etag { current_user.try :id }
  etag { @todo_list }

  before_fiter :find_todo_list

  def show
    @todo = @todo_list.todos.find(params[:id])
    fresh_when(@todo)
  end

  private
  def find_todo_list
    @todo_list = TodoList.find(params[:todo_list_id])
  end
end
```

在本例中，第一个`etag`块检查当前用户，确保没有两个登录用户拥有相同的缓存 ETag。第二个使用`TodoList`对象作为 ETag 的一部分，因此对 todo 列表的更改就是对 ETag 的更改。

### 涡轮链([宝石](https://github.com/rails/turbolinks))

Turbolinks 有点像 [pjax](https://github.com/defunkt/jquery-pjax) 的兄弟。它使用可用的`pushState`，触发 XHR 请求并替换`<body>`内容。这有 3 个主要好处:

*   不会下载 CSS/JS 两次。
*   不会重新编译。
*   不会重新评估。

Rails 4 默认启用了 Turbolinks。但是如果在`domready`事件中使用了太多的代码，这会导致一些问题。TurboLinks 宣布 4 项新活动:

*   `page:fetch`开始获取目标页面(仅在加载新页面时调用，不从缓存中调用)。
*   正在从服务器上重新检索获取的页面。
*   `page:restore`正在从 10 插槽客户端缓存中检索获取的页面。
*   页面已更改为新获取的版本。

您必须检查您的 javascript 并在必要的地方进行修正。

现在要使用它，只需安装 gem 并将`//= require turbolinks`添加到您的主 javascript 文件(通常是`application.js`)中

关于这个功能，这里有一个很棒的 Railscast。

### 关系

Rails 4 引入了新的默认模式，以避免代码重复。

#### **路由关注([宝石](https://github.com/rails/routing_concerns) )**

当路由之间共享多个资源或方法时，也会有大量的代码重复。这个特性给了我们一个新的`concern`路由定义，这有助于提高可读性，并使代码更加简洁。

```
#Rails 3.2

resources :messages  do
  resources :comments
  post :trash, :restore, on: :member
end

resources :documents do
  resources :comments
  post :trash, :restore, on: :member
end
```

```
#Rails 4

concern :commentable do
  resources :comments
end

concern :trashable do
  post :trash, :restore, on: :member
end

resources :messages, :documents, concerns: [:commentable, :trashable]
```

这可以在 Rails 3.2 中使用，只需将 [routing_concerns](https://github.com/rails/routing_concerns) gem 添加到您的应用程序中。

#### **型号和控制器关注点**

默认情况下，Rails 4 在默认目录结构和加载路径中添加了两个新文件夹:`app/controllers/concerns`和`app/models/concerns`。
DHH 在[本帖](http://37signals.com/svn/posts/3372-put-chubby-models-on-a-diet-with-concerns)中解释了这背后的想法。

通常，这段代码会存在于`lib`中。这些新目录使东西更干净，更容易找到。

现在要做到这一点，您只需将这一行添加到您的`config/application.rb`

```
config.autoload_paths += Dir["#{config.root}/app/controllers/concerns", "#{config.root}/app/models/concerns"]
```

并将您的模型和控制器模块移到这些目录中。

### Dalli 作为内存缓存适配器( [gem](https://github.com/mperham/dalli) )

Dalli 将取代 memcache-client 作为缓存的默认 gem。
这带来了几项免费的改进:

*   它比 memcache-client gem 大约快 20%。
*   通过恢复和可调超时来处理故障转移。
*   它是线程安全的。
*   使用较新的 memcached 二进制协议。

为了在你的应用中测试这一点，你可以做一些简单的改变。

在您的 gem 文件中:

```
gem 'dalli' 
```

在`config/environments/production.rb`(或者任何使用 memcached 的环境):

```
config.cache_store = :dalli_store
# just be sure to change this back to :mem_cache_store when upgrading to Rails 4
```

有一点需要注意:Dalli 依赖于 memcached 1.4+，所以如果你使用的是旧版本，一定要更新！

### 默认情况下线程安全

在新的 Rails 应用程序中，`threadsafe!`选项将在生产模式下默认启用。关闭它的方法是将`config.cache_classes`和`config.eager_load`设置为`false`。

这应该不是什么大问题，除非这个应用程序(或者它的一个瑰宝)过于依赖线程安全。

下面是 Aaron Patterson 的一篇博文，解释了这一变化及其后果:

[config.threadsafe！:它是做什么的？](http://tenderlovemaking.com/2012/06/18/removing-config-threadsafe.html)

你现在就可以在你的应用中启用`threadsafe!`，看看它们的表现如何。当升级到 Rails 4 时，您会看到一个警告，您可以一直删除这一行。

## 摘要

我们已经介绍了即将发布的 Rails 4 的一系列重大变化。有了这些变化，大多数应用程序应该很容易更新。无论如何，Rails 4 仍在积极开发中，所以将来可能需要一些其他的改变。我的目标是在这种情况下保持这篇文章的更新。

延伸阅读:

*   [Rails 4 2013 年倒计时](http://blog.remarkablelabs.com/2012/11/rails-4-countdown-to-2013)
*   [30 分钟内 4 轨](https://speakerdeck.com/spastorino/rails-4-in-30)
*   [Ruby on Rails 4.0 发行说明](http://edgeguides.rubyonrails.org/4_0_release_notes.html)

## 分享这篇文章