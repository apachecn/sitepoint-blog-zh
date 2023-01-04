# 与 Rails 和 Faye 的实时迷你聊天

> 原文：<https://www.sitepoint.com/realtime-mini-chat-rails-faye/>

![Comments - Icon](img/9f86f398b3e2e33a911d453e2a7e1cae.png)

在本文的前一部分中，我们构建了一个消息应用程序，允许用户通过脸书或 Twitter 登录并发表评论。我们还为评论表单配备了 AJAX，这样就不会在每次发布评论时都重新加载页面。

然而，我们的应用程序有一个问题——用户每次想要查看新评论时都必须重新加载页面。那不太方便，是吗？怎么才能解决这个问题？

至少有两种解决方案:AJAX 轮询和 web 套接字。第一个已经相当过时了，但是在一些不需要即时更新的场景中仍然可以使用。后者似乎更适合我们的情况。不过，还是两个都实现吧，看看哪个最好！

源代码可以在 [GitHub](https://github.com/bodrovis/SitepointMiniChat) 获得。

AJAX 轮询的源代码可以在 [comments_polling 分支](https://github.com/bodrovis/SitepointMiniChat/tree/comments_polling)获得。

工作演示可以在[http://sitepoint-minichat.herokuapp.com/](http://sitepoint-minichat.herokuapp.com/)找到。

## AJAX 轮询

我建议切换到一个新的分支(如果您正在使用 Git)来开始实现这个功能:

```
$ git checkout -b polling
```

AJAX 轮询背后的关键思想是，我们有某种计时器，它在几秒钟后触发，并发送一个请求，检查是否有任何新的注释。然后，新的注释会显示在页面上。循环重复。

不过，这个方案也有一些问题。首先，即使没有新的评论，我们也在不断地发送这些投票请求。其次，新的评论不会立即出现在页面上——它们只会在下一个投票阶段显示。轮询还有第三个问题，我们稍后会讨论。

为了开始我们的实现，创建一个新的`index` route 来检查新的注释:

*config/routes.rb*

```
[...]
resources :comments, only: [:new, :create, :index]
[...]
```

如果你正在使用 [Turbolinks](https://github.com/rails/turbolinks/) 并且还没有包含 [jquery-turbolinks](https://github.com/kossnocorp/jquery.turbolinks) gem，现在就做，否则默认的`jQuery.ready()`函数将无法工作:

*Gemfile*

```
[...]
gem 'jquery-turbolinks'
[...]
```

*application.js*

```
[...]
//= require jquery.turbolinks
//= require turbolinks
```

现在创建一个新文件*。咖啡*文件有以下代码:

*polling.coffee:*

```
window.Poll = ->
  setTimeout ->
    $.get('/comments')
  , 5000

jQuery ->
  Poll() if $('#comments').size() > 0
```

不要忘记需要这个文件:

*application.js*

```
[...]
//= require polling
```

我们正在`window`对象上创建一个新函数`Poll`(否则它对
其他脚本不可见)。这个函数设置了一个超时，在 5 秒钟后触发，并向`/comments`资源发送一个 GET 请求。如果页面上有一个`#comments`块，那么在 DOM 加载后立即调用这个函数。我们本来可以使用`setInterval`来代替，但是如果我们的服务器响应很慢，我们可能会以发送请求太快而告终。

现在创建一个`index`方法和相应的视图:

*comments_controller.rb*

```
[...]
def index
  @comments = Comment.order('created_at DESC')
end
[...]
```

*comments/index.js.erb*

```
$('#comments').find('.media-list').html('<%= j render @comments %>');
window.Poll();
```

我们正在用新的注释替换所有的注释，并再次设置计时器开始计时。你可能认为这不是一个最优的解决方案，你是对的。但现在，启动服务器，在两个独立的窗口中打开你的应用程序，并尝试发布一些评论。您的注释应该出现在两个窗口中。

太好了，现在你可以在无聊的时候和自己聊天了！

让我们优化我们的解决方案，让`index`动作只返回新的评论，而不是所有的评论。其中一个
方法是提供页面上最后一个可用评论的`id`。这样，资源将只加载比提供的多一个`id`的评论。

为了实现这一点，我们需要稍微调整一下我们的注释分部:

*comments/_ comment . html . erb*

```
<li class="media comment" data-id="<%= comment.id %>">
[...]
```

现在每个评论都有一个存储其`id`的`data-id`属性。也改变了`index`的方法:

*comments_controller.rb*

```
[...]
def index
  @comments = Comment.where('id > ?', params[:after_id].to_i).order('created_at DESC')
end
[...]
```

我们现在唯一需要做的就是发送一个 GET 请求，并提供页面上最后一条评论的`id`。然而，事情开始变得有点复杂。

想象一下这种情况:用户 A 和用户 B 打开了我们的消息应用。用户 A 发布了他的 id 为 50 的评论。用户 B 随后也发表了一条评论，并被分配了一个 id 51。现在进入轮询阶段。在用户 A 这边，一切都很好——发送了一个 id 为 50 的 GET 请求，因此显示了一个 id 为 51 的新注释。

但是，用户 B 看不到用户 A 的评论，因为将发送一个 id 为 51 的 GET 请求，而 50 小于 51，因此不会为用户 B 显示新的评论！为了克服这个问题，我们可以在评论发布后强制启动一个轮询阶段。

让我们稍微重构一下我们的 JS 代码:

*polling.coffee*

```
window.Poller = {
  poll: (timeout) ->
    if timeout is 0
      Poller.request()
    else
      this.pollTimeout = setTimeout ->
        Poller.request()
      , timeout || 5000
  clear: -> clearTimeout(this.pollTimeout)
  request: ->
    first_id = $('.comment').first().data('id')
    $.get('/comments', after_id: first_id)
}

jQuery ->
  Poller.poll() if $('#comments').size() > 0
```

我已经用一个`Poller`对象替换了`Poll`函数。这里有趣的部分是`poll`函数。这个函数只接受一个参数-`timeout`。如果`timeout`等于零，立即发起轮询请求，而不创建任何计时器。你可能认为这个条件是不需要的，因为`setTimeout(function() {}, 0);`应该会瞬间开火。然而，事实并非如此。实际上，在调用函数之前仍然会有延迟，所以我们根本不需要`setTimeout`。

如果`timeout`不等于零，创建一个`pollTimeout`函数并将其附加到`Poller`对象上。请注意，我们为超时提供了默认值 5000。

`clear`简单清零`pollTimeout`。

`request`是发送给`/comments`的实际 GET 请求。它还提供了一个`after_id`参数，该参数等于页面上最新(第一个)评论的 id。

我们还需要调整`index.js.erb`视图:

*comments/index.js.erb*

```
$('#comments').find('.media-list').prepend('<%= j render @comments %>');
Poller.poll();
```

我们已经将`html`方法改为`prepend`，将`Poll()`函数改为`Poller.poll()`。

最后，我们还需要更改`create.js.erb`:

*comments/create.js.erb*

```
Poller.clear();
Poller.poll(0);
<% if @comment.new_record? %>
alert('Your comment cannot be saved.');
<% else %>
$('#comment_body').val('');
<% end %>
```

评论发布后，立即清除当前设置的计时器，并启动投票阶段。另外，请注意，我们没有在这里添加注释——我们依靠`index.js.erb.`来完成。

现在这是一个相当混乱，不是吗？

要检查这是否正常工作，请在两个不同的窗口中打开您的应用程序，在第一个窗口中发表评论，然后迅速(在投票阶段开始之前)在第二个窗口中发表另一条评论。您应该看到两个注释都以正确的顺序显示。

AJAX 轮询现在工作正常。然而，这似乎不是最佳解决方案。web sockets 会拯救世界吗？

## Web Sockets 和 Faye

在最后一次迭代中，我们将使用[Faye](http://faye.jcoglan.com/)——一个可用于 Ruby 和 Node.js 的发布/订阅消息系统。为了将其与 Rails 集成，James Coglan 的 [faye-rails](https://github.com/jamesotron/faye-rails) gem 将派上用场。

如果在之前的迭代中，我们*定期轮询*我们的应用程序以检查新的评论，那么在这里，我们*订阅*更新，并且只有当更新(评论)被*发布*时才采取任何行动。创建评论时，通知所有订阅者，以便更新过程立即发生。

切换回`master`分支，在*宝石文件*中添加两颗新宝石

*Gemfile*

```
[...]
gem 'faye-rails', '~> 2.0`
gem 'thin'
```

并安装它

```
$ bundle install
```

我们必须切换到瘦网络服务器，因为 Faye 不能和 WEBrick 一起工作。如果您要在 Heroku 上发布您的应用程序，请在您的项目根目录下创建一个新文件:

procfile

```
web: bundle exec rails server thin -p $PORT -e $RACK_ENV
```

现在将这些行添加到*应用程序中。*

*配置/应用. rb*

```
[...]
config.middleware.delete Rack::Lock
config.middleware.use FayeRails::Middleware, mount: '/faye', :timeout => 25
[...]
```

我们使用 FayeRails 作为中间件，并将其安装在`/faye`路径上。

现在，在 *application.js* 中需要两个新文件:

*application.js*

```
[...]
//= require faye
//= require comments
```

*faye.js* 文件由`faye-rails`提供。它包含了 Faye 正常工作所需的所有 JS 代码。

创建 *comments.coffee* 文件:

*评论.咖啡*

```
window.client = new Faye.Client('/faye')

jQuery ->
  client.subscribe '/comments', (payload) ->
    $('#comments').find('.media-list').prepend(payload.message) if payload.message
```

我们正在创建一个新的 Faye 客户端，并将其附加到`window`对象上。为该客户端订阅`/comments`频道。一旦收到更新，就将新消息添加到评论列表中。这比 AJAX 轮询容易得多，不是吗？

最后要做的是修改 *create.js.erb* 视图:

*comments/create.js.erb*

```
publisher = client.publish('/comments', {
  message: '<%= j render @comment %>'
});
```

在这里，`client`用于*发布*对`/comments`通道的更新，该通道提供已发布的消息。注意，我们不需要在这里使用`prepend`方法，因为在我们的 *comments.coffee* 文件中使用了它。

启动服务器，再次与自己聊天(我希望你不会感到沮丧)。注意，与 AJAX 轮询相比，消息是立即出现的。此外，代码更简单、更短。

我们可以做的最后一件事是在评论发送时禁用“发布”按钮:

*评论.咖啡*

```
[...]
jQuery ->
  client.subscribe '/comments', (payload) ->
    $('#comments').find('.media-list').prepend(payload.message) if payload.message

  $('#new_comment').submit -> $(this).find("input[type='submit']").val('Sending...').prop('disabled', true)
```

`prop`方法用于将`input`上的`disabled`属性设置为`true`。

在 Faye 回调的帮助下，我们可以将按钮恢复到原始状态，并在评论发布后立即清除 textarea。如果在通知订户时出现错误，也显示该错误:

*comments/create.js.erb*

```
publisher = client.publish('/comments', {
  message: '<%= j render @comment %>'
});

publisher.callback(function() {
  $('#comment_body').val('');
  $('#new_comment').find("input[type='submit']").val('Submit').prop('disabled', false)
});

publisher.errback(function() {
  alert('There was an error while posting your comment.');
});
```

多酷啊。有了 web sockets，我们的应用感觉就像真正的聊天。消息以最小的延迟发送，不需要刷新页面！当然，这个应用程序仍然可以扩展为管理员用户的后端，允许他们删除评论或阻止行为不端的聊天。

你可能还想看看 Faye-rails 演示应用程序——一个非常简单的聊天程序，没有任何数据库或认证。

### 安全性

我必须说几句关于安全的话。目前任何人都可以向`/comments`通道发送任何数据，这不是很安全。Faye 的网站有一个[安全部分](http://faye.jcoglan.com/security.html)，描述了如何限制订阅、发布、加强 CSRF 保护，以及其他一些概念。如果你打算在一个真正的应用程序中实现 Faye，我鼓励你阅读它。

对于本演示，我们将实施 CSRF 保护，这将要求我们修改服务器端和客户端代码。完成这项任务最简单的方法是使用所谓的 Faye 扩展:

*配置/应用. rb*

```
require File.expand_path('../boot', __FILE__)
require File.expand_path('../csrf_protection', __FILE__)

[...]

class Application < Rails::Application
  config.middleware.use FayeRails::Middleware, extensions: [CsrfProtection.new], mount: '/faye', :timeout => 25
[...]
```

*config/csrf _ protection . Rb*

```
class CsrfProtection
  def incoming(message, request, callback)
    session_token = request.session['_csrf_token']
    message_token = message['ext'] && message['ext'].delete('csrfToken')

    unless session_token == message_token
      message['error'] = '401::Access denied'
    end
    callback.call(message)
  end
end
```

我们正在检查用户会话中是否设置了`_csrf_token`,并将其与随消息一起发送的令牌进行比较。如果它们不相同，我们将引发“拒绝访问”错误。

在客户端代码上:

*评论.咖啡*

```
window.client = new Faye.Client('/faye')

client.addExtension {
  outgoing: (message, callback) ->
    message.ext = message.ext || {}
    message.ext.csrfToken = $('meta[name=csrf-token]').attr('content')
    callback(message)
}
```

使用`addExtension`方法，将`csrfToken`存储在消息的`ext`属性中。这样，所有*发布的*消息都将包含 CSRF 令牌。如果您删除此`client.addExtension`行，打开您的应用程序以及 javascript 控制台。浏览器尝试建立连接，但失败了，因为我们要求 CSRF 令牌存在。

## 结论

我们已经到了这篇由两部分组成的文章的结尾。所有计划的功能都实现了，我们原型化了 AJAX 轮询 vs web sockets，证实了我们已经知道的:web sockets 更适合真实的聊天体验。

您是否实施过类似的解决方案？你面临过什么问题？在评论中分享你的经验吧！

感谢阅读！

==========================
更新(08.12.2014):

一位读者已经注意到，当你导航到聊天，然后到另一个页面，并返回到聊天，所有发送的消息将被复制。发生这种情况是因为每次你访问一个页面，客户端会再次订阅“评论”频道，所以当你发布消息时，它会通过所有订阅进行广播。要解决这个问题，您可以在订阅前尝试取消订阅。将下面这段代码添加到您的 **comments.coffee** 文件中:

```
try
 client.unsubscribe '/comments'
catch
 console?.log "Can't unsubscribe." # print a message only if console is defined
end
```

就在

```
client.subscribe '/comments', (payload) ->
```

线。参见 GitHub 上的 [this diff](https://github.com/bodrovis/SitepointMiniChat/commit/ddef91462f2bcd939e8ff090e7049fee7610c445#diff-7d22339592134c18f8e72b2a9ef2ec6aR14) :

非常感谢你们所有的反馈！
===========================

## 分享这篇文章