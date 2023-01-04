# 基于 Redis 的活动提要

> 原文：<https://www.sitepoint.com/activity-feeds-based-on-redis/>

今天，活动源是几乎所有网络应用的重要组成部分。基于 SaaS 的应用、社交应用等都需要用户时间表。如果管理不当，这些活动提要和用户时间表很容易失控，尤其是在大量用户定期登录的情况下。
在这种情况下，Redis 是实现易用性、操作速度、可扩展性和数据到期选项(如果在特定时间段后不再需要)的良好选择。

在我的方法中，应用程序的默认数据库是 MySQL，我在应用程序中为活动提要单独运行 Redis。这个架构决策是有争议的，因为它确实增加了管理两个独立数据存储的工作量。但是，它确实为应用程序的性能提供了显著的提升。

让我们快速浏览一下在我们的应用程序中设置 Redis。将`redis`宝石添加到我们的宝石文件并运行`bundle`。

```
gem 'redis', :git => 'https://github.com/redis/redis-rb.git'

bundle install
```

然后在我们的*config/initializer*中，创建一个名为 *redis.rb* 的文件，并初始化与 redis 服务器的连接:

```
$redis = Redis.new(:host => 'localhost', :port => 6379)
```

让我们加载 rails 控制台，看看 redis 连接是否工作:

```
$ rails c
Loading development environment (Rails 4.0.0.rc1)
1.9.3-p327 :001 > $redis
 => #<Redis client v3.0.4 for redis://localhost:6379/0>
1.9.3-p327 :002 >
```

非常好用！

这是我们的应用程序的背景:该应用程序允许人们创建并与他人分享食物。现在，一个系统时间表将提供他们所关注的人关于创建和更新膳食的活动。

