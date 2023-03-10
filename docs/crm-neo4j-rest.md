# 带有 Neo4j 和 REST 的 CRM

> 原文：<https://www.sitepoint.com/crm-neo4j-rest/>

![logo](img/c1b5ef2f15dabc47d12ac29153547acd.png)

在[之前的一篇文章](https://www.sitepoint.com/create-neo4j-graph-database-using-rest-api/)中，我们简要查看了 Neo4j 数据库，并创建了一些简单的 Ruby API 方法，这些方法利用了我们对 Cypher 查询语言的了解。我们开始组装一个简单的 CRM(客户关系管理)应用程序，试图展示图形数据库在一个高度互联的世界中导航是多么容易。在本文中，我们将通过放大一个虚构的销售组织的管理结构来继续构建 CRM 示例，并看看对数据库进行复杂查询是多么容易。

## 让我们建立数据库！

首先，让我们构建一个简单的数据库。这个数据库的数据将在 Ruby 散列结构中表示。这个散列结构可以很容易地来自 JSON 文件，而 JSON 文件又可以来自一组在线 web 表单或 Excel 电子表格。

这个特殊的数据集将描述一个销售组织的结构及其与几个客户公司的关系。在这种结构中:

*   有一名区域经理负责管理-
*   三名客户经理，每个人管理-
*   一个公司账户。每个公司账户都有-
*   一名或多名员工，其中大多数-
*   由一名经理管理。
*   该数据库保存了客户经理和客户在各自账户上的会议。

下面的图表也出现在本文的第 1 部分，描述了节点及其关系。

![Sales CRM Graph](img/e115bb2c4cc395f59c1d4000f93b40e9.png "Sales CRM Graph")

我们将用来填充数据库的 Ruby 散列结构可以在下面的清单中看到。注意，为了简单起见，这只是数据库的部分列表。它包含了足够的信息让你明白我们要去哪里。我们将`@data`变量包含在`RGraph`类的`initialize`方法中，这在上一篇文章中已经介绍过。

```
@data = {
    nodes: [
        {
            label: 'Person',
            title: 'Territory Manager',
            name: 'Linda Barnes'
        },
        {
            label: 'Person',
            title: 'Account Manager',
            name: 'Jeff Dudley',
        },
        # ...
        {
            label: 'Company',
            name: 'OurCompany, Inc.'
        },
        {
            label: 'Company',
            name: 'Acme, Inc.'
        },
        {
            label: 'Company',
            name: 'Wiley, Inc.'
        },
        {
            label: 'Company',
            name: 'Coyote, Ltd.'
        },
    ],
    relationships: [
        {
            type: 'MANAGES',
            source: 'Linda Barnes',
            destination: ['Jeff Dudley', 'Mike Wells', 'Vanessa Jones']
        },
        {
            type: 'MANAGES',
            source: 'Jesse Hoover',
            destination: ['Ralph Green', 'Patricia McDonald']
        },
        # ...
        {
            type: 'WORKS_FOR',
            destination: 'OurCompany, Inc.',
            source: ['Linda Barnes', 'Jeff Dudley', 'Mike Wells', 'Vanessa Jones']
        },
        {
            type: 'WORKS_FOR',
            destination: 'Acme, Inc.',
            source: ['Jesse Hoover', 'Ralph Green', 'Sheila Foxworthy', 'Janet Huxley-Smith',
                     'Tim Reynolds', 'Zachary Meyer', 'Milton Stacey', 'Steve Nauman', 'Patricia McDonald']
        },
        # ...
        {
            type: 'ACCOUNT_MANAGES',
            source: 'Jeff Dudley',
            destination: 'Acme, Inc.'
        },
        {
            type: 'ACCOUNT_MANAGES',
            source: 'Mike Wells',
            destination: 'Wiley, Inc.'
        },
        {
            type: 'ACCOUNT_MANAGES',
            source: 'Vanessa Jones',
            destination: 'Coyote, Ltd.'
        },
        {
            type: 'HAS_MET_WITH',
            source: 'Jeff Dudley',
            destination: ['Tim Reynolds', 'Zachary Meyer', 'Janet Huxley-Smith', 'Patricia McDonald']
        },
        {
            type: 'HAS_MET_WITH',
            source: 'Mike Wells',
            destination: ['Francine Gonzalez', 'Tsunomi Ito', 'Frank Cutler']
        },
        {
            type: 'HAS_MET_WITH',
            source: 'Vanessa Jones',
            destination: 'Tracey Stankowski'
        }
    ]
```

使用`RGraph`类，我们将创建一些代码来读入所有数据，并使用`create_node`和`create_directed_relationship`方法构建图形数据库。在前一篇文章中提供了这些方法的代码。

因为有大量的数据要处理，我们将创建上述方法的复数形式。更具体地说，我们将创建两个额外的方法:`create_nodes`和`create_directed_relationships`，它们将用于处理在`@data`散列中表达的每个节点和关系。这些复数形式将在循环结构中调用它们的单数对应物。

```
def create_nodes
  # Scan file, find each node and create it in Neo4j
  @data.each do |key,value|
    if key == :nodes
      @data[key].each do |node| # Cycle through each node
        next unless node.has_key?(:label) # Make sure this node has a label
        # We have sufficient data to create a node
        label = node[:label]
        attr = Hash.new
        node.each do |k,v|  # Hunt for additional attributes
          next if k == :label # Don't create an attribute for "label"
          attr[k] = v
        end
        create_node(label,attr)
      end
    end
  end
end
```

在上面的`create_nodes`清单的第 3 行建立了一个主循环。这个循环只是寻找`node`数组，它为每个节点提供信息。一旦这个数组被发现，第 5 行开始一个内部循环。它过滤掉所有没有`label`属性的节点。第 10 行上的循环用于累积任何节点属性的信息。最后，在收集了所有必要的信息之后，它调用`create_node`方法，该方法又将所有这些信息推入 Neo4j 数据库。

一组类似的循环用于创建节点之间的关系。然而，关系有点复杂，因为它们需要明确地标识它们所连接的节点。

这些关系在下面的`create_directed_relationships`列表中创建:

```
def create_directed_relationships
  # Scan file, look for relationships and their respective nodes
  @data.each do |key,value|
    if key == :relationships
      @data[key].each do |relationship| # Cycle through each relationship
        next unless relationship.has_key?(:type) &&
            relationship.has_key?(:source) &&
            relationship.has_key?(:destination)
        rel_type = relationship[:type]
        case rel_type
          # Handle the different types of cases
          when 'MANAGES', 'ACCOUNT_MANAGES', 'HAS_MET_WITH'
            # in all cases, we have one :Person source and one or more destinations
            from_node = {type: 'Person', name: relationship[:source]}
            to_node = (rel_type == 'ACCOUNT_MANAGES') ? {type: 'Company'} : {type: 'Person'}
            if relationship[:destination].class == Array
              # multiple destinations
              relationship[:destination].each do |dest|
                to_node[:name] = dest
                create_directed_relationship(from_node,to_node,rel_type)
              end
            else
              to_node[:name] = relationship[:destination]
              create_directed_relationship(from_node,to_node,rel_type)
            end
          when 'WORKS_FOR'
            # one destination, one or more sources
            to_node = {type: 'Company', name: relationship[:destination]}
            from_node = {type: 'Person'}
            rel_type = 'WORKS_FOR'
            if relationship[:source].class == Array
              # multiple sources
              relationship[:source].each do |src|
                from_node[:name] = src
                create_directed_relationship(from_node,to_node,rel_type)
              end
            else
              from_node[:name] = relationship[:source]
            end
        end
      end
    end
  end
end
```

关系数组位于第 3 行的循环中。然后循环每个关系，从第 5 行的循环开始。注意第 6 行到第 8 行中的过滤，我们确保每个关系至少有一个确定的类型，一个源节点和一个目的节点。

第 10 行的`case`结构区分了两种类型的关系:有一个源和多个目的地的关系，以及有一个目的地和多个源的关系。在有多个源或目的地的情况下，使用循环来遍历每一对，并且在每个循环的中心是对`create_directed_relationship`方法的调用。

为了最终用所有这些信息填充数据库，我们实例化`RGraph`并创建如下所示的节点和关系。请注意，我们必须在创建关系之前创建节点。

`rGraph = RGraph.new`

`rGraph.create_nodes`

`rGraph.create_directed_relationships`

所有这些工作的最终结果显示在下面的 Neo4j 窗口中。

![Neo4j Database Representation](img/c0c67f4c7868d278ff6db72dd85c9f22.png "Neo4j Database Representation")

在谈到最后一点之前，先评估一下我们到目前为止所做的工作。我们设计了一个数据库，并用 Ruby hash 表示数据。然后，我们使用 Ruby REST API 将所有这些信息填充到图形数据库中。现在，该问数据库一些问题了。

## 查询重要的商业信息

在 B2B 环境中工作的销售人员对理解其目标客户的层级结构非常感兴趣。熟悉层级结构的唯一方法是深入研究并直接与经理会面。

这个特定的数据库通过`HAS_MET_WITH`关系保存销售人员和他们的目标客户中的个人之间的会议。因此，区域经理想提出以下问题:在我们的目标客户中，我们*没有*见过哪些经理？更具体地说，区域经理想要一份尚未联系的经理名单、公司名称和负责的客户经理。

为了简单起见，我们将在 OurCompany，Inc .工作的客户经理简称为`am`。类似地，我们将目标客户的每个经理缩写为`tm`。最后，我们将每个目标客户公司简称为`tc`。

Cypher 查询可能如下所示:

```
MATCH (am:Person), (tm:Person), (tc:Company)
WHERE (am {title:"Account Manager"})-[:WORKS_FOR]->(:Company {name:"OurCompany, Inc."})
AND (am)-[:ACCOUNT_MANAGES]->(tc)
AND (tm)-[:WORKS_FOR]->(tc)
AND (tm)-[:MANAGES]->()
AND NOT (am)-[:HAS_MET_WITH]->(tm)
return am.name,tm.name,tc.name;
```

好吧，我承认我在这里作弊了，在没有任何警告的情况下向你抛出了一个`WHERE`条款。与经典 SQL 类似，`WHERE`子句通过向`MATCH`添加一组限定条件来帮助过滤结果。

`WHERE`条款分解如下:

*   客户经理(`am`)通过指定他/她是为我们公司
    `(am {title:"Account Manager"})-[:WORKS_FOR]->(:Company {name:"OurCompany, Inc."})`工作的具有“客户经理”头衔的人来限定

*   目标公司(`tc`)是由客户经理进行账户管理的。
    

*   目标客户经理通过他/她为目标公司(tc)
    `(tm)-[:WORKS_FOR]->(tc)`
    工作的事实来识别，并且，目标客户经理通过他/她管理某人的事实来进一步识别。
    `(tm)-[:MANAGES]->()`
    本例中最后一个括号是空的，因为我们不想指定被管理的是谁；我们只想确保被瞄准的`tm`是一名经理。

*   最后，我们过滤客户经理*没有*与目标客户经理会面的事实。我们用关键词`NOT`来传达负面关系。
    

我们在 Ruby 中编写了一个快速方法来执行这个查询，并以 JSON 结构的形式返回结果:

```
def find_managers_not_met
  query =  'MATCH (am:Person), (tm:Person), (tc:Company)'
  query += 'WHERE (am {title:"Account Manager"})-[:WORKS_FOR]->(:Company {name:"OurCompany, Inc."})'
  query += 'AND (am)-[:ACCOUNT_MANAGES]->(tc)'
  query += 'AND (tm)-[:WORKS_FOR]->(tc)'
  query += 'AND (tm)-[:MANAGES]->()'
  query += 'AND NOT (am)-[:HAS_MET_WITH]->(tm)'
  query += 'return am.name,tm.name,tc.name;'
  c = {
      "query" => "#{query}",
      "params" => {}
  }
  response = RestClient.post @url, c.to_json, :content_type => :json, :accept => :json
  puts JSON.parse(response)
end
```

最终结果显示为 Ruby 哈希，如下所示:

```
{
  "columns"=>["am.name", "tm.name", "tc.name"],
  "data"=>[
    ["Jeff Dudley", "Jesse Hoover", "Acme, Inc."],
    ["Jeff Dudley", "Ralph Green", "Acme, Inc."],
    ["Mike Wells", "Mary Galloway", "Wiley, Inc."],
    ["Vanessa Jones", "George Quincy", "Coyote, Ltd."]
  ]
}
```

您可以将第一个`data`数组解释为，“杰夫·达德利还没有见过杰西·胡佛，他是 Acme 公司的经理。”

## 摘要

图形数据库经过优化，可以处理节点之间的关系。当关键信息是基于关系的时，这个目标极大地加快了数据库查询的速度。Neo4j 是市场上最成熟、最稳定的图形数据库之一。尽管 Neo4j 提供了将数据库移植到包括 Ruby 在内的各种主要语言的库，但是也有可能——并且相当容易——简单地使用 REST API 来代替处理库。这需要熟悉 Neo4j 的 Cypher 查询语言。幸运的是，Cypher 相当容易学习，将这些查询直接放在 REST API 调用中也很简单。

## 分享这篇文章