# Ruby 面试问题:问题演练

> 原文：<https://www.sitepoint.com/ruby-interview-questions-problem-walkthroughs/>

![](img/d476c8f4fac2de560e5199e6c742e9af.png)

我们已经谈了一些关于采访 Ruby 和游戏的基本工具。在本文中，我们将考虑一些更难的面试问题，这些问题要求我们运用到目前为止所学的一些概念。这里的重点不仅仅是学习如何实现问题的解决方案，而是通向解决方案的思路。

当你紧张地站在白板前时(顺便说一句，白板编码很糟糕)，你面临的问题可能与你已经看到的东西非常不同，但是如果你真正理解解决方案背后的动机，你会没事的。事不宜迟，我们来看第一个问题。

## 最近的祖先

这是第一个问题:

> 给定二叉树中的两个节点，找出它们的最低共同祖先。

其中最低共同祖先是两个给定节点共同作为祖先的第一个节点(即，具有最小距离的节点)。好吧，让我们好好想想这件事。

假设我们正在处理节点 N1 和 N2。如果想要找到最低的共同祖先，我们可以想象将 N1 和 N2 的祖先列表上移，并检查他们是否匹配。然而，以什么顺序向上到达祖先(例如，通过所有 N1 的第一个或 N2 的第一个或间歇地这样做)是一个问题，特别是如果 N1 在树的最“左”侧，而 N2 在最右侧。让我们试着从另一个角度来思考这个问题。

假设我们已经找到了解，即最低的共同祖先是节点 R，那么，R 需要满足什么性质？它必须有 N1 的后裔，也必须有 N2 的后裔。如果只有一个或两个都不为真，那么我们知道该节点不可能是最少的共同祖先。此外，R 必须是树中具有该属性的最低的节点。查看求解条件给了我们一个想法:如果我们遍历树，检查每个节点是否有 N1 和 N2 作为我们遍历的一部分的后代，并返回(即通过我们的递归冒泡)满足该属性的最低节点。只有一个问题:如果 N2 是 N1 的孩子(或后代)之一呢？为了解决这个问题并为递归提供一个基础案例，我们检查当前节点是 N1 还是 N2，如果是，它必须是最低的共同祖先！

这种解释可能看起来有点混乱和“手动波动”,但代码使它变得更加清晰:

```
def get_lca(root, n1, n2)
  return nil if root == nil
  return root if root == n1 || root == n2
  left_lca = get_lca(root.left, n1, n2)
  right_lca = get_lca(root.right, n1, n2)
  return root if (left_lca != nil and right_lca != nil)
  if left_lca != nil then return left_lca else return right_lca end
end 
```

我们可以用一棵高度为 3 的简单树来测试这一点:

```
grandchild1 = TreeNode.new(4, nil, nil)
grandchild2 = TreeNode.new(5, nil, nil)
grandchild3 = TreeNode.new(6, nil, nil)
grandchild4 = TreeNode.new(7, nil, nil)
child1 = TreeNode.new(2, grandchild1, grandchild2)
child2 = TreeNode.new(3, grandchild3, grandchild4)
root = TreeNode.new(1, child1, child2)
puts get_lca(root, grandchild1, grandchild2).value
=> 2
puts get_lca(root, grandchild3, grandchild4).value
=> 3
puts get_lca(root, grandchild1, grandchild3).value
=> 1 
```

我们来分解一下算法。如果根是零，那么这意味着我们通过递归或者初始调用得到了一棵空树。如果根是`nil`，N1 和 N2 不可能有最低的共同祖先(我们假设它们是非零的，因为如果它们是零，这个问题就没有明确的答案)。

然后，我们有我们的“第二”基本情况:如果根是 N1 或 N2 之一，我们返回根。这是因为这种情况意味着 N1 或 N2 必须是另一个(即 N2 或 N1)的祖先。如果我们还没有完成，那么我们搜索左边和右边的子树。如果有非零返回值，这意味着在子树中找到了 N1 或 N2(或两者的祖先)。如果左边的子树包含 N1，右边的子树包含 N2，那么当前的根节点*必须*是 LCA(尝试使用归纳论证来说服自己)。如果其中只有一个不为零，那么这一边一定是正确的 LCA。

看到这种方法为什么有效的归纳证明也是有益的。我们已经处理了它的一些部分，所以它的轮廓将有助于澄清算法背后的概念。我们的目标是表明`get_lca`实际上返回节点 N1 和 N2 的最低共同祖先。我们还会假设 N1 和 N2 不一样。这是一个很容易解决的问题，但只会让争论变得更复杂，所以我们会避免它。考虑高度为 2 的树的基本情况，包括根节点。很容易验证，无论 N1 和 N2 被放在这个树的哪个位置(作为子节点或其中一个作为根节点)，`get_lca`都将返回正确的最低共同祖先。再者，`get_lca`还有另一个属性。如果这棵树只包含 N1 和 N2 中的一个，那么`get_lca`将返回这棵树包含的那个。

所以，让我们假设这两个属性对于高度为`h`的树都成立。然后，考虑一棵高度为`h+1`且根节点为`root`的树。我们有三个案子。如果根节点是 N1 和 N2 中的一个，那么我们就找到了答案。如果 N1 和 N2 在两个不同的子树上，我们处理高度为`h`的子树上的调用`get_lca`，它应该返回 N1 和 N2(即不是`nil`)，我们根据需要返回根。最后，如果 N1 和 N2 在同一个子树中，那么只有一个`get_lca`将返回非零值，并且那一定是正确的最低共同祖先，因为我们已经为高度为`h`的子树假设了这个属性。那证明`get_lca`是正确的！

