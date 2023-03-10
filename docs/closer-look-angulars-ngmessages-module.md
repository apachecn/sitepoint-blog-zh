# 详细了解 Angular 的 ngMessages 模块

> 原文：<https://www.sitepoint.com/closer-look-angulars-ngmessages-module/>

*这篇文章由[马克·托勒](https://github.com/MarcTowler)和[斯蒂芬·马克斯](https://www.sitepoint.com/author/smax/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

由于能够帮助开发人员在构建表单时打造更好的用户体验，随着 Angular 1.3 的发布，`ngMessages`模块成为了核心指令。一年后，Angular 团队继续开发这个模块，以确保它为开发人员提供使用模板显示错误消息、向错误消息添加动画等功能。八月下旬，我发表了一篇关于使用`ngMessages`模块执行基本表单验证的文章。那篇文章展示了这个模块如何工作的基础，而这篇文章旨在探索`ngMessages`授予我们的一些不同的功能。

## ngMessages 模块

当添加到 Angular 应用程序时，ngMessages API 授予用户以下指令来控制错误消息:

*   [ng 消息](https://docs.angularjs.org/api/ngMessages/directive/ngMessages)
*   [ng 消息](https://docs.angularjs.org/api/ngMessages/directive/ngMessage)
*   [ng 消息包括](https://docs.angularjs.org/api/ngMessages/directive/ngMessagesInclude)
*   [ngMessageExp](https://docs.angularjs.org/api/ngMessages/directive/ngMessageExp)

在整篇文章中，我们将研究每一个指令，以便更好地理解它们是如何工作的，以及它们为什么有用。

### ng 消息和 ng 消息的基础

让我们来看看`ng-message`和`ng-messages`指令在显示错误消息中的作用。

在[码笔](http://codepen.io)上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看笔 [ngMessages Ex 1](http://codepen.io/SitePoint/pen/LGdMWM/) 。