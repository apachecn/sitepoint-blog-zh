# 用 rails 构建一个消息传递系统

> 原文：<https://www.sitepoint.com/build-a-messaging-system-with-rails-and-actioncable/>

![](img/89a3cab9636c17552882f2272fbfd577.png)

大约一年前，我写了一篇关于[邮箱](https://github.com/mailboxer/mailboxer)的[文章](https://www.sitepoint.com/messaging-rails-mailboxer/)——这是 Rails 应用程序中发送消息的瑰宝。这个 gem 提供了一组很好的功能，但是它有一大堆问题:

*   它缺乏适当的文件。
*   这相当复杂，尤其是对初级程序员来说。因为它没有大量的文档，所以有时需要深入源代码来理解一些方法是如何工作的。
*   它没有被积极地维护。
*   它有一些错误，只要它不积极进化，谁知道这些错误什么时候会被修复。

在过去的几个月里，我收到了许多关于 Mailboxer 的问题，因此决定解释如何为 Rails 创建一个定制的消息系统。当然，这个系统不会提供 Mailboxer 的所有功能，但是对于许多应用程序来说已经足够了。当你完全理解了这个系统是如何工作的，进一步改进它就容易多了。

本文将分为两个部分:

*   第一部分将介绍准备工作、关联设置以及控制器和视图的创建。我们还将增加对表情符号的支持。在本部分结束时，您将拥有一个工作的消息传递系统。
*   第二部分将解释如何利用 ActionCable 实现实时消息传递和通知系统。ActionCable 可能是 Rails 5 最令人期待的功能，我已经在一段时间前介绍过了。除此之外，我们还将实现“用户在线”功能。

源代码可以在 [GitHub](https://github.com/bodrovis/Sitepoint-source/tree/master/Custom_Messaging_System) 获得。

工作演示可在 sitepoint-custom-messaging.herokuapp.com 获得。

## 证明

深呼吸，创建一个新的 Rails 应用程序:

```
$ rails new Messager -T 
```

ActionCable 是在 Rails 5 中引入的，因此本文的某些部分不适用于早期版本。尽管如此，非 ActionCable 概念对 Rails 3 和 Rails 4 仍然有效。

我们需要一种方法来验证用户，所以让我们用[来设计](https://github.com/plataformatec/devise):

*Gemfile*

```
# ...
gem 'devise'
# ... 
```

运行以下命令来安装 Devise 并创建必要的文件:

```
$ bundle install
$ rails generate devise:install
$ rails generate devise User
$ rails db:migrate 
```

现在`User`模型已经就位并装备了 Devise 的魔法。让我们添加一个简单的部分显示 flash 消息和几个链接:

*shared/_menu.html.erb*

```
<% flash.each do |key, value| %>
  <div>
    <%= value %>
  </div>
<% end %>

<% if user_signed_in? %>
  <p>Signed in as <%= current_user.name %> | <%= link_to 'log out', destroy_user_session_path, method: :delete %></p>
<% end %>
<ul>
  <li><%= link_to 'Home', root_path %></li>
</ul> 
```

在布局中渲染此部分:

*layouts/application . html . erb*

```
<%= render 'shared/menu' %> 
```

我们的用户实际上没有名字，但是 Devise 确实引入了一个`email`列，所以让我们利用它:

*型号/用户. rb*

```
def name
  email.split('@')[0]
end 
```

## 创建模型和建立关联

接下来，我们将需要另外两个模型，以下是它们的主要字段:

### `Conversation`

*   `author_id`–指定谁开始对话的索引字段
*   `recevier_id`–指定接收者的索引字段。我们将使用脸书的会话系统作为例子，因此在两个特定用户之间只能有一个会话。

### `PersonalMessage`

*   `body`–消息的实际文本
*   `conversation_id`–指定父对话的索引字段
*   `user_id`–指定消息作者的索引字段

就是这样。生成这两个模型和相应的迁移:

```
$ rails g model Conversation author_id:integer:index receiver_id:integer:index
$ rails g model PersonalMessage body:text conversation:belongs_to user:belongs_to 
```

通过添加另一个索引来调整第一次迁移，确保`author_id`和`receiver_id`组合的唯一性:

*db/migrate/XYZ _ create _ conversations . Rb*

```
# ...
add_index :conversations, [:author_id, :receiver_id], unique: true
# ... 
```

应用迁移:

```
$ rails db:migrate 
```

现在是棘手的部分。对话应该属于作者和接收者，但实际上这两者是同一个模型。这要求我们为`belongs_to`方法提供一个特殊的选项:

*models/conversation.rb*

```
# ...
belongs_to :author, class_name: 'User'
belongs_to :receiver, class_name: 'User'
# ... 
```

要了解更多关于 Rails 协会的信息，请阅读这篇文章。在用户端，我们还需要建立
两个关系:

*型号/用户. rb*

```
# ...
has_many :authored_conversations, class_name: 'Conversation', foreign_key: 'author_id'
has_many :received_conversations, class_name: 'Conversation', foreign_key: 'received_id'
# ... 
```

在这种情况下，需要指定使用哪个外键，因为默认情况下，Rails 使用关联名来推断键的名称。

让我们也创建一个验证来确保在相同的两个用户之间不会有两个对话:

*models/conversation.rb*

```
# ...
validates :author, uniqueness: {scope: :receiver}
# ... 
```

`PersonalMessage`也需要攻陷。在会话端建立一个`has_many`关系，指定默认的排序规则(最老的排在最前面):

*models/conversation.rb*

```
# ...
has_many :personal_messages, -> { order(created_at: :asc) }, dependent: :destroy
# ... 
```

个人消息属于对话和用户:

*models/personal _ message . Rb*

```
# ...
belongs_to :conversation
belongs_to :user
# ... 
```

此外，在这里，让我们添加一个简单的验证规则:

*models/personal _ message . Rb*

```
# ...
validates :body, presence: true
# ... 
```

最后，关注一下`User`模型:

*型号/用户. rb*

```
# ...
has_many :personal_messages, dependent: :destroy
# ... 
```

太好了，所有关联都设置好了。让我们继续讨论控制器、视图和路线。

## 显示对话

首先，添加一个全局`before_action`强制用户进行身份验证(该方法由 Devise 提供):

*application _ controller . Rb*

```
# ...
before_action :authenticate_user!
# ... 
```

在我们的应用程序的主页上，我想列出当前用户参与的所有对话。然而，问题是“参与”意味着他们要么是作者，要么是接收者:

*conversations _ controller . Rb*

```
class ConversationsController < ApplicationController
  def index
    @conversations = Conversation.participating(current_user).order('updated_at DESC')
  end
end 
```

为了实现这一点，引入一个名为`participating`的新作用域:

*models/conversation.rb*

```
# ...
scope :participating, -> (user) do
  where("(conversations.author_id = ? OR conversations.receiver_id = ?)", user.id, user.id)
end
# ... 
```

很好。添加根路由:

*config/routes.rb*

```
# ...
root 'conversations#index'
# ... 
```

创建视图

*views/conversations/index . html . erb*

```
<h1>Your conversations</h1>

<div id="conversations">
  <%= render @conversations %>
</div> 
```

添加部分以呈现对话:

*views/conversations/_ conversation . html . erb*

```
<div>
  Chatting with <%= conversation.with(current_user).name %>
  <br>
  <em><%= conversation.personal_messages.last.body.truncate(50) %></em>
  <br>
  <%= link_to 'View conversation', conversation_path(conversation) %>
  <hr>
</div> 
```

`conversation_path`待会再擒。`with`是我们现在要创建的一个方法，它返回对话的另一个参与者:

*models/conversation.rb*

```
# ...
def with(current_user)
  author == current_user ? receiver : author
end
# ... 
```

为`ConversationsController`添加`show`动作。但是，在调用此操作之前，我们必须确保用户确实被授权查看所请求的对话:

*conversations _ controller . Rb*

```
# ...
before_action :set_conversation, except: [:index]
before_action :check_participating!, except: [:index]

def show
  @personal_message = PersonalMessage.new
end

private

def set_conversation
  @conversation = Conversation.find_by(id: params[:id])
end

def check_participating!
  redirect_to root_path unless @conversation && @conversation.participates?(current_user)
end
# ... 
```

在`show`动作中，我们实例化了`@personal_message`变量，因为它将在视图中用于呈现表单。`participates?`是另一种实例方法:

*models/conversation.rb*

```
# ...
def participates?(user)
  author == user || receiver == user
end
# ... 
```

这是我们的观点:

*views/conversations/show . html . erb*

```
<h1>Chatting with <%= @conversation.with(current_user).name %></h1>

<div id="conversation-main">
  <div id="conversation-body">
    <%= render @conversation.personal_messages %>
  </div>

  <%= form_for @personal_message do |f| %>
    <%= hidden_field_tag 'conversation_id', @conversation.id %>
    <%= f.label :body %>
    <%= f.text_area :body %>

    <%= f.submit %>
  <% end %>
</div> 
```

这个观点其实很简单。首先，呈现已经存在的消息列表，然后提供一个发送新消息的表单。

创建一个分部来显示实际消息:

*views/personal _ messages/_ personal _ message . html . erb*

```
<p><%= personal_message.body %></p>
<p>at <strong><%= personal_message.created_at %></strong><br>
  by <strong><%= personal_message.user.name %></strong></p>
<hr> 
```

## 回应对话

当然，现在我们需要一个新的控制器来处理个人消息，所以创建一个:

*personal _ messages _ controller . Rb*

```
class PersonalMessagesController < ApplicationController
  before_action :find_conversation!

  def create
    @personal_message = current_user.personal_messages.build(personal_message_params)
    @personal_message.conversation_id = @conversation.id
    @personal_message.save!

    flash[:success] = "Your message was sent!"
    redirect_to conversation_path(@conversation)
  end

  private

  def personal_message_params
    params.require(:personal_message).permit(:body)
  end

  def find_conversation!
    @conversation = Conversation.find_by(id: params[:conversation_id])
    redirect_to(root_path) and return unless @conversation && @conversation.participates?(current_user)
  end
end 
```

在`before_action`中，尝试按 id 查找对话。然后，如果找到并且用户参与其中，则创建一个新的个人消息并保存它。最后，重定向回对话页面。

接下来添加路线:

*config/routes.rb*

```
# ...
resources :personal_messages, only: [:create]
resources :conversations, only: [:index, :show]
# ... 
```

## 开始新的对话

目前没有办法与用户开始新的对话，所以让我们现在修复它。创建一个控制器来管理用户:

*users_controller.rb*

```
class UsersController < ApplicationController
  def index
    @users = User.all
  end
end 
```

一种`index`观点:

*views/users/index . html . erb*

```
<h1>Users</h1>

<ul><%= render @users %></ul> 
```

还有部分:

*views/users/_ user . html . erb*

```
<li>
  <%= user.name %> | <%= link_to 'send a message', new_personal_message_path(receiver_id: user) %>
</li> 
```

调整路线:

*config/routes.rb*

```
# ...
resources :users, only: [:index]
resources :personal_messages, only: [:new, :create]
# ... 
```

同时在菜单中显示一个新链接:

*shared/_menu.html.erb*

```
# ...
<ul>
  <li><%= link_to 'Home', root_path %></li>
  <li><%= link_to 'Users', users_path %></li>
</ul> 
```

现在为`PersonalMessagesController`添加`new`动作:

*personal _ messages _ controller . Rb*

```
# ...
def new
  @personal_message = current_user.personal_messages.build
end
# ... 
```

然而，有一个问题。当`find_conversation!`方法作为`before_action`的一部分被调用时，我们称之为`@conversation = Conversation.find_by(id: params[:conversation_id])`，但是当用户点击“发送消息”链接时没有`:conversation_id`。因此，我们需要引入一点更复杂的逻辑:

*   如果设置了`:receiver_id`(也就是说，点击了“发送消息”链接)，我们试图找到另一个用户来处理该消息。
*   如果没有找到用户，重定向到根路径(当然，您可能会显示某种错误)。
*   如果找到用户，检查他和当前用户之间的对话是否已经存在。
*   如果对话确实存在，重定向到“显示对话”操作。
*   如果不存在，则呈现该表单以开始对话。
*   最后，如果没有设置`:receiver_id` id，我们试图通过`:conversation_id`找到一个现有的对话。

下面是更新后的`find_conversation!`方法和`new`动作:

*conversations _ controller . Rb*

```
# ...
def new
  redirect_to conversation_path(@conversation) and return if @conversation
  @personal_message = current_user.personal_messages.build
end

private

def find_conversation!
  if params[:receiver_id]
    @receiver = User.find_by(id: params[:receiver_id])
    redirect_to(root_path) and return unless @receiver
    @conversation = Conversation.between(current_user.id, @receiver.id)[0]
  else
    @conversation = Conversation.find_by(id: params[:conversation_id])
    redirect_to(root_path) and return unless @conversation && @conversation.participates?(current_user)
  end
end
# ... 
```

`between`是返回两个用户对话的范围:

*models/conversation.rb*

```
# ...
scope :between, -> (sender_id, receiver_id) do
  where(author_id: sender_id, receiver_id: receiver_id).or(where(author_id: receiver_id, receiver_id: sender_id)).limit(1)
end
# ... 
```

这里是观点:

*views/personal _ messages/new . html . erb*

```
<h1>New message to <%= @receiver.name %></h1>

<%= form_for @personal_message do |f| %>
  <%= hidden_field_tag 'receiver_id', @receiver.id %>

  <%= f.label :body %>
  <%= f.text_area :body %>

  <%= f.submit %>
<% end %> 
```

这个`create`动作也需要一点小小的改动。目前我们没有考虑到对话可能不存在，所以现在就修复它:

*personal _ messages _ controller . Rb*

```
# ...
def create
  @conversation ||= Conversation.create(author_id: current_user.id,
                                        receiver_id: @receiver.id)
  @personal_message = current_user.personal_messages.build(personal_message_params)
  @personal_message.conversation_id = @conversation.id
  @personal_message.save!

  flash[:success] = "Your message was sent!"
  redirect_to conversation_path(@conversation)
end
# ... 
```

就是这个！我们的消息传递系统已经完成，您可以看到它在运行！

## 一点造型

只要我们在底部显示最新的消息，让我们稍微改变一下对话页面的风格，使其更加用户友好:

*application.scss*

```
#conversation-body {
  max-height: 400px;
  overflow-y: auto;
  margin-bottom: 2em;
} 
```

在大多数情况下，用户对最近的消息感兴趣，因此滚动到消息框的底部也是一个好主意:

*JavaScript s/conversations . coffee*

```
jQuery(document).on 'turbolinks:load', ->
  messages = $('#conversation-body')
  if messages.length > 0
    messages_to_bottom = -> messages.scrollTop(messages.prop("scrollHeight"))

    messages_to_bottom() 
```

我们基本上定义一个函数，并在页面加载后立即调用它。如果您没有使用 Turbolinks，第一行应该是

```
jQuery -> 
```

最后，需要这个 CoffeeScript 文件:

*JavaScript s/application . js*

```
//= require conversations 
```

## 添加表情符号支持

微笑符号让互联网上的对话更加丰富多彩(除非有人严重滥用它们)。因此，我们为什么不在我们的应用程序中添加对表情符号的支持呢？有了[表情符号](https://github.com/github/gemoji)宝石，这很容易。将它放入*gem 文件*:

*Gemfile*

```
# ...
gem 'emoji'
# ... 
```

并通过运行以下命令进行安装:

```
$ bundle install 
```

添加一个新的 helper 方法在这里找到了:

*application_helper.rb*

```
# ...
def emojify(content)
  h(content).to_str.gsub(/:([\w+-]+):/) do |match|
    if emoji = Emoji.find_by_alias($1)
      %(<img alt="#$1" src="#{image_path("emoji/#{emoji.image_filename}")}" style="vertical-align:middle" width="20" height="20" />)
    else
      match
    end
  end.html_safe if content.present?
end
# ... 
```

此方法可用于任何视图或局部视图:

*views/personal _ messages/_ personal _ message . html . erb*

```
<p><%= emojify personal_message.body %></p>
<p>at <strong><%= personal_message.created_at %></strong><br>
  by <strong><%= personal_message.user.name %></strong></p>
<hr> 
```

你也可以在应用程序的某个地方展示一个链接，链接到[表情符号备忘单](http://www.webpagefx.com/tools/emoji-cheat-sheet/)。

## 结论

好了，我们的消息应用程序的第一个版本已经完成，并且运行得非常好。在下一部分中，我们将通过使用 ActionCable 支持的 web sockets 使它更加现代化，并实现一个“用户在线”的特性。同时，如果你有任何问题，不要犹豫与我联系。

我感谢你和我呆在一起，很快再见！

## 分享这篇文章