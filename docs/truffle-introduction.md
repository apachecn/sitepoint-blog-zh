# 介绍区块链智能合约套件 Truffle

> 原文：<https://www.sitepoint.com/truffle-introduction/>

**在*智能合约*开发的早期(大约 2016 年)，要走的路是在您最喜欢的文本编辑器中编写智能合约，并通过直接调用`geth`和`solc`来部署它们。**

让这个过程变得更加*用户友好*的方法是制作 bash 脚本，它可以首先编译，然后部署合同……这更好，但仍然非常初级——当然，脚本的问题是缺乏*标准化*和 bash 脚本的次优体验。

答案是两种截然不同的口味——*松露和 Embark*——其中松露是更受欢迎的一种(也是我们将在本文中讨论的一种)。

为了理解 Truffle 背后的推理，我们必须理解它试图解决的问题，这些问题将在下面详述。

**编译**

**环境**
契约需要有开发、集成和生产环境，每个环境都有自己的以太坊节点地址、账户等。

**测试**
合同*必须*可测试。测试软件的重要性怎么强调都不为过。对于智能合约，重要性要重要得多。所以。测试。你的。合同！

配置
你的开发、集成和生产环境应该封装在一个配置文件中，这样它们就可以提交给 git 并被队友重用。

**Web3js 集成**
Web3.js 是一个 JavaScript 框架，能够通过 Web 应用程序更轻松地与智能合约进行通信。Truffle 更进了一步，在 Truffle 控制台中启用了 Web3.js 接口，这样你就可以在浏览器之外，在开发模式下调用 Web 函数。

## 安装 Truffle

安装 Truffle 的最佳方式是使用节点包管理器(npm)。在你的电脑上设置好 NPM 后，打开终端输入以下命令来安装 Truffle:

```
npm install -g truffle 
```

*注意:Linux 机器上可能需要前缀`sudo`。*

## 入门指南

一旦安装了 Truffle，感受它如何工作的最好方法是建立名为“MetaCoin”的 Truffle 演示项目。

打开终端应用程序(在 Linux 和 macOS 上是 terminal，在 Windows 上是 Git Bash、Powershell、Cygwin 或类似的程序),将自己放在希望初始化项目的文件夹中。

然后运行以下命令:

```
mkdir MetaCoin
cd MetaCoin
truffle unbox metacoin 
```

您应该会看到如下输出:

```
Downloading...
Unpacking...
Setting up...
Unbox successful. Sweet!

Commands:

  Compile contracts: truffle compile
  Migrate contracts: truffle migrate
  Test contracts:    truffle test 
```

如果你得到一些错误，这可能是你使用了不同版本的松露。本教程针对的版本是`Truffle v4.1.5`，但是说明应该至少与几个版本相关。

## 块菌项目结构

你的松露文件夹应该看起来有点像这样:

```
.
├── contracts
│   ├── ConvertLib.sol
│   ├── MetaCoin.sol
│   └── Migrations.sol
├── migrations
│   ├── 1_initial_migration.js
│   └── 2_deploy_contracts.js
├── test
│   ├── TestMetacoin.sol
│   └── metacoin.js
├── truffle-config.js
└── truffle.js 
```

### 合同文件夹

这是您将放置所有智能合同的文件夹。

在您的 contracts 文件夹中，还有一个`Migrations.sol`文件，这是一个特殊的文件——但在下一节中会有更多的介绍。

Truffle 编译项目时，会遍历`contracts`文件夹，编译所有兼容的文件。目前，最常用的文件是扩展名为`.sol`的 Solidity 文件。

将来，这可能会过渡到 Vyper 或 SolidityX(两者都更适合智能契约开发，但目前较少使用)。

## 迁移文件夹

什么是松露迁徙？本质上，它是一个脚本，定义了如何将合同部署到区块链。

### 为什么我们需要迁移？

随着项目变得越来越复杂，部署的复杂性也相应地变得越来越复杂。

让我们举个例子:

*   你有智能合约`One`、`Two`和`Three`
*   智能契约`Three`包含对智能契约`One` *的引用，并且*在其构造函数中需要契约`Two`的地址。

