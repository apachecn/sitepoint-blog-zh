# jQuery 插件注册表简介

> 原文：<https://www.sitepoint.com/jquery-plugins-registry/>

jQuery 的受欢迎程度有增无减。在[最近一次统计](http://w3techs.com/technologies/details/js-jquery/all/all)，JavaScript 库已经被 55%的网站采用。更令人印象深刻的是，超过 90%的使用图书馆的网站都加载了它。

然而，在 jQuery 世界中，并不是一切都是完美的。尽管有一个很棒的插件系统，你如何找到你需要的插件？位于 jquery.com 的原始存储库充其量只能说是笨拙的；搜索结果很差，代码已经过时，文档也很少。谷歌是一个更好的选择，但找到合适的代码是一件碰运气的事情。最终，[开发自己的 jQuery 插件](https://www.sitepoint.com/how-to-develop-a-jquery-plugin/)通常会更容易。

幸运的是，jQuery 团队意识到了这个问题，并推出了新的 [**jQuery 插件库**](http://plugins.jquery.com/) 。他们的目标是减少碎片和分发问题，这些问题可能会成为插件开发者和消费者的障碍。

## 如何下载 jQuery 插件

您可以在 [jQuery 插件库](http://plugins.jquery.com/)中搜索、浏览或点击标签来定位插件。每个插件页面都提供了简要描述、兼容性信息以及主页、文档、演示、问题和下载的链接。

## 如何注册新的 jQuery 插件

如果你想分享你令人惊叹的 jQuery 插件，在 [jQuery 插件库](http://plugins.jquery.com/)上发布代码需要几个步骤:

1.  [选择一个唯一的名称](http://plugins.jquery.com/docs/names/)。保持简短，有描述性，不要使用已经有人取的名字。先到先得。
2.  [创建包清单](http://plugins.jquery.com/docs/package-manifest/)。这是一个 JSON 文件，描述了插件，版本，依赖关系，开发者，许可，链接等。
3.  在 GitHub 上发布你的插件。你需要[注册一个账户](https://github.com/)，[用 URL `http://plugins.jquery.com/postreceive-hook`创建一个接收后挂钩](https://help.github.com/articles/post-receive-hooks)，并使用你喜欢的 [Git 工具](https://help.github.com/articles/set-up-git)推送。

## 第一印象

jQuery 插件库是新的，有有限的插件可供选择。搜索不是最好的，插件描述很简洁，你需要链接到各种信息页面，这会减慢浏览速度。对我来说，最根本的遗漏是缺乏用户评级和评论。

也就是说，目标必须是创建一个像 [WordPress 插件目录](http://wordpress.org/extend/plugins/)一样有用的库；一个权威且易于使用的资源，拥有一个蓬勃发展的开发人员社区。让我们祝愿 jQuery 团队取得成功。

你会把你的代码发布到 jQuery 插件库中吗？如果你需要一个插件，你会先去那里吗？

## 分享这篇文章