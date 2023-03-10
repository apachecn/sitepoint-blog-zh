# 成功管理开源项目的提示

> 原文：<https://www.sitepoint.com/hints-successfully-managing-open-source-project/>

本文是微软网站开发系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

今天，我想改变一下我平常的帖子:我想与你分享运行一个开源项目意味着什么，而不是覆盖一个技术福音的主题。

两年多来，我和我的朋友大卫·罗塞特领导了 [Babylon.js](http://babylonjs.com/?WT.mc_id=16532-DEV-sitepoint-article68) 。我们在听说 IE11 将支持 WebGL ( [微软 Edge 支持更多](https://dev.windows.com/en-us/microsoft-edge/platform/status/?utm_source=SitePoint&utm_medium=article68&utm_campaign=SitePoint/?filter=f3f0000bf&search=webgl))后开始了这个项目，我们希望让人们更容易构建 3D 场景和游戏。在接下来的两年里，我把所有的业余时间都花在了为 web 开发人员制作 Babylon.js 这个简单而强大的 3D 引擎上。

## 奇妙旅程的开始

在最初的两个月里，这就像一个梦:你发展并赋予你头脑中所有的想法以生命。这是纯粹的幸福。

这个阶段就是我所说的**预备阶段**。就像在一场恋爱中，这是最好的时期，就在进入更严重的领域之前，复杂的事情可能会发生。

> 航运很重要。不是认真的。我仍然看到许多项目长期处于开发阶段。我知道这很难，但这对你完成你的项目是必要的。

在某个时间点，你准备好了——或者你认为你准备好了。

大卫和我很快遇到了第一个挑战:编码产品是不够的；你还需要在沟通方面下功夫。我们写下并同意了我们想要发表的关于 Babylon.js 的文章，我们想要发布的论坛，为了展示它我们想要参加的活动，等等。

来自 web 开发社区的反馈是巨大的。原因之一是我们与一位天才设计师(米歇尔·鲁索)合作。他不仅帮助我们发布了一个框架，还帮助我们开发了大量的 3D 场景来展示人们可以用我们的工具实现什么。

> 这是第一个重要的提示:开发一个框架只是工作的一半。让人们知道框架的存在以及为什么他们应该关心它是另一个更重要的方面。

> 这是第二个重要的提示:有一个好的框架是不够的。你必须提供大量的例子，并专注于一些真正闪亮的例子，以给开发者留下足够深刻的印象，让他们仔细看看。

## 小狗综合症

> 我第一次听说这个概念是在 2012 年的 dotjs 大会上，来自@fat

当你是世界上最快乐的开发者时，一些奇怪的事情一定会发生。开始时会很微妙，但很快会变得更加乏味。用户反馈将变得势不可挡且要求苛刻:

*   一些用户会开始表现得很奇怪，要求越来越多的功能，你需要坚定或者提出你不支持或者不打算支持这些功能的原因。永远记住，更多的特性意味着更多的工作，也意味着实现者有更多的选择。对一个用户来说被认为是好主意的东西，对其他人来说可能会被认为是额外开销和烦人的。
*   人们会要求你修改他们的代码，从而垄断你的时间(并阻止你完成项目中必要的工作)
*   其他人会要求你完全改变框架来满足他们的需求，不管你的愿景是什么，也不管你想用它来实现什么。

这就是**小狗综合症**！这是当你每天用爱和奉献宠爱的可爱小狗变异成一个你几乎无法控制的怪物。

![The Puppy Syndrome](img/a3afd4db14b5f28d590084b0079e55c9.png)

> 这是你工作中最复杂的部分。你必须坚持你的愿景，但你也必须稍微弯曲一下，以适应用户的需求。在这里运用你的常识！

这个阶段可能需要相当长的时间，你需要善于沟通，才能在你的用户群中找到盟友。从这些盟友你可以形成一个社区，带你到下一个层次。

## 利用社区

你可以拥有全宇宙最好的产品，如果你没有用户，你就什么都没有。这就是为什么我一直强迫自己遵守以下 12 条规则:

1.  准备好花大量时间不去开发你的产品。
2.  准备好花大量时间宣传你的产品。
3.  不要让一个问题超过一个工作日仍未解决或没有回应。
4.  永远不要在你的论坛上发布超过一个工作日没有回复的帖子。
5.  每天去你的论坛向用户展示这个论坛是有活力的(是的，在最开始这是一项艰难和累人的工作。不过这很重要——没有人会在空墙上写字。
6.  在推特上发布你正在做的和正在做的事情。
7.  有公开的**路线图**。
8.  请用户对您的路线图进行反馈。
9.  有一个**用户声音**(【http://babylonjs.uservoice.com/】T2)。
10.  对人好。不要傲慢。比起他们需要你，你更需要他们。
11.  投资一个像样的**文档**系统:我知道你讨厌写文档，但这是你项目中必不可少的一部分。我们在 http://doc.babylonjs.com/的[上花了很多时间，让它变得漂亮、易用和面向社区(文档本身是一个 Github 项目，社区可以贡献自己的力量)。](http://doc.babylonjs.com/?WT.mc_id=16532-DEV-sitepoint-article68)
12.  为**提供一种通过做**来学习的简单方法。这一点对我们来说非常关键。我们创建了 http://www.babylonjs-playground.com/的 T2，让 web 开发者不用做任何设置就能测试 babylon.js。减少所有的摩擦很重要。我们都很懒，如果你能消除所有的障碍，那么你会让更多的人尝试你的框架。就像 *jsfiddle* 一样，它也是一种与他人分享你的代码的方式，甚至可以很容易地提供一个 bug 回购。我们试图通过添加智能感知让它变得非常方便，这是一个在编码时学习的极好工具。

> 关于拥有一个论坛的一个题外话:我们开始在 Github 上与用户交流问题，但结果证明它不如一个常规的好的旧论坛方便。我要感谢理查德·戴维(photonstorm)主持我们的 HTML5GameDevs.com 论坛。

这一切都是为了**关注用户的需求和问题**。用户需要能够信任你的产品，保持良好的沟通渠道会让他们对你的产品充满信心。你处理请求的速度越快，你的用户就越有信心。这是你如何为你的框架建立声誉的主要构件。只要你关心你的用户，你可以从比其他工具更少的功能开始。

## 一个社区形式

如果你设法支持你的社区，他们会给你一份很棒的礼物。社区中的某个人会开始以一种漂亮而雄辩的方式回答其他人的问题——在某些情况下，甚至比你做得更好。我想当我第一次看到这一切发生时，我哭了。

如果你幸运的话，你会得到三种用户:

1.  使用你的产品的普通用户和**会问**问题
2.  使用你的产品的超级用户，提问并回答问题
3.  超级用户使用你的产品，提出问题，回答问题并贡献代码

这是你旅程的最后阶段。现在，没有你，用户也可以提交代码，做一些精彩的事情。例如，在 Babylon.js 的最新版本中，超过 40%的新特性是由社区开发的。

你现在是项目的领导者，他给出方向、目标，并致力于保持一切的连贯性和一致性。

当我收到来自世界各地的聪明人的请求时，我仍然感到同样的快乐，他们一起工作来制作一个更好的 3D 引擎。

## 结论

总而言之，这是从项目负责人的角度来看的样子(*用我的手*生成的图形)

![Point of View of a Project Leader](img/21a9c670836a76ecbb8276da6b01214f.png)

## 更多的 Web 开发实践

这篇文章是微软布道者和工程师关于实用 JavaScript 学习、开源项目和互操作性最佳实践的 web 开发系列的一部分，包括[微软 Edge](http://blogs.windows.com/msedgedev/2015/05/06/a-break-from-the-past-part-2-saying-goodbye-to-activex-vbscript-attachevent/?WT.mc_id=16532-DEV-sitepoint-article68) 浏览器和新的 [EdgeHTML 渲染引擎](http://blogs.windows.com/msedgedev/2015/02/26/a-break-from-the-past-the-birth-of-microsofts-new-web-rendering-engine/?WT.mc_id=16532-DEV-sitepoint-article68)。

我们鼓励您在 dev.microsoftedge.com 使用免费工具进行跨浏览器和设备测试，包括 Windows 10 的默认浏览器 Microsoft Edge:

*   [扫描你的网站，寻找过时的库、布局问题和可访问性](http://dev.modern.ie/tools/staticscan/?utm_source=SitePoint&utm_medium=article68&utm_campaign=SitePoint)
*   [下载适用于 Mac、Linux 和 Windows 的免费虚拟机](http://dev.modern.ie/tools/vms/windows/?utm_source=SitePoint&utm_medium=article68&utm_campaign=SitePoint)
*   [跨浏览器检查网络平台状态，包括微软 Edge 路线图](https://dev.modern.ie/platform/status/?utm_source=SitePoint&utm_medium=article68&utm_campaign=SitePoint)
*   [在您自己的设备上远程测试 Microsoft Edge](https://remote.modern.ie/?utm_source=SitePoint&utm_medium=article68&utm_campaign=SitePoint)

**向我们的工程师和传道者进行更深入的学习:**

*   **互操作性最佳实践** ( [系列](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-5-things-to-consider-as-a-web-developer/?WT.mc_id=16532-DEV-sitepoint-article68)):
    *   [如何避免浏览器检测](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-Avoiding-Browser-Detection/?WT.mc_id=16532-DEV-sitepoint-article68)
    *   [使用 CSS 前缀的最佳实践](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-CSS-Vendor-Prefixes/?WT.mc_id=16532-DEV-sitepoint-article68)
    *   [保持你的 JS 框架&库更新](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-Dealing-with-JavaScript-Libraries/?WT.mc_id=16532-DEV-sitepoint-article68)
    *   [构建插件免费网络体验](https://channel9.msdn.com/Blogs/BeLux-Developer/Riding-the-Modern-Web-Dealing-with-Plugins/?WT.mc_id=16532-DEV-sitepoint-article68)
*   [GitHub 编码实验室:跨浏览器测试和最佳实践](https://github.com/deltakosh/interoperable-web-development/?WT.mc_id=16532-DEV-sitepoint-article68)
*   哇，我可以在 Mac 电脑上测试 Edge & IE 浏览器& Linux！(来自雷伊·班戈)
*   [在不破坏网络的情况下推进 JavaScript】(来自 Christian Heilmann)](http://channel9.msdn.com/Events/WebPlatformSummit/2015/Advancing-JavaScript-without-breaking-the-web/?WT.mc_id=16532-DEV-sitepoint-article68)
*   [用 WebGL 释放 3D 渲染](https://channel9.msdn.com/Events/WebPlatformSummit/2015/Unleash-3D-rendering-with-WebGL-and-Microsoft-Edge/?WT.mc_id=16532-DEV-sitepoint-article68)(来自 David Catuhe)
*   托管网络应用和网络平台创新

**我们的社区开源项目:**

*   伏龙。JS (跨设备远程 JavaScript 测试)
*   [manifoldJS](http://manifoldjs.com/?WT.mc_id=16532-DEV-sitepoint-article68) (部署跨平台托管的 web 应用)
*   [babylonJS](http://babylonjs.com/?WT.mc_id=16532-DEV-sitepoint-article68) (轻松制作 3D 图形)

**更多免费工具和后端 web 开发工具:**

*   [Visual Studio 代码](https://code.visualstudio.com/?WT.mc_id=16532-DEV-sitepoint-article68)(用于 Mac、Linux 或 Windows 的轻量级代码编辑器)
*   [Visual Studio 开发基础知识](https://www.visualstudio.com/en-us/products/visual-studio-dev-essentials-vs.aspx/?WT.mc_id=16532-DEV-sitepoint-article68)(基于订阅的免费培训和云优势)
*   [用节点编码。JS](https://www.microsoftvirtualacademy.com/en-US/training-courses/building-apps-with-node-js-jump-start-8422/?WT.mc_id=16532-DEV-sitepoint-article68) 与[在蔚蓝云上试用](https://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=16532-DEV-sitepoint-article68)

## 分享这篇文章