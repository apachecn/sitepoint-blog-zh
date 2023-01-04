# 使用 Sudo 管理管理员

> 原文：<https://www.sitepoint.com/using-sudo-to-manage-administrators/>

当多个用户负责管理一台或多台服务器的各个部分时，sudo 命令和功能(超级用户 do)是一个很有价值的工具。特别是，当您需要限制“root”的使用和/或需要记录所有管理操作和更改，并且希望记录谁做了什么时，sudo 非常重要。

我对 sudo 的主要偏好是它的过期会话(或者 GratiSoft 称之为票证)。一旦切换到 sudo 进行活动，会话将在五分钟后过期，或者在执行命令时以五分钟(可以修改)的间隔继续。如果管理员因为根外壳没有保持打开而暂时离开他或她的工作站，这允许增加安全性。

GratiSoft 在 sudo 上维护着一个优秀的网站(并赞助 sudo 的开发)——(【http://www.courtesan.com/sudo/sudo.html】T2)。

通过构建一个配置文件，高级管理员可以通过 sudo 分配系统管理职责，根据每个主机的用户名分配可以执行的命令。此外，由于是在多主机环境中执行的，审计跟踪日志记录是在一个集中的主机上完成的，并且每个系统的本地主机日志也是如此。

GratiSoft 正确地指出，更熟悉 sudo 的一种方法是查看一个示例配置。他们[在这里](http://www.courtesan.com/sudo/sample.sudoers)贴了一个这样的文件。

由于 sudo 支持众多平台，这也适用于混合操作系统环境——参见这里的[支持的平台](http://www.courtesan.com/sudo/runson.html)。

## 分享这篇文章