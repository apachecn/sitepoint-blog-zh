# 为您的 Api.ai 助手提供实体

> 原文：<https://www.sitepoint.com/teaching-our-api-ai-assistant-concepts-via-entities/>

如果你渴望了解更多关于人工智能的知识，请查看我们的截屏视频[微软认知服务和文本分析 API](https://www.sitepoint.com/premium/screencasts/microsoft-cognitive-services-and-the-text-analytics-api) ，了解你的机器人中的人工智能情感。

![Empowering Your Api.ai Assistant with Entities](img/3603ccc687cb8e761e058cc40344d8e6.png)

在 SitePoint，我们已经看到了使用 Api.ai 和[来设置你自己的个人助理的](https://www.sitepoint.com/customizing-your-api-ai-assistant-with-intent-and-context/)[基础知识，并进一步探究了意图和背景](https://www.sitepoint.com/how-to-build-your-own-ai-assistant-using-api-ai)。在本文中，您将在这个过程中更进一步，使用 Api.ai 实体向您的助手传授完全自定义的概念。

*注意:本文于 2017 年更新，以反映 Api.ai 的最新变化*

## 用 Api.ai 构建 AI 助手

本文是系列文章之一，旨在帮助您使用 Api.ai 运行简单的个人助理:

1.  [如何使用 Api.ai 构建自己的 AI 助手](https://www.sitepoint.com/how-to-build-your-own-ai-assistant-using-api-ai/)
2.  [使用意图和上下文定制您的 Api.ai 助手](https://www.sitepoint.com/customizing-your-api-ai-assistant-with-intent-and-context/)
3.  用实体赋能你的 Api.ai 助手(这个！)
4.  [如何将您的 Api.ai 助手连接到物联网](https://www.sitepoint.com/how-to-connect-your-api-ai-assistant-to-the-iot/)

## 什么是实体？

当用户在对话中提到实体时，你希望你的个人助理理解这个概念。每个实体都有一系列的值和属性，这些值和属性包含助手需要理解以响应此概念的术语。

Api.ai 中有三种类型的实体:

*   **系统:**Api.ai 定义的实体类型如日期、颜色、邮件、数字等等，API . ai 已经理解。你可以在 [Api.ai 关于系统实体](https://docs.api.ai/docs/concept-entities#section-system-entities)的文档中找到这些实体的完整列表。
*   **开发者:**我们为个人需求而创建的实体。这些就是你在这篇文章中要关注的。
*   **用户:**这些是为使用助手的个人用户创建的，可以由 Api.ai API 生成，在单个会话中使用。我不会在这篇文章中涉及这些，但是如果读者有足够的兴趣，我可能会在未来探索这个问题！

Api.ai 的闲聊是一系列没有任何实体的陈述的例子。他们在听那些意思不会因为句子中的关键词而改变的陈述。当有人问“你好吗？”，没有变异 Api.ai 理解。然而，如果我们把它调整为“吉米·奥尔森怎么样？”，我们把这句话移到实体领域。“吉米·奥尔森”是代表一个人的实体。如果有人问“布鲁斯·韦恩(Bruce Wayne)好吗？”，你的代理人会知道他们想问一个人怎么样，他们特别想知道布鲁斯·韦恩的情况。

再举个例子，“超级英雄”的实体不是 Api.ai 知道的。你可以训练你的助手理解一系列超级英雄和他们不同的名字——“超人”、“蝙蝠侠”、“闪电侠”、“绿灯侠”、“神奇女侠”、“圣诞老人”等等。然后，它可以理解这些是你想要触发动作的特定概念，例如当你说“我们需要闪光灯！”时，通过 API 联系这些英雄。

你也可以教你的助手这些名字的同义词，这样除了像“超人”这样的名字，它还会知道超人也被称为“卡尔-艾尔”、“超人：钢铁之躯”、“超人”和“超人”。如果你一时冲动说出一个不同的名字(或者其他人试图向你的助手请求帮助，他们称他们为其他人)，你的英雄仍然会提供帮助！

虽然我很想在整篇文章中继续使用这个实体示例，但我认为最好还是在演示中关注一个更真实的示例！在你的演示中，你将教你的助手理解我从颚骨得到的一个重要指标——睡眠。最终目标是让你的助手理解类似“我昨晚睡了几个小时？”以及“我昨晚睡了多长时间？”

在本文中，我将介绍这个过程的第一步——设置您的助手理解这些语句所需的实体。在本系列的下一篇文章中，我将会看到[将您的助手 web 应用程序连接到第三方 API](https://www.sitepoint.com/how-to-connect-your-api-ai-assistant-to-the-iot/)以提供它需要响应的信息。

## 创建新实体

要创建新实体，请打开 Api.ai 控制台，并使用左侧菜单转到“实体”页面。然后，通过单击“创建实体”、“实体”菜单项上的加号或“创建第一个实体”链接(对于尚未创建实体的用户，该链接会出现)来创建实体:

![Creating a new entity](img/616a7c0cd7f6af1e94e2782ab7ed43c6.png)

在出现的页面中，输入您的实体名称。一个常见的惯例是用小写字母书写，单词之间用破折号隔开。称你的新实体为“睡眠”。选中“定义同义词”，在它下面的部分输入一个术语——“睡眠”。你也可以添加同义词，所以通过在“睡眠”旁边输入“休息”、“打盹”和“闭眼”来涵盖更多的选项。您可以通过按 Enter 键、Tab 键或分号(；)键。完成后，单击保存:

![The New Entity options](img/5cdcce8ec89fbbda103514e9cf779f84.png)

如果您返回到“实体”页面，您的新实体将显示您将用来访问它的名称— `@sleep`。

![Your saved sleep entity](img/08f92a900a11b0912206fead2b2f7827.png)

## 在意图中使用你的实体

你现在需要创造一个新的意图，训练你的私人助理识别触发你睡眠相关请求的句子。首先转到“意图”页面，创建一个新的意图，就像你在这个系列中已经做过几次的那样。

在您的新意向页面上，将您的实体包含在“用户说”语句中。对于您的睡眠实体，输入一个语句，其中包含一个通用词来表示您的实体的示例。在这种情况下，输入“我昨晚睡了几个小时？”你在这种情况下的实体是“睡眠”。

为了教会 Api.ai“睡眠”是你的实体，高亮显示这个词，就会出现一个下拉菜单。其中一个选择应该是你新的睡眠实体— `@sleep`。选择该词，该词将突出显示，表示在与您的代理交谈时，该词可能会发生变化。

您还有一个用于操作名称的字段。这是将传递到您的 web 应用程序的名称，以显示 Api.ai 认为用户想要做的事情。将你的行动命名为“睡眠时间”。

![Referring to your entity in our intent](img/0ef9db1a5de206d9d5983cfab60bdd7b.png)

在包含你的睡眠实体时，它应该像这样作为意图的参数出现:

![Your entity's parameter](img/231cc583a246355f289941aba27b1bc3.png)

然后，您可以添加各种不同的方式来表达同一种陈述，就像您在上一篇关于创建意图的文章中所做的那样。然而，这一次，只要你提到一种睡眠类型，就在其中包含“睡眠”一词，Api.ai 就会自动为你突出显示它们:

![Variations on your user says statements](img/caa6fc7d8017ad0554dfc30bbc88bfda.png)

它还可能找到 Api.ai 理解的其他预建实体，如时间段。在上面的截图中，Api.ai 选择了“昨晚”是一个时间段，并突出显示了这一点。这意味着，如果 Api.ai 听到的是“上个星期的睡眠时间，请”，它仍然应该理解句子“昨晚的睡眠时间”

为了完成你的意图，对你关于睡眠时间的意图设置一些回应。Api.ai 中的助手本身无法查找统计数据，因此您需要使用自己的 web 应用程序来完成这一任务。然而，对于助手来说，至少保持它在做所有工作的假象是很好的。要做到这一点，你的回答会像这样:“我现在就为你检索睡眠统计，请稍等！”和“现在查看你的睡眠时间”这也给了我们一点时间让我们的 web 应用程序检索这些数据。

![Your potential speech responses](img/9e985c6b775cb64cb835dbef71e85639.png)

一旦您定义了您的回答，单击保存！

如果你通过说“我昨晚休息了几个小时？”来测试你的新意图，您的助手现在会返回一个正确的语音响应和“睡眠时间”动作，以供您的 web 应用程序响应。这正是你想要的！

![Your assistant returning sleepHours successfully](img/87f6cbe1119438b5e63eab036a24c07d.png)

## 扩展您的实体

你有一个工作实体，当你想查看你休息了多少小时时，它可以让你的助手知道，但是这个实体仍然很简单。睡眠只是睡眠。休息。闭眼。事实上，用户可能会询问一些特定类型的睡眠。如果用户问“我昨晚有几个小时的快速眼动睡眠？”“快速眼动睡眠”、“深度睡眠”和“浅度睡眠”都是你的睡眠实体应该理解的不同类型的“睡眠”。你会把这些加进去。

返回实体页面并打开`@sleep`实体。在“睡眠”及其同义词“睡眠、休息、打盹、闭眼”下面，增加了新的睡眠类型，如“快速眼动睡眠”(也称为“快速眼动”)、“深度睡眠”和“浅度睡眠”。将它们作为新行包括在内，因为它们具有不同的含义，并且与通用术语“sleep”不完全相同。要添加新行，请单击“添加行”。添加新的睡眠方式后，点击“保存”保存更改:

![Adding different types of sleep](img/a223356c8903e8a7d1dd5e372de15a0e.png)

如果你现在尝试一个更具体的句子，比如“昨晚我有几个小时的快速眼动？”，您的助手仍然识别出该请求是“睡眠时间”之一，但也包含“REM 睡眠”的睡眠参数，以告诉您的 web 应用程序用户询问的是什么特定睡眠:

![Testing your assistant by asking them about REM sleep](img/28fb5bdc90e8928cc3fd3c7f9b589ed4.png)

如果你点击下面的“显示 JSON”按钮，你会看到它真正发挥作用的地方。所有这些信息都以一个易于理解的 JSON 文件的形式返回到您的 web 应用程序中，如下所示:

```
{
  "id": "7438",
  "timestamp": "2017-02-06T01:19:45.271Z",
  "result": {
    "source": "agent",
    "resolvedQuery": "How many hours of REM did I get last night?",
    "action": "sleepHours",
    "actionIncomplete": false,
    "parameters": {
      "sleep": "REM sleep"
    },
    "contexts": [],
    "metadata": {
      "intentId": "25d04df",
      "intentName": "How many hours of @sleep:sleep did I get last night?"
    },
    "fulfillment": {
      "speech": "Looking up your sleep hours now."
    }
  },
  "status": {
    "code": 200,
    "errorType": "success"
  }
}
```

其中最重要的部分是所请求的动作名称和该动作的参数:

```
"action": "sleepHours",
"parameters": {
  "sleep": "REM sleep"
},
```

这是您将在下一篇文章中用来构建您的 web 应用程序对这些查询的响应的内容。

## 结论

令人难以置信的是，在研究 Api.ai 的三篇文章之后，您仍然只是触及了该平台的皮毛！实体可以包含其他实体(包括系统实体和您自己的开发人员实体)，您可以设置需要某些信息的意图，并在用户没有提供信息时提示用户，您可以在对话中使用您之前提到的实体，使用它们的`$alias` …等等！

在系列文章的最后一篇文章[中，您将看到如何为您在关于](https://www.sitepoint.com/how-to-connect-your-api-ai-assistant-to-the-iot/)[的早期文章中创建的 web 应用程序添加功能，如何使用 Api.ai](https://www.sitepoint.com/how-to-build-your-own-ai-assistant-using-api-ai/) 构建您自己的 AI 助手，API . AI 获取 Jawbone 数据，为您提供这些查询的真实答案！

我收到了一些读者的反馈，他们喜欢构建自己的个人助理。如果你一直在跟踪，你的助手怎么样了？你训练他们做什么？请在下面的评论中告诉我，或者在 Twitter 上通过 [@thatpatrickguy](https://www.twitter.com/thatpatrickguy) 与我联系。我总是喜欢听到新兴技术激发开发人员的想法！

用情感工具赋予你的人工智能人情味。查看我们在[微软认知服务和文本分析 API](https://www.sitepoint.com/premium/screencasts/microsoft-cognitive-services-and-the-text-analytics-api) 上的截屏。

## 分享这篇文章