# Ruby 面试问题:链表和哈希表

> 原文：<https://www.sitepoint.com/ruby-interview-questions-linked-lists-and-hash-tables/>

![](img/d476c8f4fac2de560e5199e6c742e9af.png)

今年秋天，我花了一些时间在这个行业的不同地方进行面试。几乎所有打算这么做的人都知道，所有公司都会问基本上相同的问题。然而，它们在难度和范围上有所不同。面试经历的核心是算法和数据结构，即标准的、一年级的、本科生的东西。虽然我们可以花一整天的时间来确定这是否是衡量程序员能力的一个好方法(几乎可以肯定不是，尤其是对于那些主要业务是开发非常简单的 iPhone 应用的公司来说)，但事实是，学习玩这个游戏比逃避它更好(当然也更有利可图)。

大多数面试准备材料似乎不是用 Java 就是用 C++，主要是因为它们无处不在，以及它们不会“做太多”的事实，也就是说，大多数抽象都很单薄。但是，没有理由为什么一个 Rubyist 不能用我们选择的工具完成他/她的面试。在本文中，我们将使用 Ruby 解决一些难度适中的面试问题。大多数时候，更难的面试问题并不难，因为它们涉及非常复杂的代码；相反，他们只是需要更多的思考和一些洞察力。所以，通过弄清楚如何在 Ruby 中做简单的事情，你也应该着手解决困难的问题。

## 关于风格的一个注释

本文不是关于如何编写最聪明的 Ruby。这个想法是编写清晰易懂的代码，这通常比使用 Ruby 语法中所有可能的技巧更重要(尤其是在面试环境中)。因此，这里的代码尽可能简单明了。

## 链接列表

链表可能是面试中最常见的结构(与哈希表的激烈竞争)。)因此，熟悉链表是一个好主意。虽然有许多其他来源[1]来描述链表背后的概念，但基本思想是链表由一堆节点(即数据块)组成，每个节点包含一个到下一个节点的链接。有时，我们不得不使用双向链表，其中每个节点包含一个到下一个节点以及前一个节点的链接。

在解决第一个问题之前，让我们先弄清楚如何实现链表数据结构。这很简单:

```
class Node
  attr_accessor :value, :next_node 

  def initialize(value, next_node)
    @value = value
    @next_node = next_node
  end
end 
```

我们所做的只是维护每个节点的值和对下一个节点的引用。让我们先解决最简单的问题:

```
Given a node, print the linked list that begins with that node. 
```

这很简单。我们从给定的节点开始当前节点，打印出值(或者，将其添加到字符串中)，然后将当前节点移动到该节点的`@next_node`属性中。这是用 Ruby 写的:

```
 class Node
    ...
    def to_s 
      curr_node = self
      res = "["
      while curr_node.next_node != nil
        res = res + curr_node.value.to_s + ", " 
        curr_node = curr_node.next_node
      end
      res = res + curr_node.value.to_s + "]"
    ...
  end 
```

不是最漂亮的代码，但它是有效的。我们可以测试一下:

```
 head = Node.new 8, nil
  snd = Node.new 7, nil
  head.next_node = snd
  puts head 
```

这给了我们`[8,7].`好的，让我们做一些稍微复杂一点的事情:

```
Reverse a linked list given its head. 
```

这是一个非常普遍的问题，几乎没有人再使用它了。解决方案并不立即显而易见，但一旦你听了无数遍，它就会变得显而易见。基本上，我们递归地解决这个问题。给定一个节点，我们首先检查该节点是否是列表的最后一个。如果是，那么我们就立即返回(即`[6]`的倒数就是`[6]`)。如果没有，将下一个节点传递给我们的反向函数。这将反转列表并将我们传入的节点放在反转列表的末尾。然后，将当前节点放在列表的“下一个”上。这有点令人困惑，因为当前节点的 next 实际上是在反转列表的末尾结束的，所以当前节点的 next 是我们想要放置当前节点的地方。在代码中检查它:

```
def reverse_list(curr)
  return curr if curr == nil or curr.next_node == nil

  next_node = curr.next_node
  new_head = reverse_list(curr.next_node)
  next_node.next_node = curr
  curr.next_node = nil
  return new_head
end 
```

代码非常简单。“诡计”来了`next_node.next_node = curr`。因为我们知道`curr`的下一个节点将在反转列表的末尾，我们只需将`curr`添加到`curr`的下一个节点的末尾。再来看另一个问题。

```
Implement a stack using a linked list. 
```

首先，我们先明确一下什么是栈。基本上，它就像一堆书放在你的桌子上。你可以把东西放在最上面(这叫做`push`)，你可以把你放在上面的最后一本书拿下来(这叫做`pop`)。用链表实现堆栈有很多方法，我们将选择其中一种。

我们将跟踪栈顶作为链表的头。然后，我们想把东西推到栈顶，我们将简单地改变链表的头，并把新头的下一个节点指向旧头。如果我们想弹出内容，我们只需移动头(我们将让垃圾收集器处理旧头的释放)。让我们来看看:

```
class Stack
  attr_reader :head

  def initialize
    @head = nil
  end

  def push(value)
    new_head = Node.new value, @head
    @head = new_head
  end

  def pop
    @head = @head.next_node
  end
end 
```

如您所见，我们所做的只是在 push 上创建一个头部，并将头部移动到 pop 上的下一个节点。我们可以测试一下:

```
stack = Stack.new
stack.push 3
stack.push 5
stack.push 7
stack.pop
stack.push 8
print stack.head 
```

不过，这里我们忽略了一些重要的东西。如果在空栈上调用`pop`会发生什么？你应该和你的面试官讨论这样的边缘情况，并想出处理它们的方法(比如抛出一个异常)。

## 散列表

哈希表是另一种数据结构，为了在面试中胜出，你需要掌握它。在 Ruby 中，哈希表是`Hash`的实例。尽管您可能对哈希表的内部知识(例如通用哈希、链接等)了解不多。)，了解这些东西在某些时候可能会派上用场。几乎所有的算法教科书都会给你这些信息。

不像链表，我们不需要自己的哈希表实现，因为它们是 Ruby 库的一部分，而且只有少数人关心你是否可以从头开始实现哈希表。让我们来看一个简单的问题:

```
Remove duplicates in an array of numbers. 
```

我们是这样做的。我们将每个数字作为一个键放在哈希表中，然后在最后返回这些键。如果我们遇到一个我们已经见过的数字，那没什么大不了的，因为这意味着我们已经把它放到哈希表中了。

```
def remove_duplicates(list)
  set = {}
  list.each do |el|
    set[el] = 1
  end
  set.keys
end 
```

那么，在这里使用哈希表有什么意义呢？该表允许我们在固定的时间内检查我们之前是否已经见过一个元素。允许这段代码在线性时间内运行。好了，接下来的事情稍微复杂一点:

如果两个字符串由相同的字母(每个字母的频率相同)组成，则它们被视为变位词，尽管它们的顺序可能不同。写一个函数来判断一个字符串是否是另一个字符串的变位。

提出这个问题的另一种方式是，我们试图确定两个字符串中每个字母的频率是否相同。我们可以很容易地用哈希表计算频率。我们只是迭代字符串，并添加每个字母的值。然后，我们进行比较，看两个频率是否相同。这是用 Ruby 写的:

```
def get_freq(str)
  freq = Hash.new(0)
  str.each_char do |chr|
    freq[chr] += 1
  end
  freq
end

def check_anagram(str1, str2)
  return get_freq(str1) == get_freq(str2)
end 
```

这里唯一不寻常的一点是`Hash.new(0)`。这会创建一个散列，其中任何键的“缺省”值都是`0`。这可能看起来有点琐碎和不重要，但这对于几乎所有与哈希表相关的面试问题都非常有用，因为你会经常使用哈希表来计算频率。

## 包装它

这是关于采访鲁比的一小段。虽然我们到目前为止讨论的内容都很简单，但是当你有一个面试官紧盯着你的时候，把最基本的东西搞砸是多么的容易，这是非常令人惊讶的。在下一期中，我们将讨论二叉树和 LRU 缓存的实现。

## 分享这篇文章