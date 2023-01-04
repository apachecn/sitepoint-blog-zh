# 使用区块链和 BigchainDB 管理数据存储

> 原文：<https://www.sitepoint.com/managing-data-storage-with-blockchain-and-bigchaindb/>

不可否认的是，虽然比特币的未来目前可能尚不明朗，但它所依赖的底层技术——区块链——已经彻底改变了许多行业和项目，未来还会有更多。

[Ascribe](https://www.ascribe.io/) 是一家令人着迷的初创公司，它使用比特币的区块链来记录有限数量的数字艺术品的独特引用。因此，由于有限数量的“副本”,它们变得可追踪、可问责并且(希望)更有价值。

将这种方法归咎于技术问题，而这些问题主要是由于比特币的区块链本身。向其中写入所有内容既缓慢又昂贵(目前每次 80c ),并且具有最大的每日条目数和总写入容量。这也违背了典型的可伸缩数据库技术，添加节点不会提高性能，也没有真正的查询语言。这使得扩展依赖比特币区块链的业务成为一项挑战。

但区块链概念是一个强大的概念，过去几年中，其使用和合法性不断增加，甚至主要银行都宣布开发受该概念启发的技术。

Ascribe 决定结合两个世界的优势，采用一个经过验证的 NoSQL 数据库( [RethinkDB](https://www.rethinkdb.com/) )，并在其上添加一个区块链层，以增加控制、资产跟踪和额外的安全级别。

NoSQL 数据库用户对这种技术组合特别感兴趣，因为传统上，很少有人支持有助于保证数据库发生变化的'[事务](https://en.wikipedia.org/wiki/Database_transaction)。通过区块链层写入底层 NoSQL 数据库，BigchainDB 增加了事务支持。

得益于区块链层，BigChainDB 还号称完全去中心化。虽然许多分布式 NoSQL 数据库都这样声称，但通常存在伪主/从设置。

## 正在安装 BigChainDB 和依赖项

有几种方法可以安装 BigChainDB。首先，我尝试了 Docker 镜像，但是遇到了一些连接问题，发现 Python 包最可靠。

1.  [安装 RethinkDB](http://rethinkdb.com/docs/install/) ，对于其他 Mac 用户，也有自制包可用。
2.  [安装 Python 3.4+](https://www.python.org/downloads/) 。
3.  用 Pip 安装 BigChainDB-`sudo pip install bigchaindb`
4.  用`rethinkdb`启动重新思考数据库
5.  用`bigchaindb start`启动 BigChainDB，它也将为您进行配置。
6.  在`http://SERVER_IP:58080/`打开 BigChainDB(实际上是 RethinkDB UI)管理 UI

## 简单示例–消息分配和跟踪

BigchainDB 的一个主要用例(也是 Ascribe 创建它的原因)是跟踪资产，所以让我们用 Python 做一个简单的例子。首先在您的终端中运行以下命令。

```
pip install bigchaindb
bigchaindb configure
bigchaindb show-config
```

创建一个新文件 *app.py* ，并添加以下内容:

```
from bigchaindb import Bigchain
b = Bigchain()
print(b)
```

这将导入 bigchaindb 库，创建一个新对象，并使用刚刚创建的设置文件连接到该对象。

然后运行 Python 应用程序:

```
python app.py
```

您应该会看到类似`<bigchaindb.core.Bigchain object at 0x1085b0dd8>`的内容，这告诉我们一切正常。

添加以下内容:

```
from bigchaindb import Bigchain
import time

b = Bigchain()

spuser_priv, spuser_pub = b.generate_keys()
print("User Created")

digital_asset_payload = {'msg': 'This is my special message just for you'}

tx = b.create_transaction(b.me, spuser_pub, None, 'CREATE', payload=digital_asset_payload)
print("Transaction Written")

tx_signed = b.sign_transaction(tx, b.me_private)
b.write_transaction(tx_signed)
print ("Transaction Written to BC, now waiting")

time.sleep(10)

tx_retrieved = b.get_transaction(tx_signed['id'])
print(tx_retrieved)
```

这将创建一个用户和相关的密钥来访问数据库——记住这一额外的安全级别。然后，创建用于写入数据库的有效负载，分配所需的键，并写入。

新交易从区块链层传递到数据库需要几秒钟。代码等待十秒钟，然后检索并打印记录。您应该会看到类似这样的内容:

```
{
  "signature": '304502205",
  "id": "0f442bcf4a42",
  "transaction": {
      "timestamp": "1457104938.430521",
      "data": {
        "hash": "b32779e57",
        "payload": {
          "msg": "This is my special message just for you"
        }
      },
      "operation": "CREATE",
      "current_owner": "hFJKYk2",
      "new_owner": "26pdiQTTx", 
      "input": None
    }
  }
}
```

您现在有一条特殊的消息，您希望有一个人可以访问:

```
...
print("Now to transfer")

spuser2_priv, spuser2_pub = b.generate_keys()
print("Second User Created")

tx_transfer = b.create_transaction(spuser_pub, spuser2_pub, tx_retrieved['id'], 'TRANSFER')
print("Transfer Created")

tx_transfer_signed = b.sign_transaction(tx_transfer, spuser_priv)
b.write_transaction(tx_transfer_signed)
print ("Transaction Written to BC, now waiting")

time.sleep(15)

tx_transfer_retrieved = b.get_transaction(tx_transfer_signed['id'])
print("Transferred")
print(tx_transfer_retrieved)
```

这将创建第二个用户，然后获取特殊消息的事务 ID，并将其传送给第二个用户。BigChainDB 的区块链层会防止用户和你的代码两次执行同一个动作。如果您尝试再次运行上面的代码，将会抛出一个`double spend exception`。

这个例子展示了 BigChainDB 添加到 RethinkDB 的一小组方法，[在这里找到完整的列表](https://bigchaindb.readthedocs.org/en/develop/developer-interface.html)。

## HTTP 端点

目前，BigChainDB 唯一可用的客户端库是 Python，以后可能会有更多，但与此同时，有限的 HTTP 端点可用于查询现有事务:

*http://localhost:5000/API/v1/transactions/tx _ id*

或者用以下内容编写一个新事务:

*http://本地主机:5000/api/v1/transactions*

添加以下有效负载，其中`operation`可以更改，以适应可以编写的不同类型的事务:

```
{
  "id": , ""
  "signature": "",
  "transaction": {
    "current_owner": "",
    "data": {
      "hash": "",
      "payload": null
    },
  "input": null,
  "new_owner": "",
  "operation": "",
    "timestamp": ""
  }
}
```

## 去中心化未来的一部分

暂且忽略其区块链遗产，BigChainDB 提供了当前 NoSQL 和分布式数据库所缺少的许多功能。这一事实本身可能就是尝试它的理由，并可能提供有效的业务/用例。

对于你们当中的区块链爱好者来说，它也完成了一个完整的去中心化应用程序堆栈的难题。理论上，现在有了应用程序的以太坊、文件系统的 T2 和数据存储的 BigChainDB。这是一种非常不同的开发、部署和维护应用程序的方式，它将引领一个迷人的未来，我希望在下面的评论中听到你的意见。

## 分享这篇文章