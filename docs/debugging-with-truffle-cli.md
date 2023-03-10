# 使用 Truffle CLI 调试

> 原文：<https://www.sitepoint.com/debugging-with-truffle-cli/>

三十多年来，调试器一直是至关重要的软件开发工具。

现代调试器使我们能够:

*   逐行运行代码
*   在代码中设置断点
*   在断点上设置条件
*   在运行时计算表达式。

大多数现代调试器也高度集成到它们所服务的语言的开发环境中。它们允许通过点击行号来设置断点，通过悬停在变量上来评估表达式，在代码注释中编写条件断点…等等。

那么 Solidity 智能合约调试和调试器是什么状态呢？

## 坚固性调试器

和大多数区块链事物一样，我们仍处于起步阶段。基本的调试器是可用的(并且进展很快)，但是还没有编辑器集成，*和*调试器严重依赖于选择的框架。

在本文中，我们将探索捆绑在 [Truffle 套件](http://truffleframework.com/)中的 Solidity 调试器。

## 入门指南

首先，我们需要安装所有需要的工具。幸运的是，Truffle 框架集成得非常好，所以我们只需要安装它。

首先，安装 [Node.js 和 NPM](https://nodejs.org/en/download/) 。安装 Node 后，您可以通过检查工具的版本来验证它是否已安装，如下所示:

```
➜  ~ node -v
v10.2.1
➜  ~ npm -v
5.6.0 
```

如果您的节点已经启动并运行，让我们安装 Truffle 框架。通过使用`npm`，这变得足够简单，所以只需运行:

```
npm install -g truffle 
```

您可以通过检查版本来检查安装是否成功:

```
truffle version
Truffle v4.1.11 (core: 4.1.11)
Solidity v0.4.24 (solc-js) 
```

## 设置项目

现在你已经设置好了所有的块菌，让我们创建一个新的(空的)块菌项目。打开你的终端，定位到你想要的目录并运行`truffle init`。输出应该如下所示:

```
truffle init
Downloading...
Unpacking...
Setting up...
Unbox successful. Sweet!

Commands:

  Compile:        truffle compile
  Migrate:        truffle migrate
  Test contracts: truffle test 
```

完成此操作后，您应该有一个类似于以下内容的合同结构:

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

现在打开`truffle.js`文件，将以下数据放入其中:

```
module.exports = {
  networks: {
      development: {
          port: 9545,
          host: "127.0.0.1",
          network_id: "*"
      }
  }
}; 
```

保存文件并运行`truffle develop`。您应该会得到类似如下的输出:

```
truffle develop
Truffle Develop started at http://127.0.0.1:9545/

Accounts:
(0) 0x627306090abab3a6e1400e9345bc60c78a8bef57
(1) 0xf17f52151ebef6c7334fad080c5704d77216b732
(2) 0xc5fdf4076b8f3a5357c5e395ab970b5b54098fef
(3) 0x821aea9a577a9b44299b9c15c88cf3087f3b5544
(4) 0x0d1d4e623d10f9fba5db95830f7d3839406c6af2
(5) 0x2932b7a2355d6fecc4b5c0b6bd44cc31df247a2e
(6) 0x2191ef87e392377ec08e7c08eb105ef5448eced5
(7) 0x0f4f2ac550a1b4e2280d04c21cea7ebd822934b5
(8) 0x6330a553fc93768f612722bb8c2ec78ac90b3bbc
(9) 0x5aeda56215b167893e80b4fe645ba6d5bab767de

Private Keys:
(0) c87509a1c067bbde78beb793e6fa76530b6382a4c0241e5e4a9ec0a0f44dc0d3
(1) ae6ae8e5ccbfb04590405997ee2d52d2b330726137b875053c36d94e974d162f
(2) 0dbbe8e4ae425a6d2687f1a7e3ba17bc98c673636790f1b8ad91193c05875ef1
(3) c88b703fb08cbea894b6aeff5a544fb92e78a18e19814cd85da83b71f772aa6c
(4) 388c684f0ba1ef5017716adb5d21a053ea8e90277d0868337519f97bede61418
(5) 659cbb0e2411a44db63778987b1e22153c086a95eb6b18bdf89de078917abc63
(6) 82d052c865f5763aad42add438569276c00d3d88a2d062d36b2bae914d58b8c8
(7) aa3680d5d48a8283413f7a108367c7299ca73f553735860a87b08f39395618b7
(8) 0f62d96d6675f32685bbdb8ac13cda7c23436f63efbb9d07700d8669ff12b7c4
(9) 8d5366123cb560bb606379f90a0bfd4769eecc0557f1b362dcae9012b548b1e5

Mnemonic: candy maple cake sugar pudding cream honey rich smooth crumble sweet treat

⚠️  Important ⚠️  : This mnemonic was created for you by Truffle. It is not secure.
Ensure you do not use it on production blockchains, or else you risk losing funds. 
```

这开创了由`ganache-cli`(前`TestRPC`)支持的区块链块菌开发的先河。

## 撰写和部署合同

在 contracts 目录中，创建一个名为`Storage.sol`的文件。在该文件中，放置以下代码:

```
pragma solidity ^0.4.23;

contract Storage {

    uint[] private _numberStorage;

    event AddedNewNumber(uint position);

    function addNumber(uint newNumber) public returns (uint) {
        _numberStorage.push(newNumber);

        uint numberPosition = _numberStorage.length;

        emit AddedNewNumber(numberPosition);
        return numberPosition;
    }

    function getNumber(uint position) public constant returns (uint) {
        return _numberStorage[position];
    }

} 
```

完成这些后，您的文件结构应该如下所示:

```
├── contracts
│   ├── Migrations.sol
│   └── Storage.sol
├── migrations
│   └── 1_initial_migration.js
├── test
├── truffle-config.js
└── truffle.js 
```

在`migrations`目录中，创建一个名为`2_deploy_migrations.js`的新文件，并将以下代码放入其中:

```
var Storage = artifacts.require("./Storage.sol");

module.exports = function(deployer) {

    deployer.deploy(Storage);

} 
```

这段代码定义了*Truffle 如何将我们的项目迁移到区块链。*

现在在终端中打开一个新标签页(保持`truffle develop`运行)并运行`truffle migrate`。这将编译和迁移您的合同到开发区块链。您应该得到这样的输出:

```
Using network 'development'.

Running migration: 1_initial_migration.js
  Deploying Migrations...
  ... 0x819678a9812313714a27b52c30f065544a331ec5c79ec6c251bc97cd09398d08
  Migrations: 0x8cdaf0cd259887258bc13a92c0a6da92698644c0
Saving successful migration to network...
  ... 0xd7bc86d31bee32fa3988f1c1eabce403a1b5d570340a3a9cdba53a472ee8c956
Saving artifacts... 
```

现在写`truffle console`。这将为您打开一个交互式控制台来测试您的合同。在控制台中，执行以下操作:

```
~> Storage.deployed().then((i) => { iStorage = i }) // Store the contract instance into the iStorage variable

~> iStorage.AddedNewNumber({}).watch((err, res) => { console.log("NUMBER POSITION: " + res.args.position.toNumber()) }) // Subscribe to the added new number event

Filter {
  requestManager:
   RequestManager {
     provider: Provider { provider: [HttpProvider] },
     polls: {},
     timeout: null },
  options:
   { topics:
      [ '0x197006a61de03a2f3b4de7f4c4fab6e30ebedef7c1a42d716b2140f184c718b7' ],
     from: undefined,
     to: undefined,
     address: '0xdda6327139485221633a1fcd65f4ac932e60a2e1',
     fromBlock: undefined,
     toBlock: undefined },
  implementation:
   { newFilter:
      { [Function: send] request: [Function: bound ], call: [Function: newFilterCall] },
     uninstallFilter:
      { [Function: send] request: [Function: bound ], call: 'eth_uninstallFilter' },
     getLogs:
      { [Function: send] request: [Function: bound ], call: 'eth_getFilterLogs' },
     poll:
      { [Function: send] request: [Function: bound ], call: 'eth_getFilterChanges' } },
  filterId: null,
  callbacks: [ [Function] ],
  getLogsCallbacks: [],
  pollFilters: [],
  formatter: [Function: bound ] }

~> iStorage.addNumber(13) // Add a new number

{ tx:
   '0xad3f82a6a6cec39dff802f2f16e73bbbc8eff3b68c2ac4da4c371a4c84345a4f',
  receipt:
   { transactionHash:
      '0xad3f82a6a6cec39dff802f2f16e73bbbc8eff3b68c2ac4da4c371a4c84345a4f',
     transactionIndex: 0,
     blockHash:
      '0x464bc0075036cf95484dec165f0248fb0a7db929d14068a312076be14d43d1fe',
     blockNumber: 5,
     gasUsed: 63362,
     cumulativeGasUsed: 63362,
     contractAddress: null,
     logs: [ [Object] ],
     status: '0x01',
     logsBloom:
      '0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000000000000000000000000010000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000080000000000000000000000000000000000000000000000000000000000000000000000000000000000080000000004000000000000000000000000000000000000000000000000000000000000000000010000000000000' },
  logs:
   [ { logIndex: 0,
       transactionIndex: 0,
       transactionHash:
        '0xad3f82a6a6cec39dff802f2f16e73bbbc8eff3b68c2ac4da4c371a4c84345a4f',
       blockHash:
        '0x464bc0075036cf95484dec165f0248fb0a7db929d14068a312076be14d43d1fe',
       blockNumber: 5,
       address: '0x345ca3e014aaf5dca488057592ee47305d9b3e10',
       type: 'mined',
       event: 'AddedNewNumber',
       args: [Object] } ] } 
```

在您运行了`iStorage.addNumber(...)`函数之后，我们订阅的事件应该已经被触发了。如果是这种情况，输出应该包含如下内容:

```
truffle(development)> NUMBER POSITION: 1 
```

现在让我们试着从存储的位置获取数字:

```
~> iStorage.getNumber(1)
iStorage.getNumber(1)
Error: VM Exception while processing transaction: invalid opcode
    at XMLHttpRequest._onHttpResponseEnd (/usr/local/lib/node_modules/truffle/build/webpack:/~/xhr2/lib/xhr2.js:509:1)
    at XMLHttpRequest._setReadyState (/usr/local/lib/node_modules/truffle/build/webpack:/~/xhr2/lib/xhr2.js:354:1)
    at XMLHttpRequestEventTarget.dispatchEvent (/usr/local/lib/node_modules/truffle/build/webpack:/~/xhr2/lib/xhr2.js:64:1)
    at XMLHttpRequest.request.onreadystatechange (/usr/local/lib/node_modules/truffle/build/webpack:/~/web3/lib/web3/httpprovider.js:128:1)
    at /usr/local/lib/node_modules/truffle/build/webpack:/~/truffle-provider/wrapper.js:134:1
    at /usr/local/lib/node_modules/truffle/build/webpack:/~/web3/lib/web3/requestmanager.js:86:1
    at Object.InvalidResponse (/usr/local/lib/node_modules/truffle/build/webpack:/~/web3/lib/web3/errors.js:38:1) 
```

我们得到一个错误！多么方便:看起来我们现在可以使用调试器了。几乎就像是计划好的一样！

## 使用调试器

为了调试事务，您需要找到想要调试的事务的事务散列。复制交易散列，并在终端中以如下形式输入:

```
truffle debug tx_hash 
```

我们将调试将数字添加到存储中的函数。tx 散列对您来说会有所不同，但总体形式将与此类似。输出应该是相同的:

```
truffle debug 0x34d26b8bcf01f23dfbef0de28384623ca3ed3e3e7fe28f1a0968d363cf38765f
Compiling ./contracts/Migrations.sol...
Compiling ./contracts/Storage.sol...

Gathering transaction data...

Addresses affected:
 0x345ca3e014aaf5dca488057592ee47305d9b3e10 - Storage

Commands:
(enter) last command entered (step next)
(o) step over, (i) step into, (u) step out, (n) step next
(;) step instruction, (p) print instruction, (h) print this help, (q) quit
(b) toggle breakpoint, (c) continue until breakpoint
(+) add watch expression (`+:<expr>`), (-) remove watch expression (-:<expr>)
(?) list existing watch expressions
(v) print variables and values, (:) evaluate expression - see `v`

Storage.sol:

2:
3:
4: contract Storage {
   ^^^^^^^^^^^^^^^^^^

debug(development:0x34d26b8b...)> 
```

这里我们可以看到调试控制台。它比大多数开发人员习惯的现代调试器要原始得多，但是它具有所有需要的功能。让我们来探索一下它的功能。

在控制台中，运行以下一系列命令:

```
debug(development:0x34d26b8b...)> o

Storage.sol:

 8:     event AddedNewNumber(uint position);
 9:
10:     function addNumber(uint newNumber) public returns (uint) {
        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ 
```

通过键入`o`，我们*跨过了*函数行——意味着我们没有进入我们调用的函数体。

现在我们位于第 10 行——或者添加一个数字的函数的头。让我们更进一步。再次键入`o`。

```
Storage.sol:

 9:
10:     function addNumber(uint newNumber) public returns (uint) {
11:         _numberStorage.push(newNumber);
            ^^^^^^^^^^^^^^ 
```

现在，我们将数字推送到数组中。再次键入`o`。

```
Storage.sol:

11:         _numberStorage.push(newNumber);
12:
13:         uint numberPosition = _numberStorage.length; 
```

我们已经推送了数字，并正在获取该数字在数组中的位置。再次按下`o`。

```
Storage.sol:

13:         uint numberPosition = _numberStorage.length;
14:
15:         emit AddedNewNumber(numberPosition);
                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ 
```

我们在发射位置的线上。现在，我们不按`o`键，而是检查我们的变量，并尝试从数组中获取数字。我们可以使用两种方法中的一种。第一种，也是更通用的一种，只需键入`v`并检查函数上下文中所有可用的变量。

通过输入`v`来完成。输出应该是这样的:

```
numberPosition: 1
     newNumber: 12
              : 189
_numberStorage: [ 12 ] 
```

另一种方法更具体，我们可以通过输入`:<variable_name>`来实现。通过输入`:numberPosition`来完成。你应该得到`1`作为输出。

现在让我们尝试评估一个表达式。您也可以用`:`构造来实现这一点。

让我们试着用`:_numberStorage[numberPosition]`找回我们的号码:

```
debug(development:0x34d26b8b...)> :_numberStorage[numberPosition]
undefined 
```

通过运行它，我们得到输出`undefined`。这意味着那里没有号码。

对于所有开发人员来说，这个错误应该是不言而喻的。我们尝试访问位置 1 的第一个元素。因为在 Solidity 中，像在大多数语言中一样，数组是零索引的，所以我们需要用数字零来访问它。

按`q`退出调试器，让我们修复代码。在您的`Storage.sol`中更改此:

```
uint numberPosition = _numberStorage.length; 
```

对此:

```
uint numberPosition = _numberStorage.length - 1; 
```

现在再次运行`truffle migrate`，结束后运行`truffle console`。在控制台中，再次运行命令:

```
~> Storage.deployed().then((i) => { iStorage = i})
~> iStorage.AddedNewNumber({}).watch((err, res) => { console.log("NUMBER POSITION: " + res.args.position.toNumber()) });
~> iStorage.addNumber(12);
~> output: "NUMBER POSITION: 0"
~> iStorage.getNumber(0)
~> iStorage.getNumber(0).then((res) => { console.log(res.toNumber()) } )
~> output: 12 
```

您已经成功部署并调试了智能合约！

## 分享这篇文章