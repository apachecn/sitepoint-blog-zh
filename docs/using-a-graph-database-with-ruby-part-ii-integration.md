# 使用 Ruby 的图形数据库，第二部分:集成

> 原文：<https://www.sitepoint.com/using-a-graph-database-with-ruby-part-ii-integration/>

是的，它是真实的，它终于来了！期待已久的[介绍文章](https://www.sitepoint.com/using-a-graph-database-with-ruby-part-i-introduction/)的后续文章终于来了，正好赶上新的一年！

在[第一篇文章](https://www.sitepoint.com/using-a-graph-database-with-ruby-part-i-introduction/)中，我们了解了图形数据库、它们与传统数据库的区别和优势，以及 Neo4j。在这篇文章中，我们将安装 Neo4j，集成并评估本系列第一部分的[中列出的宝石。](https://www.sitepoint.com/using-a-graph-database-with-ruby-part-i-introduction/)

我们将要使用的场景是第一篇文章中简单思想的延续，这是一个社交网络示例，能够产生遍历查询，例如“假设 Bob 是我的朋友，给我所有朋友，他们是 Bob 的朋友的朋友的朋友的朋友”。

## 安装 Neo4j

安装 Neo4j 服务器非常简单。你可以在这里下载独立服务器[，或者使用](http://www.neo4j.org/install)[自制软件](http://mxcl.github.com/homebrew/)安装。对于本文，为了简单起见，我们将使用自制软件:

```
$ brew update && brew install neo4j
```

安装完成后，以下命令可用:

启动服务器:

```
$ neo4j start
```

停止服务器:

```
$ neo4j stop
```

打开 webadmin，它允许您使用 web 界面操作数据:

```
$ open http://localhost:7474/webadmin/
```

## 将 Neo4j 与 Ruby 一起使用

现在服务器已经安装好了，下一步是与 Neo4j 的集成。在[介绍文章](https://www.sitepoint.com/using-a-graph-database-with-ruby-part-i-introduction/)中提到，我们将要评测的宝石有: *Neo4j.rb* 、 *Neoid* 和 *Neography* 。

### Neo4j.rb

什么是 *[Neo4j.rb](https://github.com/andreasronge/neo4j)* ，据作者:

你可以把 Neo4j 看作是一个高性能的图形引擎，它拥有一个成熟而健壮的数据库的所有特性。程序员使用面向对象的、灵活的网络结构，而不是严格的、静态的表——同时享受完全事务性的、企业级数据库的所有好处。

这个宝石真的很广泛，我鼓励你探索它的所有功能。对于这篇文章，我将只关注一个方面:节点和关系。

#### **先决条件**

这个宝石需要 [JRuby](http://jruby.org/) 。如果你正在使用 [RVM](https://rvm.io/) ，你可以很容易地安装它:

```
$ rvm install jruby
```

接下来，通过执行以下命令切换到 JRuby:

```
$ rvm use jruby
```

#### **安装 Neo4j.rb**

您可以将`neo4j`添加到您的 gem 文件:

```
$ gem 'neo4j'
```

运行 Bundler:

```
$ bundle
```

或者手动安装:

```
$ gem install neo4j
```

#### **整合**

在创建节点和关系之前，我们需要让代码成为事务的一部分。这是必需的，因为 Neo4j 有 [ACID](http://en.wikipedia.org/wiki/ACID) 事务。

由于我们的示例相对简单，我们将封装我们的代码以在一个 *Neo4j::Transaction* 块中运行:

```
require 'rubygems'
require 'bundler/setup'
require 'neo4j'

Neo4j::Transaction.run do
  # our code here
end
```

接下来我们要做的是创建几个代表我们用户的节点:

```
require 'rubygems'
require 'bundler/setup'
require 'neo4j'

Neo4j::Transaction.run do
  me   = Neo4j::Node.new(:name => 'Me',   :age => 31)
  bob  = Neo4j::Node.new(:name => 'Bob',  :age => 29)
  mark = Neo4j::Node.new(:name => 'Mark', :age => 34)
  mary = Neo4j::Node.new(:name => 'Mary', :age => 32)
  john = Neo4j::Node.new(:name => 'John', :age => 33)
  andy = Neo4j::Node.new(:name => 'Andy', :age => 31)
end
```

接下来，我们创建用户之间的友谊关系:

```
require 'rubygems'
require 'bundler/setup'
require 'neo4j'

Neo4j::Transaction.run do
  me   = Neo4j::Node.new(:name => 'Me',   :age => 31)
  bob  = Neo4j::Node.new(:name => 'Bob',  :age => 29)
  mark = Neo4j::Node.new(:name => 'Mark', :age => 34)
  mary = Neo4j::Node.new(:name => 'Mary', :age => 32)
  john = Neo4j::Node.new(:name => 'John', :age => 33)
  andy = Neo4j::Node.new(:name => 'Andy', :age => 31)

  me.both(:friends)   << bob
  bob.both(:friends)  << mark
  mark.both(:friends) << mary
  mary.both(:friends) << john
  john.both(:friends) << andy
end
```

用户(节点)之间的友谊关系目前是这样表示的:

![](img/9c8e96dd7bcb4c2e7f3ad50a700fc3ce.png)

这个例子的最后一步是遍历节点，找到从 *Me* 到 *Andy* 的所有友谊关系。

完整的示例如下所示:

```
require 'rubygems'
require 'bundler/setup'
require 'neo4j'

Neo4j::Transaction.run do
  me   = Neo4j::Node.new(:name => 'Me',   :age => 31)
  bob  = Neo4j::Node.new(:name => 'Bob',  :age => 29)
  mark = Neo4j::Node.new(:name => 'Mark', :age => 34)
  mary = Neo4j::Node.new(:name => 'Mary', :age => 32)
  john = Neo4j::Node.new(:name => 'John', :age => 33)
  andy = Neo4j::Node.new(:name => 'Andy', :age => 31)

  me.both(:friends)   << bob
  bob.both(:friends)  << mark
  mark.both(:friends) << mary
  mary.both(:friends) << john
  john.both(:friends) << andy   puts me.outgoing(:friends).depth(5).map{|node| node[:name]}.join(" => ")
end
```

#### **运行示例**

让我们运行我们的例子，看看我们是否得到了我们期望的结果。

```
$ ruby neo4j_example.rb
```

正如所料，我们得到了以下输出:

```
Me => Bob => Mark => Mary => John => Andy
```

### 地理

什么是，据作者:

Neography 是 Neo4j Rest API 的一个瘦 Ruby 包装器

如果您不想在应用程序中使用 JRuby，但是仍然想利用 Neo4j，那么这个 gem 是一个很好的选择。虽然它有其局限性，但作者建议使用 *Neo4j.rb* 来获得 Neo4j 的全部功能。

#### **安装新词**

您可以将`neography`添加到您的 gem 文件:

```
gem 'neography'
```

运行 Bundler:

```
$ bundle
```

或者手动安装:

```
$ gem install neography
```

#### **整合**

让我们从创建几个用户节点开始:

```
me   = Neography::Node.create(name: 'Me',   age: 31)
bob  = Neography::Node.create(name: 'Bob',  age: 29)
mark = Neography::Node.create(name: 'Mark', age: 34)
mary = Neography::Node.create(name: 'Mary', age: 32)
john = Neography::Node.create(name: 'John', age: 33)
andy = Neography::Node.create(name: 'Andy', age: 31)
```

现在，让我们创建节点之间的友谊关系:

```
me.both(:friends) << bob
bob.both(:friends) << mark
mark.both(:friends) << mary
mary.both(:friends) << john
john.both(:friends) << andy
```

视觉上，友谊关系是这样表示的:

![](img/bdcde78f576b31f9d2aed236241f6f54.png)

接下来，我们遍历节点以找到从 *Me* 到 *Andy* 的所有友谊关系:

```
me.all_simple_paths_to(andy).incoming(:friends).depth(5).nodes.each do |node|
  puts node.map{|n| n.name }.join(' => ')
end
```

完整的示例如下所示:

```
require 'rubygems'
require 'neography'

me   = Neography::Node.create(name: 'Me',   age: 31)
bob  = Neography::Node.create(name: 'Bob',  age: 29)
mark = Neography::Node.create(name: 'Mark', age: 34)
mary = Neography::Node.create(name: 'Mary', age: 32)
john = Neography::Node.create(name: 'John', age: 33)
andy = Neography::Node.create(name: 'Andy', age: 31)

me.both(:friends) << bob
bob.both(:friends) << mark
mark.both(:friends) << mary
mary.both(:friends) << john
john.both(:friends) << andy me.all_simple_paths_to(andy).incoming(:friends).depth(5).nodes.each do |node|   puts node.map{|n| n.name }.join(' => ')
end
```

#### **启动服务器**

为了执行完整的示例，我们首先需要初始化 Neo4j 服务器。第一个选项是运行我们之前使用自制软件安装的服务器:

```
$ neo4j start
```

另一个选择是使用 Neography 的 rake 任务来安装/启动/停止/重启服务器。为此，只需在 Rakefile 中添加以下行:

```
require 'neography/tasks'
```

您可以执行以下任务:

```
rake neo4j:install[edition,version]  # Install Neo4j
rake neo4j:reset_yes_i_am_sure       # Reset the Neo4j Server
rake neo4j:restart                   # Restart the Neo4j Server
rake neo4j:start                     # Start the Neo4j Server
rake neo4j:stop                      # Stop the Neo4j Server
```

#### **运行示例**

当我们运行该示例时:

```
$ ruby neography_example.rb
```

我们得到以下输出:

```
Me => Bob => Mark => Mary => John => Andy
```

这显示了从 *Me* 到 *Andy* 的所有遍历节点，正如我们所料。

### Neoid

什么是 [*近地天体*](https://github.com/elado/neoid) ，据作者:

让你的活动记录存储在 Neo4j graph 数据库中，并可在上面搜索，以便进行快速的图形查询，MySQL 会在进行这些查询时进行抓取。Neoid 到 Neo4j 就像太阳黑子到 Solr。您可以获得 Neo4j 速度的好处，同时将您的模式保留在普通的旧 RDBMS 上。

#### **先决条件**

因为 Neoid 使用 ActiveRecord，所以让我们从创建一个简单的 Rails 应用程序开始:

```
$ rails new neoid_example -S -J
```

#### **安装 Neoid**

您可以将`neoid`添加到您的 gem 文件:

```
gem 'neoid', git: 'git://github.com/elado/neoid.git'
```

运行 Bundler:

```
$ bundle
```

#### **启动服务器**

因为 *Neoid* 是由*neograph*驱动的，所以我们有与 neograph 的服务器选项相同的选项。我们可以运行独立的服务器，也可以包含 Neography 提供的 rake 任务:

启动独立服务器:

```
$ neo4j start
```

要使用 Neography 的 rake 任务来安装/启动/停止/重启服务器，只需在 Rakefile 中添加以下行:

```
require 'neography/tasks'
```

您可以执行以下任务:

```
rake neo4j:install[edition,version]  # Install Neo4j
rake neo4j:reset_yes_i_am_sure       # Reset the Neo4j Server
rake neo4j:restart                   # Restart the Neo4j Server
rake neo4j:start                     # Start the Neo4j Server
rake neo4j:stop                      # Stop the Neo4j Server
```

#### **整合**

现在我们有了一个基本的 rails 应用程序，下一步是创建两个模型:*用户*和*友谊*。第一个包含一些基本属性，比如姓名和年龄。后者将保持用户之间的友谊关系。

生成用户模型:

```
$ rails g model user
```

生成友谊模型:

```
$ rails g model friendship
```

接下来，我们需要更新两个模型的迁移:

```
# User migration

class CreateUsers < ActiveRecord::Migration
  def change
    create_table :users do |t|
      t.string :name
      t.string :age
      t.timestamps
    end
  end
end
```

```
# Friendship migration

class CreateFriendships < ActiveRecord::Migration
  def change
    create_table :friendships do |t|
      t.integer :user_id
      t.integer :friend_id
      t.timestamps
    end
  end
end
```

现在，我们需要添加一个包含 Neoid 所需配置的自定义初始化器。作者建议在`config/initializers/01_neo4j.rb`中创建初始化器:

```
require('neography') unless defined?(Neography)

ENV["NEO4J_URL"] ||= "http://localhost:7474"

uri = URI.parse(ENV["NEO4J_URL"])

neo = Neography::Rest.new(uri.to_s)

Neography.configure do |c|
  c.server = uri.host
  c.port = uri.port

  if uri.user && uri.password
    c.authentication = 'basic'
    c.username = uri.user
    c.password = uri.password
  end
end

Neoid.db = neo
```

下一步将更新*用户*模型以包含 *Neoid* 并将其设置为*节点*，并且一个*具有 _many* 关联:

```
class User < ActiveRecord::Base
  include Neoid::Node

  attr_accessible :name, :age

  has_many :friends, class_name: Friendship

  neoidable do |c|
      c.field :name
    end
end
```

现在，我们需要更新我们的*友谊*模型，以包括 *Neoid* ，并将其设置为*关系*，以及几个*属于*协会:

```
class Friendship < ActiveRecord::Base
  include Neoid::Relationship

  attr_accessible :friend

  belongs_to :user
    belongs_to :friend, class_name: User

  neoidable do |c|
    c.relationship start_node: :user, end_node: :friend, type: :friends
  end

  class << self
    def create_both(user, friend)
      user.friends.create(friend: friend)
      friend.friends.create(friend: user)
    end
  end

end
```

对于此设置的最后一步，我们只需运行迁移:

```
$ rake db:migrate
```

现在设置已经完成，我们需要添加一些数据。让我们打开 rails 控制台，添加几个用户和几个朋友。

这里我们创建了六个用户。

```
me   = User.create(name: 'Me',   age: 31)
bob  = User.create(name: 'Bob',  age: 29)
mark = User.create(name: 'Mark', age: 34)
mary = User.create(name: 'Mary', age: 32)
john = User.create(name: 'John', age: 33)
andy = User.create(name: 'Andy', age: 31)
```

接下来，我们需要在用户之间建立友谊关系:

```
Friendship.create_both(me, bob)
Friendship.create_both(bob, mark)
Friendship.create_both(mark, mary)
Friendship.create_both(mary, john)
Friendship.create_both(john, andy)
```

使用 Neo4j 的 web 界面，我们可以看到这些关系是如何可视化表示的:

![](img/c047bfcf4df96cf77dbdb910a7dac35f.png)

#### **运行示例**

有了数据，我们现在需要遍历并找到 *Me* 和 *Andy* 之间的所有朋友。为此，让我们更新我们的*用户*模型，并添加以下实例方法:

```
def all_friends_to(user)
  neo_node.all_simple_paths_to(user.neo_node).incoming(:friends).depth(5).nodes.each do |node|
    puts node.map{|n| n.name }.join(' => ')
  end
end
```

接下来，重新启动 rails 控制台并执行以下命令:

```
me   = User.first
andy = User.last
me.all_friends_to(andy)
=> "Me => Bob => Mark => Mary => John => Andy"
```

正如我们所见，正如我们所料，它显示了从 *Me* 到 *Andy* 的所有遍历节点。

## 结论

本文展示了如何安装 Neo4j，以及如何使用不同的解决方案将其与 Ruby/Rails 应用程序集成的基本思想。尽管这里给出的例子仅仅触及了 Neo4j 的皮毛，但它应该有望给你足够的知识和好奇心来开始将它集成到你自己的项目中。

我希望你喜欢读这篇文章，就像我喜欢写它一样。新年快乐

## 资源

*   [Neo4j](http://www.neo4j.org)
*   [Neo4j.rb](https://github.com/andreasronge/neo4j)
*   [新文字](https://github.com/maxdemarzi/neography)
*   [近地天体](https://github.com/elado/neoid)
*   [自制](http://mxcl.github.com/homebrew/)
*   JRuby
*   [RVM](https://rvm.io/)

## 分享这篇文章