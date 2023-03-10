# 使用谷歌认证器对 WordPress 进行两步验证

> 原文：<https://www.sitepoint.com/2-step-verification-wordpress-using-google-authenticator/>

网络安全是一个大问题。每天都有成千上万的网站、品牌和在线账户遭到黑客攻击。随着 WordPress 的广泛使用，它也不能幸免于这些攻击。过去，数以千计的 WordPress 支持的网站已经被成功锁定。

臭名昭著的默认“admin”用户名和弱密码都是大问题，因为它们更容易被暴力破解。强烈建议您永远不要使用“admin”作为您的主要用户名，并且始终使用一个强密码，而不是一个普通的、容易记住的密码。

## 谷歌的两步验证

通常，你需要一个用户名和密码来登录你的 WordPress 仪表盘。如果你使用强密码，这是朝着正确方向迈出的一步，但是你知道吗，你可以通过谷歌的两步验证(也称为双因素认证)使你的 WordPress 登录更加安全？

*有许多双因素认证插件。在本文中，我只关注 Google Authenticator，它已经被许多提供商广泛支持双因素身份验证。*

启用谷歌两步验证后，在您提供用户名和密码后，系统会提示您输入一个六位数。如果您不提供这个六位数，即使您有正确的用户名和密码，您也无法登录。

谷歌的两步验证可以让你的 WordPress 网站更安全，更能抵御暴力攻击；即使您的用户名和密码被泄露，没有六位数的密码也无法登录您的网站。

## 谷歌认证器插件

在撰写本文时，有两个免费插件可以在 WordPress 中启用 Google 的两步验证。第一个插件是由 [Henrik Schack](https://profiles.wordpress.org/henrikschack/) 开发的 [Google Authenticator](https://wordpress.org/plugins/google-authenticator/) ，它有超过 10，000 个活跃安装。根据插件目录，这个插件可以兼容 WordPress 的 3.8.3 版本，但是我一直在使用它和 WordPress 的最新版本(4.1 版本)没有任何问题。

![Google Authenticator for WordPress](img/d639b213527e9f6db1d39d1714bb2eac.png)

第二个免费插件是由[朱利安·利亚布夫](https://profiles.wordpress.org/julien731/)开发的[Google Authenticator for WordPress](https://wordpress.org/plugins/wp-google-authenticator/)，它有超过 2000 个活跃安装。这个插件兼容 WordPress 的 4.1.1 版本。

## 如何在你的 WordPress 网站上安装谷歌认证器

出于这个例子的目的，我使用 Henrik Schack 的 [Google 认证器](https://wordpress.org/plugins/google-authenticator/)。

首先，下载并安装 Google Authenticator 插件。激活后，转到“用户>您的个人资料”。现在选择“激活”复选框来激活 Google 在 WordPress 中的两步验证。

![Google Authenticator Settings](img/d9e3918136ef30503f8f6d9d35b7eb92.png)

接下来，您需要修改描述，以便在 Google Authenticator 移动应用程序上识别网站条目并显示二维码。在我的情况下，我已经添加了我的博客的名称。

## 如何在移动设备上安装谷歌认证器

如果您的移动设备上没有 Google Authenticator 应用程序，您需要下载并安装此应用程序。你可以在 [2 步骤验证支持页面](https://support.google.com/accounts/answer/1066447?hl=en)阅读如何在 Android 设备、黑莓或 iPhone 上安装 Google Authenticator 的分步说明。

*   [iOS 版谷歌认证器](https://itunes.apple.com/pk/app/google-authenticator/id388497605?mt=8)
*   [谷歌安卓认证器](https://play.google.com/store/apps/details?id=com.google.android.apps.authenticator2&hl=en)

要开始使用该应用程序，请单击右上角的铅笔图标。然后，单击底部的加号图标添加网站。选择扫描条形码，将相机对准二维码。

如果扫描二维码有问题，请尝试使用密钥。选择“手动添加帐户”,并在“输入”键下的框中输入电脑屏幕上显示的密钥。确保您已选择使键*基于时间*并按下“保存”。

现在退出你的 WordPress 站点并访问登录页面。现在，您应该会在登录屏幕上看到 Google Authenticator 的附加字段。

输入您的用户名、密码和六位数代码。启动您的 Google Authenticator 移动应用程序，获取六位数代码进行登录。请记住，代码是时间敏感的，并在几秒钟内到期。如果你需要更多的时间，然后在谷歌认证设置中激活“放松”模式。

## 如果谷歌验证码不起作用怎么办(安卓)？

如果你输入了 Google Authenticator 提供的正确密码、用户名和代码，但仍然无法登录你的 WordPress 网站，那么你应该尝试一下时间校正功能。Google Authenticator 应用程序生成的代码取决于您设备上的正确时间。

为此，在谷歌认证应用程序中，进入“设置>时间校正”，然后选择“代码>立即同步”。

点击“立即同步”后，您会看到一条确认消息，表明时间已经同步。您现在应该可以使用您的验证码登录了。

同步将仅影响您的 Google Authenticator 应用程序的内部时间，不会更改您设备的日期和时间设置。

您可以在[两步验证常见问题支持页面](https://support.google.com/accounts/answer/185834?hl=en)上阅读更多关于两步验证的常见问题。

此外，请确保您的移动设备和桌面上的时间是正确的。当我的安卓手机和我的电脑有时差时，我无法登录我的 WordPress 网站。

## 使用或不使用谷歌认证

我已经为我的 Gmail 帐户使用两步验证很长时间了，它一直工作得很好。我在 WordPress 上使用 Google Authenticator 已经有几个星期了，它工作得很好。

是的，有时你可能会遇到错误，你将无法登录到你的网站，但以我的经验来看，这通常是因为谷歌认证应用程序上的时间没有正确同步。

当同步正确时，Google Authenticator for WordPress 将使你的 WordPress 网站更加安全。我强烈推荐使用这样的机制，并强烈建议您永远不要在网站的安全性上妥协。

你已经在使用 WordPress 的谷歌认证器了吗？如果有，你的经历是怎样的？为了 WordPress 的安全性，你还使用了哪些插件或服务？请在下面分享你的评论。

## 分享这篇文章