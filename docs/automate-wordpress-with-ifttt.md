# 用 IFTTT 自动化 WordPress

> 原文：<https://www.sitepoint.com/automate-wordpress-with-ifttt/>

无论你是博客写手、程序员，还是 WordPress 的普通用户，你可能都知道 WordPress 是一个有用的工具，可以用于各种任务。无论你是想推出自己的[自主业务平台](https://www.sitepoint.com/turning-wordpress-into-a-self-hosted-business-powerhouse/)还是想与世界分享你的知识，高效的工作流程对你的成功至关重要。

虽然使用 WordPress 有时可能是一个密集的过程，但是你可以通过使用[If TTT](http://ifttt.com)(If This Then That 的缩写)来简化你的工作。IFTTT 是一个强大的宏创建工具，具有简单易用的界面。如果你曾经使用过微软 Office，你可能对 VBA 脚本和它引起的麻烦很熟悉。然而今天，事情已经发生了变化，IFTTT 允许您通过几次点击来自动执行常见任务。

![IFTTT website screenshot](img/7fb89fd0fda0d9463fcb6970391b6343.png)

## 它是如何工作的

对于不熟悉 IFTTT 的人来说，这项服务相对容易上手。首先，你选择你想要自动化的应用程序(IFTTT 支持 142 个通道 [WordPress IFTTT section](http://ifttt.com/wordpress) )，然后你可以从预先制作的任务中选择，或者你可以创建自己的任务。

创建自己的“配方”(IFTTT 对宏的称呼)只需要设置一个触发器和一个要发生的动作。一切都通过网络完成，你不需要接触一行代码。如果你有一个自托管的 WordPress 安装，那么你需要确保在你的站点上启用了 XML-RPC(在设置>编写下), IFTTT 才能工作。此外，如果您的站点上安装了安全插件，您应该确保它不会阻塞 XML-RPC。

整合 IFTTT 和 WordPress 不需要安装新的插件或者调整你的网站。你所需要做的就是向 IFTTT 提供你的网站 URL 以及用户名和密码。如果您担心安全性，那么最好专门为 IFTTT 创建凭证，而不是使用您的管理员凭证。

IFTTT 为在网站上创建的食谱提供了两种类型的触发器和两种类型的动作。触发器包括添加新帖子和添加具有特定标签或类别的新帖子。IFTTT WordPress 的动作包括创建一个标准帖子和一个照片帖子。

## 帮助您入门的示例食谱

### 社会化媒体

由于社交媒体是几乎所有博客的重要组成部分，我们将首先关注它。当然，你可以使用 IFTTT 将帖子分享到脸书、Twitter 和其他社交网站，但你也可以使用 [Jetpack](http://wordpress.org/plugins/jetpack/) 来做到这一点。下面的列表关注的是不太常见的不太明显的用途:

**Instagram** :如果你是一个狂热的 Instagram 用户，你可以将贴有特定关键字的 Instagram 照片发布到你的 WordPress 类别的特定类别下。([见菜谱](http://ifttt.com/recipes/54003-instagram-tag-to-wordpress-category-as-photo-post))。

![WordPress Instagram](img/9952f2ec3e0d8b7b290670ac7eceb861.png)

**SoundCloud** :如果你是一名播客或音乐人，使用 SoundCloud 分享你的内容，你可以让 WordPress 在你添加新内容时自动生成一篇博客文章([见菜谱](http://ifttt.com/recipes/7932-post-new-soundcloud-tracks-to-your-wordpress-blog))。

YouTube :管理视频本身就是一个困难的过程。当你上传视频到 YouTube 时，自动创建你的博客文章，让你的生活变得更轻松一点([见食谱](http://ifttt.com/recipes/1712-allows-for-an-automatic-post-from-any-uploaded-youtube-video-to-your-wordpress-blog-with-the-vide)

### 公用事业

**自动写博客**:有时你可能会遇到这样的情况，你需要在你的网站上展示来自外部博客的帖子。通过使用 IFTTT，你可以从一个 RSS 提要中提取帖子，并通过你的 WordPress 站点重新发布它们([见菜谱](http://ifttt.com/recipes/19382-rss-feed-to-wordpress-blog))。

Evernote 存档:虽然[备份你的 WordPress 网站](https://www.sitepoint.com/effortless-website-backups-with-mover/)是一个相对简单的任务，但在你的工作流程中有冗余也无妨。通过使用 IFTTT，你可以自动将你的博客文章备份到 Evernote 上([见菜谱](http://ifttt.com/recipes/1306-archive-wordpress-posts-to-evernote))。

## 提醒一句

虽然 IFTTT 在处理多媒体帖子时是帮助简化帖子创建的一个很好的方法，但是当使用一个食谱来创建一个 WordPress 帖子时，你应该让 IFTTT 创建一个草稿，这样你就可以事先编辑帖子，以确保它符合你的 SEO 策略。因为仅仅发布视频和多媒体对你的读者来说没什么好处，所以在文章发布前给它们添加内容是有意义的。

从更积极的角度来看，有了 IFTTT，你所能达到的极限就是天空。这篇文章中提到的食谱只是一个入门。如前所述，你可以随时创建自己的食谱来满足自己的需求。

## 分享这篇文章