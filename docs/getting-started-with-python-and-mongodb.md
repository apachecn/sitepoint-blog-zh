# Python 和 MongoDB 入门

> 原文：<https://www.sitepoint.com/getting-started-with-python-and-mongodb/>

*本文最初发表于 [MongoDB](https://www.mongodb.com/blog/post/getting-started-with-python-and-mongodb) 。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

您可以通过利用其中一个驱动程序来开始使用 MongoDB 和您最喜欢的编程语言，其中许多驱动程序由 MongoDB 工程师维护，其他的由社区成员维护。MongoDB 有一个本地 Python 驱动程序 PyMongo，以及一个致力于使驱动程序适合 Python 社区需求的驱动工程师团队。

在这篇面向不熟悉 MongoDB 的 Python 开发人员的文章中，您将学习如何完成以下工作:

*   使用 MongoDB Atlas 创建一个免费托管的 MongoDB 数据库
*   安装 Python 驱动程序 PyMongo
*   连接到 MongoDB
*   探索 MongoDB 集合和文档
*   使用 PyMongo 执行基本的创建、检索、更新和删除(CRUD)操作

我们开始吧！

通过 MongoDB Atlas 使用免费的 MongoDB 集群，您可以立即开始使用 MongoDB。MongoDB Atlas 是一个托管数据库服务，允许您选择您的数据库大小并获得一个连接字符串！如果您对使用免费层感兴趣，请遵循本文末尾附录部分的说明。

## 安装 Python 驱动程序

在本文中，我们将安装名为“PyMongo”的 Python 驱动程序。

虽然也有社区写的其他驱动，但是 PyMongo 是 MongoDB 的官方 Python 驱动。欲了解驱动程序的详细文档，请点击查看[文档。](https://api.mongodb.com/python/current/)

安装驱动程序最简单的方法是通过 pip 包管理系统。在命令行上执行以下命令:

```
python -m pip install pymongo 
```

注意:如果您使用的是 Atlas M0(自由层)集群，则必须使用 Python 2.7.9+和 Python 3.4 或更新版本。可以通过分别发出`python --version`和`pip list`命令来检查自己安装了哪个版本的 Python 和 PyMongo。

对于驱动程序安装的变化，请查看[完整文档](https://api.mongodb.com/python/3.4.0/installation.html):

一旦安装了 PyMongo，我们就可以编写第一个应用程序，它将返回关于 MongoDB 服务器的信息。在 Python 开发环境中或从文本编辑器中输入以下代码。

```
from pymongo import MongoClient
# pprint library is used to make the output look more pretty
from pprint import pprint
# connect to MongoDB, change the << MONGODB URL >> to reflect your own connection string
client = MongoClient(<<MONGODB URL>>)
db=client.admin
# Issue the serverStatus command and print the results
serverStatusResult=db.command("serverStatus")
pprint(serverStatusResult) 
```

用 MongoDB 的连接字符串替换`<<MONGODB URL>>`。将该文件另存为`mongodbtest.py`，并通过`python mongodbtest.py`从命令行运行它。

输出示例如下所示:

```
{u'asserts': {u'msg': 0,
              u'regular': 0,
              u'rollovers': 0,
              u'user': 0,
              u'warning': 0},
 u'connections': {u'available': 96, u'current': 4, u'totalCreated': 174L},
 u'extra_info': {u'note': u'fields vary by platform', u'page_faults': 0},
 u'host': u'cluster0-shard-00-00-6czvq.mongodb.net:27017',
 u'localTime': datetime.datetime(2017, 4, 4, 0, 18, 45, 616000),
.
.
.
} 
```

注意，`u`字符来自 Python 输出，这意味着字符串以 Unicode 存储。这个例子还使用了`pprint`库，它与 MongoDB 无关，但在这里仅用于使控制台的输出结构化并具有视觉吸引力。

在这个例子中，我们连接到我们的 MongoDB 实例并发出`db.serverStatus()`命令([引用](https://docs.mongodb.com/manual/reference/command/serverStatus/))。该命令返回关于我们的 MongoDB 实例的信息，并在本例中用作对 MongoDB 执行命令的一种方式。

如果您的应用程序运行成功，您就可以继续了！

## 探索收藏和文档

MongoDB 将数据存储在文档中。文档不像 Microsoft Word 或 Adode PDF 文档，而是基于 [JSON 规范](http://www.json.org/)的 JSON 文档。

JSON 文档的一个例子如下:

![JSON document example](img/02825f7d8d0c7e305cc9107bcd84c850.png)

请注意，文档不仅仅是键/值对，还可以包括数组和子文档。数据本身可以是不同的数据类型，例如地理空间、十进制和 ISODate 等。MongoDB 内部存储了 JSON 的二进制表示，称为 BSON。这允许 MongoDB 提供像 decimal 这样的数据类型，而这些数据类型在 JSON 规范中没有定义。欲了解更多关于 BSON 规格的信息，请查看其网站。

MongoDB 中的集合是文档的容器。数据库是集合的容器。这种分组类似于关系数据库，如下图所示:

| **关系概念** | **MongoDB 当量** |
| --- | --- |
| 数据库ˌ资料库 | 数据库ˌ资料库 |
| 桌子 | 收集 |
| 行 | 文档 |
| 索引 | 索引 |

将数据存储在文档中有很多好处。虽然更深入的讨论超出了本文的范围，但是从我们简单的 Python 脚本中可以看出一些优点，如动态、灵活的模式和存储数组的能力。关于 MongoDB 文档结构[的更多信息，请看一下在线文档](https://docs.mongodb.com/manual/core/document/)。

让我们看看如何使用 PyMongo 在 MongoDB 中对文档执行基本的 CRUD 操作。

## 使用 PyMongo 执行基本的 CRUD 操作

要用 PyMongo 建立到 MongoDB 的连接，可以使用 MongoClient 类。

```
from pymongo import MongoClient
client = MongoClient('<<MongoDB URL>>’) 
```

`<<MongoDB URL>>`是 MongoDB 连接字符串的占位符。关于如何创建 MongoDB 连接字符串的详细信息，请参见[连接字符串文档](https://docs.mongodb.com/manual/reference/connection-string/)。如果您将 Atlas 用于您的 MongoDB 数据库，请参考“测试您的连接”一节，以获得有关 MongoDB Atlas 的连接字符串的更多信息。

我们现在可以创建一个引用新数据库的数据库对象，名为`business`，如下所示:

```
db = client.business 
```

一旦我们创建了这个对象，我们就可以执行 CRUD 操作了。因为我们想要查询一些有用的东西，所以让我们从构建一个样本数据生成器应用程序开始。

## 生成示例数据代码示例

使用您的开发工具或命令行文本编辑器创建一个名为`createsamples.py`的新文件，并复制以下代码:

```
from pymongo import MongoClient
from random import randint
#Step 1: Connect to MongoDB - Note: Change connection string as needed
client = MongoClient(port=27017)
db=client.business
#Step 2: Create sample data
names = ['Kitchen','Animal','State', 'Tastey', 'Big','City','Fish', 'Pizza','Goat', 'Salty','Sandwich','Lazy', 'Fun']
company_type = ['LLC','Inc','Company','Corporation']
company_cuisine = ['Pizza', 'Bar Food', 'Fast Food', 'Italian', 'Mexican', 'American', 'Sushi Bar', 'Vegetarian']
for x in xrange(1, 501):
    business = {
        'name' : names[randint(0, (len(names)-1))] + ' ' + names[randint(0, (len(names)-1))]  + ' ' + company_type[randint(0, (len(company_type)-1))],
        'rating' : randint(1, 5),
        'cuisine' : company_cuisine[randint(0, (len(company_cuisine)-1))] 
    }
    #Step 3: Insert business object directly into MongoDB via isnert_one
    result=db.reviews.insert_one(business)
    #Step 4: Print to the console the ObjectID of the new document
    print('Created {0} of 100 as {1}'.format(x,result.inserted_id))
#Step 5: Tell us that you are done
print('finished creating 100 business reviews') 
```

确保将 MongoDB 客户机连接 URL 更改为指向您的 MongoDB 数据库实例的 URL。一旦您运行这个应用程序，500 个随机命名的企业及其相应的评级将在 MongoDB 数据库`business`中创建。所有这些业务都创建在一个名为`reviews`的集合中。请注意，我们不必为了使用数据库而事先显式地创建它。这与其他数据库不同，其他数据库要求首先执行像`CREATE DATABASE`这样的语句。

在这个例子中，将数据插入 MongoDB 的命令是`insert_one()`函数。有点不言自明，`insert_one`将一个文档插入 MongoDB。结果集将返回创建的单个 ObjectID。这是插入数据的几种方法之一。如果你想在一次调用中插入多个文件，你可以使用`insert_many`功能。

除了确认插入之外，`insert_many`的结果集还将包含一个已创建的 ObjectIDs 列表。有关`insert_many`的更多信息，请参见此处的[文档](http://api.mongodb.com/python/current/api/pymongo/collection.html#pymongo.collection.Collection.insert_many)。

有关`insert_many`结果集的详细信息，请查看文档的这一部分[以及](http://api.mongodb.com/python/current/api/pymongo/results.html#pymongo.results.InsertManyResult)。

我们现在准备探索使用 Python 在 MongoDB 中查询和管理数据。为了指导这一探索，我们将创建另一个应用程序来管理我们的业务评论。

## 探索商业评论数据

现在我们的数据库中有了一组好的数据，让我们使用 PyMongo 查询一些结果。

在 MongoDB 中,`find_one`命令用于查询单个文档，很像关系数据库中使用的 select 语句。为了在 PyMongo 中使用`find_one`命令，我们传递一个指定搜索标准的 Python 字典。比如让我们通过字典，`{ ‘rating’ : 5 }`找到一单点评分数为 5 的商家。

```
fivestar = db.reviews.find_one({'rating': 5})
print(fivestar) 
```

结果将包含类似如下的数据:

```
{u'rating': 5,
 u'_id': ObjectId('58e65383ea0b650c867ef195'),
 u'name': u'Fish Salty Corporation', 
u'cuisine': u'Sushi Bar'} 
```

假设我们创建了 500 个样本数据，那么肯定有不止一个评级为 5 的企业。`find_one`方法只是一系列支持查询 MongoDB 数据的 find 语句中的一个。另一个名为`find`的语句将返回所有符合搜索条件的文档。这些游标还支持像`count()`这样的方法，它在查询中返回结果的数量。要找到评分为 5 的企业总数，我们可以使用如下的`count()`方法:

```
fivestarcount = db.reviews.find({'rating': 5}).count()
print(fivestarcount) 
```

您的结果可能会有所不同，因为数据是随机生成的，但在测试运行中，返回的值为 103。

MongoDB 可以轻松地执行这些简单的查询。但是，考虑这样一种情况，您希望对整个数据集中每个评等的出现次数进行求和。在 MongoDB 中，您可以创建 5 个单独的 find 查询，执行它们并显示结果，或者您可以使用如下的 [MongoDB 聚合管道发出一个查询:](https://docs.mongodb.com/manual/aggregation/#aggregation-pipeline)

```
from pymongo import MongoClient
# Connect to the MongoDB, change the connection string per your MongoDB environment
client = MongoClient(port=27017)
# Set the db object to point to the business database
db=client.business
# Showcasing the count() method of find, count the total number of 5 ratings 
print('The number of 5 star reviews:')
fivestarcount = db.reviews.find({'rating': 5}).count()
print(fivestarcount)
# Not let's use the aggregation framework to sum the occurrence of each rating across the entire data set
print('\nThe sum of each rating occurance across all data grouped by rating ')
stargroup=db.reviews.aggregate(
# The Aggregation Pipeline is defined as an array of different operations
[
# The first stage in this pipe is to group data
{ '$group':
    { '_id': "$rating",
     "count" : 
                 { '$sum' :1 }
    }
},
# The second stage in this pipe is to sort the data
{"$sort":  { "_id":1}
}
# Close the array with the ] tag 
] )
# Print the result
for group in stargroup:
    print(group) 
```

深入研究聚合框架超出了本文的范围，但是，如果您有兴趣了解更多关于它的内容[，请查看本文](https://docs.mongodb.com/manual/aggregation/)。

## 用 PyMongo 更新数据

类似于`insert_one`和`insert_many`，有一些功能可以帮助你更新你的 MongoDB 数据，包括`update_one`、`update_many`和`replace_one`。`update_one`方法将基于匹配文档的查询更新单个文档。例如，让我们假设我们的商业评论应用程序现在有能力让用户“喜欢”一个企业。为了说明如何用这个新的“likes”字段更新文档，让我们先来看看从我们之前的应用程序到 MongoDB 的插入中现有文档的样子。接下来，让我们更新文档，重新查询文档并查看更改。

```
from pymongo import MongoClient
#include pprint for readabillity of the 
from pprint import pprint

#change the MongoClient connection string to your MongoDB database instance
client = MongoClient(port=27020)
db=client.business

ASingleReview = db.reviews.find_one({})
print('A sample document:')
pprint(ASingleReview)

result = db.reviews.update_one({'_id' : ASingleReview.get('_id') }, {'$inc': {'likes': 1}})
print('Number of documents modified : ' + str(result.modified_count))

UpdatedDocument = db.reviews.find_one({'_id':ASingleReview.get('_id')})
print('The updated document:')
pprint(UpdatedDocument) 
```

运行上面的示例代码时，您可能会看到类似如下的结果:

```
A sample document:
{'_id': ObjectId('58eba417ea0b6523b0fded4f'),
 'cuisine': 'Pizza',
 'name': 'Kitchen Goat Corporation',
 'rating': 1}

Number of documents modified : 1

The updated document:
{'_id': ObjectId('58eba417ea0b6523b0fded4f'),
 'cuisine': 'Pizza',
 'likes': 1,
 'name': 'Kitchen Goat Corporation',
 'rating': 1} 
```

请注意，原始文档没有“likes”字段，更新后我们可以轻松地将该字段添加到文档中。这种无需昂贵的`Alter_Table`语句就能动态添加键的能力是 MongoDB 灵活数据模型的强大之处。它使快速应用程序开发成为现实。

如果您想要更新文档的所有字段并保持相同的 ObjectID，您将需要使用`replace_one`函数。关于`replace_one`的更多细节，请点击查看[py mongo 文档。](http://api.mongodb.com/python/current/api/pymongo/collection.html#pymongo.collection.Collection.replace_one)

更新功能还支持一个名为“upsert”的选项。使用 upsert，如果您试图更新的文档不存在，您可以告诉 MongoDB 创建一个新文档。

## 删除文档

与到目前为止讨论的其他命令非常相似，`delete_one`和`delete_many`命令将匹配要删除的文档的查询作为第一个参数。例如，如果您想要删除 reviews 集合中类别为“Bar Food”的所有文档，您应该发出以下命令:

```
result = db.restaurants.delete_many({“category”: “Bar Food“}) 
```

如果要删除大量文档，删除集合可能比删除所有文档更有效。

## 接下来去哪里

学习 MongoDB 和 Python 有很多选择。MongoDB 大学是一个开始学习管理、开发和其他主题(如 MongoDB 分析)的好地方。一门特别的课程是 MongoDB for Developers (Python)。本课程更深入地涵盖了本文的主题，包括对 MongoDB 聚合框架的讨论。[点击这里查看](https://university.mongodb.com/courses/M101P/about)。

## 附录:创建免费层 MongoDB Atlas 数据库

MongoDB Atlas 是一个托管数据库服务，允许您选择您的数据库大小并获得一个连接字符串！按照以下步骤开始使用您的免费数据库。

### 免费构建您的集群

按照下面的步骤创建一个免费的 MongoDB 数据库:

1.  转到 [MongoDB 图集](https://www.mongodb.com/cloud/atlas)。
2.  点击“免费开始”按钮。
3.  填写表格创建一个帐户。您将使用这些信息登录并管理您的 MongoDB。

填写表格后，网站将创建您的帐户，并弹出“构建您的新集群”窗口，如下所示:

![Build your new cluster](img/68543f8ff93764482226883ace4812b2.png)

要使用免费层，向下滚动并选择“M0”。当您这样做时，区域面板将被禁用。自由层有一些限制，选择地区的能力就是其中之一。您的数据库大小将被限制为 512MB 的存储空间。考虑到这一点，当您准备将 MongoDB 用于不仅仅是一些简单的操作时，您可以通过从“实例大小”列表中选择一个大小来轻松地创建另一个实例。在您单击“确认和部署”之前，向下滚动页面并注意此处显示的附加选项:

![Additional options in Build New Cluster dialog](img/620ac2d3ae2b59f562a2b717c1fea7ce.png)

从“Build Your New Cluster”弹出菜单中，您可以看到还有其他选项可用，包括选择 3、5 或 7 个节点的副本集以及多达 12 个碎片的集群。请注意，自由层不允许您选择超过 3 节点群集的任何内容，但是如果您移动到其他大小，这些选项将变为可用。

此时，我们几乎准备好了——最后要解决的是管理员用户名和密码。您也可以选择通过单击“自动生成安全密码”按钮为您生成一个随机密码。最后，单击“Confirm & Deploy”按钮创建您的 Atlas 集群。

### 设置您的 IP 白名单

当 Atlas 正在创建您的数据库时，您将需要定义允许哪些 IP 访问您的新数据库，因为 MongoDB Atlas 默认情况下不允许从互联网访问。这个被授予的 IP 地址列表被称为“IP 白名单”。要将您机器的 IP 地址添加到此列表，请单击“安全”选项卡，然后单击“IP 白名单”，然后单击“+添加 IP 地址”按钮。这将弹出如下所示的另一个对话框。

![Add whitelist entry](img/8226e7ce897bcf11c1843b8ab9c5e155.png)

您可以点击“添加当前 IP 地址”按钮来添加您的 IP 或提供一个特定的 IP 地址，或者通过完全不限制 IP 来启用对世界的访问(这不是一个奇妙的想法，但万一您没有其他选择，需要允许来自任何 IP 的验证)。

填写完该对话框后，单击“确认”,这将更新 MongoDB Atlas 集群上的防火墙设置。接下来，单击“Clusters”选项卡，您应该会看到新的 MongoDB 数据库已经准备就绪！

![Cluster0 ready for action](img/be9143a2574d166700e8aaf0e8fed565.png)

### 测试您的连接

在开始输入代码之前，我们希望确保 MongoDB 数据库可以从我们的开发平台上访问。一种快速的测试方法是使用 Mongo Shell 命令行工具建立连接。确保您的 MongoDB 连接信息可用。如果您正在使用 MongoDB Atlas，您可以通过单击 Clusters 选项卡上的“Connect”按钮来获取连接信息，如下所示。

![Connect button of the MongoDB Atlas cluster](img/02985605ce90f9e922ac531d34465c4d.png)

“连接”按钮将启动一个提供连接信息的对话框。在该对话框的底部，您将看到一个准备好的命令行，您只需将其复制并粘贴到命令提示符中。

![Connect with Mongo Shell section of the Connect dialog](img/24fa0677b8b85c5f73ebec62d7476675.png)

请注意，如果您照原样复制连接文本，您将不得不将 **<password></password>**替换为**管理员**用户的密码，并将 **<database></database>**替换为您希望连接的数据库的名称。

来自该对话框的命令文本很长。为了清楚起见，让我们单独看一下每个参数。

```
mongo
"mongodb://cluster0-shard-00-00-2ldwo.mongodb.net:27017,cluster0-shard-00-01-2ldwo.mongodb.net:27017,cluster0-shard-00-02-2ldwo.mongodb.net:27017/test?replicaSet=Cluster0-shard-0"
 --authenticationDatabase admin 
--ssl
--username myadmin 
--password S$meComPLeX1! 
```

第一个参数是一个字符串，包含集群中所有节点的列表，包括名为`Cluster0-shard-0`的副本集的定义。下一个参数`--authenticationDatabase`告诉我们哪个数据库包含我们想要认证的用户。

`--ssl`通过 SSL/TLS 协议强制加密连接。最后，我们提供用户名和密码，我们就连接上了！注意，如果您没有使用 MongoDB Atlas，那么您的 MongoDB 部署可能没有启用安全性或者不需要 SSL。因此，连接到它就像在命令提示符下输入“mongo”一样简单。

您现在可以使用 MongoDB 了！

如果你有兴趣学习开始构建基于 MongoDB 的应用所需的一切，你可以注册我们的免费在线课程 [MongoDB 大学](https://university.mongodb.com)。

## 分享这篇文章