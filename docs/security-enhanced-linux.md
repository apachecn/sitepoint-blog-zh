# 安全性增强的 Linux

> 原文：<https://www.sitepoint.com/security-enhanced-linux/>

操作系统安全性对我们所有人来说都是至关重要的。然而，每个系统管理员所需的不同安全级别是不同的。

对于那些寻求增强、加强 Linux 系统安全控制的人来说，SELinux 可能是答案。它代表安全增强的 Linux，是美国 NSA(国家安全局)研究项目的成果，专注于强制访问控制，对用户和设备以及应用程序和服务提供强大的控制。

SELinux 是作为一组内核补丁发布的，这些补丁包含在现有的 Linux 安装中。国家安全局声称他们只在红帽上成功测试过。

同样，Red Hat 社区刚刚宣布将 SELinux 集成到其最新的 Fedora (core 2)测试版本中，该版本是 Red Hat 专业系列发行版的替代品，后者在版本 9 中终止。Red Hat 促进了 Fedora 项目，但没有正式支持它。然而，很明显，我们的目标是测试并找到最佳的改进，然后将其应用到 Red Hat 的官方企业 Linux 产品中。

NSA 定义了 SELinux 安全性和标准 Linux 安全性之间的区别:

“安全性增强的 Linux 内核强制执行强制访问控制策略，将用户程序和系统服务器限制在完成工作所需的最低权限范围内。当以这种方式限制时，这些用户程序和系统守护程序在受到危害时(例如，通过缓冲区溢出或错误配置)造成损害的能力就会降低或消除。这种限制机制独立于传统的 Linux 访问控制机制运行。它没有“根”超级用户的概念，也没有传统 Linux 安全机制的众所周知的缺点(比如对 setuid/setgid 二进制文件的依赖)。”

更多信息:
[安全性增强的 Linux](http://www.nsa.gov/selinux/)

[红帽 Fedora 和 SELinux](http://people.redhat.com/kwade/fedora-docs/selinux-faq-en/)

## 分享这篇文章