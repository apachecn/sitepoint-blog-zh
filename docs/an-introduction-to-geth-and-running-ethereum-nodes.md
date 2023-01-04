# Geth 和运行以太坊节点简介

> 原文：<https://www.sitepoint.com/an-introduction-to-geth-and-running-ethereum-nodes/>

在本文中，我们将看看以太坊节点是什么，并探索其中最流行的一种，称为 Geth。

为了与区块链通信，我们必须使用区块链**客户端**。客户端是能够与其他客户端建立 p2p 通信通道、签署和广播交易、挖掘、部署智能合约并与之交互等的软件。客户端通常被称为**节点**。

以太坊节点必须遵循的功能的正式定义在[以太坊黄皮书](https://ethereum.github.io/yellowpaper/paper.pdf)中定义。黄皮书定义了网络上节点所需的功能、挖掘算法、私钥/公钥 [ECDSA](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm) 参数。它定义了使节点完全兼容以太坊客户端的所有特性。

根据黄皮书，任何人都可以用他们认为合适的语言创建自己的以太坊节点实现。

点击可查看[客户的完整列表。](https://github.com/ethereum/wiki/wiki/Clients,-tools,-dapp-browsers,-wallets-and-other-projects#ethereum-clients)

到目前为止，最受欢迎的客户是 [Geth](https://github.com/ethereum/go-ethereum/wiki) 和[平价](https://wiki.parity.io/)。实现的最大区别在于选择的编程语言 Geth 使用 Golang，而 Parity 使用 Rust。

因为 Geth 是目前最流行的客户端实现，所以我们现在只关注它。

## 节点类型

当你加入以太坊网络时，你可以选择运行各种类型的节点。目前的选项有:

*   光节点
*   完整节点
*   存档节点

归档节点是完整节点的一个特例，因此我们不会详细讨论它。我发现的关于节点类型的最佳总结之一在[栈交换](https://ethereum.stackexchange.com/questions/5975/what-are-the-types-of-nodes-in-ethereum):

> 一般来说，我们可以把节点软件分为两种:全节点和轻(重)节点。完整节点验证广播到网络上的数据块。也就是说，它们确保块中包含的事务(以及块本身)遵循以太坊规范中定义的规则。它们维护网络的当前状态(根据以太坊规范定义)。
> 
> 不遵循规则的事务和块不用于确定以太坊网络的当前状态。例如，如果 A 试图向 B 发送 100 个以太网，但 A 有 0 个以太网，并且一个块包含此事务，则完整节点将意识到这不符合以太坊的规则，并拒绝该块，将其视为无效。特别地，智能合约的执行是交易的一个例子。每当在交易中使用智能合约时(例如，发送 ERC-20 令牌)，所有完整节点将必须运行所有指令，以确保它们到达正确的、商定的区块链的下一状态。
> 
> 有多种方法可以达到相同的状态。例如，如果 A 有 101 个乙醚，并在一次交易中以 1 乙醚的价格将其中的 100 个给了 B，最终结果将与 A 向 B 发送 100 个 1 乙醚的交易，每笔交易支付 0.01 乙醚相同(忽略谁收到了交易费)。要知道现在是否允许 B 发送 100 以太，知道 B 当前的余额是多少就足够了。保留整个事务历史的完整节点称为完整归档节点。这些必须存在于网络中，网络才能健康运行。
> 
> 节点也可以选择丢弃旧数据；如果 B 要给 C 发 100 乙醚，乙醚是怎么得来的不重要，只需要说明 B 的账户里有 100 乙醚。相比之下，轻节点不验证每个块或事务，并且可能没有当前区块链状态的副本。它们依赖完整的节点来提供缺失的细节(或者只是缺少特定的功能)。轻型节点的优势在于它们可以更快地启动和运行，可以在更多计算/内存受限的设备上运行，并且不会消耗几乎同样多的存储。不利的一面是，其他节点中存在信任因素(它因客户端而异，可以使用概率方法/试探法来降低风险)。一些完整客户端包括具有更快同步的功能(例如，奇偶校验的 warp sync)。

## 安装 Geth

Geth 在各种平台(Windows、macOS、Linux)上的安装说明可以在[这里](https://github.com/ethereum/go-ethereum/wiki/Building-Ethereum)找到。这个列表相当全面，并且是最新的，所以我不会在文章中重复它。

## 运行 Geth

为了启动一个 Geth 节点，您唯一需要做的事情就是进入您的终端窗口并运行`geth`。当您这样做时，您应该得到类似如下的输出:

```
➜  ~ geth
INFO [06-03|11:03:13] Maximum peer count                       ETH=25 LES=0 total=25
INFO [06-03|11:03:13] Starting peer-to-peer node               instance=Geth/v1.8.10-stable/darwin-amd64/go1.10.2
INFO [06-03|11:03:13] Allocated cache and file handles         database=/Users/mjvr/Library/Ethereum/geth/chaindata cache=768 handles=128
INFO [06-03|11:03:13] Writing default main-net genesis block
INFO [06-03|11:03:14] Persisted trie from memory database      nodes=12356 size=2.34mB time=48.31016ms gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [06-03|11:03:14] Initialised chain configuration          config="{ChainID: 1 Homestead: 1150000 DAO: 1920000 DAOSupport: true EIP150: 2463000 EIP155: 2675000 EIP158: 2675000 Byzantium: 4370000 Constantinople: <nil> Engine: ethash}"
INFO [06-03|11:03:14] Disk storage enabled for ethash caches   dir=/Users/mjvr/Library/Ethereum/geth/ethash count=3
INFO [06-03|11:03:14] Disk storage enabled for ethash DAGs     dir=/Users/mjvr/.ethash                      count=2
INFO [06-03|11:03:14] Initialising Ethereum protocol           versions="[63 62]" network=1
INFO [06-03|11:03:14] Loaded most recent local header          number=0 hash=d4e567…cb8fa3 td=17179869184
INFO [06-03|11:03:14] Loaded most recent local full block      number=0 hash=d4e567…cb8fa3 td=17179869184
INFO [06-03|11:03:14] Loaded most recent local fast block      number=0 hash=d4e567…cb8fa3 td=17179869184
INFO [06-03|11:03:14] Regenerated local transaction journal    transactions=0 accounts=0
INFO [06-03|11:03:14] Starting P2P networking
INFO [06-03|11:03:16] UDP listener up                          self=enode://a4cb08519bc2bceecb8ad421871c624d5212888653bbaee309fda960f3c87ca7aa9855ee14684d521836ae88ad1986b8ca944348e976760d2bd1247ed3ca7628@[::]:30303
INFO [06-03|11:03:16] RLPx listener up                         self=enode://a4cb08519bc2bceecb8ad421871c624d5212888653bbaee309fda960f3c87ca7aa9855ee14684d521836ae88ad1986b8ca944348e976760d2bd1247ed3ca7628@[::]:30303
INFO [06-03|11:03:16] IPC endpoint opened                      url=/Users/mjvr/Library/Ethereum/geth.ipc 
```

在此之后，您应该会看到新的行周期性地出现，其中 Geth 表示“导入新的州”或“导入新的块标题”或“导入新的收据”。状态、块头和事务是[以太坊树尝试](https://ethereum.stackexchange.com/questions/15142/how-many-tries-does-ethereum-have)的一部分:它们必须被下载，以使你的节点与以太坊区块链同步。

这是一个需要很长时间的过程，所以你可以选择运行一个像这样的光节点；

```
geth --light 
```

Geth 现在需要做的只是拉最新的块头，依靠其他全节点通过使用[merkle proof](https://www.quora.com/Cryptography-How-does-a-Merkle-proof-actually-work)来验证事务。

### 访问 Geth 控制台

现在您已经创建了一个节点，您可以通过在终端中打开一个新的选项卡并运行以下命令来访问它:

```
geth attach 
```

这将把一个 Geth 控制台——这是一个用于与区块链通信的 Javascript 环境——连接到您正在运行的节点。这可以在完整客户端模式和轻量级模式下完成。

打开控制台后，键入以下内容:

```
web3.eth.blockNumber 
```

您应该得到一个数字形式的输出(例如 5631487 ),它代表以太网的当前块号。

## 创建新帐户

为了使用区块链，您需要有一个帐户。使用 Geth，您可以通过在终端中运行以下命令来实现:

```
geth account new 
```

完成后，它会要求你输入密码，你需要密码来保护你的帐户。请确保使用安全的密码，并将其安全存储。

运行`geth account new`时，Geth 所做的是更新 Geth 数据目录(Geth 存储所有必要数据的目录，包括块和头)中的一个文件。这些位置是(每个平台):

*   苹果电脑:`~/Library/Ethereum`
*   Linux: `~/.ethereum`
*   视窗:`%APPDATA%\Ethereum`

## 从其他客户端访问 Geth

当您启动 Geth 时，客户端会自动在端口`8545`启动一个 RPC 服务器。你可以通过像`web3js`或`web3j`T8 或这样的库连接到`localhost:8545`，在这个端口上访问 RPC 服务器及其[方法](https://github.com/ethereum/wiki/wiki/JSON-RPC)，用`curl`或`wget`手动调用它。

要了解如何连接外部工具，比如那些正在运行的 Geth 实例(当启动你自己的区块链时可以是私有的，或者在上面的说明中是公共的),请看[这篇文章](https://bitfalls.com/2018/03/26/connecting-myetherwallet-mist-metamask-private-blockchain/)。

## 结论

在这个简短的介绍中，我们介绍了 Geth、以太坊节点的类型以及它们的用途。您现在可以运行自己的 Geth 节点，并使用第三方工具增强它。在未来的文章中，我们将涵盖运行专用网络(你自己的以太坊区块链与 Geth)和更多。

## 分享这篇文章