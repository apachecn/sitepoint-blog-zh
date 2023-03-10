# 用 SSL 保护您的 Apache 2 服务器

> 原文：<https://www.sitepoint.com/securing-apache-2-server-ssl/>

对于那些刚接触安全套接字层(SSL)证书的人来说，保护 Apache 2 Web 服务器的安全可能是一个令人生畏的前景。然而，情况不必如此。SSL 保护 Web 服务器到 Web 浏览器的连接。继续阅读以更好地理解 SSL 证书，了解如何在 Apache 上设置它们，并启动您的启用 SSL 的站点。

在今天的网站托管环境中，许多网站管理员托管在具有现成的 SSL 设置和无安装要求的服务器上，或者具有控制面板处理繁重的配置工作的设置。无论是哪种情况，了解您的 SSL 选项以及知道如何手动管理这个过程都很重要。

*Apache 1 . x 用户注意事项*

虽然本教程专门介绍了 Apache 2，但是它和 1.x 版本的 Apache 没有太大区别。在版本 2 之前，由于出口和加密法规要求，Apache 中没有内置 SSL 支持。然而， [apache-ssl](http://www.apache-ssl.org) 和 [mod_ssl](http://www.mod_ssl.org) 都适用于 1.x 版本的 apache。本教程中的大部分 openssl 和 httpd.conf 内容涵盖了这两个版本。

##### 证书颁发机构和可信根

在我们深入研究 SSL 证书类型以及如何购买和安装证书之前，让我们简要回顾一下 SSL 证书是如何创建和管理的。

SSL 要获得广泛的商业采用，某些环境是必要的。在用户愿意通过互联网传输机密信息之前，必须建立“信任”。于是，认证机构(ca)诞生了。为了确保构建当今流行的 Web 浏览器的软件公司将包括对这些 ca 的支持，一些公司获得了其可信根证书的所有权，这些证书产生了出售给用户用于保护网站的 SSL 证书。这些公司被称为可信 ca；例子包括 GeoTrust、Thawte 和 VeriSign。

根据来自 [SSL Review](http://www.sslreview.com) 的代表的说法，控制自己的可信根证书的可信 ca 是 SSL 市场的永久参与者，为企业和中小型企业提供最高级别的业务稳定性。

SSL Review 是一个致力于揭开 SSL 证书神秘面纱的网站，虽然归一家主要的 SSL 提供商 GeoTrust 所有，但该网站相当公正。它包括一个出色的 SSL 比较图表，该图表在之前的[开源库](https://www.sitepoint.com/blog/)中有所介绍。

##### 我应该选择什么类型的证书？

有许多供应商提供各种 SSL 证书。证书的价格差别很大，从高达 1495 美元到免费，这取决于您计划使用证书的方式以及供应商提供的服务和支持。让我们来看看几个选项。

证书至少从 40 位加密开始。这些证书对于保护不通过其进行电子商务交易的外联网、内联网和网站是有用的。它们还包括 128 位加密，这是用户和网络服务器之间双向加密交易的事实标准。

***在非商业环境中保护数据***

如果您希望通过提供加密连接来保护数据，通过该连接可以对用户进行身份验证、修改记录或查看个人数据，那么低端证书可能有效。

这类证书每年的费用应该在 50 美元以下，包括可从可信认证机构 [ipsCA](http://certificates.ipsca.com/Products/SSLServer.asp) 和 FreeSSL.com[获得的证书。由于这些证书没有已知的品牌、欺诈控制或人工支持，因此它们适用于非商业或轻度商业环境，尽管它们确实提供额外的 128 位加密。根据 FreeSSL.com 的说法，轻松的商务环境是指每周不超过 50 笔交易，平均交易额为 50 美元。](http://www.freessl.com)

***通配符证书——用一个证书保护多个域***

可能存在需要保护一个域下的多个子域的情况。例如，通过一个通配符 SSL 证书，可以在一个证书下保护 www.domain.com、orders.domain.com 和 service.domain.com。

由于您可以保护无限数量的主机名，通配符证书通常比普通证书更昂贵，但对于需要保护多个域的人来说，它们变得非常划算。这类证书的价格从 299 美元到 800 美元不等，可从 FreeSSL.com、ipsCA、GeoTrust 等公司购买。

 *如果您要保护一个与商务相关活动的网站，会出现许多问题，包括浏览器兼容性、欺诈防范以及在您的经济和技术能力范围内部署最高级别的安全性。

每个受信任的 CA 都开发了自己的功能、优势和平台，以支持其对您的 SSL 业务的竞标；然而，有些提供了比其他更全面的解决方案。

当寻找电子商务 SSL 时，考虑以下问题:

*   受信任的 CA 是否为您的用户提供了一种方法，使他们可以通过 Web 浏览器验证或确认您的 SSL 证书？
*   如果您的证书被盗、损坏、冒名顶替，或者您无法使用，是否有保修保护？
*   如果在证书上投入额外的资金，会增加什么样的额外好处——如延期更新和处理交易的支付服务？

电子商务证书的价格从每年 199 美元到 1495 美元不等。它们可以从所有主要的可信 CA 提供商处获得，包括 GeoTrust、Thawte 和 VeriSign。

***通过自签名证书测试 SSL/***

您的 Apache Web 服务器已经准备好提供自签名 SSL 证书。这些证书便于测试，当然也是免费的。在大多数情况下，它们会为您的用户生成警报消息，因为它们不是从受信任的 CA 根证书生成的，并且不提供任何第三方证据来验证您站点的身份。因此，这些证书最适合内部使用或在开发和测试期间使用。

##### Apache 上的 SSL 入门

Apache 需要使用一些关键要素来保护您的 Web 服务器:OpenSSL、mod_ssl 和对服务器的 root 访问。

OpenSSL 是一个在服务器上使用安全套接字层加密的命令行工具包，可以从 openssl.org T2 获得。这个工具与 Apache 模块 mod_ssl 一起执行 ssl 相关的任务。您将需要 root 权限来将 OpenSSL 安装到它的传统目的地`/usr/local/ssl/install/openssl`。

您还必须确保 mod_ssl 在您的服务器上可用。mod_ssl 还有其他替代方案；一个是 apache-ssl，mod_ssl 代码就是从它派生出来的。然而，mod_ssl 的采用是引人注目的——在 2002 年初，将近 20%的 Apache 服务器运行它。

要查看 Apache 中哪些模块是活动的，在服务器上以 root 用户身份在终端中发出以下命令。

```
/usr/sbin/httpd -l
```

如果您安装了最新的 Linux 发行版，Apache 的模块很可能被编译为动态可加载模块，在这种情况下，您需要编辑 httpd.conf 文件，并检查以下代码行是否未被注释。

```
LoadModule ssl_module modules/libmodssl.so
```

重新启动 Apache 将加载该模块。在我的例子中，已经使用了 Red Hat 和 Apache 的基于 RPM 的安装，这是通过 apachectl 命令实现的，通常可以在`/usr/sbin/apachectl`中找到。您可以通过键入以下命令来重新启动 Apache:

```
/usr/sbin/apachectl restart
```

apachectl 有几个有用的特性，包括停止、启动、重启、状态和检查配置。请通过 man apachectl 查看手册页。

注意，在最近的 Apache 发行版中，httpd.conf 文件包含一个`<IfDefine HAVE_SSL>`部分，该部分旨在包含所有 SSL 网站的`<VirtualHost>`定义。通过将这些定义放在`<IfDefine>`部分，您可以确保除非服务器上成功加载了 SSL 支持，否则这些站点将不可用。这可以防止 SSL 暴露您的安全站点的任何问题。

##### 创建本地密钥对

如果您还没有这样做，那么您的第一步应该是创建一个本地私有/公共密钥，您可以从该密钥生成证书请求。然后，这些可以用于自签名证书，或者从 CA 购买证书。

OpenSSL 允许我们使用命令行来生成密钥。您可以选择使用强加密和密码来保护您的私钥，如下所示。

```
openssl genrsa -des3 -out domainname.com.key 1024
```

在命令行中键入以上内容将使用 TripleDES 加密创建一个私钥，1024 是密钥中生成的位数。可以选择较低的加密级别，也可以不需要密码，但是，对于那些拥有可通过互联网访问的服务器的用户，不建议使用这些选项。

注意，如果 OpenSSL 不在您的路径中，您可能需要为此输入二进制文件的完整路径；默认是`/usr/local/ssl/install/openssl/bin/openssl`。该密钥将在您所在的目录中创建。

最后，您应该修改权限，通过在命令行上发出 chmod 400 domainname.com.key 来限制对新密钥的访问。这确保了只有 root 用户可以访问该文件，并且仍然需要您用来创建密钥的密码才能打开。

##### 创建自签名证书

这是一个非常简单的步骤，但是请记住，自签名证书应该只用于内部系统或者开发和测试。

通过返回命令行并输入以下内容来生成您自己的证书:

```
openssl req -new -key domainname.com.key -x509 -out sslname.crt
```

这个命令创建一个自签名的 SSL 证书，然后可以将它放在正确的目录中。然后可以在 httpd.conf 中进行适当的修改(我们稍后会谈到这些)。

##### 生成证书签名请求

要从 CA 购买 SSL 证书，首先需要生成所谓的 CSR 或证书签名请求。这将提交到您选择的 CA，并用于创建将返回给您的官方 SSL 证书，您可以用它来保护您的 Web 服务器。

企业社会责任的要求很简单；它在 OpenSSL 的命令行上实现，如下所示:

```
/usr/local/ssl/install/bin/openssl req -new -key domainname.com.key -out domainname.com.csr
```

此命令创建。在订购 SSL 证书的过程中发送或上传到 CA 的 csr 文件。

##### 接收和安装您的 SSL 证书

通常，ca 会提供安装其 SSL 证书的详细说明；然而，我将在这里讨论一些要点。

您订购证书的 CA 将通过电子邮件向您发送证书或下载链接。严格按照提供的说明进行操作，尤其是在文本编辑器中打开证书时。不要使用文字处理器或富文本编辑器，因为证书代码可能会损坏。您还应该注意确保证书代码的开头和结尾没有前导空格或尾随空格，请参见下面的代码示例:

```
-----BEGIN CERTIFICATE----- 

MIIC8DCCAlmgAwIBAgIBEDANBgkqhkiG9w0BAQQFADCBxDELMAkGA1UEBhMCWkEx 

FTATBgNVBAgTDFdlc3Rlcm4gQ2FwZTESMBAGA1UEBxMJQ2FwZSBUb3duMR0wGwYD 

VQQKExRUaGF3dGUgQ29uc3VsdGluZyBjYzEoMCYGA1UECxMfQ2VydGlmaWNhdGlv 

biBTZXJ2aWNlcyBEaXZpc2lvbjEZMBcGA1UEAxMQVGhhd3RlIFNlcnZlciBDQTEm 

MCQGCSqGSIb3DQEJARYXc2VydmVyLWNlcnRzQHRoYXd0ZS5jb20wHhcNOTkwNTI1 

MDMwMDAwWhcNMDIwNjEwMDMwMDAwWjBTMQswCQYDVQQGEwJVUzEbMBkGA1UEChMS 

RXF1aWZheCBTZWN1cmUgSW5jMScwJQYDVQQDEx5FcXVpZmF4IFNlY3VyZSBFLUJ1 

c2luZXNzIENBLTIwgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAMYna8GjS9mG 

q4Cb8L0VwDBMZ+ztPI05urQb8F0t1Dp4I3gOFUs2WZJJv9Y1zCFwQbQbfJuBuXmZ 

QKIZJOw3jwPbfcvoTyqQhM0Yyb1YzgM2ghuv8Zz/+LYrjBo2yrmf86zvMhDVOD7z 

dhDzyTxCh5F6+K6Mcmmar+ncFMmIum2bAgMBAAGjYjBgMBIGA1UdEwEB/wQIMAYB 

Af8CAQAwSgYDVR0lBEMwQQYIKwYBBQUHAwEGCCsGAQUFBwMDBgorBgEEAYI3CgMD 

BglghkgBhvhCBAEGCCsGAQUFBwMIBgorBgEEAYI3CgMCMA0GCSqGSIb3DQEBBAUA 

A4GBALIfbC0RQ9g4Zxf/Y8IA2jWm8Tt+jvFWPt5wT3n5k0orRAvbmTROVPHGSLw7 

oMNeapH1eRG5yn+erwqYazcoFXJ6AsIC5WUjAnClsSrHBCAnEn6rDU080F38xIQ3 

j1FBvwMOxAq/JR5eZZcBHlSpJad88Twfd7E+0fQcqgk+nnjH 

-----END CERTIFICATE-----
```

使用基于 RPM 的安装默认值，我的私钥和从 CA 返回的有效 SSL 证书分别进入`/etc/httpd/conf/ssl.key/`和`/etc/httpd/conf/ssl.crt/`。您的自签名证书也应该位于后一个目录中，以代替或补充由 CA 颁发的正式 SSL 证书。

这将取决于您的 Apache 版本的安装方式。VeriSign 在其网站上提供了一个覆盖众多网络服务器的安装指南索引，包括 Apache。Apache mod_ssl 教程主要基于 modssl.org 网站上的信息。

在 httpd.conf 文件中，配置 Apache 为您要保护的域启用 SSL。首先，备份文件。然后，在您喜欢的文本编辑器中打开它。

您可以将您正在保护的虚拟主机域添加到上面提到的`<IfDefine HAVE_SSL>`部分。下面列出了一个直接来自默认 httpd.conf 文件的最小示例条目，供您参考。您应该修改路径和 IP 地址等项目，以适应您自己的环境。SSL 端口是 443，除非您专门将端口调整为另一个端口号(SSL 配置在下面以粗体显示):

```
**<IfDefine HAVE_SSL>** 

<VirtualHost 10.0.0.5:443> 

DocumentRoot /home/sites/domainname.com/html 

ServerName www.domainname.com 

ServerAlias domainname.com 

ServerAdmin admin@domainname.com 

ErrorLog /home/sites/domainname.com/logs/error_log 

TransferLog / home/sites/domainname.com/logs/access_log 

**SSLEngine on 

SSLCertificateFile /etc/httpd/conf/ssl.crt/domainname.com.crt 

SSLCertificateKeyFile /etc/httpd/conf/ssl.key/domainname.com.key 

SetEnvIf User-Agent ".*MSIE.*" nokeepalive ssl-unclean-shutdown** 

</VirtualHost> 

**</IfDefine>**
```

请注意，如果您执行服务器的硬启动，您将需要密码来访问您之前创建的 OpenSSL 私钥。重新启动时不会出现提示。

现在，您必须使用 apachectl 命令重新启动 Apache，以确保您的所有修改都已启用。然后，您应该能够通过访问[https://domainname.com/](https://domainname.com/)安全地访问您的网站。

##### 总结，附加功能和工具

正如您所看到的，虽然启动和运行 SSL 需要一些研究、规划和工作，但这并不是一项不可完成的任务。在接下来的几周里，我将在 Open Sourcery 上发表一些与 SSL 相关的主题，比如配置 Web 日志文件来记录您站点安全部分的 SSL 活动，以及一些有趣的 SSL 应用工具。敬请期待！* 

## *分享这篇文章*