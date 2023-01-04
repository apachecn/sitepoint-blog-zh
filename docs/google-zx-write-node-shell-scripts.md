# 如何用 Google 的 zx 库在 Node 中编写 Shell 脚本

> 原文：<https://www.sitepoint.com/google-zx-write-node-shell-scripts/>

在本文中，我们将了解 Google 的 zx 库提供了什么，以及我们如何使用它来编写 Node.js 的 shell 脚本。然后，我们将了解如何通过构建一个命令行工具来使用 zx 的特性，该工具可以帮助我们为新的 Node.js 项目进行引导配置。

## 编写 Shell 脚本:问题

创建一个 shell 脚本——一个由 Bash 或 zsh 之类的 shell 执行的脚本——是自动化重复任务的好方法。Node.js 似乎是编写 shell 脚本的理想选择，因为它为我们提供了许多核心模块，并允许我们导入我们选择的任何库。它还让我们能够访问 JavaScript 提供的语言特性和内置函数。

但是如果您尝试编写一个在 Node.js 下运行的 shell 脚本，您可能会发现它不像您希望的那样流畅。您需要为子进程编写特殊的处理，注意转义命令行参数，然后以`stdout`(标准输出)和`stderr`(标准错误)结束。它不是特别直观，并且会使 shell 脚本变得非常笨拙。

Bash shell 脚本语言是编写 shell 脚本的流行选择。不需要编写代码来处理子进程，它有内置的语言特性来处理`stdout`和`stderr`。但是用 Bash 编写 shell 脚本也不容易。语法可能相当混乱，使得实现逻辑或者处理诸如提示用户输入之类的事情变得困难。

