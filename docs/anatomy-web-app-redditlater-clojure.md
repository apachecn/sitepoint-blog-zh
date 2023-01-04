# Web 应用剖析:我如何在 Clojure 中构建 Reddit

> 原文：<https://www.sitepoint.com/anatomy-web-app-redditlater-clojure/>

去年我做了 [RedditLater](http://www.redditlater.com/) ，允许人们在预先安排的时间向 Reddit 发帖。它有一个适度的用法；每天几百个访问者，其中一部分是预定的帖子。在这篇文章中，我将讲述 RedditLater 是如何工作的，以及为什么我会做出一些决定。

## 体系结构

大约一年前我写了这个项目。它由一个单独的 Heroku dyno 托管。我选择 Heroku 是因为，嘿，免费托管。因此，技术决策是在考虑到这些限制的情况下做出的——即使使用率不高，托管环境也相当有限。该应用程序必须能够(大约)按时提交预定的帖子，并经受住 reddit 上偶尔出现的流量激增。

RedditLater 的工作原理是通过旋转一个单独的工作线程来监控发往 Reddit 的帖子队列，该队列与 web 服务器并行运行。工作者线程只是浏览队列中的帖子列表，每当它在`schedule` < `now`处找到一个，就将它们发送到 reddit。

RedditLater 是使用 [Clojure](http://clojure.org/) 编写的。我选择 Clojure 主要是因为我当时对 clo jure 很感兴趣。我现在还是，但我那时也是。不过现在回想起来，我可以说这是一个很好的决定。Clojure 是一种简单的函数式语言，具有对并发性的顶级支持。这不完全是主流，但它足够受欢迎，在 Heroku 上拥有[一流的支持。RedditLater 依赖于 Clojure 提供的并发性，以便在一个 Heroku 实例上运行并行任务，尤其是](https://devcenter.heroku.com/articles/getting-started-with-clojure) [Lamina](https://github.com/ztellman/lamina) 库及其出色的队列结构。

对于持久性，帖子和用户登录数据存储在托管于 [MongoHQ](http://www.mongohq.com/) 上的 Mongo 数据库中，这与 Heroku 配合得很好。我使用 Mongo 是因为这个应用程序不是数据库密集型的，而且因为 Mongo 很容易使用，特别是在 Clojure 这样的带有哈希映射文字的语言中。

(在您需要决定使用哪个数据存储库之前，我一直认为 Mongo 是您使用的数据存储库。)

## 概观

应用程序需要一些功能，这些功能可以被分成不可知的模块。主要功能可以在请求处理器和工作器之间划分。请求处理器是应用程序的 UI 和前端，接受用户输入。这个工人负责把东西发布到 Reddit 上。

## 请求处理程序

以下是网络工作者在典型工作流中执行的任务的概述，在典型工作流中，用户登录并安排帖子:

*   提供通过 URL 路由的呈现模板
*   通过 Reddit 的 OAuth 支持向 Reddit 认证用户
*   在 mongo 中为将来的 Reddit API 调用存储用户的身份验证凭证
*   在 mongo 中存储所需的帖子(包括预定时间)
*   将帖子放入工作队列进行发布

请求处理程序是使用 Clojure web 应用程序的事实标准 [Ring](https://github.com/ring-clojure/ring) 库编写的，由 [Compojure](https://github.com/weavejester/compojure) 处理路由， [Enlive](https://github.com/cgrand/enlive) 负责模板渲染。我还使用了[中间人](http://middlemanapp.com/)来模拟 UI 并生成 HTML 模板[用于 enlive](http://adambard.com/blog/middleman-enlive-your-new-god/) 。

这里没有什么太有趣的东西，只是一些任何开发人员都可能涉及的真正平凡任务的 Clojure 等价物。更有趣的部分是 post 队列。使用 Clojure 的一个好处是，从应用程序启动请求服务机制既容易又高效。这是因为 Clojure 的并发是基于线程的(进程内)，而 Python、Ruby、PHP 等。使用多进程并发。

## 工人

在工人方面，情况要简单得多:

*   从队列中取一个职位。
*   从 Mongo 获取该帖子的最新版本。
*   检查是否是时候提交这篇文章了。
    *   如果没有，将帖子放在队列的末尾。
    *   如果是这样，尝试发布。如果尝试失败，将文章添加回队列。
*   重复一遍。

以下是一个注释示例，说明了这一切是如何进行的:

(语法入门:Algol 派生语言中的`myfunction(x, y)`在 Clojure 中是`(myfunction x y)`。)

```
;; Define a post queue
(def upcoming-post-queue (lamina/queue))

(defn enqueue-post
  "Enqueue a post in the post queue."
  [post]
  (lamina/enqueue upcoming-post-queue post))

(defn time-to-post?
  "Is schedule < now?"
  [post]
  (>= (get post :schedule) (helpers/now)))

(defn process-post
  "Grab a post from the queue. If it's time to post it, post it. If not, requeue."
  []
  (let [post @upcoming-post-queue] ; Blocks until a post is in the queue
    (if (time-to-post? post)
      (reddit-api/submit post) ; If so, submit the post with the reddit-api module
      (enqueue-post post)))    ; Otherwise, add the post to the queue
  (Thread/sleep 1000.)) ; Sleep for a second

;; Called by main on startup
(defn start-worker []
  (doall (repeatedly process-post)))
```

如果你能跳过括号，你就能看到与 Python ( [Celery](http://www.celeryproject.org/) )或 Ruby ( [Resque](https://github.com/resque/resque) )等语言的解决方案相比，这个过程是如何简化的。这两者都需要您运行和管理另一个进程(在 Heroku 上每月另需 30 美元)，而且都没有这么简单的 API。

当然，这也有不好的一面——在许多服务器上扩展这种架构需要 post 队列实现某种分片。但是这种方法在有必要分布处理之前，在一台服务器上可以纵向扩展。毕竟，由于队列处理锁定，所以除了服务器规范之外，没有理由认为您不能启动尽可能多的工作线程。

## 结论

这就是全部了！仅使用这些简单的工具，RedditLater 已经愉快地连续运行了一年多(偶尔有错误修复)。当然，还有许多其他方法来设计这样的应用程序，但是我希望您今天已经从我选择的设计和工具中学到了一些东西。关于 Redditlater 本身如何工作的更多信息，这里是一些更详细的信息。

## 分享这篇文章