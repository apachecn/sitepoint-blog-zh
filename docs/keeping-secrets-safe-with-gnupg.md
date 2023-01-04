# 用 GnuPG 保守秘密

> 原文：<https://www.sitepoint.com/keeping-secrets-safe-with-gnupg/>

GnuPG 代表 *GNU Privacy Guard* ，一种基于 OpenPGP 标准的公钥加密形式。本文将向您展示如何使用 PHP 和 GnuPG PECL 扩展来执行一些基本的 GnuPG 任务。

出于我们的目的，作为想要发送和接收加密消息的人，您需要三个密钥:您自己的公钥、您自己的私钥以及您想要与之通信的人的公钥。

永远不要共享私钥，但是需要您自己的私钥来生成加密的消息。公钥可以而且必须分发。任何人想要加密仅供您查看的消息或解密您发送给他们的消息，都需要您的公钥。公钥不能用于伪造签名或解密只对您有意义的消息。

GnuPG 也是一种通过加密签名来验证非加密数据真实性的方法，但是这个主题不在这里讨论。

这里讨论的 GnuPG 扩展依赖于 GnuPG 在您所使用的机器上的安装，以及运行代码的用户帐户的配置。这里提供的示例旨在从命令行运行，并将与运行它们的用户的 GnuPG 环境交互。将这里的概念扩展到基于 web 脚本的工作应该是微不足道的。

## 生成密钥

在使用 GnuPG 做任何事情之前，您必须有一个私有/公共密钥对。虽然 PECL 扩展不允许您直接从 PHP 创建这些键，但是您可以在命令行中使用`gpg`命令，或者为您选择的任何操作系统创建一个友好的 GUI 前端。在 Linux 中，您需要的只是:

```
gpg --gen-key
```

回答几个关于您的身份的简单问题，选择一个使用您的私钥的密码短语，并接受其他所有内容的缺省值后，您的 GnuPG 密匙环就生成了。钥匙圈储存你的钥匙和任何你想与之交流的人的钥匙。

