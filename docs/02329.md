# 塞子对于长生不老药就像架子对于红宝石一样

> 原文：<https://www.sitepoint.com/elixir-plugs-are-your-friends/>

![elixir](img/2917518d7edfb61f6808ed65bee1c7b0.png)

[插头](https://github.com/elixir-lang/plug)之于[仙丹](http://elixir-lang.org)犹如[挂架](http://rack.github.io)之于[红宝石](https://www.ruby-lang.org/en/)。因此，它们都使用函数来操作 web 请求。插头背后的原理很简单，就像它们的实现一样。在本文中，我们将探索基本的插件用法，从“hello world”到 JSON API。

## 先决条件

1.  [仙丹](http://elixir-lang.org/install.html)并掌握基础仙丹[语法和概念](http://elixir-lang.org/getting-started/introduction.html)。
2.  web 请求和 HTTP 的基本知识。

## 该项目

首先，我们将使用 [mix](http://elixir-lang.org/getting-started/mix-otp/introduction-to-mix.html) 创建一个基本的 Elixir 项目。姑且称我们的项目为*间谍*。

```
$ mix new spy

* creating README.md
* creating .gitignore
* creating mix.exs
* creating config
* creating config/config.exs
* creating lib
* creating lib/spy.ex
* creating test
* creating test/test_helper.exs
* creating test/spy_test.exs

Your Mix project was created successfully.
You can use "mix" to compile it, test it, and more:

    cd spy
    mix test

Run "mix help" for more commands. 
```

如果这个输出看起来不熟悉，请查看 mix 上的本指南[，这样您就可以跟上速度。](http://elixir-lang.org/getting-started/mix-otp/introduction-to-mix.html)

现在是开始工作的时候了:首先我们必须将插件和 web 服务器(Cowboy)添加到我们项目的`mix.exs`中。然后，我们需要将它们添加到我们的应用程序的依赖项中(*是*，这是有区别的，而*是*，我知道这令人困惑。)这里是成品:

```
#mix.exs

defmodule Spy.Mixfile do
  use Mix.Project

  def project do
    [app: :spy,
     version: "0.1.0",
     elixir: "~> 1.3",
     build*embedded: Mix.env == :prod,
     start*permanent: Mix.env == :prod,
     deps: deps()]
  end

  # Configuration for the OTP application
  #
  # Type "mix help compile.app" for more information
  def application do
    [applications: [:logger, :cowboy, :plug]]
  end

  # Dependencies can be Hex packages:
  #
  #   {:mydep, "~> 0.3.0"}
  #
  # Or git/path repositories:
  #
  #   {:mydep, git: "https://github.com/elixir-lang/mydep.git", tag: "0.1.0"}
  #
  # Type "mix help deps" for more examples and options
  defp deps do
    [
      {:cowboy, "~> 1.0.0"},
      {:plug, "~> 1.0"}
    ]
  end
end 
```

现在我们可以安装我们刚刚用`mix deps.get`指定的依赖项了。一旦完成，我们就可以开始构建我们的项目了。我们将从创建一个用`Hello, world!`响应任何`GET`请求的插件开始。为此，我们将像这样构建我们的`Spy`模块:

```
#lib/spy.ex

defmodule Spy do
  import Plug.Conn

  def init(options), do: options

  def call(conn, _opts) do
    conn
    |> put_resp_content_type("text/plain") # another plug
    |> send_resp(200, "Hello, world!")
  end
end 
```

导入`Plug.Conn`模块后，我们定义了两个函数:`init/1`和`call/2`。**这是制作模块插头所需的两个功能。**

深入研究一下`call/2`函数，我们可以看到它也调用了两个 plugs。这就是插头的美妙之处:因为它们只是操纵连接的纯函数，所以它们是可组合的。这意味着任何一个插件都可能调用其他几个插件来完成它的工作。记住:插头只是接受和返回连接的函数。*就这么简单。*

让我们启动一台服务器来测试我们的插件。

```
$ iex -S mix
iex> {:ok, _pid} = Plug.Adapters.Cowboy.http(Spy, [])
# => {:ok, #PID<0.201.0>}

$ curl http://localhost:4000/
Hello, world! 
```

**先不说:**虽然在大多数情况下，我们不希望以如此冗长的方式启动我们的服务器，但这些例子更多的是说教，而不是现实，所以这种方法就足够了。只要您打开 IEx，这个服务器进程就会运行，所以要在更改代码后重新启动，这意味着您将通过再次运行这一行代码或键入`recompile`，然后按向上箭头并在您的历史记录中找到这一行来重新启动服务器。

所以我们刚刚用`curl`测试了我们的插头，效果很好！让我们更进一步，返回一些 JSON。

**步骤 1:** 将 JSON 解析器添加到应用程序中。

我们将使用[毒药](https://github.com/devinus/poison)作为我们的 JSON 编码器。让我们将它添加到我们的应用程序中:

```
#mix.exs

defmodule Spy.Mixfile do
  use Mix.Project

  def project do
    [app: :spy,
     version: "0.1.0",
     elixir: "~> 1.3",
     build_embedded: Mix.env == :prod,
     start_permanent: Mix.env == :prod,
     deps: deps()]
  end

  def application do
    # Add Poison as application dependency.
    [applications: [:logger, :cowboy, :plug, :poison]]
  end

  defp deps do
    [
      {:cowboy, "~> 1.0.0"},
      {:plug, "~> 1.0"},
      # Add Poison as Hex dependency.
      {:poison, "~> 2.2"}
    ]
  end
end 
```

既然已经添加了毒药，我们需要将响应类型改为`application/json`，然后编码一个到 JSON 的映射。这就像修改 3 行代码一样简单:

```
#lib/spy.ex

  defmodule Spy do
  # ... (concatenated for brevity)
  def call(conn, _opts) do
    # encode to JSON
    body = %{body: "Hello, world!"} |> Poison.encode!

    conn
    |> put_resp_content_type("application/json") # JSON type
    |> send_resp(200, body) # send it!

  end
  # ...
end 
```

我们用`curl`再测试一下这个。注意:如果你得到一个关于缺少依赖项和运行`mix deps.get`的消息，那么你应该，嗯，运行`mix deps.get`。另一个注意:你可以用 CTRL+C 来获取 iex 的。

```
$ curl http://localhost:4000/
{"body": "Hello, world!"} 
```

成功了！现在我们已经有了插件的基础，让我们创建 Spy 的主要功能:列出 cookies。这不是最迷人的功能，但仍然是一个有用的功能。让我们制定一个如何实施的计划:

1.  从响应中获取 cookies
2.  将 cookies 编码到 JSON
3.  为演示目的设置一个新的 cookie

既然我们有了一个计划，一步一步地执行应该很简单。让我们开始第一步:*从响应*中获取 cookies。听起来很简单，但是具体应该怎么实施呢？幸运的是，长生不老药包(大部分)都有精美的文档，所以我们可以[轻松找到正确的功能](https://hexdocs.pm/plug/Plug.Conn.html)。快速搜索“cookie”，会弹出一个选项列表。哪一个？看起来`fetch_cookies/2`功能/插件正是我们要找的。因此，让我们通过重新定义传递给主插件的内容，将这一点添加到我们的应用程序中，并取消该过程中的第 2 步:

```
#line 7 of lib/spy.ex

body = conn |> fetch_cookies |> Map.get(:cookies) |> Poison.encode! 
```

这一行简单地将连接引入到`fetch_cookies`插头中，然后该插头加载 cookies。这些 cookies 以及连接通过`Map.get/2`函数获取，然后编码到 JSON。就是这样！我们刚刚完成了 2/3 的步骤，但是我们还有一步:*设置一个新的 cookie 用于演示目的*。

对于我们的例子，我们将制作一个简单的 cookie，用键`hello`将当前日期(以字符串形式)分配给一个 cookie。如果我们回头看看我们的[插件文档](https://hexdocs.pm/plug/Plug.Conn.html)并进行搜索，我们可以看到`put_resp_cookie/4`非常适合这个。要添加我们的 cookie，我们只需将这个插件放在我们的`call/2`函数中初始调用`conn`之后。让我们来看看成品:

```
#lib/spy.ex
defmodule Spy do
  import Plug.Conn

  def init(options), do: options

  def call(conn, _opts) do
    body = conn |> fetch_cookies |> Map.get(:cookies) |> Poison.encode!

    conn
    |> put_resp_cookie("hello", DateTime.utc_now |> DateTime.to_string)
    |> put_resp_content_type("application/json") # another plug
    |> send_resp(200, body)
  end
end 
```

现在我们应该重启服务器并打开`http://localhost:4000/`，但这次是在浏览器中。第一次加载时，您可能会看到一个空白页。这很好！这意味着您的浏览器上没有存储 cookies。现在，如果您重新加载页面，您将看到我们的插件使用当前时间(分配 cookie 的时间)分配了一个新的 cookie。

*为什么我在第一次重新加载时看不到我的 cookie？*由于我们插件的设置方式，我们只能在重新加载后才能看到 cookie，因为`body`是在 cookie 设置前赋值的。

## 最后

我们刚刚创造的只是展示了 plug 全部潜力的极小一部分。插头构成了凤凰号的基础，就像齿条构成了轨道的基础一样。有了插件，你可以做任何事情，从简单的 HTTP 服务到健壮的 API。结合快速增长的 Elixir 社区和 Erlang 的纯速度和故障保护特性，您就踏上了开发人员的天堂之路。

## 分享这篇文章