# 为什么应该使用 Bcrypt 来散列存储的密码

> 原文：<https://www.sitepoint.com/why-you-should-use-bcrypt-to-hash-stored-passwords/>

在最近的 ZoneAlarm 调查中，79%的参与者被发现使用有风险的密码。这些数据被用来展示一个非常时尚的信息图表，展示了不良密码的使用有多普遍。但是，不管猜测好的密码有多难，它们可能不会比坏的密码更难破解，这取决于密码是如何存储的。

早在 2006 年，Reddit 证实他们数据库的一份副本被盗。更麻烦的是，所有的用户密码都已经[存储在 Reddit 的数据库中，而没有以任何方式掩盖](http://www.reddit.com/r/reddit.com/comments/usqe/reddits_streak_of_bad_luck_continues/ "Reddit's Streak of Bad Luck Continues... : reddit.com")！许多用户感到恐慌，因为他们在许多其他网站上也使用了相同的密码。这是一个巨大的安全漏洞，给许多不幸的 Reddit 成员带来了重大问题。令人震惊的是，一些 web 开发人员仍然认为将密码存储为没有加密或哈希的纯文本是存储密码的好方法。

现在是 2011 年，尽管大多数开发人员都认为混淆存储的密码是任何用户驱动的网站的强制性安全功能，但这种做法已经落后了。有人认为 MD5 是一种安全的密码编码方式；这是一个谎言(也是一个非常糟糕的谎言)！MD5 是隐藏非敏感数据的好方法，但使用[彩虹表](http://en.wikipedia.org/wiki/Rainbow_table "Rainbow table - Wikipedia, the free encyclopedia")可以非常容易地“解码”。我曾经听说过一个 web 开发人员的故事，他在客户删除了一封包含其密码信息的电子邮件后，无法访问客户的数据库。开发人员在他们的服务器上仔细检查了客户的文件后，发现了一个包含 MD5 密码散列的格式良好的文本文件。在登录到客户的数据库之前，这只是一个快速复制和粘贴到在线彩虹表查找实用程序的过程。这个故事有一个快乐的结局，客户端的密码被恢复，开发可以继续，但令人震惊的事实是，这个过程很容易被恶意黑客的手执行。

## BCrypt

那么，安全密码哈希的最佳选择是什么呢？PHP 中一个突出的选项是 Bcrypt。Bcrypt 是基于 Blowfish 对称分组密码加密算法的自适应散列函数。它使用一个关键因素(或工作因素)来调整散列的成本，这可能是 Bcrypt 最显著的特性。随着计算机变得越来越强大，未来增加散列的成本(时间和处理能力)的能力是 Bcrypt 区别于其他函数的真正原因。

Bcrypt 可以扩展所谓的关键因素，以补偿日益强大的计算机，并有效地“减缓”其哈希速度。更改密钥因子也会影响散列输出，因此这使得 Bcrypt 对基于 rainbow 表的攻击具有极强的抵抗力。较新的计算机可以尝试猜测散列的原始输入，但仍然需要相同的时间(或更长的时间)来验证其猜测是否匹配。这一切让 Bcrypt 几乎是未来的证明！

与其他函数相比，Bcrypt 对输入进行哈希处理的速度慢得令人难以置信，但这会产生更好的输出哈希。说到散列和加密，越快越好。编码的时间越长，计算机尝试识别输入的时间就越长。正如托马斯·普塔切克在他的文章《受够了彩虹表》中所写的，“你越能优化你的密码散列函数，你的密码散列函数越快，你的方案就越弱。”在 Bcrypt 的例子中，它非常慢。考虑一下引用自 Coda Hale 的文章[如何安全存储密码](http://codahale.com/how-to-safely-store-a-password/ "How to Safely Store A Password | codahale.com")的这段话:

> bcrypt 比 MD5 慢多少？取决于工作因素。使用工作因子 12，bcrypt 在我的笔记本电脑上大约 0.3 秒内散列了密码 yaaa。另一方面，MD5 需要不到一微秒的时间。

不过，不要认为 Bcrypt 听起来会太慢。正如我前面提到的，你可以设置你想要的散列值的大小。这意味着您可以使用最大的密钥因子来获得最大的安全性，但会牺牲时间，或者您可以使用最小的密钥因子并减少散列输入值所花费的时间。在这两种情况下，正是这个特性使得 Bcrypt 加密如此安全，所以你不能失去任何一个选项。一切都取决于你。

为了更好地理解 Bcrypt 的能力，让我们将其与合适的哈希算法(如 SHA-2)进行比较。SHA-2 不使用任何加密算法，而是使用哈希算法来生成其输出(尽管 SHA-2 和河豚的算法都经得起非常相似的审查)。Bcrypt 比 SHA-2 慢得多，因此理论上更好。SHA-2 也不像 Bcrypt 及其关键因素那样具有适应性，因此随着计算机处理能力的提高，它更容易受到基于表的攻击。目前，SHA-2 是一个完全有能力的散列函数，但是 Bcrypt 胜出了，因为它的速度(对于散列来说，越慢越好)和适应性。

## 在 PHP 中使用 Bcrypt

您可能会想，“哎呀，这些似乎都是非常复杂的函数…真的值得花时间改变我的密码散列策略来使用 Bcrypt 吗？”答案是肯定的！Bcrypt 易于使用，从长远来看是值得使用的。

如果您运行的是 PHP 5.3 版本，那么 Bcrypt 已经可用了，只需使用带有 Blowfish 所需盐的`crypt()`函数。如果您的系统支持的话，它可能在早期版本中可用，但是我推荐 5.3 版本，因为 PHP 包含了自己的算法实现，从而消除了任何额外的依赖)。

您可以通过检查是否定义了`CRYPT_BLOWFISH`常量并表示 1:

```
<?php
if (defined("CRYPT_BLOWFISH") && CRYPT_BLOWFISH) {
    echo "CRYPT_BLOWFISH is enabled!";
}
else {
    echo "CRYPT_BLOWFISH is not available";
}
```

之后，在存储密码之前，您只需使用类似以下的代码来散列您的密码:

```
<?php
$salt = '$2a$07$R.gJb2U2N.FmZ4hPp1y2CN$';
crypt("secretpassword", $salt);
```

salt 必须以`$2a$`开头，后跟一个两位数的密钥因子和另一个美元符号，然后包含 22 个字母数字字符(也允许使用句点和斜杠)。PHP 手册解释了盐的限制:

> [……]salt 如下:“$2a$”，一个两位数的成本参数，“$”，以及字母表中的 22 位数字”。/0-9A-Za-z”。在 salt 中使用此范围之外的字符将导致 crypt()返回零长度字符串。两位数成本参数是底层基于 Blowfish 的哈希算法的迭代计数的以 2 为底的对数，必须在 04-31 范围内，超出此范围的值将导致 crypt()失败。

## 摘要

PHP 允许开发人员非常轻松地使用 Bcrypt，这就引出了一个问题:为什么不使用它？如果你运行的版本低于 5.3，我恳求你升级你的 PHP 安装。对于创建存储用户密码和其他敏感数据的 web 应用程序的人来说，使用 Bcrypt 等安全散列函数的重要性应该是至关重要的。

除了它的易用性，我还鼓励你使用 Bcrypt，因为它符合摩尔定律。我知道我之前已经提到过这个问题，但是我觉得有必要强调一下这个事实的重要性。如果您现在开始使用 Bcrypt，您可以放心，您所有用户的密码都是用一个不会在一夜之间过时的函数散列的。这样做的关键是定制工作速率的能力，或者我前面提到的关键因素。

现在你知道了，我个人建议你为什么应该使用 Bcrypt 来散列密码和其他敏感数据。如果您想了解 Bcrypt 或 Blowfish 的更多信息，可以阅读以下网站和文章列表:

*   [PHP 手册–crypt](http://www.php.net/crypt "PHP: crypt - Manual")
*   [彩虹表够了:你需要知道的安全密码方案](http://chargen.matasano.com/chargen/2007/9/7/enough-with-the-rainbow-tables-what-you-need-to-know-about-s.html "Matasano Security LLC - Chargen - Enough with the Rainbow Tables :What You Need to Know About Secure Password Schemes")
*   [如何安全存储密码](http://codahale.com/how-to-safely-store-a-password/ "How To Safely Store A Passoword | codahale.com")
*   [用 Bcrypt，笨蛋！](http://yorickpeterse.com/articles/use-bcrypt-fool/ "Yorick Peterse Use BCrypt Fool!")
*   [请使用 Bcrypt 存储您的密码](http://gom-jabbar.org/articles/2008/12/03/why-you-should-use-bcrypt-to-store-your-passwords "Please use bcrypt to store your passwords")
*   [维基百科–河豚(cypher)](http://en.wikipedia.org/wiki/Blowfish_(cipher) "Blowfish (cipher) - Wikipedia, the free encyclopedia")
*   [堆栈溢出——如何在 PHP 中使用 Bcrypt 散列密码](http://stackoverflow.com/questions/4795385/how-do-you-use-bcrypt-for-hashing-passwords-in-php "How do you use bcrypt for hashing passwords in PHP? - Stack Overflow")

图片 via [瓦莱丽·波塔波娃](http://www.shutterstock.com/gallery-96482p1.html "Stock Photos | Shutterstock: Royalty-Free Subscription Stock Photography & Vector Art")/[Shutterstock](http://www.shutterstock.com "Stock Photos and Royalty-Free Images by Subscription")

## 分享这篇文章