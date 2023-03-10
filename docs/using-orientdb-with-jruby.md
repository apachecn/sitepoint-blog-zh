# 使用面向 jruby

> 原文：<https://www.sitepoint.com/using-orientdb-with-jruby/>

[![ruby_orient_sun_logo](img/ac941f8509b89a8ee4b7b5599dfb765f.png)](https://www.sitepoint.com/wp-content/uploads/2013/09/ruby_orient_sun_logo.png)

这是我关于 OrientDB 和 Ruby 系列的第二篇文章。第一篇文章并没有真正涉及任何 Rubyish 的东西，这就是这篇文章要解决的问题。

当我找到 OrientDB 时，我找到了[语言绑定](https://github.com/orientechnologies/orientdb/wiki/Programming-Language-Bindings)页面，并被该表中的 3 个 Ruby 条目所鼓舞:OrientDB-JRuby、 [OrientDB 客户端](https://github.com/ryanfields/orient_db_client)和 [OrientDB4R](https://github.com/veny/orientdb4r) 。当时，它们最近都没有更新。

OrientDB4R 后来更新到 OrientDB 1.5，但是只使用 REST API。出于性能原因，我们需要一个 JRuby 解决方案，所以 REST API 并不是一个真正的选择。

OrientDB 客户端使用 OrientDB 1.0.0.rc2，一年没有更新了。

OrientDB-JRuby(Adrian Madrid)发布于 OrientDB 1.3.0 (OrientDB 目前即将发布 1.6.0)，已经几个月没有更新了。它是 OrientDB Java API 周围的一个简单的 JRuby 包装器。如果你从未使用 JRuby 包装过 Java 库(我没有)，那么它就像打开罐子一样简单！，可以从 Ruby 调用 Javaland 中的方法。[github wiki](https://github.com/jruby/jruby/wiki/CallingJavaFromJRuby)有一个关于它有多简单的精彩介绍。

将 OrientDB-JRuby 升级到 1.5.0(实际上我们昨天已经将它迁移到 1.5.1:)非常简单，只需将 1.3.0 的 jar 文件替换为它们的更新版本，以及 Java API 的一些依赖项。让我们花一点时间来谈谈其中的一些依赖关系。

## 修补程序，蓝图，管道和小精灵

你可能认为我刚给天线宝宝起了名字，对吧？没有。Tinkerpop 是一个 Java 库的集合，自称是“图形空间中的开源软件”。它包括:

*   [蓝图](https://github.com/tinkerpop/blueprints/wiki)—“属性图”模型界面。这就像 JDBC，但是是针对图形数据库的。Blueprints 支持其他 Tinkerpop 库。
*   [Pipes](https://github.com/tinkerpop/pipes/wiki)–Pipes 提供了一种查询图形数据库的管道方法。管道提供了用于转换和过滤图形查询和遍历的对象。实际上，Gremlin 使用它来提供一种强大的方法来查询图形。
*   [Gremlin](https://github.com/tinkerpop/gremlin/wiki)–Gremlin 是“一种图形遍历语言”。可以把 Gremlin 想象成一个遍历图形的 fliunt 接口 DSL。大多数图查询都是遍历的(“从这个顶点开始，基于一些过滤器遍历关系”)，Gremlin 试图标准化这种语言。

Tinkerpop 堆栈中还有其他库，但是我们不使用它们，所以我不打算讨论它们。还有， [Neo4j](http://neo4j.org) 、 [Dex](http://www.sparsity-technologies.com/dex) 和其他图形数据库的 [Tinkerpop 实现](https://github.com/tinkerpop/)。

作为升级 OrientDB-JRuby gem 的一部分，我还必须替换各种 tinker pop jar(OrientDB-JRuby 已经使用了 Blueprints 和 Pipes jars)并添加 Gremlin jars。Gremlin 的加入立即赋予了 OrientDB-JRuby jar 创建强大查询遍历的能力，稍后我们将看到这一点。

最后，值得注意的是 OrientDB 有自己的 Java API，独立于 Tinkerpop 实现。OrientDB-JRuby gem 也包装了这个 API，它提供了一些 OrientDB Tinkerpop 实现中没有的功能位。我将尽可能地坚持 OrientDB Tinkerpop APIs，但是如果我必须进入核心 API，我将确保您了解它。

## 准备您的演示区

对于这个演示，您需要从 github 仓库安装`orientdb` gem。我建议创建一个 **orientdb-sitepoint** 目录。另外，这个 gem 需要 JRuby，所以使用 RVM 或 rbenv 并安装 JRuby 1.7.4。确保您处于 **orientdb-sitepoint** 方向，并使用以下内容创建一个 Gemfile:

```
source "https://rubygems.org"

gem 'orientdb', github: 'aemadrid/orientdb-jruby'
```

一个快速的`bundle install`将关闭 OrientDB-JRuby。

您需要运行 OrientDB 服务器，所以请参考我的第一篇文章来了解如何设置它。

## OrientDB-JRuby 源代码

正如我所说，OrientDB-JRuby 只是包装了 OrientDB 核心和 Tinkerpop APIs。如果您在 Github 上浏览源代码，您可以看到它提供的一些方便的方法，以及它基于 Java 名称空间创建的一些 Ruby 常量。例如，看一下 [`constants.rb`](https://github.com/aemadrid/orientdb-jruby/blob/master/lib/orientdb/constants.rb) 文件，演示了 gem 提供的 Ruby 常量。

```
module OrientDB
  CORE = com.orientechnologies.orient.core
  CLIENT = com.orientechnologies.orient.client

  ClusterType            = CORE.storage.OStorage::CLUSTER_TYPE
  DocumentDatabase       = CORE.db.document.ODatabaseDocumentTx
  DocumentDatabasePool   = CORE.db.document.ODatabaseDocumentPool
  DocumentDatabasePooled = CORE.db.document.ODatabaseDocumentTxPooled
  GraphDatabase          = CORE.db.graph.OGraphDatabase
  OTraverse              = CORE.command.traverse.OTraverse
  Document               = CORE.record.impl.ODocument
  IndexType              = CORE.metadata.schema.OClass::INDEX_TYPE
  OClassImpl             = CORE.metadata.schema.OClassImpl
  LocalStorage           = CORE.storage.impl.local.OStorageLocal
  LocalCluster           = CORE.storage.impl.local.OClusterLocal
  PropertyImpl           = CORE.metadata.schema.OPropertyImpl
  RecordList             = CORE.db.record.ORecordTrackedList
  RecordSet              = CORE.db.record.ORecordTrackedSet
  Schema                 = CORE.metadata.schema.OSchema
  SchemaProxy            = CORE.metadata.schema.OSchemaProxy
  SchemaType             = CORE.metadata.schema.OType
  SQLCommand             = CORE.sql.OCommandSQL
  SQLSynchQuery          = CORE.sql.query.OSQLSynchQuery
  User                   = CORE.metadata.security.OUser
  RemoteStorage          = CLIENT.remote.OStorageRemote

  #Blueprints
  BLUEPRINTS = com.tinkerpop.blueprints

  #Gremlin
  Gremlin = com.tinkerpop.gremlin.java

  OrientGraph = BLUEPRINTS.impls.orient.OrientGraph
  Conclusion = com.tinkerpop.blueprints.TransactionalGraph::Conclusion

  ...
end
```

当我们使用 gem 访问 Java API 时，您将看到我们如何使用这些常量。

## 创建数据库

我们将为这个演示创建一个名为“sitepoint-ruby-demo”的数据库。所以，启动`orientdb-console`，这是宝石提供的[脚本。这将启动一个已经需要 OrientDB 的 IRB 会话。](https://github.com/aemadrid/orientdb-jruby/blob/master/bin/orientdb_console)

我们将使用“远程”协议来访问我们的 OrientDB 服务器，这意味着我们必须使用`OServerAdmin`对象来创建数据库。这是 OrientDB 核心 Java API 的一部分。

```
jruby-1.7.4 :001 > sa = OrientDB::CLIENT::remote::OServerAdmin.new("remote:localhost")
 => #<Java::ComOrientechnologiesOrientClientRemote::OServerAdmin:0x4473e7ae>

 jruby-1.7.4 :001 > sa.connect("user", "pass") # REPLACE user, pass with your OrientDB user and pass
 => #<Java::ComOrientechnologiesOrientClientRemote::OServerAdmin:0x4473e7ae>

 jruby-1.7.4 :001 > sa.create_database("sitepoint-ruby-demo", "graph", "local")
 => #<Java::ComOrientechnologiesOrientClientRemote::OServerAdmin:0x4473e7ae>
```

我们现在应该有一个数据库用于我们今天的演示。您可以通过转至 OrientDB Studio web 应用程序并验证“sitepoint-ruby-demo”在下拉列表中来验证数据库是否已创建。

[![db_exists](img/680437c60a93a93c17238817172f7644.png)](https://www.sitepoint.com/wp-content/uploads/2013/09/db_exists.png)

(注意:您可能需要更改用户并通过)

现在，连接到数据库:

```
jruby-1.7.4 :001 > g = OrientDB::OrientGraph.new("remote:localhost/sitepoint-ruby-demo", "user", "pass")
  => #<Java::ComTinkerpopBlueprintsImplsOrient::OrientGraph:0x25f1ce01>
```

OrientDB::OrientGraph 是蓝图 API 图形数据库连接。

## 创建顶点和边类型

回想一下 OrientDB 是如何工作的，每个图数据库都有 V 和 E 表，分别代表顶点和边的超类。虽然您可以在这些表中存储记录，但是用 V 或 E 子类来表示域对象通常更有意义。

```
person_type = g.create_vertex_type("Person")
=> #<OrientDB::OClassImpl:Person super=V>
```

你可以看到它自动为这个顶点类型子类化 V。添加属性很简单，只有一个问题:默认情况下，`OrientGraph`对象总是有一个打开的事务。OrientDB 不允许您在打开的事务中间添加属性，所以我们必须下降到“原始”连接并停止事务。

```
g.raw_graph.transaction.close
=> nil

person_type.add("Name", :string)
=> #<OrientDB::OClassImpl:Person super=V Name=STRING>

jruby-1.7.4 :022 > name_prop = name_prop.get_property("Name")
 => #<OrientDB::Property:Name type=string indexed=false mandatory=false not_null=false>
jruby-1.7.4 :023 > name_prop.set_mandatory(true)
 => #<OrientDB::Property:Name type=string indexed=false mandatory=true not_null=false>
jruby-1.7.4 :024 > name_prop.set_not_null(true)
 => #<OrientDB::Property:Name type=string indexed=false mandatory=true not_null=true>
```

我们的人现在有名字了。我们将添加*年龄*和*性别*属性。

```
jruby-1.7.4 :025 > person_type.add("Age", :int)
 => #<OrientDB::OClassImpl:Person super=V Name=STRING Age=INTEGER>
jruby-1.7.4 :026 > person_type.add("Gender", :string)
 => #<OrientDB::OClassImpl:Person super=V Name=STRING Age=INTEGER Gender=STRING>
```

现在，我们需要爸爸妈妈。

```
jruby-1.7.4 :030 > dad = g.add_vertex("class:Person", {name: 'Bob', age: 40, gender: 'M'})
=> #<Java::ComTinkerpopBlueprintsImplsOrient::OrientVertex:0x617e9189>

jruby-1.7.4 :016 > dad.id.to_s
=> "#11:-2"
```

我们为爸爸添加了一个临时的(意思是，不是持久的)顶点。请记住，OrientDB 有一个有点独特的 id 模式，它由一个' # '后跟一个集群 id 和记录 ID 组成，用冒号分隔。Dad 的 id 表明 Person 子类的默认集群 id 是`11`，他还没有被持久化，因为他的记录 id 是负的(-2)。

```
jruby-1.7.4 :020 > g.commit
=> nil

jruby-1.7.4 :021 > dad.id.to_s
=> "#11:0"
```

提交图表坚持爸爸，给他一个正确的 id。现在，轮到妈妈了

```
jruby-1.7.4 :030 > mom = g.add_vertex("Person", {name: 'Mary', age: 40, gender: 'F'})
=> #<Java::ComTinkerpopBlueprintsImplsOrient::OrientVertex:0x617e9189>

jruby-1.7.4 :020 > g.commit
=> nil
```

亲爱的读者，作为一个练习，请根据下表创建孩子:

*   弟弟:鲍比，10 岁
*   妹妹:简，12 岁

## 有关系

我们这个小家庭的成员完整无缺，是时候定义他们之间的关系了。我们可以遵循相同的“创建类型”路线来创建边类型、定义边上的属性等。但是，那样会很无聊，也不会向您展示 OrientDB 如何动态地创建类型。

```
jruby-1.7.4 :026 > marriage = dad.add_edge("Family", mom, "Family", nil, {relation_name: 'spouse'})
Sep 01, 2013 11:49:52 AM com.orientechnologies.common.log.OLogManager log
WARNING: Committing the active transaction to Committing the active transaction to create the new type 'Family' as subclass of 'E'. The transaction will be reopen right after that. To avoid this behavior create the classes outside the transaction. To avoid this behavior do it outside the transaction
 => #<Java::ComTinkerpopBlueprintsImplsOrient::OrientEdge:0x477e4626>

jruby-1.7.4 :026 > g.commit
=> nil

jruby-1.7.4 :027 > marriage.id.to_s
=> "#12:0"
```

妈妈和爸爸现在结婚了。(**扔米**

我相信当我们添加边缘的时候你已经注意到了。那就是 OrientDB 对你大喊它在改变模式，它只会对“Family”edge 类型做一次。

此外，你们中更精明的人可能会问，为什么我们只给爸爸增加了边缘。那是不是说爸爸娶了妈妈但是妈妈没有嫁给爸爸？嗯，这是一个很好的问题，它的答案归结为你和你的代码想如何对待它。OrientDB(和大多数图形数据库)是“有向属性图”，这意味着每条边都有一个起点、一个终点和一个方向。你不能有一条没有方向的边。

这让我们面临一个选择。我们可以为`relation_name`属性值添加带有‘配偶’的家庭边缘，或者在代码中忽略关系的方向。这里我将忽略它，并假设任何族外或族内边都是双向的。所以，如果爸爸娶了妈妈，那么妈妈就嫁给了爸爸。

至此，你现在有了更多的家庭作业:将爸爸妈妈添加为 Bobby 和 Jane 的“父母”,然后将 Bobby 添加为“兄弟姐妹”。我会等的。完成后，OrientDB studio 中的 Family 类中的记录应该如下所示:

[![family](img/b95e3cfb928832feb186883c72d8ec06.png)](https://www.sitepoint.com/wp-content/uploads/2013/09/family.png)

这样，我们的家庭就完整了。

## 询问

在本文的开始，如果你还记得，我提到了 Gremlin 和 Pipes。尽管 OrientDB 有一种本质上类似 SQL 的查询语言，但我将重点介绍如何使用 Tinkerpop 查询工具。我感觉那就是 OrientDB 要去的地方，所以最有意义。

在现实生活中，出于性能原因，您可能希望测试这两种查询类型。核心查询语言有时更快。

Gremlin Wiki 有一些很好的例子，展示了使用 API 的 Gremlin 查询是什么样子。通读那一页，然后回来这里询问我们的家族。

为了开始一个 Gremlin 查询，您需要一个`GremlinPipeline`

```
gp = OrientDB::Gremlin::GremlinPipeline.new(g)
=> #<Java::ComTinkerpopGremlinJava::GremlinPipeline:0x5554ca01>
```

记住，这是一个*管道*。它是可链接的，并且(直到您迭代/评估它)通过链具有状态。每个管道都是以端到端的方式添加到管道中的，因此如果您想要重新开始，您需要一个新的管道。

### 获得配偶

一旦我们有了自己的`GremlinPipeline`，我们必须给它一个起点。如果我们想要得到所有的配偶，那么我们的起点是所有在`Person`类中的顶点。接下来，我们要遍历每个顶点的`Family`边，只保留那些有`relation_name`的‘配偶’的边。最后，因为我们忽略了家族的方向，所以为每条合格的边抓取两个顶点。

```
jruby-1.7.4 :078 > gp = OrientDB::Gremlin::GremlinPipeline.new(g)
 => #<Java::ComTinkerpopGremlinJava::GremlinPipeline:0x4e790cea>

jruby-1.7.4 :079 > spouses = gp.v.has("@class", "Person").outE("Family").has("relation_name", "spouse").bothV.iterate
 => [#<Java::ComTinkerpopBlueprintsImplsOrient::OrientVertex:0x42d0a46b>, #<Java::ComTinkerpopBlueprintsImplsOrient::OrientVertex:0x2c4c9e88>]

jruby-1.7.4 :080 > spouses.map{|v| v.get_property("name")}
 => ["Bob", "Mary"]
```

我们来分析一下。

*   `gp`是我们的管道
*   `v`将我们的起点设置到所有顶点。
*   `has("@class", "Person")`添加一个 [`PropertyFilterPipe`](http://www.tinkerpop.com/docs/javadocs/pipes/2.4.0/com/tinkerpop/pipes/filter/PropertyFilterPipe.html) ，检查每个顶点`@class`系统属性为`Person`。
*   `outE("Family")`添加一个 [`OutEdgePipe`](http://www.tinkerpop.com/docs/javadocs/pipes/2.4.0/com/tinkerpop/pipes/transform/OutEdgesPipe.html) ，它遍历从任何仍在流水线中的顶点出来的任何族边。
*   `has`添加另一个`PropertyFilterPipe`，为`relation_name == 'spouse'`过滤边缘。
*   `bothV`添加一个 [`BothVertices`](http://www.tinkerpop.com/docs/javadocs/pipes/2.4.0/com/tinkerpop/pipes/transform/BothVerticesPipe.html) ，它从仍在流水线中的任何边收集两个顶点。记住，我们的关系是双向的。
*   `iterate`在该点评估管道。此时调用`to_a`也会做同样的事情。

之后，我们只需循环结果并获取`name`属性，看看谁结婚了。

查看[管道 javadoc](http://www.tinkerpop.com/docs/javadocs/pipes/2.4.0/) 显示了管道的数量。事情会在一瞬间变得非常强大(和复杂)。
习惯用 Gremlin 查询需要一点时间和毅力。这是一个非常简单的例子。在我的下一篇文章中，我将展示一些更复杂的查询。

家庭作业:找出获取所有兄弟姐妹的管道。

## 把它包起来

这是使用 OrientDB-JRuby gem 的旋风之旅。显然，您可以做得更多，但这是一个良好的开端。

在我的下一篇关于 OrientDB 和 Ruby 的文章中，我计划介绍另一个名为 Oriented 的 gem，它为 OrientDB-JRuby 提供了另一个抽象层次。在此之前，开始将图表概念应用到您的工作中。我敢打赌，你会发现它适用的领域比你想象的要多得多。

## 分享这篇文章