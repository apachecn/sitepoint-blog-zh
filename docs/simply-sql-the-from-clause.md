# 简单的 SQL:FROM 子句

> 原文：<https://www.sitepoint.com/simply-sql-the-from-clause/>

***本文是鲁迪·利姆贝克的《T2》一书的第三章。***

在第 2 章`SELECT`语句的概述中，我们将`SELECT`语句分解成不同的子句，但只是简单地看了一下每个子句。在本章中，我们将从`FROM`子句开始，更详细地了解`SELECT`语句。

`FROM`子句可以很简单，也可以很复杂。然而，在所有情况下，关于`FROM`子句的重要一点是它产生了一个表格结构。这种表格结构被称为`FROM`子句的结果集。您可能还会看到它被称为中间结果集、中间表格结果集或中间表。但是，无论`SELECT`查询是从一个表、多个表还是从其他类似的表格结构中检索数据，结果总是相同的——`FROM`子句生成一个表格结构。

在这一章中，我们将回顾在 web 开发中可能遇到的常见类型的`FROM`子句。

##### 为什么从 FROM 子句开始？

要开始写一个`SELECT`语句，我的策略是暂时跳过`SELECT`子句，先写`FROM`子句。最终，我们需要在`SELECT`子句中输入一些表达式，我们可能还需要使用`WHERE`、`GROUP BY`以及其他子句。但是我们有充分的理由总是从`FROM`条款开始:

*   如果我们弄错了`FROM`子句，SQL 语句将总是返回错误的结果。是`FROM`子句产生了表格结构，这是执行`SELECT`语句中所有其他操作的起始数据集。
*   `FROM`子句是数据库系统解析 SQL 语句时查看的第一个子句。

##### 解析 SQL 语句

每当我们将一条 SQL 语句发送给数据库系统执行时，系统执行的第一个动作称为解析。数据库系统就是这样检查 SQL 语句，看它是否有语法错误。首先，它将陈述分成它的组成子句；然后，它根据每个子句的语法规则检查该子句。与我们预期的相反，数据库系统首先解析`FROM`子句，而不是`SELECT`子句。

例如，假设我们试图运行以下 SQL 语句，其中我们将 *teams* 拼错为 *teans* :

```
SELECT
  id, name
FROM
  **teans** 
WHERE
  conference = 'F'
```

在这种情况下，`FROM`子句引用了一个不存在的表，因此会立即出现语法错误。如果数据库系统首先解析`SELECT`子句，它需要检查数据库中所有表的表定义，寻找可能包含两列 name 和 id 的表定义。事实上，对于一个数据库来说，有几个包含两列 name 和 id 的表是很常见的。混乱可能会接踵而至，数据库需要我们提供更多的信息来知道从哪个表中检索姓名和 id。因此，为什么数据库系统首先解析`FROM`子句，这也是我们考虑的第一个子句。

##### 从一张桌子

我们已经看到了带有单个表的`FROM`子句。在第 1 章“SQL 简介”中，我们看到了`FROM`子句指定了 teams 表:

```
SELECT
  id, name
FROM
  **teams**
```

在第 2 章 SELECT 语句的概述中，我们看到 FROM 子句指定了条目表:

```
SELECT
  title, category
FROM
  **entries**
```

这种形式的 FROM 子句非常简单。必须至少指定一个表格结构，单个表格符合该要求。然而，当我们想要同时从多个表中检索数据时，我们需要开始使用连接。

##### 使用联接从多个表中

联接将两个表联系、关联或组合在一起。一个联接从两个表开始，然后以几种不同的方式之一将它们组合(或联接)在一起，产生一个表格结构(作为联接的结果)。实际上，动词‘加入’非常能描述发生了什么，我们一会儿就会看到。

使用特殊关键字和关键字`JOIN`在`FROM`子句中指定表的连接方式(连接类型)。有几种不同类型的联接，我将简单描述一下，这样您就可以看到它们之间的区别。然后，我们将使用我们的示例应用程序来查看特定的连接示例。

##### 连接类型

联接根据一个称为联接条件的规则，将两个表中的行组合在一起。这将比较两个表的行中的值，以确定应该联接哪些行。

有三种基本的连接类型:

*   用关键字`INNER JOIN`创建的内部连接
*   外部联接有三种形式:
    *   `LEFT OUTER JOIN`
    *   `RIGHT OUTER JOIN`
    *   `FULL OUTER JOIN`

*   交叉连接，用`CROSS JOIN`关键字创建

为了直观地显示连接是如何工作的，我们将使用两个名为 A 和 B 的表，如下所示。

![Tables A and B](img/cee2af855012145cfc02e1ba1c447db0.png)

表 A 和表 B 上的

 **这些表实际上过于简单，因为它们模糊了表名和列名之间的区别。连接条件实际上指定了必须匹配的列。此外，表格只有一列是不常见的。

