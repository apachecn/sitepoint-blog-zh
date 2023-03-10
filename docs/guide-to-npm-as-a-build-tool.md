# 把格朗特踢出去！使用 npm 作为构建工具的指南

> 原文：<https://www.sitepoint.com/guide-to-npm-as-a-build-tool/>

前端构建和工作流工具大量可用:[咕噜](http://gruntjs.com/)、[大口](http://gulpjs.com/)、[花椰菜](https://github.com/broccolijs/broccoli)和[杰克](https://github.com/jakejs/jake)等等。这些工具几乎可以自动化您在项目中反复做的任何事情，从缩小和连接源文件，到运行测试或编译代码。但问题是，你需要它们吗？您真的想在项目中引入额外的依赖项吗？答案是“不会！”。有一个免费的替代品可以为您完成这些任务中的大多数，它与 [Node.js](https://nodejs.org/) 捆绑在一起。当然我说的是 [npm](https://www.npmjs.com/) 。

在本文中，我们将讨论作为一个构建工具， [npm](https://www.npmjs.com/) 能够做些什么。如果你想在开始前快速入门 npm，请参考我们的[NPM 初学者指南](https://www.sitepoint.com/beginners-guide-node-package-manager/)。如果你想继续下去，你可以在 GitHub 上找到本文使用的代码[。](https://github.com/sitepoint-editors/buildtool)

## npm 脚本

为了开始我们的讨论，我们将为我们的新演示项目创建一个目录，我们称之为“buildtool”。完成后，我们将进入这个文件夹，然后运行命令`npm init`来创建一个`package.json`文件:

```
$ mkdir ~/buildtool && cd ~/buildtool
$ npm init
```

你会被问几个问题。您可以随意跳过全部或部分内容，因为您将用以下内容替换`package.json`文件的最终内容:

```
{
  "name": "buildtool",
  "version": "1.0.0",
  "description": "npm as a build tool",
  "dependencies": {},
  "devDependencies": {},
  "scripts": {
    "info": "echo 'npm as a build tool'"
  },
  "author": "SitePoint",
  "license": "ISC"
}
```

如你所见，我们有一个带有名为`info`的`property`的`scripts`对象。`info`的值将作为命令在 shell 中执行。通过运行以下命令，我们可以看到项目中定义的`scripts`属性(也称为*命令*)和值的列表:

```
$ npm run
```

如果您在我们的项目文件夹中运行前面的命令，您应该会看到以下结果:

```
Scripts available in buildtool via `npm run-script`:
  info
    echo 'npm as a build tool'
```

如果要运行特定的属性，可以运行以下命令:

```
$ npm run <property>
```

因此，要运行我们在`package.json`文件中定义的`info`命令，我们必须编写:

```
$ npm run info
```

它将产生以下输出:

```
$ npm run info
> buildtool@1.0.0 info /home/sitepoint/buildtool
> echo 'npm as a build tool'

npm as a build tool
```

如果你只想要`info`的输出，你可以使用`-s`标志，它使[停止 npm](https://docs.npmjs.com/misc/config#default-configs) 的输出:

```
$ npm run info -s
npm as a build tool
```

到目前为止我们只使用了一个简单的`echo`，但是这是一个非常强大的特性。命令行上的一切都是可用的，我们在这里可以非常有创造力。因此，让我们在到目前为止所介绍的基础上，安装一些`packages`来创建一些通用的工作流。

## 通用工作流程

我们希望实现的第一件事是 JavaScript 文件的林挺功能。这包括运行一个程序来分析我们的代码中潜在的错误。为此，我们将使用 [JSHint](http://jshint.com/) ，所以第一步是通过 npm 安装包:

```
$ npm install jshint --save-dev
```

执行这个命令后，您会看到一个名为`node_modules`的新子文件夹。这是 JSHint 被下载的地方。此外，我们还需要为我们的项目创建以下文件夹结构:

```
├── assets
│   ├── css
│   │   └── main.css
│   └── scripts
│       └── main.js
├── dist
├── package.json
├── node_modules
└── test
    └── test.js
```

在 Unix 系统上，这可以通过以下命令完成:

```
$ mkdir -p assets/css assets/scripts test && touch assets/css/main.css assets/scripts/main.js test/test.js
```

## 林挺

现在我们将在`main.js`文件中强制一些语法错误。此时文件是空的，所以打开它并粘贴以下内容:

```
"use strict";

var Author = new function(name){
  this.name = name || "Anonymous";
  this.articles = new Array();
}

Author.prototype.writeArticle = function(title){
  this.articles.push(title);
};

Author.prototype.listArticles = function(){
  return this.name + " has written: " + this.articles.join(", ");
};

exports.Author = Author;

var peter = new Author("Peter");
peter.writeArticle("A Beginners Guide to npm");
peter.writeArticle("Using npm as a build tool");
peter.listArticles();
```

希望这段代码的意图很清楚——我们声明了一个构造函数，它的目的是创建新的`Author`对象。我们还为`Author`的`prototype`属性附加了几个方法，这将允许我们存储和列出作者写的文章。注意`exports`语句，它将使我们的代码在定义它的模块之外可用。如果你有兴趣了解更多这方面的内容，一定要阅读:[了解 module.exports 和 Node.js 中的 exports](https://www.sitepoint.com/understanding-module-exports-exports-node-js/)。

接下来，我们必须向`package.json`中的`scripts`对象添加一个`property`，它将触发`jshint`。为此，我们将创建一个`lint`属性，如下所示:

```
"scripts": {
  "info": "echo 'npm as a build tool'",
  "lint": "echo '=> linting' && jshint assets/scripts/*.js"
}
```

在这里，我们利用`&&`操作符将命令和文件组(星号)链接起来，这些命令和文件组被视为通配符，在这种情况下，匹配在`script`目录中以`.js`结尾的任何文件。

**注意**:Windows 命令行不支持 globs，但是当给定一个命令行参数，比如`*.js`，Windows 会将它一字不差地传递给调用应用程序。这意味着供应商可以安装兼容性库，为 Windows 提供类似 glob 的功能。JSHint 为此使用了 [minimatch](https://www.npmjs.com/package/minimatch) 库。

现在让我们解开代码:

```
npm run lint -s
```

这会产生以下输出:

```
=> linting
assets/scripts/main.js: line 1, col 1, Use the function form of "use strict".
assets/scripts/main.js: line 5, col 28, The array literal notation [] is preferable.
assets/scripts/main.js: line 3, col 14, Weird construction. Is 'new' necessary?
assets/scripts/main.js: line 6, col 1, Missing '()' invoking a constructor.
assets/scripts/main.js: line 6, col 2, Missing semicolon.
assets/scripts/main.js: line 16, col 1, 'exports' is not defined.

6 errors 
```

它工作了。让我们清除这些错误，重新运行 linter 以确保正确，然后继续进行一些测试:

```
(function(){
  "use strict";

  var Author = function(name){
    this.name = name || "Anonymous";
    this.articles = [];
  };

  Author.prototype.writeArticle = function(title){
    this.articles.push(title);
  };

  Author.prototype.listArticles = function(){
    return this.name + " has written: " + this.articles.join(", ");
  };

  exports.Author = Author;

  var peter = new Author("Peter");
  peter.writeArticle("A Beginners Guide to npm");
  peter.writeArticle("Using npm as a build tool");
  peter.listArticles();
})();
```

注意我们是如何将所有东西包装在一个[立即调用的函数表达式](https://www.sitepoint.com/premium/books/javascript-novice-to-ninja/)中的。

```
npm run lint -s
=> linting
```

没有错误。我们很好！

## 测试

首先我们需要安装[摩卡包](https://www.npmjs.com/package/mocha)。Mocha 是一个简单而灵活的用于 Node.js 和浏览器的 JavaScript 测试框架。如果您想了解更多，这篇文章是一个很好的起点:[用 Mocha 进行基本前端测试&柴](http://callmenick.com/post/basic-front-end-testing-with-mocha-chai)

```
npm install mocha --save-dev
```

接下来，我们将创建一些简单的测试来测试我们之前编写的方法。打开`test.js`并添加以下内容(注意`require`语句，它使我们的代码对 mocha 可用):

```
var assert = require("assert");
var Author = require("../assets/scripts/main.js").Author;

describe("Author", function(){
  describe("constructor", function(){
    it("should have a default name", function(){
      var author = new Author();
      assert.equal("Anonymous", author.name);
    });
  });

  describe("#writeArticle", function(){
    it("should store articles", function(){
      var author = new Author();
      assert.equal(0, author.articles.length);
      author.writeArticle("test article");
      assert.equal(1, author.articles.length);
    });
  });

  describe("#listArticles", function(){
    it("should list articles", function(){
      var author = new Author("Jim");
      author.writeArticle("a great article");
      assert.equal("Jim has written: a great article", author.listArticles());
    });
  });
});
```

现在让我们给`package.json`添加一个`test`任务:

```
"scripts": {
  "info": "echo 'npm as a build tool'",
  "lint": "echo '=> linting' && jshint assets/scripts/*.js",
  "test": "echo '=> testing' && mocha test/"
} 
```

npm 有几个方便快捷的方式，分别是`npm test`、`npm start`和`npm stop`。这些都是它们的`run`对等物的别名，这意味着我们只需要运行`npm test`就可以让摩卡行动起来:

```
$ npm test -s
=> testing

  Author
    constructor
      ✓ should have a default name
    #writeArticle
      ✓ should store articles
    #listArticles
      ✓ should list articles

  3 passing (5ms)
```

## 前后挂钩

如果我们要运行我们的测试套件，它会因为一个语法错误而立即退出，这将不是非常有效的。幸运的是，npm 给了我们`pre`和`post`钩子，所以如果你运行`npm run test`，它会在结束时首先执行`npm run pretest`和`npm run posttest`。在这种情况下，我们希望在运行`test`脚本之前运行`lint`脚本。下面的`pretest`脚本使这成为可能。

```
"scripts": {
  "info": "echo 'npm as a build tool'",
  "lint": "echo '=> linting' && jshint assets/scripts/*.js",
  "test": "echo '=> testing' && mocha test/",
  "pretest": "npm run lint -s"
}
```

想象一下，我们之前没有纠正脚本中的语法错误。在这种情况下，上面的`pretest`脚本将失败，并显示非零退出代码，并且`test`脚本将不会运行。这正是我们想要的行为。

```
$ npm test -s
=> linting
assets/scripts/main.js: line 1, col 1, Use the function form of "use strict".
...
6 errors
```

使用`main.js`中的修正代码:

```
=> linting
=> testing

  Author
    constructor
      ✓ should have a default name
    #writeArticle
      ✓ should store articles
    #listArticles
      ✓ should list articles

  3 passing (6ms)
```

我们是绿色的！

## 代码精简

对于这一部分，我们需要在项目中添加一个`dist`目录，以及几个子目录和文件。这是文件夹结构的外观:

```
 ├── dist
   │   └── public
   │       ├── css
   │       ├── index.html
   │       └── js
```

在 Unix 机器上重新创建它的命令是:

```
mkdir -p dist/public/css dist/public/js && touch dist/public/index.html
```

`index.html`的内容很简单。

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>npm as a build tool</title>
    <link href='css/main.min.css' rel='stylesheet'>
  </head>
  <body>
    <h2>npm as a build tool</h2>
    <script src='js/main.min.js'></script>
  </body>
</html>
```

目前`main.js`未缩小。这是理所应当的，因为这是我们正在处理的文件，我们需要能够阅读它。然而，在我们将它上传到活动服务器之前，我们需要减小它的大小，并将其放在`dist/public/js`目录中。要做到这一点，我们可以安装 [uglify-js 包](https://www.npmjs.com/package/uglify-js)并制作一个新的脚本。

```
$ npm install uglify-js --save-dev
```

我们现在可以在`package.json`中创建一个新的`minify:js`脚本:

```
"scripts": {
  "info": "echo 'npm as a build tool'",
  "lint": "echo '=> linting' && jshint assets/scripts/*.js",
  "test": "echo '=> testing' && mocha test/",
  "minify:js": "echo '=> minify:js' && uglifyjs assets/scripts/main.js -o dist/public/js/main.min.js",
  "pretest": "npm run lint -s"
}
```

运行它:

```
$ npm run minify:js -s
=> minify:js
```

该脚本在正确的目的地创建了我们文件的缩小版本。我们将使用 [clean-css 包](https://www.npmjs.com/package/clean-css)对我们的 CSS 文件做同样的事情。

```
$ npm install clean-css --save-dev
```

并创建`minify:css`脚本。

```
"scripts": {
  "info": "echo 'npm as a build tool'",
  "lint": "echo '=> linting' && jshint assets/scripts/*.js",
  "test": "echo '=> testing' && mocha test/",
  "minify:js": "echo '=> minify:js' && uglifyjs assets/scripts/main.js -o dist/public/js/main.min.js",
  "minify:css": "echo '=> minify:css' && cleancss assets/css/main.css -o dist/public/css/main.min.css",
  "pretest": "npm run lint -s"
}
```

让我们开始写剧本吧。

```
$ npm run minify:css -s
=> minify:css
```

### 观察变化

Grunt、Gulp 等公司最擅长的事情之一是观察一组文件，并在检测到其中一个文件发生变化时重新运行特定的任务。这在这种情况下特别有用，因为手动重新运行缩小脚本会很麻烦。

好消息是，您也可以在 npm 中这样做，使用像 [watch](https://www.npmjs.com/package/watch) 这样的包，它旨在使管理文件和目录树的监视更容易。

```
$ npm install watch --save-dev
```

然后在 package.json 中，您需要指定在检测到更改时要运行的任务。在这种情况下，JavaScript 和 CSS 缩小:

```
"scripts": {
  ...
  "watch": "watch 'npm run minify:js && npm run minify:css' assets/scripts/ assets/css/"
}
```

使用以下命令启动脚本:

```
$ npm run watch
```

现在，只要`assets/scripts/`或`assets/css/`中的任何文件发生变化，就会自动调用缩小脚本。

## 构建脚本

到目前为止，我们有几个脚本，我们可以链接在一起，使一个`build`脚本应该做以下事情:林挺，测试和缩小。毕竟，一次又一次地单独运行这些任务是很痛苦的。要创建这个构建脚本，需要修改`package.json`中的脚本对象，因此:

```
"scripts": {
  "info": "echo 'npm as a build tool'",
  "lint": "echo '=> linting' && jshint assets/scripts/*.js",
  "test": "echo '=> testing' && mocha test/",
  "minify:js": "echo '=> minify:js' && uglifyjs assets/scripts/main.js -o dist/public/js/jquery.min.js",
  "minify:css": "echo '=> minify:css' && cleancss assets/css/main.css -o dist/public/css/main.min.css",
  "build": "echo '=> building' && npm run test -s && npm run minify:js -s && npm run minify:css -s",
  "pretest": "npm run lint -s"
}
```

运行`build`脚本给我们以下输出。

```
$ npm run build -s
=> building
=> linting
=> testing

  Author
    constructor
      ✓ should have a default name
    #writeArticle
      ✓ should store articles
    #listArticles
      ✓ should list articles

  3 passing (6ms)

=> minify:js
=> minify:css
```

## 服务器脚本

在我们运行了我们的`build`脚本之后，如果我们能够为我们在`dist`中的内容启动一个服务器并在浏览器中检查它，那就太好了。我们可以使用 [http-server 包](https://www.npmjs.com/package/http-server)来完成这项工作。

```
$ npm install http-server -save-dev
```

我们制作一个`server`脚本。

```
"scripts": {
  ...
  "server": "http-server dist/public/",
}
```

现在我们可以`run`我们的服务器。

```
$ npm run server
Starting up http-server, serving dist/public/ on: http://0.0.0.0:8080
Hit CTRL-C to stop the server
_ 
```

当然,`server`脚本可以添加到`build`脚本中，但是我把它留给读者作为练习。

## 结论

希望这篇文章已经展示了 npm 作为一个构建工具是多么的灵活和强大。下一次当你开始一个新项目时，试着不要直接使用诸如 Gulp 或 Grunt 之类的工具——试着只用 npm 来解决你的需求。你可能会感到惊喜。

如果你有任何问题或意见，我很乐意在下面的帖子中听到。

## 分享这篇文章