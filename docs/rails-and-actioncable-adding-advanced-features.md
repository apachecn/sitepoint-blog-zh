# 使用 Rails 和 ActionCable 进行实时消息传递

> 原文：<https://www.sitepoint.com/rails-and-actioncable-adding-advanced-features/>

![](img/89a3cab9636c17552882f2272fbfd577.png)

在上一篇文章中，我展示了如何创建一个允许用户之间一对一聊天的定制消息应用程序。我们集成了 Devise，建立了适当的关联，增加了对表情符号的支持，并使一切以同步的方式工作。然而，我真的希望这个应用程序更现代。因此，今天我们将用 ActionCable 进一步增强它，使它成为实时的。

完成本文后，您将知道如何:

*   编写客户端代码以订阅 web 套接字/通道
*   编写代码服务器端广播消息
*   安全行动电缆
*   显示关于新邮件的通知
*   动态更新对话
*   使用 Redis 实现“在线”功能
*   将您的应用程序部署到 Heroku

如果你没有看过上一篇文章，你可以使用[这个代码](https://github.com/bodrovis/Sitepoint-source/tree/master/Custom_Messaging_System)来开始。[这个分支](https://github.com/bodrovis/Sitepoint-source/tree/custom_messaging_sockets/Custom_Messaging_System)包含了具有所有实时特性的应用程序的最终版本。

在[sitepoint-custom-messaging.herokuapp.com](https://sitepoint-custom-messaging.herokuapp.com)有一个工作演示。

## 集成行动电缆

为了让我们的消息系统实时工作，我们将需要 [ActionCable](https://github.com/rails/rails/tree/master/actioncable) ，我已经在[以前的一篇文章](https://www.sitepoint.com/create-a-chat-app-with-rails-5-actioncable-and-devise/)中提到过。第一步是将它安装到某个路径上，通常只是`/cable`:

*config/routes.rb*

```
#...
mount ActionCable.server => '/cable' 
```

接下来，我们需要兼顾客户端和服务器端。

### 客户端

在 *javascripts* 目录下应该有一个名为 *cable.js* 的文件，内容如下:

*cable.js*

```
//= require action_cable
//= require_self
//= require_tree ./channels

(function() {
  this.App || (this.App = {});

  App.cable = ActionCable.createConsumer();

}).call(this); 
```

我们需要在 javascript 清单中包含这个文件:

*application.js*

```
//= require cable 
```

然后，创建一个名为`Conversations`的新通道。让我们也将所有代码从 *conversations.coffee* 移到其中:

*JavaScript/channels/conversations . coffee*

```
jQuery(document).on 'turbolinks:load', ->
  messages_to_bottom = -> messages.scrollTop(messages.prop("scrollHeight"))
  messages = $('#conversation-body')

  if messages.length > 0
    messages_to_bottom() 
```

这段代码只是在显示最新消息时将对话框滚动到最底部。可以删除*JavaScript/conversations . coffee*。

现在，我们需要订阅`Conversations`频道。但是，我们只希望在用户登录时这样做，所以如果用户登录，我们就在页面上添加一个空块:

*shared/_menu.html.erb*

```
<% if user_signed_in? %>
  <div id="current-user"></div>
        <!-- ... --> 
```

现在是咖啡脚本:

*对话.咖啡*

```
jQuery(document).on 'turbolinks:load', ->
  messages_to_bottom = -> messages.scrollTop(messages.prop("scrollHeight"))
  messages = $('#conversation-body')

  if $('#current-user').size() > 0
    App.personal_chat = App.cable.subscriptions.create {
      channel: "NotificationsChannel"
    },
    connected: ->
      # Called when the subscription is ready for use on the server

    disconnected: ->
      # Called when the subscription has been terminated by the server

    received: (data) ->
      # Called when data is received
  if messages.length > 0
     messages_to_bottom() 
```

我收到了许多关于 ActionCable 的问题，经常有读者在使用 CoffeeScript 时遇到类似“意外缩进”这样的错误。因此，请注意 CoffeeScript 严重依赖缩进和空白，所以请仔细检查您的代码，并将其与 GitHub 上发布的[源](https://github.com/bodrovis/Sitepoint-source/tree/custom_messaging_sockets/Custom_Messaging_System)进行比较。

下一步我们应该做的是监听表单上的`submit`事件并阻止它发生。相反，我们将通过我们的`Notifications`通道发送消息，同时提供对话 id:

*对话.咖啡*

```
# ...
if messages.length > 0
  messages_to_bottom()
  $('#new_personal_message').submit (e) ->
    $this = $(this)
    textarea = $this.find('#personal_message_body')
    if $.trim(textarea.val()).length > 1
      App.personal_chat.send_message textarea.val(), $this.find('#conversation_id').val()
      textarea.val('')
    e.preventDefault()
    return false 
```

这里的事情很简单。检查消息是否包含至少 2 个字符，如果是，调用`send_message`方法，稍后将对其进行编码。接下来，清除向用户指示消息已被使用的文本区域，并阻止默认操作发生。请注意，即使我们的 JavaScript 无法运行或者用户禁用了网站的 JavaScript，消息仍然会以同步方式发送。

下面是`send_message`方法:

*对话.咖啡*

```
# ...
App.personal_chat = App.cable.subscriptions.create {
  channel: "NotificationsChannel"
},
send_message: (message, conversation_id) ->
  @perform 'send_message', message: message, conversation_id: conversation_id
# ... 
```

`@perform 'send_message'`意味着我们在传递一个包含消息和对话 id 的对象时，在*服务器端*调用一个同名的方法。我们现在来写这个方法。

### 服务器端

Rails 5 应用有一个新的目录叫做 *app/channels* 。使用以下代码在该目录中创建一个新文件:

*通道/通知 _ 通道. rb*

```
class NotificationsChannel < ApplicationCable::Channel
  def subscribed
    stream_from("notifications_#{current_user.id}_channel")
  end

  def unsubscribed
  end

  def send_message(data)
    conversation = Conversation.find_by(id: data['conversation_id'])
    if conversation && conversation.participates?(current_user)
      personal_message = current_user.personal_messages.build({body: data['message']})
      personal_message.conversation = conversation
      personal_message.save!
    end
  end
end 
```

`subscribed`和`unsubscribed`是当有人开始或停止监听套接字时运行的钩子。

`send_message`是我们从客户端调用的方法。该方法检查对话是否存在以及用户是否有权访问它。这是非常重要的一步，因为否则，任何人都可能写信给任何对话。如果是真的，就保存新消息。请注意，当您对*频道*目录中的文件进行任何更改时，您必须重启 web 服务器(即使是在开发中)。

另一件需要解决的事情是身份验证，以防止任何未经授权的用户订阅套接字。不幸的是，我们无法在通道内访问 design 的`current_user`方法，所以现在添加它:

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
      verified_user = env['warden'].user
      if verified_user
        verified_user
      else
        reject_unauthorized_connection
      end
    end
  end
end 
```

`identified_by :current_user`允许通道使用`current_user`方法。`connect`当有人试图连接到插座时会自动触发。在这里，我们仅在当前用户通过身份验证的情况下设置他们。只要 Devise 使用 [Warden](https://github.com/hassox/warden) 进行认证，我们就可以使用`env['warden'].user`返回一个`ActiveRecord`对象。如果用户未通过认证，只需调用`reject_unauthorized_connection`。

`logger.add_tags 'ActionCable', current_user.email`将已订阅频道的用户信息打印到终端。

到目前为止还不错，但是在消息保存后，它应该被发送回对话的参与者，并呈现在页面上。实现这一点的方法之一是使用回调和后台作业:

*models/personal _ message . Rb*

```
# ...
after_create_commit do
  NotificationBroadcastJob.perform_later(self)
end 
```

只有在保存并提交记录后，才会调用`after_create_commit`。

下面是广播新消息的后台作业:

*作业/通知 _ 广播 _ 作业. rb*

```
class NotificationBroadcastJob < ApplicationJob
    queue_as :default

    def perform(personal_message)
      message = render_message(personal_message)
      ActionCable.server.broadcast "notifications_#{personal_message.user.id}_channel",
                                   message: message,
                                   conversation_id: personal_message.conversation.id

      ActionCable.server.broadcast "notifications_#{personal_message.receiver.id}_channel",
                             notification: render_notification(personal_message),
                             message: message,
                             conversation_id: personal_message.conversation.id
    end

    private

    def render_notification(message)
      NotificationsController.render partial: 'notifications/notification', locals: {message: message}
    end

    def render_message(message)
      PersonalMessagesController.render partial: 'personal_messages/personal_message',
                                        locals: {personal_message: message}
    end
  end 
```

`ActionCable.server.broadcast`通过信道发送数据。由于在 *notifications_channel.rb* 里面，我们说的是`stream_from("notifications_#{current_user.id}_channel")`，所以在后台作业中会使用相同的名称。

这里我们实际上发送了两个广播，一个包含要呈现的实际消息，另一个包含通知。想法很简单:如果用户当前正在浏览该消息所属的对话，只需在底部添加一条新消息。但是，如果它们在其他页面上，在右下角显示一个小的通知消息，说“您有一条来自…的新消息”。

创建一个新的控制器来呈现部分通知:

*notifications _ controller . Rb*

```
class NotificationsController < ApplicationController
end 
```

而偏题本身:

*views/notifications/_ notification . html . erb*

```
<div id="notification">
  <div class="close">close me</div>
  <p>
    <%= message.body %><br>
    from <%= message.user.name %>
  </p>
  <p><%= link_to 'Take me there now!', conversation_path(message.conversation) %></p>
</div> 
```

太好了！我们需要做的下一件事是实际呈现标记，所以回到客户端。

### 回到客户端

调整`received`回调以呈现消息或通知:

*对话.咖啡*

```
received: (data) ->
  if messages.size() > 0 && messages.data('conversation-id') is data['conversation_id']
    messages.append data['message']
    messages_to_bottom()
  else
    $('body').append(data['notification']) if data['notification'] 
```

这个回调中的条件依赖于为`#conversation-body`设置的`data-conversation-id`属性，所以现在添加它:

*views/conversations/show . html . erb*

```
<div id="conversation-body" data-conversation-id="<%= @conversation.id %>"> 
```

我们快结束了。让我们也让通知中的“关闭我”链接起作用。只要通知是动态添加的内容，我们就不能将点击事件处理程序直接绑定到它。相反，依靠事件冒泡:

*对话.咖啡*

```
$(document).on 'click', '#notification .close', ->
  $(this).parents('#notification').fadeOut(1000) 
```

此外，设置通知的样式，使其始终显示在右下角:

*application.scss*

```
#notification {
  position: fixed;
  right: 10px;
  bottom: 10px;
  width: 300px;
  padding: 10px;
  border: 1px solid black;
  background-color: #fff;
  .close {cursor: pointer}
} 
```

现在您可以启动服务器，打开两个单独的浏览器窗口(或者使用两个不同的浏览器)并测试结果！不过，自言自语可能不太有趣…

请注意，为了实时接收更新，您需要在所有浏览器中访问相同的地址，无论是`localhost:3000`还是`127.0.0.1:3000`。

## 更新对话

另一个有用且非常容易实现的特性是会话的动态更新。目前，我们通过`updated_at`对对话进行排序，但是当保存一条消息时，我们并不真正用一个新值来设置这个列。此外，如果用户正在浏览主页，动态地重新呈现按新顺序排序的对话会很好。

要使它工作，首先要修改回调:

*models/personal _ message . Rb*

```
after_create_commit do
  conversation.touch
  NotificationBroadcastJob.perform_later(self)
end 
```

`touch`将简单地用当前日期和时间设置`updated_at`列。

调整`received`回调:

*对话.咖啡*

```
received: (data) ->
  if messages.size() > 0 && messages.data('conversation-id') is data['conversation_id']
    messages.append data['message']
    messages_to_bottom()
  else
    $.getScript('/conversations') if $('#conversations').size() > 0
    $('body').append(data['notification']) if data['notification'] 
```

因此，如果用户没有浏览特定的对话，而是位于对话页面，请求 JavaScript 重新呈现他们参与的对话。相反，您可以请求一个包含所有对话的 JSON 数组，并从回调中呈现它们。

让`index`动作响应 HTML 和 JS 格式的请求:

*conversations _ controller . Rb*

```
def index
  @conversations = Conversation.participating(current_user).order('updated_at DESC')
  respond_to do |format|
    format.html
    format.js
  end
end 
```

最后， *.js.erb* 视图:

*views/conversations/index . js . erb*

```
$('#conversations').replaceWith('<%= j render @conversations %>'); 
```

就是这样！

## “在线吗？”特征

“谁在线”是消息传递系统的一个非常常见的特性。我们将用一种非常简单的格式实现它，无论用户是否在线，都用红色或绿色标记用户的名字。

为特定用户设置“在线”标志可以从控制器中完成，但我们将为此采用另一个通道(这也将用于通知某人何时在线或离线)。我们还将利用 [Redis](http://redis.io/) 来安装它，以查看实际效果。

首先加上 [redis-rb 宝石](https://github.com/redis/redis-rb)

*Gemfile*

```
gem 'redis' 
```

并安装它

```
$ bundle install 
```

接下来，创建新的`Appearances`通道:

*JavaScript s/channels/appearances . coffee*

```
jQuery(document).on 'turbolinks:load', ->
  App.personal_chat = App.cable.subscriptions.create {
    channel: "AppearancesChannel"
  },
  connected: ->

  disconnected: ->

  received: (data) -> 
```

基本上，当用户订阅这个频道时，他们就在线了。

下面是服务器端的实现:

*通道/外观 _ 通道. rb*

```
class AppearancesChannel < ApplicationCable::Channel
  def subscribed
    redis.set("user_#{current_user.id}_online", "1")
    stream_from("appearances_channel")
    ActionCable.server.broadcast "appearances_channel",
                                 user_id: current_user.id,
                                 online: true
  end

  def unsubscribed
    redis.del("user_#{current_user.id}_online")
    ActionCable.server.broadcast "appearances_channel",
                                 user_id: current_user.id,
                                 online: false
  end

  private

  def redis
    Redis.new
  end
end 
```

一旦有人订阅，在 Redis 中设置“user_SOME_ID_online”为“1”。然后开始流式传输并广播一条消息，告诉用户现在在线。

当用户退订时，我们删除“用户 _ 某些 _ ID _ 在线”密钥，并再次广播消息。很简单，真的。

当我们编写服务器端代码时，为`User`类引入一个新的`online?`实例方法:

*型号/用户. rb*

```
def online?
  !Redis.new.get("user_#{self.id}_online").nil?
end 
```

下一步是用一个`span`标签包装所有用户名，并给它一个合适的类。为了使这个过程更容易，添加下面的 helper 方法:

*application_helper.rb*

```
def online_status(user)
  content_tag :span, user.name,
              class: "user-#{user.id} online_status #{'online' if user.online?}"
end 
```

现在这个助手可以在任何视图中使用。我将在*_ 对话*部分中使用它:

*views/conversations/_ conversation . html . erb*

```
Chatting with <%= online_status conversation.with(current_user) %> 
```

现在编写`received`回调代码。它应该简单地添加或删除`online`类:

*出场.咖啡*

```
received: (data) ->
  user = $(".user-#{data['user_id']}")
  user.toggleClass 'online', data['online'] 
```

根据`data['online']`的值添加或删除类别。

当然，需要一些样式:

```
.online_status {
  color: red;
  transition: color 1s ease;
  &.online {
    color: green;
  }
} 
```

我添加了一个`transition`来使事情变得更加顺利。当然，除了用一些颜色来设计名字，你还可以使用`:after`或`:before`伪选择器来添加图标。

## 发布到 Heroku

当然，在某些时候，您可能希望与全世界共享您的应用程序。我将以 Heroku 为例解释如何做到这一点。

首先，你需要安装 Redis。Heroku 提供了一堆添加 Redis 支持的插件(或者现在被称为*元素*)。我将使用 [Redis 云](https://elements.heroku.com/addons/rediscloud)。通过键入以下命令安装其免费版本:

```
$ heroku addons:create rediscloud 
```

现在告诉 ActionCable 使用 Redis Cloud:

*config/cable.yml*

```
production:
  adapter: redis
  url: <%= ENV["REDISCLOUD_URL"] %> 
```

另外需要注意的是，当你说`Redis.new`的时候，redis-rb 会尝试连接到`localhost:6379` ( `6379`是默认端口)。这对开发来说没问题，但对生产来说不是特别好。因此我创建了这个简单的初始化器:

*config/initialize/set _ redis _ URL . Rb*

```
ENV['REDIS_URL'] = ENV["REDISCLOUD_URL"] if ENV["REDISCLOUD_URL"] 
```

默认情况下，redis-rb [首先尝试](https://github.com/redis/redis-rb#getting-started)连接到`ENV['REDIS_URL']`中设置的 URL，并使用`localhost:6379`作为后备。

另一件需要做的事情是创建 *Procfile* ,告诉 Heroku 使用 Puma 作为 web 服务器:

procfile

```
web: bundle exec puma -C config/puma.rb 
```

Puma 的配置文件可以在[这里](https://devcenter.heroku.com/articles/deploying-rails-applications-with-the-puma-web-server)找到。

最后一步是设置 ActionCable URL 并为生产环境添加允许的源。对于我的演示应用程序，配置如下:

*配置/环境/生产. rb*

```
config.action_cable.allowed_request_origins = ['https://sitepoint-custom-messaging.herokuapp.com',
                                               'http://sitepoint-custom-messaging.herokuapp.com']

config.action_cable.url = "wss://sitepoint-custom-messaging.herokuapp.com/cable" 
```

你的网址会不一样。

仅此而已！一旦完成这些步骤，您就可以将您的应用程序部署到 Heroku 并进行测试。

## 结论

那是一条很长的路。在这两篇文章中，我们创建了一个消息传递应用程序，允许一对一的对话。然后我们在 ActionCable 和 Redis 的帮助下让它实时工作。当然，这段代码可以进一步开发，并且可以引入许多新特性。如果你基于我的代码创造了一些有趣的东西，请分享！

关于消息传递和 ActionCable 的文章到此结束。一如既往，我感谢你陪我到最后，希望很快见到你！

## 分享这篇文章