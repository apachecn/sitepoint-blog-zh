# PHP 开发人员的数据结构:图表

> 原文：<https://www.sitepoint.com/data-structures-4/>

在我之前的一篇文章中，我向您介绍了[树数据结构](https://www.sitepoint.com/data-structures-2/ "Data Structures for PHP Devs: Trees")。现在我想探索一个相关的结构——图表。图在现实世界中有许多应用，例如网络优化、流量路由和社会网络分析。谷歌的 PageRank，脸书的 Graph Search，亚马逊和网飞的推荐都是图形驱动应用的例子。

在本文中，我将探讨两个使用图的常见问题——最少跳数和最短路径问题。

图是一种数学构造，用于对键/值对之间的关系进行建模。一个图由一组*顶点*(节点)和连接它们的任意数量的*边*(线)组成。这些边可以是有向的，也可以是无向的。有向边简单来说就是两个顶点之间的边，边 A→B 不被认为与 B→A 相同，无向边没有方位或方向；边 A-B 相当于 B-A。我们上次学习的树结构可以被认为是一种无向图，其中每个顶点通过一条简单的路径与至少一个其他顶点相连。

图也可以加权或不加权。加权图或网络是一种为其每条边分配权重或成本值的图。加权图通常用于确定两点之间的最佳路径、最便捷路径或最低“成本”路径。谷歌地图的行驶方向就是一个使用加权图的例子。

## 最少的跳数

图论的一个常见应用是寻找任意两个节点之间的最少跳数。和树一样，可以用两种方式遍历图:深度优先或宽度优先。我们在上一篇文章中讨论了深度优先搜索，所以让我们来看看广度优先搜索。

考虑下图:

![dg-graphs01](img/5b8b317054a101d50d704889439564c6.png)

为了简单起见，让我们假设这个图是*无向的*——也就是说，任何方向的边都是相同的。我们的任务是找到任意两个节点之间的最少跳数。

在广度优先搜索中，我们从根节点(或任何指定为根的节点)开始，一层一层地沿着树向下搜索。为了做到这一点，我们需要一个队列来维护未访问节点的列表，以便我们可以在每一级后回溯和处理它们。

一般的算法是这样的:

```
1\. Create a queue
2\. Enqueue the root node and mark it as visited
3\. While the queue is not empty do:
  3a. dequeue the current node
  3b. if the current node is the one we're looking for then stop
  3c. else enqueue each unvisited adjacent node and mark as visited

```

但是，如果不先遍历图，我们如何知道哪些节点是相邻的，更不用说是未访问的了？这就给我们带来了如何对图形数据结构建模的问题。

### 代表图表

通常有两种方法来表示一个图:要么作为邻接矩阵，要么作为邻接表。上面用邻接表表示的图形如下所示:

![dg-graphs02](img/4c373cac0d15d939e108dad823057b47.png)

用矩阵表示时，图形看起来像这样，其中 1 表示边在两个顶点之间的“关联度”:

![dg-graphs03](img/a8978a541133cc44079e3452255cfc53.png)

邻接表更节省空间，特别是对于大多数顶点对不相连的稀疏图，而邻接矩阵有助于更快的查找。最终，表示的选择将取决于可能需要什么类型的图形操作。

让我们用邻接表来表示这个图:

```
<?php
$graph = array(
  'A' => array('B', 'F'),
  'B' => array('A', 'D', 'E'),
  'C' => array('F'),
  'D' => array('B', 'E'),
  'E' => array('B', 'D', 'F'),
  'F' => array('A', 'E', 'C'),
);
```

现在，让我们看看一般的广度优先搜索算法的实现是什么样子的:

```
<?php
class Graph
{
  protected $graph;
  protected $visited = array();

  public function __construct($graph) {
    $this->graph = $graph;
  }

  // find least number of hops (edges) between 2 nodes
  // (vertices)
  public function breadthFirstSearch($origin, $destination) {
    // mark all nodes as unvisited
    foreach ($this->graph as $vertex => $adj) {
      $this->visited[$vertex] = false;
    }

    // create an empty queue
    $q = new SplQueue();

    // enqueue the origin vertex and mark as visited
    $q->enqueue($origin);
    $this->visited[$origin] = true;

    // this is used to track the path back from each node
    $path = array();
    $path[$origin] = new SplDoublyLinkedList();
    $path[$origin]->setIteratorMode(
      SplDoublyLinkedList::IT_MODE_FIFO|SplDoublyLinkedList::IT_MODE_KEEP
    );

    $path[$origin]->push($origin);

    $found = false;
    // while queue is not empty and destination not found
    while (!$q->isEmpty() && $q->bottom() != $destination) {
      $t = $q->dequeue();

      if (!empty($this->graph[$t])) {
        // for each adjacent neighbor
        foreach ($this->graph[$t] as $vertex) {
          if (!$this->visited[$vertex]) {
            // if not yet visited, enqueue vertex and mark
            // as visited
            $q->enqueue($vertex);
            $this->visited[$vertex] = true;
            // add vertex to current path
            $path[$vertex] = clone $path[$t];
            $path[$vertex]->push($vertex);
          }
        }
      }
    }

    if (isset($path[$destination])) {
      echo "$origin to $destination in ", 
        count($path[$destination]) - 1,
        " hopsn";
      $sep = '';
      foreach ($path[$destination] as $vertex) {
        echo $sep, $vertex;
        $sep = '->';
      }
      echo "n";
    }
    else {
      echo "No route from $origin to $destinationn";
    }
  }
}
```

运行下面的例子，我们得到:

```
<?php
$g = new Graph($graph);

// least number of hops between D and C
$g->breadthFirstSearch('D', 'C');
// outputs:
// D to C in 3 hops
// D->E->F->C

// least number of hops between B and F
$g->breadthFirstSearch('B', 'F');
// outputs:
// B to F in 2 hops
// B->A->F

// least number of hops between A and C
$g->breadthFirstSearch('A', 'C');
// outputs:
// A to C in 2 hops
// A->F->C

// least number of hops between A and G
$g->breadthFirstSearch('A', 'G');
// outputs:
// No route from A to G
```

如果我们使用堆栈而不是队列，遍历就变成了深度优先搜索。

## 寻找最短路径

另一个常见问题是寻找任意两个节点之间的最佳路径。早些时候我提到谷歌地图的驾驶方向作为例子。其他应用包括规划旅行路线、道路交通管理和火车/公共汽车调度。

解决这个问题的最著名的算法之一是由一位 29 岁的计算机科学家 Edsger W. Dijkstra 于 1959 年发明的。一般来说，Dijkstra 的解决方案包括检查从源节点开始的所有可能的顶点对之间的每条边，并维护具有最短总距离的顶点的更新集，直到到达或未到达目标节点，无论哪种情况都可以。

有几种方法可以实现这个解决方案，事实上，在 1959 年之后的几年里，对 Dijkstra 的原始算法进行了许多改进——使用了 MinHeaps、PriorityQueues 和 Fibonacci 堆。一些改进了性能，而另一些设计用于解决 Dijkstra 解决方案中的缺点，因为它只适用于正加权图(其中权重是正值)。

以下是一个(正)加权图的示例:

![dg-graphs04](img/4fb9a29c95b584c4926e57f0060bd2ff.png)

我们可以将此图表示为邻接表，如下所示:

```
<?php
$graph = array(
  'A' => array('B' => 3, 'D' => 3, 'F' => 6),
  'B' => array('A' => 3, 'D' => 1, 'E' => 3),
  'C' => array('E' => 2, 'F' => 3),
  'D' => array('A' => 3, 'B' => 1, 'E' => 1, 'F' => 2),
  'E' => array('B' => 3, 'C' => 2, 'D' => 1, 'F' => 5),
  'F' => array('A' => 6, 'C' => 3, 'D' => 2, 'E' => 5),
);
```

下面是一个使用 PriorityQueue 维护所有“未优化”顶点列表的实现:

```
<?php
class Dijkstra
{
  protected $graph;

  public function __construct($graph) {
    $this->graph = $graph;
  }

  public function shortestPath($source, $target) {
    // array of best estimates of shortest path to each
    // vertex
    $d = array();
    // array of predecessors for each vertex
    $pi = array();
    // queue of all unoptimized vertices
    $Q = new SplPriorityQueue();

    foreach ($this->graph as $v => $adj) {
      $d[$v] = INF; // set initial distance to "infinity"
      $pi[$v] = null; // no known predecessors yet
      foreach ($adj as $w => $cost) {
        // use the edge cost as the priority
        $Q->insert($w, $cost);
      }
    }

    // initial distance at source is 0
    $d[$source] = 0;

    while (!$Q->isEmpty()) {
      // extract min cost
      $u = $Q->extract();
      if (!empty($this->graph[$u])) {
        // "relax" each adjacent vertex
        foreach ($this->graph[$u] as $v => $cost) {
          // alternate route length to adjacent neighbor
          $alt = $d[$u] + $cost;
          // if alternate route is shorter
          if ($alt < $d[$v]) {
            $d[$v] = $alt; // update minimum length to vertex
            $pi[$v] = $u;  // add neighbor to predecessors
                           //  for vertex
          }
        }
      }
    }

    // we can now find the shortest path using reverse
    // iteration
    $S = new SplStack(); // shortest path with a stack
    $u = $target;
    $dist = 0;
    // traverse from target to source
    while (isset($pi[$u]) && $pi[$u]) {
      $S->push($u);
      $dist += $this->graph[$u][$pi[$u]]; // add distance to predecessor
      $u = $pi[$u];
    }

    // stack will be empty if there is no route back
    if ($S->isEmpty()) {
      echo "No route from $source to $targetn";
    }
    else {
      // add the source node and print the path in reverse
      // (LIFO) order
      $S->push($source);
      echo "$dist:";
      $sep = '';
      foreach ($S as $v) {
        echo $sep, $v;
        $sep = '->';
      }
      echo "n";
    }
  }
}
```

如您所见，Dijkstra 的解决方案只是广度优先搜索的一种变体！

运行以下示例会产生以下结果:

```
<?php
$g = new Dijkstra($graph);

$g->shortestPath('D', 'C');  // 3:D->E->C
$g->shortestPath('C', 'A');  // 6:C->E->D->A
$g->shortestPath('B', 'F');  // 3:B->D->F
$g->shortestPath('F', 'A');  // 5:F->D->A 
$g->shortestPath('A', 'G');  // No route from A to G
```

## 摘要

在这篇文章中，我介绍了图论的基础知识，表示图的两种方法，以及图论应用中的两个基本问题。我已经向您展示了如何使用广度优先搜索来查找任意两个节点之间的最少跳数，以及如何使用 Dijkstra 的解决方案来查找任意两个节点之间的最短路径。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章