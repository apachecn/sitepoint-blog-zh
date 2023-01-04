# Deno 模块:用法、最佳实践和节点模块导入

> 原文：<https://www.sitepoint.com/deno-modules/>

了解 Deno 模块——如果您来自 Node.js，您将遇到的最大的工作流变化。了解它们如何工作以及如何最好地使用它们，如何在 Deno 中利用 Node.js 模块和 npm 包，等等。

[Node.js](https://nodejs.org/) 是基于 Chrome 的 V8 引擎的 JavaScript 运行时，由 Ryan Dahl 开发，于 2009 年发布。

[Deno](https://www.sitepoint.com/deno-guide) 是基于 Chrome 的 V8 引擎的 JavaScript 运行时，由 Ryan Dahl 开发，2020 年发布。它是得益于十年的后见之明而创立的。这并不一定使它成为 Node.js 的续集或优于 node . js，但它偏离了这条道路。

另请参见:

*   [我们的 Deno 指南](https://www.sitepoint.com/deno-guide/)，包括我们的 Deno 内容索引(滚动到末尾)
*   [node . js 与 Deno](https://www.sitepoint.com/node-vs-deno/) 的比较，以及针对具体情况选择合适工具的指南

主要区别:Deno 本身支持类型脚本、安全性、测试和浏览器 API。模块处理受到的关注较少，但它可能是创建 JavaScript 应用程序的最大变化。在讨论 Deno 之前，让我带你回到一个更简单的时代…

## Node.js 模块

JavaScript 在 2009 年没有标准的模块系统。这部分是因为它的浏览器传统，而 ES6 / ES2015 还需要几年时间。

Node.js 不提供模块是不可思议的，所以它采用了社区变通办法中的 CommonJS。这导致了节点包管理器(Node Package Manager)或 [npm](https://www.npmjs.com/) 的开发，它允许开发人员轻松地搜索、使用和发布他们自己的 JavaScript 模块。

npm 的使用呈指数增长。它已经成为有史以来最受欢迎的软件包管理器，到 2020 年中期，托管近 150 万个模块，每天发布 800 多个新模块(来源:[modulecounts.com](http://www.modulecounts.com/))。

## Deno 模块

Deno 选择 [ES2015 模块](https://www.sitepoint.com/understanding-es6-modules/)，你`import`从绝对或相对网址:

```
import { something } from 'https://somewhere.com/somehow.js'; 
```

该 URL 上的脚本必须`export`相应地执行功能或其他值，例如

```
export function something() {
  console.log('something was executed');
} 
```

Deno 使用了与现代网络浏览器相同的模块系统。

[Node.js 也支持 ES2015 模块](https://nodejs.org/api/esm.html) …但它很复杂，仍处于试验阶段。CommonJS 和 ES2015 模块看起来相似，但工作方式不同:

*   CommonJS 在执行代码时按需从文件系统加载依赖项。
*   ES 模块是根据 URL 预先解析的，以便在执行代码之前解析进一步的导入。

Node.js 必须继续支持 CommonJS 以及处理混合的 ES 模块。因此，它假定:

1.  以`.cjs`结尾的文件使用 CommonJS
2.  以`.mjs`结尾的文件使用 es 模块
3.  以`.js`结尾的文件是普通的*，除非最近的`package.json`集`"type": "module"`或`node`的*是用`--input-type=module`选项执行的。

可以理解为什么 Deno 选择了单一标准模块系统。然而，npm 对 Node 的成功至关重要，所以发现 Deno 取消它令人惊讶。

没有包管理器。

对 npm 的一个批评是每个项目目录的庞大规模。当模块需要其他模块的特定版本时，它可以达到数百兆字节。

![Node modules mass](img/cea0daa02b9c6c7c79a3e6ee2f776adb.png)

第一次在脚本中遇到模块的 URL 时，Deno 会将模块下载并缓存在全局目录中。因此，无论有多少项目引用它，都只需要一个特定模块版本的副本。

我知道你在想:*“啊，但是如果……”怎么办*

…但是 Deno 有解决模块 URL 引起的问题的选项。

## 不可靠的 URL

URL 可能会暂时失败、改变或永远消失。这对任何软件包管理器来说都是一个问题，npm 过去也遇到过这个问题(它也允许从 URL 安装[)。](https://docs.npmjs.com/cli/install)

对于任务关键的 Node.js 应用程序，建议将您的`node_modules`目录添加到项目的 Git/other 存储库中。

Deno 支持类似的选项。您可以将环境变量`DENO_DIR`设置为当前项目中的一个目录路径，例如

```
DENO_DIR=~/myproject/deno_modules` 
```

> 在 Windows `cmd`中使用:

```
> set DENO_DIR="C:\myproject\deno_modules" 
```

或 Windows Powershell:

```
> $env:DENO_DIR="C:\myproject\deno_modules" 
```

当您的应用程序运行时，Deno 会将模块缓存到该目录中，以便将它们添加到项目的源代码控制库中。

您还可以考虑将您的依赖项捆绑到一个 JavaScript 或 TypeScript 文件中。Deno [bundle 命令](https://deno.land/manual/tools/bundler)可以一步完成:

```
deno bundle myscript.js myscript.bundle.js 
```

其中`myscript.js`是您通常使用`deno run`执行的输入脚本。由此产生的自包含的`myscript.bundle.js`文件可以被部署到一个活动的服务器上。

**与顶层捆绑等待**

Deno 支持顶级`await`:不需要将`await`调用包装在匿名`async`函数中。不幸的是，[顶层 await 无法绑定](https://github.com/denoland/deno/issues/3301)，因此必须添加一个包装函数。这是一个已知的问题，将在未来的版本中修复。

最后:*警惕不常见网址上的随机 Deno 模块！拥有良好文档和社区输入的 Deno、Github 或 Bitbucket URL 通常会更安全。*

## 模块版本控制

理想情况下，应该对模块 URL 进行版本控制，以便您引用特定的代码版本。例如， [Deno 标准库](https://deno.land/std)允许你加载一个[特定版本的 HTTP 服务器模块](https://deno.land/std@0.61.0/http/server.ts):

```
import { serve } from 'https://deno.land/std@0.61.0/http/server.ts'; 
```

可以引用[主分支](https://deno.land/std/http/server.ts)来代替:

```
import { serve } from 'https://deno.land/std/http/server.ts'; 
```

但是这会下载最新版本，将来的版本可能会与您的应用程序不兼容。

使用相似的版本约定在你自己的服务器上发布 Deno 模块是可能的，但是当它流行起来的时候，你的站点会收到大量的流量。一个更健壮的方法是在一个服务上使用一个存储库，比如 [GitHub](https://github.com/) ，并为每个版本分配一个 git 标签。诸如 denopkg.com 的[和 unpkg.com 的](https://denopkg.com/)[之类的服务可以用来提供一个公开版本的模块 URL。](https://unpkg.com/)

## 多个模块提及

在整个应用程序的代码库中，您可能需要在许多文件中引用同一个模块 URL。当您想要更新该模块时，需要在多个地方更改 URL。搜索和替换可以工作，但是它很笨重，容易出错，并且增加了合并冲突的机会。

或者，您可以使用一个单独的依赖文件来导入您在项目中使用的每个模块。它通常被命名为`deps.js`或`deps.ts`:

```
// deps.js: module dependencies

// all std path module funtions
export * as path from 'https://deno.land/std@0.61.0/path/mod.ts';

// some std datetime module functions
export { parseDate, currentDayOfYear } from 'https://deno.land/std@0.61.0/datetime/mod.ts'; 
```

然后，您可以在任何其他项目文件中引用来自`deps.js`的 Deno 模块:

```
import { path, currentDayOfYear } from './deps.js';

console.log( path.sep );
console.log( currentDayOfYear() ); 
```

当模块更新时，您只需要在`deps.js`中更改一个 URL 引用。

另一个选项是导入映射。这是一个小的 JSON 文件，通常命名为`import_map.json`，它为完整或部分 URL 分配一个名称:

```
{
  "imports": {
    "path/": "https://deno.land/std@0.61.0/path/",
    "datetime/": "https://deno.land/std@0.61.0/datetime/"
  }
} 
```

您可以在任何脚本中引用导入映射名称:

```
import * as path from 'path/mod.ts';
import { currentDayOfYear } from 'datetime/mod.ts';

console.log( path.sep );
console.log(currentDayOfYear()); 
```

然后在使用`deno run`执行应用程序时导入 JSON 文件:

```
deno run \
  --importmap=import_map.json \
  --unstable \
  myscript.js 
```

导入地图目前是一个不稳定的特性，因此需要`--unstable`标志。该功能在未来的 Deno 版本中可能会有所变化。

## 调查完整性

从 URL 引用的代码可能会在您不知情的情况下被更改或攻击。引人注目的网站已经受到威胁，因为它们直接链接到第三方客户端代码。想象一下，如果一个脚本可以访问服务器资源，它会造成多大的破坏。

> Deno 具有内置的安全性，因此脚本必须使用诸如`--allow-read`和`--allow-net`之类的标志来执行，以限制文件系统和网络访问。这将有助于防止一些问题，但它不能代替验证模块的完整性！

Deno 提供了一个[完整性检查选项](https://deno.land/manual/linking_to_external_code/integrity_checking)。如果你使用一个[单一依赖文件(如上所述)](#multiple-module-mentions)，这是最简单的:

```
// deps.js: module dependencies

// all std path module funtions
export * as path from 'https://deno.land/std@0.61.0/path/mod.ts';

// some std datetime module functions
export { parseDate, currentDayOfYear } from 'https://deno.land/std@0.61.0/datetime/mod.ts'; 
```

以下`deno`命令生成一个`lock.json`文件，包含所有导入的 Deno 模块的校验和:

```
deno cache --lock=lock.json --lock-write deps.js 
```

当其他开发人员克隆您的项目时，他们可以重新加载每个模块并验证每个模块的完整性，以保证它们与您的项目完全相同:

```
deno cache --reload --lock=lock.json deps.js 
```

Deno 不执行完整性检查。最好将这些过程作为自动化 Git 挂钩或类似工具运行。

## 使用 Node.js 模块

许多 Node.js APIs 已经被复制用于 Deno——参见[deno.land/std/node](https://deno.land/std/node)。这不是一个完整的列表，但是您会发现常见的文件、事件、缓冲区和实用程序模块。

在[deno.land/x](https://deno.land/x)有将近 800 个第三方 Deno 模块。有类似 Express.js 的框架、数据库驱动、加密功能、命令行工具等等。

你还会发现流行模块的精选列表，如 [Awesome Deno](https://github.com/denolib/awesome-deno) 。

然而，您*可能*能够导入 150 万个 Node.js 模块中的任何一个。一些 cdn 可以将 npm/CommonJS 包转换为 ES2015 模块 URL，包括:

*   [Skypack.dev](https://www.skypack.dev/)
*   [jspm.org](https://jspm.org/)
*   [unpkg.com](https://unpkg.com/)*(添加一个`?module`查询字符串到一个网址)*

你需要的模块在 Deno 中是否工作没有问题是另一回事。

幸运的是，随着 JavaScript 运行时生态系统的发展，无需特殊处理就能在 Node.js 和 Deno 上工作的跨平台模块很可能会出现。

## 更多模块问题

引用模块 URL 是有争议的，对于那些来自广受欢迎的 npm 的人来说可能会令人不安。也就是说，Deno 简化了 JavaScript 模块的使用。它解决了几个 npm 批评，同时减轻了 ES2015 模块的许多潜在副作用。

但是它远非完美。

发布 npm 模块没有痛苦，搜索[npmjs.com](https://www.npmjs.com/)也很简单。你的搜索词可能会返回 500 个结果，但是通过根据[流行度、质量和维护](https://npms.io/about)因素对包进行排序，选择瘫痪被最小化。

向 Deno 的[第三方模块列表](https://deno.land/x)提交代码难度更大。模块必须通过自动化测试，但没有质量保证，搜索结果按字母顺序排列。一旦达到几千个模块，现有系统就不太可能持续。

在 npm 中更新包也很容易。你可以运行`npm outdated`来查看更新列表，或者当[在`package.json`中引用更宽松的版本号](https://docs.npmjs.com/about-semantic-versioning#using-semantic-versioning-to-specify-update-types-your-package-can-accept)时只运行`npm install`。

Deno 中没有对等的更新检查选项。类似于包管理器的项目是可用的，包括 [Trex](https://github.com/crewdevio/Trex) 、 [Update Deno Dependencies](https://github.com/hayd/deno-udd) 和 [deno-check-updates](https://deno.land/x/deno_check_updates) ，但是这些项目通常依赖于[导入映射](#multiple-module-mentions)，并且总是依赖于语义版本化的 URL。

## 该不该转 Deno？

Node.js 没有死。它是成熟的，在运行时背后有十年的模块、技术、文档和经验。

Deno 利用了很多这方面的知识，但它非常新，将在未来几年迅速发展。现在押注 Deno 成为一个主要的应用程序可能还为时过早，但对于较小的项目来说风险较小。那些已经在使用 TypeScript 或来自其他语言的人可能会享受更轻松的体验，但 Node.js 开发人员在转换到 Deno 并返回时不会有任何问题。

然而，Deno 有一个有趣的好处:

*   它的模块系统与客户端 JavaScript 相同
*   它实现了许多浏览器 API:您可以引用一个`window`对象，设置事件监听器，启动 Web Workers，用 Fetch() API 发出远程服务器请求，等等。

在客户机或服务器上工作的同构 JavaScript 库的梦想已经向前迈出了重要的一步。

## 德诺基金会

跟上德诺的步伐。我们的 Deno Foundations collection 帮助您迈出进入 Deno 世界以及更远的第一步，并且我们会不断增加它。我们将为您带来成为职业选手所需的教程。您可以随时参考我们的索引，因为它在我们对 Deno 的介绍结束时更新:

➤ [德诺基金会](https://www.sitepoint.com/deno-guide#foundations)

## 分享这篇文章