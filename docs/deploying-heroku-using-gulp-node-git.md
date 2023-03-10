# 使用 Gulp、Node 和 Git 部署到 Heroku

> 原文：<https://www.sitepoint.com/deploying-heroku-using-gulp-node-git/>

不管你是否使用过它，你可能在网上闲逛的时候听说过 Heroku。Heroku 允许您部署、运行和管理用 Ruby、Node.js、Java、Python、Clojure、Scala 和 PHP 编写的项目。这个平台给许多开发者带来了快乐，因为它惊人的[构建包](//github.com/heroku/heroku-buildpack-nodejs)创建了一个健壮的部署工作流。

![Heroku Deployment](img/1fe453d792d011fe98545e08d94a1dbe.png)

让我们来看看使用 Gulp、Git 和 Node 将项目部署到 Heroku 所需的文件。

## Heroku 术语

在我们开始之前，我们将确保定义一些术语。

Dyno

A lightweight Linux container that runs a single user-specified command. A dyno can run any command available in its default environment. Read more about Dyno types [here](//devcenter.heroku.com/articles/dynos#types-of-dynos).

Buildpacks

These lie behind the slug compilation process. They’re open source, enabling you to extend Heroku to other languages and frameworks. Buildpacks take your application, its dependencies, and the language runtime, and produce slugs.

Slug

A bundle of your source, fetched dependencies, the language runtime, and compiled/generated output of the build system – ready for execution.

## 文件要求

为了在 Heroku 上运行一个基于节点的项目，我们需要一些东西来开始。我们首先需要的是一个项目。出于我们的目的，我们将使用 Transformicons 开源项目作为本教程的示例。你也可以使用 Transformicons repo 和你自己的免费 Heroku 账号进行本文之外的练习！

### 制作简介

首先创建一个名为 [`Procfile`](https://devcenter.heroku.com/articles/procfile) 的空白文件，并将其放在项目的根目录下。`Procfile`是“进程文件”的简称，用于明确声明启动你的 app 应该执行什么命令。默认情况下，不需要`Procfile`，但是对于基于节点的项目来说，创建一个是很好的形式。

如果没有定义`Procfile`，那么将使用`npm start`作为 web 流程类型自动创建一个。下面是 Transformicons 的`Procfile`进程命令:

```
web: node node_modules/gulp/bin/gulp build
```

上面的代码行让我们可以使用 Node 的软件包管理器(NPM)提供的本地安装版本的 Gulp，而不必使用全局安装的版本。在这种情况下，该命令的`gulp build`部分将启动服务器，编译 Sass，连接/修改 JavaScript，替换资产调用，缓存半身文件名，用汇编程序编译模板，最后缩小我们编译的 HTML 文档！*这是我们尽情跳舞的部分*

### 创建生产服务器

对于那些使用 Gulp 服务资产的人来说，您可以在您的`gulpfile.js`中使用下面的任务为 Heroku 启动一个静态服务器，以便在浏览器中查看部署的项目。

```
gulp.task('serveprod', function() {
  connect.server({
    root: [your_project_path],
    port: process.env.PORT || 5000, // localhost:5000
    livereload: false
  });
});
```

如果需要的话，开发人员也可以选择使用类似于基于节点的服务器的东西，但是对于这个讨论，我们将使用 Gulp 来处理。

## 定义正确的依赖关系

Gulp 要求项目目录的根目录中存在一个`package.json`文件。在这个文件中，您必须注意定义的依赖项是否已安装。默认情况下，Heroku config 被设置为生产，并将从`dependencies`对象安装依赖项。

`devDependencies`对于`gulp clean`这样的任务，就像下面的情况一样，不会安装在生产环境中。使用下面的例子，注意我在`dependencies`下需要许多项目，这些项目是部署所必需的，比如缩小、连接以及任何通常有助于构建项目文件本身的东西。

```
{
  "dependencies": {
    "gulp": "^3.8.10",
    "gulp-autoprefixer": "^1.0.1",
    "gulp-concat": "^2.4.1",
    "gulp-connect": "^2.0.6",
    "gulp-extname": "^0.2.0",
    "gulp-load-plugins": "^0.7.0",
    "gulp-minify-html": "^0.1.8",
    "gulp-rev": "^2.0.1",
    "gulp-sass": "^1.2.2",
    "gulp-template": "^1.1.1",
    "gulp-uglify": "^1.0.1",
    "gulp-usemin": "^0.3.8",
    "gulp-util": "^3.0.1",
    "assemble": "git://github.com/assemble/assemble#v0.6.0",
    "lodash": "^2.4.1",
    "marked": "^0.3.2"
  },
  "devDependencies": {
    "gulp-clean": "^0.3.1"
  }
}
```

## 部署到 Heroku

现在您的项目已经准备好了部署到 Heroku 所需的文件，下载 [Heroku toolbelt](https://toolbelt.heroku.com) 并安装在您的机器上。Heroku 命令行客户端将安装到`/usr/local/heroku`，`/usr/local/heroku/bin`将添加到您的 [`PATH`](//alistapart.com/article/the-path-to-enlightenment) 。假设您已经有了 Heroku 的用户名和密码，首先从 shell 登录您的 Heroku 帐户。

```
heroku login
```

登录后，按照下面的顺序运行下面的命令(假设您的项目已经初始化了 git repo)。

```
cd [mysitedir]
heroku create
git push heroku master
heroku open
```

`heroku create`命令在 Heroku 上创建一个新的应用程序——以及一个必须用来接收应用程序源代码的 Git remote。在部署到 Heroku 之前，确保您的代码(尤其是远程存储库中没有的任何更改)被推送到 GitHub(或者 Bitbucket，如果您选择的话),否则在执行`git push heroku master`时，更改将不会被检测到。

如果您需要检查生产代码，您总是可以在 shell 中使用`foreman start`来测试在本地机器上部署的指令和功能。如果你想知道 Heroku 的部署日志是什么样的，请随意查看这个代码共享。

![Heroku GitHub](img/bcbd85c8b83f4a5af846e1fb5d5dfebd.png)

*GitHub 用户注意事项* : Heroku 被限制为每个用户、每个应用每小时 75 个 Git 请求的滚动窗口。一旦达到这个限制，为了保护 Git 服务，Git 请求会被拒绝几分钟，直到请求级别低于这个限制。

## 回首

让我们看看到目前为止发生了什么，以及为什么 Heroku 对于部署来说如此酷。最重要的是，我们已经阻止了生产代码进入我们的 GitHub repo，最终避免了将来编译后的代码可能会经常发生的合并冲突。

我们还设法纯粹出于部署目的连接、缩小和缓存了 bust 文件引用。这种方法防止我们在每个单独的文档中手工编写替换引用。所有这些任务都是由机器自动完成的！现在让我们去掉那个`myproject.herokuapp.com`域名。

## 自定义域

对于那些想要自定义域名的人，你应该知道，在撰写本文的时候，删除`www`是不可能的，所以如果你想要这样的行为，把来自非`www`引用的调用转移到`www`引用是一种很好的方式。

![Domain Heroku](img/b07b9f026ab0d1b9f76b8923a76811e9.png)

要创建自定义 CNAME，只需将`myproject.herokuapp.com`域添加到您的`CNAME`记录中，然后进行域名转发，如果您选择这样做的话。

## 附加注释

Heroku 用户应该花时间去理解 [buildpack-api 缓存](https://devcenter.heroku.com/articles/buildpack-api#caching)的解释，它也阐明了流程如何与 Heroku 项目一起工作。如果在任何时候你需要通过命令行获得一些“英雄式”的帮助，你总是可以输入`heroku help`来获得可用选项的列表。

关于 Gulp，有时您可能需要完全“比利·穆雷”,在部署时控制 Gulp 的流，确保一个进程在另一个进程开始之前完成。为了确保一个任务在另一个任务开始之前完成，给你的`src`行一个名为`stream`的`var`，并在你的任务管道完成后返回流。

这确保一个任务知道另一个任务何时完成，避免同时并行运行。这是一个看起来像什么的例子:

```
gulp.task('sass', function() {
  var stream = gulp.src(paths.sitesass + '/**/*.scss')
    .pipe($.sass())
    .pipe($.autoprefixer({
      browsers: ['last 2 versions'],
      cascade: false
    }))
    .pipe(gulp.dest(paths.sitecss))
    .pipe($.connect.reload());

  return stream; // return stream to notify us of completion
});

gulp.task('cssmin', ['sass'], function() {
  var stream = gulp.src(paths.sitecss + '/*.css')
    .pipe(minifyCSS({keepBreaks:true}))
    .pipe(gulp.dest(paths.sitecss));

  return stream; // return stream to notify us of completion
});
```

## 结论

高效的工作流和有助于轻松部署的工具对于任何开发人员来说都是必不可少的。不管工具是什么，尽可能做到最好，不要害怕打破习惯去探索未知。立即开始，增强您的工作流和部署方法。

### 进一步阅读

*   [部署节点:Heroku 开发中心](https://devcenter.heroku.com/articles/deploying-nodejs)
*   [Heroku 特性](https://www.heroku.com/features)
*   [吞咽式入门](//github.com/gulpjs/gulp/blob/master/docs/getting-started.md)
*   [SSH Git Transport w/Heroku](https://devcenter.heroku.com/articles/git#ssh-git-transport)
*   Heroku 上的&环境

## 分享这篇文章