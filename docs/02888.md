# 开始使用 Foundation 6 的 CLI 工具

> 原文：<https://www.sitepoint.com/getting-started-with-foundation-6s-cli-tools/>

最近发布的 Foundation 6 给了你很多使用它的方法和工具。你可以[下载](http://foundation.zurb.com/sites/download.html)静态文件，或者你可以使用 [Yeti Launch](http://foundation.zurb.com/develop/yeti-launch.html) ，一个 Mac 专用桌面应用程序(很快就会有 Windows 版本)。

一个不太为人所知的特性是 Foundation 6 提供的一组命令行工具,这也是我将在本文中介绍的内容。Foundation 6 是一个非常灵活的前端框架，除了许多显而易见的 CSS 功能和 JavaScript 插件外，它还有很棒的开发工具。

## 我什么时候需要使用 Foundation 的 CLI 工具？

您可能会问，当您可以访问静态 JavaScript 和 CSS 文件时，为什么还要使用 CLI 工具呢？这些文件工作得很好。您可能需要标准的结构和易于使用的工作流程，这是可以的。

我相信你可以通过这种方式实现你的目标，但是我知道有很多开发人员想要在构建过程中获得更多的控制权。这将包括 SCSS 编译，连接，缩小，图像优化和模板。我更喜欢使用这些额外的工具。因此，在一些使用案例中，CLI 工具可能是更好的选择。

如果您经常使用命令行，但不知道 Foundation 6 在这方面到底提供了什么，或者您不使用命令行，但可能想学习一些新东西，那么本文适合您。

## 运行正常的

要开始，有一些先决条件。你需要安装 [NodeJS](https://nodejs.org) 和应该和 NodeJS 一起安装的 npm 工具。你还需要 [Git](https://git-scm.com/) ， [Gulp](http://gulpjs.com/) ，还有 [Bower](http://bower.io/) ，我们的`foundation-cli`都会用到。您可以通过运行以下命令来安装它们:

```
$ npm install --global gulp bower
```

在一些系统上，你需要超级用户权限，特别是在全局安装 npm 的时候，所以你可能需要在本文描述的命令前使用`sudo`。

### 安装`foundation-cli`

如果您的计算机上已经安装了 Foundation 5 CLI，您将只能访问其中一个命令，这取决于您的命令行环境是如何配置的。所以还是先把旧工具拆了比较好。您可以使用以下命令来完成此操作:

```
$ gem uninstall foundation
$ npm uninstall --global foundation-cli
```

然后安装新的 CLI 工具:

```
$ npm install --global foundation-cli
```

你可以在基金会的文档中阅读更多关于安装[的信息。](http://foundation.zurb.com/sites/docs/installation.html#command-line-tool)

如果你不想在你的系统上安装`foundation-cli`并且你在过去已经安装了 Gulp 和 Bower，你可以简单地用[基础模板](https://github.com/zurb/foundation-zurb-template/)克隆这个库，然后不使用`foundation`命令，你可以使用`gulp`和`npm`命令。一切都应该像使用`foundation-cli`时一样工作。

## 基础 CLI 给了我什么？

如前所述，Foundation CLI 在引擎盖下使用 Gulp 和 Bower。什么是 Gulp 和 Bower？Gulp 是一个工具包，它将帮助您在开发工作流程中自动化痛苦或耗时的任务。在这里，我们可以想到 SCSS 编译，缩小，拼接，但也有图像压缩或其他有用的任务。Bower 是一个用于 web 的包管理器，它允许您通过命令行下载和安装前端库。例如，安装 jQuery 是一行命令:`bower install jquery`。

Foundation CLI 为三个基础框架中的任何一个下载并安装空白模板:站点、应用程序和电子邮件。所有这些模板都可以与 Gulp 和 Bower 一起使用，并带有预先配置的 Gulp 任务和 Bower 安装源。这类似于[约曼](http://yeoman.io/)这样的工具。

## 使用 Foundation CLI

安装完`foundation-cli`后，您可以通过运行以下命令开始使用它:

```
$ foundation new --framework sites --template zurb
```

如你所见，我们使用`foundation`作为名称，而不是`foundation-cli`。此外，我们将只看一看网站`zurb`高级模板的基础。我们需要使用`--framework`标志来选择合适的框架，还需要使用`--template`标志来选择合适的模板。也可以选择`basic`模板，不过我觉得高级的好很多，如果你想仔细看看的话。

安装完成后，您应该有一个项目文件夹，其名称与您在安装过程中提供的名称相同。此外，所有依赖项都应该安装在那里。现在您需要做的就是`cd`进入新创建的项目，并进入您可以运行的文件夹:

```
$ foundation watch
```

魔术来了！这是运行 Gulp 构建和服务器任务以及`watch`命令。这意味着它会触发所有配置好的 Gulp 任务，您可以在代码中看到这些任务。因此，当您运行这个命令时，您应该会在控制台中看到一些信息。目前最重要的是:

```
------------------------------------
  Local: http://localhost:8000
  External: [... your external IP here ...]
-----------------------------------------------
  UI: http://localhost:3001
  UI External: [... your external IP here ...]
-----------------------------------------------
 [BS] Serving files from: dist
```

这里有关于正在运行的服务器的信息。第一个是您的实际应用程序，您还有一个用于[浏览器同步](https://www.browsersync.io/)测试的 UI 服务器(我们稍后会谈到)。您可以看到您的应用程序文件来自`dist`目录，您可以在浏览器中访问`http://localhost:8000`并查看标准的 Foundation 6 初学者模板。

## 看看里面有什么

我认为这是最令人兴奋的部分，但在达到这一点之前，我们必须经历所有的安装过程。

### 文件夹结构、Gulpfile.js、JavaScript/CSS 资产

让我们来看看新创建的项目的文件夹结构。最重要的文件夹是`src`和`dist`。您的开发工作将主要在`src`文件夹中完成，而您的所有生产文件将在`dist`文件夹中准备。您运行的服务器也提供该文件夹中的文件。这意味着你可以准备你想要的工作空间，但是最终，生产就绪文件将会在`dist`文件夹中，这就是你想要的成品。

那么，这怎么可能呢？让我们在这里看一下我们最重要的文件——`gulpfile.js`。如果你不熟悉 Gulp，你可能想看看这个入门教程。Gulp 并不像一开始看起来那么可怕，但它很重要，因为这是所有魔法发生的地方。

Gulp 基于许多 Gulp 插件，这些插件通过简单的 npm 包增加了额外的功能。在这个新的基础项目中，它们在`package.json`中定义。它们也是在运行`foundation new`(如上)时自动下载安装的，所以你不需要担心这个。

当您打开 Gulp 文件时，您可以看到每个任务(如 clean、copy、sass 和 JavaScript)都是使用负责这部分工作的特殊 Gulp 插件类似地定义的。此外，正如您在文件底部看到的，有一些主任务，如“build”或“default ”,它们聚合了其他任务。基础样板配置好了，基本不需要做什么。当然你可以随意调整。

使用这种配置，您可以在`src/assets/scss`文件夹中编写您的 scss，并且可以在`src/assets/js`文件夹中编写您的 JavaScript 文件。你也可以把你的图片放在`src/assets/img`文件夹里。当您运行`foundation watch`或`foundation build`时，所有这些文件都会被复制到`dist`文件夹中。根据选项的不同，可以对图像进行压缩或优化。这都是在`gulpfile.js`里配置的。

Gulp 配置及其插件是另一篇文章的主题。现在让我们看看`.html`文件，并创建高级布局和与帕尼尼的关系。

### 帕尼尼和车把模板

帕尼尼是基金会团队打造的一款非常棒的简单工具。使用 Panini，您可以创建具有一致布局和可重用部分的页面。

在您的`dist`文件夹中，有现成的静态 HTML 文件。当然，如果你只有一个文件，一切都很简单。当您想要创建许多包含几个相同部分的 HTML 文件时，可能会出现问题。它可以是页脚、侧边栏、页眉或许多其他被称为*部分*的元素。

没有 Panini，您需要将所有重复的代码复制到每个 HTML 文件中，如果需要更改，您必须在每个文件中手动完成，或者在您的文本编辑器中进行查找并替换。使用 Panini，您可以在编辑时在一个地方完成所有这些操作，所有文件都将被编辑并复制到`dist`文件夹中。

同样重要的是，Panini 是建立在[手柄](http://handlebarsjs.com/)上的模板库。它也可以在你的 HTML 文件中编译 Markdown。关于帕尼尼的更多信息可以在基金会的文件中找到[。](http://foundation.zurb.com/sites/docs/panini.html)

让我们看看项目中的 Panini 模板文件夹结构。我们需要打开`src`文件夹。这里我们有`data`、`layouts`、`pages`和`partials`。如你所料，在`layouts`文件夹中，我们可以编写我们的主要布局框架。在这里，我们可以定义一个页眉和页脚，它将在所有页面上重复出现。

如果你想只使用一种布局，你可以把文件命名为`default.html`。您将在我们的项目中找到类似的演示文件。如果你想使用多种布局，你可以用特殊的 body 标签`{{> body}}`创建更多的文件(参见 default.html 的例子)，你需要在你的页面中使用特殊的标记来告诉编译器一个页面应该使用哪种布局。它被称为[前体](http://jekyllrb.com/docs/frontmatter/)，看起来像:

```
---
layout: my-custom-layout
---
```

这些标记应该放在页面文件内容的顶部。这只适用于使用这种布局的页面，其他所有页面都将使用默认布局。

我们来看看`pages`文件夹。在这个文件夹中，您将找到`index.html`文件，这是一个内容演示页面。如你所见，它没有任何`html`或`body`标签。这是因为它只是将被注入到先前讨论的`default.html`布局中的内容。
您当然可以添加相似的页面，但有些页面可能会使用不同的布局。

如果你想要一些小的、可重用的 HTML 元素，你可以在`partials`文件夹中创建它们。我们创建的演示项目中没有任何文件，但这真的很简单。只需用一个 HTML 片段创建一个文件，并随意命名该文件。然后在您的布局文件或 pages 文件中，您可以通过使用类似于:`{{> my-partial-file}}`(注意没有文件扩展名)的内容来导入这个部分。仅此而已。所有这些都将被连接、编译并复制到`dist`文件夹中。

还有一个文件夹叫`data`。您可以在这里以`.json`或`.yml`文件的格式提供一些数据。例如，假设我在`data`文件夹中有一个`myList.json`文件，其内容如下:

```
{
  items: ["item 1", "item 2", "item 3"]
}
```

在布局页面或部分 HTML 文件中，我可以使用类似于:

```
<ul>
  {{#each myList.items}}
    <li class="item-name">{{this}}</li>
  {{/each}}
</ul>
```

这让您可以遍历 JSON 数据并生成一些 HTML。我们应该得到的是一个带有数组名称的条目列表。

如你所见，当你想创建复杂的 HTML 结构并且不想重复时，Panini 是一个非常有用的工具。

### 浏览器同步:同步浏览器测试和实时重新加载

我讨论的 Foundation CLI 工具的最后一个预配置特性是 [BrowserSync](https://www.browsersync.io/) 。你可能知道，前端工作很难，因为你需要在许多设备和许多分辨率上测试你的网站。现在想象一下，你有一张大桌子，上面有许多不同的设备连接到你的网站。当你点击某个东西或滚动页面时，所有设备都会这样做。这很好，因为你可以实时看到哪些地方需要纠正，哪些地方做得不好。

我们创建的项目会自动提供您的外部 IP 地址(见上文)。您可以将它粘贴到所有不同的设备浏览器中，以连接到同一个 BrowserSync 引擎并开始测试。

BrowserSync 还提供实时更改，因此，如果您保存了某些内容，它会出现在浏览器窗口中，而无需手动刷新页面。它还会出现在所有连接的设备上，您可以通过`foundation-cli`和`zurb`模板免费获得，无需任何额外工作。多棒啊。

## 最后的话

我个人认为 Zurb 基金会团队在为开发者提供优秀的工具和支架方面做得非常好。这也很重要。不仅仅是一些现成的插件和 CSS 样式。使用 Foundation for Sites 6 是一种很棒的体验。想象一下，在不进行后端编程的情况下，您可以使用 Foundation CLI 工具完成多少工作。你可以创建静态网站和博客，它们也可以非常先进。更不用说很多情况下，静态的博客和网站甚至更快更好。

我真的鼓励你好好看看帕尼尼。您还可以找到一些关于 [npm 软件包自述文件](https://www.npmjs.com/package/panini)的文档。它可以做很多我在这里没有描述过的很棒的事情，比如 Markdown 编译或者定制助手。您可以阅读关于 Gulp 的内容，并仔细查看由`foundation-cli`工具生成的`zurb`模板/项目中使用的所有 Gulp 任务。

还有一点——我没有提到压缩的 CSS 和 JavaScript 文件。如果您使用的是`foundation watch`命令，您将在 dist 文件夹中拥有 CSS 和 JavaScript 文件，但是它们将被解压缩。如果您想要该文件夹中的生产就绪文件，只需运行`foundation build`。如果您想了解更多关于`foundation`命令的内容，只需运行`foundation help`命令。当需要更新项目中的依赖项时，可以运行`foundation update`。

所以总而言之，我希望你在这里学到了一些东西。如果你有问题，请在评论中告诉我，或者查看我的个人资料，以获得与我联系的不同方式。

## 分享这篇文章