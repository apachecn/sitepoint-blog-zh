# 松露:测试智能合约

> 原文：<https://www.sitepoint.com/truffle-testing-smart-contracts/>

在我们对 Truffle 的[介绍中，我们讨论了 Truffle 是什么，以及它如何帮助你自动化编译、测试和部署智能合约的工作。](https://www.sitepoint.com/truffle-introduction/)

在本文中，我们将探索如何测试智能合约。测试是质量智能合同开发中最重要的方面。

为什么要广泛测试？所以你可以避免类似于[这个](http://www.businessinsider.com/ethereum-price-parity-hack-bug-fork-2017-11)，或者[这个](https://coincodex.com/article/50/the-dao-hack-what-happened-and-what-followed/)的事情。智能合约处理价值，有时是巨大的价值——这使它们成为有时间和技能攻击它们的人非常感兴趣的猎物。

你不希望你的项目最终成为[区块链的坟墓](https://magoo.github.io/Blockchain-Graveyard/)，对吧？

## 入门指南

我们将打造一个简单的、基于智能合同的二手商品市场。

打开您的终端，将自己定位在您想要构建项目的文件夹中。在该文件夹中，运行以下命令:

```
mkdir HashMarket
cd HashMarket
truffle init 
```

您应该会得到一个看起来有点像这样的结果:

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

您还会得到一个如下所示的文件结构:

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

要复习这些文件，请看一下[以前的文章](https://www.sitepoint.com/truffle-introduction/)。简而言之，我们有了基本的`truffle.js`和两个用于向区块链进行初始迁移的文件。

### 准备测试环境

最简单的测试方法是在本地网络上进行。我强烈推荐使用`ganache-cli`(以前称为`TestRPC`)工具进行契约测试。

安装`ganache-cli`(需要节点包管理器):

```
npm install -g ganache-cli 
```

之后，打开一个单独的终端窗口或标签并运行:

```
ganache-cli 
```

您应该会看到类似如下的输出:

```
Ganache CLI v6.1.0 (ganache-core: 2.1.0)

Available Accounts
==================
(0) 0xd14c83349da45a12b217988135fdbcbb026ac160
(1) 0xc1df9b406d5d26f86364ef7d449cc5a6a5f2e8b8
(2) 0x945c42c7445af7b3337834bdb1abfa31e291bc40
(3) 0x56156ea86cd46ec57df55d6e386d46d1bbc47e3e
(4) 0x0a5ded586d122958153a3b3b1d906ee9ff8b2783
(5) 0x39f43d6daf389643efdd2d4ff115e5255225022f
(6) 0xd793b706471e257cc62fe9c862e7a127839bbd2f
(7) 0xaa87d81fb5a087364fe3ebd33712a5522f6e5ac6
(8) 0x177d57b2ab5d3329fad4f538221c16cb3b8bf7a7
(9) 0x6a146794eaea4299551657c0045bbbe7f0a6db0c

Private Keys
==================
(0) 66a6a84ee080961beebd38816b723c0f790eff78f0a1f81b73f3a4c54c98467b
(1) fa134d4d14fdbac69bbf76d2cb27c0df1236d0677ec416dfbad1cc3cc058145e
(2) 047fef2c5c95d5cf29c4883b924c24419b12df01f3c6a0097f1180fa020e6bd2
(3) 6ca68e37ada9b1b88811015bcc884a992be8f6bc481f0f9c6c583ef0d4d8f1c9
(4) 84bb2d44d64478d1a8b9d339ad1e1b29b8dde757e01f8ee21b1dcbce50e2b746
(5) 517e8be95253157707f34d08c066766c5602e519e93bace177b6377c68cba34e
(6) d2f393f1fc833743eb93f108fcb6feecc384f16691250974f8d9186c68a994ef
(7) 8b8be7bec3aca543fb45edc42e7b5915aaddb4138310b0d19c56d836630e5321
(8) e73a1d7d659b185e56e5346b432f58c30d21ab68fe550e7544bfb88765235ae3
(9) 8bb5fb642c58b7301744ef908fae85e2d048eea0c7e0e5378594fc7d0030f100

HD Wallet
==================
Mnemonic:      ecology sweet animal swear exclude quote leopard erupt guard core nice series
Base HD Path:  m/44'/60'/0'/0/{account_index}

Listening on localhost:8545 
```

这是为您创建的所有帐户`ganache-cli`的列表。你可以使用任何你想使用的账户，但是这些账户会预装乙醚，所以它们非常有用(因为测试需要乙醚支付[汽油费用](https://www.sitepoint.com/ethereum-transaction-costs))。

之后，转到您的`truffle.js`或`truffle-config.js`文件，并将开发网络添加到您的配置中:

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

### 撰写智能合同

我们要做的第一件事是编写 HashMarket 智能契约。我们将尽可能保持简单，同时仍然保留所需的功能。

HashMarket 是区块链上的一种易贝。它使得卖家可以发布产品，买家可以购买产品。它还使卖家能够删除未售出的产品。

在您的项目中，在`contracts`文件夹中，创建一个新文件并将其命名为`HashMarket.sol`。在该文件中，添加以下代码:

```
pragma solidity 0.4.21;

contract HashMarket {

    // Track the state of the items, while preserving history
    enum ItemStatus {
        active,
        sold,
        removed
    }

    struct Item {
        bytes32 name;
        uint price;
        address seller;
        ItemStatus status;
    }

    event ItemAdded(bytes32 name, uint price, address seller);
    event ItemPurchased(uint itemID, address buyer, address seller);
    event ItemRemoved(uint itemID);
    event FundsPulled(address owner, uint amount);

    Item[] private _items;
    mapping (address => uint) public _pendingWithdrawals;

    modifier onlyIfItemExists(uint itemID) {
        require(_items[itemID].seller != address(0));
        _;
    }

    function addNewItem(bytes32 name, uint price) public returns (uint) {

        _items.push(Item({
            name: name,
            price: price,
            seller: msg.sender,
            status: ItemStatus.active
        }));

        emit ItemAdded(name, price, msg.sender);
        // Item is pushed to the end, so the lenth is used for
        // the ID of the item
        return _items.length - 1;
    }

    function getItem(uint itemID) public view onlyIfItemExists(itemID)
    returns (bytes32, uint, address, uint) {

        Item storage item = _items[itemID];
        return (item.name, item.price, item.seller, uint(item.status));
    }

    function buyItem(uint itemID) public payable onlyIfItemExists(itemID) {

        Item storage currentItem = _items[itemID];

        require(currentItem.status == ItemStatus.active);
        require(currentItem.price == msg.value);

        _pendingWithdrawals[currentItem.seller] = msg.value;
        currentItem.status = ItemStatus.sold;

        emit ItemPurchased(itemID, msg.sender, currentItem.seller);
    }

    function removeItem(uint itemID) public onlyIfItemExists(itemID) {
        Item storage currentItem = _items[itemID];

        require(currentItem.seller == msg.sender);
        require(currentItem.status == ItemStatus.active);

        currentItem.status = ItemStatus.removed;

        emit ItemRemoved(itemID);
    }

    function pullFunds() public returns (bool) {
        require(_pendingWithdrawals[msg.sender] > 0);

        uint outstandingFundsAmount = _pendingWithdrawals[msg.sender];

        if (msg.sender.send(outstandingFundsAmount)) {
            emit FundsPulled(msg.sender, outstandingFundsAmount);
            return true;
        } else {
            return false;
        }
    }
} 
```

完成这些之后，试着运行`truffle compile`看看代码是否可以编译。因为坚固性倾向于改变惯例，如果你的代码不能编译，可能的解决方案是使用旧版本的编译器(0.4.21。是写这个的版本，不会有问题)。

### 编写迁移

您需要编写一个迁移，让 Truffle 知道如何将您的合同部署到区块链。进入`migrations`文件夹，创建一个名为`2_deploy_contracts.js`的新文件。在该文件中，添加以下代码:

```
var HashMarket = artifacts.require("./HashMarket.sol");

module.exports = function(deployer) {
    deployer.deploy(HashMarket);
}; 
```

因为我们只有一份合同，所以迁移文件非常简单。

现在运行`truffle migrate`,希望您会得到这样的结果:

```
Using network 'development'.

Running migration: 1_initial_migration.js
  Deploying Migrations...
  ... 0xad501b7c4e183459c4ee3fee58ea9309a01aa345f053d053b7a9d168e6efaeff
  Migrations: 0x9d69f4390c8bb260eadb7992d5a3efc8d03c157e
Saving successful migration to network...
  ... 0x7deb2c3d9dacd6d7c3dc45dc5b1c6a534a2104bfd17a1e5a93ce9aade147b86e
Saving artifacts...
Running migration: 2_deploy_contracts.js
  Deploying HashMarket...
  ... 0xcbc967b5292f03af2130fc0f5aaced7080c4851867abd917d6f0d52f1072d91e
  HashMarket: 0x7918eaef5e6a21a26dc95fc95ce9550e98e789d4
Saving successful migration to network...
  ... 0x5b6a332306f739b27ccbdfd10d11c60200b70a55ec775e7165358b711082cf55
Saving artifacts... 
```

## 测试智能合约

可以使用 Solidity 或者 JavaScript 进行智能合约测试。在测试智能合约时，可靠性可能更直观一点，但是 JavaScript 给了你更多的可能性。

### 坚固性测试

为了开始测试，在项目的`test`文件夹中，创建一个名为`TestHashMarket.sol`的文件。Truffle 套件为我们提供了测试助手函数，所以我们需要导入这些函数。在文件的开头，添加:

```
pragma solidity ^0.4.20;

import "truffle/Assert.sol";
import "truffle/DeployedAddresses.sol";
import "../contracts/HashMarket.sol"; 
```

前两项进口是重要的。

`Assert`导入让我们可以访问各种测试功能，如`Assert.equals()`、`Assert.greaterThan()`等。通过这种方式，Assert 与 Truffle 一起工作来自动化大多数“无聊”的代码编写。

`DeployedAddresses` import 为我们管理合同地址。因为每次你改变你的合同，你必须把它重新部署到一个新的地址*和*即使你不改变它，每次你测试合同应该重新部署从原始状态开始。`DeployedAddresses`库为我们管理这个。

现在让我们写一个测试。在`import`指令下，添加以下内容:

```
contract TestHashMarket {

    function testAddingNewProduct() public {
        // DeployedAddresses.HashMarket() handles contract address
        // management for us
        HashMarket market = HashMarket(DeployedAddresses.HashMarket());

        bytes32 expectedName = "T";
        uint expectedPrice = 1000;

        uint itemID = market.addNewItem(expectedName, expectedPrice);

        bytes32 name;
        uint price;
        address seller;
        uint status;

        (name, price, seller, status) = market.getItem(itemID);

        Assert.equal(name, expectedName, "Item name should match");
        Assert.equal(price, expectedPrice, "Item price should match");
        Assert.equal(status, uint(HashMarket.ItemStatus.active), "Item status at creation should be .active");
        Assert.equal(seller, this, "The function caller should be the seller");
    }

} 
```

让我们来看看测试的一些重要部分。首先:

```
HashMarket market = HashMarket(DeployedAddresses.HashMarket()); 
```

这段代码使用`DeployedAddresses`库来创建一个用于测试的`HashMarket`契约
的新实例。

```
Assert.equal(<current>, <expected>, <message>) 
```

这部分代码负责检查两个值是否相等。如果是，它向测试套件传递一个成功消息。如果没有，则表示失败。它还会附加消息，这样您就可以知道*您的代码在哪里失败了。*

现在让我们运行这个:

```
truffle test 
```

您应该会得到这样的结果:

```
TestHashMarket
    1) testAddingNewProduct
    > No events were emitted

  0 passing (879ms)
  1 failing

  1) TestHashMarket testAddingNewProduct:
     Error: VM Exception while processing transaction: revert
      at Object.InvalidResponse (/usr/local/lib/node_modules/truffle/build/webpack:/~/web3/lib/web3/errors.js:38:1)
      at /usr/local/lib/node_modules/truffle/build/webpack:/~/web3/lib/web3/requestmanager.js:86:1
      at /usr/local/lib/node_modules/truffle/build/webpack:/~/truffle-provider/wrapper.js:134:1
      at XMLHttpRequest.request.onreadystatechange (/usr/local/lib/node_modules/truffle/build/webpack:/~/web3/lib/web3/httpprovider.js:128:1)
      at XMLHttpRequestEventTarget.dispatchEvent (/usr/local/lib/node_modules/truffle/build/webpack:/~/xhr2/lib/xhr2.js:64:1)
      at XMLHttpRequest._setReadyState (/usr/local/lib/node_modules/truffle/build/webpack:/~/xhr2/lib/xhr2.js:354:1)
      at XMLHttpRequest._onHttpResponseEnd (/usr/local/lib/node_modules/truffle/build/webpack:/~/xhr2/lib/xhr2.js:509:1)
      at IncomingMessage.<anonymous> (/usr/local/lib/node_modules/truffle/build/webpack:/~/xhr2/lib/xhr2.js:469:1)
      at endReadableNT (_stream_readable.js:1106:12)
      at process._tickCallback (internal/process/next_tick.js:178:19) 
```

我们的测试失败了:-(

让我们检查一下合同中可能存在的错误。

经过仔细检查，我们会发现我们合同的问题出在我们`addNewItem`方法的`return`语句中:

```
function addNewItem(bytes32 name, uint price) public returns (uint) {

        _items.push(Item({
            name: name,
            price: price,
            seller: msg.sender,
            status: ItemStatus.active
        }));

        // Item is pushed to the end, so the lenth is used for
        // the ID of the item
        return _items.length;
} 
```

因为数组是零索引的，并且我们使用数组位置作为 ID，所以我们实际上应该返回`_items.length - 1`。修复此错误并再次运行:

```
truffle test 
```

你应该会得到一个更好的消息:

```
TestHashMarket
  ✓ testAddingNewProduct (130ms)

1 passing (729ms) 
```

我们已经成功地使用 Truffle 测试来修复我们代码中一个非常可能的错误！

## JavaScript 测试

Truffle 使我们能够使用 JavaScript 进行测试，利用 Mocha 测试框架。这使您能够编写更复杂的测试，并从您的测试框架中获得更多的功能。

好吧，让我们写测试。首先，在`test`文件夹中，创建一个文件，命名为`hashmarket.js`。

我们需要做的第一件事，是用 JavaScript 获取对我们合同的引用。为此，我们将使用 Truffle 的`artifacts.require(...)`函数:

```
var HashMarket = artifacts.require("./HashMarket.sol"); 
```

现在我们有了对契约的引用，让我们开始编写测试。首先，我们将使用提供给我们的`contract`函数:

```
contract("HashMarket", function(accounts) {

}); 
```

这为我们的契约创建了一个测试套件。现在为了测试，我们使用 Mocha `it`语法:

```
contract("HashMarket", function(accounts) {
    it("should add a new product", function() {

    });
}); 
```

这描述了我们将要编写的测试，并向我们展示了一条消息，让我们知道测试的目的。现在让我们来编写测试本身。最后，`hashmarket.js`文件应该如下所示。源代码的注释中解释了原因:

```
var HashMarket = artifacts.require("./HashMarket.sol");

contract("HashMarket", function(accounts) {
    it("should add a new product", function() {

        // Set the names of test data
        var itemName = "TestItem";
        var itemPrice = 1000;
        var itemSeller = accounts[0];

        // Since all of our testing functions are async, we store the
        // contract instance at a higher level to enable access from
        // all functions
        var hashMarketContract;

        // Item ID will be provided asynchronously so we extract it
        var itemID;

        return HashMarket.deployed().then(function(instance) {
            // set contract instance into a variable
            hashMarketContract = instance;

            // Subscribe to a Solidity event
            instance.ItemAdded({}).watch((error, result) => {
                if (error) {
                    console.log(error);
                }
                // Once the event is triggered, store the result in the
                // external variable
                itemID = result.args.itemID;
            });

            // Call the addNewItem function and return the promise
            return instance.addNewItem(itemName, itemPrice, {from: itemSeller});
        }).then(function() {
            // This function is triggered after the addNewItem call transaction
            // has been mined. Now call the getItem function with the itemID
            // we received from the event
            return hashMarketContract.getItem.call(itemID);
        }).then(function(result) {
            // The result of getItem is a tuple, we can deconstruct it
            // to variables like this
            var [name, price, seller, status] = result;

            // Start testing. Use web3.toAscii() to convert the result of
            // the smart contract from Solidity bytecode to ASCII. After that
            // use the .replace() to pad the excess bytes from bytes32
            assert.equal(itemName, web3.toAscii(name).replace(/\u0000/g, ''), "Name wasn't properly added");
            // Use assert.equal() to check all the variables
            assert.equal(itemPrice, price, "Price wasn't properly added");
            assert.equal(itemSeller, seller, "Seller wasn't properly added");
            assert.equal(status, 0, "Status wasn't properly added");
        });
    });
}); 
```

运行`truffle test`,您应该会得到这样的结果:

```
TestHashMarket
  ✓ testAddingNewProduct (109ms)

Contract: HashMarket
  ✓ should add a new product (64ms)

2 passing (876ms) 
```

您的测试已经通过，您可以确信没有回归错误。

对于家庭作业，编写契约中所有其他功能的测试。

## 分享这篇文章