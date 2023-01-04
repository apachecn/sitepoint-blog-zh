# 使用 PaperTrail 进行版本控制

> 原文：<https://www.sitepoint.com/versioning-papertrail/>

![arrow sign reload refresh rotation](img/c2120954673727289e5384046b6dbc20.png)

想象一下这种情况:你打开网站的管理页面做一些清理工作，发现一些很久没人浏览的旧数据，然后删除了它。删除成功，一切正常…但是一秒钟后…“不！！！这些数据包含了可能改变世界的非常重要的信息！”你会意识到。但是，它已经过去了，世界仍然没有改变(如果您有数据库的备份，仍然有机会恢复)。

我们能在我们的 Rails 应用程序中防止这种情况吗？是的，我们可以！`paper_trail`来救援了！

在本文中，我们将讨论如何在`paper_trail` gem 的帮助下实现一个“历史”页面和一个“撤销”按钮(以及“重做”)。

源代码可以在 [GitHub](https://github.com/bodrovis/SitePointVersioningUndo) 上获得。

工作演示可在[http://undoer.radiant-wind.com/](http://undoer.radiant-wind.com/)获得。

## 准备演示项目

对于这个演示，我将使用 Rails 4.0.4，但同样的解决方案也可以用 Rails 3 来实现。

我们将构建一个简单的博客，允许注册用户添加、更新和销毁帖子。用户将能够撤销与帖子相关的每个操作(例如，撤销无意的删除)。我们还将提供一个“历史”页面，显示用户在处理帖子时执行的操作列表(以及一些其他信息)。

好了，该开始了。通过运行以下命令，创建一个没有默认测试套件的新 Rails 应用程序:

```
$ rails new undoer -T
```

以下是我们将要使用的宝石列表:

*Gemfile*

```
gem 'paper_trail', '~> 3.0.1'
gem 'bootstrap-sass', '~> 3.1.1.0'
gem 'devise'
```

`bootstrap-sass`包括 Twitter Bootstrap，我们的老朋友，它将帮助我们设计项目，而`devise`将用于设置一些非常基本的认证。

由 Andy Stewart 创建的`paper_trail`，是本文的主角——它将帮助我们创建“历史”页面和“撤销”按钮。还有一些其他的 gem 提供版本控制，但是我发现`paper_trail`是最方便的。以前，我用的是集体创意的[审计](https://github.com/collectiveidea/audited)，但是它并不那么有用，而且已经 8 个月没有更新了。此外，它与 Rails 4 的兼容性也有问题。

演示应用程序有一个控制器(除了`ApplicationController`)`PostsController`，它将用于管理我们的帖子。最初，它有七个默认方法:`index`、`new`、`create`、`destroy`、`edit`和`update`，以及一些与它们相关的视图。我不会深入讨论如何创建这些方法和视图的细节，因为它们是非常基本的(为了简洁，您可以使用`rails g scaffold Posts`来创建它们)。

演示还包含两个模型:`Post`和`User`。`posts`表包含以下列:

*   `id`
*   `title`
*   `body`
*   `created_at`
*   `updated_at`

`User`模型由 Devise 生成，包含电子邮件和一些其他特殊字段。同样，我不会深入介绍设置 Devise 的细节，因为这与本文的主题无关。你可以使用以下指南开始:[https://github.com/plataformatec/devise#getting-started](https://github.com/plataformatec/devise#getting-started)。此外，请随意使用任何身份验证方法。

路由文件如下所示:

*routes.rb*

```
devise_for :users
root to: 'posts#index'
resources :posts
```

此时，我们已经准备好设置`paper_trail`。首先，创建一个特殊的表来存储版本(这是`paper_trail`的一个很酷的特性——如果我们想以后清除旧版本，我们只需要访问一个表)。

运行这些命令:

```
$ bundle exec rails generate paper_trail:install
$ bundle exec rake db:migrate
```

这将创建并应用所需的迁移。现在，将下面一行添加到您的`Post`模型:

*车型/后 rb*

```
class Post < ActiveRecord::Base
  has_paper_trail
end
```

仅此而已！现在，对`posts`表的所有更改都将被自动审计。多酷啊。

您还可以指定不应跟踪哪些更改。例如，如果`posts`在中有一个`view_count`列，而我们不想跟踪对它所做的更改，那么修改看起来就像:

*车型/后 rb*

```
has_paper_trail ignore: [:view_count]
```

有些字段可以完全跳过，这意味着它们既不会被跟踪，也不会包含在对象的序列化版本中):

*车型/后 rb*

```
has_paper_trail skip: [:view_count]
```

也可以指定事件:

*车型/后 rb*

```
has_paper_trail on: [:update, :create]
```

或者提供是否跟踪事件的条件:

*车型/后 rb*

```
has_paper_trail if: Proc.new { |t| t.title.length > 10 },
                unless: Proc.new { |t| t.body.blank? }
```

简单而强大。

## 显示日志

现在，您可能想要显示审计过的版本，嗯？很简单，只需添加以下路线:

*routes.rb*

```
[...]
get '/posts/history', to: 'posts#history', as: :posts_history
resources :posts
```

到历史页面查看帖子。如果您的应用程序有许多正在被审计的模型，那么创建一个单独的`VersionsController`并将所有版本相关的方法放在那里是一个好主意。然而，在我们的例子中，只有`Post`模型被审计，所以让我们坚持使用一个控制器。

向控制器添加新方法:

*controllers/posts _ controller . Rb*

```
def history
  @versions = PaperTrail::Version.order('created_at DESC')
end
```

注意，我们必须使用`PaperTrail::Version`，而不仅仅是`Version`。这行代码从我们之前创建的`versions`表中提取所有记录的事件，并按照创建日期对它们进行排序。在一个真正的应用程序中，使用`will_paginate`或`kaminari` gem 对这些事件进行分页是明智的。

渲染事件:

*posts/history.html.erb*

```
<div class="container">
  <h1>History</h1>

  <ul>
    <% @versions.each do |version| %>
      <li>
        <%= l(version.created_at, format: "%-d.%m.%Y %H:%M:%S %Z") %><br/>
        Event ID: <%= version.id %><br/>
        <b>Target:</b> <%= version.item_type %>
        (id: <%= version.item_id %>); <b>action</b> <%= version.event %>;<br/>
        <div>
          More info:
          <pre><%= version.object %></pre>
        </div>
      </li>
    <% end %>
  </ul>
</div>
```

以下是显示的数据:

*   这件事发生的时间。
*   `version.id`–该事件的 ID。
*   `version.item_type`–事件的型号名称。在我们的例子中，是`Post`。
*   `version.item_id`–被改变的资源(Post)的 ID。
*   `version.event`–应用于资源的操作(创建、更新、销毁)。
*   `version.object`–完全转储已更改的资源。

到目前为止，一切顺利。尽管如此，仍有一些事情可以改进。例如，更改了哪些字段(尤其是更新操作)？这很容易实现。

创建并应用以下迁移:

```
$ rails g migration add_object_changes_to_versions object_changes:text
$ rake db:migrate
```

这也可以在设置`paper_trail`时完成。您只需要为生成器提供一个合适的选项:

```
$ rails generate paper_trail:install --with-changes
```

无需进一步操作，`paper_trail`将自动区分版本。

现在，我们可以向视图添加一个新的`div`块:

*posts/history.html.erb*

```
[...]
<div>
  Changeset:
  <pre><%= version.changeset %></pre>
</div>
[...]
```

这将显示事件前后的属性值(如果属性保持不变，则不会显示)。

## 跟踪用户特定信息

好了，现在我们知道了我们珍贵的博文被删除的*时间*。但是，我们不知道*是谁*！是时候解决这个问题了。

让我们跟踪负责该操作的用户的 IP 地址。当然，IP 地址是可以伪造的，但是这里的要点是解释如何将元数据与事件数据一起存储。继续创建新的迁移:

```
$ rails g migration add_ip_to_versions ip:string
$ rake db:migrate
```

默认情况下，`paper_trail`不会在`ip`列中存储任何东西，所以我们需要帮助一下。将此方法添加到`ApplicationController`:

*控制器/应用 _ 控制器. rb*

```
def info_for_paper_trail
  # Save additional info
  { ip: request.remote_ip }
end
```

将使用此方法获取一些附加信息，并将其存储为元数据。如果你使用 Rails 3 或者 Rails 4 的`protected_attributes`,你还需要创建一个初始化器:

*initializer/paper _ trail . Rb*

```
module PaperTrail
  class Version < ActiveRecord::Base
    attr_accessible :ip
  end
end
```

元数据也可以像这样在模型中提供(假设我们有一个`timestamp`列):

*车型/后 rb*

```
has_paper_trail meta: { timestamp: Time.now }
```

最后要做的是在视图中添加一行:

*posts/history.html.erb*

```
[...]
<b>Remote address:</b> <%= version.ip %><br/>
[...]
```

存储 IP 很好，但是请记住，我们已经建立了一个基本的身份验证系统——难道我们不能保存负责该操作的用户吗？那就太方便了！当然，我们也可以这样做。

这一次，我们不需要对`versions`表应用迁移，因为它已经包含了一个用于存储用户信息的`whodunnit`列。我们需要做的就是在`ApplicationController`中创建另一个方法:

*控制器/应用 _ 控制器. rb*

```
[...]
def user_for_paper_trail
  # Save the user responsible for the action
  user_signed_in? ? current_user.id : 'Guest'
end
[...]
```

`user_signed_in?`方法是由 Devise 提供的——基本上，它告诉用户是否登录。`current_user`也是由 Devise 定义的，它返回当前登录的用户作为资源(如果用户没有登录，它返回`nil`)。)这样，我们可以很容易地获取当前用户的`id`。目前，只有经过认证的用户才能管理我们博客中的帖子，但这种情况可能会改变。对于未登录的情况，我们指定“Guest”。

最后要做的是显示这个新信息:

*posts/history.html.erb*

```
[...]
<b>User:</b>
<% if version.whodunnit && version.whodunnit != 'Guest' %>
  <% user = User.find_by_id(version.whodunnit) %>
  <% if user %>
    <%= user.email %>
    (last seen at <%= l(user.last_sign_in_at, format: "%-d.%m.%Y %H:%M:%S") %>)
  <% end %>
<% else %>
  Guest
<% end %>
[...]
```

我们的“历史”页面现在提供了一些非常有用的信息。我们可以跟踪事件发生的时间，发生了什么变化，资源在变化前是什么样子，以及谁对这种变化负责。厉害！

## 撤销操作

让我们转到第二部分，允许用户撤销他们的操作。

为此，创建一个新方法来撤消所请求的操作:

*controllers/posts _ controller . Rb*

```
def undo
  @post_version = PaperTrail::Version.find_by_id(params[:id])

  begin
    if @post_version.reify
      @post_version.reify.save
    else
      # For undoing the create action
      @post_version.item.destroy
    end
    flash[:success] = "Undid that!"
  rescue
    flash[:alert] = "Failed undoing the action..."
  ensure
    redirect_to root_path
  end
end
```

上面，通过 id 找到一个版本(我们稍后会生成一个适当的链接)。然后，使用`reify`方法检查资源是否有以前的版本。如果资源是在当前版本中创建的，这个方法将返回`nil`(显然，如果资源是刚刚创建的，它没有任何以前的版本。)要么使用`@post_version.reify.save`回滚到以前的版本，要么使用`@post_version.item.destroy`销毁新创建的资源(`@post_version.item`返回实际的资源)。很简单，不是吗？

当然，我们需要增加一条新路线:

*routes.rb*

```
[...]
post '/posts/:id/undo', to: 'posts#undo', as: :undo
resources :posts
[...]
```

在用户对帖子做了一些事情后，应该向用户显示一个撤销操作的链接。最简单的方法是将此链接放入 flash 消息中，因此请确保将其呈现在您的布局中的某个位置:

*layouts/application . html . erb*

```
<div class="container">
  <% flash.each do |key, value| %>
    <div class="alert alert-<%= key %>">
      <button type="button" class="close" data-dismiss="alert">&times;</button>
      <%= value.html_safe %>
    </div>
  <% end %>
</div>
```

注意`html_safe`的使用——这是必需的，因为否则，我们的链接将呈现为原始文本。

在生成撤销链接的`PostsController`中创建一个私有方法:

*controllers/posts _ controller . Rb*

```
[...]
private

def make_undo_link
  view_context.link_to 'Undo that plz!', undo_path(@post.versions.last), method: :post
end
```

我们不能在控制器内部使用`link_to`，所以对`view_context`的引用指向实际视图。`@post.versions`获取`Post`资源的所有版本，`@post.versions.last`获取最新版本。这种方法可以这样使用:

*controllers/posts _ controller . Rb*

```
[...]
def update
  @post = Post.find_by_id(params[:id])
  if @post.update_attributes(post_params)
    flash[:success] = "Post was updated! #{make_undo_link}"
    redirect_to post_path(@post)
  else
    render 'edit'
  end
end
[...]
```

一定要将它添加到`create`和`destroy`方法中。

请在演示应用程序中尝试一下。注意，`paper_trail`也在跟踪动作的撤销。

## 阅读一个动作

好的，我已经撤消了一个操作…但是现在我想重做它。砰。这里，我们应该引入一个恢复撤销的重做链接。只需要很少的修改。

创建另一个私有方法:

*controllers/posts _ controller . Rb*

```
[...]
private

def make_redo_link
  params[:redo] == "true" ? link = "Undo that plz!" : link = "Redo that plz!"
  view_context.link_to link, undo_path(@post_version.next, redo: !params[:redo]), method: :post
end
[...]
```

这个方法和`make_undo_link`很像。主要区别在于`params[:redo]`是`false`的`true`。基于这个参数，改变链接的文本 URL 实际上保持不变。这是因为重做基本上意味着恢复到以前的版本，这与撤消操作完全相同。

更改`undo`方法中的 flash 消息:

*controllers/posts _ controller . Rb*

```
[...]
def undo
[...]
  flash[:success] = "Undid that! #{make_redo_link}"
[...]
```

仅此而已！用户可以任意多次撤销和重做他们的操作，每次都被`paper_trail`记录下来。

这里唯一的问题是`versions`桌子会很快变胖。这可能需要通过一些后台进程来删除旧条目。这项工作将使用类似于:

```
PaperTrail::Version.delete_all ["created_at < ?", 1.week.ago]
```

您还可以通过将这一行放到初始化器中来限制每个对象创建的版本数:

```
PaperTrail.config.version_limit = 3
```

## 结论

这就把我们带到了文章的结尾。记住，你可以用`paper_trail`做比我们讨论的更多的事情。例如，您可以在给定的时间(`@post.version_at(1.day.ago)`)获取版本，或者在版本之间导航(`@post.previous_version`、`@post.next_version`)，或者使用模型关联。您甚至可以创建一个系统来区分资源的两个任意版本(类似于在基于 wiki 的网站上实现的区分系统)。

我希望这篇文章对你有用并且有趣。如果没有，我可以重做(呻吟)…

## 分享这篇文章