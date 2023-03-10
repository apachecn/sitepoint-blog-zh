# 云切片:Redhat OpenShift

> 原文：<https://www.sitepoint.com/cloud-slice-redhat-openshift/>

## 什么是 Redhat OpenShift

OpenShift 是 Redhat 提供的平台即服务(PaaS)。它为您提供了一个部署、扩展和管理应用程序的环境，而您作为开发人员可以专注于构建应用程序。OpenShift 已经内置了对 PHP、Ruby、Java、node.js、Perl 的支持，并且可以在支持 cartridge 的情况下进行扩展，以托管 clojure 和其他平台。

## 它是如何工作的？

对于开发人员，您可以通过 web 控制台或命令行创建应用程序。我会提到命令行，因为它将符合我们的下一步。对于命令行交互，您需要 GitHub gems 和名为“rhc”的 openshift gem。成功安装后，您可以使用“rhc setup”命令登录。可以使用“rhc app create”命令和基于您正在创建的应用程序类型的适当参数来创建应用程序。你可以用你最喜欢的 IDE 在本地修改创建的应用程序。您可以添加应用程序所需的服务，这些服务称为“墨盒”。例如，要添加 MongoDB，您可以使用命令“rhc app cartridge add-a twt-c MongoDB-2.0”。现在，您的应用程序实际上是“git push”了！

## 幕后发生了什么

Redhat 是开源领域的先驱和领导者，毫无疑问，他们使用自己强大的技术来支持 openShift。Red Hat Enterprise Linux (RHEL)构成了 OpenShift 的基础，se Linux 用于增强安全性和多个应用程序之间的隔离。默认情况下，OpenShift 会自动伸缩，因此当应用程序负载增加时，您不必担心。如果愿意，用户可以选择手动缩放。对于建筑爱好者来说，这个链接详细解释了这个建筑。

## 使用和扩展！

OpenShift 支持 Java、Ruby、Perl、PHP 等。但是，如果您有开箱不支持的东西，也不用担心。任何可以在 RHEL 6.2 X64 上运行并能与 HTTP 对话的二进制文件都可以通过自己创建一个插件来配置，如这里的[所解释的](https://openshift.redhat.com/community/developers/do-it-yourself)。OpenShift 看起来是一个很有前途的平台，拥有 Linux 的强大功能和 Redhat 的开源技术！

## 分享这篇文章