Google 的 zx [库](https://www.npmjs.com/package/zx)帮助使用 Node.js 编写 shell 脚本变得高效而有趣。

### 跟随的要求

遵循本文有一些要求:

*   理想情况下，您应该熟悉 JavaScript 和 Node.js 的基础知识。
*   您将需要在终端中舒适地运行命令。
*   你需要安装 [Node.js](https://nodejs.org/en/download/) > = v14.13.1。

本文中的所有代码都可以在 GitHub 上获得。

## 谷歌的 zx 是如何工作的？

Google 的 zx 提供了封装子进程的创建以及处理这些进程中的`stdout`和`stderr`的功能。我们将使用的主要函数是`$`函数。这是一个实际应用的例子:

```
import { $ } from "zx";

await $`ls`; 
```

下面是执行这段代码的输出:

```
$ ls
bootstrap-tool
hello-world
node_modules
package.json
README.md
typescript 
```

上面例子中的 JavaScript 语法可能看起来有点奇怪。它使用了一种叫做[标记模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)的语言特性。功能上和写`await $("ls")`一样。

Google 的 zx 提供了其他几个实用函数来简化 shell 脚本，例如:

*   `cd()`。这允许我们改变当前的工作目录。
*   `question()`。这是 Node.js [readline](https://nodejs.org/api/readline.html) 模块的包装器。它使得提示用户输入变得简单明了。

除了 zx 提供的实用函数之外，它还为我们提供了几个流行的库，例如:

*   。这个库允许我们给脚本的输出添加颜色。
*   **[最小](https://www.npmjs.com/package/minimist)** 。解析命令行参数的库。然后它们被暴露在一个`argv`物体下。
*   **[取](https://www.npmjs.com/package/node-fetch)** 。一个流行的[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 的 Node.js 实现。我们可以用它来发出 HTTP 请求。
*   **[-fs-extra](https://www.npmjs.com/package/fs-extra)**。一个公开 Node.js 核心 [fs 模块](https://nodejs.org/api/fs.html)的库，以及许多使文件系统工作更容易的附加方法。

现在我们知道了 zx 给了我们什么，让我们用它创建我们的第一个 shell 脚本。

## 你好，谷歌 zx 世界

首先，让我们创建一个新项目:

```
mkdir zx-shell-scripts
cd zx-shell-scripts

npm init --yes 
```

然后我们可以安装`zx`库:

```
npm install --save-dev zx 
```

*注意:`zx`文档建议使用 npm 全局安装库。相反，通过将它作为项目的本地依赖项来安装，我们可以确保 zx 总是被安装，并控制我们的 shell 脚本使用的版本。*

### 顶级`await`

为了在`async`函数之外使用 Node.js 中的顶级`await`—`await`——我们需要在 [ECMAScript (ES)模块](https://nodejs.org/api/esm.html)中编写代码，这些模块支持顶级`await`。我们可以通过在我们的`package.json`中添加`"type": "module"`来表明一个项目中的所有模块都是 es 模块，或者我们可以将单个脚本的文件扩展名设置为`.mjs`。对于本文中的例子，我们将使用`.mjs`文件扩展名。

### 运行命令并捕获其输出

让我们创建一个名为`hello-world.mjs`的新脚本。我们将添加一个 [shebang 行](https://dev.to/meleu/what-the-shebang-really-does-and-why-it-s-so-important-in-your-shell-scripts-2755)，它告诉操作系统(OS) [内核](https://en.wikipedia.org/wiki/Kernel_(operating_system))用`node`程序运行脚本:

```
#! /usr/bin/env node 
```

现在我们将添加一些使用 zx 运行命令的代码。

在下面的代码中，我们运行一个命令来执行 [ls](https://en.wikipedia.org/wiki/Ls) 程序。`ls`程序将列出当前工作目录(脚本所在的目录)中的文件。我们将从该命令的流程中捕获标准输出，将其存储在一个变量中，然后将其记录到终端:

```
// hello-world.mjs

import { $ } from "zx";

const output = (await $`ls`).stdout;

console.log(output); 
```

*注意:`zx`文档建议将`/usr/bin/env zx`放在脚本的 shebang 行中，但是我们使用了`/usr/bin/env node`来代替。这是因为我们已经安装了`zx`作为项目的本地依赖项。然后我们显式地从`zx`包中导入我们想要使用的函数和对象。这有助于弄清楚我们脚本中使用的依赖项来自哪里。*

然后，我们将使用 [chmod](https://en.wikipedia.org/wiki/Chmod) 使脚本可执行:

```
chmod u+x hello-world.mjs 
```

让我们运行我们的脚本:

```
./hello-world.mjs 
```

我们现在应该会看到以下输出:

```
$ ls
hello-world.mjs
node_modules
package.json
package-lock.json
README.md
hello-world.mjs
node_modules
package.json
package-lock.json
README.md 
```

您会在我们的 shell 脚本的输出中注意到一些事情:

*   我们运行的命令(`ls`)包含在输出中。
*   该命令的输出显示两次。
*   在输出的末尾有一个额外的新行。

默认情况下，`zx`在`verbose`模式下运行。它将输出您传递给`$`函数的命令，并输出该命令的标准输出。我们可以通过在运行`ls`命令之前添加以下代码行来改变这种行为:

```
$.verbose = false; 
```

大多数命令行程序，比如`ls`，会在输出的末尾输出一个新的行字符，以使输出在终端中更具可读性。这对可读性很好，但是因为我们将输出存储在一个变量中，所以我们不需要额外的新行。我们可以用 JavaScript [String#trim()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/trim) 函数来摆脱它:

```
- const output = (await $`ls`).stdout; + const output = (await $`ls`).stdout.trim(); 
```

如果我们再次运行我们的脚本，我们会看到事情看起来好得多:

```
hello-world.mjs
node_modules
package.json
package-lock.json 
```

## 使用 Google 的 zx 和 TypeScript

如果我们想在 TypeScript 中编写使用`zx`的 shell 脚本，我们需要考虑一些小的差异。

注意:TypeScript 编译器提供了许多配置选项，允许我们调整它如何编译我们的 TypeScript 代码。考虑到这一点，下面的 TypeScript 配置和代码设计为在大多数版本的 TypeScript 下工作。

首先，让我们安装运行 TypeScript 代码所需的依赖项:

```
npm install --save-dev typescript ts-node 
```

[ts-node](https://www.npmjs.com/package/ts-node) 包提供了一个 TypeScript 执行引擎，允许我们传输和运行 TypeScript 代码。

我们需要创建一个包含以下配置的`tsconfig.json`文件:

```
{
  "compilerOptions": {
    "target": "es2017",
    "module": "commonjs"
  }
} 
```

现在让我们创建一个名为`hello-world-typescript.ts`的新脚本。首先，我们将添加一个 shebang 行，告诉我们的操作系统内核使用`ts-node`程序运行脚本:

```
#! ./node_modules/.bin/ts-node 
```

为了在我们的类型脚本代码中使用`await`关键字，我们需要将它包装在一个立即调用的函数表达式(IIFE)中，正如 [zx 文档](https://www.npmjs.com/package/zx)中所推荐的:

```
// hello-world-typescript.ts

import { $ } from "zx";

void (async function () {
  await $`ls`;
})(); 
```

然后，我们需要使脚本可执行，以便我们可以直接执行它:

```
chmod u+x hello-world-typescript.ts 
```

当我们运行脚本时:

```
./hello-world-typescript.ts 
```

…我们应该会看到以下输出:

```
$ ls
hello-world-typescript.ts
node_modules
package.json
package-lock.json
README.md
tsconfig.json 
```

用 TypeScript 中的`zx`编写脚本类似于使用 JavaScript，但是需要一点额外的配置和包装我们的代码。

## 构建项目引导工具

既然我们已经学习了使用 Google 的 zx 编写 shell 脚本的基本知识，我们将使用它来构建一个工具。这个工具将自动创建一个通常很耗时的过程:引导新 Node.js 项目的配置。

我们将创建一个交互式 shell 脚本来提示用户输入。它还将使用`zx`捆绑的`chalk`库来突出显示不同颜色的输出，并提供友好的用户体验。我们的 shell 脚本还将安装我们的新项目需要的 npm 包，因此我们可以马上开始开发了。

### 入门指南

让我们创建一个名为`bootstrap-tool.mjs`的新文件，并添加一个 shebang 行。我们还将从`zx`包中导入将要使用的函数和模块，以及 Node.js 核心`path`模块:

```
#! /usr/bin/env node

// bootstrap-tool.mjs

import { $, argv, cd, chalk, fs, question } from "zx";

import path from "path"; 
```

与我们之前创建的脚本一样，我们希望使我们的新脚本可执行:

```
chmod u+x bootstrap-tool.mjs 
```

我们还将定义一个 helper 函数，它以红色文本输出一条错误消息，并以`1`的[错误退出代码](https://en.wikipedia.org/wiki/Exit_status)退出 Node.js 进程:

```
function exitWithError(errorMessage) {
  console.error(chalk.red(errorMessage));
  process.exit(1);
} 
```

当我们需要处理错误时，我们将通过 shell 脚本在不同的地方使用这个助手函数。

### 检查相关性

我们正在创建的工具将需要运行使用三个不同程序的命令:`git`、`node`和`npx`。我们可以使用的库[来帮助我们检查这些程序是否已经安装并且可以使用。](https://www.npmjs.com/package/which)

首先，我们需要安装`which`包:

```
npm install --save-dev which 
```

然后我们可以导入它:

```
import which from "which"; 
```

然后我们将创建一个使用它的`checkRequiredProgramsExist`函数:

```
async function checkRequiredProgramsExist(programs) {
  try {
    for (let program of programs) {
      await which(program);
    }
  } catch (error) {
    exitWithError(`Error: Required command ${error.message}`);
  }
} 
```

上面的函数接受一组程序名。它遍历数组，并为每个程序调用`which`函数。如果`which`找到了程序的路径，就会返回。否则，如果程序丢失，它将抛出一个错误。如果任何程序丢失，我们调用我们的`exitWithError`助手来显示一条错误消息并停止运行脚本。

我们现在可以添加一个对`checkRequiredProgramsExist`的调用来检查我们的工具所依赖的程序是否可用:

```
await checkRequiredProgramsExist(["git", "node", "npx"]); 
```

### 添加目标目录选项

因为我们正在构建的工具将帮助我们引导新的 Node.js 项目，所以我们希望运行我们在项目目录中添加的任何命令。我们现在将在脚本中添加一个`--directory`命令行参数。

`zx`捆绑了[minimit](https://www.npmjs.com/package/minimist)包，它解析传递给我们脚本的任何命令行参数。这些解析后的命令行参数由`zx`包作为`argv`提供。

让我们添加一个名为`directory`的命令行参数检查:

```
let targetDirectory = argv.directory;
if (!targetDirectory) {
  exitWithError("Error: You must specify the --directory argument");
} 
```

如果`directory`参数已经被传递给我们的脚本，我们想要检查它是存在的目录的路径。我们将使用由`fs-extra`提供的`fs.pathExists`方法:

```
targetDirectory = path.resolve(targetDirectory);

if (!(await fs.pathExists(targetDirectory))) {
  exitWithError(`Error: Target directory '${targetDirectory}' does not exist`);
} 
```

如果目标目录存在，我们将使用由`zx`提供的`cd`函数来改变我们当前的工作目录:

```
cd(targetDirectory); 
```

如果我们现在运行没有`--directory`参数的脚本，我们会收到一个错误:

```
$ ./bootstrap-tool.mjs

Error: You must specify the --directory argument 
```

### 检查全局 Git 设置

一会儿，我们将在项目目录中初始化一个新的 Git 存储库，但是首先我们要检查 Git 是否有它需要的配置。我们希望确保代码托管服务(如 [GitHub](https://github.com/) )能够正确识别我们的提交。

为此，让我们创建一个`getGlobalGitSettingValue`函数。它将运行命令`git config`来检索 Git 配置设置的值:

```
async function getGlobalGitSettingValue(settingName) {
  $.verbose = false;

  let settingValue = "";
  try {
    settingValue = (
      await $`git config --global --get ${settingName}`
    ).stdout.trim();
  } catch (error) {
    // Ignore process output
  }

  $.verbose = true;

  return settingValue;
} 
```

你会注意到我们正在关闭 zx 默认设置的`verbose`模式。这意味着，当我们运行`git config`命令时，该命令和它发送到标准输出的任何内容都不会显示。我们在函数的结尾重新打开了详细模式，这样我们就不会影响我们稍后在脚本中添加的任何其他命令。

现在我们将创建一个接受 Git 设置名称数组的`checkGlobalGitSettings`。它将遍历每个设置名称，并将其传递给`getGlobalGitSettingValue`函数以检索其值。如果设置没有值，我们将显示一条警告消息:

```
async function checkGlobalGitSettings(settingsToCheck) {
  for (let settingName of settingsToCheck) {
    const settingValue = await getGlobalGitSettingValue(settingName);
    if (!settingValue) {
      console.warn(
        chalk.yellow(`Warning: Global git setting '${settingName}' is not set.`)
      );
    }
  }
} 
```

让我们调用添加对`checkGlobalGitSettings`的调用，并检查`user.name`和`user.email` Git 设置是否已经设置:

```
await checkGlobalGitSettings(["user.name", "user.email"]); 
```

### 初始化新的 Git 存储库

我们可以通过添加以下命令在项目目录中初始化一个新的 Git 存储库:

```
await $`git init`; 
```

### 生成一个`package.json`文件

每个 Node.js 项目都需要一个`package.json`文件。在这里，我们定义关于项目的元数据，指定项目所依赖的包，并添加小的实用程序脚本。

在为我们的项目生成一个`package.json`文件之前，我们将创建几个助手函数。第一个是`readPackageJson`函数，它将从项目目录中读取一个`package.json`文件:

```
async function readPackageJson(directory) {
  const packageJsonFilepath = `${directory}/package.json`;

  return await fs.readJSON(packageJsonFilepath);
} 
```

然后，我们将创建一个`writePackageJson`函数，我们可以用它将更改写入项目的`package.json`文件:

```
async function writePackageJson(directory, contents) {
  const packageJsonFilepath = `${directory}/package.json`;

  await fs.writeJSON(packageJsonFilepath, contents, { spaces: 2 });
} 
```

我们在上述函数中使用的`fs.readJSON`和`fs.writeJSON`方法是由`fs-extra`库提供的。

定义了我们的`package.json`助手函数后，我们可以开始考虑我们的`package.json`文件的内容了。

Node.js 支持两种模块类型:

*   **[CommonJS 模块](https://nodejs.org/api/modules.html)【CJS】**。使用`module.exports`导出函数和对象，使用`require()`将它们加载到另一个模块中。
*   **[ECMAScript 模块](https://nodejs.org/api/esm.html) (ESM)** 。使用`export`导出函数和对象，使用`import`将它们加载到另一个模块中。

Node.js 生态系统正在[逐渐采用 ES 模块](https://simonplend.com/what-you-need-to-know-about-es-modules-in-node-js/)，这在客户端 JavaScript 中很常见。当事情处于这个过渡阶段时，我们需要决定我们的 Node.js 项目将默认使用 CJS 还是 ESM 模块。让我们创建一个`promptForModuleSystem`函数，询问这个新项目应该使用哪种模块类型:

```
async function promptForModuleSystem(moduleSystems) {
  const moduleSystem = await question(
    `Which Node.js module system do you want to use? (${moduleSystems.join(
      " or "
    )}) `,
    {
      choices: moduleSystems,
    }
  );

  return moduleSystem;
} 
```

上面的函数使用了 zx 提供的`question`函数。

我们现在将创建一个`getNodeModuleSystem`函数来调用我们的`promptForModuleSystem`函数。它将检查输入的值是否有效。如果不是，它会再次问这个问题:s

```
async function getNodeModuleSystem() {
  const moduleSystems = ["module", "commonjs"];
  const selectedModuleSystem = await promptForModuleSystem(moduleSystems);

  const isValidModuleSystem = moduleSystems.includes(selectedModuleSystem);
  if (!isValidModuleSystem) {
    console.error(
      chalk.red(
        `Error: Module system must be either '${moduleSystems.join(
          "' or '"
        )}'\n`
      )
    );

    return await getNodeModuleSystem();
  }

  return selectedModuleSystem;
} 
```

我们现在可以通过运行 [npm init](https://docs.npmjs.com/cli/v8/commands/npm-init) 命令来生成我们项目的`package.json`文件:

```
await $`npm init --yes`; 
```

然后我们将使用我们的`readPackageJson`助手函数来读取新创建的`package.json`文件。我们会询问项目应该使用哪个模块系统，将其设置为`packageJson`对象中`type`属性的值，然后将其写回项目的`package.json`文件:

```
const packageJson = await readPackageJson(targetDirectory);
const selectedModuleSystem = await getNodeModuleSystem();

packageJson.type = selectedModuleSystem;

await writePackageJson(targetDirectory, packageJson); 
```

*提示:当运行带有`--yes`标志的`npm init`时，为了在`package.json`中获得合理的默认值，请确保设置了 npm `init-*` [配置设置](https://docs.npmjs.com/cli/v8/using-npm/config)。*

### 安装所需的项目依赖项

为了在运行了我们的引导工具之后更容易开始项目开发，我们将创建一个`promptForPackages`函数，询问要安装什么 npm 包:

```
async function promptForPackages() {
  let packagesToInstall = await question(
    "Which npm packages do you want to install for this project? "
  );

  packagesToInstall = packagesToInstall
    .trim()
    .split(" ")
    .filter((pkg) => pkg);

  return packagesToInstall;
} 
```

为了防止在输入包名时出现输入错误，我们将创建一个`identifyInvalidNpmPackages`函数。这个函数将接受一组 npm 包名，然后运行 [npm view](https://docs.npmjs.com/cli/v8/commands/npm-view) 命令来检查它们是否存在:

```
async function identifyInvalidNpmPackages(packages) {
  $.verbose = false;

  let invalidPackages = [];
  for (const pkg of packages) {
    try {
      await $`npm view ${pkg}`;
    } catch (error) {
      invalidPackages.push(pkg);
    }
  }

  $.verbose = true;

  return invalidPackages;
} 
```

让我们创建一个`getPackagesToInstall`函数，它使用我们刚刚创建的两个函数:

```
async function getPackagesToInstall() {
  const packagesToInstall = await promptForPackages();
  const invalidPackages = await identifyInvalidNpmPackages(packagesToInstall);

  const allPackagesExist = invalidPackages.length === 0;
  if (!allPackagesExist) {
    console.error(
      chalk.red(
        `Error: The following packages do not exist on npm: ${invalidPackages.join(
          ", "
        )}\n`
      )
    );

    return await getPackagesToInstall();
  }

  return packagesToInstall;
} 
```

如果任何软件包名称不正确，上面的函数将显示一个错误，然后再次询问要安装的软件包。

一旦我们得到了要安装的有效包的列表，让我们用`npm install`命令安装它们:

```
const packagesToInstall = await getPackagesToInstall();
const havePackagesToInstall = packagesToInstall.length > 0;
if (havePackagesToInstall) {
  await $`npm install ${packagesToInstall}`;
} 
```

### 为工具生成配置

创建项目配置是我们用项目引导工具实现自动化的最佳选择。首先，让我们添加一个命令来生成一个`.gitignore`文件，这样我们就不会意外地提交我们在 Git 存储库中不想要的文件:

```
await $`npx gitignore node`; 
```

上面的命令使用 [gitignore](https://www.npmjs.com/package/gitignore) 包从 [GitHub 的 gitignore 模板](https://github.com/github/gitignore)中获取 Node.js `.gitignore`文件。

为了生成我们的 [EditorConfig](https://editorconfig.org/) 、[prettle](https://prettier.io/)和 [ESLint](https://eslint.org/) 配置文件，我们将使用一个名为 [Mrm](https://mrm.js.org/) 的命令行工具。

让我们全局安装我们将需要的`mrm`依赖项:

```
npm install --global mrm mrm-task-editorconfig mrm-task-prettier mrm-task-eslint 
```

然后添加`mrm`命令来生成配置文件:

```
await $`npx mrm editorconfig`;
await $`npx mrm prettier`;
await $`npx mrm eslint`; 
```

Mrm 负责生成配置文件，以及安装所需的 npm 包。它还提供了大量的[配置选项](https://mrm.js.org/docs/getting-started)，允许我们调整生成的配置文件以匹配我们的个人偏好。

### 生成基本自述文件

我们可以使用我们的`readPackageJson`助手函数从项目的`package.json`文件中读取项目名称。然后我们可以生成一个基本的 Markdown 格式的自述文件，并将其写入一个`README.md`文件:

```
const { name: projectName } = await readPackageJson(targetDirectory);
const readmeContents = `# ${projectName} ... `;

await fs.writeFile(`${targetDirectory}/README.md`, readmeContents); 
```

在上面的函数中，我们使用了由`fs-extra`公开的`fs.writeFile`的 promise 变体。

### 将项目框架提交给 Git

最后，是时候提交我们用`git`创建的项目框架了:

```
await $`git add .`;
await $`git commit -m "Add project skeleton"`; 
```

然后，我们将显示一条消息，确认我们的新项目已经成功引导:

```
console.log(
  chalk.green(
    `\n✔️ The project ${projectName} has been successfully bootstrapped!\n`
  )
);
console.log(chalk.green(`Add a git remote and push your changes.`)); 
```

### 启动一个新项目

现在，我们可以使用我们创建的工具来引导一个新项目:

```
mkdir new-project

./bootstrap-tool.mjs --directory new-project 
```

看看我们所做的一切！

## 结论

在本文中，我们已经了解了如何借助 Google 的 zx 库在 Node.js 中创建强大的 shell 脚本。我们使用它提供的实用函数和库来创建一个灵活的命令行工具。

到目前为止，我们构建的工具只是一个开始。这里有一些你可能想自己添加的功能想法:

*   **自动创建目标目录。**如果目标目录不存在，提示用户并询问他们是否希望为他们创建该目录。
*   **开源卫生。**询问用户他们是否正在创建一个开源项目。如果是，运行命令生成[许可](https://choosealicense.com/)和[贡献者约定](https://www.contributor-covenant.org/)文件。
*   在 GitHub 上自动创建存储库。添加使用 [GitHub CLI](https://cli.github.com/) 在 GitHub 上创建远程存储库的命令。一旦使用 Git 提交了初始框架，就可以将新项目推送到这个存储库。

本文中的所有代码都可以在 GitHub 上获得。

## 分享这篇文章