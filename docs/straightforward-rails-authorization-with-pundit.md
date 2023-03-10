# 使用 Pundit 进行简单的 Rails 授权

> 原文：<https://www.sitepoint.com/straightforward-rails-authorization-with-pundit/>

![LTKkxELyc](img/7b112f8f93ef4a66e3f5a739a8d36c22.png)

这是“Rails 授权”系列的第二篇文章。在[之前的文章](https://www.sitepoint.com/cancancan-rails-authorization-dance/)中，我们讨论了[康康康](https://github.com/CanCanCommunity/cancancan)，这是一个广为人知的解决方案，由 Ryan Bates 发起，现在得到了一群爱好者的支持。今天我将向你介绍一个不太受欢迎但仍然可行的库，叫做[专家](https://github.com/elabs/pundit)，由 [ELabs](http://www.elabs.se/) 的人们创建。

“学者”的意思是“一个学者”，“一个聪明的家伙”(有时用作负面描述)，但你不需要成为天才才能在你的项目中使用它。Pundit 很容易理解，相信我，你会喜欢的。当我浏览它的文档时，我爱上了它。

Pundit 背后的想法是[使用普通的旧 Ruby 类和方法](https://github.com/elabs/pundit#just-plain-old-ruby)而不涉及任何特殊的 DSL。这个 gem 只是增加了几个有用的助手，所以，总而言之，你可以按照你认为合适的方式来设计你的系统。这个方案比康康康要低级一点，两者对比真的很有意思。

在本文中，我们将讨论 Pundit 的所有主要特性:使用访问规则、使用助手方法、
以及定义允许的属性。

工作演示可在[sitepoint-pundit.herokuapp.com](https://sitepoint-pundit.herokuapp.com)获得。

源代码可以在 [GitHub](https://github.com/bodrovis/Sitepoint-source/tree/master/Authorization_with_Pundit) 上找到。

## 准备

我们的准备会非常快。继续创建一个新的 Rails 应用程序。Pundit 支持 Rails 3 和 4，但是这个演示将使用 4.1 版本。

将以下宝石放入您的*宝石档案*:

*Gemfile*

```
[...]
gem 'pundit'
gem 'clearance'
gem 'bootstrap-sass'
[...] 
```

Clearance 将用于快速设置身份验证。在我的[“带许可的简单 Rails 认证”](https://www.sitepoint.com/simple-rails-authentication-with-clearance/)文章中已经介绍了这一点，所以您可以参考它以获得更多信息。

Bootstrap 将用于基本样式，尽管你可以像往常一样跳过它。

别忘了跑步

```
$ bundle install 
```

现在，运行 Clearance 的生成器，它将创建一个`User`模型和一些基本配置:

```
$ rails generate clearance:install 
```

修改布局以包含 flash 消息，因为 Clearance 和 Pundit 依赖这些消息向用户显示信息:

*layouts/application . html . erb*

```
[...]
<div id="flash">
  <% flash.each do |key, value| %>
    <div class="alert alert-<%= key %>"><%= value %></div>
  <% end %>
</div>
[...] 
```

为`Post`创建新的脚手架:

```
$ rails g scaffold Post title:string body:text 
```

我们还希望用户在使用应用程序前登录:

*application _ controller . Rb*

```
[...]
before_action :require_login
[...] 
```

设置根路由:

*routes.rb*

```
[...]
root to: 'posts#index'
[...] 
```

### 管理员用户

为了完成我们的实验室环境的设置，我们需要在`users`表中添加一个`admin`字段，所以像这样修改迁移:

*xxx_create_users.rb*

```
[...]
t.boolean :admin, default: false, null: false
[...] 
```

运行迁移:

```
$ rake db:migrate 
```

最后，让我们添加一个小按钮来轻松地在管理状态之间切换:

*layouts/application . html . erb*

```
[...]
<% if current_user %>
  <div class="well well-sm">
    Admin: <strong><%= current_user.admin? %></strong><br>
    <%= link_to 'Toggle admin rights', user_path(current_user), method: :patch, class: 'btn btn-info' %>
  </div>
<% end %>
[...] 
```

我们必须检查`current_user`是否存在，否则未经认证的用户将会看到一个错误。

添加路线:

*routes.rb*

```
[...]
resources :users, only: [:update]
[...] 
```

和控制器:

*users_controller.rb*

```
class UsersController < ApplicationController
  def update
    @user = User.find(params[:id])
    @user.toggle!(:admin)
    flash[:success] = 'OK!'
    redirect_to root_path
  end
end 
```

就是这样！实验室环境已经准备好了，所以现在我们可以开始玩 Pundit 了。

## 整合专家

首先，在`ApplicationController`中添加以下一行:

*application _ controller . Rb*

```
[...]
include Pundit
[...] 
```

接下来，运行 Pundit 的生成器:

```
$ rails g pundit:install 
```

这将在 **app/policies** 文件夹中创建一个包含策略的基类。**政策类**是 Pundit 的核心，我们将与他们广泛合作。基本策略类如下所示:

*应用/策略/应用 _ 策略. rb*

```
class ApplicationPolicy
  attr_reader :user, :record

  def initialize(user, record)
    raise Pundit::NotAuthorizedError, "must be logged in" unless user
    @user = user
    @record = record
  end

  def index?
    false
  end

  def show?
    scope.where(:id => record.id).exists?
  end

  def create?
    false
  end

  def new?
    create?
  end

  # [...]
  # some stuff omitted

  class Scope
    # [...]
  end
end 
```

每个策略都是一个基本的 Ruby 类，但是您必须记住几件事情:

*   策略应该以它们所属的模型命名，但是要以单词`Policy`为前缀。例如，将`PostPolicy`用于`Post`型号。如果你没有相关的模型，你仍然可以使用 Pundit——阅读更多[这里](https://github.com/elabs/pundit#headless-policies)。
*   `initialize`方法的第一个参数是用户记录。Pundit 使用`current_user`方法来得到它，但是如果你没有这样的方法，这种行为可以通过重写`pundit_user`来改变。在这里阅读更多。
*   第二个参数是模型对象。但是它不一定是一个`ActiveModel`对象。
*   policy 类必须实现类似于`create?`或`new?`的查询方法来检查访问权限。

如果您正在使用基本策略类并从它继承，您实际上并不需要担心大部分这些东西，但是有时您可能需要一个自定义策略类(例如，当您没有相应的模型时)。

## 提供访问规则

现在，让我们编写第一个访问规则。例如，我们只希望管理员用户能够销毁帖子。这很容易做到！在**策略**文件夹中创建一个新的 **post_policy.rb** 文件，并粘贴以下代码:

*policies/post_policy.rb*

```
class PostPolicy < ApplicationPolicy
  def destroy?
    user.admin?
  end
end 
```

如您所见，`destroy?`方法将返回`true`或`false`，表明用户是否被授权执行某个操作。

在控制器内部，我们需要检查我们的规则:

*posts_controller.rb*

```
[...]
def destroy
  authorize @post
  @post.destroy

  redirect_to posts_url, notice: 'Post was successfully destroyed.'
end
[...] 
```

`authorize`接受第二个可选参数，以提供要使用的规则的名称。如果您的操作名称不同，例如:

```
def publish
  authorize @post, :update?
end 
```

您也可以传递类名而不是实例，例如，如果您没有要使用的资源:

```
authorize Post 
```

如果不允许用户执行该操作，将会引发一个错误。我们需要拯救它，并显示一个有用的信息。最简单的解决方案是只呈现一些基本的文本:

*application _ controller . Rb*

```
[...]
rescue_from Pundit::NotAuthorizedError, with: :user_not_authorized

private

def user_not_authorized
  flash[:warning] = "You are not authorized to perform this action."
  redirect_to(request.referrer || root_path)
end
[...] 
```

但是，您可能需要为不同的情况设置自定义消息，或者将其翻译为其他语言。这也是可能的:

*application _ controller . Rb*

```
[...]
rescue_from Pundit::NotAuthorizedError, with: :user_not_authorized

private

def user_not_authorized(exception)
  policy_name = exception.policy.class.to_s.underscore
  flash[:warning] = t "#{policy_name}.#{exception.query}", scope: "pundit", default: :default
  redirect_to(request.referrer || root_path)
end
[...] 
```

不要忘记更新翻译文件:

*config/locales/en.yml*

```
en:
 pundit:
   default: 'You cannot perform this action.'
   post_policy:
     destroy?: 'You cannot destroy this post!' 
```

如果用户不能销毁帖子，那么呈现“销毁”按钮是没有意义的。幸运的是，Pundit 提供了一个特殊的助手方法:

*views/posts/index . html . erb*

```
[...]
<% if policy(post).destroy? %>
  <td><%= link_to 'Destroy', post, method: :delete, data: { confirm: 'Are you sure?' } %></td>
<% end %>
[...] 
```

去看看吧！一切都应该运行良好。

您可能会想，在策略中，我们不会处理用户根本没有经过身份验证的情况。要修复它，只需添加下面一行:

*策略/应用 _ 策略. rb*

```
[...]
def initialize(user, record)
  raise Pundit::NotAuthorizedError, "must be logged in" unless user
  @user = user
  @record = record
end
[...] 
```

只是不要忘记从`ApplicationController`内部的`Pundit::NotAuthorizedError`错误中解救出来。

### 建立关系

现在，让我们在帖子和用户之间建立一个一对多的关系。创建新迁移并应用它:

```
$ rails g migration add_user_id_to_posts user:references
$ rake db:migrate 
```

修改模型文件:

*车型/后 rb*

```
[...]
belongs_to :user
[...] 
```

*型号/用户. rb*

```
[...]
has_many :posts
[...] 
```

此外，用一些演示记录填充数据库会很棒。为此使用 *seeds.rb* :

```
20.times do |i|
  Post.create({title: "Post #{i + 1}", body: 'test body', user_id: i > 10 ? 1 : 2})
end 
```

我们只是创建了 20 个属于不同用户的职位。如果需要，可以随意修改这段代码。

奔跑

```
$ rake db:seed 
```

来填充数据库。

修改策略以允许用户销毁自己的帖子:

*policies/post_policy.rb*

```
[...]
def destroy?
  user.admin? || record.user == user
end
[...] 
```

您可能还记得，`record`被设置为我们正在处理的对象。这很好，但我们可以做得更多。创建一个范围来只加载用户拥有的帖子怎么样？权威人士也支持这一点！

## 使用范围

首先，创建一个新的非 RESTful 动作:

*posts_controller.rb*

```
def user_posts
end 
```

*routes.rb*

```
resources :posts do
  collection do
    get '/user_posts', to: 'posts#user_posts', as: :user
  end
end 
```

添加顶部菜单:

*layouts/application . html . erb*

```
[...]
<nav class="navbar navbar-inverse">
  <div class="container">
    <div class="navbar-header">
      <%= link_to 'Pundit', root_path, class: 'navbar-brand' %>
    </div>
    <div id="navbar">
      <ul class="nav navbar-nav">
        <li><%= link_to 'All posts', posts_path %></li>
        <li><%= link_to 'Your posts', user_posts_path %></li>
      </ul>
    </div>
  </div>
</nav>
[...] 
```

我们需要创建一个实际的范围。在 **application_policy.rb** 文件中有一个`Scope`类，它包含以下代码:

*策略/应用 _ 策略. rb*

```
class Scope
  attr_reader :user, :scope

  def initialize(user, scope)
    @user = user
    @scope = scope
  end

  def resolve
    scope
  end
end 
```

就像政策一样，有几件事需要考虑:

*   该类应该命名为`Scope`，并嵌套在您的策略类中。
*   传递给`initialize`方法的第一个参数是*用户*，就像策略一样。
*   第二个参数是*范围*(`ActiveRecord`或`ActiveRecord::Relation`或其他任何东西的实例)。
*   `Scope`类实现了一个名为`resolve`的方法，该方法返回一个可迭代的结果。

只需从基类继承并实现自己的`resolve`方法:

*policies/post_policy.rb*

```
class PostPolicy < ApplicationPolicy
  class Scope < Scope
    def resolve
      scope.where(user: user)
    end
  end
  [...]
end 
```

这个范围只是加载属于一个用户的所有文章。在控制器中使用这个新的作用域:

*posts_controller.rb*

```
[...]
def user_posts
  @posts = policy_scope(Post)
end
[...] 
```

您可能想从 *index* 视图中提取一些代码到片段中(并稍微修饰一下表格):

*views/posts/_ post . html . erb*

```
<tr>
  <td><%= post.title %></td>
  <td><%= post.body %></td>
  <td><%= link_to 'Show', post %></td>
  <td><%= link_to 'Edit', edit_post_path(post) %></td>
  <% if policy(post).destroy? %>
    <td><%= link_to 'Destroy', post, method: :delete, data: { confirm: 'Are you sure?' } %></td>
  <% end %>
</tr> 
```

*views/posts/_ list . html . erb*

```
<table class="table table-bordered table-striped table-condensed table-hover">
  <thead>
  <tr>
    <th>Title</th>
    <th>Body</th>
    <th colspan="3"></th>
  </tr>
  </thead>

  <tbody>
  <%= render @posts %>
  </tbody>
</table>

<br>

<%= link_to 'New Post', new_post_path %> 
```

*views/posts/index . html . erb*

```
<h1>Listing Posts</h1>

<%= render 'list' %> 
```

现在，只需创建一个新视图:

*views/posts/user _ posts . html . erb*

```
<h1>Your Posts</h1>

<%= render 'list' %> 
```

启动服务器并观察结果！

在视图中，您可以使用以下代码仅选择所需的记录:

```
<% policy_scope(@user.posts).each do |post| %>
<% end %> 
```

## 强制授权

如果你想检查授权是否在你的控制器中发生，使用`verify_authorized`作为 after 动作。您还可以选择要检查的操作:

*posts_controller.rb*

```
[...]
after_action :verify_authorized, only: [:destroy]
[...] 
```

同样可以确保进行范围界定:

*posts_controller.rb*

```
[...]
after_action :verify_policy_scoped, only: [:user_posts]
[...] 
```

但是，在某些情况下，执行授权检查可能是不合理的，所以您可能希望跳过它。例如，如果没有找到要销毁的记录，我们就返回，而不进行任何进一步的操作。为此使用了`skip_authorization`方法:

```
[...]
def destroy
  if @post.present?
    authorize @post
    @post.destroy
  else
    skip_authorization
  end

  redirect_to posts_url, notice: 'Post was successfully destroyed.'
end

private

def set_post
  @post = Post.find_by(id: params[:id])
end
[...] 
```

## 允许的参数

我们要讨论的最后一个特性是在 Pundit 的策略中定义允许参数的能力。请注意，您必须使用 Rails 4 或 Rails 3 和 [strong_params](https://github.com/rails/strong_parameters) gem 才能正常工作。

假设我们有一些只有管理员才能修改“特殊”参数。让我们添加相应的迁移:

```
$ rails g migration add_special_to_posts special:boolean 
```

稍微修改一下迁移:

*XXX _ add _ special _ to _ posts . Rb*

```
[...]
add_column :posts, :special, :boolean, default: false
[...] 
```

并应用它:

```
$ rake db:migrate 
```

现在，在您的策略中定义一个新方法:

*policies/post_policy.rb*

```
[...]
def permitted_attributes
  if user.admin?
    [:title, :body, :special]
  else
    [:title, :body]
  end
end
[...] 
```

因此，管理员可以设置所有属性，而用户只能修改`title`和`body`。最后，更新控制器方法:

*posts_controller.rb*

```
[...]
def create
  @post = Post.new
  @post.update_attributes(permitted_attributes(@post))

  if @post.save
    redirect_to @post, notice: 'Post was successfully created.'
  else
    render :new
  end
end

def update
  if @post.update(permitted_attributes(@post))
    redirect_to @post, notice: 'Post was successfully updated.'
  else
    render :edit
  end
end
[...] 
```

`permitted_attributes`是一个帮助器方法，它期望传递一个资源。在`create`方法中有一个小陷阱:你需要初始化`@post`，然后设置它的属性。如果您这样做:

```
@post = Post.new(permitted_attributes(@post)) 
```

将引发一个错误，因为您试图将一个不存在的对象传递给`permitted_attributes`。

您可以不使用`permitted_attributes`，而是坚持使用基本的`post_params`，并对其进行如下修改:

*posts_controller.rb*

```
[...]
def post_params
  params.require(:post).permit(policy(@post).permitted_attributes)
end
[...] 
```

最后，修改视图:

*views/posts/_ form . html . erb*

```
[...]
<div class="field">
  <%= f.label :special %><br>
  <%= f.check_box :special %>
</div>
[...] 
```

*views/posts/_ list . html . erb*

```
[...]
<thead>
<tr>
  <th>Title</th>
  <th>Body</th>
  <th>Special?</th>
  <th colspan="3"></th>
</tr>
</thead>
[...] 
```

*views/posts/_ post . html . erb*

```
<tr>
  <td><%= post.title %></td>
  <td><%= post.body %></td>
  <td><%= post.special? %></td>
  <td><%= link_to 'Show', post %></td>
  <td><%= link_to 'Edit', edit_post_path(post) %></td>
  <% if policy(post).destroy? %>
    <td><%= link_to 'Destroy', post, method: :delete, data: { confirm: 'Are you sure?' } %></td>
  <% end %>
</tr> 
```

现在启动服务器，并尝试将“特殊”参数设置为 true，而作为一个基本用户，您应该不能这样做。

## 结论

在本文中，我们讨论了 Pundit——一个使用基本 Ruby 类的伟大的授权解决方案。我们已经了解了它的大部分特性。我鼓励您浏览它的文档，因为您可能会对如何提供额外的上下文或[手动指定策略类](https://github.com/elabs/pundit#manually-specifying-policy-classes)感兴趣。

你以前用过 Pundit 吗？你会考虑在将来使用它吗？你认为它比康康康更方便还是只是一种不同的解决方案？请在评论中分享你的观点！

一如既往，我感谢你陪着我。如果你想让我谈一个话题，尽管开口。再见！

## 分享这篇文章