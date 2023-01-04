# Java 的五分钟线程课

> 原文：<https://www.sitepoint.com/java-thread-class-tutorial/>

### 目录 

*   [线程基础知识](#threadbasics)
*   [创建线程](#creatingthreads)
*   [启动线程](#launchingthreads)
*   [精加工螺纹](#finishingthreads)
*   [等待线程](#waitingforthreads)
*   [结论](#conclusions)
*   [评论](#comments)

在一个正在运行的 Java 程序中，所有的代码都是在线程中执行的，在一个线程中，所有的事情都是顺序发生的，一条指令接一条指令。当 Java(或者更确切地说是 JVM )启动时，它为要在其中执行的`main`方法创建一个线程。从那里，可以创建新的线程来执行与主线程并行的代码。最基本的方法是使用`Thread`类。

本文不需要任何多线程编程的知识，但是你需要熟悉核心的 Java 概念，比如[类](https://docs.oracle.com/javase/tutorial/java/concepts/class.html)和[接口](https://docs.oracle.com/javase/tutorial/java/concepts/interface.html)。

## 线程基础

Java 提供了一个 [`Thread`类](https://docs.oracle.com/javase/8/docs/api/java/lang/Thread.html)，可以用来启动新线程，等待它们完成，或者以更高级的方式与它们交互，这超出了本文的范围。

### 创建线程

要创建一个线程，需要通过实现`Runnable`接口来定义一个代码块，这个接口只有一个抽象方法`run`。这个实现的一个实例可以传递给`Thread`的构造函数。

让我们从打印三条消息并在每次打印后等待半秒钟的例子开始。

```
class PrintingRunnable implements Runnable {

    private final int id;

    public PrintingRunnable(int id) {
        this.id = id;
   }

    @Override
    // This function will be executed in parallel
    public void run() {
        try {
            // Print a message five times
            for (int i = 0; i < 3; i++) {
                System.out.println("Message " + i + " from Thread " + id);
                // Wait for half a second (500ms)
                Thread.sleep(500);
            }
        } catch (InterruptedException ex) {
            System.out.println("Thread was interrupted");
        }
    }

} 
```

`InterruptedException`由`Thread.sleep()`抛出。处理它可能很微妙，但我不会在这里深入探讨——你可以在[这篇文章](https://www.ibm.com/developerworks/library/j-jtp05236/)中读到。

我们可以使用`Runnable`实现来创建一个`Thread`实例。

```
Thread thread = new Thread(new PrintingRunnable(1)); 
```

### 启动线程

有了线索，是时候开始了:

```
System.out.println("main() started");
Thread thread = new Thread(new PrintingRunnable(1));
thread.start();
System.out.println("main() finished"); 
```

如果运行此代码，您应该会看到类似如下的输出:

```
main() started
Message 0 from Thread 1
main() finished
Message 1 from Thread 1
Message 2 from Thread 1 
```

注意来自`main`方法和我们开始的线程的消息是交错的。这是因为它们并行运行，并且它们的执行不可预测地交错。事实上，每次运行程序时，您都会看到略有不同的输出。与此同时，单线程中的指令*总是按照预期的顺序执行，正如你从增加的消息号中看到的。*

如果到目前为止您只看到了顺序 Java 代码，那么您可能会对程序在`main`方法完成后继续运行感到惊讶。事实上，正在执行`main`方法的线程没有任何特殊处理，一旦*所有*线程完成，JVM 就会完成程序执行。

我们也可以用同样的方法启动多线程:

```
System.out.println("main() started");
for (int i = 0; i < 3; i++) {
    Thread thread = new Thread(new PrintingRunnable(i));
    thread.start();
}
System.out.println("main() finished"); 
```

在这种情况下，所有三个线程将并行输出消息:

```
main() started
Message 0 from Thread 0
main() finished
Message 0 from Thread 1
Message 0 from Thread 2
Message 1 from Thread 0
Message 1 from Thread 2
Message 1 from Thread 1
Message 2 from Thread 1
Message 2 from Thread 0
Message 2 from Thread 2 
```

### 精加工螺纹

那么一个线程什么时候结束呢？它发生在两种情况之一:

*   执行`Runnable`中的所有指令
*   从`run`方法中抛出一个未被捕获的异常

到目前为止，我们只遇到了第一种情况。为了查看第二个选项是如何工作的，我实现了一个`Runnable`，它打印一条消息并抛出一个异常:

```
class FailingRunnable implements Runnable {

    @Override
    public void run() {
        System.out.println("Thread started");

        // The compiler can detect when some code cannot be reached
        // so this "if" statement is used to trick the compiler
        // into letting me write a println after throwing
        if (true) {
            throw new RuntimeException("Stopping the thread");
        }
        System.out.println("This won't be printed");
    }

} 
```

现在让我们在一个单独的线程中运行这段代码:

```
Thread thread = new Thread(new FailingRunnable());
thread.start();
System.out.println("main() finished"); 
```

这是输出:

```
Thread started
Exception in thread "Thread-0" java.lang.RuntimeException: Stopping the thread
    at com.example.FailingRunnable.run(App.java:58)
    at java.lang.Thread.run(Thread.java:745)
main() finished 
```

如您所见，最后一条`println`语句没有执行，因为一旦抛出异常，JVM 就停止了线程的执行。

您可能会感到惊讶，尽管抛出了异常,`main`函数仍继续执行。这是因为不同的线程相互独立，如果其中任何一个线程失败，其他线程将继续运行，就像什么都没发生一样。

### 正在等待线程

我们需要对线程执行的一个常见任务是等待它完成。在 Java 中，这非常简单。我们需要做的就是在一个线程实例上调用`join`方法:

```
System.out.println("main() started");
Thread thread = new Thread(new PrintingRunnable(1));
thread.start();
System.out.println("main() is waiting");
thread.join();
System.out.println("main() finished"); 
```

在这种情况下，调用线程将被阻塞，直到目标线程完成。当目标线程中的最后一条指令被执行时，调用线程被恢复:

```
main() started
main() is waiting
Message 0 from Thread 1
Message 1 from Thread 1
Message 2 from Thread 1
main() finished 
```

注意“main() finished”打印在来自`PrintingThread`的所有消息之后。以这种方式使用`join`方法，我们可以确保某些操作在特定线程中的所有指令之后严格执行。如果我们在一个已经完成的线程上调用`join`，调用会立即返回，并且调用线程不会被暂停。这使得等待几个线程变得容易，只需循环遍历它们的集合，并在每个线程上调用`join`。

因为`join`让调用线程暂停，所以也抛出了一个`InterruptedException`。

![Parallel Threads](img/c0dceacccd6846f9be2c25f4227ff68c.png)

## 结论

在这个帖子里。您已经学习了如何在 Java 中创建独立的指令线程。要创建一个线程，你需要实现`Runnable`接口并使用一个实例来创建一个`Thread`对象。线程可以用`start`启动，当它执行完指令或者抛出一个未被捕获的异常时，它的执行就结束了。要等到一个线程的执行完成，我们可以使用`join`方法。

通常线程会相互交互，这会导致一些在编写单线程代码时不会发生的意外问题。探索类似于 [*竞争条件*](http://stackoverflow.com/a/34550) 、 [*同步*、*锁*](http://winterbe.com/posts/2015/04/30/java8-concurrency-tutorial-synchronized-locks-examples/) 和 [*并发集合*](https://docs.oracle.com/javase/tutorial/essential/concurrency/collections.html) 的主题，以了解更多相关信息。

如今，`Thread`被认为是多线程编程中的底层工具。除了显式地创建线程，您可能还想使用 [*线程池*](https://docs.oracle.com/javase/tutorial/essential/concurrency/pools.html) 来限制应用程序中的线程数量，并使用 [*执行器*](http://www.baeldung.com/java-executor-service-tutorial) 来执行异步任务。

## 分享这篇文章