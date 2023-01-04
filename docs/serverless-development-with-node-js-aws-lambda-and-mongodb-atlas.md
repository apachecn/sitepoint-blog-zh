# 使用 Node.js、AWS Lambda 和 MongoDB Atlas 进行无服务器开发

> 原文：<https://www.sitepoint.com/serverless-development-with-node-js-aws-lambda-and-mongodb-atlas/>

*本文最初发表于 [mongoDB](https://www.mongodb.com/blog/post/serverless-development-with-nodejs-aws-lambda-mongodb-atlas?utm_medium=sp-synd&utm_source=sitepoint&utm_content=serverless&jmp=sp-ref) 。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

近年来，开发人员的情况发生了巨大的变化。对于我们开发人员来说，在我们自己的机器上运行我们所有的工具(数据库、web 服务器、开发 ide……)曾经是相当常见的，但是云服务，如 [GitHub](https://github.com/) 、 [MongoDB Atlas](https://www.mongodb.com/cloud/atlas?jmp=adref?utm_medium=sp-synd&utm_source=sitepoint&utm_content=serverless&jmp=sp-ref) 和 [AWS Lambda](https://aws.amazon.com/lambda/) 正在彻底改变游戏。它们使得开发人员在任何地方和任何设备上编写和运行代码变得越来越容易，而没有(或很少)依赖性。

几年前，如果您的机器崩溃、丢失或只是没电了，您可能需要几天时间才能获得一台新机器，并按照以前的方式正确设置和配置您需要的一切。

有了云中的开发人员工具，您现在可以从一台笔记本电脑切换到另一台笔记本电脑，并且中断最小。然而，这并不意味着一切都是美好的。在云中编写和调试代码仍然具有挑战性；作为开发人员，我们知道拥有一个本地开发环境，虽然更加轻量级，但仍然是非常有价值的。

这正是我在这篇博客中试图向您展示的:如何轻松地将一个 [AWS Lambda](https://aws.amazon.com/lambda/) Node.js 函数与托管在 [MongoDB Atlas](https://www.mongodb.com/cloud/atlas?jmp=adref?utm_medium=sp-synd&utm_source=sitepoint&utm_content=serverless&jmp=sp-ref) 中的 MongoDB 数据库集成，即 MongoDB 的 DBaaS(数据库即服务)。更具体地说，我们将编写一个简单的 Lambda 函数，在 MongoDB Atlas 数据库中存储的集合中创建一个文档。我将一步一步地指导你完成这个教程，你应该在不到一个小时内完成。

让我们从启动和运行的必要要求开始:

1.  拥有 IAM 和 Lambda 服务管理权限的用户可以使用的 Amazon Web Services 帐户。如果你还没有，请[注册一个免费的 AWS 账户](https://aws.amazon.com/console)。
2.  带有 [Node.js](https://nodejs.org/) 的本地机器(我告诉过你我们不会这么容易摆脱本地开发环境……)。我们将在下面的教程中使用 Mac OS X，但在 Windows 或 Linux 上执行同样的任务应该相对容易。
3.  一个[活蹦乱跳的 MongoDB 图集](https://www.mongodb.com/cloud/atlas?jmp=adref?utm_medium=sp-synd&utm_source=sitepoint&utm_content=serverless&jmp=sp-ref)集群。如果你还没有的话，[注册一个免费的 MongoDB Atlas 账户](https://cloud.mongodb.com/user/register?jmp=adref&_ga=2.124298826.1707121898.1519563647-1376163451.1519563647?utm_medium=sp-synd&utm_source=sitepoint&utm_content=serverless&jmp=sp-ref)，[只需点击几下就能创建一个集群](https://docs.atlas.mongodb.com/create-new-cluster/?_ga=2.124298826.1707121898.1519563647-1376163451.1519563647)。您甚至可以尝试我们的 [M0，免费集群层](https://www.mongodb.com/blog/post/announcing-free-tier-and-live-migration-tool-for-mongodb-atlas?utm_medium=sp-synd&utm_source=sitepoint&utm_content=serverless&jmp=sp-ref)，非常适合小型开发项目！).

既然您已经了解了需求，那么让我们来谈谈编写、测试和部署 Lambda 函数的具体步骤:

1.  默认情况下，MongoDB Atlas 是安全的，但是作为应用程序开发人员，我们应该采取一些措施来确保我们的应用程序符合[最低权限访问最佳实践](https://en.wikipedia.org/wiki/Principle_of_least_privilege)。也就是说，我们将通过创建一个对我们的应用程序数据库只有读/写权限的 MongoDB Atlas 数据库用户来微调权限。
2.  我们将在本地机器上建立一个 Node.js 项目，并确保在将 lambda 代码部署到 Amazon Web Services 之前，在本地对其进行端到端测试。
3.  然后，我们将创建 AWS Lambda 函数，并上传 Node.js 项目来初始化它。
4.  最后，我们将对我们的 Lambda 函数进行一些修改，以加密一些敏感数据(如 MongoDB Atlas 连接字符串)并从函数代码中解密。

## 关于 VPC 对等的简短说明

我没有深入研究在我们的 MongoDB Atlas 集群和 AWS Lambda 之间设置 VPC 对等的细节，原因有二:1)我们已经有了一个详细的 [VPC 对等文档页面](https://docs.atlas.mongodb.com/security-vpc-peering/?_ga=2.120686536.1707121898.1519563647-1376163451.1519563647)和一个我强烈推荐的 Atlas 帖子中的 [VPC 对等；2)M0 集群(我用来构建那个演示)](https://www.mongodb.com/blog/post/introducing-vpc-peering-for-mongodb-atlas?jmp=adref?utm_medium=sp-synd&utm_source=sitepoint&utm_content=serverless&jmp=sp-ref)[不支持 VPC 对等](https://docs.atlas.mongodb.com/create-new-cluster/&_ga=2.124969674.1707121898.1519563647-1376163451.1519563647#atlas-free-tier?jmp=adref)。

如果您没有设置 VPC 对等，会发生以下情况:

1.  您必须将臭名昭著的 0.0.0.0/0 CIDR 块添加到您的 MongoDB Atlas 集群 IP 白名单中，因为您不知道 AWS Lambda 使用哪个 IP 地址来调用您的 Atlas 数据库。
2.  你将为你的 Lambda 函数和你的 Atlas 集群之间的带宽使用付费。

如果您只是想编写这个演示代码，这两个警告可能没问题，但是如果您计划部署一个生产就绪的 Lambda-Atlas 集成，设置 VPC 对等是我们强烈推荐的安全最佳实践。M0 是我们目前的免费产品；查看我们的 [MongoDB Atlas 定价页面](https://www.mongodb.com/cloud/atlas/pricing?jmp=adref?utm_medium=sp-synd&utm_source=sitepoint&utm_content=serverless&jmp=sp-ref)，了解所有可用的实例大小。

*提醒一下，对于开发环境和低流量网站，M0、M10 和 M20 实例大小应该没问题。但是，对于支持高流量应用程序或大型数据集的生产环境，建议使用 M30 或更大的实例大小。*

## 在 MongoDB Atlas 集群中设置安全性

确保您的应用程序符合最低特权访问策略对于保护您的数据免受恶意威胁至关重要。这就是为什么我们将设置一个特定的数据库用户，该用户只能对我们的旅行数据库进行读/写访问。让我们看看如何在 MongoDB Atlas 中实现这一点:

在 ***集群*** 页面，选择 ***安全*** 选项卡，并按下**添加新用户**按钮

![Clusters](img/f5aecfee8264a072f52649e2cc852579.png)

![Lambda user](img/e695e99e7e5ce9db98dda9c3a817028c.png)

在 ***用户权限*** 部分，选择<strongshow advanced="" options="">链接。这允许我们在特定的数据库上分配读/写，而不是任何数据库。</strongshow>

![user privileges](img/717ccb6c1a0fec8578f3234d716149ac.png)

然后，您可以选择分配更细粒度的访问控制权限:

![Access Control](img/0a615fc61d227804155f2bfb39c4899a.png)

在**选择角色**下拉列表中，选择读写，并在**数据库**字段中填写您将用来存储文档的数据库的名称。我已经选择把它命名为`travel`。

![Select Roles](img/98741c36c6d98519c96d2aebe97d8852.png)

在 ***密码*** 部分，使用**自动生成安全密码**按钮(并记录生成的密码)或设置您喜欢的密码。然后按下**添加用户**按钮确认用户创建。

让我们抓住集群连接字符串，因为我们需要它在 Lambda 代码中连接到我们的 MongoDB Atlas 数据库:

假设你已经[创建了一个 MongoDB Atlas 集群](https://docs.atlas.mongodb.com/create-new-cluster?jmp=adref&_ga=2.225103450.1707121898.1519563647-1376163451.1519563647)，点击集群旁边的**连接**按钮:

![Connect cluster](img/7a3cc24aafecedc174799e0eb8467f5b.png)

复制 ***URI 连接字符串*** 值，并将其安全地存储在文本文档中。我们将在后面的代码中需要它，以及您刚刚设置的密码。

![URI Connection String](img/5b1a115fe17e8ad90c6ac9c901c43097.png)

此外，如果您没有使用 VPC 对等，请导航到 ***IP 白名单*** 选项卡并添加 0.0.0.0/0 CIDR 块或按下**允许从任何地方访问**按钮。提醒一下，强烈建议不要在生产环境中使用该设置，这可能会使您的 MongoDB Atlas 集群容易受到恶意攻击。

![Add whitelist entry](img/137f534da267275aeff39c1d624a55ce.png)

## 创建一个本地 Node.js 项目

尽管 Lambda 函数在多种语言中都得到了支持，但我还是选择使用 Node.js，这是因为 JavaScript 作为一种通用编程语言越来越受欢迎，而且 [MEAN](https://www.mongodb.com/blog/post/the-modern-application-stack-part-1-introducing-the-mean-stack?utm_medium=sp-synd&utm_source=sitepoint&utm_content=serverless&jmp=sp-ref) 和 [MERN](https://www.mongodb.com/blog/post/the-modern-application-stack-part-5-using-reactjs-es6-and-jsx-to-build-a-ui-the-rise-of-mern?utm_medium=sp-synd&utm_source=sitepoint&utm_content=serverless&jmp=sp-ref) 栈(缩写为 **M** ongoDB、 **E** xpress.js、**Angular**/**R**eact、 **N** ode.js)取得了巨大成功另外，说实话，我喜欢它是一种解释型的轻量级语言，不需要大量的开发工具和编译器。

现在是时候写一些代码了，所以让我们继续使用 Node.js 作为 Lambda 函数的语言选择。

首先创建一个文件夹，比如 lambda-atlas-create-doc

```
mkdir lambda-atlas-create-doc 
&& cd lambda-atlas-create-doc
```

接下来，从终端控制台运行以下命令，用 package.json 文件初始化我们的项目

```
npm init
```

系统会提示您配置一些字段。我将它们留给您自己去创造，但是请注意，我选择将入口点设置为 app.js(而不是默认的 index.js ),所以您可能也想这样做。

我们将需要使用 [MongoDB Node.js 驱动程序](https://www.npmjs.com/package/mongodb),这样我们就可以从 Lambda 函数连接到我们的 MongoDB 数据库(在 Atlas 上),所以让我们通过从我们的项目根目录运行以下命令来安装它:

```
npm install mongodb --save
```

我们还想在本地编写和测试我们的 lambda 函数，以加快开发并简化调试，因为在 Amazon Web Services 中每次实例化一个 Lambda 函数并不是特别快(调试实际上是不存在的，除非你是`console.log()`函数的粉丝)。我选择使用 [lambda-local 包](https://www.npmjs.com/package/lambda-local)，因为它提供了对环境变量的支持(我们稍后会用到):

```
(sudo) npm install lambda-local -g
```

创建一个`app.js`文件。这将是包含我们的 lambda 函数的文件:

```
touch app.js
```

现在，您已经导入了所有必需的依赖项并创建了 Lambda 代码文件，在您选择的代码编辑器(Atom、Sublime Text、Visual Studio Code……)中打开 app.js 文件，并用以下代码段初始化它:

```
'use strict'

var MongoClient = require('mongodb').MongoClient;

let atlas_connection_uri;
let cachedDb = null;

exports.handler = (event, context, callback) => {
  var uri = process.env['MONGODB_ATLAS_CLUSTER_URI'];

  if (atlas_connection_uri != null) {
    processEvent(event, context, callback);
  } 
  else {
    atlas_connection_uri = uri;
    console.log('the Atlas connection string is ' + atlas_connection_uri);
    processEvent(event, context, callback);
  } 
};

function processEvent(event, context, callback) {
  console.log('Calling MongoDB Atlas from AWS Lambda with event: ' + JSON.stringify(event));
}
```

让我们暂停一下，评论一下上面的代码，因为您可能已经注意到了一些特殊的结构:

*   该文件完全按照 Amazon Web Services 期望的 Lambda 代码编写(例如，使用“exports.handler”函数)。这是因为我们正在使用 [lambda-local](https://www.npmjs.com/package/lambda-local) 来本地测试我们的 lambda 函数，这方便了我们按照 AWS Lambda 期望的方式编写代码。一会儿会有更多的内容。
*   我们正在声明 MongoDB Node.js 驱动程序，它将帮助我们连接和查询我们的 MongoDB 数据库。
*   还要注意，我们在处理函数之外声明了一个`cachedDb`对象。顾名思义，它是一个我们计划在 AWS Lambda 为我们的函数实例化的底层容器期间缓存的对象。这使得我们可以节省一些宝贵的毫秒(甚至几秒)来创建 Lambda 和 MongoDB Atlas 之间的数据库连接。要了解更多信息，请阅读我的后续博文[如何用 MongoDB Atlas](https://www.mongodb.com/blog/post/optimizing-aws-lambda-performance-with-mongodb-atlas-and-nodejs?utm_medium=sp-synd&utm_source=sitepoint&utm_content=serverless&jmp=sp-ref) 优化 Lambda 性能。
*   我们使用一个名为`MONGODB_ATLAS_CLUSTER_URI`的环境变量来传递我们的 Atlas 数据库的 uri 连接字符串，主要是出于安全目的:我们显然不想在我们的函数代码中硬编码这个 uri，以及非常敏感的信息，比如我们使用的用户名和密码。由于从 2016 年 11 月起 [AWS Lambda 支持环境变量](https://aws.amazon.com/blogs/aws/new-for-aws-lambda-environment-variables-and-serverless-application-model)(正如 [lambda-local](https://www.npmjs.com/package/lambda-local) NPM 包所做的那样)，我们不使用它们就是失职。
*   函数代码看起来有点复杂，似乎没有用的 if-else 语句和 processEvent 函数，但当我们使用 AWS 密钥管理服务(KMS)添加解密例程时，一切都会变得清楚。事实上，我们不仅希望将我们的 MongoDB Atlas 连接字符串存储在一个环境变量中，而且还希望加密它(使用 AWS KMS ),因为它包含高度敏感的数据(请注意，即使您有免费的 AWS 帐户，当您使用 AWS KMS 时也可能会产生费用)。

现在我们已经完成了代码注释，让我们创建一个`event.json`文件(在根项目目录中)并用以下数据填充它:

```
{
  "address" : {
    "street" : "2 Avenue",
    "zipcode" : "10075",
    "building" : "1480",
    "coord" : [ -73.9557413, 40.7720266 ]
  },
  "borough" : "Manhattan",
  "cuisine" : "Italian",
  "grades" : [
    {
      "date" : "2014-10-01T00:00:00Z",
      "grade" : "A",
      "score" : 11
    },
    {
      "date" : "2014-01-16T00:00:00Z",
      "grade" : "B",
      "score" : 17
    }
  ],
 "name" : "Vella",
 "restaurant_id" : "41704620"
}
```

(如果您想知道，这个 JSON 文件就是我们将发送到 MongoDB Atlas 来创建我们的 [BSON](http://bsonspec.org/) 文档的文件)

接下来，通过在终端控制台中运行以下命令，确保设置正确:

```
lambda-local -l app.js -e event.json -E {\"MONGODB_ATLAS_CLUSTER_URI\":\"mongodb://lambdauser:$PASSWORD@lambdademo-shard-00-00-7xh42.mongodb.net:27017\,lambdademo-shard-00-01-7xh42.mongodb.net:27017\,lambdademo-shard-00-02-7xh42.mongodb.net:27017/$DATABASE?ssl=true\&replicaSet=lambdademo-shard-0\&authSource=admin\"}
```

如果您想用自己的集群 URI 连接字符串来测试它(我确信您会这样做)，不要忘记对 E 参数中的双引号、逗号和&字符进行转义，否则 lambda-local 将抛出一个错误(您还应该用自己的值替换$PASSWORD 和$DATABASE 关键字)。

在本地运行它之后，您应该得到以下控制台输出:

![Console output](img/c7ac82e481fbc9267f31cb15b3639a8b.png)

如果您得到一个错误，检查您的连接字符串和双引号/逗号/和号转义(如上所述)。

现在，让我们通过定制`processEvent()`函数并添加一个`createDoc()`函数来开始我们函数代码的实质内容:

```
function processEvent(event, context, callback) {
  console.log('Calling MongoDB Atlas from AWS Lambda with event: ' + JSON.stringify(event));
  var jsonContents = JSON.parse(JSON.stringify(event));

  //date conversion for grades array
  if(jsonContents.grades != null) {
    for(var i = 0, len=jsonContents.grades.length; i  connecting to database');
    MongoClient.connect(atlas_connection_uri, function (err, db) {
      cachedDb = db;
        return createDoc(db, jsonContents, callback);
      });
    }
    else {
      createDoc(cachedDb, jsonContents, callback);
    }
  }
  catch (err) {
    console.error('an error occurred', err);
  }
}

function createDoc (db, json, callback) {
  db.collection('restaurants').insertOne( json, function(err, result) {
    if(err!=null) {
      console.error("an error occurred in createDoc", err);
      callback(null, JSON.stringify(err));
    }
    else {
      console.log("Kudos! You just created an entry into the restaurants collection with id: " + result.insertedId);
      callback(null, "SUCCESS");
    }
    //we don't need to close the connection thanks to context.callbackWaitsForEmptyEventLoop = false (above)
   //this will let our function re-use the connection on the next called (if it  can re-use the same Lambda container)
     //db.close();
  });
};
```

请注意，连接到 MongoDB Atlas 数据库并插入文档是多么容易，我添加了一小段代码来将 JSON 日期(格式化为符合 ISO 的字符串)转换为真实的 JavaScript 日期，MongoDB 可以将这些日期存储为 BSON 日期。

您可能还注意到了我的性能优化注释和对 context . callbackwaitsforemptyeventloop = false 的调用。如果你有兴趣理解它们的意思(我认为你应该理解！)，请参考我的后续博文《如何用 MongoDB Atlas 优化 Lambda 性能》。

现在你已经准备好在本地测试你的 Lambda 函数了。像以前一样使用相同的 lambda-local 命令，希望您会得到一个不错的“Kudos”成功消息:

![Console output](img/fef3907244d78dd0eb38db3e5ffc9cfe.png)

如果在您的本地机器上一切顺利，让我们将本地 Node.js 项目发布为一个新的 Lambda 函数！

## 创建 Lambda 函数

我们要做的第一步是压缩 Node.js 项目，因为我们不会在 Lambda 代码编辑器中编写 Lambda 代码函数。相反，我们将选择 zip 上传方法将我们的代码推送到 AWS Lambda。

我在终端控制台中使用过`zip`命令行工具，但是任何方法都可以(只要你压缩顶层文件夹中的文件，而不是顶层文件夹本身！) :

```
zip -r archive.zip node_modules/ app.js package.json
```

接下来，登录 AWS 控制台并导航到 [IAM 角色](https://console.aws.amazon.com/iam/home?#/roles)页面，使用**awslambdabasiceexecutionrole**权限策略创建一个角色(例如 LambdaBasicExecRole ):

![AWS Lambda Basic Execution Role](img/83b698d81d86db49780d687878a62490.png)

现在让我们导航到 AWS Lambda 页面。点击**立即开始**(如果你从未创建过 Lambda 函数)或点击**创建 Lambda 函数**按钮。我们不会使用任何蓝图，也不会配置任何触发器，所以直接在左侧导航栏中选择**配置功能**:

![AWS Lambda Configure](img/19cb043eb90bd68140c4920e2af4ba5f.png)

在 ***配置功能*** 页面，输入您的功能名称(如`MongoDB_Atlas_CreateDoc`)。运行时被自动设置为`Node.js 4.3`，这对我们来说是完美的，因为这是我们将要使用的语言。在**代码录入类型**列表中，选择`Upload a .ZIP file`，如下截图所示:

![Configure function](img/e64540b0e02b2659a7bfbc5be3f0f622.png)

点击**上传**按钮，选择之前创建的压缩 Node.js 项目文件。

在 ***Lambda 函数处理程序和角色*** 部分，将**处理程序**字段值修改为`app.handler`(为什么？这里有一个提示:我已经为我的 Lambda 函数代码使用了一个`app.js`文件，而不是一个`index.js`文件…)并选择了我们刚刚创建的现有`LambdaBasicExecRole`角色:

![Lambda function handler](img/14a39e1feff505871f81465027e39cc9.png)

在 ***高级设置*** 部分，您可能想要将**超时**值增加到 5 或 10 秒，但这总是可以在以后进行调整的。将 VPC 和 KMS 键字段保留为默认值(除非您想使用 VPC 和/或 KMS 键)，然后按下**下一步**。

最后，检查你的 Lambda 函数，点击底部的**创建函数**。恭喜你，你的 Lambda 函数已经上线，你应该会看到一个类似下面截图的页面:

![Lambda create function](img/e1245182c9379deff82e7240f8e304d6.png)

但是你还记得我们对环境变量的使用吗？现在是配置它们并使用 [AWS 密钥管理服务](https://aws.amazon.com/kms/)来保护它们的时候了！

## 配置并保护您的 Lambda 环境变量

在 Lambda 函数的 ***代码*** 选项卡中向下滚动，并创建一个具有以下属性的环境变量:

| 名字 | 价值 |
| --- | --- |
| 蒙古数据库 _ 地图集 _ 集群 _URI | 你的 _ 地图集 _ 聚类 _ URI _ 值 |

![Environment variables](img/0c57b0eed61e9580da30f3f747d1e2bb.png)

此时，您可以按页面顶部的**保存并测试**按钮，但是为了额外的(也是推荐的)安全性，我们将加密该连接字符串。

选中 **Enable encryption helpers** 复选框，如果您已经创建了一个加密密钥，请选择它(否则，您可能需要创建一个——这相当简单):

![Encription key](img/596df60128e25af9923e6825a1c2a5e6.png)

接下来，为 MONGODB_ATLAS_CLUSTER_URI 变量选择 **Encrypt** 按钮:

![Select encrypt](img/58ccf2bc5b5ffad95c9b5f413b439a2c.png)

回到内联代码编辑器，在顶部添加以下行:

```
const AWS = require('aws-sdk');
```

并用以下代码替换“exports.handler”方法中“else”语句的内容:

```
const kms = new AWS.KMS();
  kms.decrypt({ CiphertextBlob: new Buffer(uri, 'base64') }, (err, data) => {
  if (err) {
    console.log('Decrypt error:', err);
    return callback(err);
  }
  atlas_connection_uri = data.Plaintext.toString('ascii');
  processEvent(event, context, callback);
});
```

(希望我们最初编写的复杂代码现在有意义了！)

如果你想查看我用过的[完整功能代码](https://gist.github.com/raphaellondner-mongodb/95a0e08b4a916fe8868928a1ba3134d5#file-lambda-mongodb-example)，可以查看下面的[要点](https://gist.github.com/raphaellondner-mongodb/95a0e08b4a916fe8868928a1ba3134d5#file-lambda-mongodb-example)。而对于 Git 粉丝来说， [full Node.js 项目源代码](https://github.com/rlondner/aws-lambda-atlas-sample)也可以在 [GitHub](https://github.com/rlondner/aws-lambda-atlas-sample) 上获得。

现在按下**保存和测试**按钮，在**输入测试事件**文本编辑器中，粘贴我们 event.json 文件的内容:

![Input test event](img/2455e15649b96a51d6e37a0e616a9049.png)

滚动并按下**保存和测试**按钮。

如果您正确地配置了一切，您应该在 Lambda 日志输出中收到以下成功消息:

![Lambda log output](img/aad58eee3971e6319de83a2f8a38fc5f.png)

太棒了。你可以在继续阅读前几分钟细细品味你的成功。

## 下一步是什么？

我希望这篇 AWS Lambda-MongoDB Atlas 集成教程为您提供了开始第一个 Lambda 项目的正确步骤。现在，您应该能够在本地编写和测试 Lambda 函数，并在 AWS KMS 中安全地存储敏感数据(比如您的 MongoDB Atlas 连接字符串)。

那么接下来你能做什么呢？

*   如果你还没有一个 [MongoDB Atlas](https://www.mongodb.com/cloud/atlas?jmp=adref?utm_medium=sp-synd&utm_source=sitepoint&utm_content=serverless&jmp=sp-ref) 账户，现在[创建一个还为时不晚！](https://www.mongodb.com/cloud/atlas?jmp=adref?utm_medium=sp-synd&utm_source=sitepoint&utm_content=serverless&jmp=sp-ref)
*   如果您不熟悉 [MongoDB Node.js 驱动程序](https://www.npmjs.com/package/mongodb)，请查看我们的 [Node.js 驱动程序文档](http://mongodb.github.io/node-mongodb-native/2.2/?_ga=2.145796852.1707121898.1519563647-1376163451.1519563647)以了解如何充分利用 MongoDB API。此外，我们还为刚开始使用 MongoDB 的 Node.js 开发人员提供了一个[在线 Node.js 课程](https://university.mongodb.com/courses/M101JS/about?_ga=2.124879562.1707121898.1519563647-1376163451.1519563647)。
*   了解如何可视化您使用 Lambda 函数创建的数据，下载 [MongoDB Compass](https://www.mongodb.com/products/compass?utm_medium=sp-synd&utm_source=sitepoint&utm_content=serverless&jmp=sp-ref) 并阅读[使用 MongoDB Compass](https://www.mongodb.com/blog/post/visualizing-your-data-with-mongodb-compass?utm_medium=sp-synd&utm_source=sitepoint&utm_content=serverless&jmp=sp-ref) 可视化您的数据，了解如何将其连接到 MongoDB Atlas。
*   打算建很多 Lambda 函数？通过阅读我们的[集成 MongoDB Atlas、Twilio 和 AWS 简单电子邮件服务与 AWS 步骤功能的帖子](https://www.mongodb.com/blog/post/integrating-mongodb-atlas-twilio-and-aws-simple-email-service-with-aws-step-functions?utm_medium=sp-synd&utm_source=sitepoint&utm_content=serverless&jmp=sp-ref)，了解如何将它们与 AWS 步骤功能协调起来。
*   了解如何在更复杂的场景中集成 MongoDB 和 AWS Lambda，查看我们更高级的博客文章:[用 AWS Lambda 和 MongoDB Atlas 开发脸书聊天机器人](https://www.mongodb.com/blog/post/developing-a-facebook-chatbot-with-aws-lambda-and-mongodb-atlas?utm_medium=sp-synd&utm_source=sitepoint&utm_content=serverless&jmp=sp-ref)。

当然，不要犹豫问我们任何问题，或者在下面的评论中留下你的反馈。编码快乐！

喜欢这篇文章吗？重播我们的网络研讨会，我们有一个关于 AWS Lambda 无服务器架构的交互式教程。

## 分享这篇文章