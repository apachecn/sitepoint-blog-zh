# 集成 MongoDB 和 Amazon Kinesis，实现智能、持久的流

> 原文：<https://www.sitepoint.com/integrating-mongodb-and-amazon-kinesis-for-intelligent-durable-streams/>

*本文最初发表于 [MongoDB](https://synd.co/2NCzyKk​) 。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

您可以在 MongoDB 上构建您的在线运营工作负载，并通过使用 MongoDB Stitch 触发器启动 [Amazon Kinesis](https://aws.amazon.com/kinesis/) 流处理操作来实时响应事件。

让我们来看一个示例场景，在这个场景中，用户在网站上采取的操作会产生一个数据流。我们将持久地存储数据，同时为 Kinesis 流程提供数据，对弃车、产品推荐甚至信用卡欺诈检测等进行流分析。

我们将通过设置一个缝合触发器来实现这一点。当在 MongoDB 中进行相关数据更新时，触发器将使用 Stitch 函数来调用 AWS Kinesis，正如您在这个架构图中看到的:

![](img/5041b42e75907935a00884df9d3618b2.png)

#### 你需要做的是

1.  **一个 Atlas 实例**
    如果你还没有在 Atlas 上运行的应用程序，你可以点击这里阅读我们的[Atlas 入门指南。在本例中，我们将使用一个名为 ***streamdata*** 的数据库，以及一个名为 ***clickdata*** 的集合，我们将从基于 web 的电子商务应用程序中写入数据。](https://docs.atlas.mongodb.com/getting-started/)
2.  **一个 AWS 账户和一个 Kinesis 流**
    在本例中，我们将使用一个 Kinesis 流向下游发送数据到其他应用程序，如 Kinesis Analytics。这是我们想要更新的流。
3.  **一个 Stitch 应用**
    如果你还没有一个 Stitch 应用，[登录 Atlas](https://cloud.mongodb.com/user#/atlas/login) ，点击左侧导航中的 **Stitch 应用**，然后点击**创建新应用**。

### 创建收藏

第一步是从 Stitch 应用程序控制台创建一个数据库和集合。点击左侧导航菜单中的**规则**，点击**添加收藏**按钮。键入 **streamdata** 作为数据库，键入 **clickdata** 作为集合名称。选择标记为用户只能**读写他们自己的数据**的模板，并提供一个字段名称，我们将在其中指定用户 id。

![Figure 2\. Create a collection](img/b551c148c1e2a17272719546873925db.png)

### 配置 Stitch 与 AWS 对话

Stitch 让你配置*服务*与外部[服务交互，比如 AWS Kinesis](https://docs.mongodb.com/stitch/reference/partner-services/amazon-service/) 。在左边的导航栏中选择**服务**，点击**添加服务**按钮，选择 AWS 服务，设置 **AWS [访问密钥 ID，以及](https://aws.amazon.com/blogs/security/wheres-my-secret-access-key/)秘密访问密钥**。

![Figure 3\. Service Configuration in Stitch](img/3e946f08ccd3291be803ac7745ddbcd1.png)

*服务*使用*规则*来指定一个服务针脚可以使用的方面，以及如何使用。通过点击标有 NEW RULE 的按钮，添加一条规则，使该服务能够与 Kinesis 进行通信。将规则命名为“kinesis ”,因为我们将使用这个特定的规则来启用与 AWS Kinesis 的通信。在标记为 Action 的部分中，选择标记为 Kinesis 的 API，然后选择所有操作。

![Figure 4\. Add a rule to enable integration with Kinesis](img/4d20cdda547701c98dbb8960c85cb425.png)

### 编写一个函数，将文档流式传输到 Kinesis

现在我们有了一个工作的 AWS 服务，我们可以用它将记录放入 Kinesis 流中。在 Stitch 中，我们是通过函数来实现的。让我们设置一个 *putKinesisRecord* 函数。

从左侧菜单中选择函数，然后单击创建新函数。为函数提供一个名称，并将以下内容粘贴到函数体中。

![Figure 5\. Example Function - putKinesisRecord](img/dd8f2d9c885d29744e93422d74973c5a.png)

```
exports = function(event){
 const awsService = context.services.get('aws');
try{
   awsService.kinesis().PutRecord({
     Data: JSON.stringify(event.fullDocument),
     StreamName: "stitchStream",
     PartitionKey: "1"
      }).then(function(response) {
        return response;
      });
}
catch(error){
  console.log(JSON.parse(error));
}
}; 
```

### 测试功能

让我们通过手动调用该函数来确保一切正常。在**函数编辑器**中，点击**控制台**查看 Stitch 的交互式 javascript 控制台。

![](img/d9a5e36ac3b08943db161033a4324920.png)

从触发器调用的函数需要一个事件。为了测试函数的执行，我们需要向函数传递一个虚拟事件。在 Stitch 中从控制台创建变量很简单。只需将变量的值设置为 JSON 文档。对于我们的简单示例，使用以下代码:

```
event = {
   "operationType": "replace",
   "fullDocument": {
       "color": "black",
       "inventory": {
           "$numberInt": "1"
       },
       "overview": "test document",
       "price": {
           "$numberDecimal": "123"
       },
       "type": "backpack"
   },
   "ns": {
       "db": "streamdata",
       "coll": "clickdata"
   }
}
exports(event); 
```

将以上内容粘贴到控制台上，点击**按钮，运行功能为**。选择一个用户，该功能将执行。

哒哒！

### 用缝合触发器把它放在一起

我们将 MongoDB 集合放在 Atlas 中，从我们的 web 应用程序接收事件。我们已经为数据准备好了运动流。我们有一个缝合功能，可以将数据放入 Kinesis 流中。

配置缝合触发器非常简单，几乎是虎头蛇尾。单击左侧导航栏中的**触发器**，命名您的触发器，提供数据库和集合上下文，并选择 Stitch 将通过执行函数对其做出反应的数据库事件。

对于数据库和集合，使用第一步中的名称。现在，我们将使用触发器设置想要观察的操作。(一些触发器可能会关心所有的操作——插入、更新、删除和替换——而另一些可能更有效，因为它们在逻辑上只关心其中的一些操作。)在我们的例子中，我们将观察插入、更新和替换操作。

现在我们指定我们的*putkinesirecord*函数作为链接函数，我们就完成了。

![Figure 6\. Trigger Configuration in Stitch](img/cfe60218d8f689ba87ea58e4d3ec342b.png)

作为触发器执行的一部分，Stitch 将转发与触发器事件相关联的细节，包括事件中涉及的整个文档(即集合中新插入、更新或删除的文档)。)在这里，我们可以评估传入文档的一些条件或属性，并决定是否将记录放到流中。

### 测试触发器！

Amazon 提供了一个仪表板，让您可以查看与进入数据流的数据相关的详细信息。

![Figure 7\. Kinesis Stream Monitoring](img/7bfab8126bedfa628ee03d47b119fd62.png)

当您在 Stitch 中执行该函数时，您将开始看到数据进入 Kinesis 流。

### 构建更多功能

到目前为止，我们的触发器是非常基本的——它监视一个集合，当任何更新或插入发生时，它将整个文档馈送到我们的 Kinesis 流。从这里，我们可以构建一些更智能的功能。作为本文的总结，让我们看看一旦数据被持久地存储在 MongoDB 中并放入流中，我们可以对其做些什么。

一旦记录在 Kinesis 流中，您就可以配置额外的下游服务来处理数据。一个常见的用例结合了 Amazon Kinesis 数据分析来对流式数据执行分析。[亚马逊 Kinesis 数据分析](https://aws.amazon.com/kinesis/data-analytics/)提供预配置的模板来完成异常检测、简单警报、聚合等任务。

例如，我们的数据流将包含来自购买的订单。这些订单可能来自销售点系统，也可能来自我们基于 web 的电子商务应用程序。Kinesis Analytics 可用于创建处理传入数据流的应用程序。对于我们的例子，我们可以构建一个[机器学习算法](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/app-anomaly-detection.html)来检测数据中的异常，或者从我们流中的[滑动](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/sliding-window-concepts.html)或[翻滚](https://docs.aws.amazon.com/kinesisanalytics/latest/dev/tumbling-window-concepts.html)窗口数据创建一个产品性能排行榜。

![Figure 8\. Amazon Data Analytics - Anomaly Detection Example](img/46f937040431964a6908a97b4a21222c.png)

### 包扎

现在你可以把 MongoDB 和 Kinesis 连接起来了。从这里开始，您可以利用 Amazon Web Services 提供的众多服务中的任何一个来构建您的应用程序。在本系列的下一篇文章中，我们将重点关注将数据从 Kinesis 返回到 MongoDB。与此同时，让我们知道你用 Atlas，Stitch 和 Kinesis 构建了什么！

#### 资源

**蒙戈布地图集**

*   [入门](https://www.mongodb.com/presentations/tutorial-series-getting-started-with-mongodb-atlas)–教程播放列表
*   [免费注册](https://www.mongodb.com/cloud/atlas/lp/general)
*   [常见问题解答](https://www.mongodb.com/cloud/atlas/faq)

**MongoDB 针法**

*   [入门文档](https://docs.mongodb.com/stitch/getting-started/)
*   [MongoDB 缝合教程](https://docs.mongodb.com/stitch/tutorials/)
*   [MongoDB Stitch 白皮书](https://www.mongodb.com/collateral/mongodb-stitch-serverless-platform)
*   [网络研讨会–2018 年 8 月 8 日](https://www.mongodb.com/webinar/mongodb-stitch)

**亚马逊 Kinesis**

*   [入门](https://docs.aws.amazon.com/streams/latest/dev/getting-started.html)
*   [Kinesis 数据流](https://aws.amazon.com/kinesis/data-streams/)
*   [Kinesis 数据分析](https://aws.amazon.com/kinesis/data-analytics/)

## 分享这篇文章