# Elixir 的 Ecto 查询 DSL:超越基础

> 原文：<https://www.sitepoint.com/elixirs-ecto-querying-dsl-beyond-the-basics/>

![elixir logo](img/9741ad77d53dee5a432797fe68ac3186.png)

本文建立在我在[理解 Elixir 的 Ecto 查询 DSL:基础知识](https://www.sitepoint.com/understanding-elixirs-ecto-querying-dsl-the-basics/)中介绍的基础之上。我现在将探索 Ecto 更高级的特性，包括查询合成、连接和关联、SQL 片段注入、显式强制转换和动态字段访问。

再一次，我们假设了关于仙丹的[基础知识，以及我在](http://elixir-lang.org/getting-started/introduction.html)[介绍仙丹的 Ecto 库](https://www.sitepoint.com/introduction-to-elixirs-ecto-library/)中提到的 Ecto 基础知识。

## 查询组合

Ecto 中的独立查询可以组合在一起，允许创建可重用的查询。

例如，让我们看看如何创建三个独立的查询并将它们组合在一起，以获得更简洁、更可重用的代码:

```
SELECT id, username FROM users;
SELECT id, username FROM users WHERE username LIKE "%tp%";
SELECT id, username FROM users WHERE username LIKE "%tp%" LIMIT 10, 0; 
```

```
offset = 0
username = "%tp%"

# Keywords query syntax
get_users_overview = from u in Ectoing.User,
  select: [u.id, u.username]

search_by_username = from u in get_users_overview,
  where: like(u.username, ^username)

paginate_query = from search_by_username,
  limit: 10,
  offset: ^offset

# Macro syntax
get_users_overview = (Ectoing.User
|> select([u], [u.id, u.username]))

search_by_username = (get_users_overview
|> where([u], like(u.username, ^username)))

paginate_query = (search_by_username
|> limit(10)
|> offset(^offset))

Ectoing.Repo.all paginate_query 
```

SQL 版本非常重复，但是另一方面，Ecto 版本非常枯燥。第一个查询(`get_users_overview`)只是检索基本用户信息的一般查询。第二个查询(`search_by_username`)建立在第一个查询的基础上，根据我们正在搜索的用户名过滤用户名。第三个查询(`paginate_query`)建立在第二个查询的基础上，在第二个查询中，它限制了结果并从特定的偏移量获取结果(为分页提供基础)。

不难想象，当搜索某个特定用户时，上述三个查询可以一起使用来提供搜索结果。每个查询还可以与其他查询结合使用，以满足其他应用程序的需求，而无需在整个代码库中重复查询的某些部分。

## 加入和关联

查询时，连接是非常基本的，然而我们现在才刚刚涉及到它们。这样做的原因是因为仅仅学习 Ecto 中的连接是没有用的:我们还需要了解关联。虽然这些并不难学，但它们并不像到目前为止所涉及的其他主题那样无关紧要。

简而言之，关联使开发人员能够处理模型中的表关系(实现为外键)。它们在每个模型的模式中使用 [`has_one/3`](https://hexdocs.pm/ecto/Ecto.Schema.html#has_one/3) 和 [`has_many/3`](https://hexdocs.pm/ecto/Ecto.Schema.html#has_many/3) 宏(对于包含其他模型的模型)和 [`belongs_to/3`](https://hexdocs.pm/ecto/Ecto.Schema.html#belongs_to/3) 宏(对于属于其他模型的模型——那些具有外键的模型)来定义。

查看我们的 Ectoing 应用程序，我们可以看到一个`Ectoing.User`模型和`Ectoing.Message`模型之间关联的例子。在`Ectoing.User`中定义的模式定义了以下关联:

```
has_many :messages, Ectoing.Message 
```

我们可以看到一个用户有许多消息(`Ectoing.Message`)，我们称这个关联为`:messages`。

在`Ectoing.Message`模型中，我们定义了以下关联关系:

```
belongs_to :user, Ectoing.User 
```

这里，我们说模型`Ectoing.Message`属于`Ectoing.User`模型。我们也把这个协会命名为`:user`。默认情况下，Ecto 会将一个`_id`附加到`belongs_to`关联名称上，并将其用作外键名(所以在这里是`:user_id`)。通过指定`foreign_key`选项来手动指定外键名，可以覆盖这个默认行为。例如:

```
# Ectoing.Message
belongs_to :user, Ectoing.User, foreign_key: some_other_fk_name 
```

现在让我们来看一个简单的查询，它使用一个连接来获取用户及其消息:

```
SELECT * FROM users u INNER JOIN messages m ON u.id = m.user_id WHERE u.id = 4; 
```

```
# Keywords query syntax
query = from u in Ectoing.User,
  join: m in Ectoing.Message, on: u.id == m.user_id,
  where: u.id == 4

# Macro syntax
query = (Ectoing.User
|> join(:inner, [u], m in Ectoing.Message, u.id == m.user_id)
|> where([u], u.id == 4))

Ectoing.Repo.all query 
```

返回值:

```
[%Ectoing.User{__meta__: #Ecto.Schema.Metadata<:loaded>,
  firstname: "Jane",
  friends_of: #Ecto.Association.NotLoaded<association :friends_of is not loaded>,
  friends_with: #Ecto.Association.NotLoaded<association :friends_with is not loaded>,
  id: 4,
  inserted_at: #Ecto.DateTime<2016-05-15T20:23:58Z>,
  messages: #Ecto.Association.NotLoaded<association :messages is not loaded>,
  surname: "Doe",
  updated_at: #Ecto.DateTime<2016-05-15T20:23:58Z>,
  username: "jane_doe"},
 %Ectoing.User{__meta__: #Ecto.Schema.Metadata<:loaded>,
  firstname: "Jane",
  friends_of: #Ecto.Association.NotLoaded<association :friends_of is not loaded>,
  friends_with: #Ecto.Association.NotLoaded<association :friends_with is not loaded>,
  id: 4,
  inserted_at: #Ecto.DateTime<2016-05-15T20:23:58Z>,
  messages: #Ecto.Association.NotLoaded<association :messages is not loaded>,
  surname: "Doe",
  updated_at: #Ecto.DateTime<2016-05-15T20:23:58Z>,
  username: "jane_doe"}] 
```

值得注意的是，我们有许多卸载的关联，包括`:messages`关联。可以通过两种方式之一来加载这个关联:从查询的结果集或者从查询本身中。可以使用 [`Repo.preload`](https://hexdocs.pm/ecto/Ecto.Repo.html#c:preload/2) 功能从结果集中加载关联:

```
results = Ectoing.Repo.all query
Ectoing.Repo.preload results, :messages 
```

使用`assoc`和`preload`函数的组合可以从查询中加载关联:

```
SELECT * FROM users u INNER JOIN messages m ON u.id = m.user_id WHERE u.id = 4; 
```

```
# Keywords query syntax
query = from u in Ectoing.User,
  join: m in assoc(u, :messages),
  where: u.id == 4,
  preload: [messages: m]

# Macro syntax
query = (Ectoing.User
|> join(:inner, [u], m in assoc(u, :messages))
|> where([u], u.id == 4)
|> preload([u, m], [messages: m]))

Ectoing.Repo.all query 
```

现在，我们在结果中加载了消息关联:

```
[%Ectoing.User{__meta__: #Ecto.Schema.Metadata<:loaded>,
  firstname: "Jane",
  friends_of: #Ecto.Association.NotLoaded<association :friends_of is not loaded>,
  friends_with: #Ecto.Association.NotLoaded<association :friends_with is not loaded>,
  id: 4,
  inserted_at: #Ecto.DateTime<2016-05-15T20:23:58Z>,
  messages: [%Ectoing.Message{__meta__: #Ecto.Schema.Metadata<:loaded>,
    id: 5,
    inserted_at: #Ecto.DateTime<2016-05-15T20:23:58Z>,
    message_body: "Message 5",
    updated_at: #Ecto.DateTime<2016-05-15T20:23:58Z>,
    user: #Ecto.Association.NotLoaded<association :user is not loaded>,
    user_id: 4},
   %Ectoing.Message{__meta__: #Ecto.Schema.Metadata<:loaded>,
    id: 6,
    inserted_at: #Ecto.DateTime<2016-05-15T20:23:58Z>,
    message_body: "Message 6",
    updated_at: #Ecto.DateTime<2016-05-15T20:23:58Z>,
    user: #Ecto.Association.NotLoaded<association :user is not loaded>,
    user_id: 4}],
  surname: "Doe",
  updated_at: #Ecto.DateTime<2016-05-15T20:23:58Z>,
  username: "jane_doe"}] 
```

关联隐式地连接到主键和外键列上，因此我们不必指定一个`:on`子句。从上面我们还可以看到，在预加载关联时，它们并没有被延迟加载。如果需要，必须显式加载关联。

因为本文专门关注 Ecto 的 DSL 查询，所以我们不会在这里讨论关联的插入、更新或删除。有关这方面的更多信息，请查看博客文章[与 Ecto 协会和嵌入](http://blog.plataformatec.com.br/2015/08/working-with-ecto-associations-and-embeds/)合作。

## SQL 片段注入

虽然 Ecto 为我们提供了很多功能，但它只提供了 SQL 中常见操作的函数(它的目标不是模拟整个 SQL 语言)。当我们需要返回到原始 SQL 时，我们可以使用 [`fragment/1`](https://hexdocs.pm/ecto/Ecto.Query.API.html#fragment/1) 函数，使得 SQL 代码能够直接注入到查询中。

例如，让我们对用户名字段执行区分大小写的搜索:

```
SELECT username FROM users WHERE username LIKE BINARY '%doe'; 
```

```
username = "%doe"

# Keywords query syntax
query = from u in Ectoing.User,
  select: u.username,
  where: fragment("? LIKE BINARY ?", u.username, ^username)

# Macro syntax
query = (Ectoing.User
|> select([u], u.username)
|> where([u], fragment("? LIKE BINARY ?", u.username, ^username)))

Ectoing.Repo.all query 
```

(以上包含 MySQL 特有的 SQL。如果您使用的是另一个数据库，那么这不适合您。)

`fragment/1`函数将 SQL 代码作为字符串，我们希望将它作为第一个参数注入。它允许将列和值绑定到 SQL 代码片段。这是通过字符串中的占位符(如问号)完成的，传递给`fragment`的后续参数分别绑定到每个占位符。

## 显式造型

Ecto 使用模型模式定义的另一种方式是将查询中的插值表达式自动转换为模式中定义的相应字段类型。这些插值表达式被转换为与它们进行比较的字段的类型。例如，如果我们有一个像`u.username > ^username`这样的查询片段，其中 u.username 在模式中被定义为`field :username, :string`，那么`username`变量将被 Ecto 自动转换为一个字符串。

然而，有时我们并不总是希望 Ecto 将插值表达式转换为定义的字段类型。其他时候，Ecto 将无法推断表达式的类型(通常，这是在涉及 SQL 代码片段的时候)。在这两种情况下，我们可以使用 [`type/2`](https://hexdocs.pm/ecto/Ecto.Query.API.html#type/2) 函数来指定表达式以及它应该被转换成的类型。

让我们来看看第一种情况，我们想要将一个表达式转换成另一种类型，因为这是更有趣的场景。在我们的 Ectoing 应用程序中，我们使用了 [`Ecto.Schema.timestamps`](https://hexdocs.pm/ecto/Ecto.Schema.html#timestamps/1) 宏向每个表添加两个额外的字段:`updated_at`和`inserted_at`。默认情况下，宏将这些字段的类型设置为类型`Ecto.DateTime`。现在，如果我们想知道本月有多少用户注册，我们可以使用如下的简单查询:

```
Ectoing.Repo.all from u in Ectoing.User,                   
select: count(u.id),                                       
where: u.inserted_at >= ^Ecto.Date.from_erl({2016, 05, 01}) 
```

然而，这会给我们一个`Ecto.CastError`，因为一个`Ecto.Date`结构不能被转换成一个`Ecto.DateTime`结构(因为我们正在比较内插的`Ecto.Date`表达式和一个`Ecto.DateTime`类型的字段)。在这种情况下，我们可以构建一个`Ecto.DateTime`结构，或者我们可以向 Ecto 指定我们希望将表达式转换为`Ecto.Date`而不是`Ecto.DateTime`:

```
Ectoing.Repo.all from u in Ectoing.User,                   
select: count(u.id),                                       
where: u.inserted_at >= type(^Ecto.Date.from_erl({2016, 05, 01}), Ecto.Date) 
```

现在，埃克托愉快地接受了询问。在 cast 操作之后，它将插值的`Ecto.Date`表达式转换为底层的`:date`类型，然后让底层数据库(在本例中是 MySQL)处理日期和日期时间之间的比较。

## 动态字段访问

让我们回到我们一起编写查询的例子，在这里我们执行了用户名搜索:

```
search_by_username = from u in get_users_overview,
  where: like(u.username, ^username) 
```

像它之后的分页查询一样，我们也可以推广这个查询，这样它就可以搜索给定表中的任何字段。这可以通过执行动态字段访问来完成:

```
query = Ectoing.User
query_by_field = :username
username = "%tp%"

# Keywords query syntax
search_by_field = from table in query,
  where: like(field(table, ^query_by_field), ^username)

# Macro syntax
search_by_field = (query
|> where([table], like(field(table, ^query_by_field), ^username)))

Ectoing.Repo.all search_by_field 
```

[`field/2`](https://hexdocs.pm/ecto/Ecto.Query.API.html#field/2) 函数用于需要动态指定字段的时候。它的第一个参数是要访问的字段的表，第二个参数是字段本身的名称，指定为原子。使用类似上面的一般查询，我们可以将其封装在一个函数中，并使用参数从给定查询中指定的表中搜索任何给定的字段。

## 结论

在这篇文章和我的上一篇关于 Ecto 查询 DSL 的文章中，我们已经介绍了它的很多功能。提到的特性应该涵盖了在应用程序中使用 Ecto 时遇到的绝大多数情况。但是还有一些主题没有涉及到(比如查询前缀)。在备受期待的 Ecto 2.0 版本中，还有所有即将到来的新特性，包括子查询、聚合查询和多对多关联。这些以及其他不特定于 Ecto 的查询 DSL 的特性将在以后的文章中介绍——敬请关注！

## 分享这篇文章