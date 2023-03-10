# 用 CDN 加速你的 WordPress 站点

> 原文：<https://www.sitepoint.com/speed-up-your-wordpress-site-with-a-cdn/>

你曾经想象过通过实现一个内容交付网络(CDN)来加速你的 WordPress 网站吗？本教程演示了使用亚马逊 CloudFront 和 Rackspace 云文件(带 Akamai CDN)的 CDN 配置如何加速 WordPress 网站。在继续之前，如果你不知道 CDN，那么[这篇文章](https://www.sitepoint.com/content-delivery-networks-cdn-get-to-the-edge/)将提供 CDN 童话的快速浏览。

## 设置 W3 总缓存

 **在开始之前，你应该已经安装了 WordPress。之后，安装 [W3 总缓存插件](http://wordpress.org/extend/plugins/w3-total-cache/)。你会在你的工具条中看到一个新的标签，叫做“性能”。要配置您的 CDN，请从“常规”部分开始。有几个步骤你应该注意:-

*   缩小:勾选“启用”，选择“手动”作为缩小模式。

![](img/d570733390b4698f0ba42f5bfa552d2c.png)

*   CDN:选中“Enable”，暂时将 CDN 类型设置为自托管(稍后我们会将其更改为 Amazon/Rackspace)。

![](img/a37ea6bba6f5ec1d4f08b5edd64e27ad.png)

点击“保存所有设置”按钮，进入“缩小”部分。现在采取以下行动:

*   常规:启用“重写 URL 结构”。
*   HTML & XML:启用“HTML 缩小设置”。
*   JS:启用“JS minify 设置”，勾选截图中显示的复选框，并添加您的。JS 文件(您可以在前端的源代码中搜索这些文件)。
*   只使用模板文件，没有 Adsense 或分析代码或类似的东西。如果需要脚本来加载页面，请在嵌入位置下拉列表中使用“阻止”，否则请使用“非阻止”。

![](img/87642d5e6bc8d296fee446927af63088.png)

*   CSS:启用“CSS minify 设置”，再次勾选截图中的复选框。添加您的 CSS 文件(您可以通过与。JS 文件)。

![](img/ae8ba3a1698a16591766edfcce8d54cb.png)

之后，再次保存您的所有设置，并继续到 CDN 部分。在这里，您需要采取以下步骤:

*   常规:选中从“主机附件”到“导入外部媒体库附件”的所有框，以完全控制内容。
*   配置:暂时不要管这些，这是你填写 Amazon 或 Rackspace 详细信息的地方。
*   高级:选中前三个框，最后一个将 cookie 域设置为“domainname.com”。

![](img/8ac402f4e7e71fec7fcfca7ce42b7f42.png)

保存所有设置。您将在 W3 总缓存设置屏幕的顶部看到一些红色和黄色消息。我们将稍后处理这些问题，因为首先您需要设置 Amazon CloudFront 或 Rackspace Cloud 文件作为您的 CDN。**将 Amazon CloudFront 设置为您的 CDN** 让我们从 Amazon CloudFront 开始。如果你还没有注册，注册亚马逊网络服务并登录你的账户。登录后，你应该为 S3 和 CloudFront 分别注册。现在回到你的 WordPress 安装，打开 W3 Total Cache 的“General”部分，在你第一次选择“Self-hosted”的地方，现在选择“Origin Pull”下的“Amazon CloudFront”(这意味着 CDN 将从你的站点提取必要的文件)。单击“保存所有设置”。

![](img/a80126b72b6590a8ff3440bb2b3bc335.png)

要让它工作，请采取以下步骤:

*   再次打开 CDN 部分，并滚动到“配置”。填写您的“访问密钥 ID”和“密钥”。这些密钥可以在亚马逊 AWS 门户的“我的账户”中找到，然后选择“安全凭证”。

| ![](img/75931458c526087c25f618ef306b36c0.png) | ![](img/a5bb6be671134d6738a3bf5a389c86b9.png) |

*   在“访问凭证”中，您将找到您的“访问密钥 ID”和(在您单击“显示”后)您的“秘密访问密钥”。现在把这些复制到你的 WordPress CDN 配置中。

![](img/42b331c375f444ea562fe467fc1de8c2.png)

*   单击“创建发行版”，如果您的凭证正确，您将看到以下通知:

![](img/5718c2875646a0b251f3d55f36ae9c39.png)

*   现在，您将看到一个新的主机名被填入，看起来类似于:

![](img/a703a4a35001eb6859bd8088b9566d0f.png)

*   等到您的 CloudFront 发行版部署完毕，这可以在 Amazon 的 AWS 管理控制台的 CloudFront 部分进行验证。

| ![](img/ecd11300bc9ca64e77a6c1b758ae32d5.png) | ![](img/a29774363b09ab6818eca21578d3383b.png) |
| ![](img/4ee1ede08507a41898422d6ba4ad700b.png) | ![](img/1c1b31805a17c322201dd064de6488aa.png) |
| “进行中”时，您必须等待。 | 一旦你看到这个，你就可以走了。 |

*   为了确保这一点，使用“Test CloudFront distribution”按钮进行测试。
*   您的文件会被自动拉入 CloudFront CDN(当您的发行版处于“进行中”时会发生这种情况)。分发“进行中”后，将附件导入媒体库。在 CDN 部分的顶部有一个按钮可以做到这一点，称为“将附件导入媒体库”。按下此按钮，并在随后的弹出窗口中单击“开始”。上传完成后，关闭并保存配置。
*   要想知道它是否真的在工作，请退出 WordPress 并再次打开网站。在源代码中，您应该会看到这样的行:

| ![](img/aa73749f80466b6ca38137ad4d33703b.png) |
| *这是你的缩小的 CSS 文件，从 CloudFront 加载* |
| ![](img/23cab11c0aaed2514b1d31ab2805b0f0.png) |
| 这是从 CloudFront 加载的标题图像。 |

## **将 Rackspace 云文件设置为您的 CDN**

使用 W3 Total Cache，您还可以选择 Rackspace 云文件，Akamai 是您的 CDN 提供商。首先，确保您有一个 Rackspace 云帐户。之后，在 W3 Total Cache 的“General”部分，滚动到 CDN 配置，并选择“Rackspace Cloud Files”作为 CDN 类型。你只能选择“源推送”，这意味着你必须自己上传文件(插件在这方面帮助你)。

![](img/2d1e348bca0bf94c98639b3296a970ab.png)

点击“保存所有设置”，并执行以下操作:

*   转到 CDN 部分，并滚动到配置。
*   填写您的 Rackspace Cloud 用户名(在注册时选择)。
*   插入您的 API 密钥。您可以通过在 Rackspace 登录到“Rackspace 开放云”，并在登录后选择“API 密钥”来检索它。

| ![](img/45515f26e4cc84922787dbb2c44003a6.png) | ![](img/91442a67202f132da6a366e1b4d99e1a.png) |

*   如果你还没有一个 API 密匙，那就做一个新的。点击“显示密钥”，并复制它。

![](img/36ea0566c562bc9d4c85cc200d0f0920.png)

*   现在回到你的 WordPress 安装，将 API 密钥插入你的 CDN 配置 API 密钥字段。
*   将位置保持为“US”(您可以将其更改为 UK，但这无关紧要)。
*   填写容器名称(在您的帐户中应该是唯一的名称)，然后单击“创建容器”。如果你做得好，你会看到这个:
*   为了确保安全，请点击“测试云文件上传”,并确保您通过测试。

![](img/4ba27cedafe15e63efa58648335ad735.png)

*   点击“保存所有设置按钮”。
*   现在滚动到顶部，你会看到一大堆通知。

![](img/5005f685126ca990b727e1ff1176ecec.png)

*   你基本上必须全部点击，从“导出媒体库”开始，然后上传所有文件，清空页面缓存和缩小缓存。
*   就是这样！插件已经确保你的文件的 CDN 功能已经打开。为了确保它能正常工作，请在退出 WordPress 后再次检查，并在源代码中查找类似这样的内容:

| ![](img/160e93fde58ac2e94e6cc2a4b27dd770.png) |
| *您从 Rackspace 云文件加载的缩小的 CSS 文件* |
| ![](img/bcf45950c74dbf651ec5d0f19b0b0946.png) |
| *您从 Rackspace 云文件加载的图像* |

*   您也可以随时登录到您的 Rackspace 云帐户的“文件”中，并打开您的容器。

![](img/3480c0c15f90252c5443908ca4ca8372.png)

## **结论**

正如你所看到的，为你的 WordPress 博客建立一个 CDN 是相当简单的。W3 总缓存使它非常容易和快速，因此您的网站可以加载得更快。

## 分享这篇文章**