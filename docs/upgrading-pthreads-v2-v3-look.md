# 从 Pthreads v2 升级到 v3:需要注意什么

> 原文：<https://www.sitepoint.com/upgrading-pthreads-v2-v3-look/>

随着 pthreads v3 的发布， [pthreads](https://github.com/krakjoe/pthreads) 扩展发生了相当大的变化。本文旨在为那些希望将其应用程序从 pthreads v2 升级到 v3 的人提供必要的信息。

如果你不熟悉 pthreads，可以看看我的 pthreads 简介！

非常感谢乔·沃特金斯校对并帮助改进我的文章。

![Abstract image of parallel tracks with superimposed numbers 2 and 3, indicating version change](img/aea8a6df3a7fb875694777f0432eb80b.png)

## 通用更改

pthreads v3 中有一些一般性的更改。第一点，也许是最突出的一点，pthreads *不能在命令行界面之外的任何环境中使用。出于安全和可伸缩性方面的考虑，它从来没有打算在 web 服务器环境中使用(即在 FCGI 进程中)，因此现在已经实施了 pthreads v2 的建议。*

工人也发生了一些变化。以前，需要跟踪分配给工作线程的工作对象，否则如果它们在被工作线程执行之前被销毁，就会出现分段错误。这是众所周知的行为，在 PHP 中的[多线程与 pthreads](https://gist.github.com/krakjoe/6437782) 要点中用下面的代码片段简洁地演示了这一行为:

```
class W extends Worker {
    public function run(){}
}
class S extends Stackable {
    public function run(){}
}
/* 1 */
$w = new W();
/* 2 */
$j = array(
    new S(), new S(), new S()
);
/* 3 */
foreach ($j as $job)
    $w->stack($job);
/* 4 */
$j = array();
$w->start();
$w->shutdown(); 
```

这不再是一个问题，因为工人自己现在跟踪堆叠的工作对象。

此外，在 pthreads v3 中，方法修饰符的含义有一些变化。在 pthreads v2 中，方法修饰符在线程对象的上下文中有特殊的含义。具体来说，受保护的方法具有隐式同步访问(使它们能够被多个上下文安全地执行)，而私有方法只能被它们所绑定的上下文执行。出于可靠性考虑，这些不同的语义现已被删除。

例如，以下面的片段为例:

```
class ExampleThread extends Thread {
    public $value = 0;

    public function run()
    {
        $this->exclusive();
    }

    protected function exclusive()
    {
        for ($i = 0; $i < 10000; ++$i) {
            ++$this->value;
        }
    }
}

class Test extends ExampleThread {
    public function callExclusive()
    {
        $this->exclusive();
    }
};

$thread = new Test();
$thread->start();
$thread->callExclusive();
$thread->join();

var_dump($thread->value); 
```

在 pthreads v2 中，从主上下文和新线程上下文中调用`ExampleThread::exclusive`方法是安全的。脚本结束时输出的值总是`int(20000)`。但是在 pthreads v3 中，由于两个不同步的`for`循环之间的竞争条件，这个值可以是 1 到 20000 之间的任何值。

为了在 pthreads v3 中实现完全相同的行为，我们必须使用内置的`Threaded::synchronized`方法显式同步访问。这只需要应用于`ExampleThread::exclusive`方法的主体:

```
protected function exclusive()
{
    $this->synchronized(function () {
        for ($i = 0; $i < 10000; ++$i) {
            ++$this->value;
        }
    });
} 
```

关于移除私有方法修饰符语义，这只是解除了以前的限制。因此，利用该行为的代码不需要任何更改。

## 移除的类别

已经删除了`Mutex`和`Cond`类。这是因为由于`Threaded`类已经提供了同步特性，所以不需要它们的功能。在 PHP 代码中使用互斥锁和条件也不是特别安全，因为错误的代码很容易导致死锁。

扩展了`Threaded`的`Collectable`类也被移除了。现在，我们有了一个由`Threaded`实现的`Collectable`接口。该接口只实施了一个`isGarbage`方法。不再需要`setGarbage`方法，因为 pthreads 会自动处理任务何时应该被视为垃圾(当任务执行完毕时)。`Threaded`类实现了一个默认的`Threaded::isGarbage`方法，该方法应该在大多数情况下使用。默认实现将总是返回`true`，因为任务队列中的任何任务都是垃圾(任务在执行前不能被收集)。只有在极少数情况下才需要定制实现，因此覆盖`Threaded::isGarbage`方法应该是罕见的。

下面是在 pthreads 中使用内置垃圾收集器的简单示例:

```
$worker = new Worker();

for ($i = 0; $i < 10; ++$i) {
    $worker->stack(new class extends Threaded {});
}

$worker->start();

while ($worker->collect()); // blocks until all tasks have finished executing and have been collected

$worker->shutdown(); 
```

最后，之前别名为`Threaded`类的`Stackable`类已经被删除。任何扩展了`Stackable`的类现在都应该改为扩展`Threaded`。

## 移除的方法

下列方法已被删除:

*   `Threaded::getTerminatedInfo`–因为序列化异常是不安全的。没有内置的替代方法，但是由于 PHP 7 已经将绝大多数致命错误转换为异常，因此可以使用无所不包的异常处理程序来代替:

    ```
    $task = new class extends Thread {
        public function run()
        {
            try {
                $this->task();
            } catch (Throwable $ex) {
                // handle error here
                var_dump($ex->getMessage());
            }
        }
        private function task()
        {
            $this->data = new Threaded();
            $this->data = new StdClass(); // RuntimeException thrown
            var_dump(1); // never reached
        }
    };
    $task->start() && $task->join(); 
    ```

    (见下面新添加的`Volatile`类，以及为什么上面的代码是错误的。)

*   因为 PHP 7 有匿名类，使用起来更好。

*   `Threaded::isWaiting`–因为同步时根本不需要它。线程不应该询问它是否在等待什么，因此，除了这种方法之外没有其他选择。

*   `Threaded::lock`及其对应的`Threaded::unlock`——出于同样的原因，`Mutex`和`Cond`类被移除。鉴于同步现在同步`Threaded`对象的属性表，应该使用。

*   `Thread::kill`–由于执行不安全。别无选择——在如此高级的环境中，代码根本不需要杀死一个线程。

*   `Thread::detach`–由于不安全。没有替代方案——任何依赖于此的代码都需要重写。

*   `Worker::isWorking`–由于没有必要。为了查看工人是否还有剩余任务，应该使用`Worker::getStacked`方法，该方法将返回剩余堆栈的大小。

## 改变方法

以下方法已更改:

*   `Worker::unstack`–它不再接受参数(该参数之前从堆栈中移除了传递的任务)。这意味着现在默认只是从堆栈中删除第一个任务(最老的一个)，而不是从堆栈中删除所有任务。

*   `Pool::collect`–它现在返回要收集的任务数，收集器回调现在是可选的。如果不使用收集器回调，则使用默认的`Worker::collector`方法。

## 新类别

由于`Threaded`类的新的不变性语义，添加了`Volatile`类，其中如果它们的属性是`Threaded`对象，那么它们就是不可变的。`Volatile`类使得以前依赖于这类成员可变性的代码再次变得可变。

例如，下面的代码片段可以在 pthreads v2 上运行:

```
class Task extends Threaded
{
    public function __construct()
    {
        $this->data = new Threaded();
        $this->data = new StdClass(); // previously ok, but not in pthreads v3
    }
}

new Task(); 
```

但是现在在 pthreads v3 中，`$this->data`的重新分配将抛出一个`RuntimeException`，因为它是一个来自`Threaded`类的`Threaded`属性。为了有效地重新分配属性，`Task`类应该扩展`Volatile`:

```
class Task extends Volatile
{
    public function __construct()
    {
        $this->data = new Threaded();
        $this->data = new StdClass();
    }
}

new Task(); 
```

分配给`Threaded`对象属性的数组现在被自动强制为`Volatile`对象，而不是`Threaded`对象，因此它们的行为基本保持不变。

虽然这个新的不变性约束增加了一点复杂性，但它的引入是为了显著提高访问`Threaded`对象的`Threaded`属性的性能。

## 新方法

添加了以下方法:

*   `Worker::collect`–引入这一功能是为了释放已经在工作堆栈上执行完的任务。可以传入一个可选的收集器函数，但是默认收集器(来自`Worker::collector`)在大多数情况下应该足够了。

    例如，以下内容:

    ```
    $worker = new Worker();

    var_dump(memory_get_usage()); // original memory usage

    for ($i = 0; $i < 500; ++$i) {
        $worker->stack(new class extends Threaded {});
    }

    var_dump(memory_get_usage()); // memory usage after stacking 500 tasks

    $worker->start();
    while ($worker->collect());
    $worker->shutdown();

    var_dump(memory_get_usage()); // memory usage after worker shutdown 
    ```

    产出:

    ```
    int(372912)
    int(486304)
    int(374528) 
    ```

    通过调用`Worker::collect`的代码行，内存使用几乎恢复正常。如果没有它，在堆叠的 500 个任务和关闭工作线程之间，内存使用不会发生变化。虽然内存最终会在销毁对象时被释放，但最好是显式释放内存(特别是对于可能需要执行许多任务的长时间运行的进程)。所以要经常收集工人留下的垃圾(还有水池)。

*   `Worker::collector`–这是作为`Worker::collect`方法使用的默认实现引入的。当我们想要延迟收集失效对象时，我们可以覆盖这个方法。如上所述，默认收集器在大多数情况下已经足够了，所以只有在您知道自己在做什么的情况下才重写这个方法！

*   `Threaded::notifyOne`–这是对`Threaded::notify`的补充，允许信号只发送到一个等待同步的上下文中。

## 结论

pthreads v3 有许多变化，使扩展更具性能和更健壮。有些事情已经变得更简单了，特别是那些只能通过同步机制(`Threaded::wait`和`Threaded::notify`)处理的共享资源。其他事情的复杂性增加了一点，特别是关于新的不变性限制(以换取更好的性能)。但总的来说，pthreads v3 已经得到了很好的清理，看起来越来越好。

你在用吗？您必须从 v2 升级到 v3 吗？请告诉我们——我们很想写一个动手升级的例子。

## 分享这篇文章