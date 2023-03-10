# 在 C#中使用 Async/Await 进行异步编程

> 原文：<https://www.sitepoint.com/asynchronous-programming-using-async-await-in-c/>

**在 C#中引入了`async`和`await`关键字，使[异步编程](https://docs.microsoft.com/en-us/dotnet/csharp/async)在。NET 平台更容易。这些关键字从根本上改变了大多数 C#生态系统中的代码编写方式。异步编程已经成为主流，现代框架如 ASP.NET 核心都是完全异步的。**

由于对 C#生态系统有如此大的影响，异步编程被证明是非常有价值的。但是什么是异步编程呢？

本文将介绍异步编程，展示`async`和`await`关键字的用法，讨论死锁陷阱，最后给出一些用这些关键字重构阻塞 C#代码的技巧。

先说术语。

## 并发、并行和异步

这三个术语有什么区别？它们都是多线程的应用，它们的定义是重叠的，并且经常互换使用。这就是为什么利用多线程的实现术语会令人困惑的原因。

我们将仔细研究这些术语之间的细微差别，这样我们就可以对异步编程有一个清晰的定义。

让我们假设一个 GUI 应用程序作为例子。

### 同步执行:一件接一件地做事情

用户单击一个按钮，并等待应用程序完成对 click 事件的处理。因为一次只能发生一件事，所以 UI 停止响应，直到事件被完全处理。同样，当 UI 可用于用户输入时，应用程序不能在后台做任何事情。

### 并发的:同时做多件事情

用户点击一个按钮，应用程序在后台触发一个单独的线程来执行满足用户请求所需的任务*并发*。在启动后台线程后，负责处理 UI 事件的线程立即变得可用，从而保持 UI 的响应性。

### 并行:同时复制多份东西

用户指示应用程序处理文件夹中的所有文件。应用程序用处理逻辑触发多个线程，并在这些线程之间分发文件。

### 异步:不必等到一个任务完成后再开始另一个任务

应用程序异步启动数据库查询*。在查询进行的同时，它还开始异步读取文件*。当两个任务都在进行中时，它会进行一些计算。
当所有这些任务完成后，它使用所有这三个操作的结果来更新 UI。**

 **## 异步编程

基于上面的术语，我们可以简单地将异步编程定义如下:

> 执行线程不应该等待 I/O 任务或 CPU 任务完成。

I/O 绑定操作的例子可以是文件系统访问、数据库访问或 HTTP 请求。CPU 密集型操作的例子包括调整图像大小、转换文档或加密/解密数据。

### 利益

使用异步编程有几个好处:

*   通过在异步活动期间“暂停”执行并将线程释放回线程池来避免线程池饥饿
*   保持用户界面的响应性
*   并发可能带来的性能提升

## 异步编程模式

。NET 为执行异步操作提供了三种模式。

### 异步编程模型(APM): *遗留*

也被称为`IAsyncResult`模式，它通过使用两种方法来实现:`BeginOperationName`和`EndOperationName`。

```
public class MyClass { 
    public IAsyncResult BeginRead(byte [] buffer, int offset, int count, AsyncCallback callback, object state) {...};
    public int EndRead(IAsyncResult asyncResult);
} 
```

来自微软文档:

> 在调用`BeginOperationName`之后，应用程序可以继续在调用线程上执行指令，而异步操作发生在不同的线程上。对于每个对`BeginOperationName`的调用，应用程序也应该调用`EndOperationName`来获得操作的结果。

### 基于事件的异步模式(EAP): *传统*

这个模式是通过编写一个`OperationNameAsync`方法和一个`OperationNameCompleted`事件来实现的:

```
public class MyClass { 
    public void ReadAsync(byte [] buffer, int offset, int count) {...};
    public event ReadCompletedEventHandler ReadCompleted;
} 
```

异步操作将以异步方法开始，当异步操作完成时，该方法将触发`Completed`事件以使结果可用。使用 EAP 的类也可能包含一个`OperationNameAsyncCancel`方法来取消正在进行的异步操作。