*不要担心`A`和`B`实际上可能代表什么。他们可能是任何东西。下图中的想法是让您将注意力集中在要联接的行中的值上。表`A`有一个名为 a 的列以及值为 102、104、106 和 107 的行。表`B`有一个名为 b 的列以及值为 101、102、104、106 和 108 的行。*

##### 内部联接

对于内部联接，只返回满足 on 子句中条件的行。内部联接是最常见的联接类型。在大多数情况下，例如下面的示例，ON 子句指定两列必须有匹配的值。在这种情况下，如果一个表(A)的行中的值(A 列的值)等于另一个表(B)的行中的值(B 列的值),则满足联接条件，并且这些行被联接:

```
SELECT
  a, b
FROM
  **A INNER JOIN B
    ON a=b**
```

下图说明了这是如何工作的。

![A INNER JOIN B](img/41592ed80eb53c99edabc7dc4439f2fe.png)

如您所见，当值相等时，`A`中的一行被连接到`B`中的一行。因此，结果集中返回值 102、104 和 106。`A`中的值 107 在`B`中没有匹配项，因此不包含在结果集中。类似地，`B`中的值 101 和 108 在`A`中不匹配，所以它们也不包含在结果集中。如果这样做更容易，您可以认为匹配的行实际上被连接成一个更长的行，然后对其余的`SELECT`语句进行操作。

##### 外部联接

接下来，我们将看看外部连接。外部联接与内部联接的不同之处在于，不匹配的行也可以被返回。因此，大多数人认为外部连接包含不符合连接条件的行。这是正确的，但可能有点误导，因为外部连接确实包括所有匹配的行。典型的外部连接有许多匹配的行，只有少数不匹配。

有三种不同类型的外部联接:左联接、右联接和全联接。我们将从左侧外部连接开始。

##### 左外部联接

对于左外连接，将返回左表中的所有行，而不管它们在右表中是否有匹配的行。哪个是左边的桌子，哪个是右边的桌子？这些只是在`OUTER JOIN`关键词左边和右边提到的表格。例如，在下面的语句中，`A`是左表，`B`是右表，并且在`FROM`子句中指定了左外连接:

```
SELECT
  a, b 
FROM
  **A LEFT OUTER JOIN B 
    ON a=b**
```

下图显示了这种连接的结果。记住–左外连接返回左表中的所有行，以及右表中的匹配行(如果有)。

![A LEFT OUTER JOIN B](img/f22fb6fcc48a1bc60ea7482c4649fcc4.png)

注意来自`A`的所有值都被返回。这是因为`A`是左表。对于在`B`中没有匹配项的 107，我们看到它确实包含在结果中，但是在来自`B`的特定结果行中没有值。目前，可以认为来自`B`的值是缺失的——当然，对于 107 来说是缺失的。

##### 右侧外部联接

对于右外连接，将返回右表中的所有行，而不管它们在左表中是否匹配。换句话说，除了返回右表的所有行之外，右外联接的工作方式与左外联接完全相同:

```
SELECT
 a, b
FROM
  **A RIGHT OUTER JOIN B
    ON a=b**
```

在上面的例子中，`A`仍然是左边的表，`B`仍然是右边的表，因为这是它们相对于`OUTER JOIN`关键字被提及的地方。因此，连接的结果包含表`B`中的所有行，以及表`A`中的匹配行，如果有的话，如图 3.4 所示，“右外连接 B”。

![A RIGHT OUTER JOIN B](img/f0fbc8be08cafe14b982ec5a5fa0a041.png)

右外部联接与左外部联接相反。对于相同位置的相同表—`A`作为左表,`B`作为右表——右外连接的结果与左外连接的结果非常不同。这一次，所有来自`B`的值都被返回。在 101 和 108 在`A`中不匹配的情况下，它们确实包含在结果中，但是在它们来自`A`的特定结果行中没有值。同样，来自`A`的那些值丢失了，但是该行仍然被返回。

##### 完整的外部连接

对于完全外部联接，将返回两个表中的所有行，而不管它们在另一个表中是否匹配。换句话说，完全外连接的工作方式就像左外连接和右外连接一样，只是这一次两个表的所有行都被返回。考虑这个例子:

```
SELECT
  a, b
FROM
  **A FULL OUTER JOIN B
    ON a=b**
```

还是那句话，`A`是左桌，`B`是右桌，虽然这次真的不重要。完全外部联接返回两个表中的所有行，以及另一个表中的匹配行(如果有的话)，如下所示。

![A FULL OUTER JOIN B](img/bd53583f205ab915a6470d407f835889.png)

完全外部联接是左外部联接和右外部联接的组合。(更严格地说，如果你还记得你在学校学过的数学中的集合论，它是左外连接和右外连接结果的并集。)匹配的行当然包括在内，但与任一表都不匹配的行也包括在内。

##### 内部连接和外部连接的区别

外部联接的结果将始终等于两个表之间对应的内部联接的结果加上来自左表、右表或两者的一些不匹配的行，这分别取决于它是左外部联接、右外部联接还是完全外部联接。

