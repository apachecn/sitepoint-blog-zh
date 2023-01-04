# 中间件架

> 原文：<https://www.sitepoint.com/rack-for-middlewares/>

在之前的文章中，我们把 Rack 教程伪装成 PHP 和 Ruby 的比较。这篇文章的目的是指出大多数 PHP 开发人员都是从一条经过深思熟虑的学习道路开始的。我们走向“hello world ”, breeze 由一些基本的 HTML 和 PHP 交织而成，上升到分离视图和业务逻辑，然后最终使用一个合适的社区驱动框架。然而，当谈到学习 Ruby 时，我们忘记了这一切，抓起 Rails 开始学习。

它说明了很多关于 Rails(和/或 Ruby)的东西，以至于许多开发人员在开发过程中学会了这门语言。许多优秀的开发人员花了数小时在 Rails 中抽象出真正繁重的工作，尽管这很神奇，但有时我们需要先动手，然后才能巩固我们的理解。

我指出，Ruby 本身在为 web 开发时可能会很痛苦。结果是绕过了我们对 PHP 的良好学习曲线。不像 PHP，我们不能仅仅用 ERB 把 HTML 放在 web 服务器上，然后期望它能工作。然而，我们可以使用 Rack 并获得 PHP 脚本的一些即时性，同时抽象出足够的 HTTP 协议来保持我们的理智。

## 不止是杂种狗和独角兽

上一篇文章最后提到了神秘的短语“中间件”。那么，什么是中间件呢？关于[栈溢出](http://stackoverflow.com/questions/2256569/what-is-rack-middleware)有一个很好的解释，但是在这个上下文中我们将使用 50%正确的解释。它是位于服务器和应用程序之间的一段代码，允许我们过滤某些响应和请求。

为了给你更多的理解，Rails，Sinatra Merb 等等。都是在机架上构建的。机架，除了给我们的服务器(瘦，杂种，独角兽等)一个通用接口。)，用于创建形成最终应用程序的类似堆栈的组件结构。我们可以将微型机架应用程序注入该堆栈，以执行所需的任务。

## 明白要点了吗？

