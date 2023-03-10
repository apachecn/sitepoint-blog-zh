# 前往古巴，在 Ruby 中使用 React Today

> 原文：<https://www.sitepoint.com/use-react-today-in-ruby-by-heading-to-cuba/>

![react](img/935e65fe46de1cf8938f7daf8b8973fe.png)

你们都听说过 React。它不再是一个新手，已经在易变的 Javascript 生态系统中获得了足够的立足点，我们不担心在 6 个月内学习一些多余的东西。React 是基于一些非常好的想法；它用一个虚拟文档对象模型和一个惊人的快速差分算法取代了模型-视图-控制器模式中的视图，该算法确保对页面的唯一更新是您所做的更改。唯一的缺点是不得不写 JSX 或 Javascript！幸运的是，我们可以用一些专门解决这个问题的 Ruby APIs 来解决这个问题。

### React 适合你的应用吗？

React 在与单页面应用程序一起使用时，或者在任何部分页面重新加载有益的情况下，都会大放异彩。Rails 从 4.0 开始就有了 Turbolinks(如果你不熟悉的话，可以想想 PJAX)，所以如果你想通过部分重新渲染来提高速度，你可能会更喜欢它。React 也没有为您提供任何 AJAX 功能、数据层或任何其他我们在现代 web 开发中认为理所当然的东西。因此，如果你认为它是 Rails 的替代产品，那你的日子就不好过了。

此外，由于它主要是为动态页面重新呈现而设计的，如果你有一个大部分是静态的网站，这将是一个小回报的大投资。哦，如果你支持低于 IE9 的浏览器，那么这不适合你，因为 React 不能和它们一起工作，永远也不会。简而言之，就像任何事情一样，如果你没有发挥你正在使用的工具的优势，那么你最好不要使用它。

说完了，让我们开始吧！

### 综合

