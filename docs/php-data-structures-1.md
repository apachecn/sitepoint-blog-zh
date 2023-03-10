# PHP 开发人员的数据结构:堆栈和队列

> 原文：<https://www.sitepoint.com/php-data-structures-1/>

数据结构，或者说*抽象数据类型* (ADT)，是一种模型，它由一组可以在自身上执行的操作定义，并且受到这些操作效果的约束的限制。它在可以对底层数据做什么和如何做之间建立了一堵墙。

我们大多数人在日常生活中都熟悉堆栈和队列，但是超市队列和自动售货机与数据结构有什么关系呢？让我们找出答案。在本文中，我将向您介绍两种基本的抽象数据类型——堆栈和队列——它们源于日常使用。

## 大量

在通常的用法中，一堆东西通常是分层排列的，例如，你桌子上的一堆书，或者学校餐厅里的一堆托盘。按照计算机科学的说法，堆栈是一个具有特定属性的顺序集合，因为放在堆栈上的最后一个对象将是第一个被移除的对象。这种属性通常被称为*后进先出*，或者**后进先出**。糖果、薯片和香烟自动售货机的工作原理相同；搁架上装载的最后一件物品首先被分发。

抽象地说，堆栈是一个线性的项目列表，其中对列表的所有添加(一个“推送”)和删除(一个“弹出”)都被限制在一个末端，即定义为(堆栈的)“顶部”。定义堆栈的基本操作是:

*   **init**–创建堆栈。
*   **push**–将一个项目添加到堆栈的顶部。
*   **弹出**–移除添加到堆栈顶部的最后一个项目。
*   **顶部**–查看堆栈顶部的项目，但不移除它。
*   **is empty**–返回堆栈是否不再包含项目。

还可以将堆栈实现为具有最大容量。如果堆栈已满，并且没有足够的槽来接受新的实体，就称之为*溢出*—“堆栈溢出”一词由此而来。同样，如果在空堆栈上尝试弹出操作，则会发生“堆栈下溢”。

知道我们的堆栈是由 LIFO 属性和一些基本操作定义的，特别是 push 和 pop，我们可以很容易地使用数组实现堆栈，因为数组已经提供了 push 和 pop 操作。

下面是我们的简单堆栈的样子:

```
<?php
class ReadingList
{
    protected $stack;
    protected $limit;

    public function __construct($limit = 10) {
        // initialize the stack
        $this->stack = array();
        // stack can only contain this many items
        $this->limit = $limit;
    }

    public function push($item) {
        // trap for stack overflow
        if (count($this->stack) < $this->limit) {
            // prepend item to the start of the array
            array_unshift($this->stack, $item);
        } else {
            throw new RunTimeException('Stack is full!'); 
        }
    }

    public function pop() {
        if ($this->isEmpty()) {
            // trap for stack underflow
	      throw new RunTimeException('Stack is empty!');
	  } else {
            // pop item from the start of the array
            return array_shift($this->stack);
        }
    }

    public function top() {
        return current($this->stack);
    }

    public function isEmpty() {
        return empty($this->stack);
    }
}
```

在这个例子中，我使用了`array_unshift()`和`array_shift()`，而不是`array_push()`和`array_pop()`，所以堆栈的第一个元素总是在顶部。您可以使用`array_push()`和`array_pop()`来维护语义一致性，在这种情况下，堆栈的第 n 个元素成为顶部。这两种方式都没有区别，因为抽象数据类型的全部目的是从数据的实际实现中抽象出数据的操作。

让我们向堆栈中添加一些项目:

```
<?php
$myBooks = new ReadingList();

$myBooks->push('A Dream of Spring');
$myBooks->push('The Winds of Winter');
$myBooks->push('A Dance with Dragons');
$myBooks->push('A Feast for Crows');
$myBooks->push('A Storm of Swords'); 
$myBooks->push('A Clash of Kings');
$myBooks->push('A Game of Thrones');
```

要从堆栈中移除某些项目:

```
<?php
echo $myBooks->pop(); // outputs 'A Game of Thrones'
echo $myBooks->pop(); // outputs 'A Clash of Kings'
echo $myBooks->pop(); // outputs 'A Storm of Swords'
```

让我们看看栈顶是什么:

```
<?php
echo $myBooks->top(); // outputs 'A Feast for Crows'
```

如果我们移除它呢？

```
<?php
echo $myBooks->pop(); // outputs 'A Feast for Crows'
```

如果我们增加一个新项目呢？

```
<?php
$myBooks->push('The Armageddon Rag');
echo $myBooks->pop(); // outputs 'The Armageddon Rag'
```

您可以看到堆栈是基于后进先出的原则运行的。最后添加到堆栈中的东西首先被移除。如果您继续弹出项目，直到堆栈为空，您将得到一个堆栈下溢运行时异常。

