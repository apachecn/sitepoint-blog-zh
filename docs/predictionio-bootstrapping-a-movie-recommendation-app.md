# 预测:启动一个电影推荐应用程序

> 原文：<https://www.sitepoint.com/predictionio-bootstrapping-a-movie-recommendation-app/>

在本教程中，我将带您了解一个开源机器学习服务器 [PredictionIO](http://prediction.io/) ，它允许您创建能够完成以下任务的应用程序:

*   推荐项目(如电影、产品、食物)
*   预测用户行为
*   确定项目相似性
*   排列项目

![](img/5c505a91c4e87b78b6207df7a0b7da05.png)

使用 PredictionIO，您几乎可以轻松构建任何机器学习应用程序。你不必处理数字和算法，你可以专注于开发应用程序本身。

*注意，这两部分的教程是我们旧的[预测帖子](https://www.sitepoint.com/create-movie-recommendation-app-prediction-io-setup/)的复兴，由于工具的一些变化，它不再准确到需要完全重写的程度。*

## 安装和配置

您需要做的第一件事是安装 PredictionIO 及其依赖项。您可以通过以下方式之一来实现:

1.  [启动一个 AWS 实例](https://docs.prediction.io/install/launch-aws/)
2.  快速安装脚本
3.  手动安装
4.  [流浪安装](https://docs.prediction.io/install/install-vagrant/)
5.  [Docker 安装](https://docs.prediction.io/community/projects/#docker-installation-for-predictionio)
6.  [Terminal.com](https://www.terminal.com/snapshot/f444bfb7538dfc596485374f56167ec6f79cbc16f793f013ad120067070eb81a)

我们将使用流浪者方法安装。这样，我们可以在本地进行预测，而不必处理令人头疼的安装问题。然而，如果你喜欢冒险，可以随意选择他们文档中提到的任何安装方法。或者，如果你已经有一个现有的 [Homestead 改进安装](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)，你可以使用它，然后用快速安装脚本安装 PredictionIO。

要安装 PredictionIO with vagger，请打开终端并执行以下命令来克隆用于启动 prediction io 虚拟机的 vagger 文件。

```
git clone https://github.com/PredictionIO/PredictionIO-Vagrant.git 
```

完成后，导航到目录并切换到主分支:

```
cd PredictionIO-Vagrant/
git checkout master 
```

打开`Vagrantfile`并根据需要更改 cpu 执行上限和内存设置。默认情况下，内存设置为`2048mb`，cpu 执行上限为`90`。如果你有更多的内存和强大的处理器，把它提高到一个更高的值。注意`cpuexecutioncap`的值最多只能达到`100`。这里的默认值`90`表示单个虚拟 CPU 最多可以使用单个主机 CPU 的 90%。

```
config.vm.provider "virtualbox" do |v|
    v.customize ["modifyvm", :id, "--cpuexecutioncap", "90", "--memory", "2048"]
end 
```

为了向主机公开虚拟机，您需要注释掉下面一行。如果您愿意，可以随意更改 ip 的值。

```
config.vm.network :private_network, ip: "192.168.33.10" 
```

然后，您可以编辑主机的主机文件，以指向此 IP 地址。例如，您可以通过浏览器上的`http://movierec.dev`访问应用程序。

```
192.168.33.10   movierec.dev 
```

完成`Vagrantfile`的配置后，保存它，然后执行以下命令启动虚拟机:

```
vagrant up 
```

因为这是您第一次运行该命令，所以它将继续执行预配置文件(`provision.sh`)中的安装命令，如果您看一下，它基本上是使用快速安装脚本。如果已经安装了 PredictionIO，它将简单地用`pio-start-all`命令启动它。这将启动 PredictionIO 及其所有依赖项。

![predictionIO install complete](img/ebf88710e2102132d9911b276ec8c245.png)

## 电影数据库应用编程接口

由于我们正在构建一个电影推荐应用程序，我们需要有一个像样的电影列表，可以用于我们的应用程序。为此，我们将使用[电影数据库 API](http://www.themoviedb.org/documentation/api) 。

为了访问他们的 API，你首先必须在他们的网站上注册一个账户。在您的帐户页面上，点击 *API* 链接。然后会提示您输入个人信息以及使用类型。完成后，您将收到一封电子邮件，其中包含您可以用来执行请求的 API 密钥。

## 应用程序概述

在我们构建应用程序之前，我首先向您提供一个概述。
应用程序应该尽可能简单，所以我们将只实现两个基本功能:

*   **学习阶段**–这是应用程序将从数据库中随机挑选一部特定电影并展示给用户的阶段。然后，我们将要求用户输入他是否喜欢或不喜欢这部电影。

*   **推荐阶段**–这是应用程序根据用户在学习阶段的输入推荐电影的部分。

如果你是一个视觉型的人，下面是学习阶段的样子:

![learning phase](img/50c6f8c0751ffeb5ce780467207d8304.png)

这是推荐阶段:

![recommendation phase](img/1b64edd63a56bdb6abb692b5259e2259.png)

我们不会实现登录系统。这意味着每次在浏览器中访问应用程序时，用户都被视为新用户。用户的会话将在整个学习阶段保持不变，只有在向用户显示推荐电影列表后才会被清除。

## 创建推荐引擎

预测是通过利用引擎来工作的。引擎负责做出预测。它们包含用于处理应用程序提供的数据的机器学习算法。然后，在机器学习算法完成其工作后，它可以响应预测查询。

PredictionIO 团队已经提供了官方模板引擎，可以进行产品排名、电子商务推荐、相似产品推荐、项目分类、通用推荐等工作。更高级的 PredictionIO 用户也创建了自己的引擎。这些引擎可以在 [templates.prediction.io](http://templates.prediction.io/) 找到。在本教程中，我们将使用[通用推荐引擎](http://templates.prediction.io/PredictionIO/template-scala-parallel-recommendation)。您可以使用`pio template get`命令安装引擎。这需要两个参数:引擎的 ID 和您想要给引擎起的名字。

```
pio template get PredictionIO/template-scala-parallel-recommendation RecommendationEngine 
```

*注意:这将要求你输入模板的 Scala 包名。你可以在这里输入任何你想要的东西。一个好的习惯是`com.`然后是你的名字。之后，它将在当前目录下安装引擎。所以如果你想稍微组织一下，你可以创建一个名为`predictionio-engines`的文件夹，把你所有的引擎放在里面。*

一旦创建了引擎，我们需要更新它，以便它可以处理自定义事件。推荐引擎的默认配置只处理给特定项目的评级。对于这个电影推荐应用程序，我们只会让用户喜欢或不喜欢一个随机的电影。我们可以通过编辑您可以在引擎目录的根目录中找到的`src/main/scala/DataSource.scala`文件来配置它。

打开文件后，查找`eventNames`变量，并将其值设置为`Some(List("like", "dislike"))`。接下来，查找`ratingValue`变量，并将其类型和值设置如下:

```
Double = event.event match {
  case "like" => 5.0
  case "dislike" => 1.0 
  case _ => throw new Exception(s"Unexpected event ${event} is read.")
} 
```

下面是默认设置和更新设置的对比:

```
//default
eventNames = Some(List("rate", "buy")),

//updated
eventNames = Some(List("like", "dislike")),

//default
val ratingValue: Double = event.event match {
  case "rate" => event.properties.get[Double]("rating")
  case "buy" => 4.0 // map buy event to rating value of 4
  case _ => throw new Exception(s"Unexpected event ${event} is read.")
}

//updated
val ratingValue: Double = event.event match {
  case "like" => 5.0
  case "dislike" => 1.0 
  case _ => throw new Exception(s"Unexpected event ${event} is read.")
} 
```

## 创建新的预测应用程序

对于每一个我们想使用 PredictionIO 的应用，都有一个对应的 PredictionIO 应用。单个服务器上可以托管多个应用程序，因此这用于标识每个应用程序。
您可以使用`pio app new`命令创建新的应用程序。

```
pio app new MovieRecommendationApp 
```

这将返回一个访问密钥，我们可以在将要创建的应用程序中使用它。您现在可以记下访问键，或者稍后使用`pio app list`命令来列出您当前拥有的所有应用程序。

## 安装依赖项

现在我们已经准备好构建应用程序了。我们将为这个应用程序使用[流明微框架](http://lumen.laravel.com)。您可以使用 Composer 提供的`create-project`命令来安装它:

```
composer create-project laravel/lumen blog "5.1.*" 
```

一旦完成，我们就可以更新`lumen`目录中的`composer.json`文件，这样我们就可以安装其他依赖项了。所需对象现在应该如下所示:

```
"require": {
    "laravel/lumen-framework": "5.1.*",
    "vlucas/phpdotenv": "~1.0",
    "predictionio/predictionio": "~0.8.2",
    "elasticsearch/elasticsearch": "~1.0",
    "guzzlehttp/guzzle": "~5.0"
}, 
```

因为我们使用`composer create-project`安装了 lumen，所以`laravel/lumen-framework`和`vlucas/phpdotenv`是默认存在的。我们拥有的其他依赖关系如下:

*   `predictionio/predictionio`–PHP SDK 的[预测。这允许我们与预测服务器对话。](https://github.com/PredictionIO/PredictionIO-PHP-SDK)
*   一个 PHP 的 [elasticsearch 客户端。我们用它来保存和检索电影细节。](https://github.com/elastic/elasticsearch-php)
*   `guzzlehttp/guzzle`–[PHP 的 HTTP 客户端](https://github.com/guzzle/guzzle)。我们用它向 TMDB API 发出请求。

之后，我们需要做的就是执行`composer install`。

## 结论

在这个介绍性的部分中，我们学习了如何设置 PredictionIO，同时定制推荐引擎和安装依赖项。请继续关注下一部分，我们将开始构建电影推荐应用程序。

## 分享这篇文章