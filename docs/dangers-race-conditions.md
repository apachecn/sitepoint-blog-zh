# 五分钟后比赛条件的危险

> 原文：<https://www.sitepoint.com/dangers-race-conditions/>

### 目录 

*   [比赛条件](#raceconditions)
*   [一个简单的例子](#asimpleexample)
*   [增量如何产生竞争条件](#howincrementcreatesaracecondition)
*   [一个不那么简单的例子](#anotsosimpleexample)
*   [结论](#conclusions)
*   [评论](#comments)

*竞争条件*是多线程代码的一个不良属性。它表示程序的结果依赖于特定的操作顺序，但是底层平台(在 Java 的情况下，JVM)并不保证这种顺序。因此，结果通常会在运行中波动，因为它取决于不同线程的操作交错的程度。在 Java 中，当多个线程共享同一个对象并对其进行变异时，最常出现竞争情况。

这篇文章的唯一先决条件是[一个线程](https://www.sitepoint.com/java-thread-class-tutorial)的基础知识。

## 竞赛条件

### 简单的例子

先说个例子。这里我实现了一个`Runnable`,它将共享计数器递增一万倍:

```
class IncrementingRunnable implements Runnable {

    private final MutableInteger mutableInteger;

    public IncrementingRunnable(MutableInteger mutableInteger) {
        this.mutableInteger = mutableInteger;
    }

    @Override
    public void run() {
        for (int i = 0; i < 10_000; i++) {
            mutableInteger.increment();
        }
    }

} 
```

因为标准 Java `Integer`的值在创建后不能更新，所以我实现了一个简单的`MutableInteger`类，它有两个方法:

*   `increment`增加其值
*   `getValue`得到结果值。

```
class MutableInteger {

    private int value = 0;

    public void increment() {
        value++;
    }

    public int getValue() {
        return value;
    }

} 
```

这样，我们可以将同一个计数器传递给多个线程，这些线程将并行递增它:

```
List<Thread> threads = new ArrayList<>();

// Variable to increment from multiple threads
MutableInteger integer = new MutableInteger();
// Run 10 threads to increment the same variable
for (int i = 0; i < 10; i++) {
    Thread thread = new Thread(new IncrementingRunnable(integer));
    thread.start();
    threads.add(thread);
}

// Wait until all threads are finished
for (Thread thread : threads) {
    thread.join();
}

System.out.println("Result value: " + integer.getValue()); 
```

(如果你不确定`Thread::start`或`join`如何工作，请阅读这篇[五分钟线程介绍](https://www.sitepoint.com/java-thread-class-tutorial)。)

因为我们有十个线程，每个线程执行 10，000 次增量，我们可能期望最终值是 100，000。但是实际结果可能会让你吃惊:

```
Result value: 61505 
```

更惊人的是，每次执行应用的总和都不一样，但总是小于 10 万。

### 增量如何产生竞争条件

为了理解这里发生了什么，我们需要了解增量运算是如何工作的。您可能认为这是一个不可分割的操作，没有任何中间状态，但实际上，CPU 执行几个低级指令，如下所示:

```
//i++
$tmp = i; // Store value to a temporary storage
$tmp = $tmp + 1; // Increment value in the temporary storage
i = $tmp; // Write result value back 
```

当只有一个线程更新计数器时，这不会导致任何问题。但是当有多个线程时，它们可以互相干扰，以任意顺序执行底层操作:

```
// T1 is Thread 1; T2 is Thread 2
T1: $tmp_1 = i;
T1: $tmp_1 = $tmp_1 + 1;
T2: $tmp_2 = i;
T2: $tmp_2 = $tmp_2 + 1;
T2: i = $tmp_2;
T1: i = $tmp_1; 
```

在这个特定的例子中，线程 1 和 2 都将相同的`i`(比如说`5`)加载到它们各自的临时变量中。因此，在两次递增后写回`6`，但是两次递增的预期结果应该是`7`。

这就是为什么当我们运行我们的应用程序时，最终结果总是小于 100，000。单个线程不知道有其他线程也在递增相同的数字。因此，最终的结果取决于哪个线程以什么顺序访问内存。

### 一个不那么简单的例子

当我们处理复杂的数据结构时，也会出现竞争情况。让我们看一个多线程更新同一个`LinkedList`实例的例子:

```
class ListAddRunnable implements Runnable {

    private final LinkedList<Integer> list;

    public ListAddRunnable(LinkedList<Integer> list) {
        this.list = list;
    }

    @Override
    public void run() {
        for (int i = 0; i < 10_000; i++) {
            list.add(i);
        }
    }

} 
```

使用这个`Runnable`，我们可以再次启动 10 个线程，每个线程都应该向同一个列表添加 10_000 个元素，然后等待它们完成。这是我得到的输出:

```
Result value: 44157 
```

这里发生了什么？为了理解它，我们首先需要了解`LinkedList`尤其是`add`是如何工作的。在内部`LinkedList`被表示为一个节点链，每个节点包含两个引用:一个指向添加到列表中的元素，另一个指向下一个节点。`add`方法将一个节点添加到列表的末尾——下面是该方法的简化版本:

```
void add(E element) {
    Node<E> newSecondLast = this.last;
    // create the new node (null says there is no next node)
    Node<E> newNode = new Node<>(element, null);
    // use it as the last node and let the old 'last' refer to it
    this.last = newNode;
    newSecondLast.next = newNode;
} 
```

(你可以在维基百科上阅读更多关于`LinkedList`是如何实现的[。)](https://en.wikipedia.org/wiki/Linked_list)

现在，让我们来看看如果两个线程同时添加元素时可能发生的一系列事件:

```
// First thread starts adding an element to the end of the list
T1: Node<E> newSecondLast = this.last;
T1: Node<E> newNode = new Node<>(element, null);
// Second thread adds an element to the end of the list
T2: Node<E> newSecondLast = this.last;
T2: Node<E> newNode = new Node<>(element, null);
T2: this.last = newNode;
T2: newSecondLast.next = newNode;
// First thread continues and overwrites the last element of the list
T1: this.last = newNode;
T1: newSecondLast.next = newNode1 
```

注意，除了 `this.last`之外，所有变量都是两个线程*的局部变量。它们读写共享字段的顺序决定了程序的成败。在上面的例子中，两者都读取了相同的最后一个节点*，从那以后就没有办法到达正确的状态，因为要么 T1 覆盖 T2，要么相反。

同样，程序的行为取决于线程之间不可预测的交互。

![Race Condition on Horse](img/2202b43a90ebd59a27d3d2d0e1fb8ce3.png)

## 结论

在这篇文章中，您已经了解到，当多个线程以不保证正确的方式更新共享状态时，就会发生争用情况。这可能很难发现，特别是如果多线程编程对您来说是新的，但是，作为一个经验法则，每次您有一个可被多线程访问的对象时，您都应该特别小心。

有几种方法可以防止竞态条件。最基本的方法是使用[同步](https://docs.oracle.com/javase/tutorial/essential/concurrency/locksync.html)和[锁](https://docs.oracle.com/javase/tutorial/essential/concurrency/newlocks.html)——底层原语来构建并发算法。JDK 还提供了广泛的[并发集合](https://docs.oracle.com/javase/tutorial/essential/concurrency/collections.html)和[原子值](https://docs.oracle.com/javase/tutorial/essential/concurrency/atomicvars.html)，可以用作高级构建块。

另一种避免竞态条件的方法是首先避免可变状态。为此，您需要使用[不可变对象](https://docs.oracle.com/javase/tutorial/essential/concurrency/immutable.html)和[不可变数据结构](https://en.wikipedia.org/wiki/Persistent_data_structure)，它们在每次更新时创建自身的副本，而不是就地执行更改。

## 分享这篇文章