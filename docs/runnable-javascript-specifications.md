# 如何构建可运行的 JavaScript 规范

> 原文：<https://www.sitepoint.com/runnable-javascript-specifications/>

编程不仅仅是给计算机如何完成一项任务的指令，它也是以一种精确的方式与其他人，甚至是未来的自己交流思想。这种沟通可以有多个目标，也许是为了分享信息，或者只是为了更容易地修改——如果你不理解或者不记得很久以前做过什么，就很难改变一些事情。文档是关键，无论是代码中的简单注释还是描述程序整体功能的完整文档。

![A Simple JavaScript Specification File](img/1b2ecb5baa678e1029ea087ef3803be7.png)

当我们编写软件时，我们还需要确保代码具有预期的功能。虽然有[正式的方法来定义语义](https://en.wikipedia.org/wiki/Semantics_(computer_science)#Approaches)，但最简单快捷(但不太严格)的方法是将该功能投入使用，并看看它是否能产生预期的结果。

大多数开发人员都熟悉这些实践:将代码文档作为注释来明确代码块的目标，以及一系列测试来确保函数给出期望的输出。

但是通常文档和测试是在不同的步骤中完成的。通过统一这些实践，我们可以为参与项目开发的任何人提供更好的体验。本文探索了一个运行 JavaScript 规范的程序的简单实现，该规范适用于文档和测试。

我们将构建一个命令行界面，它在一个目录中找到所有的规范文件，提取在每个规范中找到的所有断言，并评估它们的结果，最终显示哪些断言失败了，哪些通过了。

## 规范格式

每个规范文件将从一个[模板文本](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals.)中导出一个单独的字符串。第一行可以作为规范的标题。模板文字允许我们在字符串之间嵌入 JS 表达式，每个表达式代表一个断言。为了识别每个断言，我们可以用一个独特的字符开始一行，在这种情况下，我们可以使用条形字符(`|`)和破折号(`-`)的组合，它类似于一个[十字转门符号](https://en.wikipedia.org/wiki/Turnstile_(symbol))，有时可以作为逻辑断言的符号表示。

下面是一个示例，并对其用法进行了一些解释:

```
const dependency = require('./dependency')

module.exports = ` Example of a Specification File

  This project allows to test JavaScript programs using specification files.
  Every *.spec.js file exports a single template literal that includes a general
  explanation of the file being specified. Each file represents a logical
  component of a bigger system. Each logical component is composed of several
  units of functionality that can be tested for certain properties.
  Each one of this units of functionality may have one or more
  assertions. Each assertion is denoted by a line as the following:

  |- ${dependency} The dependency has been loaded and the first assert has
  been evaluated.

  Multiple assertions can be made for each file:

  |- ${false} This assertion will fail.

  |- ${2 + 2 === 4} This assertion will succeed.

  The combination of | and - will form a Turnstile ligature (|-) using the appropriate
  font. Fira Code is recommended. A Turnstile symbol was used by Gottlob Frege
  at the start of sentenses being asserted as true.

  The intended usage is for specification-first software. Where the programmer
  defines the high level structure of a program in terms of a specification,
  then progressively builds the parts conforming that specification until all
  the tests are passed. A desired side-effect is having a simple way to generate
  up-to-date documentation outside the code for API consumers. ` 
```

现在让我们继续我们程序的高层结构。

## 我们程序的结构

我们程序的整个结构可以用几行代码来定义，除了两个 Node.js 库来处理文件系统(`fs`)和目录路径(`path`)之外，没有任何依赖性。在这一节中，我们只定义程序的结构，函数的定义将在下一节中给出。

```
#!/usr/bin/env node

const fs = require('fs')
const path = require('path')

const specRegExp = /\.spec\.js$/
const target = path.join(process.cwd(), process.argv[2])

// Get all the specification file paths
// If a specification file is provided then just test that file
// Otherwise find all the specification files in the target directory
const paths = specRegExp.test(target)
  ? [ target ]
  : findSpecifications(target, specRegExp).filter(x => x)

// Get the content of each specification file
// Get the assertions of each specification file
const assertionGroups = getAssertions(getSpecifications(paths))

// Log all the assertions
logAssertions(assertionGroups)

// Check for any failed assertions and return an appropriate exit code
process.exitCode = checkAssertions(assertionGroups) 
```

因为这也是我们的 [CLI](https://en.wikipedia.org/wiki/Command-line_interface) ( *命令行界面*)的入口点，我们需要添加第一行，即 [shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)) ，它指示这个文件应该由`node`程序执行。不需要添加特定的库来处理命令选项，因为我们只对单个参数感兴趣。但是，如果您计划以相当大的方式扩展此计划，您可以考虑其他选项。

为了获得目标测试文件或目录，我们必须用用户提供的参数作为执行命令(使用`process.argv[2]`)时的第一个参数*连接*执行命令(使用`process.cwd()`)的路径。您可以在流程对象的[的 Node.js 文档中找到对这些值的引用。通过这种方式，我们获得了目标目录/文件的绝对路径。](https://nodejs.org/docs/latest/api/process.html)

现在，我们要做的第一件事是找到所有的 JavaScript 规范文件。如第 12 行所示，我们可以使用[条件操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)来提供更大的灵活性:如果用户提供一个规范文件作为目标，那么我们就直接使用该文件路径，否则，如果用户提供一个目录路径，那么我们就必须找到所有与我们的模式匹配的文件，如`specRegExp`常量所定义的，我们使用一个`findSpecifications`函数来完成这个任务，我们将在后面定义这个函数。这个函数将返回目标目录中每个规范文件的路径数组。

在第 18 行，我们将常量`assertionGroups`定义为两个函数`getSpecifications()`和`getAssertions()`的组合结果。首先，我们获取每个规范文件的内容，然后从中提取断言。我们将在后面定义这两个函数，现在只需注意，我们使用第一个函数的输出作为第二个函数的参数，从而简化了过程，并在这两个函数之间建立了直接的联系。虽然我们可能只有一个函数，但是通过将它们分开，我们可以更好地了解实际的过程，记住程序应该清晰易懂；仅仅让它发挥作用是不够的。

`assertionsGroup`常量的结构如下:

`assertionGroup[specification][assertion]`

接下来，我们将所有这些断言记录到用户日志中，作为使用`logAssertions()`函数报告结果的一种方式。每个断言将包含结果(`true`或`false`)和一个小描述，我们可以使用这些信息为每种类型的结果赋予一种特殊的颜色。

最后，我们根据断言的结果定义了[退出代码](https://nodejs.org/docs/latest/api/process.html#process_process_exitcode)。这给了进程关于程序如何结束的信息:*进程是成功了还是失败了？*。退出代码为`0`意味着进程成功退出，或者如果失败则为`1`，或者在我们的例子中，当*至少有一个*断言失败时。

## 查找所有规范文件

为了找到所有的 JavaScript 规范文件，我们可以使用一个递归函数来遍历用户作为 CLI 参数指定的目录。当我们搜索时，应该用我们在程序开始时定义的正则表达式(`/\.spec\.js$/`)检查每个文件，它将匹配所有以`.spec.js`结尾的文件路径。

```
function findSpecifications (dir, matchPattern) {
  return fs.readdirSync(dir)
    .map(filePath => path.join(dir, filePath))
    .filter(filePath => matchPattern.test(filePath) && fs.statSync(filePath).isFile())
} 
```

我们的`findSpecifications`函数接受一个目标目录(`dir`)和一个标识规范文件的正则表达式(`matchPattern`)。

## 获取每个规范的内容

因为我们是导出模板文字，所以获取内容和被评估的断言很简单，我们必须导入每个文件，当它被导入时，所有的断言都会被自动评估。

```
function getSpecifications (paths) {
  return paths.map(path => require(path))
} 
```

使用`map()`函数，我们使用节点的`require`函数用文件的内容替换数组的路径。

## 从文本中提取断言

此时，我们有了一个数组，其中包含每个规范文件的内容以及已经评估过的断言。我们使用十字转门指示器(`|-`)来找到所有这些断言并提取它们。

```
function getAssertions (specifications) {
  return specifications.map(specification => ({
    title: specification.split('\n\n', 1)[0].trim(),
    assertions: specification.match(/^( |\t)*(\|-)(.|\n)*?\./gm).map(assertion => {
      const assertionFragments = /(?:\|-) (\w*) ((?:.|\n)*)/.exec(assertion)

      return {
        value: assertionFragments[1],
        description: assertionFragments[2].replace(/\n /, '')
      }
    })
  }))
} 
```

此函数将返回一个类似的数组，但是用一个遵循以下结构的对象替换每个规范的内容:

```
{
  title: <String: Name of this particular specification>,
  assertions: [
    {
      value: <Boolean: The result of the assertion>,
      description: <String: The short description for the assertion>
    }
  ]
} 
```

用规范字符串的第一行设置`title`。然后每个断言作为一个数组存储在`assertions`键中。`value`将断言的结果表示为一个*布尔*。我们将使用这个值来知道断言是否成功。此外，描述将显示给用户，作为识别哪些断言成功，哪些失败的一种方式。我们在每种情况下都使用正则表达式。

## 记录结果

我们在程序中构建的数组现在有一系列 JavaScript 规范文件，这些文件包含找到的断言及其结果和描述的列表，所以除了向用户报告结果之外，没有什么要做的。

```
function logAssertions(assertionGroups) {
  // Methods to log text with colors
  const ansiColor = {
    blue: text => console.log(`\x1b[1m\x1b[34m${text}\x1b[39m\x1b[22m`),
    green: text => console.log(`\x1b[32m  ✔ ${text}\x1b[39m`),
    red: text => console.log(`\x1b[31m  ✖ ${text}\x1b[39m`)
  }

  // Log the results
  assertionGroups.forEach(group => {
    ansiColor.blue(group.title)

    group.assertions.forEach(assertion => {
      assertion.value === 'true'
        ? ansiColor.green(assertion.description)
        : ansiColor.red(assertion.description)
    })
  })

  console.log('\n')
} 
```

我们可以根据结果用颜色格式化我们的输入。为了在终端上显示颜色，我们需要添加 [ANSI 转义码](https://en.wikipedia.org/wiki/ANSI_escape_code)。为了简化它们在下一个块中的用法，我们将每种颜色保存为一个`ansiColor`对象的方法。

首先，我们要显示规范的标题，记住我们为每个规范使用数组的第一维，我们将其命名为断言的`group`。)然后，我们根据它们各自的颜色记录所有的断言:绿色表示评估为`true`的断言，红色表示具有另一个值的断言。请注意比较，我们将`true`、**作为字符串**进行检查，因为我们从每个文件中接收字符串。

## 检查结果

最后，最后一步是检查所有测试是否成功。

```
function checkAssertions (assertionGroups) {
  return assertionGroups.some(
    group => group.assertions.some(assertion => assertion.value === 'false')
  ) ? 1 : 0
} 
```

我们使用`Array`的`some()`方法检查每个断言组(规范),看看*是否至少有一个*值是`'``false``'`。我们嵌套了其中的两个，因为我们有一个二维数组。

## 运行我们的程序

此时，我们的 CLI 应该准备好运行一些 JavaScript 规范，看看断言是否被拾取和评估。在`test`目录中，您可以复制本文开头的规范示例，并将以下命令粘贴到您的`package.json`文件中:

```
"scripts": {
  "test": "node index.js test"
} 
```

…其中`test`是包含样本规范文件的目录的名称。

当运行`npm test`命令时，您应该会看到带有各自颜色的结果。

## 遗言

我们实现了一个非常简单但有用的命令行程序，可以帮助我们制作更好的软件。我们可以从中吸取一些教训:

*   软件可以既简单又有用。
*   如果我们想要不同的东西，我们可以构建自己的工具，没有理由去遵从。
*   软件不仅仅是“让它工作”,还包括交流思想。
*   有时我们可以通过改变观点来改善一些事情。在这种情况下，规范文件的格式:只是一个简单的字符串！

这个程序的工作流程的一个例子是，在你的项目中每个模块放置一个`.spec.js`文件，以断言的形式详细描述程序应该具有的预期功能和属性。你可以用这种方式勾画一个新项目的想法，并不断改进，直到所有的断言都通过。

你可以在这里找到本文[使用的源代码。](https://github.com/KolceBackyard/spec)

## 分享这篇文章