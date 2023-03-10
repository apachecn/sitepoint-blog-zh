# 在 Deno 中使用文件系统

> 原文：<https://www.sitepoint.com/deno-file-system/>

在本文中，我们将在对 Deno 的[介绍的基础上，创建一个可以在文件和文件夹中搜索文本的命令行工具。我们将使用 Deno 提供的一系列 API 方法来读写文件系统。](https://www.sitepoint.com/learn-deno/)

在我们的上一期文章中，我们使用 Deno 构建了一个命令行工具来请求第三方 API 。在本文中，我们将把网络放在一边，构建一个工具，让您在文件系统中搜索当前目录下的文件和文件夹中的文本——类似于像`grep`这样的工具。

*注意:我们并不是在构建一个像`grep`一样优化和高效的工具，也不打算取代它！构建这样一个工具的目的是为了熟悉 Deno 的文件系统 API。*

## 安装 Deno

我们将假设您已经在本地机器上安装并运行了 Deno。你可以查看 [Deno 网站](https://deno.land/#installation)或[以前的文章](https://www.sitepoint.com/deno-fetch-data-third-party-api/)以获得更多详细的安装说明，也可以获得关于如何为你选择的编辑器添加 Deno 支持的信息。

在写这篇文章的时候，Deno 的最新稳定版本是 **1.10.2** ，所以我在这篇文章里用的就是这个。

作为参考，你可以在 GitHub 上找到这篇文章[的完整代码。](https://gist.github.com/SitePointEditors/b70ef271a3a04ac4df368b85cf53ab82)

## 用 Yargs 建立我们的新指挥部

和上一篇文章一样，我们将使用 [Yargs](https://deno.land/x/yargs@v17.0.1-deno) 来构建用户可以用来执行我们工具的接口。让我们创建`index.ts`并用以下内容填充它:

```
import yargs from "https://deno.land/x/yargs@v17.0.1-deno/deno.ts";

interface Yargs<ArgvReturnType> {
  describe: (param: string, description: string) => Yargs<ArgvReturnType>;
  demandOption: (required: string[]) => Yargs<ArgvReturnType>;
  argv: ArgvReturnType;
}

interface UserArguments {
  text: string;
}

const userArguments: UserArguments =
  (yargs(Deno.args) as unknown as Yargs<UserArguments>)
    .describe("text", "the text to search for within the current directory")
    .demandOption(["text"])
    .argv;

console.log(userArguments); 
```

这里有一点值得指出:

*   我们通过指向它在 Deno 存储库中的路径来安装 Yargs。我明确地使用了一个精确的版本号来确保我们总是得到那个版本，这样我们就不会在脚本运行时使用最新的版本。
*   在撰写本文时，Yargs 的 Deno + TypeScript 体验并不好，所以我创建了自己的接口，并使用它来提供一些类型安全。
*   包含我们将向用户询问的所有输入。现在，我们只要求输入`text`，但是将来我们可以扩展它来提供一个要搜索的文件列表，而不是假设当前目录。

我们可以用`deno run index.ts`来运行它，看看我们的 Yargs 输出:

```
$ deno run index.ts
Check file:///home/jack/git/deno-file-search/index.ts
Options:
  --help     Show help                                                 [boolean]
  --version  Show version number                                       [boolean]
  --text     the text to search for within the current directory      [required]

Missing required argument: text 
```

现在是开始实施的时候了！

## 列出文件

在开始搜索给定文件中的文本之前，我们需要生成一个要搜索的目录和文件列表。Deno 提供了 [`Deno.readdir`](https://doc.deno.land/builtin/stable#Deno.readDir) ，这是“内置”库的一部分，意味着你不必导入它。您可以在全局名称空间上使用它。

`Deno.readdir`是异步的，返回当前目录下的文件和文件夹列表。它将这些条目作为 [`AsyncIterator`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/asyncIterator) 返回，这意味着我们必须使用`for await ... of`循环来获得结果:

```
for await (const fileOrFolder of Deno.readDir(Deno.cwd())) {
  console.log(fileOrFolder);
} 
```

这段代码将从当前的工作目录中读取(这是`Deno.cwd()`给我们的)并记录每个结果。但是，如果您现在尝试运行该脚本，您会得到一个错误:

```
$ deno run index.ts --text='foo'
error: Uncaught PermissionDenied: Requires read access to <CWD>, run again with the --allow-read flag
for await (const fileOrFolder of Deno.readDir(Deno.cwd())) {
                                                   ^
    at deno:core/core.js:86:46
    at unwrapOpResult (deno:core/core.js:106:13)
    at Object.opSync (deno:core/core.js:120:12)
    at Object.cwd (deno:runtime/js/30_fs.js:57:17)
    at file:///home/jack/git/deno-file-search/index.ts:19:52 
```

请记住，Deno 要求所有脚本都被明确授予从文件系统中读取的权限。在我们的例子中，`--allow-read`标志将使我们的代码能够运行:

```
~/$ deno run --allow-read index.ts --text='foo'
{ name: ".git", isFile: false, isDirectory: true, isSymlink: false }
{ name: ".vscode", isFile: false, isDirectory: true, isSymlink: false }
{ name: "index.ts", isFile: true, isDirectory: false, isSymlink: false } 
```

在这种情况下，我在构建工具的目录中运行脚本，因此它会找到 TS 源代码、`.git`存储库和`.vscode`文件夹。让我们开始编写一些函数来递归地导航这个结构，因为我们需要找到目录中的所有文件，而不仅仅是顶级文件。此外，我们可以添加一些常见的忽略。我不认为有人会希望脚本搜索整个`.git`文件夹！

在下面的代码中，我们创建了`getFilesList`函数，它获取一个目录并返回该目录中的所有文件。如果遇到一个目录，它将递归地调用自己来查找任何嵌套文件，并返回结果:

```
const IGNORED_DIRECTORIES = new Set([".git"]);

async function getFilesList(
  directory: string,
): Promise<string[]> {
  const foundFiles: string[] = [];
  for await (const fileOrFolder of Deno.readDir(directory)) {
    if (fileOrFolder.isDirectory) {
      if (IGNORED_DIRECTORIES.has(fileOrFolder.name)) {
        // Skip this folder, it's in the ignore list.
        continue;
      }
      // If it's not ignored, recurse and search this folder for files.
      const nestedFiles = await getFilesList(
        `${directory}/${fileOrFolder.name}`,
      );
      foundFiles.push(...nestedFiles);
    } else {
      // We found a file, so store it.
      foundFiles.push(`${directory}/${fileOrFolder.name}`);
    }
  }
  return foundFiles;
} 
```

我们可以这样使用它:

```
const files = await getFilesList(Deno.cwd());
console.log(files); 
```

我们还得到了一些看起来不错的输出:

```
$ deno run --allow-read index.ts --text='foo'
[
  "/home/jack/git/deno-file-search/.vscode/settings.json",
  "/home/jack/git/deno-file-search/index.ts"
] 
```

## 使用`path`模块

我们现在可以将文件路径与模板字符串结合起来，如下所示:

```
`${directory}/${fileOrFolder.name}`, 
```

但是如果使用 Deno 的`path`模块来做这件事会更好。这个模块是 Deno 作为其标准库的一部分提供的模块之一(很像 Node 的`path`模块)，如果你使用过 Node 的`path`模块，代码看起来会非常相似。在编写的时候，Deno 提供的`std`库的最新版本是`0.97.0`，我们从`mod.ts`文件导入`path`模块:

```
import * as path from "https://deno.land/std@0.97.0/path/mod.ts"; 
```

`mod.ts`总是导入 Deno 的标准模块时的入口点。这个模块[的文档位于 Deno 站点](https://doc.deno.land/https/deno.land/std@0.97.0/path/mod.ts)上，列出了`path.join`，它将采用多条路径并将它们连接成一条路径。让我们导入并使用这些函数，而不是手动组合它们:

```
// import added to the top of our script
import yargs from "https://deno.land/x/yargs@v17.0.1-deno/deno.ts";
import * as path from "https://deno.land/std@0.97.0/path/mod.ts";

// update our usages of the function:
async function getFilesList(
  directory: string,
): Promise<string[]> {
  const foundFiles: string[] = [];
  for await (const fileOrFolder of Deno.readDir(directory)) {
    if (fileOrFolder.isDirectory) {
      if (IGNORED_DIRECTORIES.has(fileOrFolder.name)) {
        // Skip this folder, it's in the ignore list.
        continue;
      }
      // If it's not ignored, recurse and search this folder for files.
      const nestedFiles = await getFilesList(
        path.join(directory, fileOrFolder.name),
      );
      foundFiles.push(...nestedFiles);
    } else {
      // We found a file, so store it.
      foundFiles.push(path.join(directory, fileOrFolder.name));
    }
  }
  return foundFiles;
} 
```

当使用标准库时，记住固定一个特定的版本是至关重要的。如果不这样做，您的代码将始终加载最新版本，即使其中包含会破坏您的代码的更改。标准库上的 Deno 文档对此进行了更深入的研究，我建议阅读该页面。

## 读取文件的内容

与 Node 不同，Node 让您通过`fs`模块和`readFile`方法读取文件内容，Deno 提供了 [`readTextFile`](https://doc.deno.land/builtin/stable#Deno.readTextFile) 作为其核心的一部分，这意味着在这种情况下我们不需要导入任何额外的模块。`readTextFile`假设文件编码为 UTF-8，对于文本文件，这通常是您想要的。如果你正在使用一个不同的文件编码，你可以使用更通用的 [`readFile`](https://doc.deno.land/builtin/stable#Deno.readFile) ，它没有任何关于编码的假设，并让你传入一个特定的解码器。

一旦我们获得了文件列表，我们就可以遍历它们，并以文本形式读取它们的内容:

```
const files = await getFilesList(Deno.cwd());

files.forEach(async (file) => {
  const contents = await Deno.readTextFile(file);
  console.log(contents);
}); 
```

因为我们在找到匹配项时想知道行号，所以我们可以在新的行字符(`\n`)上拆分内容，然后依次搜索每一行，看是否有匹配项。这样，如果有的话，我们就知道行号的索引，这样我们就可以把它报告给用户:

```
files.forEach(async (file) => {
  const contents = await Deno.readTextFile(file);
  const lines = contents.split("\n");
  lines.forEach((line, index) => {
    if (line.includes(userArguments.text)) {
      console.log("MATCH", line);
    }
  });
}); 
```

为了存储我们的匹配，我们可以创建一个表示一个`Match`的接口，并在我们找到匹配时将它们推送到一个数组中:

```
interface Match {
  file: string;
  line: number;
}
const matches: Match[] = [];
files.forEach(async (file) => {
  const contents = await Deno.readTextFile(file);
  const lines = contents.split("\n");
  lines.forEach((line, index) => {
    if (line.includes(userArguments.text)) {
      matches.push({
        file,
        line: index + 1,
      });
    }
  });
}); 
```

然后我们可以注销匹配:

```
matches.forEach((match) => {
  console.log(match.file, "line:", match.line);
}); 
```

但是，如果您现在运行该脚本，并为它提供一些肯定匹配的文本，您仍然会看到没有匹配记录到控制台。这是人们在`forEach`呼叫中使用`async`和`await`时经常犯的错误；在认为自己完成之前，`forEach`不会等待回调完成。以这段代码为例:

```
files.forEach(file => {
  new Promise(resolve => {
    ...
  })
}) 
```

JavaScript 引擎将执行在每个文件上运行的`forEach`,生成一个新的承诺，然后继续执行剩余的代码。它不会自动等待那些承诺来解决，当我们使用`await`时也是一样。

好消息是，这将在一个`for ... of`循环中按预期工作，而不是:

```
files.forEach(file => {...}) 
```

我们可以换成:

```
for (const file of files) {
  ...
} 
```

`for ... of`循环将连续执行每个文件的代码，当看到使用了`await`关键字时，它将暂停执行，直到该承诺得到解决。这意味着，在循环执行之后，我们知道所有的承诺都已解决，现在我们确实将匹配记录到屏幕上:

```
$ deno run --allow-read index.ts --text='readTextFile'
Check file:///home/jack/git/deno-file-search/index.ts
/home/jack/git/deno-file-search/index.ts line: 54 
```

让我们对输出进行一些改进，使其更易于阅读。与其将匹配存储为一个数组，不如让它成为一个`Map`，其中键是文件名，值是所有匹配的一个`Set`。这样，我们可以通过列出按文件分组的匹配来阐明我们的输出，并拥有一个让我们更容易浏览数据的数据结构。

首先，我们可以创建数据结构:

```
const matches = new Map<string, Set<Match>>(); 
```

然后，我们可以通过将它们添加到给定文件的`Set`中来存储匹配。这比以前多了一点工作量。我们现在不能将项目放入数组。我们首先必须找到任何现有的匹配(或创建一个新的`Set`)然后存储它们:

```
for (const file of files) {
  const contents = await Deno.readTextFile(file);
  const lines = contents.split("\n");
  lines.forEach((line, index) => {
    if (line.includes(userArguments.text)) {
      const matchesForFile = matches.get(file) || new Set<Match>();
      matchesForFile.add({
        file,
        line: index + 1,
      });
      matches.set(file, matchesForFile);
    }
  });
} 
```

然后我们可以通过迭代`Map`来记录匹配。当您在一个`Map`上使用`for ... of`时，每次迭代都会给出一个包含两个条目的数组，其中第一个是映射中的键，第二个是值:

```
for (const match of matches) {
  const fileName = match[0];
  const fileMatches = match[1];
  console.log(fileName);
  fileMatches.forEach((m) => {
    console.log("=>", m.line);
  });
} 
```

我们可以做一些析构来使它更整洁一点:

```
for (const match of matches) {
  const [fileName, fileMatches] = match; 
```

或者甚至:

```
for (const [fileName, fileMatches] of matches) { 
```

现在，当我们运行脚本时，我们可以看到给定文件中的所有匹配项:

```
$ deno run --allow-read index.ts --text='Deno'
/home/jack/git/deno-file-search/index.ts
=> 15
=> 26
=> 45
=> 54 
```

最后，为了使输出更加清晰，让我们存储实际匹配的行。首先，我将更新我的`Match`界面:

```
interface Match {
  file: string;
  lineNumber: number;
  lineText: string;
} 
```

然后更新存储匹配项的代码。这里关于 TypeScript 的一个非常好的事情是，您可以更新`Match`接口，然后让编译器告诉您需要更新的代码。我会经常更新一个类型，然后等待 VS 代码突出任何问题。如果您不太记得所有代码需要更新的地方，这是一种非常有效的工作方式:

```
if (line.includes(userArguments.text)) {
  const matchesForFile = matches.get(file) || new Set<Match>();
  matchesForFile.add({
    file,
    lineNumber: index + 1,
    lineText: line,
  });
  matches.set(file, matchesForFile);
} 
```

输出匹配的代码也需要更新:

```
for (const [fileName, fileMatches] of matches) {
  console.log(fileName);
  fileMatches.forEach((m) => {
    console.log("=>", m.lineNumber, m.lineText.trim());
  });
} 
```

我决定在我们的`lineText`上调用`trim()`,这样，如果匹配的行严重缩进，我们就不会在结果中显示出来。我们将去掉输出中的任何前导(和尾随)空格。

至此，我可以说我们的第一个版本已经完成了！

```
$ deno run --allow-read index.ts --text='Deno'
Check file:///home/jack/git/deno-file-search/index.ts
/home/jack/git/deno-file-search/index.ts
=> 15 (yargs(Deno.args) as unknown as Yargs<UserArguments>)
=> 26 for await (const fileOrFolder of Deno.readDir(directory)) {
=> 45 const files = await getFilesList(Deno.cwd());
=> 55 const contents = await Deno.readTextFile(file); 
```

## 按文件扩展名过滤

让我们扩展这个功能，这样用户就可以通过一个`extension`标志过滤我们匹配的文件扩展名，用户可以将扩展名传递给这个标志(比如`--extension js`只匹配`.js`文件)。首先，让我们更新 Yargs 代码和类型，告诉编译器我们正在接受一个(可选的)扩展标志:

```
interface UserArguments {
  text: string;
  extension?: string;
}

const userArguments: UserArguments =
  (yargs(Deno.args) as unknown as Yargs<UserArguments>)
    .describe("text", "the text to search for within the current directory")
    .describe("extension", "a file extension to match against")
    .demandOption(["text"])
    .argv; 
```

然后我们可以更新`getFilesList`,让它接受一个可选的第二个参数，这个参数可以是我们可以传递给函数的配置属性的对象。我经常喜欢函数接受配置项的对象，因为向该对象添加更多的项比更新函数以要求传入更多的参数要容易得多:

```
interface FilterOptions {
  extension?: string;
}

async function getFilesList(
  directory: string,
  options: FilterOptions = {},
): Promise<string[]> {} 
```

现在，在函数体中，一旦我们找到一个文件，我们现在检查:

*   用户没有提供用于过滤的`extension`。
*   用户确实提供了一个`extension`来过滤，并且文件的扩展名与他们提供的相匹配。我们可以使用`path.extname`，它返回给定路径的文件扩展名(对于`foo.ts`，它将返回`.ts`，因此我们获取用户传入的扩展名并在它前面加上一个`.`)。

```
async function getFilesList(
  directory: string,
  options: FilterOptions = {},
): Promise<string[]> {
  const foundFiles: string[] = [];
  for await (const fileOrFolder of Deno.readDir(directory)) {
    if (fileOrFolder.isDirectory) {
      if (IGNORED_DIRECTORIES.has(fileOrFolder.name)) {
        // Skip this folder, it's in the ignore list.
        continue;
      }
      // If it's not ignored, recurse and search this folder for files.
      const nestedFiles = await getFilesList(
        path.join(directory, fileOrFolder.name),
        options,
      );
      foundFiles.push(...nestedFiles);
    } else {
      // We know it's a file, and not a folder.

      // True if we weren't given an extension to filter, or if we were and the file's extension matches the provided filter.
      const shouldStoreFile = !options.extension ||
        path.extname(fileOrFolder.name) === `.${options.extension}`;

      if (shouldStoreFile) {
        foundFiles.push(path.join(directory, fileOrFolder.name));
      }
    }
  }
  return foundFiles;
} 
```

最后，我们需要更新对`getFilesList`函数的调用，向它传递用户输入的任何参数:

```
const files = await getFilesList(Deno.cwd(), userArguments); 
```

## 查找和替换

最后，让我们扩展我们的工具，允许基本的替换。如果用户通过了`--replace=foo`，我们将从他们的搜索中获取任何匹配，并在将文件写入磁盘之前，用提供的单词替换它们——在本例中为`foo`。我们可以用 [`Deno.writeTextFile`](https://doc.deno.land/builtin/stable#Deno.writeTextFile) 来做到这一点。(就像使用`readTextFile`一样，如果你需要对编码进行更多的控制，你也可以使用`writeFile`。)

同样，我们将首先更新我们的 Yargs 代码，以允许提供参数:

```
interface UserArguments {
  text: string;
  extension?: string;
  replace?: string;
}

const userArguments: UserArguments =
  (yargs(Deno.args) as unknown as Yargs<UserArguments>)
    .describe("text", "the text to search for within the current directory")
    .describe("extension", "a file extension to match against")
    .describe("replace", "the text to replace any matches with")
    .demandOption(["text"])
    .argv; 
```

我们现在可以做的是更新我们的代码，循环遍历每个单独的文件来搜索任何匹配。一旦我们检查了每一行的匹配，我们就可以使用`replaceAll`方法(这是一个内置于 JavaScript 中的相对较新的方法[)来获取文件的内容，并将每个匹配替换为用户提供的替换文本:](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replaceAll)

```
for (const file of files) {
  const contents = await Deno.readTextFile(file);
  const lines = contents.split("\n");
  lines.forEach((line, index) => {
    if (line.includes(userArguments.text)) {
      const matchesForFile = matches.get(file) || new Set<Match>();
      matchesForFile.add({
        file,
        lineNumber: index + 1,
        lineText: line,
      });
      matches.set(file, matchesForFile);
    }
  });

  if (userArguments.replace) {
    const newContents = contents.replaceAll(
      userArguments.text,
      userArguments.replace,
    );
    // TODO: write to disk
  }
} 
```

写入磁盘就是调用`writeTextFile`，提供文件路径和新内容:

```
if (userArguments.replace) {
  const newContents = contents.replaceAll(
    userArguments.text,
    userArguments.replace,
  );
  await Deno.writeTextFile(file, newContents);
} 
```

然而，当运行这个命令时，我们会得到一个权限错误。Deno 将文件读取和文件写入分成不同的权限，因此您需要传递`--allow-write`标志以避免错误:

```
$ deno run --allow-read index.ts --text='readTextFile' --extension=ts --replace='jackWasHere'
Check file:///home/jack/git/deno-file-search/index.ts
error: Uncaught (in promise) PermissionDenied: Requires write access to "/home/jack/git/deno-file-search/index.ts", run again with the --allow-write flag
    await Deno.writeTextFile(file, newContents); 
```

您可以传递`--allow-write`或者更具体地传递`--allow-write=.`，这意味着该工具只允许在当前目录中写文件:

```
$ deno run --allow-read --allow-write=. index.ts --text='readTextFile' --extension=ts --replace='jackWasHere'
/home/jack/git/deno-file-search/index.ts
=> 74 const contents = await Deno.readTextFile(file); 
```

## 编译成可执行文件

既然我们已经有了自己的脚本，并准备好分享它，让我们要求 Deno 将我们的工具捆绑到一个可执行文件中。这样，我们的最终用户就不必运行 Deno，也不必每次都传入所有相关的权限标志；捆绑销售时我们可以做到这一点。 [`deno compile`](https://deno.land/manual/tools/compiler) 让我们这样做:

```
$ deno compile --allow-read --allow-write=. index.ts
Check file:///home/jack/git/deno-file-search/index.ts
Bundle file:///home/jack/git/deno-file-search/index.ts
Compile file:///home/jack/git/deno-file-search/index.ts
Emit deno-file-search 
```

然后我们可以调用可执行文件:

```
$ ./deno-file-search index.ts --text=readTextFile --extension=ts
/home/jack/git/deno-file-search/index.ts
=> 74 const contents = await Deno.readTextFile(file); 
```

我真的很喜欢这种方式。我们能够捆绑该工具，因此我们的用户不必编译任何东西，并且通过预先提供权限，我们意味着用户不必这样做。当然，这是一种取舍。一些用户可能希望提供权限，这样他们就可以完全了解我们的脚本能做什么和不能做什么，但是我认为更多的时候将权限加入到可执行文件中是有好处的。

## 结论

我真的很喜欢在德诺工作。与 Node 相比，我喜欢 TypeScript、Deno Format 和其他工具开箱即用。我不必设置我的节点项目，然后更漂亮，然后找出最好的方法来添加 TypeScript 到其中。

Deno(不出所料)不像 Node 那样完善或充实。Node 中存在的许多第三方包都没有很好的 Deno 等价物(尽管我希望这一点会随着时间的推移而改变)，并且有时文档虽然很全面，但是很难找到。但是这些都是你在任何相对较新的编程环境和语言中会遇到的小问题。我强烈推荐探索 Deno 并尝试一下。它肯定会留在这里。

SitePoint 上关于 Deno 的文章越来越多。[如果你想进一步探索迪诺，请点击这里](https://www.sitepoint.com/tag/deno/)查看。

## 分享这篇文章