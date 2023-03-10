# 以太坊 DApps:为 DAO 契约构建 Web3 UI

> 原文：<https://www.sitepoint.com/building-ethereum-dapps-web3-ui-dao-contract/>

在这个关于用以太坊构建 DApps 的系列教程的第 6 部分中，我们通过添加投票、黑名单/取消黑名单、红利分配和撤销来完善 DAO，同时加入了一些额外的辅助函数。在本教程中，我们将建立一个网络界面来与我们的故事互动，否则我们不能指望任何用户参与。所以这是我们的故事的最后一部分，在我们把它放到野外之前。

由于这不是一个 web 应用程序教程，我们将保持事情非常简单。下面的代码还不能用于生产，它只是作为如何将 JavaScript 连接到区块链的概念证明。但是首先，让我们添加一个新的迁移。

## 自动转账

现在，当我们部署令牌和 DAO 时，它们位于区块链上，但不进行交互。为了测试我们构建的东西，我们需要手动将令牌所有权和余额转移到 DAO，这在测试过程中可能会很繁琐。

让我们编写一个新的迁移来完成这个任务。创建文件`4_configure_relationship.js`并将以下内容放入其中:

```
var Migrations = artifacts.require("./Migrations.sol");
var StoryDao = artifacts.require("./StoryDao.sol");
var TNSToken = artifacts.require("./TNSToken.sol");

var storyInstance, tokenInstance;

module.exports = function (deployer, network, accounts) {

    deployer.then(function () {
            return TNSToken.deployed();
        }).then(function (tIns) {
            tokenInstance = tIns;
            return StoryDao.deployed();
        }).then(function (sIns) {
            storyInstance = sIns;
            return balance = tokenInstance.totalSupply();
        }).then(function (bal) {
            return tokenInstance.transfer(storyInstance.address, bal);
        })
        .then(function (something) {
            return tokenInstance.transferOwnership(storyInstance.address);
        });
} 
```

下面是这段代码的作用。首先，你会注意到它是基于承诺的。里面全是`then`来电。这是因为在调用下一个函数之前，我们依赖于返回一些数据的函数。所有契约调用都是基于承诺的，这意味着它们不会立即返回数据，因为 Truffle 需要向节点请求信息，所以会做出在未来某个时间返回数据的*承诺*。我们通过使用`then`并为所有的`then`调用提供回调函数来强制代码等待这个数据，当这个结果最终给出时，回调函数就会被调用。

所以，按顺序:

*   首先，向节点请求已部署令牌的地址并返回它
*   然后，接受这些数据，将其保存到一个全局变量中，并请求所部署的 DAO 的地址并返回它
*   然后，接受该数据，将其保存到一个全局变量中，并请求 token contract 的所有者在其帐户中的余额，从技术上讲，这是总供应量，并返回该数据
*   然后，一旦获得了这个余额，就用它来调用这个令牌的`transfer`函数，并将令牌发送到 DAO 的地址并返回结果
*   然后，忽略返回的结果——我们只是想知道它是什么时候完成的——最后将令牌的所有权转移到 DAO 的地址，返回数据但不丢弃它。

运行`truffle migrate --reset`现在应该会产生如下输出:

![Truffle migrate](img/6454a7d1efa0ffd7f704b41563da4c94.png)

## 前端

前端是一个常规的、静态的 HTML 页面，其中包含一些用于与区块链通信的 JavaScript 和一些 CSS 以使事情不那么难看。

让我们在子文件夹`public`中创建一个文件`index.html`，并赋予它以下内容:

