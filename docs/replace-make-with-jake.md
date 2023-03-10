# 用杰克替换马克

> 原文：<https://www.sitepoint.com/replace-make-with-jake/>

所有创新的根源都是懒惰。对于 IT 领域来说尤其如此，在这个领域，我们受到流程自动化的驱动。部署是一个特别烦人的过程，因此需要自动化。部署还包括构建软件的关键步骤，即编译和修改源代码以获得运行的应用程序。一开始，人们使用一组脚本来执行相同的构建过程。一旦相同的脚本集必须被复制和再次使用，很明显必须创建一个公共系统。

软件 [Make](http://www.gnu.org/software/make/) 已经被证明是一个非常好的解决问题的方法。它很灵活，并且遵循一个清晰的概念，但是这种灵活性是有代价的。我们正在构建的许多伟大的软件创新无法与 Make 一起工作。我们没有任何扩展或包，可扩展的配置是不可能的。为了避免这些问题，生成 makefile、使用外部脚本或拥有多个 makefile 的模式非常常见。

我们不应该为了拥有一个工作构建系统而不得不退回到一个古老的工具链。我们应该采用我们非常了解的现代技术和软件堆栈。在本文中，我将向你介绍[杰克](http://jakejs.com/)。它将 Make 的概念和优点与 Node.js 环境相结合。这意味着我们可以使用任何我们喜欢的模块，脚本不仅是可能的，而且是被鼓励的。

## 专门化的任务运行器与通用的构建工具

使用 JavaScript 环境创建构建工具的想法并不新鲜。今天，每个前端开发人员都知道 Grunt 或 Gulp。而且在很多场景下，这些工具应该还是首选。所以问题是:我应该在哪里使用什么工具？

对于与 web 相关的任务，比如缩小 JavaScript 文件、添加 CSS 前缀或优化图像，任务运行器是首选。但是即使在这种情况下，也可以考虑使用 Jake，因为它是上述工具的超集。它不太专业，在这种情况下使用它也没什么不好。

考虑到这一点，如果您想要替换另一个构建工具(如 Make ),或者如果您有另一个遵循经典依赖规则方法的构建过程，那么 Jake 是一个更好的选择。通用构建工具的美妙之处在于它可以在许多上下文中使用。

在我们详细讨论 Jake 的优点之前，有必要看一下 Make 及其出色的概念。

## 看一看牌子

每个构建系统都需要三样东西:

1.  完成工作的工具(软件或功能)
2.  指定要做哪种工作的规则
3.  依赖项来指定要应用哪种规则

这项工作通常是将一个源文件转换成另一个文件。基本上，这种构建系统中的所有操作都是不可变的，这给了我们最大的灵活性和可预测性。

## 上等的

Node.js 生态系统具有许多增强用户终端体验的优秀模块。这对于构建工具来说尤其方便。由于遗留的(简单的)DOM 操作，JavaScript 是一种非常注重字符串的语言。这与 Unix 命令行哲学配合得非常好。但是 Jake 优于其竞争对手的另一个原因是:已经集成了测试和观察文件变化的特殊功能。

Jake 将规则依赖方法包装在一个名为 *tasks* 的层次结构中。这些任务可以并行运行，并将调用可用于控制流的事件，尽管存在并发性。任务可以分组，如规则、文件、目录、打包、发布、测试和观察。对于创建真正有用的构建过程来说，这些选项已经足够多了，这些构建过程高度灵活，并且完全符合我们的要求。最值得注意的是，监视任务使我们能够调用一些操作，比如一旦某些文件或目录发生更改，就运行构建过程。

像其他构建工具一样，Jake 使用一种特殊的文件来描述构建过程。这个文件被称为 Jakefile，并使用 **Jakefile.js** 作为它的默认名称。然而，也可以使用其他名字的简短列表，例如 **Jakefile** ，它们会被自动识别。也可以使用自定义文件名，但是在这种情况下，您必须明确指定所使用的文件名。

Jakefile 是一个包含所需模块、定义所有任务并设置一些规则的文件。为了将一些结构应用到我们的任务中，我们还可以使用一个叫做*名称空间*的特殊构造。在本文中我们不会深入讨论名称空间，但是这个概念本身可能有助于减少较大 Jakefiles 的潜在混乱。

## 编译应用程序的示例 makefile

在我们开始使用 Jakefile 示例之前，我们必须安装 Jake。如果您使用 [npm](https://www.npmjs.com/) ，安装会很简单，因为您只需输入以下命令:

```
npm install -g jake
```

我要解释的例子有点长，但它接近真实世界的代码，并说明了几个重要的概念。我们将通过浏览每个街区来浏览所有的线路。我们将假装编译一些 C++应用程序，但是这个例子不需要任何关于 C++的知识。

文件的第一行是:

```
var chalk = require('chalk');
```

这里我们包含了一个名为“chalk”的 Node.js 模块。粉笔是给终端输出着色的非常有用的工具，它应该是大多数 Jakefiles 的一部分。

如前所述，我们可以充分利用 Node.js 生态系统。因此，在下一节中，我们将指定一些常量，这些常量对于提高灵活性非常重要。如果我们使用 JavaScript，我们必须正确地使用它。

```
var sourceDirectory = 'src';
var outputDirectory = 'bin';
var objectDirectory = 'obj';
var includeDirectory = 'include';
var applicationName = 'example';
var isAsync = { async: true };
```

接下来的几行还定义了一些常量，但这次我们也允许外部参数覆盖我们自己的定义。我们不希望重写构建过程只是为了尝试另一个编译器，或者指定不同的标志。通过如下所示的`process.env`对象可以使用这些参数:

```
var cc = process.env.cc || 'g++';
var cflags = process.env.cflags || '-std=c++11';
var options = process.env.options || '-Wall';
var libs = process.env.libs || '-lm';
var defines = process.env.defines || '';
```

现在真正的交易开始了。我们使用`jake.FileList`构造函数来创建一个新的文件列表，其中包含所有带有*的文件。cpp* 目录中作为他们扩展名的所有源文件。然后这个列表被用来创建一个包含所有目标文件的相似文件列表。此时这些文件可能不存在，但这不是什么大问题。事实上，我们并不使用文件检索来指定目标文件的列表，而是将现有文件列表中的一些 JavaScript 映射表示为数组。实现该描述的代码如下所示:

```
var files = new jake.FileList();
files.include(sourceDirectory + '/*.cpp');
var target = outputDirectory + '/' + applicationName;
var objects = files.toArray().map(function(fileName) {
  return fileName
           .replace(sourceDirectory, objectDirectory)
           .replace('.cpp', '.o');
});
```

然后，一些方便的实用程序开始发挥作用。我们为输出定义函数，例如普通信息或警告:

```
var info = function(sender, message) {
  jake.logger.log(['[', chalk.green(sender), '] ', chalk.gray(message)].toMessage());
};

var warn = function(sender, message) {
  jake.logger.log(['[', chalk.red(sender), '] ', chalk.gray(message)].toMessage());
};
```

完成后，我们设置一个正则表达式来使用所有的对象文件。稍后，我们将使用它作为从源文件创建目标文件的规则的条件。我们还定义了一个函数，用于将正确的目标文件名转换回其对应的源文件名:

```
var condition = new RegExp('/' + objectDirectory + '/.+' + '\\.o$');
var sourceFileName = function(fileName) {
   var index = fileName.lastIndexOf('/');
   return sourceDirectory + fileName.substr(index).replace('.o', '.cpp');
};
```

我们已经掉进兔子洞了。现在我们需要定义两个函数，作为做一些实际工作的访问点:

*   将现有的目标文件链接在一起。它们在给定的场景中形成一个可执行文件
*   将源文件编译成目标文件

这两个函数使用提供的回调。回调将被传递给负责运行系统命令的`jake.exec`函数:

```
var link = function(target, objs, callback) {
   var cmd = [cc, cflags, '-o', target, objs, options, libs].join(' ');
   jake.exec(cmd, callback);
};

var compile = function(name, source, callback) {
   var cmd = [cc, cflags, '-c', '-I', includeDirectory, '-o',
              name, source, options, '-O2', defines].join(' ');
   jake.exec(cmd, callback);
};
```

在下一个片段中，揭示了 Jakefile 的两个关键部分:

1.  我们设置了一个转换规则来从源文件创建目标文件。我们使用前面定义的正则表达式和函数来获取所有请求的目标文件及其对应的源文件。此外，我们注释这个可以异步运行。因此，我们可以并行运行多个从源到目标的文件创建。在回调中，我们通过调用内置的`complete`方法来关闭规则
2.  我们定义了一个文件规则，它从多个依赖项中创建一个目标。该函数再次被标记为能够异步运行。使用`jake.mkdirP`方法，我们确保用于存储输出的目录确实存在，否则它将被创建。

有了这两种规则，我们就能够设置一些任务。任务是可以通过命令行从构建工具中访问的规则。

```
rule(condition, sourceFileName, isAsync, function() {
   jake.mkdirP(objectDirectory);
   var name = this.name;
   var source = this.source;
   compile(name, source, function() {
      info(cc, 'Compiled ' + chalk.magenta(source) + ' to ' +
           chalk.magenta(name) + '.');
      complete();
   });
});

file(target, objects, isAsync, function() {
   jake.mkdirP(outputDirectory);
   link(target, objects, function() {
      info(cc, 'Linked ' + chalk.magenta(target) + '.');
      complete();
   });
});
```

最后，我们设置了三个任务。一个用于创建文档，另一个用于编译应用程序，还有一个默认任务，当在命令行上不带任何参数地调用`jake`时执行该任务。默认任务有一个特殊的名字`default`，它依赖于另外两个已定义的任务。文档任务故意为空。它的存在只是为了说明多任务的概念。

```
desc('Creates the documentation');
task('doc', [], isAsync, function() {
   info('doc', 'Finished with nothing');
});

desc('Compiles the application');
task('compile', [target], isAsync, function() {
   info('compile', 'Finished with compilation');
});

desc('Compiles the application and creates the documentation');
task('default', ['compile', 'doc'], function() {
   info('default', 'Everything done!');
});
```

通过在终端上运行`jake compile`可以运行像`compile`这样的特殊任务。通过运行命令`jake -ls`显示所有定义的任务及其各自的描述。

## 结论

Jake 是一个强大的构建工具，应该安装在每台装有 Node.js 的计算机上。我们可以利用现有的 JavaScript 技能，以高效和轻量级的方式创建无缝的构建脚本。Jake 是独立于平台的，并且使用了一长串可能的构建工具中的最佳特性。此外，我们可以访问任何 Node.js 模块或其他软件。这包括解决创建前端构建过程问题的专门任务运行器。

## 分享这篇文章