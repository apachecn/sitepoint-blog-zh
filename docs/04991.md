# 通过奖励和游戏化增加参与度

> 原文：<https://www.sitepoint.com/increase-engagement-with-merit-and-gamification/>

游戏化或声誉系统在网络和移动应用中变得非常流行。记录用户的活动有助于网站找出谁应该得到奖励，并鼓励用户做出贡献。像 StackOverflow 和 Foursquare 这样的网站是两个常见的应用程序，它们的成功很大程度上归功于游戏化。这些声誉系统已经在论坛和社区上使用了很长时间，并且在建立高质量的用户参与方面效果很好。

有几个宝石可以帮助无缝地为用户建立游戏化的体验。我认为非常有用、可定制且维护良好的一个法宝是[优点](https://github.com/tute/merit)。

游戏化背后的简单规则是，当用户执行一个动作或实现一个可量化的目标时，就会有奖励。例如:
*当用户评论 10 篇文章时，他们被给予初级评论家徽章
*当用户发表 5 篇文章时，他们被给予初级贡献者徽章

## 设置价值

要开始使用 Merit，请将以下代码添加到您的 gem 文件中。

```
gem 'merit'
```

奔跑

```
bundle install
```

一旦包完成，添加初始化器和迁移。初始化程序提到了我们想要使用的徽章的名字。我们还可以定义计划使用哪个数据存储(PostgreSQL 或 mongoDB)。在迁移中，指定了要“游戏化”的模型。例如，我们将跟踪用户活动，因此我们需要将迁移添加到用户模型中:

```
bundle exec rails g merit:install
=> Creates the initializer in /config/initializer/merit.rb

bundle exec rails g merit model_name (eg: user)
=>  would add has_merit to the user model
```

现在迁移数据库:

```
bundle exec rake db:migrate
```

另一个需要注意的文件夹是 **app/models/merit/*** 。该目录保存了发给用户的徽章的规则定义。在普通的 Ruby 文件中编写规则的能力是使用 Merit 的主要优势之一。使用基于文件的方法使其高度可定制。

让我们创建我们的第一个徽章。

我们计划使用的徽章需要写在初始化文件**config/initializer/merit . Rb**中。创建徽章的方法是`Merit::Badge.create!`。它接受以下参数:

*   `:id`整数(必需)
*   `:name`这是您引用徽章的方式(必填)
*   `:level` (optional)
*   `:description` (optional)
*   `:custom_fields`您希望与徽章相关联的任何其他内容的散列(可选)

示例:

```
Merit::Badge.create!(
  id: 1,
  name: "Jr.Critic",
  description: "Over 10 comments"
)
Merit::Badge.create!(
  id: 2,
  name: "Sr.Critic",
  description: "Over 50 comments"
)
```

一旦创建了徽章，就该定义将该徽章授予用户的规则了。如前所述，授予徽章的规则在 **app/models/merit/*** 文件夹中定义。我们需要创建一个名为 **badge_rules.rb** 的文件。请注意，我们可以提供临时或永久的徽章。当不再符合临时徽章的规则时，该徽章将被撤销。

通过调用`grant_on`方法并提供一个块来定义规则。该方法的参数是:

*   `'controller#action'`–触发程序块的控制器和动作的名称
*   `:badge`–徽章的`:name`
*   `:level`–徽章的`:level`
*   `:to`–持有徽章接收器的对象的名称。在相关的控制器动作中需要一个名为`@model`的变量，比如`posts_controller.rb`的`@post`或者`comments_controller.rb`的`@comment`
    *   可以是一个方法名，其中调用过的目标对象应该检索到该对象的徽章。例如，如果是`:user`，merit 将在内部调用`@model.user`来查找要授予谁徽章
    *   可以是`:itself`，在这种情况下，它标记目标对象本身(`@model`)
    *   默认为`:action_user`，即`current_user`
*   `:model_name`–如果控制器的名称与型号不同，则定义控制器的名称(如`User`型号为`RegistrationsController`)。
*   `:multiple`–徽章是否可以多次授予。`false`默认情况下。
*   `:temporary`–如果条件不再成立，是否应撤销徽章。`false`-徽章永久保存-默认。
*   `&block`–可以是下列之一:
    *   空/未包含:始终授予徽章
    *   一种计算结果为布尔值的块。它接收目标对象作为参数(例如，如果您正在处理一个`PostsController`动作，则为`@post`)。
    *   一个块，其散列由要在目标对象上运行的方法和预期的方法返回值组成

示例:

```
# app/models/merit/badge_rules.rb
grant_on 'comments#create',  badge: 'Jr.Critic', to: :user do |comment|
  comment.user.comments.count >= 10 &&  comment.user.comments.count < 50
end
grant_on 'comments#create',  badge: 'Sr.Critic', to: :user do |comment|
  comment.user.comments.count >= 50
end
```

从上面的代码可以清楚地看出，只有当用户的评论数大于 10 且小于 50 时，初级评论家徽章才有效。当他的评论数超过 50 时，将会颁发一个新的徽章，初级徽章将被取消。

要访问当前用户的徽章(假设您登录的用户称为`current_user`):

```
current_user.badges
```

如果您想要手动添加/删除徽章，请使用:

```
current_user.add_badge(badge.id)
current_user.rm_badge(badge.id)
```

上述情况的一个用例是当你想给付费客户一个“高级用户”徽章时。不用编写规则并多次检查，只需从支付控制器和操作中添加即可。

## 点字法

从这里开始，我们可以建立一个很酷的声誉系统，但如果没有显示点，它就不完整。积分的计算类似于徽章系统。在名为(你猜对了)**app/models/merit/point _ rules . Rb**的文件中定义积分规则。

使用名为`score`的方法配置点数，该方法接受与徽章系统相同的参数。

例如，如果您要求用户在评论一篇文章时增加一分:

```
score 1, on: 'comments#create', to: [:user]
 score 5, on: 'comments#vote_up', to: [:comment_owner]
```

`:user`是指向用户的模型(评论)的属性。在`vote_up`的情况下，评论的所有者被`Vote`模型中的属性引用为`comment_owner`。

您可以手动添加点数，就像徽章一样:

```
current_user.add_points(20, category: 'Optional category')
current_user.subtract_points(10, category: 'Optional category')
```

要查找给定日期以来获得的积分，例如排行榜，请使用:

```
score_points = current_user.score_points(category: 'Optional category')
score_points.where("created_at > '#{1.month.ago}'").sum(:num_points)
```

## 分级制

与积分和徽章类似，排名在**app/models/merit/rank _ rules . Rb**中定义。

排名规则是用`set_rank`方法创建的。这些参数如下:

*   `:level`–等级(越大越好，按字典顺序)
*   `:to`–检查新排名是否适用的模型或范围
*   `:level_name`–属性名称(默认为空，产生“级别”属性，如果设置，则附加“级别 _ # {级别名称}”)

示例:

```
set_rank level: 2, to: User.active do |user|
  user.comments > 10 && user.followers >= 10
end
```

这是更多的功能与优点。使用类似 ActiveRecord Observer 的东西，可以很容易地跟踪获得的徽章/积分，并为用户创建所需的通知。尝试一下 gem，让游戏化，从而让你的 web 应用程序有更多的参与度。

## 分享这篇文章