我们将使用 [redis 时间表](https://github.com/felixclack/redis-timeline) gem 来创建我们基于 redis 的时间表。在本文的后面，我们将解构这个 gem 的功能。gem 使用一个`after_create`回调作为事件来跟踪特定的活动。这也为我们提供了必要的 id。

当前版本的 gem 支持 Rails 3.2(由于回调依赖于 ActiveSupport 和 ActiveModel 3.2。)然而，我只是分叉并更新了 gemspec 依赖项，以与 Rails 4.0.0.rc1 [一起工作。您可以在您的 Gemfile 中使用我的 fork，如下所示:](https://github.com/saurabhbhatia/redis-timeline)

```
gem 'redis_timeline', :git => 'https://github.com/saurabhbhatia/redis-timeline.git'</p>

<p>bundle install
```

redis_timeline gem 要求我们首先设置一个 actor。它通常连接到我们的用户类，因为参与者负责一个动作，而用户执行应用程序中的所有动作。

```
class User < ActiveRecord::Base
  include Timeline::Actor
  has_many :meals
end
```

在设置了 actor 之后，我们需要设置要跟踪的类。在我们的例子中，我们需要跟踪膳食类`create`和`edit`方法。让我们看看如何做到这一点。

我们首先设置一个`include Timeline::Track`方法来设置对特定模型的跟踪。现在你可以定义你想要“跟踪”的方法和你想要它“跟踪”的回调。

```
class Meal < ActiveRecord::Base
 include Timeline::Track
 belongs_to :user

  track :new_meal,
    on: :create,
    actor: :user

  track :edit_meal,
    on: :update,
    actor: :user
end
```

让我们加载 rails 控制台，看看这是否可行。

```
rails c
Loading development environment (Rails 4.0.0.rc1)
1.9.3-p327 :001 > meal = Meal.create(:title => 'Nachos', :description => 'Cheesy & Corny' , :user_id => 1)
 => #<Meal id: 5, title: "Nachos", description: "Cheesy & Corny", created_at: "2013-06-23 09:13:30", updated_at: "2013-06-23 09:13:30", user_id: 1>
```

为了在时间轴上进行调用，我们需要获取参与者(在我们的例子中是用户)并调用`timeline`。

```
1.9.3-p327 :002 > user = User.find(1)
1.9.3-p327 :003 > user.timeline
 => [#<Timeline::Activity actor=#<Timeline::Activity class="User" display_name="#<User:0x000000049b2900>" id=1> created_at="2013-06-23T17:13:31+08:00" object=#<Timeline::Activity class="Meal" display_name="#<Meal:0x000000051b50f8>" id=5> target=nil verb="new_meal">]
```

时间轴对象包含类名、餐名、用户对象和一个动词。动词基本上表示被跟踪的动作。

这个还是少了点什么。我们需要向用户的追随者推送我们的更新。我们的 follow 机制也是用 Redis 构建的，看起来像这样。为了方便起见，我在这里使用了这篇文章来创建这个机制，并在我的用户模型中定义了它。

```
def follow!(user)
  $redis.multi do
    $redis.sadd(self.redis_key(:following), user.id)
    $redis.sadd(user.redis_key(:followers), self.id)
  end
end

def unfollow!(user)
  $redis.multi do
    $redis.srem(self.redis_key(:following), user.id)
    $redis.srem(user.redis_key(:followers), self.id)
  end
end

def followers
  user_ids = $redis.smembers(self.redis_key(:followers))
  User.where(:id => user_ids)
end

def following
  user_ids = $redis.smembers(self.redis_key(:following))
  User.where(:id => user_ids)
end
```

为了将用户的更新发送给追随者，我们将使用“followers”键，并按照下面的代码片段进行配置。gem 包括一个名为“followers”的参数，在这里我们可以发送存储在 Redis 数据库中的 followers 密钥。

```
track :new_post,
  on: :create,
  actor: :user,
  followers: :followers

track :edit_post,
  on: :update,
  actor: :user,
  followers: :followers
```

为了显示您的提要，您可以调用“current_user”方法并在您的控制器中调用提要。(注意:这假设您正在使用类似[device](https://github . com/plataformatec/device)的工具，它提供了一个“current_user”助手。)

```
def index
 @feed = current_user.timeline
end
```

### 在后台

gem 使用一种“写时扇出”模型来调用 Redis 存储。它使用 [`lpush`](http://redis.io/commands/lpush) 来实现这一点。提要存储为一个散列列表。它首先使用 setters 创建所有要跟踪的项目和 id 的散列。然后使用`lpush`将它们发送到一个列表中。您可以在[赛道课程](https://github.com/felixclack/redis-timeline/blob/master/lib/timeline/track.rb)文档中查看更多详细信息。

```
def redis_add(list, activity_item)
  Timeline.redis.lpush list, Timeline.encode(activity_item)
end
```

为了读写列表，它使用了 JSON 编码器和解码器。这是因为 redis 将其所有对象都视为字符串，哈希需要序列化为字符串才能存储。这是在[助手类](https://github.com/felixclack/redis-timeline/blob/master/lib/timeline/helpers.rb)中定义的。

helper 类中还定义了使用`lrange`读取列表的方法。

```
def get_list(options={})
  Timeline.redis.lrange options[:list_name], options[:start], options[:end]
end
```

### 一些技巧

还可以做其他事情来优化列表:

1.  修剪列表:使用列表作为数据结构的一个好处是你可以修剪它。例如，如果您想要一个通用的系统时间表，并且想要显示最近的 100 个对象，您可以简单地将列表调整到这个时间表。

    ```
    LTRIM <user timeline list key> 0 99
    ```

2.  终止列表:如果你觉得维护系统时间表的永久存储开销太大，你可以在给定的时间间隔内终止它。时间以秒为单位定义。

    ```
    EXPIRE <user timeline list key> 3600
    ```

### 结论

Redis 是开发和管理活动提要并将它们从系统中分离出来的有效方法。这有助于扩展主应用程序，因为它可以将一些不必要的写入保留在主数据库之外。现在，去创建那些活动源吧！

## 分享这篇文章