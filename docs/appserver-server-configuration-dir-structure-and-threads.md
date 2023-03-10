# appserver–服务器配置、目录结构和线程

> 原文：<https://www.sitepoint.com/appserver-server-configuration-dir-structure-and-threads/>

在 Appserver 系列的第[部分](https://www.sitepoint.com/appserver-a-production-ready-php-based-server/)中，我们讨论了 Appserver 架构与标准 web 服务器堆栈的高度差异，并让您开始使用 Appserver 实例。如果你错过了那部分，请花时间[去读一下](https://www.sitepoint.com/appserver-a-production-ready-php-based-server/)。

![Appserver node diagram](img/1272872258db096fff8750007825ae27.png)

在这一部分，我们将更深入地探索 Appserver 架构。我们将介绍不同上下文的概念和 Appserver 的开箱即用部分，它们涵盖了大多数流行 PHP 框架提供的一些基础知识。我们还将配置 web 服务器并研究应用程序的结构。完成后，您应该对 Appserver 与线程、web 服务器及其设置相关的上下文有了相当的了解。

在后续部分中，我们将继续更详细地讨论 servlet 引擎、持久性容器、beans、消息传递系统和计时器模块。(*注:随着这个系列的演变，方向也发生了变化，为了包含更多的实用信息来打散干巴巴的理论。*)

## 上下文和线程

正如我们在第一部分中所讨论的，在今天的标准 web 服务器场景中，您将拥有一个 web 服务器和一个 web 服务器模块(mod_php)或一个 php 进程管理器(php-FPM ),来服务于 PHP 脚本/应用程序。web 服务器和 PHP 进程管理器或模块都处理自己的工作和线程，为网页或 PHP 应用程序提供服务。

![Server module gif](img/cf9543c61581fa9126d8d00328eac69e.png)

在这方面，Appserver 也为客户端开发人员处理线程。然而，线程的用法有些不同。在 appserver 运行期间，线程中构建的内容不会不断地构建和销毁。换句话说，只要 appserver 在运行，您让它运行的代码就会为每个请求继续运行(驻留在内存中)。这个基本的区别正在被重复，因为它对于理解我们将要深入的其他一切非常重要。

我们在上一篇文章中讨论了主题。现在你可能会问，什么是上下文？Appserver 中上下文的定义是“线程的运行时环境”。因此，每个线程都有自己的上下文。一个线程和它的上下文相对于一般进程的优势在于它能够与其他线程共享它的内容，并且仍然是“线程安全的”。人们可以称之为优势，但如果不加控制，它会变成一场噩梦。因此，为了避免噩梦，Appserver 为您控制共享，并且它是通过上下文或线程继承来完成的。

Appserver 提供了许多标准上下文。一旦 Appserver 启动并运行，它将构建一个层次结构或上下文树，根上下文是顶层或父上下文。从那里，您将有容器上下文和下面的服务器上下文，web 服务器驻留在那里。web 服务器构建工作环境(工作环境的数量是可配置的)。正是工作者上下文允许并行处理请求，它是在每个工作者内部构建和控制的。

![Worker contexts](img/c78b6333bbf4c697ca5dff93dd4e7afa.png)

子上下文可以有选择地从它们的父上下文继承实例、配置值、常量、函数、类定义甚至注释。必要时，上下文的形成也允许关注点的清晰分离。但是，请不要将这种继承与通常的 OOP 继承混淆。这不完全一样。

上下文之间的数据共享，以及上下文不会随着每个请求而被破坏这一事实，是 Appserver 中的功能，它有望提高专门为 Appserver 构建的应用程序的性能。像应用程序初始化(引导)的结果这样的事情，通常被认为是 PHP 应用程序中成本最高的过程之一，现在可以驻留在内存中。此外，工作可以传递给其他上下文，因此应用程序进程不必等待工作的完成(异步编程)。这些是使用 Appserver 给 web 开发人员带来的一些更大的优势。

在深入了解 appserver 的模块和编程之前，我们需要了解一下它使用的一些编程概念。

## Appserver 中的编程概念和技术

### 释文

![Annotations in use](img/bfd927dd83c020ba79772cf9ee59e19e.png)

在整个 Appserver 中使用的一项重要且可能有争议的技术是注释。使用注释背后的核心思想是“让 Appserver 易于配置”，或者更确切地说，“让 Appserver 易于编程”。注释在 Appserver 环境中的编程中起着重要作用，从路由到声明 beans 和设置 servlets。

尽管注释是 appserver 配置系统的核心部分，但是客户端开发人员仍然可以使用 XML 文件进行配置。事实上，几乎所有被注释的内容都可以通过应用程序目录中的`web.xml`文件来设置甚至覆盖。因此，对于那些不想在代码中加入注释的人来说，可以使用 XML 文件进行设置。

### 面向方面的编程

![Aspect oriented programming diagram](img/14884b30c16ce21d6cba203d7f202da0.png)

Appserver 中使用的基本编程范例是 AOP 或面向方面的编程。您并不局限于此，但这是一种现代的编程方式，Laravel 之类的公司也遵循这种方式，这可能是目前最流行的 PHP 框架。因此，这种面向对象的范例非常值得研究。理解横切关注点或方面、编织和连接点的含义将帮助您理解 Appserver 的设计和正确使用背后的一些基础。

### 合同设计

Appserver 中也遵循合同设计。这很方便，因为它在所有模块之间实施了更严格的类型化。Appserver 对方法的参数和返回值使用注释类型注释(这在大多数 ide 中是标准的),并使用它们来执行更严格的类型化。如果在运行时没有正确遵循类型，可能会引发异常。例外，您也可以自己创建例外。也可以记录这些异常。稍后我们将展示一个这样的例子。

现在，让我们深入了解每个可用的模块。

### 网络服务器

Appserver 的主要目标之一是为 PHP 开发人员提供一个相对容易使用，但是企业级的 web 服务器栈。它还提供了大多数 PHP 框架中的一些功能，这为更快速的 PHP 开发环境奠定了基础。事实上,“内置”web 服务器实际上是这些框架中缺少的重要部分。

Appserver 中的 web 服务器是服务器框架的一部分。它是一个纯 PHP 编写的 web 服务器。是啊！PHP！它还表示 Appserver 的服务器上下文的主模块。目前，可以通过 HTTP 1.1 连接到 web 服务器。计划在未来的版本中支持 HTTP 2.0，目前是 1.3.0，也可能是 2.0。

一个核心功能来自 web 服务器，以`$request`和`$response`对象的形式出现，大多数人可能会从大多数流行的框架中认出它。由于 Appserver 遵循 AOP，对象实际上将通过类似`HttpServletRequestInterface`的接口被调用。在下一部分中，我们将在回顾与 Appserver 一起安装的示例应用程序时开始使用请求和响应对象。

从 Appserver 用户或客户机开发人员的角度来看，web 服务器的配置和使用类似于 Apache web 服务器。在 web 服务器模块中，重写、虚拟主机、设置环境变量和 HTTP 认证都是可能的。

服务器上下文最重要的配置文件在`/etc/appserver`下。在那里，您将找到用于服务器、web 服务器和容器设置的`appserver.xml`文件以及`/conf.d/virtual-hosts.xml`文件，您的虚拟主机定义将被添加到这些文件中。已经安装了一个示例文件，它将帮助您设置虚拟主机。

现在让我们在 appserver 的 webserver 中设置一个虚拟主机。

### 创建虚拟主机

希望[您已经遵循了本系列第一部分](https://www.sitepoint.com/appserver-a-production-ready-php-based-server/)中的说明，并且在您方便的本地虚拟机上运行了 Appserver。在过去的博文中，我们使用的是 Debian Wheezy VM。安装程序应该已经为您设置了示例应用程序，您可以在 URL `http://my-app.com:9080/example`下找到它。我们将设置 Appserver 为 URL `http://my-app.com`下的示例应用程序提供服务(`my-app.com`代表您为服务器设置的名称，应该与 Bruno 在文章中建议的[不同)。这个简短的过程会让你有一种建立虚拟主机的感觉。](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)

#### 步骤 1–更改端口(一次性更改)

不完全必要，但仍然方便。我们将不再需要为每个 URL 输入端口号`:9080`。首先，通过停止服务，确保没有其他 web 服务器阻塞端口 80，如下所示:

```
service nginx stop 
```

或者

```
service apache2 stop 
```

然后转到`/opt/appserver/etc/appserver`目录，打开`appserver.xml`。搜索以下行。

```
<server 
    name="http"
    type="\AppserverIo\Server\Servers\MultiThreadedServer" 
```

在它们下面，寻找端口参数。

```
<param name="port" type="integer">9080</param> 
```

并将`9080`改为`80`。

#### 步骤 2–更改/添加虚拟主机

现在转到`/conf.d`目录，打开`virtual-hosts.xml`文件。应该是这样的。

```
<?xml version="1.0" encoding="UTF-8"?>
<!--
 ! The following is a basic showcase example for a virtual host configuration.
 ! Some more examples can be found at:
 !
 ! http://appserver.io/get-started/documentation/webserver.html#virtualhost-examples
 -->
<virtualHosts >
    <virtualHost name="example.local www.example.local">
        <params>
           <param name="admin" type="string">info@appserver.io</param>
           <param name="documentRoot" type="string">webapps/example</param>
        </params>
        <rewrites>
            <rewrite condition="-d{OR}-f{OR}-l" target="" flag="L" />
        </rewrites>
        <accesses>
            <access type="allow">
                <params>
                    <param name="X_REQUEST_URI" type="string">^.*
                    </param>
                </params>
            </access>
        </accesses>
        </virtualHost>
            <virtualHost name="dbadmin.awesome.dev">
                <params>
                    <param name="admin" type="string">info@appserver.io</param>
                    <param name="documentRoot" type="string">webapps/phpmyadmin</param>
                </params>
            <rewrites>
                <rewrite condition="-d{OR}-f{OR}-l" target="" flag="L" />   
            </rewrites>
        <accesses>
            <access type="allow">
                <params>
                    <param name="X_REQUEST_URI" type="string">^.*
                    </param>
                </params>
            </access>
        </accesses>
    </virtualHost>
</virtualHosts> 
```

需要注意的是文件的不同区域，比如，`virtualHost`，`accesses`和`rewrites`。这些是您将在每个虚拟主机上处理的常见配置。还要注意`documentRoot`参数。这将帮助我们避免在 URL 中添加目录`/example`。

将`virtualHost`名称改为如下所示。

```
 <virtualHost name="my-app.com www.my-app.com"> 
```

同样，`my-app.com`是你用流浪者盒子创建的名字。我们也在这里添加了`www.my-app.com`，因为我们正在模拟一个访问`www.`子域的适当网站。

为了使这些更改生效，您现在需要重新启动 Appserver。

```
service appserver restart 
```

如果您愿意(您应该这样做)，您可以添加一个重写规则，它将拦截任何对`www.`子域的调用，并将其重新路由到主域，反之亦然。我们先用前者，`www.`子域到主域。这遵循了一个非常重要且相当标准的 SEO 规则。[相同的内容不应该在不同的网址下访问！](https://moz.com/learn/seo/duplicate-content)

要创建这个规则，在`<rewites>`下，在已经存在的重写条件下添加下面一行。

```
<rewrite condition="^www\.@$HTTP_HOST" target="$REQUEST_SCHEME://my-app.com$X_REQUEST_URI" flag="R,L" /> 
```

这个条件查找任何前缀为`www.`的 URL，并将其重新路由到目标方案，这是我们的主域。还要注意，我们可以简单地添加变量`$X_REQUEST_URI`，而不是反向引用。这为正则表达式节省了一点计算时间。您可以在重写规则中使用 PHP 中通常可用的任何`$_SERVER`数组值。那很方便，不是吗？

查看您现在是否可以在您的域名下看到示例应用程序。在我们的示例中，它将位于:

`http://my-app.com`

如果一切顺利，您应该看到示例应用程序。

![Example app running](img/cf2a067809a22e48a16c659d62091229.png)

在我们深入研究 servlets 之前，最后一点需要注意。Appserver 还自带 PHP-FPM，目前运行在端口 9010 上的是 5.5.x。您也可以通过更改`appserver.xml`文件中的端口来运行您自己的 FPM。此外，Appserver 目前不会自动启动其 FPM 服务，所以如果你需要运行不是直接为 Appserver 构建的应用程序(如 WordPress)，记得也要启动它。要启动该服务，请在 shell 控制台中输入

`service appserver-php5-fpm start`

如果您需要对 Appserver 的 PHP 设置进行任何更改，您也可以在`/opt/appserver/etc`下找到相关的`.ini`文件。

## Servlet 引擎

如果你可以把 web 服务器想象成 Appserver 的眼睛、耳朵和嘴巴，那么 servlet 引擎就是大脑的[颞叶。那些喜欢 Java 的人可能也认识到了 Appserver 的 servlet 和](https://en.wikipedia.org/wiki/Temporal_lobe)[Java servlet](https://www.sitepoint.com/java-servlets-1/)之间的相似之处。在理论和实践中，它们几乎是一回事。

### 痛苦的引导过程不再

对于当前的 PHP web 服务器堆栈，所有主要框架或重型 web 应用程序最昂贵的过程之一是自举过程。这个过程是读取配置和设置运行时环境的地方。由于每个请求都必须完成这个过程，这是性能损失的一个来源，通常通过某种缓存方案来补偿，这增加了应用程序的复杂性。使用 appserver，引导过程只在服务器启动时进行一次，所以基本上是自动缓存的。与 servlet 引擎中的 servlet 一样，引导代码保存在内存中，直到 appserver 停止。appserver 的初始化部分非常重要，也是它的主要优点之一。

### Appserver 文件结构

在我们继续之前，我们首先需要理解 Appserver 应用程序的文件和目录结构。它不同于大多数 PHP 项目中常见的文件结构。实际的应用程序位于目录`/webapps`下。在我们的 Appserver 安装中，您应该在`/example`目录下看到示例应用程序。在这个目录下，您会发现下面的目录结构。

![Example file structure](img/3a617dccdb268d80e274c961d25d6003.png)

下面是 Appserver 应用程序中重要目录的简要介绍。

`/WEB-INF`–这个目录包含面向客户端的 PHP 类。您会注意到，在示例应用程序中，既有 servlets(在应用程序中不直接使用)的示例，也有在应用程序中使用的“action”类。这些类构成了应用程序的控制器。

`/META-INF`–该目录包含后端服务的代码。这是应该保存方面和域模型的任何入口代码的地方。

这个目录保存了 META-INF 和 WEB-INF 类之间共享的公共类。

`/vendor`–这个目录包含了你用 [Composer](https://www.sitepoint.com/re-introducing-composer/) 导入到你的项目中的任何库。Appserver 支持 Composer 和 PSR-0 进行类自动加载。由于需要自定义目录结构，PSR-4 不受支持。

`/static`–该目录是所有静态资源(即 JS、CSS 和图像文件)的主目录，这些资源是 web 应用程序正确渲染所必需的。

以上目录是默认的目录结构。该结构也可以通过 META-INF 或 WEB-INF 中的一个附加的`context.xml`文件进行定制。如果您确实想打破默认的结构，[关于这样做的更多信息可以在 appserver 文档](http://appserver.io/get-started/documentation/1.0/naming-directory.html)中找到。

**重要注意事项**–servlet 引擎(WEB-INF)和持久性容器(META-INF)中使用的类之间的通信可以通过代理对象来完成。这些对象可以通过网络进行通信，类似于 Java 中的远程方法调用。这意味着可以将这两层外推到它们自己的机器上，并让它们在基于 SOA 的系统[中作为服务运行](https://en.wikipedia.org/wiki/Service-oriented_architecture)。这种通过网络发送对象的能力允许 Appservers 的非对称伸缩，这是它的另一个内置特性。

### 结论

我们已经介绍了 Appserver 中的上下文和线程，以及 Appserver 如何通过在线程的上下文中预先构建必要的模块来为您处理线程中的“脏活”。我们已经简要介绍了现有的不同编程工具和范例，比如 AOP 或契约式设计。我们已经让您启动并运行了 Appserver 的 web 服务器，并解释了应用程序文件结构应该是什么样子。我们还深入研究了 web 服务器的配置。

唷！

实际上，我们甚至还没有触及 Appserver 的皮毛。这是一个很好的平台，并且提供了一个新的机会来用 PHP 构建更强大和更容易扩展的 web 应用程序。

在接下来的部分中，我们将深入研究其他现成的模块，比如 servlet 引擎和消息队列。

一如既往，我们非常欢迎您对任何遗漏领域的评论、批评和请求！

注意:以上帖子是用 appserver 1 . 0 . 6 版制作的。最新版本 1.1.0 是在这篇文章发表之前发布的。在本系列的下一部分中，我们将尽可能多地介绍这些新特性。

## 分享这篇文章