您想将它集成到您的 Rails 项目中吗？没问题！Rails 生态系统有几种集成方法，包括 WebPack、Gulp 和一些插件( [react-rails](https://github.com/reactjs/react-rails) 似乎在撰写本文时处于领先地位),它们确实做到了这一点。如果你不使用 Rails 怎么办？如果你更喜欢像古巴或辛纳屈这样的微观框架给你带来的轻松感呢？好吧，那我们还有一点工作要做。

首先，开始一个新的框架项目。为此，我们将使用 Cuba，但它将同样适用于 Sinatra(对您的 **config.ru** 做一些小的修改)。对于快速和肮脏的项目，我使用优秀的 [Cuba Genie](https://rubygems.org/gems/cuba_genie) ，它将 Bootstrap、Cuba 和一堆漂亮的东西捆绑到一个简单的项目中。

假设您有一个简单的项目，下面是您如何集成 React:

将以下宝石添加到您的**宝石文件中:**

```
gem "opal-jquery"
gem "reactive-ruby" 
```

并运行`bundle install`

首先，创建一些简单的 HTML 模板。我正在使用[微粒](https://github.com/soveran/mote):

```
<!-- views/layout.mote -->
<!doctype html>
<html>
  <head>
    <script src="/assets/react.js"></script>
    <script>{{ app.loader }}</script>
  </head>
  <body>
    <div id="layout">
        {{ content }}
    </div>
  </body>
</html>

<!-- home.mote -->
<div id='content'></div> 
```

这里有几件事需要讨论:首先，包含`<div id="content">`。浏览过 React 教程的人可能还记得 React 需要 DOM 上的一个挂载点。这是那个。

**/assets/react . js**–这个文件在我们的文件系统中不存在；它是在运行时从 **react.rb** 传输过来的，我们一会儿就会看到。

`{{ app.loader }}`将翻译成这个字符串:

```
if (typeof(Opal) !== 'undefined') {
  Opal.mark_as_loaded("opal");
  Opal.mark_as_loaded("corelib/runtime.self");
  Opal.mark_as_loaded("jquery.self");
  Opal.mark_as_loaded("sources/react.self");
  Opal.load("react");
} 
```

这是处理加载第三方库，包括 React 和 jQuery，同时允许 transpiler 提供从我们的 Ruby 代码生成的 JS。没有这根线就不行。

现在让我们创建一个简单的 React 类。不管文件类型如何，它都将位于您的 **js** 目录中:

```
# react.rb:
require 'opal'
require 'jquery'
require 'opal-jquery'
require 'reactive-ruby'

class HelloWorld
  include React::Component
  def render
    h1 {"Hello, World!"}
  end
end 
```

这是一个简单的带有文本`h1`、`Hello, World`的`div`。它相当于下面的 JSX:

```
ReactDOM.render(<h1>Hello World</h1>, document.getElementById('content')); 
```

我们将使用 jQuery 的`document.ready`事件来装载 React。这需要将 jQuery 添加到项目中:

```
Document.ready? do
  React.render(React.create_element(HelloWorld), Element["#content"])
end 
```

接下来，我们需要将 loader 方法添加到 Cuba 实例中。这可以通过将以下代码添加到您的 Cuba 类中来实现:

```
# app.rb
require 'opal'
def loader
  opal = Opal::Server.new {|s|
    s.append_path 'js'
    s.main = 'react'
  }
  Opal::Processor.load_asset_code(opal.sprockets, 'react')
end 
```

最后，如果不对我们的 **config.ru** 进行一些修改，所有这些都将无法工作:我们需要为我们的资产提供服务，设置一些模板变量，并为我们的应用程序做好传输准备。你的 **config.ru** 应该是这样的:

```
# config.ru
require 'bundler'
require_relative 'app'

Bundler.require

opal = Opal::Server.new {|s|
  s.append_path 'js'
  s.main = 'app'
}

sprockets   = opal.sprockets
maps_prefix = '/__OPAL_SOURCE_MAPS__'
maps_app    = Opal::SourceMapServer.new(sprockets, maps_prefix)

# Monkeypatch sourcemap header support into sprockets
::Opal::Sprockets::SourceMapHeaderPatch.inject!(maps_prefix)

map maps_prefix do
  run maps_app
end

map '/assets' do
  run sprockets
end

run(Cuba) 
```

这将启动一个 Opal 服务器，为 transpiler 设置入口点，为链轮注入头支持，并从 assets 目录提供所有这些服务。

关键时刻:导航至`127.0.0.1:9292`查看:

```
`Hello, World` 
```

恭喜你；您现在正在一个微框架中运行 Javascript 技术的前沿！

### 笔记

将一些东西集成到您的项目中，这是一个惊人的工作量。我们必须配置 transpilers，设置挂载点，定义入口点，编写 HTML。全都是为了“你好，世界！”。我很快对此感到厌倦，并编写了一个名为 [cuba_react](https://github.com/disavowd/cuba-react) 的 Gem 来为您做所有这些事情。我主张使用 gem，而不是花几个小时在 Pry 的调试控制台上，狂热地试图确定你的 transpiler 看不到源文件或没有返回你认为它是什么的原因。

更重要的是，gem 将自动完成这一切，并允许您跳过无聊的东西，直接开始玩 React！将`cuba_react`添加到您的**gem 文件**、`bundle install`，并利用内置的 CLI 通过`cuba_react generate`为您生成所有这些文件。文档可以在[这里](https://github.com/disavowd/cuba-react)找到。

### 扩展我们的示例

很自然地，“你好，世界！”在现实世界中几乎毫无用处，所以您应该稍微扩展一下我们的示例。查看优秀的 [reactrb 文档](http://reactrb.org)以获得一些可能的示例，但这里有一个简单的登录类:

```
class Nav < React::Component::Base
  def render
    div do
      input(class: :handle, type: :text, placeholder: "Enter Your Handle")
      button(type: :button) { "login!" }.on(:click) do
        alert("#{Element['input.handle'].value} logs in!")
      end
    end
  end
end 
```

至于接下来的步骤，我建议看看官方的 React [入门教程](https://facebook.github.io/react/docs/getting-started.html)，这不是我见过的最好的文档，但肯定会让你熟悉一些概念。这些例子都在 JSX，所以把它们翻译成 Ruby 是留给读者的一个练习，但是它应该是相当简单易懂的。之后，我会向初学者推荐[react](https://reactforbeginners.com)，这是一个很好的视频系列，解释了框架背后的概念，并详细介绍了如何构建一个相对复杂的应用程序。也有一些优秀的 [egghead.io](http://egghead.io) React 视频。

我希望这是一个有益的练习，现在你可以更好地将 React 投入到 Cuba 项目中。编码快乐！

## 分享这篇文章