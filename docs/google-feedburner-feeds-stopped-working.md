# 你的谷歌 Feedburner 订阅停止工作了吗？

> 原文：<https://www.sitepoint.com/google-feedburner-feeds-stopped-working/>

**更新:下面的改动修复了！**

我刚刚注意到，我所有网站的 feedburner 订阅数量已经降到了零！

![feedburner-no-readers](img/e68c9d6ff15d43fe2c913f1be01adc4b.png "feedburner-no-readers")

如果你直接点击地址，它似乎还在工作。我已经将我的信息从 http://www.jquery4u.com/feed/的[重定向到 http://feeds.feedburner.com/jquery4u 的](http://www.jquery4u.com/feed/)到 T2 的的 feedburner，但是它仍然在那里并且工作。

登录 Google Feedburner，看到 0 个订阅者。

![jquery-4u-feed-subscribers-count](img/9da04ff4317b625b3e282e1a78a963c3.png "jquery-4u-feed-subscribers-count")

如你所见，它从大约 1800 下降到 0。

![feedburner-subscribers-dropped-to-zero](img/4bd57e1923a32ff8c7c722b8d5bccbe9.png "feedburner-subscribers-dropped-to-zero")

正如你所看到的，其他网站也看到了同样的事情:
![web-design-depot-subscribers](img/d69ef46ac8bf474b567d5b15419444b9.png "web-design-depot-subscribers")

我在某处读到，12 月 1 日，谷歌的一些 API 将被弃用，我不认为 Feedburner 会在那个名单上。

## 狐狸

将进料地址从**feeds.feedburner.com**改为【feedproxy.google.com 。~~我现在已经完成了，所以希望重定向会发现这一点，统计数据会很快恢复正常。~~

如果任何人知道我不知道的事情，分享它可能会帮助其他遇到同样问题的人。

**更新:**
我刚刚看了一下[官方 Feeburner API 页面](http://code.google.com/apis/feedburner/)，看起来谷歌确实放弃了进一步的开发。很多网站使用这项服务，所以不知道为什么？

> 重要提示:自 2011 年 5 月 26 日起，Google Feedburner APIs 已被正式弃用，以反映它们不再进行积极的开发和实验，而这正是代码实验室项目中 API 的标志。但是，我们目前没有删除现有功能的计划。

如果没有删除现有功能的计划，那么如果 API 仍然是活动的(看起来是这样),就不知道为什么提要计数会中断

**更新:**
看来上面改地址就搞定了！
![feedburner-followers-fixed](img/3b735d6c1a178a471e4ddbfdc18df875.png "feedburner-followers-fixed")

## 进料燃烧器的替代品

谁知道有什么不贵的好选择吗？

## 分享这篇文章