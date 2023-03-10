# 使用 REST API 创建 Neo4j 图形数据库

> 原文：<https://www.sitepoint.com/create-neo4j-graph-database-using-rest-api/>

![logo](img/c1b5ef2f15dabc47d12ac29153547acd.png)

尽管图论的根源可以追溯到近 300 年前，但是基于图论的数据库还没有进入第二个十年。这些数据库相当年轻，但它们已经在一些最大的在线公司中得到广泛应用，包括谷歌、脸书和易贝。此外，医疗保健、零售、游戏和能源等大型行业正在利用图形数据库。

为什么受欢迎？图形数据库是围绕实体之间的关系建立的，这种范式非常适合人类大脑思考事物的方式，尤其是社会互动。例如，脸书的《朋友》强调人与人之间的关系。

传统关系数据库有什么问题？具有讽刺意味的是，关系数据库在查询关系信息时表现很差。性能差是由于用于定义关系的昂贵的连接操作。连接操作导致关系数据库搜索大量信息，只丢弃除相关条目之外的所有条目。例如，通过 4 度分离的搜索(例如；朋友的朋友的朋友)几乎会使关系数据库变得毫无用处，因为所需的 CPU 周期和内存随着度数的增加而呈指数级增加。

相比之下，图形数据库在搜索四个分离度时几乎不费吹灰之力。事实上，起决定作用的并不一定是搜索本身，而是搜索结果的数量，这很容易产生数百万个条目。

在这篇由两部分组成的文章中，我们将使用最成熟、最流行的图形数据库 Neo4j。Neo4j 是用 Java 写的，但是有很多语言的接口，包括 Ruby。本着灵活性的精神，我们将避开 Ruby 接口，转而利用 REST API 接口，这使得我们只需使用 **rest_client** 和 **json** gems 就可以使用 Neo4j。因此，本文中的代码可以轻松地重构为其他语言，比如 PHP、Python 甚至 JavaScript。

为了完成这个旅程，本文将重点介绍如何设置 Neo4j 来为一个销售部门实现一个简单的 CRM(客户关系管理)。我们将简要地看一下用于与 Neo4j 通信的 Cypher 查询语言，然后我们将使用 API 将信息放入数据库。

## 背景:棍子和泡泡

看看任何一个工程会议室，你很可能会在白板上看到棒泡图。更具体地说，您将看到围绕概念画的圆圈，以及指向其他圆圈的箭头。这些类型的图表非常有效，因为人类大脑将世界视为相互联系的实体。每个实体由一个圆圈表示，包含任意数量的属性，如名称、日期、计算机 IP 地址等。互连圆圈的箭头表示关系，例如货币、数据、IP 数据报等的流动。

在我们的 CRM 示例中，我们将展示一个客户区域经理和一个销售客户经理之间的关系。该界面出现在下面的简单图形中:

![Graph of the relationship Linda manages Jeff](img/10ff49bdaa4c1453b416f12eabb8ba39.png "Graph of the relationship Linda manages Jeff")

注意每个节点(圆圈)有几个属性，比如标签、名称和标题。严格地说，label 属性是唯一必需的属性，因为它描述了节点的性质。这种关系被简单地描述为`:Manages`，在这种情况下，它的意图很明显:Linda 管理 Jeff。

我们使用“ASCII-Art”的形式来表示这种关系，如下所示:

`(Linda)-[manages]->(Jeff)`

在这种情况下，将 Linda 和 Jeff 用括号括起来大致表示圆形节点，它们的关系用箭头表示:`-->`表示这是一个有向图。这种关系由单词`manages`进一步描述。

## Neo4j 密码查询语言

Neo4j Cypher 查询语言实现了一个非常类似于上述 ASCII-Art 的协议。例如，我们通过使用关键字`CREATE`创建了`Linda`和`Jeff`节点，然后是节点的定义。

```
CREATE (:Person {name:"Linda Barnes", title:"Territory Manager"} );
CREATE (:Person {name:"Jeff Dudley", title:"Account Manager"} );
```

节点标签使用您选择的任何单词来标识，前面加一个冒号。在这种情况下，我们将 Linda 和 Jeff 都指定为人物，并使用标签`:Person`来表示这种指定。我们可以用`:Human`、`:individual`、`:Dude`或者任何你觉得合适的名字来代替`:Person`。选择标签名称的规则是选择含义明确的术语，并始终使用它们。

