# 用 Caporal.js 创建你自己的约曼式脚手架工具

> 原文：<https://www.sitepoint.com/scaffolding-tool-caporal-js/>

![Create Your Own Yeoman-Style Scaffolding Tool with Caporal.js](img/43a63fe9f06ae58a288f519075d17404.png)

开始一个新项目(尤其是作为一名 JavaScript 开发人员)通常是一个重复而乏味的过程。对于每个新项目，我们通常需要添加一个`package.json`文件，引入一些标准的依赖项，配置它们，创建正确的目录结构，添加各种其他文件……这样的例子不胜枚举。

但是我们是懒惰的开发者，对吗？幸运的是，我们可以实现自动化。它不需要任何特殊的工具或奇怪的语言——如果你已经知道 JavaScript，这个过程实际上相当简单。

在本教程中，我们将使用 Node.js 构建一个跨平台的命令行界面(CLI)。这将允许我们使用一组预定义的模板快速搭建出一个新项目。它将是完全可扩展的，这样你就可以很容易地使它适应你自己的需要，并且自动化掉你工作流程中繁琐的部分。

## 为什么要自己卷？

虽然有很多类似的工具来完成这项任务(比如 [Yeoman](http://yeoman.io) )，但是通过构建我们自己的工具，我们可以获得知识、经验，并使其完全可定制。您应该始终考虑创建您的工具而不是使用现有的工具，尤其是当您试图解决专门的问题时。这听起来可能与总是重用软件的惯例相反，但是在某些情况下，实现自己的工具可能是非常有益的。获取知识总是有帮助的，但你也可以想出高度个性化和高效的工具，特别是根据你的需求定制的工具。

也就是说，我们不会完全重新发明轮子。CLI 本身将使用名为 [Caporal.js](https://github.com/mattallty/Caporal.js) 的库来构建。在内部，它还将使用[提示](https://github.com/flatiron/prompt)来请求用户数据，并使用 [shellJS](http://documentup.com/shelljs/shelljs) 在 Node.js 环境中为我们提供一些 Unix 工具。我选择这些库主要是因为它们易于使用，但是在完成本教程之后，您将能够用最适合您需求的替代库来替换它们。

和以往一样，你可以在 Github 上找到已经完成的项目:[https://github.com/sitepoint-editors/node-scaffolding-tool](https://github.com/sitepoint-editors/node-scaffolding-tool)

现在让我们开始吧…

## 使用 Caporal.js 启动和运行

首先，在你的电脑上创建一个新的目录。建议为这个项目建立一个专用目录，它可以长时间保持不变，因为每次都会从那里调用最终命令。

进入目录后，创建一个包含以下内容的`package.json`文件:

```
{
  "name": "scaffold",
  "version": "1.0.0",
  "main": "index.js",
  "bin": {
    "scaffold": "index.js"
  },
  "dependencies": {
    "caporal": "^0.3.0",
    "colors": "^1.1.2",
    "prompt": "^1.0.0",
    "shelljs": "^0.7.7"
  }
} 
```

这已经包括了我们需要的一切。现在要安装这些包，执行`npm install`，所有标记的依赖项将在我们的项目中可用。在撰写本文时，这些软件包的版本是最新的。如果同时有了新的版本，你可以考虑更新它们(注意任何 API 的变化)。

注意`bin`中的`scaffold`值。它指出了我们的命令名和每次我们在终端(`index.js`)中输入该命令时将要调用的文件。您可以根据需要随意更改该值。

## 构建入口点

我们的 CLI 的第一个组成部分是`index.js`文件，它包含一个命令、选项和我们可以使用的相应功能的列表。但是在写这个文件之前，让我们先更详细地定义一下我们的 CLI 要做什么。

*   主要(也是唯一的)命令是`create`，它允许我们创建自己选择的项目样板文件。
*   `create`命令带有一个强制的`template`参数，指示我们想要使用哪个模板。
*   它还需要一个`--variant`选项，允许我们选择模板的特定变体。
*   如果没有提供特定的变量，它将使用默认变量(我们将在后面定义)。

Caporal.js 允许我们以简洁的方式定义以上内容。让我们将以下内容添加到我们的`index.js`文件中:

```
#!/usr/bin/env node

const prog = require('caporal');

prog
  .version('1.0.0')
  .command('create', 'Create a new application')
  .argument('<template>', 'Template to use')
  .option('--variant <variant>', 'Which <variant> of the template is going to be created')
  .action((args, options, logger) => {
    console.log({
      args: args,
      options: options
    });
  });

prog.parse(process.argv); 
```

第一行是一个 [Shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)) ，表示这是一个 Node.js 可执行文件。

> 这里包含的 shebang 只适用于类似 Unix 的系统。Windows 没有 shebang 支持，所以如果你想在 Windows 上直接执行这个文件，你必须寻找一个变通方法。通过 npm 运行该命令(在本节末尾解释)将适用于所有平台。

接下来，我们将`Caporal.js`包作为`prog`包含进来，并开始定义我们的程序。使用[命令函数](https://github.com/mattallty/Caporal.js?#commandname-description---command)，我们将`create`命令定义为第一个参数，将一个小描述定义为第二个参数。这将显示在为我们的 CLI 自动生成的帮助选项中(使用`--help`)。

然后，我们将`template`参数链接到[参数函数](https://github.com/mattallty/Caporal.js?#argumentsynopsis-description-validator-defaultvalue---command)中，因为它是一个必需的参数，所以我们将它放在尖括号(`<`和`>`)中。

我们可以通过在[选项函数](https://github.com/mattallty/Caporal.js?#optionsynopsis-description-validator-defaultvalue-required---command)中编写`--variant <variant>`来定义变量选项。这意味着我们命令的选项被称为`--variant`，其值将存储在一个`variant`变量中。

最后，在[动作命令](https://github.com/mattallty/Caporal.js?#actionaction---command)中，我们传递另一个将处理当前命令的函数。将使用三个参数调用此回调:

*   传递的参数(`args`)
*   通过选项(`options`)
*   在屏幕上显示事物的实用程序对象(`logger`)。

此时，我们将记录所传递的参数和选项的值，这样我们就可以了解如何从 CLI 获取执行操作所需的信息。

最后一行将来自`scaffold`命令的信息传递给 Caporal.js 解析器，解析器将完成繁重的工作。

### 让 CLI 在全球可用

我们现在可以测试我们的应用程序，看看是否一切都按计划进行。为此，我们需要使用 [npm 的 link 命令](https://docs.npmjs.com/cli/link)让它对我们的系统全局可用。从项目根目录执行以下命令:

```
npm link 
```

这个过程完成后，我们将能够在终端的任何目录中执行`scaffold`,而不必显式引用我们的`index.js`文件:

```
scaffold create node --variant mvc 
```

你应该得到这样的回应:

```
{ args: { template: 'node' }, options: { variant: 'mvc' } } 
```

这是我们接下来将使用模板创建项目的信息示例。

## 构建模板

我们的模板将由文件和目录结构组成，我们需要启动并运行特定类型的项目。每个模板都有一个带有一些占位符值的`package.json`文件，我们可以用真实数据填充它。

首先，在项目中创建一个`templates`目录，并在其中创建一个`node`目录。在`node`目录中，创建一个`default`目录(如果我们不提供`variant`选项，将使用该目录)和另一个名为`mvc`的目录(使用 [MVC 架构](https://www.sitepoint.com/node-js-mvc-application/)创建一个 Node.js 项目)。

最终的结构应该是这样的:

```
.
└── templates
    └── node
        ├── default
        └── mvc 
```

现在我们需要用项目文件填充我们的`default`和`mvc`文件夹。你可以自己创建一些，也可以使用[示例应用](https://github.com/sitepoint-editors/node-scaffolding-tool/tree/master/templates/node)中提供的那些。

接下来，我们可以将变量标识符放在我们需要动态值的地方。每个模板文件夹应该包含一个`package.json`文件。打开它们，用大写字母(没有空格)和方括号包含所有变量。

这是我们默认模板中的 [package.json](https://github.com/sitepoint-editors/node-scaffolding-tool/blob/master/templates/node/default/package.json) 文件:

```
 {
  "name": "[NAME]",
  "version": "[VERSION]",
  "description": "[DESCRIPTION]",
  "main": "server.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node server.js",
    "start:dev": "nodemon server.js"
  },
  "author": "[AUTHOR]",
  "license": "[LICENSE]",
  "dependencies": {
    "dotenv": "^2.0.0",
    "hapi": "^16.1.0",
    "hoek": "^4.1.0"
  },
  "devDependencies": {
    "nodemon": "^1.11.0"
  }
} 
```

创建所有变量后，将它们放在同一个模板目录下的一个`_variables.js`文件中，如下所示:

```
/*
 * Variables to replace
 * --------------------
 * They are asked to the user as they appear here.
 * User input will replace the placeholder  values
 * in the template files
 */

module.exports = [
  'name',
  'version',
  'description',
  'author',
  'license'
]; 
```

导出的数组中的名称与文件中的名称相同，但是是小写的，并且没有方括号。我们将使用该文件在 CLI 中询问每个值。

现在我们可以开始为`create`命令构建函数，它将完成所有的工作。

## 构建“创建”功能

在我们的`index.js`文件中，我们之前向`action()`传递了一个简单的函数，它记录了 CLI 接收到的值。现在我们要用一个新的函数来替换这个函数，它会将模板文件复制到执行`scaffold`命令的目录中。我们还将用通过用户输入获得的值替换占位符变量。

在一个`lib`目录中(为了保持有序)，添加一个`create.js`文件，并将以下内容放入其中:

```
module.exports = (args, options, logger) => {

}; 
```

我们将把应用程序的所有逻辑放在这个函数中，这意味着我们需要相应地修改我们的`index.js`文件:

```
#!/usr/bin/env node

const prog = require('caporal');
const createCmd = require('./lib/create');

prog
  .version('1.0.0')
  .command('create', 'Create a new application')
  .argument('<template>', 'Template to use')
  .option('--variant <variant>', 'Which <variant> of the template is going to be created')
  .action(createCmd);

prog.parse(process.argv); 
```

### 导入依赖项和设置变量

现在回到`create.js`文件，我们可以将以下内容放在文件的开头，使所需的包可用:

```
const prompt = require('prompt');
const shell = require('shelljs');
const fs = require('fs');
const colors = require("colors/safe");

// Set prompt as green and use the "Replace" text
prompt.message = colors.green("Replace"); 
```

注意提示信息的[定制](https://github.com/flatiron/prompt#customizing-your-prompt)设置。这完全是可选的。

在导出的函数中，我们首先要添加一些变量:

```
const variant = options.variant || 'default';
const templatePath = `${__dirname}/../templates/${args.template}/${variant}`;
const localPath = process.cwd(); 
```

正如你所看到的，我们抓取了传递给`scaffold`命令的`variant`选项，如果这个选项被省略了，就把它设置为`'default'`。变量`templatePath`包含指定模板的完整路径，而`localPath`包含对执行命令的目录的引用。

### 复制模板文件

使用`shellJS`中的 [cp 函数](http://documentup.com/shelljs/shelljs#cpoptions-source--source--dest)复制文件的过程非常简单。在我们刚刚包含的变量下，添加以下内容:

```
if (fs.existsSync(templatePath)) {
  logger.info('Copying files…');
  shell.cp('-R', `${templatePath}/*`, localPath);
  logger.info('✔ The files have been copied!');
} else {
  logger.error(`The requested template for ${args.template} wasn't found.`)
  process.exit(1);
} 
```

首先，我们确保模板存在，如果不存在，我们将使用 Caporal.js 的`logger.error()`函数退出显示错误消息的过程。如果模板存在，我们将使用`logger.info()`显示通知消息，并使用`shell.cp()`复制文件。`-R`选项表示它应该将文件*从模板路径递归地复制到命令正在执行的路径。文件复制完成后，我们会显示一条确认消息。因为 shellJS 函数是同步的，我们不需要使用回调、承诺或任何类似的东西——我们只需要以过程化的方式编写代码。*

### 替换变量

虽然替换文件中的变量听起来是一件复杂的事情，但是如果我们使用正确的工具，这是非常简单的。其中之一是 Unix 系统的经典 [sed](https://en.wikipedia.org/wiki/Sed) 编辑器，它可以动态转换文本。ShellJS [为我们提供了这个实用程序](http://documentup.com/shelljs/shelljs#sedoptions-search_regex-replacement-file_array)，它可以在 Unix 系统(Linux 和 MacOS)以及 Windows 上运行。

要进行所有替换，请在您的文件中，在我们之前创建的代码下面添加以下代码:

```
const variables = require(`${templatePath}/_variables`);

if (fs.existsSync(`${localPath}/_variables.js`)) {
  shell.rm(`${localPath}/_variables.js`);
}

logger.info('Please fill the following values…');

// Ask for variable values
prompt.start().get(variables, (err, result) => {

  // Remove MIT License file if another is selected
  // Omit this code if you have used your own template
  if (result.license !== 'MIT') {
    shell.rm(`${localPath}/LICENSE`);
  }

  // Replace variable values in all files
  shell.ls('-Rl', '.').forEach(entry => {
    if (entry.isFile()) {
      // Replace '[VARIABLE]` with the corresponding variable value from the prompt
      variables.forEach(variable => {
        shell.sed('-i', `\\[${variable.toUpperCase()}\\]`, result[variable], entry.name);
      });

      // Insert current year in files
      shell.sed('-i', '\\[YEAR\\]', new Date().getFullYear(), entry.name);
    }
  });

  logger.info('✔ Success!');
}); 
```

我们从读取开始，`variables`被设置为我们之前创建的模板的`_variables.js`文件的内容。

然后，因为我们已经从模板中复制了所有文件，第一个`if`语句将从我们的本地目录中删除`_variables.js`文件，因为它只在 CLI 中需要。

使用[提示工具](https://github.com/flatiron/prompt)获得每个变量的值，将变量数组传递给`get()`函数。这样，CLI 将询问我们数组中每个项目的值，并将结果保存在一个名为`result`的对象中，该对象将被传递给回调函数。该对象包含每个变量作为键，输入的文本作为值。

下一个`if`语句只有在您使用存储库中包含的模板时才是必需的，因为我们还包含一个许可证文件。尽管如此，看看我们如何检索每个变量的值还是很有用的，在本例中是使用`result.license`从`license`属性中检索的。如果用户输入的不是`MIT`的许可，那么我们使用 ShellJS 的`rm()`函数从目录中删除`LICENSE`文件。

现在我们到了有趣的部分。通过使用来自 ShellJS 的 [ls 函数](http://documentup.com/shelljs/shelljs#lsoptions-path-)，我们可以获得当前目录(`.`)中所有文件的列表，我们将在其中替换变量。我们传递给它`-Rl`选项，所以它变成递归的，返回一个[文件对象](https://nodejs.org/api/fs.html#fs_class_fs_stats)而不是文件名。

我们使用`forEach()`遍历文件对象列表，对于每个对象，我们使用`isFile()`函数检查是否正在接收文件。如果我们得到一个目录，我们什么也不做。

然后，对于我们得到的每个文件，我们循环遍历所有变量，并像这样执行`sed`函数:

```
shell.sed('-i', `\\[${variable.toUpperCase()}\\]`, result[variable], entry.name); 
```

这里我们传递了允许我们替换文本的`-i`选项，然后我们传递了一个 regex 字符串，它将匹配大写的`variable`标识符，并且用方括号(`[`和`]`)括起来。然后，正则表达式的每一个匹配将被相应变量(`result[variable]`)的值所替换，最后我们传递从`forEach()`函数(`entry.name`)中替换的文件的名称。

第二个`sed`是完全可选的。这只是用当前年份替换`[YEAR]`事件。对`LICENSE`或`README.md`文件有用。

就是这样！我们现在可以在一个空目录中再次执行我们的命令，看看它是如何生成一个项目结构并用新值替换所有变量的:

```
// To generate a Node.js MVC project
scaffold create node --variant mvc

// To generate a default Node.js project
scaffold create node 
```

执行完命令后，它会开始询问您变量值，一旦过程完成，它会显示一条成功消息。要检查一切是否按预期进行，请打开一个包含变量的文件，您应该看到在 CLI 过程中输入的文本，而不是大写的标识符。

如果您已经使用了[repo](https://github . com/site point-editors/Node-scaffolding-tool
)中的模板，那么您应该已经生成了工作节点项目，可以通过运行`npm install`然后运行`npm start`来启动这些项目。

## 下一步做什么

我们已经成功地创建了一个 CLI 工具来从模板创建新的 Node.js 项目，但是我们不必就此止步。因为我们是从零开始构建我们的工具，所以我们对它能做什么有绝对的自由。你可以把以下想法作为灵感:

*   扩展变量以替换代码块而不是简单的单词；您可以在`sed`函数中使用更复杂的正则表达式和捕获组来实现这一点。
*   添加更多的命令来为每种项目创建特定的文件，比如 MVC 模板的新模型。
*   包括将项目部署到服务器的命令，这可以通过 SSH 使用 rsync 和远程命令的库来实现。
*   如果您有一个复杂的设置，您也可以尝试添加命令来构建静态资产或源文件，这对于静态站点非常有用。
*   使用`mv`功能从变量名重命名文件。

## 结论

在本教程中，我演示了如何构建一个 CLI，以便在熟悉的环境中快速启动新项目。但是这不是一个单一用途的项目——您可以根据需要扩展它。自动化工具的创建是开发人员的特征。如果你发现自己在做重复性的工作，就停下来想一想是否可以自动化。大多数情况下这是可能的，而且长期的好处是巨大的。

现在轮到你了？你喜欢自动化去除重复和乏味的工作吗？你选择的工具包是什么？请在下面的评论中告诉我。

*这篇文章由[琼·尹](https://github.com/newjs)、[卡米洛·雷耶斯](https://www.sitepoint.com/author/creyes/)和[蒂姆·塞韦里安](https://www.sitepoint.com/author/tseverien/)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

## 分享这篇文章