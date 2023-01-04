# 将内容导入 Drupal

> 原文：<https://www.sitepoint.com/importing-content-into-drupal/>

任何 web 系统中最痛苦的步骤之一是导入内容——尤其是使用 Drupal。这是我一直害怕的事情。CSV 自定义脚本和逐行解析数据的图像或笨拙的 SQL 命令浮现在脑海中。呸！

那都是过去的事了。我现在很开心。我最近发现了一个模块，它是 Drupal Contrib 社区皇冠上的宝石— [Feeds](https://drupal.org/project/feeds) 。

![Feeds Module](img/97115fbe7a3ddbfb4c74baa7b9524ad1.png)

Feeds 模块对很多事情都很有用(RSS/ATOM/OPML 聚合和导入， [PubSubHubbub](https://developmentseed.org/blog/2010/feb/23/pubsubhubbub-support-drupal) 支持，CSV 导入等等),但是现在我只关注 CSV 导入功能。设置起来非常简单。

第一步是将模块安装到您的 Drupal 站点中。在即将发布的 Drupal 7 版本中，这将像点击一样简单——Drupal 7 支持基于 web 的模块安装。对于 Drupal 6，我们没有这样的奢侈品，它完全是手动的。所以首先下载[模块](https://drupal.org/project/feeds)。*快速说明一下，这个模块还没有正式发布(它在 6.x-1.0-alpha14)，但是据说，它工作得非常好。*下载完成后，将其解压缩并移动或上传到您的/sites/all/modules 目录。请访问您的管理模块区域(/admin/build/modules/)来安装它。

![](img/e21a7cbbd47f219b1df29fb7d0d7efcb.png)

现在它已经安装好了，这是最简单的部分！有点超前，但是相信我。努力是值得的。

首先，快速创建一个新的提要导入器(/admin/build/feeds/create)，并为其命名(这将成为访问导入器的 URL)。现在，有一些步骤用下图突出显示:

![Steps to CSV importing](img/31b60b3447faeeeef7e46d6736c453d0.png)

1.  基本设置:进行以下更改–将“附加到内容类型”更改为“独立表单”，将最小刷新次数设置为“从不”。
2.  Fetcher:将此设置为直接文件上传或 HTTP 检索。
3.  解析器:选择 CSV 解析器。
4.  处理器:选择节点处理器。
5.  节点处理器设置:选择要导入的内容类型。如果您希望导入的内容过期，您可以选择设置要更新的现有节点
6.  创建节点设置:这里是将 CSV 列标题映射到节点的字段名称的地方。

这就是设置导入过程的全部内容。许多步骤，但它们真的很简单。现在您需要实际导入内容。访问导入页面(/import ),并单击您在上面创建的导入程序的名称。上传 CSV 文件和 Feeds 模块将立即开始创建您的节点。如果您在任何映射中犯了错误，您可以轻松删除最后导入的内容并重新开始。当我在测试阶段的时候，这让我节省了很多时间。

这只是这个模块所能做的一小部分。所以我鼓励你尝试一下。

## 分享这篇文章