我们将基于这一知识来创建我们的第一个机架中间件。中间件的目的是从我们的应用程序中捕获包含[GitHub Gist](https://gist.github.com/)URL 的响应，并自动将 URL 的纯文本转换成一个非常格式化的嵌入 Gist；在这篇 [GitHub 博客文章](https://github.com/blog/122-embedded-gists)中有生动的描述。

首先，我们将创建一个框架应用程序。毫无疑问，您会记得，我们需要一个接受环境散列的`call`方法，并且捕获任何正在使用我们的中间件的应用程序似乎是明智的。

```
module Rack
  class Gist

    def initialize(app)
      @app = app
    end

    def call(env)
      status, headers, response = @app.call(env)
      [status, headers, response]
    end

  end
end
```

正如你所看到的，我用一个框架模块命名了 Gist 应用程序，这让我们可以通过`Rack::Gist`来调用它，当然最终确定了这个应用程序的名称。所以现在把父目录从`super_awesome_ninja_rack_middleware`重命名为`rack_gist`！

像所有的 Rack 应用程序一样，我们的应用程序响应一个`call`方法。目前，这个方法只是捕获状态、标题和正文。当我们在这个应用中处理一个响应时，为了清晰起见，`body`被恰当地命名为`response`。

我们只想解析 HTML 响应，所以让我们通过检查标题来确保这一点。

```
module Rack
  class Gist

    def initialize(app)
      @app = app
    end

    def call(env)
      status, @headers, response = @app.call(env)
      if html?
        #do something  
      end
      [status, @headers, response]
    end

    private 

    def html?
      @headers["Content-Type"].include? "text/html"
    end

  end
end
```

现在，我们要检查响应中是否有任何 gists。因为这是一个非常简单的任务，我们将使用正则表达式。

```
module Rack
  class Gist

    def initialize(app)
      @app = app
    end

    def call(env)
      status, @headers, response = @app.call(env)
      if html?
        parsed_response = ""
        response.each do |r|
          parsed_response = r.gsub(/(https://|)gist.github.com/(d+)/) do |gist|
            gist = "https://" + gist unless gist.start_with? "https://"
            "<script src="#{gist}.js"></script>"
          end
        end
        response = [parsed_response]
      end
      [status, @headers, response]
    end

    private

    def html?
      @headers["Content-Type"].include? "text/html"
    end

  end
end
```

我们只是寻找`gist.github.com` url，根据需要添加`https://`协议，然后在重新分配响应之前将 gist url 包装在适当的脚本标记中。

完成了。不完全是。

## 哪里出了问题？

所以我们已经走了很远，不需要知道什么应用程序将使用这个中间件，HTTP 协议，当然，我们还没有写一个测试。

我不是在谈论单元测试。如果我们对 HTTP 或 Rack 规范知之甚少，那么单独的单元测试可能无法捕获我所想到的错误。

让我们考虑一下我们的中间件的行为:它捕获响应，可能修改它，并在正常流中将其传递回来。查看[框架规范](http://rack.rubyforge.org/doc/files/SPEC.html)，我们看到标题应该有一个内容长度。这是为了满足 HTTP 规范，使报头包含正确的内容长度。显然，如果中间件修改了响应，内容长度现在将是不正确的。

Rack 附带了`Rack::Lint`模块，允许我们在开发时验证我们的应用程序是否符合相关合同。果不其然，如果我们向中间件添加测试，我们会收到以下错误。

```
Rack::Lint::LintError:
Content-Length header was 81, but should be 108
```

这是一个廉价的解决方案。Rack 很擅长提供我们需要的少量但足够的东西。这次是`Rack::Utils`。我们可以包含`Utils`模块，然后调用`bytesize`并将头部设置为新的长度。

```
include Rack::Utils
#
# ...
#
@headers['Content-Length'] &&= bytesize(parsed_response).to_s
```

方法`bytesize`没什么特别的。检查源代码，您会发现它只是返回传递给它的字符串的大小，字面意思是`string.size`。但是我喜欢把所有的东西都放在架子上，而且`bytesize`是我们领域特有的，所以我们不要只使用`response.size`。

蝙蝠里又一个有用的工具(“圣架，蝙蝠侠！”)皮带是[试管架](https://github.com/brynary/rack-test)宝石。这个小巧的 gem 为我们的测试设置请求头、跟踪重定向和管理会话提供了帮助。我发现这有助于我更专注于测试而不是设置。

## 在野外

我们已经准备好推出我们的中间件。我们到底要怎么做？对于 Rails，从 v3 开始，Rack 中间件已经成为核心的一部分，使得注入中间件变得非常简单。如果我们想将它应用到单个控制器的响应中，那么弹出我们的`vendor`或`lib`目录中的源代码。

```
class GistController < ApplicationController

  use Rack::Gist

  def index
    # views with embedded gists
  end

end
```

你应该熟悉这里的`use`方法，那是一个普通的老方法。

当然，如果我们的中间件适用于整个应用程序，我们可以在 config 部分的`config/application.rb`中添加。

```
module GistTest
  class Application < Rails::Application
    # other config
    config.middleware.use Rack::Gist
  end
end
```

在每次请求时被注入到我们的中间件堆栈中。瞧。

我喜欢做的另一件事是把我的中间件包装成宝石(它们应该是可重用的)。来源，加上测试可以在[上找到通常的嫌疑人](https://github.com/bangline/rack_gist)。

## 接下来去哪里？

到目前为止，我们已经讨论了 Rack 以及它是如何构成我们所了解和喜爱的主要 Ruby 框架的基础的。它不仅仅是我们应用程序的服务器接口。我们可以利用它的能力，以一种比任何助手都更简单、更便携的方式添加低级功能。

我想展示一个中间件，它实际上给了我们一些东西，而不是请求统计。我们可以更进一步，使用像 [nokogiri](http://nokogiri.org/) 或 [hpricot](http://rubygems.org/gems/hpricot) 这样的宝石来处理要点在锚标签中的情况。我想甚至有用 JavaScript 来完成所有这些的情况。

不管怎样，我们完成的是最有用的机架中间件的面包和黄油。你的下一个 Rack 应用程序的一个很好的、鼓舞人心的资源是 [CodeRack](http://coderack.org/) 。在那里挑选一些项目并检查源代码。

希望在将来，在您开始为某些响应增加应用程序的复杂性之前( [ReCaptcha](http://www.google.com/recaptcha) 跃入脑海)，考虑一下如何将问题应用到中间件的下游，大多数情况下这样做更有意义。

## 分享这篇文章