注意，节点属性`name`和`title`的创建方式非常类似于 Ruby 中的符号键散列。对放置在节点中的属性没有实际限制。此外，在创建节点时，您不必指定所有属性。您可以随时修改节点，以提供更清晰的含义。这比 MySQL 或 PostgreSQL 等关系数据库灵活得多。关系数据库迫使您更改表设计，这会影响表中的所有行。在图形数据库中，您只需对一个或多个节点进行更改，它只会影响您正在更改的节点。

现在是时候将这两个节点连接到一个有向图关系中了，这个有向图关系暗示了 Linda 管理 Jeff 的事实。这个过程分两步完成:1)创建对每个节点的引用，2)定义关系。我们使用`MATCH` Cypher 关键字来创建引用。

```
MATCH ( a:Person {name:"Linda Barnes"} ), ( b:Person {name:"Jeff Dudley"} )
CREATE (a)-[:Manages]->(b);
```

请注意如何通过标识`:Person`标签和`name`属性来建立对每个节点的唯一引用。引用`a`和`b`随后在`CREATE`命令中使用，以在两个节点之间建立`:Manages`关系。使用`>`符号传达方向。(注意，去掉`>`符号可以创建一个无向图；然而，在分层管理的意义上，说 Linda 和 Jeff 互相管理是没有意义的，因此使用了有向图。)

注意，与节点类似，关系也可以包含属性，尽管在这个特殊的例子中，我们选择不在`:Manages`关系中包含任何属性。

## 用 Ruby 运行 Neo4j

让我们看看使用 Ruby **rest_client** API 会是什么样子。

然而，在我们开始之前，我们需要访问 Neo4j 数据库。出于学习目的，我们将按照 Neo4j 网站上的说明将其安装在本地机器上。

