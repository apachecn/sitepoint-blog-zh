# 凤凰为铁路:形式助手和模型

> 原文：<https://www.sitepoint.com/phoenix-for-railsies-form-helpers-and-models/>

![phoenix](img/a681ad42c41b241cbff8069a281bbf9a.png)

Phoenix 是一个为 [Elixir](http://elixir-lang.org) 构建的 web 框架，这是一种构建在 Erlang 虚拟机上的令人敬畏的语言。Phoenix 来自 Rails 背景，看起来很熟悉。尽管它缺少 Rails 带给我们的一些好处，但 Phoenix 对 WebSockets 等技术有很好的支持，这使它成为现代 web 开发的一个很好的候选。其次，由于它是在 Erlang 虚拟机上构建的，许多基准测试表明它可以比 Rails 更好地扩展。在任何情况下，它都是添加到任何 Rubyist 的腰带上的一个伟大的工具。

在我的上一篇文章中，我们介绍了凤凰城的一些基础知识。这一次，我们将使用表单助手和模型构建 Remynders 应用程序。让我们直接开始吧。你可以在 Github 仓库中找到本文和最后一篇文章[中的代码。](https://github.com/sitepoint/remynders)

## 表单 HTML

如果您回到我们上次离开的地方(查看存储库的“article1”分支)，我们设置了索引视图，但是想要设置一个表单来创建一个新的提醒。这需要在**web/views/reminder _ view . ex**中创建一个空视图:

```
defmodule Remynders.ReminderView do
  use Remynders.Web, :view
end 
```

我们仍然需要一个模板来向用户显示表单。让我们在**templates/reminder/new . html . eex**中创建模板:

```
<form>
  <div class="input_wrapper center_block">
    <label>Reminder text</label>
    <input type="number" value="10"></input>
  </div>

  <div class="input_wrapper center_block">
    <label>Minutes from now</label>
    <input type="number" value="10"></input>
  </div>

  <div class="input_wrapper center_block">
    <label>Your email</label>
    <input type="email"></input>
  </div>

  <div class="input_wrapper center_block">
    <input type="submit" value="Remynd me" class="button"></div>
  </div>
</form> 
```

BOO 到难看的、没有样式的输入字段，所以在 **web/static/app.scss** 中添加一些 CSS:

```
textarea:focus, input:focus{
    outline: 0;
}

label {
  font-weight: bold;
  font-size: 20px;
  display: block;
  margin: 20px;
}

.input_wrapper {
  margin-top: 20px;
}

input[type="number"], input[type="email"], input[type="number"]:active {
  font-size: 25px;
  text-align: center;
  font-weight: bold;
  border: 5px solid #000;
  padding: 15px;
  width: 225px;
}

input[type="email"] {
  font-size: 18px;
}

input[type="submit"] {
  width: 265px;
  padding: 15px;
  border-color:; #cc0000;
  background-color: #fff;
} 
```

现在，看一看`http://127.0.0.1:4000/reminders/new`，这是表单。但是，等一下，我们实际上并没有用这个表单填充模型。为此，使用`form_for`表单助手。在我们做*那个*之前，是时候设置我们的模型了。

## 模型

我们需要一个模型来代表提醒的概念。在 Phoenix 中， [Ecto](https://github.com/elixir-lang/ecto) 用于处理与数据库相关的事情。基本上，这是一种通过特定领域语言在 Elixir 中编写查询的方法。这与基于 OOP 的 Rails 方法有点不同，但是大部分设置非常相似。

在 Rails 中，我们会发出一个`rails generate model`命令，后跟一个`rake db:migrate`。凤凰城非常相似:

```
mix phoenix.gen.model Reminder reminders minutes:integer email:string title:string 
```

我们应该得到如下输出:

*   `creating priv/repo/migrations/20150805211918_create_reminder.exs`
*   `creating web/models/reminder.ex`
*   `creating test/models/reminder_test.exs`

在那里，模型被创建。但是，我们需要在数据库中为它腾出空间。Ecto 附带了 MySQL 和 PostgreSQL 的适配器。默认情况下，Phoenix 会为您设置 PostgreSQL 适配器。为此，我们需要几个命令(根据 PostgreSQL 设置的需要编辑 **config/dev.exs** ):

```
$ mix ecto.create
$ mix ecto.migrate 
```

Phoenix 从 Rails 和类似 Rails 的框架中借鉴了很多想法，包括迁移。在 **priv/repo/migrations** 中，您将找到由这两个命令设置的模型和数据库表的迁移。

## 表单助手

现在我们有了模型，让我们设置表单来实际使用模型。看一下 **web/models/reminder.ex** 中的模型:

```
defmodule Remynders.Reminder do
  use Remynders.Web, :model

  schema "reminders" do
    field :minutes, :integer
    field :email, :string
    field :title, :string

    timestamps
  end

  @required_fields ~w(minutes email title)
  @optional_fields ~w()

  @doc """
  Creates a changeset based on the `model` and `params`.

  If `params` are nil, an invalid changeset is returned
  with no validation performed.
  """
  def changeset(model, params \\ :empty) do
    model
    |> cast(params, @required_fields, @optional_fields)
  end
end 
```

这里重要的方法是`changeset`。我们不需要太担心它是如何工作的，但是调用这个方法基本上会为模型创建一个“变更集”，以防我们想要更新它。我们需要更新控制器代码(**web/controllers/Reminder _ controller . ex**)，以允许模板访问新提醒实例的变更集:

```
def new(conn, _params) do
  changeset = Reymnders.Reminder.changeset(%Reymnders.Reminder{})
  render conn, "new.html", changeset: changeset
end 
```

这是怎么回事？基本上，我们通过一个新的 Reminder 实例建立一个变更集，并将其传递给模板。让我们将模板(**web/templates/reminder/new . html . eex**)更改为:

```
<%= form_for @changeset, reminder_path(@conn, :create), fn f -> %>
  <div class="input_wrapper center_block">
    <label>Reminder text</label>
    <%= text_input f, :title %>
  </div>

  <div class="input_wrapper center_block">
    <label>Minutes from now</label>
    <%= number_input f, :minutes, [{:value, "10"}] %>
  </div>

  <div class="input_wrapper center_block">
    <label>Your email</label>
    <%= text_input f, :email %>
  </div>

  <div class="input_wrapper center_block">
    <%= submit "Remynd me" %>
  </div>
<% end %> 
```

正如你所看到的，HTML 现在使用表单助手。现在，我们需要向控制器添加一个`create`动作:

```
def create(conn, %{"reminder" => reminder_params}) do
  changeset = Reminder.changeset(%Reminder{}, reminder_params)
  case Repo.insert(changeset) do
    {:ok, _reminder} ->
      conn
      |> put_flash(:info, "Reminder created successfully.")
      |> redirect(to: "/")
    {:error, changeset} ->
      render(conn, "new.html", changeset: changeset)
  end
end 
```

此外，为了能够只将提醒模型称为`Reminder`而不是`Remynders.Reminder`，创建一个别名，就像我们上次为`Helpers`所需要的那样。所以，把这个贴在控制器中的`defmodule`行之后:

```
alias Remynders.Reminder 
```

用填写好的表格快速检查，很明显我们的代码是有效的，但是目前还很模糊。让我们一点一点地分解它。

```
def create(conn, %{"reminder" => reminder_params}) do 
```

在我们看第二个参数之前，这看起来是一个无关紧要的函数定义。到底发生了什么事？！这使用了 Elixir 的一个类似函数式编程的特性:模式匹配。我们说的是，在第二个`create`的论证中，期望得到一本字典。我们希望将这个字典中与键“reminder”相关联的值放入变量`reminder_params`中，以便稍后在方法中引用它。

```
changeset = Reminder.changeset(%Reminder{}, reminder_params) 
```

这是从我们刚刚收到的提醒参数中构造一个变更集。具体来说，它是一个带有`%Reminder{}`的空提醒实例和一组应该带有`reminder_params`的值。然后，`Reminder.changeset`指出为了从第一个参数中得到第二个参数中的值，需要做什么改变。

```
case Repo.insert(changeset) do 
```

首先要考虑的重要部分是`Repo.insert(changeset)`。一般来说，数据库的改变是通过`Repo`模块来完成的。通过该语句，我们告诉 Ecto 将变更集插入底层数据库。接下来，考虑一下`case`关键词。这有点像关于`Repo.insert`的返回值的`switch`语句(如果你有 C/Java 背景的话),但是大约强大一百万倍。本质上，我们是根据返回值进行模式匹配。

```
{:ok, _reminder} ->
    conn
    |> put_flash(:info, "Reminder created successfully.")
    |> redirect(to: "/") 
```

这是案例陈述的一部分。这里，我们考虑提醒被成功插入数据库的情况。具体来说，如果我们得到给定格式的返回值(即使用`:ok`而不是`:error`)，那么这个块中的代码应该会执行。为了理解代码的其余部分，我们需要理解`|>`操作符。基本上，`a |> b(c, d)`是`b(a, c, d)`的不同写法，即左手被求值，然后作为第一个参数传递给右半部分。如果您碰巧喜欢 Haskell，这有点像`$`操作符的逆操作。如果你碰巧对 F#感兴趣，那么它或多或少就像管道操作符。

我们使用`|>`操作符将这些方法调用链接在一起，因为`put_flash`和`redirect_to`都需要`conn`作为它们的第一个参数。这放入了一个“flash”通知(就像我们在 Rails 中处理`flash[:notice]`一样)，然后重定向到应用程序的根目录。

```
{:error, changeset} ->
    render(conn, "new.html", changeset: changeset) 
```

第二种情况简单得多。万一出错，只需将变更集传递回我们刚才所在的页面，以便用用户已经提供的值加载表单。但是，我们如何告诉用户到底哪里出错了呢？

## 包装它

这一点以及其他一些事情将是下一篇文章的主题。我们仍然需要实现通知和错误，以及一种及时向用户交付剩余内容的方法。在下一期文章中，我们将讨论这两个问题以及其他一些趣闻。在写这篇文章时，我意识到 Phoenix 目前没有令人惊讶的、最新的文档，这对于一个仍在发展中的项目来说有点过分了。希望这个指南有助于填补这一空白，并为想尝试新事物的铁路人提供一个快速入门的过程。

## 分享这篇文章