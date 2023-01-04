# 在数据库中存储分层数据，第 3 部分

> 原文：<https://www.sitepoint.com/hierarchical-data-database-3/>

***自动完成树的遍历***

既然您已经看到了使用这个表可以做的一些方便的事情，那么是时候学习如何自动创建这个表了。虽然这是一个很好的练习，第一次用一棵小树，我们真的需要一个脚本来为我们做所有这些计数和树行走。

让我们写一个脚本，将邻接表转换成修改过的前序树遍历表。

```
<?php   

function rebuild_tree($parent, $left) {   

    // the right value of this node is the left value + 1   

    $right = $left+1;   

    // get all children of this node   

    $result = mysql_query('SELECT title FROM tree '.   

                           'WHERE parent="'.$parent.'";');   

    while ($row = mysql_fetch_array($result)) {   

        // recursive execution of this function for each   

        // child of this node   

        // $right is the current right value, which is   

        // incremented by the rebuild_tree function   

        $right = rebuild_tree($row['title'], $right);   

    }   

    // we've got the left value, and now that we've processed   

    // the children of this node we also know the right value   

    mysql_query('UPDATE tree SET lft='.$left.', rgt='.   

                 $right.' WHERE title="'.$parent.'";');   

    // return the right value of this node + 1   

    return $right+1;   

}   

?>
```

这是一个递归函数。您应该以`rebuild_tree('Food',1);`开始，然后该函数检索“Food”节点的所有子节点。

如果没有孩子，它设置它的左值和右值。左值给定，1，右值就是左值加 1。如果有子节点，这个函数会重复，并返回最后一个右边的值。然后，该值被用作“Food”节点的正确值。

递归使得这个函数很难理解。然而，这个函数实现的结果与我们在本节开始时手动实现的结果相同。它遍历树，为它看到的每个节点添加一个。运行该函数后，您将看到左右值仍然相同(快速检查:根节点的右值应该是节点数的两倍)。

***添加节点***

我们如何向树中添加一个节点？有两种方法:可以保留表中的父列，然后重新运行`rebuild_tree()`函数——一个简单但不优雅的函数；或者，您可以更新新节点右侧所有节点的左值和右值。

第一种选择很简单。使用邻接表方法进行更新，使用改进的前序树遍历算法进行检索。如果想要添加一个新节点，只需将它添加到表中并设置父列。然后，您只需重新运行`rebuild_tree()`函数。这很简单，但是对于大树来说不是很有效。

添加和删除节点的第二种方法是更新新节点右侧所有节点的左值和右值。让我们看一个例子。我们想添加一种新的水果，一个“草莓”，作为最后一个节点和“红色”的子节点。首先，我们必须腾出一些空间。“红色”的正确值应该从 6 更改为 8，7-10“黄色”节点应该更改为 9-12 等。更新“红色”节点意味着我们必须给所有大于 5 的左右值加 2。

我们将使用以下查询:

```
UPDATE tree SET rgt=rgt+2 WHERE rgt>5;   

UPDATE tree SET lft=lft+2 WHERE lft>5;
```

现在我们可以添加一个新的节点“草莓”来填充新的空间。这个节点有左 6 和右 7。

```
INSERT INTO tree SET lft=6, rgt=7, title='Strawberry';
```

如果我们运行我们的`display_tree()`函数，我们将看到新的‘草莓’节点已经成功地插入到树中:

```
Food   

  Fruit   

    Red   

      Cherry   

      Strawberry   

    Yellow   

      Banana   

  Meat   

    Beef   

    Pork
```

***缺点***

首先，改进的前序树遍历算法似乎很难理解。它肯定没有邻接表方法简单。然而，一旦你习惯了左和右属性，很明显你可以用这种技术做几乎所有你可以用邻接表方法做的事情，而且改进的前序树遍历算法要快得多。更新树需要更多的查询，这比较慢，但是检索节点只需要一个查询。

## 结论

现在，您已经熟悉了在数据库中存储树的两种方式。虽然我对改进的前序树遍历稍有偏好，但在您的特定情况下，邻接表方法可能更好。我将让你自己判断。

最后一点注意:正如我已经说过的，我不建议您使用节点的标题来引用该节点。您确实应该遵循数据库规范化的基本规则。我没有使用数字标识，因为那样会降低示例的可读性。

***延伸阅读***

数据库向导 Joe Celko:
[http://search database . tech target . com/tip/1，289483，sid13_gci537290，00.html](http://searchdatabase.techtarget.com/tip/1,289483,sid13_gci537290,00.html)

另外两种处理分层数据的方法:
[http://www . evolt . org/article/Four _ ways _ to _ work _ with _ hierarchical _ data/17/4047/index . html](http://www.evolt.org/article/Four_ways_to_work_with_hierarchical_data/17/4047/index.html)

Xindice，“原生 XML 数据库”:
[http://xml.apache.org/xindice/](http://xml.apache.org/xindice/)

递归的解释:
[http://www . strath . AC . uk/IT/Docs/c course/subsection 3 _ 9 _ 5 . html](http://www.strath.ac.uk/IT/Docs/Ccourse/subsection3_9_5.html)

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com/)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如 [PHP & MySQL 初学者网络开发](https://learnable.com/courses/php-mysql-web-development-for-beginners-13)。

**Go to page:** [1](/hierarchical-data-database) | [2](/hierarchical-data-database-2/) | [3](/hierarchical-data-database-3/)

## 分享这篇文章