# 生活在边缘

> 原文：<https://www.sitepoint.com/living-on-the-edge/>

正如我在上一篇博文的评论中所暗示的，Rails 2.0 的候选版本 1 刚刚发布，其中包含了许多改进和错误修复，在为最终版本做准备时，我们建议任何使用预发布版本的人升级他们的应用程序——但是你该怎么做呢？这比你想象的要容易得多，你可以选择哪些应用程序需要改造。

虽然您可以通过 gems 在一个公共路径中引用 Rails 库，但是您也可以基于每个站点安装特定的版本。Rails 将自动在供应商目录中查找 rails 文件夹——如果找到了，它将使用该文件夹。

## 冰冻铁轨

最简单的方法可能是使用冻结耙子任务。在应用程序的根目录下运行以下命令应该可以解决问题:

```
 rake rails:freeze:edge TAG=rel_2-0-0_RC1 

```

## 活塞

如果你更有冒险精神，想玩最前沿的，你可以使用 Piston 来帮助你管理你所有的 rails 插件(包括核心！).现在，如果你是一个优秀的小开发者，你应该使用某种版本控制库，我个人的选择是 subversion (SVN)。这恰好是 Rails 核心团队和许多(如果不是大多数)Rails 插件开发者的选择。

但是，如果你想对你的应用程序进行版本控制，并轻松地更新你最喜欢的插件，会发生什么呢？嗯，在过去，您可能会使用 svn:external 属性，它允许您在同一个存储库中保存多个代码树。当然这很容易做到，但是为什么不把它变得更容易呢？进入[活塞](http://piston.rubyforge.org/usage.html)。

Piston 页面更好地解释了它是如何工作的以及如何设置它，所以我将把它留给您来通读，但是要查看 Rails 的 edge 版本，您需要做的就是:

```
 piston import http://dev.rubyonrails.org/svn/rails/trunk vendor/rails 

```

那就跑吧

```
 piston update vendor/rails 

```

来更新一下！

## 分享这篇文章