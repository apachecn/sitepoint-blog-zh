# 用 Ruby on Rails 和 GraphQL 构建 API

> 原文：<https://www.sitepoint.com/building-apis-ruby-rails-graphql/>

![Building APIs with Ruby on Rails and GraphQL](img/5add0b921719954d90db7bbe8d4dc90b.png)

如今，为移动和网络应用程序设计 API 已经成为一个非常普遍的问题。自从十年前智能手机的爆发(以及随之而来的移动应用的爆发)，REST APIs 已经成为应用服务器和客户端之间交换数据的主要标准。

开发 API 时遇到的最大问题之一是后端返回给客户的数据的结构和粒度。假设你正在建立一个类似 Twitter 的社交网络，用户可以关注其他用户。在设计 API 时，您可能希望添加一个端点(`GET /users/123`)来检索特定用户的数据。服务器应该在 API 响应中发送关于这个用户的追随者的数据吗？或者它应该保持非常轻的响应，因为你需要的只是关于这一个用户的基本数据？如果你的客户需要这个用户的全部数据，但是只需要他的关注者的用户名和个人资料图片怎么办？

现在，您可能正在考虑在调用端点时使用查询参数做一些小技巧，类似于`GET /users/123?full=true&with_followers=true&light_followers=true`。然而，我敢打赌，您很容易理解这种方法是如何成为您和所有其他需要使用您的 API 的工程师头疼的一大原因。

现在想象一下，你的应用程序像 GitHub 或脸书一样复杂，用户、帖子、关系等之间有交错的数据。头痛现在变成了一场噩梦。

输入 GraphQL ，这是几年前脸书在将他们的主应用程序迁移到本地应用程序时创建的一种查询语言。脸书是非常复杂的 T2 数据架构的一个很好的例子。这就是为什么他们设计了一种更好的方法来处理数据:

> 让客户机向服务器请求它需要的数据。

回到我们之前的例子，假设我们的移动应用程序想要请求某个特定用户的一些数据，但只需要他的前 20 个关注者的基本信息。发送到服务器的 GraphQL 查询如下所示:

```
{
  user(id: 123) {
    id
    username,
    email,
    bio,
    profile_picture,
    followers(size: 20) {
      id
      username,
      profile_picture
    }
  }
} 
```

实现 GraphQL 协议的 API 将使用以下 JSON 数据进行响应:

```
{
  "user": {
    "id": 123,
    "username": "foo",
    "email": "foo@myapp.com",
    "bio": "I'm just a sample user, nothing much to say."
    "profile_picture": "https://mycdn.com/somepicture.jpg",
    "followers": [
        {
        "id": 134,
        "username": "bar",
        "profile_picture": "https://mycdn.com/someotherpicture.jpg"
      },
      {
        "id": 153,
        "username": "baz",
        "profile_picture": "https://mycdn.com/anotherpicture.jpg"
      },

      // and another 18 followers at maximum
    ]
  }
} 
```

在本教程中，我们将看到如何使用 GraphQL 语言和 Ruby on Rails 实现一个简单的电影数据库 API。

## 创建项目

我们将使用`--api`选项创建 Rails 项目，以使用全栈框架的轻量级版本，仅包含构建 REST API 所需的内容。

```
$ gem install rails
$ rails new graphql-tutorial --api
$ cd graphql-tutorial/ 
```

现在将`graphql`宝石添加到您的宝石文件中:

```
gem 'graphql' 
```

并执行`$ bundle install`安装 gem。我们现在可以用`$ rails server`启动应用服务器。

我们需要生成两个模型，一个代表电影，另一个代表电影演员:

```
$ rails g model Movie title:string summary:string year:integer
$ rails g model Actor name:string bio:string 
```

我们将在两个模型之间实现多对多关系，因此我们必须为连接表生成一个迁移:

```
$ rails g migration CreateActorsMovies 
```

然后更新模型:

```
# app/models/movie.rb

class Movie < ActiveRecord::Base
  has_and_belongs_to_many :actors
end

# app/models/actor.rb

class Actor < ActiveRecord::Base
  has_and_belongs_to_many :movies
end 
```

我们现在有一个非常简单的 Rails 应用程序设置，有两个模型。现在让我们构建实现 GraphQL 模式的 API。

## 构建 GraphQL 模式

在深入代码之前，让我们再多谈一点 GraphQL 规范。我们将从分析下面的查询开始，我们将为我们的应用程序实现这个查询:

```
{
  movie(id: 12) {
    title
    year
    actors(size: 6) {
      name
    }
  }
} 
```

让我们把它分成以下几个部分:

*   在第一个开始和最后一个结束括号内是我们的 GraphQL 查询的主体，也称为**根对象**或**查询对象**。这个对象有一个单独的字段`movie`和一个单独的参数`id`。API 负责返回带有指定`id`的电影对象。
*   在这个字段`movie`中，我们需要标量字段`title`和`year`以及集合字段`actors`。对于最后一个，我们给出了一个`size`参数，指定我们只想要与这部电影相关的前 6 个演员。
*   最后，我们为保存在`actors`字段中的集合的每个参与者请求单个字段`name`。

现在我们已经简要了解了 GraphQL 语言的可能性，我们可以通过定义根查询对象来开始实现:

```
# app/types/query_type.rb

QueryType = GraphQL::ObjectType.define do
  name "Query"
  description "The query root for this schema"

  field :movie do
    type MovieType
    argument :id, !types.ID
    resolve -> (obj, args, ctx) {
      Movie.find(args[:id])
    }
  end

  field :actor do
    type ActorType
    argument :id, !types.ID
    resolve -> (obj, args, ctx) {
      Actor.find(args[:id])
    }
  end
end 
```

