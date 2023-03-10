# 微软紧跟 OpenID 潮流

> 原文：<https://www.sitepoint.com/microsoft-jumps-on-openid-bandwagon/>

![](img/ceab89954047185b0c6a9704377dcf71.png "openid-logo") *现在*OpenID 终于主流了吗？微软[今天](http://dev.live.com/blogs/devlive/archive/2008/10/27/421.aspx)宣布，它将使所有 Windows Live ID 账户——约有 4.2 亿多用户——能够登录 OpenID 网站。微软宣布推出 Windows Live ID OpenID 提供程序的社区技术预览版(他们称之为测试版)。

微软在一篇博客声明中高度赞扬了 OpenID。微软的桂鑫·特林说，“OpenID 是一个新兴的，事实上的用户认证标准网络协议。”

微软希望在 2009 年的某个时候推出对 OpenID 的全面支持。比如雅虎！，他在去年 1 月宣布支持 OpenID，微软将成为 OpenID 提供商，但它似乎不会成为“依赖方”——换句话说，似乎很可能你将无法使用从其他地方获得的 OpenID 登录到微软属性。

这有点令人失望，因为只有当提供用户服务的提供商也成为依赖方时，OpenID 才能完全发挥作用。一切都需要双向的。

## OpenID 越来越大，但仍然不是主流

在微软和雅虎的支持下！现在可能有超过 7.5 亿的 OpenID 登录。肯定有重叠(我既有 Windows Live ID 又有 Yahoo！ID，例如，我相信很多人都这样做)，但即使如此，微软和雅虎！是网络上最大的两个在线 ID 提供商，所以这是 OpenID 的重大胜利。

但是 OpenID 仍然不是主流。作为一个雅虎！用户体验研究[显示，几周前](https://www.sitepoint.com/yahoo-mainstream-has-no-idea-what-openid-is/)，OpenID 还没有很好地销售给公众。新增 4.2 亿个 OpenID 账户并不能解决营销问题。

举个例子，微软的指令测试了 OpenID Provider 程序的当前技术预览版，用于 Live ID:

1.  前往[https://login.live-int.com/](https://login.live-int.com/)并使用注册按钮在 INT 环境中设置一个 Windows Live ID 测试帐户。
2.  前往[https://login.live-int.com/beta/ManageOpenID.srf](https://login.live-int.com/beta/ManageOpenID.srf)设置您的 OpenID 测试别名。
3.  在任何支持 OpenID 2.0 的网站上，在 OpenID 登录框中键入**openid.live-INT.com**，通过您的 Windows Live ID OpenID 别名登录该网站。

当然，一旦微软的支持在明年某个时候投入生产，打开 OpenID 将变得更加容易，但即使如此，在用户端使用 OpenID 的惯例对许多用户来说还是太陌生了。大多数 OpenID 登录框如下图所示:

![](img/7a54018ae9cf5e4e5b444735d9a447b2.png "openid-login")

这不是大多数人习惯的那种签名形式。两个字段“用户名”和“密码”的方法在用户心中根深蒂固，以至于许多人在看到 OpenID 登录表单时感到困惑，不知道如何进行。雅虎的用户！的测试证实了这一点，许多人报告说，当他们没有看到他们习惯的密码框时，他们感到困惑。

“关键要点[来自雅虎！我们在本月早些时候写道:“即使 OpenID 已经为主流做好了准备，主流似乎还没有为 OpenID 做好准备。”它肯定会从简化中受益(就注册和登录而言)，但普通用户开始采用 OpenID 需要做的主要事情是，它需要以一种完全不同的方式进行推介。"

OpenID 基金会董事会成员 David Recordon[在我们帖子的评论](https://www.sitepoint.com/yahoo-mainstream-has-no-idea-what-openid-is/#comment-809549)中指出，OpenID 仍然处于传统采用曲线的早期采用者阶段。因此，只看 OpenID 账户的数量是有误导性的——主流还没有使用 OpenID。

正如我们几周前所写的，OpenID 从根本上来说是一个不错的想法，但是对于许多用户来说，它的实现远非理想，而且它的卖点也很糟糕。此外，如果没有像微软和雅虎这样的大型 OpenID 提供商。同样成为依赖方，用户的主要利益——你可以使用你的单一 OpenID 登录到你使用的所有网站——也失去了。雅虎！建议出版商“推广 OpenID 的实用性，而不是技术”。然而，当失去效用时，向主流推销就变得不可能了。

## 分享这篇文章