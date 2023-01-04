# 使用 GNU Make 作为前端开发构建工具

> 原文：<https://www.sitepoint.com/using-gnu-make-front-end-development-build-tool/>

CSS 预处理程序(如 Sass)和任务运行程序(如 Grunt)的流行使得构建过程成为前端开发的一部分。在任务运行者/构建工具领域，不缺乏选择或意见，最受欢迎的是[咕哝](http://gruntjs.com/)和[吞咽](http://gulpjs.com/)。

我在工作和个人项目中写了相当多的 JavaScript，但是我避免使用 Grunt 或 Gulp，除非绝对必要。我更喜欢 GNU Make T1，原因有很多:

*   它释放了 Unix shell 的力量；STDIN 和 STDOUT 的功能非常丰富。
*   它已经在我使用的所有环境中可用。
*   它要么有我需要的所有工具，要么允许在没有不必要的包装模块的情况下访问它们，这些包装模块将我绑定到我选择的构建工具的任何可用版本。

我不是唯一一个认为你可能不需要所有那些花哨的构建工具的人。然而，我确实认为缺少的是作为前端开发构建工具的实用介绍。虽然我鼓励你使用 RTFM，但我不会指着一本 70 年代写的 186 页的软件手册说“去吧！”。

因此，让我们看看 Make，看看它如何高效地构建我们的 CSS 和 JavaScript 资产。

## 构建工具与任务运行者

当我完全厌倦了通过声明性 JSON 编程，并在 Makefile 中重新实现资产编译时，我带来了命名任务的概念——如“设置”、“css”和“资产”等名称。我的 Makefile 目标看起来像这样:

```
setup:
    bower install
    mkdir -p htdocs/assets/css
    mkdir -p htdocs/assets/js
```

我带来了任务执行者的心态。正确的用法是将目标与先决条件配对，允许 Make 仅在源文件发生更改时构建新文件。

## Makefile 规则基础

为了最大限度地发挥 Make 的潜力，您需要将您想要的结果恰当地框定为构建目标、它们的先决条件(依赖项)以及将这些依赖项转化为预期输出的方法。即:

```
htdocs/robots.txt: support-files/robots.txt
    cp support-files/robots.txt htdocs/robots.txt
```

这是一个有意简化的例子，但是它包含了 Makefile 规则的核心结构。在第一行我们有**目标** ( `htdocs/robots.txt`)。冒号之后是目标的**先决条件**。当 make 解析这个 Makefile 时，它会读取这个规则，并将其解释为“如果自目标生成以来，源已被更改，则重新生成目标”。为了重新生成目标，它执行`target: source`对下的制表符缩进行，称为*配方* (Makefile 配方必须是制表符缩进的，而不是空格缩进的)。

上面这个简单的例子似乎有点…嗯，真的很可怕。复制一个文件需要大量的打字工作。Make 通过提供在配方中使用的自动变量对此进行了改进。我鼓励你阅读所有可用的[自动变量](http://www.gnu.org/software/make/manual/make.html#Automatic-Variables)，但我最常用的是:

*   `$@`–目标文件名
*   `$ – the filename of the first prerequisite`
*   `$?`–所有先决条件的空格分隔列表

使用自动变量，我们前面的例子变成:

```
htdocs/robots.txt: support-files/robots.txt
    cp $< $@
```

假设我们在`support-files`上加了一个`humans.txt`，并希望将其发布给`htdocs`。我们不想复制和粘贴前面的规则，但是我们可以通过在目标和先决条件中使用**词干**来改进它:

```
htdocs/%.txt: support-files/%.txt
    cp $< $@
```

现在我们有进展了。上面的将会跨任何文本文件复制，但是当源文件改变时**只有**。如果您要连续运行它两次，在第二次运行时，它将退出而不复制任何文件。

## 编译 Sass

让我们把我们所学的内容变得有用。假设我们有以下目录结构:

```
|_ assets
|  |__ css
|  |__ js
|
|_ htdocs
|  |__ css
|  |__ js
```

我们的源文件位于`assets`中，编译后的结果由 http 服务器从`htdocs`目录中提供。接下来是使用 SassC 从 Sass 源生成 CSS 的 Make 规则:

```
htdocs/css/%.css: assets/css/%.scss
    sassc -t compressed -o $@ $?
```

只有一个先决条件的单一命令配方。它指示 Make:“如果源 Sass 文件比构建目标文件新，运行这个`sassc`命令，将目标(`$@`)作为输出(`-o`选项)，将先决条件作为源”。

如果您运行这个，您将看到命令被打印到终端，以及来自`sassc`二进制文件的任何输出。这很快就会变得嘈杂，因此我们可以通过在命令前面加上`@`来抑制命令响应行为。让我们更上一层楼，让这个食谱更有趣:

```
htdocs/css/%.css: assets/css/%.scss
    @echo Compiling $@
	@mkdir -p $(@D)
    @sassc -t compressed -o $@ $?
    @node_modules/.bin/autoprefixer $@
```

上面正在执行这些命令:

*   回显目标文件名，以便我们可以看到正在编译的内容
*   如果目标文件的目录不存在(包括子目录！).这引入了一个新的自动变量——`$(@D)`——目标文件的目录。
*   从 Sass 源代码编译 CSS
*   使用 autoprefixer 将任何必需的供应商前缀添加到我们的 CSS 中，这样我们就不必将它们放在我们的 Sass 中。

我们将使用同样的方法来缩小我们的 JavaScript。如果你正在使用 CoffeeScript 或 ES6 JavaScript，你也可以转换它们。

```
htdocs/js/%.js: assets/js/%.js
    @echo Processing $@
	@mkdir -p $(@D)
    @node_modules/.bin/jsmin --level 2 --output $@ $?
```

我在 JS 和 CSS 构建过程中都使用了 Node，因为这是前端开发的最佳工具。但这并不意味着我需要将这些节点模块包装在 Grunt 或 Gulp 包装器中，并在另一个具有自己语法的节点程序中运行它们。

## 多重先决条件

我参与的每个 Sass 项目都在一个文件中定义了变量，这些变量需要导入到其他 Sass 文件中。因此，我们应该在 Sass 编译示例中考虑全局依赖性。

```
htdocs/css/%.css: assets/css/%.scss assets/css/_settings.scss
    @echo Compiling $@
	@mkdir -p $(@D)
    @sassc -t compressed -o $@ $<
    @node_modules/.bin/autoprefixer $@
```

需要注意的变化是，我们现在已经将`_settings.scss`添加到先决条件列表中，并且我们已经将对`sassc`的调用更改为使用第一个先决条件文件名(`$`)作为其输入，而不是使用整个先决条件部分。现在，如果带有匹配词干(`%`通配符)*或*`_settings.scss`文件的 Sass 文件发生变化，目标 CSS 文件将被重新编译。

因为第一个先决条件中的 stem 可以匹配子目录，所以我们需要确保子目录存在于我们的`htdocs/css`目录中。我们使用`mkdir`和`$(@D)`自动变量来完成这项工作，该变量包含目标路径的目录部分。你不必使用`$(@D)`，用`$(shell basedir $@)`调用 shell 也会得到相同的结果；自动变量只是一种便利。

## 变量

重复太多了，我不喜欢。如果我们改变项目的目录结构，我们将不得不去改变那些目录名的每一个实例。让我们通过用变量替换不同的目录来清理它:

```
SASS_SRC := assets/css
JS_SRC   := assets/js
CSS_DIR  := htdocs/css
JS_DIR   := htdocs/js
BIN      := node_modules/bin

$(CSS_DIR)/%.css: $(SASS_SRC)/%.scss $(SASS_SRC)/_settings.scss
    @echo Compiling $@
    @mkdir -p $(@D)
    @sassc -t compressed -o $@ $<
    @$(BIN)/autoprefixer $@

$(JS_DIR)/%.js: $(JS_SRC)/%.js
    @echo Processing $@
    @mkdir -p $(@D)
    @$(BIN)/jsmin --level 2 --output $@ $?
```

这里的两个新概念是使用`:=`操作符设置变量，以及在 Make 规则中通过将变量放在括号中并以`$`作为前缀来使用变量。你不必像我一样排列变量赋值，那只是个人偏好。

## 猛击你的指尖

我担心给人留下这样的印象:您的 Make 规则应该调用您已经安装的其他程序来完成实际工作。为了演示，让我们使用核心 GNU 实用程序，在获取我们的网页所使用的 JavaScript 库时，为我们的用户和服务器节省多个 HTTP 请求。

```
JS_LIB_FILE := $(JS_DIR)/libs.js
JS_LIBS := bower_components/jquery/dist/jquery.min.js \
    bower_components/lodash/dist/lodash.min.js \
    bower_components/fooqux/dist/fooqux.min.js

$(JS_LIB_FILE): $(JS_LIBS)
    cat $? > $@
```

结果是，如果任何一个源代码库发生变化(比如 Bower 提供了新的版本)，那么`libs.js`文件也会更新。不错！

作为最后一个例子，这里有一些更复杂的东西，仍然使用标准的实用程序—生成所有文件名和这些文件的校验和的清单。我们在[很快地使用了这种变体](http://swiftly.com/)和其他 [99designs](http://99designs.com/) 产品来为我们的服务器提供它需要的数据，以在每个文件的基础上写破坏缓存的资产链接(类似于 Ruby 的链轮)。

```
DIST := htdocs/assets

# if any compiled assets changes, regenerate the manifest
$(DIST)/.manifest: $(shell find $(DIST) -type f -name '*.css' -or -name '*.js')
    find $(DIST) -type f -exec cksum {} \; | sed -e "s#$(DIST)/##" | cut -f1,3 -d" " > $@
```

这个食谱一开始有点令人费解，但是记住你只需要在命令提示符下运行`man find`来了解关于`find`的所有信息，或者用`man sed`来设置，或者用`man cut`来切割。这三个部分中的每一部分都可以单独执行以了解更多信息，因为它们都使用 STDIN 和 STDOUT。

## 总结

我可以理解一些已经熟悉 JavaScript 的开发人员更喜欢 JavaScript 构建工具，坚持认为他们不想学习“另一件事”。正如我希望在这里演示的那样，对于我们的目的来说，Make 只不过是一个目标/先决条件/配方语法，为我们填充了一些方便的变量，并且可以直接访问 shell 脚本来执行我们需要的任何处理。

每个 web 开发人员都应该习惯于在 bash 中执行基本的文件任务，所以如果您一直犹豫不决，那么这可能是一个好机会，可以在正确的方向上给自己一点动力。

## 分享这篇文章