因此，左外连接和右外连接之间的区别只是左表的行是否全部返回，是否匹配右表的行，或者右表的行是否全部返回，是否匹配左表的行。

同时，一个完整的外部连接将总是包括来自左外部连接和右外部连接的结果。

##### 交叉连接

对于交叉连接，返回两个表中的每一行，并连接到另一个表的每一行，而不管它们是否匹配。交叉连接的显著特征是它没有 ON 子句，正如您在下面的查询中看到的:

```
SELECT
  a, b
FROM
  **A CROSS JOIN B**
```

![A CROSS JOIN B](img/b20ed650ab7007c8fa626a6603488faf.png)

交叉连接可能非常有用，但非常罕见。它们的目的是产生一个表格结构，包含代表两组值(在我们的例子中，来自两个表的列)的所有可能组合的行，如图 3.6“交叉连接 B”所示；这在生成测试数据或寻找缺失值时非常有用。

##### 旧式联接

还有另一种类型的连接，在`FROM`子句中有一个逗号分隔的表列表，在`WHERE`子句中有必要的连接条件；这种类型的连接有时被称为“旧式”连接，或“逗号列表”连接，或“`WHERE`子句”连接。例如，对于`A`和`B`表，它看起来像这样:

```
SELECT
  a, b
FROM
  A, B
WHERE
  a=b
```

这些旧式联接只能是内部联接；其他连接类型只有在非常专有和混乱的语法下才是可能的，数据库系统供应商自己警告说不推荐使用这种语法。将此与推荐的`INNER JOIN`语法进行比较:

```
SELECT
  a, b
FROM
  A INNER JOIN B
    ON a=b
```

你可以在野外看到这些老式的连接，但是我要提醒你不要自己写。总是使用`JOIN`语法。

回顾一下我们对连接的快速调查，有三种基本的连接类型和总共五种不同的变体:

*   内部连接
*   左外部联接、右外部联接和完全外部联接
*   交叉连接

现在来看一些更现实的例子。

##### 真实世界加入

第 2 章，`SELECT`语句概述介绍了内容管理系统条目表，我们将在下面的查询中继续使用它来演示如何编写连接。图 3.7，“条目表”显示了它的部分内容，但不是全部。例如，缺少内容列。

![The entries table](img/3185b101ec61e487f9604485eb7c5d79.png)

在我们的 CMS 网站中，我们的目标是在网站上为每个类别提供自己的区域，从网站的主菜单和首页链接。科学区域将包含科学类别中的所有条目，幽默区域将包含幽默类别中的所有条目，依此类推，如图 3.8“建议的 CMS 网站结构”所示。为此，每个条目都有一个类别，存储在每行的 category 列中。

![A suggested CMS site structure](img/6aef49d680abcec3d6472008331fc589.png)

主类别页面本身需要的不仅仅是我们在条目表中看到的一个单词类别名称。网站访问者希望了解每个部分的内容，所以我们需要为每个类别取一个更具描述性的名称。但是在站点的什么地方存储它呢？我们可以将较长的名称直接硬编码到网站的每个主要部分页面中。然而，更好的解决方案是将名称保存在数据库中。另一个表可以很好地完成这项工作，因此我们为此创建了 categories 表；我们给它两列——类别和名称——如图 3.9“类别表”所示。

![The categories table](img/63f6d811b82b1f7c31d8279b49ee4581.png)

category 列是 categories 表中每行的键。之所以称之为键，是因为该列中的值是唯一的，用于标识每一行。这是我们将用来连接到条目表的列。我们将在第 10 章“关系完整性”中学习更多关于设计带键表的知识。现在，让我们探索连接 categories 和 entries 表的不同方法。

##### 创建类别表

创建 categories 表的脚本可以在附录 C 的示例脚本中找到，也可以在本书的下载文件 CMS _ 05 _ Categories _ INNER _ JOIN _ entries . SQL 中找到。

##### 内部联接:类别和条目

我们要看的第一种联接类型是内部联接:

```
SELECT
  categories.name, entries.title, entries.created
FROM
  **categories
    INNER JOIN entries
      ON entries.category = categories.category**
```

下图显示了该查询的结果。

![The results of the inner join](img/1f6146c3c5b40fbcb92330d17e3cc4b3.png)

让我们逐个子句地遍历查询，检查它在做什么，同时将查询与它产生的结果进行比较。当然，查询的第一部分是`FROM`子句:

```
FROM
  categories
    INNER JOIN entries
      ON entries.category = categories.category
```

使用关键字`INNER JOIN`将 categories 表连接到 entries 表。`ON`子句指定了连接条件，该条件规定了两个表的行必须如何匹配才能参与连接。它使用点符号(tablename.rowname)来指定 categories 表中的行仅在它们的 category 列中的值相等时才与 entries 表中的行匹配。我们将在本章后面更详细地讨论点符号。

