# 构建并发布你自己的 Grunt 插件

> 原文：<https://www.sitepoint.com/build-and-publish-your-own-grunt-plugin/>

Grunt 是一个广泛流行的 JavaScript 任务运行器。它的架构基于插件，您可以组合和配置这些插件，为您的 web 应用程序创建一个强大的构建系统。Grunt 生态系统非常庞大，它提供了数百个插件来帮助你完成单调重复的任务，比如林挺、测试、缩小、图像处理等等。

我成功地构建并发布了我的 Grunt 插件，我渴望与你分享我在这个过程中获得的经验。我将向你展示如何构建你自己的小 Grunt 插件，并通过 [npm 包管理器](https://www.npmjs.com/)发布它。

我们将在本文中构建的插件将作为所谓的*印刷孤儿*——段落或块元素最后一行的单个单词——的补救措施，将最后一个空格替换为不可破坏的空格。这是一个非常简单的任务，但是在实现它的时候，我们将触及所有相关的主题，比如设置、最佳实践、配置、测试和发布。

![A typographic orphan in a text paragraph](img/2bb17717289528221ea2e21bbb57b0ab.png)

如果你想深入了解 Grunt 的机制，或者希望对现有的插件有所贡献，这篇文章就是为你准备的。在开始之前，我建议你花些时间看一下官方的[入门指南](http://gruntjs.com/getting-started)和艾蒂安·马格拉夫的文章，标题是[如何咕噜咕噜地实现工作流自动化](https://www.sitepoint.com/how-to-grunt-and-gulp-your-way-to-workflow-automation/)。

我们将在本文中构建的插件可以在 [GitHub](https://github.com/sitepoint-editors/grunt-typographic-adoption) 上获得。为了方便起见，我在存储库中添加了标记(名为`step01`–`step04`)。如果你想跟随手边的代码，只需检查相应的标签。例如，命令`git checkout tags/step02`反映了第 2 部分后代码*的状态。*

## 搭建你的游乐场

假设你已经在你的机器上安装了 [Node.js](https://docs.npmjs.com/getting-started/installing-node) ，我们可以立即开始设置我们的插件框架。幸运的是，Grunt 团队提供了一个叫做`grunt-init`的好工具来简化插件开发。我们将使用`npm`在全球范围内安装这个工具，并从 Git 中克隆 Grunt 插件模板:

```
npm install -g grunt-init
git clone git://github.com/gruntjs/grunt-init-gruntplugin.git .grunt-init/gruntplugin
```

现在我们准备为插件创建一个新目录，并运行命令`grunt-init`:

```
mkdir grunt-typographic-adoption
cd grunt-typographic-adoption
grunt-init gruntplugin
```

我们会被提示一些关于插件元数据的问题。在命名 grunt 插件时，记住 *grunt-contrib* 名称空间是为 Grunt 团队维护的任务保留的。所以，你的首要任务是找到一个尊重这一规则的有意义的名字。既然我们在处理印刷孤儿，我认为一个名字作为*grunt-typographic-adoption*可能是合适的。

如果在运行`grunt-init`之前，你将新的插件文件夹置于版本控制之下，并设置了 GitHub 的遥控器，那么你是幸运的。脚手架脚本将使用 Git 和 GitHub 提供的信息来填充许多您必须勾选的点。坚持 Grunt 和 Node.js 版本的默认值，除非您的一些依赖项需要一个特定的值。关于版本化你自己的插件，你应该熟悉[语义版本化](http://semver.org/)。我建议你看一下[项目搭建](http://gruntjs.com/project-scaffolding)的官方文档，在那里你可以读到更多关于`grunt-init`的其他可用模板和指定默认提示答案的方法。

现在，让我们看一下我们现在拥有的目录和文件结构:

```
.gitignore
.jshintrc
Gruntfile.js
LICENSE
README.md
package.json
- tasks
  | - typographic_adoption.js
- test
  | - expected
  | - custom_options
    | - default_options
  | - fixtures
    | - 123
    | - testing
  | - typographic_adoption_test.js
```

一旦你将你的插件置于版本控制之下,`.gitignore`文件就派上用场了(这是你应该做的操作，希望你已经执行过了！).`Gruntfile.js`指定了构建我们的插件需要做什么，幸运的是，它附带了一些预定义的任务，即 JavaScript 林挺(在`.jshintrc`中配置)和一个简单的测试套件(我们一会儿将详细检查相应的`test`文件夹)。`LICENSE`和`README.md`是不言自明的，预先填充了标准的内容，一旦你决定发布你的插件，它们就非常重要。

最后，`package.json`包含了关于我们插件的所有信息，包括它的所有依赖项。让我们安装并运行它:

```
npm install
grunt
```

如果一切进展顺利，我们将获得我们的`typographic_adoption`任务和输出`Done, without errors.`的奖励。给自己一个鼓励，因为我们有一个全功能的咕哝插件。它还没有做任何特别有用的事情，但是我们会实现的。整个魔术发生在`tasks/typographic_adoption.js`，在那里我们将实现我们的反寡妇代码。但是首先，我们要写一些测试。

## 测试驱动开发

首先实现测试总是一个好主意，从而指定您希望您的任务完成什么。我们将使测试再次通过，这给了我们一个很好的提示，即我们正确地实现了所有的东西。测试驱动的开发是惊人的，你的插件的用户会感谢你的！

那么我们想要完成什么呢？我已经告诉过你，我们想要解决印刷孤儿，即在一个段落或其他块元素的最后一行的单个单词。我们将通过扫描文件中的 HTML 块元素，提取内部文本，并用一个不可破坏的空格替换最后一个空格。

换句话说，我们用这个来填充插件:

```
<p>
  Lorem ipsum dolor sit amet, consetetur sadipscing elitr,
  sed diam nonumy eirmod tempor invidunt ut labore et dolore
  magna aliquyam erat, sed diam voluptua.
</p>
```

我们希望它能转变成这样:

```
<p>
  Lorem ipsum dolor sit amet, consetetur sadipscing elitr,
  sed diam nonumy eirmod tempor invidunt ut labore et dolore
  magna aliquyam erat, sed diam&nbsp;voluptua.
</p>
```

由于我们的插件框架附带了`nodeunit`测试任务，我们可以很容易地实现这种测试。

机制很简单:

1.  Grunt 对在`Gruntfile.js`中指定的所有文件执行我们的排版采用任务(最佳实践是将它们放入`test/fixtures`)。
2.  转换后的文件存储在`tmp`(`.gitignore`文件确保这个文件夹永远不会进入您的代码库)。
3.  `nodeunit`任务在`test`中寻找测试文件并找到`typographic_adoption_test.js`。该文件指定了任意数量的测试，即检查`tmp`中的文件是否与`test/expected`中的文件相同。
4.  在命令行上通知我们哪些测试失败了，或者整个测试套件是否通过了。

通常，您为每个配置构建一个测试，以确保您的任务可以处理所有类型的场景和边缘情况。让我们花些时间来考虑一下 Grunt 插件的可能配置。我们基本上希望用户能够配置我们的任务在哪些 HTML 元素中运行。默认选项可以是每一个包含文本的 HTML 块元素(`h1`、`p`、`blockquote`、`th`和许多其他元素)，而我们让用户自定义这个选项，设置任意的 CSS 选择器。这有助于扩大或缩小我们的任务范围。

现在是我们动手的时候了。首先，导航到`test/fixtures`，删除`123`文件，将`testing`编辑成一个简单的 HTML 文件，其中包含一些你想用来测试你的插件的块元素。我决定用一篇关于漫威的*黑寡妇*的短文，因为印刷孤儿有时也被称为寡妇。

现在，复制`test/fixtures/testing`的内容并用它覆盖`test/expected`中的两个文件。在你的插件处理完`testing`文件后，根据你的期望编辑它们。对于带有自定义选项的情况，我选择了用户只希望`<p>`元素被去形态化的场景。

最后，编辑`Gruntfile.js`,只针对您的`testing`文件(这意味着从`files`数组中移除`123`位),并在`test/typographic_adoption_test.js`中给你的测试一个有意义的描述。

真相大白的时刻已经到来。在项目的根目录下运行`grunt`:

```
grunt
...
Warning: 2/2 assertions failed
```

太棒了。我们所有的测试都失败了，让我们来解决这个问题。

## 执行任务

在我们开始实现之前，我们应该考虑我们可能需要的助手。因为我们希望在 HTML 文件中搜索某些元素并改变它们的文本部分，所以我们需要一个具有类似 jQuery 功能的 DOM 遍历引擎。我发现 [cheerio](https://github.com/cheeriojs/cheerio) 非常有用，而且很轻便，但是你可以随意使用任何你觉得舒服的东西。

让我们把 cheerio 作为一个依赖项:

```
npm install cheerio --save
```

这将把 cheerio 包安装到您的`node_modules`目录中，并且由于`--save`，它会保存在您的`package.json`中的`dependencies`下。剩下唯一要做的事情就是打开`tasks/typographic_adoption.js`并加载 cheerio 模块:

```
module.exports = function(grunt) {
  var cheerio = require('cheerio');
  ...
```

现在，让我们修正我们的可用选项。在这个阶段，用户只能配置一样东西:他们想要去孤立的元素。在`grunt.registerMultiTask`函数中查找`options`对象，并相应地更改它:

```
var options = this.options({
  selectors: 'h1.h2.h3.h4.h5.h6.p.blockquote.th.td.dt.dd.li'.split('.')
});
```

`options`对象为我们提供了插件用户放入他们的`Gruntfile.js`中的所有定制设置，还提供了设置默认选项的可能性。继续修改您自己的`Gruntfile.js`中的`custom_options`目标，以反映您在第 2 章中测试的任何内容。因为我只想处理段落，所以看起来像这样:

```
custom_options: {
  options: {
    selectors: ['p']
  },
  files: {
    'tmp/custom_options': ['test/fixtures/testing']
  }
}
```

请务必查阅 [Grunt API 文档](http://gruntjs.com/api/grunt)了解更多信息。

现在我们已经有了 cheerio 和我们的选项，我们可以继续实现插件的核心了。回到`tasks/typographic_adoption.js`，在您构建选项对象的那一行的正下方，用以下代码替换脚手架代码:

```
this.files.forEach(function(f) {
  var filepath = f.src, content, $;

  content = grunt.file.read(filepath);
  $ = cheerio.load(content, { decodeEntities: false });

  $(options.selectors.join(',')).each(function() {
    var text = $(this).html();
    text = text.replace(/ ([^ ]*)$/, ' $1');
    $(this).html(text);
  });

  grunt.file.write(f.dest, $.html());
  grunt.log.writeln('File "' + f.dest + '" created.');
});
```

我们正在循环我们在`Gruntfile.js`中指定的所有文件。我们为每个文件调用的函数用`grunt.file` API 加载文件的内容，将它送入 cheerio，并搜索我们在选项中选择的所有 HTML 元素。一旦找到，我们就用一个不可破坏的空格替换每个元素文本中的最后一个空格，并把它写回到一个临时文件中。我们的测试套件现在可以将这些临时文件与我们预期的文件进行比较，希望它能向您显示如下内容:

```
grunt
...
Running "nodeunit:tests" (nodeunit) task
Testing typographic_adoption_test.js..OK
>> 2 assertions passed (59ms)

Done, without errors.
```

厉害！我们刚刚实现了我们自己的小 Grunt 插件，它的工作就像一个魅力！

![A typographic orphan in a text paragraph](img/200c22b1352f73361599998ac0fe660a.png)

如果您愿意，您可以进一步改进、扩展和润色它，直到您对结果感到满意，并愿意与其他开发人员分享为止。

## 发布您的插件

发布我们的插件很容易，只需要几分钟。在我们发布代码之前，我们必须确保一切都设置正确。

让我们看一下`package.json`文件，npm 在注册表中使用的所有信息都保存在这个文件中。我们最初的模板已经注意到将`gruntplugin`添加到`keywords`列表中，这对于我们的插件作为 [Grunt 插件](http://gruntjs.com/plugins)被发现是必不可少的。这是花些时间添加更多关键字的时刻，这样人们就可以很容易地找到我们的插件。

我们还负责我们的`README.md`文件，并为我们未来的用户提供关于我们任务的一般用法、用例以及选项的文档。多亏了`grunt-init`,我们已经有了一份不错的初稿，可以在此基础上进行润色。

一旦这些准备工作完成，我们就可以发布我们的插件了。如果你还没有 npm 账户，[你可以在他们的网站](https://www.npmjs.com/)上创建一个，或者在命令行启动 npm 并在那里设置一切。以下命令将要求您输入用户名和密码，或者在 npm 上创建一个新用户并在`.npmrc`保存您的凭据，或者让您登录:

```
npm adduser
```

注册并登录后，您可以继续将您的插件上传到 npm:

```
npm publish
```

就是这样！所有需要的信息都从`package.json`文件中自动检索。[看看我们刚刚在这里创建的 Grunt 插件](https://www.npmjs.com/package/grunt-typographic-adoption)。

## 结论

感谢这篇教程，你已经学会了如何从头开始创建一个 Grunt 插件。此外，如果你已经发布了它，你现在就是一个 Grunt 插件的骄傲拥有者了，这个插件可以在网上获得，可以被其他 Web 开发者使用。坚持下去，继续维护你的插件，坚持测试驱动开发。

如果你正在构建一个 Grunt 插件或者已经构建了一个插件，并且想要分享一些关于这个过程的东西，请在下面的部分发表评论。我想再次强调，我们在本文中构建的插件可以在 [GitHub](https://github.com/sitepoint-editors/grunt-typographic-adoption) 上获得。

## 分享这篇文章