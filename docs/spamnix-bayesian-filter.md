# 复习–带贝叶斯过滤器的垃圾邮件

> 原文：<https://www.sitepoint.com/spamnix-bayesian-filter/>

我意识到现在有很多垃圾邮件解决方案。如果你有一个很适合你的，那么你就不需要今天的建议了。然而，如果你正在浪费宝贵的时间处理垃圾邮件，花几分钟读读这篇文章，因为…

我想我刚刚解决了我的垃圾邮件问题。

如果你已经上网一段时间，你会意识到垃圾邮件已经失控。我现在一天收到 1500 多封垃圾邮件。见鬼，我甚至会收到关于解决垃圾邮件问题的垃圾邮件。(我想知道电话销售人员什么时候会开始打电话来问我是否想上全国禁止来电名单……)

无论如何，我可以继续谈论垃圾邮件和我尝试过的失败的解决方案。直到最近，垃圾邮件确实让我非常头疼。我浪费了太多的时间在垃圾中寻找真正的邮件。但是我相信我终于找到了解决办法…

##### Spamnix 的新测试版 1.2.16 带有贝叶斯过滤器

说什么？是的，那是英语。在过去一年左右的时间里，我一直在使用一种叫做“Spamnix”(没有贝叶斯)的工具来过滤我的垃圾邮件。直到最近，我对 Spamnix 相当满意，因为它清除了我 80%的垃圾邮件。不幸的是，随着垃圾邮件数量的增加，这种“80%解决方案”每天会在我的收件箱里留下大约 400 封垃圾邮件，与我的真实邮件混杂在一起。

不再是了。

Spamnix 的最新测试版在[spamnix.com](http://www.spamnix.com)(具体地说，在[http://spamnix.com/beta.html](http://spamnix.com/beta.html))可以买到，它包含一个叫做贝叶斯过滤器的聪明的训练设备。

重要提示:Spamnix 仅适用于 Eudora 电子邮件用户，但我将要谈到的贝叶斯过滤器现在也出现在 Outlook 和 Outlook Express 的反垃圾邮件程序中。一会儿我会给 Outlook 用户推荐一个程序。

##### 它是如何工作的

好了，现在我将解释为什么这种新的贝叶斯过滤器如此有效，以及我是如何激活它来领导我与垃圾邮件的战斗的。在我解释我采取的五个简单步骤时，请不要离开我。

1.  I went to that URL above and downloaded the Beta version of Spamnix, and I installed it. It plugged into my Eudora email program seamlessly.
2.  I then downloaded my email and as usual I had a high number… 1,690 emails that were mostly junk. As it downloaded, as usual, Spamnix caught about 80% of the junk and filtered it into a special mailbox named "Spamnix."
3.  I then started picking through the rest of my inbox. But, this time, instead of simply deleting the additional junk that the filter didn’t catch, I transferred all the junk into a NEW email box that I named "confirmed garbage."
4.  Then, in my Eudora program, I clicked "Tools, Spamnix: Change Settings" and then clicked the "TRAIN" button. I told the program to look at that new email box and confirmed that it contained only spam. The Bayesian filter then trained itself to avoid all junk like that in the future.
5.  然后，我对我知道不是垃圾邮件的大约 500 封邮件进行了贝叶斯过滤器训练，让它在我过去几个月的发件箱中进行训练。这是贝叶斯过滤器的另一个巧妙之处——与简单的基于内容的过滤器不同，它从垃圾邮件和良好的电子邮件中学习，从而形成一种不断适应的高效反垃圾邮件方法。

##### 结果呢

在第一次“训练”后，第二天我的收件箱里有 98%的垃圾邮件。没错，第二天我下载了 1820 封邮件，只有 38 封垃圾邮件通过了这个过滤器进入了我的收件箱。我挽回了一个小时的挫败感。或者我应该说，我现在每周节省了 5 个小时。

每天，我的过滤能力越来越强。当新的垃圾邮件进入我的收件箱时，我只需点击一个拒绝按钮，并告诉贝叶斯过滤器根据邮件训练自己。下次我查看邮件时，任何类似的邮件都无法通过过滤器。

现在你可能想知道硬币的另一面…也就是说，有多少合法的电子邮件意外地被扔进了垃圾堆？

嗯，也有好消息！到目前为止，假阳性几乎不存在。在那批 1820 封邮件中，我只有两条合法邮件被标记为垃圾邮件。我是通过去 spamnix 邮箱(垃圾邮件去的地方)把邮件按主题分类找到它们的。这让我可以快速浏览，以确保没有合法的邮件丢失。我每天只需花 5 分钟，而手动筛选垃圾邮件需要一个小时。

现在，我收到了大量的电子邮件，所以我能够使用大量的邮件来训练我的贝叶斯过滤器。这可能是它在我看来表现如此之好的主要原因之一。您向贝叶斯过滤器显示的好的和坏的邮件样本越小，垃圾邮件就越有可能进入您的收件箱，或者更糟的是，合法邮件将被过滤为垃圾邮件。当然，我仍然需要等待所有的邮件下载，但这是我已经接受的事情。我只是在晚上开始下载我的信息，或者我设置 Eudora 每隔几个小时接收一次信息。

##### 底线

Spamnix 及其内置的贝叶斯过滤器是我迄今为止发现的最好的垃圾邮件解决方案。

如果你还在与垃圾邮件作斗争，试试内置贝叶斯过滤器的工具。你很难找到像这样的东西。我仍然对我刚刚腾出的时间感到敬畏。

如果您是 Outlook 和 Outlook Express 用户，也可以使用基于贝叶斯过滤器的垃圾邮件工具。许多可靠来源推荐的一个这样的程序叫做“垃圾邮件恶霸”。或者，用你最喜欢的浏览器搜索“贝叶斯 Outlook 过滤器”，你会发现更多的尝试。

## 分享这篇文章