下图详细显示了 categories 表到 entries 表的内部连接如何产生查询结果集。因为这是一个内部连接，categories 表中的每一行都只连接到 entries 表中在各自的类别列中有匹配值的行。

![The inner join in detail](img/d44bb5e3f0283cb791ec862a56b39a3f.png)

*表中的一些条目被隐藏*

entries 表实际上有几个没有显示的附加列:id、updated 和 content。这些列也是可用的，但是为了保持图表简单，省略了它们。事实上，如果包含了内容列，图表会非常混乱，因为它包含多行文本。因为查询中根本没有提到这些列，所以将它们包含在图中可能会使查询变得混乱。一些读者肯定会问，“嘿，这些是从哪里来的？”

*   关于 categories 和 entries 表的行匹配，请注意:
    幽默的 categories 行匹配了两个 entries 行，匹配行的两个实例都在结果中，幽默类别的名称出现了两次。
*   博客的类别行与条目行不匹配。因此，由于这是一个内部联接，因此该类别不会出现在结果中。
*   其他类别行各匹配一个条目行，这些匹配的行出现在结果中。

以稍微不同的方式陈述这些观察结果，我们可以看到 categories 表中的一行不能匹配 entries 表中的任何行、一行或多行。

*一对多关系*

categories 表中的一行和 entries 表中的匹配行之间的关系不止一个方面，这是我们所说的一对多关系的基本特征。每个类别可以有多个条目。

即使给定的类别(博客)可能没有匹配的条目，并且只有一个类别(幽默)有多个条目，类别和条目表之间的关系在结构上仍然是一对多的关系。一旦表中完全填充了动态数据，很可能所有类别都会有许多条目。

可以说，从另一个方向来看这个关系，我们可以看到每个条目只能属于一个类别。这是 entries 表中 category 列只有一个值的直接结果，它只能匹配 categories 表中的一个类别值。然而，不止一个条目可以匹配同一类别，就像我们看到的幽默条目一样。所以一对多关系也是多对一关系。只是取决于正在讨论的关系的方向。

现在，我们已经检查了`FROM`子句，并了解了`INNER JOIN`及其`ON`条件是如何指定如何连接表的，我们可以看看`SELECT`子句:

```
SELECT 
  categories.name
, entries.title
, entries.created
```

如您所料，`SELECT`子句只是指定内部连接结果中的哪些列将包含在结果集中。

##### 前导逗号

注意,`SELECT`子句现在已经被写成每列一行，使用一种称为前导逗号的约定；这将用于分隔列表中第二项和后续项的逗号放在它们所在行的前面。这乍一看可能不寻常，但是语法完全没问题；请记住，新行和空白被 SQL 忽略，就像它们被 HTML 忽略一样。有经验的开发人员可能更习惯在行尾使用逗号，如下所示:

```
SELECT 
  categories.name,
  entries.title,
  entries.created
```

我使用前导逗号作为编码风格约定，以使 SQL 查询更具可读性和可维护性。可读性和可维护性的重要性怎么强调都不为过。例如，看看您能否发现这个假设查询中的两个编码错误:

```
SELECT
  first_name,
  last_name,
  title
  position,
  staff_id,
  group,
  region,
FROM
  staff
```

现在看看你是否能发现这里的编码错误:

```
SELECT
  first_name
, last_name
, title
  position
, pay_scale
, group
, region
,
FROM
  staff
```

查询在列列表中间缺少一个逗号，但在列表末尾有一个不需要的额外逗号。哪个例子中的错误更容易发现？

此外，如果使用键盘在文本编辑器中编辑 SQL，前导逗号更容易处理。有时您需要从`SELECT`子句中移动或删除一列，使用键盘的 Shift 和箭头键选择(突出显示)这一行更容易。类似地，删除最后一列也需要删除前一行的结尾逗号，这很容易忘记。在关键字`FROM`前面的悬空逗号是一个常见的错误，使用前导逗号很难做到这一点。

##### 联接后所有列都可用

在任何连接中，被连接的表的所有列都可以用于`SELECT`查询，即使它们没有被查询使用。让我们再来看看我们的内部联接:

```
SELECT
  categories.name
, entries.title
, entries.created
FROM
  categories
    INNER JOIN entries
      ON entries.category = categories.category
```

在大多数连接查询中，被连接的表通常包含比在`SELECT`子句中提到的更多的列。这里也是如此；entries 表中还有查询中没有提到的其他列。我们没有将它们包括在图 3.11“详细的内部连接”中，只是为了保持图的简单。尽管该图是正确的，但可能会被解释为稍有误导，因为它只显示了查询的结果集，而不是由内部连接生成的表格结构。

下图详细描述了查询的实际处理过程，并显示了由 FROM 子句和内部连接生成的表格结构；它包括两个类别列，每个表一个。这个表格结构，即中间表，是由数据库系统在执行连接时生成的，并为`SELECT`子句临时保存。

![How an inner join is actually processed](img/c7ed4b0a81fdd2ffc9de20cb364abdf4.png)

