# 新遗迹:实时红宝石监控

> 原文：<https://www.sitepoint.com/new-relic-realtime-ruby-monitoring/>

*本文由[新遗迹](http://newrelic.com/)赞助。感谢您支持使 SitePoint 成为可能的公司！*

分析 web 应用程序的性能非常重要。可能存在影响特定用户组的瓶颈，或者可能存在测试中未发现的错误。通常，与生产环境相比，应用程序在开发或试运行时的反应会有所不同，在生产环境中，会有 1000 个(或更多)用户并发访问。当您的生产环境的细微差别出现时，您将需要一种方法来衡量正在发生的事情。哪些请求很慢或正在下降？你使用的外部系统堵塞了管道吗？你的用户是高兴还是沮丧？实时性能监控是找到这类问题答案的唯一途径。

在本文中，我们将探讨一种获得这些答案的方法。传统上，为应用程序设置某种形式的监控既困难又耗时。 [New Relic](https://newrelic.com/) 是一款分析软件即服务，提供关于你的 Ruby(或 node 或。NET 或几乎任何种类的)应用程序。这使您能够监控应用程序的整体性能和健康状况，并掌握其性能。

一旦您收集了指标并分析了图表；您可以根据反馈开始优化应用程序的代码和性能。因为它是实时工作的，New Relic 还将有助于识别当您将更新部署到应用程序时导致的任何新问题。使用 New Relic 的一个很大的优势是所有的数据都是基于你的应用程序的实际使用，并且在发生的时候呈现出来。

New Relic 通过在服务器和您的应用程序之间插入 Rack 中间件来工作，该中间件收集数据，然后将数据发送回 New Relic 服务器。您可能会担心这本身会造成具有讽刺意味的性能下降，但实际上这种差异如此之小，以至于无法察觉。从收集的数据中获得的收益远远超过了在收集数据时造成的微小延迟。

## 支持

New Relic 最初是一个 Ruby 应用程序，所以它对不同版本的 Ruby 都有很好的全面支持。它支持 1.87 以上的所有 MRI 版本、1.6.0 以上的 JRuby 版本和 2.2.1 以上的 Rubinius 版本。对大多数 Ruby Web 服务器也有很好的支持，包括 Passenger、Thin、Puma、Unicorn、Rainbows！，还有韦布里克。你可以使用 Rails，Sinatra，Padrino，或者只是一个简单的 Rack 应用程序。数据库支持也很优秀，Active Record，DataMapper，Sequel，MongoDB 都支持。

## 我们将要报道的内容

在这篇文章中，我将解释如何从头构建一个基本的 Sinatra 应用程序，将其部署到 Heroku，并使用新的 Relic 客户端来分析其性能。我们将讨论以下内容:

*   构建一个基本的 Sinatra 应用程序
*   部署到 heroku
*   建立新遗迹
*   实时监控应用程序

如果你不想用 Heroku 部署你的应用程序，你可以直接去[https://newrelic.com/#signup](https://newrelic.com/#signup)注册一个免费账户。注册后，您将被带到起始页，在那里您按照说明开始。你也可以轻松地使用 Rails 应用程序或任何其他基于框架的框架来代替 Sinatra。

## 制作测试应用程序

让我们从创建一个基本的“Hello World”类型的应用程序开始。首先，我们需要创建一个包含以下内容的 Gemfile:

```
source 'https://rubygems.org'
ruby '2.1.0'
gem "sinatra"
gem "newrelic_rpm"

group :test do
  gem "rack-test"
  gem "rake"
end
```

通过在终端中输入以下内容来安装 gems:

```
$ bundle install
```

现在，在同一个目录中创建一个名为 **test.rb** 的文件，并添加以下代码:

```
ENV['RACK_ENV'] = 'test'
require 'minitest/autorun'
require 'rack/test'
require_relative 'main.rb'

include Rack::Test::Methods

def app
  Sinatra::Application
end

describe "Application" do

  it "should say hello" do
    get '/'
    last_response.must_be :ok?
    last_response.body.must_equal "Hello New Relic!"
  end

end
```

这些是相当标准的规格，使用 MiniTest 来测试非常基本的响应。现在让我们创建一个文件，以便通过这些测试。

创建一个名为 **main.rb** 的文件，并使用以下代码将其保存在同一目录中:

```
require 'sinatra'
require 'newrelic_rpm'

get '/' do
  "Hello New Relic!"
end
```

让我们测试一下它是否有效:

```
$ ruby test.rb
Run options: --seed 5936

# Running:

.

Finished in 0.086612s, 11.5457 runs/s, 23.0914 assertions/s.

1 runs, 2 assertions, 0 failures, 0 errors, 0 skips
```

一切似乎都很好，所以让我们使用 Heroku 部署它。首先我们需要创建一个 **config.ru** 文件，保存在同一个目录下，内容如下:

```
require './main'
run Sinatra::Application
```

现在我们需要使用 Git 进行版本控制:

```
$ git init
$ git add .
$ git commit -m 'initial commit'
```

使用 Heroku 创建应用程序:

```
$ heroku create
```

部署:

```
$ git push heroku master
```

这将成功地将我们的应用程序部署到 Heroku，所以现在我们准备开始使用 New Relic。我们将在终端中使用 Heroku 客户端来实现这一点:

```
$ heroku addons:add newrelic:stark
```

这将安装 Stark 计划，该计划包括所有功能，但仅限于每月平均 1.5 dynos(您可以随时升级到付费计划)。

一旦我们安装了新的遗迹插件，我们需要设置什么应用程序名称将在新的遗迹。这是使用配置变量完成的:

```
$ heroku config:set NEW_RELIC_APP_NAME="Test Sinatra App"
```

要查看新遗迹界面，你必须通过 Heroku 网站:选择你安装了新遗迹插件的应用程序，然后在插件列表中点击新遗迹。

新的遗迹界面将开始加载。

![screenshot1](img/604f2592dc80161743c163488ef3089a.png)

几秒钟后，您应该会看到这样的屏幕:

![screenshot2](img/ed542868b1d48bac61a770f263d563cb.png)

单击您的应用程序名称，您将被带到应用程序监控概述仪表板，它看起来应该类似于下面的屏幕截图:

![screenshot3](img/b53ea83acbe9290003a091affe106ddb.png)

这个仪表板显示了关于您的应用程序的所有基本信息。不幸的是，目前没有数据显示，因为应用程序没有收到任何请求。

为了开始查看一些数据，我们的应用程序需要一些请求。由于我们刚刚推出它，它不太可能有很多游客。谢天谢地，我们可以使用 Apache 基准测试工具来生成大量的请求。要在基于 Debian 的操作系统中安装这个工具，您可以使用以下命令:

```
$ sudo apt-get update
$ sudo apt-get install apache2-utils
```

我们可以用下面的命令为我们的应用程序生成几千个请求:

```
$ ab -n 2000 -c 10 http://hidden-coast-2573.herokuapp.com/
```

显然，您应该在这里替换您自己的应用程序的 URL！

让我们回到新遗迹仪表板，看看一些数据:

![screenshot4](img/1518536044e581b50b9fe8d1bdfb9a6e.png)

这里提供了大量的信息，一开始可能会令人望而生畏。我们将从一些基本的统计数据开始，比如响应时间、吞吐量、Apdex 分数、错误率和最近的事件。控制面板默认显示过去 30 分钟的统计数据，尽管可以通过单击右上角的“过去 30 分钟”按钮并选择另一个时间段来进行更改。

## 响应时间、吞吐量和 Apdex 得分

在 Monitoring Overview 屏幕上，您可以在左上角的折线图中看到平均请求时间。您可以悬停、单击并拖动图表上的任何位置，以获取更多信息并放大。正如您在截图中看到的，我们的应用程序目前需要 10 毫秒到 50 毫秒来处理每个请求。

吞吐量显示在仪表板的右中部，代表我们的应用程序每分钟可以处理的请求数量。再看一下上面的截图，我们的应用程序每分钟处理一千到两千个请求。

仪表板的右上角是一个名为 Apdex score 的指标。这是根据页面加载时间来衡量用户满意度的行业标准。它的工作原理是设置一个 T 值，这是一个页面应该加载的阈值时间。New Relic 默认这个是 0.5 秒。然后，Apdex 会跟踪每个页面的加载时间，并将其分类为:

*   满意:响应时间小于或等于 T (0.5 秒或更短)
*   容忍:响应时间大于 T 且小于或等于 4T(介于 0.5 秒和 2 秒之间)
*   受挫:响应时间大于 4T (2 秒或更长)

我们的页面加载时间非常快，给了我们一个优秀的 Apdex 分数。

错误率显示在仪表板的中间底部，目前我们做得很好，为 0%，完全没有错误！

## 放慢速度

NewRelic 最有用的一点是，你可以做出改变，并立即看到它们的效果。要了解这一点，让我们添加另一个内置随机延迟的路由处理程序。这将使网页需要一点时间来加载，我们应该看到新的遗迹反映的变化。

首先，让我们在 **test.rb** 文件中的 describe 块内添加一个额外的规范:

```
it "should get there in the end" do
  get '/delay'
  last_response.must_be :ok?
  last_response.body.must_equal "Sorry for keeping you waiting"
end
```

在 **main.rb** 的底部添加相应的 route handler:

```
get '/delay' do
  sleep rand(3)
  "Sorry for keeping you waiting"
end
```

此代码将在显示消息之前添加 0 到 3 秒之间的随机延迟。

让我们测试一下代码是否有效:

```
$ ruby test.rb

Run options: --seed 44113

# Running:

..

Finished in 1.140616s, 1.7534 runs/s, 3.5069 assertions/s.

2 runs, 4 assertions, 0 failures, 0 errors, 0 skips
```

一切正常，所以让我们部署到 Heroku:

```
$ git commit -a -m 'Added a delay'
$ git push heroku master
```

现在我们需要为这个 URL 生成几个请求:

```
$ ab -n 2000 -c 10 http://hidden-coast-2573.herokuapp.com/delay
```

这将花费比以前更长的时间(因为我们已经创建了内置的延迟)。

现在我们可以看看新遗迹中的结果:

![screenshot5](img/08b740fdc3ecd47bf7b6ab1b49e9e7f4.png)

首先，注意折线图上的垂直线——这标志着我们进行部署的时间。该部署也作为一个条目出现在右下角的“最近事件”列中。

我们现在看到请求时间比以前长了很多。该图分为两部分——绿色表示请求时间，蓝色表示 Ruby 处理时间。蓝色的大块告诉我们延迟是由 Ruby 引起的，而不是服务器。吞吐量现在下降到每分钟 200 到 500 个请求之间，这导致我们的 Apdex 得分下降，其中一些请求被分类为“失败”。当 Apdex 得分低于 0.85 时，在最近事件列中也会出现警告。

最棒的是，我们可以在代码部署后立即看到我们的更改所产生的影响。

但是我们仍然没有错误，这是我们将在下一节中解决的问题。

## 错误报告

要查看 New Relic 如何报告错误，请添加另一个路由处理程序，它会在 10%的时间内生成随机错误。首先，让我们向 **test.rb** 中的 describe 块添加一个规范:

```
it "should be fine" do
  get '/error'
  last_response.must_be :ok?
  last_response.body.must_equal "Everything is fine"
end
```

然后在 **main.rb** 的底部添加一个路由处理程序

```
get '/error' do
  if rand(10) == 0
    raise "A Gremlin has got into the system!"
  else
    "Everything is fine"
  end
end
```

这将产生 1/10 的随机误差。

让我们测试一下代码:

```
$ ruby test.rb
Run options: --seed 36091

# Running:

...

Finished in 0.105132s, 28.5355 runs/s, 57.0709 assertions/s.

3 runs, 6 assertions, 0 failures, 0 errors, 0 skips
```

一切似乎都没问题，所以让我们部署代码:

```
$ git commit -a -m 'Added an error'
$ git push heroku master
```

为此 URL 生成一些请求:

```
$ ab -n 2000 -c 10 http://hidden-coast-2573.herokuapp.com/error
```

让我们再来看看《新遗迹》:

![screenshot6](img/f93d8b78c6ffe7f713f5d7158540e4b0.png)

我们可以看到错误率已经攀升到 9.71%，这是我们所期望的。此外，最近事件栏中的一个条目显示错误率超过了 5%。

如果您点击顶部的“事件”选项卡并选择“错误”，您将看到所有已发生错误的明细。正如您在下面的截图中看到的，错误被命名，因此我们可以看到我们的应用程序中的错误是“一个小精灵进入了系统！”

![screenshot7](img/2e81595e3d65302ed0fc5096e70c6769.png)

在“Events”部分，尝试单击“deployments”，在这里您可以查看每个部署的所有统计信息的细分。

## 报告

New Relic 能够生成大量报告来总结应用程序指标。如果您单击顶部的“Reports”选项卡，然后选择“SLA ”,您可以看到一份“Servie Level Agreement”报告，该报告将告诉您每天有多少请求、Apdex 得分是多少、错误率以及有多少用户感到满意、容忍或失望。

## 摘要

这就完成了关于如何将一个基本的 Ruby 应用程序部署到 Heroku 并使用 New Relic 监控其性能的教程。我们已经了解了如何测量响应时间、吞吐量和 Apdex 分数，以及如何在部署代码更新后实时查看这些指标的变化。

New Relic 发现了我们的应用程序中的错误，以及页面加载时间缓慢是由 Ruby 而不是服务器造成的。所有这些信息真正有用的地方在于，它们来自一个正在运行的应用程序——请注意，我们的应用程序通过了我们运行的所有测试，尽管其中隐藏着一些我们只有在重复使用的情况下才能发现的错误。

## 更进一步

在这篇文章中，我们仅仅触及了你可以用 New Relic 监控的表面。以下是其他一些值得研究的特性:

*   您可以为某些事件设置警报，例如当出现某个错误率或 Apdex 分数低于某个级别时
*   您可以使用浏览器选项卡来监控最终用户信息，如浏览器、操作系统和地理区域。这与谷歌分析的工作方式类似
*   您可以监控数据库交互的性能
*   您可以监控外部服务的性能
*   您可以监控后台进程的性能
*   自定义控制面板是一项强大的功能，允许您创建自己的控制面板，其中包含针对任何特定指标的自定义图表和表格。

文档非常全面，帮助台也非常及时，所以一旦开始使用，您应该能够很快掌握这些功能。

## 结论

希望这篇文章有助于展示 New Relic 如何帮助您实时监控 Ruby 应用程序的性能，并在发生时提供即时反馈。这允许您快速响应更新后可能出现的任何问题。

你已经在用 New Relic 了吗？你想尝试一下吗？如果您已经使用了它，您认为哪些指标最有用？请在下面的评论中告诉我们。

## 分享这篇文章