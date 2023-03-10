# 机器人、Lita 和 HipChat 带来的乐趣

> 原文：<https://www.sitepoint.com/fun-robots-lita-hipchat/>

![lita](img/ca16a08b00d86be41da11afc120a6468.png)

我打赌你看过某种科幻电影，其中一个英雄启动一台超级电脑，而不是用键盘和鼠标手动完成所有任务，只是使用语音命令(喝咖啡或清洗超级英雄服装时)。看起来真的很酷。难道我们不能用 Ruby 创建一些类似但更简单的东西吗？

例如，拥有某种机器人，它会对我们用键盘输入的命令做出反应。此外，如果可以添加新的命令来扩展我们机器人的功能，那就太好了。这很容易做到！

来认识一下 [Lita](https://www.lita.io/) ，一个可爱的机器人伴侣。它可以连接到任何聊天服务，并很容易通过自定义插件进行扩展。

在本文中，我将向您展示如何设置 Lita，将它连接到 [HipChat](http://hipchat.com) (一个流行的聊天平台)，并使用新命令增强我们的聊天机器人，以便与示例 Rails 应用程序的 API 进行交互。

示例 todo 应用程序的源代码可在[github.com/bodrovis/SitePointReminder](https://github.com/bodrovis/SitePointReminder)获得。

该机器人的源代码可在 github.com/bodrovis/SitePointBot 获得。

## 丽塔的特征

好吧，丽塔能给我们提供什么好东西？正如我所说，它可以与任何聊天服务。目前支持 [IRC](https://github.com/jimmycuadra/lita-irc) 、 [HipChat](https://github.com/jimmycuadra/lita-hipchat) 、
Campfire、 [Slack](https://github.com/kenjij/lita-slack) 、 [Twitter](https://github.com/fukayatsu/lita-twitter) 和 Japanese[ido bata](https://github.com/fukayatsu/lita-idobata)，但是你可以自己编写适配器来连接任何你需要的平台。

已经创建了各种各样的[插件](https://www.lita.io/plugins)来提供不同的功能(比如获取 whois 信息、查询谷歌、与 JIRA 互动等等),你可以轻松地编写自己的插件。

有了 Lita，你可以自动完成繁琐的任务，只需在聊天室输入“Lita，请这样做”就可以了。当然，这个项目是开源的，并且是用世界上最好的编程语言 Ruby 编写的:)。

我希望你渴望马上开始摆弄 Lita！

## 使用 HipChat 连接

对于这个演示，我决定使用 HipChat，所以你需要访问[hipchat.com](http://hipchat.com)并创建两个账户:一个给你，一个给你忠实的机器人。确认一下，暂时放在一边。

接下来，通过运行以下命令安装 [lita](https://github.com/jimmycuadra/lita) 本身:

```
$ gem install lita
```

如果你用的是 Windows，那么就会出现与 [Puma](http://puma.io/) 安装相关的错误，因为这个 web 服务器是 Lita 的[依赖项](https://github.com/jimmycuadra/lita/blob/master/lita.gemspec#L29)之一。要解决这个问题，请参考本指南。简而言之，您需要下载并解压[这个 OpenSSL 包](http://packages.openknapsack.org/openssl/openssl-1.0.0k-x86-windows.tar.lzma)。然后，将`libeay32.dll`和`ssleay32.dll`复制到您的`ruby/bin`目录中，并运行以下命令:

```
$ gem install puma -- --with-opt-dir=c:\openssl
```

用提取 OpenSSL 的路径替换`c:\openssl`。当你完成后，新的`lita`命令应该可以从你的命令行得到。

在创建新机器人之前，还应该安装 Redis DBMS，因为 Lita 将它用作数据存储。访问[redis.io/download](http://redis.io/download)页面选择适合你的版本(也有非官方的 Windows 版本)。启动 Redis 后，您就可以继续了！

键入以下命令创建一个新的 Lita 项目(姑且称之为 Bot):

```
$ lita new bot
```

将创建一个 **bot** 目录和几个文件。首先，通过取消注释下面一行来编辑 **Gemfile** :

*Gemfile*

```
[...]
# gem "lita-hipchat"
[...]
```

下次运行

```
$ bundle install
```

HipChat 适配器现在已经就绪。接下来打开 **lita_config.rb** ，它包含了所有必要的设置。更改这些参数:

*   `config.robot.name`(您在 HipChat 上创建机器人账户时提供的)
*   `config.robot.adapter`(设置为`:hipchat`)
*   `config.redis`(在这里传递一个哈希，比如:`{host: "127.0.0.1", port: 6379}`)
*   `config.adapters.hipchat.jid`(要找到它，请访问[hipchat.com](http://hipchat.com)，打开账户设置，然后导航到 XMPP/Jabber 信息页面。“Jabber ID”字段是您所需要的)
*   `config.adapters.hipchat.password`(机器人的密码)
*   `config.adapters.hipchat.debug`(设置为`true`在控制台中查看调试信息。例如，如果您的机器人无法连接到聊天平台，这将非常有用)
*   `config.adapters.hipchat.rooms`(聊天室名称或`:all`)

太好了！更改这些设置后，您就可以开始连接了。类型

```
$ lita
```

在您的控制台中(从 bot 文件所在的目录)启动应用程序。接下来，去 hipchat.com，用你的个人账户登录。加入聊天室，你的机器人将已经在那里等待你的命令！当然，在这一点上，并不是很有用。您可以通过键入以下命令来检查 Lita 的版本:

```
@lita info
# or
lita, info
# or
lita: info
# or
lita info
```

不要忘记用你的机器人的名字替换`lita`。顺便说一句，如果你不想在发布命令时总是指定你的机器人的名字，把`config.adapters.shell.private_chat`选项设置为`true`。这将所有消息视为命令，因此您不需要在它们前面加上 Lita 的名称。

如果这个命令成功，那么一切都正常。

## 集成 Lita 插件

你的机器人可以响应非常有限的命令范围(比如显示版本或加入一个房间——阅读更多关于它们的信息[这里](http://docs.lita.io/getting-started/usage/))。幸运的是，已经有各种各样的[插件](https://www.lita.io/plugins)可供 Lita 使用。它们分为三种类型:

*   **适配器**用于连接特定的聊天服务(如`lita-hipchat`)。
*   **处理程序**添加新的功能，用户将在运行时与之交互。具体来说，他们可以与[聊天](http://docs.lita.io/plugin-authoring/handlers/#chat-routes)和 [HTTP](http://docs.lita.io/plugin-authoring/handlers/#http-routes) 路线。我们稍后将讨论聊天路线。
*   **扩展**为开发其他插件和扩展核心 Lita 框架提供了新的特性。它们用在复杂的场景中，所以在很多情况下，你不需要使用它们。

让我们尝试将几个处理程序集成到我们的项目中。例如，有一个 [lita-google-images](https://github.com/jimmycuadra/lita-google-images) 处理程序，它使用指定的关键字查询 Google Images，并返回一个相关的图像作为结果。这个插件真的很容易集成。只需将它添加到您的 gem 文件中:

*Gemfile*

```
[...]
gem "lita-google-images"
[...]
```

奔跑

```
$ bundle install
```

重启你的机器人。现在，您可以发出如下命令:

```
@lita image cat
@lita img cat
```

酷！

另一个潜在有用但非常容易集成的扩展是 [lita-whois](https://github.com/glebtv/lita-whois) ，它搜索 whois 记录(在 [Whois](https://github.com/weppos/whois) 客户端的帮助下)。再一次把这一行放到你的 gem 文件中:

*Gemfile*

```
[...]
gem "lita-whois"
[...]
```

奔跑

```
$ bundle install
```

重启 Lita。现在，您可以发出如下命令:

```
@lita whois example.com
@lita whois 8.8.8.8
```

查看完整信息。

如果你想在本地服务器的某个地方记录下所有的聊天活动(当你不在线的时候，看看你的同事在谈论你:)，那么就使用 [lita-logger](https://github.com/machadolab/lita-logger) 。这需要通过 **lita_config.rb** 文件进行一些基本的设置:

lita _ config . Rb

```
[...]
config.handlers.logger.log_file = "chat.log" # replace this with a path to your log file
config.handlers.logger.enable_http_log = true
[...]
```

别忘了跑步

```
$ bundle install
```

然后重启你的应用。现在，聊天中的所有活动都将被记录到指定的文件中。大哥哥(还是姐姐？)在看着你，呃？

作为作业，集成 [lita-chuck_norris](https://github.com/MrTin/lita-chuck_norris) 扩展，在你觉得无聊的时候读一些关于这个硬汉的笑话(它们是通过向`http://api.icndb.com/jokes/random.json`发出 GET 请求获取的)。顺便说一下，还有许多其他类似的插件只是为了好玩而创建的，所以请查看[插件页面](https://www.lita.io/plugins)。也许你会受到启发，自己创造一些东西！

## 创建(又一个)待办事项应用程序

拜托，别那样看着我。你可能已经厌倦了 Todo 应用程序，但是在这个演示中，它只是一个配角。我们需要一个应用程序，让我们的机器人通过 API 与之交互。

该应用程序将支持以下功能:

*   创建待办事项
*   编辑待办事项
*   列出待办事项
*   将待办事项标记为完成(实质上意味着销毁它)

所有这些操作都可以通过一个简单的 API 来访问，我们的机器人将使用这个 API。没有认证，授权之类的东西。

这个应用程序实际上可以用任何语言/框架编写，但我将坚持使用 Rails 4.1。创建一个没有默认测试套件的名为 Reminder 的新应用程序:

```
$ rails new reminder -T
```

你可以设计一点风格，或者保留默认的难看的主题，因为这种风格与我们的目标无关。如果你想继续，那么把这个宝石添加到**宝石文件**中:

```
[...]
gem 'bootstrap-sass'
[...]
```

另外，确保`gem 'jbuilder'`未被注释，因为我们将需要它通过 API 返回结果。

那就跑

```
$ bundle install
```

将这些行放到 **application.css.scss** 文件中

*application.css.scss*

```
[...]
@import 'bootstrap';
@import 'bootstrap/theme';
[...]
```

并且调整布局以利用 Bootstrap 的样式，如果您想要:

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

现在，让我们生成一个非常简单的 Todo 模型，并应用相应的迁移:

```
$ rails g model Todo title
$ rake db:migrate
```

添加一些必要的路线:

*config/routes.rb*

```
[...]
resources :todos, only: [:new, :create, :index, :destroy]
root to: 'todos#index'
[...]
```

接通控制器。让我们从`index`动作开始:

*todos_controller.rb*

```
class TodosController < ApplicationController
  def index
    respond_to do |format|
      @todos = Todo.order('created_at DESC')
      format.html
      format.json
    end
  end
end
```

我们已经决定构建一个简单的 API，所以利用`respond_to`方法向用户呈现所需的格式。还应该创建相应的视图。首先，HTML 格式:

*todos/index.html.erb*

```
<% content_for(:page_header) { "Your todos" } %>

<%= link_to 'Add todo', new_todo_path, class: 'btn btn-primary' %>

<div class="panel">
  <div class="panel-body">
    <ul>
      <% @todos.each do |todo| %>
        <li><%= todo.title %> <%= link_to 'Done', todo_path(todo), method: :delete %></li>
      <% end %>
    </ul>
  </div>
</div>
```

和 JSON 格式:

*全部/索引。json.jbuilder*

```
json.array! @todos.each do |todo|
  json.id todo.id
  json.title todo.title
end
```

我们正在渲染一个 todos 数组；每个都有一个`id`和一个`title`。不要忘记，你需要 [jBuilder](https://github.com/rails/jbuilder) 连接到项目中，这样才能正常工作。

好了，现在是`new`和`create`动作:

*todos_controller.rb*

```
class TodosController < ApplicationController
  [...]
  def new
    @todo = Todo.new
  end

  def create
    @todo = Todo.new(todo_params)
    respond_to do |format|
      if @todo.save
        format.html do
          flash[:success] = 'Todo created!'
          redirect_to root_path
        end
        format.json { head :no_content }
      else
        format.html { render :new }
        format.json { render json: @todo.errors.full_messages, status: :unprocessable_entity }
      end
    end
  end
  [...]

  private

  def todo_params
    params.require(:todo).permit(:title)
  end
end
```

我们再次使用`respond_to`。注意，`new`动作不需要相应的`.json`视图，因为响应就在控制器中呈现。

最后，`destroy`行动:

*todos_controller.rb*

```
def destroy
  todo = Todo.find_by_id(params[:id])
  respond_to do |format|
    if todo && todo.destroy
      format.html do
        flash[:success] = 'Todo marked as done!'
        redirect_to root_path
      end
      format.json { head :no_content }
    else
      format.html do
        flash[:warning] = 'There was an error.'
        redirect_to root_path
      end
      format.json { head 500 }
    end
  end
```

还有一件事:调整`ApplicationController`,这样当没有提供 CSRF 令牌时(例如创建记录时)就不会引发异常。在一个真正的应用程序中，你会设置某种身份验证来阻止用户做一些他们不应该做的事情，但我们今天没有这样做:

*application _ controller . Rb*

```
[...]
protect_from_forgery
[...]
```

我刚刚删除了这里的`with: exception`，这样就使用了`:null_session`(这是默认选项)。顺便说一下，在这条线的上方有一个提示:

```
# Prevent CSRF attacks by raising an exception.
# For APIs, you may want to use :null_session instead.
```

这样你就不会忘记这件事。在此阅读更多关于此[的内容。](http://api.rubyonrails.org/classes/ActionController/RequestForgeryProtection/ClassMethods.html)

太棒了。又一个 todo 应用程序被创建，我们可以教我们的机器人如何使用它。

## 创建自定义 Lita 处理程序

记住，Lita 有三种类型的插件:适配器、处理程序和扩展。为了添加自定义功能，请构建一个扩展。

如果你计划与世界分享你的新闪亮插件，那么使用以下三个命令之一:

*   `lita adapter NAME`
*   `lita handler NAME`
*   `lita extension NAME`

这将为您设置基本的文件结构，并在插件的名称前加上前缀`lita-`(这是 Lita 的作者引入的一个约定)。稍后，你可以将你的代码打包成一个宝石，发布到 [RubyGems](http://rubygems.com) ，并开始接收来自用户的狂热反馈。

然而，我们的处理程序仅供内部使用，所以让事情简单一些。继续在你的 **bot** 目录下创建一个新文件 **lita-reminder.rb** ，该目录当前包含 **lita_config.rb** 和 **Gemfile** 文件。

通过在顶部添加以下行来修改配置文件:

lita _ config . Rb

```
require './lita-reminder'
[...]
```

现在丽塔知道了我们新的，但却是空的，负责人。将以下代码粘贴到 **lita-reminder.rb** 中:

*lita-reminder.rb*

```
module Lita
  module Handlers
    class Reminder < Handler
    end
  end
  Lita.register_handler(Reminder)
end
```

机器人启动时，处理器将被注册。是充实它的时候了。让我们实施三项行动:

*   全部加进去
*   销毁(标记为完成)待办事项
*   列出待办事项

将需要一个[聊天路线](http://docs.lita.io/plugin-authoring/handlers/#chat-routes)来告诉我们的机器人何时执行哪个动作。基本上，聊天路线是一个正则表达式。如果 Lita 在其中一条消息中找到匹配，它将执行相应的操作。让我们创建一个:

*lita-reminder.rb*

```
[...]
class Reminder < Handler
  route(/^remind plz$/, :index, command: true, help: { "remind plz" => "Remind about your todos." })
end
[...]
```

当 Lita 看到一个以“remind plz”开头的消息时，它会调用一个`index`方法，这个方法很快就会被创建。这里的`command: true`是指只有在消息中提到了 bot 才会触发这条路由(消息要么是私有的，要么是以 bot 的名字为前缀)。`help`包含了路由调用的例子和它的作用。在我们的例子中，键入

```
@lita help remind plz
```

查看此帮助消息。请注意，带有帮助的响应将始终作为私人消息发送——不确定这是错误还是预期行为。

我们现在可以实现`index`方法和一些实用方法:

*lita-reminder.rb*

```
[...]
def index(response)
  todos = parse get("http://127.0.0.1:3000/todos.json")
  if todos.any?
    response.reply("Your todos:")
    todos.each do |todo|
      response.reply("# #{todo['id']}: #{todo['title']}")
    end
  else
    response.reply('You have done all the todos! Good job!')
  end
end

private

def get(url)
  Net::HTTP.get make_uri(url)
end

def parse(obj)
  MultiJson.load(obj)
end

def make_uri(url)
  URI(url)
end

[...]
```

这些实用方法很简单:`get`用于向提供的 URI 发送 GET 请求，`parse`解析一个 JSON 响应(`MultiJson`在 Lita 核心中连接)，而`make_uri`从提供的 URL 创建一个 URI。

传递给`index`方法的`response`参数是什么？它是`Lita::Response`类的一个实例，是检查消息细节和响应消息的主要接口。它有几个有用的方法。这里，我们只使用一个–`reply`,它用提供的字符串进行回复。

基本上，`index`方法向`http://127.0.0.1:3000/todos.json`发送一个 GET 请求，解析响应，或者以`ID - title`格式呈现一个 todo 列表，或者说没有找到 todo。

如果在未来，我们的 todos 应用程序被转移到生产服务器上会怎样？我们需要在使用它的任何地方改变它的链接。难道我们不能创建某种设置，让用户可以提供一个链接到那里的 todo 应用程序吗？看来我们可以！

*lita-reminder.rb*

```
[...]
config :server

[...]

def index(response)
  todos = parse get("#{config.server}/todos.json")
  [...]
end

[...]
```

这里的`config :server`是指`server`是可供用户指定的选项。然后可以通过使用`config.server`来访问它(顺便说一下，所有的配置变量在运行时都被冻结了)。提供这个新设置:

lita _ config . Rb

```
[...]
Lita.configure do |config|
    [...]
    config.handlers.reminder.server = 'http://127.0.0.1:3000'
end
```

不错！现在，添加用于添加新 todo 的路线:

*lita-reminder.rb*

```
[...]

module Lita
  module Handlers
    class Reminder < Handler
      route(/^todo\s+(.+)$/, :create, command: true, help: { "todo TODO" => "Adds new todo to the list." })

      def create(response)
        todo = response.match_data[1]
        result = post "#{config.server}/todos.json", 'todo[title]' => todo
        if result.code.to_i.success?
          response.reply("#{todo} was added.")
        else
          response.reply("I've encountered the following errors while saving your todo: #{parse(result.body)}")
        end
      end

      private

      def post(url, data = {})
        Net::HTTP.post_form(make_uri(url), data)
      end
    end
    Lita.register_handler(Reminder)
  end
end

class Numeric
  def success?
    self > 199 && self < 300
  end
end
```

在路由中，我使用一个捕获组来获取新 todo 的名称。`create`方法使用`response.match_data[1]`获取匹配数据中的这个捕获组(`match_data`是`response`中的另一个可用方法)。代码的其余部分非常简单:我们发出一个 POST 请求，提供 todo 的标题。

最后，摧毁路线:

*lita-reminder.rb*

```
[...]
route(/^done todo\s+(\d+)$/, :destroy, command: true, help: { "done todo TODO_ID" => "Marks todo with the specified number as done." })
[...]
def destroy(response)
  todo_id = response.match_data[1]
  result = delete "#{config.server}/todos/#{todo_id}.json"
  if result.code.to_i.success?
    response.reply("Todo # #{todo_id} was marked as done.")
  else
    response.reply("I've encountered an error while marking your todo as done.")
  end
end

private

def delete(url)
  uri = make_uri(url)
  http = Net::HTTP.new(uri.host, uri.port)
  request = Net::HTTP::Delete.new(uri.path)
  http.request(request)
end
[...]
```

应该提供要销毁的 todo 的 ID。稍后，使用`match_data[1]`构造获取 todo。然后，发送删除请求。

顺便说一句，如果你不想你的 todos 被完全摧毁，那么[可能会采取类似的解决方案。](https://github.com/ActsAsParanoid/acts_as_paranoid)

启动你的机器人，并尝试管理你的待办事项。如果 Lita 也能为你做一顿晚餐，岂不是很酷？

## 结论

在本文中，我们看了 Lita，一个可扩展的机器人伴侣。我们将它与 HitChat 集成在一起，添加了许多插件，甚至编写了自己的句柄来与 todo 应用程序 API 进行通信。请随意试验这段代码或创建更复杂的东西(不要忘记在评论中分享链接！).此外，如果您曾经在生产中使用过 Lita 来执行一些实际任务，请分享您的经验——那会很有趣！

## 分享这篇文章