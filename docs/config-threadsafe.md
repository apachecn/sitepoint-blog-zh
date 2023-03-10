# 什么是 config.threadsafe！

> 原文：<https://www.sitepoint.com/config-threadsafe/>

[![ct](img/99b3ed65f3846c9c5178d08e4da292e1.png)](https://www.sitepoint.com/wp-content/uploads/2013/09/ct.png)

我们都喜欢 Rails，因为它的优雅和对完成我们的任务所提供的特性的巨大支持。Rails 功能丰富，真正允许开发人员专注于应用程序，而不是重复的任务，如查询数据库、管理路由、组合模板等。

在为开发人员提供开发 web 应用程序的便利的过程中，Rails 经常对开发人员隐藏许多细节。它这样做是有意的，因为 Rails 不想让开发人员在构建有用的东西时，为他们的工作增加负担。不过，开发人员可以随时阅读 Rails 源代码，而且一旦应用程序开始变大，大多数人都会这么做。

初学者和中级开发人员经常发现很难理解 Rails 中的一些重要概念，比如 Rack、Rack 中间件、ActiveRecord、资产管道、线程安全等等。在这篇文章中，我将关注 Rails 中的线程安全。

先说多线程。多线程是计算机科学中一个非常重要的概念，我们不能低估线程的重要性。线程无处不在，用于执行非常重要的工作。据[维基百科](http://en.wikipedia.org/wiki/Thread_%28computing%29):

> 在计算机科学中，执行的**线程**是可由操作系统调度程序独立管理的最小编程指令序列。线程是一个轻量级的进程。

正如维基百科所说，*线程是轻量级进程*。属于同一进程的线程共享该进程的资源。这就是为什么就内存而言，线程比进程更经济。在 web 环境中，线程通常用于异步处理一些后台作业或长时间运行的任务。但是我们在处理线程时必须非常小心，否则我们可能会因为多线程系统中出现的一个叫做**的竞争条件**的概念而经历意想不到的结果。据[维基百科](http://en.wikipedia.org/wiki/Race_condition#Software):

> 当独立的计算机进程或执行线程依赖于某种共享状态时，软件中就会出现竞争情况。对共享状态的操作是必须互斥的关键部分。不遵守这条规则可能会破坏共享状态。

我们已经回顾了计算机科学中一些有用的概念，现在让我们介绍 Rails 中的线程安全。Rails 中的线程安全并不新鲜，事实上它可以追溯到 Rails 2.3。*.Josh Peek 为 Rails 的线程安全做了令人难以置信的工作。Rails 中的线程安全避免了前面提到的竞争情况。

这意味着，在多线程的 web 服务器环境中，我们的代码应该是线程安全的。如果多个线程正在访问我们的 web 应用程序，那么当所有线程都完成处理时，我们的共享数据应该不会被破坏。

但是 Rails 不能保证这一点，因为开发人员可能会犯错误，导致非线程安全代码。那么，Rails 是如何为我们提供线程安全代码的保证的呢？

默认情况下，Rails 在我们的中间件堆栈中添加了一个名为“Rack::Lock”的中间件。这个中间件是默认堆栈中的第二个中间件。要了解 Rails 应用程序包含什么中间件，只需在应用程序目录中键入`rake middleware`。

第一个中间件`ActionDispatch::Static`，用于服务静态资产，如 JavaScript、CSS 文件和图像。

保证在给定时间只有一个线程在执行。如果我们去掉这个中间件，那么就可以同时执行多个线程。从 Ruby 1.9 开始，MRI Ruby 有一种称为 GIL(全局解释器锁)或 GVL(全局虚拟机锁/巨型虚拟机锁)的机制。GIL 保证在任何给定的时间只有一个线程在执行，并且在多线程 Ruby 的情况下，它执行上下文切换。如果某个正在执行的线程正在等待某项操作完成，Ruby 足够智能，可以开始处理另一个线程。

让我们看一个 Rails threadsafety 的实际例子。

创建一个示例 Rails 应用程序。

```
rails new test_app
```

现在`cd`进入新创建的 Rails 项目并捆绑安装必要的 gem。

```
bundle install
```

在这之后，创建一个具有一些基本动作的控制器。

```
rails generate controller thread_safety index simple infinite
```

这将创建一个名为`ThreadSafetyController`的控制器，带有`index`、`simple`和`infinite`动作。打开`app/views/thread_safety/index.html.erb`，粘贴以下代码:

```
<button id="simple_request">Simple Request</button>
&nbsp;
<button id="infinite_request">Infinite Request</button>

<script type="text/javascript">
    $("#simple_request").click(function() {
        $.get("/users/simple", function(data) {
            alert(data)
        });
    });

    $("#infinite_request").click(function() {
        $.get("/users/infinite", function(data) {
            alert(data)
        });
    });
</script>
```

这将创建一个带有两个按钮的页面。两个按钮的目的是将 Ajax 请求发送到相应的动作，并在警告框中显示返回的数据。

让我们在`app/controllers/thread_safety_controller.rb`中添加一些服务器端代码

```
def simple
  sleep(1)
  render :text => "Welcome from simple method"
end

def infinite
  while true
  end
end
```

上面的代码非常简单。`simple`休眠 1 秒钟，然后向客户端提交一个简单的`text/plain`响应。而`infinite`方法是一个无限循环，永远不会返回任何东西。通过键入`rails s`启动一个服务器，并导航到[http://localhost:3000/thread _ safety/index](http://localhost:3000/thread_safety/index)

单击“简单”按钮，一秒钟后，您将在服务器发送的警告框中得到响应。现在，点击“无限”按钮，等待响应。

由于故意的无限循环，`infinite`方法永远不会返回。再次按下“简单”按钮。如果您期望看到与我们之前得到的服务器相同的响应，那么您就错了:-)。

这是线程安全。Rails 通过一次只允许一个线程执行来保证我们的代码是线程安全的。除非执行线程完成其处理，否则其他线程无法执行。因为`infinite`线程因为一个无限循环而永远不会结束，所以其他线程没有机会处理。

即使我们在生产环境中运行我们的应用程序，我们也会得到相同的结果。这很酷，因为只有一个线程被执行，我们可以保证我们的 Rails 代码是线程安全的。但是有一个问题。

如果您已经使用 WEBrick 将该应用程序部署到生产环境中(您不应该这样做),那么您的用户可能会体验到较差的性能，因为在任何给定的时间都只有一个线程在执行。如果某个线程的执行时间比平时长，其他线程将不得不等待。这将会惹恼用户。

我们的资产不会面临这个问题，因为一旦响应被发送回客户机，我们的服务器就可以同时为多个客户机提供静态资产。这是因为静态资产由堆栈中的第一个中间件`ActionDispatch::Static`处理。

如何在不阻塞其他请求的情况下处理多个请求？我们可以通过启用`development.rb`或`production.rb`文件中的`config.threadsafe!`来简单地禁用线程安全。当我们启用这个选项时，我们的应用程序会发生巨大的变化。

现在，您可以在单击“无限”按钮后多次单击“简单”按钮，它将从服务器返回一个响应。我们已经在 Rails 应用中启用了多线程，但是现在编写线程安全代码是我们的责任。更多关于`config.threadsafe!`的深入信息，请阅读亚伦·帕特森的[这篇](http://tenderlovemaking.com/2012/06/18/removing-config-threadsafe.html)的精彩文章。

我们已经成功地展示了 Rails 中线程安全的概念。让我与你分享一些好消息。即使禁用了`config.threadsafe!`选项，您也可以使用基于流程的 web 服务器来处理多个请求。如果我们在 Unicorn 或 Apache Passenger / Nginx Passenger 等服务器上运行我们的应用程序，即使我们触发了无限的 Ajax 请求，简单的 Ajax 请求也总能成功返回。

这是因为基于进程的 web 服务器创建工作进程，并且每个进程持有我们的应用程序的一个实例。Apache Passenger 为它处理的每个请求生成一个子进程。因此，当我们发出无限 Ajax 请求时，一个工作线程开始处理该请求，当我们实例化简单 Ajax 请求时，它由一个新产生的子线程处理。Aaron Patterson 在他的文章中提到[,`config.threadsafe!`对基于流程的服务器没有影响。](http://tenderlovemaking.com/2012/06/18/removing-config-threadsafe.html)

我希望您今天学到了一些关于 Rails 和线程安全的知识。感谢阅读！

## 分享这篇文章