##### 当在查询中执行连接时

对我们的第一个示例连接查询的分析得出了两个要点:

*   联接产生中间表格结果集；
*   SELECT 子句出现在`FROM`子句之后，对中间结果集进行操作。

在本章开始时，我提到过`FROM`子句是我们提交查询时数据库系统解析的第一个子句。如果没有语法错误，数据库系统将继续执行查询。好吧，原来`FROM`子句也是数据库系统执行的第一个子句。

您可以将连接查询的执行视为以下列方式工作。首先，数据库系统基于在`FROM`子句中指定的连接生成一个中间表格结果集。这包含两个表中的所有列。然后，数据库系统使用`SELECT`子句从这个中间结果集中只选择指定的列，并将它们提取到作为查询结果返回的最终表格结构中。

##### 限定列名

最后，让我们再看一下我们的内部连接查询:

```
SELECT
  **categories**.name
, **entries**.title
, **entries**.created  
FROM
  categories
    INNER JOIN entries
      ON entries.category = categories.category
```

此查询中使用的每个列名都由其表名限定，使用点符号，其中表名在列名之前，表名和列名之间有一个点。

当查询中有多个相同列名的实例时，必须限定列名。(当然，这些将来自不同的表；同一列名在一个表中不可能有多个实例，因为一个表中的所有列都必须有唯一的名称。)如果您没有唯一地标识名称相同但位于不同表中的每一列，您将会收到一个关于不明确名称的语法错误。无论查询是否引用这两个列，这都适用；每一个引用都必须被限定。

当查询中只有一个列名实例时，限定列名就变成可选的了。因此，我们可以编写以下代码，并返回相同的结果集:

```
SELECT
  name
, title
, created
FROM
  categories
    INNER JOIN entries
      ON entries.category = categories.category
```

然而，在这种情况下限定所有的列名是一个好主意，因为当您查看`SELECT`子句时，您并不总是能够分辨出每个列来自哪个表。如果您对查询中涉及的表非常熟悉，这可能会特别令人沮丧，比如当您对他人(甚至是您自己，几个月前)编写的查询进行故障诊断时。

##### 始终限定连接查询中的每一列

即使在一个连接查询中可能不需要限定某些甚至所有的列，限定多表查询中的每一列也是好的 SQL 编码风格的一部分，因为它使查询更容易理解。

在某种程度上，限定列名使得查询不言自明:它清楚地表明了查询在做什么，从而更容易在文档中解释。

##### 表别名

限定列名的另一种方法是使用表别名。表别名是在查询中分配给表的别名。实际上，表别名通常比表名短。例如，下面是使用表别名的相同内部联接:

```
SELECT
  **cat**.name
, **ent**.title
, **ent**.created  
FROM
  categories **AS cat** 
    INNER JOIN entries **AS ent** 
      ON **ent**.category = **cat**.category
```

这里，categories 表被赋予别名 cat，entries 表被赋予别名 ent。你可以自由选择任何你想要的名字；表别名是临时的，仅在查询期间有效。有些人喜欢尽可能使用单个字母作为表的别名，因为这样可以减少查询中的字符数，从而更容易阅读。

使用表别名的唯一注意事项是，一旦为表指定了别名，就不能再使用表名来限定查询中的列；在整个查询过程中，您必须使用一致的别名。然而，一旦查询完成，您就可以自由地通过全名、相同的别名甚至不同的别名来引用原始表；这里的要点是，表别名只在包含它的查询期间定义。

##### 左外部联接:类别和条目

继续我们的连接查询，我们将检查的左侧外部连接查询与我们刚刚讨论的内部连接查询完全相同，除了它使用`LEFT OUTER JOIN`作为连接关键字:

```
SELECT
  categories.name
, entries.title
, entries.created  
FROM
  categories
    **LEFT OUTER JOIN** entries 
      ON entries.category = categories.category
```

下图显示了上述查询的结果。

![The results of the left outer join query](img/0c980b6fa0b7ffd6e2b10ea4b10c1fb2.png)

这个左外部连接查询和前面的内部连接查询之间的唯一区别是在结果集中包含了一个额外的行——用于名为 Log On to My Blog 的类别。因为查询使用了外部联接，所以包含了附加行。具体来说，这是一个左外连接，因此左表 categories 表的所有行都必须包含在结果中。您可能还记得，左边的表就是在`LEFT OUTER JOIN`关键字左边提到的表。下图更详细地显示了连接和选择的过程。

![How a left outer join query is actually processed](img/9473d54613eb28a71ca7dfd5c2818826.png)

为了更明显地看出哪个表是左边的，哪个表是右边的，我们可以编写不带换行符和空格的连接，这样 categories 在这个连接中更明显地是左边的表:

```
FROM **categories LEFT OUTER JOIN entries**
```

让我们再看一下左外连接的结果，因为我需要指出外连接的一个更重要的特征。

##### 左外连接的一个应用:站点地图

