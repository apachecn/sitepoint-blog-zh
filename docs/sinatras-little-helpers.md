# 辛纳屈的小助手

> 原文：<https://www.sitepoint.com/sinatras-little-helpers/>

当我在写关于 Sinatra 的小书时，我学到了 Sinatra 提供的一些辅助方法。其中一些我已经知道，但并没有真正使用很多，有些我甚至不知道。

Sinatra 有大量的帮助器方法，这些方法有助于为开发人员带来如此美好的体验。我在这里收集了一些例子。其中一些可能对你来说是新的，或者像我一样，你可能以前听说过它们，并感谢提醒。

## 启用和禁用

您可以在 Sinatra 中使用`set`方法轻松进行应用程序范围的设置(或者模块范围的设置，如果您使用的是模块化风格的应用程序):

```
set :title, "My Amazing Blog"
set :comments, true
set :tags ,false
```

但是如果设置是一个[布尔值](https://www.sitepoint.com/boolean-data-type/)，你可以使用`enable`和`disable`方法来代替。这不仅使用更少的代码，而且读起来更好。

```
enable :comments
disable :tags
```

您也可以将其用于内置设置:

```
enable :logging
enable :sessions
```

Sinatra 还为任何布尔设置创建了一个方便的助手方法，以帮助检查它们是否为真。基本上，它是设置的名称，后跟一个问号。这允许我们编写易于阅读和遵循的代码来检查某个设置是否为真:

```
if settings.comments? then display @page.comments
redirect to('/') unless settings.?
```

## 哪个环境

以下方法使检查正在使用的环境变得轻而易举:

```
production?
development?
test?
```

这意味着你可以写一些好看的条件语句，比如:

```
require 'sinatra/reloader' if development?
run! unless test?
```

## 暂停并通过

这两种方法可以在路由处理程序中用来控制 HTTP 请求周期。`halt`将停止请求，并显示可选的消息或视图和状态代码。如果一个过程花费的时间太长或者发生了严重错误，可以使用它。其他用途包括如果有人试图查看未经授权的网页。

```
get '/important-process' do
  halt 500 if something_bad_happened?
  "Everything is okay"
end

get '/secret-page' do
  halt 401,"This page is secret" unless logged_in?
  "For your eyes only"
end
```

`pass`方法可用于将该方法向下传递到下一个匹配的路径。在下面的例子中，我们有一个标准的路由，说 hello，但是如果在 URL 中输入的名称是“daz ”,那么我想将请求传递给更具体的路由处理程序，它将显示一个更好的消息:

```
get '/:name' do
  pass if params[:name] == 'daz'
  "Hello, #{params[:name]}"
end

get '/daz' do
  "Well hello there DAZ!"
end
```

当然，在上面的例子中，更改路由处理程序的顺序会更容易，但是在某些情况下，您可能无法控制这一点。

## Request.xhr？

如果您想知道请求是否是使用 Ajax 发出的，那么`Request.xhr?` helper 方法就派上了用场。如果使用了 XML HTTP 请求对象，将返回 true。如果没有使用 Ajax，我经常用它来做重定向，就像下面这个用来删除页面的例子。如果使用 Ajax，那么页面将被删除，我们希望用户停留在“/pages”页面，但如果不使用 Ajax，那么我们将不得不手动将他们重定向回“/pages”页面:

```
delete '/page/:id' do
  page = Page.get params[:id]
  page.destroy
  redirect to('/pages') unless request.xhr?
end
```

## 重定向回来

如果你有一个路由处理器，它基本上执行一个操作，然后把用户送回他们来的页面，那么`back` helper 方法就是你所需要的，让你的代码读起来很好。与 redirect helper 方法一起使用，这将简单地把用户送回他们来的页面。例如，我们看到的删除页面路由处理程序:

```
delete '/page/:id' do
  page = Page.get params[:id]
  page.destroy
  redirect back unless request.xhr?
end
```

## 泼溅

在 Sinatra 中，您可以通过在路由定义中的命名参数前面放置一个冒号来创建命名参数，如下所示:

```
get '/hello/:name' do

end
```

这将存储在`params[:name]`中的:name 位置输入的任何内容。我们可以比这更笼统，在路线中使用*。这允许输入任意数量的文本。可以通过 params[:splat]访问它，例如:

```
get '/goodbye/*/hello/*' do

end
```

URL '/goodbye/shoes/hello/flip flops '将给出 params[:splat] = ["shoes "，" flipflops"]

如果您不喜欢使用单词 splat，也可以将它与块参数一起使用。这也使您的代码更具可读性和自解释性:

```
get '/*.*' do |path,ext|

end
```

在上面的例子中，route '/application.js '会产生变量`path = 'application'`和`ext = 'js'`，然后可以在 route handler 块中使用它们。

您还可以使用 splat 来抓取长度不确定的 URL:

```
get '/blog/ *' do |path|
  @page = Page.first(:slug =&gt; path.first)
end
```

在上面的例子中，可以使用`path.first`访问在“/blog”后输入的路线。例如，像“/blog/all/about/sinatra”这样的路由将导致`path.first = '/all/about/sinatra'`。这在处理漂亮的 URL 时很有用。

## 缓存控制

你可以通过直接设置头来控制页面如何缓存，但是还有`cache_control`、`expires`、`last_modified`和`etag`这些助手让缓存控制变得易如反掌。

例如，如果您在一个简单的 CMS 系统中有一个页面模型，您可以使用以下代码:

```
get '/page/:id'
  @page = Page.get(params[:id])
  last_modified @page.updated_at
  etag @page.content
  markdown @page.content
end
```

对于所有页面的快速和脏缓存控制，您可以使用应用程序启动时间来全局设置`last_modified`和`etag`头。虽然应用程序启动时间可能不是所有文件最后一次修改的实际时间，但我们通常可以假设，如果应用程序已经重新启动，那么一定发生了一些变化。在这种情况下，清除缓存并重新加载页面是值得的。要设置应用程序启动时间，请将以下代码放在配置块中:

```
configure do
  set :start_time, Time.now
end
```

这是因为配置块中的代码在启动时只运行一次。现在我们可以使用这个设置来设置`last_modified`和`etag`头。设置是一个`Time`对象，所以它可以用于`last_modified`头，但是它需要被转换成一个字符串才能用于`etag`头。如果将以下代码放在 before 块中，则它将应用于所有页面:

```
before do
  last_modified settings.start_time
  etag settings.start_time.to_s
  cache_control
end
```

现在，如果你刷新一个没有被修改的页面，你应该得到一个 304 状态码，而不是 200。这是减少应用程序中服务器负载的一种有效方法，它避免了任何不必要的服务器往返。

## 这是所有的乡亲

所有这些方法的作用是使代码更容易阅读和维护。如果你看看上面的一些代码样本，它们读起来就像一个完整的英语句子，并且非常明显地说明了正在做什么。这些方法还可以让您执行巧妙的技巧并使用更少的代码。这就是使用 Sinatra 的美妙之处——代码短小精悍。本书中更详细地介绍了这些助手方法中的大多数。你用过这些并发现它们有用吗，我错过了吗？请在下面的评论中留下你的答案。

## 分享这篇文章