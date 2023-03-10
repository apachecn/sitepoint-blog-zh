# (几乎)每个项目都必须使用的 6 个 Meteor 包

> 原文：<https://www.sitepoint.com/6-must-use-meteor-packages-almost-every-project/>

已经有数以千计的用于 Meteor JavaScript 框架的包，所以不管你想在你的 web 应用程序中添加什么特性，有人可能已经做了一个包，巧妙地实现了那些精确的想法。方便吧？

但是当然，随着更多的选择而来的是选择的悖论。弄清楚哪些包可能会派上用场可能会让人不知所措。

以下是我认为最有帮助的几条:

## 1.[铁路由器](https://atmospherejs.com/iron/router)

我已经从初级开发人员那里收到过几次这样的问题，“我如何用 Meteor 创建一个多页面应用程序？”

因为，当一个人以前从来没有构建过 web 应用程序时，并不会立即直觉地认为创建一个新页面并不像创建一个新文件那么简单。我向他们介绍的是*路由*的广阔世界。

路由允许开发人员用更少的代码和灵活的项目结构制作多页应用程序。它们还有许多其他优点，其中大部分在您构建更大更复杂的应用程序时会变得明显，但是目前，有两个要点需要理解:

1.  绝大多数的 Meteor 应用程序都会以某种方式使用路由。
2.  要处理这种路由，Iron 路由器包是最佳选择。

Iron Router 无所不包——友好、深入且支持良好——您可以使用以下命令将其添加到项目中:

```
meteor add iron:router
```

安装后，在 JavaScript 文件中创建一个路径:

```
this.route('about');
```

然后创建一个同名的模板:

```
<template name="about">
    <h1>About</h1>
</template>
```

您现在可以访问[http://localhost:3000/about](http://localhost:3000/about)路径，并看到“about”模板。

然而，这是一个非常简单的路由示例。更深入的介绍，请看我为可学的制作的视频。

## 2.[集合 2](https://atmospherejs.com/aldeed/collection2)

大多数 Meteor 应用程序会以某种方式与数据库进行交互。但是默认情况下，您必须手动验证用户从数据库中插入、编辑和删除的数据。

Collection2 通过扩展 Meteor 的功能来帮助这个过程，允许它“提供指定模式的支持，然后在插入和更新时根据该模式进行验证。”例如，您可以使“Books”集合有一个必须是字符串的`title`字段和一个必须是日期的`lastCheckedOut`字段。

下面是一个模式示例:

```
var Schemas = {};

Schemas.Book = new SimpleSchema({
    title: {
        type: String,
        label: "Title",
        max: 200
    },
    author: {
        type: String,
        label: "Author"
    },
    copies: {
        type: Number,
        label: "Number of copies",
        min: 0
    },
    lastCheckedOut: {
        type: Date,
        label: "Last date this book was checked out",
        optional: true
    },
    summary: {
        type: String,
        label: "Brief summary",
        optional: true,
        max: 1000
    }
});
```

要将此包添加到项目中，请编写以下命令:

```
meteor add aldeed:collection2
```

但是要了解 Collection2 的全部功能，请务必阅读官方文档。

## 3.卡迪拉

性能并不是构建 web 应用程序的一个吸引人的部分，但它仍然是一个值得思考的重要问题。你可以拥有世界上最不可思议的功能，但是，如果用户每次都被迫坐下来等待，你真的会考验他们的耐心。

为了处理这个问题，[卡迪拉](https://kadira.io)。这是一个性能监控工具，类似于 New Relic。您注册一个在线帐户，将软件包添加到一个 Meteor 项目中，然后，从一个基于 web 的界面，您将深入了解各种性能瓶颈和其他需要考虑的事情。

您可以使用以下命令将 Kadira 添加到您的项目中:

```
meteor add meteorhacks:kadira
```

如果你不知道从哪里开始学习性能监测，我建议你去看看防弹流星——这是一个由 Kadira 的创造者设计的互动课程，它深入研究了这个精确的主题。

## 4.[旋转器](https://atmospherejs.com/sacha/spin)

即使有世界上所有的性能调整，有些东西还是需要一些时间来加载，为了使这个加载过程看起来不那么混乱，我们可以使用 Spinner 包。这个包可以快速添加一个经典的，旋转加载符号到应用程序中。

要将微调器添加到项目中，请使用以下命令:

```
meteor add sacha:spin
```

然后在需要时使用“微调器”模板:

```
{{> spinner}}
```

您还可以使用许多选项对其进行配置:

```
Meteor.Spinner.options = {
    lines: 13, // The number of lines to draw
    length: 10, // The length of each line
    width: 5, // The line thickness
    radius: 15, // The radius of the inner circle
    corners: 0.7, // Corner roundness (0..1)
    rotate: 0, // The rotation offset
    direction: 1, // 1: clockwise, -1: counterclockwise
    color: '#fff', // #rgb or #rrggbb
    speed: 1, // Rounds per second
    trail: 60, // Afterglow percentage
    shadow: true, // Whether to render a shadow
    hwaccel: false, // Whether to use hardware acceleration
    className: 'spinner', // The CSS class to assign to the spinner
    zIndex: 2e9, // The z-index (defaults to 2000000000)
    top: 'auto', // Top position relative to parent in px
    left: 'auto' // Left position relative to parent in px
};
```

使用 Spinner 包最简单的方法是将它与 Iron Router 包集成在一起。

## 5.[时刻](https://atmospherejs.com/mrt/moment)

[Moment.js](http://momentjs.com) 使得在 JavaScript 中解析、验证、操作和显示日期变得容易，为 Meteor 安装 Moment 包是最快的入门方式。

要安装该软件包，请使用以下命令:

```
meteor add mrt:moment
```

当然，你如何使用 Moment 将取决于你在做什么，它如此不可思议的地方在于它的灵活性。大多数应用程序都会以某种方式利用时间和日期，无论何时，这一时刻都像是上天的赐福。我建议查看文档以了解它能做的所有事情。

## 6. [NPM](https://atmospherejs.com/meteorhacks/npm)

流星是建立在节点之上，节点本身有无数的软件包可以通过 NPM。要使用这些软件包，最简单的方法是安装 NPM 软件包:

```
meteor add meteorhacks:npm
```

一个`packages.json`文件将在你的项目目录中创建，在这里你可以定义你想在你的项目中使用的包:

```
{
  "redis": "0.8.2",
  "github": "0.1.8"
}
```

然后可以使用一个`npmRequire`函数来利用这个包的功能:

```
var Github = Meteor.npmRequire('github');
```

你只需要在服务器上使用这段代码(它在客户端上不起作用)，如果你遇到问题，很可能与大多数 NPM 软件包的异步特性有关，但这仍然是一个快速简单的方法来为你的作品获得更多的“自由”功能。

## 结论

诚然，说一小撮 Meteor 软件包是“最好的”有点言不由衷，因为在现实中，软件包的价值是由它在特定情况下的适当性决定的。尽管如此，这些选项是一些最广泛适用的可用包，如果您还没有检查它们中的任何一个，那么我建议您这样做。

## 分享这篇文章