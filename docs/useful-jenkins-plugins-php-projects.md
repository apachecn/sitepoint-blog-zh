# PHP 项目中更有用的 Jenkins 插件

> 原文：<https://www.sitepoint.com/useful-jenkins-plugins-php-projects/>

在本系列的前几篇文章中，我们设置了 Jenkins 和我们的项目，并对最初的几个构建进行了分析。到目前为止，我们已经看到了关于项目质量的有趣结果。在这篇文章中，我们将看看更多的工具和插件，我们可以用来检查前端资产。

## CSSLint

到目前为止，我们只分析了我们的 PHP 代码。很有可能你的项目中也包含了一些使用 CSS 的 web 界面。我们可以通过使用 [CSS Lint](http://csslint.net/) 来分析我们的 CSS。

首先，我们需要在 Jenkins 服务器上安装 CSS Lint。因为我们已经安装了 NPM，我们只需运行下面的命令来安装 CSSLint。

```
sudo npm install -g csslint
```

一旦安装完毕，我们需要更新我们的`build.xml`文件并添加一个新的目标。您可以定义应该检查哪些部分，以及它应该是警告还是错误。关于可能规则的完整概述可以在这里找到[。](https://github.com/CSSLint/csslint/wiki/Rules)

```
<target name="csslint" description="Run the CSSLint tool on CSS files">
	<fileset dir="${basedir}/src" id="cssfiles.raw">
	  <include name="**/*.css" />
	</fileset>
	<pathconvert pathsep=" " property="cssfiles.clean" refid="cssfiles.raw" />
	<exec executable="csslint" output="${basedir}/build/logs/csslint.xml">
	  <arg line="--warnings=box-model,floats --errors=ids,important --format=lint-xml ${cssfiles.clean}" />
	</exec>
 </target>
```

不要忘记在“构建”目标中添加目标作为依赖项。你可以在这里看到我在[上的全部提交。](https://github.com/peternijssen/Jumph/commit/e9c7ac69c38be9a2d445f3bd8c432ad16eafd52e)

我们还需要改变詹金斯项目的配置。确保您转到项目的项目视图页面，并单击左侧菜单中的“配置”。如果你一直向下滚动，你会在某处看到一个名为“报告违规”的块。在这个表中，您会注意到一个名为“csslint”的记录。我们所要做的就是将报告文件添加到最后一个输入字段中。

您也可以配置前 3 个输入字段。这些输入字段告诉 Jenkins 它应该如何为这个给定的检查标记您的项目。例如，太阳下面的 10 表示如果您有 0 到 10 个问题，报告将包含一个太阳。如果你有 11 期或更多，它将是多云。通过这个，Jenkins 试图告诉你你的项目的状态是什么。当然最好有太阳图标。暴风雨图标标记最低值，表示项目状况不佳。黄色球甚至表明构建不稳定。这并不意味着构建失败了，但是你真的应该仔细看看你的项目。

![jenkins](img/a7b879a9a31aa12520bc7b85747a4318.png)

在您再次构建项目后，违规图形将显示它发现的 CSS Lint 问题的数量。

![jenkins](img/d3d78219a92275d2215f435fb69e0b9c.png)

如果您想知道问题的确切位置和问题是什么，您可以单击左侧菜单中的“违规”并滚动到 CSS Lint。在这里，您可以看到文件列表，通过单击某个文件，您可以看到有问题的确切行。

![jenkins](img/8c7f79c8893596b280122ffdaf5c99b6.png)

## Java Script 语言

除了 CSS，您可能还想验证您的 JavaScript。为此，我们可以使用 JSHint，因为它可以将文件返回给 Jenkins，这些文件可以被几个插件读取。

*JSHint 是 JSLint 的一个分支。两者都可以验证您的 JavaScript 文件，但是我更喜欢 JSHint 而不是 JSLint，因为它更主动维护，也更灵活*

我们首先在 Jenkins 服务器上安装 JSHint。

```
sudo npm install -g jshint
```

我们将再次通过添加一个 JSHint 目标来更改我们的`build.xml`文件。不要忘记改变你的建造目标。你可以在这里看到我对[的全部承诺。](https://github.com/peternijssen/Jumph/commit/92f0229bb1cd60e7ee1a5a4631f84cbc4759ad1d)

```
<target name="jshint" description="Run the JSHint tool on JavaScript files">
  <fileset dir="${basedir}/src" id="jsfiles.raw">
    <include name="**/*.js" />
  </fileset>
  <pathconvert pathsep=" " property="jsfiles.clean" refid="jsfiles.raw" />
  <exec executable="jshint" output="${basedir}/build/logs/jshint.xml">
    <arg line="--reporter=jslint ${jsfiles.clean}" />
  </exec>
 </target>
```

我们再次改变项目的配置，就像我们对 CSSLint 所做的那样。注意，我们将目标中的输出报告器设置为我们的`build.xml`文件中的`jslint`。这意味着它将输出一种与 JSLint 兼容的格式。我们的违规插件能够通过在 JSlint 后面的输入字段中填写报告文件来读取这种格式。

![jenkins](img/81b16af70b1da74fddf52b27e7418bb2.png)

与 CSSLint 一样，您将在违规图中看到 JSHint 的结果。在违规页面中，您可以看到问题的确切行及其详细信息。

![jenkins](img/e8b736d340365d07958e257f96eb98b3.png)

![jenkins](img/2bbe26dfe40c5924e183791355baa4c1.png)

## 未完成的任务

总有这样的情况，你不能马上完成一个特定的特性，或者它需要一些额外的改进。您可能会在代码中添加一个“@todo”来表示它仍然在 todo 列表中。通过我们将要安装的插件，你可以收集这些标签，快速浏览仍然打开的内容。

为此，你需要安装“任务扫描插件”。如果您不确定如何安装这个插件，请查看第一篇文章。安装后，回到你的项目视图，点击左侧菜单中的“配置”。

如果你一直向下滚动，你会看到一个选择框，上面写着“添加构建后操作”。点击它，并选择“扫描工作区中未完成的任务”

![jenkins](img/168ad45d2d185ae2638a18f8a79ce21f.png)

在所有其他配置块之间，会出现一个新的块来配置任务扫描器。首先，我们必须填写我们想要扫描的目录和文件。现在我只想扫描 src 目录中的 PHP 文件，所以我填写了`src/**/*.php`。接下来，我将选择哪些单词具有高、中、低优先级。我相信“FIXME”是高优先级，“TODO”是中等优先级，“DEPRECATED”是低优先级。最后，我正在配置，我想忽略字母大小写，这样我就可以确保所有的单词都被提取出来。我的配置现在看起来像这样。

![jenkins](img/61ee6e1b3b7c40eb47b0efa7364baae9.png)

单击底部的“保存”后，我们可以单击左侧菜单中的“立即构建”来开始分析。如果我们有几个构建，那么在 project view 页面上会出现一个图表，显示打开任务的总数。

![jenkins](img/690497cb16dbdbd1259745ab7866a754.png)

我们可以单击左侧菜单中的“打开的任务”来获取有关当前打开的所有任务的更多信息。该视图看起来非常类似于从 PHP CodeSniffer 和 PHP MD 等工具返回的结果。

![jenkins](img/dd19f964e15946d428b67d06101d45fb.png)

## 结论

你能和詹金斯做的事情数不胜数。但是，请注意，不同的工具正在做繁重的工作，Jenkins 只是将信息结合起来，创建漂亮的报告和可视化。

如果你认为我们已经使用了所有可能的工具，那你就错了。插件的列表是无穷无尽的，工具的列表更是如此。也许你对 PhpDocumentor 感兴趣，而不是 PhpDox。也许你正在与萨斯合作，你想 lint SCSS 文件。幸运的是，你有一个[工具](https://github.com/causes/scss-lint)。詹金斯还可以帮助你开发你的 [Android](https://wiki.jenkins-ci.org/display/JENKINS/Android+Emulator+Plugin) 应用程序和 [iOS](https://wiki.jenkins-ci.org/display/JENKINS/Xcode+Plugin) 应用程序。

你自己要用詹金斯吗？你已经在用了吗？你有没有什么想看的文章或者有什么想说的？我很乐意在下面的评论中听到你的意见。

## 分享这篇文章