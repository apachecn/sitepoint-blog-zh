# 使用 Rails 和服务器发送的事件进行迷你聊天

> 原文：<https://www.sitepoint.com/mini-chat-rails-server-sent-events/>

![cloud Sharing Concept](img/73e9a269973034a8cbf05db11458f9b5.png)

最近，我写了两篇关于用 Rails 构建聊天应用程序的文章([用 Rails 进行迷你聊天](https://www.sitepoint.com/mini-chat-rails/)和[用 Rails 和 Faye 进行实时迷你聊天](https://www.sitepoint.com/realtime-mini-chat-rails-faye/)——点击这里查看工作演示[)。在那些文章中，我解释了如何构建一个简单的迷你聊天应用程序，并使用两种方法使其真正异步:AJAX 轮询(不太喜欢)和 Web 套接字。](http://sitepoint-minichat.herokuapp.com)

一位读者在评论中提到 Web Sockets 可以被 HTML5 服务器发送的事件所取代，所以我决定研究这个话题。于是，这篇文章就活过来了:)。

我们将使用前几篇文章中相同的 Rails 应用程序，因此，如果你想继续，可以从相应的 [GitHub repo](https://github.com/bodrovis/SitepointMiniChat) 中克隆它。代码的最终版本位于[上交所](https://github.com/bodrovis/SitepointMiniChat/tree/sse)。

本文涉及的主题有:

*   服务器发送的事件概述
*   使用 Rails 4 `ActionController::Live`进行流式传输
*   Puma 网络服务器的基本设置
*   使用 PostgreSQL 监听/通知功能发送通知
*   用 JSON 和 HTML 呈现新注释(借助下划线. js 模板)

是时候开始了！

## 大意

HTML5 引入了一个 API 来处理服务器发送的事件。SSE 背后的主要思想很简单:web 页面订阅 web 服务器上的事件源，该事件源传输更新。网页不需要经常轮询服务器来检查更新(就像我们用 AJAX 轮询所做的那样)——它们会自动到来。请注意，客户端的脚本只能*监听*更新，不能*发布*任何东西(与客户端可以订阅和发布的 Web Sockets 相比)。因此，所有发布功能都由服务器执行。

大概 SSE 的一个主要缺点就是完全不支持 ie 浏览器(你惊讶吗？).不过，也有一些[聚合填充可用](http://html5doctor.com/server-sent-events/)。

使用 SSE 技术比 Web Sockets 更容易，并且有一些用例，比如 Twitter 更新或更新足球(篮球、排球等)的统计数据。)实时匹配。你可能会对关于比较 Web Sockets 和 SSE 的讨论感兴趣。

回到我们的示例，我们必须执行以下操作:

*   在服务器上创建事件源(路由和控制器动作)
*   添加流功能(幸运的是有`ActionController::Live`，我们将很快讨论它)
*   每次创建评论时发送通知，以便事件源向客户端发送更新(我们将使用 PostgreSQL LISTEN/NOTIFY，但也有其他可能)
*   每次收到通知时呈现新的评论

正如您所看到的，完成这项任务的步骤并不多，但其中一些非常棘手。但是我们不怕困难，那就开始吧！

## 设置应用程序和服务器

如果你正在跟进，从这个 [GitHub repo](https://github.com/bodrovis/SitepointMiniChat) 中克隆源代码，并切换到一个名为`sse`的新分支:

```
$ git checkout -b sse
```

现在，通过移除 Faye 进行一些清理，它为我们提供了 Web Sockets 功能。从**gem 文件**中删除这一行

*Gemfile*

```
[...]
gem 'faye-rails'
[...]
```

以及来自 **application.js** :

*application.js*

```
[...]
//= require faye
[...]
```

删除 **comments.coffee** 文件中的所有代码，删除 **application.rb** 中的 Faye 配置:

*配置/应用. rb*

```
[...]
require File.expand_path('../csrf_protection', __FILE__)
[...]
config.middleware.delete Rack::Lock
config.middleware.use FayeRails::Middleware, extensions: [CsrfProtection.new], mount: '/faye', :timeout => 25
[...]
```

同时删除**config/csrf _ protection . Rb**文件。我们不再需要它了，因为客户端不能用 SSE 发布更新。

向注释模型添加一个新方法，格式化注释的创建日期:

*models/comment.rb*

```
[...]
def timestamp
  created_at.strftime('%-d %B %Y, %H:%M:%S')
end
[...]
```

并从局部调用它:

*comments/_ comment . html . erb*

```
[...]
<h4 class="media-heading"><%= link_to comment.user.name, comment.user.profile_url, target: '_blank' %> says
  [at <%= comment.timestamp %>]</h4>
[...]
```

最后，简化`CommentsController`中的`create`动作:

*comments_controller.rb*

```
[...]
def create
  if current_user
    @comment = current_user.comments.build(comment_params)
    @comment.save
  end
end
[...]
```

太好了，我们准备好了。下一步是设置一个支持多线程的 web 服务器，这是 SSE 所需要的。默认的 WEBrick 服务器缓冲输出，所以它不会工作。在这个演示中，我们将使用由 Evan Phoenix 等人开发的用于高速&并发的 web 服务器。在你的**gem 文件**中替换这一行

*Gemfile*

```
[...]
gem 'thin'
[...]
```

用`gem 'puma'`运行

```
$ bundle install
```

如果您使用的是 Windows，安装 Puma 还需要几个额外的步骤:

*   [下载](http://rubyinstaller.org/downloads/)和[安装](https://github.com/oneclick/rubyinstaller/wiki/Development-Kit)dev kit 包，如果由于某种原因你没有的话
*   [下载](http://packages.openknapsack.org/openssl/openssl-1.0.0k-x86-windows.tar.lzma)并解压 OpenSSL 开发者包(比如到 **c:\openssl**
*   将 OpenSSL 的 dll(从 **openssl/bin** 复制到 **ruby/bin** 目录
*   通过发出`gem install puma -- --with-opt-dir=c:\openssl`命令安装 Puma

如果需要，在此阅读更多[。](https://github.com/hicknhack-software/rails-disco/wiki/Installing-puma-on-windows)

是时候配置彪马了。Heroku 提供了一个很好的[指南](https://devcenter.heroku.com/articles/deploying-rails-applications-with-the-puma-web-server)解释如何设置 Puma，所以让我们使用它。在 **config** 目录下新建一个 **puma.rb** 文件，添加以下内容:

*config/puma.rb*

```
workers Integer(ENV['PUMA_WORKERS'] || 3)
threads Integer(ENV['MIN_THREADS']  || 1), Integer(ENV['MAX_THREADS'] || 16)

preload_app!

rackup      DefaultRackup
port        ENV['PORT']     || 3000
environment ENV['RACK_ENV'] || 'development'

on_worker_boot do
  # worker specific setup
  ActiveSupport.on_load(:active_record) do
    config = ActiveRecord::Base.configurations[Rails.env] ||
        Rails.application.config.database_configuration[Rails.env]
    config['pool'] = ENV['MAX_THREADS'] || 16
    ActiveRecord::Base.establish_connection(config)
  end
end
```

对于一个真正的应用程序，所有这些数字都应该进行调整，尤其是如果你预计会有很多用户同时访问你的网站(否则可用的连接会很快耗尽)。

此外，将 **Procfile** (位于应用程序的根目录中)的内容替换为:

procfile

```
web: bundle exec puma -C config/puma.rb
```

现在快跑

```
$ rails s
```

检查服务器是否正确启动。酷！

我们还需要做一些改动。首先，将**config/environments/development . Rb**中的`config.eager_load`和`config.cache_classes`设置为`true`，以在您的开发人员机器上测试流和 SSE:

*config/environments/development . Rb*

```
[...]
config.cache_classes = true
config.eager_load = true
[...]
```

请记住，将这些设置设为`true`，每次修改代码时都必须重新加载服务器。

最后，将开发数据库改为 PostgreSQL(我们将使用它的 LISTEN/NOTIFY 功能，而 SQLite 不支持它)。如果您的机器上没有安装 RDBMS，请访问[下载](http://www.postgresql.org/download/)部分。安装 PostgreSQL 不是[太差](https://www.google.com/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=install%20postgresql)。

*config/database.yml*

```
[...]
development:
  adapter: postgresql
  encoding: unicode
  database: database_name
  pool: 16
  username: username
  password: password
  host: localhost
  port: 5432
[...]
```

将`pool`的值设置为等于 Puma 的`MAX_THREADS`设置，这样每个用户都可以连接到数据库。

至此，我们已经完成了所有必需的配置，可以继续了！

## 流动

下一步是为我们的服务器添加流功能。对于这个任务，需要一个路由和控制器方法。让我们使用`/comments`路线作为事件源，修改您的路线文件，如下所示:

*config/routes.rb*

```
[...]
resources :comments, only: [:new, :create, :index]
[...]
```

`index`动作需要配备流功能，幸运的是，Rails 4 引入了专门为此任务设计的 [ActionController::Live](http://edgeapi.rubyonrails.org/classes/ActionController/Live/SSE.html) 。我们需要做的就是将这个模块包含到我们的控制器中

*comments_controller.rb*

```
class CommentsController < ApplicationController
  include ActionController::Live
[...]
```

并将响应类型设置为`text/event-stream`

*comments_controller.rb*

```
[...]
def index
  response.headers['Content-Type'] = 'text/event-stream'
[...]
```

现在，在`index`方法中，我们有了流功能。然而，目前我们没有机制来通知这个方法什么时候添加了新的评论，所以它不知道什么时候流更新。现在，让我们在这个方法中创建一个框架，稍后再回到它:

*comments_controller.rb*

```
def index
  response.headers['Content-Type'] = 'text/event-stream'
  sse = SSE.new(response.stream)
  begin
    Comment.on_change do |data|
      sse.write(data)
    end
  rescue IOError
    # Client Disconnected
  ensure
    sse.close
  end
  render nothing: true
end
```

我们使用`sse`局部变量来流更新。`on_change`是一种监听通知的方法，我们将在几分钟后编写它。`rescue IOError`块是用来处理用户断开连接的情况。

这里的`ensure`块至关重要——我们必须关闭流来释放线程。

`write`方法接受一组参数，但是它可以用一个字符串来调用:

```
sse.write('Test')
```

在这里，`Test`将被流式传输到客户端。也可以提供 JSON 对象:

```
sse.write({name: 'Test'})
```

我们还可以通过设置`event`来提供事件名称:

```
sse.write({name: 'Test'}, event: "event_name")
```

然后在客户端使用这个名称来识别发送了哪个事件(对于更复杂的场景)。另外两个选项是`retry`，允许设置重新连接时间，以及`id`，用于跟踪事件的顺序。如果在向浏览器发送 SSE 时连接中断，服务器将收到一个值等于`id`的`Last-Event-ID`报头。

好了，现在我们来关注一下通知。

## 通知

今天，我决定使用 PostgreSQL 的 [LISTEN](http://www.postgresql.org/docs/9.3/static/sql-listen.html) / [NOTIFY](http://www.postgresql.org/docs/9.0/static/sql-notify.html) 功能来实现通知。然而，有一些指南解释了如何使用 [Redis' Pub/Sub](http://redis.io/topics/pubsub) 机制来达到同样的目的。

要发送通知消息，可以使用`after_create`回调:

*models/comment.rb*

```
[...]
after_create :notify_comment_added
[...] 
private

def notify_comment_added
  Comment.connection.execute "NOTIFY comments, 'data'"
end
[...]
```

这里使用原始 SQL 通过`comments`通道发送数据。目前，还不清楚要发送哪些数据，但我们会在适当的时候回到这个方法。

接下来，我们需要编写在前一次迭代中引入的`on_change`方法。该方法监听`comments`通道:

*models/comment.rb*

```
[...]
class << self
  def on_change
    Comment.connection.execute "LISTEN comments"
    loop do
      Comment.connection.raw_connection.wait_for_notify do |event, pid, comment|
        yield comment
      end
    end
  ensure
    Comment.connection.execute "UNLISTEN comments"
  end
end
[...]
```

`wait_for_notify`用于等待通道上的通知。一旦通知(及其数据)到达，我们就将它(存储在`comment`变量中)传递给控制器的方法:

*comments_controller.rb*

```
[...]
Comment.on_change do |data|
  sse.write(data)
end
[...]
```

所以，`data`就是注释。

现在，我们需要在客户端做一些修改来订阅我们新的闪亮事件源。

## 订阅事件源

订阅事件源非常简单:

*评论.咖啡*

```
source = new EventSource('/comments')
```

事件监听器可以附加到`source`。基本的事件监听器称为`onmessage`，当没有命名事件的数据到达时，它被触发。您还记得，在服务器端，我们可以为`write`方法提供一个`event`选项，如下所示:

```
sse.write({name: 'Test'}, event: "event_name")
```

所以，如果没有设置`event`字段，我们的`onmessage`非常简单:

*评论.咖啡*

```
source = new EventSource('/comments')

source.onmessage = (event) ->
  console.log event.data
```

如果使用`event`字段，应使用以下结构:

*评论.咖啡*

```
source = new EventSource('/comments')
source.addEventListener("event_name", (event) ->
  console.log event.data
)
```

好的，我们还没有决定发送哪些数据，但是在我们到达那里之前还有一些其他的事情要做。用户发送评论后，禁用“发布”按钮:

*评论.咖啡*

```
[...]
jQuery ->
  $('#new_comment').submit ->
    $(this).find("input[type='submit']").val('Sending...').prop('disabled', true)
```

然后，一旦保存了注释，启用按钮并清除文本区域:

*comments/create.js.erb*

```
<% if !@comment || @comment.errors.any? %>
alert('Your comment cannot be saved.');
<% else %>
$('#comment_body').val('');
$('#new_comment').find("input[type='submit']").val('Submit').prop('disabled', false)
<% end %>
```

最后，所有的部分都准备好了，是时候把它们粘在一起，完成拼图了。进行下一次迭代！

## 把这些放在一起

是时候解决最后一个问题了——将向客户端发送什么数据来轻松呈现新添加的评论？我将向您展示两种可能的解决方案:使用 JSON 和使用 HTML(借助于`render_to_string`方法)。

### 将数据作为 JSON 传递

首先，让我们使用 JSON 来尝试这种方法。我们所要做的就是发送带有 JSON 格式的新注释数据
的通知，然后重新发送给客户机，解析它，并呈现注释。很简单。

创建一个新方法来准备 JSON 数据，并从`notify_comment_added`调用它:

*models/comment.rb*

```
[...]
def basic_info_json
  JSON.generate({user_name: user.name, user_avatar: user.avatar_url, user_profile: user.profile_url,
                 body: body, timestamp: timestamp})
end

private

def notify_comment_added
  Comment.connection.execute "NOTIFY comments, '#{self.basic_info_json}'"
end
[...]
```

如您所见，simple 生成了一个 JSON 对象，其中包含显示注释所需的所有数据。请注意，这些单引号是必需的，因为我们必须将该字符串作为 SQL 查询的一部分发送。如果没有单引号，您将得到一个“不正确的陈述”错误。

在控制器中使用这些数据:

*comments_controller.rb*

```
def index
  response.headers['Content-Type'] = 'text/event-stream'
  sse = SSE.new(response.stream)
  begin
    Comment.on_change do |comment|
      sse.write(comment)
    end
  rescue IOError
    # Client Disconnected
  ensure
    sse.close
  end
  render nothing: true
end
```

在控制器中，将接收到的数据发送到客户端。

在客户端，我们必须解析数据并使用它来呈现注释。然而，正如您所记得的，我们的评论模板相当复杂。当然，我们*可以*只创建一个包含 HTML 标记的 JS 变量，但是那样会很繁琐和不方便。因此，让我们使用[下划线. js](http://underscorejs.org/) 模板(你可以选择其他替代物——例如，[把手。JS](http://handlebarsjs.com/) )。

首先，将下划线. js 添加到项目中:

*Gemfile*

```
[...]
gem 'underscore-rails'
[...]
```

奔跑

```
$ bundle install
```

*application.js*

```
[...]
//= require underscore
[...]
```

然后创建一个新模板:

*templates/_comment.html*

```
<script id="comment_temp" type="text/template">
  <li class="media comment">
    <a href="<%%- user_profile %>" target="_blank" class="pull-left">
      <img src="<%%- user_avatar %>" class="media-object" alt="<%%- user_name %>" />
    </a>
    <div class="media-body">
      <h4 class="media-heading">
        <a href="<%%- user_profile %>" target="_blank"><%%- user_name %></a>
        says
        [at <%%- timestamp %>]</h4>
      <p><%%- body %></p>
    </div>
  </li>
</script>
```

我给了这个模板一个 ID`comment_temp`,以便以后引用。我只是简单地复制了**comment/_ comment . html . erb**文件中的所有内容，并使用`来标记应该插入可变内容的地方。`

 `我们必须在页面中包含此模板:

*comments/new.html.erb*

```
[...]
<%= render 'templates/comment' %>
```

现在我们可以使用这个模板了:

*评论.咖啡*

```
source = new EventSource('/comments')

source.onmessage = (event) ->
  comment_template = _.template($('#comment_temp').html())
  comment = $.parseJSON(event.data)
  if comment
    $('#comments').find('.media-list').prepend(comment_template( {
      body: comment['body']
      user_name: comment['user_name']
      user_avatar: comment['user_avatar']
      user_profile: comment['user_profile']
      timestamp: comment['timestamp']
    } ))

[...]
```

`comment_template`包含模板的内容，`comment`是服务器发送的数据。我们通过将所有需要的数据传递给模板，在注释列表中添加一个新的注释。太棒了。

然而，这种解决方案有一个缺点。如果注释模板需要修改，我们需要在两个地方更改标记。我认为这更适合于不需要任何复杂标记来呈现数据的情况。

### 以 HTML 形式传递数据

现在让我们看看另一个解决方案:-使用`render_to_string`方法。在这个场景中，我们只需要将新注释的 ID 发送到控制器，获取注释，呈现其模板，并将生成的标记发送到客户端。在客户端，这个标记只需要插入到页面上，不需要修改。

调整您的模型:

*车型/评论. rb*

```
[...]
private

def notify_comment_added
  Comment.connection.execute "NOTIFY comments, '#{self.id}'"
end
[...]
```

控制器:

*comments_controller.rb*

```
def index
  response.headers['Content-Type'] = 'text/event-stream'
  sse = SSE.new(response.stream)
  begin
    Comment.on_change do |id|
      comment = Comment.find(id)
      t = render_to_string(partial: 'comment', formats: [:html], locals: {comment: comment})
      sse.write(t)
    end
  rescue IOError
    # Client Disconnected
  ensure
    sse.close
  end
  render nothing: true
end
```

`render_to_string`方法类似于`render`，但是它不将结果作为响应体发送给浏览器——它将结果保存为字符串。注意，我们必须提供`formats`，否则 Rails 将搜索格式为`text/event-stream`的片段(因为我们已经在前面设置了响应头)。

最后，在客户端:

```
source = new EventSource('/comments')

source.onmessage = (event) ->
  $('#comments').find('.media-list').prepend($.parseHTML(event.data))
```

[…]

那很简单。只需通过解析接收到的字符串来添加新的注释。

此时，启动服务器，在两个窗口中打开您的应用程序，并与自己愉快地聊天。消息将几乎立即显示，无需任何重新加载，就像我们使用 Web Sockets 时一样。

## 结论

在用服务器发送事件替换 Web 套接字的过程中，我们已经了解了 Puma、`ActionController::Live`、PostgreSQL 监听/通知功能和下划线. js 模板的使用。总而言之，我认为这个解决方案不如 Faye 的 Web Sockets 更可取。

首先，它涉及额外的开销，并且只适用于 PostgreSQL(或者 Redis，如果您使用它的发布/订阅机制的话)。此外，使用 Web 套接字似乎更适合我们的需求，因为我们需要客户端的订阅和发布机制。

我希望这篇文章对你有意思！你曾经在你的项目中使用过服务器发送事件吗？你更喜欢使用 Web Sockets 来构建一个类似的聊天应用程序吗？分享一下你的看法，我很想知道！` 

## `分享这篇文章`