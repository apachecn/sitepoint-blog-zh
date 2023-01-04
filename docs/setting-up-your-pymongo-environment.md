# 设置您的 PyMongo 环境

> 原文：<https://www.sitepoint.com/setting-up-your-pymongo-environment/>

*本文最初发表于 [MongoDB](https://www.mongodb.com/blog/post/pymongo-monday-setting-up-your-pymongo-environment) 。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

在本文中，我们将向开发人员介绍使用 Python 编程语言对 MongoDB 进行编程。PyMongo 是我们用来与 MongoDB 服务器交互的客户端库的名称(在 MongoDB 中我们称之为“驱动程序”)。

为了开始，我们需要安装一个典型的 MongoDB Python 开发人员使用的工具链。

## 安装 m

首先出场的是 [**m**](https://github.com/aheckmann/m) 。很难在网上找到，除非你搜索“MongoDB m”， **m** 是一个管理和并行使用 MongoDB 服务器的多个安装的工具。如果你想尝试最新最好的测试版，但仍然继续在我们当前的稳定版本上进行主线开发，这是一个非常有价值的工具。

安装 **m** 最简单的方法是使用[NPM](https://nodejs.org/en/)Node.js 包管理器(事实证明它不仅仅适用于 node . js)。

```
$ sudo npm install -g m
Password:******
/usr/local/bin/m -> /usr/local/lib/node_modules/m/bin/m
+ m@1.4.1
updated 1 package in 2.361s
$ 
```

如果不能或者不想用 npm，可以直接从 GitHub [repo](https://github.com/aheckmann/m) 下载安装。详见[自述](https://github.com/aheckmann/m/blob/master/README.md)那里。

今天我们将使用 **m** 来安装当前的稳定生产版本( [4.0.2](https://docs.mongodb.com/manual/release-notes/4.0/) 在撰写本文时)。

我们运行**稳定的**命令来实现这一点。

```
$ m stable
MongoDB version 4.0.2 is not installed.
Installation may take a while. Would you like to proceed? [y/n] y
... installing binary

######################################################################## 100.0%
/Users/jdrumgoole
... removing source
... installation complete
$ 
```

如果你需要在另一个程序中直接使用这个路径，你可以使用 m bin。

```
$ m bin 4.0.0
/usr/local/m/versions/4.0.1/bin
$ 
```

要运行相应的二进制 do **m 使用稳定的**:

```
$ m use stable
2018-08-28T11:41:48.157+0100 I CONTROL  [main] Automatically disabling TLS 1.0, to force-enable TLS 1.0 specify --sslDisabledProtocols 'none'
2018-08-28T11:41:48.171+0100 I CONTROL  [initandlisten] MongoDB starting : pid=38524 port=27017 dbpath=/data/db 64-bit host=JD10Gen.local
2018-08-28T11:41:48.171+0100 I CONTROL  [initandlisten] db version v4.0.2
2018-08-28T11:41:48.171+0100 I CONTROL  [initandlisten] git version: fc1573ba18aee42f97a3bb13b67af7d837826b47
< other server output >
...
2018-06-13T15:52:43.648+0100 I NETWORK  [initandlisten] waiting for connections on port 27017 
```

现在我们有了一个正在运行的服务器，我们可以通过 [mongo shell](https://docs.mongodb.com/manual/mongo/) 来确认它的工作情况。

```
$ mongo
MongoDB shell version v4.0.0
connecting to: mongodb://127.0.0.1:27017
MongoDB server version: 4.0.0
Server has startup warnings:
2018-07-06T10:56:50.973+0100 I CONTROL  [initandlisten]
2018-07-06T10:56:50.973+0100 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
2018-07-06T10:56:50.973+0100 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
2018-07-06T10:56:50.973+0100 I CONTROL  [initandlisten] ** WARNING: You are running this process as the root user, which is not recommended.
2018-07-06T10:56:50.973+0100 I CONTROL  [initandlisten]
2018-07-06T10:56:50.973+0100 I CONTROL  [initandlisten] ** WARNING: This server is bound to localhost.
2018-07-06T10:56:50.973+0100 I CONTROL  [initandlisten] **          Remote systems will be unable to connect to this server.
2018-07-06T10:56:50.973+0100 I CONTROL  [initandlisten] **          Start the server with --bind_ip < address> to specify which IP
2018-07-06T10:56:50.973+0100 I CONTROL  [initandlisten] **          addresses it should serve responses from, or with --bind_ip_all to
2018-07-06T10:56:50.973+0100 I CONTROL  [initandlisten] **          bind to all interfaces. If this behavior is desired, start the
2018-07-06T10:56:50.973+0100 I CONTROL  [initandlisten] **          server with --bind_ip 127.0.0.1 to disable this warning.
2018-07-06T10:56:50.973+0100 I CONTROL  [initandlisten]

---
Enable MongoDB's free cloud-based monitoring service to collect and display
metrics about your deployment (disk utilization, CPU, operation statistics,
etc).

The monitoring data will be available on a MongoDB website with a unique
URL created for you. Anyone you share the URL with will also be able to
view this page. MongoDB may use this information to make product
improvements and to suggest MongoDB products and deployment options to you.

To enable free monitoring, run the following command:
db.enableFreeMonitoring()
---

> 
```

这些警告是标准的。它们标记这个数据库没有默认设置的访问控制，它只监听来自运行它的机器的连接( *localhost* )。我们将在以后的节目中学习如何设置访问控制和监听更多的端口。

## 安装 PyMongo 驱动程序

但是这个系列不是关于 MongoDB Shell，它使用 JavaScript 作为其领域的硬币，而是关于 Python。我们如何用 Python 连接数据库？

首先我们需要安装 MongoDB Python 驱动程序， [PyMongo](https://docs.mongodb.com/ecosystem/drivers/) 。按照 MongoDB 的说法，驱动程序是一个特定于语言的客户端库，允许开发人员以他们自己的编程语言的习语与服务器进行交互。

对于 Python，这意味着用`pip`安装驱动程序。在 node.js 中使用`npm`安装驱动程序，在 Java 中可以使用`maven`。

```
$ pip3 install pymongo
Collecting pymongo
  Downloading https://files.pythonhosted.org/packages/a1/e0/51df08036e04c1ddc985a2dceb008f2f21fc1d6de711bb6cee85785c1d78/pymongo-3.7.1-cp27-cp27m-macosx_10_13_intel.whl (333kB)
    100% |████████████████████████████████| 337kB 4.1MB/s
Installing collected packages: pymongo
Successfully installed pymongo-3.7.1
$ 
```

我们建议您使用一个[虚拟环境](https://www.sitepoint.com/virtual-environments-python-made-easy/)来隔离您的 PyMongo Monday 代码。这不是必需的，但是对于隔离不同的开发流非常方便。

现在我们可以连接到数据库:

```
$ python
Python 3.6.5 (v3.6.5:f59c0932b4, Mar 28 2018, 03:03:55)
[GCC 4.2.1 (Apple Inc. build 5666) (dot 3)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import pymongo                                                  (1)
>>> client = pymongo.MongoClient(host="mongodb://localhost:8000")   (2)
>>> result = client.admin.command("isMaster")                       (3)
>>> import pprint
>>> pprint.pprint(result)
{'ismaster': True,
 'localTime': datetime.datetime(2018, 6, 13, 21, 55, 2, 272000),
 'logicalSessionTimeoutMinutes': 30,
 'maxBsonObjectSize': 16777216,
 'maxMessageSizeBytes': 48000000,
 'maxWireVersion': 6,
 'maxWriteBatchSize': 100000,
 'minWireVersion': 0,
 'ok': 1.0,
 'readOnly': False}
>>> 
```

首先我们导入 PyMongo 库 *(1)* 。然后，我们创建一个本地`client`对象 *(2)* ，它保存这个服务器的连接池和其他状态。我们通常不希望每个程序有一个以上的`MongoClient`对象，因为它提供了自己的连接池。

现在我们准备向服务器发出一个命令。在这种情况下，它是标准的 MongoDB 服务器信息命令，它被错误地称为`isMaster` *(3)* 。这是 MongoDB 早期版本的遗留物。它出现在 MongoDB 的 pre 1.0 版本中(现阶段已经超过十年)。`isMaster`命令返回一个`dict`，它详细描述了一系列服务器信息。为了以更易读的方式格式化它，我们导入了`pprint`库。

## 结论

我们已经安装了 MongoDB，安装了 Python 客户端库(又名驱动程序)，启动了一个`mongod`服务器，并在客户端和服务器之间建立了连接。

下周我们将介绍 MongoDB 上的 CRUD 操作，从 **Create** 开始。

如需直接反馈，请在 [twitter/jdrumgoole](https://twitter.com/jdrumgoole) 上提出您的问题。这样每个人都能看到答案。

试用 MongoDB 的最佳方式是通过 [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) 我们在 AWS、谷歌云平台(CGP)和 Azure 上提供的完全托管的数据库服务。

## 分享这篇文章