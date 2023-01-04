# 如何用 JavaScript 编写 Shell 脚本

> 原文：<https://www.sitepoint.com/shell-scripts-javascript/>

“如何用 JavaScript 编写 Shell 脚本”是我们最新的 JavaScript 时事通讯的社论。

本周，我不得不升级一个客户的网站以使用 SSL。这本身并不是一项困难的任务——安装证书只是点击一个按钮——然而一旦我进行了切换，我就剩下许多混合内容的警告。修复这些问题意味着我必须浏览主题目录(这是一个 WordPress 站点),并识别所有通过 HTTP 包含资产的文件。

以前，我会使用一个小的 Ruby 脚本来实现自动化。Ruby 是我学习的第一门编程语言，非常适合这类任务。然而，我们最近发表了一篇关于使用 Node 创建命令行界面的文章。这篇文章提醒了我，JavaScript 早已超越了浏览器，并且可以(和其他许多东西一样)在桌面脚本中发挥巨大作用。

在本文的剩余部分，我将解释如何使用 JavaScript 递归遍历目录中的文件，并识别指定字符串的任何出现。我还将温和地介绍用 JavaScript 编写 shell 脚本，并让您走上编写自己的 shell 脚本的道路。

### 建立

这里唯一的先决条件是 Node.js。如果你还没有安装，你可以去他们的网站[下载一个二进制文件](https://nodejs.org/en/download/)。或者，您可以使用 nvm 等版本管理器。我们在这里有一个关于 T2 的教程。

## 您的第一个 Shell 脚本

那么从哪里开始呢？我们需要做的第一件事是遍历主题目录中的所有文件。幸运的是，Node 的本地文件系统模块附带了一个我们可以使用的 [readdir 方法](https://nodejs.org/api/fs.html#fs_fs_readdir_path_options_callback)。它将目录路径和回调函数作为参数。回调得到两个参数(`err`和`entries`)，其中`entries`是目录中`entries`的名称数组，不包括`.`和`..`——分别是当前目录和父目录。

```
const fs = require('fs');

function buildTree(startPath) {
  fs.readdir(startPath, (err, entries) => {
    console.log(entries);
  });
}

buildTree('/home/jim/Desktop/theme'); 
```

*如果您正在遵循这一点，将上面的内容保存在一个名为`search_and_replace.js`的文件中，并使用`node search_and_replace.js`从命令行运行它。您还需要调整您正在使用的目录的路径。*

## 添加递归

到目前为止一切顺利！上面的脚本将目录的顶级条目记录到控制台，但是我的主题文件夹包含子目录，这些子目录也包含需要处理的文件。这意味着我们需要遍历条目数组，并让函数为它遇到的任何目录调用自己。

为此，我们首先需要确定我们是否在处理一个目录。幸运的是，文件系统模块对此也有一个方法: [lstatSync](https://nodejs.org/docs/latest/api/fs.html#fs_fs_lstatsync_path) 。这将返回一个 [fs。Stats](https://nodejs.org/docs/latest/api/fs.html#fs_class_fs_stats) 对象，它本身有一个`isDirectory`方法。该方法相应地返回`true`或`false`。

注意，这里我们使用的是同步版本的`lstat`。这对于一次性脚本来说很好，但是如果性能很重要的话，应该首选异步版本。

```
const fs = require('fs');

function buildTree(startPath) {
  fs.readdir(startPath, (err, entries) => {
    console.log(entries);
    entries.forEach((file) => {
      const path = `${startPath}/${file}`;

      if (fs.lstatSync(path).isDirectory()) {
        buildTree(path);
      }
    });
  });
}

buildTree('/home/jim/Desktop/theme'); 
```

如果您运行该脚本，您将会看到它打印了当前目录及其包含的每个子目录的文件和文件夹列表。成功！

## 识别要处理的文件

接下来，我们需要添加一些逻辑来识别任何 PHP 文件，打开它们并在它们中搜索我们正在寻找的字符串。这可以使用一个简单的正则表达式来检查以“.”结尾的文件名。如果条件满足，就调用一个`processFile`函数，将当前路径作为参数传递给它。

让我们对路径名的构造方式做一个小小的改进。到目前为止，我们一直使用字符串插值，但是由于正斜杠的原因，这只能在 Unix 环境下工作。然而，节点的路径模块提供了一个[连接方法](https://nodejs.org/api/path.html#path_path_join_paths)，它将分隔符考虑在内。

```
const fs = require('fs');
const Path = require('path');

function processFile(path) {
  console.log(path);
}

function buildTree(startPath) {
  fs.readdir(startPath, (err, entries) => {
    entries.forEach((file) => {
      const path = Path.join(startPath, file);

      if (fs.lstatSync(path).isDirectory()) {
        buildTree(path);
      } else if (file.match(/\.php$/)) {
        processFile(path);
      }
    });
  });
}

buildTree('/home/jim/Desktop/theme'); 
```

如果您在此时运行脚本，它应该递归一个目录树，并打印出它可能找到的任何 php 文件的路径。

## 在文件中搜索文本

剩下要做的就是打开脚本找到的文件并处理它们。这可以使用节点的 [readFileSync](https://nodejs.org/api/fs.html#fs_fs_readfilesync_file_options) 方法来完成，该方法接受文件路径及其编码(可选)作为参数。如果指定了编码，那么这个函数返回一个字符串。否则它返回一个缓冲区。

现在，我们可以将文件的内容读入一个变量，然后我们可以在每个换行符上分割这个变量，并遍历得到的数组。之后，使用 JavaScript 的 match 方法查找我们想要的单词或短语就很简单了:

```
function processFile(path) {
  const text = fs.readFileSync(path, 'utf8');
  text.split(/\r?\n/).forEach((line) => {
    if (line.match('http:\/\/')) {
      console.log(line.replace(/^\s+/, ''));
      console.log(`${path}\n`);
    }
  });
} 
```

如果你现在运行这个脚本，它会打印出找到匹配的每一行以及文件名。

## 更进一步

在我的特殊情况下，这就足够了。该脚本抛出了一些“http”的实例，我可以手动修复它们。任务完成。然而，使用`replace()`和 [fs.writeFileSync](https://nodejs.org/api/fs.html#fs_fs_writefilesync_file_data_options) 来改变每一个事件并将新内容写回到文件中，这将是一个简单的自动化过程。您还可以使用 [child_process.exec](https://nodejs.org/docs/latest/api/child_process.html#child_process_child_process_exec_command_options_callback) 在 Sublime 中打开文件以供编辑:

```
const exec = require('child_process').exec;
...
exec(`subl ${path}`) 
```

这种脚本适用于一大堆任务，而不仅仅是操作文本文件。例如，您可能想要批量重命名一堆音乐曲目，或者从目录中删除每个`Thumbs.db`文件。也许您想从远程 API 获取数据，解析 CSV 文件，或者动态生成文件。这份清单还在继续…

您还可以使 JavaScript 文件可执行，这样当您点击它们时它们就会运行。Axel Rauschmayer 通过 Node.js 在他的文章[中深入探讨了这个问题。](http://2ality.com/2011/12/nodejs-shell-scripting.html)

## 结论

现在我们有了。我演示了如何使用 JavaScript 遍历目录树并操作其中包含的文件子集。这是一个简单的例子，但是它强调了 JavaScript 可以用于浏览器之外的许多任务，桌面脚本就是其中之一。

现在轮到你了。你用 JavaScript 自动化脚本任务吗？如果没有，您是否有不同的首选语言，或者您是 bash 纯粹主义者？你自动化哪种任务？请在下面的评论中告诉我。

## 分享这篇文章