```
<!DOCTYPE HTML>

<html lang="en">
<head>
    <title>The Neverending Story</title>
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1"/>
    <meta name="description" content="The Neverending Story is an community curated and moderated Ethereum dapp-story">
    <link rel="stylesheet" href="assets/css/main.css"/>
</head>
<body>

    <div class="grid-container">
        <div class="header container">
            <h1>The Neverending Story</h1>
            <p>A story on the Ethereum blockchain, community curated and moderated through a Decentralized Autonomous Organization (DAO)</p>
        </div>
        <div class="content container">
            <div class="intro">
                <h3>Chapter 0</h3>
                <p class="intro">It's a rainy night in central London.</p>
            </div>
            <hr>
            <div class="content-submissions">
                <div class="submission">
                    <div class="submission-body">This is an example submission. A proposal for its deletion has been submitted.</div>
                    <div class="submission-submitter">0xbE2B28F870336B4eAA0aCc73cE02757fcC428dC9</div>
                    <div class="submission-actions">
                        <div class="deletionproposed" data-votes="3024" data-deadline="1531607200"></div>
                    </div>
                </div>
                <div class="submission">
                        <div class="submission-body">This is a long submission. It has over 244 characters, just we can see what it looks like when rendered in the UI. We need to make sure it doesn't break anything and the layout also needs to be maintained, not clashing with actions/buttons etc.</div>
                        <div class="submission-submitter">0xbE2B28F870336B4eAA0aCc73cE02757fcC428dC9</div>
                        <div class="submission-actions">
                            <div class="delete"></div>
                        </div>
                </div>
                <div class="submission">
                        <div class="submission-body">This is an example submission. A proposal for its deletion has been submitted but is looking like it'll be rejected.</div>
                        <div class="submission-submitter">0xbE2B28F870336B4eAA0aCc73cE02757fcC428dC9</div>
                        <div class="submission-actions">
                            <div class="deletionproposed" data-votes="-790024" data-deadline="1531607200"></div>
                        </div>
                </div>
            </div>    
        </div>
        <div class="events container">
            <h3>Latest Events</h3>
            <ul class="eventlist">

            </ul>
        </div>
        <div class="information container">
            <p>Logged in / out</p>
            <div class="avatar">
                <img src="http://placeholder.pics/svg/200/DEDEDE/555555/avatar" alt="avatar">
            </div>
            <dl>
                <dt>Contributions</dt>
                <dd>0</dd>
                <dt>Deletions</dt>
                <dd>0</dd>
                <dt>Tokens</dt>
                <dd>0</dd>
                <dt>Proposals submitted</dt>
                <dd>0</dd>
                <dt>Proposals voted on</dt>
                <dd>0</dd>
            </dl>
        </div>
    </div>

<script src="assets/js/web3.min.js"></script>
<script src="assets/js/app.js"></script>
<script src="assets/js/main.js"></script>

</body>
</html> 
```

注意:这是一个非常非常基本的框架，只是为了演示集成。请不要指望这是最终产品！

您可能在`web3`文件夹中遗漏了`dist`文件夹。该软件仍处于测试阶段，因此仍有可能出现小失误。要解决这个问题，用`dist`文件夹安装 web3，运行`npm install ethereum/web3.js --save`。

对于 CSS，让我们将一些基本的东西放入`public/assets/css/main.css`:

```
@supports (grid-area: auto) {
    .grid-container{
      display: grid;
      grid-template-columns: 6fr 5fr 4fr;
      grid-template-rows: 10rem ;
      grid-column-gap: 0.5rem;
      grid-row-gap: 0.5rem;
      justify-items: stretch;
      align-items: stretch;
      grid-template-areas:
      "header header information"
      "content events information";
      height: 100vh;
    }
    .events {
      grid-area: events;
    }
    .content {
      grid-area: content;
    }
    .information {
      grid-area: information;
    }
    .header {
      grid-area: header;
      text-align: center;
    }

    .container {
        border: 1px solid black;
        padding: 15px;
        overflow-y: scroll;
    }

    p {
        margin: 0;
    }
  }

body {
    padding: 0;
    margin: 0;
    font-family: sans-serif;
} 
```

那么作为 JS，我们将从`public/assets/js/app.js`开始:

```
var Web3 = require('web3');

var web3 = new Web3(web3.currentProvider);
console.log(web3); 
```

这是怎么回事？

