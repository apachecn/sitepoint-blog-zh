# 萨斯多克 2-闪亮的流光章鱼出来了！

> 原文：<https://www.sitepoint.com/sassdoc-2-shiny-streamy-octopus/>

几个月前，我公布了 SassDoc 的初稿，这是一个用于 Sass 的文档工具。从那时起，这是一条多么漫长的道路啊。几天前，我们终于发布了 SassDoc 的第二个主要版本，名为*闪亮流光章鱼*。我们已经为版本 2 工作了几个月，并花了几周时间进行测试，让有才华的人测试我们的产品，结果发现它足够好，可以发布了。耶！

SassDoc 的第二个版本背后有两个原因:第一个原因是清理代码库。我在 2014 年年中写了 SassDoc 的第一稿，在那之前我们一直在做我的一些代码。虽然代码本身并不差，但它的可伸缩性肯定不是很好，所以我们需要一个更加健壮的基础来应对未来。幸运的是， [Valérian](https://twitter.com/valeriangalliat) 、 [Fabrice](https://twitter.com/fweinb) 和 [Pascal](https://twitter.com/pascalduez) 是三个非常有才华的 JavaScript 开发人员，他们将我的旧的蹩脚代码[变成了一只巨大的野兽](https://www.youtube.com/watch?v=Mg8txvKXT4U)。

推出 SassDoc v2 的第二个原因，也是最重要的一个原因，是为我们构建新功能奠定坚实的基础。这样看来，这个版本并没有带来多少新的功能。它主要包括重构整个代码库、修复 bug、改进 API、摒弃过时的功能以及所有那些因为 API 中断而只能在主要版本上做的事情。

那么，如果我给你一个快速的游览，以及一些关于如何从 SassDoc v1 升级到闪亮的 Streamy Octopus 的提示，会怎么样呢？

## 新品牌

萨斯多克最初是作为一项实验开始的。最开始的时候，是我尝试 [Node.js](http://nodejs.org/) 的游乐场。很快，它不再是一个实验，实际的人正在实际的项目中使用它，如 [ThoughtBot](http://thoughtbot.com/) 为 [Neat](http://neat.bourbon.io/) 和 [Bourbon](http://bourbon.io/) 工作的人，以及 [The Guardian](http://www.theguardian.com/uk) 和 [Financial Times](http://www.ft.com/home/uk) 的前端团队。

所以我们决定给品牌这个新版本一些爱。我重新设计了[整个网站](http://sassdoc.com)以拥有*漂亮的*文档。我们让[瑞达·莱姆登](https://twitter.com/kaishin)为我们设计了一个标志，而[阿里克斯·卢卡斯](http://whyalix.com)创作了一幅插图，给萨斯多克一些视觉力量。

也就是说，我们很清楚不是每个人都会喜欢这个新品牌。这很公平。该站点只包含文档，默认主题可以定制。如果你真的不喜欢默认主题，我们已经设置好了一切，所以你可以在几分钟内建立自己的主题。

现在，如果你觉得你可以改善默认主题，我们会很乐意合并任何彻底的[拉请求](https://github.com/sassdoc/sassdoc-theme-default/pulls)，所以请放心去坚果和建议我们任何变化！

## API 中断

让我们从真实的事情开始:API 中断。令人惊讶的是，主要的 API 变化并没有我最初想象的那么多。根据您的项目，为了过渡到新版本，您可能只需要对 Sass 文件做很少的修改，甚至不需要做任何修改。

## 不再是 c 风格了

我认为在这一点上，我们为最终用户(也就是你)所做的最大改变是完全摒弃 C 风格的注释。你知道我以前是如何推销 SassDoc 的吗？我说 C 风格的注释(`/** ... */`)和内联注释(`///`)都受支持。现在不再是这样了。我们认为提供两种编写文档的方法是令人困惑的，并且最终没有多大帮助。此外，使用 C 风格注释的库作者会用大量的注释块污染用户的代码，这真的不是很酷。

为了帮助从 C 风格转移到内联注释，我们自己的 Valérian 写了一个小脚本。但是请注意！这是一个原始的查找和替换，不能 100%防弹(实际上这个脚本不能成功地将文件级注释从 C 风格转换为内联)。请务必仔细检查您的代码，以确保一切都是正确的。

对于 GNU sed:

```
find . -name '*.s[ac]ss' -exec sed -i 's,^/\*\*,///,;s,^  *\*\*/,////,;s,^  *\*/,///,;s,^  *\*,///,' {} +
```

对于 Mac/BSD sed:

```
find . -name '*.s[ac]ss' -exec sed -i ' 's,^/\*\*,///,;s,^  *\*\*/,////,;s,^  *\*/,///,;s,^  *\*,///,' {} +
```

Windows 用户，建议你试试 Git shell 中的 GNU 版本。

## 将默认值括起来

我们做的另一个重要的改变是在`@property`和`@parameter`注释(当然还有它们的别名)中使用括号(`[]`)而不是圆括号(`()`),不幸的是这不能通过简单的 shell 脚本自动修复。在那之前，您可以写类似这样的内容:

```
/// @param {String} $foo ('bar') - Baz
```

这个注释意味着文档项接受一个`$foo`参数，它的默认值是字符串`bar`。这已经不起作用了。从现在开始，你必须使用括号，把你的代码变成:

```
/// @param {String} $foo ['bar'] - Baz
```

这是一个有点艰难的选择，但是我们在使用括号作为包装器时遇到了一些解析问题。想想包含括号的默认值，比如函数调用(比如`length()`)。有时，我们的解析器会阻塞，无法获取正确的值。长话短说，我们决定走最安全的路，使用括号。

为了将这些括号转换成括号，Valérian(再次)编写了一个小脚本。同样，这是虚拟的搜索和替换，所以一定要仔细检查所有内容，特别是因为脚本会将`(length($list))`转换为`[length[$list]]`。我说过，不是防弹的。

对于 GNU 用户:

```
find . -type f -name '*.s[ac]ss' -exec sed -ri '/@param|@prop/y/()/[]/' {} +
```

对于 Mac/BSD 用户:

```
find . -type f -name '*.s[ac]ss' -exec sed -Ei ' '/@param|@prop/y/\(\)/\[\]/' {} +
```

Windows 用户，建议你试试 Git shell 中的 GNU 版本。

## 输出文件夹

几周前，有人来到 GitHub 上，告诉我们他已经删除了整个工作目录。废话。原来他没有阅读 wiki(我们当时没有专门的网站),在那里我们警告运行 SassDoc 时目标文件夹会被清除。为他辩护，这不完全是他的错:如果目标文件夹不是空的，我们没有提示或中止。我相信我们搞砸了。

不久后，我们推出了一些带有标志的东西，以此来强制/提示您。它工作得更好，它防止了任何事故，它使 API 变得更加复杂。所以我们采用了非常简单的方法，我丢弃了很长时间，因为我不太喜欢它:我们让 SassDoc 总是在当前路径输出它自己的目录(`./sassdoc`)，并带有一个选项(`--dest`)来更改这个位置。

所以，现在使用 SassDoc 变得简单多了。CLI API 仅要求源文件夹。目标文件夹可以用`--dest`设置，如果省略将默认为`./sassdoc`。

要记录一个特定的文件夹，比如`stylesheets/`，将其作为第一个参数传递。要更改目标文件夹的路径，如`docs/sass/`，只需使用`--dest`选项。

```
sassdoc stylesheets/ --dest ./docs/sass/
```

## 新功能

我说过这个版本本质上是 API 的一个重大重构，但是我们显然把它作为一个引入新的次要特性的机会。

## 更强大的 API

在前一节中，我们已经看到了如何调整 API 以使一切都变得更加健壮(尤其是关于输出文件夹)。您会很高兴地知道，我们还让您可以定义[排除模式](http://sassdoc.com/configuration/#exclude)，这样您就可以阻止 SassDoc 解析一些文件和/或文件夹。

例如，您可以让 SassDoc 解析您的样式表文件夹，但忽略供应商样式表。仅使用 CLI API，看起来像这样(引号很重要):

```
sassdoc stylesheets/ '!stylesheets/vendors/*'
```

现在，您可以使用配置文件以更持久的方式进行设置，如下所示:

```
exclude:
  - ./stylesheets/vendors/*
```

按照同样的思路，现在可以通过管道*将流*传输到 SassDoc。简而言之，现在可以在控制台中对文件运行 SassDoc。例如:

```
cat stylesheets/utils/_functions.scss | sassdoc -
```

它使得调试一个有文档记录的 Sass 文件变得很容易，特别是当它与将 SassDoc 数据结构打印为 JSON:

```
cat stylesheets/utils/_functions.scss | sassdoc --parse -
```

您甚至可以使用`curl`在不在您机器上的文件上运行它。例如，对于 GitHub 上托管的文件:

```
curl https://raw.githubusercontent.com/SassDoc/sassdoc-theme-default/master/scss/utils/_functions.scss | sassdoc --parse -
```

说到调试，我们引入了一个`--debug`选项来打印关于当前设置的信息，比如 SassDoc 版本、参数、启用的选项等等。在存储库上打开一个问题时特别有用:只需放上由`--debug`打印的跟踪。

```
» [DEBUG] argv: ["scss/","--debug"]
» [DEBUG] process.argv: ["node","/Users/admin/Documents/projects/sassdoc/sassdoc/bin/sassdoc","scss/","--debug"]
» [DEBUG] sassdoc version: 2.0.3
» [DEBUG] node version: 0.10.33
» [DEBUG] npm version: 2.1.9
» [DEBUG] platform: darwin
» [DEBUG] cwd: /Users/admin/Documents/projects/sassdoc/sassdoc-theme-default
» [DEBUG] env: {
  "strict": false,
  "file": ".sassdocrc",
  "dest": "sassdoc",
  "dir": "/Users/admin/Documents/projects/sassdoc/sassdoc-theme-default",
  "package": {},
  "themeName": "default",
  "src": [
    "scss/"
  ]
}
» [DEBUG] task: documentize
» [DEBUG] Dumping data to `sassdoc-data.json`.
```

## 更好的默认主题

我们也在默认主题中投入了大量的爱，尽可能地改进它。我们增加的一个重要功能是，你可以在生成的文档中使用谷歌分析或基本上任何其他跟踪系统。为此，我们在配置中引入了两个额外的键:`googleAnalytics`和`trackingCode`。

[`googleAnalytics`属性](http://sassdoc.com/customising-the-view/#google-analytics)只是一个映射到你的网站的谷歌分析跟踪键。主题将打印相关的 JavaScript 代码片段和您的密钥，这样您就可以获得 GA 跟踪。

```
googleAnalytics: UA-XXXXX-YY
```

同样地， [`trackingCode`选项](http://sassdoc.com/customising-the-view/#tracking-code)也有助于在你的文档上放置一些跟踪系统，除了它更宽松一点。基本上，它是一些在关闭`标签之前打印的 HTML。当然，要小心你放进去的东西。`

```
trackingCode: |
  <img src="http://piwik.example.org/piwik.php?idsite={$IDSITE}amp;rec=1" style="border:0" alt="" />
```

## 下一步是什么？

我们在发布第二版前几周就冻结了 SassDoc 的特性集，以便专注于重构一切，而不会有新的开发妨碍我们。现在闪亮的流章鱼出来了，我们可以回去实现新的功能，同时我们有很多建议。除其他外:

*   添加用一个由短到长的摘录( [#256](https://github.com/SassDoc/sassdoc/issues/256) )来介绍文档的能力。
*   增加了为同一个项目定义几个组以及嵌套组的能力( [#135](https://github.com/SassDoc/sassdoc/issues/135) )。
*   添加用`@name`注释( [#296](https://github.com/SassDoc/sassdoc/issues/296) )覆盖项目名称的功能。
*   增加了通过`@access`而不是主题中的组来排序项目的能力( [#239](https://github.com/SassDoc/sassdoc/issues/239) )。
*   如果物品以`_`或`-` ( [#340](https://github.com/SassDoc/sassdoc/issues/340) )开头，添加一个选项来假设该物品是私有的。
*   自动填充更多的东西，比如默认值( [#304](https://github.com/SassDoc/sassdoc/issues/304) )。
*   允许主题继承和子主题( [#272](https://github.com/SassDoc/sassdoc/issues/272) )。

我想在接下来的几周内我们会看到更多的建议。当然，我们非常重视反馈，所以如果你有什么要说的，请一定要说出来。

但是请允许我借这篇文章的机会声明，SassDoc 仍然没有计划成为一个全前端文档系统。那样的话， [SassDoc 就不能记录 CSS 选择器](http://sassdoc.com/frequently-asked-questions/#does-sassdoc-support-css-classes-and-ids-)了，我们也不打算在短期内增加这个特性(也许理解一下 *v3* )。

SassDoc 旨在记录 Sass API 和项目。我们正在做这件事，但我们真的很努力地想把它做好。我认为，在进入像 CSS 选择器这样的大型事物之前，我们在那个领域还有很多进展要做。

无论如何，一定要看看第二版的[变更日志和](https://github.com/SassDoc/sassdoc/blob/develop/CHANGELOG.md#200--shiny-streamy-octopus)[官方网站](http://sassdoc.com)来阅读文档。当然，我们可以在 Twitter 上回答你的任何问题；就 ping [@SassDoc_](https://twitter.com/sassdoc_) 或者我自己！` 

## `分享这篇文章`