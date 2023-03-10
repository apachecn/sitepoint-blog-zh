# Browserify 入门

> 原文：<https://www.sitepoint.com/getting-started-browserify/>

随着我们称之为网络的美丽怪兽每年都在进化，JavaScript 实现变得越来越复杂。我们中的许多人现在都在使用 JavaScript 模块——独立运行的组件组合在一起作为一个有凝聚力的整体工作，但可以愉快地替换任何组件，而不会导致世界末日。我们中的许多人一直在使用 AMD 模块模式，并要求巧妙地完成这一点。

去年，Browserify 一炮而红，引起了人们的极大兴趣。随着尘埃开始落定，我想写一篇关于 Browserify 是什么，它是如何工作的，以及将它添加到您的工作流程中的一些选项的概述。

## 什么是 Browserify？

Browserify 允许我们在浏览器中使用 node.js 样式的模块。我们定义依赖关系，然后 Browserify 将它们打包成一个整洁的 JavaScript 文件。您可以使用`require('./yourfancyJSfile.js')`语句包含所需的 JavaScript 文件，还可以从 npm 导入公开可用的模块。Browserify 为您生成源映射也很简单，这样您就可以单独调试每个 JS 文件，尽管事实上它们都是连在一起的。

## 为什么要导入节点模块？