根对象可以有两个直接的子类型，这是我们在应用程序中定义的两个模型，`movie`和`actor`。为每个字段定义指定其类型，稍后将在它自己的类中定义。我们还指定了该字段可以接受的参数——只有具有特殊类型`ID!`的`id`(`!`表示该参数是必需的)。

最后，我们提供了一个`resolve`函数，在这里我们获取查询中给出的 ID 值，并从数据库返回相关的模型。注意，在我们的例子中，我们唯一做的事情是调用活动记录方法`Actor::find`，但是我们可以自由地实现任何我们想要的东西，只要我们返回被请求的数据。

**不要忘记将包含我们的类型定义的目录添加到自动加载路径列表中:**

```
# config/application.rb

config.autoload_paths < < Rails.root.join("app", "types") 
```

我们仍然需要定义类型来处理`movie`和`actor`字段。下面是定义`MovieType`的代码:

```
 # app/types/movie_type.rb

MovieType = GraphQL::ObjectType.define do
 name "Movie"
  description "A Movie"
  field :id, types.ID
  field :title, types.String
  field :summary, types.String
  field :year, types.Int
  field :actors do
    type types[ActorType]
    argument :size, types.Int, default_value: 10
    resolve -> (movie, args, ctx) {
      movie.actors.limit(args[:size])
    }
  end
end 
```

除了标量字段`title`、`summary`和`year`之外，该定义与之前的定义相似。我们不需要提供一个`resolve`方法，因为这个库会通过它们的名字来推断模型字段。`actors`字段类型是`[ActorType]`，它是类型为`ActorType`的对象的集合。

我们还指出可以给字段`actors`一个可选的`size`参数。定义`resolve`方法允许我们使用活动记录 API 来处理它的值，以限制集合的大小。

下面的`ActorType`的定义是以非常相似的方式完成的:

```
# app/types/actor_type.rb

ActorType = GraphQL::ObjectType.define do
  name "Actor"
  description "An Actor"
  field :id, types.ID
  field :name, types.String
  field :bio, types.String
  field :movies do
    type types[MovieType]
    argument :size, types.Int, default_value: 10
    resolve -> (actor, args, ctx) {
      actor.movies.limit(args[:size])
    }
  end
end 
```

最后，我们可以创建包含根查询对象作为其查询成员的模式:

```
# app/types/schema.rb

Schema = GraphQL::Schema.define do
  query QueryType
end 
```

我们现在可以使用它作为 API 控制器内部查询的入口点:

```
# app/controllers/movies_controller.rb

class MoviesController < ApplicationController
  # GET /movies
  def query
    result = Schema.execute params[:query]
    render json: result
  end
end 
```

我们现在可以在 API 中使用 GraphQL 查询了！我们来补充一些数据…

```
$ bundle exec rails c

> movie = Movie.create!(title: "Indiana Jones", year: 1981, summary: "Raiders of the Lost Ark")
> actor = Actor.create!(name: "Harrison Ford", bio: "Some long biography about this actor")
> movie.actors < < actor 
```

…并使用任何 HTTP 客户端尝试端点:

```
curl -XGET http://localhost:3000/movies -d "query={
  movie(id: 1) {
    title,
    year,
    actors {
      name
    }
  }
}" 
```

## 更进一步

对于那些想继续学习如何在 Rails 中使用 GraphQL 的人，这里有一些有趣的事情可以尝试:

*   现在，我们只能通过 ID 查询特定的电影，因为我们定义了单数的`movie`字段。如果我们想要 1993 年上映的电影列表呢？我们可以将另一个字段`movies`添加到我们的根`Query`中，采用过滤参数，如`year`来过滤并返回记录列表:

    ```
    ```ruby
    # app/types/query_type.rb

    field :movies do
      type types[MovieType]
      argument :year, types.Int
      resolve -> (obj, args, ctx) {
        if args[:year].present?
          Movie.where(year: args[:year])
        else
          Movie.all
        end
      }
    end
    ``` 
    ```

*   我们可以允许客户端指定返回列表`actors`或`movies`的顺序，以防我们开始有很多记录。这也可以通过使用可选参数并在`resolve`函数中处理它们来实现。

*   在某些时候，我们可能希望限制客户端对某些对象或字段的访问。例如，我们可以让用户通过在客户端请求中发送的一些令牌进行身份验证。然后，我们将根据该用户权限检查对记录或字段的访问。GraphQL gem 也为[提供了一些处理这个](https://rmosolgo.github.io/graphql-ruby/queries/authorization)的方法。

还有很多东西有待探索。你可以查看 gem 的[文档](https://rmosolgo.github.io/graphql-ruby/)，以及 GraphQL 的[官方规范](http://graphql.org/learn/)来深入了解它的细节。

我希望您喜欢发现 GraphQL 提供的可能性，并希望您在未来的项目中尝试一下！

* * *

**在 SitePoint 上的进一步阅读**

*   本书:[铁轨深潜](https://www.sitepoint.com/premium/books/rails-deep-dive)
*   截屏:[测试你的 Rails 应用中的视图、路线和助手问题](https://www.sitepoint.com/premium/screencasts/testing-views-routes-and-helpers-for-problems-in-your-rails-app)
*   截屏:[用 RSpec 测试你的 Rails 应用模型](https://www.sitepoint.com/premium/screencasts/testing-your-rails-app-models-with-rspec)
*   截屏:[用夹具和工厂管理样本数据](https://www.sitepoint.com/premium/screencasts/managing-sample-data-with-fixtures-and-factories)
*   截屏:[用 RSpec 设置自动化测试](https://www.sitepoint.com/premium/screencasts/setting-up-automated-testing-with-rspec)

## 分享这篇文章