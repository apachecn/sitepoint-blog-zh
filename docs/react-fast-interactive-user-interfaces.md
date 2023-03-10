# 如何判断 React 是否最适合你的下一个项目

> 原文：<https://www.sitepoint.com/react-fast-interactive-user-interfaces/>

如今，用户期望时尚、高性能的 web 应用程序越来越像本地应用程序。已经设计出一些技术来减少用户第一次访问时加载网站的等待时间。然而，在公开大量交互性的 web 应用程序中，用户操作发生和应用程序响应之间的时间间隔也很重要。原生应用程序感觉很快，而网络应用程序被期望表现相同，即使在不太理想的互联网连接上。

许多现代 JavaScript 框架涌现出来，可以非常有效地解决这个问题。可以被安全地认为是最流行和最健壮的 JavaScript 库之一，您可以用它来为 web 应用程序创建快速、交互式的用户界面。

在本文中，我将讨论 React 擅长什么以及它的工作原理，这将为您提供一些背景信息，帮助您决定这个库是否适合您的下一个项目。

## 什么是反应？

React 是脸书创造的，它简单地将自己标记为用于构建用户界面的 JavaScript 库。

这是一个开源项目，迄今为止，已经在 GitHub 上获得了超过 74，000 颗星星。

反应是:

*   **声明性**:你只需要为你的应用程序中的每个状态设计*简单的视图*，当你的数据改变时，React 将有效地更新和呈现正确的组件。
*   基于组件的(Component-based):你可以通过组装一些封装的组件来创建你的 React 驱动的应用程序，每个组件管理自己的状态。
*   **一次学会，随处写** : React 不是一个成熟的框架；它只是一个渲染视图的库。

## 虚拟 DOM 是如何工作的？

虚拟 DOM 是 React 快速渲染用户界面元素及其变化的核心。让我们更深入地了解它的机制。

HTML 文档对象模型或 DOM 是一个

> HTML 和 XML 文档的编程接口。DOM 将文档表示为一组具有属性和方法的结构化节点和对象。本质上，它将网页连接到脚本或编程语言。— [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction)

每当您想以编程方式更改 web 页面的任何部分时，都需要修改 DOM。根据文档的复杂性和大小，遍历 DOM 并更新它可能需要比用户可能接受的时间更长的时间，尤其是如果考虑到当 DOM 发生变化时浏览器需要做的工作。事实上，每次 DOM 更新时，浏览器都需要重新计算 CSS，并在网页上执行布局和重绘操作。

React 使开发人员能够对网页进行修改，而不必直接处理 DOM。这是通过**虚拟 DOM** 完成的。

虚拟 DOM 是 DOM 的轻量级抽象模型。React 使用`render`方法从 React 组件创建一个节点树，并更新该树以响应由动作引起的数据模型的变化。

每当 React 应用程序中的底层数据发生变化时，React 都会创建一个新的用户界面虚拟 DOM 表示。

### 用虚拟 DOM 更新 UI 更改

在更新浏览器的 DOM 时，React 大致遵循以下步骤:

*   每当发生变化时，React 都会在虚拟 DOM 表示中重新呈现整个 UI。
*   React 然后计算以前的虚拟 DOM 表示和新的虚拟 DOM 表示之间的差异。
*   最后，React 用实际更改的内容修补真实的 DOM。如果什么都没有改变，React 根本不会处理 HTML DOM。

有人可能会认为，这样一个过程，包括在内存中保存虚拟 DOM 的两个表示并比较它们，可能比直接处理实际 DOM 要慢。这就是高效的 diff 算法、批处理 DOM 读/写操作以及将 DOM 更改限制到最低限度的原因，这使得使用 React 及其虚拟 DOM 成为构建高性能应用程序的最佳选择。

## React 对每个项目都有用吗？

顾名思义，React 非常擅长制作超级反应式用户界面——也就是说，能够快速响应事件和随之而来的数据变化的用户界面。脸书工程师[乔丹·沃克](https://twitter.com/jordwalke)对*反应*这个名字的评论很有启发性:

> 这个 API 可以对任何状态或属性变化做出反应，并且可以处理任何形式的数据(就像图形本身一样有深度的结构)，所以我认为这个名字是合适的。— Vjeux，"[我们的第一批 50，000 颗恒星](https://facebook.github.io/react/blog/2016/09/28/our-first-50000-stars.html)"

虽然有些人会说所有项目都需要 React，但我认为 React 非常适合那些需要保持复杂的交互式 UI 与底层数据模型的频繁变化保持同步的 web 应用程序，这是毫无争议的。

React 旨在以高效的方式处理有状态组件(这并不意味着开发人员不需要优化他们的代码)。因此，受益于这种能力的项目可以被认为是 React 的良好候选。

Chris Coyier 概述了以下相关的情况，当[做出反应是有意义的](https://css-tricks.com/project-need-react/)时，我倾向于赞同:

*   许多状态管理和 DOM 操作。即启用和禁用按钮、激活链接、更改输入值、关闭和展开菜单等。在这种项目中，React 使管理有状态组件变得更快更容易。正如《React 路由器》的合著者[迈克尔·杰克逊](https://twitter.com/mjackson)在[的推特](https://twitter.com/mjackson/status/849638783142076416) :

    > 中恰如其分地说的那样重点是，反应过来处理了计算数字正射影像图实际需要发生什么变化的困难部分,而不是我。这是无价的

*   **战斗意大利面**。通过直接修改 DOM 来跟踪复杂状态可能会导致代码杂乱无章，至少如果没有特别注意代码的组织和结构的话。

## 资源

如果您对 React 及其虚拟 DOM 的工作方式感兴趣，您可以在这里了解更多信息:

*   [通过 SitePoint 全面的 React hub 学习 React](https://www.sitepoint.com/learn/react/) 。
*   [来自脸书工程师的 React 视频](https://facebook.github.io/react/community/videos.html)
*   “React.js 中虚拟 DOM 的真正好处”
*   Bartosz Krajka 的《[虚拟 DOM 和 DOM 的区别](http://reactkungfu.com/2015/10/the-difference-between-virtual-dom-and-dom/)
*   “React 慢，React 快:实践中优化 React 应用”，Franç ois Zaninotto
*   “如何为您的公司选择合适的前端框架”，作者 Chris Lienert

## 结论

React 和其他类似的 JavaScript 库简化了快速响应状态变化的敏捷的、事件驱动的用户界面的开发。一个潜在的目标可以从弥合 web 应用和本地应用之间的鸿沟的愿望中找到:用户希望 web 应用感觉像本地应用一样流畅和无缝。

这是现代 web 开发的方向。这并不是偶然的，最新更新的 *[Create React App](https://www.sitepoint.com/create-react-app/)* ，一个让零配置创建 React 应用成为可能的项目，已经默认附带了[创建渐进式 web 应用(PWAs)的功能](https://facebook.github.io/react/blog/2017/05/18/whats-new-in-create-react-app.html)。这些应用利用[服务工作者](https://developers.google.com/web/fundamentals/getting-started/primers/service-workers)和[离线优先缓存](https://developers.google.com/web/fundamentals/instant-and-offline/offline-cookbook/#cache-falling-back-to-network)来最小化延迟并使 web 应用离线工作。

*我们与开源软件合作，为您带来来自 React 开发者的 **6 个专业技巧。更多开源内容，请查看[开源软件](http://bit.ly/opensourcecraft)。***

[https://www.youtube.com/embed/xa-_FIy2NgE?ecver=2](https://www.youtube.com/embed/xa-_FIy2NgE?ecver=2)

## 分享这篇文章