```
PHP Fatal error:  Uncaught exception 'RuntimeException' with message 'Stack is empty!' in /home/ignatius/Data Structures/code/array_stack.php:33
Stack trace:
#0 /home/ignatius/Data Structures/code/example.php(31): ReadingList->pop()
#1 /home/ignatius/Data Structures/code/array_stack.php(54): include('/home/ignatius/...')
#2 {main}
  thrown in /home/ignatius/Data Structures/code/array_stack.php on line 33
```

哦，你好……PHP 友好地提供了一个堆栈跟踪，显示了异常之前和之前的程序执行调用堆栈！

## SPLStack

SPL 扩展提供了一组标准数据结构，包括 SplStack 类(PHP5 >= 5.3.0)。我们可以使用如下的 SplStack 实现相同的对象，尽管要简洁得多:

```
<?php
class ReadingList extends SplStack
{
}
```

`SplStack`类实现了比我们最初定义的多一些的方法。这是因为`SplStack`是作为双向链表实现的，它提供了实现可遍历堆栈的能力。

链表本身是另一种抽象数据类型，是用于表示特定序列的对象(节点)的线性集合，其中集合中的每个节点都维护指向集合中下一个节点的指针。最简单的形式，链表看起来像这样:

![dstructure1-01](img/cce4ae441417bf76943e45af58cfc0eb.png)

在双向链表中，每个节点有两个指针，分别指向集合中的下一个和上一个节点。这种类型的数据结构允许双向遍历。

![dstructure1-02](img/ec72af6e8c067fd7fded5cfd4d1d2a86.png)

标有叉号(X)的节点表示空节点或标记节点，表示遍历路径的终点(即路径终止符)。

由于`ReadingList`被实现为`SplStack`，我们可以向前(自顶向下)*和向后(自底向上)遍历堆栈。`SplStack`的默认遍历模式是 LIFO:*

```
<?php
// top-down traversal
// default traversal mode is SplDoublyLinkedList::IT_MODE_LIFO|SplDoublyLinkedList::IT_MODE_KEEP
foreach ($myBooks as $book) {
    echo $book . "n"; // prints last item first!
}
```

要以相反的顺序遍历堆栈，我们只需将迭代器模式设置为 FIFO(先进先出):

```
<?php
// bottom-up traversal
$myBooks->setIteratorMode(
    SplDoublyLinkedList::IT_MODE_FIFO|SplDoublyLinkedList::IT_MODE_KEEP
);
foreach ($myBooks as $book) {
    echo $book . "n";  // prints first added item first
}
```

## 行列

如果你曾经在超市收银台排队，那么你会知道第一个排队的人最先得到服务。在计算机术语中，队列是另一种抽象数据类型，它基于先入先出或 T2 先进先出的原则运行。库存也在先进先出的基础上进行管理，特别是如果这些物品是易腐的。

定义队列的基本操作是:

*   **init**–创建队列。
*   **入队**–将项目添加到队列的“末端”(尾部)。
*   **出列**–从队列的“前面”(头部)移除一个项目。
*   **is empty**–返回队列是否不再包含项目。

因为`SplQueue`也是使用双向链表实现的，所以 top 和 pop 的语义在这个上下文中是相反的。让我们将我们的`ReadingList`类重新定义为一个队列:

```
<?php
class ReadingList extends SplQueue
{
}

$myBooks = new ReadingList();

// add some items to the queue
$myBooks->enqueue('A Game of Thrones');
$myBooks->enqueue('A Clash of Kings');
$myBooks->enqueue('A Storm of Swords');
```

`SplDoublyLinkedList`还实现了`ArrayAccess`接口，因此您也可以将项目作为数组元素添加到`SplQueue`和`SplStack`中:

```
<?php
$myBooks[] = 'A Feast of Crows';
$myBooks[] = 'A Dance with Dragons';
```

要删除队列前面的项目:

```
<?php
echo $myBooks->dequeue() . "n"; // outputs 'A Game of Thrones'
echo $myBooks->dequeue() . "n"; // outputs 'A Clash of Kings'
```

`enqueue()`是`push()`的别名，但是注意`dequeue()`不是`pop()`的别名；`pop()`在队列的上下文中有不同的含义和功能。如果我们在这里使用了`pop()`，它将从队列的末端(尾部)移除项目，这违反了 FIFO 规则。

类似地，要查看队列前面的内容，我们必须使用`bottom()`而不是`top()`:

```
<?php
echo $myBooks->bottom() . "n"; // outputs 'A Storm of Swords'
```

## 摘要

在本文中，您已经看到了如何在编程中使用 stack 和 queue 抽象数据类型。这些数据结构是抽象的，因为它们是由可以在自身上执行的操作定义的，从而在其实现和底层数据之间创建了一堵墙。

这些结构也受到这种操作的影响:只能从堆栈的顶部添加或移除项，并且只能从队列的前面移除项，或者将项添加到队列的后面。

图片由 [Alexandre Dulaunoy](http://www.flickr.com/photos/31797858@N00/8631389697/) 通过 Flickr

## 分享这篇文章