# PHP vs. RUBY:有什么意义？

> 原文：<https://www.sitepoint.com/php-vs-ruby-whats-the-point/>

毫无疑问，你已经读过很多文章中的一篇，比较了 PHP 和 Ruby 的优点，或者更常见的，让我恼怒的是，PHP 和 Rails。我希望你和我一样，认为这些文章是毫无意义的练习，无异于语言套话。这不是一个公平的竞争环境。

不知何故，为网络开发将这两种不同的野兽放在一起进行比较。PHP 又名个人主页工具(感谢[维基百科](http://en.wikipedia.org/wiki/PHP))是一种专门为网络开发的语言/框架。另一方面，Ruby 是一种通用语言，由一个叫 Matz 的人构思，目的是创造一种乌托邦式的编程语言。

从 PHP 开始，您可能会创建一些带有 HTML 代码的页面。然后在意识到维护起来有多痛苦之后，您将开始使用类似于 [Smarty](http://www.smarty.net/) 的东西来抽象您的业务逻辑和表示层。你会变得更加面向对象，毫无疑问，你会抓住像 [Zend Framework](http://framework.zend.com/ "Zend Framework") 或 [CodeIgniter](http://codeigniter.com/ "Code Igniter") 这样的东西来开发你自己的应用程序。希望这些听起来都很熟悉。

说到 Ruby，那么多开发者忽略了这样一条明智的开发之路。我知道我确实做到了。大多数人从哪里开始接触 Ruby，包括我自己？当然是铁轨。我们看了“建立一个博客”的视频，很快，我们都被卖了。我绝不会阻止任何人学习 Rails 并使用它来运行，但是它不是适合初学者的 Ruby。当你使用 Rails 时，很多优秀的开发人员已经花了很多时间抽象出所有可怕的细节。迁移只是工作，路由只是工作，日志和测试就在那里供您使用。Rails 是一个框架，它让您能够专注于您想要解决的问题。

## Hello Rack

开始使用 Rails 的一个很好的理由是，当涉及到 web 开发时，Ruby 本身就是一种入侵。当然，我们可以使用标准库`CGI`类，上传文件到服务器，使其可执行，我们就完成了。与 PHP 脚本相比。

甚至 DHH 也在博客上谈论 PHP 的即时性。你的满足感是即时的。想测试一个快速的 bug 修复吗？只需点击浏览器上的刷新。没有这种重启杂种狗，乘客，或任何需要。

那么，我们怎样才能在不求助于`rails s`的情况下获得那种即时的 Ruby web 应用呢？那么，我们使用 Rails 本身使用的框架怎么样？[架](http://rack.rubyforge.org/)。

Rack 是 Rails 应用程序和 HTTP 协议之间的接口。它也是几乎所有 Ruby web 框架的基础，包括辛纳特拉、T4 和 T2。

Rack 整合了框架开发人员跨项目复制所有底层代码。它基本上收集任何可用的 web 服务器，并使用它来服务您的应用程序(我们所说的 web 服务器是 mongrel、WEBrick、thin 等等)。

要开始使用 Rack，只需安装 gem，创建 rackup 文件(*。ru)，并启动 app。

机架的 **hello world** 如下(`hello.ru`):

```
class HelloWorld
  def call(env)
    [200, {"Content-Type" => "text/html"}, ["<h1>Hello world!</h1>"]]
  end
end

run HelloWorld.new
```

然后在控制台里，`rackup hello.ru`。您将会看到服务器启动端口(通常是 9292)的一些服务器输出。只要导航到 http://localhost:9292 就能看到荣耀了。

为了剖析这个简单的应用程序(它确实是一个应用程序)，基本上我们有一个名为`call`的方法，它接收环境并返回一个由三个部分组成的数组:状态、标题和主体。就环境而言，我们不是在谈论登台、生产等。相反，它是我们在 PHP 的`$_SERVER`超级全局、`REQUEST_METHOD`等中看到的更多 CGI 变量集。

状态代码非常简单明了，头散列的内容也很熟悉。我们在 hello world 示例中看到的主体是一个数组，或者更具体地说，它必须响应`each`。最后，在文件的结尾，我们看到`run`方法正在创建 hello world 应用程序的一个实例。

所以 Rack 应用程序的基本规则是它必须有一个方法`call`，该方法接受环境散列并返回状态、头和主体，主体必须响应`each`。

## 回声“你好，世界”

我们已经看到了一个基本的机架应用程序，但这与以下简单性相比如何呢:

```
<? php
echo "<h1>Hello World</h1>";
?>
```

从表面上看，它似乎更令人费解，所以让我们看看 Rack 给了你什么让它更有吸引力。

## 建筑商

说到 **hello world** PHP 是很难被击败的。幸运的是，对于美国的 Rubyist 来说， **hello world** 应用程序的需求很低，而 Rack 提供的不仅仅是包装服务器。Rack 本身附带了许多微型 Rack 应用程序，可以帮助我们构建框架。它们包括所有有用的东西，比如日志、会话、url 映射等等。

Rack 的绝对瑰宝之一必须是`Rack::Builder`。这是一种领域特定语言(DSL ),允许轻松地构建和混合机架应用程序。考虑构建一个包含公共页面和秘密页面的 PHP 应用程序。在 PHP 中，我们可以创建两个文件来执行这些任务。另一种方法是创建一个`.htaccess`文件，将所有传入的请求指向一个文件，如下所示。

```
RewriteEngine on
RewriteBase /
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^(.+)$ /index.php?page=$1 [L]
```

```
<?php
if ($_SERVER['REQUEST_URI'] == '/secret') {
  echo "Shhhh";
} else {
  echo "This is public";
}
?>
```

不算太坏，它使用 Rack::Builder 的实现看起来像这样:

```
app = Rack::Builder.new do
  map "/" do
    run Proc.new {|env| [200, {"Content-Type" => "text/html"}, ["This is public"]] }
  end

  map "/secret" do
    run Proc.new {|env| [200, {"Content-Type" => "text/html"}, ["Shhhh"]] }
  end
end

run app
```

很整洁吗？我们在这里所做的是实现生成器来将 URL 映射到给定的动作。这些动作只是`Proc`刚刚我们找到我们的脚，他们返回我们在**你好世界**应用中看到的黄金三重奏。

这也是无限可扩展的。例如，我们来看一个路径，比如“/secret/files”。我们的 PHP 版本变得令人毛骨悚然，足以让我们重新思考(我们不想再向相对目录添加文件了，对吗？)，在 Rack 中我们简单地嵌套一些`map`块。

```
map "/secret" do
  map "/" do
    run Proc.new {|env| [200, {"Content-Type" => "text/html"}, ["Shhhhh"]] }
  end

  map "/files" do
    run Proc.new {|env| [200, {"Content-Type" => "text/html"}, ["Here be dragons"]] }
  end
end
```

希望你几乎被卖了。当我们还在感受爱情的时候，让我们添加一些古怪的玩具来增加一点情趣。

## Rack =非常性感

我们之前提到过，机架不仅仅是一个服务器接口。它附带了大量本身就是机架应用程序的“组件”。现在，我们将看看如何实现其中的几个。

我总是发现日志在开发应用程序时很有帮助。

```
require 'logger'

  app = Rack::Builder.new do
  use Rack::CommonLogger
  Logger.new('my_rack.log')

  map "/" do
    run Proc.new {|env| [200, {"Content-Type" => "text/html"}, ["This is public"]] }
  end

  map "/secret" do
    map "/" do
      run Proc.new {|env| [200, {"Content-Type" => "text/html"}, ["Shhhhh"]] }
    end

  map "/files" do
    run Proc.new {|env| [200, {"Content-Type" => "text/html"}, ["Here be dragons"]] }
  end
end

run app
```

我们一直在谈论秘密区域和龙，所以我们最好把它们都锁起来。HTTP 基本认证总是有利于保护事物。

```
require 'logger'

  app = Rack::Builder.new do
  use Rack::CommonLogger
  Logger.new('my_rack.log')

  map "/" do
    run Proc.new {|env| [200, {"Content-Type" => "text/html"}, ["This is public"]] }
  end

  map "/secret" do
    use Rack::Auth::Basic do |user, password|
      user == 'super_user' && password == 'secret'
    end

    map "/" do
      run Proc.new {|env| [200, {"Content-Type" => "text/html"}, ["Shhhhh"]] }
    end

    map "/files" do
      run Proc.new {|env| [200, {"Content-Type" => "text/html"}, ["Here be dragons"]] }
    end

  end
end

run app
```

这非常简单，我还记得创建的日子。htpasswd 文件等等。

## 支持

我开始写这篇文章时，好像它是一篇 PHP vs Ruby 的废话。在整篇文章中，我提到了我们如何在 PHP 中做到这一点，并将其与 Rack 进行了比较。这完全是一种狡猾的冲动，在我们每个人身上捕食语言巨魔。事实是，两者都有优点，甚至将 Rack 与 PHP 进行比较也是不公平的。

我希望这已经让您体会到使用 Rack 是多么灵活、可维护和令人愉快。当学习 Ruby 时，这是一个很好的起点，因为有足够的“魔力”来保持我们的兴趣，但不足以模糊学习。

但是我们还没有完成。你会记得所有的大框架都是建立在机架之上的。我们实际上可以使用 Rack 实现神秘的中间件，拦截我们应用程序的正常流程，并暂时将控制权交给 Rack 应用程序。可能有点吓人，但是 Rails 喜欢。

## 分享这篇文章