# 闪烁(1)，一个满足所有开发者需求的可编程指示器

> 原文：<https://www.sitepoint.com/blink1-a-programmable-indicator-for-all-developer-needs/>

去年夏末，有人递给我一个奇怪的 USB 设备，我把它放进包里，然后放在书架上，直到今天，我觉得是时候把它插入我的电脑，看看它能做什么。

这个设备就是 [Blink(1)](http://blink1.thingm.com/) ，一个完全开源的 USB 供电指示灯，它是高度可编程和可定制的，可以满足你对 USB 供电指示灯的任何需求。

尽管这听起来像是一个小众设备，但它声称用户来自皮克斯、微软、夏普、Twitter、谷歌和其他大公司。

## 入门指南

硬件设备的问题是你需要得到一个。眨眼是负担得起的(不到 30 美元或欧元)，并且很容易找到。如果你喜欢你在这里读到的，也许你会为自己买一个，或者想知道为什么你会关心一个可编程灯。

### 软件

Blink 的所有软件都是免费开源的，你可以下载一个 [Mac 和 Windows GUI](http://blink1.thingm.com/blink1control/) ，一个 [CLI 工具](https://github.com/todbot/blink1#blink1-tool)和无数的[库和 SDK](http://blink1.thingm.com/libraries/)，其中一些我们将在这里介绍。

GUI 应用程序提供了对闪烁的大量控制，并且将涵盖您可能有的许多用例。

![Blink Control App](img/5ddbc0e530cdb423bb73d9646edbbf5e.png)

从左至右，主选项卡允许配置:

*   **开始**:快速进入启用和禁用眨眼的默认模式。
*   **IFTTT** :对 [IFTTT](https://ifttt.com/recipes) 食谱做出反应，我们接下来会讲到。
*   **工具**:添加可以控制眨眼的脚本或者脚本的 URL。
*   **邮件**:检查电子邮件账户触发光模式。
*   **硬件**:对本地机器上的电池、CPU 和 RAM 变化做出反应。

Blink Control 应用程序的许多元素都是不言自明的，并且工作得很好，但是作为 Kickstarter /开源项目的典型，我在这里和那里发现了一个问题(最重要的是，如果 GUI 是打开的，CLI 工具将无法工作)，但没有开发人员无法解决的问题。

## 快速试验

让我们试着建立一个最灵活和潜在有用的部分，并在 GitHub 问题分配给我们时建立一个 IFFTTT 集成。

首先，我们需要 Blink 的 IFFTTT 键，它位于控制应用程序的左侧。

![IFFT Key](img/084363da33fa8b187e385ceb79e82113.png)

接下来，找到并设置连接 GitHub 到 Blink 的 [IFFTTT 配方，在升级时添加您的 IFFTTT 密钥。请确保将其命名为机器可读的名称，因为我们稍后会用到它，即](https://ifttt.com/recipes/188585-if-new-issue-assigned-to-you-then-send-blink-event)

![Rule Name](img/f323c08a120002e0fc21bdf9b477b587.png)

现在，在 Blink Control 应用程序中添加一个匹配的 IFFTTT 规则，将其命名为与规则相同的名称，并设置您选择的灯光模式。

![Blink IFFT Rule](img/1ddeb7716850cdc36a5a40c5fc304d6f.png)

接下来创建一个问题，自己分配，等待 IFFTTT 和 Blink 进行交流，您应该会看到:

![Blink Flashing Red](img/b4691270e4cc5fc81e074136e934b8c4.png)

## 黑客眨眼

Blink Control 应用程序允许我们做很多事情，但当直接与应用程序流结合时最有用，这是 Blink 擅长的地方。

Blink 拥有 Java 客户端库，Node。JS，Python，Go，QT，C，.Net、Ruby、命令行和 REST API，所以提供了连接几乎任何你想连接的应用程序的可能性。

一个显而易见的用例是来自持续集成系统的状态更新，有预先存在的库用于 [Travis](https://rubygems.org/gems/travis-blink1) 、 [GitLab](https://www.cloudpipes.com/integrations/gitlab-ci/blink1) 和 [TeamCity](https://github.com/brettswift/buildblink) 以及任何其他通过编写特定语言插件或使用 REST 支持的构建工具。

控制闪烁是一种情况，或者告诉它打开或关闭，以及显示什么样的颜色模式。如果您使用客户端库或 REST 接口，原理是相同的。

例如，告诉闪烁以默认预设模式之一闪烁:

```
curl 'http://localhost:8934/blink1/pattern/play?pname=policecar'
```

并停止特定图案的闪烁:

```
curl 'http://localhost:8934/blink1/pattern/stop?pname=policecar'
```

创建并播放一个名为“sitepoint”的自定义图案，该图案会将 SitePoint 徽标颜色交替显示五次:

```
curl 'http://localhost:8934/blink1/pattern/add?pname=sitepoint&pattern=5,%23f9a140,0.5,%23208ab1,0.5'

curl 'http://localhost:8934/blink1/pattern/stop?pname=sitepoint'
```

点击阅读更多关于界面[的内容。](https://github.com/todbot/blink1/blob/master/docs/app-url-api-examples.md)

## 看到光明了吗？

如你所见，眨眼只做一件事，但是让它做这件事的可能性和方法几乎是无穷无尽的。如果你使用这样的设备，你可能已经被说服了。如果你没有实际用途，眨眼可能是学习编程的一个很好的视觉工具，因为即时反馈是令人满意的。

我很想听听你会用眨眼做什么，我要去写一个脚本，当这篇文章收到评论时通知我。

## 分享这篇文章