# 如何使用 Google Analytics 跟踪 JavaScript 和 Ajax 事件

> 原文：<https://www.sitepoint.com/google-analytics-track-javascript-ajax-events/>

所有使用谷歌分析追踪网站用户的人举起手来。公平地说，统计那些不使用谷歌分析的人可能会更快。无论你对谷歌及其统治世界的计划有什么看法，谷歌分析是一个了不起的工具，它彻底改变了网络统计。

为了查看访问者报告，你需要将分析跟踪代码嵌入到你网站的每个网页中。

方法如下:

1.  [注册 Google Analytics](https://www.google.com/analytics/) (或者关联您现有的 Google/Gmail ID)。
2.  打开左侧的*管理*部分(cog 图标)，从*账户*栏中*创建新账户*。
3.  打开*属性*栏，点击*使用您网站的 URL 创建新属性*。您的网站将被分配一个跟踪 ID，其格式为 UA-XXXXX-Y。
4.  从*跟踪信息*子菜单中打开*跟踪代码*。

虽然这种跟踪代码有几个较老的版本，但是， [Google 推荐](https://developers.google.com/analytics/devguides/collection/analyticsjs/)为现代浏览器使用以下 JavaScript 实现:

```
<!-- Google Analytics tracking code -->
<script> window.ga=window.ga||function(){(ga.q=ga.q||[]).push(arguments)};ga.l=+new Date;
ga('create', 'UA-XXXXX-Y', 'auto');
ga('send', 'pageview'); </script>
<script async src='https://www.google-analytics.com/analytics.js'></script>
<!-- end Google Analytics tracking code --> 
```

将此添加到您网站的模板中，并用您的新跟踪 ID 替换 **UA-XXXXX-Y** 。Google 建议在 HTML 的`<head>`部分插入跟踪代码，但实际上它可以放在任何地方。我喜欢将它插入页面底部(在结束 body 标签之前)或者在页面加载后调用它，以确保它的优先级低于其他任务(这对网站性能更好)。

从这一刻起，谷歌分析将跟踪和报告您的用户的访问，以及他们的用户人口统计和用户行为，并在您的网站。

## 页面内事件

上面提到的标准跟踪代码对于简单的、只有内容的网站和基本的 WordPress 主题是足够的，但是它不能记录“页面内”事件，例如:

*   Ajax 调用
*   视频播放
*   文档下载
*   社交媒体互动
*   客户端交互
*   出站链接，或…
*   不会导致正常页面负载的任何其他活动。

可以使用以下 JavaScript 代码记录页面内事件，该代码调用全局 Google Analytics 对象:

```
ga(
  'send',
  'event',
  [eventCategory],
  [eventAction],
  [eventLabel],
  [eventValue]
); 
```

另一种方法是使用 JavaScript 对象:

```
ga('send', {
  hitType: 'event',
  eventCategory: [eventCategory],
  eventAction: [eventAction],
  eventLabel: [eventLabel],
  eventValue: [eventValue],
  [transport: beacon ]
}); 
```

让我们分别看看这个对象中的每一条线…

### [事件类别](必需)

特定类型事件的单一名称(例如，视频交互的“视频”或 PDF 链接的“下载”)。

### [事件操作](必需)

导致事件被触发的用户交互(例如，视频的“播放”或下载的文件名)。

### [事件标签](可选)

对事件进行分类的可选标签。例如，我们可以使用一个活动名称，如“冬季活动”。所有事件，无论是下载、视频播放、出站链接还是其他，都可以使用相同的标签进行分类。

### [eventValue] (optional)

与事件关联的可选数值。对于视频，我们可以定义视频的文件大小或长度，以便可以报告总带宽和平均带宽统计。

你也可以记录货币金额，但要知道谷歌分析有记录电子商务交易的特殊设施。

### [传输:信标](可选)

这用于跟踪表单和出站链接。当新页面开始加载时，浏览器停止在当前网页上执行 JavaScript，因此该选项不需要响应。

## 简单链接跟踪

事件跟踪可用于使用内嵌`onclick`事件记录类似 PDF 下载的内容:

```
<a href="document.pdf" onclick="ga('send', 'event', 'download', this.href); return true;">download</a> 
```

在这个例子中，*事件类别*被设置为`download`并且*事件动作*是文件 URL。

更有效的方法是，我们可以定义一个单独的 JavaScript 事件处理程序来记录所有的 PDF 下载，不管有多少:

```
// record all PDF download events
document.body.addEventListener('click', e => {
  let t = e.target;
  if (t.href && t.href.endsWith('.pdf')) {
    ga('send', {
      hitType: 'event',
      eventCategory: 'download',
      eventAction: t.href,
      transport: beacon
    });
  }
}, false); 
```

## 社交媒体互动

事件跟踪也可用于跟踪您网站上的社交媒体互动。谷歌为此提供了一个[社交互动 API](https://developers.google.com/analytics/devguides/collection/analyticsjs/social-interactions) ，它是事件跟踪片段的副产品:

```
ga(
  'send',
  'social',
  [socialNetwork],
  [socialAction],
  [socialTarget]
); 
```

你也可以用这个:

```
ga('send', {
  hitType: 'social',
  socialNetwork: [socialNetwork],
  socialAction: [socialAction],
  socialTarget: [socialTarget]
}); 
```

同样，让我们单独看看这个对象中的每一行…

### [社交网络](必填)

相关的社交网络(如脸书、推特等)。

### [社会活动](必需)

动作的类型(例如，赞、发送、推文等)。

### [社会目标](必需)

社会互动的目标。这通常是一个 URL。

## 实时报告中的事件跟踪

事件操作应该在实时报告中立即可用(*实时→事件*)。24 小时后，可在*行为→事件*部分获得更多详细信息。

有关更多信息，请参考 Google Analytics 文档:

*   [事件跟踪](https://developers.google.com/analytics/devguides/collection/analyticsjs/events)
*   [社交互动](https://developers.google.com/analytics/devguides/collection/analyticsjs/social-interactions)

请务必查看我们 UX 分析系列的更多文章。

## 分享这篇文章