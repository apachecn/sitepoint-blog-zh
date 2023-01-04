# PHP 5.6.0。放

> 原文：<https://www.sitepoint.com/php-5-6-0-released/>

PHP 5.6.0。被许多人认为是非常重要的基石，[今天发布了](http://php.net/archive/2014.php#id2014-08-28-1)。我们已经在[之前的](https://www.sitepoint.com/new-features-php-5-6/) [帖子](https://www.sitepoint.com/php-5-6-end-beta/)和[中讨论了这个版本带来的变化，其他人](http://www.brandonsavage.net/php-5-6-is-coming-next-week/)也写了相关内容。

![](img/f91732b4cdbb08a12ebeaf7767ff516f.png)

## 概述

简单回顾一下:

1.  添加了 CLI web 服务器中的 MIME 类型
2.  内部运算符重载
3.  现在接受超过 2GB 的上传
4.  POST 数据内存使用减少，`::/input`可重复使用
5.  改进了可变函数的语法，这些函数可以接受任意数量的参数
6.  参数解包
7.  常量标量表达式
8.  PHPDBG 默认绑定
9.  Zip 改进
10.  导入命名空间函数和常数
11.  求幂运算(`$a = 2**3;`)
12.  默认 UTF-8
13.  GMP 操作员超载

关于业务连续性中断，其中一些包括:

1.  GMP 资源现在是对象，这将打破 is_resource 以前的用法
2.  mcrypt 需要有效的密钥和 iv
3.  json_decode 对“真”、“假”和“空”的大小写更严格

你可以通过阅读我们以前写的关于这些主题的文章，或者阅读迁移指南来了解细节:[http://docs.php.net/manual/en/migration56.new-features.php](http://docs.php.net/manual/en/migration56.new-features.php)

## 更新

你可能想知道更新程序——你必须在你的操作系统中添加新的 repos 或者从源代码中编译才能让它们工作吗？虚拟机呢？好吧，你*可以*那样做(见我们的旧帖子关于让 [RC1 运行宅地](https://www.sitepoint.com/help-develop-php-5-6-rc1-homestead/)，或者 [coderabbi](https://twitter.com/coderabbi) 关于[升级当前宅地盒子到 5.6](http://coderabbi.github.io/posts/upgrading-laravel-homestead-to-php-56/) 的帖子)，但是你不必这样做。Taylor Otwell 已经承诺用 5.6 更新原始的 Homestead box，所以你可以像往常一样继续使用我们的 [Homestead 改进版](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)-最多五分钟就可以启动并运行。

> 我将对它进行升级，这样人们就可以进行“流浪者盒子更新”了
> 
> —泰勒·奥特威尔(@ taylorotwell)[2014 年 8 月 22 日](https://twitter.com/taylorotwell/statuses/502807749525114881)

就像那个人说的，你需要做的就是运行`vagrant box update`，你的盒子就会被更新为最新版本。这既适用于原来的[宅基地](https://www.sitepoint.com/6-reasons-move-laravel-homestead/)，也适用于我自己的[宅基地改良后的](https://stackedit-beta.herokuapp.com/www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)。流浪汉可能需要一段时间来重新下载盒子，但一旦完成，一切都应该像以前一样容易。

注意:盒子已经更新，请享用！

如果你想跟踪原始的盒子，看看它什么时候更新，请看[这里](https://vagrantcloud.com/laravel/homestead/versions)。

## 现在怎么办？

那么下一步是什么？当内部团队正在开发 PHPNG 和 PHP7 时，无论它们最终会被称为什么，都要花时间熟悉 PHP 5.6。如果你使用共享主机，请他们升级。如果他们没有这样做的计划，抛弃他们，告诉他们你不支持过时。在 DigitalOcean 获得一个便宜的[虚拟服务器——见鬼，使用这个链接你甚至可以得到 10 美元，让你托管一个二级服务器一整个月，或者一个一级服务器两个月。这是足够的时间来看看他们提供什么。](https://www.digitalocean.com/?refcode=ccc3ee7d288d)

使用 Heroku 的免费层来启动和运行 5.6，使用它，探索。抢先一步，一头扎进前沿，不要让自己被那些准备冒险的人甩在后面。我们是稳定的，这不再是测试版或 RC 模式-它是安全的升级，它只会从长远来看有利于您的应用程序。如果你有一些遗留代码需要维护，如果它与 5.6 不兼容，也要抛弃它。

你在现实世界的用例中试验过 5.6 的特性吗？请在下面的评论中告诉我们！更好的是——如果你能把这些功能的高级演示放在一起，我们会为你出版它们的权利付钱。前进，并增加您的项目！

## 分享这篇文章