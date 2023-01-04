# 用 Rails 轮询您的用户

> 原文：<https://www.sitepoint.com/polling-users-rails/>

![bright poll of light bulb](img/a97910ca7672b1fb707c9d4f98fa5156.png)

这里有一些问题要问你:

*   你曾经在一个网站上参加过民意调查吗？是/否？
*   你自己创造过民意测验吗？是/否？
*   今天构建一个允许用户创建自己的投票并参与其中的 web 应用程序怎么样？！是/否？

在本文中，我将向您展示如何构建一个 web 应用程序，允许经过身份验证的用户创建、管理和参与投票。在构建过程中，我们将讨论以下内容:

*   Rails 和 Cocoon gem 中的嵌套属性
*   具有中间表的多对多关系
*   使用 OmniAuth 策略通过脸书进行身份验证
*   使用 jQuery Validate 插件和少量 AJAX 来改善用户体验
*   模型缓存、计数器缓存(带有 cache_culture gem)和渴望加载以提高性能
*   用 Bootstrap 的进度条可视化民意测验的统计数据(和一些数学知识)

所有这些只需要六次迭代！酷吗？那我们开始吧！

源代码可以在 [GitHub](https://github.com/bodrovis/SitePointPoller) 获得。

工作演示可以在[http://sitepoint-poller.herokuapp.com](http://sitepoint-poller.herokuapp.com)找到。

## 一些基础工作

我无法为我们的 web 服务想出一个很酷的名字，所以为了简单起见，我们称它为 Poller。创建一个没有默认测试套件的新 Rails 应用程序:

```
$ rails new poller -T
```

我们将有许多由不同用户创建的投票，有无限数量的投票选项。创建一个表来保存投票主题和可能选项的列表是不明智的，因为每个用户只能投票一次。此外，每个用户选择的选项也将被记录，以计算总票数。因此，投票选项和用户之间将存在关系。

因此，让我们创建两个单独的表—`polls`和`vote_options`。第一个表只有一个字段(除了默认的`id`、`created_at`和`updated_at`):

*   `topic`(`text`)——投票的主题(咄)

`vote_options`表具有:

*   `title`(`string`)–投票选项的实际文本
*   `poll_id`(`integer`)——在`vote_option`和`polls`之间建立一对多关系的外键

创建并应用适当的迁移:

```
$ rails g model Poll topic:text
$ rails g model VoteOption title:string poll:references
$ rake db:migrate
```

修改模型文件，添加关系和一些验证:

*models/poll.rb*

```
[...]
has_many :vote_options, dependent: :destroy
validates :topic, presence: true
[...]
```

*models/vote_option.rb*

```
[...]
validates :title, presence: true
[...]
```

现在，是时候处理视图、控制器和路线了。创建`PollsController`:

*polls_controller.rb*

```
class PollsController < ApplicationController
  def index
    @polls = Poll.all
  end

  def new
    @poll = Poll.new
  end

  def create
    @poll = Poll.new(poll_params)
    if @poll.save
      flash[:success] = 'Poll was created!'
      redirect_to polls_path
    else
      render 'new'
    end
  end

  private

  def poll_params
    params.require(:poll).permit(:topic)
  end
end
```

以及相应的路线:

*config/routes.rb*

```
resources :polls
root to: 'polls#index'
```

我是 Twitter Bootstrap 的粉丝，它帮助我们建立一个漂亮的设计，所以把它添加到 **Gemfile** :

*Gemfile*

```
[...]
gem 'bootstrap-sass'
[...]
```

别忘了跑`bundle install`！

将您的 **application.css** 重命名为 **application.css.scss** ，并将其内容替换为:

```
@import 'bootstrap';
@import 'bootstrap/theme';
```

像这样改变布局:

*layouts/application . html . erb*

```
[...]
<div class="navbar navbar-inverse">
  <div class="container">
    <div class="navbar-header">
      <%= link_to 'Poller', root_path, class: 'navbar-brand' %>
    </div>
    <ul class="nav navbar-nav">
      <li><%= link_to 'Add poll', new_poll_path %></li>
    </ul>
  </div>
</div>

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

我们使用`yield :page_header`来显示页面标题，而不需要每次都复制粘贴那些`div`和`h1`标签。

创建一些视图:

*polls/index.html.erb*

```
<% content_for(:page_header) {"Participate in our polls right now!"} %>
```

*polls/new.html.erb*

```
<% content_for(:page_header) {"Create a new poll"} %>

<%= render 'form' %>
```

*polls/_form.html.erb*

```
<%= form_for @poll do |f| %>
  <%= render 'shared/errors', object: @poll %>

  <div class="form-group">
    <%= f.label :topic %>
    <%= f.text_area :topic, rows: 3, required: true, class: 'form-control' %>
  </div>

  <%= f.submit 'Create', class: 'btn btn-primary btn-lg' %>
<% end %>
```

*shared/_errors.html.erb*

```
<% if object.errors.any? %>
  <div class="alert alert-warning">
    <h4>The following errors were found:</h4>
    <ul>
      <% object.errors.full_messages.each do |msg| %>
        <li><%= msg %></li>
      <% end %>
    </ul>
  </div>
<% end %>
```

基础工作已经完成，我们准备进入文章的实质部分。

## 创建投票选项

停一会儿，看看到目前为止都做了些什么。我们有一些控制器方法和一些视图来列出和创建新的投票。但是，我们没有为特定投票创建投票选项的页面。是否应该为此创建一个单独的控制器和视图？假设您需要十个投票选项，您会想要提交表单十次吗？

在创建投票的同时创建投票选项要好得多。这可以通过使用 Rails 的[嵌套属性](http://api.rubyonrails.org/classes/ActiveRecord/NestedAttributes/ClassMethods.html)来实现，以允许通过父记录保存相关记录的属性。

首先，我们必须在 **poll.rb** 中启用嵌套属性(因为`Poll`是`VoteOption`的父节点):

*models/poll.rb*

```
[...]
accepts_nested_attributes_for :vote_options, :reject_if => :all_blank, :allow_destroy => true
[...]
```

`:reject_if => :all_blank`如果没有投票选项，则引发错误并拒绝保存投票。

`:allow_destroy => true`允许用户通过嵌套属性删除投票选项(当打开
一个编辑投票页面时，我们将很快创建该页面)。

为了允许在同一个页面上创建无限数量的投票选项，您可以编写一些辅助函数和一些 JavaScript。然而，在这个演示中，我们将使用内森·范·德·奥韦拉创造的[茧](https://github.com/nathanvda/cocoon)宝石来帮助我们快速获得想要的结果。这个 gem 帮助构建动态嵌套表单，并与基本的 Rails 表单、Formtastic 和 Simple_form 一起工作。

将这些宝石添加到您的宝石文件中:

*Gemfile*

```
[...]
gem 'jquery-turbolinks'
gem "cocoon"
[...]
```

并运行`bundle install`。

只有在使用 [Turbolinks](https://github.com/rails/turbolinks/) 的情况下，才应该添加 [jquery-turbolinks](https://github.com/kossnocorp/jquery.turbolinks) 。它将默认的 jQuery `document.ready`事件带回页面加载。

包括相应的 JavaScript 文件:

*application.js*

```
[...]
//= require jquery.turbolinks
//= require cocoon
[...]
```

在开始构建嵌套表单之前，要做的最后一件事是稍微改变控制器的方法，以允许一些新的属性:

*polls_controller.rb*

```
[...]
def poll_params
  params.require(:poll).permit(:topic, vote_options_attributes: [:id, :title, :_destroy])
end
[...]
```

仅当您希望允许通过嵌套属性销毁投票选项时，才需要允许`:_destroy`。

让我们继续实际的形式:

*polls/_form.html.erb*

```
<%= form_for @poll do |f| %>
  <%= render 'shared/errors', object: @poll %>

  <div class="form-group">
    <%= f.label :topic %>
    <%= f.text_area :topic, rows: 3, required: true, class: 'form-control' %>
  </div>

  <div class="panel panel-default">
    <div class="panel-heading">Options</div>
    <div class="panel-body">
      <%= f.fields_for :vote_options do |options_form| %>
        <%= render 'vote_option_fields', f: options_form %>
      <% end %>

      <div class="links">
        <%= link_to_add_association 'add option', f, :vote_options %>
      </div>
    </div>
  </div>

  <%= f.submit 'Create', class: 'btn btn-primary btn-lg' %>
<% end %>
```

`fields_for` helper 方法用于构建一个嵌套的表单，而`vote_option_fields`是我们马上要创建的部分表单。

是 Cocoon 引入的 helper 方法，它呈现一个链接来动态添加新的嵌套字段(在我们的例子中是添加一个新的投票选项)。该方法接受要在页面上显示的链接的名称、表单生成器对象以及关联的复数名称。需要用`div class="links"`包装这个帮助器，因为嵌套字段将被添加到这个包装器之前。此外，这个助手希望在调用它的同一个目录中找到部分“*singular _ association _ name*_ fields”。如果你想让它使用另一个分部，使用`partial`选项，如下所示:

```
link_to_add_association 'add something', f, :somethings,
    :partial => 'shared/something_fields'
```

有很多其他选项可以传递给这个助手。

创建新的分部:

*polls/vote _ option _ fields . html . erb*

```
<div class="nested-fields">
  <div class="form-group">
    <%= f.label :title %>
    <%= f.text_field :title, class: 'form-control', required: true %>
  </div>
  <%= link_to_remove_association "remove option", f %>
</div>
```

这里需要一个带有`nested-fields`类的包装器。`link_to_remove_association`是另一个 Cocoon 助手，用于呈现移除嵌套表单的链接。当提交父表单时，相应的记录也被删除(假设您已经在前面为`accepts_nested_attributes_for`方法指定了`:allow_destroy => true`，并允许控制器中的`_destroy`属性)。

启动您的服务器，尝试创建一个投票和一些相关的选项。看起来很简单，是吧？

### 列出和管理投票

在这一点上，我们必须解决一些细节问题。具体来说，`index`页面不显示已创建的投票列表，也无法编辑或销毁投票。这很容易解决。

首先，向控制器添加更多的方法:

*polls_controller.rb*

```
[...]
def edit
  @poll = Poll.find_by_id(params[:id])
end

def update
  @poll = Poll.find_by_id(params[:id])
  if @poll.update_attributes(poll_params)
    flash[:success] = 'Poll was updated!'
    redirect_to polls_path
  else
    render 'edit'
  end
end

def destroy
  @poll = Poll.find_by_id(params[:id])
  if @poll.destroy
    flash[:success] = 'Poll was destroyed!'
  else
    flash[:warning] = 'Error destroying poll...'
  end
  redirect_to polls_path
end
[...]
```

接下来，添加代码以显示所有可用的投票:

*polls/index.html.erb*

```
[...]
<% @polls.each do |poll| %>
  <div class="well">
    <h2><%= poll.topic %></h2>

    <div class="btn-group">
      <%= link_to 'Edit', edit_poll_path(poll), class: 'btn btn-default' %>
      <%= link_to 'Delete', poll_path(poll),
                  method: :delete,
                  class: 'btn btn-danger', data: {confirm: 'Are you sure?'} %>
    </div>
  </div>
<% end %>
```

另外，添加一个简单的样式来删除页眉的上边距:

*application.css.scss*

```
.well {
  h2 {
    margin-top: 0;
  }
}
```

最后，创建一个编辑视图:

*polls/edit.html.erb*

```
<% content_for(:page_header) {"Edit poll"} %>

<%= render 'form' %>
```

至此，民意调查结束。是时候实现核心功能了:身份验证和实际投票。

## 证明

让我们允许用户通过脸书进行身份验证。要做到这一点，我们需要为 [OmniAuth](https://github.com/intridea/omniauth) 制定一个[脸书战略](https://github.com/mkdynamic/omniauth-facebook)，我在我之前的[帖子](https://www.sitepoint.com/mini-chat-rails/)中已经描述过了。

*Gemfile*

```
[...]
gem 'omniauth-facebook'
[...]
```

然后运行`bundle install`。

好了，现在通过在**config/initializer**目录中创建一个 **omniauth.rb** 文件来设置新的脸书提供程序，其内容如下:

*config/initializer/omni auth . Rb*

```
Rails.application.config.middleware.use OmniAuth::Builder do
  provider  :facebook, 
            ENV['FACEBOOK_KEY'], ENV['FACEBOOK_SECRET'],
            scope: 'public_profile', display: 'page', image_size: 'square'
end
```

通过在[脸书开发者](https://developers.facebook.com/)页面上创建一个新的应用来获得密钥-秘密对，打开“设置”选项卡并添加以下信息:

*   添加新的“平台”(“网站”)
*   在“站点 URL”中填写您站点的地址
*   填写“应用程序域”(应来自网站 URL)
*   填写联系人电子邮件。

接下来，导航到“状态和审查”选项卡，并激活您的应用程序(这将使每个人都可以使用它，仅供参考)。返回“仪表板”，注意“应用程序 ID”和“应用程序密码”——这些是你正在寻找的密钥。将添加到 OmniAuth 初始值设定项中。

创建一个表来存储用户信息。我们只需要一些基本数据:

*   `uid`(`string`)–脸书上唯一的用户标识符
*   `name`(`string`)——姓名
*   `image_url`(`string`)–获取用户头像的 URL

创建适当的迁移:

```
$ rails g model User name:string image_url:string uid:string:index
```

`uid`将频繁用于查找用户，因此将该字段编入索引是个好主意。

现在应用您的迁移:

```
$ rake db:migrate
```

添加一些新路线:

*routes.rb*

```
get '/auth/:provider/callback', to: 'sessions#create'
get '/auth/failure', to: 'sessions#auth_fail'
get '/sign_out', to: 'sessions#destroy', as: :sign_out
```

`/auth/:provider/callback`路由是用户在成功认证后被重定向到的地方(以及包含用户数据的认证散列)。

在控制器上:

*会话 _ 控制器. rb*

```
class SessionsController < ApplicationController
  def create
    user = User.from_omniauth(request.env['omniauth.auth'])
    cookies[:user_id] = user.id
    flash[:success] = "Welcome, #{user.name}!"
    redirect_to root_url
  end

  def destroy
    cookies.delete(:user_id)
    flash[:success] = "Goodbye!"
    redirect_to root_url
  end

  def auth_fail
    render text: "You've tried to authenticate via #{params[:strategy]}, but the following error
occurred: #{params[:message]}", status: 500
  end
end
```

`request.env['omniauth.auth']`包含脸书发送的用户数据。`from_omniauth`方法获取并存储用户数据。如您所见，这个控制器没有什么特别之处。

*型号/用户. rb*

```
class User < ActiveRecord::Base
  class << self
    def from_omniauth(auth)
      uid = auth.uid
      info = auth.info.symbolize_keys!
      user = User.find_or_initialize_by(uid: uid)
      user.name = info.name
      user.image_url = info.image
      user.save!
      user
    end
  end
end
```

我们需要一种方法来检查用户是否经过身份验证。传统上称之为`current_user`:

*application _ controller . Rb*

```
[...]
def current_user
  @current_user ||= User.find_by(id: cookies[:user_id]) if cookies[:user_id]
end

helper_method :current_user
[...]
```

`helper_method`确保`current_user`也可以在视图中使用。

太好了。最后要做的是允许用户验证或显示用户信息以及“注销”链接(如果已经登录):

*layouts/application . html . erb*

```
[...]
<ul class="nav navbar-nav">
  <li><%= link_to 'Add poll', new_poll_path %></li>
</ul>
<ul class="nav navbar-nav navbar-right">
  <% if current_user %>
    <li><%= image_tag current_user.image_url, alt: current_user.name %></li>
    <li><%= link_to 'Logout', sign_out_path %></li>
  <% else %>
    <li><%= link_to 'Sign in', '/auth/facebook' %></li>
  <% end %>
</ul>
[...]
```

在进入下一个迭代之前，如果用户已经过身份验证，让我们在每个投票旁边添加一个“参与”按钮:

*polls/index.html.erb*

```
<% @polls.each do |poll| %>
  <div class="well">
    <h2><%= poll.topic %></h2>

    <p>
      <% if current_user %>
        <%= link_to 'Participate!', poll_path(poll), class: 'btn btn-primary btn-lg block' %>
      <% else %>
        Please sign in via <%= link_to 'Facebook', '/auth/facebook' %> to participate in this poll.
      <% end %>
    </p>

    <div class="btn-group">
      <%= link_to 'Edit', edit_poll_path(poll), class: 'btn btn-default' %>
      <%= link_to 'Delete', poll_path(poll),
                  method: :delete,
                  class: 'btn btn-danger', data: {confirm: 'Are you sure?'} %>
    </div>
  </div>
<% end %>
```

需要一种新的控制器方法:

*polls_controller.rb*

```
[...]
def show
  @poll = Poll.find_by_id(params[:id])
end
[...]
```

认证系统已经准备好了，现在是开始设计投票功能的时候了。

## 投票

正如我们前面讨论的，必须设置用户和投票选项之间的关系，以跟踪哪个用户选择了哪个选项。每个用户可以为许多选项投票(但是他不能为属于同一个投票的多个选项投票)，并且每个选项可以由许多用户选择。因此，我们需要一个带有中间表的多对多关系(也可以使用直接的多对多关系，但是不够灵活)。

让我们调用这个新的中间表`votes`并创建相应的迁移:

```
$ rails g model Vote user:references vote_option:references
```

然后，稍微修改一下迁移文件:

*migrations/XXX _ create _ votes . Rb*

```
[...]
add_index :votes, [:vote_option_id, :user_id], unique: true
[...]
```

这将创建一个聚簇索引，该索引强制执行`vote_option_id`和`user_id`组合的唯一性。显然，同一个用户不能对同一个选项投多张票。

接下来应用此迁移:

```
$ rake db:migrate
```

您的模型文件应该如下所示:

*models/vote.rb*

```
class Vote < ActiveRecord::Base
  belongs_to :user
  belongs_to :vote_option
end
```

将这些关联添加到`User`和`VoteOption`模型中:

*型号/用户. rb*

```
[...]
has_many :votes, dependent: :destroy
has_many :vote_options, through: :votes
[...]
```

*models/vote_option.rb*

```
[...]
has_many :votes, dependent: :destroy
has_many :users, through: :votes
[...]
```

为`show`动作创建一个视图:

*polls/show.html.erb*

```
<% content_for(:page_header) {"Share your opinion"} %>

<h2><%= @poll.topic %></h2>

<%= render 'voting_form' %>
```

实际的投票形式被带到一个单独的部分——这将很快派上用场。

*polls/_ voting _ form . html . erb*

```
<%= form_tag votes_path, method: :post, remote: true, id: 'voting_form' do %>
  <%= hidden_field_tag 'poll[id]', @poll.id %>

  <%= render partial: 'polls/vote_option', collection: @poll.vote_options, as: :option %>

  <% if current_user.voted_for?(@poll) %>
    <p>You have already voted!</p>
  <% else %>
    <%= submit_tag 'Vote', class: 'btn btn-lg btn-primary' %>
  <% end %>
<% end %>
```

这里我们使用不存在的`votes_path`路径来创建一个新的投票。此表单将异步发送。`voted_for?`方法检查用户是否已经参与了指定的投票——它将很快被创建。

如您所见，我们使用`@poll.vote_options`来获取投票选项，所以添加[急切加载](http://guides.rubyonrails.org/active_record_querying.html#eager-loading-associations)是一个好的做法:

*polls_controller.rb*

```
[...]
def show
  @poll = Poll.includes(:vote_options).find_by_id(params[:id])
end
[...]
```

添加新路线:

*routes.rb*

```
[...]
resources :votes, only: [:create]
[...]
```

并创建部分:

*polls/_ vote _ option . html . erb*

```
<div class="form-group">
  <%= content_tag(:label) do %>
    <% unless current_user.voted_for?(@poll) %>
      <%= radio_button_tag 'vote_option[id]', option.id %>
    <% end %>
    <%= option.title %>
  <% end %>
</div>
```

`content_tag`用`label`标签包装单选按钮和选项的标题。如果用户已经参与了投票，则不会显示单选按钮。

是时候实施`voted_for?`方法了:

*型号/用户. rb*

```
[...]
def voted_for?(poll)
  vote_options.any? {|v| v.poll == poll }
end
[...]
```

在这里，我们检查用户是否有属于指定投票的投票选项。稍后，您可以使用[模型缓存](http://edgeguides.rubyonrails.org/caching_with_rails.html#low-level-caching)来提高应用程序的性能，如下所示:

```
def voted_for?(poll)
  Rails.cache.fetch('user_' + id.to_s + '_voted_for_' + poll.id.to_s) { vote_options.any? {|v| v.poll == poll } }
end
```

并在每次用户参与投票时刷新该缓存。

最后，我们需要一个控制器和`create`方法:

*votes_controller.rb*

```
class VotesController < ApplicationController
  def create
    if current_user && params[:poll] && params[:poll][:id] && params[:vote_option] && params[:vote_option][:id]
      @poll = Poll.find_by_id(params[:poll][:id])
      @option = @poll.vote_options.find_by_id(params[:vote_option][:id])
      if @option && @poll && !current_user.voted_for?(@poll)
        @option.votes.create({user_id: current_user.id})
      else
        render js: 'alert(\'Your vote cannot be saved. Have you already voted?\');'
      end
    else
      render js: 'alert(\'Your vote cannot be saved.\');'
    end
  end
end
```

在这里，检查所有必需的参数是否都已发送，用户是否已经过身份验证，以及用户是否还没有参与投票。如果所有这些条件都为真，则创建一个新的投票。否则，显示带有错误消息的警报。在视图上:

*votes/create.js.erb*

```
$('#voting_form').replaceWith('<%= j render 'polls/voting_form' %>');
```

这里，使用之前创建的部分表单，用新的投票表单替换旧的投票表单。

让我们为我们的投票添加一些可视化。

## 显示投票统计数据

目前,`show`页面缺少一个重要部分:它没有显示每个选项有多少用户投票。
的确，这需要修正！然而，只显示投票数是很无聊的，所以让我们利用 Bootstrap 的风格来可视化统计数据:

*polls/_ vote _ option . html . erb*

```
<div class="form-group">
  <%= content_tag(:label) do %>
    <% unless current_user.voted_for?(@poll) %>
      <%= radio_button_tag 'vote_option[id]', option.id %>
    <% end %>
    <%= option.title %>
  <% end %>
  <%= visualize_votes_for option %>
</div>
```

这里我只添加了一行调用`visualize_votes_for` helper 的代码:

*helpers/polls_helper.rb*

```
module PollsHelper
  def visualize_votes_for(option)
    content_tag :div, class: 'progress' do
      content_tag :div, class: 'progress-bar',
                  style: "width: #{option.poll.normalized_votes_for(option)}%" do
        "#{option.votes.count}"
      end
    end
  end
end
```

用另一个`div.progress`包装具有类`progress-bar`的`div`。这些类是由 Bootstrap 提供的
，原本是用来显示[进度条](http://getbootstrap.com/components/#progress)，但是我认为我们也可以在这种情况下使用它们。

`div.progress-bar`有一个`style`属性来设置它的宽度。宽度以百分比指定，显然不应超过 100%。为了确保这一点，我使用了`normalized_votes_for`方法:

*models/poll.rb*

```
[...]
def normalized_votes_for(option)
  votes_summary == 0 ? 0 : (option.votes.count.to_f / votes_summary) * 100
end
[...]
```

首先，检查汇总投票计数是否不为零(`votes_summary`是另一个即将引入的方法)——如果不进行这种检查，我们可能会得到除以零的错误。因此，如果投票的任何选项都没有投票，只需返回零。否则，使用 ActiveRecord 的`count`方法检查指定选项的投票数。这个结果除以总票数，然后乘以 100，将其转换为百分比。

请注意，也应该使用`to_f`将`option.votes.count`转换为 float。否则，除法的结果将始终是一个整数。

这个方法很简单。例如，如果投票共有 10 票，选项 A 有 3 票，选项 B 有 7 票，则:

*   **选项 A** : (3 / 10) * 100 = 30 (%)
*   **选项 B** : (7 / 10) * 100 = 70 (%)

太好了！最后，我们需要`votes_summary`方法:

*models/poll.rb*

```
[...]
def votes_summary
  vote_options.inject(0) {|summary, option| summary + option.votes.count}
end
[...]
```

在这里， [`inject`](http://ruby-doc.org/core-2.1.1/Enumerable.html#method-i-inject) 用于累加值(`0`在这里意味着初始值为零；`summary`是累加器)。同样，您可以使用模型缓存来提高性能。

最后要做的是修改进度条的背景，使它看起来更好一些:

*application.css.scss*

```
[...]
.progress {
  background-image: linear-gradient(to bottom, #bbb 0%, #ccc 100%)
}
[...]
```

这个`votes_summary`方法也可以用在这样的视图中:

*polls/_ voting _ form . html . erb*

```
<%= form_tag votes_path, method: :post, remote: true, id: 'voting_form' do %>
  <%= hidden_field_tag 'poll[id]', @poll.id %>

  <%= render partial: 'polls/vote_option', collection: @poll.vote_options, as: :option %>

  <p><b>Total votes: <%= @poll.votes_summary %></b></p>
[...]
```

*polls/index.html.erb*

```
[...]
<% @polls.each do |poll| %>
  <div class="well">
    <h2><%= poll.topic %> (voted: <%= poll.votes_summary %>)</h2>
[...]
```

在继续之前，我应该提醒你一个小问题。如果您启动服务器并尝试在投票中投票，投票计数会更新，但投票按钮会保留。刷新页面，投票按钮将被替换为“您已经投票”文本。这是因为 Rails 在`voted_for?`方法中缓存了关联。当再次呈现`_voting_form`部分时，该方法提供先前的结果(`false`)，即使实际上创建了新的投票。这个问题至少有三种可能的解决方案。

第一种方法是在使用`reset`方法创建新的投票后清除关联缓存:

*votes_controller.rb*

```
[...]
def create
  [...]
  @option.votes.create({user_id: current_user.id})  + current_user.votes.create({vote_option_id: @option.id})
  current_user.vote_options.reset
[...]
```

第二个是稍微重写了一下`voted_for?`方法:

*user.rb*

```
[...]
def voted_for?(poll)
  votes.any? {|v| v.vote_option.poll == poll}
end
[...]
```

通过这种方式，我们直接指定了中间模型，Rails 将立即知道创建了新的投票。

第三种解决方案是在调用关联时将`force_reload`设置为 true:

*user.rb*

```
[...]
def voted_for?(poll)
  vote_options(true).any? {|v| v.poll == poll }
end
[...]
```

如果你知道这个问题的其他解决方案，请在评论中分享。

### 一点缓存

你可能已经注意到,`option.votes.count`被用在一些方法中。增加一个计数器缓存来改进这个查询怎么样？

解决这个问题的一个可能的方法是使用[反文化](https://github.com/magnusvk/counter_culture)宝石创造了马格努斯·冯·克勒。

将宝石放入您的宝石档案中:

*Gemfile*

```
[...]
gem 'counter_culture', '~> 0.1.23'
[...]
```

并运行`bundle install`。

接下来，运行生成器并应用创建的迁移:

```
$ rails g counter_culture VoteOption votes_count
$ rake db:migrate
```

另外，将以下代码行添加到*vote.rb*中:

```
counter_culture :vote_option
```

现在，投票计数的计数器缓存将被自动创建和管理。很酷，不是吗？

counter_culture gem 可以用在更复杂的场景中，有很多选项，所以请查看它的文档。

### 一点客户端验证

为了稍微改善用户体验，添加一些客户端验证来检查用户在投票前是否选择了其中一个选项。如果没有，显示一个错误，而不是提交表单。有一个很棒的 jQuery 插件叫做 [jQuery Validate](http://jqueryvalidation.org/) ，顾名思义，它可以帮助创建各种验证规则。只需[下载](http://jqueryvalidation.org/files/jquery-validation-1.13.0.zip)插件文件，将它们放在**供应商/资产/JavaScript**中，并将 *jquery.validate.js* 和 *additional-methods.js* 包含到您的项目中:

*application.js*

```
[...]
//= require jquery.validate
[...]
```

在最简单的情况下，您需要做的就是在表单上使用`validate()`方法来使用 Validate 的魔力。但是，我们有一个稍微复杂一点的场景，所以我们必须提供一些选项:

*polls/show.html.erb*

```
[...]
<script data-turbolinks-track="true">
  $(document).ready(function() {
    var voting_form = $('#voting_form');
    voting_form.validate({
      focusInvalid: false,
      errorClass: 'alert alert-warning',
      errorElement: "div",
      errorPlacement: function(error, element) { voting_form.before(error); },
      rules: {
        'vote_option[id]': {
          required: true
        }
      },
      messages: {
        'vote_option[id]': {
          required: "Please select one of the options."
        }
      }
    });
  });
</script>
```

`focusInvalid`表示第一个无效字段不应被聚焦(因为我们有许多单选框，只应选择其中一个)。`errorClass`指定分配给错误消息的 CSS 类。`errorElement`设置错误消息框的包装器，`errorPlacement`提供在放置错误消息之前调用的函数。我希望它放在表单之前，所以我在这里使用 jQuery 的`before`方法(`error`包含实际的错误消息元素)。

`rules`使用验证规则获取对象。只要我们想确保只有一个单选框被选中，就使用`vote_option[id]`(这是单选框的名称)。`messages`反过来用于为提供的规则设置自定义错误消息。

此时，您可以通过尝试提交一个没有选中单选按钮的表单来检查验证是如何工作的。厉害！

## 显示用户的个人资料

我们已经完成了核心功能。用户可以参与投票并检查统计数据。最后要做的事情是创建用户的个人资料页面，以显示他们参与的投票。这可以通过三个简单的步骤来完成:创建控制器、创建视图和添加新的路由。

*users_controller.rb*

```
class UsersController < ApplicationController
  def show
    @user = User.find_by_id(params[:id])
  end
end
```

*users/show.html.erb*

```
<% content_for(:page_header) do %>
  <%= image_tag @user.image_url, alt: @user.name %>
  <%= "#{@user.name}'s profile" %>
<% end %>

<h2>Participation in polls</h2>

<% @user.vote_options.each do |option| %>
  <div class="panel panel-default">
    <div class="panel-heading"><%= link_to option.poll.topic, poll_path(option.poll) %></div>
    <div class="panel-body">
      <%= option.title %>
    </div>
  </div>
<% end %>
```

获取每个用户的投票选项，并将其与投票主题一起显示。在这里使用急切加载来提高性能:

*users_controller.rb*

```
[...]
def show
  @user = User.includes(:vote_options).find_by_id(params[:id])
end
[...]
```

别忘了设定路线:

*routes.rb*

```
[...]
resources :users, only: [:show]
[...]
```

最后要做的事情是提供用户个人资料的链接:

*layouts/application . html . erb*

```
[...]
<ul class="nav navbar-nav navbar-right">
  <% if current_user %>
    <li><%= image_tag current_user.image_url, alt: current_user.name %></li>
    <li><%= link_to 'Profile', user_path(current_user) %></li>
    <li><%= link_to 'Logout', sign_out_path %></li>
  <% else %>
    <li><%= link_to 'Sign in', '/auth/facebook' %></li>
  <% end %>
</ul>
[...]
```

启动您的服务器并检查它！

## 结论

在构建这个 Poller 应用程序时，我们讨论了 nested_attributes、Cocoon gem，看了一下标准化投票计数，并讨论了一些缓存问题，如模型缓存和使用 counter_culture gem。此外，我们还研究了 jQuery Validate 插件和一些奇特的 Bootstrap 样式。我想今天到此为止吧！

当然，这个应用程序可能会通过添加更多的缓存，重新投票或取消投票的能力，更多的样式等等来改进。请随意用演示代码克隆我的 [repo 并进行实验！](https://github.com/bodrovis/SitePointPoller)

希望你觉得这篇文章有用和有趣(我应该创建一个投票来找出答案吗？).谢谢你陪我到最后，再见！

## 分享这篇文章