一旦生成，您的密匙环将自动填充您的第一个私钥/公钥。您应该导出您的公钥和私钥的副本，并将它们保存在一个安全的地方，以便您可以在将来与 GnuPG 的所有交互中重用相同的密钥。您可能还想考虑将您的公钥发布到一个密钥服务器，以便以一种安全的方式提供给任何需要它的人。关于 GnuPG 工具和密钥服务器的更多信息可以在 [gnupg 网站](http://gnupg.org)上找到。

## 导出密钥

一种导出 GnuPG 公钥的方法是使用 PHP。但是，在执行导出之前，您必须找到新公钥的密钥“指纹”。钥匙圈里的每把钥匙都有一个指纹，作为唯一的标识符。要列出它们，循环遍历一个`gnupg_keylistiterator()`对象:

```
<?php
$iter = new gnupg_keylistiterator();
foreach ($iter as $fingerprint => $user) {
    print "$fingerprint: $usern";
}
```

对于我的工作站上名为“testuser”的用户的密匙环，上面的代码会产生以下输出:

```
6436010991C352981F95F9A5D5AF916244CD449B: Test User (This is a test account) <test@example.com>
```

密钥本身是二进制数据，因此要实际编码和打印公钥的 ASCII 版本，我们需要一些新代码:

```
<?php
$gpg = new gnupg();
$export = $gpg->export("6436010991C352981F95F9A5D5AF916244CD449B");
print $export;
```

这将创建一个新的 gnupg 对象，并使用 export()方法导出对应于所提供指纹的公钥。在我的例子中，测试用户的密钥是:

```
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v1.4.2 (GNU/Linux)

mQGiBFDP4+cRBACupVdbSBVa5wLjKFoELiS9IunzQA6hj2P1jCYmXtwSPwvvrXRX
LqhpkmXmWrHvo+4WW8aDjI2csuh8z4gNB+YGkaB3iKZxyhdazKco0R1BU7fjPrUn
4iqVqkpaixcdMVDPkjjBTJDEdjxWWieErSzbzkjy8wuTJR+lAVFWTcuTOwCg9WAg
bLwpPIlcULQ+D/l3sLt2AaED/jgdcTvMsVG96xZGWT9bewl/HVX+2ZpCWIkbE34E
AABS8c9hwNEz3QvkEqoxcf5sC8ujtvTf3Gky4E6h4KZrpCZuZ8kFRQiAhqP6XwNi
8jJM0BK8ooYPFP4UsfRQImc4XczL/njtwpwJ8wX2UOb7oglJB5acMulvzpD4cRvK
FkqIA/9NzGFExUeJeJ8b6GC17KDOIHfBwlrcGvc85c574fUVekV9jGGaAzl6i89O
OIWQCELH9s8quTA23tDeMcHRwUMHAYxh7lf2stoJmwz0IySpUCkmxcUhB5TIzBOB
jHifGAlJKby6gNJhlvghTxXlVGsQiM14JLJo2+hS2qMc17vp77QyVGVzdCBVc2Vy
IChUaGlzIGlzIGEgdGVzdCBhY2NvdW50KSA8dGVzdEB0ZXN0LmNvbT6IYAQTEQIA
IAUCUM/j5wIbAwYLCQgHAwIEFQIIAwQWAgMBAh4BAheAAAoJENWvkWJEzUSb6iIA
nRhCMJ3CN+1QXSD2zAtsoWgVebqZAKDLp1636WZ3XZAIVOlHBnpMTfcdA7kCDQRQ
z+PwEAgAkRnVYvmbpkGCCU8T56Wh2+jMA5GVPbPuyqmprfvgrt7yp9EEXfXRrGVA
KYMbFDU4+Vdc2U9Wm/T6P7McvI3dJARWOD6NeqOh+k6yTYxH+qznsU2d/7+Vugfx
zxVXtllhpnyWc5AlAr6yo98UdjO+q5RNM3fzSdjHth+MCJqEBfkyTui2VN9odnnS
IkDFHJyVcaUfQ580RpLWllwAC3+Urntc02S6xxEr03fKIe/wuhXC9gpG5lWu3hyj
k0PlsSh55w5gb4zF3Q2yDbToAuvSg1My0ytIPddmjjBx8gs4PCnB+48R52jsT/uI
Ju6j83Ddy1Ph1cnalChIpMit2Hm40wADBQf+J+NRhzLByj3YRsRK9ohZ0dm1+sMU
S86Xv676IQMgTzohFCAicnFZtTJx6N35pox/azOOGxAoof79TPv1r897bysAXjmu
qjW2mMpAfhnNY18Wf2SB+R7oYsID/z4fOQltyX54z8Fi40OO6UtI1X3Lh4KGzhUK
AnkgVeYGxupiK2HHtxeknEiKuAY+RXiQRYIC8YgAZboKqbWt8WVSMwctLbtRgXQC
VlkcZzwPk+mPocxYcnID+mZgIpwX0/+Zs4OOlyrf1k1oI6zQA7tJ+etGugtee78j
e8gub9CYvyCJMFM3pI5OEKC9BRSjPjgSw/6kQEc4bHP1Bn4WqPgh2d61Q4hJBBgR
AgAJBQJQz+PwAhsMAAoJENWvkWJEzUSbAhYAoKnL/nGFmJejtoPdws+QRW95VECn
AJ4yFFV1axpTDbIClQQTerJS7xpxjg==
=g/FY
-----END PGP PUBLIC KEY BLOCK-----
```

这个扩展只允许我们导出公钥，所以如果你想以类似的方式导出你的私钥用于备份，那么你需要使用 GnuPG 工具本身。

## 导入密钥

我们现在有三把私人交换所需的钥匙。我们已经生成了自己的公钥/私钥，上面的 testuser 公钥是我们用来作为第三方公钥的。

为了与 testuser 通信，我们需要将她的公钥放入我们的密匙环。导入公钥可以通过将公钥传递给`import()`方法来完成:

```
<?php
$keydata = "FULL PUBLIC KEY FROM ABOVE";
$gpg = new gnupg();
$info = $gpg->import($keydata);
```

您可以在命令行上使用`gpg --list-keys`来验证这个密钥已经被添加到您的密匙环中，并使用前面的`gnupg_keylistiterator()`示例来获取指纹。

## 加密邮件

有了密匙环中的所有密钥，我们可以为 testuser 加密一条重要的消息。该消息包含问题的答案:

> 问:换一个灯泡需要多少个程序员？

我们将使用 testuser 的公钥来确保她是唯一能够解密答案的人。加密答案的代码是:

```
<?php
$gpg = new gnupg();
$gpg->addencryptkey("6436010991C352981F95F9A5D5AF916244CD449B");
$enc = $gpg->encrypt("A: None, that is a hardware problem!");
print $enc;
```

我们使用`addencryptkey()`来指定用哪个公钥来加密这个消息，这个公钥应该是接收方的，而不是发送方的。在这种情况下，它是 testuser 的公钥指纹。

一旦加密，没有人能够解密消息，除非他们的密匙环中有 testuser 的私钥……这个密匙环应该是 testuser 自己。

## 解密消息

另一方面，一旦 testuser 收到消息，她可以使用下面的代码来解密机密答案:

```
<?php
$encrypted = "THE UNREADABLE ENCRYPTED MESSAGE SHOULD BE PASTED HERE";
$gpg = new gnupg();
$gpg->adddecryptkey("6436010991C352981F95F9A5D5AF916244CD449B","test1234");
$plain = $gpg->decrypt($encrypted);
print $plain;
```

加密信息存储在`$encrypted`中。我们创建一个新的`gnupg()`对象，通过指纹*和*我们的私钥密码来指定应该使用哪个 testuser 的密钥来解密消息，然后打印解密的消息。值得一提的是，解密消息总是需要接收者输入他们在`addencryptkey()`中与私钥相关联的密码，除了在 gnupg v2 中不能在代码中输入纯文本密码——在 CLI 中运行此代码时，会提示您输入密码。如果您计划在基于 web 的 gnupg v2 工具中使用这个函数，您可能需要考虑在 web 用户的私钥中排除密码短语。

## 最后

既然我们知道如何用 GnuPG 加密和解密，为什么还要用 PHP 呢？使用 SSL 可以保护在 web 服务器和客户机之间传输的数据，而 SSH 可以用来保护 shell 会话，那么为什么还要使用其他方法呢？

有时，您需要确保信息的安全，不仅是在传输过程中，还包括信息存储的位置以及中间的所有层。也许你需要把金融信息传送到一个遥远的地方；您能信任系统管理员或在该位置有物理访问权的人吗？或者某个项目要求您使用不安全的协议(如 FTP)传输数据。

虽然我们上面的简单例子对于传输加密消息很有用，但是 GPG 和 gnupg 扩展也可以通过使用签名来验证未加密消息的真实性。电子邮件也是一种众所周知的不安全的通信方法，消息很容易被伪造，虽然这里没有详细描述，但使用加密和加密签名可以确保安全性和真实性。

如果您正在编写一个要求严格数据安全性的应用程序，我鼓励您进一步探索 GnuPG 工具选项和概念。

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章