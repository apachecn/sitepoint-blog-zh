# MetaMask:以太坊区块链的简单使用方法

> 原文：<https://www.sitepoint.com/metamask-ethereum-blockchain/>

*这篇关于 MetaMask 的介绍最初发表在 [Bruno 的 Bitfalls 网站](https://bitfalls.com/2018/02/16/metamask-send-receive-ether/)上，经允许在此转载。*

在这篇文章中，我们将解释什么是元掩码，它的用途，以及如何使用它。

* * *

为了更好地理解以下内容，请阅读:

*   [什么是区块链？](https://www.sitepoint.com/blockchain-what-it-is-how-it-works-why-its-so-popular)
*   [以太坊是什么？](https://www.sitepoint.com/ethereum-introduction)
*   什么是以太坊测试网？

* * *

## 元掩码简介

MetaMask 是 Google Chrome、Vivaldi、Opera 和 Firefox 对浏览器的扩展，使 web 应用程序可以轻松地与以太坊区块链通信。换句话说，MetaMask 是你浏览器的一个[钱包](https://bitfalls.com/2017/08/31/what-cryptocurrency-wallet/)。

你可以从[官方网站](http://metamask.io)下载它，或者你可以获得[勇敢浏览器](http://brave.com)，它是谷歌 Chrome 的[分支](https://bitfalls.com/2017/11/07/segwit2x-fork-can-expect-whos-behind/)，并带有一些非常加密货币友好的功能(内置元掩码等)。

![MetaMask installation screen](img/546944561c1dc82f22152e7c9b8eedfd.png)

在安装之前，MetaMask 会请求一些相当宽松的权限:

![MetaMask permissions](img/7d7ee7a85b9bb97054f9a70c63dce72f.png)

它希望完全控制复制粘贴命令，允许与外部资源(区块链的网站和节点)对话，并可以查看和修改您访问的每个网站的内容。换句话说，它想要*一切*。只要有一家声誉良好的公司在背后支持，这就没什么好担心的。尽管如此，这些权限还是值得记住的；这已经不是第一次有恶意的演员得到一个流行的扩展，并[肆虐](https://thehackernews.com/2017/07/chrome-extention-hacking-adware.html)。

## 设置

安装后，扩展的图标将出现在工具栏中。单击它将显示带有用户界面的弹出窗口。第一个屏幕将警告我们，我们正在处理测试软件，我们生成并使用 MetaMask 的地址对我们访问的每个页面都是可见的，除非我们在访问它之前退出 MetaMask。

![MetaMask beta](img/85a3e59bd6c9e7431726aab5de8c2fe3.png)

下面的屏幕警告我们关于服务条款，以及 MetaMask 不应该为发生的任何不好的事情负责的事实。

![MetaMask terms of service](img/f1b3ef95b1b6981b651327dedf8b27bc.png)

将文本滚动到底部将使接受选项可用。然后，我们进入密码选择屏幕。

![MetaMask password](img/62c183bd9f25f4bfcc9cc29f93ce3eaa.png)

就像使用 [Mist](https://bitfalls.com/2018/02/12/explaining-ethereum-tools-geth-mist/) 一样，密码将对生成的 JSON 文件进行加密，该文件包含关于我们的[钱包](https://bitfalls.com/2017/08/31/what-cryptocurrency-wallet/)的信息。用户每次登录元掩码时都需要输入密码。

输入密码后，MetaMask 将显示一个**种子**——一个单词列表，如果我们由于硬件或软件错误而无法访问浏览器或钱包文件，可以从该列表中重新生成生成的地址。

![MetaMask seed](img/33a377e37b887089580ad532bb3a43f6.png)

如果您打算继续使用这些钱包，应该将该列表打印出来并保存在安全的地方。就当是一个[纸钱包](https://bitfalls.com/2017/09/08/best-ways-protect-cryptocurrency-wallet/)吧。

## 帐目

完成此过程后，将会打开一个包含单个帐户的新屏幕:帐户 1。

![Account 1](img/a8c10000bbbc4e2e997219e8257faf0f.png)

如果您将鼠标光标放在帐户名上，将会出现编辑选项，允许您对其进行重命名。

![Account renamed](img/415cf3b5d2d24f7913e50f946b2ef454.png)

在左上角，你可以切换网络。点击*主网*，选择另一个类似 Ropsten 的。你可以在这里找到更多关于这些测试网[的信息。](https://bitfalls.com/2018/02/08/what-is-an-ethereum-testnet-and-how-is-it-used/)

帐户名称旁边的三个点是一个菜单。他们提供了一些关于帐户的选项，如在 Etherscan 上检查帐户(只有当 MetaMask 连接到 Mainnet 或公共 testnet 时才可能)，显示地址的二维码以便用手机扫描或打印纸质钱包，将地址复制到剪贴板(即粘贴到钱包软件中)，以及*导出私钥*，这让你可以从**开放帐户**创建私钥——一个类似密码的数字和字母的集合。这可以用*代替许多钱包工具生成的加密 JSON 文件*来将钱包导入另一个软件。

![MetaMask Private Key](img/849cd11a6aeacf95464986941c1360f9.png)

三个点上方有两个按钮:三条水平线引导我们到设置菜单(目前我们不感兴趣)和一个被弯曲箭头包围的人体轮廓图标。这是*账户*菜单，让我们创建新账户或导入以前创建的账户。

![](img/40433e34e61afd7ef77da5542529fb79.png)

*创建账户*选项立即创建一个新账户，使其在 UI 中可用。

![New account available](img/b6187f496a1c907ad1d5aa382b803a7e.png)

*Import Account* 选项引导我们进入允许我们导入 JSON 文件或私钥的屏幕(就像前面提到的通过选项导出的那个)。JSON 文件通常由 MyEtherWallet 或 Mist 等程序生成。具体来说，这意味着这些钱包中的任何一个都可以生成兼容其他钱包消费的数据，从而保持您的资金安全并可跨软件移植。

## 发送和接收

现在让我们试着发送和接收一些以太。

首先，让我们切换到 Ropsten 网络。如果您还没有，请从 MetaMask 左上角的菜单中选择 *Ropsten* 。请注意，地址保持不变:这是因为用于生成一对[密钥](https://bitfalls.com/glossary/#private-key)的算法是相同的，并且是技术相关的，而不是网络相关的。一个网络上的私钥可以解锁另一个网络上的钱包，所以即使共享一个 Testnet 私钥也要小心！只有这些账户的余额不同。点击了解更多关于这个[的信息。](https://bitfalls.com/2018/02/08/what-is-an-ethereum-testnet-and-how-is-it-used/)

接下来，让我们为该帐户选择*将地址复制到剪贴板*。

![Copy address to clipboard](img/ce2cebdc5f2a4b98d054fe9865c9fdad.png)

然后，让我们从其中一个**水龙头点**获取一些免费的乙醚——这些点是专门为此目的而建造的。

*   https://水龙头. metamask.io 自动读取你的浏览器的 metamask 地址，并向其发送 Ether。
*   http://水龙头. ropsten.be:3001 要求您手动输入乙醚的发送地址。如果您正在使用此站点，请使用第一个字段将之前复制的地址粘贴到其中。

这两个选项都可以。因为这些水龙头使用真正的以太坊区块链(但在测试网上)，资金到达可能需要一分钟。

既然我们的一个账户上有乙醚，让我们试着把它送到另一个账户上。首先，我们选择我们创建的帐户并复制它的地址。然后我们切换到有乙醚的账户，去*发送*。

![Funds have arrived](img/255a552ae427b1a2516614c9b87c2917.png)

这是我们输入收件人地址的屏幕。在“金额”下，让我们输入类似于 0.1 的值，并保留交易数据字段不变。单击下一步。

MetaMask 将生成一个用于签名的事务(TX)并呈现给您。

![Send screen](img/0720f35c4c4ca7f47ac9c6e5e6f70b04.png)

正如本文中的[所解释的，气价和气限是可变的，并且取决于网络的繁忙程度。在 Ropsten 网络上，这两个值都是默认值。按下*拒绝*将取消发送并返回上一个屏幕，*重置*将返回默认值，如果您做了任何更改，*提交*将发送。](https://www.sitepoint.com/ethereum-transaction-costs)

![Transaction suggestion](img/2e55a87ed35340f81b64c2031686f1de.png)

最多几分钟后，交易应该就确认了。

![Sent transaction](img/76c94f6949c3ef0963f63a7985fe9268.png)

## 代币

元掩码中对标记的支持充其量是有限的。它们目前只支持标准的 ERC20 令牌，而且只是部分支持:它们不容易从 MetaMask 发送，只能相对容易地查看。幸运的是，只要元掩码帐户处于解锁和开放状态，访问以下三个页面中的任何一个都将允许从当前开放的地址发送令牌:

*   [以太坊钱包](https://wallet.ethereum.org/)
*   [令牌忍者](https://tokenninja.github.io/)
*   [我的钱包](https://www.myetherwallet.com/)

## Web3

重要的是要记住，当您解锁元掩码扩展时，元掩码会将 Web3.js 注入到您打开的每个网站中。Web3 是一个软件库，允许网站轻松地与以太坊区块链进行通信。

虽然 Web3 注入本身并不存在安全风险，但这并不意味着它不会成为安全风险。当你登录时，不仅所有被访问的网站都会看到你的 MetaMask 以太坊地址，而且如果有人向 Web3、MetaMask 本身，甚至你正在访问的网页注入一些恶意代码，他们可能会收集到比你愿意分享的更多的私人信息(你在网站上的用户帐户及其与以太坊地址的连接，从而有效地识别你的身份)。

因此，建议在 MetaMask 钱包中保留少量乙醚，并且只在没有任何其他扩展的干净浏览器中使用 MetaMask。

我们将在下一篇文章中详细介绍 Web3。

## 结论

MetaMask 是一个非常实用的解决方案，可以使用以太坊区块链并在浏览器中保留以太。虽然它可能有一些安全问题，但 MetaMask 以其简单易用的特点极大地促进了区块链技术和加密货币的主流化——你将在我们关于运行你自己的私人区块链的帖子中看到这一点。

## 分享这篇文章