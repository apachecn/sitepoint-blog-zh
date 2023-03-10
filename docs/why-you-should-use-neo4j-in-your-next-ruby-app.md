# 为什么你应该在下一个 Ruby 应用中使用 Neo4j

> 原文：<https://www.sitepoint.com/why-you-should-use-neo4j-in-your-next-ruby-app/>

![neo4j-logo-2015 (1)](img/639c925a786da155cfc9ed48e8453e99.png)

在我的时间里，我需要存储大量的数据，并且我已经使用了许多大的竞争者:PostgreSQL、MySQL、SQLite、Redis 和 MongoDB。虽然我已经积累了使用这些工具的丰富经验，但我不能说它们中的任何一个曾经使任务变得有趣。我爱上了 Ruby，因为它很有趣，也因为它让我可以不碍事地做更强大的事情。虽然我没有意识到这一点，但数据持久性的常见嫌疑正在妨碍我。但是我已经找到了新欢:让我告诉你关于 [Neo4j](https://neo4j.com) 的事情。

## Neo4j 是什么？

Neo4j 是图形数据库！这意味着它针对管理和查询实体(**节点**)之间的连接(**关系**)进行了优化，这与使用表的关系数据库不同。

为什么这很棒？想象一个没有外键的世界。数据库中的每个实体都可以有许多直接引用其他实体的关系。如果您想探索这些关系，没有表或索引扫描，只需跟随一些连接。这与典型的对象模型非常匹配。不过，它更强大，因为 Neo4j 在提供我们期望的许多数据库功能的同时，还为我们提供了在数据中查询复杂模式的工具。

## ActiveNode 简介

为了连接到 Neo4j，我们将使用 [`neo4j`宝石](https://neo4jrb.io/)。您可以在 [gem 的文档](https://neo4jrb.readthedocs.org/en/stable/Setup.html)中找到在 Rails 应用程序中连接 Neo4j 的说明。此外，具有如下所示代码的应用程序可以作为运行 Rails 应用程序在[GitHub 库](https://github.com/neo4j-examples/asset_portal/tree/sitepoint)中获得(使用`sitepoint` Git 分支)。当你的数据库启动并运行后，使用`rake load_sample_data`命令来填充你的数据库。

下面是一个来自资产管理 Rails 应用程序的`Asset`模型的基本示例:

`app/models/asset.rb`

```
class Asset
  include Neo4j::ActiveNode

  property :title

  has_many :out, :categories, type: :HAS_CATEGORY
end
```

让我们来分解一下:

*   `neo4j` gem 给了我们`Neo4j::ActiveNode`模块，我们`include`用它来制作模型。
*   类名`Asset`意味着这个模型将负责 Neo4j 中所有标记为`Asset`的节点(标签的作用类似于表名，只是一个节点可以有很多标签)。
*   我们有一个`title`属性来描述单个节点
*   我们有一个针对`categories`的传出`has_many`关联。这种关联通过跟踪数据库中的`HAS_CATEGORY`关系帮助我们找到`Category`对象。

使用此模型，我们可以执行一个基本查询来查找资产并获取其类别:

```
2.2.0 :001 > asset = Asset.first
 => #<Asset uuid: "0098d2b7-a577-407a-a9f2-7ec4153cfa60", title: "ICC World Cup 2015 ">
2.2.0 :002 > asset.categories.to_a
 => [#<Category uuid: "91cd5369-605c-4aff-aad1-b51d8aa9b5f3", name: "Classification">]
```

任何熟悉`ActiveRecord`或`Mongoid`的人都会看过这个几百遍。为了更有趣一点，让我们定义一个`Category`模型:

```
class Category
  include Neo4j::ActiveNode

  property :name

  has_many :in, :assets, origin: :categories
end
```

这里我们的关联有一个`origin`选项来引用`Asset`模型上的`categories`关联。如果我们愿意，我们可以再次指定`type: :HAS_CATEGORY`。

## 创建建议

如果我们想要获得与我们的资产共享一个类别的所有资产，会怎么样？

```
2.2.0 :003 > asset.categories.assets.to_a
 => [#<Asset uuid: "d2ef17b5-4dbf-4a99-b814-dee2e96d4a09", title: "WineGraph">, ...]
```

刚刚发生了什么？ActiveNode 生成了对数据库的查询，该查询指定了从我们的资产到共享一个类别的所有其他资产的路径。然后数据库将这些资产返回给我们。下面是它使用的查询:

```
MATCH
  asset436, asset436-[rel1:`HAS_CATEGORY`]->(node3:`Category`),
  node3<-[rel2:`HAS_CATEGORY`]-(result_assets:`Asset`)
WHERE (ID(asset436) = {ID_asset436})
RETURN result_assets

Parameters: {ID_asset436: 436}
```

这是一种叫做 [Cypher](https://neo4j.com/docs/stable/cypher-query-lang.html) 的查询语言，相当于 Neo4j 的 SQL。请特别注意节点定义周围的括号和表示关系的箭头的 ASCII 艺术风格。这个 Cypher 查询有点冗长，因为 ActiveNode 是通过算法生成的。如果由一个人来编写这个查询，它看起来会像这样:

```
MATCH source_asset-[:HAS_CATEGORY]->(:Category)<-[:HAS_CATEGORY]-(result_assets:Asset)
WHERE ID(source_asset) = {source_asset_id}
RETURN result_assets

Parameters: {source_asset_id: 436}
```

我发现 Cypher 比 SQL 更简单、更强大，但在本文中我们不会太担心 Cypher。如果你以后想了解更多，你可以找到很棒的[教程](https://neo4j.com/developer/cypher/)和一个完整的[参考卡](https://neo4j.com/docs/stable/cypher-refcard/)。

如您所见，我们可以使用 Neo4j 跨越我们的实体。大不了！我们也可以在 SQL 中用几个`JOINS`来做这件事。虽然 Cypher 看起来很酷，但我们还没有突破任何主要领域。如果我们想使用这个查询来基于共享的类别进行一些资产推荐呢？我们希望对资产进行排序，以便对那些类别最相似的资产进行排序。让我们在模型上创建一个方法:

```
class Asset
  ...

  Recommendation = Struct.new(:asset, :categories, :score)

  def asset_recommendations_by_category(common_links_required = 3)
    categories(:c)
      .assets(:asset)
      .order('count(c) DESC')
      .pluck('asset, collect(c), count(c)').reject do |_, _, count|
      count < common_links_required
    end.map do |other_asset, categories, count|
      Recommendation.new(other_asset, categories, count)
    end
  end
end
```

这里有一些有趣的事情需要注意:

*   我们将变量定义为我们的链的一部分，以便稍后使用(`c`和`asset`)。
*   我们使用 Cypher `collect`函数给出一个包含共享类别数组的结果列(见下表)。还要注意，我们得到的是完整的对象，而不仅仅是列/属性:

| 资产 | 收集(c) | 计数(c) |
| --- | --- | --- |
| # | [# <category>]</category> | one |
| # | [# <category>，# <category>，…]</category></category> | four |
| # | [# <category>，# <category>]</category></category> | Two |
| … | … | … |

你注意到没有一个`GROUP BY`从句吗？Neo4j 足够聪明，能够意识到`collect`和`count`是聚合函数，它在我们的结果中按照非聚合列进行分组(在本例中，这只是`asset`变量)。

拿那个 SQL！

作为最后一步，我们可以就不仅仅是共同的类别提出建议。我们在 Neo4j 中有如下子图:

![](img/c992cba95fe6183f5a4fd65443cf9e84.png)

除了共享类别之外，让我们考虑一下有多少创建者和查看者资产是相同的:

```
class Asset
  ...
  Recommendation = Struct.new(:asset, :score)

  def secret_sauce_recommendations
    query_as(:source)
      .match('source-[:HAS_CATEGORY]->(category:Category)<-[:HAS_CATEGORY]-(asset:Asset)').break
      .optional_match('source<-[:CREATED]-(creator:User)-[:CREATED]->asset').break
      .optional_match('source<-[:VIEWED]-(viewer:User)-[:VIEWED]->asset')
      .limit(5)
      .order('score DESC')
      .pluck(
        :asset,
        '(count(category) * 2) + (count(creator) * 4) + (count(viewer) * 0.1) AS score').map do |other_asset, score|
      Recommendation.new(other_asset, score)
    end
  end
end
```

在这里，我们深入研究，并开始形成我们自己的查询。结构是相同的，但是我们并没有通过一个共享的类别在两个资产之间找到一条路径，而是指定了另外两条可选的路径。我们可以将这三条路径都设为可选，但是 Neo4j 需要将我们的资产与数据库中的其他资产进行比较。通过对通过`Category`节点的路径使用`match`而不是`optional_match`，我们要求至少有一个共享类别。这极大地限制了我们的搜索空间。

在图中有一个共享类别，零个共享创建者和两个共享查看者。这意味着“Ruby”和“Ruby on Rails”之间的分数是:

```
(1 * 2) + (0 * 4) + (2 * 0.1) = 2.2
```

还要注意，我们正在对这三条路径的`count`集合进行计算(和排序)。这对我来说太酷了，以至于一想到它我就有点兴奋…

## 轻松授权

让我们解决另一个常见的问题。假设你的 CEO 来到你的办公桌前，说“我们已经开发了一个很棒的应用，但是客户希望能够控制谁可以看到他们的东西。你能内置一些隐私控制吗？”这似乎很简单。让我们加上一面旗帜，允许私人资产:

```
class Asset
  ...
  property :public, default: true

  def self.visible_to(user)
    query_as(:asset)
      .match_nodes(user: user)
      .where("asset.public OR asset<-[:CREATED]-user")
      .pluck(:asset)
  end
end
```

这样，您可以显示用户可以看到的所有资产，因为资产是公共的，或者因为查看者拥有它。没问题，但也没什么大不了的。在另一个数据库中，您可以只对两个列/属性进行查询。让我们变得更疯狂一点！

产品经理找到你，说“嘿，谢谢你，但是现在人们希望能够让其他用户直接访问他们的私人资料”。没问题！您可以构建一个 UI，让用户添加和删除资产的`VIEWABLE_BY`关系，然后像这样查询它们:

```
class Asset
  ...

  def self.visible_to(user)
    query_as(:asset)
      .match_nodes(user: user)
      .where("asset.public OR asset<-[:CREATED]-user OR asset-[:VIEWABLE_BY]->user")
      .pluck(:asset)
  end
end
```

否则这将是一个连接表。在这里，您只是添加了另一个用户可以访问资产的路径。您需要花点时间来欣赏 Neo4j 的无模式特性。

对一天的工作感到满意后，你靠在椅子上，啜饮下午茶。当然，这时社交媒体客户服务代表会过来说“用户喜欢这个新功能，但他们希望能够创建群组并为群组分配访问权限。你能做到吗？哦，还有，你能允许任意的群体等级吗？”你深深地盯着他们的眼睛看了几分钟，然后回答:“当然！”。由于这开始变得复杂，我们来看一个例子:

![](img/6c05e1d7bc0e9fdeff8d4591d0ae34c6.png)

如果这两个资产都是私有的，那么你的代码到目前为止给了马茨和泰德洛夫访问 Ruby 的权限，给了 DHH 访问 Ruby on Rails 的权限。要添加组支持，您可以从直接分配的组开始:

```
class Asset
  ...

  def self.visible_to(user)
    query_as(:asset)
      .match_nodes(user: user)
      .where("asset.public OR asset<-[:CREATED]-user OR asset-[:VIEWABLE_BY]->user OR asset-[:VIEWABLE_BY]->(:Group)<-[:BELONGS_TO]-user")
      .pluck('DISTINCT asset')
  end
end
```

这非常简单，因为您只需要添加另一个路径。当然是两跳，但那对我们来说已经过时了。Tenderlove 和 Yehuda 将能够看到“Ruby on Rails”资产，因为他们是“Railsists”组的成员。还要注意:现在有些用户有多条路径可以访问一个资产(比如 Matz 通过 Rubyists 组和通过`CREATED`关系访问 Ruby ),你需要返回`DISTINCT asset`。

但是，在组的层次结构中指定任意路径会花费您更多的时间。您浏览了 Neo4j 文档，直到找到了所谓的“变量关系”并尝试了一下:

```
class Asset
  ...

  def self.visible_to(user)
    query_as(:asset)
      .match_nodes(user: user)
      .where("asset.public OR asset<-[:CREATED]-user OR asset-[:VIEWABLE_BY]->user OR asset-[:VIEWABLE_BY]->(:Group)<-[:HAS_SUBGROUP*0..5]-(:Group)<-[:BELONGS_TO]-user")
      .pluck('DISTINCT asset')
  end
end
```

你做到了！该查询将找到一个组可以访问的资产，并遍历任何一组从零到五的 `HAS_SUBGROUP`关系，最后检查用户是否在最后一个组中。你是故事中的英雄，你的公司因为你这么快就完成了工作而给你发奖金！

## 结论

你可以用 Neo4j 做很多很棒的事情(包括使用它的[惊人的网络界面](https://neo4j.com/developer/guide-neo4j-browser/)来用 Cypher 探索你的数据),这我就不多说了。它不仅是一种以简单直观的方式存储数据的好方法，还为高效查询高度关联的数据提供了很多好处(请相信我，即使您没有意识到，您的数据也是高度关联的)。我鼓励你去看看 Neo4j，并在你的下一个项目中尝试一下！

## 分享这篇文章