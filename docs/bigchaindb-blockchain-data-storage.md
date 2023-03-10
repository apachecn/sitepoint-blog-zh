# BigchainDB:区块链和数据存储

> 原文：<https://www.sitepoint.com/bigchaindb-blockchain-data-storage/>

**自从我写了[这篇文章](https://www.sitepoint.com/managing-data-storage-with-blockchain-and-bigchaindb/)以来， [BigchainDB](https://www.bigchaindb.com) 和[区块链](https://www.sitepoint.com/blog/)空间已经发生了很大的变化，所以现在似乎是时候重新审视和刷新区块链对传统计算空间的一个更基础的部分:数据存储的影响了。**

BigchainDB 最初是作为一种技术来取代 Ascribe 数字艺术品跟踪项目中的比特币区块链，后来扩展为废弃的 [IPDB](https://medium.com/ipdb-blog) 的一个组件，现在则作为 bold [海洋协议](https://oceanprotocol.com)的存储层。

这种使用上的变化导致了 BigchainDB 的基础和实现的变化，正如 [RethinkDB](https://www.rethinkdb.com/) 的关闭一样，迫使团队将存储引擎切换到健壮的 [MongoDB](http://mongodb.com) 。数据库之上的区块链层提供了[事务支持](https://en.wikipedia.org/wiki/Database_transaction)，有助于保证数据库发生变化，并增加额外的控制和安全性，但已经成熟，2018 年 BigchainDB 将达到 2.0。

所有这些变化现在意味着 BigchainDB 鼓励您使用[他们的公共网络](https://testnet.bigchaindb.com)而不是部署您的实例。这种方法在某种程度上与传统的分布式数据库实践相反，但更适合过去几年基于区块链的项目的发展，有助于 BigchainDB 将其平台货币化(通过 ICO 或 SaaS 模型)，这是一个有趣的变化。时间会证明客户是否愿意将数据存储在公共网络中，但是使用访问令牌来确保安全性和隐私性，这在概念上与使用云托管的数据库没有太大区别。

如果你愿意，你仍然可以[安装你自己的 BigchainDB 实例](https://docs.bigchaindb.com/projects/server/en/latest/quickstart.html)，但是我觉得公司会越来越鼓励你不要这样做。

无论你选择哪一个，你都可以使用官方的 [Python](https://github.com/bigchaindb/bigchaindb-driver) 、 [JavaScript](https://github.com/bigchaindb/js-bigchaindb-driver) 或者社区驱动。例如，使用 JavaScript，安装带有`npm install bigchaindb-driver`的包，创建连接，并使用适当的写入器和读取器的键向数据库写入和读取资产。

您可以在这里阅读驱动程序[的完整文档，在这里](https://docs.bigchaindb.com/projects/js-driver/en/latest/usage.html)阅读数据库[的完整文档，但是下面的示例为`author`创建一个文章资产，然后将其分配给`assignee`:](https://bigchaindb.readthedocs.io/en/latest/index.html)

```
const driver = require('bigchaindb-driver')
const author = new driver.Ed25519Keypair()
const assignee = new driver.Ed25519Keypair()

console.log('Author: ', author.publicKey)
console.log('Assignee: ', assignee.publicKey)

const assetdata = {
    'article': {
        'title': 'Blockchain DBs',
        'body': 'Article body',
    }
}

const txCreateAuthorSimple = driver.Transaction.makeCreateTransaction(
    assetdata,
    [driver.Transaction.makeOutput(
        driver.Transaction.makeEd25519Condition(author.publicKey))
    ],
    author.publicKey
)

const txCreateAuthorSimpleSigned = driver.Transaction.signTransaction(txCreateAuthorSimple, author.privateKey)

let conn = new driver.Connection('https://test.bigchaindb.com/api/v1/', {
    app_id: '<APP_ID>',
    app_key: '<APP_KEY>'
})

conn.postTransactionCommit(txCreateAuthorSimpleSigned)
    .then(retrievedTx => console.log('Transaction', retrievedTx.id, 'successfully posted.'))

    .then(() => {
        const txTransferAssignee = driver.Transaction.makeTransferTransaction(
            [{tx: txCreateAuthorSimpleSigned, output_index: 0}],
            [driver.Transaction.makeOutput(driver.Transaction.makeEd25519Condition(assignee.publicKey))],
            {price: '100 dollars'}
        )

        let txTransferAssigneeSigned = driver.Transaction.signTransaction(txTransferAssignee, author.privateKey)
        console.log('Posting signed transaction: ', txTransferAssigneeSigned)

        return conn.postTransactionCommit(txTransferAssigneeSigned)
    })
    .then(res => {
        console.log('Response from BDB server:', res)
        return res.id
    })
    .then(tx => {
        console.log('Is Assignee the owner?', tx['outputs'][0]['public_keys'][0] == assignee.publicKey)
        console.log('Was Author the previous owner?', tx['inputs'][0]['owners_before'][0] == author.publicKey)
    })
    // Search for asset based on the serial number of the bicycle
    .then(() => conn.searchAssets('Blockchain DBs'))
    .then(assets => console.log('Found assets with title:', assets)) 
```

## 其他选择

BigchainDB 不再是唯一基于区块链的数据库，这取决于您的定义和您想要实现的目标。

[FlureeDB](https://flur.ee/) 用区块链层包装了一个[图形风格的数据库](https://en.wikipedia.org/wiki/Graph_database)，当你考虑区块链的本质时，这有一定的意义。由于有了图形基础，它可以与 GraphQL 和 React 很好地集成。它仍在积极开发中，并遵循熟悉的数据库融资模式，有一个有限的社区版本，以及额外的容量，安全性和对高级用户的支持。FlureeDB 对区块链技术的参与似乎是在用代币代替金钱和某种形式的共识机制。这个项目不是开源的，所以很难说它到底是什么。

从内存来看， [OrbitDB](https://github.com/orbitdb/orbit-db) 的存在时间与 BigchainDB 差不多，但它是为更简单的应用程序需求而设计的。虽然它使用 [IPFS](https://ipfs.io/) 进行存储(有些人可能会称之为某种数据库)，但它并不自称是一个“区块链数据库”，而是一个分散应用程序的选择。

TiesDB 在它的网站上做了很多大胆的声明，但是关于它是如何实现这些声明的细节很少，而且由于[的知识库](https://github.com/TiesNetwork/ties.db)和关于如何运行数据库的文档也很少，很难确认它是否实现了。在[的知识库自述文件](https://github.com/TiesNetwork/ties.db/blob/dev/README.md)中有一些白皮书，你可以仔细阅读，但是它们仍然主要涉及理论而不是实践。有趣的是，它还允许你删除数据，这虽然是传统数据库的一个基本部分，但有点违背区块链的理想。这个决定没有对错；一些开发者不得不考虑妥协，将区块链技术推向主流。

Swarm 是一个以太坊组件，是分布式应用程序的默认存储机制( [Dapps](https://www.sitepoint.com/ipfs-swarm-decentralized-content-publication-storage/) )。它没有提供这样一个无缝的开始方式，但是如果你已经在研究以太坊的其他组件，那么[阅读文档了解更多细节](http://swarm-guide.readthedocs.io/en/latest/introduction.html)。

Filecoin 做了一些不同的事情。它提供了一种机制来跟踪数据中心和互联网周围的备用存储块之间的事务。它允许你使用传统存储，但通过一个区块链层，让用户竞标你提供的空间，并跟踪他们的使用情况。

这两种技术在本文的[中有更详细的描述。](https://www.sitepoint.com/ipfs-swarm-decentralized-content-publication-storage/)

## 去中心化未来的一部分

暂且忽略其区块链遗产，BigchainDB 提供了当前 NoSQL 和分布式数据库所缺少的功能。这一事实本身可能就是尝试它的理由，并可能提供有效的业务/用例。

对于你们当中的区块链爱好者来说，BigchainDB 和其他替代品也完成了一个完整的分散式应用程序堆栈的拼图——用[以太坊](https://www.ethereum.org/)作为应用程序， [IPFS](https://ipfs.io/) 作为文件系统，BigchainDB 作为数据存储。各部分已经就绪，可以以不同的方式开发、部署和维护应用程序，引领一个迷人的未来，我很想听听您的意见。

## 分享这篇文章