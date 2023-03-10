# 第四集:什么是“正常”，真的吗？

> 原文：<https://www.sitepoint.com/episode-4-whats-normal-really/>

很抱歉错过了这一周，同道中人——现实生活，以及所有这一切——我会努力不让它再次发生。

无论如何，让我们进入本周的问题。前几周我贴了一个[寻宝游戏](https://www.sitepoint.com/scavenger-hunt/)求公开数据([答](https://www.sitepoint.com/episode-1-answers/))；今天，我们将继续处理这些数据。

当然，找到可行的数据来源只是第一步；一旦你想好了用什么，你就得想好怎么用。因为我是一个认证的数据库极客，一旦我手头有了一些好数据，我做的第一件事就是开始考虑数据库设计。

当我们谈论数据库设计时，我们通常谈论的是[正式数据库规范化](http://en.wikipedia.org/wiki/Database_normalization)，具体来说就是[第一](http://en.wikipedia.org/wiki/1NF)、[第二](http://en.wikipedia.org/wiki/2NF)和[第三](http://en.wikipedia.org/wiki/3NF)范式。尽管我将是第一个承认形式规范化通常需要让位于实用设计或性能需求的人，但本周我们将忽略这个重要的警告，一头扎进去。

同样，这里是我们在寻宝游戏中找到的五个数据源:

1.  来自美国农业部的食物的营养成分。
2.  (链接到)[美国各大城市的人口统计数据](http://www2.census.gov/census_2000/datasets/100_and_sample_profile/)，由美国人口普查局提供。
3.  最新的 [SEC 文件](http://sec.gov/Archives/edgar/xbrlrss.xml)(RSS 格式，不多不少)直接来自马嘴。
4.  [历史天然气价格](https://www.eia.gov/dnav/pet/pet_pri_gnd_dcus_nus_w.htm)，来自能源信息署(在写这个测试之前我从未听说过)。
5.  少年司法和犯罪预防办公室(司法部的一部分)的少年逮捕率。

那么，**中的这些来源分别是哪种范式(以及为什么)？**

 **我们将在本周末讨论答案以及更多关于数据库规范化的含义。

#### 奖金挑战！

对于一个额外的挑战，选择一个源，并为它定义一个完全规范化(即 3NF)的模式。这里没有一个“正确”的答案，但是如果有人有足够的勇气张贴他们的模式，当我们检查答案时，我会批评他们。

#### 你自己也有问题吗？

和往常一样，如果你有一个问题、难题或挑战，你认为会是这个测验的一个好问题，请发电子邮件给我，地址是*雅各布-jacobian.org*。** 

## **分享这篇文章**