该示例要求契约不仅要按顺序部署，而且要相互交叉引用。简而言之，迁移使我们能够自动化这一过程。

您应该如何做的大致概述如下:

```
var One = artifacts.require("One");
var Two = artifacts.require("Two");
var Three = artifacts.require("Three");

module.exports = function(deployer) {
  deployer.deploy(One).then(function() {
    deployer.deploy(Two).then(function() {
      deployer.deploy(Three, One.address);
    })
  });
}; 
```

除此之外，迁移还允许您做许多其他很酷的事情，例如:

*   为部署设置最大气体量
*   更改部署的`from`地址
*   部署库
*   调用任意契约函数

### 初始迁移

正如您在`MetaCoin`项目中注意到的，您有一个名为`1_initial_migration.js`的文件。这个文件的作用是将`Migrations.sol`合同部署到区块链。

通常你不必对这个文件做任何事情，一旦它被初始化，所以我们不会太关注这个。

## 测试文件夹

我说过了:你！必须！测试！聪明！合同！没有但是，没有如果，没有可能:你必须做。

但是如果你要做这件事，有一个自动化的工具让你无缝地做这件事会很酷。

Truffle 通过内置的测试框架实现了这一点。它使您能够用 Solidity 或 JavaScript 编写测试。

MetaCoin 项目中的例子基本上是不言自明的，所以我们不会对此进行过多的讨论。

关键是，如果您正在编写可靠性测试，您可以使用可靠性`import`指令将合同导入到测试中:

```
import "../contracts/MetaCoin.sol"; 
```

如果您用 JavaScript 编写它们，您可以用`artifacts.require()` helper 函数导入它们:

```
var MetaCoin = artifacts.require("./MetaCoin.sol"); 
```

## 配置文件

配置文件被称为`truffle.js`或`truffle-config.js`。在大多数情况下，它将被称为`truffle.js`，但是由于 Windows 机器上奇怪的命令优先规则，这是一个后备。

只要知道，当你看到`truffle.js`或`truffle-config.js`时，它们基本上是同一个东西。(还有，不要在 windows 上用 CMDPowerShell 明显更好。)

配置文件定义了一些东西，下面将详细介绍。

**环境**
开发、测试、现场(生产)。您可以定义 Geth note 的地址、`network_id`、用于部署的最大气体、您愿意支付的气体价格。

**项目结构**
您可以更改文件的构建和位置，但这不是必需的，甚至不建议这样做。

**编译器版本和设置**
修复`solc`版本，设置`-O`(优化)参数。

**套餐管理**

*   Truffle 可以与 EthPM(以太坊软件包管理器)一起工作，但它仍然非常不确定。
*   您可以为 EthPM 设置依赖项，以便在您的 Truffle 项目中使用。

**项目描述**
谁做了这个项目，项目名称是什么，联系地址等。

**牌照**
使用 [GPLv3](https://pbs.twimg.com/profile_images/561116211258220544/FlICmGnj_400x400.jpeg) 。

## 运行代码

为了编译您的合同，请运行以下命令:

```
truffle compile 
```

为了运行迁移，您可以使用以下命令:

```
truffle migrate 
```

或者您可以通过指定一个环境来实现:

```
truffle migrate --network live 
```

为了测试您的合同，请运行以下命令:

```
truffle test 
```

或者，您可以通过运行以下命令来运行特定于*的*测试:

```
truffle test ./path/to/FileTest.sol 
```

## 结论

Truffle 是一个非常方便的工具，可以让这个全新生态系统的开发变得更加容易。它旨在将来自其他发展世界的标准和通用实践引入区块链实验的一个小角落。

这个快速教程演示并解释了基础知识，但是要真正理解 Truffle，您需要更深入地研究并在实际项目中进行实验。这就是我们将在 SitePoint 的[区块链中心](https://www.sitepoint.com/blog/)探索的。接下来，我们将在[测试智能合约](https://www.sitepoint.com/truffle-testing-smart-contracts)和[松露迁移](https://www.sitepoint.com/truffle-migrations-explained)中看到更多细节。

## 分享这篇文章