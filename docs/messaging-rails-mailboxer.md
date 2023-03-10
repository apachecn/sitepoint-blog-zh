# 用 rails 和邮箱传递消息

> 原文：<https://www.sitepoint.com/messaging-rails-mailboxer/>

![red mail box](img/902b681c5c62a010ea33634cd246bcc9.png)

最近我写了一系列关于制作聊天应用的文章:[与 Rails 的迷你聊天](https://www.sitepoint.com/mini-chat-rails/)，[与 Rails 和 Faye 的实时迷你聊天](https://www.sitepoint.com/realtime-mini-chat-rails-faye/)，以及[与 Rails 和服务器发送事件的迷你聊天](https://www.sitepoint.com/mini-chat-rails-server-sent-events/)。这些帖子在读者中很受欢迎，我收到了很多评论和电子邮件反馈。这太棒了，我想再次感谢你们分享你们的想法。

一位读者提到了 Mailboxer，这是实现消息系统的一个很好的解决方案。所以，我决定研究它，并与你分享结果。

Mailboxer 是 Rails 的瑰宝，是构建社交网络的 [social_stream](https://github.com/ging/social_stream) 框架的一部分。它是一个通用的消息传递系统，允许任何模型“可传递消息”，为它配备了一些通用的方法。使用 Mailboxer，您可以创建与一个或多个收件人的对话(邮件被组织到文件夹中——sent box、inbox、trash ),并通过电子邮件发送通知。甚至可以在不同型号之间发送消息，添加附件！唯一的缺点是缺少文档，所以我希望这篇文章会有用。

我们将讨论一款演示应用，它:

*   对设备使用基本身份验证
*   允许用户使用 Gravatar 管理头像
*   集成 Mailboxer
*   创建一个 GUI 来开始新的对话，以及回复现有的对话(使用 Bootstrap 的样式和选择的 jQuery 插件)
*   显示文件夹并允许在它们之间轻松切换
*   允许将对话标记为已读，废弃对话，并恢复它们。它也会清理垃圾桶。
*   设置电子邮件通知

Rails 4 将用于这个演示，但几乎相同的解决方案可以用 Rails 3.2 实现(Mailboxer 不再支持版本 3.1)。

源代码可以在 [GitHub](https://github.com/bodrovis/SitePoint-Mailboxer) 上获得。

工作演示可以在[sitepoint-mailboxer.herokuapp.com](http://sitepoint-mailboxer.herokuapp.com)找到。

## 准备应用程序

假设我们必须创建一个供内部使用的私人消息系统，在这个系统中，员工可以讨论不同的话题。这个系统应该允许用户创建与无限数量的接收者的对话，提供通知系统，并允许删除过时的对话。

让我们称之为 *Synergy* 并开始创建一个没有默认测试套件的新 Rails 应用程序:

```
$ rails new Synergy -T
```

将这些宝石放入你的 Gemfile(我将坚持使用 Bootstrap，但是你可以使用任何其他 CSS 框架，使用你自己的设计，或者完全跳过美化网站):

*Gemfile*

```
[...]
gem 'bootstrap-sass'
gem 'bootstrap-will_paginate'
gem 'will_paginate'
[...]
```

奔跑

```
$ bundle install
```

如果您想继续，请放入 Bootstrap 的文件:

*application.css.scss*

```
@import 'bootstrap';
@import 'bootstrap/theme';
```

接下来稍微调整一下布局:

*layouts/application . html . erb*

```
[...]
<div class="container">
  <% flash.each do |key, value| %>
    <div class="alert alert-<%= key %>">
      <%= value %>
    </div>
  <% end %>

  <div class="page-header">
    <h1><%= yield :page_header %></h1>
  </div>

  <%= yield %>
</div>
[...]
```

我们还可以添加一个帮助器方法来轻松呈现页面标题:

*application_helper.rb*

```
[...]
def page_header(text)
  content_for(:page_header) { text.to_s }
end
[...]
```

### 证明

在实现消息传递功能之前，我们需要一个能够传递消息的模型。创建`User`模型:

```
$ rails g model User name:string
$ rake db:migrate
```

你可以使用任何类型的认证，但是我喜欢用[设计](https://github.com/plataformatec/devise/)。Devise 的基本设置非常简单，并且有[大量的文档](https://github.com/plataformatec/devise/wiki)帮助你进一步定制东西。

掉落一颗新宝石:

*Gemfile*

```
[...]
gem 'devise'
[...]
```

并安装它:

```
$ bundle install
```

现在我们可以利用 Devise 的代码生成器为我们做一些工作:

```
$ rails generate devise:install
```

请务必阅读安装后消息，以完成一些附加步骤。具体来说，您需要为开发和生产调整`config.action_mailer.default_url_options`设置，因为它将用于向用户发送电子邮件(例如，帮助他们恢复忘记的密码)。请注意，除非您在**环境/开发. rb** 中设置了`config.action_mailer.perform_deliveries = true`，否则不会在开发中发送电子邮件。

这里有一些关于如何配置 ActionMailer 的例子。

准备就绪后，运行以下命令，用 Devise 创建`User`模型:

```
$ rails generate devise User
$ rake db:migrate
```

您可能希望在应用迁移之前检查生成的迁移，以便向表中添加更多的字段(以启用可确认或可锁定的模块)。然后，您还需要相应地调整模型。

最后，跑

```
$ rails generate devise:views
```

将 Devise 的视图直接复制到您的项目中，以便对其进行一些修改。用户将能够更改他们的姓名，因此在注册表中添加一个新字段:

*views/device/registrations/new . html . erb*

```
[...]
<%= f.label :name %>
<%= f.text_field :name %>
[...]
```

将相同的代码放到**视图/device/registrations/edit . html . erb**中(或者将其重构为部分代码)，这样用户就可以在注册时提供他们的名字，并在以后进行编辑。

这是 Rails 4，所以`strong_params`正在运行。允许传递新的`:name`参数:

*application _ controller . Rb*

```
[...]
before_action :configure_permitted_parameters, if: :devise_controller?

protected

def configure_permitted_parameters
  devise_parameter_sanitizer.for(:sign_up) << :name
  devise_parameter_sanitizer.for(:account_update) << :name
end
```

`devise_controller?`方法由 Devise 提供。这里，我们只允许使用`:name`属性来创建和编辑帐户。如果您忘记这样做，用户将无法设置他们的名称。

此时，您还可以对视图进行一些样式化。我不会介绍这一步，因为它并不太难，并且高度依赖于您的设置(无论您是否使用 Bootstrap)。如果你决定使用 Bootstrap，那么由 Devise 生成的 flash 消息将不会被样式化。要解决这个问题，可以使用 SASS `@extend`方法，如下所示:

*application.css.scss*

```
[...]
.alert-notice {
  @extend .alert-success;
}

.alert-alert {
  @extend .alert-warning;
}
```

## 集成邮箱

很好，我们终于准备好继续主要任务了——集成和设置 Mailboxer。

首先，添加新的宝石:

*Gemfile*

```
[...]
gem "mailboxer"
[...]
```

并安装它:

```
$ bundle install
```

生成并应用所有需要的迁移，并创建一个初始化文件:

```
$ rails g mailboxer:install
$ rake db:migrate
```

看一下**config/initializer/mail boxer . Rb**看看你可以修改哪些选项。现在，让我们保留这个文件——稍后我们将设置发送电子邮件通知。

我们的模型需要做一点小小的改动，以使其具备邮箱功能:

*型号/用户. rb*

```
[...]
acts_as_messageable
[...]
```

## 显示对话

正如这篇关于为 Mailboxer 创建 GUI 的[小指南](https://github.com/mailboxer/mailboxer/wiki/GUI-Example-on-a-real-application)中所建议的，最佳方式是创建两个控制器:一个用于消息，一个用于对话。单个消息被分组到对话中。稍后，你会发现对话可以是不同的类型。

首先为对话创建一个新的控制器:

*conversations _ controller . Rb*

```
class ConversationsController < ApplicationController
  before_action :authenticate_user!
  before_action :get_mailbox

  def index
    @conversations = @mailbox.inbox.paginate(page: params[:page], per_page: 10)
  end

  private

  def get_mailbox
    @mailbox ||= current_user.mailbox
  end
end
```

每个用户都有自己的邮箱，依次分为收件箱、发送箱和垃圾箱。目前我们只处理收件箱。

方法是设计的一部分。我们只希望经过认证的用户访问我们的应用程序，所以它被设置为`before_action`。如果用户未通过身份验证，她将被重定向到登录页面。

如你所见，我也在使用由`will_paginate`提供的`paginate`方法。分页的样式由`bootstrap-will_paginate`提供。

添加一些路线(其他控制器方法将很快添加):

*config/routes.rb*

```
[...]
resources :conversations, only: [:index, :show, :destroy]
[...]
```

现在的观点是:

*views/conversations/index . html . erb*

```
<% page_header "Your Conversations" %>

<ul class="list-group">
  <%= render partial: 'conversations/conversation', collection: @conversations %>
</ul>

<%= will_paginate %>
```

`page_header`是我们之前创建的 helper 方法。`will_paginate`显示分页控件(仅当有多个页面时)。我们可以把它写成`will_paginate @conversations`，但是 gem 足够聪明，能够理解我们在这种情况下想要分页的内容(约定胜于配置！).

我们必须为`render`指定`partial`参数，因为`@conversations`是`Mailboxer::Conversation::ActiveRecord_Relation`的一个实例，因此 Rails 将默认查找位于 **mailboxer/conversations** 目录中的`_conversation`部分。

现在实际部分:

*views/conversations/_ conversation . html . erb*

```
<li class="list-group-item clearfix">
  <%= link_to conversation.subject, conversation_path(conversation) %>
</li>
```

每个对话都有一个主题和一些信息，这些信息将呈现在 *show* 页面上。很快就需要 CSS 类了。

向布局添加菜单:

*layouts/application . html . erb*

```
[...]
<body>
  <nav class="navbar navbar-inverse">
    <div class="container">
      <div class="navbar-header">
        <%= link_to 'Synergy', root_path, class: 'navbar-brand' %>
      </div>
      <ul class="nav navbar-nav">
        <% if user_signed_in? %>
          <li><%= link_to 'Edit Profile', edit_user_registration_path %></li>
          <li><%= link_to 'Your Conversations', conversations_path %></li>
          <li><%= link_to 'Log Out', destroy_user_session_path, method: :delete %></li>
        <% else %>
          <li><%= link_to 'Log In', new_user_session_path %></li>
        <% end %>
      </ul>
    </div>
  </nav>
</body>
[...]
```

`user_signed_in?`方法，以及大部分路线，都是由 Devise 提供的。

接下来是*秀*动作:

*conversations _ controller . Rb*

```
class ConversationsController < ApplicationController
  before_action :authenticate_user!
  before_action :get_mailbox
  before_action :get_conversation, except: [:index]

  def show
  end

  private

  def get_conversation
    @conversation ||= @mailbox.conversations.find(params[:id])
  end
end
```

我添加了一个新的`before_action`并调整了现有的。

您可能知道当没有找到记录时，`find`方法会引发一个异常。这是我们想要的，但例外应该被解救。为了简单起见，让我们使用`rescue_from`方法:

*application _ controller . Rb*

```
[...]
rescue_from ActiveRecord::RecordNotFound do
  flash[:warning] = 'Resource not found.'
  redirect_back_or root_path
end

def redirect_back_or(path)
  redirect_to request.referer || path
end
[...]
```

我们只是用一条警告消息将用户重定向回来。如果没有设置`referer`字段(例如，如果用户安装了一个插件来清除该字段)，他们将被重定向到`root_path`。

现在的观点是:

*views/conversations/show . html . erb*

```
<% page_header "Conversation" %>

<div class="panel panel-default">
  <div class="panel-heading"><%= @conversation.subject %></div>

  <div class="panel-body">
    <div class="messages">
      <% @conversation.receipts_for(current_user).each do |receipt| %>
        <% message = receipt.message %>

        <%= message.sender.name %>
          says at <%= message.created_at.strftime("%-d %B %Y, %H:%M:%S") %>
        <%= message.body %>
      <% end %>
    </div>
  </div>
</div>
```

我们呈现每封邮件，显示发件人的姓名、创建日期和正文。让我们稍微改变一下`.messages`容器的样式，这样它就不会变得太高了:

*application.css.scss*

```
[...]
.messages {
  max-height: 400px;
  overflow-y: auto;
  margin-bottom: 1em;
  margin-top: 1em;
}
```

不错，一些基本的观点是存在的，但是，我们仍然缺乏应用程序的重要部分:

*   用户应该知道他在和谁聊天
*   用户需要能够开始新的对话
*   用户应该能够回复对话
*   Sentbox 和 trash 应该显示在对话页面上
*   用户应该能够将对话标记为已读

## 显示用户头像

虽然这与 Mailboxer 无关，但我认为显示头像会让我们的应用程序看起来更漂亮。然而，允许用户将他们的头像直接上传到应用程序会有些过分，所以让我们使用 [Gravatar](http://gravatar.com) 和 [gravatar *image* tag](https://github.com/mdeering/gravatar_image_tag) 将其与 Rails 集成。

将新宝石放入宝石档案:

*Gemfile*

```
[...]
gem 'gravatar_image_tag'
[...]
```

然后跑

```
$ bundle install
```

此外，添加一个辅助方法来轻松渲染头像:

*application_helper.rb*

```
[...]
def gravatar_for(user, size = 30, title = user.name)
  image_tag gravatar_image_url(user.email, size: size), title: title, class: 'img-rounded'
end
[...]
```

创建单独的部分以呈现对话参与者的头像(当前用户除外):

*views/conversations/_ participants . html . erb*

```
<% conversation.participants.each do |participant| %>
  <% unless participant == current_user %>
    <%= gravatar_for participant %>
  <% end %>
<% end %>
```

修改以下视图:

*views/conversations/show . html . erb*

```
<% page_header "Conversation" %>

<p>Chatting with
  <%= render 'conversations/participants', conversation: @conversation %>
</p>

<div class="panel panel-default">
  <div class="panel-heading"><%= @conversation.subject %></div>

  <div class="panel-body">
    <div class="messages">
      <% @conversation.receipts_for(current_user).each do |receipt| %>
        <div class="media">
          <% message = receipt.message %>
          <div class="media-left">
            <%= gravatar_for message.sender, 45, message.sender.name %>
          </div>

          <div class="media-body">
            <h6 class="media-heading"><%= message.sender.name %>
              says at <%= message.created_at.strftime("%-d %B %Y, %H:%M:%S") %></h6>
            <%= message.body %>
          </div>
        </div>
      <% end %>
    </div>
  </div>
</div>
```

*views/conversations/_ conversation . html . erb*

```
<li class="list-group-item clearfix">
  [...]
  <p><%= render 'conversations/participants', conversation: conversation %></p>
</li>
```

当我们在这里时，显示对话的最后一条消息及其创建日期:

*views/conversations/_ conversation . html . erb*

```
<li class="list-group-item clearfix">
  [...]
  <p><%= render 'conversations/participant', conversation: conversation %></p>

    <p><%= conversation.last_message.body %>
      (<span class="text-muted"><%= conversation.last_message.created_at.strftime("%-d %B %Y, %H:%M:%S") %></span>)</p>
</li>
```

我们已经完成了化身。是我们允许用户开始对话的时候了。

## 创建对话

创建一个对话实际上意味着在提供主题的同时创建一条新消息(尽管这是可选的)。这意味着需要一个新的控制器:

*messages_controller.rb*

```
class MessagesController < ApplicationController
  before_action :authenticate_user!

  def new
  end

  def create
    recipients = User.where(id: params['recipients'])
    conversation = current_user.send_message(recipients, params[:message][:body], params[:message][:subject]).conversation
    flash[:success] = "Message has been sent!"
    redirect_to conversation_path(conversation)
  end
end
```

在*创建*动作中，找到一组用户(存储在`params['recipients']`中)并利用 Mailboxer 的`send_message`方法，传入收件人、正文和主题。稍后，我们将启用电子邮件通知，以便用户在收到新消息时能够知道。

现在的观点是:

*views/messages/new . html . erb*

```
<% page_header "Start Conversation" %>

<%= form_tag messages_path, method: :post do %>
  <div class="form-group">
    <%= label_tag 'message[subject]', 'Subject' %>
    <%= text_field_tag 'message[subject]', nil, class: 'form-control', required: true %>
  </div>

  <div class="form-group">
    <%= label_tag 'message[body]', 'Message' %>
    <%= text_area_tag 'message[body]', nil, cols: 3, class: 'form-control', required: true %>
  </div>

  <div class="form-group">
    <%= label_tag 'recipients', 'Choose recipients' %>
    <%= select_tag 'recipients', recipients_options, multiple: true, class: 'form-control' %>
  </div>

  <%= submit_tag 'Send', class: 'btn btn-primary' %>
<% end %>
```

`recipients_options`是我们需要创建的一个助手方法:

*messages_helper.rb*

```
module MessagesHelper
  def recipients_options
    s = ''
    User.all.each do |user|
      s << "<option value='#{user.id}'>#{user.name}</option>"
    end
    s.html_safe
  end
end
```

不要忘记路线:

*config/routes.rb*

```
[...]
resources :messages, only: [:new, :create]
[...]
```

让我们在*对话#索引*页面上显示一个“开始对话”链接:

*views/conversations/index . html . erb*

```
<% page_header "Your Conversations" %>

<p><%= link_to 'Start conversation', new_message_path, class: 'btn btn-lg btn-primary' %></p>
[...]
```

从技术上来说，一切都准备好发布你的第一条消息。你可以和自己愉快地聊天，或者注册另一个帐号来模拟两个用户的情况。

但是，选择收件人不是很方便。目前，呈现一个基本的选择字段，因此，如果有许多用户，在列表中查找某个人可能是一项单调乏味的任务。我们可以使用 jQuery 插件[choosed](https://harvesthq.github.io/chosen/)使用一些超级功能来增强这个字段，使选择更加用户友好。有一个[选择的——rails](https://github.com/tsechingho/chosen-rails)gem 使得将这个插件集成到 Rails 应用程序变得更加容易。

将此宝石添加到您的宝石文件中:

*Gemfile*

```
[...]
gem 'chosen-rails'
[...]
```

我还必须指定`sass-rails`和`coffee-rails`的版本，因为我得到了与 **application.css.scss** 文件相关的错误，这是一个[已知的错误](https://github.com/rails/sass-rails/issues/85):

*Gemfile*

```
[...]
gem 'chosen-rails'
gem 'sass-rails', '~> 4.0.5'
gem 'coffee-rails', '~> 4.1.0'
gem 'jquery-turbolinks'
[...]
```

此外，我使用了 [jquery-turbolinks](https://github.com/kossnocorp/jquery.turbolinks) gem 来恢复使用 turbolinks 时的默认 jquery 页面加载事件。

别忘了跑步

```
$ bundle install
```

并将选择的添加到 **application.js** 和 **application.css.scss** :

*JavaScript s/application . js*

```
[...]
//= require jquery.turbolinks
//= require chosen-jquery
[...]
```

*样式表/application.css.scss*

```
[...]
@import 'chosen';
[...]
```

现在让我们将类`.chosen-it`添加到我们的选择标记中:

*views/messages/new . html . erb*

```
[...]
<div class="form-group">
  <%= label_tag 'recipients', 'Choose recipients' %>
  <%= select_tag 'recipients', recipients_options, multiple: true, class: 'form-control chosen-it' %>
</div>
[...]
```

并且给这个职业的所有元素装备天选的魔法:

*JavaScript/messages . coffee*

```
jQuery ->
  $('.chosen-it').chosen()
```

*JavaScript s/application . js*

```
[...]
//= require messages
[...]
```

现在重新加载服务器，导航到 *conversations/new* ，看看新的闪亮的 select 标签。用起来方便多了不是吗？

我们可以更进一步，在选择标签中显示头像和用户名。有一个用于选择的[图像选择](https://github.com/websemantics/Image-Select)扩展。只需将 [ImageSelect.jquery.js](https://github.com/websemantics/Image-Select/blob/master/ImageSelect.jquery.js) 和 [ImageSelect.css](https://github.com/websemantics/Image-Select/blob/master/ImageSelect.css) 文件挂接到您的项目中，并分别在 **application.js** 和 **application.css.scss** 中要求它们。然后，稍微修改一下 helper 方法:

*messages_helper.rb*

```
module MessagesHelper
  def recipients_options
    s = ''
    User.all.each do |user|
      s << "<option value='#{user.id}' data-img-src='#{gravatar_image_url(user.email, size: 50)}'>#{user.name}</option>"
    end
    s.html_safe
  end
end
```

然后重新加载服务器并检查结果。非常酷！

### 回复对话

现在，用户可以创建对话，但没有回复选项！为了解决这个问题，我们需要另一个表单和一个控制器方法，以及一个新的路由:

*views/conversations/show . html . erb*

```
[...]
<%= form_tag reply_conversation_path(@conversation), method: :post do %>
  <div class="form-group">
    <%= text_area_tag 'body', nil, cols: 3, class: 'form-control', placeholder: 'Type something...', required: true %>
  </div>
  <%= submit_tag "Send Message", class: 'btn btn-primary' %>
<% end %>
```

您还可以通过添加另一个文本字段来允许用户添加主题。就当是家庭作业吧。:)

*conversations _ controller . Rb*

```
[...]
def reply
  current_user.reply_to_conversation(@conversation, params[:body])
  flash[:success] = 'Reply sent'
  redirect_to conversation_path(@conversation)
end
[...]
```

Mailboxer 方法`reply_to_conversation`让这变得轻而易举。它接受要回复的对话、消息体、可选主题和一些其他参数。请注意，如果对话被移动到回收站(我们将很快处理)，它将被默认恢复。请看一下[源代码](https://github.com/mailboxer/mailboxer/blob/54277d6971af71c04bf50f5599e8efcba9f5ee60/lib/mailboxer/models/messageable.rb#L115)了解更多信息。

现在的路线是:

*config/routes.rb*

```
[...]
resources :conversations, only: [:index, :show, :destroy] do
  member do
    post :reply
  end
end
[...]
```

非常好，基本的聊天系统已经启动并运行了！

## 实现 Sentbox 和 Trash

目前，我们只显示用户的收件箱。然而，显示 sentbox 和 trash 文件夹也是一个好主意。

标记要渲染的文件夹的最简单方法可能是使用 GET 参数，所以让我们相应地调整控制器:

*conversations _ controller . Rb*

```
[...]
before_action :get_box, only: [:index]

def index
  if @box.eql? "inbox"
    @conversations = @mailbox.inbox
  elsif @box.eql? "sent"
    @conversations = @mailbox.sentbox
  else
    @conversations = @mailbox.trash
  end

  @conversations = @conversations.paginate(page: params[:page], per_page: 10)
end

private

def get_box
  if params[:box].blank? or !["inbox","sent","trash"].include?(params[:box])
    params[:box] = 'inbox'
  end
  @box = params[:box]
end
[...]
```

引入了新的私有方法`get_box`来获取请求的文件夹。

看风景。如果你使用 Bootstrap，我建议使用垂直[导航条](http://getbootstrap.com/components/#nav-pills)来呈现文件夹列表。此外，当前文件夹应该突出显示。为此创建一个助手方法:

*conversations_helper.rb*

```
module ConversationsHelper
  def mailbox_section(title, current_box, opts = {})
    opts[:class] = opts.fetch(:class, '')
    opts[:class] += ' active' if title.downcase == current_box
    content_tag :li, link_to(title.capitalize, conversations_path(box: title.downcase)), opts
  end
end
```

这个方法接受链接的标题(也用于指定 GET 参数)、当前打开的文件夹和一个直接传递给`content_tag`方法的带有选项的散列。然后，检查`opts`散列是否已经有了一个`class`键。如果不是，将其设置为空字符串，然后追加一个`active`类，如果这是当前框。

更改视图:

*views/conversations/index . html . erb*

```
<% page_header "Your Conversations" %>

<div class="row">
  <div class="col-sm-3">
    <ul class="nav nav-pills nav-stacked">
      <%= mailbox_section 'inbox', @box %>
      <%= mailbox_section 'sent', @box %>
      <%= mailbox_section 'trash', @box %>
    </ul>
  </div>

  <div class="col-sm-9">
    <ul class="list-group">
      <%= render partial: 'conversations/conversation', collection: @conversations %>
    </ul>

    <%= will_paginate %>
  </div>
</div>
```

下一步是为每个尚未被丢弃的对话添加一个“添加到垃圾”按钮。对于已删除的对话，应该显示“恢复”按钮。

*views/conversations/_ conversation . html . erb*

```
<li class="list-group-item clearfix">
  <%= link_to conversation.subject, conversation_path(conversation) %>

  <div class="btn-group-vertical pull-right">
    <% if conversation.is_trashed?(current_user) %>
      <%= link_to 'Restore', restore_conversation_path(conversation), class: 'btn btn-xs btn-info', method: :post %>
    <% else %>
      <%= link_to 'Move to trash', conversation_path(conversation), class: 'btn btn-xs btn-danger', method: :delete,
                  data: {confirm: 'Are you sure?'} %>
      <% end %>
    <% end %>
  </div>

  <p><%= render 'conversations/participant', conversation: conversation %></p>

  <p><%= conversation.last_message.body %>
    (<span class="text-muted"><%= conversation.last_message.created_at.strftime("%-d %B %Y, %H:%M:%S") %></span>)</p>
</li>
```

相应的方法:

*conversations _ controller . Rb*

```
[...]
before_action :authenticate_user!
before_action :get_mailbox
before_action :get_conversation, except: [:index]
before_action :get_box, only: [:index]

[...]
def destroy
  @conversation.move_to_trash(current_user)
  flash[:success] = 'The conversation was moved to trash.'
  redirect_to conversations_path
end

def restore
  @conversation.untrash(current_user)
  flash[:success] = 'The conversation was restored.'
  redirect_to conversations_path
end
[...]
```

我又一次调整了之前的动作，使它们只在需要的时候发生。`move_to_trash`和`untrash`是 Mailboxer 提出的两种方法，它们是不言自明的。

现在路线:

*config/routes.rb*

```
[...]
resources :conversations, only: [:index, :show, :destroy] do
  member do
    post :restore
  end
end
[...]
```

“清空垃圾箱”按钮怎么样？简单:

*views/conversations/index . html . erb*

```
[...]
<div class="col-sm-9">
  <% if @box == 'trash' %>
    <p><%= link_to 'Empty trash', empty_trash_conversations_path, class: 'btn btn-danger', method: :delete,
                   data: {confirm: 'Are you sure?'} %></p>
  <% end %>
  <ul class="list-group">
    <%= render partial: 'conversations/conversation', collection: @conversations %>
  </ul>

  <%= will_paginate %>
</div>
[...]
```

相应的方法:

*conversations _ controller . Rb*

```
before_action :get_conversation, except: [:index, :empty_trash]
[...]
def empty_trash
  @mailbox.trash.each do |conversation|
    conversation.receipts_for(current_user).update_all(deleted: true)
  end
  flash[:success] = 'Your trash was cleaned!'
  redirect_to conversations_path
end
[...]
```

路线是:

*config/routes.rb*

```
resources :conversations, only: [:index, :show, :destroy] do
  collection do
    delete :empty_trash
  end
end
```

## 将对话标记为已读

让我们允许用户将对话标记为已读。要实现它，我们还需要另一种方法、路线和按钮:

*views/conversations/_ conversation . html . erb*

```
[...]
<div class="btn-group-vertical pull-right">
  <% if conversation.is_trashed?(current_user) %>
    <%= link_to 'Restore', restore_conversation_path(conversation), class: 'btn btn-xs btn-info', method: :post %>
  <% else %>
    <%= link_to 'Move to trash', conversation_path(conversation), class: 'btn btn-xs btn-danger', method: :delete,
                data: {confirm: 'Are you sure?'} %>

    <% if conversation.is_unread?(current_user) %>
      <%= link_to 'Mark as read', mark_as_read_conversation_path(conversation),
                  class: 'btn btn-xs btn-info', method: :post %>
    <% end %>
  <% end %>
</div>
[...]
```

这里使用了`is_unread?`方法(必须指定一个用户)。还有另一个方法`is_read?`做相反的事情。

*conversations _ controller . Rb*

```
[...]
def mark_as_read
  @conversation.mark_as_read(current_user)
  flash[:success] = 'The conversation was marked as read.'
  redirect_to conversations_path
end
[...]
```

最后，路线:

*config/routes.rb*

```
[...]
resources :conversations, only: [:index, :show, :destroy] do
  member do
    post :mark_as_read
  end
end
[...]
```

太棒了。你也可以调整 *show* 动作，这样当对话被打开时就被标记为已读…更多的家庭作业…

## 电子邮件通知

请记住，Mailboxer 可以在用户每次收到消息时发送电子邮件通知。该特性在初始化器中启用:

*config/initializer/mail boxer . Rb*

```
Mailboxer.setup do |config|
  #Configures if you application uses or not email sending for Notifications and Messages
  config.uses_emails = true

  #Configures the default from for emails sent for Messages and Notifications
  config.default_from = "no-reply@mailboxer.com"

  #Configures the methods needed by mailboxer
  config.email_method = :mailboxer_email
  config.name_method = :name
  [...]
end
```

`config.email_method`和`config.name_method`分别告诉 Mailboxer 如何获得电子邮件和姓名。`name`已经出现在我们的`User`模型中，但是没有`mailboxer_email`。您可以尝试将这个值更改为仅仅是`email`，因为这个方法是由 Devise 添加的，但是这会导致一个错误，因为 Mailboxer 向它传递了一个包含接收到的消息的参数。选择要么重新定义这个方法，要么创建一个新的方法。我将坚持第二种选择:

*user.rb*

```
[...]
def mailboxer_email(object)
  email
end
[...]
```

电子邮件通知现已启用(请确保按照前面的描述设置 ActionMailer。此外，不要忘记，默认情况下，邮件将不会在开发中发送。是的，我在演示应用中禁用了这个功能。)

## 结论

咻。那是相当多要讨论的，不是吗？我们看了 Mailboxer 的基本特性，包括消息、不同类型的对话、管理它们以及设置电子邮件通知。我们还将 Devise 集成到应用程序中，并利用 Gravatar 来使事情看起来更漂亮。

我希望这篇文章对你有用。顺便说一下，你可能会对 Mailboxer wiki 上的[这个页面](https://github.com/mailboxer/mailboxer/wiki/GUI-Example-on-a-real-application)和[这个展示 Mailboxer 基本特性的示例应用](https://github.com/RKushnir/mailboxer-app)感兴趣。

一如既往，欢迎您的反馈。如果你想让我谈一个特定的话题，请尽管问。编码快乐！

## 更新日期:2015 年 3 月 29 日

我收到了很多读者的反馈和问题——知道我的文章有用真是太好了。有一个问题被问了好几次——“我如何添加一个按钮来向特定用户发送消息”？我相信这是一个非常常见的特性，并决定将它作为更新添加到本文中。

这很容易做到。应该从“开始对话”页面的下拉列表中自动选择指定的用户。我认为提供给用户的最好方法是使用 GET 参数。所以把`MessagesController`修改成这样:

**messages_controller.rb**

```
def new
  @chosen_recipient = User.find_by(id: params[:to].to_i) if params[:to]
end
```

现在，`@chosen_recipient`要么包含一个用户记录，要么包含一个`nil`值。

观点:

**views/messages/new . html . erb**

```
<div class="form-group">
  <%= label_tag 'recipients', 'Choose recipients' %>
  <%= select_tag 'recipients', recipients_options(@chosen_recipient), multiple: true, class: 'form-control chosen-it' %>
</div>
```

我们只是将`@chosen_recipient`传递给助手方法。

**messages_helper.rb**

```
def recipients_options(chosen_recipient = nil)
  s = ''
  User.all.each do |user|
    s << "<option value='#{user.id}' data-img-src='#{gravatar_image_url(user.email, size: 50)}' #{'selected' if user == chosen_recipient}>#{user.name}</option>"
  end
  s.html_safe
end
```

这里是一个更新版本的`recipients_options`助手方法。如果用户等同于所选用户，只需为选项设置`selected`属性。

基本上就是这样。为了演示这是如何工作的，添加一个单独的页面，其中有一个用户列表和每个用户旁边的“发送消息”按钮。

**config/routes.rb**

```
resources :users, only: [:index]
```

**users_controller.rb**

```
class UsersController < ApplicationController
  def index
    @users = User.order('created_at DESC').paginate(page: params[:page], per_page: 30)
  end
end
```

**views/users/index . html . erb**

```
<% page_header "Users" %>

<%= will_paginate %>

<ul>
  <% @users.each do |user| %>
    <li>
      <strong><%= user.name %></strong>
      <% unless current_user == user %>
        <%= link_to 'Send message', new_message_path(to: user.id), class: 'btn btn-default btn-sm' %>
      <% end %>
    </li>
  <% end %>
</ul>

<%= will_paginate %>
```

这就是了。不断收到反馈！

## 分享这篇文章