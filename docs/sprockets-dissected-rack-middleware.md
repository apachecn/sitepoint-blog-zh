# 链轮剖析:机架和中间件

> 原文：<https://www.sitepoint.com/sprockets-dissected-rack-middleware/>

![sprocketsmag](img/a1ebe827455041434bf1145c961234b4.png)

在之前的一篇文章中，我解释了如何通过 Rails 和链轮将资产标签插入 HTML。将资产标签插入 HTML 是提供资产的第一步。在这篇文章中，我继续我的旅程，回答剩下的问题:

*   什么是`/assets`及其目的？
*   `ActionDispatch::Static`中间件是用来做什么的？

### 什么是`/assets`？

默认情况下，使用链轮的 Rails 应用程序会在每个资源的路径上添加`/assets`。然而,`/assets`不仅仅是资产的前缀；这也是一个`rack`端点。

基本上，在生成响应时，链轮会在每个资产前加上`/assets`，导致每个请求这些资产的浏览器向`http(s)://your_domain/assets/asset_name`发送调用。`/assets/asset_name`是我们希望浏览器请求的资产的名称。

每当调用`/assets/asset_name`时，Rails 应用程序需要通过服务该资产来响应。正如我提到的，`/assets`是一个`rack`端点，那么它是由我们的 Rails 路由处理的吗？Rails 应用程序中的每个端点或路径都是在`config/routes.rb`中定义的，但是当您查看它的内容时，您不会找到任何`/assets`的路径。然而，我们的资产**正在**得到服务。让我们看看怎么做。

在任何现有的 Rails 应用程序中构建一个`rack`端点非常容易。每个`rack`端点都必须遵循一些严格的准则，这些准则是:

*   它必须响应`call`方法
*   它必须返回一个包含三条信息的数组:HTTP 状态代码、响应头和响应本身

例如，将以下代码放入我们的示例 Rails 应用程序(在第一篇文章中创建)`config/routes.rb`文件中:

```
mount Proc.new {|env| [200, {'Content-Type' => 'text/html'}, ["SitePoint rocks!"]]} => "/sitepoint"
```

`mount`将`/sitepoint`定义为`rack`端点。`Proc.new`将返回一个 Ruby 块，它响应`call`方法。该块返回一个包含状态代码、状态头和响应的数组。

运行 Rails 应用程序(`rails server`)并导航到`http://localhost:3000/sitepoint`。你会看到`SitePoint rocks!`显示在浏览器中。这是对 Rails 中的`rack`端点的快速而肮脏的介绍。让我们回到链轮上，看看链轮是如何做到这一点的。

链轮使用以下代码在`/assets`处安装一个`rack`端点，这里定义为:

```
app.routes.prepend do
  mount app.assets => config.assets.prefix
end
```

`app.assets`是`Rails.application.assets`，它是`Sprockets::Environment`的一个实例，在上一篇文章中讨论过。`config.assets.prefix`是`/assets`。`Sprockets`将`/assets`添加到铁路路线中，因为`rack`将匹配第一个**匹配路线定义中的每个请求。因此，通过前置，`Sprockets`确保了`/assets`优先于其他`rack`端点。**

`app.assets`必须响应`call`，以便作为`rack`端点进行处理。查看`sprockets/lib/sprockets/environment.rb`中`Sprockets::Environment`的代码，`call`继承自`Sprockets::Base`。进一步探索`sprockets/lib/sprockets/base.rb`，它正在包括一些模块，其中一个是`Sprockets::Server`。代码分解成`Sprockets::Server`和`sprockets/lib/sprockets/server.rb`，定义了一个`call`方法。因此，`app.assets`正在从`Sprockets::Server`获取一个`call`方法，每次有对`/assets`的请求时都会调用这个方法。这个`call`方法返回适当的资产。

在`sprockets/lib/sprockets/server.rb:42`定义的`find_asset`，返回`Sprockets::BundledAsset`的一个实例，前提是它是 JavaScript 或者 CSS。如果是图像，则返回一个`Sprockets::StaticAsset`。当浏览器调用`/assets/application.js`时，那么`find_asset`将返回`Sprockets::ProcessedAsset`的一个实例。

在`line:62`有对`ok_response`的调用。`ok_response`在`line:187`上定义，通过将资产放入响应中，根据`rack`规范返回一个数组。这个数组是从`ok_response`返回的，T5 是从`call`返回的。

我们已经看到一个`asset`实例被呈现为对`/assets/application.js`的响应。但是这足以发送回客户端吗？答案是“看情况”。

根据`rack`，从`rack`端点发送的响应必须响应`each`。`Sprockets::BundledAsset`和`Sprockets::StaticAsset`都继承自`Sprockets::Asset`类。`sprockets/lib/sprockets/asset.rb:82`定义了`each`，这意味着我们的`asset`实例响应`each`并且与`rack`兼容。