查看我们的`LEFT OUTER JOIN`查询结果，很容易看出它们是如何构成 CMS 网站地图的基础的。例如，可以由这些查询结果生成的站点地图的 HTML 可能是:

```
<h2>Gentle Words of Advice</h2> 
<ul> 
  <li>Be Nice to Everybody (2009-03-02)</li> 
</ul> 

<h2>Stories from the Id</h2> 
<ul> 
  <li>What If I Get Sick and Die? (2008-12-30)</li> 
</ul> 

<h2>Log On to My Blog</h2> 

<h2>Humorous Anecdotes</h2> 
<ul> 
  <li>Hello Statue (2009-03-17)</li> 
  <li>Uncle Karl and the Gasoline (2009-02-28)</li> 
</ul> 

<h2>Our Spectacular Universe</h2> 
<ul> 
  <li>The Size of Our Galaxy (2009-04-03)</li> 
</ul> 
```

如果您是一名经验丰富的 web 开发人员，您可能会看到如何使用特定的应用程序语言将查询结果转换为 HTML。

请注意，登录到我的博客类别没有条目，但是包含在结果中(因为它是一个左外部连接)。因此，应用程序逻辑需要检测这种情况，而不是为该类别中的条目生成无序列表(

`NULLs`

##### 外部连接会产生空值

我们的左外连接包括左表中没有右表匹配的行，如图 3.13“左外连接查询的结果”所示。那么博客类别结果行的 title 和 created 列中的值到底是什么呢？记住，这些列来自条目表。

答案是:他们是`NULL`。

`NULL`是 SQL 中的一个特殊值，代表没有值。在左外连接中，对于左表中不匹配的行，来自右表的列在结果集中是`NULL`。这实际上意味着那里没有值，这是有意义的，因为对于左表中的特定行，右表中没有匹配的行。

当谈到使用数据库时，使用是日常生活的一部分。我们第一次遇到 NULL(尽管很短暂)是在第 1 章，SQL 介绍，在一个示例`CREATE TABLE`语句中使用了它，我们将在整本书中再次看到`NULL`。

##### 右外连接:条目和类别

下面的右外连接查询产生的结果与我们刚刚介绍的左连接查询完全相同:

```
SELECT
  categories.name
, entries.title
, entries.created  
FROM
  **entries
    RIGHT OUTER JOIN categories** 
      ON entries.category = categories.category
```

但这怎么可能呢？

希望你已经找到了答案:我改变了表格的顺序！在右外部连接查询中，我写道:

```
FROM **entries** RIGHT OUTER JOIN **categories**
```

在前面的左外连接查询中，我有:

```
FROM **categories** LEFT OUTER JOIN **entries**
```

从这种迂回中得到的教训是，左外部连接和右外部连接是完全等价的，只是哪个表是外部表的问题:哪个表的所有行都包含在结果集中。因此，许多从业者避免编写右外查询，而是通过改变表的顺序将它们转换为左外连接；这样，返回所有行的表总是在左边。对于大多数人来说，左外连接似乎比右外连接更容易理解。

##### 右外连接:类别和条目

如果我没有在前面的右外连接中交换表的顺序会怎么样？假设查询是:

```
SELECT
  categories.name
, entries.title
, entries.created  
FROM
  **categories
    RIGHT OUTER JOIN entries** 
      ON entries.category = categories.category
```

这一次，与我们的第一个左外连接一样，categories 表在左边，entries 表在右边。下图显示了该查询的结果与我们之前的内部连接的结果相同。

![The results of the right outer join query](img/8dd26819eb93dc3b20b00bf92bcfe894.png)

怎么会这样呢？这样是不是更迂回？不，这次不行；原因是因为它是表的实际内容。记住，右外连接返回右表的所有行，不管是否匹配左表中的行。entries 表是正确的表，但是在这个特定的实例中，每个条目都有一个匹配的类别。将返回所有条目，并且没有不匹配的行。

因此，证明右外连接产生与内连接相同的结果并不奇怪，因为它强调了外连接的规则，即返回来自外表的所有行，无论是否有匹配的行。在这种情况下，什么都没有。

要真正看到正确的外部连接，我们需要一个缺少匹配类别的条目。让我们为名为 computers 的新类别在 entries 表中添加一个条目，如下图所示。

![A new addition to the entries table](img/fd09a456f975cfdbe2aaffec5abb14dc.png)

##### 尝试您的 SQL

将这一额外的行添加到条目表中的`INSERT`语句可以在“内容管理系统”一节中找到。

图 3.17，“右外连接查询的结果-第二次”显示，当我们使用新的类别重新运行右外连接查询时，结果是预期的。

![The results of the right outer join query -- take two](img/7d43bcd6e1f4c0ca7598854f136f8ebc.png)

这一次，我们在查询结果中看到不匹配的条目，因为在 categories 表中没有 computers 类别的行。

##### 完全外部联接:类别和条目

我们的下一个示例连接查询是完整的外部连接。正如您所料，完整的外部连接查询语法与我们目前看到的其他连接类型非常相似:

```
SELECT
  categories.name
, entries.title
, entries.created  
FROM
  **categories
    FULL OUTER JOIN entries
      ON entries.category = categories.category**
```

这一次，连接关键字是`FULL OUTER JOIN`，但是一个不幸的错误发生在至少一个常见的数据库系统中。在 MySQL 中，尽管它是标准 SQL，但不支持`FULL OUTER JOIN`,结果是一个语法错误:SQL Error:您的 SQL 语法中有一个错误；查看与您的 MySQL 服务器版本相对应的手册，了解在“`OUTER JOIN`条目`ON` …”附近使用的正确语法

下图显示了在其他支持`FULL OUTER JOIN`的数据库系统中的结果。

![The results of the full outer join query](img/2f245ed38ca65c3e11f420966bea0289.png)

请注意，结果集包括左表和右表中不匹配的行。这是我们前面看到的完全外连接的显著特征；这两个表都是外部表，因此包含了两个表中不匹配的行。正是因为这个原因，全外连接在 web 开发中很少见，因为很少有需要它们的情况。相比之下，内连接和左外连接相当常见。

##### 联合查询

如果您的数据库系统不支持`FULL OUTER JOIN`语法，那么通过稍微复杂一点的查询(称为 union)也可以获得相同的结果。联合查询本身不是联接。然而，大多数人认为联合查询产生的结果是由两个连接或附加在一起的结果集组成的。联合查询仅在非常宽松的意义上执行连接。

让我们来看看一个联合查询:

```
SELECT
  categories.name
, entries.title
, entries.created   
FROM
  categories
    LEFT OUTER JOIN entries 
      ON entries.category = categories.category
**UNION**
SELECT
  categories.name
, entries.title
, entries.created   
FROM
  categories
    RIGHT OUTER JOIN entries     
      ON entries.category = categories.category
```

正如您所看到的，我们在本章前面看到的左外连接和右外连接查询只是用关键字`UNION`连接在一起。一个联合查询由许多结合了`UNION`操作符的`SELECT`语句组成。在这个上下文中，它们被称为子选择，因为它们从属于整个`UNION`查询；它们只是查询的一部分，而不是独立执行的查询。有时它们也被称为子查询，尽管这个术语通常用于更具体的情况，我们很快就会遇到。

执行时，`UNION`操作只是将每个子选择查询产生的结果集组合成一个结果集。图 3.19“联合查询的工作原理”显示了上述示例的工作原理:

我在前面提到过，连接操作可以想象为将一个表中的一行连接到另一个表中的一行的末尾——如果您愿意，也可以称之为水平连接。因此，union 操作就像一个垂直连接——第二个结果集被追加到第一个结果集的末尾。

![How a union query works](img/2cbed22e15716d517cd7a3578f920d55.png)

有趣的是，重复的内容会被删除。您可以很容易地看到重复项——它们是整行，其中每个列值都相同。本例中产生重复的原因是由于两个子选择——左外部连接和右外部连接——从相同的两个表中返回匹配相同连接条件的行。因此，两个子选择都返回匹配的行，从而在中间结果中创建重复的行。只有不匹配的行不会重复。

你可能想知道为什么`UNION`删除重复；答案很简单，它就是这样设计的。这就是`UNION`操作符的工作方式。

##### 联合和联合所有

有时，保留由 union 操作产生的所有行，而不删除重复的行是很重要的。这可以通过使用关键字`UNION ALL`而不是`UNION`来实现。

*   `UNION`删除重复行。每组重复行中只有一行包含在结果集中。
*   `UNION ALL`保留由 union 的子选择生成的所有行，维护重复的行。

`UNION ALL`明显更快，因为搜索重复行以删除它们的需要是多余的。

我们的联合查询删除了重复的行，这意味着上面的联合查询产生的结果与完整的外部连接相同。当然，这个例子就是为了达到这个目的而设计的。

关于联合查询还有很多要说的，但是现在，让我们用一点来结束这一部分:联合查询，像连接查询一样，产生一个表结构作为它们的结果集。

##### 视图

视图是我们可以创建的另一种数据库对象，就像表一样。然而，视图是不真实的，因为它们实际上不存储数据(不像表)。视图是为了便于引用和重用而命名的`SELECT`语句(通常是复杂的语句),可以用于多种目的:

*   他们可以通过提供列别名来定制一个`SELECT`语句。
*   它们可以是由定义中的`SELECT`语句产生的结果集的别名。如果视图中的`SELECT`语句包含多个表之间的连接，那么在对视图进行查询之前，数据库会有效地预先连接这些表。第二个查询看到的只是一个要查询的表。这可能是使用视图最重要的好处。
*   他们可以加强数据库的安全性。数据库的用户可能被限制查看所有的底层表；相反，他们可能只被授权访问视图。典型的例子是 employees 表，其中包含姓名、部门和薪水等列。由于工资的保密性质，很少有人被允许直接访问这样的表；相反，提供了一个排除机密列的特殊视图。

为了进行演示，下面是如何将前面使用的内部连接查询定义为视图:

```
SELECT
  categories.name
, entries.title
, entries.created   
FROM
  categories
    LEFT OUTER JOIN entries 
      ON entries.category = categories.category
**UNION**
SELECT
  categories.name
, entries.title
, entries.created   
FROM
  categories
    RIGHT OUTER JOIN entries     
      ON entries.category = categories.category
```

当然，它不是一个表——视图本身并不实际存储由它的`SELECT`语句产生的结果集。这里视图名的使用是通过执行视图的底层`SELECT`语句，将其结果存储在一个中间表中，并使用该表作为`FROM`子句的结果。以上查询的结果，如下所示。

![Selecting from a view](img/9088c8287a69df8250bdb368cf3f6bc1.png)

这个结果集类似于定义视图的内部连接查询产生的结果集。注意，只返回了两列，因为在其`FROM`子句中使用视图的`SELECT`语句(与定义视图的`SELECT`语句相反)只要求两列。另外，请注意，在视图定义中，名为 category_name 的列别名被分配给了 categories 表的 name 列；这是任何使用视图的`SELECT`语句中必须使用的列名，也是结果集中使用的列名。

视图定义的一个特殊含义是，只有在视图的`SELECT`语句中定义的列才可用于任何使用该视图的查询。尽管 entries 表有一个内容列，但该列对于视图是未知的，如果在使用视图的查询中引用该列，将会生成语法错误。

##### Web 开发中的视图

作为 web 开发人员，视图与我们的日常任务有什么关系？

*   在团队环境中处理大型项目时，您可能只被授权访问视图，而不是基础表。例如，数据库管理员(DBA)可能已经构建了数据库，而您只是在使用它。您可能甚至没有意识到您正在使用视图。这是因为，从语法上讲，表和视图在`FROM`子句中的使用方式完全相同。
*   当您构建自己的数据库时，为了方便起见，您可能希望创建视图。例如，如果您经常需要在站点内的不同页面上显示条目列表及其类别，那么编写`FROM` entries_with_category 要比编写底层连接容易得多。

##### 子查询和派生表

我们从检查`FROM`子句开始这一章，从简单的表到各种类型的连接。我们简要地看到了一个`UNION`查询及其子选择，我们还看到了视图如何使复杂的连接表达式更容易使用。为了结束这一章，我们将快速浏览一下派生表。这里有一个例子:

```
SELECT
  title
, category_name
FROM
  **( SELECT
      entries.title
    , entries.created
    , categories.name AS category_name                     
    FROM
      entries
        INNER JOIN categories 
          ON categories.category = entries.category
  )** AS entries_with_category
```

这里的派生表是圆括号中的整个`SELECT`查询(语法中需要圆括号来分隔包含的查询)。派生表是一种常见的子查询，它是从属于另一个查询(或嵌套在另一个查询中)的查询(很像联合查询中的子选择)。

它看起来也很熟悉，不是吗？该子查询与上一节定义的 entries_with_categories 视图中使用的查询相同。事实上，正如每个视图都需要一个名称一样，每个派生表也必须有一个名称，同样使用`AS`关键字(在最后一行)将名称 entries_with_category 指定为派生表的表别名。考虑到这些相似性，派生表通常也称为内联视图。也就是说，它们定义了一个表格结构(子查询生成的结果集),直接内嵌在 SQL 语句中，而子查询生成的表格结构则用作外部或主查询的`FROM`子句的数据源。

简而言之，任何产生表格结构的东西都可以在`FROM`子句中被指定为数据源。甚至一个我们简单讨论过的`UNION`查询也可以用在`FROM`子句中，如果它被指定为派生表的话；整个`UNION`查询将放在限定派生表的括号中。

派生表在 SQL 中非常有用。我们会在整本书中看到其中的几个。

##### 总结:FROM 子句

在本章中，我们研究了`FROM`子句，以及它如何为`SELECT`语句指定数据源。在`FROM`子句中可以指定许多不同类型的表格结构:

*   单个表格
*   连接的表
*   视图
*   子查询或派生表

最后——这是本书的关键概念之一——不仅`FROM`子句指定了一个或多个从中提取数据的表格结构，而且`FROM`子句的执行结果也是另一个表格结构，称为中间结果集或中间表。一般来说，在数据库系统处理`SELECT`子句之前，首先生成这个中间表。

在第 4 章的`WHERE`子句中，我们将看到如何使用`WHERE`子句过滤由`FROM`子句产生的表格结构。

***希望你喜欢那个样章。如果你想阅读更多来自[的简单 SQL](https://www.sitepoint.com/books/sql1/) ，你可以[下载样本 PDF](https://www.sitepoint.com/books/sql1/samplechapters.php) ，它包含 3 个章节，或者去买这本书！***** 

## **分享这篇文章**