# 用 PHP 中的 Pthreads 进行并行编程——基础

> 原文：<https://www.sitepoint.com/parallel-programming-pthreads-php-fundamentals/>

*这篇文章由[克里斯多佛·皮特](https://www.sitepoint.com/author/assertchris/)进行了同行评议。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

* * *

PHP 开发人员似乎很少利用并行性。同步单线程编程的简单性当然很有吸引力，但有时使用一点并发性可以带来一些有价值的性能改进。

在本文中，我们将看看如何使用 [pthreads 扩展](https://github.com/krakjoe/pthreads)在 PHP 中实现线程化。这需要安装 PHP 7.x 的 ZTS (Zend Thread Safety)版本，以及 pthreads v3。(在撰写本文时，PHP 7.1 用户需要从 pthreads repo 的 master 分支进行安装——参见[这篇文章的](https://www.sitepoint.com/install-php-extensions-source/#installing-a-third-party-extension)部分，了解从源代码构建第三方扩展的细节。)

只是快速澄清一下:pthreads v2 针对 PHP 5.x，不再支持；pthreads v3 面向 PHP 7.x，正在积极开发中。

![Parallel execution abstract image](img/27a03d396b9c2711e586b84222fe9cc1.png)

非常感谢[Joe Watkins](https://twitter.com/krakjoe)(pthreads 扩展的创建者)校对并帮助改进我的文章！

## 何时不使用 pthreads

在我们继续之前，我想首先澄清一下你*什么时候不应该*(以及*不能*)使用 pthreads 扩展。

在 pthreads v2 中，建议 pthreads *不应在 web 服务器环境中使用*(即在 FCGI 进程中)。从 pthreads v3 开始，这个建议已经被强制执行，所以现在你根本*不能*在 web 服务器环境中使用它。其中两个突出的原因是:

1.  在这样的环境中使用多线程是不安全的(会导致 IO 问题和其他问题)。
2.  它不能很好地扩展。例如，假设您有一个 PHP 脚本，它创建了一个新线程来处理一些工作，并且该脚本在每个请求时都被执行。这意味着对于每个请求，您的应用程序将创建一个新线程(这是 1:1 线程模型，一个线程对应一个请求)。如果您的应用程序每秒处理 1，000 个请求，那么它每秒创建 1，000 个线程！在一台机器上运行这么多线程会很快淹没它，而且随着请求速率的增加，这个问题只会更加严重。

这就是为什么在这样的环境下线程不是一个好的解决方案。如果您正在寻找线程作为 IO 阻塞任务(如执行 HTTP 请求)的解决方案，那么让我为您指出*异步编程*的方向，这可以通过 [Amp](http://amphp.org/docs/amp/) 等框架来实现。SitePoint 已经发布了一些关于这个主题的优秀文章(比如[编写异步库](https://www.sitepoint.com/writing-async-libraries-lets-convert-html-to-pdf/)和[用 PHP](https://www.sitepoint.com/modding-minecraft-with-php-buildings-from-code/) 修改《我的世界》)，如果你感兴趣的话。

说完了，让我们直接开始吧！

## 处理一次性任务

有时，您会希望以多线程的方式处理一次性任务(比如执行一些 IO 绑定的任务)。在这种情况下，`Thread`类可以用来创建一个新的线程，并在那个单独的线程中运行一些工作单元。

例如:

```
$task = new class extends Thread {
    private $response;

    public function run()
    {
        $content = file_get_contents("http://google.com");
        preg_match("~<title>(.+)</title>~", $content, $matches);
        $this->response = $matches[1];
    }
};

$task->start() && $task->join();

var_dump($task->response); // string(6) "Google" 
```

在上面,`run`方法是我们将在新线程内部执行的工作单元。当调用`Thread::start`时，产生新的线程并调用`run`方法。然后，我们将派生的线程重新加入主线程(通过`Thread::join`)，主线程将一直阻塞，直到单独的线程完成执行。这确保了在我们试图输出结果(存储在`$task->response`中)之前，任务已经执行完毕。

用线程相关的逻辑来污染一个类的职责可能是不可取的(包括必须定义一个`run`方法)。我们能够通过让这些类扩展`Threaded`类来隔离这些类，这样它们就可以在其他线程中运行:

```
class Task extends Threaded
{
    public $response;

    public function someWork()
    {
        $content = file_get_contents('http://google.com');
        preg_match('~<title>(.+)</title>~', $content, $matches);
        $this->response = $matches[1];
    }
}

$task = new Task;

$thread = new class($task) extends Thread {
    private $task;

    public function __construct(Threaded $task)
    {
        $this->task = $task;
    }

    public function run()
    {
        $this->task->someWork();
    }
};

$thread->start() && $thread->join();

var_dump($task->response); 
```

任何需要在独立线程*中运行的类都必须以某种方式*扩展`Threaded`类。这是因为它提供了在不同线程中运行的必要能力，以及提供了隐含的安全性和有用的接口(如资源同步)。

让我们快速看一下 pthreads 公开的类的层次结构:

```
Threaded (implements Traversable, Collectable)
    Thread
        Worker
    Volatile
Pool 
```

我们已经看到并学习了关于`Thread`和`Threaded`类的基础知识，所以现在让我们看看剩下的三个(`Worker`、`Volatile`和`Pool`)。

## 回收线

为每个要并行的任务创建一个新线程是非常昂贵的。这是因为 pthreads 必须采用无共享架构来实现 PHP 内部的线程化。这意味着必须为每个创建的线程复制 PHP 解释器的当前实例的整个执行上下文(包括每个类、接口、特征和函数)。因为这会导致明显的性能影响，所以应该尽可能重用线程。线程可以以两种方式重用:用`Worker` s 或用`Pool` s。

`Worker`类用于在另一个线程内部同步执行一系列任务。这是通过创建一个新的`Worker`实例(这会创建一个新线程)，然后将任务堆叠到那个单独的线程上(通过`Worker::stack`)。

这里有一个简单的例子:

```
class Task extends Threaded
{
    private $value;

    public function __construct(int $i)
    {
        $this->value = $i;
    }

    public function run()
    {
        usleep(250000);
        echo "Task: {$this->value}\n";
    }
}

$worker = new Worker();
$worker->start();

for ($i = 0; $i < 15; ++$i) {
    $worker->stack(new Task($i));
}

while ($worker->collect());

$worker->shutdown(); 
```

输出:

![Pool output](img/7e39e0e79aa4066d6473cb58ff3d3a1e.png)

以上通过`Worker::stack`将 15 个任务堆叠到新的`$worker`对象上，然后按照堆叠的顺序处理它们。如上所述,`Worker::collect`方法用于在任务执行完毕后清理任务。通过在 while 循环中使用它，我们阻塞主线程，直到在我们触发`Worker::shutdown`之前，所有堆叠的任务都已经执行完毕并被清理。过早关闭工作线程(即仍有任务要执行)仍会阻塞主线程，直到所有任务都已执行完毕——这些任务不会被垃圾收集(导致内存泄漏)。

`Worker`类提供了一些与其任务堆栈相关的其他方法，包括用于移除最旧堆栈项目的`Worker::unstack`，以及用于表示执行堆栈上项目数量的`Worker::getStacked`。worker 的堆栈只保存将要执行的任务。一旦堆栈中的一个任务被执行，它就会被移除，然后放在一个单独的(内部)堆栈上进行垃圾收集(使用`Worker::collect`)。

执行许多任务时重用线程的另一种方法是使用线程池(通过`Pool`类)。线程池由一组`Worker`提供支持，使任务能够并发执行*，其中并发因子(线程池运行的线程数量)在创建线程池时指定。*

 *让我们修改上面的例子，改为使用一个工人池:

```
class Task extends Threaded
{
    private $value;

    public function __construct(int $i)
    {
        $this->value = $i;
    }

    public function run()
    {
        usleep(250000);
        echo "Task: {$this->value}\n";
    }
}

$pool = new Pool(4);

for ($i = 0; $i < 15; ++$i) {
    $pool->submit(new Task($i));
}

while ($pool->collect());

$pool->shutdown(); 
```

输出:

![Pool output](img/653d4580e54e2369c6354a82f312b607.png)

使用游泳池和使用工人之间有一些显著的区别。首先，池不需要手动启动，一旦任务可用，它们就开始执行任务。其次，我们*将*任务提交到池中，而不是*堆叠*它们。另外，`Pool`类不扩展`Threaded`，因此它可能不会被传递给其他线程(不像`Worker`)。

作为一个良好的实践，工作线程和池应该总是在任务完成后被收集，并被手动关闭。通过`Thread`类创建的线程也应该连接回创建线程。

## pthreads 和(im)可变性

要介绍的最后一个类是`Volatile`——pthreads v3 的新增部分。不变性已经成为 pthreads 中的一个重要概念，因为没有它，性能会严重下降。因此，默认情况下，本身是`Threaded`对象的`Threaded`类的属性现在是不可变的，因此它们在初始赋值后不能被重新赋值。现在支持这种属性的显式可变性，并且仍然可以通过使用新的`Volatile`类来实现。

让我们快速看一个例子来演示新的不变性约束:

```
class Task extends Threaded // a Threaded class
{
    public function __construct()
    {
        $this->data = new Threaded();
        // $this->data is not overwritable, since it is a Threaded property of a Threaded class
    }
}

$task = new class(new Task()) extends Thread { // a Threaded class, since Thread extends Threaded
    public function __construct($tm)
    {
        $this->threadedMember = $tm;
        var_dump($this->threadedMember->data); // object(Threaded)#3 (0) {}
        $this->threadedMember = new StdClass(); // invalid, since the property is a Threaded member of a Threaded class
    }
}; 
```

另一方面，`Volatile`类的属性是可变的:

```
class Task extends Volatile
{
    public function __construct()
    {
        $this->data = new Threaded();
        $this->data = new StdClass(); // valid, since we are in a volatile class
    }
}

$task = new class(new Task()) extends Thread {
    public function __construct($vm)
    {
        $this->volatileMember = $vm;

        var_dump($this->volatileMember->data); // object(stdClass)#4 (0) {}

        // still invalid, since Volatile extends Threaded, so the property is still a Threaded member of a Threaded class
        $this->volatileMember = new StdClass();
    }
}; 
```

我们可以看到，`Volatile`类覆盖了由它的父类`Threaded`强制的不变性，使得`Threaded`属性能够被重新分配(以及`unset()`)。

关于可变性和`Volatile`类——数组，还有最后一个基本主题要讲。当 pthreads 中的数组被分配给一个`Threaded`类的属性时，它会被自动强制为`Volatile`对象。这是因为在 PHP 中从多个上下文操纵一个数组是不安全的。

让我们再快速看一个例子，以便更好地理解事情:

```
$array = [1,2,3];

$task = new class($array) extends Thread {
    private $data;

    public function __construct(array $array)
    {
        $this->data = $array;
    }

    public function run()
    {
        $this->data[3] = 4;
        $this->data[] = 5;

        print_r($this->data);
    }
};

$task->start() && $task->join();

/* Output:
Volatile Object
(
    [0] => 1
    [1] => 2
    [2] => 3
    [3] => 4
    [4] => 5
)
*/ 
```

我们可以看到，`Volatile`对象可以被视为数组，因为它们通过子集操作符(`[]`)支持基于数组的操作(如上所示)。`Volatile`然而，通用的基于数组的函数不支持类，比如`array_pop`和`array_shift`。相反，`Threaded`类以内置方法的形式为我们提供了这样的操作。

作为示范:

```
$data = new class extends Volatile {
    public $a = 1;
    public $b = 2;
    public $c = 3;
};

var_dump($data);
var_dump($data->pop());
var_dump($data->shift());
var_dump($data);

/* Output:
object(class@anonymous)#1 (3) {
  ["a"]=> int(1)
  ["b"]=> int(2)
  ["c"]=> int(3)
}
int(3)
int(1)
object(class@anonymous)#1 (1) {
  ["b"]=> int(2)
}
*/ 
```

其他支持的操作包括`Threaded::chunk`和`Threaded::merge`。

## 同步

本文的最后一个主题是 pthreads 中的同步。同步是一种允许对共享资源进行受控访问的技术。

例如，让我们实现一个简单的计数器:

```
$counter = new class extends Thread {
    public $i = 0;

    public function run()
    {
        for ($i = 0; $i < 10; ++$i) {
            ++$this->i;
        }
    }
};

$counter->start();

for ($i = 0; $i < 10; ++$i) {
    ++$counter->i;
}

$counter->join();

var_dump($counter->i); // outputs a number from between 10 and 20 
```

如果不使用同步，输出是不确定的。多个线程写入单个变量而不进行受控访问会导致更新丢失。

让我们通过添加同步来纠正这一点，以便我们接收到正确的`20`输出:

```
$counter = new class extends Thread {
    public $i = 0;

    public function run()
    {
        $this->synchronized(function () {
            for ($i = 0; $i < 10; ++$i) {
                ++$this->i;
            }
        });
    }
};

$counter->start();

$counter->synchronized(function ($counter) {
    for ($i = 0; $i < 10; ++$i) {
        ++$counter->i;
    }
}, $counter);

$counter->join();

var_dump($counter->i); // int(20) 
```

同步的代码块也可以使用`Threaded::wait`和`Threaded::notify`(以及`Threaded::notifyOne`)相互协作。

这是两个同步 while 循环的交错增量:

```
$counter = new class extends Thread {
    public $cond = 1;

    public function run()
    {
        $this->synchronized(function () {
            for ($i = 0; $i < 10; ++$i) {
                var_dump($i);
                $this->notify();

                if ($this->cond === 1) {
                    $this->cond = 2;
                    $this->wait();
                }
            }
        });
    }
};

$counter->start();

$counter->synchronized(function ($counter) {
    if ($counter->cond !== 2) {
        $counter->wait(); // wait for the other to start first
    }

    for ($i = 10; $i < 20; ++$i) {
        var_dump($i);
        $counter->notify();

        if ($counter->cond === 2) {
            $counter->cond = 1;
            $counter->wait();
        }
    }
}, $counter);

$counter->join();

/* Output:
int(0)
int(10)
int(1)
int(11)
int(2)
int(12)
int(3)
int(13)
int(4)
int(14)
int(5)
int(15)
int(6)
int(16)
int(7)
int(17)
int(8)
int(18)
int(9)
int(19)
*/ 
```

您可能已经注意到了围绕对`Threaded::wait`的调用设置的附加条件。这些条件至关重要，因为它们只允许同步回调在收到通知*和*时恢复，指定的条件是`true`。这很重要，因为通知可能来自其他地方，而不是打给`Threaded::notify`。因此，如果对`Threaded::wait`的调用没有包含在条件中，我们将对*虚假的唤醒调用*开放，这将导致不可预测的代码。

## 结论

我们已经看到了它的五个类 pthreads 包(`Threaded`、`Thread`、`Worker`、`Volatile`和`Pool`，包括每个类使用时的覆盖。我们还研究了 pthreads 中新的不变性概念，并快速浏览了它支持的同步特性。有了这些基础知识，我们现在可以开始研究将 pthreads 应用到一些真实的用例中了！这将是我们下一篇文章的主题。

同时，如果你有一些关于 pthreads 的应用想法，不要犹豫，把它们放到评论区吧！

## 分享这篇文章*