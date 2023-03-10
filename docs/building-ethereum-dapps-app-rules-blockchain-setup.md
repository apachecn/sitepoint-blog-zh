# 用以太坊构建 dapp:应用规则和区块链设置

> 原文：<https://www.sitepoint.com/building-ethereum-dapps-app-rules-blockchain-setup/>

这是使用[以太坊](https://www.sitepoint.com/ethereum-introduction)区块链构建分散式应用程序系列的第一部分。

我们要建造三样东西:

1.  自定义令牌
2.  用代币做票的刀
3.  用于与智能合同交互的原型 web 用户界面

按照本课程中的说明，您将能够构建自己的定制以太坊令牌，无论是否附带 DAO 和应用程序。

## 我们将会建造什么

我们正在建设的项目叫做*永无止境的故事* (TNS)。完整的项目可以在 storydao.bitfalls.com 的[找到。它的完整代码是 GitHub](http://storydao.bitfalls.com) 上的[。](https://github.com/bitfalls/storydao)

Neverending Story 是一个带有输入字段(文本和图像)的 web 应用程序，安装了[元掩码](https://bitfalls.com/2018/02/16/metamask-send-receive-ether/)的用户可以根据特定条件发送一段文本或一幅图像。(图片每 50 段才出现一次，而且只能是 URL。)

完整的故事呈现在输入字段的旁边。故事中存在的条目越多，提交条目的成本就越高。提交条目是每个付费、被列入白名单并至少拥有一个 TNS 令牌的人都可以进行的操作。

动作类型:

1.  **提交条目**:要求发送者至少有一个 TNS 令牌，并且在白名单中(见下文)。成本`0.0001 ether * number of entries in current chapter`(参见下面的“章节”部分)。这可以是图像每 50 个文本条目只有一次，只能是一个网址。否则就是文字。没有代码，只有纯文本。在任一情况下，文本字段大小限制为 256 个字符。
2.  **转移所有权【仅限所有者】**:可以将所有权转移到新地址。
3.  **降低费用【仅所有者】**:所有者可以降低提交参赛作品的费用(参见下面的“费用”部分)，而无需请求投票。
4.  **购买 TNS 代币**:向 DAO 发送以太，获得自动发回的一定比例的 TNS 代币。如果 DAO 中没有足够的 TNS 令牌，您必须从交换中获取它们。
5.  **检查令牌余额**:只读动作，检查 DAO 中有多少 TNS 令牌。
6.  **白名单**:当以太网被发送到 DAO 的地址时，这个函数被自动调用。它将发送者记录在一个白名单数组中，以供将来参考，这样获得大量 TNS 的人就不能只是将它们分发给不同的帐户。
7.  **结束章节**:触发分配乙醚红利和重置入门费的章节结束流程。
8.  **提取**:当利益相关者想要提取他们的红利时调用。
9.  **所有者提取【仅所有者】**:由所有者调用以提取费用收入。

利益相关方(TNS 令牌持有者)将能够对提案进行投票，任何获得“是”票多于“否”票的提案都将获得批准。票数并不重要；比例很重要。根据提案的类型，提案需要在给定的时间内开放投票。

提案类型:

1.  **删除条目**:投票确认后，目标条目被删除。投票时间: **48 小时**。
2.  **紧急移除入口【仅限所有者】**:只能由所有者触发。当通过投票确认时，目标条目被删除。投票时间: **12 小时**。
3.  **紧急删除图像【仅所有者】**:仅适用于图像条目。只能由所有者触发。当通过投票确认时，目标条目被删除。投票时间: **4 小时**。

稍后，我们可以使用相同的方法添加其他类型的提案。

当然，所有的**提议**都需要清楚地列在用户界面的某个地方，这样人们就知道需要他们的投票。一个选民只需要一个 TNS 代币就可以投票，但是他们拥有的代币越多，他们的选票就越有价值。

重要提示:投票期间，用户的令牌会被锁定。这防止了鲸鱼 TNS 持有者压倒每一个活跃的投票。选民们将不得不把他们的投票权分配到对他们来说重要的选票上。

### 章

在 1000 个条目或三周时间后，任何用户都可以调用一个章节的结尾。DAO 随后会将收集到的乙醚按比例分配给所有 TNS 持有者。持有人不需要被列入 DAO 的白名单就可以获得股息红利。他们只需要在分发时持有 TNS 令牌。

分发是一个拉机制:持有者需要调用 DAO 的**撤销**函数来获得他们的以太。它不会被自动调用。撤回窗口是 72 小时。50%无人认领的乙醚归主人所有，而 50%进入下一章的红利。

### 费用

提交的报名费的 1%归当前所有者。其余的放入一个资金池，在每章结束后，按比例分配给所有 TNS 持有者。

业主的费用进入一个单独的余额，他们可以定期随意提取。

要获得白名单，用户必须向 DAO 发送 0.01 ether。未来所有代币的购买价格都要低得多。0.01 英镑的入场费是为了防止茜玻病的发作。如果用户第一次发送超过 0.01，则 0.01 将进入白名单，而剩余部分将用于计算用户获得的 TNS 数量。所有令牌将一次性返还给用户。

简而言之，把这个项目想象成一个众包的选择你自己的冒险故事，有人群监管和社区审查。一个故事——道。

## 自举:PoA 私有区块链

我们将做两个引导程序:这一个将集中在本地运行我们自己的真实以太坊区块链。它将使用[权威证明](https://www.sitepoint.com/proof-of-stake-vs-proof-of-work)作为共识机制，并像任何真正的以太坊[测试网](https://bitfalls.com/2018/02/08/what-is-an-ethereum-testnet-and-how-is-it-used/)一样运作。

*   下载并安装 [Virtualbox](https://www.virtualbox.org/) 。
*   下载安装[流浪汉](https://www.vagrantup.com/)。如果你不确定什么是虚拟机，请看[这篇文章](https://www.sitepoint.com/re-introducing-vagrant-right-way-start-php/)。
*   从[这里](https://ethereum.github.io/go-ethereum/downloads/)下载 Geth。一定要向下滚动下载“Geth & Tools”，而不仅仅是“Geth”。如果你在 macOS 上，也可以用家酿，用`brew install ethereum`安装。
*   Mist 可以在这里下载[。一定要下载“迷雾”而不是“以太坊钱包”。Mist 是以太坊“浏览器”，可以打开各种 DApps。以太坊钱包也是一样的东西，但是被锁定为钱包模式，所以你不能用它打开其他应用。这是为了防止非技术用户访问恶意的 DApps。](https://github.com/ethereum/mist/releases)

如果你很好奇，可以了解更多关于薄雾的知识，然后点击这里。

我们需要 VirtualBox 和 vagger，因为我们将使用[这种方法](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)来启动两个虚拟机，我们的节点将在这两个虚拟机上运行，模拟两台运行以太坊节点的计算机。

接下来，按照[本帖](https://www.sitepoint.com/puppeth-introduction)中的说明，创建一个 PoA 私人区块链。

您将得到两个运行的节点。每个都将挖掘到自己的地址。记下那些地址。我的是:

*   节点 1: `0x4b61dc81fe382068e459444e8beed1aab9940e3b`
*   节点 2: `0x97e01610f1c4f4367c326ed1e9c41896b4378458`

## 引导:Ganache CLI

我们可以采用的第二种引导类型是 Ganache CLI，以前称为 Testrpc，这对于测试我们的契约特别有用。

Ganache 是一个基于 JavaScript 的本地运行的区块链模拟。每次我们运行测试或重新启动应用程序时，它都会从头开始重新运行区块链。这有助于我们快速测试边缘案例，但不适用于长期合同的长期测试。

首先用`npm install -g ganache-cli`安装 Ganache。

然后，只需运行`ganache-cli`就可以了。您应该会看到类似如下的输出:

```
$ ganache-cli
Ganache CLI v6.1.0 (ganache-core: 2.1.0)
(node:40584) ExperimentalWarning: The fs.promises API is experimental

Available Accounts
==================
(0) 0xa0b7139a36ecda5ffda66b9cf97cb9de36e63f2f
(1) 0x1f5546797a0ff7efe42ecafaeebd5c932f1a0143
(2) 0x0eacbad38a642db2204574ad01b2b51c82ff7080
(3) 0x77f40a8add69b0e0806c0c506208e5783b89076d
(4) 0x1ea41547984ecb949c2b2df311bffe0fdeae4632
(5) 0xa1ad154fd5fd11ebe5410c992e5e97b461c516a2
(6) 0x34da52fd90c015a41bcc383ba3d804f7cebbc84e
(7) 0xddd5232788c1f1192d6ac5e82e74ca80945e119e
(8) 0x7ebc838124a676eac57f9b6275cd29b1a1c63d4d
(9) 0x6feed7913319ffb1b01f767960dd843ea7f96181

Private Keys
==================
(0) 62727ad35a288eb34f268cffb1ce620ef3ee80910138aed0e81f24d912fd8a49
(1) a6c76b382c655dcc66dd92428e3e0a0f14b7458162ad8e5cbbbcc64d3362d28c
(2) eef05f81fd995329c80d8875d5cb62b81f8f28c39951665b4b15688dc48b4c47
(3) 5ae06fc34da5d47a64a814ee088f7c6f0d1cae3c63d7ad2d6b71b8128bce1764
(4) 8cc43f28054f90243dea496263bd9a45f33db44ea3956ab8d0e8704e15d0787e
(5) dcf37436237105ea2f5b1be608b6aa1fe6fb7ca80b8b23ce01ff96930a2a3045
(6) f896b6f0ee11ea272c1567ec1950f7ff610df79193cbb7b668ae0ea11f6ec825
(7) 877b5868dca9a2f5c7d9546647171c9825f1b02922442f18dd4e90d108b9e54d
(8) 7f1f3515d71d348a78ae85a755e02df49be4e0b374447b822abe5a6481fe0c58
(9) 20d50b28c8b051406edc6aa61becc3443e430d7d68925a108958f8abecd55ed3

HD Wallet
==================
Mnemonic:      soda tower talk dynamic swim tattoo edit cook pair bid glance beauty
Base HD Path:  m/44'/60'/0'/0/{account_index}

Listening on localhost:8545 
```

Ganache 在这里所做的是启动一个几乎没有挖掘时间和节点的私有区块链。交易一进来，它就执行。它还生成 10 个预装了大量虚拟以太网的地址，并吐出它们的私钥，这样你就可以将它们导入各种工具，如 [MetaMask](https://bitfalls.com/2018/02/16/metamask-send-receive-ether/) 、 [MyEtherWallet](https://www.sitepoint.com/myetherwallet-introduction) 或之前下载的 Mist。

根据我们的需要，我们将混合使用这种方法和上面的方法，所以请确保您设置了这两种方法。

*注意:最好或者替代地，使用 [Ganache UI 工具](http://truffleframework.com/ganache/)拥有一个可视化界面来管理您的 Ganache 区块链。*

## 结论

使用[本指南](https://bitfalls.com/2018/03/26/connecting-myetherwallet-mist-metamask-private-blockchain/)通过[元掩码](https://bitfalls.com/2018/02/16/metamask-send-receive-ether/)、 [MyEtherWallet](https://www.sitepoint.com/myetherwallet-introduction) 或之前下载的 Mist 等工具连接到私有区块链(任一版本)。默认情况下，两个引导选项在运行时都在`localhost:8545`上运行，因此连接到它们的过程是相同的。

现在，我们的应用程序的规则和功能已经布置好，我们的私有区块链已经设置好，我们可以连接到它，接下来让我们关注工具、包和依赖项。

## 分享这篇文章