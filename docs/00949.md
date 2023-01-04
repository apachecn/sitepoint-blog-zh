# 编译和智能合同:ABI 解释

> 原文：<https://www.sitepoint.com/compiling-smart-contracts-abi/>

大多数智能合约都是用高级编程语言开发的。目前最受欢迎的是[稳健](https://solidity.readthedocs.io/en/v0.4.24/)，而[维伯](https://github.com/ethereum/vyper)希望在不久的将来登上王位。

但是，驱动以太坊的机制无法理解高级语言，而是用低级得多的语言交谈。

## 以太坊虚拟机(EVM)

以太坊智能合约是在运行完整以太坊客户端的所有节点上运行的编程指令集。以太坊中运行智能合约指令的部分被称为 EVM。这是一个虚拟机，不像 Java 的 JVM。EVM 读取称为**以太坊字节码**的智能合约的底层表示。

以太坊字节码是由多个 **[操作码](https://github.com/trailofbits/evm-opcodes)** 组成的汇编语言。每个操作码在以太坊区块链上执行特定的操作。

问题是，我们如何从这里开始:

```
pragma solidity 0.4.24;

contract Greeter {

    function greet() public constant returns (string) {
        return "Hello";
    }

} 
```

对此:

```
PUSH1 0x80 PUSH1 0x40 MSTORE PUSH1 0x4 CALLDATASIZE LT PUSH2 0x41 JUMPI PUSH1 0x0 CALLDATALOAD PUSH29 0x100000000000000000000000000000000000000000000000000000000 SWAP1 DIV PUSH4 0xFFFFFFFF AND DUP1 PUSH4 0xCFAE3217 EQ PUSH2 0x46 JUMPI JUMPDEST PUSH1 0x0 DUP1 REVERT JUMPDEST CALLVALUE DUP1 ISZERO PUSH2 0x52 JUMPI PUSH1 0x0 DUP1 REVERT JUMPDEST POP PUSH2 0x5B PUSH2 0xD6 JUMP JUMPDEST PUSH1 0x40 MLOAD DUP1 DUP1 PUSH1 0x20 ADD DUP3 DUP2 SUB DUP3 MSTORE DUP4 DUP2 DUP2 MLOAD DUP2 MSTORE PUSH1 0x20 ADD SWAP2 POP DUP1 MLOAD SWAP1 PUSH1 0x20 ADD SWAP1 DUP1 DUP4 DUP4 PUSH1 0x0 JUMPDEST DUP4 DUP2 LT ISZERO PUSH2 0x9B JUMPI DUP1 DUP3 ADD MLOAD DUP2 DUP5 ADD MSTORE PUSH1 0x20 DUP2 ADD SWAP1 POP PUSH2 0x80 JUMP JUMPDEST POP POP POP POP SWAP1 POP SWAP1 DUP2 ADD SWAP1 PUSH1 0x1F AND DUP1 ISZERO PUSH2 0xC8 JUMPI DUP1 DUP3 SUB DUP1 MLOAD PUSH1 0x1 DUP4 PUSH1 0x20 SUB PUSH2 0x100 EXP SUB NOT AND DUP2 MSTORE PUSH1 0x20 ADD SWAP2 POP JUMPDEST POP SWAP3 POP POP POP PUSH1 0x40 MLOAD DUP1 SWAP2 SUB SWAP1 RETURN JUMPDEST PUSH1 0x60 PUSH1 0x40 DUP1 MLOAD SWAP1 DUP2 ADD PUSH1 0x40 MSTORE DUP1 PUSH1 0x5 DUP2 MSTORE PUSH1 0x20 ADD PUSH32 0x48656C6C6F000000000000000000000000000000000000000000000000000000 DUP2 MSTORE POP SWAP1 POP SWAP1 JUMP STOP LOG1 PUSH6 0x627A7A723058 KECCAK256 SLT 0xec 0xe 0xf5 0xf8 SLT 0xc7 0x2d STATICCALL ADDRESS SHR 0xdb COINBASE 0xb1 BALANCE 0xe8 0xf8 DUP14 0xda 0xad DUP13 LOG1 0x4c 0xb4 0x26 0xc2 DELEGATECALL PUSH7 0x8994D3E002900 
```

## 可靠性编译器

现在，我们将把重点放在 Solidity 编译器上，但同样的原则也适用于 Vyper 或任何其他用于 EVM 的高级语言。

要事第一:安装 [Node.js](https://nodejs.org/en/) 。

完成此操作后，到您的终端运行以下命令:

```
npm install -g solc 
```

这将安装`solc`——Solidity 编译器。现在创建一个空目录。在该目录中创建一个名为`SimpleToken.sol`的文件，并放入以下代码:

```
pragma solidity ^0.4.24;

contract SimpleToken {

    mapping(address => uint) private _balances;

    constructor() public {
        _balances[msg.sender] = 1000000;
    }

    function getBalance(address account) public constant returns (uint) {
        return _balances[account];
    }

    function transfer(address to, uint amount) public {
        require(_balances[msg.sender] >= amount);

        _balances[msg.sender] -= amount;
        _balances[to] += amount;
    }
} 
```

这是最简单的令牌智能合约，但是它有几个重要的特性对本教程很有用。它们是:

*   公共职能
*   私人功能
*   性能

完成这些之后，运行文件上新安装的`solc`。您可以通过运行以下命令来完成此操作:

```
solcjs SimpleToken.sol 
```

您应该会得到类似如下的输出:

```
Invalid option selected, must specify either --bin or --abi 
```

您的编译应该会失败。

刚刚发生了什么？什么是`bin`什么是`abi`？

`bin`只是编译后字节码的一个紧凑的二进制表示。`PUSH`、`PULL`或`DELEGATECALL`引用的不是操作码，而是它们的二进制表示，用文本编辑器阅读时看起来像随机数。

## ABI —应用程序二进制接口

一旦我们的`bin`输出被部署到区块链，契约将获得它的地址，字节码将被推入以太坊存储器。但是一个大问题仍然存在:我们如何解释代码？

仅仅从字节码是无法知道契约有函数`transfer(:)`和`getBalance(:)`的。更不清楚这些功能是`public`、`private`还是`constant`。合同在没有上下文的情况下被部署*。*

签订这样的合同几乎是不可能的。我们不知道每个函数在字节码中的位置，它接受哪些参数，或者我们是否被允许调用它。这就是 ABI 发挥作用的地方。

ABI 是一个描述已部署契约及其功能的`.json`文件。它允许我们将契约上下文化并调用它的函数。

让我们再次尝试运行我们的`solcjs`。运行以下命令:

```
solcjs SimpleToken.sol --abi
solcjs SimpleToken.sol --bin 
```

您的目录现在应该具有如下结构:

```
.
├── SimpleToken.sol
├── SimpleToken_sol_SimpleToken.abi
└── SimpleToken_sol_SimpleToken.bin 
```

`SimpleToken_sol_SimpleToken.abi`文件应该是这样的:

```
[{
    "constant": false,
    "inputs": [{
        "name": "to",
        "type": "address"
    }, {
        "name": "amount",
        "type": "uint256"
    }],
    "name": "transfer",
    "outputs": [],
    "payable": false,
    "stateMutability": "nonpayable",
    "type": "function"
}, {
    "constant": true,
    "inputs": [{
        "name": "account",
        "type": "address"
    }],
    "name": "getBalance",
    "outputs": [{
        "name": "",
        "type": "uint256"
    }],
    "payable": false,
    "stateMutability": "view",
    "type": "function"
}, {
    "inputs": [],
    "payable": false,
    "stateMutability": "nonpayable",
    "type": "constructor"
}] 
```

我们可以看到文件描述了合同的功能。它定义了:

*   它们的名字:函数的名字
*   他们的支付能力:你是否可以发送以太给他们
*   输出:函数的返回值
*   它们的状态可变性:函数是只读的还是有写权限。

通过阅读，这些都很容易理解。但是前面我提到过，ABI 还定义了用户如何调用函数——也就是说，函数的*位置*与智能合约地址相关。

知道函数的名字是不够的；我们还需要知道*如何*(在哪里)调用它。

这是通过对我们前面提到的功能属性(名称、可支付性、输出等)运行确定性算法来完成的。).这个功能的细节可以在[这里](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI#function-selector-and-argument-encoding)找到。

## 例子

ABI 是契约接口的描述。它不包含任何代码，不能单独运行。字节码是可执行的 EVM 代码，但是它本身是没有上下文的。

为了在智能合约中调用函数，我们需要同时使用 ABI 和字节码。幸运的是，当我们使用提供的框架之一与智能合约交互时，这些都被抽象掉了。

使用`web3.js`框架的一个例子如下所示:

```
var simpletokenContract = web3.eth.contract([{"constant":false,"inputs":[{"name":"to","type":"address"},{"name":"amount","type":"uint256"}],"name":"transfer","outputs":[],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"account","type":"address"}],"name":"getBalance","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"inputs":[],"payable":false,"stateMutability":"nonpayable","type":"constructor"}]);
var simpletoken = simpletokenContract.new(
   {
     from: web3.eth.accounts[0],
     data: "0x608060405234801561001057600080fd5b50620f42406000803373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002081905550610252806100666000396000f30060806040526004361061004c576000357c0100000000000000000000000000000000000000000000000000000000900463ffffffff168063a9059cbb14610051578063f8b2cb4f1461009e575b600080fd5b34801561005d57600080fd5b5061009c600480360381019080803573ffffffffffffffffffffffffffffffffffffffff169060200190929190803590602001909291905050506100f5565b005b3480156100aa57600080fd5b506100df600480360381019080803573ffffffffffffffffffffffffffffffffffffffff1690602001909291905050506101de565b6040518082815260200191505060405180910390f35b806000803373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020541015151561014257600080fd5b806000803373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060008282540392505081905550806000808473ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600082825401925050819055505050565b60008060008373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000205490509190505600a165627a7a72305820c9da07d4976adbf00a4b5fe4e23330dbaf3cdcbfd4745eed78c702bf27d944060029",
     gas: '4700000'
   }, function (e, contract){
    console.log(e, contract);
    if (typeof contract.address !== 'undefined') {
         console.log('Contract mined! address: ' + contract.address + ' transactionHash: ' + contract.transactionHash);
    }
 }) 
```

首先，我们定义了`simpelTokenContract`，它是从外部看合同的*描述*。我们通过向它传递`SimpleToken.sol`的 ABI 来做到这一点。

然后，我们通过调用`simpletokenContract.new(...)`并向其传递合同的*数据*(可执行代码)来创建合同`simpletoken`的`instance`。

web3.js 在后台合并了这两者，现在拥有了调用我们的智能契约上的函数所需的所有信息。

## 结论

在这个智能合约编译的简短概述中，我们解释了 ABI 以及如何调用部署在以太坊区块链上的智能合约。虽然您实际上永远不会直接使用它，但是知道它是值得的，因为太多的抽象会导致错误。

## 分享这篇文章