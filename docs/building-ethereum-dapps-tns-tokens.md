# 使用以太坊构建 DApps:创建、部署 TNS 令牌

> 原文：<https://www.sitepoint.com/building-ethereum-dapps-tns-tokens/>

在这个关于用以太坊构建 DApps 的系列教程的第 1 部分中，我们引导了两个版本的本地区块链用于开发:一个 Ganache 版本和一个完全私有的 PoA 版本。

在这一部分中，我们将深入其中，构建并部署我们的 TNS 令牌——在 Story DAO 中，用户将使用该令牌对提案进行投票。

## 先决条件

按照上一部分，安装并运行一个 Ganache 版本。或者，如果你没有遵循第一部分，运行任何本地版本的区块链，但是确保你可以用我们需要的工具连接它。

我们假设您有一个正在运行的私有区块链，并且能够通过终端应用程序，或者在 Windows 上，通过 Git Bash、console、CMD Prompt、Powershell 等应用程序，在控制台和操作系统的终端中键入命令。

## 基本依赖关系

为了开发我们的应用程序，我们可以使用几个框架和初学者工具包之一: [Dapp](http://dapp.readthedocs.io/en/latest/) ， [eth-utils](https://github.com/ethersphere/eth-utils) ， [Populus](https://populus.readthedocs.io/en/latest/quickstart.html) ， [Embark](https://github.com/embark-framework/embark) …等等。但我们会选择目前的生态系统之王[松露](http://truffleframework.com/)。

使用以下内容安装它:

```
npm install -g truffle 
```

这将使`truffle`命令随处可用。现在我们可以用`truffle init`开始项目了。

## 启动令牌

让我们直接进入它，并建立我们的令牌。这将是一个有点标准的千篇一律的 ERC20 令牌。(在这篇帖子里你会看到哪个转折更低。)首先，我们将引入一些依赖项。OpenZeppelin 库是久经考验的高质量可靠契约，可用于扩展和构建来自。

```
npm install openzeppelin-solidity 
```

接下来，让我们创建一个新的令牌文件:

```
truffle create contract TNSToken 
```

truffle 在这里生成的默认模板有点过时，所以我们来更新一下:

```
pragma solidity ^0.4.24;

contract TNStoken {
    constructor() public {

    }
} 
```

到目前为止，令牌契约的构造函数应该被称为与契约本身相同，但是为了清楚起见，它被改为`constructor`。它还应该总是有一个修饰符，告诉编译器谁被允许部署和与这个契约交互(`public`表示每个人)。

## 萨吉马思

在这种情况下，我们将使用的唯一 Zeppelin 合同是他们的`SafeMath`合同。在 Solidity 中，我们使用关键字`import`导入契约，而编译器通常不需要完整路径，只需要相对路径，如下所示:

```
pragma solidity ^0.4.24;

import "../node_modules/openzeppelin-solidity/contracts/math/SafeMath.sol";

contract TNStoken {
    using SafeMath for uint256;
    constructor() public {

    }
} 
```

那么，`SafeMath`是什么？很久以前，因为代码中的一个数学问题，[1840 亿比特币被创造出来](https://bitfalls.com/2018/01/14/curious-case-184-billion-bitcoin/)。为了防止类似的问题(在以太坊中这是不可能的)，SafeMath 库存在了。当两个数字的大小为`MAX_INT`(即操作系统中可能的最大数字)时，将它们相加会使值“绕回”到零，就像汽车的里程表在达到 999999 公里后被重置为 0 一样。SafeMath 库有如下函数:

```
/**
* @dev Adds two numbers, throws on overflow.
*/
function add(uint256 a, uint256 b) internal pure returns (uint256 c) {
  c = a + b;
  assert(c >= a);
  return c;
} 
```

这个函数防止了这个问题:它检查两个数的和是否仍然大于两个操作数中的每一个。

虽然在撰写可靠性合同时不太容易犯这种愚蠢的错误，但安全总比后悔好。

到了`using SafeMath for uint256`，我们用这些“安全”的版本替换了 Solidity 中的标准 uint256 数字(256 位无符号——也就是仅正数——整数)。我们不是像这样对数字求和:`sum = someBigNumber + someBiggerNumber`，而是像这样对它们求和:`sum = someBigNumber.add(someBiggerNumber)`，因此在我们的计算中是安全的。

## ERC20 从零开始

随着我们的数学变得安全，我们可以创建我们的令牌。

ERC20 是一个标准，有一个定义良好的接口，所以作为参考，我们把它加入到合同中。点击阅读[令牌标准。](https://medium.freecodecamp.org/lets-talk-about-the-ethereum-token-standards-you-need-to-know-8af9fcb7e54b)

因此，ERC20 令牌应该具有的功能是:

```
pragma solidity ^0.4.24;

import "../node_modules/openzeppelin-solidity/contracts/math/SafeMath.sol";

contract ERC20 {
    function totalSupply() public view returns (uint256);
    function balanceOf(address who) public view returns (uint256);
    function transfer(address to, uint256 value) public returns (bool);
    event Transfer(address indexed from, address indexed to, uint256 value);
    function allowance(address owner, address spender) public view returns (uint256);
    function transferFrom(address from, address to, uint256 value) public returns (bool);
    function approve(address spender, uint256 value) public returns (bool);
    event Approval(address indexed owner, address indexed spender, uint256 value);
}

contract TNStoken {

    using SafeMath for uint256;

    constructor() public {

    }
} 
```

这看起来很复杂，但实际上非常简单。这是我们的令牌需要拥有的函数的“目录”,我们将一个一个地构建它们，并解释每个函数的含义。把上面的一个*接口*看作我们的令牌。当我们创建 Story DAO 应用程序时，我们将看到这是如何以及为什么有用。

### 基本余额

我们开始吧。一个**令牌**实际上只是以太坊区块链中的一个“电子表格”，就像这样:

```
| Name | Amount |
|:--|:--|
| Bruno | 4000 |
| Joe | 5000 |
| Anne | 0 |
| Mike | 300 | 
```

因此，让我们创建一个`mapping`，它本质上与合同中的电子表格完全一样:

```
mapping(address => uint256) balances; 
```

按照上面的界面，这需要附带一个`balanceOf`函数，可以读取这个表:

```
function balanceOf(address _owner) public view returns (uint256) {
    return balances[_owner];
} 
```

函数`balanceOf`接受一个参数:`_owner`是公共的(任何人都可以使用)，是一个`view`函数(意味着它可以免费使用——不需要交易),并返回一个`uint256`数，即发送的地址所有者的余额。每个人的代币余额都是公开可读的。

### 总供水量

了解令牌的总供应量对其用户和硬币跟踪应用程序来说非常重要，所以让我们定义一个契约属性(变量)来跟踪它和另一个免费函数来读取它:

```
uint256 totalSupply_;
function totalSupply() public view returns (uint256) {
    return totalSupply_;
} 
```

### 发送令牌

接下来，让我们确保一些代币的所有者可以将它们转让给其他人。我们还想知道何时发生了转移，因此我们也将定义一个转移事件。一个**传输事件**让我们通过 JavaScript 监听区块链中的传输，这样我们的应用程序就可以知道这些事件是何时发出的，而不是不断地手动检查传输是否发生。事件在契约中与变量一起声明，并用关键字`emit`发出。现在让我们在合同中加入以下内容:

```
event Transfer(address indexed from, address indexed to, uint256 value);

function transfer(address _to, uint256 _value) public returns (bool) {
    require(_to != address(0));
    require(_value <= balances[msg.sender]);

    balances[msg.sender] = balances[msg.sender].sub(_value);
    balances[_to] = balances[_to].add(_value);
    emit Transfer(msg.sender, _to, _value);
    return true;
} 
```

这个函数接受两个参数:`_to`，它是将接收令牌的目的地址，以及`value`，它是令牌的数量。需要记住的是，`value`是令牌最小单位的数量，而不是整个单位。因此，如果一个令牌声明有 10 位小数，那么为了发送一个令牌，您将发送 10000000000。这种粒度级别允许我们处理极少量的事务。

该函数是公共的，这意味着任何人都可以使用它——包括其他合同和用户——如果操作成功，它将返回`true`。

然后，该函数进行一些健全性检查。首先，它检查目的地址不是空地址。换句话说，代币不能被遗忘。接下来，它通过将余额(`balances[msg.sender]`)与为发送而传递的值进行比较，来检查发送者是否被允许发送这么多令牌。如果这些检查中的任何一项失败，该函数将拒绝交易并失败。它将退还发送的任何令牌，但在此之前执行该函数所花费的时间将会用完。

接下来的两行从发送者的余额中减去代币的数量，并将该数量添加到目的地的余额中。然后用`emit`发出事件，传入一些值:发送者、接收者和金额。现在，订阅此合同上的转移事件的任何客户都将收到此事件的通知。

好了，现在我们的代币持有者可以分发代币了。信不信由你，这就是你需要的基本令牌。但是我们将超越这一点，增加更多的功能。

### 津贴

有时，第三方可能被允许从另一个账户的余额中提款。这对于游戏来说是很有用的，它可以促进游戏内的购买，分散的交换等等。我们通过构建一个名为`allowance`的多维`mapping`来实现这一点，它存储所有这样的权限。让我们添加以下内容:

```
mapping (address => mapping (address => uint256)) internal allowed;
event Approval(address indexed owner, address indexed spender, uint256 value); 
```

该事件的存在是为了让监听的应用程序可以知道某人已经预先批准了其他人对其余额的消费——这是一个有用的功能，也是标准的一部分[。](https://github.com/ethereum/EIPs/issues/20)

该映射将地址与另一个映射相结合，后者将地址与数字相结合。它基本上形成了这样一个电子表格:

![Table of Values](img/2db1d07d0244e9edfd38fa2e2a5064f1.png)

因此 Bob 的余额可能被 Mary 花费 1000 个代币，而 Billy 花费 50 个代币。鲍勃最多可以用 750 个代币来支付玛丽的余额。比利的余额玛丽最多可以花 300 代币，乔最多可以花 1500 代币。

假设这个映射是`internal`，那么它只能被这个契约中的函数以及使用这个契约作为基础的契约使用。

要批准其他人从您的帐户中消费，您可以使用被允许消费您的代币的人的地址、他们被允许消费的金额来调用`approve`函数，并在函数中发出一个`Approval`事件:

```
function approve(address _spender, uint256 _value) public returns (bool) {
  allowed[msg.sender][_spender] = _value;
  emit Approval(msg.sender, _spender, _value);
  return true;
} 
```

我们还需要一种方法来读取一个用户可以从另一个用户的账户上花多少钱:

```
function allowance(address _owner, address _spender) public view returns (uint256) {
    return allowed[_owner][_spender];
} 
```

所以这是另一个`read only`函数(`view`)，意思是可以自由执行。它只是读取剩余的可提取余额。

那么，一个人如何去请别人呢？具有新的`transferFrom`功能:

```
function transferFrom(address _from, address _to, uint256 _value) public returns (bool) {
    require(_to != address(0));
    require(_value <= balances[_from]);
    require(_value <= allowed[_from][msg.sender]);

    balances[_from] = balances[_from].sub(_value);
    balances[_to] = balances[_to].add(_value);
    allowed[_from][msg.sender] = allowed[_from][msg.sender].sub(_value);
    emit Transfer(_from, _to, _value);
    return true;
} 
```

和以前一样，有健全性检查:目的地址不能是空地址，所以不能向黑洞发送令牌。被转移的价值不仅需要小于或等于被转移价值的账户的当前余额，还需要小于或等于消息发送者(发起该交易的地址)仍被允许为其花费的余额。

接下来，余额被更新，并且允许的余额与发出关于转移的事件之前的余额同步。

*注意:代币持有者可以在没有更新`allowed`映射的情况下使用代币。如果令牌持有者使用`transfer`手动发送令牌，就会发生这种情况。在这种情况下，持有人拥有的代币可能会少于第三方可以为其花费的限额。*

有了批准和津贴，我们还可以创建函数，让令牌持有者增加或减少某人的津贴，而不是完全覆盖值。尝试这样做作为一个练习，然后参考下面的解决方案源代码。

```
function increaseApproval(address _spender, uint _addedValue) public returns (bool) {
    allowed[msg.sender][_spender] = (
    allowed[msg.sender][_spender].add(_addedValue));
    emit Approval(msg.sender, _spender, allowed[msg.sender][_spender]);
    return true;
}

function decreaseApproval(address _spender, uint _subtractedValue) public returns (bool) {
    uint oldValue = allowed[msg.sender][_spender];
    if (_subtractedValue > oldValue) {
        allowed[msg.sender][_spender] = 0;
    } else {
        allowed[msg.sender][_spender] = oldValue.sub(_subtractedValue);
    }
    emit Approval(msg.sender, _spender, allowed[msg.sender][_spender]);
    return true;
} 
```

## 构造器

到目前为止，我们仅仅建立了一个象征性的“契约”。但是这个令牌是什么？它叫什么？它有多少位小数？我们如何使用它？

在最开始，我们定义了一个`constructor`函数。现在，让我们完成它的身体，并添加属性`name`、`symbol`和`decimals`:

```
string public name;
string public symbol;
uint8 public decimals;

constructor(string _name, string _symbol, uint8 _decimals, uint256 _totalSupply) public {
    name = _name;
    symbol = _symbol;
    decimals = _decimals;
    totalSupply_ = _totalSupply;
} 
```

这样做让我们可以在以后为相同类型的其他令牌重用该契约。但是，既然我们确切地知道我们在构建什么，让我们硬编码这些值:

```
string public name;
string public symbol;
uint8 public decimals;

constructor() public {
    name = "The Neverending Story Token;
    symbol = "TNS";
    decimals = 18;
    totalSupply_ = 100 * 10**6 * 10**18;
} 
```

当显示令牌信息时，各种以太坊工具和平台会读取这些详细信息。当契约被部署到以太网时，构造函数被自动调用，因此这些值将在部署时自动配置。

关于`totalSupply_ = 100 * 10**6 * 10**18;`的一句话:这只是让人类更容易读懂数字的一种方式。由于以太坊中的所有传输都是用最小单位的以太或令牌(包括小数)完成的，最小单位是小数点后的 18 位小数。这就是为什么单个 TNS 令牌是`1 * 10**18*`。此外，我们想要 1 亿台，所以这是`100 * 10**6`或`100*10*10*10*10*10*10`。这使得这个数字比`100000000000000000000000000`更具可读性。

## 替代发展流动

或者，我们可以扩展 Zeppelin 合同，修改一些属性，这样我们就有了令牌。大多数人都是这样做的，但是当处理可能处理他人数百万资金的软件时，我个人倾向于想知道*确切地*我在代码中放了什么，所以在我个人的情况下，盲目的代码重用是最小的。

```
pragma solidity ^0.4.24;

import "../node_modules/openzeppelin-solidity/contracts/math/SafeMath.sol";
import "../node_modules/openzeppelin-solidity/contracts/token/ERC827/ERC20Token.sol";

contract TNStoken is ERC20Token {
    using SafeMath for uint256;

    string public name;
    string public symbol;
    uint8 public decimals;
    uint256 totalSupply_;

    constructor() public {
        name = "The Neverending Story Token";
        symbol = "TNS";
        decimals = 18;
        totalSupply_ = 100 * 10**6 * 10**18;
    }
} 
```

在这种情况下，我们使用`is`符号来声明我们的令牌`is ERC20Token`。这使得我们的 token *扩展*ERC 20 契约，进而扩展 StandardToken，等等…

不管怎样，我们的令牌已经准备好了。但是，谁可以从多少代币开始，如何开始呢？

## 初始余额

让我们把所有的代币都给契约的制定者。否则，令牌将无法发送给任何人。通过在`constructor`末尾添加以下行来更新它:

```
balances[msg.sender] = totalSupply_; 
```

## 令牌锁定

鉴于我们打算使用令牌作为投票权(即，您在投票期间锁定多少令牌代表您的投票有多强大)，我们需要一种方法来防止用户在投票后发送它们，否则我们的 DAO 将容易受到 **Sybil 攻击** —一个拥有 100 万令牌的人可以注册 100 个地址，并通过将它们发送到不同的地址并用新地址重新投票来获得 1 亿令牌的投票权。因此，我们将防止转移与一个人投入到投票中的令牌一样多的令牌，这些令牌是针对每个提案的每次投票累计的。这就是我们在本文开头提到的转折。让我们将以下事件添加到合同中:

```
event Locked(address indexed owner, uint256 indexed amount); 
```

然后让我们添加锁定方法:

```
function increaseLockedAmount(address _owner, uint256 _amount) onlyOwner public returns (uint256) {
    uint256 lockingAmount = locked[_owner].add(_amount);
    require(balanceOf(_owner) >= lockingAmount, "Locking amount must not exceed balance");
    locked[_owner] = lockingAmount;
    emit Locked(_owner, lockingAmount);
    return lockingAmount;
}

function decreaseLockedAmount(address _owner, uint256 _amount) onlyOwner public returns (uint256) {
    uint256 amt = _amount;
    require(locked[_owner] > 0, "Cannot go negative. Already at 0 locked tokens.");
    if (amt > locked[_owner]) {
        amt = locked[_owner];
    }
    uint256 lockingAmount = locked[_owner].sub(amt);
    locked[_owner] = lockingAmount;
    emit Locked(_owner, lockingAmount);
    return lockingAmount;
} 
```

每个方法都确保不会锁定或解锁非法数量，然后在改变给定地址的锁定数量后发出一个事件。每个函数还返回现在为该用户锁定的新金额。尽管如此，这仍然不妨碍发送。让我们修改一下`transfer`和`transferFrom`:

```
function transfer(address _to, uint256 _value) public returns (bool) {
    require(_to != address(0));
    require(_value <= balances[msg.sender] - locked[msg.sender]); // <-- THIS LINE IS DIFFERENT
    // ...

function transferFrom(address _from, address _to, uint256 _value) public returns (bool) {
    require(_to != address(0));
    require(_value <= balances[_from] - locked[_from]);
    require(_value <= allowed[_from][msg.sender] - locked[_from]); // <-- THIS LINE IS DIFFERENT
    // ... 
```

最后，我们需要知道用户锁定或解锁了多少令牌:

```
function getLockedAmount(address _owner) view public returns (uint256) {
    return locked[_owner];
}

function getUnlockedAmount(address _owner) view public returns (uint256) {
    return balances[_owner].sub(locked[_owner]);
} 
```

就是这样:我们的令牌现在可以从外部锁定，但只能由令牌契约的所有者锁定(这将是我们将在接下来的教程中构建的 Story DAO)。让我们使令牌契约成为可拥有的——即允许它拥有一个所有者。用`import "../node_modules/openzeppelin-solidity/contracts/ownership/Ownable.sol";`导入`Ownable`合同，然后修改该行:

```
contract StoryDao { 
```

…成为这样:

```
contract StoryDao is Ownable { 
```

## 完整代码

此时，带有自定义函数注释的令牌的完整代码如下所示:

```
pragma solidity ^0.4.24;

import "../node_modules/openzeppelin-solidity/contracts/math/SafeMath.sol";
import "../node_modules/openzeppelin-solidity/contracts/ownership/Ownable.sol";

contract TNStoken is Ownable {

    using SafeMath for uint256;

    mapping(address => uint256) balances;
    mapping(address => uint256) locked;
    mapping (address => mapping (address => uint256)) internal allowed;
    uint256 totalSupply_;

    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);
    event Locked(address indexed owner, uint256 indexed amount);

    string public name;
    string public symbol;
    uint8 public decimals;

    constructor() public {
        name = "The Neverending Story Token";
        symbol = "TNS";
        decimals = 18;
        totalSupply_ = 100 * 10**6 * 10**18;
        balances[msg.sender] = totalSupply_;
    }

    /**
    @dev _owner will be prevented from sending _amount of tokens. Anything
beyond this amount will be spendable.
    */
    function increaseLockedAmount(address _owner, uint256 _amount) public onlyOwner returns (uint256) {
        uint256 lockingAmount = locked[_owner].add(_amount);
        require(balanceOf(_owner) >= lockingAmount, "Locking amount must not exceed balance");
        locked[_owner] = lockingAmount;
        emit Locked(_owner, lockingAmount);
        return lockingAmount;
    }

    /**
    @dev _owner will be allowed to send _amount of tokens again. Anything
remaining locked will still not be spendable. If the _amount is greater
than the locked amount, the locked amount is zeroed out. Cannot be neg.
    */
    function decreaseLockedAmount(address _owner, uint256 _amount) public onlyOwner returns (uint256) {
        uint256 amt = _amount;
        require(locked[_owner] > 0, "Cannot go negative. Already at 0 locked tokens.");
        if (amt > locked[_owner]) {
            amt = locked[_owner];
        }
        uint256 lockingAmount = locked[_owner].sub(amt);
        locked[_owner] = lockingAmount;
        emit Locked(_owner, lockingAmount);
        return lockingAmount;
    }

    function transfer(address _to, uint256 _value) public returns (bool) {
        require(_to != address(0));
        require(_value <= balances[msg.sender] - locked[msg.sender]);

        balances[msg.sender] = balances[msg.sender].sub(_value);
        balances[_to] = balances[_to].add(_value);
        emit Transfer(msg.sender, _to, _value);
        return true;
    }

    function approve(address _spender, uint256 _value) public returns (bool) {
        allowed[msg.sender][_spender] = _value;
        emit Approval(msg.sender, _spender, _value);
        return true;
    }

    function transferFrom(address _from, address _to, uint256 _value) public returns (bool) {
        require(_to != address(0));
        require(_value <= balances[_from] - locked[_from]);
        require(_value <= allowed[_from][msg.sender] - locked[_from]);

        balances[_from] = balances[_from].sub(_value);
        balances[_to] = balances[_to].add(_value);
        allowed[_from][msg.sender] = allowed[_from][msg.sender].sub(_value);
        emit Transfer(_from, _to, _value);
        return true;
    }

    function increaseApproval(address _spender, uint _addedValue) public returns (bool) {
        allowed[msg.sender][_spender] = (
        allowed[msg.sender][_spender].add(_addedValue));
        emit Approval(msg.sender, _spender, allowed[msg.sender][_spender]);
        return true;
    }

    function decreaseApproval(address _spender, uint _subtractedValue) public returns (bool) {
        uint oldValue = allowed[msg.sender][_spender];
        if (_subtractedValue > oldValue) {
            allowed[msg.sender][_spender] = 0;
        } else {
            allowed[msg.sender][_spender] = oldValue.sub(_subtractedValue);
        }
        emit Approval(msg.sender, _spender, allowed[msg.sender][_spender]);
        return true;
    }

    /**
    @dev Returns number of tokens the address is still prevented from using
    */
    function getLockedAmount(address _owner) public view returns (uint256) {
        return locked[_owner];
    }

    /**
    @dev Returns number of tokens the address is allowed to send
    */
    function getUnlockedAmount(address _owner) public view returns (uint256) {
        return balances[_owner].sub(locked[_owner]);
    }

    function balanceOf(address _owner) public view returns (uint256) {
        return balances[_owner];
    }

    function totalSupply() public view returns (uint256) {
        return totalSupply_;
    }

    function allowance(address _owner, address _spender) public view returns (uint256) {
        return allowed[_owner][_spender];
    }

} 
```

## 结论

这一部分帮助我们构建了一个基本的令牌，我们将使用它作为永无止境故事中的参与/共享令牌。虽然令牌具有效用，但根据其定义，它是一种控制更大机构决策的资产，即 T2 安全令牌。留意[差异](https://bitfalls.com/2018/02/20/switzerland-publishes-ico-regulations-crypto-token-classifications/)。

在本系列的下一部分，我们将学习如何编译、部署和测试这个令牌。

## 分享这篇文章