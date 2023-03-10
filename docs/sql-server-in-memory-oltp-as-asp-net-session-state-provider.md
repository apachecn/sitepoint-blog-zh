# 作为 ASP.NET 会话状态提供程序的 SQL Server 内存中 OLTP

> 原文：<https://www.sitepoint.com/sql-server-in-memory-oltp-as-asp-net-session-state-provider/>

*特别感谢[马修·威尔金](https://www.sitepoint.com/author/mwilkin/)好心帮助[同行评审](https://www.sitepoint.com/introduction-to-sitepoints-peer-review/)这篇文章。*

* * *

互联网应用程序使用 HTTP 协议，遵循请求-响应范式。因为每个请求都是相互独立的，所以我们需要找到一种方法来跨多个请求维护一些信息。为此，我们要么将所有公共数据放在每个请求中，要么将键放在请求中，以便根据每个键将数据存储在服务器上。

状态管理是任何 web 应用程序的基本要求。当用户与应用程序交互时，我们需要维护用户的偏好——应用程序状态及其相应的动作和响应，以及可能与应用程序相关的其他元数据。因此，今天的大多数 web 应用程序框架都为状态管理提供了各种现成的特性。

## ASP.NET 会话状态管理

ASP.NET 框架是构建 web 应用程序最常用的框架之一。它为快速应用程序开发提供了丰富的特性和工具。由于会话的简单性和灵活性，在许多 ASP.NET 应用程序中，会话通常被用作状态管理技术。让我们快速了解一下会话状态管理。

![Diagram showing a Session workflow](img/6f6413183830850efe076125d1b0929e.png)

从高层次上看，上图显示了两种类型的会话模式:InProc(进程内)和 OutProc(进程外)。使用 InProc 模式时，会话数据存储在 web 服务器的内存中，而 OutProc 允许我们将会话数据存储在状态服务器、SQL Server 或任何其他自定义位置。InProc 是最快的，因为数据可以在 web 服务器的内存中获得，不需要序列化/反序列化或 I/O。

但是这并不适合 Web Farm/Web Garden 场景，这种场景在企业应用程序中很常见，其中 SQL Server 用于存储会话状态。web 场是一组托管应用程序的 Web 服务器，其中负载平衡器根据服务器的负载路由请求。另一方面，Web Garden 涉及多个分配给相同应用程序池的工作进程(w3wp.exe ),由宿主应用程序使用。如果一台服务器或一个工作进程无法处理流量负载，两者都提供了一种扩展应用程序的方法。

每当需要可靠的会话时，企业应用程序往往会利用 SQL Server 中的高可用性功能，如冗余和集群。然而，使用 SQL Server 有额外的性能成本，因为我们需要在读取/写入 SQL 时序列化/反序列化会话数据。这需要 I/O 操作，因为数据驻留在基于磁盘的文件中。

会话数据通常很小，但本质上非常动态。在负载较重时，许多线程可能会在其他线程读取数据的同时写入会话数据。当线程主动访问小区域中的数据时，锁和闩锁争用开始产生问题。读取和写入数据开始花费更多的时间，这导致了糟糕的用户体验。所以每种模式都有一个权衡，但是正如我们所讨论的，我们最终会在许多场景中使用 SQL，并承担性能成本。为了获得更好的性能，我们投入了更多的资金，向系统中添加了更强大的服务器，但受益程度有限，所有可用的资源都没有得到最佳利用。

## 什么是内存 OLTP？

内存中 OLTP 是微软(相对而言)新推出的在线事务处理技术，该技术随 SQL Server 2014 一起推出，旨在借助永久驻留在内存中的内存优化表提供高性能数据库引擎，并使用本机存储过程编译器。

这些表不会受到闩锁的影响，因为内存中 OLTP 引擎使用无锁算法和结构。

微软开始着手一个代号为 Hekaton 的项目，旨在提供比基于磁盘的引擎快 100 倍的数据库引擎。在 SQL Server 2014 中，他们推出了这种新的引擎，比传统引擎快 30 到 40 倍。他们在 SQL Server 2016(目前处于预览版)中做了大量更改，使其更加强大。因此，如果我们利用 SQL Server 的这一特性，那么我们几乎可以减轻作为会话状态提供者的 <q>IO 绑定的</q> SQL 的性能成本。

## 使用 SQL Server 内存中 OLTP 进行会话状态管理

要使用内存 OLTP，我们可以编写自己的自定义提供程序。然而，已经有一个通过 NuGet 提供的包利用了这个特性，所以我们将使用它，而不是使用我们自己的包。配置这个包非常简单。NuGet 包可以在 NuGet 站点上找到[，也可以使用包管理器控制台安装](https://www.nuget.org/packages/Microsoft.Web.SessionState.SqlInMemory/)

```
PM> Install-Package Microsoft.Web.SessionState.SqlInMemory
```

此程序包对您的应用程序执行以下更改:

1.  添加一个程序集引用`Microsoft.Web.SessionState.SqlInMemory`

2.  更新 web.config 中的 sessionState 配置以使用 SqlInMemoryProvider:

    ```
    <sessionState mode="Custom" customProvider="SqlInMemoryProvider">
        <providers>
            <add name="SqlInMemoryProvider" type="Microsoft.Web.SessionState.SqlInMemoryProvider" 
                 connectionString="data source=***;initial catalog=ASPStateInMemory;User ID=user;Password=password;" />
        </providers>
    </sessionState>
    ```

    相应地更改连接字符串。

3.  提供一个名为`ASPStateInMemory.sql`的 SQL 脚本，可以用来配置内存数据库。在执行之前，请确保使用如下所述的任何配置更改来更新脚本。

    a.更改数据库的名称，默认命名为`ASPStateInMemory`。替换

    ```
    CREATE DATABASE [ASPStateInMemory] to CREATE DATABASE <your database name>
    ```

    b.配置数据库主要组的路径。
    替换

    ```
    NAME = ASPStateInMemory, FILENAME 'D:\SQL\data\ASPStateInMemory_data.mdf' with NAME = ASPStateInMemory, FILENAME <path of mdf file>
    ```

    c.根据您自己的服务器更改指定内存优化文件组路径的文件名。替换

    ```
    NAME = ASPStateInMemory_xtp, FILENAME = 'D:\SQL\data\ASPStateInMemory_xtp' with NAME = ASPStateInMemory_xtp, FILENAME = <path of xtp file>
    ```

    d.内存中 OLTP 提供了两种持久性选项:

    *   `SCHEMA_ONLY`:在服务器重启的情况下，表将被重新创建，以前的数据将丢失。它速度更快，并且在我们有瞬态数据时使用。默认情况下，它是启用的。
    *   `SCHEMA_AND_DATA`:在服务器重启的情况下，模式和数据被维护，类似于基于磁盘的表。要配置它，将`DURABILITY`属性从`SCHEMA_ONLY`更改为`SCHEMA_AND_DATA`。

执行后，它创建一个新的数据库[ASPStateInMemory](默认)，其中有两个表作为 dbo。[会话]和 dbo。[SessionItems]。

## 处理过期的会话

当我们使用 SQL 进行会话状态管理时，我们需要删除过期的会话数据。在传统方式中，我们使用 SQL Server 代理作业来定期删除过期的会话。

同样，这里我们有一个新的存储过程`DeleteExpiredSessions`，可以在作业中配置它来删除过期的会话。默认情况下，会话超时是 20 分钟，可以根据我们的需要进行配置，方法是在我们的 web.config 中将 timeout 属性添加到 sessionState 标记中，并且每次访问会话时，都会重置该超时。

## 主要优势

与使用传统的 SQL Server 会话状态相比，使用 SQL Server 会话状态时，内存中 OLTP 会将性能提高 30 到 40 倍。我们还可以使用内存 OLTP 来利用 SQL 的以下特性:

1.  借助 SQL Server **AlwaysOn** 功能，我们可以使我们的会话高度可用。我们还可以利用传统的方法，通过故障转移集群实现高可用性。为了做到这一点，我们需要对脚本进行一些更改，比如将持久性设置为`SCHEMA_AND_DATA`并更改`SessionItems`表的 Id 约束。

2.  我们可以利用 SQL Server 的地理冗余特性，这使得它具有持久性和高可用性。地理冗余在两个地理位置较远的站点之间复制数据，因此应用程序可以从一个站点切换到另一个站点。利用这一点，万一一个站点由于某种原因出现故障，可以使用另一个站点，因为它拥有所有可用的数据和配置。

3.  内存 OLTP 可用于网络农场和网络花园场景。

4.  就性能而言，内存 OLTP 与`InProc`模式一样好。

正如最近的一个案例研究所示(您可以从 Microsoft.com 下载 Word 文档), ASP.NET 应用程序的吞吐量——使用传统的 SQL Server 维护会话状态——在迁移后增加了 16 倍(每秒 15000 到 250000 个请求)。它允许将许多逻辑分区的服务器整合为一个。它根本不需要任何代码更改。

## 摘要

本文研究了 ASP.NET 会话状态提供程序及其可用选项、主要挑战，以及每个选项的缺点。它还研究了 SQL Server 2014 中引入的内存 OLTP 选项，该选项可用于存储会话状态信息。

内存 OLTP 将数据存储在服务器内存中，使用本机存储过程编译器，并且没有锁/闩争用。它的性能比前代产品快 30 到 40 倍，在移动到内存数据库后，应用程序的吞吐量可以提高 16 倍。

但是，SQL Server 2014 存在一些限制，例如内存优化表的大小、缺乏并行计划以及本机编译中的一些问题。所有这些都有望在 SQL Server 2016 中得到解决，这将使该功能更加强大和灵活。

## 分享这篇文章