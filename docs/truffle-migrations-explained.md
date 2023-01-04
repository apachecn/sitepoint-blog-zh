# 块菌迁移解释

> 原文：<https://www.sitepoint.com/truffle-migrations-explained/>

一般来说，迁移是开发人员自动部署数据及其支持结构的方法。它们对于管理新软件版本的部署非常有用，因此并不是区块链开发独有的。

Truffle 迁移使我们能够将智能合同“推送”到以太坊区块链(本地[，tesnet 或 mainnet](https://bitfalls.com/2018/02/08/what-is-an-ethereum-testnet-and-how-is-it-used/) )，并设置必要的步骤来将合同与其他合同链接，以及用初始数据填充合同。

迁移真正出彩的地方是区块链上的合同地址管理。这份通常单调乏味的工作几乎完全被松露抽象化了。

## 先决条件

确保你已经安装了[松露框架](http://truffleframework.com/)和 [Ganache CLI](https://github.com/trufflesuite/ganache-cli) 。

## 入门指南

首先，选择一个项目文件夹，然后运行`truffle init`。您应该会得到类似如下的输出:

```
Downloading...
Unpacking...
Setting up...
Unbox successful. Sweet!

Commands:

  Compile:        truffle compile
  Migrate:        truffle migrate
  Test contracts: truffle test 
```

这个命令在您所在的目录中创建一个准系统 Truffle 项目。该目录如下所示:

```
.
├── contracts
│   └── Migrations.sol
├── migrations
│   └── 1_initial_migration.js
├── test
├── truffle-config.js
└── truffle.js 
```

首先，在`contracts`目录中，创建一个名为`Storage.sol`的新文件，应该如下所示:

```
pragma solidity ^0.4.21;

contract Storage {

    mapping (string => string) private _store;

    function addData(string key, string value) public {
        require(bytes(_store[key]).length == 0);
        _store[key] = value;
    }

    function removeData(string key) public returns (string) {
        require(bytes(_store[key]).length != 0);
        string prev = _store[key];
        delete _store[key];
        return prev;
    }

    function changeData(string key, string newValue) public {
        require(bytes(_store[key]).length != 0);
        _store[key] = newValue;
    }

} 
```

## 初始迁移

您可能已经注意到，当您运行`truffle init`时，会创建两个文件。他们是`Migrations.sol`和`1_initial_migration.js`。

初始迁移文件很少需要更改。他们所做的基本上是跟踪区块链上的地址。

`Migrations.sol`文件可以是你想要的任何样子，但是它必须符合一个固定的接口，看起来像是由`truffle init`命令创建的接口。您可以在这些文件中做一些高级的迁移处理，但是正如我所说的，很少需要这样做。

这同样适用于`1_initial_migration.js`文件。 *it* 所做的只是将`Migrations.sol`文件推送到所需的区块链。

## 迁移数据

为了将智能合约部署到以太坊区块链，您必须首先编写迁移。为了开始，在您的`migrations`目录中，创建一个名为`2_deploy_contracts.js`的文件。您的项目结构现在应该如下所示:

```
.
├── contracts
│   ├── Migrations.sol
│   └── Storage.sol
├── migrations
│   ├── 1_initial_migration.js
│   └── 2_deploy_contracts.js
├── test
├── truffle-config.js
└── truffle.js 
```

为了部署带有迁移的智能合约，首先我们需要访问他们的*工件*。这些文件描述了合同地址、部署合同的网络以及合同具有的功能。

那么所有这些数据来自哪里呢？

在您的项目目录中，运行`truffle compile`。如果一切顺利，您应该会得到类似如下的输出:

```
Compiling ./contracts/Migrations.sol...
Compiling ./contracts/Storage.sol...

Writing artifacts to ./build/contracts 
```

根据编译器的版本，你可能会得到一些警告，但是只要没有错误，你就可以开始了。

现在再次检查您的项目目录结构:

```
.
├── build
│   └── contracts
│       ├── Migrations.json
│       └── Storage.json
├── contracts
│   ├── Migrations.sol
│   └── Storage.sol
├── migrations
│   ├── 1_initial_migration.js
│   └── 2_deploy_contracts.js
├── test
├── truffle-config.js
└── truffle.js 
```

注意，现在有一个`build`文件夹，其中包含两个文件——`Migrations.json`和`Storage.json`——它们与`contracts`目录中的智能合同文件相匹配。

这些`*.json`文件包含各自智能合约的描述。描述包括:

*   合同名称
*   契约 ABI(应用程序二进制接口-智能契约中所有函数及其参数和返回值的列表)
*   合同字节码(编译的合同数据)
*   契约部署的字节码(部署到区块链的字节码的最新版本)
*   上次编译协定时使用的编译器版本
*   已部署合同的网络列表以及每个网络上的合同地址。

这个文件使 Truffle 能够创建一个 JavaScript 包装器来与智能合约通信。例如，当您在 JavaScript 代码中调用`contract.address`时，Truffle 框架从`*.json`文件中读取地址，并实现合同版本和网络之间的轻松转换。

## 编写迁移

有了这些知识，让我们编写我们的第一次迁移。在`2_deploy_contracts.js`文件中，写下:

```
// Fetch the Storage contract data from the Storage.json file
var Storage = artifacts.require("./Storage.sol");

// JavaScript export
module.exports = function(deployer) {
    // Deployer is the Truffle wrapper for deploying
    // contracts to the network

    // Deploy the contract to the network
    deployer.deploy(Storage);
} 
```

编写迁移就是这么简单。为了运行迁移脚本，请在终端中运行以下命令:

```
truffle migrate 
```

您应该会得到一个错误消息:

```
Error: No network specified. Cannot determine current network. 
```

这意味着 Truffle 无法找到您想要部署的网络。

为了使用模拟以太坊区块链，在终端中打开一个新标签并运行`ganache-cli`。您应该会得到类似如下的输出:

```
Ganache CLI v6.1.0 (ganache-core: 2.1.0)

Available Accounts
==================
(0) 0x828da2e7b47f9480838f2077d470d39906ad1d8e
(1) 0xa4928865329324560185f1c93b5ebafd7ae6c9e8
(2) 0x957b8b855bed52e11b2d7e9b3e6427771f299f3f
(3) 0xf4b6bcabedaf1ccb3d0c89197c4b961460f1f63d
(4) 0x4bcae97be4a0d1f9a6dea4c23df8a2bffdb51291
(5) 0xe855c7cccac3a65ad24f006bf084c85c0197a779
(6) 0x168cb232283701a816a3d118897eedfcae2aec9d
(7) 0x49563e64868e1d378e20b6ab89813c1bbaa0fd48
(8) 0x467c6f6f526eee9f66776197e3a9798c1cbf78e0
(9) 0xf65b47a3c663e2cc17ded8f197057a091686da43

Private Keys
==================
(0) 8729d0f1d876d692f2f454f564042bd11c1e6d0c9b1808954f171f6f7b926fd6
(1) 452dfeee16e5a0e34fa5348f0ef11f39a8b4635e5f454f77fc228ca9598f6997
(2) 9196ad9fd6234f09ee13726cb889dcbc438c15f98e8ff1feb36a93758fa6d10a
(3) fa47edd832e896314544b98d7e297ac2ce2097b49f8a9d7e7ae0e38154db8760
(4) 7ba1a96db190c14aaee5401dd5faab1af9074d7e6f24bc2f24b5084514bbf405
(5) 90088ce271f227db6be251c3055872c0d3dbdda9fc23ed119cf9d55db7c91259
(6) c36afd6f8f291b45e94ef0059576a86602e9a982b87e0c6fb25cfab4d68e9030
(7) 2766ac8aee110e9ad1ea68d1f28aaafb464fb1ef2a759bf5b2f628d256043c15
(8) 51ccf45f87806e8e9f30f487d6cdd0b44de3ad103f0d8daf9f1e20d9a4728dd9
(9) 398c0f079448c1e3724c9267f07ca4ab88233fc995a3d463c7c64d1a191688f5

HD Wallet
==================
Mnemonic:      void august badge future common warfare dismiss earn dog shell vintage dice
Base HD Path:  m/44'/60'/0'/0/{account_index}

Listening on localhost:8545 
```

这意味着你已经建立了一个私有的区块链，它正在`localhost:8545`上运行。现在让我们设置 Truffle 来部署到网络中。

将以下内容放入`truffle.js`文件:

```
module.exports = {
  networks: {
    development: {
      host: "127.0.0.1",
      port: 8545,
      network_id: "*"
    }
  }
}; 
```

这仅仅意味着您正在将合同部署到运行于`localhost:8545`的网络上。

现在运行`truffle migrate`。您应该会得到类似如下的输出:

```
Using network 'development'.

Running migration: 1_initial_migration.js
  Deploying Migrations...
  ... 0x06595c0eccde8cb0cf642df07beefea11e3e96bfb470e8dbaf6567cecc37aed8
  Migrations: 0x6008e9a2c213d51093d0f18536d1aa3b00a7e058
Saving successful migration to network...
  ... 0x392fb34c755970d1044dc83c56df6e51d5c4d4011319f659026ba27884126d7b
Saving artifacts...
Running migration: 2_deploy_contracts.js
  Deploying Storage...
  ... 0xb8ec575a9f3eca4a11a3f61170231a1816f7c68940d8487e56567adcf5c0a21e
  Storage: 0xd8e2af5be9af2a45fc3ee7cdcb68d9bcc37a3c81
Saving successful migration to network...
  ... 0x15498a1f9d2ce0f867b64cdf4b22ddff56f76aee9cd3d3a92b03b7aa4d881bac
Saving artifacts... 
```

松露把你的合同迁移到网络上保存了艺术品。在构建目录的`Storage.json`文件中，通过检查`networks`对象来检查这是否正确。您应该会看到类似这样的内容:

```
"networks": {
  "1525343635906": {
    "events": {},
    "links": {},
    "address": "0xd8e2af5be9af2a45fc3ee7cdcb68d9bcc37a3c81",
    "transactionHash": "0xb8ec575a9f3eca4a11a3f61170231a1816f7c68940d8487e56567adcf5c0a21e"
  }
} 
```

`1525343635906`是网络的 ID。(以太坊主网络和所有主要测试网络都有固定的 id，如 1、2、3 等。)

`address`是合同部署到的地址。

`transactionHash`是用于合同部署的事务的散列。

我们将在教程的后面看到这是如何有用的。

### 多重合同

块菌移植真正出彩的地方是当有多个合同需要编译、部署和跟踪时(几乎所有区块链项目都有)。

迁移不仅允许我们用一个命令部署多个契约，还允许我们在契约上运行任意函数，获取这些函数的返回值，并将它们传递给后续的契约。

现在，在您的`contracts`目录中，创建一个名为`InfoManager.sol`的文件。在文件中写下这样一份合同:

```
pragma solidity ^0.4.21;

import "./Storage.sol";

contract InfoManager {

    Storage private _dataStore;

    uint private _lastAdded;

    function InfoManager(Storage dataStore) public {
        _dataStore = dataStore;
    }

    function addData(string key, string value) public {
        require((now - 1 days) > _lastAdded);
        _dataStore.addData(key, value);
    }

} 
```

正如我们所看到的，这个合同依赖于`Storage`合同。不仅如此，它将`Storage`契约作为其构造函数中的一个参数。让我们来看看使这成为可能的迁移。迁移包含在名为`2_deploy_contracts.js`的同一个文件中:

```
var Storage = artifacts.require("./Storage.sol");
var InfoManager = artifacts.require("./InfoManager.sol");

module.exports = function(deployer) {

    // Deploy the Storage contract
    deployer.deploy(Storage)
        // Wait until the storage contract is deployed
        .then(() => Storage.deployed())
        // Deploy the InfoManager contract, while passing the address of the
        // Storage contract
        .then(() => deployer.deploy(InfoManager, Storage.address));
} 
```

部署的语法是:

```
...
deployer.deploy(`ContractName`, [`constructor params`]) // Returns a promise
... 
```

由于`deploy(...)`函数返回一个承诺，您可以用任何您喜欢的方式处理它，除了`async`由于某种原因在迁移中不工作。

您还可以在部署协定后运行自定义步骤。例如，迁移可能如下所示:

```
deployer.deploy(Storage)
    .then(() => Storage.deployed())
    .then((instance) => {
        instance.addData("Hello", "world")
    }).then(() => deployer.deploy(InfoManager, Storage.address)); 
```

这将在部署`InfoManager`契约之前，在键`data`处用字符串`world`填充`Storage`契约。

这很有用，因为有时契约之间的相互依赖使得一些数据必须在契约构造函数的范围之外检索或插入。

### 网络

您可以根据您所在的网络有条件地运行某些迁移。这对于在开发阶段填充模拟数据或者将已经部署的 mainnet 契约输入到您的契约中非常有用。

这是通过“扩展”插入的`module.exports`函数参数来实现的:

```
module.exports = function(deployer, network) {
    if (network == "live") {
        // do one thing
    } else if (network == "development") {
        // do other thing
    }
} 
```

### 帐目

`module.exports`默认功能也暴露了你通过以太坊节点或钱包提供商访问的账户。这里有一个例子:

```
module.exports = function(deployer, network, accounts) {
    var defaultAccount;
    if (network == "live") {
        defaultAccount = accounts[0]
    } else {
        defaultAccount = accounts[1]
    }
} 
```

### 图书馆

您还可以通过使用`deployer.link(...)`函数来链接现有的库(已经部署的):

```
...
deployer.deploy(MyLibrary);
deployer.link(MyLibrary, MyContract);
deployer.deploy(MyContract);
... 
```

## 结论

通过使用上面概述的技术，您可以自动化您的大多数区块链部署，并减少分散应用程序开发中涉及的大量样板工作。

## 分享这篇文章