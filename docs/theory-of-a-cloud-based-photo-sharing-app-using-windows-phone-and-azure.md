# 使用 Windows Phone 和 Azure 的基于云的照片共享应用的理论

> 原文：<https://www.sitepoint.com/theory-of-a-cloud-based-photo-sharing-app-using-windows-phone-and-azure/>

在接下来的几篇文章中，我们将稍微偏离主题，讨论如何将 Windows Phone 应用程序连接到云。虽然这些概念可以应用于任何云服务提供商，但出于讨论的目的，我们将重点讨论 Windows Azure。

我们要学习的例子是一个照片分享风格的应用程序 Capture TechEd，它实际上是为我在最近的 TechEd NZ 和 TechEd Australia 活动中的 Phone+Cloud 会议做准备而创建的。总之，该应用程序允许用户拍摄照片并将其分享给 Capture TechEd 应用程序的其他用户。事件的顺序如下:

*   用户使用 Capture TechEd 应用程序拍照
*   用户输入评论并将照片分配到特定的 TechEd 轨道
*   照片被上传到 Blob 存储器
*   上传的照片 URL、评论和跟踪信息被添加到队列中进行处理
*   照片出列并调整到 173×173(Windows Phone 开始磁贴的大小)
*   Toast 通知会发送给应用程序的所有用户，以将他们的开始磁贴更新为新照片。

在这篇文章中，我们将从较高的层面来介绍这些步骤，以便你理解这些概念。然后在随后的文章中，我们将详细讨论每一步，并给出实现每一步所需的代码。

### 第一步:用相机拍照

这相对来说是不言自明的——您需要使用 CameraCaptureTask 用相机拍摄照片，并将其保存到独立的存储中。

### 步骤 2:上传照片到 Blob 存储器

写入 Windows Azure Blob 存储相对简单，因为它包括对您想要存储 Blob 的 URL 的`POST`数据进行适当的`HttpWebRequest`调用。然而，为了写入 Blob 存储，你需要一个访问密钥，而不是在应用程序中打包一个访问密钥(不是一个好主意)，Windows Azure 支持一个称为共享访问签名的概念。您可以将这些视为令牌，可以分配这些令牌来授予应用程序对受保护资源的权限。对于 Capture TechEd 应用程序，我们将使用托管在 Windows Azure 中的 WCF 服务来分配限时共享访问签名，该签名将授予应用程序对 Blob 存储的写权限。
￼

[![Cloud Photo Figure 1](img/44740fe5f1a81cc86165b4dcce044f8c.png)](https://www.sitepoint.com/wp-content/uploads/2011/09/sp0014_01.jpg)

图 1

图 1 展示了手机应用程序如何首先请求一个共享访问签名，然后使用它将照片存储到 Blob 中。

### 步骤 3:上传处理作业

这里实际上有两步。第一个是从 TechEd OData 提要中检索 TechEd 曲目列表。一旦用户选择了将照片关联到哪个音轨，上传照片的 URL、评论和选择的音轨将被上传到另一个 WCF 服务，同样托管在 Windows Azure 中(图 2)。
￼

[![Cloud Photo Figure 2](img/fdfcc7206952ae8f58554c352a6363da.png)](https://www.sitepoint.com/wp-content/uploads/2011/09/sp0014_02.jpg)

图 2

### 步骤 4:将作业添加到队列中

在 WCF 服务中，上传的作业被添加到 Windows Azure 队列中(图 3)。这是一个非常快速的操作，可以防止在等待响应时客户端出现任何阻塞。

[![Cloud Photo Figure 3](img/f7cde3c939a2ce2319853abd9d0983f5.png)](https://www.sitepoint.com/wp-content/uploads/2011/09/sp0014_03.jpg)

图 3

### 第五步:图像处理

工作者角色(托管在 Windows Azure 中)从队列中选取每个作业。然后，它从 Blob 存储器中检索相应的照片，调整其大小，并将其保存回 Blob 存储器。调整后的照片的 URL、评论和音轨作为新记录被添加到 SQL Azure 中(图 4)。由于 SQL Azure 的性质，不希望任何前端应用程序直接接触它。这意味着您应该尝试使用队列、表和 Blob 存储的组合来将您的网站和服务从其中分离出来。
￼

[![Cloud Photo Figure 4](img/945ed24f4a41b4decb323a3b7667baf8.png)](https://www.sitepoint.com/wp-content/uploads/2011/09/sp0014_04.jpg)

图 4

### 步骤 6:通过通知更新起始图块

一旦记录被添加到 SQL Azure，应用程序的所有用户都会收到一个更新起始磁贴背景图像的磁贴通知。由于应用程序可能有大量的潜在用户，因此发送这些通知的过程也应该是可伸缩的，这一点很重要。为了发送单个磁贴通知，应用程序必须对与您想要向其发送通知的设备相对应的`ChannelUri`执行`Http POST`。如果您依次遍历每个注册的设备，这可能需要很长时间。相反，将所有设备添加到另一个队列中，这样您就有可能扩展工作人员角色的数量，从而优化发送通知的时间(图 5)。
￼

[![Cloud Photo Figure 5](img/844b4f1061623cc3a1ab017d7b67e971.png)](https://www.sitepoint.com/wp-content/uploads/2011/09/sp0014_05.jpg)

图 5

### 第七步:检索最近的照片

当 Capture TechEd 应用程序运行时，最新的一组照片将显示在应用程序中。在这一步中，我们将使用 OData 来呈现这些照片(图 6)。理想情况下，你会希望启用某种形式的输出缓存来提高效率，否则每个请求都会直接连接到 SQL Azure，我们已经讨论过这是一个大禁忌
￼

[![Cloud Photo Figure 6](img/300296abba7addfdf8137de0d99c99a6.png)](https://www.sitepoint.com/wp-content/uploads/2011/09/sp0014_06.jpg)

图 6

### 摘要

使用云的最大挑战之一是，您必须调整处理应用程序架构的方式。如果您查看图 7，它覆盖了该应用程序中的每个步骤，您会注意到它本质上非常像意大利面条。在这里，我们牺牲了架构的简单性(例如，直接支持 SQL Server 数据库的 WCF 服务)来换取性能。特别是手机应用程序的性能——在每个阶段，我们的目标都是减少用户完成特定任务的等待时间。
￼

[![Cloud Photo Figure 7](img/75634b9ae750eb0635815a95ed937676.png)](https://www.sitepoint.com/wp-content/uploads/2011/09/sp0014_07.jpg)

图 7

### 其他想法

最后一个要考虑的问题，实际上并没有在谈话中提到，那就是手机应用程序如何访问调整大小后的照片。作为调整大小操作的一部分，照片被上传回 Blob 存储器。然而，写入 SQL Azure 并随后被手机应用程序用来检索调整大小的照片的 URL 实际上是通过 CDN 路由的。使用 CDN 意味着手机从最近的分发点下载照片，分发点在澳大利亚的悉尼，而不是亚洲或美国的基本 Blob 存储。这可以产生明显更好的性能，除非用户不幸成为第一个访问调整大小的照片的人，因为它需要首先从 Blob 存储下载到 CDN-当然是由 CDN 自动下载！

## 分享这篇文章