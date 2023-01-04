# 云切片:混合云的 Eucalyptus

> 原文：<https://www.sitepoint.com/cloud-slice-eucalyptus-for-hybrid-clouds/>

## 桉树是什么？

Eucalyptus 是一个平台，可以让您使用现有的硬件来构建内部基础架构即服务(IAAS)。您不需要购买特殊的硬件来构建私有云。Eucalyptus 是用类似于 Amazon Web Services 等公共云提供商的 API 构建的，因此您可以轻松集成私有云和公共云。

## 那交易是什么？

让我们快速总结一下桉树的一些主要特征

*   您可以利用对各种硬件的现有投资来构建 IaaS，从而为您提供计算和存储服务。自助服务控制台使您能够调配和配置计算、存储和其他设备
*   您可以与 AWS 等公共云无缝集成，未来将支持更多平台。您可以构建高可用性，并提供热故障转移和修复
*   您可以从 vSphere、ESX、KVM 和 XEN 管理多个虚拟机管理程序集群
*   Eucalyptus 与 SAN 集成得很好，可以按需分配和取消分配存储。借助 EBS 之类的存储，您可以保存虚拟机，包括状态，以便更快地启动。

## 公共云集成

在撰写本文时，Eucalyptus 提供了与一些众所周知的 Amazon Web Services (AWS)特性的无缝集成，如 EC2、EBS、S3 等。

## 如何试驾桉树？

[FastStart](http://go.eucalyptus.com/Download-FastStart) 是 Eucalyptus 的预配置安装，可以在您的硬件上运行。您可以通过订阅来试驾 [Eucalyptus，在那里您有更多的虚拟机管理程序选择。你也可以下载源代码，在这里玩桉树](http://go.eucalyptus.com/Test-Drive-with-Eucalyptus-Support.html)[游戏](http://www.eucalyptus.com/download/eucalyptus)

## 分享这篇文章