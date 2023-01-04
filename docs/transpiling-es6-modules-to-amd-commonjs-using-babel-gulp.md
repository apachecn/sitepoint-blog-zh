# 使用 Babel & Gulp 将 ES6 模块传输到 AMD & CommonJS

> 原文：<https://www.sitepoint.com/transpiling-es6-modules-to-amd-commonjs-using-babel-gulp/>

ECMAScript 6(又名 ECMAScript 2015 或 ES6)，JavaScript 下一版本的规范[已经获得批准](http://www.infoq.com/news/2015/06/ecmascript-2015-es6)，浏览器供应商正在努力实现它。与 ECMAScript 的早期版本不同，ES6 对这种语言进行了大量的修改，使其非常适合今天使用的规模。Sitepoint 的[有许多文章](https://www.sitepoint.com/?s=ECMAScript+6)涵盖了这些特性。

虽然浏览器[还没有实现所有的特性](https://kangax.github.io/compat-table/es6/)，但是我们已经可以在开发过程中利用 ES6，并在发布应用程序之前将其转换为浏览器可以理解的版本。[巴别塔](https://babeljs.io/)和[特雷尔](https://github.com/google/traceur-compiler)是用于这一目的的两种领先的运输工具。微软的 JavaScript 类型化超集， [TypeScript](http://www.typescriptlang.org/) 也可以用作 ES6 transpiler。

在我之前的一篇文章的[中，我介绍了如何使用 ES6 编写 Angular 1.x 应用程序。在那篇文章中，我使用了 Traceur 的动态 transpiler 来运行这个应用程序。虽然这种方法可行，但最好是事先传输文件，减少浏览器中的工作量。在本文中，我们将看到如何将相同的示例应用程序移植到 ES5，并将模块移植到 CommonJS 或，并使用 Babel 使其在当今的浏览器上运行。尽管该示例是基于 Angular 的，但是 transpilation 技术可以用于任何有效的 ES6 代码。](https://www.sitepoint.com/writing-angularjs-apps-using-es6/)

和以往一样，你可以在我们的 GitHub repo 上找到这篇文章[的代码。](https://github.com/sitepoint-editors/Angular-ES6-BookShelf-Modules)

## 模块的重要性

任何用于编写大型应用程序的语言的一个关键特性是能够以模块的形式加载应用程序的不同部分。模块不仅可以帮助我们保持代码的整洁，还可以减少全局作用域的使用。一个模块的内容对任何其他模块都不可用，除非其他模块显式加载它。

模块的重要性不仅限于应用程序。即使是大型 JavaScript 库也可以利用模块系统将其对象导出为模块，使用这些库的应用程序可以根据需要导入这些模块。Angular 2 和 [Aurelia](http://aurelia.io/) 已经开始使用这个功能。

如果你想快速入门如何使用 ES6 中的模块，请阅读:[了解 ES6 模块](https://www.sitepoint.com/understanding-es6-modules/)

## 关于示例应用程序

我们的示例应用程序的主题是一个虚拟书架。它由以下页面组成:

1.  主页:显示可以标记为已读或移动到归档的活动书籍列表。
2.  Add book page:通过接受书名和作者名将新书添加到书架上。它不允许有重复的标题。
3.  存档页面:列出所有存档的书籍。

该应用程序是使用 AngularJS 1.3 和 ES6 构建的。如果你查看`app`文件夹中的任何文件，你会看到关键字`export`和`import`用于从当前模块导出对象和从其他模块导入对象。现在，我们的工作是使用[巴别塔的吞咽任务](https://www.npmjs.com/package/gulp-babel)将这些模块转换成一个现有的模块系统。

### 但我没有用棱角。我只想把 ES6 模块转换成 CommonJS/AMD

别担心！我们掩护你。稍加调整，下面演示的方法可以用于任何涉及 ES6 模块的项目。角度在这里并不重要。

## 转换为普通

CommonJS 是由 [CommonJS 集团](http://wiki.commonjs.org/wiki/CommonJS)定义的模块系统。这是一个同步模块系统，其中模块使用`require`函数加载，使用`module`对象的`exports`属性导出。默认情况下，`module`对象应该在所有模块中都可用。

Node.js 使用这个模块系统，所以它在本地定义了`module`对象，并使它对您的应用程序可用。由于浏览器没有定义这个对象，我们需要使用一个名为 [Browserify](http://browserify.org/) 的工具来填补这个空白。

在我们开始之前，我们还需要安装几个 npm 包。这些将使我们能够使用 Babel 和 Browserify 结合 Gulp 将我们的 ES6 模块转换为一种常见的模块格式，并将应用程序打包为一个文件供浏览器使用。

*   将 ES6 代码转换成普通的 ES5
*   [browser fiy](https://www.npmjs.com/package/browserify)—通过捆绑所有依赖项，让您在浏览器中`require('modules')`
*   [vinyl-source-stream](https://www.npmjs.com/package/vinyl-source-stream) —直接处理 Browserify 模块，避免使用大口浏览包装器
*   [vinyl-buffer](https://www.npmjs.com/package/vinyl-buffer) —将流转换为缓冲区(对于不支持流的吞咽式丑化是必要的)
*   [囫囵吞枣](https://www.npmjs.com/package/gulp-uglify) —缩小文件
*   [删除](https://www.npmjs.com/package/del) —允许您删除文件和文件夹
*   [gulp-rename](https://www.npmjs.com/package/gulp-rename) —一个让你重命名文件的插件

您可以通过键入以下命令获得该批次:

```
npm install gulp-babel browserify gulp-browserify vinyl-source-stream vinyl-buffer gulp-uglify del gulp-rename --save-dev 
```

现在让我们开始在`gulpfile.js`中使用这些包。我们需要编写一个任务来获取所有 ES6 文件并将其传递给 Babel。babel 中默认的模块系统是 CommonJS，所以我们不需要给 Babel 函数发送任何选项。

```
var babel = require('gulp-babel'),
    browserify = require('browserify'),
    source = require('vinyl-source-stream'),
    buffer = require('vinyl-buffer'),
    rename = require('gulp-rename'),
    uglify = require('gulp-uglify'),
    del = require('del');

gulp.task('clean-temp', function(){
  return del(['dest']);
});

gulp.task('es6-commonjs',['clean-temp'], function(){
  return gulp.src(['app/*.js','app/**/*.js'])
    .pipe(babel())
    .pipe(gulp.dest('dest/temp'));
}); 
```

希望这里没有什么太令人困惑的东西。我们声明了一个名为`es6-commonjs`的任务，它获取 app 目录及其任何子目录中的任何 JavaScript 文件。然后它通过 Babel 将它们传输，Babel 又将单个文件转换成 ES5 和 CommonJS 模块，并将转换后的文件复制到`dest/temp`文件夹中。`es6-commonjs`任务有一个名为`clean-temp`的依赖项，它将在`es6-commonjs`任务运行之前删除`dest`目录和其中的所有文件。

如果你想使代码更加明确，并指定模块系统，你可以修改巴别塔的用法如下:

```
.pipe(babel({
  modules:"common"
})) 
```

现在，我们可以通过应用 Browserify 从这些单独的文件创建一个单独的捆绑文件，然后使用 uglify 包缩小输出。以下代码片段说明了这一点:

```
gulp.task('bundle-commonjs-clean', function(){
  return del(['es5/commonjs']);
});

gulp.task('commonjs-bundle',['bundle-commonjs-clean','es6-commonjs'], function(){
  return browserify(['dest/temp/bootstrap.js']).bundle()
    .pipe(source('app.js'))
    .pipe(buffer())
    .pipe(uglify())
    .pipe(rename('app.js'))
    .pipe(gulp.dest("es5/commonjs"));
}); 
```

上面的任务有两个依赖项:第一个是`bundle-commonjs-clean`任务，它将删除目录`es5/commonjs`，第二个是之前讨论过的`es6-commonjs`任务。一旦这些都运行了，这个任务就把合并和缩小的文件`app.js`放到文件夹`es5/commonjs`中。该文件可以在`index.html`中直接引用，页面可以在浏览器中查看。

最后，我们可以添加一个任务来开始:

```
gulp.task('commonjs', ['commonjs-bundle']); 
```

## 转换为 AMD

[异步模块定义(AMD)系统](https://en.wikipedia.org/wiki/Asynchronous_module_definition)顾名思义就是异步模块加载系统。它允许多个相关模块并行加载，并且在尝试加载其他模块之前不会等待一个模块被完全加载。

[Require.js](http://requirejs.org) 是用来和 AMD 合作的库。可通过 Bower 获得 RequireJS:

```
bower install requirejs --save 
```

我们还需要 require.js 的 Gulp 插件来捆绑应用程序。为此安装`gulp-requirejs` npm 包。

```
npm install gulp-requirejs --save-dev 
```

现在，我们需要编写将 ES6 代码转换成 ES5 和 AMD 代码的任务，然后使用 RequireJS 进行捆绑。这些任务与 CommonJS 小节中创建的任务非常相似。

```
var requirejs = require('gulp-requirejs');

gulp.task('es6-amd',['clean-temp'], function(){
    return gulp.src(['app/*.js','app/**/*.js'])
    .pipe(babel({ modules:"amd" }))
    .pipe(gulp.dest('dest/temp'));
});

gulp.task('bundle-amd-clean', function(){
  return del(['es5/amd']);
});

gulp.task('amd-bundle',['bundle-amd-clean','es6-amd'], function(){
  return requirejs({
    name: 'bootstrap',
    baseUrl: 'dest/temp',
    out: 'app.js'
  })
  .pipe(uglify())
  .pipe(gulp.dest("es5/amd"));
});

gulp.task('amd', ['amd-bundle']); 
```

为了在 index.html 页面上使用最终的脚本，我们需要添加一个对 RequireJS 的引用，生成脚本，然后加载`bootstrap`模块。`app`文件夹中的`bootstrap.js`文件引导 AngularJS 应用程序，所以我们需要加载它来启动 AngularJS 应用程序。

```
<script src="bower_components/requirejs/require.js" ></script>
<script src="es5/amd/app.js"></script>
<script> (function(){
    require(['bootstrap']);
  }()); </script> 
```

## 结论

模块是 JavaScript 中一个期待已久的特性。他们将到达 ES6，但不幸的是，他们的原生浏览器支持目前很差。然而，这并不意味着你今天不能使用它们。在本教程中，我演示了如何使用 Gulp、Babel 和各种插件将 ES6 模块转换为可以在浏览器中运行的 CommonJS 和 AMD 格式。

至于 ES6 呢？自从 ES6 发布以来，它在社区中获得了很多关注。它已经被几个 JavaScript 库或框架使用，包括 Bootstrap 的 JavaScript 插件、Aurelia、Angular 2 和其他几个。TypeScript 还增加了对包括模块在内的一些 ES6 特性的支持。今天学习和使用 ES6，将会减少将来转换代码的工作量。

## 分享这篇文章