因为我们同意假设我们的所有用户都将安装[元掩码](https://bitfalls.com/2018/02/16/metamask-send-receive-ether/)，并且元掩码将自己的 Web3 实例注入到任何被访问网页的 DOM 中，所以我们基本上可以在我们的网站中从元掩码访问“钱包提供者”。事实上，如果我们在页面打开时登录 MetaMask，我们将在控制台中看到以下内容:

![MetaMask provider is active](img/8d0e358985e4b4dd4629fd82ce7ba289.png)

注意 MetamaskInpageProvider 是如何活动的。事实上，如果我们在控制台中键入`web3.eth.accounts`，我们通过 MetaMask 访问的所有帐户都会被打印出来:

![Account is printed into the console](img/622659f57e859db304c4072712d210a3.png)

然而，这个特殊的帐户是默认添加到我个人的元掩码中的，因此它的余额为 0 eth。这不是我们跑步 Ganache 或 PoA 系列的一部分:

![An empty account](img/3191974869172db038e343c0a3ad0579.png)

请注意，查询我们的活动(元掩码)帐户的余额会得到 0，而查询我们的一个私人区块链帐户的余额会得到 100 以太(在我的例子中是 Ganache，所以所有帐户都用 100 以太初始化)。

### 关于语法

您会注意到这些调用的语法看起来有点奇怪:

```
web3.eth.getBalance("0x35d4dCDdB728CeBF80F748be65bf84C776B0Fbaf", function(err, res){console.log(JSON.stringify(res));}); 
```

为了读取区块链数据，大多数元掩码用户不会在本地运行节点，而是从 Infura 或另一个远程节点请求。正因为如此，我们几乎可以指望滞后。为此，[同步方法一般不支持](https://github.com/MetaMask/faq/blob/master/DEVELOPERS.md#dizzy-all-async---think-of-metamask-as-a-light-client)。相反，一切都是通过承诺或回调来完成的——就像本文开头的部署步骤一样。这是否意味着你需要非常熟悉为以太坊开发 JS 的承诺？不。意思是这样的。当在 DOM 中进行 JS 调用时…

*   总是提供一个回调函数作为你正在调用的函数的最后一个参数
*   假设它的返回值有两个:第一个是`error`，然后是`result`。

所以，基本上，就从延迟反应的角度来考虑吧。当节点返回数据时，JavaScript 将调用您定义为回调函数的函数。是的，这确实意味着你不能期望你的代码像写的那样一行一行地执行！

想了解更多关于承诺、回拨和所有这些异步爵士乐的信息，请看[这篇文章](https://www.sitepoint.com/overview-javascript-promises/)。

## 账户信息

如果我们打开如上所示的网站框架，我们会看到类似这样的内容:

![The website skeleton](img/7d4e97270c549989253fdccbab216104.png)

让我们用*真实的*数据填充关于账户信息的最右边一列。

### 会议

当用户未登录到他们的元掩码扩展时，帐户列表将为空。当元掩码甚至没有安装时，提供程序将是空的(未定义)。当他们登录 MetaMask 时，提供者将可用，并提供帐户信息和与连接的以太坊节点的交互(live 或 Ganache 或其他)。

*提示:测试时，你可以点击右上角的头像图标，然后选择退出，退出 MetaMask。如果用户界面看起来不像下面的截图，你可能需要通过打开菜单并点击“试用”来激活测试版用户界面。*

![MetaMask Log out](img/8f4150cd939fb90874a4f944b6fb516f.png)

首先，让我们将右侧状态栏的所有内容替换为用户注销后的消息:

```
<div class="information container">
    <div class="logged out">
        <p>You seem to be logged out of MetaMask or MetaMask isn't installed. Please log into MetaMask - to learn more,
            see
            <a href="https://bitfalls.com/2018/02/16/metamask-send-receive-ether/">this tutorial</a>.</p>
    </div>
    <div class="logged in" style="display: none">
        <p>You are logged in!</p>
    </div>
</div> 
```

处理这个的 JS 看起来是这样的(在`public/assets/js/main.js`):

```
var loggedIn;

(function () {

    loggedIn = setLoggedIn(web3.currentProvider !== undefined && web3.eth.accounts.length > 0);

})();

function setLoggedIn(isLoggedIn) {
    let loggedInEl = document.querySelector('div.logged.in');
    let loggedOutEl = document.querySelector('div.logged.out');

    if (isLoggedIn) {
        loggedInEl.style.display = "block";
        loggedOutEl.style.display = "none";
    } else {
        loggedInEl.style.display = "none";
        loggedOutEl.style.display = "block";
    }

    return isLoggedIn;
} 
```

第一部分— `(function () {` —包装网站加载后要执行的逻辑。所以当页面准备好的时候，里面的任何东西都会被立即执行。调用一个函数`setLoggedIn`，并向其传递一个条件。条件是:

1.  设置了`web3`对象的 currentProvider(即网站中存在一个 web3 客户端)。
2.  有非零数量的帐户可用，即一个帐户可通过该 web3 提供商使用。换句话说，我们至少登录了一个账户。

如果这些条件一起评估为`true`，则`setLoggedIn`功能使“注销”消息不可见，而“登录”消息可见。

所有这些都有一个额外的好处，那就是也可以使用任何其他的 web3 提供商。如果元掩码替代最终出现，它将立即与这段代码兼容，因为我们并不明确期望在任何地方出现元掩码。

### 帐户头像

因为以太坊钱包的每个私钥都是唯一的，所以它可以用来生成唯一的图像。这就是你在 MetaMask 的右上角或使用 MyEtherWallet 时看到的彩色头像的来源，尽管 Mist、MyEtherWallet 和 MetaMask 都使用不同的方法。让我们为登录的用户生成一个并显示它。

[迷雾](https://www.sitepoint.com/mist-introduction/)中的图标是由 Blockies 库生成的——但这是一个定制的，因为原始版本有一个损坏的随机数生成器，可以为不同的密钥生成相同的图像。所以要安装这个，下载[这个文件](https://github.com/ethereum/blockies/blob/master/blockies.min.js)到你的`assets/js`文件夹中。然后，在`index.html`中，我们将它放在`main.js`之前:

```
 <script src="assets/js/app.js"></script>
    <script src="assets/js/blockies.min.js"></script>
    <script src="assets/js/main.js"></script>

</body> 
```

我们还应该升级`logged.in`容器:

```
<div class="logged in" style="display: none">
    <p>You are logged in!</p>
    <div class="avatar">

    </div>
</div> 
```

在`main.js`中，我们启动该功能。

```
 if (isLoggedIn) {
      loggedInEl.style.display = "block";
      loggedOutEl.style.display = "none";

      var icon = blockies.create({ // All options are optional
          seed: web3.eth.accounts[0], // seed used to generate icon data, default: random
          size: 20, // width/height of the icon in blocks, default: 8
          scale: 8, // width/height of each block in pixels, default: 4
      });

      document.querySelector("div.avatar").appendChild(icon); 
```

所以我们升级 JS 代码的登录部分，生成图标并粘贴到 avatar 部分。在渲染之前，我们应该将它与 CSS 稍微对齐:

```
div.avatar {
    width: 100%;
    text-align: center;
    margin: 10px 0;
} 
```

现在，如果我们在登录 MetaMask 时刷新页面，我们应该会看到生成的头像图标。

![The Avatar Icon](img/1eb340ee195d486daccd927de47a5ffc.png)

### 账户余额

现在让我们输出一些账户余额信息。

我们有一堆只读函数供我们使用，这些函数是我们专门为此开发的。因此，让我们查询区块链，向它询问一些信息。为此，我们需要通过以下步骤[调用智能契约函数](https://bitfalls.com/2018/04/08/how-to-call-ethereum-smart-contract-functions/)。

#### 1.ABI

获取我们正在调用的函数的合同的 ABI。ABI 包含函数签名，所以我们的 JS 代码知道如何调用它们。点击了解更多关于 ABI 的信息。

编译后打开项目文件夹中的`build/TNSToken.json`和`build/StoryDao.json`文件，只选择*`abi`部分即可获得 TNS 令牌的 ABI 和 StoryDAO 所以方括号`[`和`]`之间的部分:*

 *![ABI selection](img/73b25722554adb4ac9d78ba321a50c62.png)

我们将把这个 ABI 放在 JavaScript 代码的顶部，放入`main.js`中，如下所示:

![Token and DAO ABI loaded](img/cadd59bbe6d6a271b1fce9546588b02d.png)

*请注意，上面的截图显示了由我的代码编辑器(Microsoft Visual Code)折叠的缩写插入。如果您查看行号，您会注意到令牌的 ABI 是 400 行代码，而 DAO 的 ABI 是另外 1000 行，因此将其粘贴到本文中是没有意义的。*

#### 2.实例化令牌

```
if (loggedIn) {

    var token = TNSToken.at('0x3134bcded93e810e1025ee814e87eff252cff422');
    var story = StoryDao.at('0x729400828808bc907f68d9ffdeb317c23d2034d5');
    token.balanceOf(web3.eth.accounts[0], function(error, result) {console.log(JSON.stringify(result))});
    story.getSubmissionCount(function(error, result) {console.log(JSON.stringify(result))});
//... 
```

我们用 Truffle 给我们的地址调用每个契约，并分别为每个契约创建一个实例— `token`和`story`。然后，我们简单地调用函数(像以前一样异步)。控制台给我们两个零，因为 MetaMask 中的帐户有 0 个令牌，并且因为故事中目前有 0 个提交。

![Console produces two zeroes](img/36a2e2973bded226221d47084e947c80.png)

#### 3.读取和输出数据

最后，我们可以用现有的信息填充用户的个人资料数据。

让我们更新我们的 JavaScript:

```
var loggedIn;

(function () {

    loggedIn = setLoggedIn(web3.currentProvider !== undefined && web3.eth.accounts.length > 0);

    if (loggedIn) {

        var token = TNSToken.at('0x3134bcded93e810e1025ee814e87eff252cff422');
        var story = StoryDao.at('0x729400828808bc907f68d9ffdeb317c23d2034d5');

        token.balanceOf(web3.eth.accounts[0], function(error, result) {console.log(JSON.stringify(result))});
        story.getSubmissionCount(function(error, result) {console.log(JSON.stringify(result))});

        readUserStats().then(User => renderUserInfo(User));
    }

})();

async function readUserStats(address) {
    if (address === undefined) {
        address = web3.eth.accounts[0];
    }
    var User = {
        numberOfSubmissions: await getSubmissionsCountForUser(address),
        numberOfDeletions: await getDeletionsCountForUser(address),
        isWhitelisted: await isWhitelisted(address),
        isBlacklisted: await isBlacklisted(address),
        numberOfProposals: await getProposalCountForUser(address),
        numberOfVotes: await getVotesCountForUser(address)
    }
    return User;
}

function renderUserInfo(User) {
    console.log(User);

    document.querySelector('#user_submissions').innerHTML = User.numberOfSubmissions;
    document.querySelector('#user_deletions').innerHTML = User.numberOfDeletions;
    document.querySelector('#user_proposals').innerHTML = User.numberOfProposals;
    document.querySelector('#user_votes').innerHTML = User.numberOfVotes;
    document.querySelector('dd.user_blacklisted').style.display = User.isBlacklisted ? 'inline-block' : 'none';
    document.querySelector('dt.user_blacklisted').style.display = User.isBlacklisted ? 'inline-block' : 'none';
    document.querySelector('dt.user_whitelisted').style.display = User.isWhitelisted ? 'inline-block' : 'none';
    document.querySelector('dd.user_whitelisted').style.display = User.isWhitelisted ? 'inline-block' : 'none';
}

async function getSubmissionsCountForUser(address) {
    if (address === undefined) {
        address = web3.eth.accounts[0];
    }
    return new Promise(function (resolve, reject) {
        resolve(0);
    });
}
async function getDeletionsCountForUser(address) {
    if (address === undefined) {
        address = web3.eth.accounts[0];
    }
    return new Promise(function (resolve, reject) {
        resolve(0);
    });
}
async function getProposalCountForUser(address) {
    if (address === undefined) {
        address = web3.eth.accounts[0];
    }
    return new Promise(function (resolve, reject) {
        resolve(0);
    });
}
async function getVotesCountForUser(address) {
    if (address === undefined) {
        address = web3.eth.accounts[0];
    }
    return new Promise(function (resolve, reject) {
        resolve(0);
    });
}
async function isWhitelisted(address) {
    if (address === undefined) {
        address = web3.eth.accounts[0];
    }
    return new Promise(function (resolve, reject) {
        resolve(false);
    });
}
async function isBlacklisted(address) {
    if (address === undefined) {
        address = web3.eth.accounts[0];
    }
    return new Promise(function (resolve, reject) {
        resolve(false);
    });
} 
```

让我们更改个人资料信息部分:

```
<div class="logged in" style="display: none">
    <p>You are logged in!</p>
    <div class="avatar">

    </div>
    <dl>
        <dt>Submissions</dt>
        <dd id="user_submissions"></dd>
        <dt>Proposals</dt>
        <dd id="user_proposals"></dd>
        <dt>Votes</dt>
        <dd id="user_votes"></dd>
        <dt>Deletions</dt>
        <dd id="user_deletions"></dd>
        <dt class="user_whitelisted">Whitelisted</dt>
        <dd class="user_whitelisted">Yes</dd>
        <dt class="user_blacklisted">Blacklisted</dt>
        <dd class="user_blacklisted">Yes</dd>
    </dl>
</div> 
```

您会注意到我们在获取数据时使用了承诺，尽管我们的函数目前只是模拟函数:它们立即返回平面数据。这是因为这些函数中的每一个都需要不同的时间来获取我们要求它获取的数据，所以我们将在填充用户对象之前等待它们的完成，然后将它传递给更新屏幕信息的渲染函数。

如果你对 JS 承诺不熟悉，想了解更多，请看[这篇文章](https://medium.freecodecamp.org/javascript-from-callbacks-to-async-await-1cc090ddad99)。

目前，我们所有的功能都是模拟的；在有东西读之前，我们需要写一些东西。但是首先，我们需要做好准备，注意那些正在发生的写操作！

### 收听事件

为了能够跟踪契约发出的事件，我们需要监听它们——否则我们就把所有那些`emit`语句白白放进了代码中。我们构建的模拟 UI 的中间部分就是用来容纳这些事件的。

下面是我们如何收听区块链发出的事件:

```
// Events

var WhitelistedEvent = story.Whitelisted(function(error, result) {
    if (!error) {
        console.log(result);
    }
}); 
```

这里我们调用 StoryDao 契约的`story`实例上的`Whitelisted`函数，并向其中传递一个回调。每当触发此给定事件时，都会自动调用此回调。因此，当用户被列入白名单时，代码会自动将该事件的输出记录到控制台。

然而，这将仅获得由网络挖掘的最后块的最后事件。因此，如果从块 1 到 10 触发了几个白名单事件，它将只向我们显示块 10 中的事件(如果有)。更好的方法是使用这种方法:

```
story.Whitelisted({}, { fromBlock: 0, toBlock: 'latest' }).get((error, eventResult) => {
  if (error) {
    console.log('Error in myEvent event handler: ' + error);
  } else {  
    // eventResult contains list of events!
    console.log('Event: ' + JSON.stringify(eventResult[0].args));
  }
}); 
```

注意:将上述内容放入 JS 文件底部的一个单独的部分，一个专门用于事件的部分。

这里，我们使用`get`函数，它让我们定义从中获取事件的块范围。我们使用 0 到 latest，这意味着我们可以获取所有这种类型的事件。但是这也带来了与上面的观察方法相冲突的问题。watching 方法输出最后一个块的事件，`get`方法输出全部。我们需要一种方法让 JS 忽略双重事件。不要写那些你已经从历史中获取的东西。我们将进一步深入探讨这一点，但现在，让我们先来处理白名单。

### 帐户白名单

最后，让我们来看一些写操作。

第一个也是最简单的一个是加入白名单。请记住，要获得白名单，一个帐户需要向 DAO 的地址发送至少 0.01 以太网。你会在部署时得到这个地址。如果您的 Ganache/PoA 链在本课程的两个部分之间重启，没关系，只需用`truffle migrate --reset`重新运行迁移，您将获得令牌和 DAO 的新地址。在我这里，刀的地址是`0x729400828808bc907f68d9ffdeb317c23d2034d5`，我的令牌在`0x3134bcded93e810e1025ee814e87eff252cff422`。

上面的一切都设置好了，让我们试着发送一些以太网到 DAO 地址。让我们用 0.05 ether 来尝试一下，只是为了好玩，这样我们可以看看 DAO 是否也为我们提供了额外计算的令牌，用于超额支付。

*注意:不要忘记定制气体量——只需使用标有红色的图标在 21000 的限制上再加一个零。为什么这是必要的？因为由简单以太网发送触发的函数(回退函数)执行超过 21000 的额外逻辑，这对于简单发送来说已经足够了。所以我们需要提高限额。不要担心:超过这个限额的任何东西都会立即退还。要了解天然气的工作原理，请看[这里](https://www.sitepoint.com/ethereum-transaction-costs)。*

![Sending ether to the DAO](img/86f034879872c020788f1b1c4e187bf1.png)

![Customizing Gas Amount](img/eebd9463785b473500dd3daab89de333.png)

交易确认后(您会在 MetaMask 中看到“已确认”)，我们可以检查 MetaMask 帐户中的代币金额。我们需要首先将我们的自定义令牌添加到元掩码中，以便它可以跟踪它们。根据下面的动画，流程如下:选择 MetaMask 菜单，向下滚动到*添加令牌*，选择*自定义令牌*，粘贴迁移时松露给你的令牌地址，点击*下一个*，看看余额是否没问题，然后选择*添加令牌*。

![Token adding animation](img/84d69b2cea327eff21642e4640648fab.png)

对于 0.05 eth，我们应该有 40 万个令牌，我们确实有。

![Customizing Gas Amount](img/574590027c5ba919bd92e233816d7776.png)

但是，这个活动呢？我们被告知这个白名单了吗？让我们看看控制台。

![Event in console](img/c9d429d81f1c1c8fffe9c3aa6d92847c.png)

事实上，完整的数据集就在那里——发出事件的地址、从中挖掘的块号和散列等等。所有这些中有一个是`args`对象，它告诉我们事件数据:addr 是被列入白名单的地址，status 是它被添加到白名单还是从白名单中删除。成功！

如果我们现在刷新页面，事件将再次出现在控制台中。但是怎么做呢？我们没有将任何新人加入白名单。事件为什么会火？EVM 的事件不像 JavaScript 那样是一次性的。当然，它们包含任意数据，只作为输出，但是它们的输出将永远注册在区块链中，因为导致它们的事务也将永远注册在区块链中。因此，事件在发出后仍然存在，这使我们不必将它们存储在某个地方，并在页面刷新时调用它们！

现在让我们将它添加到 UI 中的事件屏幕上！编辑 JavaScript 文件的事件部分，如下所示:

```
// Events

var highestBlock = 0;
var WhitelistedEvent = story.Whitelisted({}, { fromBlock: 0, toBlock: "latest" });

WhitelistedEvent.get((error, eventResult) => {
  if (error) {
    console.log('Error in Whitelisted event handler: ' + error);
  } else {  
    console.log(eventResult);
    let len = eventResult.length;
    for (let i = 0; i < len; i++) {
      console.log(eventResult[i]);
      highestBlock = highestBlock < eventResult[i].blockNumber ? eventResult[i].blockNumber : highestBlock;
      printEvent("Whitelisted", eventResult[i]);
    }
  }
});

WhitelistedEvent.watch(function(error, result) {
  if (!error && result.blockNumber > highestBlock) {
    printEvent("Whitelisted", result);
  }
});

function printEvent(type, object) {
  switch (type) {
    case "Whitelisted":
      let el;
      if (object.args.status === true) {
          el = "<li>Whitelisted address "+ object.args.addr +"</li>";
      } else {
          el = "<li>Removed address "+ object.args.addr +" from whitelist!</li>";
      }
      document.querySelector("ul.eventlist").innerHTML += el;
    break;
    default:
    break;
  }
} 
```

哇，事情变得很复杂，是吧？别担心，我们会澄清的。

`highestBlock`变量将记住从历史中获取的最新块。我们创建了事件的一个实例，并为其附加了两个侦听器。一个是`get`，从历史中获取所有事件，记住最新的区块。另一个是`watch`，它“实时”观察事件，并在新事件出现在最近的街区时触发。只有当刚进来的块大于我们记忆中最高的块时，观察器才会触发，确保只有新的事件被追加到事件列表中。

我们还增加了一个`printEvent`函数，让事情变得更简单；我们也可以在其他事件类型中重复使用它！

如果我们现在测试这个，我们可以很好的打印出来。

![Event is on screen](img/f0636de15be35991e725eeabb61384a3.png)

对于我们的故事可能引发的所有其他事件，现在尝试这样做吧！看看您是否能找出如何一次性处理它们，而不是为它们每个都编写这个逻辑。(提示:在数组中定义它们的名称，然后遍历这些名称并动态注册事件！)

#### 人工检查

还可以通过在 MyEtherWallet 中打开 StoryDAO 并调用其`whitelist`函数来手动检查它的白名单和所有其他公共参数。

![Whitelist inspection](img/1c3f235b4d0334119c1e675ed20f6b30.png)

您会注意到，如果我们检查刚刚发送白名单数量的帐户，我们将得到一个`true`返回，表明这个帐户确实存在于`whitelist`映射中。

在将其他功能添加到 web UI 之前，使用相同的功能菜单来试验这些功能。

### 提交参赛作品

最后，让我们从我们的 UI 进行一次适当的写函数调用。这一次，我们将提交一个条目到我们的故事中。首先，我们需要清除开始时放在那里的示例条目。将 HTML 编辑为:

```
<div class="content container">
    <div class="intro">
        <h3>Chapter 0</h3>
        <p class="intro">It's a rainy night in central London.</p>
    </div>
    <hr>
    <div class="submission_input">
        <textarea name="submission-body" id="submission-body-input" rows="5"></textarea>
        <button id="submission-body-btn">Submit</button>
    </div>
    ... 
```

和一些基本的 CSS:

```
.submission_input textarea {
  width: 100%;
} 
```

我们添加了一个非常简单的文本区域，用户可以通过它提交新条目。

现在做 JS 部分吧。

首先，让我们通过添加一个新事件并修改我们的`printEvent`函数来准备接受这个事件。我们还可以对整个事件部分进行一点重构，使其更加可重用。

```
// Events

var highestBlock = 0;
var WhitelistedEvent = story.Whitelisted({}, { fromBlock: 0, toBlock: "latest" });
var SubmissionCreatedEvent = story.SubmissionCreated({}, { fromBlock: 0, toBlock: "latest" });

var events = [WhitelistedEvent, SubmissionCreatedEvent];
for (let i = 0; i < events.length; i++) {
  events[i].get(historyCallback);
  events[i].watch(watchCallback);
}

function watchCallback(error, result) {
  if (!error && result.blockNumber > highestBlock) {
    printEvent(result.event, result);
  }
}

function historyCallback(error, eventResult) {
  if (error) {
    console.log('Error in event handler: ' + error);
  } else {  
    console.log(eventResult);
    let len = eventResult.length;
    for (let i = 0; i < len; i++) {
      console.log(eventResult[i]);
      highestBlock = highestBlock < eventResult[i].blockNumber ? eventResult[i].blockNumber : highestBlock;
      printEvent(eventResult[i].event, eventResult[i]);
    }
  }
}

function printEvent(type, object) {
  let el;
  switch (type) {
    case "Whitelisted":
      if (object.args.status === true) {
          el = "<li>Whitelisted address "+ object.args.addr +"</li>";
      } else {
          el = "<li>Removed address "+ object.args.addr +" from whitelist!</li>";
      }
      document.querySelector("ul.eventlist").innerHTML += el;
    break;
    case "SubmissionCreated":
      el = "<li>User " + object.args.submitter + " created a"+ ((object.args.image) ? "n image" : " text") +" entry: #" + object.args.index + " of content " + object.args.content+"</li>";
      document.querySelector("ul.eventlist").innerHTML += el;
    break;
    default:
    break;
  }
} 
```

现在我们需要做的就是实例化一个新事件，然后为它定义一个`case`。

接下来，让我们使提交成为可能。

```
document.getElementById("submission-body-btn").addEventListener("click", function(e) {
    if (!loggedIn) {
        return false;
    }

    var text = document.getElementById("submission-body-input").value;
    text = web3.toHex(text);

    story.createSubmission(text, false, {value: 0, gas: 400000}, function(error, result) {
        refreshSubmissions();
    });
});

function refreshSubmissions() {
    story.getAllSubmissionHashes(function(error, result){
        console.log(result);
    });
} 
```

这里，我们向提交表单添加了一个事件监听器，一旦提交，如果用户没有登录，它首先拒绝所有内容，然后获取内容并将其转换为十六进制格式(这是我们需要将值存储为`bytes`)。

最后，它通过调用`createSubmission`函数并提供两个参数来创建一个事务:条目的文本和`false`标志(意思是，不是图像)。第三个参数是事务设置:value 表示发送多少乙醚，gas 表示您希望默认多少汽油限制。这可以在客户端(MetaMask)中手动更改，但这是一个很好的起点，可以确保我们不会遇到限制。最后一个参数是回调函数，我们现在已经习惯了，这个回调函数将调用一个刷新函数来加载故事的所有提交。目前，这个刷新功能只加载故事散列，并把它们放入控制台，这样我们就可以检查一切工作。

*注意:乙醚量为 0，因为第一个条目是自由的。进一步的条目将需要添加乙醚。我们会把动态计算留给你做作业。提示:我们的 DAO 中有一个`calculateSubmissionFee`函数就是为了这个目的。*

在这一点上，我们需要改变 JS 顶部的一些东西，在那里函数在页面加载时自动执行:

```
if (loggedIn) {

    token.balanceOf(web3.eth.accounts[0], function(error, result) {console.log(JSON.stringify(result))});
    story.getSubmissionCount(function(error, result) {console.log(JSON.stringify(result))});

    web3.eth.defaultAccount = web3.eth.accounts[0]; // CHANGE

    readUserStats().then(User => renderUserInfo(User));
    refreshSubmissions(); // CHANGE
} else {
    document.getElementById("submission-body-btn").disabled = "disabled";
} 
```

这些变化用`// CHANGE`标记:第一个变化让我们设置执行交易的默认帐户。在 Web3 的未来版本中，这可能会成为默认设置。第二个在页面加载时刷新提交的内容，所以当站点打开时，我们得到一个完整加载的故事。

如果您现在尝试提交条目，MetaMask 应该会在您单击*提交*时打开，并要求您确认提交。

![Submitting an entry](img/7c4d54811505b26a7e46438b8505ae6d.png)

![Confirming an entry](img/9d2fb927846a8c0fc6f41b6cc3cf1c26.png)

您还应该在 events 部分看到打印出来的事件。

![Event confirmed](img/c97a03b2a4bbb45c5e60a70b4297a13a.png)

控制台应该回显这个新条目的散列。

![Hash printed](img/5a9925198dd052632b9ed419308a0d44.png)

*注意:元掩码目前在专用网络和随机数方面存在问题。这里描述的是，很快就会修复，但是万一你在提交条目时在你的 JavaScript 控制台中得到`nonce`错误，现在的权宜之计是重新安装元掩码(禁用和启用都不起作用)。请记住首先备份您的种子短语:您将需要它来重新导入您的元掩码帐户！*

最后，让我们获取这些条目并显示它们。让我们从一点 CSS 开始:

```
.content-submissions .submission-submitter {
  font-size: small;
} 
```

现在让我们更新`refreshSubmissions`函数:

```
function refreshSubmissions() {
  story.getAllSubmissionHashes(function (error, result) {
    var entries = [];
    for (var i = 0; i < result.length; i++) {
      story.getSubmission(result[i], (err, res) => {

        if (res[2] === web3.eth.accounts[0]) {
          res[2] = 'you';
        }
        let el = "";
        el += '<div class="submission">';
        el += '<div class="submission-body">' + web3.toAscii(res[0]) + '</div>';
        el += '<div class="submission-submitter">by: ' + res[2] + '</div>';
        el += '</div>';
        el += '</div>';
        document.querySelector('.content-submissions').innerHTML += el;
      });
    }
  });
} 
```

我们滚动所有的提交，得到它们的散列，取出每一个，并在屏幕上输出。如果提交者与登录用户相同，则打印“您”而不是地址。

![Rendered submission](img/3ba2b029fa8792108dc45671ad0395d1.png)

让我们添加另一个条目进行测试。

![Another entry](img/091233f8395b3e9518c2b4ee3f871088.png)

## 结论

在这一部分，我们为 DApp 开发了一个基本前端的开端。

因为开发完整的前端应用程序本身也是一门课程，所以我们将把进一步的开发作为家庭作业留给您。只需像演示的那样调用函数，将它们绑定到一个常规的 JavaScript 流中(或者通过 VueJS 之类的框架，或者普通的旧 jQuery，或者像我们上面所做的那样的原始 JS ),然后将它们绑定在一起。这实际上就像与标准的服务器 API 对话。如果您确实遇到了困难，请查看项目回购以获取代码！

您可以进行的其他升级:

*   检测 web3 提供者何时改变或可用帐户的数量何时改变，指示登录或注销事件，并自动重新加载页面
*   除非用户登录，否则禁止呈现提交表单
*   除非用户至少有一个令牌，否则阻止呈现投票和删除按钮，等等。
*   让人们提交和渲染 Markdown！
*   按时间(块号)排序事件，而不是按类型！
*   让事件更漂亮、更易读:不要显示十六进制内容，而是将其翻译成 ASCII 码，并截短为 30 个左右的字符
*   使用合适的 JS 框架，比如 VueJS，从你的项目中获得一些可重用性，并拥有更好的结构化代码。

在下一个也是最后一个部分，我们将把重点放在将我们的项目部署到实时互联网上。敬请期待！

## 分享这篇文章*