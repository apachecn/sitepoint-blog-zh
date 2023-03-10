# 从零到云:设置 EC2 沙箱，第 1 部分

> 原文：<https://www.sitepoint.com/from-zero-to-cloud-1/>

如果你是一个有事业心的 web 应用程序开发人员，在过去的几年里，你无疑对尝试亚马逊的云产品很感兴趣。也许你的目标是“在成功中生存”，这是 Jurg van Vliet 和 Flavia Paganelli 编写的亚马逊 EC2 的潜台词；我相信我们所有人都希望有一天能面对这场斗争。不管您调查 EC2 的原因是什么，您的时间都是值得的。

这是将 EC2 设置为开发人员的沙箱的三部分系列的第一部分。第 1 部分很简单，但是值得一读，因为它将带您完成在 Amazon Web Services (AWS)上设置帐户的所有必要步骤。第 2 部分将教你如何配置你的开发环境，并帮助你找到一个可靠的亚马逊机器映像(AMI)。[第 3 部分](https://www.sitepoint.com/from-zero-to-cloud-3/ "From Zero to Cloud: Setting up an EC2 Sandbox, Part 3")将向您展示如何安装 Apache、MySQL 和 PHP，然后克隆您自己的 AMI。

## EC2 的优势

自 2006 年亚马逊推出云产品以来，我一直对它们感到好奇，但无论是对生态系统差异的感知，还是对因按使用付费结构而招致天文数字账单的担忧，都让我没有动手。现在，在体验了 EC2 之后，我可以说，这个云中可能没有适合每个人的东西，但是对于 web 应用程序开发人员来说，肯定有适合他们的东西。

EC2 编程最大的收获是，我实际上可以设计和构建多层系统。对于经典的托管，我觉得我必须搁置我所学的关于适当架构的知识，在一台机器上运行所有的东西。这种方法通常足以运行一个博客或一个小型商业网站，但我这个纯粹主义者总是觉得要抓紧时间等待。我什么时候可以将我的静态内容分离出来，放在一个优化的内容交付网络上？如果我的数据库增长到需要拆分的程度，该怎么办？如果我的新应用程序点击了 Reddit 的首页，而我的成功之窗被 HTTP 500 和 503 错误关闭了，该怎么办？

EC2 允许我们以计算机科学课程中教授的方式构建 web 应用程序，但是成本如何呢？如果您计划运行一台利用率为 100%的机器，EC2 的成本确实高于共享或专用主机。这就是为什么这篇文章是为你，精明的 web 应用程序开发人员，因为你有工具来建立，而不是网页。你想第一次就把它们造好。如果你为它付费，你想要一个沙盒，当你不使用它的时候，你不介意关掉它。

## 报名

事不宜迟，让我们拿出邮箱、电话和信用卡，注册 EC2 吧！在你最喜欢的网络浏览器中，进入[http://aws.amazon.com](http://aws.amazon.com/ "Amazon Web Services")，点击页面顶部的登录 AWS 管理控制台。我们将被带到这里:

![AWS Sign In screen](img/5cd6373a768bdf033d2570e457c6b643.png)

在页面上输入我们的电子邮件地址后，我们将被带到这里:

[![Contact Information screen](img/97e0923917c8641033e83f5af3170bb3.png)](https://www.sitepoint.com/wp-content/uploads/2011/09/from-zero-to-cloud-02.png)

在我们填写联系信息、检查同意条款并提交页面后，系统会提示我们输入信用卡信息。由于按使用付费的支付结构，亚马逊需要一张有效的信用卡。

![AWS Payment Information screen](img/ee67ee2a2811b31ea2344caef529836b.png)

接下来，我们需要验证我们的电话号码。这是一个简单的 1 分钟的过程，通过这个过程，自动服务人员会拨打电话并提供 pin 码，然后我们会将该 pin 码输入到帐户创建向导的下一阶段。

![AWS Identity Verification screen](img/8e9f546cefdd01d7dd188c6e2021655d.png)

最后，帐户创建完成，亚马逊向我们发送了一封确认电子邮件。现在我们可以第一次登录 AWS 管理控制台了。

如果您的兴趣被激起，请继续关注[第 2 部分](https://www.sitepoint.com/from-zero-to-cloud-2/ "From Zero to Cloud: Setting up an EC2 Sandbox, Part 2")，在那里我们将超越注册，开始配置开发环境和安装 AMI！

图片 via[light spring](http://www.shutterstock.com/gallery-540784p1.html "Stock Photos | Shutterstock: Royalty-Free Subscription Stock Photography & Vector Art")/[Shutterstock](http://www.shutterstock.com "Stock Photos and Royalty-Free Images by Subscription")

## 分享这篇文章