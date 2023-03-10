# 那个 J2EE 人还是不懂 PHP

> 原文：<https://www.sitepoint.com/the-j2ee-guy-still-doesnt-get-php/>

继 [Friendster 切换到 PHP](https://www.sitepoint.com/blog/) 之后，有趣的是看到[在这里](http://troutgirl.com/blog/index.php?/archives/22_Friendster_goes_PHP.html)展开的火焰战争([杰夫](http://www.procata.com/blog)在这里[做了一个整洁的链接集合](http://www.procata.com/blog/archives/2004/06/30/php-scalability-and-perforamnce/))。

在对“显然 Friendster 不知道 Java”的效果的各种评论之后，认为[这](http://troutgirl.com/blog/index.php?/archives/22_Friendster_goes_PHP.html)结束了那一行；

> 我们这里有不止一个而是两个写了最畅销的 JSP 书籍的人。这并不一定意味着他们是伟大的 Java 开发人员，但我确实认为我们的人和任何团队一样优秀。

我不想火上浇油，但似乎有一些 J2EE 开发者就是不“理解”PHP。这本身就很有趣，因为 PHP 有什么难以理解的地方？在一些基本层面上，J2EE 人和 PHP 人之间似乎存在思维定势的差异，这意味着(至少在一个方向上)人们无法理解另一种方法也能工作(而且实际上可能工作得更好)。

**虚拟 CGI**

也许说 PHP 在 web 服务器上做什么(作为一个 Apache 模块)最简单的方法就是把它和 CGI 进行比较(每个人都理解 CGI 对吗？).

附注/免责声明:我不是谈论 PHP 请求生命周期、性能和可伸缩性的最佳人选。PHP 真的需要 [Sterling](http://edwardbear.org/serendipity/) 、 [George](http://www.schlossnagle.org/~george/blog/) 和 Rasmus 聚在一起，写一篇详细的论文，说明它是如何工作的，为什么 PHP 会扩展，这样我们就可以从此过上幸福的生活。

我对 CGI 的理解是一个请求生命周期的样子；

1.Apache 收到页面请求，发现它需要 CGI 处理

2.Apache 派生一个进程来处理请求(产生开销)

3.无论 CGI 二进制执行什么，它的启动(更多的开销)

4.CGI 二进制程序处理请求，并向浏览器发送响应

5.流程/ CGI 二进制“模具”——返回步骤 1

对于 PHP(作为 Apache 模块),除了 PHP 在进程内运行之外，几乎完全相同，这意味着没有派生外部进程的开销，并且 PHP“启动”时需要做的工作也少得多。换句话说，我们说的只是上面的第 1 步和第 4 步。试图在此进一步解释[。](https://www.sitepoint.com/blog/)

相比之下，作为 Java servlet 运行的*应用程序*是常驻内存的。PHP 开发人员将会话信息存储在文件或数据库中，而 Java 开发人员*可能*将它放在内存中。这是理解 Java 和 PHP 路径之间差异的*要点*。

我在 2000 年发现了一篇非常有用的论文[Web 到数据库应用程序的备选解决方案的性能比较](http://rain.vislab.olemiss.edu/~ww1/homepage/project/mypaper.htm)，其中讨论了更多细节。当考虑性能时，它倾向于 servlet，并带有一些关键词，如“因为 servlet 是用高度可移植的 Java 语言编写的，并且遵循标准框架，所以它们提供了一种以独立于服务器和操作系统的方式创建复杂的服务器扩展的方法。”注意，有人已经看过标签了。

但是这是最重要的一点。

**可扩展性！=性能(！！！)**

不幸的是，PHP 可扩展性神话的作者搞错了，我已经在 Friendster 上看到了相同效果的评论。

是的，主题*与*相关，但是可伸缩性更多的是关于当你添加更多资源时会发生什么，以及如何增加你的应用程序处理的请求量。参见[关于可扩展性的维基百科](http://en.wikipedia.org/wiki/Scalability)。通常(如果您没有提前计划)，当性能由于负载增加而开始下降时，您就会开始考虑扩展。

Java servlet 在最佳条件下(例如大量空闲内存)比 PHP 脚本执行得更好，这与可伸缩性无关。关键是，随着容量的增加，您的应用程序能否继续提供*一致的*性能——例如，您能否通过添加硬件来维持性能？

换句话说，“这个页面需要 0.5 秒来完成它的响应。我们能保持每天 50 万次的点击量吗？”(可扩展性)是我们感兴趣的*而不是*”这一页需要 0.5 秒。怎么能把它减少到 0.1 呢？”(性能)。

通常人们谈论两种类型的可扩展性——垂直(向现有的“大机器”添加新的处理器、磁盘、内存或购买“更大的机器”)和水平(添加额外的“机器”并在它们之间分配负载)。

纵向可伸缩性易于实现，但从长期来看通常更昂贵。通常情况下，内存容量是有限制的，例如，您可以添加到现有的“盒子”中，而“下一个盒子”的价格是原来的三倍，但只能增加 20%的容量。

横向可伸缩性需要更多的努力/技巧，但可以证明是非常成功的，正如[谷歌力量的秘密来源](http://blog.topix.net/archives/000016.html)中所沉思的那样——用极其便宜的部件建造一台“超级计算机”。对于跨多个系统的文件系统(以及扩展数据库)复制，通常有一系列成熟的解决方案可供选择，许多是开源的。内存复制是另一个故事(现在回到那个*重点*。

你信任谁？
其中一条评论[这里](http://troutgirl.com/blog/index.php?/archives/22_Friendster_goes_PHP.html#c29)指出“J2EE 可以集群运行”，以照顾内存复制为例。

就在这里，你需要问“什么是 Java？”。它只是一种编程语言吗？还是说运行时+库是一个操作系统？

阅读[开发更安全的 Java 代码的十二条规则](http://www.javaworld.com/javaworld/jw-12-1998/jw-12-securityrules.html)，像“使你的类不可序列化:序列化是危险的，因为它允许对手得到你的对象的内部状态。”。

什么？！？

这个安全提示在 PHP 中根本不适用——要么全有，要么全无。那些我向其公开对象的对象是我隐式信任的(暂时忽略 RPC)。当然有一些 PHP 框架，但是你不会发现主机将它们作为服务提供。

这是我自己对“Java 方式”和应用服务器如 JBoss 的根本问题。他们似乎重新发明了一大堆轮子(尤其是涉及复制的地方)，这些轮子已经被很好地划分了领域。J2EE 只有四岁左右。

正如 Rasmus 在谈论“无共享”时一直说的，PHP 将所有“硬东西”委托给其他系统。Apache(我认为可以放心地说它是可信的)负责处理请求，在需要时分叉子节点。Rasmus 推荐使用像 [Squid](http://www.squid-cache.org/) 这样的工具来平衡负载。

谈到会话数据，我更愿意相信文件系统或数据库集群，而不是 J2EE 集群，关键点是支持它的机制和工具的成熟度(对于系统管理员而言)。也有一些重要的因素进入了 Linux 集群，这为 PHP 提供了另一条道路，同时也存在其他替代方案，如 [MSession](http://www.php.net/msession) 或[memcached](http://www.danga.com/memcached/)([pecl::memcache](http://pecl.php.net/package/memcache))。

一个有趣的阅读是[为什么 Java 对系统管理员来说很糟糕](http://www.panix.com/userdirs/jdw/javasucks.html),其中有一些非常有效的观点，比如；

 `java.io.FileNotFoundException: somefile (No such file or directory)
at java.io.FileInputStream.open(Native Method)
at java.io.FileInputStream.(FileInputStream.java:64)
at sun.tools.jar.Main.run(Main.java:186)
at sun.tools.jar.Main.main(Main.java:904)`

和...相对

 `Warning: file(somefile): failed to open stream: No such file or directory in /home/hfuecks/scripts/reader.php on line 10`

在 O'Reillys [Java SysAdmin](http://www.onjava.com/topics/java/javasys) (！有趣的是 Java 中的[作业调度](http://www.onjava.com/pub/a/onjava/2004/03/10/quartz.html)。

嗯嗯…

 `$ man cron`

"第四次伯克利分配 1993 年 12 月 20 日"

**思维定势不连续**
在回应拉斯姆斯[关于共享无/无限横向可扩展性](http://troutgirl.com/blog/index.php?/archives/22_Friendster_goes_PHP.html#c14)的评论时，有人叫马克[写了](http://troutgirl.com/blog/index.php?/archives/22_Friendster_goes_PHP.html#c32)；

“Rasmus，你的帖子正是不使用 PHP 的原因。您将会话状态、进程间消息传递和应用程序状态推送到数据库。看在 Friendster 的份上，我希望他们有一个巨大的集群 Oracle 实例，因为一旦超过了他们数据库的能力，网站就会崩溃。

就减轻数据库负担而言，JSP 比 PHP 要高效得多，原因就在上面。沙盒化每个请求本质上是一个错误，因为做任何种类的面向对象都需要你每次点击一个页面就加载用户的配置文件。"

典型的 PHP 方法是将用户的配置文件存储为会话数据的一部分，其中包含与该会话相关的所有内容——每个请求只需加载一次这个*,并用它填充任何必要的对象。当然，数据库调用(如果你正在使用的话)是开销，但这是可以管理的开销。通过巧妙使用缓存，可以消除更多的数据库调用(例如获取内容)。*

 *[George](http://www.schlossnagle.org/~george/blog/) 在[扩展 Oracle 和 PHP](http://otn.oracle.com/pub/articles/php_experts/scaling_oracle_and_php.html) 中提供了一个很好的讨论，我认为这突出了 PHP 和 Java 开发人员之间思维模式的一个关键差异。以这个建议为例；

“如果您的 Web 应用程序中的平均页面包含九个图像，那么只有 10%的 Web 服务器请求实际上使用了分配给它们的持久连接。换句话说，90%的请求浪费了宝贵的(从可伸缩性的角度来看，也是昂贵的)Oracle 连接句柄。您的目标应该是确保只有需要 Oracle 连接性(或者至少需要动态内容)的请求才由动态 Web 服务器提供服务。这将增加每个进程完成的与 Oracle 相关的工作量，从而减少生成动态内容所需的子进程数量。

最简单的方法是把你所有的图片下载到一个单独的网络服务器(或者一组网络服务器)上。"

这个技巧是特定于应用程序及其运行环境的。我认为 PHP 开发人员以这种方式考虑应用程序；每一个都是独一无二的，当涉及到扩展时，需要一套独特的解决方案。

与此同时，Java 方法正在寻找一种“一刀切”的解决方案——这种解决方案将带您远离像这样的特定解决方案，并为您提供一个可以“一劳永逸”的环境。虽然这是一个令人钦佩的目标，但它需要(并且已经)重新发明了许多轮子，并且需要一个基本的信念，即软件可以大规模生产并且仍然满足需求。虽然这种想法似乎超出了 J2EE 的思维范围。

最终认为这是“进程/分支”(LAxP)与“运行时/线程”(J2EE /)之间的摊牌。网)。当问到“PHP 可伸缩吗？”您实际上是在问“Process / Fork + X 持久性存储可伸缩吗？”。在许多方面，质疑*Nix 是否可扩展…* 

## *分享这篇文章*