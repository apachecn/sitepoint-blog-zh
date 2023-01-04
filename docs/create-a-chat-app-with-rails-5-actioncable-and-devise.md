# 使用 Rails 5、ActionCable 和 Devise 创建一个聊天应用程序

> 原文：<https://www.sitepoint.com/create-a-chat-app-with-rails-5-actioncable-and-devise/>

![](img/1eeace00232304afaa145033baf44992.png)

Rails 5 引入了一系列新的强大功能，但其中最令人期待的当然是 [ActionCable](https://github.com/rails/rails/tree/master/actioncable) 。ActionCable 将 WebSockets 无缝集成到您的应用程序中，并提供客户端 JS 和服务器端 Ruby 框架。通过这种方式，您可以用与应用程序其余部分相同的风格编写实时功能，这真的很酷。

通过我们的教程[在 SitePoint 上模拟用户行为并测试你的 ruby 应用](https://www.sitepoint.com/premium/screencasts/feature-tests-with-rspec-simulate-user-behavior-and-test-your-ruby-app)，了解更多关于 Ruby 的知识。

几个月前，我写了一系列文章，描述了如何用 Rails 构建迷你聊天，包括使用 AJAX、Faye 驱动的 WebSockets 和服务器发送的事件。这些文章获得了一些关注，所以我决定撰写本系列的一个新部分，指导如何使用 ActionCable 来实现相同的目标。

然而，这一次，我们将面临一个稍微复杂一点的任务，并讨论以下主题:

*   准备应用程序和集成设备
*   聊天室简介
*   设置 ActionCable
*   客户端编码
*   借助后台作业编写服务器端代码
*   ActionCable 基本授权简介
*   准备要部署到 Heroku 的应用程序

源代码可以在 [GitHub](https://github.com/bodrovis/Sitepoint-source/tree/master/Chat_with_ActionCable_and_Devise) 找到。

工作演示可在 sitepoint-actioncable.herokuapp.com 获得。

## 准备应用程序

首先创建一个新的应用程序。对 ActionCable 的支持仅在 Rails 5 中添加，因此您必须使用该版本(目前 5.0.0.rc1 是最新版本):

```
$ rails new CableChat -T 
```

现在添加一些宝石:

*Gemfile*

```
[...]
gem 'devise'
gem 'bootstrap', '~> 4.0.0.alpha3'
[...] 
```

[设计](https://github.com/plataformatec/devise)将用于认证和授权(你可以阅读[这篇文章](https://www.sitepoint.com/devise-authentication-in-depth/)以了解更多信息)和[引导 4](https://github.com/twbs/bootstrap-rubygem)-用于造型。

奔跑

```
$ bundle install 
```

添加引导程序的样式:

*样式表/应用程序. scss*

```
@import "bootstrap"; 
```

运行以下命令来安装 Devise，生成一个新的`User`模型，并复制视图用于进一步定制:

```
$ rails generate devise:install
$ rails generate devise User
$ rails generate devise:views
$ rails db:migrate 
```

现在，只允许经过身份验证的用户访问网站的所有页面:

*application _ controller . Rb*

```
[...]
before_action :authenticate_user!
[...] 
```

### 闲聊室

下一步是添加对聊天室的支持，因此生成以下模型:

```
$ rails g model ChatRoom title:string user:references
$ rails db:migrate 
```

聊天室应该有一个创建者，所以确保在`chat_rooms`和`users`之间建立一对多的关系:

*models/chat_room.rb*

```
[...]
belongs_to :user
[...] 
```

*型号/用户. rb*

```
[...]
has_many :chat_rooms, dependent: :destroy
[...] 
```

编写一个控制器来列出和创建聊天室:

*聊天室控制器 rb*

```
class ChatRoomsController < ApplicationController
  def index
    @chat_rooms = ChatRoom.all
  end

  def new
    @chat_room = ChatRoom.new
  end

  def create
    @chat_room = current_user.chat_rooms.build(chat_room_params)
    if @chat_room.save
      flash[:success] = 'Chat room added!'
      redirect_to chat_rooms_path
    else
      render 'new'
    end
  end

  private

  def chat_room_params
    params.require(:chat_room).permit(:title)
  end
end 
```

现在有一些非常简单的观点:

*views/chat _ rooms/index . html . erb*

```
<h1>Chat rooms</h1>

<p class="lead"><%= link_to 'New chat room', new_chat_room_path, class: 'btn btn-primary' %></p>

<ul>
  <%= render @chat_rooms %>
</ul> 
```

*views/chat _ rooms/_ chat _ room . html . erb*

```
<li><%= link_to "Enter #{chat_room.title}", chat_room_path(chat_room) %></li> 
```

*views/chat _ rooms/new . html . erb*

```
<h1>Add chat room</h1>

<%= form_for @chat_room do |f| %>
  <div class="form-group">
    <%= f.label :title %>
    <%= f.text_field :title, autofocus: true, class: 'form-control' %>
  </div>

  <%= f.submit "Add!", class: 'btn btn-primary' %>
<% end %> 
```

### 信息

当然，我们应用程序的主要亮点是聊天信息。它应该属于用户和聊天室。为此，运行以下命令:

```
$ rails g model Message body:text user:references chat_room:references
$ rails db:migrate 
```

确保建立适当的关系:

*models/chat_room.rb*

```
[...]
belongs_to :user
has_many :messages, dependent: :destroy
[...] 
```

*型号/用户. rb*

```
[...]
has_many :chat_rooms, dependent: :destroy
has_many :messages, dependent: :destroy
[...] 
```

*models/message.rb*

```
[...]
belongs_to :user
belongs_to :chat_room
[...] 
```

到目前为止，一切顺利。当用户进入聊天室时应该显示消息，所以创建一个新的`show`动作:

*聊天室控制器 rb*

```
[...]
def show
  @chat_room = ChatRoom.includes(:messages).find_by(id: params[:id])
end
[...] 
```

注意这里的[包括用于快速加载的](http://api.rubyonrails.org/classes/ActiveRecord/QueryMethods.html#method-i-includes)方法。

现在的观点是:

*views/chat _ rooms/show . html . erb*

```
<h1><%= @chat_room.title %></h1>

<div id="messages">
  <%= render @chat_room.messages %>
</div> 
```

*views/messages/_ message . html . erb*

```
<div class="card">
  <div class="card-block">
    <div class="row">
      <div class="col-md-1">
        <%= gravatar_for message.user %>
      </div>
      <div class="col-md-11">
        <p class="card-text">
          <span class="text-muted"><%= message.user.name %> at <%= message.timestamp %> says</span><br>
          <%= message.body %>
        </p>
      </div>
    </div>
  </div>
</div> 
```

在这部分中采用了三种新方法:`user.name`、`message.timestamp`和`gravatar_for`。要构建一个名称，让我们简单地从用户的电子邮件中去掉域名部分(当然，在一个真实的应用程序中，您会希望允许他们在注册时或在“编辑个人资料”页面输入名称):

*型号/用户. rb*

```
[...]
def name
  email.split('@')[0]
end
[...] 
```

`timestamp`依靠`strftime`以用户友好的格式显示消息的创建日期:

*models/message.rb*

```
[...]
def timestamp
  created_at.strftime('%H:%M:%S %d %B %Y')
end
[...] 
```

`gravatar_for`是显示用户 gravatar 的助手:

*application_helper.rb*

```
module ApplicationHelper
  def gravatar_for(user, opts = {})
    opts[:alt] = user.name
    image_tag "https://www.gravatar.com/avatar/#{Digest::MD5.hexdigest(user.email)}?s=#{opts.delete(:size) { 40 }}",
              opts
  end
end 
```

这里要做的最后两件事是对消息容器进行一点样式化:

```
#messages {
  max-height: 450px;
  overflow-y: auto;
  .avatar {
    margin: 0.5rem;
  }
} 
```

添加路线:

*config/routes.rb*

```
[...]
resources :chat_rooms, only: [:new, :create, :show, :index]
root 'chat_rooms#index'
[...] 
```

最后，准备工作已经完成，我们可以对聊天的核心功能进行编码了。

## 添加操作电缆

### 客户端

在继续之前，如果您的计算机上还没有 Redis，请安装它。Redis 可用于 [nix](http://redis.io/download) ，通过[家酿](http://brew.io)，也可用于 [Windows](https://github.com/MSOpenTech/redis) 。

接下来，调整 gem 文件:

*Gemfile*

```
[...]
gem 'redis', '~> 3.2'
[...] 
```

然后跑

```
$ bundle install 
```

现在您可以修改 *config/cable.yml* 文件来使用 Redis 作为适配器:

*config/cable.yml*

```
[...]
adapter: redis
url: YOUR_URL
[...] 
```

或者干脆用`adapter: async`(默认值)。

此外，修改您的 *routes.rb* 以在某个 URL 上挂载 ActionCable:

*config/routes.rb*

```
[...]
mount ActionCable.server => '/cable'
[...] 
```

检查在 *javascripts* 目录下有一个 *cable.js* 文件，内容如下:

*javascripts/cable.js*

```
//= require action_cable
//= require_self
//= require_tree ./channels

(function() {
  this.App || (this.App = {});

  App.cable = ActionCable.createConsumer();

}).call(this); 
```

该文件必须在 *application.js* 中:

*JavaScript s/application . js*

```
[...]
//= require cable
[...] 
```

`Consumer`是一个 web socket 连接的客户端，可以订阅一个或多个通道。每个 ActionCable 服务器可以处理多个连接。`Channel`类似于 MVC 控制器，用于流式传输。你可以在这里阅读更多关于 ActionCable 的术语。

因此，让我们创建一个新的渠道:

*JavaScript/频道/房间.咖啡*

```
App.global_chat = App.cable.subscriptions.create {
    channel: "ChatRoomsChannel"
    chat_room_id: ''
  },
  connected: ->
    # Called when the subscription is ready for use on the server

  disconnected: ->
    # Called when the subscription has been terminated by the server

  received: (data) ->
    # Data received

  send_message: (message, chat_room_id) ->
    @perform 'send_message', message: message, chat_room_id: chat_room_id 
```

这里，我们基本上为消费者订阅了`ChatRoomsChannel`并传递了当前房间的 id(此时我们并没有真正传递任何东西，但很快就会修复)。订阅有许多自我解释的回调:`connected`、`disconnected`和`received`。此外，订阅定义了主函数(`send_message`)，该函数调用与服务器同名的方法，并将必要的数据传递给该方法。

当然，我们需要一个允许用户发送消息的表单:

*views/chat _ rooms/show . html . erb*

```
<%= form_for @message, url: '#' do |f| %>
  <div class="form-group">
    <%= f.label :body %>
    <%= f.text_area :body, class: 'form-control' %>
    From 2 to 1000 characters
  </div>

  <%= f.submit "Post", class: 'btn btn-primary btn-lg' %>
<% end %> 
```

`@message`实例变量应该设置在控制器内部:

*聊天室控制器 rb*

```
[...]
def show
  @chat_room = ChatRoom.includes(:messages).find_by(id: params[:id])
  @message = Message.new
end
[...] 
```

当然，您可能会使用基本的`form`标记，而不是依赖 Rails 表单生成器，但是这允许我们以后利用 I18n 翻译之类的东西。

让我们也为消息添加一些验证:

*models/message.rb*

```
[...]
validates :body, presence: true, length: {minimum: 2, maximum: 1000}
[...] 
```

这里要解决的另一个问题是为我们的脚本提供房间的 id。让我们借助 HTML `data-`属性来求解:

*views/chat _ rooms/show . html . erb*

```
[...]
<div id="messages" data-chat-room-id="<%= @chat_room.id %>">
  <%= render @chat_room.messages %>
</div>
[...] 
```

准备就绪后，我们可以在脚本中使用房间的 id:

*JavaScript/频道/房间.咖啡*

```
jQuery(document).on 'turbolinks:load', ->
  messages = $('#messages')
  if $('#messages').length > 0

    App.global_chat = App.cable.subscriptions.create {
        channel: "ChatRoomsChannel"
        chat_room_id: messages.data('chat-room-id')
      },
      connected: ->
        # Called when the subscription is ready for use on the server

      disconnected: ->
        # Called when the subscription has been terminated by the server

      received: (data) ->
        # Data received

      send_message: (message, chat_room_id) ->
        @perform 'send_message', message: message, chat_room_id: chat_room_id 
```

注意`jQuery(document).on 'turbolinks:load'`部分。只有当你使用支持这一新活动的 [Turbolinks](https://github.com/turbolinks/turbolinks-classic) 5 时，才应该这样做。你可能会考虑使用 [jquery-turbolinks](https://rubygems.org/gems/jquery-turbolinks) 来恢复默认的 jquery 事件，但不幸的是它[与 Turbolinks 5](https://github.com/kossnocorp/jquery.turbolinks/issues/56) 不兼容。

脚本的逻辑非常简单:检查页面上是否有一个`#messages`块，如果有，在提供房间 id 的同时订阅频道。下一步是监听表单的`submit`事件:

*JavaScript/频道/房间.咖啡*

```
jQuery(document).on 'turbolinks:load', ->
  messages = $('#messages')
  if $('#messages').length > 0

    App.global_chat = App.cable.subscriptions.create
    # ...

    $('#new_message').submit (e) ->
      $this = $(this)
      textarea = $this.find('#message_body')
      if $.trim(textarea.val()).length > 1
        App.global_chat.send_message textarea.val(), messages.data('chat-room-id')
        textarea.val('')
      e.preventDefault()
      return false 
```

提交表单时，获取消息正文，检查其长度至少为 2，然后调用`send_message`函数向聊天室的所有访问者广播新消息。接下来，清除 textarea 并阻止表单提交。

### 服务器端

我们的下一个任务是在我们的服务器上引入一个频道。在 Rails 5 中，有一个名为 *channels* 的新目录来托管它们，所以在那里创建一个 *chat_rooms_channel.rb* 文件:

*channels/chat _ rooms _ channel . Rb*

```
class ChatRoomsChannel < ApplicationCable::Channel
  def subscribed
    stream_from "chat_rooms_#{params['chat_room_id']}_channel"
  end

  def unsubscribed
    # Any cleanup needed when channel is unsubscribed
  end

  def send_message(data)
    # process data sent from the page
  end
end 
```

`subscribed`是从具有给定名称的频道开始流式传输的特殊方法。只要我们有多个房间，频道的名称就会不同。还记得吗，我们在脚本中订阅频道时提供了`chat_room_id: messages.data('chat-room-id')`？多亏了它，通过调用`params['chat_room_id']`，可以在`subscribed`方法中获取`chat_room_id`。

`unsubscribed`是一个在流停止时触发的回调函数，但是我们不会在这个演示中使用它。

最后一个方法—`send_message`—在我们从脚本中运行`@perform 'send_message', message: message, chat_room_id: chat_room_id`时触发。`data`变量包含发送数据的散列，因此，例如，要访问消息，您可以键入`data['message']`。

有多种方法来广播接收到的消息，但我将向您展示一个基于 DHH 提供的演示的非常简洁的解决方案(我也发现了[这篇文章](http://www.akitaonrails.com/2015/12/28/fixing-dhh-s-rails-5-chat-demo)有一个稍微不同的方法)。

首先，修改`send_message`方法:

*channels/chat _ rooms _ channel . Rb*

```
[...]
def send_message(data)
  current_user.messages.create!(body: data['message'], chat_room_id: data['chat_room_id'])
end
[...] 
```

一旦我们收到消息，把它保存到数据库。你甚至不需要检查提供的聊天室是否存在——默认情况下，在 Rails 5 中，记录的父记录必须存在才能保存。这种行为可以通过为`belongs_to`关系设置`optional: true`来改变(在这里阅读 Rails 5 [的其他变化)。](https://www.sitepoint.com/onwards-to-rails-5-additions-changes-and-deprecations/)

但是有一个问题——device 的`current_user`方法在这里对我们不可用。要解决这个问题，修改*应用 _ 电缆*目录中的*连接. rb* 文件:

*频道/应用 _ 电缆/连接. rb*

```
module ApplicationCable
  class Connection < ActionCable::Connection::Base
    identified_by :current_user

    def connect
      self.current_user = find_verified_user
      logger.add_tags 'ActionCable', current_user.email
    end

    protected

    def find_verified_user # this checks whether a user is authenticated with devise
      if verified_user = env['warden'].user
        verified_user
      else
        reject_unauthorized_connection
      end
    end
  end
end 
```

有了这一点，我们甚至同时实现了两个目标:`current_user`方法现在可用于通道，未经身份验证的用户无法广播他们的消息。

对`logger.add_tags 'ActionCable', current_user.email`的调用用于在控制台中显示调试信息，因此您将看到类似如下的输出:

```
[ActionCable] [test@example.com] Registered connection (Z2lkOi8vY2FibGUtY2hhdC9Vc2VyLzE)
[ActionCable] [test@example.com] ChatRoomsChannel is transmitting the subscription confirmation
[ActionCable] [test@example.com] ChatRoomsChannel is streaming from chat_rooms_1_channel 
```

Under the hood Devise 使用[管理员](https://github.com/hassox/warden)进行认证，因此`env['warden'].user`试图获取当前登录的用户。如果没有找到，`reject_unauthorized_connection`禁止广播。

现在，让我们添加一个在消息实际保存到数据库后触发的回调，以安排一个后台作业:

*models/message.rb*

```
[...]
after_create_commit { MessageBroadcastJob.perform_later(self) }
[...] 
```

在这个回调中`self`是一个保存的消息，所以我们基本上把它传递给作业。立即编写作业:

*jobs/message _ broadcast _ job . Rb*

```
class MessageBroadcastJob < ApplicationJob
  queue_as :default

  def perform(message)
    ActionCable.server.broadcast "chat_rooms_#{message.chat_room.id}_channel",
                                 message: 'MESSAGE_HTML'
  end
end 
```

`perform`方法执行实际的广播，但是我们想要广播的数据怎么办呢？同样，有几种方法可以解决这个问题。你可以发送带有消息数据的 JSON，然后在客户端使用一个模板引擎，比如 [Handlebars](http://handlebarsjs.com/) 。然而，在这个演示中，让我们从之前创建的*messages/_ message . HTML . erb*片段发送 HTML 标记。该部分可以在控制器的帮助下进行渲染:

*jobs/message _ broadcast _ job . Rb*

```
class MessageBroadcastJob < ApplicationJob
  queue_as :default

  def perform(message)
    ActionCable.server.broadcast "chat_rooms_#{message.chat_room.id}_channel",
                                 message: render_message(message)
  end

  private

  def render_message(message)
    MessagesController.render partial: 'messages/message', locals: {message: message}
  end
end 
```

为了让它工作，您必须创建一个空的`MessagesController`:

*messages_controller.rb*

```
class MessagesController < ApplicationController
end 
```

## 回到客户端

太好了，现在服务器端准备好了，我们可以完成我们的脚本了。只要我们广播 HTML 标记，它就可以直接放在页面上，无需任何进一步的操作:

*JavaScript/频道/房间.咖啡*

```
[...]
App.global_chat = App.cable.subscriptions.create {
    channel: "ChatRoomsChannel"
    chat_room_id: messages.data('chat-room-id')
  },
  connected: ->
    # Called when the subscription is ready for use on the server

  disconnected: ->
    # Called when the subscription has been terminated by the server

  received: (data) ->
    messages.append data['message']

  send_message: (message, chat_room_id) ->
    @perform 'send_message', message: message, chat_room_id: chat_room_id
[...] 
```

我唯一不喜欢的是，默认情况下，用户看到的是旧消息，而新消息被放在底部。您可以使用`order`方法对它们进行适当的排序，并在`received`回调中用`prepend`替换`append`，但是我希望我们的聊天表现得像 Slack 一样。在 Slack 中，较新的消息也放在底部，但聊天窗口会自动滚动到它们。这很容易通过页面加载后调用的以下函数来实现:

*JavaScript/频道/房间.咖啡*

```
jQuery(document).on 'turbolinks:load', ->
  messages = $('#messages')
  if $('#messages').length > 0
    messages_to_bottom = -> messages.scrollTop(messages.prop("scrollHeight"))

    messages_to_bottom()

    App.global_chat = App.cable.subscriptions.create
    # ... 
```

当有新消息到达时，我们也滚动到底部(因为默认情况下它不会被聚焦):

*JavaScript/频道/房间.咖啡*

```
[...]
received: (data) ->
  messages.append data['message']
  messages_to_bottom()
[...] 
```

太好了！在 GitHub 上查看结果脚本[。](https://github.com/bodrovis/Sitepoint-source/blob/master/Chat_with_ActionCable_and_Devise/app/assets/javascripts/channels/rooms.coffee)

## 向 Heroku 推进

如果你想把你新的闪亮聊天推给 Heroku，必须采取一些额外的行动。首先，你需要安装一个 Redis 插件。有许多[插件](https://elements.heroku.com/)可供选择:例如，你可以使用[redisude](https://elements.heroku.com/addons/rediscloud)。安装插件后，调整 *cable.yml* 以提供正确的 Redis URL。对于 Rediscloud，它存储在`ENV["REDISCLOUD_URL"]`环境变量中:

*config/cable.yml*

```
production:
  adapter: redis
  url: <%= ENV["REDISCLOUD_URL"] %>
[...] 
```

下一步是列出允许订阅频道的源:

*配置/环境/生产. rb*

```
[...]
config.action_cable.allowed_request_origins = ['https://your_app.herokuapp.com',
                                               'http://your_app.herokuapp.com']
[...] 
```

最后，您必须提供 ActionCable URL。只要我们的 *routes.rb* 有`mount ActionCable.server => '/cable'`，对应的设置应该是:

*配置/环境/生产. rb*

```
[...]
config.action_cable.url = "wss://sitepoint-actioncable.herokuapp.com/cable"
[...] 
```

有了这些，您就可以将代码推送到 Heroku 并观察结果。耶！

## 结论

在本文中，我们讨论了如何设置 ActionCable 并编写一个支持多个房间的迷你聊天应用程序。该应用程序包括客户端和服务器端，同时提供基本的授权机制。我们还使用了一个后台作业来启动广播过程，并讨论了将应用程序部署到 Heroku 所需的步骤。

希望你觉得这篇文章有用而且有趣。你试过使用 ActionCable 吗？你喜欢吃吗?请在评论中分享你的观点。在 Twitter 上关注我，成为第一个知道我的文章的人，再见！

通过我们的教程[在 SitePoint 上模拟用户行为并测试你的 ruby 应用](https://www.sitepoint.com/premium/screencasts/feature-tests-with-rspec-simulate-user-behavior-and-test-your-ruby-app)，了解更多关于 Ruby 的知识。

## 分享这篇文章