# 节点程序包管理器指南:安装 npm +使用命令和模块

> 原文：<https://www.sitepoint.com/npm-guide/>

本分步指南将向您展示如何安装 npm，并掌握节点程序包管理器(npm)命令行界面中的常用命令。

Node.js 使得在服务器上用 JavaScript 编写应用程序成为可能。它构建在 [V8 JavaScript 运行时](https://v8.dev/)之上，用 C++编写——所以速度很快。最初，它旨在作为应用程序的服务器环境，但开发者开始使用它来创建工具，以帮助他们实现[本地任务自动化](https://www.sitepoint.com/implement-task-queue-node-js/)。从那时起，一个全新的基于节点的工具生态系统(如 [Grunt](http://gruntjs.com/) 、 [Gulp](http://gulpjs.com/) 和 [webpack](https://webpack.js.org) )已经进化到改变前端开发的面貌。

为了在 Node.js 中使用这些工具(或包),我们需要能够以一种有用的方式安装和管理它们。这就是节点包管理器 npm 的用武之地。它会安装您想要使用的软件包，并提供一个有用的界面来使用它们。

在本指南中，我们将了解使用 npm 的基础知识。我们将向您展示如何在本地和全局模式下安装软件包，以及删除、更新和安装软件包的某个版本。我们还将向您展示如何使用`package.json`来管理项目的依赖关系。如果你更喜欢视频，为什么不注册 SitePoint Premium，观看我们的免费截屏:[什么是 npm，我如何使用它？](https://www.sitepoint.com/premium/screencasts/what-is-npm-and-how-can-i-use-it?utm_source=blog&utm_medium=articles)

但是在我们开始使用 npm 之前，我们首先必须在我们的系统上安装 Node.js。让我们现在做那件事。

## 用 Node.js 安装 npm

前往 Node.js [下载页面](https://nodejs.org/en/download/)获取你需要的版本。有 Windows 和 Mac 安装程序可用，以及预编译的 Linux 二进制文件和源代码。对于 Linux，您也可以通过软件包管理器[安装 Node，如这里概述的](https://nodejs.org/en/download/package-manager/)。

对于本教程，我们将使用 v12.15.0。在撰写本文时，这是节点的当前[长期支持(LTS)版本。](https://github.com/nodejs/Release#release-schedule)

提示:你也可以考虑用版本管理器安装节点。这就否定了下一节中提出的权限问题。

让我们看看节点安装在哪里，并检查版本:

```
$ which node
/usr/bin/node
$ node --version
v12.15.0 
```

为了验证您的安装是否成功，让我们试试 Node 的 REPL:

```
$ node
> console.log('Node is running');
Node is running
> .help
.break    Sometimes you get stuck, this gets you out
.clear    Alias for .break
.editor   Enter editor mode
.exit     Exit the repl
.help     Print this help message
.load     Load JS from a file into the REPL session
.save     Save all evaluated commands in this REPL session to a file

Press ^C to abort current expression, ^D to exit the repl 
```

Node.js 安装成功了，所以我们现在可以将注意力集中在 npm 上，它包含在安装中:

```
$ which npm
/usr/bin/npm
$ npm --version
6.13.7 
```

## 安装 npm 更新

npm 最初代表 Node Package Manager，是一个独立于 Node.js 的项目。它的更新频率更高。您可以在此[页面](https://www.npmjs.com/package/npm)上查看最新的可用 npm 版本。如果你意识到你有一个旧版本，你可以更新如下。

对于 Linux 和 Mac 用户，使用以下命令:

```
npm install -g npm@latest 
```

对于 Windows 用户来说，这个过程可能稍微复杂一些。这是在[项目的主页](https://github.com/npm/cli/#windows-install-or-upgrade)上写的:

> npm 3 中对 Windows 用户做了许多改进，如果运行 npm 的最新版本，您会有更好的体验。要升级，要么使用[微软的升级工具](https://github.com/felixrieseberg/npm-windows-upgrade)，[下载 Node](https://nodejs.org/en/download/) 的新版本，要么按照[安装/升级 npm](https://npm.community/t/installing-upgrading-npm/251/2) 帖子中的 Windows 升级说明进行。

对于大多数用户来说，升级工具将是最好的选择。要使用它，您需要以管理员身份打开 PowerShell 并执行以下命令:

```
Set-ExecutionPolicy Unrestricted -Scope CurrentUser -Force 
```

这将确保您可以在您的系统上执行脚本。接下来，您需要安装 [npm-windows-upgrade](https://www.npmjs.com/package/npm-windows-upgrade) 工具。安装该工具后，您需要运行它，以便它可以为您更新 npm。在提升的 PowerShell 控制台中执行所有这些操作:

```
npm install --global --production npm-windows-upgrade
npm-windows-upgrade --npm-version latest 
```

## 节点封装模块

npm 可以在本地或全局模式下安装软件包。在本地模式下，它将软件包安装在父工作目录的`node_modules`文件夹中。此位置归当前用户所有。

如果您没有使用[版本管理器](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)(您可能应该使用)，全局包安装在 root 拥有的`{prefix}/lib/node_modules/`中(其中`{prefix}`通常是`/usr/`或`/usr/local`)。这意味着您必须使用`sudo`来全局安装软件包，这可能会在解析第三方依赖关系时导致权限错误，同时也是一个安全问题。

让我们改变这一切！

![Parcel delivery company](img/6e59c6e01c4c0d0e9bdd58624a8762f7.png)

Time to manage those packages

## 更改 npm 全局包的位置

让我们看看`npm config`给出了什么输出:

```
$ npm config list
; cli configs
metrics-registry = "https://registry.npmjs.org/"
scope = ""
user-agent = "npm/6.13.7 node/v12.15.0 linux x64"

; node bin location = /usr/bin/nodejs
; cwd = /home/sitepoint
; HOME = /home/sitepoint
; "npm config ls -l" to show all defaults. 
```

这给了我们关于安装的信息。目前，获取当前的全球位置很重要:

```
$ npm config get prefix
/usr 
```

这是我们想要更改的前缀，以便在我们的主目录中安装全局包。为此，请在您的个人文件夹中创建一个新目录:

```
$ cd ~ && mkdir .node_modules_global
$ npm config set prefix=$HOME/.node_modules_global 
```

通过这个简单的配置更改，我们改变了全局节点包的安装位置。这也在我们的主目录中创建了一个`.npmrc`文件:

```
$ npm config get prefix
/home/sitepoint/.node_modules_global
$ cat .npmrc
prefix=/home/sitepoint/.node_modules_global 
```

我们仍然在 root 拥有的位置安装了 npm。但是因为我们改变了我们的全球包位置，我们可以利用这一点。我们需要再次安装 npm，但这一次是在用户拥有的新位置。这也将安装最新版本的 npm:

```
npm install npm@latest -g 
```

最后，我们需要将`.node_modules_global/bin`添加到我们的`$PATH`环境变量中，这样我们就可以从命令行运行全局包。为此，在您的`.profile`、`.bash_profile`或`.bashrc`后添加以下行，并重启您的终端:

```
export PATH="$HOME/.node_modules_global/bin:$PATH" 
```

现在，我们的`.node_modules_global/bin`将首先被找到，并将使用 npm 的正确版本:

```
$ which npm
/home/sitepoint/.node_modules_global/bin/npm
$ npm --version
6.13.7 
```

提示:如果你使用一个节点版本管理器，你可以避免这一切。查看本教程，了解如何:[使用 nvm](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/) 安装 Node.js 的多个版本。

## 在全局模式下安装 npm 软件包

目前，我们只在全球范围内安装了一个软件包，即 npm 软件包本身。所以让我们改变这种情况，安装 [UglifyJS](https://www.npmjs.com/package/uglify-js) (一个 JavaScript 缩小工具)。我们使用`--global`标志，但这可以缩写为`-g`:

```
$ npm install uglify-js --global
/home/sitepoint/.node_modules_global/bin/uglifyjs -> /home/sitepoint/.node_modules_global/lib/node_modules/uglify-js/bin/uglifyjs
+ uglify-js@3.7.7
added 3 packages from 38 contributors in 0.259s 
```

正如您在输出中看到的，安装了额外的包。这些都是 UglifyJS 的依赖。

## 列出 npm 安装的全局软件包

我们可以用`npm list`命令列出已经安装的全局包:

```
$ npm list --global
home/sitepoint/.node_modules_global/lib
├─┬ npm@6.9.0
│ ├── abbrev@1.1.1
│ ├── ansicolors@0.3.2
│ ├── ansistyles@0.1.3
│ ├── aproba@2.0.0
│ ├── archy@1.0.0
....................
└─┬ uglify-js@3.5.3
  ├── commander@2.19.0
  └── source-map@0.6.1 
```

然而，输出相当冗长。我们可以用`--depth=0`选项来改变这一点:

```
$ npm list -g --depth=0
/home/sitepoint/.node_modules_global/lib
├── npm@6.13.7
└── uglify-js@3.7.7 
```

那更好；现在我们只看到我们已经安装的包以及它们的版本号。

任何全局安装的软件包都可以从命令行获得。例如，下面是如何使用丑陋的包将`example.js`缩小成`example.min.js`:

```
$ uglifyjs example.js -o example.min.js 
```

## 在本地模式下安装 npm 软件包

当您在本地安装软件包时，通常使用一个`package.json`文件。让我们继续创建一个:

```
$ mkdir project && cd project

$ npm init
package name: (project)
version: (1.0.0)
description: Demo of package.json
entry point: (index.js)
test command:
git repository:
keywords:
author:
license: (ISC) 
```

按下`Return`接受默认值，然后再次按下确认您的选择。这将在项目的根目录下创建一个`package.json`文件:

```
{
  "name": "project",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
} 
```

*提示:如果你想更快地生成一个`package.json`文件，使用`npm init --y`。*

除了`main`和`scripts`之外，这些字段都是不言自明的。`main`字段是程序的主要入口点，而`scripts`字段允许您指定在软件包生命周期的不同时间运行的脚本命令。我们现在可以让它们保持原样，但是如果你想了解更多，请参见关于 npm 的 [package.json 文档](https://docs.npmjs.com/files/package.json)和关于使用 npm 作为构建工具的文章[。](https://www.sitepoint.com/guide-to-npm-as-a-build-tool/)

现在让我们试着安装[下划线](http://underscorejs.org/):

```
$ npm install underscore
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN project@1.0.0 No repository field.

+ underscore@1.9.2
added 1 package from 1 contributor and audited 1 package in 0.412s
found 0 vulnerabilities 
```

*注意，创建了一个锁定文件。我们稍后将回到这一点。*

现在，如果我们查看一下`package.json`，我们会看到添加了一个`dependencies`字段:

```
{
  ...
  "dependencies": {
    "underscore": "^1.9.2"
  }
} 
```

## 使用 package.json 管理 npm 依赖项

如您所见，下划线 v1.9.2 安装在我们的项目中。版本号前面的插入符号(`^`)表示在安装时，npm 将获取它所能找到的最高版本的包，其中只有主版本必须匹配(除非存在一个`package-lock.json`文件)。在我们的例子中，这将是低于 v2.0.0 的任何版本。这种版本依赖(major.minor.patch)的方法被称为**语义版本控制**。你可以在这里了解更多:[语义版本化:为什么你应该使用它](https://www.sitepoint.com/semantic-versioning-why-you-should-using/)。

还要注意下划线是作为`dependencies`字段的属性保存的。这已成为 npm 最新版本的默认设置，用于运行应用程序所需的包(如下划线)。也可以通过指定一个`--save-dev`标志将包保存为`devDependency`。`devDependencies`包是用于开发目的的吗——例如，用于运行测试或传输代码。

*提示:您还可以将`private: true`添加到`package.json`中，以防止私有存储库的意外发布，并抑制运行`npm install`时生成的任何警告。*

毫无疑问，使用`package.json`来指定项目依赖关系的最大原因是可移植性。例如，当您克隆其他人的代码时，您所要做的就是在项目根目录下运行`npm i`, NPM 将解析并获取所有必要的包，供您运行应用程序。我们稍后将更详细地讨论这个问题。

在结束这一部分之前，让我们快速检查一下下划线是否有效。在项目根目录下创建一个名为`test.js`的文件，并添加以下内容:

```
const _ = require("underscore");
console.log(_.range(5)); 
```

使用`node test.js`运行该文件，您应该会看到`[0, 1, 2, 3, 4]`输出到屏幕上。

## 卸载 npm 本地软件包

npm 是一个包管理器，所以它必须能够删除包。让我们假设当前的下划线包给我们带来了兼容性问题。我们可以移除软件包并安装旧版本，如下所示:

```
$ npm uninstall underscore
removed 1 package in 0.386s

$ npm list
project@1.0.0 /home/sitepoint/project
└── (empty) 
```

## 安装特定版本的 npm 软件包

我们现在可以在我们想要的版本中安装下划线包。我们通过使用@符号附加一个版本号来实现:

```
$ npm install underscore@1.9.1
+ underscore@1.9.1
added 1 package in 1.574s

$ npm list
project@1.0.0 /home/sitepoint/project
└── underscore@1.9.1 
```

## 更新 npm 软件包

让我们检查一下下划线包是否有更新:

```
$ npm outdated
Package     Current  Wanted  Latest  Location
underscore    1.9.1   1.9.2   1.9.2  project 
```

*Current* 列显示了本地安装的版本。*最新*栏告诉我们软件包的最新版本。而*通缉*栏告诉我们最新版本的软件包，我们可以升级到不破坏我们现有的代码。

还记得之前的`package-lock.json`文件吗？在 npm v5 中引入，这个文件的目的是确保依赖关系在安装项目的所有机器上保持*完全*相同。对于 npm 修改了`node_modules`文件夹或`package.json`文件的任何操作，都会自动生成该文件。

如果你愿意，你可以继续尝试。删除`node_modules`文件夹，然后重新运行`npm i`(这是`npm install`的简称)。npm 将重新安装下划线 v1.9.1，即使我们刚刚看到 v1.9.2 可用。这是因为我们在`package-lock.json`文件中指定了版本 1.9.1:

```
{
  "name": "project",
  "version": "1.0.0",
  "lockfileVersion": 1,
  "requires": true,
  "dependencies": {
    "underscore": {
      "version": "1.9.1",
      "resolved": "https://registry.npmjs.org/underscore/-/underscore-1.9.1.tgz",
      "integrity": "sha512-5/4etnCkd9c8gwgowi5/om/mYO5ajCaOgdzj/oW+0eQV9WxKBDZw5+ycmKmeaTXjInS/W0BzpGLo2xR2aBwZdg=="
    }
  }
} 
```

在`package-lock.json`文件出现之前，不一致的包版本被证明是令开发人员非常头疼的事情。这通常通过使用一个`npm-shrinkwrap.json`文件来解决，该文件必须手动创建。

现在，让我们假设下划线的最新版本修复了我们之前的错误，我们希望将我们的包更新到那个版本:

```
$ npm update underscore
+ underscore@1.9.2
updated 1 package in 0.236s

$ npm list
project@1.0.0 /home/sitepoint/project
└── underscore@1.9.2 
```

*提示:要实现这一点，下划线必须在`package.json`中作为依赖项列出。如果我们有许多想要更新的过时模块，我们也可以执行`npm update`。*

## 搜索 npm 包

在本教程中，我们已经使用了几次`mkdir`命令。有没有节点包有这个功能？让我们用`npm search`:

```
$ npm search mkdir
NAME                      | DESCRIPTION          | AUTHOR          | DATE
mkdir                     | Directory creation…  | =joehewitt      | 2012-04-17
fs-extra                  | fs-extra contains…   | =jprichardson…  | 2019-06-28
mkdirp                    | Recursively mkdir,…  | =isaacs…        | 2020-01-24
make-dir                  | Make a directory…    | =sindresorhus   | 2019-04-01
... 
```

还有( [mkdirp](https://www.npmjs.com/package/mkdirp) )。让我们安装它:

```
$ npm install mkdirp
+ mkdirp@1.0.3
added 1 package and audited 2 packages in 0.384s 
```

现在创建一个`mkdir.js`文件并复制粘贴以下代码:

```
const mkdirp = require('mkdirp');

const made = mkdirp.sync('/tmp/foo/bar/baz');
console.log(`made directories, starting with ${made}`); 
```

接下来，从终端运行它:

```
$ node mkdir.js
made directories, starting with /tmp/foo 
```

## 使用 npm 重新安装项目依赖项

让我们首先再安装一个包:

```
$ npm install request
+ request@2.88.0
added 48 packages from 59 contributors and audited 65 packages in 2.73s
found 0 vulnerabilities 
```

检查`package.json`:

```
"dependencies": {
  "mkdirp": "^1.0.3",
  "request": "^2.88.0",
  "underscore": "^1.9.2"
}, 
```

注意依赖列表会自动更新。如果你想安装一个包而不把它保存在`package.json`中，只需使用`--no-save`参数。

让我们假设您已经将项目源代码克隆到了另一台机器上，并且我们想要安装依赖项。让我们先删除`node_modules`文件夹，然后执行`npm install`:

```
$ rm -R node_modules
$ npm list --depth=0
project@1.0.0 /home/sitepoint/project
├── UNMET DEPENDENCY mkdirp@1.0.3
├─┬ UNMET DEPENDENCY request@2.88.0
  ...
└── UNMET DEPENDENCY underscore@1.9.2

npm ERR! missing: mkdirp@1.0.3, required by project@1.0.0
npm ERR! missing: request@2.88.0, required by project@1.0.0
npm ERR! missing: underscore@1.9.2, required by project@1.0.0
...

$ npm install
added 50 packages from 60 contributors and audited 65 packages in 1.051s
found 0 vulnerabilities 
```

如果你查看你的`node_modules`文件夹，你会发现它又被重新创建了。通过这种方式，您可以轻松地与他人共享您的代码，而不会因为依赖关系而膨胀您的项目和源代码库。

## 管理 npm 的缓存

当 npm 安装一个软件包时，它会保留一个副本，因此下次您想要安装该软件包时，它不需要访问网络。副本缓存在您的主路径的`.npm`目录中:

```
$ ls ~/.npm
anonymous-cli-metrics.json  _cacache  index-v5  _locks  _logs  node-sass 
```

随着时间的推移，这个目录会变得杂乱无章，所以偶尔清理一下是很有用的:

```
$ npm cache clean --force 
```

如果您的系统上有多个想要清理的节点项目，您也可以从工作区中清除所有的`node_module`文件夹:

```
find . -name "node_modules" -type d -exec rm -rf '{}' + 
```

## 使用 npm 审核来扫描依赖关系中的漏洞

您是否注意到了分散在 CLI 输出中的所有这些`found 0 vulnerabilities`？这是因为 npm 中引入了一个新特性，它允许开发人员扫描依赖项以发现已知的安全漏洞。

让我们通过安装旧版本的`express`来尝试一下这个特性:

```
$ npm install express@4.8.0

express@4.8.0
added 36 packages from 24 contributors and audited 123 packages in 2.224s
found 21 vulnerabilities (8 low, 9 moderate, 4 high)
  run `npm audit fix` to fix them, or `npm audit` for details 
```

一旦我们完成安装，我们得到一个快速报告，发现了多个漏洞。您可以运行命令`npm audit`来查看更多详细信息:

```
$ npm audit

 === npm audit security report ===

# Run  npm install express@4.17.1  to resolve 21 vulnerabilities
┌───────────────┬──────────────────────────────────────────────────────────────┐
│ High          │ Regular Expression Denial of Service                         │
├───────────────┼──────────────────────────────────────────────────────────────┤
│ Package       │ negotiator                                                   │
├───────────────┼──────────────────────────────────────────────────────────────┤
│ Dependency of │ express                                                      │
├───────────────┼──────────────────────────────────────────────────────────────┤
│ Path          │ express > accepts > negotiator                               │
├───────────────┼──────────────────────────────────────────────────────────────┤
│ More info     │ https://nodesecurity.io/advisories/106                       │
└───────────────┴──────────────────────────────────────────────────────────────┘

┌───────────────┬──────────────────────────────────────────────────────────────┐
│ Moderate      │ Timing Attack                                                │
├───────────────┼──────────────────────────────────────────────────────────────┤
│ Package       │ cookie-signature                                             │
├───────────────┼──────────────────────────────────────────────────────────────┤
│ Dependency of │ express                                                      │
├───────────────┼──────────────────────────────────────────────────────────────┤
│ Path          │ express > cookie-signature                                   │
├───────────────┼──────────────────────────────────────────────────────────────┤
│ More info     │ https://nodesecurity.io/advisories/134                       │
└───────────────┴──────────────────────────────────────────────────────────────┘ 
```

你会得到一个有漏洞的包的详细列表。如果您查看`Path`字段，它显示了依赖路径。例如，路径`express > accepts > negotiator`意味着快递依赖于`Accepts`包。`Accepts`包依赖于包含漏洞的`negotiator`包。

有两种方法可以解决所有这些问题。我们可以按照建议执行命令`npm install express@4.17.1`，或者运行`npm audit fix`。让我们做后者:

```
$ npm audit fix

+ express@4.17.1
added 20 packages from 14 contributors, removed 7 packages and updated 29 packages in 1.382s
fixed 21 of 21 vulnerabilities in 122 scanned packages 
```

命令`npm audit fix`自动将任何兼容的更新安装到易受攻击的依赖项中。虽然这看起来很神奇，但是请注意，漏洞不可能总是自动修复。如果你正在使用一个已经经历了重大改变的包，这种情况可能会发生，如果更新的话，可能会破坏你当前的项目。对于这种情况，您必须检查您的代码并手动应用修复。

如果你不介意用突破性的改变升级软件包，你也可以运行`npm audit fix --force`。执行完命令后，运行`npm audit`以确保所有的漏洞都已解决。

## npm 别名

您可能已经注意到，运行 npm 命令有多种方式。以下是一些常用的 npm 别名的简要列表:

*   `npm i <package>`:安装本地包
*   `npm i -g <package>`:安装全局包
*   `npm un <package>`:卸载本地包
*   `npm up` : npm 更新包
*   `npm t`:运行测试
*   `npm ls`:列出已安装的模块
*   `npm ll`或`npm la`:在列出模块的同时打印附加的包信息

您也可以一次安装多个软件包，如下所示:

```
$ npm i express momemt lodash mongoose body-parser webpack 
```

如果您想查看所有常见的 npm 命令，只需执行`npm help`查看完整列表。你也可以在我们的文章[中了解更多让你成为 npm 忍者](https://www.sitepoint.com/10-npm-tips-and-tricks/)的 10 个技巧和诀窍。

## 使用 npx 执行包

你可能也会在旅途中听到关于 npx 的谈论。不要把这和 npm 混为一谈。正如我们所了解的，npm 是一个用于*管理*你的包的工具，而 npx 是一个用于*执行*包的工具。它与 npm 版本 5.2+捆绑在一起。

npx 的典型用途是执行一次性命令。例如，假设您想要启动一个简单的 HTTP 服务器。你*可以*在你的系统上全局安装 [http-server 包](https://www.npmjs.com/package/http-server)，如果你会经常使用`http-server`的话，这很好。但是，如果您只想测试这个包，或者希望将全局安装的模块保持在最低限度，那么您可以切换到想要运行它的目录，然后执行下面的命令:

```
npx http-server 
```

这将启动服务器，而无需全局安装任何东西。

你可以[在这里](https://blog.npmjs.org/post/162869356040/introducing-npx-an-npm-package-runner)阅读更多关于 npx 的内容。

## 结论

在本教程中，我们已经介绍了使用 npm 的基础知识。我们已经展示了如何从项目的下载页面安装 Node.js，如何改变全局包的位置(这样我们就可以避免使用`sudo`)，以及如何在本地和全局模式下安装包。我们还讨论了删除、更新和安装包的某个版本，以及管理项目的依赖项。

从这里开始，你可以[比较 npm 和 Yarn](https://www.sitepoint.com/yarn-vs-npm/) 来找出哪个最适合你的需求。你可以使用 nvm 处理更复杂的需求[，节点版本管理器](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)，或者学习[如何托管和发布私有 npm 包](https://www.sitepoint.com/private-npm-packages-verdaccio/)。如果你想探索下一代 JavaScript 运行时，你可以[学习 Deno](https://www.sitepoint.com/learn-deno/) 并阅读[Deno 包管理如何工作](https://www.sitepoint.com/deno-modules/)。

随着每个新版本的发布，npm 正在向前端开发领域大步迈进。据其联合创始人称，其用户群正在发生变化，大多数用户根本不用它来编写 Node。相反，它正在成为人们用来在前端组装 JavaScript 的工具(说真的，你可以用它来安装任何东西)，并且成为编写现代 JavaScript 不可或缺的一部分。

## 分享这篇文章