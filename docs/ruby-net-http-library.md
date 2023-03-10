# 看 Ruby 的 Net::HTTP 库

> 原文：<https://www.sitepoint.com/ruby-net-http-library/>

![Screenshot 2014-04-26 08.43.13](img/047281d01e3e9eebba376fab64b8a385.png)

我们时常发现自己需要访问 REST APIs。有一些很棒的[库](https://github.com/rest-client/rest-client)就是为了这个，但是这些库后面的库呢？在本文中，我们将看看 Ruby 的`Net::HTTP`库。我们将介绍简单的 REST API 原则，如:

*   `GET`
*   `POST`
*   `PUT`
*   `DELETE`

我还将讨论更高级的主题，如:

*   HTTP 阻止
*   301 重定向
*   头球
*   基本认证
*   文件下载

## 什么是休息，你为什么要关心？

REST 代表 **RE** 表象 **S** 状态 **T** 转移。Roy Fielding 在他的博士论文中将其理论化:[架构风格和基于网络的软件架构的设计](http://jpkc.fudan.edu.cn/picture/article/216/35/4b/22598d594e3d93239700ce79bce1/7ed3ec2a-03c2-49cb-8bf8-5a90ea42f523.pdf)，然后由万维网联盟(W3C)为 HTTP 1.1 实现。它基本上是一种软件(主要是 web)架构风格，允许轻松的 API 访问，以及一些其他有用的目标。

REST APIs 关注四个主要动词:`GET`、`POST`、`PUT`和`DELETE`。我们可以使用 Ruby 的`Net::HTTP`库来访问 REST API。在本文中，我们将学习如何做到这一点，以及其他一些好东西。如果您想要所有 HTTP 1.1 方法的完整定义，那么您应该访问这个 W3C 页面。

*兴奋？*我们开始吧。

## 简单:测试

让我们使用 [Sinatra](http://www.sinatrarb.com/) 作为我们的小 API 服务器。如果你不熟悉辛纳屈，你可以在 [10 分钟](http://www.sinatrarb.com/intro.html)中学习基本内容。这是我们的服务器:

```
# file: rest-server.rb
require 'sinatra'

# all status codes given are based on W3C standards

get '/' do
  'Here is some data.'
end

post '/' do
  puts "Data: '#{params[:foo]}' recieved, creating object."
  status 201
end

put '/' do
  puts "Data: '#{params[:foo]}' recieved, updating object."
end

delete '/' do
  puts 'Deleting data.'
end
```

现在要启动它，只需打开一个新的终端标签:

```
$ ruby rest-server.rb
== Sinatra/1.4.4 has taken the stage on 4567 for development with backup from Thin
Thin web server (v1.6.1 codename Death Proof) # might be WEBrick instead
Maximum connections set to 1024
Listening on localhost:4567, CTRL+C to stop
```

现在，您的计算机上运行了一个功能性 REST API。

## 简单:获取请求

一个`GET`请求*得到*，或者从服务器读取信息*。这个要求很常见。当您访问这个页面时，您的浏览器发出一个`GET`请求，从服务器返回一个超文本文档。你可以在 Ruby 中做同样的事情。在我们的例子中，我们将*从我们的服务器获取*数据。*

```
require 'net/http'
require 'uri'

url = 'http://localhost:4567/' # trailing slash is important
uri = URI.parse(url)

Net::HTTP.get(uri) # GET request
# => "Here is some data."
```

这个简单的例子从我们的服务器获取数据并返回它。很优雅，对吧？这是丑陋的一行程序:

```
require 'net/http' # URI is required by Net::HTTP by default

Net::HTTP.get(URI 'http://localhost:4567/')
# => "Here is some data."
```

显然，作为一个智能的 SitePoint Ruby 阅读器，这段代码存在一些问题。不要担心，我们将在稍后的高级`Net::HTTP`部分讨论其中的大部分。

## 简单:发布请求

一个`POST`请求*发布*，或者发送数据/参数*到服务器*。API 使用它来接收来自用户的数据。这个要求也是相当普遍的。每次你发送一条推文，你都是用你的推文作为参数向 Twitter 发送一个`POST`请求。在这个例子中，我们将向服务器发送一个`POST`请求。

```
require 'net/http'
require 'uri'

url = 'http://localhost:4567/'
uri = URI.parse(url)

params = {foo: "bar"}

Net::HTTP.post_form(uri, params)
# => #<Net::HTTPCreated 201 Created readbody=true>
```

当然，下面是它丑陋的一行代码:

```
require 'net/http'

# ugly, right?
Net::HTTP.post_form(URI('http://localhost:4567/'), {foo:"bar"})
# => #<Net::HTTPCreated 201 Created readbody=true>
```

## 简单:PUT 请求

一个`PUT`请求*放置*，或者更新服务器上的东西。API 主要在更新服务器上的数据时使用它。例如，如果您在 GitHub 上编辑一个 pull 请求，您将发送一个包含您的数据的`PUT`请求。不幸的是，`PUT`在 Ruby `Net::HTTP`库中没有得到很好的记录，所以没有很好的解决方案。

```
require 'net/http'
require 'uri'

url = 'http://localhost:4567/'
uri = URI.parse(url)

params = {foo: "change"}

http = Net::HTTP.new(uri.host, uri.port)
request = Net::HTTP::Put.new(uri.path)
request.set_form_data(params)
http.request(request) # the actual PUT request
# => #<Net::HTTPOK 200 OK readbody=true>
```

我不打算把它显示为一行，因为它会超过 80 个字符，不仅不可读，而且违背了 [Ruby 风格指南](https://github.com/bbatsov/ruby-style-guide)。

## 简单:删除请求

一个`DELETE`请求*从服务器上删除*一些东西。如果你要删除一条推文，你将向 Twitter 发送一个删除请求。这个例子和上一个非常相似。

```
require 'net/http'
require 'uri'

http = Net::HTTP.new(uri.host, uri.port)
request = Net::HTTP::Delete.new(uri.path)
http.request(request)
# => #<Net::HTTPOK 200 OK readbody=true>
```

太好了。我们已经使用 Ruby 学习了基础和 REST 动词。请记住，大多数 HTTP 请求有多种处理方式。我们将在下一节讨论其中的一些。

## 高级:测试

对于高级部分，我们的服务器必须更加复杂，以便返回和接收数据，就像我们在每个示例中试图模仿的 API 一样。因此，在每个示例中，都需要更改服务器。

从现在开始，我们将关注于`GET`和`POST` HTTP 方法。如果您需要将下面的技术应用到其他方法中，只需将请求的简单版本与高级技术混合即可。

## 高级:HTTP 阻止

在[干](http://en.wikipedia.org/wiki/Don't_repeat_yourself)的原理中，HTTP 块很重要。HTTP 块就是简单的`Net::HTTP`块。这似乎有点令人困惑，但这是一个非常简单的概念。

首先，我们创建一个新方法。我们称它为`http_request`，给它一个参数`uri`。

```
require 'net/http'

def http_request(uri)

end
```

现在是有趣的部分。调用`Net::HTTP.new`并将其让给一个块。

```
require 'net/http'

def http_request(uri)
  Net::HTTP.start(uri.host, uri.port) do |http|
    yield(http, uri)
  end
end
```

这意味着我们可以像这样做非常酷的事情:(使用我们制作的简单服务器)

```
# ... http block code above ...

uri = URI 'http://localhost:4567/'

http_request(uri) do |http, uri|
  http.get(uri.path).body
end
# => "Here is some data."
```

我们将在接下来的例子中使用这些，所以请熟悉一下。只要记住`http_request`和`Net::HTTP.start`是一回事，只是一个捷径。

## 高级:301 重定向

哦不！我们的服务器刚刚上了一个台阶，现在有能力重定向请求。

```
require 'sinatra'

get '/foo/?' do
  redirect '/bar'
end

get '/bar/?' do
  'Hello!'
end
```

如果我们尝试前面简单的`GET`方法，让我们看看现在会发生什么…

```
require 'net/http'

uri = URI 'http://localhost:4567/foo'
Net::HTTP.get(uri)
# => ""
```

嗯……那什么也没给我们。但是，作为聪明人，我们尝试第二种简单的方法。

```
require 'net/http'

def http_request(uri)
  Net::HTTP.start(uri.host, uri.port) do |http|
    yield(http, uri)
  end
end

uri = URI 'http://localhost:4567/foo'
http_request uri do |http, uri|
  http.get(uri.path)
end
# => #<Net::HTTPFound 302 Moved Temporarily readbody=true>
```

这给了我们更多的信息，但仍然没有。我们能做些什么来跟随这些重定向而不迷路呢？有两种方法。

方法一使用`Net::HTTP.get_response`从服务器获取信息，而不仅仅是主体。

```
require 'net/http'

uri = URI 'http://localhost:4567/foo'

def get_follow_redirects(uri, request_max = 5)
  raise "Max number of redirects reached" if request_max <= 0

  response = Net::HTTP.get_response(uri)
  case response.code.to_i
  when 200
    response.body
  when 301..303
    get_follow_redirects(URI(response['location']), request_max - 1)
  else
    response.code
  end
end

get_follow_redirects(uri)
# => "Hello!"
```

方法二涉及以类似的方法使用 HTTP 块。

```
require 'net/http'

uri = URI 'http://localhost:4567/foo'

def http_request(uri)
  Net::HTTP.start(uri.host, uri.port) do |http|
    yield(http, uri)
  end
end

def get_follow_redirects(uri, request_max = 5)
  http_request uri do |http, uri|
    response = http.head(uri.path)
    case response.code.to_i
    when 200
      http.get(uri.path).body
    when 301..303
      get_follow_redirects(URI(response['location']), request_max - 1)
    else
      response.code
    end
  end
end

get_follow_redirects(uri)
# => "Hello!"
```

正如你所看到的，这个方法和另一个非常相似。这完全取决于您的偏好以及您希望在多大程度上控制请求。

## 高级:标题

在前面的例子中，我们使用报头来查找 301/302 重定向位置。在下一个例子中，我们将隔离这种能力，这样我们就可以访问头的任何部分。

首先，让我们发出一个请求，看看我们得到什么样的头。在这个例子中，我们将首先使用简单的服务器。

```
require 'net/http'

uri = URI 'http://localhost:4567/'

response = Net::HTTP.get_response(uri)
response.header.to_hash.inspect
# => "{\"content-type\"=>[\"text/html;charset=utf-8\"], \"content-length\"=>[\"18\"], \"x-xss-protection\"=>[\"1; mode=block\"], \"x-content-type-options\"=>[\"nosniff\"], \"x-frame-options\"=>[\"SAMEORIGIN\"], \"connection\"=>[\"keep-alive\"], \"server\"=>[\"thin 1.6.1 codename Death Proof\"]}"
```

很可爱，是吧？如果你不需要所有的标题，你可以使用`response['something']`作为快捷方式。

当然，还有一个 HTTP block 方法版本的这个…

```
require 'net/http'

# ... http block code ...

uri = URI 'http://localhost:4567/'

http_request(uri) do |http, uri|
  response = http.head(uri.path)
  response.to_hash.inspect
end
# => "{\"content-type\"=>[\"text/html;charset=utf-8\"], \"content-length\"=>[\"18\"], \"x-xss-protection\"=>[\"1; mode=block\"], \"x-content-type-options\"=>[\"nosniff\"], \"x-frame-options\"=>[\"SAMEORIGIN\"], \"connection\"=>[\"keep-alive\"], \"server\"=>[\"thin 1.6.1 codename Death Proof\"]}"
```

HTTP 头非常有用。您可以获得关于数据源的各种信息，并利用这些信息为您服务。

## 高级:基本身份验证

这是一种不太常见的情况，但和上一种情况一样有用。有时 API 提供基本的 http 认证。让我们在我们的服务器中模拟一下:

```
require 'sinatra'

# source: http://recipes.sinatrarb.com/p/middleware/rack_auth_basic_and_digest#label-HTTP+Basic+Authentication
use Rack::Auth::Basic, "Protected Area" do |username, password|
  username == 'foo' && password == 'bar'
end

get '/' do
  'Classified data.'
end
```

现在我们得想办法获取机密数据。我们开始吧。

```
require 'net/http'

def http_request(uri)
  Net::HTTP.start(uri.host, uri.port) do |http|
    yield(http, uri)
  end
end

uri = URI 'http://localhost:4567/'

req = Net::HTTP::Get.new(uri)
req.basic_auth 'foo', 'bar'

http_request uri do |http, uri|
  response = http.request(req)
  response.body
end

# => "Classified data."
```

当然，这种类型的身份验证在 OAuth 存在的情况下并不常见，但是知道这一点总是有好处的。

## 高级:文件下载

有时候作为开发人员，我们需要一种编程方式来下载文件。这就像发出一个`GET`请求并把每一行放到一个新文件中一样简单。对于本例，我们将使用来自互联网的真实文件。没有必要为这种情况重写我们的服务器。

对于我们的第一个例子，我们将修改我们之前提出的简单的`GET`请求。

```
require 'net/http'

content = Net::HTTP.get(URI 'http://www.google.com/humans.txt') # get the contents of the file

File.open('humans.txt', 'w+') do |file|
  file.write(content) # write the contents
end
```

简单吧？但我们是精明的程序员。直播呢？让我们找点乐子。

```
require 'net/http'

uri = URI 'http://www.google.com/humans.txt'

def http_request(uri)
  Net::HTTP.start(uri.host, uri.port) do |http|
    yield(http, uri)
  end
end

http_request uri do |http, uri|
  request = Net::HTTP::Get.new(uri)

  http.request(request) do |response|
    open('humans.txt', 'w+') do |file|
      response.read_body do |chunk|
        file.write(chunk)
      end
    end
  end
end
```

对于小文件，第二种方法只是多了几行代码，但是对于大文件，这可以大大加快下载速度。

## 高级:SSL

SSL 在 API 和其他 web 服务中非常常见。别担心，我们会掩护你的。

对于下一个例子，我们将使用 Google，因为它使用 SSL 进行连接。我们不会使用 Sinatra，因为配置它超出了本教程的范围。

```
require 'net/https' # notice the change to https

uri = URI 'https://www.google.com/'

http = Net::HTTP.new(uri.host, 443) # hardcoded for forced SSL
http.use_ssl = true
http.verify_mode = OpenSSL::SSL::VERIFY_PEER

req = Net::HTTP::Get.new(uri.path) # or any other request you might like
resp = http.request(req)
resp.body
# => ... very long response body (Google's homepage) ...
```

## 结论

你可以用`Net::HTTP`库做很多事情。它是可扩展的，但也很容易使用。我建议你也去看看[http party](https://github.com/jnunemaker/httparty)、 [Rest-Client](https://github.com/rest-client/rest-client) 和 [Curb](https://github.com/taf2/curb) 宝石。它们提供了一种非常简单的方式来以更好的方式访问这些相同的方法。无论你选择什么，记住你的浏览器能做的事情 Ruby 也能做。

## 进一步阅读

*   [架构风格和基于网络的软件架构设计](http://jpkc.fudan.edu.cn/picture/article/216/35/4b/22598d594e3d93239700ce79bce1/7ed3ec2a-03c2-49cb-8bf8-5a90ea42f523.pdf)
*   [Ruby Net::HTTP 文档](http://ruby-doc.org/stdlib-2.1.1/libdoc/net/http/rdoc/Net/HTTP.html)
*   [HTTP 代码定义](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)
*   [HTTP 方法定义](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)
*   [Ruby IO::文件模式定义](http://ruby-doc.org/core-2.1.1/IO.html#method-c-new-label-IO+Open+Mode)

## 分享这篇文章