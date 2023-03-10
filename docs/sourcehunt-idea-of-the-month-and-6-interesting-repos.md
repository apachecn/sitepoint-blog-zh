# Sourcehunt:本月的想法和 6 个有趣的回复！

> 原文：<https://www.sitepoint.com/sourcehunt-idea-of-the-month-and-6-interesting-repos/>

是时候让我们的[每月寻找新的开源库来使用并贡献给](https://www.sitepoint.com/sourcehunt-open-source-week-edition/)了！

如果您是 Sourcehunt 的新手，这是我们的月度帖子，旨在推广那些有趣或有前途的开源项目，并在 Github stars 或 pull 请求方面提供帮助。

这是我们回馈社会的方式——推广我们使用(或可能使用)的项目，以便它们获得足够的曝光率来吸引更广泛的受众、强大的社区以及可能的新贡献者或赞助商。

![Sourcehunt logo](img/407bcf1bded70a8c020759619779150b.png)

* * *

### [genkgo/archive-stream](https://github.com/genkgo/archive-stream)[5★]

在阅读了关于 PHP 中流式文件压缩和下载的非常有趣的文章后，我们偶然发现了这个包。

这个包提供了一个内存高效的 API，用于将 ZIP 文件作为 PSR 7 消息进行流式传输。它有一定的限制，即只支持 Zip 64(Zip 规范的 4.5 版)格式，如果下载在完成前失败，文件不能恢复，但除此之外，如果你已经涉水通过 PSR-7 水域，使用它非常简单:

```
$archive = (new Archive())
    ->withContent(new CallbackStringContent('callback.txt', function () {
        return 'data';
    }))
    ->withContent(new StringContent('string.txt', 'data'))
    ->withContent(new FileContent('file.txt', 'local/file/name.txt'))
    ->withContent(new EmptyDirectory('directory'));

$response = $response->withBody(
    new Psr7Stream(new ZipReader($archive))
); 
```

该库虽然有许多贡献者做了专业的工作来防止它被愚弄，并且已经存在了 8 个多月，但是缺乏曝光度。奇怪的是，这里没有问题(问题特性本身被禁用了)也没有 PRs 要审查，但是作者总是可以使用更多的测试人员、技术作者在博客中展示库，以及维护人员。

你通常如何发送拉链，如果有的话？为什么不尝试一下呢？

* * *

### [patrickschur/语言检测](https://github.com/patrickschur/language-detection) [270 ★]

Patrick 最初通过我们的脸书页面联系我们，要求在本月的 sourcehunt 中被提及，鉴于这个包的出色，我们无法抗拒。

该软件包使用特定的训练材料来寻找出现单词的频率，并将其与测试样本进行匹配以检测其语言。根据自述文件:

> 这个库可以检测给定文本字符串的语言。它可以将许多不同习惯用法中的给定训练文本解析成一系列 N 元语法，并以 JSON 格式构建一个数据库文件，用于检测阶段。然后，它可以获取给定的文本，并使用先前在训练阶段生成的数据库来检测其语言。该库带有用于训练和检测 106 种语言文本的文本样本。

该库是有据可查的、经过良好测试的、构建良好的——使用起来非常简单，添加更多的训练数据也非常简单。

这个包可以使用一些深入的教程和维护者，所以如果你想做一个低维护的工作，那就注册吧！

* * *

### [https://github.com/JWHennessey/phpInsight](https://github.com/JWHennessey/phpInsight)【195★】

用于从文本中提取情感。这可以是积极的、中性的或消极的。这就像一个非常基本的，免费的，开源版本的 Semantria。

该库已经发布了几年，已经有几年没有更新了，但我们分享它是有原因的:a)它真的很有趣，玩起来很有意思，b)见下文。

与此同时，该库可以在几个问题上使用一些帮助，并且总是有精确的调整要做。能帮忙就帮忙！

* * *

### isfonzar/情绪温度计 [42 ★]

使用上面的 PhpInsight 库，这个程序进入 Twitter，搜索提供的短语或术语，并测量 Twitter 对它的情感。这意味着它将获取谈论这个问题的推文，分析它们的情绪，并返回一个整体结果。

现在，你可能会说，在演示中，一个唐纳德·特朗普的 twitter 搜索产生 40%的正面情绪，只有 20%的负面情绪，这没有多大意义，作者确实说过情绪会随着一天中的时间等因素而变化(也许他在俄罗斯测试的时候是中午？)，但显然，该库可以使用一些调整和微调，所以继续前进，直接进入。它还可以使用查找其他社交网络的功能——例如，脸书的公共帖子可能是一个有趣的帖子。

* * *

### [paragonie/PHP-Cookie](https://github.com/paragonie/PHP-Cookie)[7★]

有一个新的 CSRF 杀手在城里，paragonIE 的 PHP-Cookie 包是第一个真正利用它的。

如果你错过了，Cookies 现在在新的浏览器中支持 SameSite 指令，有效地杀死了所有现代客户端中的 CSRF。这仍然让旧的浏览器容易受到攻击，所以你肯定不应该仅仅依赖于 SameSite，但这是朝着正确方向迈出的一步，我们所要做的就是等待被采用。

PHP-Cookie 更进了一步，将功能作为 PHP 包添加进来——现在你可以很容易地从 PHP 设置 SameSite cookies。在此库中:

*   Secure 设置为 TRUE，除非您覆盖它。
*   HTTP-Only 设置为 TRUE，除非您覆盖它。
*   除非您覆盖它，否则相同站点设置为严格。

注意 PHP 7+是必须的。

如果你感兴趣的话，还有一个将它实现到原生 PHP 的建议:https://bugs.php.net/bug.php?id=72230

* * *

### kamranahmedse/为人类设计模式 [7718 ★]

最后但同样重要的是，非代码回购。这个令人敬畏的人类可读的设计模式解释汇编周末在 Reddit 和 Hacker News 上爆红，并一直在增长。

作者的母语不是英语，所以仍然有相当多的错别字要改正，短语要纠正，另外一些设计模式的例子也很方便。但老实说，这种回购有足够的惯性，无需进一步推广就能生存——我们只是想让你注意到它，以防你错过它。

* * *

二月份就这样了。找到你能咬进去的东西了吗？

和往常一样，请把你的链接和 [#sourcehunt](https://twitter.com/search?q=#sourcehunt) 标签一起扔给我们！如果您用我们提到的项目构建了一些东西，或者如果您提交了一个您想要谈论的详细的拉请求，请向我们大喊一声，我们将确保全世界都知道它！

本月最佳应用+教程创意:将脸书的公开帖子添加到情绪温度计中，将语言检测添加到组合中，使用谷歌翻译来翻译非英语帖子，然后获取所有这些帖子的情绪。连续几天/几周，每小时为一个术语建立一个情绪图，并找出喜欢和不喜欢一个短语的趋势。将这种混合添加到仪表板中，用户只需定义一个术语，就会生成一个带有趋势图的小部件。如果术语和它们的值被保存起来以备后用，那么如果另一个用户请求(例如 Donald Trump ),数据已经在那里并被重用，从而节省了带宽和 API 调用，这是加分的。

编码快乐！

## 分享这篇文章