# Rackspace 云文件

> 原文：<https://www.sitepoint.com/rackspace-cloud-files/>

Rackspace 云文件为您的公共和私人文件提供无限的在线存储。公共文件通过 Akamai 的高速内容传输网络下载。

提供包括文件存储在内的服务可能会很痛苦:安全性、磁盘空间、文件夹结构、备份等。文件存储网络消除了托管文件的痛苦，使我们能够专注于我们的核心任务。

使用云文件，您可以创建一个容器并将文件存储在其中。您可以将容器视为可以存储文件但不能存储其他文件夹的文件夹。如果你喜欢把你的图片从你的样式表中分离到不同的文件夹中，这是一个令人失望的做法，但是它也可以帮助你的网页加载时间，我们将在下面进一步探讨。你不会因为你有多少个容器而产生费用，但是一定要明智地命名它们。

您可以在此注册 Rackspace 云账户[。在您使用他们的服务之前，不会向您收费。](http://www.rackspacecloud.com)

## 远程服务器的优势

从远程服务器加载静态文件有几个好处。你的浏览器通常一次只能从一个域加载 4-8 个文件。如果你从同一个域加载了多个图片、css 和 javascript 文件，它们将会排队等待，无法快速下载。以今天的网速，这不是一个大问题，但它仍然存在。通过将一些静态文件推送到云文件，你的网络浏览器将同时从每个域加载 4-8 个文件。

云文件的另一个很好的用途是上传你的视频文件，然后直接通过容器的公共链接播放。这意味着你的服务器不再被网络浏览器的视频流所束缚。使用 Rackspace 内容交付网络不仅可以更快地传输内容，还可以释放您自己服务器上的资源。

## 价格点

使用云文件也不贵。如果你首页上的精彩视频占用了 50 千兆字节的带宽，你的成本将低于 10 美元。目前，每千兆字节的存储成本为 0.15 美元，每千兆字节的带宽成本为 0.18 美元。在他们的[定价页面](http://www.rackspace.com/cloud/cloud_hosting_products/files/pricing/)上，他们提供了一个计算器，所以你可以得到一个估价。

## 只说不做

关于你可以用它做什么已经说得够多了。一个实际的用例呢。我使用云文件有两个不同的原因。

首先，我将云服务器的快照存储在云文件的私有容器中。如果我需要启动一台相同的服务器，云服务器将使用保存在云文件中的快照启动一个新实例。

第二个是存储用户上传的图像和样式表，通过公共容器在 https 上提供服务。每个用户都有自己的容器，所以我们不必担心覆盖文件名。在电子商务领域工作，通过 https 显示网页是必须的。通过云文件，我们可以免费获得一个 https URL。不需要单独的 SSL 证书来提供另一个子域的静态文件。这也让我们能够专注于我们的使命。我们的客户一定要用这个吗？不，但是他们可以得到。最初，我们让客户上传文件给我们。一旦发生这种情况，文件被放入一个队列，以便上传到内容交付网络。文件一旦上传，就会从我们的服务器上删除。它实现起来非常简单，对我们来说非常有用。

## 一些可用的工具

有许多工具可以帮助使用云文件。这里有几个免费的。

### 云文件控制面板

您可以通过 Rackspace 云帐户访问云文件控制面板。
![](img/4bedf2fba76c43f74d6ae608a043de4e.png)

### 云文件 API

云文件 API 允许您构建自定义客户端上传到他们的网络。[点击此处](http://www.rackspace.com/cloud/cloud_hosting_products/files/api/)您可以找到软件开发工具包以及 API 文档。

### FireUploader

FireUploader 是 Mozilla Firefox 的插件。它的工作方式非常类似于 FTP 客户端，可以同步上传多个文件。


### Cyberduck

Cyberduck 是一个用于 Windows 和 Mac 的 FTP 客户端，可以上传到几个云提供商，包括 Rackspace CloudFiles 和亚马逊 S3。


图片 via[zentilia](http://www.shutterstock.com/gallery-212377p1.html)/[Shutterstock](http://shutterstock.com)

## 分享这篇文章