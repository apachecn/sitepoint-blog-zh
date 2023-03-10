# 跟踪 URL 工具

> 原文：<https://www.sitepoint.com/tracking-urls-tool/>

Ed (Haystack) Kohler 今天在[高排名论坛](http://www.highrankings.com/forum/index.php?act=ST&f=30&t=14367&st=0#entry137157)上发布了一个非常好的工具的链接。[这个用于 Google Adwords](http://support.webstreamdynamics.com/tracking-url-creator.asp) 的跟踪 URL 创建工具将一系列搜索术语和关键字匹配选项(精确、短语、宽泛)、最高出价金额和基本 URL 作为输入。然后，它会生成一个关键词列表，可以直接复制并粘贴到 Adwords 活动中。

管理和优化 PPC 活动是容易得多，当你可以很容易地确定哪些关键字投标产生的点击率和销售额，这个工具使跟踪网址非常容易设置。

作为演示，我在工具中加载了两个搜索词，“关键词研究”和“关键词”。我用 www.example.com/ad.php 作为我的基本网址，最高出价为 0.25 美元。下面是生成的输出:

`keyword research ** .25 ** http://www.example.com/ad.php?source=gaw&kw=keyword-research-br
"keyword research" ** .25 ** http://www.example.com/ad.php?source=gaw&kw=keyword-research-ph
[keyword research] ** .25 ** http://www.example.com/ad.php?source=gaw&kw=keyword-research-ex
keywords ** .25 ** http://www.example.com/ad.php?source=gaw&kw=keywords-br
"keywords" ** .25 ** http://www.example.com/ad.php?source=gaw&kw=keywords-ph
[keywords] ** .25 ** http://www.example.com/ad.php?source=gaw&kw=keywords-ex`

这组跟踪 URL 将向我的 ad.php 脚本传递两个变量。

如果我们使用相同的脚本(ad.php)来跟踪其他活动，那么“源”变量将非常有用。如果在多个 Adwords 活动中使用了相同的关键字，则该值可以从“gaw”更改为其他标识符。如果需要的话，使用任何文本编辑器的查找和替换操作来添加额外的变量是相当容易的，或者如果我们只为 Adwords 活动使用这个特定的脚本 URL，则删除“source=gaw”。

“kw”变量包含我出价的搜索词，以及一对标识关键字匹配选项的尾随字符(例如，ph，br)。对于一个只有最低限度技能的程序员来说，从这个变量中解析出搜索词并做一些有用的事情，比如将信息存储在数据库中、将搜索词添加到页面上的副本中，等等，这是一个相对琐碎的任务。

对于使用跟踪网址的人，有一点需要注意。搜索引擎蜘蛛，尤其是谷歌机器人，确实有一个坏习惯，以某种方式找到这些网址。如果你最终有可能向蜘蛛抛出重复的内容(例如，你使用同一个登陆页面和多个跟踪 URL)，确保你使用 robots.txt 来阻止蜘蛛跟踪这些 URL。有了像我们假设的“ad.php”这样的广告跟踪脚本，很容易在我们的 robots.txt 文件中添加一行并阻止蜘蛛。

`User-agent: *
Disallow: /ad.php`

今天到此为止，伙计们，明天带着新的东西回来。

## 分享这篇文章