# 以太坊 DApps:跨合同沟通和代币销售

> 原文：<https://www.sitepoint.com/building-ethereum-dapps-cross-contract-communication-token-selling/>

在这个用以太坊构建 DApps 系列教程的第 4 部分中，我们开始构建和测试我们的 DAO 契约。现在让我们更进一步，根据我们的[介绍](https://www.sitepoint.com/building-ethereum-dapps-app-rules-blockchain-setup)，处理向故事添加内容和标记。

## 添加令牌

为了使一个契约能够与另一个契约交互，它需要知道另一个契约的接口——它可用的功能。因为我们的 TNS 令牌有一个相当简单的接口，所以我们可以将它包含在 DAO 的契约中，在`contract StoryDao`声明之上，在`import`语句之下:

```
contract LockableToken is Ownable {
    function totalSupply() public view returns (uint256);
    function balanceOf(address who) public view returns (uint256);
    function transfer(address to, uint256 value) public returns (bool);
    event Transfer(address indexed from, address indexed to, uint256 value);
    function allowance(address owner, address spender) public view returns (uint256);
    function transferFrom(address from, address to, uint256 value) public returns (bool);
    function approve(address spender, uint256 value) public returns (bool);
    event Approval(address indexed owner, address indexed spender, uint256 value);
    function approveAndCall(address _spender, uint256 _value, bytes _data) public payable returns (bool);
    function transferAndCall(address _to, uint256 _value, bytes _data) public payable returns (bool);
    function transferFromAndCall(address _from, address _to, uint256 _value, bytes _data) public payable returns (bool);

    function increaseLockedAmount(address _owner, uint256 _amount) public returns (uint256);
    function decreaseLockedAmount(address _owner, uint256 _amount) public returns (uint256);
    function getLockedAmount(address _owner) view public returns (uint256);
    function getUnlockedAmount(address _owner) view public returns (uint256);
} 
```

请注意，我们不需要粘贴函数的“内容”，只需要粘贴它们的签名(框架)。这就是契约之间交互所需的全部内容。

现在我们可以在 DAO 契约中使用这些函数了。计划如下:

*   启动令牌(我们已经这样做了)
*   从同一个地址启动 DAO
*   将所有令牌从令牌启动器发送到 DAO，然后将契约的所有权转移到 DAO 本身
*   此时，DAO 拥有所有的令牌，并可以使用 transfer 函数将它们出售给人们，或者可以使用 approve 函数(在投票时很有用)将它们保留下来用于消费，等等。

但是 DAO 如何知道令牌部署在哪个地址上呢？我们告诉它。

首先，我们在 DAO 契约的顶部添加一个新变量:

```
LockableToken public token; 
```

然后，我们添加一些功能:

```
constructor(address _token) public {
    require(_token != address(0), "Token address cannot be null-address");
    token = LockableToken(_token);
} 
```

构造函数是在部署契约时自动调用的函数。它对于初始化像关联契约、默认值等值很有用。在我们的例子中，我们将使用它来消费和保存 TNS 令牌的地址。`require`检查是为了确保令牌的地址是有效的。

现在，让我们添加一个函数，让用户检查 DAO 中还有多少令牌可以出售，如果出现问题，需要进行这样的更改，还可以更改为另一个令牌。这种变化也值得一个事件，所以让我们把它也加入进来。

```
event TokenAddressChange(address token);

function daoTokenBalance() public view returns (uint256) {
    return token.balanceOf(address(this));
}

function changeTokenAddress(address _token) onlyOwner public {
    require(_token != address(0), "Token address cannot be null-address");
    token = LockableToken(_token);
    emit TokenAddressChange(_token);
} 
```

第一个函数被设置为`view`，因为它不改变区块链的状态；它不会改变任何值。这意味着这是一个对区块链的免费只读函数调用:它不需要付费事务。它还将令牌的余额作为一个数字返回，因此这需要在函数的签名上用`returns (uint256)`声明。令牌有一个`balanceOf`函数(参见我们在上面粘贴的接口),它接受一个参数——要检查其余额的地址。我们正在检查我们(这个)道的余额，所以“这个”，我们把“这个”变成一个带`address()`的地址。

令牌地址更改功能允许所有者(管理员)更改令牌合同。这和构造函数的逻辑是一样的。

让我们看看如何让人们现在购买代币。

### 购买代币

按照本系列的前一部分，用户可以通过以下方式购买令牌:

*   如果已经列入白名单，则使用回退功能。换句话说，只是将以太发送到 DAO 契约。
*   通过发送超过白名单所需费用的费用来使用`whitelistAddress`功能。
*   直接调用 buyTokens 函数。

然而，有一个警告。当有人从外部调用`buyTokens`函数时，如果 DAO 中没有足够的令牌出售，我们希望它失败。但是，当有人通过白名单功能购买令牌，在第一次白名单尝试中发送过多时，我们不希望它失败，因为这样白名单过程将被取消，因为所有事情都会立即失败。以太坊中的交易是原子性的:要么一切都必须成功，要么什么都不成功。所以我们会做两个`buyTokens`函数。

```
// This goes at the top of the contract with other properties
uint256 public tokenToWeiRatio = 10000;

function buyTokensThrow(address _buyer, uint256 _wei) external {

    require(whitelist[_buyer], "Candidate must be whitelisted.");
    require(!blacklist[_buyer], "Candidate must not be blacklisted.");

    uint256 tokens = _wei * tokenToWeiRatio;
    require(daoTokenBalance() >= tokens, "DAO must have enough tokens for sale");
    token.transfer(_buyer, tokens);
}

function buyTokensInternal(address _buyer, uint256 _wei) internal {
    require(!blacklist[_buyer], "Candidate must not be blacklisted.");
    uint256 tokens = _wei * tokenToWeiRatio;
    if (daoTokenBalance() < tokens) {
        msg.sender.transfer(_wei);
    } else {
        token.transfer(_buyer, tokens);
    }
} 
```

因此，存在 1 亿个 TNS 代币。如果我们设定每个以太网 10000 个令牌的价格，那么每个令牌的价格将下降到 4-5 美分，这是可以接受的。

这些功能在对被禁止的用户和其他因素进行健全性检查后进行一些计算，并立即将代币发送给买家，买家可以在他们认为合适的时候开始使用代币——投票或在交易所出售。如果刀中的代币数量少于买家想要购买的数量，买家将获得退款。

部分`token.transfer(_buyer, tokens)`是我们使用 TNS 令牌合同发起从*当前*位置(DAO)到目的地`_buyer`的金额`tokens`的转账。

现在我们知道人们可以得到令牌，让我们看看是否可以实现提交。

## 结构和提交

根据我们的介绍文章，提交一个条目将花费故事中条目数量的 0.0001 倍。我们只需要对未删除的提交进行计数(因为提交可以被删除)，所以让我们添加所需的属性和一个方法来帮助我们。

```
uint256 public submissionZeroFee = 0.0001 ether;
uint256 public nonDeletedSubmissions = 0;

function calculateSubmissionFee() view internal returns (uint256) {
    return submissionZeroFee * nonDeletedSubmissions;
} 
```

注意:坚固性有时间和以太单位。点击了解更多信息。

这个费用只能由车主更改，只能下调。对于增加，它需要投票表决。让我们编写递减函数:

```
function lowerSubmissionFee(uint256 _fee) onlyOwner external {
    require(_fee < submissionZeroFee, "New fee must be lower than old fee.");
    submissionZeroFee = _fee;
    emit SubmissionFeeChanged(_fee);
} 
```

我们将发出一个事件来通知所有正在观察的客户端费用已经更改，所以我们来声明这个事件:

```
event SubmissionFeeChanged(uint256 newFee); 
```

提交的内容可以是最多 256 个字符的文本，同样的限制也适用于图像。只有它们的类型会改变。这是自定义结构的一个很好的用例。让我们定义一个新的数据类型。

```
struct Submission {
    bytes content;
    bool image;
    uint256 index;
    address submitter;
    bool exists;
} 
```

这就像是我们智能契约中的一个“对象类型”。该对象具有不同类型的属性。`content`是一个 [`bytes`类型值](https://medium.com/@cryptopusco/bytes-and-strings-in-solidity-f2cd4e53f388)。`image`属性是一个布尔值，表示它是否是一个图像(真/假)。`index`是与提交的普通编号相等的编号；它在所有提交列表中的索引(0，1，2，3 …)。`submitter`是提交条目的帐户的地址，而`exists`标志的存在是因为在映射中，所有键的所有值都被初始化为默认值(false ),即使键还不存在。

换句话说，当你有一个映射`address => bool`，这个映射已经将*世界上所有的*地址设置为“假”。以太坊就是这么运作的。因此，通过检查提交是否存在于某个散列中，我们会得到“是”，而提交可能根本不存在。exists 标志对此有所帮助。它让我们检查提交是否存在*和*是否存在——也就是说，提交了，而不仅仅是由 EVM 隐式添加的。此外，它使得以后“删除”条目更加容易。

注意:从技术上讲，我们也可以检查以确保提交者的地址不是零地址。

现在，让我们定义两个事件:一个用于删除条目，一个用于创建条目。

```
event SubmissionCreated(uint256 index, bytes content, bool image, address submitter);
event SubmissionDeleted(uint256 index, bytes content, bool image, address submitter); 
```

不过，有一个问题。以太坊中的映射是不可迭代的:我们不能在没有[大量黑客攻击](https://github.com/chriseth/solidity-examples/blob/master/iterable_mapping.sol)的情况下遍历它们。

为了遍历它们，我们将为这些提交创建一个标识符数组，其中数组的键将是提交的索引，而值将是我们为每个提交生成的唯一散列。Solidity 为我们提供了从任意值生成散列的`keccak256`散列算法，我们可以将它与当前块号结合使用，以确保一个条目不会在同一块中重复，并为每个条目获得某种程度的唯一性。我们这样用:`keccak256(abi.encodePacked(_content, block.number));`。我们需要`encodePacked`传递给算法的变量，因为它需要来自我们的单个参数。这就是这个函数的作用。

我们还需要在某个地方存储提交，所以让我们再定义两个契约变量。

```
mapping (bytes32 => Submission) public submissions;
bytes32[] public submissionIndex; 
```

好了，现在让我们试着构建`createSubmission`函数。

```
function createSubmission(bytes _content, bool _image) external payable {

    uint256 fee = calculateSubmissionFee();
    require(msg.value >= fee, "Fee for submitting an entry must be sufficient.");

    bytes32 hash = keccak256(abi.encodePacked(_content, block.number));
    require(!submissions[hash].exists, "Submission must not already exist in same block!");

    submissions[hash] = Submission(
        _content,
        _image,
        submissionIndex.push(hash),
        msg.sender,
        true
    );

    emit SubmissionCreated(
        submissions[hash].index,
        submissions[hash].content,
        submissions[hash].image,
        submissions[hash].submitter
    );

    nonDeletedSubmissions += 1;
} 
```

让我们一行一行地过一遍:

```
function createSubmission(bytes _content, bool _image) external payable { 
```

该函数接受字节的内容(字节是一个动态大小的字节数组，可用于存储任意数量的数据)和一个布尔标志，表示该输入是否为图像。该函数只能从外部调用，并且是可支付的，这意味着它在事务调用的同时接受以太网。

```
uint256 fee = calculateSubmissionFee();
require(msg.value >= fee, "Fee for submitting an entry must be sufficient."); 
```

接下来，我们计算提交新条目的成本，然后检查随事务传递的值是否等于或大于费用。

```
bytes32 hash = keccak256(abi.encodePacked(_content, block.number));
require(!submissions[hash].exists, "Submission must not already exist in same block!"); 
```

然后我们计算这个条目的散列值(`bytes32`是一个 32 字节的固定大小的数组，所以 32 个字符也是`keccak256`输出的长度)。我们使用这个散列来找出具有该散列的提交是否已经存在，如果存在，就取消所有操作。

```
submissions[hash] = Submission(
    _content,
    _image,
    submissionIndex.push(hash),
    msg.sender,
    true
); 
```

该部分在`submissions`映射中的散列位置创建一个新的提交。它只是通过契约中定义的新结构传递值。注意，虽然您可能已经习惯了其他语言中的`new`关键字，但在这里并不是必需的(或允许的)。然后我们发出事件(不言自明)，最后是`nonDeletedSubmissions += 1;`:这增加了下一次提交的费用(见`calculateSubmissionFee`)。

但是这里缺少了很多逻辑。我们仍然需要:

*   说明图像，以及
*   检查白名单/黑名单的存在和提交帐户上的 1 个 TNS 令牌所有权。

先做图像。我们最初的计划是每 50 篇文章只能提交一张图片。我们还需要两个契约属性:

```
uint256 public imageGapMin = 50;
uint256 public imageGap = 0; 
```

你肯定已经知道我们将如何处理这件事了吧？让我们在用`submissions[hash] = ...`创建新提交之前，将以下内容添加到我们的`createSubmission`方法中。

```
if (_image) {
    require(imageGap >= imageGapMin, "Image can only be submitted if more than {imageGapMin} texts precede it.");
    imageGap = 0;
} else {
    imageGap += 1;
} 
```

非常简单:如果条目应该是一个图像，那么首先检查图像之间的间隔是否大于 49，如果是，就将其重置为 0。否则，将差距增加一。就这样，每 50 次(或更多)提交现在可以是一个图像。

最后，让我们进行访问检查。我们可以把这个代码*放在*费用计算之前，紧接在函数的入口点之后，因为访问检查应该首先发生。

```
require(token.balanceOf(msg.sender) >= 10**token.decimals());
require(whitelist[msg.sender], "Must be whitelisted");
require(!blacklist[msg.sender], "Must not be blacklisted"); 
```

第一行检查消息发送方的令牌数是否超过令牌契约中小数位数的 10 次方(因为我们可以更改令牌地址，所以稍后可能会有另一个令牌取代我们的令牌，而那个令牌可能没有 18 位小数！).换句话说，在我们的例子中，`10**token.decimals`是`10**18`，也就是`1 000 000 000 000 000 000`，或者 1 后面跟 18 个 0。如果我们的令牌有 18 位小数，那就是`1.000000000000000000`，或者一(1)个 TNS 令牌。请注意，在分析这段代码时，您的编译器或 linter 可能会给出一些警告。这是因为令牌的`decimals`属性是公共的，所以它的 getter 函数是自动生成的(`decimals()`)，但是它没有明确地列在我们在契约顶部列出的令牌的接口中。为了解决这个问题，我们可以通过添加以下代码行来更改接口:

```
function decimals() public view returns (uint256); 
```

还有一件事:由于使用合同有所有者费用，目前设置为 1%，让我们把所有者可以提取的金额放在一边，其余的放在 DAO 中。最简单的方法是跟踪所有者可以收回多少，并在每次提交创建后增加这个数字。让我们在合同中添加一个新的属性:

```
uint256 public withdrawableByOwner = 0; 
```

然后将其添加到我们的`createSubmission`函数的末尾:

```
withdrawableByOwner += fee.div(daofee); 
```

我们可以用这样一个函数让所有者退出:

```
function withdrawToOwner() public {
    owner.transfer(withdrawableByOwner);
    withdrawableByOwner = 0;
} 
```

这将允许的数量发送给所有者，并将计数器重置为 0。如果所有者不想提取全部金额，我们可以为这种边缘情况添加另一个函数:

```
function withdrawAmountToOwner(uint256 _amount) public {
    uint256 withdraw = _amount;
    if (withdraw > withdrawableByOwner) {
        withdraw = withdrawableByOwner;
    }
    owner.transfer(withdraw);
    withdrawableByOwner = withdrawableByOwner.sub(withdraw);
} 
```

因为我们会经常通过散列来引用提交，所以让我们写一个函数来检查提交是否存在，这样我们就可以用它来替换我们的`submissions[hash].exists`检查:

```
function submissionExists(bytes32 hash) public view returns (bool) {
    return submissions[hash].exists;
} 
```

阅读提交材料的其他一些辅助功能也是必要的:

```
function getSubmission(bytes32 hash) public view returns (bytes content, bool image, address submitter) {
    return (submissions[hash].content, submissions[hash].image, submissions[hash].submitter);
}

function getAllSubmissionHashes() public view returns (bytes32[]) {
    return submissionIndex;
}

function getSubmissionCount() public view returns (uint256) {
    return submissionIndex.length;
} 
```

这是不言自明的。`getSubmission`获取提交数据，`getAllSubmissionHashes`获取系统中所有的唯一散列，`getSubmissionCount`列出总共有多少个提交(包括删除的)。我们在客户端(在 UI 中)使用这些函数的组合来获取内容。

完整的`createSubmission`函数现在看起来像这样:

```
function createSubmission(bytes _content, bool _image) storyActive external payable {

    require(token.balanceOf(msg.sender) >= 10**token.decimals());
    require(whitelist[msg.sender], "Must be whitelisted");
    require(!blacklist[msg.sender], "Must not be blacklisted");

    uint256 fee = calculateSubmissionFee();
    require(msg.value >= fee, "Fee for submitting an entry must be sufficient.");

    bytes32 hash = keccak256(abi.encodePacked(_content, block.number));
    require(!submissionExists(hash), "Submission must not already exist in same block!");

    if (_image) {
        require(imageGap >= imageGapMin, "Image can only be submitted if more than {imageGapMin} texts precede it.");
        imageGap = 0;
    } else {
        imageGap += 1;
    }

    submissions[hash] = Submission(
        _content,
        _image,
        submissionIndex.push(hash),
        msg.sender,
        true
    );

    emit SubmissionCreated(
        submissions[hash].index,
        submissions[hash].content,
        submissions[hash].image,
        submissions[hash].submitter
    );

    nonDeletedSubmissions += 1;
    withdrawableByOwner += fee.div(daofee);
} 
```

### 删除

那么删除投稿呢？这很简单:我们只需将`exists`标志切换到`false`！

```
function deleteSubmission(bytes32 hash) internal {
    require(submissionExists(hash), "Submission must exist to be deletable.");
    Submission storage sub = submissions[hash];

    sub.exists = false;
    deletions[submissions[hash].submitter] += 1;

    emit SubmissionDeleted(
        sub.index,
        sub.content,
        sub.image,
        sub.submitter
    );

    nonDeletedSubmissions -= 1;
} 
```

首先，我们确保提交存在并且没有被删除；然后我们从存储器中取回它。接下来，我们将它的`exists`标志设置为 false，将 DAO 中该地址的删除次数增加 1(这在跟踪用户后来删除了多少条目时很有用；这可能会导致黑名单！)，我们发出一个删除事件。

最后，我们通过减少系统中未删除提交的数量来减少新提交的创建费用。让我们不要忘记在我们的契约中添加一个新的属性——一个跟踪这些删除的属性:

```
mapping (address => uint256) public deletions; 
```

## 部署变得更加复杂

既然我们在另一个契约中使用了令牌，我们需要更新部署脚本(`3_deploy_storydao`)来将令牌的地址传递给 StoryDao 的构造函数，如下所示:

```
var Migrations = artifacts.require("./Migrations.sol");
var StoryDao = artifacts.require("./StoryDao.sol");
var TNSToken = artifacts.require("./TNSToken.sol");

module.exports = function(deployer, network, accounts) {
  if (network == "development") {
    deployer.deploy(StoryDao, TNSToken.address, {from: accounts[0]});
  } else {
    deployer.deploy(StoryDao, TNSToken.address);
  }
}; 
```

点击阅读关于配置部署[的更多信息。](https://truffleframework.com/docs/getting_started/migrations)

## 结论

在这一部分中，我们添加了参与者从我们的 DAO 购买令牌以及向故事中添加提交内容的功能。DAO 契约的另一部分仍然存在:投票和民主化。这是我们将在下一篇文章中处理的问题。

## 分享这篇文章