### 基于任务的异步模式(TAP): *推荐*

我们只有一个返回`Task`或通用`Task<T>`对象的`OperationNameAsync`方法:

```
public class MyClass { 
    public Task<int> ReadAsync(byte [] buffer, int offset, int count) {...};
} 
```

`Task`和`Task<T>`类在 TAP 中模拟异步操作。了解`Task`和`Task<T>`类对于理解 TAP 很重要，对于理解和使用`async` / `await`关键词很重要，下面就更详细的说一下这两个类。

## 任务和任务

`Task`和`Task<T>`类是. NET 中异步编程的核心。它们促进了与它们所代表的异步操作的各种交互，例如:

*   添加连续任务
*   阻塞当前线程等待任务完成
*   信令取消(通过`CancellationToken` s)

在启动一个异步操作并获得一个`Task`或`Task<T>`对象后，可以继续使用当前执行线程异步执行其他不需要任务结果的指令，或者根据需要与任务进行交互。

下面是一些使用任务来可视化实际效果的示例代码:

```
using System;
using System.Threading.Tasks;

public class Example {
    public static void Main() {
       Task<DataType> getDataTask = Task.Factory.StartNew(() => { return GetData(); } );
       Task<ProcessedDataType> processDataTask = getDataTask.ContinueWith((data) => { return ProcessData(data);} );
       Task saveDataTask = processDataTask.ContinueWith((pData) => { SaveData(pData)} );
       Task<string> displayDataTask = processDataTask.ContinueWith((pData) => { return CreateDisplayString(pData); } );
       Console.WriteLine(displayDataTask.Result);
       saveDataTask.Wait();
    }
} 
```

让我们浏览一下代码:

*   我们想**得到一些数据**。我们使用`Task.Factory.StartNew()`创建一个立即开始运行的任务。这个任务异步运行`GetData()`方法*，完成后，它将数据分配给它的`.Result`属性。我们将这个任务对象分配给`getDataTask`变量。*
**   我们希望**处理`GetData()`方法将提供的数据**。调用`.ContinueWith()`方法，我们*异步*创建另一个任务，并将其设置为`getDataTask`的延续。这个第二个任务将把第一个任务的`.Result`作为输入参数(`data`)，并用它异步调用`ProcessData()`方法*。完成后，它会将**处理过的数据**分配给它的`.Result`属性。我们将这个任务分配给`processDataTask`变量。(需要注意的是，目前，我们不知道`getDataTask`是否完成，也不关心。我们只知道当它完成时我们想要发生什么，我们为此编写代码。)***   我们要**保存处理过的数据**。我们使用相同的方法创建第三个任务，当数据处理完成时，它将异步调用`SaveData()`*，并将其设置为`processDataTask`的延续。***   我们还想让**显示处理后的数据**。我们不必等到数据保存后再显示它，所以我们创建了第四个任务，它将在数据处理完成时从处理过的数据*异步地*创建显示字符串，并将它**也**设置为`processDataTask`的延续。(现在我们有两个任务被分配为`processDataTask`的延续。一旦`processDataTask`完成，这些任务将同时*和*开始。)*   我们希望**将显示字符串**打印到控制台。我们用`displayDataTask`的`.Result`属性调用`Console.WriteLine()`。`.Result`属性访问是一个**阻塞**操作；我们的执行线程将*阻塞*，直到`displayDataTask`完成。*   我们希望在离开`Main()`方法和退出程序之前**确保数据被保存**。然而，在这一点上，我们不知道`saveDataTask`的状态。我们调用`.Wait()`方法让**阻塞**我们的执行线程，直到`saveDataTask`完成。***

 **## 几乎很好

如上所述，TAP 和`Task` / `Task<T>`类对于应用异步编程技术来说非常强大。但是仍然有改进的空间:

*   使用任务所需的样板代码相当冗长。
*   分配延续并对应该运行哪个任务做出粒度决策意味着许多细节应该由程序员来处理，这增加了复杂性并使代码容易出错。(冗长加上复杂性的增加，意味着代码难以理解，因此难以维护。)
*   尽管有这么强大的功能，但是没有办法在不阻塞执行线程的情况下等待任务完成。

这些缺点可能成为团队采用 TAP 的重大挑战。

这就是`async`和`await`关键字发挥作用的地方。

## `async`和`await`

这些关键字是为了应对使用`Task`和`Task<T>`类的挑战而引入的。它们*并不*代表异步编程的另一种方式；他们使用`Task`和`Task<T>`类，简化 TAP 的应用程序，同时保持`Task`类在需要时提供给程序员的能力。

让我们来看看每一个。

### 异步ˌ非同步(asynchronous)

`async`关键字被添加到方法签名中，以支持在方法中使用`await`关键字。它还指示编译器创建一个状态机来处理异步，但这超出了本文的范围。

一个`async`方法的返回类型总是`Task`或`Task<T>`。它是由编译器检查的，所以这里没有太多出错的空间。

### 等待

`await`关键字用于异步等待`Task`或`Task<T>`完成。它暂停当前方法的执行，直到正在等待的异步任务完成。与调用`.Result`或`.Wait()`的区别在于 *await 关键字将当前线程发送回线程池*，而不是让它保持在*阻塞*状态。

在引擎盖下，它:

*   为**异步**方法的剩余部分创建一个新的`Task`或`Task<T>`对象
*   将这个新任务作为继续任务分配给等待任务，
*   为延续任务分配上下文要求

在某些情况下，最后一点也是导致死锁的原因。我们稍后会谈到它，但首先让我们来看看`async`和`await`关键字的作用。

## 它看起来像什么

考虑来自 ASP.NET 应用程序的以下片段:

让我们浏览一下代码:

*   第 3 行:执行线程进入`DoSomethingAndReturnSomeValAsync()`方法，同步调用`DoSomething()`方法*。*
**   第 4 行:执行线程仍然同步进入`DoSomethingElseAsync()`方法*，直到`Task<SomeType>`返回的点(未显示)。***   在返回到第 4 行时，它遇到了`await`关键字，所以它在那里暂停执行并返回到线程池。*   现在剩下的`DoSomethingElseAsync()`正在*中执行，等待*和`Task<SomeType>`。*   第 4 行:当*等待* ed 任务完成时，从线程池中分配一个线程来接管`DoSomethingAndReturnSomeValAsync()`方法剩余部分的执行，从`await`关键字继续。这个线程将`Task<SomeType>`的结果赋给`someObj`变量。*   第 5 行:新线程构造了一个`ReturnType`对象，并从方法中返回它。**

 **### 在后台

当执行线程遇到第 4 行的关键字`await`时，它执行以下操作:

*   它创建了一个包含`DoSomethingAndReturnSomeValAsync()`方法剩余部分的`Task<ReturnType>`。
*   它将这个新任务设置为 [*的延续(如在 task。ContinueWith())* 从`DoSomethingElseAsync()`返回的`Task<SomeType>`的](https://docs.microsoft.com/en-us/dotnet/api/system.threading.tasks.task.continuewith?view=netframework-4.7.2)，以及所需的上下文。
*   然后它放弃执行并返回线程池。

因此，`ReturnType`对象实际上是从这个由`await`关键字创建的`Task<ReturnType>`返回的。因此，在我们的`async`方法的签名中，返回类型为`Task<ReturnType>`。

更多细节请参见 MS 文档中的[任务<结果>和](https://docs.microsoft.com/en-us/dotnet/api/system.threading.tasks.task-1?view=netframework-4.7.2)[基于任务的异步模式](https://docs.microsoft.com/en-us/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)。

## 如何更新现有代码

下面的示例显示了从同步方法调用异步方法的不同方式:******