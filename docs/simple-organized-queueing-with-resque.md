# 使用 Resque 进行简单、有组织的排队

> 原文：<https://www.sitepoint.com/simple-organized-queueing-with-resque/>

在使用过许多不同的排队系统之后，从古老的 [BackgrounDRb](http://backgroundrb.rubyforge.org/) ，到 [DelayedJob](https://github.com/tobi/delayed_job) ，再到 roll-your-own solutions，我选定了优秀的 [Resque](https://github.com/defunkt/resque) 。

GitHub 的克里斯·万斯特拉斯的著名博客文章说明了一切，而[的自述](https://github.com/defunkt/resque/blob/master/README.markdown)包含了你所能期望的一切。然而，Resque 为如何将它集成到你的应用程序中留下了很多想象空间。这当然是它的优势之一，因为它允许高度的灵活性。

在本文中，我将介绍一种我认为特别有用、干净，最重要的是简单的集成策略。

## 它自己的名称空间

为了摆脱对数据库的依赖，这种依赖可能会导致各种性能问题，Resque 使用 [Redis](http://redis.io) 作为其数据存储(注:如果您想了解更多关于使用 Redis 的信息，请阅读我以前的文章[这里](https://www.sitepoint.com/introduction-to-using-redis-with-rails/))。

假设您已经在应用程序中设置了 Redis，您可能已经有了一个$redis 全局变量来表示连接。Resque 默认使用 redis-namespace gem 来避免污染 redis 服务器的关键空间，但我个人喜欢控制这样的重要细节。

幸运的是，Resque 允许这样做，因此初始化连接就像在`config/initializers/resque.rb`中添加以下内容一样简单: