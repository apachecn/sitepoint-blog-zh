# 无痛账户 UX 的 3 个规则:登录

> 原文：<https://www.sitepoint.com/3-rules-painless-account-ux-login-screens/>

[!['Welcome. Come in this Door' elegantly painted onto a glass door frame.](img/595bc2e2c1baa87ba4c7b6b202c3c6e0.png)](https://www.flickr.com/photos/brent_nashville/3027331148/)

照片:SeeMidTN.com(又名布伦特)

在本文的第一部分[中，我们概述了遵循三个小规则如何让账户管理不那么痛苦。](https://www.sitepoint.com/3-rules-painless-account-ux/)

快速回顾一下这些规则:

[1\. Don’t make the user guess](#rule-1-dont-make-the-user-guess "Jump to this rule")

Almost every service on the web handles account management slightly differently. As a result, you have to be explicit about the requirements for your service. Otherwise, you’re just being cruel.

[2\. Balance security with usability](#rule-2-balance-security-with-usability "Jump to rule 2")

Often, security folk will insist on an approach that compromises the user experience. This is foolish, because poor usability will lead to workarounds, and workarounds in turn lead to weakened security. Instead, aim for a design that is both secure and usable.

[3\. Keep it simple](#rule-3-keep-it-simple "Jump to rule 3")

Account management is one of your biggest potential barriers to usage. Make the barrier practically invisible through a simple, seamless, pain-free design.

* * *

在第一部分中，我们展示了如何应用这些规则来改进创建新的*账户的过程。现在让我们看看如何使用这些规则来改善登录体验。*

 *## 登录过程

### 规则 1:不要让用户猜测

登录屏幕通常相对较小，一个字段用于输入用户名，另一个字段用于输入密码。不过，极简并不总是简单，尤其是当你隐藏了有助于向用户解释事情的信息时。

*   如果用户名是用户没有创建的标识符，例如电力账号，告诉用户用户名是什么以及他们可以在哪里获得它(例如“在你的账单的右上角”)。
*   如果用户名是电子邮件地址，告诉用户。
*   如果密码是数字 pin，告诉用户。
*   如果空间允许，请使用“记住这台计算机上的用户名”，而不是“记住我”。(“记住我”并没有真正告诉用户会发生什么。)

#### 示例:做什么*不做什么*

Roam Express 是一家支付道路通行费的电子标签提供商，它不会告诉你在哪里可以找到你的标签号。

![Roam Express](img/3d3e379f6d63a607046e79bd3c6a1b29.png)

#### 示例:更好的方法

LG 做得更好，具体到用户名是一个电子邮件地址，以及“记住我”复选框会做什么。

![LG Login](img/88b6f2ab274893738600b371c2f1f551.png)

### 规则 2:平衡安全性和可用性

登录时，主要的挑战安全性与可用性的挑战与你如何处理不正确的尝试有关。您向(合法)用户提供的信息越多，他们就越有能力恢复并正确登录。然而，这些信息也可能被黑帽黑客用来确定用户名和密码。

解决方案是:

*   提供适量的信息(例如，当登录失败时告诉用户，如果可以的话，告诉用户是用户名还是密码的问题)；
*   给用户至少两次，最好是三次尝试——错别字容易犯；和
*   应用时间限制和惩罚，而不是把人们完全锁在外面。

托马斯·贝克达尔在他的文章[密码的可用性](http://www.baekdal.com/insights/password-security-usability)中对时间限制和惩罚做了最好的描述:

> 你需要做的就是阻止自动黑客脚本有效工作。你需要做的是:
> 
> 1.在登录尝试之间添加时间延迟。而不是让人们一次又一次地签到。在每次尝试之间增加 5 秒钟的延迟。它足够短，不会被注意到(需要超过 5 秒的时间来意识到您尝试了一个错误的密码，并键入一个新的)。而且，它迫使黑客只能每 5 秒发出一次登录请求(而不是每秒 100 次)。
> 
> 2.如果一个人输入错误的密码超过 10 次，比如 1 小时，就要加上惩罚期。同样，这严重干扰了黑客脚本的有效工作。

#### 示例:更好的方法

优步告诉你你的两个证书中哪一个是错的。

![Uber login screen](img/d2d5a3c427985698a2042fbcea7f635f.png)

### 规则 3:保持简单

你可能会想，考虑到一个日志表格有多小，它们一定都很简单。唉，你错了！看看这些服务是如何使流程变得过于复杂的。

#### 示例:做什么*不做什么*

西澳大利亚交响乐团在登录时询问推广代码*。*

![Western Australia Symphony Orchestra login screen](img/13056d6ebf403447121eec8104e9f95a.png)

#### 示例:做什么*不做什么*

合作书店曾经让登录变得比实际需要的更加困难。他们*隐藏了密码字段*，直到电子邮件地址被输入和验证(使用 tab 键，不多！).(谢天谢地，经过最近的重新设计，他们已经转向了更标准的登录方式。)

![Coop Bookshop login screen](img/7ecc619ea162ad7665b9ef3932d686f2.png)

上面的两个例子显示了添加不必要的字段或步骤，这是您绝对应该避免的。你还能做什么来保持登录简单？

*   确保行动号召中的日志易于查找，无论用户何时需要。右上角是一个常见的位置。
*   支持自助服务和立即重置用户密码(以及用户名，如果用户选择的话)。重置应该需要足够的身份验证(理想情况下是双因素身份验证，而不是安全问题，后者很难很好地实现)。不要“明文”发送密码，例如通过电子邮件，尤其是永久密码，因为电子邮件可能会被拦截或黑客攻击。这就是重置比恢复更可取的原因。
*   如果用户名是电子邮件地址，并且用户使用手机或平板电脑，请切换到设备的电子邮件输入优化键盘。类似地，换成数字键盘作为密码。更多信息见[触摸键盘类型](http://baymard.com/labs/touch-keyboard-types)。
*   你肯定需要**而不是**一个登录(或注册)表单上的“清除”或“重置”按钮(参见上面关于*不需要*做什么的 Roam Express 示例)！
*   将“忘记用户名”和“忘记密码”链接放在各自字段附近。
*   不要太担心“登录”和“签到”，两者都有利弊。例如，有些人认为“登录”是技术术语，但是“登录”更容易与“注册”混淆。选择一种方法并坚持下去；在这种情况下，一致性比具体的用词更重要。哦，注意“登录”是一个名词(例如，“你的登录是什么？”)而“登录”/“登录”是动词(例如，“请登录您的帐户”)。参见[“登录不是动词”](http://loginisnotaverb.com)。

#### 示例:做什么*不做什么*

Computershare 将“忘记用户名/密码”链接不仅放在远离相应字段的地方，而且放在显著的蓝色登录框之外(这几乎可以保证它们不会被看到)。

![Computershare's login](img/93531f504beac1a9dc402905474c98ab.png)

#### 示例:更好的方法

与 Computershare 不同，Skype 将忘记用户名链接(“忘记了您的 Skype 用户名？”)就在用户名字段的下面，这很理想。对于“忘记密码”链接也是如此。

![Skype's login screen](img/2aebb06417b1f63d326017b3c49da995.png)

#### 示例:做什么*不做什么*

旅游网站 Kayak 在通过手机输入电子邮件地址时不会调整键盘。

![Kayak's login screen](img/c34f52c16f5f91e1298a7d9bcfaa1120.png)

#### 示例:更好的方法

通过手机输入电子邮件地址时，Tripadvsor 会调整键盘。

![Tripadvisor](img/ef02e9c4bb3c72e4198e2a97f1714cc6.png)

原来如此。对于无痛注册和登录:

*   不要让用户猜测；
*   平衡安全性和可用性；和
*   保持简单。

有什么方法可以实现这些我没有提到的规则吗？请在评论中告诉我们。

否则，继续前进，创造伟大的客户管理经验！* 

## *分享这篇文章*