# 使用 Zdog 学习 3D 设计和动画制作

> 原文：<https://www.sitepoint.com/learn-zdog/>

有一个很酷的 JavaScript 库，像[克里斯·甘农](https://twitter.com/ChrisGannon/status/1133750218883764227)、[瓦尔·海德](https://twitter.com/vlh/status/1138162693381246976)和 [CodePen](https://twitter.com/CodePen/status/1137060155340853254) 都对此赞不绝口。你也可以在[产品搜索](https://www.producthunt.com/posts/zdog)上找到它，在那里它做得相当好。这个图书馆不是别人，正是戴夫·德桑德罗的狗。

在这篇文章中，我将向您介绍 Zdog，并向您展示一些由令人惊叹的开发人员制作的可爱演示，您可以进行逆向工程并从中学习。

让我们开始吧！

## Zdog 是什么

DeSandro 在图书馆的专用网站上解释了 Zdog 的内容:

> Zdog 是一个用于`<canvas>`和 SVG 的 3D JavaScript 引擎。使用 Zdog，您可以在 Web 上设计和渲染简单的 3D 模型。Zdog 是一个*伪* -3D 引擎。它的几何图形存在于 3D 空间中，但呈现为平面形状。这使得 Zdog 与众不同。

换句话说，你可以设计、显示和动画基于 SVG 或`<canvas>`的 3D 模型。

Zdog 很小(整个库有 2，100 行代码，精简为 28KB)并且对开发人员友好(您可以使用其简单明了的声明性 API 快速实现它)。

## Zdog 入门

与大多数 JavaScript 库一样，您可以通过以下方式将 Zdog 包含到您的项目中

*   简单地从这个链接下载这个库的副本到您的本地环境:`https://unpkg.com/zdog@1/dist/zdog.dist.min.js`(对于未混合的版本，`https://unpkg.com/zdog@1/dist/zdog.dist.js`)；
*   从 CDN 获取库:`https://unpkg.com/zdog@1/dist/zdog.dist.min.js`
*   或者如果你使用`npm`的话，输入`npm install zdog`

这里是 CodePen 上的官方`Hello World`演示，让你马上开始使用这个库:

见 [CodePen](https://codepen.io) 上戴夫·德桑德罗([@德桑德罗](https://codepen.io/desandro) )
的笔 [Zdog–hello world canvas](https://codepen.io/desandro/pen/YbrLaO/)。