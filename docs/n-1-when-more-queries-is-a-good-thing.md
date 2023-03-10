# N + 1:当更多的查询是一件好事

> 原文：<https://www.sitepoint.com/n-1-when-more-queries-is-a-good-thing/>

![](img/73a03cdafbe37c871e406928cfcb8014.png)

在过去的一周里，我一直在试图理解 Rails 中的急切加载是如何通过减少查询次数来消除臭名昭著的 N+1 查询问题的。我最初的假设是尽可能减少查询的数量是目标。然而，我对我的发现感到惊讶。

## 使用`includes`减少查询

你读到的大多数关于臭名昭著的 N + 1 查询问题的帖子都引用了`includes`方法来解决这个问题。`includes`用于通过使用尽可能少的查询次数来急切加载与模型相关的关联。为此，在引擎盖下，它根据情况使用预载或左外连接。我将在后续章节中解释这两种情况。

这在[活动记录查询方法文档](http://apidock.com/rails/ActiveRecord/QueryMethods/includes)中用例子很好地解释了。

## 什么时候怎么用`includes`？

假设我们的用户可以有很多帖子，并且可以对任何帖子发表评论。每个帖子可以有很多评论。基本结构如下面的代码片段所示:

```
# models/users.rb
class User < ApplicationRecord
  has_many :posts
  has_many :comments
end

# models/posts.rb
class Post < ApplicationRecord
  has_many :comments
  belongs_to :user
end

# models/comments.rb
class Comment < ApplicationRecord
  belongs_to :user
  belongs_to :post
end 
```

现在，如果我们想要用户的信息，包括用户发表的帖子以及他们的评论，简单地调用`User.all`将首先加载用户，然后它将获取每个用户的帖子。在获取帖子之后，它将获取用户对这些帖子的评论。如果我们有 10 个用户，每个人有 5 个帖子，平均每个帖子有 2 个评论，那么一个`User.all`将会完成大约 1 + 5 + 10 个查询。

```
# users_controller.rb
def index
  @users = User.all
  render json: @users
end 
```

一个简单的解决方案是使用`includes`告诉活动记录我们想要获取用户和所有相关的帖子:

```
@users = User.all.includes(:posts) 
```

![posts are preloaded, comments are not preloaded](img/12360a50380d298dc452aaa720d6c5f9.png)

帖子是预加载的，评论不是预加载的

这稍微提高了性能，因为它首先获取用户，然后在后续查询中获取与这些用户相关的帖子。现在以前的 1 + 5 + 10 查询减少为 1 + 1 + 10 查询。但是如果与帖子相关的评论也能提前加载，那就更好了。这将把所有数据减少到 1 + 1 + 1，总共 3 次查询来获取所有数据。请看下面的代码片段就明白了:

```
# users_controller.rb
def index
  @users = User.all.includes(:posts => [:comments])
  render json: @users
end 
```

![All data is loaded in just 3 queries, one for users, one for posts, and one for comments related to posts](img/3d661474db4efdbc658a1a873f999b4a.png)

所有数据都在 3 个查询中加载，一个针对用户，一个针对帖子，一个针对与帖子相关的评论

*所有数据都在 3 个查询中加载，一个针对用户，一个针对帖子，一个针对与帖子相关的评论*

在数组中传递注释告诉活动记录也预加载与文章相关的注释。如果某些注释关系需要预加载，我们可以改变传递给`includes`方法的参数，就像这样:

```
User.all.includes(:posts => [:comments => [:another_relationship]]) 
```

这样，可以预加载任意数量的嵌套关系。对于上述所有查询，`includes`使用预加载。

## 获取带有特定标题的帖子

```
User.all.includes(:posts => [:comments]).where('posts.title = ?', some_title) 
```

这将引发一个错误。鉴于，

```
User.all.includes(:posts => [:comments]).where(posts: {    title: some_title }) 
```

会给我们预期的结果。发生这种情况是因为当散列条件通过时，执行用户和帖子的左外连接来获取具有特定标题的帖子的用户。

![Specifying hash conditions on  included relations](img/45d3193e7d1020fb7e4a8e76a2180c6e.png)

在包含关系上指定散列条件

但是，如果我们想使用纯字符串或数组条件而不是散列条件来指定包含关系的条件，该怎么办呢？请看下面的例子:

```
User.all.includes(:posts => [:comments]).where('posts.title = ?', some_title).references(:posts) 
```

注意到`references(:posts)`部分了吗？`references`告诉`includes`用一个*左外连接*强制连接`posts`关系。要理解这一点，请参见上面代码行生成的示例查询:

![Example query when posts relation is force joined with includes through references](img/537a17b949f1ae5c2b0a5092a9bfe919.png)

帖子关系通过引用与 includes 强制连接时的查询示例

我们将查询数量从 1 + 5 + 10 减少到 1 个。太好了！

## 但是，少并不总是多

请看最后两个示例查询。两者都有 3 到 4 行长，并且都有类似于`t0_r1`、`t0_r2`、…、`t2_r5`的子字符串。这似乎不同寻常。我不是 SQL 专家，不知道这是什么意思。这些被称为交叉连接或笛卡尔连接。

因此，使用*引用*或*散列条件*来指定包含关系的条件会导致非常长的查询和不必要的外部连接，这可能会对性能和内存产生负面影响。相反，将一个大的查询拆分成几个查询会更有好处。

[活动记录文档](http://guides.rubyonrails.org/active_record_querying.html#eager-loading-associations)明确说明，当你需要查询关联时，你应该使用带有`includes`的连接查询，而不是`references`。

> 尽管 Active Record 允许您在急切加载的关联上指定条件，就像`joins`一样，但推荐的方式是使用连接。

一种更好的方式来指定关于急切加载的关联的条件:

```
User.all.joins(:posts).where('posts.title = ? ', some_title).includes(:posts => [:comments]) 
```

这将生成 1 + 1 + 1 查询，并只加载具有与给定条件相匹配的帖子的用户，比如特定的标题等。

## 结论

急切加载关联非常有用，可以在很大程度上提高性能，但是当加载许多嵌套关联时，也会造成严重的损害。我个人有点惊讶，减少去数据库的次数实际上会让事情变得更糟。

在[发动机场](https://blog.engineyard.com/2009/thats-not-a-memory-leak-its-bloat)的这个帖子很好地解释了与急切装载关联相关的问题。例如，不要忘记添加分页并限制要获取的记录。

我希望你喜欢这篇关于 N + 1 查询的快速文章。

## 分享这篇文章