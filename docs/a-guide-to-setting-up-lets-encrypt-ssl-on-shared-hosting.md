# 设置指南让我们在共享主机上加密 SSL

> 原文：<https://www.sitepoint.com/a-guide-to-setting-up-lets-encrypt-ssl-on-shared-hosting/>

最近几年，谷歌和其他公司通过鼓励在每个网站上使用 TLS/SSL(传输层安全/安全套接字层)来加强网络的安全性。

![padlock image](img/db61d31863303e64914c15c91f8eb94f.png)

谷歌通过提高网站的排名进一步鼓励使用 HTTPS，并且像其他浏览器制造商一样，通过在没有加密的网站上引入越来越多的警告，逐渐加大了 Chrome 的热度。

直到最近，SSL 证书还相当昂贵。[让我们加密](https://letsencrypt.org/)通过提供免费证书改变了这一点。

有很多在线教程展示如何安装 Let's Encrypt certificates，但它们往往需要相当多的技术专长(如何操作 Linux web 服务器和管理 root 访问的知识)，很少指导你如何通过一个通用的**共享主机**控制面板来安装它。如果你的 cPanel 或其他控制面板实例不提供像*一键安装让我们加密 SSL* 这样的功能(很多都不提供)，那么你可能会觉得别无选择，只能通过你的虚拟主机提供商花钱买一个证书。

然而，在这篇文章中，我将向你展示如何使用来自 [SSL 的免费服务](https://www.sslforfree.com/)在你的共享虚拟主机服务器上安装一个加密 SSL 证书。SSL 免费允许您安装一个让我们加密 SSL 证书，而不必有根访问和 VPS，但只是一个普通的共享虚拟主机服务器。

* * *

*想让您的 SSL 技术更上一层楼吗？看看尼克·詹纳基斯的这个精彩课程:*

![](img/0a8420a9a37449d523155b391746c790.png)[The Best Way to Secure Your Site with HTTPS](https://httpswithletsencrypt.com/ref-sp)Nick JanetakisEverything you need to take your site from insecure to an A+ rated secure site in just 3 hours. SitePoint fans get it for 40% off.[](https://httpswithletsencrypt.com/ref-sp)[](https://httpswithletsencrypt.com/ref-sp)

* * *

## 安装让我们免费使用 SSL 加密 SSL

**第一步:**前往免费的[SSL](https://www.sslforfree.com/)网站。

**第二步:**在**输入您的网站安全**输入框中，输入您的网站域名地址(例如:`yourdomain.com`)。

![SSL for Free](img/0560b5cfbea7c17fdf08acf37a2cfbef.png)

**第三步:** SSL 免费会自动为`yourdomain.com`和`www.yourdomain.com`提供 SSL 证书。

**第四步:**如果您想添加另一个子域(例如:`sub.yourdomain.com`，点击**添加/编辑域名**链接，您将返回到域名地址输入页面。

**第五步:**在输入框中添加你想要的子域。

### 通过 SSL 免费验证您的域名和/或子域

有三种方法可以免费通过 SSL 验证您的域(您可以选择其中任何一种)。

1.  第一种是通过**自动 FTP 验证**。在这里，你会被要求提供关于你的虚拟主机 FTP 帐号的数据。
2.  第二种是通过**手动验证**。系统会提示您从 SSL 免费下载两个文件，稍后您会将它们上传到您的虚拟主机服务器。
3.  第三种是通过**手动验证**(DNS)。这样，你会被提示通过你的虚拟主机控制面板创建一个新的 TXT 记录。

让我们来看看每个验证步骤。

### 方法一。自动 FTP 验证

**第一步:**点击**自动 FTP 验证**按钮。

![Automatic FTP verification](img/41b74ff8192df49f770751bd099b0bb3.png)

第二步:接下来，你会被提示从你的虚拟主机 FTP 账户输入数据。完整输入数据:

![Entering FTP data](img/00fc2660bc867e8a2eed51ee798d520f.png)

如果你觉得这种方式不舒服(给你的 FTP 账号访问数据)，那么你可以选择下面的第二种方式(我强烈推荐)。

### 方法二。人工验证

**步骤 1:** 你将被提示免费下载两个由 SSL 生成的文件，然后你将它们上传到你的虚拟主机服务器。点击**手动验证**。(**注意:**不要关闭这个标签页/页面！)

**第二步:**下面会提示你免费下载两个 SSL 生成的文件。下载这两个文件，并将它们保存在本地计算机上。

**第三步:**按照这个 SSL For Free 页面的指示，新建一个名为`.well-known`的文件夹/目录。(如果你在 Windows 环境下，把它命名为`.well-known.`——也就是在末尾多加一个点。)在这个目录中，创建一个名为`acme-challenge`的新目录。将下载的两个文件复制粘贴到`acme-challenge`目录中。

**步骤 4:** 现在使用你喜欢的 FTP 应用程序(比如 *FileZilla* )将两个文件上传到你的虚拟主机服务器。

**第五步:**将`.well-known`目录从你的本地电脑上传到你的虚拟主机服务器的根目录(它的目录，而不是里面的内容)。

![Uploading the folder via FTP](img/dc76439a9d2c92b0af440b9b0a6859e2.png)

**步骤 6:** 现在在你的浏览器中打开一个新的标签/页面，输入你的域名 URL，以及这两个文件的位置。确保看到随机数字和字母:

![The file paths](img/7b4d685eff0fedfe0e84dd42fa75d97c.png)

**步骤 7:** 返回 SSL 免费标签/页面(页面在**步骤 1** ，点击**下载 SSL 证书**。确保你不会得到这样的回复:

![Error message to avoid](img/7372b9c3b081d3ec19f03a7a3a52471e.png)

**步骤 8:** 如果得到错误回复，请重复上面的**步骤 1** ，直到 SSL 免费加载**加载 SSL 证书账户**页面。然后将进入**安全生成 SSL 证书**页面:

![Generating and SSL certificate](img/e15fc47b1167da0610fe4f4188a1d969.png)

### 方法三。手动验证(DNS)

在最后一个验证方法选项中，系统会提示您在虚拟主机控制面板中创建新的 TXT 记录。

cPanel 中的步骤如下:

**第一步:**点击免费站点 SSL 上的**手动验证(DNS)** 按钮。

**步骤 2:** 然后，系统会提示您根据该页面上的数据创建一条新的 TXT 记录:

![Setting up TXT records](img/902f882edbffccf53bdcecb39cbcc5c4.png)

**第三步:**在浏览器中打开一个新的选项卡/页面，在服务器上登录 CPanel。进入**区域编辑器**，添加一条记录，数据如下:

*   **类型:** TXT
*   **姓名:**_ acme-challenge . your domain . com
*   TTL:14400
*   **TXT 数据:**【对应**步骤 2** 中的第一条 TXT 记录数据】

点击**添加记录**:

![Clicking the Add Record button](img/5df1909518a85eae6d0876cd04cee0a5.png)

**步骤 4:** 然后添加第二个新的 TXT 记录，包含以下数据:

*   **类型:** TXT
*   **姓名:**_ acme-challenge . www . your domain . com
*   TTL:14400
*   **TXT 数据:**【对应**步骤 2** 中的第二条 TXT 记录数据】

再次点击**添加记录**。

第五步:最终的结果将会是这样的:

![Complete TXT records in CPanel](img/7ce4f2100c67ea015f3c47645cef770c.png)

**第六步:**返回 SSL 免费标签/页面(页面在**第一步**，点击**下载 SSL 证书**。如果您已经正确设置了 TXT 记录，您应该会得到如下结果:

![Confirming TXT records](img/f46fbf152fb83e7c8f8b48f1d45be25e.png)

**第七步:**进入**加载 SSL 证书账户**页面，然后进入**安全生成 SSL 证书**页面:

![Generating and SSL certificate](img/e15fc47b1167da0610fe4f4188a1d969.png)

### 免费的 SSL 证书

一旦 SSL For Free 成功创建 SSL 证书，您将被提示输入您的电子邮件地址和密码，以便在证书过期前一周收到电子邮件通知(可选)。

如果您填写或已经登录，将会显示您在 SSL 中注册的免费服务域的 SSL 证书列表:

![Domains registered on SSL for Free](img/1393202c02fa94e38fdaee09ff4f9d7b.png)

在同一页面上，您将看到您的域的**证书**、**私钥**和 **CA 捆绑包**的数据。您可以下载这三个证书，或者将它们逐个输入到 cPanel web 托管服务器中:

![SSL certificates](img/a68ad4ced4d70d2ca2a29c928e4b222b.png)

### 在 cPanel 虚拟主机服务器中安装 SSL 证书

SSL For Free 提供了安装 SSL 证书的各种指南的链接，但这里我只是展示如何在 cPanel web 托管服务器上安装 SSL 证书。

**步骤 1:** 访问您的虚拟主机服务器 cPanel。点击 **SSL/TLS** 图标(在“安全”下)。

**步骤 2:** 在底部的**为您的站点安装和管理 SSL**选项中，点击**管理 SSL 证书**链接。

**第三步:**选择你已经通过 SSL 免费注册的域名。

**第四步:**将 SSL 免费生成的证书数据复制粘贴到各个输入框中(**证书**、**私钥**、 **CA Bundle** 数据)。

**第五步:**在此页面底部，点击**安装证书**。点击通知中的**确定**，确认您的证书已成功安装在您的 web 服务器上。接下来，您将看到一个安装了 SSL 证书的域列表。

### 检查您的 SSL 证书

您可以再次确认您的 SSL 证书已经正确安装。去**[name Checker](https://decoder.link/sslchecker/)**，然后输入你的域名地址，也可以直接去`https://decoder.link/sslchecker/yourdomain.com/443`。

您应该会得到类似如下的报告:

![Namecheap confirmation](img/0f5e0cabebf276c73f5afd42b989fd79.png)

您也可以在`https://www.ssllabs.com/ssltest/analyze.html?d=yourdomain.com`在 Qualys SSL 实验室进行检查。

**注意:**一旦你的 SSL 证书成功安装，为了让 HTTPS 完美运行，别忘了把你的默认站点地址从`http://yourdomain.com`改成`https://yourdomain.com`。你可以通过使用一个`.htaccess`文件(有很多关于这个的教程)或者如果你使用 WordPress，你可以通过 **WordPress 管理面板**直接分配 HTTPS 地址。

### 最后

我希望这篇教程已经向你展示了使用 Let's Encrypt 来保护你的共享主机网站是多么容易。诚然，不得不使用第三方中介并不理想，但当你考虑你可以节省的钱时，这并不是一个大的缺点。没必要再出去买证书了。

使用 Let's Encrypt 证书需要记住的一点是，它们的有效期只有 90 天，而不是一年(像许多付费证书一样)。自动化重新安装的一种常见方式是通过 cron 作业，尽管在共享主机上并不总是允许这样做。我建议你和你的虚拟主机谈谈，看看他们在这方面能提供什么。

## 分享这篇文章