# 使用 Jade 和 Grunt 加速 HTML 制作

> 原文：<https://www.sitepoint.com/using-jade-and-grunt-to-speed-up-html-production/>

每当我们谈论构建 web 应用程序时，我们都需要谈论语言预处理程序(将用特定语言编写的源代码转换为用本地语言编写的目标代码)。您经常会发现自己使用的语言不是 web 浏览器本身所支持的，因为它们通过提供普通 HTML(例如 Jade)、CSS(例如 Sass)和 JavaScript(例如 CoffeeScript)中没有的功能来帮助您摆脱重复的工作。

HTML [模板引擎](http://en.wikipedia.org/wiki/Comparison_of_web_template_engines)是一个预处理器，它让我们能够按照应用程序设计的( <abbr title="Don't Repeat Yourself">DRY</abbr> )原则编写动态的、可重用的 HTML 文档。

[Jade](http://jade-lang.com) 是 Node.js(服务器端 JavaScript)的开源 HTML 模板语言。Jade 是完全免费的，它提供了快速、简单和有趣的 HTML。它深受 [Haml](http://haml.info/) 的影响，最初是作为一个模块在 Node 中创建的。它通常包括大多数高级编程语言共有的特性，重点是处理纯文本。

当使用像 Bootstrap 和 Foundation 这样的现代 CSS 框架时，大部分开发完全变成了制作 HTML，这就带来了 Jade 的强大功能。一旦你开始使用它，它将成为你不可或缺的工具。

在本教程中，我将介绍如何结合 [Grunt](http://gruntjs.com/) 来加速 HTML 的制作并注入具有 Jade 特性的普通数据。

本教程的工具集将包括:

*   翡翠
*   咕哝
*   NodeJS

## 准备环境:安装

下面是安装 Node.js、Jade 和 Grunt 命令行界面(CLI)的步骤。如果您已经安装了这些，您可以跳到本教程的下一部分。

1.  进入[nodejs.org](http://nodejs.org)，点击“安装”按钮下载最新版本的 node。执行下载的文件并遵循安装说明。

2.  npm(节点包管理器)将与 Node 捆绑在一起，可以通过终端使用。打开你的终端，在项目目录下执行`npm init`。然后按照说明创建`package.json`。

3.  要安装 Grunt，请输入`npm install --save-dev grunt`
4.  安装 Grunt CLI(命令行界面)直接从命令行执行任务。
5.  要安装 Jade 任务的 Grunt 插件，输入`npm install --save-dev grunt-contrib-jade`
6.  要安装 Jade，请键入`npm install --save-dev jade`

**注意:**:安装 npm 软件包有三个标志:

1.  `--save`表示这些包是项目本地所需的，并在依赖属性下列出。
2.  在本地添加软件包，但作为开发依赖项。它们在`package.json`中的 devDependencies 下列出。
3.  `-g`表示应该全局安装软件包；这允许您在终端中执行 grunt，而不管当前的工作目录。

将会创建一个`package.json` manifest 文件，它表示项目所依赖的包的列表，并且包含一些元数据，比如项目名称、版本和描述。

安装完成后，您的`package.json`清单将如下所示:

```
{
  "name": "jadeTutorial",
  "version": "1.0.0",
  "description": "Jade with Grunt tutorial",
  "main": "index.js",
  "author": "Nouran Mahmoud",
  "license": "ISC",
  "devDependencies": {
    "grunt": "^0.4.5",
    "grunt-contrib-jade": "^0.14.1",
    "jade": "^1.9.2",
  }
}
```

此外，还将创建一个`node_modules`目录，其中将包含之前安装的 npm 模块`grunt`和`jade`。

## 什么是咕噜声？

我将向您介绍 Grunt，一个流行的任务运行器，我们将使用它在您的开发工作流中自动执行耗时的命令行任务。任务是一个动作，它产生一个目标，就像编译 Jade 生成 HTML 一样。使用 Grunt，我们将能够只通过一个命令行运行 Jade、观察任务和执行任何其他任务。如果你以前没听说过任务跑者，那也没关系；这不是一个难以理解的概念。

Grunt 并不是这项工作的最佳工具。如果你对使用其他构建工具感到好奇，你可以查看这些资源:[如何使用 npm 作为构建工具](http://blog.keithcirkel.co.uk/how-to-use-npm-as-a-build-tool/)或[gulp . js 简介](https://www.sitepoint.com/introduction-gulp-js/)。

### 创建 Gruntfile.js

Grunt 使用`gruntfile.js`来加载任何任务，并使用您需要的任何参数对其进行配置，我将一路向您展示。

Grunt 任务是通过将一个对象传递给`grunt.initConfig`方法，由任务定义受影响的文件，并传递一些其他选项来配置的。了解更多关于 Grunt 的信息。

`grunt.registerTask`方法定义了一个当我们通过命令行运行`grunt build`时将被执行的任务。数组`[jade, watch]`表示为完成构建过程而分别运行的任务。

首先，这是最少的`Gruntfile.js`代码:

```
module.exports = function (grunt) {
  grunt.initConfig({});
  grunt.registerTask('default', []);
};
```

### 设置咕哝玉任务

我们将创建基本的 Gruntfile，并将以下代码放入其中:

```
module.exports = function(grunt) {
  // Project configuration.
  grunt.initConfig({
    jade: {
      compile: {
        options: {
          pretty: true,
        },
        files: {
          'build/index.html': 'app/views/jobs.jade'
        }
      }
    }
  });
  // These plugins provide necessary tasks.
  grunt.loadNpmTasks('grunt-contrib-jade');
  // Default task.
  grunt.registerTask('build', 'Convert Jade templates into html templates', ['jade']);
};
```

设置一个 grunt 任务需要通过`npm`注册表获取插件并加载插件包，插件包通过`grunt.loadNpmTasks`方法包含任务，因此您可以配置并执行它们。

前面的任务配置将单独编译每个 Jade 文件，并将结果放在带有单个`index.html`文件的构建目录中。

### 设置普通监视任务

手表任务是最有用的插件之一。这将观察您的更改，重新构建并立即自动进行，而不需要每次我们做出更改时都运行`jade`任务。

要添加**观察**任务，使用以下命令安装插件:

```
npm install --save-dev grunt-contrib-watch
```

然后将其配置添加到`Gruntfile.js`:

```
watch: {
  grunt: { files: ['Gruntfile.js'] },
  jade: {
    files: 'app/views/**/*.jade',
    tasks: ['jade']
  }
}
```

这将为 Jade 文件和 Grunt 文件创建一个“监视”任务。要完成这个任务，您还需要加载 Grunt 任务并注册该任务:

```
grunt.registerTask('default','Convert Jade templates into html templates',
                  ['jade','watch']);
grunt.loadNpmTasks('grunt-contrib-watch');
```

现在，当您运行`grunt`命令时，它将完成以下任务:

1.  编玉
2.  运行`watch`任务来观察 Jade 文件的变化，一旦发生变化就自动重新编译。

该进程将一直处于活动状态，直到被`CTRL+C`终止

**注意:**`grunt`命令是`grunt default`的快捷方式/别名。它定义了当我们运行`grunt`命令时，要执行什么任务——在我们的例子中是`jade`和`watch`。

### 脚手架 gruntfile.js 生成器

您可以使用 [`grunt-init-gruntfile`](https://github.com/gruntjs/grunt-init-gruntfile) 脚手架命令创建一个基本的 Gruntfile。在本教程中，我们不需要它的所有默认任务，所以我们不会使用它。

## 项目结构

我们将创建源文件夹层次结构，如下所示:

```
app
  └──views
     | └── layout.jade
     | └── jobs.jade
     | └── jobs-container.jade
     └──── partials
            |
            └─ head.jade
            └─ footer.jade
```

执行`grunt jade`命令，将为您创建以下结构。

```
build
  └── index.html
```

## 一个简单的玉石例子

Jade 是基于缩进和空白的，因为每一级缩进都会在 HTML 中创建一级嵌套元素。举例来说，下面的代码展示了使用 Jade 生成 HTML 文档的基本结构。

```
doctype html
html
  head
    title Jade Tutorial
  body
    p.className#idName Hello SitePoint
    | Readers!
    img(src="images/image.png")
```

它编译成:

```
<!DOCTYPE html>
<html>
  <head>
    <title> Jade Tutorial </title>
  </head>
  <body>
    <p class="className" id="idName"> Hello SitePoint Readers! </p>
    <img src="images/image.png">
  </body>
</html>
```

使用 Jade，您不必编写结束标记，这有助于避免潜在的错误，例如忘记添加结束标记或丢失它们的正确顺序。行首的任何文本都被解释为标签。您需要为每个标签使用适当的缩进。

**注意:**如果标记名被省略，将创建一个`div`作为默认标记。

要向标签添加属性，请将它们放在标签名称后面的括号内，并遵循`name=value`格式。若要分隔多个属性，请使用逗号。此外，类和 id 可以分别用`.`和`#`符号编写，就像前面的例子一样。

### 使用管道字符(`|`)

有时我们需要在一个新的行上写一个标签的原始内容，但是，正如我前面提到的，*行首的任何文本都被认为是一个标签*。为了避免这种情况，在行首使用竖线或`|`字符，并将标签的内部文本写在新行上。

**注意:**为了查看您通过编译 Jade 文件所做的更改，您需要基于我们之前创建的 Grunt 任务运行`grunt`。

## 强大的玉石功能，带有咕噜声

在上一节中，我向您简要介绍了 Jade 的语法。在接下来的几节中，我们将介绍一些其他常见且强大的 Jade 特性，以及一些更高级的特性。我们将通过一个简单的“职位空缺页面”示例来实现这一点。你可以在这个 GitHub repo 的[源代码文件](https://uploads.sitepoint.com/wp-content/uploads/2015/08/1439871509JadeAndGruntTut-master.zip)或[中找到每种情况的完整例子。开始吧！](https://github.com/NouranMahmoud/JadeAndGruntTut)

## 阻断和延伸(玉的传承)

名为`layout.jade`的文件将是我们页面布局的基本结构，这是一种枯燥的概念，因为您不需要为每个页面编写这些标签。相反，你需要`extend`布局，并开始编写`block content`特定代码，正如我们将看到的。

块是一种分隔或组织布局的东西。然后可以在另一个文件中覆盖它。

**注意:**为了简洁和集中，去掉了 CSS 和 JavaScript 代码。你可以在[回购](https://github.com/NouranMahmoud/JadeAndGruntTut)中找到这些。此外，我使用了`copy` Grunt 任务将`js`和`style`目录从`app`目录转移到`build`目录。

下面是`layout.jade`文件:

```
doctype html
html(lang="en" dir="ltr")
  block head
    include partials/head.jade

  body
    block content
    block footer
      include partials/footer.jade
```

可以根据需要创建`footer.jade`和`head.jade`。对于我们的页面，我们将使用 Foundation 和 jQuery 来帮助我们建立一些基本的设计和功能。

下面是`partials/head.jade`文件:

```
head
  meta(name="viewport", content="width=device-width, initial-scale=1.0")
  meta(charset="UTF-8") 
  title Jobs
  link(rel = "stylesheet" href = "style/foundation.min.css")
  link(rel = "stylesheet" href = "style/app.css")
```

下面是`partials/footer.jade`文件:

```
div#footer.row
  div.small-12.large-12.columns
    p Copyright (c) foobar
script(src="js/jquery.min.js")
script(src="js/foundation.min.js")
script(src="js/app.js")
```

在下一段代码中，我们将创建`jobs.jade`，它通过使用`extends`关键字继承我们的布局，然后覆盖我们需要的块，从而使用所有其他文件。在这个例子中，我们覆盖了`content block`。

`jobs.jade`:

```
extends layout.jade
block content
  div#container.row
    div.small-12.large-12.columns
      include job-container.jade
```

但是如果我们不需要覆盖整个块呢？如果我们只需要添加内容呢？我们以`block head`为例。我们需要在页眉中为这个页面添加一个特殊的脚本，所以我们将在`block`后使用`append`或`prepend`关键字。

```
// append head can be used without block 'append head'
block append head 
  script.
    alert('Welcome to SitePoint')
```

## 使用迭代和混合

在上一节中，我们包括了`job-container.jade`。在这一节中，我们将看看循环和混合的好处，以及如何在 Jade 中使用它们。

以下是新代码片段的初始版本:

```
- each job in jobs
  div.sub-container.row
    div.title.small-12.large-12.columns 
      p #{job.title}  
    div.description.display.small-12.large-12.columns
      div.row
        div.block.small-12.large-12.columns
          p Skills and Requirements
          ul
            - each skill in job.skills
              li #{skill}
        div.block.small-12.large-12.columns
          p Preferred Skills
          ul
            - each pskill in job.pskills
              li  #{pskill}
      button.apply.button(value="apply") Apply
```

如你所见，我们使用了多个普通的循环语句，就像第一行`- each job in jobs`中的语句一样。`-`字符用于不直接添加任何输出的无缓冲代码。

在前面的代码片段中，使用 mixins 会使冗余代码变得更加枯燥，如下面的代码所示:

```
- each job in jobs
  div.sub-container.row
    div.title.small-12.large-12.columns 
      p #{job.title}   
    div.description.display.small-12.large-12.columns
      div.row
        +skillsBlock("Skills and Requirements", job.skills)
        +skillsBlock("Preferred Skills", job.pskills)
      button.apply.button(value="apply") Apply
mixin skillsBlock(title, loopItems)
  div.block.small-12.large-12.columns
    p= title
    ul
      - each skill in loopItems
        li  #{skill}
```

这里我们使用`mixin`特性创建一个可重复使用的玉块。Mixins 就像方法(或函数),它们有名称，并在其内部代码中接受参数进行评估。在上面的例子中，我们创建了一个名为`skillsBlock()`的 mixin。

要使用这个`mixin`，我们只需写下它的名称并传递适当的参数值，在 mixin 名称前加上一个`+`符号来标识它是一个 mixin 调用。

**注意:**和上面的代码一样，我们可以通过`=`、`!=`或者`#{}`来赋值。但是使用`!=`时要小心，它不做任何转义，所以对于用户输入来说是不安全的。

## 通过 Grunt 传递 JSON 数据

在建立了我们的环境之后，让我们通过 Grunt 任务将 JSON 数据从一个 JSON 文件传递给 Jade，从而将 Grunt 和 Jade 结合起来做一些了不起的事情。

首先，创建 JSON 文件 [data.json](https://github.com/NouranMahmoud/JadeAndGruntTut/blob/master/data.json) ，并用您想要的数据填充它。

然后打开`Gruntfile.js`，将`data`属性添加到`Jade`任务的选项中，如下所示:

```
jade: {
  compile: {
    options: {
      pretty: true,
      data: {
        jobs: grunt.file.readJSON('data.json')
      }
    },
    files: {
      'build/index.html': 'app/views/jobs.jade'
    }
  }
}
```

`jobs`数据/局部变量(传递给 Jade 模板的数据称为局部变量)属性将被传递给在`files`属性中标识的 Jade 文件，并且可以在 Jade 代码中使用。这种方式使您能够轻松地向网页添加文本内容。

最后，如果您查看演示，您可以看到页面的外观[。当然，源代码是经过编译的，所以在这种情况下演示并不重要。截图如下。](http://nouranmahmoud.github.io/SitePointJadeTutDemo/)

[![Jade tutorial result](img/fa37658a953c35cb92ac4eae0d386253.png)](http://nouranmahmoud.github.io/SitePointJadeTutDemo/)

## 包扎

使用模板引擎的原因可能各不相同，但它们可以归类为更高的生产率、更少的重复和更好的语法。此外，您可能不希望用本地语言编写代码，因为它太重复、容易出错，或者您只是不喜欢它。这就是像 Jade 这样的语言发挥作用的地方，这种语言旨在保持代码简洁明了。

Jade 帮助您编写更干净的代码，这使您的工作不那么单调乏味。我们已经介绍了使用 Grunt 的一些基础知识、技巧和诀窍。关于 Jade，还有更多我们在本教程中没有涉及到的内容，所以你可以查看官方文档了解更多。

## 分享这篇文章