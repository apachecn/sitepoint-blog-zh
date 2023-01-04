# 如何使用 Bit 在应用程序之间共享 React 组件

> 原文：<https://www.sitepoint.com/sharing-react-components-easily-bit/>

这是组件的时代。为 UI 组件构建的框架，如 React，使我们能够将 UI 分割成单独的、可重用的部分，这些部分可以独立开发和使用。

因此，React 组件通常可以在多个项目和应用程序中进行调整和重用。

通过在我们的项目之间共享它们，我们可以受益于它们的模块化和可重用性，而不是复制它们或重新发明轮子。

![Share code between projects with Bit](img/b8a8e25165b67481e7100ff41f58e2c3.png)

[Bit](https://bitsrc.io) 是一个开源项目，它与 Git 和 npm 一起工作，以加速代码共享，并降低必须分割代码库和对多个包进行更改的开销。相反，您可以轻松地共享任何现有存储库的部分内容，在其他项目中使用它们，并轻松地在它们之间同步更改。

让我们看看怎么做。

## 示例:为什么共享组件很难

再来看[这个 React app](https://github.com/itaymendel/movie-app) 。

如您所见，它在`src/components`目录中包含了八个可重用的组件。他们中的一些人也使用全局风格，这可能也可以在其他应用程序中使用。

现在，假设我想在不同的应用程序中使用其中一个组件。

在 Bit 之前，我的主要选择是拆分这个回购，创建一个新的回购，添加所有需要的配置——包括构建和测试环境——并发布一个包。我必须这样做八次，同时还要在它们之间复制代码。我最终要维护九个存储库，并且必须在它们之间进行更改。

我也可以使用 Lerna，但它会迫使我将项目的代码库重构为 monorepo，即使这样，我仍然必须在我的应用程序中手动定义和维护所有这些包的设置和依赖树。最重要的是，所有这些包的可发现性也很差，这使得它们更难被采用。

面对这种开销，大多数人很可能会以复制粘贴代码告终，这是非常糟糕的。

## 与 Bit 共享 React 组件

[Bit](https://bitsrc.io) 是共享 React 组件的最快方式，开销几乎为零。

Bit 让您可以轻松地共享来自任何现有存储库的组件，并通过 npm 在其他存储库和项目中使用它们，而不是将您的代码库分成更多的存储库。

Bit 背后的思想是将您共享的代码的表示从项目的文件系统中分离出来。

因此，您可以简单地指向您想要共享的组件，并从您的项目中导出它们——而无需实际更改其结构或源代码。

一旦共享，您的组件将可以使用您最喜欢的软件包管理器进行安装。

Bit 的另一个优点是，它可以跟踪在多个存储库中找到的实际源代码，因此您也可以使用它轻松地导入组件的代码，并从任何其他项目中对其进行更改——并让 Bit sync 为您进行更改。

通过将组件的表示从我们实际的文件结构中分离出来，Bit 跟踪我们源代码中的组件，并使我们能够快速地将任何文件或文件子集转换成可重用的组件。使用简单的 glob 模式(见下文)，整个库或项目中的组件可以立即共享，而无需改变我们的源代码本身或文件结构。

任何组件都可以在任何应用程序或项目中单独共享、发现和使用。它也可以从任何项目环境中修改和更新，选择是否以及如何让我们的朋友从他们自己的项目中更新我们的组件(反之亦然)。

共享的组件可以一起分组到“范围”中，范围是可以被认为是共享公共属性的单个组件的“播放列表”的集合。使用免费的 Bit community hub 时，每个组件都与其渲染的视觉效果、测试结果、半自动生成的文档等一起呈现。

下面是从上面的 React 电影应用程序中分享的组件的[范围。
你可以看看这个](https://bitsrc.io/bit/movie-app)[英雄组件](https://bitsrc.io/bit/movie-app/components/hero)。

![React hero component with Bit](img/cdc4f46022568611135016d39f6f8734.png)

不管我们使用什么工具来安装我们的组件，我们都可以完全控制我们的依赖图，并且清楚地了解我们的项目中使用的组件。共享代码也有助于保持我们的 UI 符合我们的设计原则，因为当我们在不同的项目中一次又一次地实现相同的组件时，我们可以很容易地控制变化。

让我们试一个例子。

## 快速启动

下面我们分享一下项目目录结构中的 UI 组件`button`、`login`和`logo`。

```
$ tree
.
├── App.js
├── App.test.js
├── favicon.ico
├── index.js
└── src
    └── components
        ├── button
        │   ├── Button.js
        │   ├── Button.spec.js
        │   └── index.js
        ├── login
        │   ├── Login.js
        │   ├── Login.spec.js
        │   └── index.js
        └── logo
            ├── Logo.js
            ├── Logo.spec.js
            └── index.js

5 directories, 13 files 
```

首先，让我们为项目安装 Bit 并初始化它:

```
npm install bit-bin -g
cd project-directory
bit init 
```

现在让我们使用 Bit 来跟踪这些组件。不要忘记添加[构建](https://docs.bitsrc.io/docs/building-components.html)和[测试](https://docs.bitsrc.io/docs/testing-components.html)环境。

```
bit add src/components/* # use a glob pattern to track multiple components or a single path to track a single component. 
```

现在让我们使用位来锁定一个版本并定义它们的依赖关系:

```
$ bit tag --all 1.0.0
3 components tagged | 3 added, 0 changed, 0 auto-tagged
added components:  components/button@1.0.0, components/login@1.0.0, components/logo@1.0.0 
```

现在让我们将组件共享给一个[远程作用域](https://bitsrc.io):

```
$ bit export username.scopename  # Share components to this Scope
exported 3 components to scope username.scopename 
```

请注意，使用`--eject`标志，您可以从源代码中删除导出的组件，并将其作为包依赖项添加到项目的`package.json`文件中。

就是这样。您现在可以使用 npm 和 Yarn 安装组件，或者使用 Bit 从任何项目中轻松编辑和更新它们的代码。

你可以从这里开始。

### 从任何项目进行更改

如果您正在使用需要修改的代码组件，您可以使用 Bit 导入您的组件，在项目的上下文中对其进行修改，然后再将其共享出来。使用`eject`标志，您甚至可以将组件弹出，使其成为项目的包依赖项。

请注意，Bit 能够跨不同的项目跟踪和同步源代码的变更，即使代码实际上来源于这些项目。如果你愿意，你可以把它看作是没有重复的“管理复制粘贴”。

## 展望未来

[Bit](htps://bitsrc.io) 可能是用最少的开销共享 React 组件的最快方法。使用 Bit，您可以避免复制粘贴代码，并在不同的应用程序中使用组件时，更容易进行更改和维护组件。

您的整个团队可以在一个范围内组织您的所有组件，并创建不同的版本以在不同的地方使用。新的团队成员可以很容易地发现这些组件，并利用您已经编写的现有代码来完成您的工作。

Bit 是一个[开源项目](https://github.com/teambit/bit)，所以请随时[开始](https://bitsrc.io/)，建议反馈或帮助改进它！

[https://www.youtube.com/embed/vm_oOghNEYs](https://www.youtube.com/embed/vm_oOghNEYs)

## 分享这篇文章