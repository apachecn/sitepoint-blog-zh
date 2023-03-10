# Ruby 面试问题:LRU 缓存和二叉树

> 原文：<https://www.sitepoint.com/ruby-interview-questions-lru-cache-and-binary-trees/>

![](img/d476c8f4fac2de560e5199e6c742e9af.png)

正如我们上次在前后谈到的[，整个行业中大多数软件工程职位的面试问题都与算法和数据结构有关。通过一点实践，用 Ruby 解决这些问题是可能的，而且相当令人愉快。我们已经介绍了一些基础知识:链表和散列表。这一次，我们将深入一点。我们将用 Ruby 实现一个 LRU 缓存(并解释这是什么),并研究二叉树。](https://www.sitepoint.com/ruby-interview-questions-linked-lists-and-hash-tables/)

## LRU 高速缓存

假设我们想要构建一个项目缓存，并且这个缓存只能保存固定数量的项目。这就引出了一个问题:当缓存满了，我们想插入一个条目时，会发生什么呢？回答这个问题的一个常见方法是最近最少使用(LRU)驱逐策略。基本上，这意味着我们删除(即驱逐)缓存中最近最少使用的项目，以便为新项目腾出空间。

问题是:我们如何实现一个高效的 LRU 缓存？缓存必须能够做几件事情。首先，我们必须能够用相关的键将东西插入其中。其次，我们应该能够使用一个条目的键来获取它。

这是一个极其常见的面试问题，也是一个在最初几轮中仍然会被问到的问题。它需要两个概念的结合才能正确。LRU 缓存通常被实现为一个哈希表和一个双向链表(您也可以使用一个八叉树，但这并不常见)。它是这样工作的。哈希表的键由缓存的键组成，值实际上是双向链表中的节点。我们总是跟踪这个列表的开头和结尾。当我们想从这个缓存中获取一个值时，我们在散列表中查找那个键，并从双向链表中返回相关的节点。此外，我们将该节点从列表中的任何位置移动到列表的最后。这确保了最近最少使用的项目总是在列表的最前面。因此，当我们想在缓存中插入一个新的条目，而缓存已满时，我们只需从列表的前面弹出一个条目。

要在 Ruby 中实现这一点，我们需要一个双向链表。谢天谢地，这很容易实现:

```
class Node
  attr_accessor :value, :next_node, :prev_node 

  def initialize(value, prev_node, next_node)
    @value = value
    @prev_node = prev_node
    @next_node = next_node
  end
end 
```

我们只是跟踪下一个节点和上一个节点。现在，开始实现我们的缓存。如前所述，我们需要跟踪几件事情:列表的头部、列表的尾部以及缓存中的条目数量。

```
 class LRU
    attr_accessor :head, :tail, :num_items, :max_items
    def initialize(max_items)
      @max_items = max_items
      @table = {}
      @head = nil
      @tail = nil
      @num_items = 0
    end
  end 
```

现在，让我们弄清楚如何实现`set(key, value)`方法。

```
class LRU
  ...
  def set(key, value)
    @num_items += 1
    if @num_items > @max_items
      @head = @head.next_node
    end

    new_node = Node.new value, @tail, nil
    @head = new_node if tail == nil 
    @tail.next_node = new_node if @tail != nil
    @tail = new_node
    @table[key] = new_node 
  end
  ...
end 
```

如果我们一步一步地看，代码相当简单。首先，我们检查缓存是否已满。如果是这样，我们必须从双向链表的头部驱逐，以便为下一个节点腾出空间。然后，我们用给定的值创建一个新节点，并粘贴到列表的末尾。最后，我们用给定的键将这个新节点(现在是双向链表的尾部)添加到表中。`get`操作涉及一些稍微复杂一点的代码:

```
class LRU
  ...
  def get(key)
    res = @table[key]
    return res if res.next_node == nil

    if res.prev_node != nil
      res.prev_node.next_node = res.next_node
    else
      @head = res.next_node
      @head.prev_node = nil
    end

    @tail.next_node = res
    res.next_node = nil
    res.prev_node = @tail
    @tail = res
  end
  ...
end 
```

`get`方法的目标是返回与键相关联的节点，并将该节点移动到列表的最末尾。如果关联的节点已经在列表的末尾，那么我们返回(这就是为什么我们检查`res`的`next_node`)。如果我们选择的节点在列表的最开始，那么我们必须移动头部本身。如果它不在列表的开头，我们就简单地把它从列表的中间“挑选”出来。最后，将节点移动到列表的新尾节点。就是这样！我们可以很容易地测试缓存实现:

```
lru = LRU.new(3)
lru.set('a', 1)
lru.set('b', 2)
lru.set('c', 3)
puts lru.head
lru.get('a')
puts lru.head
lru.get('b')
puts lru.head
lru.get('c')
puts lru.head
lru.set('d', 8)
lru.set('e', 9)
puts lru.head 
```

确保您真正理解正在发生的事情的一个好方法是尝试预测这段代码将打印出什么。如果你理解了整个哈希表和双向链表的结构，那么在面试当天实现 LRU 缓存或类似的东西应该没有问题。

## 二叉树

另一个面试最爱是卑微的二叉树。这个概念非常简单。我们有两个链接，而不是像单链表那样每个节点都有一个链接。这两个导致了节点的“子节点”。你应该能猜到这看起来像什么 Ruby:

```
class TreeNode
  attr_accessor :left, :right, :value
  def initialize(value, left, right)
    @value = value
    @left, @right = left, right
  end
end 
```

好吧，酷。如果我们想打印这个东西呢？事实证明，有几种方法可以做到这一点。有“前序”、“按序”和“后序遍历”。前序是我们先看节点，然后看左子树，再看右子树。按顺序是先左子树，再节点，再右子树。最后，后序是左子树，右子树，然后是节点。让我们来看看如何实现有序遍历。我们不构建表示这种遍历的迭代器，而是打印出节点值。这是:

```
class TreeNode
  ...
  def print_inorder
    @left.print_inorder unless @left.nil?
    puts @value
    @right.print_inorder unless @right.nil?
  end  
  ...
end 
```

这种方法完全符合定义。只要子树不为空，我们将遍历它们并打印中间“当前”节点的值。我们可以测试这个:

```
left_child = TreeNode.new(1, nil, nil)
right_child = TreeNode.new(3, nil, nil)
root = TreeNode.new(2, left_child, right_child)
root.print_inorder 
```

这应该在单独的行上打印一、二和三。好吧，酷。我们把它实现为一个可以使用`each`的枚举器怎么样？看看这个:

```
def inorder(&block)
  @left.inorder(&block) unless @left.nil?
  yield @value
  @right.inorder(&block) unlessif @right.nil?
end 
```

这段代码有点特殊，因为它用`&block`显式地传递这个块。我们必须这样做，因为我们在`inorder`中递归。这意味着每个递归步骤都应该能够获得块。这东西非常容易使用:

```
root.inorder do |value| 
  puts value
end 
```

实现前序和后序遍历只是涉及到在`yield`语句周围移动，所以我们将关注其他东西。

另一个最受欢迎的采访是二叉查找树。这是一棵二叉树，它有一个特殊的，非常有用的属性。对于每个节点，与该节点关联的值大于或等于其左侧子树中的所有值，小于或等于其右侧子树中的所有值。这引出了一个自然的问题(也是一个经常被问到的问题):我们如何确定一棵特定的二叉树是否是二叉查找树(BST)？

几乎所有二叉树问题的关键是递归思维。每个节点都有一个左子树和一个右子树(尽管它们可能是空的)，对于左右子树中的每个节点也是如此。所以，让我们来思考这个特殊的问题。

首先，从基础案例开始。如果我们有一个左右子树都为空的单个节点(即叶节点)，那么我们保证该单个节点是 BST，因为它空空地满足 BST 属性。

好，现在，考虑一个只有左子树的节点，我们保证左子树是 BST。然后，只要我们考虑的节点的值大于或等于它的左子树的根，我们就有一个 BST。

从这里不难看出，只要特定节点的左右子树是 BST，并且所考虑的节点在左右子树的值之间，那么整个树就是 BST。让我们将这个想法转化为代码:

```
 def bst?
  return false if (@left != nil and @value < @left.value)
  return false if (@right != nil and @value > @right.value)
  left_bst = @left == nil ? true : @left.bst? 
  right_bst = @right == nil ? true : @right.bst?
  return (left_bst and right_bst)
end 
```

令人欣慰的是，一旦你头脑中有了递归方法，就很容易转换成代码。我们测试当前节点是否违反了 BST 定义，然后递归地检查任一子树是否违反了 BST 定义。我们可以对此进行测试(使用之前定义的`root`):

```
puts root.bst?
left_child.value = 3
puts root.bst? 
```

稍微研究一下这个应该会让您更好地理解递归是如何工作的，以及 BST 定义的价值。

## 结论

所以，我们到了。我们已经通过几个常见的面试问题研究了 Ruby 中的 LRU 缓存和二叉树。这应该给你一个用 Ruby(或者任何其他语言，如果你只是想学习如何解决类似面试的问题)准备面试的良好开端。在下一篇文章中，我们将研究一些更难的问题，这些问题涉及来自不同数据结构和算法的概念。

## 分享这篇文章