进入 [Neo4j 网站](https://www.neo4j.org)，点击下载页面。打开此页面后，应该会自动下载适合您机器的正确软件。下载软件包时，您可以阅读逐步说明以将其安装到您的计算机上。如果您还没有安装 Oracle JDK (Java 开发工具包)，您必须在安装 Neo4j 之前找到并安装它。幸运的是，Neo4j 下载页面包含一个到 JDK 下载页面的链接，所以定位、下载和安装 JDK 是一件很简单的事情。

我们不会在这里讨论安装细节，因为这个过程在 Neo4j 下载页面上有文档记录。请按照安装说明进行操作。出奇的容易！

下载并安装所有内容后，`cd`到 Neo4j 解压缩文件夹，并使用以下命令启动服务器:

`bin/neo4j start`

通过在本地主机上打开浏览器到[端口 7474，连接到 Neo4j 服务器，并看到欢迎屏幕，如下所示。](https://localhost:7474)

![Neo4j Opening Page](img/bab187dcbfd6701d8e5f691703c91e45.png "Neo4j Opening Page")

这个页面非常有用，因为它可以将您当前的数据库显示为经典的“棒泡”图。它还允许您手动测试您的密码命令，并立即看到结果。

为了准备 Ruby 代码，如果您的系统上还没有安装 gems for rest_client 和 json，请安装它们:

```
gem install rest-client
gem install json
```

我们终于可以执行一些 Ruby 代码并与 Neo4j 服务器对话了！

我们将从创建一个类`RGraph`开始。在本文的整个过程中，我们将构建这个类。

```
require 'json'
require 'rest_client'

class RGraph

  def initialize
    @url = 'https://localhost:7474/db/data/cypher'
  end

end
```

注意 URL 地址，`/db/data/cypher`。这是将使用 Cypher 查询语言的所有 API 调用的基址。

现在让我们定义一个创建 Neo4j 图形数据库节点的方法。

```
def create_node (label,attr={})
  # Create a node
  query = ''  # holds the final query string
  attributes = '' # holds the attribute portion of the query, if any
  if attr.size == 0
    # No attributes, so create a simple node
    query += "CREATE (:#{label});"
  else
    # Create the attribute clause portion of the query
    attributes += '{ '
    attr.each do |key,value|
      attributes += "#{key.to_s}: '#{value}',"
    end
    attributes.chomp!(',') # Neo4j hates extra commas!
    attributes += ' }'
    query += "CREATE (:#{label} " + attributes + ');'
  end
  c = {
      "query" => "#{query}",
      "params" => {}
  }
  RestClient.post @url, c.to_json, :content_type => :json, :accept => :json
end
```

注意第 1 行中的`label`参数，它是节点的必需部分。属性是可选的；然而，创建一个没有属性的节点是很不寻常的。属性携带该节点的信息。在我们简单的 CRM 示例中，属性包含人名和职位。它还可以包含其他信息，如出生日期、工资、喜欢的颜色或任何有助于识别这个独特个体的信息。

注意第 18 行创建了一个临时散列，其中包含实际的 Cypher 查询以及其他可选参数。我们可以在`params`元素中定义属性的内容，而不是直接在命令行中包含属性。这在您有一些复杂的参数并且想要将其分解成不同的行的情况下可能是有用的。然而，在我们的简单例子中，我们将所有参数包含在查询本身中，并传入一个空的`params`元素。

实际的 REST 调用发生在第 22 行。

这很简单，不是吗？你可能会怀疑建立关系也同样简单。嗯，差不多就是这样。请记住，关系连接两个节点。因此，我们必须使用一个 Cypher `MATCH`语句来定位这两个节点，并在后续的`CREATE`语句中引用这些节点。

```
def create_directed_relationship (from_node, to_node, rel_type)
  # Create a directed relationship between nodes
  query = ''  # Holds the final query string
  attributes = '' # holds identifying attributes, if any
  # First put together the two matching statements to find the
  # source and destination nodes
  query += "MATCH ( a:#{from_node[:type]} "
  from_node.each do |key,value|
    next if key == :type # Don't count "type" as an attribute
    attributes += "#{key.to_s}: '#{value}',"
  end
  attributes.chomp!(',') # Get rid of extra comma
  query += "{ #{attributes} }),"
  attributes = '' # Reset to process next MATCH statement
  query += " ( b:#{to_node[:type]} "
  to_node.each do |key,value|
    next if key == :type # Don't count "type" as an attribute
    attributes += "#{key.to_s}: '#{value}',"
  end
  attributes.chomp!(',') # Get rid of extra comma
  query += "{ #{attributes} }) "
  # The "a" and "b" nodes are now identified, so now create the relationship
  query += "CREATE (a)-[:#{rel_type}]->(b);"
  c = {
      "query" => "#{query}",
      "params" => {}
  }
  RestClient.post @url, c.to_json, :content_type => :json, :accept => :json
end
```

第一行中的`from_node`和`to_node`参数实际上是惟一描述两个连接节点的散列。在这种情况下，我们采用了简单的(也许*对于生产发布来说太*简单了！)规则，要求它们包含一个 key- >值对，该值对包含用于指定节点标签的`:type`键。任何额外的键用于帮助进一步识别唯一节点。所有这些属性的结果都被折叠到 Cypher `MATCH`语句中，然后添加到`query`字符串中。

第 23 行在`CREATE`命令中折叠，指定最后一个参数`rel_type`作为关系的标签。

第 28 行调用 REST，将关系存储在数据库中。

## CRM 数据库

下图显示了在本文的下一部分中，我们将如何使用上述代码。我们将填写`RGraph`类中的其余方法，然后使用这些方法填写一个非常简单的销售 CRM 的所有节点和关系。

![Sales CRM Graph](img/e115bb2c4cc395f59c1d4000f93b40e9.png "Sales CRM Graph")

这个 CRM 的结构将允许区域经理(在本例中是 Linda)对数据进行复杂的查询。例如，Linda 可以问这样一个问题，“在我负责的所有公司中，谁是我们尚未联系的所有客户经理，谁是相关的负责客户经理？”

## 摘要

在这两篇文章的第一篇中，我们简要地查看了 Neo4j 数据库，并使用 Cypher 查询语言构建了一些简单的查询。然后我们创建了一个 Ruby 类来利用我们的 Cypher 知识来运行 REST API 调用。在下一篇文章中，我们将通过一个简单的 CRM 实现来展示销售组织如何在 Neo4j 中表示他们的客户关系，从而允许区域经理进行复杂的查询并获得对客户帐户的有价值的洞察力。

## 分享这篇文章