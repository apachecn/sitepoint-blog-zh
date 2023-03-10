# 用 Node.js 构建 JavaScript 命令行界面(CLI)

> 原文：<https://www.sitepoint.com/javascript-command-line-interface-cli-node-js/>

尽管 Node.js 对于“传统”的 web 应用程序来说很棒，但它的潜在用途要广泛得多。微服务、REST APIs、工具、与物联网甚至桌面应用合作:它是你的后盾。

Node.js 真正有用的另一个领域是构建命令行应用程序——这就是我们在本文中要做的。我们将从查看大量旨在帮助使用命令行的第三方包开始，然后从头构建一个真实的示例。

我们要构建的是一个初始化 Git 存储库的工具。当然，它会在引擎盖下运行，但它的作用不止于此。它还将直接从命令行在 GitHub 上创建一个远程存储库，允许用户交互地创建一个`.gitignore`文件，最后执行初始提交和推送。

与以往一样，本教程附带的代码可以在我们的 [GitHub repo](https://github.com/sitepoint-editors/ginit) 上找到。

![Build a Node CLI](img/df22fe5a3fd2be6a55394b7c0fefc6ca.png)

*本文更新于 2020 年。要获得更深入的 JavaScript 知识，请阅读我们的书， [JavaScript:新手到忍者，第二版](https://www.sitepoint.com/premium/books/javascript-novice-to-ninja-2nd-edition?utm_source=blog&utm_medium=articles)。*

## 为什么要用 Node.js 构建命令行工具？

在我们深入并开始构建之前，有必要了解一下为什么我们会选择 Node.js 来构建命令行应用程序。

最明显的优势是，如果你正在阅读这篇文章，你可能已经对它很熟悉了——事实上，对 JavaScript 也很熟悉。

另一个关键优势是，正如我们将看到的那样，强大的 Node.js 生态系统意味着在数十万个可用于各种目的的包中，有一些是专门为帮助构建强大的命令行工具而设计的。

最后，我们可以使用`npm`来管理任何依赖性，而不必担心特定于操作系统的包管理器，如 Aptitude、Yum 或 Homebrew。

提示:那不一定是真的，因为你的命令行工具可能有其他的外部依赖。

## 我们要建造的是:ginit

![Ginit, our Node CLI in action](img/041c368a7393561a63549ab890d9ce40.png)

在本教程中，我们将创建一个命令行实用程序，我称之为 **ginit** 。这是`git init`，但在类固醇。

你可能想知道这到底是什么意思。

毫无疑问，您已经知道，`git init`在当前文件夹中初始化一个 Git 存储库。然而，这通常只是将新的或现有的项目连接到 Git 的过程中所涉及的许多重复步骤中的一个。例如，作为典型工作流的一部分，您很可能:

1.  通过运行`git init`初始化本地存储库
2.  创建一个远程存储库，例如在 GitHub 或 Bitbucket 上——通常通过离开命令行并启动 web 浏览器
3.  添加遥控器
4.  创建一个`.gitignore`文件
5.  添加您的项目文件
6.  提交初始文件集
7.  推进到远程存储库。

通常会涉及到更多的步骤，但是我们将坚持使用这些步骤来实现我们的应用程序。然而，这些步骤是相当重复的。如果我们可以从命令行完成这一切，而不需要复制粘贴 Git URLs 之类的东西，不是更好吗？

所以 ginit 要做的是在当前文件夹中创建一个 Git 存储库，创建一个远程存储库——为此我们将使用 GitHub 然后将其添加为远程存储库。然后，它将提供一个简单的交互式“向导”来创建一个`.gitignore`文件，添加文件夹的内容并将其推送到远程存储库。这可能不会节省你的时间，但会消除你开始一个新项目时的一些摩擦。

记住这一点，让我们开始吧。

## 应用程序依赖性

有一点是肯定的:就外观而言，这款游戏机永远不会有图形用户界面那么复杂。然而，这并不意味着它必须是简单、丑陋、单色的文本。你可能会惊讶于在保持功能性的同时，你能在视觉上做多少事情。我们将看到几个用于增强显示的库: [chalk](https://www.npmjs.com/package/chalk) 用于给输出着色， [clui](https://www.npmjs.com/package/clui) 用于添加一些额外的可视组件。只是为了好玩，我们将使用 [figlet](https://www.npmjs.com/package/figlet) 创建一个基于 ASCII 的奇特横幅，我们还将使用 [clear](https://www.npmjs.com/package/clear) 来清除控制台。

就输入和输出而言，低级的 [Readline](https://nodejs.org/api/readline.html) Node.js 模块可以用来提示用户并请求输入，在简单的情况下就足够了。但是我们将利用一个第三方包，它增加了更高的复杂性— 【T2 问询者】。除了提供提问的机制，它还实现了简单的输入控件:比如单选按钮和复选框，但是是在控制台中。

我们还将使用[minimit](https://www.npmjs.com/package/minimist)来解析命令行参数。

下面是我们将专门用于命令行开发的包的完整列表:

*   [粉笔](https://www.npmjs.com/package/chalk) —给输出着色
*   [清除](https://www.npmjs.com/package/clear) —清除终端屏幕
*   [clui](https://www.npmjs.com/package/clui) —绘制命令行表格、仪表和微调器
*   [figlet](https://www.npmjs.com/package/figlet) —从文本创建 ASCII 图片
*   [询问者](https://www.npmjs.com/package/inquirer) —创建交互式命令行用户界面
*   [最小化](https://www.npmjs.com/package/minimist) —解析参数选项
*   [configstore](https://www.npmjs.com/package/configstore) —轻松加载和保存配置，无需考虑位置和方式。

此外，我们还将使用以下内容:

*   [@ octo kit/REST](https://www.npmjs.com/package/@octokit/rest)—node . js 的 GitHub REST API 客户端
*   [@ octo kit/auth-basic](https://www.npmjs.com/package/@octokit/auth-basic)—[GitHub 认证策略之一的实现](https://github.com/octokit/auth.js)
*   [lodash](https://www.npmjs.com/package/lodash) —一个 JavaScript 实用程序库
*   [simple-git](https://www.npmjs.com/package/simple-git) —在 Node.js 应用程序中运行 git 命令的工具
*   [touch](https://www.npmjs.com/package/touch) —实现 Unix touch 命令的工具。

## 入门指南

尽管我们将从头开始创建应用程序，但是不要忘记，您也可以从本文附带的[库中获取代码的副本。](https://github.com/sitepoint-editors/ginit)

为项目创建一个新目录。当然，你不必称之为`ginit`:

```
mkdir ginit
cd ginit 
```

创建一个新的`package.json`文件:

```
npm init -y 
```

编辑成这样:

```
{
  "name": "ginit",
  "version": "1.0.0",
  "description": "'git init' on steroids",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [
    "Git",
    "CLI"
  ],
  "author": "<YOUR NAME>",
  "license": "ISC"
} 
```

现在安装依赖项:

```
npm install chalk clear clui figlet inquirer minimist configstore @octokit/rest @octokit/auth-basic lodash simple-git touch 
```

现在在同一个文件夹中创建一个`index.js`文件和`require`以下依赖项:

```
const chalk = require('chalk');
const clear = require('clear');
const figlet = require('figlet'); 
```

## 添加一些辅助方法

我们将创建一个`lib`文件夹，在这里我们将帮助器代码分成几个模块:

*   **files.js** —基础档案管理
*   **inquirer.js** —命令行用户交互
*   **github.js** —接入令牌管理
*   **repo.js** — Git 仓库管理。

先说`lib/files.js`。在这里，我们需要:

*   获取当前目录(获取默认的 repo 名称)
*   检查目录是否存在(通过查找名为`.git`的文件夹来确定当前文件夹是否已经是 Git 存储库)。

这听起来很简单，但是需要考虑几个问题。

首先，您可能想使用`fs`模块的 [realpathSync](https://nodejs.org/api/fs.html#fs_fs_realpathsync_path_options) 方法来获取当前目录:

```
path.basename(path.dirname(fs.realpathSync(__filename))); 
```

当我们从同一个目录中调用应用程序时(例如，使用`node index.js`)，这是可行的，但是请记住，我们将使我们的控制台应用程序在全球可用。这意味着我们需要我们正在工作的目录的名称，而不是应用程序所在的目录。为此，最好使用 [process.cwd](https://nodejs.org/api/process.html#process_process_cwd) :

```
path.basename(process.cwd()); 
```

其次，检查文件或目录是否存在的首选方法[不断改变](http://stackoverflow.com/a/4482701)。目前的方式是使用`existsSync`。如果路径存在，返回`true`，否则返回`false`。

最后，值得注意的是，当您编写命令行应用程序时，使用这些方法的同步版本就可以了。

将所有这些放在一起，让我们在`lib/files.js`中创建一个实用程序包:

```
const fs = require('fs');
const path = require('path');

module.exports = {
  getCurrentDirectoryBase: () => {
    return path.basename(process.cwd());
  },

  directoryExists: (filePath) => {
    return fs.existsSync(filePath);
  }
}; 
```

返回到`index.js`并确保您`require`了新文件:

```
const files = require('./lib/files'); 
```

有了这些，我们就可以开始开发应用程序了。

## 正在初始化节点 CLI

现在让我们实现控制台应用程序的启动阶段。

为了演示我们为增强控制台输出而安装的一些软件包，让我们清空屏幕，然后显示一个横幅:

```
// index.js

clear();

console.log(
  chalk.yellow(
    figlet.textSync('Ginit', { horizontalLayout: 'full' })
  )
); 
```

您可以使用`node index.js`运行应用程序。下面显示了它的输出。

![The welcome banner on our Node CLI, created using Chalk and Figlet](img/067644c648025f924696f1af59e9c6f3.png)

接下来，让我们运行一个简单的检查来确保当前文件夹还不是 Git 存储库。这很简单:我们只需使用刚刚创建的实用程序方法检查一个`.git`文件夹是否存在:

```
//index.js

if (files.directoryExists('.git')) {
  console.log(chalk.red('Already a Git repository!'));
  process.exit();
} 
```

*提示:注意我们使用[粉笔模块](https://www.npmjs.com/package/chalk)来显示红色信息。*

## 提示用户输入

我们需要做的下一件事是创建一个函数，提示用户输入 GitHub 凭证。

为此，我们可以使用[问询者](https://www.npmjs.com/package/inquirer)。该模块包括许多用于各种类型提示的方法，大致类似于 HTML 表单控件。为了收集用户的 GitHub 用户名和密码，我们将分别使用`input`和`password`类型。

首先，创建`lib/inquirer.js`并插入以下代码:

```
const inquirer = require('inquirer');

module.exports = {
  askGithubCredentials: () => {
    const questions = [
      {
        name: 'username',
        type: 'input',
        message: 'Enter your GitHub username or e-mail address:',
        validate: function( value ) {
          if (value.length) {
            return true;
          } else {
            return 'Please enter your username or e-mail address.';
          }
        }
      },
      {
        name: 'password',
        type: 'password',
        message: 'Enter your password:',
        validate: function(value) {
          if (value.length) {
            return true;
          } else {
            return 'Please enter your password.';
          }
        }
      }
    ];
    return inquirer.prompt(questions);
  },
}; 
```

如您所见，`inquirer.prompt()`向用户提出了一系列问题，以数组的形式作为第一个参数。每个问题都由一个对象组成，该对象定义了字段的`name`、`type`(我们在这里只是分别使用`input`和`password`，但是稍后我们会看到一个更高级的例子)，以及要显示的提示(`message`)。

用户提供的输入将作为`Promise`传递给调用函数。如果成功，我们将得到一个具有两个属性的简单对象— `username`和`password`。

您可以通过向`index.js`添加以下内容来测试所有这些:

```
const inquirer  = require('./lib/inquirer');

const run = async () => {
  const credentials = await inquirer.askGithubCredentials();
  console.log(credentials);
};

run(); 
```

然后使用`node index.js`运行脚本。

![Getting user input with Inquirer](img/6900ec5a429e7624e441ba036f2fa57b.png)

*提示:当你完成测试时，不要忘记从`index.js`中删除行`const inquirer = require('./lib/inquirer');`，因为在这个文件中我们实际上不需要它。*

## 处理 GitHub 认证

下一步是创建一个函数来检索 GitHub API 的 OAuth 令牌。本质上，我们将“交换”令牌的用户名和密码。

当然，我们不希望用户每次使用该工具时都必须输入凭据。相反，我们将为后续请求存储 OAuth 令牌。这就是 [configstore](https://www.npmjs.com/package/configstore) 包的用武之地。

### 存储配置

存储配置表面上非常简单:您可以简单地读写 JSON 文件，而不需要第三方包。但是，configstore 包提供了一些关键优势:

1.  考虑到您的操作系统和当前用户，它会为您确定最合适的文件位置。
2.  不需要显式地读取或写入文件。您只需修改一个 configstore 对象，它会在后台自动完成。

要使用它，只需创建一个实例，传递给它一个应用程序标识符。例如:

```
const Configstore = require('configstore');
const conf = new Configstore('ginit'); 
```

如果`configstore`文件不存在，它将返回一个空对象并在后台创建文件。如果已经有一个`configstore`文件，其内容将对您的应用程序可用。您现在可以将`conf`作为一个简单的对象，根据需要获取或设置属性。如上所述，你不需要担心事后挽回。会有人替你处理的。

*提示:在 macOS 上，你可以在`/Users/[YOUR-USERNME]/.config/configstore/ginit.json`中找到这个文件。在 Linux 上，它在`/home/[YOUR-USERNME]/.config/configstore/ginit.json`* 中。

## 与 GitHub API 通信

让我们创建一个库来处理 GitHub 令牌。创建文件`lib/github.js`并将以下代码放入其中:

```
const CLI = require('clui');
const Configstore = require('configstore');
const Octokit = require('@octokit/rest');
const Spinner = CLI.Spinner;
const { createBasicAuth } = require("@octokit/auth-basic");

const inquirer = require('./inquirer');
const pkg = require('../package.json');

const conf = new Configstore(pkg.name); 
```

现在让我们添加检查我们是否已经获得访问令牌的函数。我们还将添加一个允许其他库访问`octokit` (GitHub)函数的函数:

```
let octokit;

module.exports = {
  getInstance: () => {
    return octokit;
  },

  getStoredGithubToken: () => {
    return conf.get('github.token');
  },
}; 
```

如果一个`conf`对象存在并且它有`github.token`属性，这意味着在存储器中已经有一个令牌。在这种情况下，我们将令牌值返回给调用函数。我们稍后会谈到这一点。

如果没有检测到令牌，我们需要获取一个。当然，获取 OAuth 令牌涉及到网络请求，这意味着用户要等待一段时间。这让我们有机会看看为基于控制台的应用程序提供了一些增强的 [clui](https://www.npmjs.com/package/clui) 包，其中包括一个动画旋转器。

创建微调器很容易:

```
const status = new Spinner('Authenticating you, please wait...');
status.start(); 
```

完成后，只需停止它，它就会从屏幕上消失:

```
status.stop(); 
```

*提示:您也可以使用`update`方法动态设置标题。如果您有一些进度指示，例如显示完成的百分比，这可能会很有用。*

以下是使用 GitHub 进行认证的代码:

```
module.exports = {
  getInstance: () => { ... },
  getStoredGithubToken: () => { ... },

  getPersonalAccesToken: async () => {
    const credentials = await inquirer.askGithubCredentials();
    const status = new Spinner('Authenticating you, please wait...');

    status.start();

    const auth = createBasicAuth({
      username: credentials.username,
      password: credentials.password,
      async on2Fa() {
        // TBD
      },
      token: {
        scopes: ['user', 'public_repo', 'repo', 'repo:status'],
        note: 'ginit, the command-line tool for initalizing Git repos'
      }
    });

    try {
      const res = await auth();

      if(res.token) {
        conf.set('github.token', res.token);
        return res.token;
      } else {
        throw new Error("GitHub token was not found in the response");
      }
    } finally {
      status.stop();
    }
  },
}; 
```

让我们一步一步来:

1.  我们使用之前定义的`askGithubCredentials`方法提示用户输入凭证。
2.  我们使用 [createBasicAuth](https://www.npmjs.com/package/@octokit/auth-basic?activeTab=readme#createbasicauthtoken-options) 方法来创建一个`auth`函数，我们将在下一步中调用它。我们向该方法传递用户的用户名和密码，以及一个具有两个属性的令牌对象:
    *   提醒我们 OAuth 令牌的用途的注释。
    *   `scopes` —此授权所在的范围列表。你可以在 GitHub 的文档中阅读关于可用范围的更多信息。
3.  然后我们`await`在`try`块中调用`auth`函数的结果。
4.  如果认证成功，并且响应中存在令牌，我们在`configstore`中为下一次设置它，并返回令牌。
5.  如果令牌丢失，或者由于某种原因身份验证没有成功，错误就会在堆栈中冒泡，这样我们就可以在`index.js`中捕捉到它。我们稍后将实现该功能。

您创建的任何访问令牌，无论是手动创建还是像我们在这里一样通过 API 创建，您都可以在这里[看到它们](https://github.com/settings/tokens)。在开发过程中，您可能会发现需要删除 ginit 的访问令牌——可通过上面提供的`note`参数识别——以便重新生成它。

如果你一直在跟进，并想尝试我们目前为止所做的，你可以更新`index.js`如下:

```
const github = require('./lib/github');

...

const run = async () => {
  let token = github.getStoredGithubToken();
  if(!token) {
    token = await github.getPersonalAccesToken();
  }
  console.log(token);
}; 
```

第一次运行时，应该会提示您输入用户名和 GitHub 密码。然后，该应用程序应该在 GitHub 上创建一个个人访问令牌，并将该令牌保存到`configstore`，然后将其登录到控制台。此后每次运行应用程序时，应用程序都会直接从`configstore`中提取令牌，并将其记录到屏幕上。

### 处理双因素身份认证

希望您注意到了上面代码中的`on2Fa`方法。当用户在其 GitHub 帐户上启用了双因素身份验证时，将调用该函数。让我们现在填写:

```
// inquirer.js

const inquirer = require('inquirer');

module.exports = {
  askGithubCredentials: () => { ... },

  getTwoFactorAuthenticationCode: () => {
    return inquirer.prompt({
      name: 'twoFactorAuthenticationCode',
      type: 'input',
      message: 'Enter your two-factor authentication code:',
      validate: function(value) {
        if (value.length) {
          return true;
        } else {
          return 'Please enter your two-factor authentication code.';
        }
      }
    });
  },
}; 
```

我们可以从`on2Fa`方法中调用`getTwoFactorAuthenticationCode`方法，就像这样:

```
// github.js

async on2Fa() {
  status.stop();
  const res = await inquirer.getTwoFactorAuthenticationCode();
  status.start();
  return res.twoFactorAuthenticationCode;
}, 
```

现在，我们的应用程序可以处理启用双因素身份验证的 GitHub 帐户。

## 创建存储库

一旦我们获得了 OAuth 令牌，我们就可以用它来创建 GitHub 的远程存储库。

同样，我们可以使用 Inquirer 来询问一系列问题。我们需要回购的名称，我们将要求一个可选的描述，我们还需要知道它应该是公共的还是私有的。

我们将使用[minimit](https://www.npmjs.com/package/minimist)从可选的命令行参数中获取名称和描述的默认值。例如:

```
ginit my-repo "just a test repository" 
```

这将设置默认名称为`my-repo`，描述为`just a test repository`。

下面一行将把参数放在一个用下划线索引的数组中:

```
const argv = require('minimist')(process.argv.slice(2));
// { _: [ 'my-repo', 'just a test repository' ] } 
```

*提示:这只是触及了最小包装的表面。您还可以使用它来解释标志、开关和名称/值对。查看文档以获取更多信息。*

我们将编写代码来解析命令行参数，并询问一系列问题。首先，更新`lib/inquirer.js`如下:

```
const inquirer = require('inquirer');
const files = require('./files');

module.exports = {
  askGithubCredentials: () => { ... },
  getTwoFactorAuthenticationCode: () => { ... },

  askRepoDetails: () => {
    const argv = require('minimist')(process.argv.slice(2));

    const questions = [
      {
        type: 'input',
        name: 'name',
        message: 'Enter a name for the repository:',
        default: argv._[0] || files.getCurrentDirectoryBase(),
        validate: function( value ) {
          if (value.length) {
            return true;
          } else {
            return 'Please enter a name for the repository.';
          }
        }
      },
      {
        type: 'input',
        name: 'description',
        default: argv._[1] || null,
        message: 'Optionally enter a description of the repository:'
      },
      {
        type: 'list',
        name: 'visibility',
        message: 'Public or private:',
        choices: [ 'public', 'private' ],
        default: 'public'
      }
    ];
    return inquirer.prompt(questions);
  },
}; 
```

接下来，创建文件`lib/repo.js`并添加以下代码:

```
const CLI = require('clui');
const fs = require('fs');
const git = require('simple-git/promise')();
const Spinner = CLI.Spinner;
const touch = require("touch");
const _ = require('lodash');

const inquirer = require('./inquirer');
const gh = require('./github');

module.exports = {
  createRemoteRepo: async () => {
    const github = gh.getInstance();
    const answers = await inquirer.askRepoDetails();

    const data = {
      name: answers.name,
      description: answers.description,
      private: (answers.visibility === 'private')
    };

    const status = new Spinner('Creating remote repository...');
    status.start();

    try {
      const response = await github.repos.createForAuthenticatedUser(data);
      return response.data.ssh_url;
    } finally {
      status.stop();
    }
  },
}; 
```

一旦我们有了这些信息，我们可以简单地使用 GitHub 包来[创建一个 repo](https://octokit.github.io/rest.js/#octokit-routes-repos-create-for-authenticated-user) ，这将为我们提供一个新创建的存储库的 URL。然后，我们可以在本地 Git 存储库中将其设置为远程。然而，首先让我们交互地创建一个`.gitignore`文件。

## 创建. gitignore 文件

下一步，我们将创建一个简单的命令行“向导”来生成一个`.gitignore`文件。如果用户在现有的项目目录中运行我们的应用程序，让我们向他们显示当前工作目录中已经存在的文件和目录列表，并允许他们选择忽略哪些文件和目录。

Inquirer 包为此提供了一个`checkbox`输入类型。

![Inquirer’s checkboxes in action](img/88dbb4e7777a40c8e4efd0dff2ed7eaf.png)

我们需要做的第一件事是扫描当前目录，忽略`.git`文件夹和任何现有的`.gitignore`文件(我们通过使用 lodash 的[而不使用](https://lodash.com/docs#without)方法来做到这一点):

```
const filelist = _.without(fs.readdirSync('.'), '.git', '.gitignore'); 
```

如果没有什么要添加的，那么继续下去就没有意义了，所以让我们简单地`touch`当前的`.gitignore`文件并退出函数:

```
if (filelist.length) {
  ...
} else {
  touch('.gitignore');
} 
```

最后，让我们利用 Inquirer 的复选框“widget”来列出文件。在`lib/inquirer.js`中插入以下代码:

```
askIgnoreFiles: (filelist) => {
  const questions = [
    {
      type: 'checkbox',
      name: 'ignore',
      message: 'Select the files and/or folders you wish to ignore:',
      choices: filelist,
      default: ['node_modules', 'bower_components']
    }
  ];
  return inquirer.prompt(questions);
}, 
```

请注意，我们还可以提供一个默认列表。在这种情况下，我们预先选择了`node_modules`和`bower_components`，如果它们存在的话。

有了查询者代码，我们现在可以构造`createGitignore()`函数了。将此代码插入`lib/repo.js`:

```
createGitignore: async () => {
  const filelist = _.without(fs.readdirSync('.'), '.git', '.gitignore');

  if (filelist.length) {
    const answers = await inquirer.askIgnoreFiles(filelist);

    if (answers.ignore.length) {
      fs.writeFileSync( '.gitignore', answers.ignore.join( '\n' ) );
    } else {
      touch( '.gitignore' );
    }
  } else {
    touch('.gitignore');
  }
}, 
```

一旦“提交”，我们就通过连接所选择的文件列表来生成一个`.gitignore`,用换行符隔开。我们的函数现在很好地保证了我们已经得到了一个`.gitignore`文件，所以我们可以继续初始化 Git 存储库。

## 从应用程序内部与 Git 交互

有很多方法可以与 Git 交互，但是最简单的方法可能是使用 [simple-git](https://www.npmjs.com/package/simple-git) 包。这提供了一组可链接的方法，这些方法在幕后运行 Git 可执行文件。

这些是我们将使用它来自动化的重复性任务:

1.  运行`git init`
2.  添加`.gitignore`文件
3.  添加工作目录的剩余内容
4.  执行初始提交
5.  添加新创建的远程存储库
6.  将工作目录推送到远程。

在`lib/repo.js`中插入以下代码:

```
setupRepo: async (url) => {
  const status = new Spinner('Initializing local repository and pushing to remote...');
  status.start();

  try {
    git.init()
      .then(git.add('.gitignore'))
      .then(git.add('./*'))
      .then(git.commit('Initial commit'))
      .then(git.addRemote('origin', url))
      .then(git.push('origin', 'master'));
  } finally {
    status.stop();
  }
}, 
```

## 把所有的放在一起

首先，让我们在`lib/github.js`中设置一个助手函数来设置`oauth`认证:

```
githubAuth: (token) => {
  octokit = new Octokit({
    auth: token
  });
}, 
```

接下来，我们在`index.js`中创建一个函数来处理获取令牌的逻辑。将此代码放在`run()`函数之前:

```
const getGithubToken = async () => {
  // Fetch token from config store
  let token = github.getStoredGithubToken();
  if(token) {
    return token;
  }

  // No token found, use credentials to access GitHub account
  token = await github.getPersonalAccesToken();

  return token;
}; 
```

最后，我们通过编写处理应用程序主要逻辑的代码来更新`run()`函数:

```
const repo = require('./lib/repo');

...

const run = async () => {
  try {
    // Retrieve & Set Authentication Token
    const token = await getGithubToken();
    github.githubAuth(token);

    // Create remote repository
    const url = await repo.createRemoteRepo();

    // Create .gitignore file
    await repo.createGitignore();

    // Set up local repository and push to remote
    await repo.setupRepo(url);

    console.log(chalk.green('All done!'));
  } catch(err) {
      if (err) {
        switch (err.status) {
          case 401:
            console.log(chalk.red('Couldn\'t log you in. Please provide correct credentials/token.'));
            break;
          case 422:
            console.log(chalk.red('There is already a remote repository or token with the same name'));
            break;
          default:
            console.log(chalk.red(err));
        }
      }
  }
}; 
```

如您所见，我们确保用户在依次调用我们所有的其他函数(`createRemoteRepo()`、`createGitignore()`、`setupRepo()`)之前得到验证。该代码还处理任何错误，并向用户提供适当的反馈。

你可以在我们的 GitHub repo 上查看完整的 [index.js](https://github.com/sitepoint-editors/ginit/blob/master/index.js) 文件。

此时，你应该有一个工作的应用程序。试一试，并确信它能如预期的那样工作。

## 让 ginit 命令在全球范围内可用

剩下要做的一件事就是让我们的命令在全球范围内可用。为此，我们需要在`index.js`的顶部添加一个 [shebang](https://en.wikipedia.org/wiki/Shebang_%28Unix%29) 行:

```
#!/usr/bin/env node 
```

接下来，我们需要向我们的`package.json`文件添加一个`bin`属性。这将命令名(`ginit`)映射到要执行的文件名(相对于`package.json`):

```
"bin": {
  "ginit": "./index.js"
} 
```

之后，全局安装该模块，您将拥有一个有效的 shell 命令:

```
npm install -g 
```

提示:这也适用于 Windows，因为 [npm 会在你的脚本](http://stackoverflow.com/a/10398567/1136887)旁边安装一个 cmd 包装器。

如果您想确认安装工作正常，可以使用以下命令列出全局安装的节点模块:

```
npm ls -g --depth=0 
```

## 更进一步

我们已经有了一个相当漂亮的、尽管简单的命令行应用程序来初始化 Git 存储库。但是您还可以做更多的事情来进一步增强它。

如果你是一个 Bitbucket 用户，你可以修改程序来使用 Bitbucket API 创建一个存储库。有一个 [Node.js API 包装器](https://www.npmjs.com/package/bitbucket-api)可以帮助你开始。您可能希望添加一个额外的命令行选项或提示，询问用户是否希望使用 GitHub 或 Bitbucket (Inquirer 正好适合这种情况),或者仅仅用 Bitbucket 替代 GitHub 特定的代码。

您还可以提供工具来为`.gitgnore`文件指定您自己的一组缺省值，而不是一个硬编码的列表。首选项包可能适合这里，或者您可以提供一组“模板”——也许提示用户输入项目的类型。你可能还想考虑将它与 [.gitignore.io](https://www.gitignore.io/) 命令行工具/API 集成起来。

除此之外，您可能还想添加额外的验证，提供跳过某些部分的能力，等等。

## 分享这篇文章