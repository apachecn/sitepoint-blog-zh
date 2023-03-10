# 火狐推出即时语言翻译

> 原文：<https://www.sitepoint.com/instant-language-translation-comes-to-firefox/>

去年三月，当谷歌发布他们的 AJAX 语言应用程序接口时，我满怀希望地认为会有很多很酷的新应用程序被开发出来，让我可以体验以前无法理解的网络部分。许多应用程序都有超越简单本地化的潜力。

“我不确定 API 有多快，但是即时消息的即时翻译怎么样？当然，谷歌的机器翻译远非完美，但想象一下，在你只能接触到母语的情况下，能够用两种语言与人对话，”我[在读写网](http://www.readwriteweb.com/archives/google_releases_ajax_language_api.php)上写道。

Web 开发人员 Dave Trainer 去年 3 月在我关于语言 API 的帖子上发表了评论，现在他发布了一个非常酷的 [Greasemonkey](http://www.greasespot.net/) 脚本，该脚本(几乎)为 Firefox 添加了即时翻译。一旦你安装了名为 GMtranslator 的脚本，你可以通过[培训师的博客](http://www.minimalmedia.com/en/blog/dave/introducing-gmtranslator-for-firefox)下载，脚本白名单上的任何网站都会在屏幕右下角显示一个小的翻译控制框。

![](img/4aeda08cc1d8ab458a91f6c2679b2d95.png "gmtranslator")

该脚本可以翻译英语，葡萄牙语，西班牙语，德语，法语，意大利语和瑞士。你只需点击你想翻译的语言，然后点击你想翻译的文本。你不必告诉脚本你是从哪种语言翻译过来的——Google 的 API 通常可以从上下文中判断出来——文本选择是使用 jQuery 框架通过 JavaScript 完成的。

这个脚本确实有一些缺点。首先，它不是即时的。当它 ping Google AJAX 语言 API 并等待响应时，会有一些延迟，这意味着我的即时、无缝翻译聊天的梦想仍然不太可能实现(但肯定是可行的)。其次，你只能选择成块的文本进行翻译，而不是一次翻译整个页面，这有点笨拙。如果您可以设置脚本自动将页面翻译成首选语言，而根本不用手动选择要翻译的文本，那就太好了。

翻译本身只能和谷歌的机器翻译一样好，这意味着它们是可用的，但远非完美。没有专业翻译会被淘汰出局。尽管如此，Greasemonkey 的 GMtranslator 还是会出现在我的必备 Firefox 插件列表中，并且肯定会在翻译其他国家博客上的新闻时派上用场。

**更新:**谷歌今天宣布，[为谷歌阅读器增加了一个类似的功能](http://googlereader.blogspot.com/2008/11/is-your-web-truly-world-wide.html)。“翻译成我的语言”选项会自动将博客文章翻译成你的母语。该功能是一个 20%的项目，大概是基于相同的 API，教练的 Greasemonkey 脚本使用。非常酷。

## 分享这篇文章