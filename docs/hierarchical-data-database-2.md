# 在数据库中存储分层数据，第 2 部分

> 原文：<https://www.sitepoint.com/hierarchical-data-database-2/>

## 改进的前序树遍历

现在，让我们看看另一种存储树的方法。递归可能很慢，所以我们宁愿不使用递归函数。我们还想尽量减少数据库查询的数量。最好是，每个活动只有一个查询。

我们将从水平放置圣诞树开始。从根节点(' Food ')开始，在它的左边写一个 1。顺着树走到‘水果’，在旁边写一个 2。这样，你沿着树的边走(遍历)，同时在每个节点的左右两边写一个数字。最后一个数字写在“食物”节点的右边。在此图像中，您可以看到整个编号树，以及一些指示编号顺序的箭头。

![1105_numbering](img/5a2197ececca223c001a27e68a8d2dea.png)

我们将这些数字称为左和右(例如，“食物”的左值是 1，右值是 18)。如您所见，这些数字表明了每个节点之间的关系。因为“红色”有数字 3 和 6，所以它是 1-18“食物”节点的后代。同样的，我们可以说左值大于 2，右值小于 11 的所有节点都是 2-11“果实”的后代。树结构现在存储在左值和右值中。这种遍历树并计数节点的方法被称为“改进的前序树遍历”算法。

在我们继续之前，让我们看看这些值在我们的表中是什么样子的:

![1105_table2](img/554e77cf1eba469762539ba22e2abb46.png)

请注意，单词“左”和“右”在 SQL 中有特殊的含义。因此，我们必须使用' lft '和' rgt '来标识列。还要注意，我们不再真正需要“parent”列。我们现在有了 lft 和 rgt 值来存储树结构。

***取回树***

如果您想使用一个包含左值和右值的表来显示树，您首先必须标识出您想要检索的节点。例如，如果您想要“Fruit”子树，您必须只选择左值在 2 和 11 之间的节点。在 SQL 中，这将是:

```
SELECT * FROM tree WHERE lft BETWEEN 2 AND 11;
```

返回:

![1105_table3](img/a55836075c797001e340a956b42b52b1.png)

这就是:一个查询中的一整棵树。为了像显示递归函数一样显示该树，我们必须在该查询中添加一个 ORDER BY 子句。如果您在表格中添加和删除行，您的表格可能不会按正确的顺序排列。因此，我们应该根据行的左侧值对行进行排序。

```
SELECT * FROM tree WHERE lft BETWEEN 2 AND 11 ORDER BY lft ASC;
```

剩下的唯一问题是缩进。

为了显示树结构，子节点应该比父节点缩进一些。我们可以通过保存一堆正确的值来做到这一点。每次从一个节点的子节点开始，都将该节点的正确值添加到堆栈中。您知道该节点的所有子节点的右值都小于父节点的右值，因此通过将当前节点的右值与堆栈中最后一个右节点的右值进行比较，您可以看到是否仍在显示该父节点的子节点。显示完一个节点后，从堆栈中删除它的右值。如果你计算堆栈中的元素，你将得到当前节点的级别。

```
<?php  

function display_tree($root) {  

    // retrieve the left and right value of the $root node  

    $result = mysql_query('SELECT lft, rgt FROM tree '.  

                           'WHERE title="'.$root.'";');  

    $row = mysql_fetch_array($result);  

    // start with an empty $right stack  

    $right = array();  

    // now, retrieve all descendants of the $root node  

    $result = mysql_query('SELECT title, lft, rgt FROM tree '.  

                           'WHERE lft BETWEEN '.$row['lft'].' AND '.  

                           $row['rgt'].' ORDER BY lft ASC;');  

    // display each row  

    while ($row = mysql_fetch_array($result)) {  

        // only check stack if there is one  

        if (count($right)>0) {  

            // check if we should remove a node from the stack  

            while ($right[count($right)-1]<$row['rgt']) {  

                array_pop($right);  

            }  

        }  

        // display indented node title  

        echo str_repeat('  ',count($right)).$row['title']."n";  

        // add this node to the stack  

        $right[] = $row['rgt'];  

    }  

}  

?>
```

如果您运行这段代码，您将得到与上面讨论的递归函数完全相同的树。我们的新函数可能会更快:它不是递归的，只使用两个查询。

***路径到一个节点***

有了这个新算法，我们还必须找到一种新的方法来获得到特定节点的路径。要获得这个路径，我们需要该节点的所有祖先的列表。

对于我们的新表结构，这真的不需要太多工作。例如，当您查看 4-5“Cherry”节点时，您会看到所有祖先的左侧值都小于 4，而所有右侧值都大于 5。要获取所有祖先，我们可以使用以下查询:

```
SELECT title FROM tree WHERE lft < 4 AND rgt > 5 ORDER BY lft ASC;
```

注意，就像我们前面的查询一样，我们必须使用 ORDER BY 子句对节点进行排序。该查询将返回:

```
+-------+

| title |

+-------+

| Food  |

| Fruit |

| Red   |

+-------+
```

我们现在只需连接这些行，就可以得到通往“Cherry”的路径。

***多少子孙***

如果你给我一个节点的左右值，我可以用一点数学告诉你它有多少个后代。

随着每个后代将节点的右侧值增加 2，后代的数量可以通过以下公式计算:

```
descendants = (right â€“ left - 1) / 2
```

通过这个简单的公式，我可以告诉你，2-11 '水果'节点有 4 个后代节点，8-9 '香蕉'节点只是一个子节点，而不是父节点。

**Go to page:** [1](/hierarchical-data-database) | [2](/hierarchical-data-database-2/) | [3](/hierarchical-data-database-3/)

## 分享这篇文章