导入模块是一件好事——不要访问一系列网站来下载 JavaScript 库，只需使用`require()`语句将它们包含进来，确保模块已经安装好，可以开始运行了。像 [jQuery](https://www.npmjs.org/package/jquery/) 、[下划线](https://www.npmjs.org/package/underscore)、[主干](https://www.npmjs.org/package/backbone)甚至 [Angular](https://www.npmjs.org/package/angular/) (作为非官方发行版)这样的常用 JavaScript 库都可以使用。如果你正在一个已经运行 node 的站点上工作，那么用一种通用的方法来构造你所有的 JS，你会进一步简化事情。我真的很喜欢这个概念。

## 你需要什么

要开始使用 Browserify，您至少需要:

*   [node.js](http://nodejs.org/)
*   [NPM](https://npmjs.org/)–默认情况下，它随 node 一起安装。
*   我会解释如何安装这个。
*   一个您准备驯服的 JavaScript 模块包！

## 入门指南

要开始，你需要在你的电脑上安装[节点](http://nodejs.org/)和 [npm](https://npmjs.org/) 。如果你正在寻找安装这些软件的指导，请点击上面的链接。如果你完全被卡住了，试试这些关于[通过包管理器](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager)安装 Node.js 的说明。使用 Browserify 不需要实际做任何节点工作。我们安装 node 只是因为 npm 运行它。获得 npm 后，可以使用以下命令安装 Browserify:

```
npm install -g browserify
```

我们在这里做的是使用 npm 在你的机器上全局安装 Browserify】告诉 npm 全局安装一个模块)。

如果您收到以下开头的错误:

```
Error: EACCES, mkdir '/usr/local/lib/node_modules/browserify'
```

那你就有权限问题了。你可以`sudo`这个命令，但是我建议你查看[这个](http://howtonode.org/introduction-to-npm)的帖子。

## 创建您的第一个浏览器文件

让我们首先创建一个浏览器化的 JavaScript 文件，它导入了一个非常流行的模块，[下划线](https://www.npmjs.org/package/underscore)。我们将使用下划线来追踪超人。我已经调用了我的 JS 文件`main.js`，并把它放在我的项目中的一个`js`文件夹中。

我们首先在 JavaScript 中使用 Browserify 的`require()`语句将`_`变量赋给下划线:

```
var _ = require('underscore');
```

接下来，我们将使用下划线中的`each()`和`find()`函数。我们将搜索两个名称数组，并运行一个`console.log`来判断它是否看到超人。莱克斯·卢瑟梦寐以求的高级产品。我们最终的 JavaScript 代码将如下所示:

```
var _ = require('underscore'),
  names = ['Bruce Wayne', 'Wally West', 'John Jones', 'Kyle Rayner', 'Arthur Curry', 'Clark Kent'],
  otherNames = ['Barry Allen', 'Hal Jordan', 'Kara Kent', 'Diana Prince', 'Ray Palmer', 'Oliver Queen'];

_.each([names, otherNames], function(nameGroup) {
  findSuperman(nameGroup);
});

function findSuperman(values) {
  _.find(values, function(name) {
    if (name === 'Clark Kent') {
      console.log('It\'s Superman!');
    } else {
      console.log('... No superman!');
    }
  });
}
```

我们希望确保 Browserify 在试图将 npm 模块添加到我们的项目中时能够找到它。这样做的基本步骤包括打开终端，导航到保存 JavaScript 项目的文件夹，然后运行以下命令在该文件夹中安装下划线:

```
npm install underscore
```

对于那些不熟悉 node 和 npm 如何工作的人来说，这将在您的项目中创建一个名为`node_modules`的文件夹，其中包含您的下划线模块的代码。该命令在`https://registry.npmjs.org/underscore`从 npm 存储库中检索最新版本的下划线。有了我们的`node_modules`文件夹中的那个模块，Browserify 现在可以找到并使用它了。

## 第一次运行浏览器功能

当我们运行 Browserify 时，它会用我们所有附加的模块构建一个新的 JavaScript 文件。在这种情况下，它将构建一个包含下划线的 JavaScript 文件。我们需要为这个新文件决定一个名字，我选了`findem.js`。我从项目的根文件夹中运行此命令:

```
browserify js/main.js -o js/findem.js -d
```

该命令读取您的`main.js`文件，并将其输出到由`-o`选项定义的`findem.js`文件中。我已经包含了`-d`选项，这样它也会为我们生成一个源地图，这样我们可以干净地调试`main.js`和`underscore`作为单独的文件。

## 使用浏览器确认输出

从这里开始，就像在您的页面上包含任何其他 JS 文件一样简单:

```
<script src="js/findem.js"></script>
```

## 导入您自己的 JavaScript 文件

不太可能所有应用程序都来自节点模块。要包含您自己的 JavaScript，您可以使用相同的`require()`函数。下面的 JavaScript 行将名为`your_module.js`的 JS 文件导入到`greatestModuleEver`变量中:

```
greatestModuleEver = require('./your_module.js');
```

要像这样导入我们的 JavaScript，我们只需要将 JavaScript 构造成一个模块。为此，我们必须定义`module.exports`。下面显示了一种方法。

```
module.exports = function(vars) {
  // Your code
}
```

#### 旁注！

如果您有一堆不在 npm 中的 JavaScript 库，并且您正在寻找一种更简单的方法将它们全部放入 Browserify，您可以使用 [Browserify-shim](https://github.com/thlorenz/browserify-shim) npm 模块来为您转换这些文件。我们不会在本文中使用它，但一些开发者可能会热衷于尝试一下。

### 我们的模块示例

为了给出一个简单的例子来说明这是如何工作的，我们将从前面的超级英雄搜索示例中取出数组，并用一个单独的 JS 模块替换它们，该模块返回一个姓名数组。该模块看起来像这样:

```
module.exports = function() {
  return ['Barry Allen', 'Hal Jordan', 'Kara Kent', 'Diana Prince', 'Ray Palmer', 'Oliver Queen', 'Bruce Wayne', 'Wally West', 'John Jones', 'Kyle Rayner', 'Arthur Curry', 'Clark Kent'];
}
```

接下来，我们将使用`names = require('./names.js')`将该模块导入到我们的代码中:

```
var _ = require('underscore'),
  names = require('./names.js');

findSuperman(names());

function findSuperman(values) {
  _.find(values, function(name) {
    if (name === 'Clark Kent') {
      console.log('It\'s Superman!');
    } else {
      console.log('... No superman!');
    }
  });
}
```

我们的`names`变量引用了从我们的模块导出的函数。因此，当我们将名称数组传递给我们的`findSuperman()`函数时，我们使用上面的`names`变量作为带括号的函数。

再次从命令行运行`browserify`命令来编译它，在浏览器中打开它，它应该像预期的那样运行，搜索数组中的每个值并记录它是否看到超人:

![Our console logs found Superman from our module](img/ec7d602ee20758d626120c23bfa91e22.png)

## 在我们的应用程序中传递变量和共享模块

为了给这个相当简单的超人狩猎应用程序增加一点复杂性，让我们把我们的`findSuperman()`函数变成一个模块。这样，理论上我们可以在 JavaScript 的不同部分找到超人，并且我们可以很容易地用一个更有效的模块替换我们的超人狩猎模块。

我们可以将变量传递给我们的模块，并在我们的`module.exports`函数中使用它们，所以我们将在一个名为`findsuperman.js`的文件中创建一个模块，该文件将被赋予一个名称数组:

```
module.exports = function (values) {
  var foundSuperman = false;

  _.find(values, function(name) {
    if (name === 'Clark Kent') {
      console.log('It\'s Superman!');
      foundSuperman = true;
    } else {
      console.log('... No superman!');
    }
  });

  return foundSuperman;
}
```

我为我们的`findSuperman()`函数添加了一个返回值。如果它找到超人，它将返回 true。否则，它将返回 false。由使用这个模块的代码来决定它使用这个真/假值做什么。然而，我们在上面的模块中遗漏了一件事。我们在函数中使用了下划线，但是没有声明它。我们可以在模块本身以及顶部声明它，如下所示:

```
var _ = require('underscore');

module.exports = function (values) {
  ...
```

当使用 Browserify 时，它会检查所有导入的 JS 文件，并且只导入提到过一次的每个模块。所以我们在主 JS 文件中需要下划线，在`findsuperman.js`中也需要，但是当 Browserify 把它打包时，它只把它放在最终的 JS 文件中一次。很漂亮吧？

我们实际的 JavaScript 应用程序现在将使用我们的新模块及其新返回的 true/false 值。出于演示目的，我们将坚持使用一个简单的`document.write`来说明它是否从我们的名字中找到了超人:

```
var _ = require('underscore'),
  names = require('./names.js'),
  findSuperman = require('./findsuperman.js');

if (findSuperman(names())) {
  document.write('We found Superman');
} else {
  document.write('No Superman...');
}
```

我们甚至不再需要在主 JS 文件中导入下划线，所以您可以毫不费力地删除它。它最终仍然会通过包含在`findsuperman.js`文件中而被导入。

## 使用`package.json`管理浏览器的 npm 依赖项

假设你有一个热心的朋友也想使用你的代码。期望他们知道他们需要首先安装 npm 下划线模块有点困难。解决方法是在项目的根目录下创建一个名为`package.json`的文件。这个文件为您的项目提供了一个名称(确保名称中没有空格)、描述、作者、版本，在我们的例子中最重要的是一个 npm 依赖项列表。对于那些使用 node 开发的人，我们在这里使用完全相同的东西:

```
{
  "name": "FindSuperman",
  "version": "0.0.1",
  "author": "Patrick Catanzariti",
  "description": "Code designed to find the elusive red blue blur",
  "dependencies": {
    "underscore": "1.6.x"
  },
  "devDependencies": {
    "browserify": "latest"
  }
}
```

依赖项列表目前仅限于我们的单个`"underscore": "1.6.x"`，其中依赖项的第一部分是名称，第二部分是版本。`latest`或`*`将检索 npm 拥有的最新版本。或者，您可以输入诸如`1.6`(对于 1.6 版)和`1.6.x`(对于 1.6.0 到 1.7 版，但不包括 1.7 版)之类的数字。

我们还可以将 browserify 本身作为一个依赖项，但是它不是项目运行的依赖项——我们应用程序的任何用户都可以找到超人，而无需运行 Browserify。这是我们的`devDependencies`模块之一，开发者需要它来更新这个应用程序。

现在我们已经有了一个`package.json`文件，我们不需要让我们的朋友运行`npm install underscore`。他们只需运行`npm install`，所有必需的依赖项将被安装到他们的`node_modules`文件夹中。

## 自动化浏览过程

每次更改文件时都在命令行中运行`browserify`很烦人，而且一点也不方便。幸运的是，有一些选项可以自动运行 Browserify。

### npm

npm 本身能够运行命令行脚本，就像您手动输入的那些脚本一样。为此，只需将一个`scripts`部分放入您的`package.json`中，如下所示:

```
"scripts": {
  "build-js": "browserify js/main.js > js/findem.js"
}
```

要运行它，您可以在命令行中键入以下内容:

```
npm run build-js
```

但是这不够方便。我们仍然需要每次手动运行该命令。真烦人。所以更好的选择是使用一个叫做 [watchify](https://www.npmjs.org/package/watchify) 的 npm 模块。Watchify 很简单，很容易，而且节省了大量时间。它会观察您的 JS 的变化，并自动重新运行 Browserify。

为了将它加入到我们的`package.json`中，我们将把它添加到我们的`devDependencies`中，并包含一个用于查看我们的 JS 的新脚本(当我们想要构建我们的 JS 而不需要改变文件时，将`build-js`留在那里)。

```
"devDependencies": {
  "browserify": "latest",
  "watchify": "latest"
},
"scripts": {
  "build-js": "browserify js/main.js > js/findem.js",
  "watch-js": "watchify js/main.js -o js/findem.js"
}
```

要运行它，只需输入以下命令。

```
npm run watch-js
```

它会运行并发挥它的魔力。它没有说太多让你知道发生了什么，这可能是令人困惑的。如果您更喜欢它向您提供它正在做什么的详细信息，可以像这样将`-v`添加到您的 watchify 命令中:

```
"watch-js": "watchify js/main.js -o js/findem.js -v"
```

它每次运行时都会给你这样的反馈:

```
121104 bytes written to js/findem.js (0.26 seconds)
121119 bytes written to js/findem.js (0.03 seconds)
```

#### 在 npm 中生成源地图

要使用 npm 生成源地图，请在您的`browserify`或`watchify`命令后添加`-d`:

```
"scripts": {
  "build-js": "browserify js/main.js > js/findem.js -d",
  "watch-js": "watchify js/main.js -o js/findem.js -d"
}
```

为了在`watchify`中既有用于调试的`-d`又有用于详细输出的`-v`,您可以像这样组合它们:

```
"watch-js": "watchify js/main.js -o js/findem.js -dv"
```

### 咕哝

很多人(包括我自己)已经使用了 [Grunt](http://gruntjs.com/) 有一段时间了，并且已经相当习惯了。幸运的是，对于这些类型，Browserify 也能很好地处理 Grunt 构建！

为了使用 grunt，我们需要修改我们的`package.json`文件。我们将不再使用`scripts`部分，取而代之的是依赖 Grunt。相反我们会增加几个新的`devDependencies`:

```
{
  "name": "FindSuperman",
  "version": "0.0.1",
  "author": "Patrick Catanzariti",
  "description": "Code designed to find the elusive red blue blur",
  "dependencies": {
    "underscore": "1.6.x"
  },
  "devDependencies": {
    "browserify": "latest",
    "grunt": "~0.4.0",
    "grunt-browserify": "latest",
    "grunt-contrib-watch": "latest"
  }
}
```

我们增加了依赖项:

*   grunt-确保我们已经为项目安装了 Grunt。
*   grunt-browserify-允许你在 Grunt 中运行 browser ify 的模块。
*   grunt-contrib-watch-该模块将监视我们的文件，并在文件发生变化时运行 Browserify。

然后，我们在项目的根目录下创建一个名为`gruntFile.js`的文件。在这个 Grunt 文件中，我们将有以下内容:

```
module.exports = function(grunt) {
  grunt.loadNpmTasks('grunt-contrib-watch');
  grunt.loadNpmTasks('grunt-browserify');

  grunt.registerTask('default', ['browserify', 'watch']);

  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    browserify: {
      main: {
        src: 'js/main.js',
        dest: 'js/findem.js'
      }
    },
    watch: {
      files: 'js/*',
      tasks: ['default']
    }
  });
}
```

我们从 Grunt 文件开始，加载我们在`package.json`文件中需要的 npm 模块:

```
grunt.loadNpmTasks('grunt-contrib-watch');
grunt.loadNpmTasks('grunt-browserify');
```

我们将我们希望运行的唯一一组任务注册为我们的`default`任务(`browserify`和`watch`):

```
grunt.registerTask('default', ['browserify', 'watch']);
```

我们设置了 Grunt `initConfig`对象(所有 Grunt 文件都寻找这个对象):

```
grunt.initConfig({
```

在其中，我们指出我们的`package.json`文件在哪里:

```
pkg: grunt.file.readJSON('package.json'),
```

接下来是我们的 Browserify 设置，它们基本上设置了我们的源 JS 文件的位置，以及我们希望构建到的浏览器验证代码和文件:

```
browserify: {
  main: {
    src: 'js/main.js',
    dest: 'js/findem.js'
  }
},
```

然后，我们设置了一个`watch`任务，以便在`js`文件夹中发生任何变化时重新运行我们的 Browserify 任务:

```
watch: {
    files: 'js/*',
    tasks: ['default']
}
```

因为我们的新`devDependencies`(我们的项目中没有安装 Grunt，也没有安装这两个模块)，我们需要先安装`npm install`。一旦你允许它运行并安装任何模块，你就可以运行非常简单的`grunt`命令让它开始监视你的项目。

#### 在 Grunt 中生成源地图

在`grunt-browserify`的 2.0.1 版本中，需要定义源地图的方式发生了变化，这导致了许多在线指南不正确！让 Grunt 和 Browserify 为您生成源地图的正确方法是将`debug: true`添加到`options`内的`bundleOptions`内，如下所示:

```
browserify: {
  main: {
    options: {
      bundleOptions: {
        debug: true
      }
    },
    src: 'js/main.js',
    dest: 'js/findem.js'
  }
},
```

这个看起来复杂的选项设置是为了以一种友好且容易兼容的方式包含未来的浏览选项。

### 吞咽

Gulp 是 Browserify 的小报情人。网络上的文章经常将 Browserify 和 Gulp 这两者结合起来——这是最先进的 JavaScript 构建过程。我不会说 Browserify 粉丝需要使用 Gulp，这主要是不同语法之间的个人偏好。你可以(正如你在上面看到的)非常愉快地使用 npm 或 Grunt 来构建你的 Browserify 文件。我个人是小型项目的干净简单的 npm 构建过程的粉丝。

为了在 Gulp 中完成以上工作，我们将从全局安装 Gulp 开始:

```
npm install -g gulp
```

我们将更新我们的`package.json`文件，以包含一些我们需要的新的`devDependencies`:

```
"devDependencies": {
  "browserify": "latest",
  "watchify": "latest",
  "gulp": "3.7.0",
  "vinyl-source-stream": "latest"
}
```

我们添加了以下内容:

*   [watch ify](https://www.npmjs.org/package/watchify)–我们在上面的 npm 示例中也使用了这个方法。同一个模块。
*   吞咽——这个相当重要的模块给了我们所有人吞咽的好处！
*   这是一个模块，它接收输入并返回一个文件给我们放在某个地方。

Browserify 的输出有一个流 API，我们可以直接使用它。一堆指南会建议使用`gulp-browserify`插件，然而 Browserify 不推荐这样做，更喜欢我们使用 Browserify 的流 API 输出。我们使用`vinyl-source-stream`来获取这个 Browserify 输出，并将其放入一个文件中，以便我们在某个地方输出。

然后，我们在项目的根目录下创建一个名为`gulpfile.js`的文件。这是所有吞咽功能的去处:

```
var browserify = require('browserify'),
    watchify = require('watchify'),
    gulp = require('gulp'),
    source = require('vinyl-source-stream'),
    sourceFile = './js/main.js',
    destFolder = './js/',
    destFile = 'findem.js';

gulp.task('browserify', function() {
  return browserify(sourceFile)
  .bundle()
  .pipe(source(destFile))
  .pipe(gulp.dest(destFolder));
});

gulp.task('watch', function() {
  var bundler = watchify(sourceFile);
  bundler.on('update', rebundle);

  function rebundle() {
    return bundler.bundle()
      .pipe(source(destFile))
      .pipe(gulp.dest(destFolder));
  }

  return rebundle();
});

gulp.task('default', ['browserify', 'watch']);
```

我们从导入 npm 模块开始，这是相当容易理解的。然后，我们为构建设置三个变量:

*   `sourceFile`–我们浏览过的文件的位置和文件名(在本例中为`js/main.js`
*   `destFolder`–我们将最终文件输出到的文件夹位置
*   `destFile`–我们希望最终文件使用的文件名

我将在下面更详细地解释这段代码。

#### Browserify 和 Gulp 如何协同工作

我们的第一个任务是我们的`browserify`任务，我们是这样定义的:

```
gulp.task('browserify', function() {
```

它首先将我们的`main.js`文件传递给 Browserify npm 模块:

```
return browserify(sourceFile)
```

然后，我们使用 Browserify 流 API 返回一个包含 JavaScript 内容的可读流:

```
.bundle()
```

从那里，我们用管道把它放到一个文件名为`findem.js`的文件中，然后通过管道把它放到我们的`js`文件夹中。

```
.pipe(source(destFile))
.pipe(gulp.dest(destFolder));
```

我们基本上把我们的输入通过不同的阶段，最终成为我们的最终项目，应该是一个闪亮的新 JavaScript 文件！

#### 结合 Watchify 和 Gulp

如前所述，直接使用 Browserify 有点烦人，因为当你更新文件时让它自动运行要容易得多。为此，我们再次使用`watchify` npm 模块。

我们首先设置一个名为`watch`的任务(如果你愿意，你也可以称之为`watchify`……这里真的由你决定):

```
gulp.task('watch', function() {
```

我们将 watchify 模块分配给`bundler`变量，因为我们将使用它两次:

```
var bundler = watchify(sourceFile);
```

然后我们添加一个事件处理程序，它在调用`update`事件时运行一个名为`rebundle()`的函数。基本上，每当 watchify 看到文件改变，它就会运行`rebundle()`:

```
bundler.on('update', rebundle);
```

那么什么是`rebundle()`？这几乎正是我们的`browserify`任务在上面所做的:

```
function rebundle() {
    return bundler.bundle()
      .pipe(source(destFile))
      .pipe(gulp.dest(destFolder));
  }

  return rebundle();
});
```

有可能在一些敏锐的 JavaScript 优化中将`browserify`和`watchify`合并在一起，但是为了简单起见，我决定在本文中将它们分开。关于这个令人印象深刻且更复杂的例子，请看[丹·泰洛的入门文档](https://github.com/greypants/gulp-starter)。

为了完成我们的`gulpfile.js`，我们定义了默认任务，其工作方式与 grunt 中的默认任务相同。

```
gulp.task('default', ['browserify', 'watch']);
```

要运行上面的 Gulp 代码，您有三种选择。最简单的方法是运行您创建的默认任务，在命令行中只需要一个词:

```
gulp
```

这将运行一次`browserify`任务，并且`watch`任务将开始观察文件的任何变化。

您还可以专门运行您的`browserify`任务:

```
gulp browserify
```

或者你的`watch`任务:

```
gulp watch
```

#### 使用 Gulp 和 Browserify 生成源地图

要为您的 JavaScript 生成一个源映射，请在两个`bundle()`函数中包含`{debug:true}`。

我们的任务应该是这样的:

```
gulp.task('browserify', function() {
  return browserify(sourceFile)
  .bundle({debug:true})
  .pipe(source(destFile))
  .pipe(gulp.dest(destFolder));
});
```

我们的`watch`任务中的`rebundle()`函数如下所示:

```
function rebundle() {
  return bundler.bundle({debug:true})
      .pipe(source(destFile))
      .pipe(gulp.dest(destFolder));
}
```

## 结论

对于 Browserify 来说，现在还为时尚早，它肯定会随着时间的推移而发展和成熟。在目前的状态下，它已经是构建模块化 JavaScript 的一个非常方便的工具，对于那些在后端使用 Node 的人来说尤其出色。当在项目的前端和后端都使用 npm 模块时，对于节点开发人员来说，代码变得更加清晰。如果您还没有尝试过 Browserify，请在您的下一个 JavaScript 项目中尝试一下，看看它是否会震撼您的世界。

## 其他资源

还有很多其他的浏览资源。你可能想看看一些方便的小东西:

*   Browserify 手册——詹姆斯·哈利迪关于 Browserify 入门的非常有价值的手册。绝对值得一读！
*   丹·泰洛的《万物贴》——一篇展示更高级用法的真正透彻的文章。
*   [就像咕哝和要求已经过时，现在一切都是关于大口大口地吃东西](http://www.100percentjs.com/just-like-grunt-gulp-browserify-now/)——马丁·吉纳夫用一个例子讲述了他突然转变为大口大口地吃东西的经历。
*   关于如何使用 Gulp 的更多信息，作者 Craig Buckler。

## 分享这篇文章