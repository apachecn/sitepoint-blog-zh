# 超越 Rails 抽象:深入了解数据库内部

> 原文：<https://www.sitepoint.com/beyond-rails-abstractions-dive-database-internals/>

![a dive into database internals](img/1b56b9ea975e63115272aa1e89a9470b.png)

抽象是奇妙的东西。看一看 [Rails](http://rubyonrails.org/) :我们可以用相对较少的代码行实现大量的功能。例如，为了快速启动和运行，我不一定需要知道很多关于我的数据库是如何实现的。

这种与核心功能隔离的不利之处在于，开发人员没有学到他们可能应该学到的所有东西。就在上周，我通过删除索引解决了一个缓慢的查询，因为有人不理解基数或索引是如何工作的。你能自己实现一个数据库吗？让我们深入研究另一项我们认为理所当然的技术！

## 索引是如何工作的？

当查询数据库时，我们期望相对较快的查找时间。如果您要实现一个函数来获取两个数字之间的每个值，您可能会尝试检查每个值是否符合标准，但是这对于大型数据集来说效率非常低，因为它是以线性时间(`O(n)`)执行的。您需要的是一种按照指定顺序存储记录的方法，以加快访问速度。像[二叉查找树](https://en.wikipedia.org/wiki/Binary_search_tree) (BST)这样的结构就是这样做的，通过确保任何给定的记录大于其左子树中的所有记录，但小于其右子树中的所有项目。这使得我们的搜索时间大大缩短，并且是数据库索引的支柱。

BST 让我们失望的地方是我们很少需要特定的记录。相反，我们更希望所有记录都符合特定的标准。一个简单的解决方案是再次检查每条记录，这将我们带回线性时间，或`O(n)`。我们需要的是一种按顺序存储所有记录的方法，遍历该列表，然后在满足条件时停止(在本例中，是查询的上限)。非常适合这个任务的数据结构是 [B+树](https://en.wikipedia.org/wiki/B%2B_tree)，它将访问时间降低到`O(m + log n)`。

在研究这篇文章时，我被这种方法的低效所震惊。表面上，所需要的只是对有序列表的快速索引——这一任务非常适合于犯罪性地未被充分利用且鲜为人知的数据结构，即[跳过列表](https://en.wikipedia.org/wiki/Skip_list)。事实证明，虽然 B+树通常比其他数据结构中的等价操作需要更多的 CPU 工作，但它们擅长于需要从磁盘到内存的更少的传输，这是一个自然的瓶颈。这篇[谷歌博客文章](https://opensource.googleblog.com/2013/01/c-containers-that-save-memory-and-time.html)提供了一些背景来解释这是为什么。此外，B+树在最坏情况下也是高效的——当数据集较大时，这是一个巨大的因素。

## 为什么使用太多索引不是一个好主意？

让我们想象在数据库中删除一行。这意味着我们还需要删除 B+树中相应的节点。您不能只删除节点，因为我们依赖于数据结构的有序性质，这意味着我们除了确保树的整体高度尽可能低之外，还需要保留顺序。为了解决这个问题，我们的数据库引擎将利用智能插入和删除方法，但是这将花费对数(`O(log n)`)时间。至关重要的是，每次执行插入或删除操作时，任何应用索引的字段都必须重新平衡树。

具有很少可能选项的字段(如布尔型、三值型等)。)据说基数低。[这里的](https://www.ibm.com/developerworks/data/library/techarticle/dm-1309cardinal/)是一篇很棒的 IBM 文章，它警告对低基数字段进行索引的陷阱，但是它的要点是您应该避免这样做。然而，有趣的是，我以前减少了 80%的查询，只是通过索引一个真/假列，所以一如既往地，profile profile profile！

## 查询分析器

让我们以一个标准查询为例，跟随它的旅程:

```
SELECT id, email, admin, credits FROM users WHERE credits > 2 + 3; 
```

与[编译器](https://www.sitepoint.com/lexers-parsers-and-asts-oh-my-how-ruby-executes/)类似，解析器将文本转换成解析树:

```
>>> SELECT email, admin, credits FROM users WHERE credits > 2 + 3                                                         
LOG:  parse tree:
DETAIL:     {QUERY
  :commandType 1
  :querySource 0
  :canSetTag true
  :utilityStmt <>
  :resultRelation 0
  :hasAggs false
  :hasWindowFuncs false
  :hasSubLinks false
  :hasDistinctOn false
  :hasRecursive false
  :hasModifyingCTE false
  :hasForUpdate false
  :hasRowSecurity false
  :cteList <>
  :rtable (
    {RTE
    :alias <>
    :eref
      {ALIAS
      :aliasname users
      :colnames ("id" "created_at" "updated_at" "email" "encrypted_password
      " "confirmation_token" "remember_token" "admin" "" "credits")
      }
    :rtekind 0
    :relid 27043
    :relkind r
    :tablesample <>
    :lateral false
    :inh true
    :inFromCl true
    :requiredPerms 2
    :checkAsUser 0
    :selectedCols (b 12 16 18)
    :insertedCols (b)
    :updatedCols (b)
    :securityQuals <>
  Time: 0.001s 
```

对于那些在家玩的人来说，我正在使用优秀的 [pgcli](http://pgcli.com/) 和 [Postgres](https://www.postgresql.org/) 以及以下两个设置:

```
SET client_min_messages=debug1;
SET debug_print_parse=on; 
```

回到查询解析器。在此过程中，它会检查您的输入是否合法。关键词顺序不对？错别字？是查询解析器将错误踢回给您(注意下面例子中的`WHRE`):

```
SELECT email, admin, credits FROM users WHRE credits > 2 + 3;
syntax error at or near "credits" 
```

接下来，查询解析器检查数据库元数据:您的表存在吗？该列是否存在？你要做的手术有可能吗？你有通过数据库许可系统的授权吗？

```
SELECT email, admin, credits FROM users WHERE credits > 'Ostrich';
invalid input syntax for integer: "Ostrich" 
```

如果一切正常，那么查询解析器将把树传递到下一步。

## 查询重写

随着我们的树成功构建，是时候优化它了。查询重写器获取我们的树，并对其应用规则列表。例如，如果您在一个已经有唯一约束的列上放置了一个`DISTINCT`，那么这个`DISTINCT`就会被丢弃。整数计算被替换为常数(我们示例中的 2 + 3 在这里被替换为 5)。这里有许多其他规则，但最终目的是确保查询执行所导致的时钟周期保持在最小。

最终结果是一个查询计划，从理论上讲，它是一个高度优化且高效的 SQL 查询。但是 DB 引擎如何知道哪些是最有效的连接等等？为此，我们需要快速绕道…

## 统计数字

一个好的数据库引擎会收集统计数据。比如一个表中的记录数、一列中有多少不同的值、最小值/最大值等等。这是为了查询优化过程，也是您的查询快速的原因。例如，如果您试图将一个有一百万行的表连接到一个有一百行的表，那么连接记录较少的表会快得多。它实际上比这更深入，计算包含分位数和标准偏差等信息的直方图，但你得到的想法。

没有什么比错误的统计数据更不利于快速查询了。如果查询优化器认为有 200 条记录，而实际上有 2 亿条，并在此基础上计算了直方图和统计数据，这可能是查询执行的分钟数和小时数之差。因此，定期触发统计数据收集非常重要。

## 查询执行器

最后，是时候执行我们的查询了！查询管理器检查它是否有足够的资源(内存和 CPU)，如果允许，就开始执行。首先，它将检查内存中的数据缓存(记住，内存比磁盘快得多)，并触发缓存管理器预取所需的下一个数据块，如查询计划所示。它将丢弃不再需要的数据，您可以通过查看一个叫做[缓冲区/缓存命中率](http://www.craigkerstiens.com/2012/10/01/understanding-postgres-performance/)的东西来检查它是否做得很好。大约 99%是你的目标。

如果这是一个写/更新/删除操作，您的[事务管理器](https://wiki.postgresql.org/wiki/DTM)将在这里保护您的数据完整性，但是因为我们只是做一个简单的读取，剩下的就是数据库的客户端管理器向客户端返回一个漂亮的页面响应。

## 包扎

有很多[很棒的](https://www.postgresql.org/docs/manuals/)资源可以让你更好地理解数据库，还有很多我在这里没有涉及到。但是这应该有望提供足够的概述，帮助您开始知道在进行概要分析时应该寻找什么。最起码，在盲目加指数之前你可能要三思！

## 分享这篇文章