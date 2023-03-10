# 使用 Transloadit 上传基本图片

> 原文：<https://www.sitepoint.com/using-transloadit-for-basic-picture-uploads/>

![](img/fc759c3ea4f3d7b8866380dfe4d65b69.png "assembly")

最近，我需要研究将图片上传到我们的 web 应用程序的更好方法。该应用基于 Rails，托管在 Heroku 上。Heroku 用只读文件系统来约束它的托管应用程序，我相信你们很多人都知道。实际上，只读文件系统是一个*最终*约束，这意味着 Heroku 将允许应用程序写入文件系统上的一个小的临时空间。但是，临时空间被频繁地清理，没有预先判断，所以其中任何文件都不会存在很长时间。

由于 Heroku 确实允许写入这个临时空间，所以可以部署一个允许用户上传图片的应用程序，您的应用程序可以根据需要操作/调整图片大小。有很多这样做的例子，像[carrier 这样的 gems 已经有了](https://github.com/jnicklas/carrierwave)，这是我们开始的地方。不幸的是，这种方法不能与 Rails 一起伸缩。使用 Rails 上传文件会消耗一个 web dyno。如果文件很大，并且上传需要超过 30 秒，那么你的应用程序将开始到处抛出超时错误。[这篇文章](https://devcenter.heroku.com/articles/s3)对此做了更详细的介绍。

简而言之，如果你在 Heroku 上有一个严肃的 Rails 应用程序，需要上传文件，你需要找到一个直接上传文件到 S3(或者一个专门的图片服务)的方法，并在你的 Rails 应用程序之外执行图片操作。你的生活会因此变得更好。

## 选择

在我对这个问题的研究中，我遇到了 [CarrierWave-direct](https://github.com/dwilkie/carrierwave_direct) ，它允许直接上传到 S3。我认真地试着让它适应我们现有的流程，但做不到。另外，我相信 Carrierwave 会把文件从 S3 下载到 Heroku 的服务器上进行处理，这是我想避免的。我在这里不是要抨击 CarrierWave 或 CarrierWave-direct，而是要在我探讨 Transloadit 如何适应这种情况之前，引起人们对它们的注意。

## Transloadit 基础知识

和地球上的其他开发者一样，Node 引起了我的注意。我每天都读它，试图跟上社区里发生事情。这是我第一次听说 [Transloadit](http://transloadit.com/) 的地方。从那以后，我听到了很多对其服务的好评，所以我决定试一试。Transloadit 的[基础由四项组成:装配、机器人、步骤和模板。](http://transloadit.com/docs)

### 装配

基本上，程序集是 Transloadit 服务器上的一个单独的作业。在我们今天的例子中，一个集合将包含图片的上传和大小调整。

### 机器人

Transloadit 机器人(我喜欢他们这样称呼他们)每个人都有一项特殊技能。一些机器人调整图像大小，一些存储文件等。你可以根据需要使用步骤将机器人带入你的工作中

### 步骤(或组装步骤)

步骤是组件的一部分。调整图像大小是一个步骤(由机器人执行),将文件写入 S3 是另一个步骤

### 模板

模板将所有其他东西集合在一起。作为开发人员，您的工作是创建一个包含由机器人执行的组装步骤的模板，以满足您的应用程序的需求。模板是在 JSON 中指定的，所以它应该在您的工作范围之内。换句话说，创建模板非常简单。

## 示例应用程序:上传图片

好了，让我们用一个人工设计的应用程序把这一切结合起来。这个应用程序将只允许用户上传照片，并创建照片的缩略图和“全尺寸”版本。我不会用 Rails 应用程序的基本设置细节来烦你，但是源代码是[这里](https://github.com/RubySource/transloadit_demo)

### 设置 Transloadit

首先，去注册一个 Transloadit 账户。他们有一个免费的计划，应该可以让你通过这个演示(虽然，很遗憾，没有多远。)

Transloadit 需要知道如何处理我们的图片，所以我们需要转到我们的 [templates](http://transloadit.com/templates) 部分并创建一个新模板。

我在上面提到过，我们想要图片的缩略图和全尺寸版本，所以我们需要定义一个模板来创建这些版本。通过查看 Transloadit 网站上的大量文档，我们可以将模板构建为: