# 构建新的解析服务器&基于 MongoDB Atlas 的应用程序

> 原文：<https://www.sitepoint.com/building-new-parse-server-mongodb-atlas-based-application/>

*本文最初发表于 [mongoDB](https://www.mongodb.com/blog/post/building-a-new-parse-server-and-mongodb-atlas-based-application?utm_medium=sp-synd&utm_source=sitepoint&utm_content=parse&jmp=sp-ref) 。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

无论您是从废弃的 Parse.com(api.parse.com)迁移还是构建新的应用程序，解析服务器社区都是活跃而强大的，并且自从[解析服务器](https://github.com/ParsePlatform/parse-server)版本 2.1.11 以来，就有了对 MongoDB 3.2 的支持，这使得 MongoDB Atlas 成为基于解析服务器的应用程序的理想后端。

现有的托管 Parse/api.parse.com 用户可以使用 [Parse 的数据库迁移工具](http://blog.parse.com/announcements/introducing-parse-server-and-the-database-migration-tool/)使用如下连接字符串直接将他们的后端迁移到 MongoDB Atlas(粗体项目替换为您的详细信息):

```
mongodb://username:password@node1.mongodb.net:27017,node2.mongodb.net:27017,node3.mongodb.net:27017/applicationDbName?replicaSet=clusterName-shard-0&ssl=true&authSource=admin
```

我们将在这篇博文中了解到:

*   如何部署一个 [MongoDB Atlas](https://www.mongodb.com/atlas?utm_medium=sp-synd&utm_source=sitepoint&utm_content=parse&jmp=sp-ref) 集群
*   如何部署[解析服务器](https://github.com/ParsePlatform/parse-server)(在我们的例子中，我们将展示如何使用 AWS Elastic Beanstalk quick start 进行部署，但是已经更新为使用最新版本的解析服务器)
*   如何配置解析服务器连接到 MongoDB Atlas
*   如何确认连通性

## 如何用 MongoDB Atlas 后端设置一个新的示例解析服务器应用程序

1.  部署 MongoDB Atlas 集群
2.  考虑规模选项，但对于 hello world 风格的应用程序，先从小规模开始。您总是可以在以后扩展(MongoDB Atlas 允许您迁移到更大的实例，而不会让您的数据库停机)。
3.  在[mongodb.com/atlas](https://www.mongodb.com/atlas?utm_medium=sp-synd&utm_source=sitepoint&utm_content=parse&jmp=sp-ref)注册 MongoDB Atlas
4.  构建并部署您的第一个集群(在我们的示例中，我们将使用一个小型 M10 实例大小的副本集，并将其部署到美国东部地区)![Parse Server Clusters](img/937196c84c5c7a86589d11bb0c087d7c.png)
5.  ![MongoDB Atlas Cluster](img/2cc59d7250874ce582bce096ac0ce605.png)
6.  我们将在**应用数据库名称数据库**上创建一个至少具有读写权限的用户(或者自动创建的具有 readWriteAnyDatabase@admin 权限的用户也可以)![Parse Server Mongo DB Atlas](img/b0f4b5a8093e76875b96e947ca9f1218.png)
7.  出于测试的目的，我们最初将对所有 IP 地址开放 IP 地址(0.0.0.0/0):以后我们应该只对我们的应用服务器的公共 IP 地址开放。![Parse Server MongoDB Atlas](img/b830a664765986759922e457a611d5a5.png)
8.  选择部署解析服务器的位置和方式:
9.  此处描述了许多选项[，其中一些选项提供了比其他选项更简单的设置。AWS 弹性豆茎和](https://github.com/ParsePlatform/parse-server/wiki#community-links) [Heroku](https://heroku.com/deploy?template=https://github.com/ParsePlatform/parse-server-example) 是容易的选择。

### 为了这篇博文的目的，我们将使用 AWS Elastic Beanstalk 作为解析服务器的快速入门，方法是使用下面的 URL(需要 AWS 帐户):

1.  [点击查看 AWS Elastic Beanstalk](https://console.aws.amazon.com/elasticbeanstalk/home?region=us-east-1#/newApplication?applicationName=ParseServer&solutionStackName=Node.js&tierName=WebServer&instanceType=t2.small&sourceBundleUrl=https://s3.amazonaws.com/elasticbeanstalk-samples-us-east-1/eb-parse-server-sample/parse-server-example.zip) 查看解析服务器快速启动部署示例。
2.  但是我们将确保安装 Parse Server 2.1.12 或更高版本，例如在 [parse-server-example](https://github.com/ParsePlatform/parse-server-example) 中，确保 package.json 文件包含“Parse-Server”:“2.2.16”(其中 2 . 2 . 16 是本文撰写时的最新版本)。
3.  解析服务器示例可以从 github: ![GitHub Parse Server Example Repo](img/95902e28de2e0d69f7a27e13a08628fd.png)下载
4.  如果我们提取 Zip 文件，我们可以编辑 package.json
    ![Parse Server Example GitHub Download Button](img/c904c969a61a6c87309adf69ec9e3f48.png)中的版本
5.  我们将把解析服务器版本设置为 2.2.16(撰写本文时的最新版本)![Package.json with Parse Server Installation Info](img/7b440b11161b67de80800a2d3db8fac5.png)
6.  我们将选择目录中的文件，并将它们重新压缩成一个新的 Zip 文件![Files compression](img/f7627f5a65e569a137601aa4234ac65d.png)
7.  我们将上传新的 zip 文件，这样就可以部署它了![Uploading new zip file](img/83b53e795431a16c2a3cca170df07eda.png)
8.  配置解析服务器以连接到 MongoDB Atlas
9.  AWS 弹性豆茎 UI 内部![AWS Elastic Beanstalk UI](img/fb72cb42a2d493e4242ac389d329fe26.png)
10.  我们将导航至左侧菜单
    ![Parse Server](img/707992a05cb38a88ff76c38875b8baa7.png)的“配置”部分
11.  然后，我们将通过单击齿轮图标导航到“软件配置”部分，并向下滚动到“环境属性”部分![Environment Properties](img/1e25fe69d075075616e746a0a01bc186.png)
12.  在环境属性中，我们将使用我们想要的任何 myAppId、myFileKey、mySecretMasterKey(因为这是一个新的应用程序，所以我们设置了它们)。
13.  我们将把设置为显示在 AWS Elastic Beanstalk 应用程序 UI 顶部“URL:…”![Setting the SERVER_URL](img/4bd4d8ffd0d086678f9bcfe5bed6df45.png)右侧的内容
14.  我们将如下设置 DATABASE_URI(用我们特定集群的详细信息替换粗体文本)
15.  `mongodb://username:password@node1.mongodb.net:27017,node2.mongodb.net:27017,node3.mongodb.net:27017/applicationDbName?replicaSet=clusterName-shard-0&ssl=true&authSource=admin`
16.  我们可以看到，适当的 MongoDB URI 应该在 MongoDB Atlas 的“Connect”UI 中，位于驱动程序连接部分![Location of appropriate MongoDB URI](img/31606b393dcdb705eb7eb19cb191c3c0.png)之下
17.  测试以确认我们的示例应用程序与解析服务器和 MongoDB Atlas 后端的连接性:

```
$ curl -X POST \
> -H "X-Parse-Application-Id: newParseTest" \
> -H "Content-Type: application/json" \
> -d '{"score":1337,"playerName":"John Doe","cheatMode":false}' \
> http://parseserver-365pk-env.us-east-1.elasticbeanstalk.com/parse/classes/GameScore
```

```
Returns:
{
  "objectId": "YMgGV6kVTP",
  "createdAt": "2016-08-26T14:54:26.580Z"
}
```

```
$ curl -X GET \
> -H "X-Parse-Application-Id: newParseTest" \
> -H "X-Parse-Master-Key: MASTER_KEY" \
> http://parseserver-365pk-env.us-east-1.elasticbeanstalk.com/parse/classes/GameScore
```

```
Returns:
{
  "results": [
    {
      "objectId": "YMgGV6kVTP",
      "score": 1337,
      "playerName": "John Doe",
      "cheatMode": false,
      "createdAt": "2016-08-26T14:54:26.580Z",
      "updatedAt": "2016-08-26T14:54:26.580Z"
    }
  ]
}
```

现在您可以使用 SDK 构建一个新的应用程序，指向您的 Parse Server + MongoDB Atlas 实例！

## 分享这篇文章