# 用 Ruby 的 Goliath 理解并发编程

> 原文：<https://www.sitepoint.com/understanding-concurrent-programming-with-ruby-goliath/>

PostRank 最近发布了一个新的 Ruby web 服务器: [Goliath](https://github.com/postrank-labs/goliath) 。它以与 node.js 和 nginx 相同的方式使用事件循环来实现高水平的并发，但添加了一些特殊的调料，允许以同步方式编写传统复杂的异步代码。

例如，Ruby 中的异步代码通常如下所示(使用 eventmachine 库):

```
require 'eventmachine'
require 'em-http'

EM.run {
  EM::HttpRequest.new('https://www.sitepoint.com/').get.callback {|http|
    puts http.response
  }
}
```

这很好，因为它允许应用程序在 HTTP 请求完成时做其他事情(它是“非阻塞的”)，但是要连续获取两个站点，您需要嵌套回调:

```
EM::HttpRequest.new('https://www.sitepoint.com/').get.callback {|http|
  # extract_next_url is a fake method, you get the idea
  url = extract_next_url(http.response)

  EM::HttpRequest.new(url).get.callback {|http2|
    puts http2.response
  }
}
```

可以想象，这种模式很快就会变得混乱。Goliath 允许我们以我们熟悉的简单同步方式编写上述代码:

```
http = EM::HttpRequest.new("http://www.sitepoint.com").get
# extract_next_url is a fake method, you get the idea
url = extract_next_url(http.response)
http2 = EM::HttpRequest.new(url).get
```

…然而在幕后，它仍然异步执行！在 HTTP 请求运行时，可以运行其他代码。

这让我震惊。它是如何工作的？让我们找出答案。

## 纤维

从[文档](https://github.com/postrank-labs/goliath)来看，Goliath 声称通过“利用 Ruby 1.9+中引入的 Ruby 纤程”来发挥其魔力。这第一个提示让我们去[红宝石博物馆](http://www.ruby-doc.org/core-1.9/classes/Fiber.html)寻找:

> 纤程是在 Ruby 中实现轻量级协作并发的原语。基本上，它们是一种创建可以暂停和恢复的代码块的方法，就像线程一样。主要的区别是它们从来不会被抢占，调度必须由程序员而不是 VM 来完成。

呃，太多大词了。让我们开始钻研 Goliath 代码。Goliath 文档包含代理站点的完整示例:

```
require 'goliath'

require 'em-synchrony'
require 'em-synchrony/em-http'

class HelloWorld < Goliath::API
  def response(env)
    req = EM::HttpRequest.new("http://www.google.com/").get
    resp = req.response

    [200, {}, resp]
  end
end

# to play along at home:
#   $ gem install goliath
#   $ gem install em-http-request --pre
#   $ ruby hello_world.rb -sv
```

我们知道，对于以异步方式发生的这种情况，在那个`#get`调用中一定有一些有趣的事情在进行，所以让我们试着找到它。我的蜘蛛感觉告诉我它会在`em-synchrony/em-http`的某个地方…

```
$ gem unpack em-synchrony
Unpacked gem: '/Users/xavier/Code/tmp/em-synchrony-0.3.0.beta.1'
$ cd em-synchrony-0.3.0.beta.1
# I used tab completion on the next line to find the exact path
$ cat lib/em-synchrony/em-http.rb
```

这揭示了:

```
# em-synchrony/lib/em-synchrony/em-http.rb
begin
  require "em-http"
rescue LoadError =< error
  raise "Missing EM-Synchrony dependency: gem install em-http-request"
end

module EventMachine
  module HTTPMethods
     %w[get head post delete put].each do |type|
       class_eval %[
         alias :a#{type} :#{type}
         def #{type}(options = {}, &amp;blk)
           f = Fiber.current

            conn = setup_request(:#{type}, options, &amp;blk)
            conn.callback { f.resume(conn) }
            conn.errback  { f.resume(conn) }

            Fiber.yield
         end
      ]
    end
  end
end
```

头奖！纤维！这似乎是对现有的`em-http`库的猴子式修补，所以在我们进一步深入之前，让我们看看没有纤程的*的正常`em-http`代码是什么样子的。在 [em-http-request wiki](https://github.com/igrigorik/em-http-request/wiki/Issuing-Requests) 上有一个简单的例子:*

```
EventMachine.run {
  http = EventMachine::HttpRequest.new('http://google.com/').get :query =< {'keyname' =< 'value'}

  http.errback { p 'Uh oh'; EM.stop }
  http.callback {
    p http.response_header.status
    p http.response_header
    p http.response

    EventMachine.stop
  }
}
```

它看起来与上面的代码非常相似，当我们深入研究时，它变得更加相似。

```
$ gem unpack em-http
ERROR:  While executing gem ... (Gem::RemoteFetcher::FetchError)
    SocketError: getaddrinfo: nodename nor servname provided, or not known (http://rubygems.org/latest_specs.4.8.gz)
# Oh noes it doesn't work!
# Search for em gems
$ gem list em- 

*** LOCAL GEMS ***

em-http-request (1.0.0.beta.2, 0.3.0)
em-socksify (0.1.0)
em-synchrony (0.3.0.beta.1)

$ gem unpack em-http-request # Ah that is probably it
$ cd em-http-request-1.0.0.beta.2
$ ack "get" lib/
lib/em-http/http_connection.rb
4:    def get    options = {}, &amp;blk;  setup_request(:get,   options, &amp;blk); end
```

注意最后一行的`get`直接服从`setup_request`，这与上面纤程示例中的调用相同。是的，差不多一样。现在我们可以回到光纤代码。

```
f = Fiber.current
conn = setup_request(:#{type}, options, &amp;blk)
conn.callback { f.resume(conn) }
conn.errback  { f.resume(conn) }
Fiber.yield
```

似乎正在发生的是，当回调触发时，不是立即做任何工作，而是在当前纤程上调用`resume`，大概是在调用`yield`的点开始备份这个线程。查看`Fiber.yield`的文档证实了这一点，并在最后一句解释了`conn`变量是如何从这个方法返回的:

> 将控制权交还给恢复纤程的上下文，并传递传递给它的任何参数。下一次调用 resume 时，纤程将在此时继续处理。传递给下一个简历的任何参数都将是该 Fiber.yield 表达式计算的值。

## 使用它

我们现在对歌利亚是如何施展魔法有了一个概念，尽管可能还很模糊。让我们通过尝试编写一些模拟它的代码来看看我们是否正确。

请记住，纤程技巧只是简化回调代码的一种方式，因此我们应该能够先编写一个非纤程感知的方法，然后再清理它。我喜欢从一个非常简单的例子开始，所以我们要写一个基本的 Goliath 类，它阻塞一秒钟，然后呈现一些文本。

```
class Surprise < Goliath::API
  def response(env)
    sleep 1
    [200, {}, "Surprise!"]
  end
end
```

在你的网络浏览器中点击它，它会等待一秒钟。虽然 tiger 没有这么快，但是当我们同时发出多个请求时会发生什么呢:

```
$ ab -n 3 -c 3 127.0.0.1:9000/ | grep "Time taken"
Time taken for tests:   3.011 seconds
```

唉，我们的网络服务器一次只能服务一个请求。那不是网络规模。这个`sleep`调用不仅阻塞了我们的响应，而且*了整个服务器*。这就是我们首先转向事件编程的原因。让我们试试经典的 EventMachine 计时器:

```
class Surprise < Goliath::API
  def response(env)
    EventMachine.add_timer 1, proc {
      [200, {}, "Surprise!"]
    }
  end
end
```

当然这是行不通的，因为`#response`方法需要同步。在这种情况下发生的是,`#add_timer`返回`nil`, Goliath 立即试图呈现它，并在这个过程中爆炸。计时器在稍后的某个时间触发，没有代码还在关注。我们不能将计时器过程的结果作为方法的返回值发送。

我们需要将第一个例子的同步特性与第二个例子的异步元素结合起来；一个美丽的科学怪人。希望你已经明白我们可以用纤维来缝合。

```
class Surprise < Goliath::API
  def response(env)
    f = Fiber.current
    EventMachine.add_timer 1, proc { f.resume }
    Fiber.yield

    [200, {}, "Surprise!"]
  end
end
```

我们窃取我们在上面的`em-synchronicity/em-http`中看到的模式，获取当前纤程并在异步回调中设置一个`resume`调用，该调用在`Fiber.yield`处恢复执行。用 ab 进行测试，我们看到这确实解决了我们的并发问题:

```
$ ab -n 3 -c 3 127.0.0.1:9000/ | grep "Time taken"
Time taken for tests:   1.009 seconds
```

这些纤维很酷。

## 包扎

在探索 Goliath 源代码和相关库的过程中，我们发现了它是如何将异步伪装成同步的，并且能够通过一个简单的例子将这些知识付诸实践。

为了练习您的代码阅读，这里有一些其他的研究任务供您尝试:

*   找到 Goliath 调用`#response`方法的地方，看看是否还有其他潜在的纤程技巧。
*   研究一下`em-synchrony`为其提供 API 的其他库，比如`em-mongo`。
*   [Rack-fiber_pool](https://github.com/mperham/rack-fiber_pool) 在类似的环境中使用纤程，看看它在做什么。

请在评论中告诉我们你的进展。下周请继续关注代码丛林中更激动人心的冒险。

## 分享这篇文章