在`each`方法中，`to_s`作为参数传递给块。在`line:55`有一个方法`to_s`，它返回`asset`的源。每个`asset`实例在一个`source`实例变量中有其来源，该变量用于获取相应资产的源代码。我们在`to_s`方法中做同样的事情，获取源代码并将其返回给适当的方法调用。然后，我们的 Rails 应用程序将所请求资产的源代码传输到 HTTP 客户端。

这就是链轮如何为我们的资产服务。对于每种类型的资产调用、JavaScript、样式表、图像、字体等，上述过程都是相同的。

### 什么是`ActionDispatch::Static`？

`ActionDispatch::Static`是默认 Rails 中间件堆栈中的一个中间件。它的主要目的是服务于静态资产。静态资产是在提供服务之前不需要任何预处理的资产，比如图像和字体。JavaScript 和样式表文件不被认为是静态资产，因为在提供它们之前需要一些预处理。然而，当我们执行`rake assets:precompile`时，那些资产变成了静态资产，因为它们被预处理并保存为文件，所以它们可以像图像一样被提供。让我们看看这个中间件是如何工作的。

`ActionDispatch::Static`配置中间件并添加到中间件栈[这里](https://github.com/rails/rails/blob/v3.2.17/railties/lib/rails/application.rb#L257):

```
middleware.use ::ActionDispatch::Static, paths["public"].first, config.static_cache_control
```

`ActionDispatch::Static`在`actionpack/lib/action_dispatch/middleware/static.rb:47`中定义。它的构造函数中有三个参数:`app`、`path`和`cache_control`。提供`app`参数由`rack`负责。Rails 正在为第二个参数传递`paths["public"].first`。`paths["public"].first`是 Rails 应用程序中`public`目录的绝对路径。如果我们已经在`config/environments`下的相关环境文件中启用了`config.serve_static_assets`，那么`ActionDispatch::Static`就是执行`rake middleware`时列表中的第一个中间件。它将作为我们的 Rails 应用程序的第一个执行点。

看`actionpack/lib/action_dispatch/middleware/static.rb:53`有一个`call`的方法，是`rack`用的。在该方法中，在`line:54`处，正在使用的`HTTP method`上有一个`case`声明。如果请求的`HTTP method`是`GET`或`HEAD`，那么这个中间件正在评估请求路径，看它是否是一个需要服务的静态资产。`line:50`的构造函数为`ActionDispatch::Static`显示如下:

```
@file_handler = FileHandler.new(path, cache_control)
```

在同一个文件中定义了`FileHandler`类，就在`ActionDispatch::Static`之上。在`line:57`，在这个`@file_handler`实例上有一个对`match?`的调用。在`line:12`可以看到`match?`的定义。此方法将评估所提供的路径，以查看它是否是静态资产。如果这个方法返回`true`，那么`ActionDispatch::Static`将服务于资产，并且控制权不会传递给堆栈中的其他中间件。如果`match?`返回`false`，控制权将传递给后续中间件。

`match?`的`line:15`将提供的路径与`@root`连接起来。这是我们的 Rails 应用程序的`public`目录的绝对路径吗？

例如，对`/assets/application.js`的请求与公共目录路径连接起来成为`/Users/imran/work/rails_asset_serve/public/assets/application.js`(您的将会不同)。`line:19`上的代码试图确定这个文件是否存在。如果是，那么这个文件将由`ActionDispatch::Static`处理，否则将由链轮处理。

在`development`环境中，`public`目录中没有资产，所以它们都由链轮处理。然而，在`production`中，`public`目录中的静态资产是存在的，因为我们通常使用`rake assets:precompile`来编译资产。这里，`match?`返回`true`，资产由`ActionDispatch::Static`服务。

在`line:59`，对`@file_handler`实例上的`call`进行调用。`call`在`line:26`上定义，后者又在`@file_server`上调用`call`，这是`Rack::File`的一个实例。

`Rack::File`在`rack/lib/rack/file.rb`中定义。在`line:30`上的`call`方法中，调用了`dup`，这创建了当前实例的浅层副本。在新创建的副本上调用`_call`(在`line:36`上定义)，执行不同的检查，然后在`line:53`上调用`serving`。

在`serving`中，在`line:73`上，`rack`响应的数组被创建，其中`self`作为响应对象出现。在`serving`结束时，返回这个`response`数组。

我之前问过`self`是否足以向客户端发送响应？答案是`yes`。因为`self`响应`line:104`上定义的`each`。在`each`中，打开一个文件进行读取，并以 8KB 的块读取到传入的块中，该块由我们的 Rails 应用程序传输到浏览器。

这就是`ActionDispatch::Static`服务静态资产的方式。

## 结论

Rails 是由 Ruby 社区中最聪明的人编写的令人难以置信的代码库。通过阅读源代码，您不仅提高了技能，还获得了学习其他地方可能找不到的新事物的机会。例如，我发现，通过使用`ActionDispatch::Static`，我们可以使用范围标题为我们的资产的[部分提供服务，这是我以前没有意识到的。](http://en.wikipedia.org/wiki/Byte_serving)

我希望这些文章能帮助您更多地了解导轨和链轮如何为资产服务。请让我知道你的想法！

## 分享这篇文章