对归纳证明有一个概念的意义，并不是当有人提到“归纳”这个词的时候，你可以把它倒背如流。但是，它应该有助于巩固您对算法如何工作以及为什么工作的理解。花时间去理解它，直到证据本身自然而然地出现。我们正在利用的关键属性是，如果 N1 和 N2 都出现在给出的树中，那么`get_lca`实际上会返回正确的 LCA，但是如果 N1 和 N2 中只有一个出现，那么它也会返回哪个出现。

## 合并排序列表

有点不同的是:

> 合并两个排序的整数链表。

通常，先用一个例子，然后再用一个通用策略是一个好主意。我们来考虑一下`[4, 5, 9, 11]`和`[6, 7, 8, 12]`。目标是设计一种方法，它采用这两个链表(即它们的头节点)并返回一个合并的排序列表的头节点。让我们称名单的头头为`head1`和`head2`。`4`是我们自`4 < 6`以来新形成的名单中的第一个元素。因此，我们必须跟踪合并列表的头部——姑且称之为`res`。使`res`等于`head1`，使其包含`4`的值。现在怎么办？

我们仍然有一个看起来非常相似的问题:试图合并列表`[5, 9, 11]`和`[7, 8, 12]`。既然是不同输入的同一个问题，我们可以用递归！只需递归地传递`head1.next`和`head2`！这将返回一个新的列表头，我们将把它钉在`res`的末尾。

这个例子中的算法实际上非常简单。假设给了我们`head1`和`head2`。如果其中一个是`nil`，我们只返回另一个，因为如果其中一个是空的，我们没有合并要做。如果没有，比较两个头的数值。把小的那个叫做`small_head`，大的那个叫做`big_head`。然后，递归合并`small_head.next`和`big_head`，并将其添加到结果列表的末尾。让我们用代码演示一下:

```
def merge_sorted(head1, head2)
  if head1 == nil
    return head2
  elsif head2 == nil
    return head1
  end

  small_head = (head1.value < head2.value) ? head1 : head2
  big_head = (head1.value >= head2.value) ? head1 : head2
  res = small_head
  res.next_node = merge_sorted small_head.next_node, big_head
  return res
end 
```

代码很好地反映了描述(甚至变量名！)所以挺好理解的。你应该试着建立一个归纳的论点来解释为什么这行得通。注意，归纳参数比我们为`get_lca`构造的参数简单，因为我们只关心`merge_sorted`的一个属性:它正确合并排序链表的事实。我们可以测试代码:

```
stack1 = Stack.new
stack1.push 11
stack1.push 9
stack1.push 5
stack1.push 4

stack2 = Stack.new
stack2.push 12
stack2.push 8
stack2.push 7
stack2.push 6

puts stack1.head
=> [4, 5, 9, 11]
puts merge_sorted(stack1.head, stack2.head)
=> [4, 5, 6, 7, 8, 9, 11, 12] 
```

注意，我们使用上一篇文章中的`Stack`类来创建列表。如果你愿意，欢迎你用一堆`Node`对象来构建它。这里有一个有趣的问题:原始列表会发生什么变化？让我们检查一下:

```
puts merge_sorted(stack1.head, stack2.head)
puts stack1.head
=> [4, 5, 6, 7, 8, 9, 11, 12] 
```

如果您运行这个，您将看到列表没有被保留。这是因为我们正在就地合并列表。你应该和你的面试官讨论就地修改的优点和缺点，并据此进行。为了保留原来的列表，我们要做的就是创建一个新的`res`节点，而不是让它等于`small_node`。

## 二分搜索法树

让我们来看看一个更简单但相当普遍的问题:

> 在二叉查找树中插入一个节点，同时保持二叉查找树的属性。

您可能还记得，二叉查找树是满足以下属性的树:每个节点都有一个大于或等于其所有左侧后代且小于或等于其所有右侧后代的值。

既然我们可以在问题中看到“二进制”和“树”，我们可能应该考虑递归地这样做。给定要插入的节点(姑且称之为 N)和树的根(称之为 R)，检查 N 的值是否大于 R 的值。如果是这样，那么我们知道 N 可以放在右边的子树中。如果没有，可以放在左子树中。让我们来实现它:

```
def insert_in_bst(root, node)
  return node if root == nil
  if node.value > root.value
    if root.right == nil
      root.right = node
    else
      insert_in_bst root.right, node
    end
  else
    if root.left == nil
      root.left = node
    else
      insert_in_bst root.left, node
    end
  end
end 
```

我们所做的就是遍历树，一旦找到我们的节点可以“适合”的地方(即，它不会太大或太小，从而满足二叉查找树属性)，就插入它。试着想想这是否会一直产生一个平衡的二叉查找树。如果不是，我们怎么能写一个这样的算法呢？

## 包装它

希望这篇关于面试问题的回顾能帮助你用 Ruby 找到下一份工作。下一次我们将继续讨论一个相当深奥但有时很有用的数据结构:Bloom filter。

## 分享这篇文章