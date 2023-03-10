# Ruby 中的 RethinkDB

> 原文：<https://www.sitepoint.com/rethinkdb-ruby/>

![rethinkdb](img/90ed17d3ea0b85a53860d4b8709f8ba1.png)

你可能听说过一些 NoSQL 数据库:Redis，MongoDB，Cassandra 等等。，所有这些都提供了各种不同的优势，同时也带来了一系列的缺点。如果您正在构建一个新的应用程序，选择一个满足您需求的数据库是非常重要的。在本文中，我们将介绍 RethinkDB，看看如何设置 RethinkDB，它如何适应 NoSQL 的环境，以及如何在 Ruby 中使用它。

## 为什么要重新思考 DB？

RethinkDB 是一个 JSON 文档存储。这意味着它允许您存储大量 JSON 文档，然后在以后查询它们。原来 RethinkDB 不是唯一可用的文档存储。差远了。甚至在 RethinkDB 项目开始的时候，对于寻找 NoSQL 数据库的人来说，已经有了大量的选择。那么，学习另一个数据库有什么意义呢？简而言之，RethinkDB 结合了其竞争对手的一些最佳特性，并呈现出一组非常引人注目的优势。

它的近亲之一是 MongoDB。Mongo 迅速流行起来，因为它关注开发人员的生产力:在 Mongo 中创建文档和查询文档非常容易。不幸的是，Mongo 在[缩放](http://patrickmcfadin.com/2014/02/11/mongodb-this-is-not-the-database-you-are-looking-for/)方面有相当严重的[问题](http://diegobasch.com/ill-give-mongodb-another-try-in-ten-years)。诚然，其中一些问题是由糟糕的部署实践引起的，但并不是所有提出的问题都是没有根据的。但是，Mongo 确实提高了开发人员的工作效率。另一方面，我们有像 Riak 这样的数据库，为其编写代码并不那么有趣，但通常无需开发运营团队付出大量努力就可以很好地扩展。RethinkDB 试图处于这种“权衡”的中间(似乎没有真正的理由来权衡):它试图提供类似 Mongo 的易用性，同时仍然让开发人员满意。

## 入门指南

好吧，让我们给自己弄一份 RethinkDB 的拷贝，这样我们就可以开始使用它了。幸运的是，他们有一个漂亮的[安装页面](http://rethinkdb.com/docs/install/)，实际上告诉我们该怎么做。请注意，Windows 目前不被官方支持；需要一个类似*nix 的系统(Mac OS X 就可以)。如果你在 OS X，家酿啤酒是个不错的选择:

```
brew update && brew install rethinkdb
```

对于流行的服务器 Linux 发行版(例如 Ubuntu、CentOS、Debian)，RethinkDB 提供了很好的二进制文件。如果您不属于任何常见的类别，那么您可以从源代码构建，您可以获得以下内容的副本:

```
wget http://download.rethinkdb.com/dist/rethinkdb-1.16.2-1.tgz
```

要检查 rethinkdb 是否安装正确并使其运行，请执行以下操作:

```
rethinkdb
```

要查看(非常漂亮的)管理界面，请在浏览器中点击`127.0.0.1:8080`。从这里，您可以查看“集群”的当前状态(当前由一台服务器组成)。我们也可以通过前往[“数据浏览器”](http://127.0.0.1:8080/#dataexplorer)选项卡来启动几个查询。

![data_explorer](img/663602130851cf9af8ed8833cf2de4fe.png)

查询编辑器*真的*滑头；尝试键入:

```
r.dbCreate('testdb')
r.db('testdb').tableCreate('test_table')
```

这将在“test db”中创建一个名为“testdb”的数据库和一个名为“test *table”的表。ReQL，RethinkDB 的查询语言，并不是一种你必须学习的特殊语言，也不是一组你必须放在一起的特殊 JSON 对象。相反，它由来自您选择的语言的函数调用组成。目前，Ruby、Python 和 Node 都有官方驱动。让我们来看看如何使用 Ruby 驱动程序。*

## Ruby 驱动程序

首先，我们需要得到宝石:

```
gem install rethinkdb
```

让我们在前面创建的“testdb”数据库中制作另一个表:

```
require 'rethinkdb'
include RethinkDB::Shortcuts

conn = r.connect(:host => "localhost", :port => 28015)
r.db("testdb").table_create("people").run(conn)
```

运行这个应该会创建我们想要的表。让我们分解一些重要的行:

```
include RethinkDB::Shortcuts
```

这一行让我们使用`r`来调用 RethinkDB 查询函数。只是让编写查询代码变得更好一点。如果不想使用快捷方式，可以创建自己的 RethinkDB 引用:

```
r = RQL.new
```

接下来，我们通过调用`r.connect`建立一个连接:

```
conn = r.connect(:host => "localhost", :port => 28015)
```

下面是实际的查询:

```
r.db("testdb").table_create("people").run(conn)
```

这一行代表了 RethinkDB 查询 DSL 的一个非常重要的部分。这些调用通常可以被链接起来，因为它们都返回 RQL 对象(如果你对 Javascript 有一些经验，你可能会喜欢这个想法)。使用`.db("testdb")`，我们选择数据库。用`.table_create("people")`链接起来为我们创建了正确的表。但是，在我们按下`.run(conn)`之前，这些都不会真正发生，它会针对我们之前创建的连接对象所指向的 RethinkDB 实例运行查询。

好的，让我们假设您已经运行了这个查询，并且已经创建了表。我们如何证实这一点？我们不想仅仅为了列出数据库中的表而创建另一个文件，所以我们将使用 Ruby REPL ( `pry`或`irb`，您自己选择)。我们首先要建立一个连接:

```
r.connect(:host => "localhost", :port => 28015).repl
```

注意到我们在最后加上的那个`.repl`了吗？这样，RQL 就会认为您将使用此连接作为默认连接。这使得与 REPL 打交道容易多了。让我们检查一个查询，该查询将获取“testdb”中的表列表:

```
r.db('testdb').table_list.run
```

这应该会返回如下内容:

```
["people", "test_table"]
```

好吧，酷。但是等等。我们刚刚运行的示例中缺少了很多内容。如果事情出了差错怎么办？

## 异常处理

当处理数据库时，事情总是出错。我们总是希望确保我们的代码能够承受，或者至少能够响应在处理 RethinkDB 时出现的问题。让我们以稍微不同的方式重写我们先前的例子:

```
require 'rethinkdb'
include RethinkDB::Shortcuts

table = "people"
db = "testdb"

begin
    conn = r.connect(:host => "localhost", :port => 28015)
rescue Exception => err
    puts "Cannot connect to RethinkDB at localhost:28015"
end

begin
    r.db(db).table_create(table).run(conn)
rescue RethinkDB::RqlRuntimeError => err
    puts "Error occurred in creating #{table} table within #{db}"
    puts err
end

begin
    puts "Tables inside #{db}: ", r.db(db).table_list().run(conn)
rescue RethinkDB::RqlRuntimeError => err
    puts err
ensure
    conn.close
end
```

基本上，我们已经将所有东西包装在异常中，并且还添加了一个`ensure`子句，这样我们就可以关闭到 RethinkDB 的连接。您可以像看待任何其他异常一样看待 RethinkDB 异常，只是它们通常非常重要。因此，如果您选择忽略(即不拯救)给定的异常，请完全理解其后果。

## 文档

好的，我们如何把东西放到我们的桌子里？您可能还记得，RethinkDB 是一个文档存储，这意味着它存储 JSON 对象。让我们将一个人放入我们的`people`表中(我将呈现 REPL 表单，但是将它移入代码只需要将连接对象传入`run`):

```
res = r.db("testdb").table("people").insert({:name => "Dhaivat"}).run
```

它应该会返回类似这样的内容:

```
{"deleted"=>0, "errors"=>0, "generated_keys"=>["417b5b3e-c107-4577-acfc-cd11a76f9174"], "inserted"=>1, "replaced"=>0, "skipped"=>0, "unchanged"=>0}
```

请注意，这是一个 Ruby 散列，因此我们如何从该结构中获取值是非常清楚的(例如，`res['generated_keys']`给出了 insert 生成的主键列表)。这个结构有几个重要的部分。首先，`generated_keys`是一个主键列表(在本例中，这个列表只包含一个元素)，您可以在以后使用它从表中查找相同的文档。其次，`inserted`的正值告诉我们在查询中插入了一个元素。要用一个查询插入多个文档，只需将它们放在一个列表中并传递给`.insert`。这里最重要的一点是，RethinkDB 根本不关心文档的结构。与 MySQL(和其他 RDMBS)中指定表结构不同，RethinkDB 允许您在每次将数据放入数据库时即兴使用。这意味着要么你的数据库交互代码必须非常小心，要么你必须确保不要让旧的结构停留太久。作为练习，为“John”、“Jane”和“George”添加文档。

插入文档非常容易。再把它们弄出来怎么样？我们可以得到一个指向表中某一点的迭代器。如果我们想遍历表中的所有对象:

```
itr = r.db('testdb').table('people').run
itr.each do |document|
    p document
end
```

如果我们想根据某种约束条件进行过滤呢？我们所要做的就是构建一个指定过滤器的查询:

```
itr = r.db('testdb').table('people').filter {|person| person['name'].eq("John")}.run
itr.each do |document|
    p document
end
```

这里，我们使用`filter {|person| person['name'].eq("John")}`来指定一个布尔条件。注意，我们用`.eq`做等式比较，因为 RQL 不喜欢我们使用`==`。基本上，我们可以编造的任何布尔条件都可以用作过滤器的一部分。我们可以用类似的方式删除文档:

```
r.db('testdb').table('people').
    filter {|person| person['name'].eq("John")}.
    delete.run
```

我们可以根据`id` *很容易地查询出真正的*:

```
itr = r.db('testdb').table('people')
itr.get(id)
```

更新以类似的方式工作:

```
itr.update({name: "Dhaivat Pandya"}).run
```

用`itr`可迭代的任何文档都将被更新，因此名称字段被设置为`"Dhaivat Pandya"`。

## 关于 RQL 的一个笔记

如果您习惯于 SQL，您可能会想，如果我们已经先拉回了整个表，那么过滤还有什么意义。我们没有。出现在`.run`之前的所有内容都被放在一个查询中，发送到服务器，服务器只返回您请求的内容。长话短说，所有查询都在服务器上运行。我在 RQL 的经历相当愉快；查询链接、简单过滤等。让 RQL 的使用变得愉快。

## 包装它

本文应该是对 RethinkDB 的一个快速介绍。但是，还有大量的东西没有触及，例如表连接、map-reduce 等。在以后的文章中，我们将深入研究所有这些令人兴奋的话题。

请在下面的评论中回答任何问题。

## 分享这篇文章