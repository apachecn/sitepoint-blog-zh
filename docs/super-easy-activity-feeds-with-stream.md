# 超级简单的活动源

> 原文：<https://www.sitepoint.com/super-easy-activity-feeds-with-stream/>

![stram](img/ccb2e7d8932dedd85c95c026ecb541ea.png)

以前我写过如何使用 [public_activity](https://github.com/chaps-io/public_activity) gem 在 Rails 中创建[活动提要。今天，我将向您介绍](https://www.sitepoint.com/activity-feeds-rails/)[流](https://getstream.io/)，这是一个提供 API 来轻松构建复杂的可伸缩提要的平台。我们将一起创建一个演示应用程序，并将其与 Stream 集成。

Stream 每月免费更新多达 300 万次。它支持多地区和实时更新。有针对 Ruby、Python、Javascript、PHP、Java、C#、Scala 和 Go 的客户端，也有针对 Rails、Django 和 Laravel 框架的[集成](https://getstream.io/docs/#frameworks)。

目前这个平台正在积极发展(最近[筹集了 175 万美元](http://techcrunch.com/2015/08/24/selling-news-feeds-as-a-service-stream-raises-1-75/))，你当然应该试一试。

在[sitepoint-stream.herokuapp.com](https://sitepoint-stream.herokuapp.com)有一个工作演示。

这篇文章的源代码可以在 [GitHub](https://github.com/bodrovis/Sitepoint-source/tree/master/Feeds_with_Stream) 上找到。

特别感谢 Tommaso Barbugli 为本文提供了有价值的信息。

## 溪流:过去和未来

Stream 平台由 Thierry Schellenbach 和 Tommaso Barbugli 创建，他们是开源 [Stream-Framework](https://github.com/tschellenbach/Stream-Framework) 的维护者，这是一个 Python 库，用于构建可扩展的新闻提要和活动流。他们指出，即使使用这个库，许多开发人员也发现很难准备所需的基础设施(需要 Redis 或 Cassandra、RabbitMQ 和 Python Celery)。此外，许多人要求一个 HTTP API 层，这样他们就可以在其他编程语言中使用它。创作 Stream 的想法就是这样诞生的。经过六个月的测试，第一个测试版发布了。

目前，该团队由蒂埃里、托马索和三名工程师组成(如果你有兴趣，Stream [正在招聘](https://getstream.io/careers/))。最近，他们得到了一笔不错的投资，所以这家公司的未来看起来非常光明。在不久的将来，该团队计划让向应用程序添加个性化提要变得更加容易。该方法是将以用户为中心的分析平台与机器学习相结合。

如果你想了解 Stream 背后的基本概念，花一点时间来玩这个[交互式教程](http://getstream.io/get_started/#intro)。此外，有一个已经构建好的 [Rails 演示应用](https://github.com/GetStream/Stream-Example-Rails)，你可以把它作为一个例子。

## 准备演示应用程序

正如我已经说过的，Stream 提供了三个框架的集成。当然，我们将选择我们最喜欢的 Rails，并使用 [stream-rails](https://github.com/GetStream/stream-rails) gem。在本文中，我将使用 Rails 4，但是 stream-rails [也可以使用 ActiveRecord 3。](https://github.com/GetStream/stream-rails/blob/master/stream_rails.gemspec#L22)

在继续之前，在这里注册——它是免费的(每月多达 300 万次更新)，然而[付费计划](http://getstream.io/pricing/)也是可用的。

继续创建一个没有默认测试套件的新 Rails 应用程序:

```
$ rails new FeedMe -T 
```

投入以下宝石:

*Gemfile*

```
[...]
gem 'stream_rails'
gem 'devise'
gem 'bootstrap-sass'
[...] 
```

然后跑

```
$ bundle install 
```

我将使用[design](https://github.com/plataformatec/devise/)来快速构建认证，但是如果您愿意，您可以使用另一种解决方案。(我最近报道了其中的一些，所以你有很多选项可以选择)。

让我们和我们的老朋友 Bootstrap 一起设计一下这个应用程序:

*application.scss*

```
@import 'bootstrap-sprockets';
@import 'bootstrap'; 
```

*layouts/application . html . erb*

```
[...]
<nav class="navbar navbar-inverse">
  <div class="container">
    <div class="navbar-header">
      <%= link_to 'FeedMe', root_path, class: 'navbar-brand' %>
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

### 证明

运行设计生成器来安装所有需要的文件并创建一个`User`模型:

```
$ rails g devise:install
$ rails g devise User 
```

让我们给每个用户一个名字:

```
$ rails g migration add_name_to_users name:string 
```

现在运行迁移:

```
$ rake db:migrate 
```

如果您正在使用受保护的属性，那么在注册时应该允许使用`name`属性:

*application _ controller . Rb*

```
[...]
before_action :configure_permitted_parameters, if: :devise_controller?

protected

def configure_permitted_parameters
  devise_parameter_sanitizer.for(:sign_up) << :name
end
[...] 
```

将设计视图复制到*视图*文件夹中进行定制:

```
$ rails g devise:views 
```

调整注册视图以包含`name`字段:

*views/device/registration/new . html . erb*

```
[...]
<div class="field">
  <%= f.label :name %><br />
  <%= f.text_field :name %>
</div>
[...] 
```

在我们处理视图的同时，我们还要更新布局以允许用户注销:

*layouts/application . html . erb*

```
[...]
<ul class="nav navbar-nav">
  <% if user_signed_in? %>
    <li>
      <a href="#" class="dropdown-toggle" data-toggle="dropdown">
        <i class="glyphicon"></i> <%= current_user.name %><b class="caret"></b>
      </a>
      <ul class="dropdown-menu">
        <li><%= link_to 'Log out', destroy_user_session_path, method: :delete %></li>
      </ul>
    </li>
  <% end %>
</ul>
[...] 
```

### 根页面

现在创建静态页面控制器，以及根页面的视图和相应的路由:

*pages_controller.rb*

```
class PagesController < ApplicationController
  before_action :authenticate_user!

  def index
  end
end 
```

`authenticate_user!`是由 Devise 提供的方法，如果用户未通过身份验证，该方法会将用户重定向到登录页面。

*config/routes.rb*

```
[...]
root to: 'pages#index'
[...] 
```

*views/pages/index . html . erb*

```
<div class="page-header"><h1>Welcome!</h1></div> 
```

太好了，现在是时候添加一些“项目”到我们的页面上了，这样我们的用户就可以像在 Pinterest 中一样将它们固定住。

### 添加项目

我们的项目看起来如何并不重要，因此，对于演示来说，它们将有一个标题和一条消息。然后，每个用户都可以锁定和取消锁定这些操作，有关这些操作的信息将显示在活动提要中。

首先，创建`Item`模型:

```
$ rails g model Item user:references title:string message:text
$ rake db:migrate 
```

在用户端设置一对多关联:

*型号/用户. rb*

```
[...]
has_many :items
[...] 
```

现在控制器:

*items_controller.rb*

```
class ItemsController < ApplicationController
  before_action :authenticate_user!

  def new
    @item = Item.new
  end

  def create
    @item = Item.new(item_params)
    @item.save
    flash[:success] = "Item created!"
    redirect_to root_path
  end

  private

  def item_params
    params.require(:item).permit(:message, :title)
  end
end 
```

路线:

*routes.rb*

```
[...]
resources :items, only: [:new, :create]
[...] 
```

和观点:

*views/items/new.html.erb*

```
<div class="page-header"><h1>New item</h1></div>

<%= form_for @item do |f| %>
  <div class="form-group">
    <%= f.label :title %>
    <%= f.text_field :title, class: 'form-control' %>
  </div>

  <div class="form-group">
    <%= f.label :message %>
    <%= f.text_area :message, class: 'form-control' %>
  </div>

  <%= f.submit 'Create', class: 'btn btn-primary' %>
<% end %> 
```

让我们也在根页面上显示项目列表

*pages_controller.rb*

```
[...]
def index
  @items = Item.order('created_at DESC')
end
[...] 
```

*views/pages/index . html . erb*

```
<div class="page-header"><h1>Welcome!</h1></div>

<%= render @items %> 
```

*views/items/_ item . html . erb*

```
<div class="well well-sm">
  <%= time_ago_in_words item.created_at %> ago
  <h3><%= item.title %></h3>
  <p><%= item.message %></p>
</div> 
```

太棒了。你可以手动创建一些项目，或者使用 *seeds.rb* 来自动完成这个任务
(当然，这是首选方式)。

## 集成流

集成 Stream 真的很容易。创建一个新的初始化文件:

*config/initializer/stream _ rails . Rb*

```
require 'stream_rails'

StreamRails.configure do |config|
  config.api_key     = ENV["STREAM_KEY"]
  config.api_secret  = ENV["STREAM_SECRET"]
  config.timeout     = 30
end 
```

要获得密钥对，您应该登录到 [getstream.io](http://getstream.io) 并打开您的仪表板。这里将为您创建一个应用程序(确保状态设置为“开”)，这是您可以获取密钥和秘密的地方。

我将密钥对存储在环境变量中，但是你可以使用另一种方法，只要确保这些密钥不能被公开访问。

## 固定和取消固定项目

Stream 将 ActiveRecord 模型作为活动存储在提要中。活动是基本上告诉谁在哪个对象上执行了哪个动作的对象。在最简单的情况下，活动由一个*参与者*、一个*动词*和一个*对象*组成。流[要求模型](https://github.com/GetStream/stream-rails#activity-fields)响应以下方法:

*   `activity_object`–返回活动的对象(例如，ActiveRecord 模型)。
*   `activity_actor`–返回执行活动的参与者(默认为`self.user`)。
*   `activity_verb`–返回动词的字符串表示(默认为模型的类名)。

首先，我们希望允许用户锁定和取消锁定项目。为此，我们需要一个单独的`Pin`模型
，配备 Stream 的方法:

```
$ rails g model Pin user:references item:references
$ rake db:migrate 
```

调整模型:

*型号/pin.rb*

```
[...]
belongs_to :user
belongs_to :item

validates :item, presence: true, uniqueness: {scope: :user}
validates :user, presence: true, uniqueness: {scope: :item}

include StreamRails::Activity
as_activity

def activity_object
  self.item
end
[...] 
```

`include StreamRails::Activity`和`as_activity`为`Pin`配备 Stream 的功能。注意，我没有使用`activity_actor`，因为默认值(`self.user`)非常适合我们。我们也将`activity_verb`保留为默认值`Pin`。

现在，让我们将锁定和取消锁定按钮添加到项目 partial:

*views/items/_ item . html . erb*

```
<div class="well well-sm">
  <%= time_ago_in_words item.created_at %> ago
  <h3><%= item.title %></h3>
  <p><%= item.message %></p>

  <%= render "pins/form", item: item %>
</div> 
```

下面是大头针的部分形式:

*views/pins/_ form . html . erb*

```
<% if item.user_pin(current_user) %>
  <%= button_to "Unpin", pin_path(item.user_pin(current_user)), method: :delete, class: "btn btn-primary btn-sm btn-danger" %>
<% else %>
  <%= form_for :pin, url: pins_path do |f| %>
    <input class="btn btn-primary btn-sm" type="submit" value="Pin">
    <%= f.hidden_field :item_id, value: item.id %>
  <% end %>
<% end %> 
```

如您所见，pinning 简单地意味着在提供项目和
用户 id 的同时在`pins`表中创建新记录。当然，取消固定意味着删除该记录。下面是相应的控制器:

*pins_controller.rb*

```
class PinsController < ApplicationController
  before_action :authenticate_user!

  def create
    @pin = Pin.new(pin_params)
    @pin.user = current_user
    @pin.save!
    flash[:success] = "Pinned!"
    redirect_to root_path
  end

  def destroy
    @pin = Pin.find(params[:id])
    @pin.destroy
    flash[:success] = "Unpinned!"
    redirect_to root_path
  end

  private

  def pin_params
    params.require(:pin).permit(:item_id)
  end
end 
```

只要我们调整了模型，这些动作就会被 Stream 监控。

最后，路线:

*routes.rb*

```
[...]
resources :pins, only: [:create, :destroy]
[...] 
```

我们的下一步是允许用户互相关注。只有被关注用户的操作才会显示在个性化订阅源中
。

## 追随和取消追随用户

关注也意味着创建一个简单的记录，告诉用户关注了谁:

```
$ rails g model Follow target_id:integer user_id:integer
$ rake db:migrate 
```

像这样修改迁移:

*db/migrations/XXX _ create _ follows . Rb*

```
class CreateFollows < ActiveRecord::Migration
  def change
    create_table :follows do |t|
      t.integer :target_id, index: true
      t.integer :user_id, index: true

      t.timestamps null: false
    end

    add_index :follows, [:target_id, :user_id], unique: true
  end
end 
```

调整`Follow`模型以添加流的功能并建立关联:

*models/follow.rb*

```
[...]
belongs_to :user
belongs_to :target, class_name: "User"

validates :target_id, presence: true
validates :user_id, presence: true

include StreamRails::Activity
as_activity

def activity_notify
  [StreamRails.feed_manager.get_notification_feed(self.target_id)]
end

def activity_object
  self.target
end
[...] 
```

这种方法

```
def activity_notify
  [StreamRails.feed_manager.get_notification_feed(self.target_id)]
end 
```

用于构建[通知源](https://github.com/GetStream/stream-rails#notification-feed)。这种类型的提要对于通知某些用户某个操作非常有用。在我们的例子中，我们通知用户有人跟踪了他们。

别忘了在另一边建立协会:

*型号/用户. rb*

```
[...]
has_many :follows

def followed_by(user = nil)
  user.follows.find_by(target_id: id)
end
[...] 
```

`followed_by`是一种检查用户是否关注某人的方法。我们很快就会用到它。

我们需要显示用户列表，并提供一个跟随/取消跟随按钮。下面是`UserController`:

*users_controller.rb*

```
class UsersController < ApplicationController
  def index
    @users = User.all
  end
end 
```

路线:

*routes.rb*

```
[...]
resources :users, only: [:index]
[...] 
```

和视图:

*views/users/index . html . erb*

```
<div class="page-header"><h1>Users</h1></div>

<%= render @users %> 
```

*views/users/_ user . html . erb*

```
<h3><%= user.name %></h3>

<% if user.followed_by(current_user) %>
  <%= button_to "Unfollow", follow_path(user.followed_by(current_user)), method: :delete, :class => "btn btn-primary btn-sm btn-danger" %>
<% else %>
  <%= form_for :follow, url: follows_path do |f| %>
    <%= f.hidden_field :target_id, value: user.id %>
    <input class="btn btn-primary btn-sm btn-default" type="submit" value="Follow">
  <% end %>
<% end %> 
```

这与我们的固定/取消固定功能非常相似。

让我们更新顶部菜单:

*layouts/application . html . erb*

```
[...]
<li><%= link_to 'Users', users_path %></li>
[...] 
```

当然，我们还需要一个控制器来管理以下内容:

*follows_controller.rb*

```
class FollowsController < ApplicationController
  before_action :authenticate_user!

  def create
    follow = Follow.new(follow_params)
    follow.user = current_user
    if follow.save
      StreamRails.feed_manager.follow_user(follow.user_id, follow.target_id)
    end
    flash[:success] = 'Followed!'
    redirect_to users_path
  end

  def destroy
    follow = Follow.find(params[:id])
    if follow.user_id == current_user.id
      follow.destroy!
      StreamRails.feed_manager.unfollow_user(follow.user_id, follow.target_id)
    end
    flash[:success] = 'Unfollowed!'
    redirect_to users_path
  end

  private

  def follow_params
    params.require(:follow).permit(:target_id)
  end
end 
```

`StreamRails.feed_manager.follow_user(follow.user_id, follow.target_id)`关注一个用户；必须提供参与者和目标的 id。`unfollow_user`的工作方式完全相同。

不要忘记设置路线:

*routes.rb*

```
[...]
resources :follows, only: [:create, :destroy]
[...] 
```

太好了，现在确保一切都正常工作。最后一步是显示实际的提要。

## 呈现提要

我们将呈现三个提要:

*   [用户的个人供稿](https://github.com/GetStream/stream-rails#user-feed)。顾名思义，这个提要显示某个用户的所有操作。
*   [平面新闻提要](http://getstream.io/docs/#flat-feeds)展示最近发生的事情。平面提要呈现没有任何分组的活动，这是流中的默认提要类型。
*   [聚合新闻提要](http://getstream.io/docs/#aggregated-feeds)允许用户指定聚合格式。我们将使用此提要分别显示 pin 和 follows。
*   [用户的通知订阅源](https://getstream.io/docs/#notification-feeds)类似于聚合订阅源，但是通知可以标记为已读，您可以获得未看到和未读通知的数量。

对于这些提要，将需要一个`FeedsController`。我将从用户的个人提要开始:

*feeds_controller.rb*

```
class FeedsController < ApplicationController
  before_action :authenticate_user!
  before_action :create_enricher

  def user
    @user = User.find(params[:id])
    feed = StreamRails.feed_manager.get_user_feed(@user.id)
    results = feed.get['results']
    @activities = @enricher.enrich_activities(results)
  end

  private

  def create_enricher
    @enricher = StreamRails::Enrich.new
  end
end 
```

使用这行代码`feed = StreamRails.feed_manager.get_user_feed(@user.id`我们可以访问用户的 feed。

那个`create_enricher`方法是什么？从提要中读取的原始数据如下所示:

```
{"actor": "User:1", "verb": "like", "object": "Item:42"} 
```

这种格式不能在模板中使用。因此，富集机制准备从提要中加载的数据，以便在模板中使用。在`create_enricher`中，我们实例化了`StreamRails::Enrich`类，然后简单地使用`enrich_activities`来准备我们的数据。

以下是路线:

*routes.rb*

```
[...]
scope path: '/feeds', controller: :feeds, as: 'feed' do
  get 'user/:id', to: :user, as: :user
end
[...] 
```

和观点:

*views/feeds/user . html . erb*

```
<div class="page-header"><h1>My feed</h1></div>

<% for activity in @activities %>
  <%= render_activity activity %>
<% end %> 
```

`render_activity`是模板中使用的另一个[特殊方法](https://github.com/GetStream/stream-rails#templating)。该方法期望接收丰富的数据，并且将在*活动*(对于平面提要)或*聚合 _ 活动*(对于聚合提要)文件夹中查找片段。部分应该以动作的动词命名(固定、跟随等。)

*视图/活动/_follow.html.erb*

```
<div class="well well-sm">
  <p><%= time_ago_in_words activity['time'] %> ago</p>

  <p><strong><%= activity['object'].name %></strong> and <strong><%= activity['actor'].name %></strong> are now friends</p>
</div> 
```

`render_activity`自动发送`activity`到局部的局部范围。这里我们只是访问对象和目标的名字。我们可以调用`activity['object'].name`,因为`activity['object']`返回了`User`类的一个实例。

*views/activity/_ pin . html . erb*

```
<div class="well well-sm">
  <p><%= time_ago_in_words activity['time'] %> ago</p>
  <p>
    <strong><%= activity['actor'].name %></strong> pinned
    <strong><%= activity['object'].title %></strong>
  </p>
</div> 
```

这里的过程是相同的:我们显示谁钉住了哪个项目。

现在在顶部菜单中提供一个链接来访问这个新创建的提要:

*layouts/application . html . erb*

```
[...]
<ul class="dropdown-menu">
  <li><%= link_to 'My feed', feed_user_path(current_user) %></li>
  [...]
</ul>
[...] 
```

此外，修改用户部分:

*views/users/_ user . html . erb*

```
<h3><%= link_to user.name, feed_user_path(user) %></h3>
[...] 
```

现在，让我们添加平面提要:

*feeds_controller.rb*

```
[...]
def flat
  feed = StreamRails.feed_manager.get_news_feeds(current_user.id)[:flat]
  results = feed.get['results']
  @activities = @enricher.enrich_activities(results)
end
[...] 
```

`feed = StreamRails.feed_manager.get_news_feeds(current_user.id)`访问新闻订阅源，而`[:flat]`表示应该获取平面订阅源。

*views/feeds/flat . html . erb*

```
<div class="page-header"><h1>Flat feed</h1></div>

<% for activity in @activities %>
  <%= render_activity activity %>
<% end %> 
```

我们再次将丰富的数据传递给`render_activity`。只要我们已经创建了*活动*文件夹和其中的片段，我们就可以继续聚合提要。

*feeds_controller.rb*

```
[...]
def aggregated
  feed = StreamRails.feed_manager.get_news_feeds(current_user.id)[:aggregated]
  results = feed.get['results']
  @activities = @enricher.enrich_aggregated_activities(results)
end
[...] 
```

这次是`[:aggregated]`而不是`[:flat]`。

*views/feeds/aggregated . html . erb*

```
<div class="page-header"><h1>Aggregated feed</h1></div>

<% for activity in @activities %>
  <%= render_activity activity %>
<% end %> 
```

这里，要求在 *aggregated_activity* 文件夹中有单独的部分:

*views/aggregated _ activity/_ pin . html . erb*

```
<% if activity['actor_count'] == 1 %>
  <%= activity['activities'][0]['actor'].name %> pinned <%= pluralize(activity['activity_count'], 'item') %>
<% elsif activity['actor_count'] == 2 %>
  <%= activity['activities'][0]['actor'].name %> and <%= activity['activities'][1]['actor'].name %> pinned <%= activity['activity_count'] %> items
<% else %>
  <%= activity['activities'][0]['actor'].name %>, <%= activity['activities'][1]['actor'].name %> and <%= activity['actor_count'].name - 2 %> more pinned <%= activity['activity_count'] %> items
<% end %>

<div class="pull-right">
  <i class="glyphicon glyphicon-time"></i> <%= time_ago_in_words(activity['updated_at']) %> ago
</div>

<% for activity in activity['activities'] %>
  <%= render_activity activity %>
<% end %> 
```

`for activity in activity['activities']`逐个处理每个活动，`render_activity activity`在我们最近创建的*活动*文件夹中使用相同的片段。请注意，您可以向该方法传递其他参数，以便选择其他部分，例如:

```
<%= render_activity activity, :prefix => "aggregated_" %> 
```

这将寻找前缀为`aggregated_`的部分。

*views/aggregated _ activity/_ follow . html . erb*

```
<i class="glyphicon glyphicon-time"></i> <%= time_ago_in_words(activity['updated_at']) %> ago

<% for activity in activity['activities'] %>
  <%= render_activity activity %>
<% end %> 
```

最后添加通知源:

*feeds_controller.rb*

```
[...]
def notification
  feed = StreamRails.feed_manager.get_notification_feed(current_user.id)
  results = feed.get['results']
  @activities = @enricher.enrich_aggregated_activities(results)
end
[...] 
```

*views/feeds/notification . html . erb*

```
<div class="page-header"><h1>Your notification feed</h1></div>

<% for activity in @activities %>
  <%= render_activity activity %>
<% end %> 
```

设置路线:

*routes.rb*

```
[...]
scope path: '/feeds', controller: :feeds, as: 'feed' do
  get 'me', to: :user
  get 'flat', to: :flat
  get 'aggregated', to: :aggregated
  get 'notification', to: :notification
end
[...] 
```

此外，更新顶部菜单:

*layouts/application . html . erb*

```
[...]
<li><%= link_to 'Flat feed', feed_flat_path %></li>
<li><%= link_to 'Aggregated feed', feed_aggregated_path %></li>
<li><%= link_to 'Notification feed', feed_notification_path %></li>
[...] 
```

现在启动您的服务器，检查这一切是如何工作的！只是不要忘记，您必须跟踪用户才能查看他的操作(您也可以跟踪自己)。

## 结论

在本文中，我们讨论了 Stream，这是一个可以轻松构建可伸缩活动提要的平台。请随意浏览它的文档并进一步试验。

你试过用 Stream 吗？你会考虑在将来使用它吗？请在评论中分享你的观点。谢谢你和我呆在一起，再见！

## 分享这篇文章