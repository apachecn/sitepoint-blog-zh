# Remix:为以太坊区块链开发智能合约

> 原文：<https://www.sitepoint.com/remix-smart-contracts-ethereum-blockchain/>

Remix 是一个用来编写、编译和调试 Solidity 代码的 Solidity IDE。T2 是一种高级的、面向合同的编程语言，用于编写智能合同。它受到了 C++、Python 和 JavaScript 等流行语言的影响。

IDE 代表[集成开发环境](https://www.sitepoint.com/what-is-an-ide/)，是一个带有一组工具的应用程序，旨在帮助程序员执行与软件开发相关的不同任务，如编写、编译、执行和调试代码。

在开始使用 Remix 开发智能合约之前，请确保您熟悉一些基本概念。特别是，给这些关于[区块链](https://www.sitepoint.com/blockchain-what-it-is-how-it-works-why-its-so-popular)和[以太坊](https://bitfalls.com/2017/09/19/what-ethereum-compare-to-bitcoin/)的文章读一读。

### 什么是智能合约/Dapp？

智能合同是双方之间的一种无信任协议，它利用区块链技术来强制双方遵守条款，而不是依赖传统方式，如信任中间人或使用法律来处理纠纷。

使用以太坊区块链，你可以用 Solidity 语言创建智能合同(以及其他语言)。以太坊不是唯一可以用来创建智能联系人的平台，但它是最受欢迎的选择，因为它从一开始就被设计为支持构建智能联系人。

[Dapp](http://ethdocs.org/en/latest/contracts-and-transactions/developer-tools.html#dapps) 代表 ***de* 集中式 *app* lication** 是一个 web3 应用，可以有一个用 JavaScript、HTML、CSS 等传统语言编写的前端和一个运行在区块链上的智能合约(作为后端代码)。因此，您可以简单地将 Dapp 视为前端以及相关的区块链智能合同。

与部署在区块链本身的智能合约不同，Dapp 的前端可以托管在 CDN 等集中式服务器上，也可以托管在 [Swarm](http://ethdocs.org/en/latest/contracts-and-transactions/developer-tools.html#swarm) 等分散式存储上。

## 访问 Remix IDE

你可以通过不同的方式访问 Remix IDE:在线，通过 Chrome 这样的网络浏览器，从本地安装的副本，或者从 Mist(以太坊 Dapp 浏览器)。

### 使用浏览器内混音 IDE

您可以从 web 浏览器访问 Remix IDE，无需任何特殊安装。访问[https://remix.ethereum.org/](https://remix.ethereum.org/)，你会看到一个完整的集成开发环境，有一个代码编辑器和各种面板，用于编译、运行和调试你的智能合约。你会有一个默认的例子*选票*合同，你可以玩。

![Remix IDE](img/a55c3ae540f63add5089f11a8f5cec2c.png)

### 从薄雾开始重新混合 IDE

你可以通过点击*开发*，然后*打开 Remix IDE* ，从 Mist 启动 Remix IDE。混音将在新窗口中打开。如果这是你第一次运行 IDE，你会看到一个简单的例子*选票*合同。

要熟悉 Mist，请看[这篇文章](https://bitfalls.com/2018/02/12/explaining-ethereum-tools-geth-mist/)。

### 运行你自己的 Remix IDE

您也可以通过执行以下命令来运行自己的 Remix IDE 副本:

```
npm install remix-ide -g
remix-ide 
```

您需要安装 Node.js 和 npm。查看这个 [GitHub 库](https://github.com/ethereum/remix-ide)了解更多信息。

## 重新混合面板

在了解了如何打开 Remix IDE 之后，现在让我们来看看组成 IDE 的各种面板。

### 文件浏览器

文件资源管理器提供了一个视图，其中显示了存储在浏览器存储器中的已创建文件。右键单击文件，然后从上下文菜单中选择正确的操作，可以重命名或删除任何文件。

![context menu options](img/88c027b71231dea944115cfe711e0fb3.png)

请注意，默认情况下，文件浏览器使用浏览器的本地存储，这意味着如果您清除或操作系统自动清除存储，您可能会丢失所有文件。对于高级工作，建议使用[Remix d](https://github.com/ethereum/remixd)——一个 Node.js 工具(可从 npm `npm install -g remixd`获得)，它允许 Remix IDE 访问您计算机的文件系统。

现在让我们来看看使用浏览器顶部的按钮可以执行的不同操作。

![Remix buttons](img/f7217c077bbca7647f2d1a57459dadca.png)

#### 在 Remix 中创建/打开文件

您可以使用左上角带有`+`图标的第一个按钮在浏览器本地存储器中创建一个新文件。然后你可以在打开的对话框中提供一个名字，并按下 *OK* 。

使用左上第二个按钮，你可以从你的计算机文件系统中打开一个现存的 Solidity 文件到 Remix IDE 中。该文件也将存储在浏览器的本地存储中。

#### 将资源管理器文件发布为 GitHub Gists

使用左上第三个和第四个按钮，您可以将 IDE 中的文件发布为公共 GitHub gist。

#### 将文件复制到 Remix IDE 的另一个实例

使用左上第五个按钮，您可以通过提供实例的 URL 将文件从本地存储复制到 Remix 的另一个实例。

#### 连接到本地文件系统

如果你正在运行 Remixd 工具，最后一个按钮可以用来连接 Remix IDE 到你的本地文件系统。

### 坚固性代码编辑器

Solidity 代码编辑器提供了一个界面，您可以在其中编写具有许多功能的代码，如语法高亮显示、自动重新编译、自动保存等。您可以打开多个标签页，也可以使用左上角的 *+/-* 按钮增加/减小字体大小。

![Solidity Code Editor](img/2c464dc09936b65af111fcbdca3b595e.png)

### 末端的

编辑器下面的终端窗口集成了一个 JavaScript 解释器和`web3`对象。您可以在当前上下文中执行 JavaScript 代码，可视化从 IDE 执行的操作，可视化所有网络事务或从 Remix IDE 创建的事务等。您还可以在终端中搜索数据并清除日志。

![Remix terminal](img/384e52bdf5ded78cadb5a316d18ae23d.png)

### 标签面板

*选项卡*面板提供了许多用于使用 IDE 的选项卡:

*   *编译*页签:用于编译智能合同并在 Swarm 上发布

*   *运行*页签:用于向配置好的环境发送事务

*   *设置*选项卡:用于更新设置，如编译器版本和编辑器的许多常规设置

*   *调试器*页签:用于调试事务

*   *分析*页签:用于获取最新编译的信息

*   *支持*标签:用于连接混音社区。

![Remix tabs](img/0c3a112e494a4f6296989d67c0fce27d.png)

## 混合执行环境

Remix IDE 为执行事务提供了许多环境:

*   JavaScript VM:在浏览器中用 JavaScript 实现的沙盒区块链，以模拟真实的区块链。

*   注入 Web3:注入 web3 如`Mist`和`Metamask`、[连接你到你私人区块链](https://bitfalls.com/2018/03/26/connecting-myetherwallet-mist-metamask-private-blockchain/)的提供者。

*   Web3 Provider:带有 geth、奇偶校验或任何以太坊客户端的远程节点。可用于连接到真实网络，或直接连接到您的私人区块链，中间没有元掩码。

![Remix Execution Environments](img/9e092880a0e7bbccff4e48e767494c5a.png)

## 使用 Remix IDE 编译和部署智能合约

为了演示我们可以使用 Remix IDE 实现什么，我们将使用一个示例契约，看看我们如何做到:

*   在 Remix IDE 中编译合同
*   当没有遵循最佳实践时，查看编译器发出的一些警告
*   在 JavaScript EVM(以太坊虚拟机)上部署合同
*   对已部署的合同进行交易
*   请参见终端 IDE 中的读写示例。

我们将使用本教程中的以下示例合同[，它实现了区块链抽奖:](https://bitfalls.com/2018/03/31/solidity-development-crash-course-building-blockchain-raffle/)

```
pragma solidity ^0.4.20;

contract Blocksplit {

    address[] public players;
    mapping (address => bool) public uniquePlayers;
    address[] public winners;

    address public charity = 0xc39eA9DB33F510407D2C77b06157c3Ae57247c2A;

    function() external payable {
        play(msg.sender);
    }

    function play(address _participant) payable public {
        require (msg.value >= 1000000000000000 && msg.value <= 100000000000000000);
        require (uniquePlayers[_participant] == false);

        players.push(_participant);
        uniquePlayers[_participant] = true;
    }

} 
```

契约声明了一些变量，例如:

*   `players`数组变量，保存抽彩参与者的地址。
*   `uniquePlayers`映射，用于保存独特的玩家，这样玩家就不会从同一个地址多次参与。(一个地址将被映射到一个布尔值，真或假，指示参与者是否已经参与。)
*   `winners`数组变量，它将保存获胜者的地址。
*   `charity`变量，保存一个慈善机构的硬编码地址，利润将流向该地址。

它还宣布:

*   标记为`payable`的回退功能，使智能合约能够接受付款。
*   一个`play()`功能，使参与者能够通过提供地址来参加抽奖。

你可以从这个[教程](https://bitfalls.com/2018/03/31/solidity-development-crash-course-building-blockchain-raffle/)中读到更多关于这个合同的细节，在这里所有的代码都有详细的解释。

现在，打开来自 remix.ethereum.org 的混音 IDE。

接下来，点击带有 *+* 图标的按钮，创建一个新文件。

![Creating a new file](img/dcd04853e8f48841b5dff3739f9e086d.png)

将弹出一个新对话框，输入文件名(`blocksplit.sol`)，然后按*确定*:

![Naming the file](img/8294045d3c68c968670b7b9b7534db61.png)

代码编辑器中会打开一个新的选项卡，您可以在其中开始编写合同。所以只需将之前的合同代码复制并粘贴到那里。

首先从编制合同开始。从*编译*选项卡点击*开始编译*按钮。

我们得到一个消息框，其中有两个由代码静态分析引发的警告。

![Two warnings](img/3f33eb0a78f040bb9c5b96f7e5859504.png)

如果您点击消息框，您将被带到*分析*选项卡，该选项卡提供关于警告的更多信息:

![warning info](img/2c51abb69d047a676f300893abe7849f.png)

如果功能的气体需求过高，则发出第一个警告，如果`require()`或`assert()`功能使用不当，则发出第二个警告。

您还可以使用*分析*选项卡中的复选框来确定您希望编译器何时发出警告。

![determine when you want the compiler to emit warnings](img/b99ab9368e3c185c180d066fa82a30da.png)

接下来，让我们用 JavaScript VM 部署契约。切换到*运行*选项卡，从下拉菜单中选择 *JavaScript VM* 。

![Selecting the JavaScript VM](img/87cfb5364d7255cd9cc943c5efa3c633.png)

接下来，单击合同名称下方的*部署*按钮。

![The Deploy button](img/8c5437e9cf95ed320c762a4f25477458.png)

一旦合同在 JavaScript VM 上成功部署，就会在 *Run* 选项卡的底部打开一个框。

![Run tab box](img/63686f46a0361074af1deb44581af85c.png)

在已部署契约的名称和地址下，我们有一些红色和蓝色的按钮。红色按钮指的是导致写入区块链(在我们的例子中是回退和播放功能)并需要一个事务的操作，蓝色按钮指的是从区块链读取( *charity，players，uniquePlayers 和 winners* 我们在契约代码中定义的公共变量)。

您还会在 IDE 终端中看到与下面的屏幕截图类似的消息。

![Terminal message](img/de9dae6999caf6cdc9b1c4f444250261.png)

目前，唯一保存值的变量是 *charity* 变量，因为地址是硬编码在代码中的，所以如果你点击相应的按钮，你将获得该地址的值。

![address value](img/24cd61398b0d1f25a2114c71dca27ec3.png)

该合同允许参与者通过向地址发送以太网(使用可支付的回调函数)或用参与者的地址调用`play()`函数来加入抽彩。

JavaScript VM 提供了五个假账户，每个账户有 100 个以太值，我们可以用它们来测试合同。您可以从*环境*下拉菜单下的*账户*下拉菜单中选择一个活期账户。

![Select a current account](img/8d2c27bb74fa02524f4c7be8cce8558b.png)

现在，为了向契约发送货币，我们首先从下拉菜单中设置*值*变量(在 0.001 和 0.1 之间)和单位(以太)。然后，我们通过简单地点击相应的红色按钮来调用回退功能。

![Calling the fallback function](img/57d8e03097525d18658d7d9a58798861.png)

就是这样。我们已经给合同寄了钱。所选账户的地址应添加到`players`数组中。要进行检查，只需点击蓝色的*玩家*按钮。

![Clicking on the blue Players button](img/2758008fa36533e1da02dae5a3dea999.png)

您可以通过选择一个新帐户来添加其他参与者，并重复之前的过程(要检查帐户是否被添加到阵列中，只需在*玩家*按钮旁边的文本框中输入该帐户的索引，从 0 到 4)。

如果您添加一个已经参与的帐户，您应该在终端中得到一个警告，并且交易将失败，并显示如下消息:

![Failure message](img/92b81366f464a7bd2fb8e60fb4df6e70.png)

## 混合替代品

智能合约的开发和部署有许多替代方法，例如:

*   Truffle :宣传为以太坊瑞士军刀，并声称是以太坊最受欢迎的开发框架，其使命是让您的生活变得更加轻松。在接下来的教程中，我们将会大量使用松露。

*   [Embark](https://github.com/embark-framework/embark) :一个允许你轻松开发和部署分散应用(DApps)的框架。

*   [MetaMask](https://metamask.io/) :一座桥梁，让你今天就能在浏览器中访问明天的分布式网络。它允许您在浏览器中运行以太坊 DApps，而无需运行完整的以太坊节点。关于如何用 MetaMask 开发，查看这个[常见问题](https://github.com/MetaMask/faq/)。

*   Dapp : Dapp 是一个用于智能合约开发的简单命令行工具。

*   不同的插件增加了对流行 ide 的可靠性支持，比如[这个可视化代码插件](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity)和[这个 Atom 插件](https://atom.io/packages/language-ethereum)等等。

## 结论

我们已经向您介绍了为以太坊区块链开发智能合约的 Remix IDE。您可以在[文档](http://remix.readthedocs.io/en/latest/)中找到更详细的信息。

有了基本的介绍，您可以自由地深入下去，尝试更改代码，探索编辑器提供的不同功能和选项卡。

## 分享这篇文章