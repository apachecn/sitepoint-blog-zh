# 零到哲基尔 20 分钟

> 原文：<https://www.sitepoint.com/zero-to-jekyll-in-20-minutes/>

![](img/220c09dad6ed751530b5bd4ac57a9d1b.png "Dr_Jekyll_and_Mr_Hyde_poster")

## 为什么是杰基尔？

作为 Rubyist 爱好者，你会经常看到像`rails new blog`这样开头的示例代码，这很有诱惑力。我们中的许多人都掉进了那个兔子洞，因为作为一名程序员，从头开始并控制每个小的方面感觉是正确的。这是你的孩子，你希望它是完美的。

那不重要！

除了少数例外，为博客编写自己的 web 应用程序不仅是多余的，而且实际上会降低你制作内容的能力。最后，你创建博客是因为你相信你有话要说，所以说吧。这就是 Jekyll 所做的:它不碍事，为您提供定制应用程序的所有便利，而不必将所有时间浪费在开发上。

我最近在自己的博客上从 Rails 切换到 Jekyll。这是一次有益的经历，我想它可能适合你。首先，我们来说说什么是哲基尔。

> Jekyll 是一个博客感知的静态网站生成器。

从本质上说，它是一个引擎，可以消化你的 markdown，并在一个`_site`目录中吐出一堆 HTML 页面，这样你就可以从你最喜欢的 web 服务器(Nginx/Apache)上提供这些页面。实际上还有很多，但是一旦你有了基本的结构，你所要做的就是写下内容，它就会输出一个漂亮的网站。

它使用 Liquid[【1】](https://github.com/Shopify/liquid)模板来允许你的站点中有更多的语义组织，以及可扩展的助手方法。它还支持几个 markdown 转换器，所以你可以用你喜欢的风格来写(比如 Markdown、Textile、HTML 等等)

既然你对 Jekyll 有所了解，我们可以直接开始创建博客了。

## 基础

Jekyll 站点的结构有些灵活，只包含一些必需的元素。Jekyll wiki 上列出了许多伟大的网站，这些网站提供了一些关于 Jekyll 网站构成的见解。也就是说，我们将使用 [@mojombo 的](https://github.com/mojombo/mojombo.github.com)网站作为参考，并做一些修改。

如果您打算继续下去，请运行以下命令: