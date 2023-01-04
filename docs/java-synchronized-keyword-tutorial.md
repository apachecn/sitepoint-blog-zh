# Java 的 Synchronized 关键字三分钟

> 原文：<https://www.sitepoint.com/java-synchronized-keyword-tutorial/>

### 目录 

*   [同步基础知识](#synchronizationbasics)
*   [使用同步关键字](#usingthesynchronizedkeyword)
*   [同步的工作原理](#howsynchronizedworks)
*   [多重锁定](#multiplelocks)
*   [在现实世界的 Java 应用程序中计数](#countinginarealworldjavaapplication)
*   [结论](#conclusions)
*   [评论](#comments)

关键字`synchronized`可以用来确保一次只有一个线程执行特定的代码段。这是防止争用情况的简单方法，当几个线程同时更改共享数据时，会发生争用情况，从而导致错误的结果。使用`synchronized`,整个方法或选定的块都可以是单线程的。

本文要求具备 [Java 线程](https://www.sitepoint.com/java-thread-class-tutorial/)和[竞态条件](https://www.sitepoint.com/dangers-race-conditions)的基础知识。

## 同步基础

让我们看看如何在方法上使用`synchronized`,更细粒度地在代码块上使用。我还将解释它是如何工作的。

### 使用`synchronized`关键字

如果我们给一个方法添加一个`synchronized`修饰符，JVM 保证同一时间只有一个线程可以执行一个方法:

```
class MutableInteger {

    private int value;

    public synchronized void increment() {
        value++;
    }

    public synchronized int getValue() {
        return value;
    }

} 
```

如果几个线程试图同时执行一个同步的方法，只有一个线程能够完成。其他的将被暂停，直到第一个退出该方法。(在某种意义上，它的工作方式很像交通灯，确保并发执行不会发生冲突。)因此，递增`value`计数器的线程不会[覆盖彼此的结果](https://www.sitepoint.com/dangers-race-conditions#howincrementcreatesaracecondition)，并且每次递增都会被记录下来。

为了查看关键字`synchronized`是否有效，我创建了十个线程，每个线程都将同一个计数器递增一万次。我用代码创建了[要点——它基本上与解释竞争条件](https://gist.github.com/mushketyk/ce91ca5da7a7c5b39a17d2633ba32317)的[文章相同。正如所料，结果总是一样的，并且总是正确的:](https://www.sitepoint.com/dangers-race-conditions#asimpleexample)

```
Result value: 100000 
```

`synchronized`关键字只限制线程访问一个对象。如果你有两个不同的实例，两个不同的线程可以同时使用它们，它们不会互相阻塞:

```
MutableInteger integer1 = new MutableInteger();
MutableInteger integer2 = new MutableInteger();
// Threads are using different objects and don't
// interact with each other
Thread thread1 = new Thread(new IncrementingRunnable(integer1));
Thread thread2 = new Thread(new IncrementingRunnable(integer2)); 
```

### `synchronized`如何工作

同步有两种形式:同步方法和同步语句。到目前为止，我们只遇到了第一种类型，下面是第二种:

```
class MutableInteger {

    private int value;

    private Object lock = new Object();

    public void increment() {
        // Only one thread can execute this block of code at any time
        synchronized (lock) {
            value++;
        }
    }

    public int getValue() {
        // Only one thread can execute this block of code at any time
        synchronized (lock) {
            return value;
        }
    }

} 
```

当线程进入同步块时，它试图获取与传递给语句的对象相关联的锁。(在 Java 中，一个对象的锁通常被称为它的*监视器*。)如果另一个线程当前持有锁，则当前线程将被暂停，直到锁被释放。否则线程成功并进入同步块。

当线程完成同步块时，它释放锁，另一个线程可以获取它。当线程离开同步块时，即使抛出异常，锁也会被释放。

顺便说一下，在方法中而不是在块中使用`synchronized`关键字只是使用对象自己的锁进行同步的简写:

```
// this is equivalent to 'public synchronized void ...'
public void increment() {
    synchronized (this) {
        value++;
    }
} 
```

也许你现在想知道如果同步一个静态方法，使用什么锁。

```
public static synchronized void foo() { ... } 
```

在这种情况下，get 锁定的是类对象本身，因此它相当于以下内容:

```
public static void foo() {
    synchronized (TheClassContainingThisMethod.class) {
        ...
    }
} 
```

### 多重锁

到目前为止，我们已经看到一个类只使用一个锁对象，但是在一个类中使用多个锁是很常见的。这允许两个不同的线程并行地对单个对象执行两种不同的方法:

```
class TwoCounters {

    private Object lock1 = new Object();
    private Object lock2 = new Object();

    private int counter1;
    private int counter2;

    public void incrementFirst() {
        synchronized (lock1) {
            counter1++;
        }
    }

    public void incrementSecond() {
        synchronized (lock2) {
            counter2++;
        }
    }

} 
```

### 在真实的 Java 应用程序中计数

同步通常用在现实世界的 Java 应用程序中，并且是一个强大的工具，但是我不建议您像我们在这里所做的那样使用它来聚合来自不同线程的值。Java 为此提供了一个特殊的类，称为 [`AtomicInteger`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/atomic/AtomicInteger.html) ，作为其丰富并发包的一部分。它比我们在这里实现的`MutableInteger`有更好的性能和更丰富的接口。

!["The synchronized keyword works much like a traffic light."](img/622356d4ca512611c73fb23ce21eba95.png)

## 结论

在这篇文章中，你已经学习了如何用关键字`synchronized`避免竞争情况，它保证只有一个线程可以执行给定的代码块。它可以在方法级别使用，在这种情况下，它将对象本身用作锁；也可以在块级别使用，在这种情况下，必须指定锁对象。任何 Java 对象都可以用作锁。

一个类似的基本并发特性是`Object`方法`wait`和`notify`，它们可以用来实现[保护块](https://docs.oracle.com/javase/tutorial/essential/concurrency/guardmeth.html)来协调不同线程之间的动作。除此之外，Java 还有 [`Lock`接口](https://docs.oracle.com/javase/tutorial/essential/concurrency/newlocks.html)，它允许实现类似于`synchronized`关键字的同步，但具有更大的灵活性。如果你想在多线程中使用集合，看看[同步包装器](https://docs.oracle.com/javase/tutorial/collections/implementations/wrapper.html)，特别是[并发集合](https://docs.oracle.com/javase/tutorial/essential/concurrency/collections.html)。

## 分享这篇文章