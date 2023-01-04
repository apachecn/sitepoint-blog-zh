# 系统管理员对“为什么使用 PHP”的看法

> 原文：<https://www.sitepoint.com/the-sysadmin-view-on-why-php/>

来自 Python 人群的一个搞笑:[PHP filter](http://projects.dowski.com/projects/phpfilter)–PHP“支持”下 [CherryPy](http://www.cherrypy.org/) 。不过，这也有严重的一面——它吐出了一些看起来像 PHP *解析*错误的东西——也就是说，这是一个开发人员的问题(例如，有人将 PHP 直接 ftp 到他们的实时 web 服务器上进行“测试”),而不是一个*运行时*错误。

更确切地说，你上一次看到 PHP 运行时错误导致整个应用程序或 web 服务器瘫痪是什么时候？不——“MySQL 连接失败:无法连接到本地 MySQL 服务器”不算数——PHP 和 web 服务器仍在运行——MySQL 服务器(或其他)是罪魁祸首。

对于 PHP 来说，脚本很难关闭运行时环境 web 服务器——我认为你必须故意这样做，也许是填满磁盘空间或其他方式。无辜的错误、运行时问题的特定实例(例如脚本执行时间过长)和 bug 仍然局限于特定的请求和处理它们的 PHP 脚本。在下一个请求中，我们[从头开始](https://www.sitepoint.com/lazy-php-part-1/)。

现在可以合理地宣称 Apache + mod_php 比任何其他类似的环境为更多的动态页面 HTTP 请求提供了服务。尽管有缺点，但这只是通过数字的权重来测试的。这意味着一个运行成本很低的平台，而且凌晨 2 点被叫醒的可能性也更小。

总之——最近浏览了一个很棒的博客: [FastCGI、SCGI 和 Apache:背景和未来](http://www.vmunix.com/mark/blog/archives/2006/01/02/fastcgi-scgi-and-apache-background-and-future/)从一个系统管理员的角度，讨论了给定对 [FastCGI](http://www.fastcgi.com/) 和像 Rails 这样的[框架的新需求的选项。在很大程度上，这也解释了为什么我们最终选择了 PHP。](http://duncandavidson.com/essay/2005/12/railsdeployment)

为了真正理解 Mark Mayo 的讨论，有必要大致了解一下最常用的技术方法，这些方法用于实现能够处理多个网页请求(并发)并将请求传递给程序(例如 PHP 脚本)进行处理的服务器。请注意，这并不是对多任务处理的深入指导——它更多的是我的虚拟理解/观点。如果你想吃更多的肉，维基百科上有一个好的起点[这里](http://en.wikipedia.org/wiki/Thread_(computer_science))。

*   Forking: an HTTP server process spawns a child processes to handle each incoming request, the children either expiring (exit) or returning to a “pool” for reuse, when the request is finished (Apache 1.3x does the latter).

    使用 Apache + CGI 脚本，Apache 子进程必须依次派生 CGI 程序在其中运行的子进程，所以速度非常慢。FastCGI 通过为进一步的请求保持 CGI 进程运行来消除这种情况(但是这样做需要更多的复杂性)。

    使用 mod_php，脚本在 Apache 子进程*本身*中运行。这减少了进一步分支的开销，意味着 PHP“运行时”只需要在创建 Apache 子进程时加载。

    分叉很好，因为它相对容易实现，而且(在很大程度上)多任务问题不会推给应用程序开发人员。

    这种模式受系统管理员欢迎的另一个原因是，子进程可以“崩溃”(例如，PHP 脚本中的无限循环)，而不会影响主服务器进程——这可能是共享主机愿意安装 mod_php 的首要原因——他们不必因为客户对服务器的操作而不断重启服务器。

    另外，对于 CGI 来说，将安全问题推给操作系统更容易，允许用户脚本以他们的权限运行，而不是 web 服务器用户的权限。

    mod_php 不属于这种情况，它违反了正常的 UNIX 文件系统安全性。PHP 脚本只需要在文件系统上*可读*，mod_php 就可以执行它们。

    分叉的缺点是分叉一个新进程(相对)缓慢/昂贵，并且每个子进程在运行时都要消耗内存和资源，而共享这些资源可能更有效。mod_php 方法是保持这种成本最小的最简单的方法。

    此外，Windows 并不真正支持 UNIX 风格的分叉，更强调线程，这可能是一个问题，如果你想你的服务器在 Windows 下运行良好。

*   Threads: threads run inside a single process and work on the basis of time-sharing: each thread gets a certain amount of time to do stuff. Threads are now used in Apache 2.x and are also common in Java application servers (which are themselves HTTP servers)

    线程的优势在于“创建”成本比分叉更低(例如，更快)，并且更容易在线程之间“共享”(例如，共享一个变量)。旁注:当 Java 人员说他们已经有了一个比 PHP 性能更好的 web 服务器时，他们可能说的是实话(但是记住性能！=缩放)

    不利的一面是，有些人认为线程很难编码，很容易产生死锁和竞争等难以调试的问题。这可能只是 web 服务器上的开发人员的问题——你不需要把线程推给在你的 web 服务器下运行的应用程序的编写人员——但是越复杂，错误就越多，等等。

    此外(更详细的实现细节)，如果服务器中的每个线程都有自己的 I/O 流来处理传入的请求，这很可能会吞噬内存/资源，而且大多数操作系统只支持有限数量的线程并发运行——有关详细的讨论，请参见[C10K 问题](http://www.kegel.com/c10k.html#threaded)(实际上，总体来说是非常好的阅读)。

    线程和 web 服务器的另一个问题是，给定的线程有更好的机会关闭整个服务器，尽管这可能更多的是实现细节。

*   Asynchronous I/O: it’s common in programming to use *sychronous* (blocking) I/O – you read from a “stream” and your code (process) stops execution until the read is complete.

    异步 I/O 使用非阻塞系统调用来允许您的代码(进程)继续并行地做其他事情(例如，更多的 I/O)。回调(或类似的)只在特定事件发生时执行(例如文件结束)。这些天来，多亏了 AJAX，我们都很熟悉这种做事方式，对吗？；).

    也许异步 I/O 最重要的例子是 Python 的 [twisted](http://twistedmatrix.com/) 框架，我想在未来几年我们会听到越来越多的消息。

    异步 I/O 很好，因为它没有线程的限制，可能会更有效地利用资源。用这种方式编写代码可能更容易(取决于你的 API——在较低的层次上，更难),尽管它仍然不像分叉那样容易 twisted 所做的大部分工作是为异步 I/O 提供一个很好的 API，为你解决大多数并发问题，这样你就可以专注于更高层次的问题。

    我猜你也有“用户代码”用异步 I/O 关闭整个服务器的风险——还没有看到 twisted 如何处理这个问题——也许这只是实现细节。

    顺便说一句，你可能会惊讶地注意到，最近的 PHP 版本也有对异步 I/O 的一些 T2 支持。

当然，这并不像我建议的那样明确。首先，您是哪种类型的开发人员将影响您的世界观:Linux 内核开发人员会看到语言和库设计人员的不同问题和界限，而语言和库设计人员反过来会对使用可用 API 的应用程序开发人员有不同的看法。一个给定的 web 服务器可能会使用不止一种方法——也许是所有三种方法。

事实似乎是，异步 I/O 现在才在 web 服务器上成熟/流行起来。与此同时，鉴于 Rails、 [web.py](http://webpy.org/) 等类似技术的出现，FastCGI 又重新受到需求和开发。尽管如此，mod_php 仍然(今天)代表了系统管理员所有弊端中较小的一个——不是完美的解决方案(例如安全问题),但却是最好的折衷方案——至少在可预见的未来是这样。

顺便说一句:如果你想从另一个角度了解 PHP 的过去，请看 Adam Trachtenberg 的 : [中间件之战:PHP 对抗世界](http://www.trachtenberg.com/PHP.pdf) (PDF)。

## 分享这篇文章