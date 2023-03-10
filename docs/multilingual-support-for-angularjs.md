# AngularJS 的多语言支持

> 原文：<https://www.sitepoint.com/multilingual-support-for-angularjs/>

在某些情况下，需要提供多语言支持。有时，在您构建的应用程序中提供对不同语言的支持，并为您的用户提供以不同习惯用法查看内容的可能性，这可能是一个好主意。在本教程中，我将向你展示如何为任何 AngularJS 应用程序添加多语言支持。

我们将使用 AngularJS 构建一个需要多语言支持的单页面应用程序，这样用户无需刷新页面就可以在多种语言之间即时切换。在这种情况下，我们需要对我们的应用程序做更多的事情，包括翻译它的文本，在不同语言之间即时切换，或者改变布局方向(RTL 到 LTR)。

本文[中开发的所有代码都可以在 GitHub](https://github.com/sitepoint-editors/multilingualwithangular) 上获得。

## 环境设置

在我将要展示的例子中，我将使用 [Bower](http://bower.io/) 和 [Gulp](http://gulpjs.com/) 来尽可能使我们的开发环境更加自动化和灵活。如果您的系统上还没有安装它们，或者您从未在您的开发工作流程中使用过它们，我强烈建议您安装它们，并开始了解它们。以下是可能对此有用的文章列表:

*   [带有 Bower 的浏览器的包管理](https://www.sitepoint.com/package-management-for-the-browser-with-bower/)
*   [如何咕噜咕噜地实现工作流自动化](https://www.sitepoint.com/how-to-grunt-and-gulp-your-way-to-workflow-automation/)
*   [从约曼、咕噜和鲍尔开始你的 AngularJS 发展](https://www.sitepoint.com/kickstart-your-angularjs-development-with-yeoman-grunt-and-bower/?utm_content=bufferfd1f4&utm_medium=social&utm_source=plus.google.com&utm_campaign=buffer)

作为第一项任务，让我们通过在命令行中运行项目目录中的`bower init`来设置 Bower，我们称之为`multilingualwithangular`。`bower init`将以交互方式创建一个名为`bower.json`的清单文件，该文件将包含一些关于项目的信息以及先前安装的前端依赖项的列表。

下一步是安装最初需要的包。

```
bower install angular angular-translate --save
```

让我们设置 Gulp 并安装这些基本包。首先，我们需要运行命令`npm init`并遵循几个简单的步骤来创建一个`package.json`文件，该文件将包含关于项目以及如何管理 Node.js 模块的一些信息。

接下来，我们将在项目中安装 Gulp:

```
npm install gulp --save-dev
```

我们还需要一些 JavaScript 和 Sass 以及其他自动化工具的 Gulp 依赖项。

```
npm install gulp-sass gulp-uglify gulp-concat run-sequence browser-sync --save-dev
```

此时，我们必须在项目目录中创建一个空的`gulpfile.js`配置文件。它将用于定义我们的吞咽任务，如 JavaScript 和 Sass。[你可以看看我的 GitHub 库](https://github.com/sitepoint-editors/multilingualwithangular/blob/master/gulpfile.js)里完整的配置文件。

在 JavaScript 任务中，我们将添加两个文件，`angular`和`angular-translate`，以及位于`/js`目录中的主 JavaScript 文件。然后，我们将它们连接在一起，并使用名为 [Uglify](https://github.com/mishoo/UglifyJS) 的 Node.js 库来压缩和减小文件的大小。

```
'use strict';

var gulp         = require('gulp');
var sass         = require('gulp-sass');
var concat       = require('gulp-concat');
var uglify       = require('gulp-uglify');
var runSequence  = require('run-sequence');
var browserSync  = require('browser-sync');

gulp.task('js', function(){
  return gulp.src([
    './bower_components/angular/angular.js',
    './bower_components/angular-translate/angular-translate.js',

    './js/app.js'])
    .pipe(concat('app.min.js'))
    .pipe(uglify())
    .pipe(gulp.dest('./js'))
});

gulp.task('serve', function() {
  browserSync({
    server: {
      baseDir: "./"
    }
  });
});

gulp.task('build', [], function() {
  runSequence('js');
});

gulp.task('default', ['build'], function() {});
```

完成后，我们可以运行之前创建的`gulp build`任务。它将运行`js`任务，然后生成一个`/js/app.min.js`文件，该文件将包含在一个简单的 HTML 文件中。

```
<!DOCTYPE HTML>
<html>
<head>
  <title>Multilingual AngularJS</title>
  <meta charset="utf-8">
</head>

<body>
  <script src="js/app.min.js"></script>
</body>
</html>
```

要在 localhost 环境中打开项目，运行`gulp serve`，这将自动打开一个指向 [localhost:3000](http://localhost:3000/) 的浏览器选项卡。

## 使用角度平移添加平移

完成这些初始配置任务后，是时候向前迈出一步，为应用程序文本添加翻译支持了。我们将以阿拉伯语和英语作为我们的主要语言。就语法、句法和书写方向而言，它们是完全不同的语言(从右向左的阿拉伯语和从左向右的英语)。

[angular-translate](https://angular-translate.github.io/) 是一个 [AngularJS](https://angularjs.org/) 模块，我们可以用它来翻译文本。它提供了许多有趣的特性，比如过滤器、指令和 i18n 数据的异步加载。

首先我们来设置 AngularJS，用`angular-translate`配置

```
// js/app.js

var app = angular.module('Multilingual', ['pascalprecht.translate']);

app.config(['$translateProvider', function($translateProvider) {

  $translateProvider
  .translations('ar', {
    'HELLO': 'مرحبا'
  })
  .translations('en', {
    'HELLO': 'Hello'
  })
  .preferredLanguage('ar');

}]);
```

然后，让我们稍微修改一下 HMTL:

```
<html ng-app="Multilingual">
```

然后从命令行运行`gulp build`在 JavaScript 文件中构建新的更改。在前面的代码片段中，我们有:

*   创建了一个名为`Multilingual`的角度模块。
*   将`angular-translate`模块作为依赖项注入到我们的应用程序中作为`pascalprecht.translate`。
*   在`.config()`方法中注入`$translateProvider`。
*   使用`.translations()`方法注册不同语言的翻译表，并将语言关键字如`en`或`ar`设置为第一个参数。
*   使用`.preferredLanguage()`方法设置首选语言(这很重要，因为我们使用多种语言，所以我们可以教`angular-translate`在第一次加载时使用哪种语言)。

让我们看一个使用`translate`过滤器的`angular-translate`的例子

```
<h2>{{ 'HELLO' | translate }}</h2>
```

如[翻译指令文档](https://angular-translate.github.io/docs/#/guide/05_using-translate-directive)所述，一个视图中有太多过滤器会设置太多观察表达式。更好更快的实现方法是使用`translate`指令。使用该指令的另一个原因是，当我们的模板被加载时，用户有可能在它被 AngularJS 渲染之前看到原始的`{{ 'HELLO' | translate }}`。

我们使用该指令的方式是将翻译 ID 作为`translate`指令的属性值传递。

```
<h2 translate="HELLO"></h2>
```

有时我们可能需要知道我们是否遗漏了一些翻译 id。`angular-translate-handler-log`提供了一个非常好的方法来帮助我们解决这个问题，这个方法叫做`useMissingTranslationHandlerLog()`,它会将任何丢失翻译 ID 的警告记录到控制台中。要使用它，我们必须先安装它。你可以和鲍尔一起做:

```
bower install angular-translate-handler-log --save
```

然后，更新 JavaScript Gulp 任务:

```
gulp.task('js', function(){
  return gulp.src([
    './bower_components/angular/angular.js',
    './bower_components/angular-translate/angular-translate.js',

    // New file
    './bower_components/angular-translate-handler-log/angular-translate-handler-log.js',

    './js/app.js'])
    .pipe(concat('app.min.js'))
    .pipe(uglify())
    .pipe(gulp.dest('./js'));
});
```

最后，使用该方法直接在`$translateProvider`上运行`gulp build`，如下所示:

```
$translateProvider
  .translations('ar', {
    'HELLO': 'مرحبا'
  })
  .translations('en', {
    'HELLO': 'Hello'
  })
  .preferredLanguage('ar')
  .useMissingTranslationHandlerLog();
```

如果我们错过了`HELLO`的翻译，由于这种方法，我们将得到一个警告消息，说“HELLO 的翻译不存在”。

### 异步加载翻译文件

除了在`.config()`方法中直接添加不同语言的翻译数据之外，还有另一种异步和延迟加载的方法。实际上，[有几种方法可以完成这个任务](https://angular-translate.github.io/docs/#/guide/12_asynchronous-loading)，但是在本教程中我们将只使用`angular-translate-loader-static-files`扩展。

首先，我们需要用 Bower 安装扩展:

```
bower install angular-translate-loader-static-files --save
```

一旦安装完毕，我们需要用扩展文件路径更新 Gulp 任务，然后运行`gulp build`。

```
gulp.task('js', function(){
  return gulp.src([
    './bower_components/angular/angular.js',
    './bower_components/angular-translate/angular-translate.js',
    './bower_components/angular-translate-handler-log/angular-translate-handler-log.js',

    // New file
    'bower_components/angular-translate-loader-static-files/angular-translate-loader-static-files.js',

    './js/app.js'])
    .pipe(concat('app.min.js'))
    .pipe(uglify())
    .pipe(gulp.dest('./js'));
});
```

此时，我们需要创建一个`/translations`目录并添加语言翻译文件。该结构将如下所示:

```
translations
├── ar.json
└── en.json
```

在`ar.json`文件中，写下下面报告的内容:

```
{
  "HELLO": "مرحبا",
  "BUTTON_LANG_AR": "العربية",
  "BUTTON_LANG_EN": "الإنجليزية",
  "WELCOME_MESSAGE": "مرحباً في موقع AngularJS المتعدد اللغات"
}
```

相反，在`en.json`文件中保存以下内容:

```
{
  "HELLO": "Hello",
  "BUTTON_LANG_AR": "Arabic",
  "BUTTON_LANG_EN": "English",
  "WELCOME_MESSAGE": "Welcome to the AngularJS multilingual site"
}
```

现在，我们可以使用下面的方法，使用`useStaticFilesLoader`方法告诉`angular-translate`使用特定的模式加载哪些语言文件:

```
prefix - specifies file prefix
suffix - specifies file suffix
```

下面是 JavaScript 文件的变化:

```
// js/app.js

app.config(['$translateProvider', function($translateProvider) {

  $translateProvider
  .useStaticFilesLoader({
    prefix: '/translations/',
    suffix: '.json'
  })
  .preferredLanguage('ar')
  .useMissingTranslationHandlerLog();
}]);
```

如果我们想给文件添加一个前缀，我们可以使用一个前缀(在本例中是`locale-`)来重命名每个文件:

```
translations
├── locale-ar.json
└── locale-en.json
```

通过应用这一更改，我们必须如下更新`app.js`文件:

```
// js/app.js

app.config(['$translateProvider', function($translateProvider) {

  $translateProvider
  .useStaticFilesLoader({
    prefix: '/translations/locale-',
    suffix: '.json'
  })
  .preferredLanguage('ar')
  .useMissingTranslationHandlerLog()
}]);
```

这里`angular-translate`将我们的代码连接成`{{prefix}}{{langKey}}{{suffix}}`，然后加载`/translations/locale-en.json`文件。

### 在不同语言之间切换

到目前为止，我们已经看到了如何处理两种语言的文本翻译。然而，我们仍然不能在运行时从浏览器切换到另一种语言。为此，我们需要为每种语言添加一个按钮来进行切换。

```
<div ng-controller="LanguageSwitchController">
  <button ng-show="lang == 'en'" ng-click="changeLanguage('ar')" translate="BUTTON_LANG_AR"></button>
  <button ng-show="lang == 'ar'" ng-click="changeLanguage('en')" translate="BUTTON_LANG_EN"></button>
</div>
```

我们还可以创建一些`$rootScope`属性，并在我们的 HTML 代码中使用它们来设置初始布局方向和第一次加载中的`lang`属性，以后每当语言改变时绑定它们。

```
// js/app.js

app.run(['$rootScope', function($rootScope) {
  $rootScope.lang = 'en';

  $rootScope.default_float = 'left';
  $rootScope.opposite_float = 'right';

  $rootScope.default_direction = 'ltr';
  $rootScope.opposite_direction = 'rtl';
}])
```

`angular-translate`提供了一个叫做`use`的简便方法，它接受一个参数，并根据传递的参数为我们设置语言。此外，我们将侦听`$translateChangeSuccess`事件，该事件在翻译更改成功后被触发，以确保语言已经更改。然后，我们可以根据选择的语言修改`$rootScope`属性:

```
// js/app.js

app.controller('LanguageSwitchController', ['$scope', '$rootScope', '$translate',
  function($scope, $rootScope, $translate) {
    $scope.changeLanguage = function(langKey) {
      $translate.use(langKey);
    };

    $rootScope.$on('$translateChangeSuccess', function(event, data) {
      var language = data.language;

      $rootScope.lang = language;

      $rootScope.default_direction = language === 'ar' ? 'rtl' : 'ltr';
      $rootScope.opposite_direction = language === 'ar' ? 'ltr' : 'rtl';

      $rootScope.default_float = language === 'ar' ? 'right' : 'left';
      $rootScope.opposite_float = language === 'ar' ? 'left' : 'right';
    });
}]);
```

并对标记应用以下更改:

```
<html lang="{{ lang }}" ng-app="Multilingual">
```

在我的标题为[使用助手类来干燥和缩放 CSS](https://www.sitepoint.com/using-helper-classes-dry-scale-css/) 的文章中，您可以看到另一个使用 HTML 中的这些方向属性作为助手类的例子:

```
<div class="text-{{ default_float }}"></div>
```

### 记住语言

到目前为止，我们已经建立了切换语言功能，我们能够更改语言以使用我们最喜欢的语言。下一步是让应用程序记住我们选择的语言，这样下次我们启动它时就不用再切换到那种语言了。

我们将教我们的应用程序记住语言，使用浏览器 localStorage 来存储选择的语言，我们将使用[angular-translate-storage-local](https://github.com/angular-translate/bower-angular-translate-storage-local)扩展来实现这个目的。可以想象，下一步就是安装了。我们会和鲍尔一起做:

```
bower install angular-translate-storage-local --save
```

运行这个命令，我们还将安装`angular-cookies`和`angular-translate-storage-cookie`作为依赖项。一旦安装完毕，我们需要用运行`gulp build`的新文件更新 Gulp 任务:

```
gulp.task('js', function(){
  return gulp.src([
    './bower_components/angular/angular.js',
    './bower_components/angular-translate/angular-translate.js',
    './bower_components/angular-translate-handler-log/angular-translate-handler-log.js',
    'bower_components/angular-translate-loader-static-files/angular-translate-loader-static-files.js',

    // New files
    './bower_components/angular-cookies/angular-cookies.js',
    './bower_components/angular-translate-storage-cookie/angular-translate-storage-cookie.js',
    './bower_components/angular-translate-storage-local/angular-translate-storage-local.js',

    './js/app.js'])
    .pipe(concat('app.min.js'))
    .pipe(uglify())
    .pipe(gulp.dest('./js'));
});
```

有了这些代码，接下来的步骤是:

*   添加`ngCookies`作为依赖项。
*   通过`useLocalStorage()`告知`$translateProvider`使用本地存储

我们需要这样做:

```
var app = angular.module('Multilingual', [
  'pascalprecht.translate',
  'ngCookies'
  ]);

app.config(['$translateProvider', function($translateProvider) {
  $translateProvider
  .useStaticFilesLoader({
    prefix: '/translations/',
    suffix: '.json'
  })
  .preferredLanguage('ar')
  .useLocalStorage()
  .useMissingTranslationHandlerLog()
}]);
```

`angular-translate`将保存我们用`NG_TRANSLATE_LANG_KEY`键通过`preferredLanguage()`设置的初始语言。它会在浏览器 localStorage 中指定语言作为它的值，然后在每次用户切换语言时更新它。当用户打开应用程序时，`angular-translate`将从本地存储中检索 **it** 。

![Local Storage](img/b6e0371fc9de86b2cdd484335d0f853b.png)

## 使用布局方向

我们已经到了演示部分。如果您使用两种书写方向相同的语言(例如英语和法语)，那么配置就完成了。如果一个语言方向是 RTL，而另一个是 LTR，我们需要做一些额外的工作来调整一些布局方案。

假设这是 LTR 语言(英语)的 CSS 代码:

```
.media-image { padding-right: 1rem; }
```

对于 RTL 语言，上面的代码应该镜像为`padding-left`而不是`padding-right`:

```
.media-image { padding-left: 1rem; }
```

然而，这根本不是一个好的实践，因为它很耗时，并且涉及代码重复:

```
[lang='ar'] .media-image {
  padding-right: 0;
  padding-left: 1rem;
}
```

为了解决这个问题，我们需要编写一些 CSS 代码，并允许以有效、自动化和动态的方式支持 RTL 语言和 LTR 语言。有了这种方法，我们就不必重复或覆盖 CSS 规则。我鼓励你阅读我的文章，标题是[用 Sass 和 Grunt](https://www.sitepoint.com/manage-rtl-css-sass-grunt/) 管理 RTL CSS，了解更多关于这种技术以及如何在你的项目中使用它。

我们将在本教程中使用 Gulp 并添加一个 Sass 任务来实现它，该任务需要`ltr-app.scss`和`rtl-app.scss`。我们将导入主 Sass 文件，以及其中的特定于方向的变量:

```
gulp.task('sass', function () {
  return gulp.src(['./sass/ltr-app.scss', './sass/rtl-app.scss'])
  .pipe(sass())
  .pipe(gulp.dest('./css'));
});

// Update the build task with sass
gulp.task('build', [], function() {
  runSequence('js', 'sass');
});
```

`sass/ltr-app.scss`文件应该如下所示:

```
// LTR language directions

$default-float:       left;
$opposite-float:      right;

$default-direction:   ltr;
$opposite-direction:  rtl;

@import 'style';
```

这是`sass/rtl-app.scss`的代码:

```
// RTL language directions

$default-float:       right;
$opposite-float:      left;

$default-direction:   rtl;
$opposite-direction:  ltr;

@import 'style';
```

最后，这是一个关于`sass/style.scss`的例子:

```
body { direction: $default-direction; }

.column { float: $default-float; }

.media-image { padding-#{$opposite-float}: 1rem; }
```

有了所有这些代码，您可以运行`gulp build`，Sass 任务将生成两个文件。`css/rtl-app.css`会有下面列出的代码:

```
/* css/rtl-app.css */

body { direction: rtl; }

.column { float: right; }

.media-image { padding-left: 1rem; }
```

`css/ltr-app.css`文件将包含以下报告的内容:

```
/* css/ltr-app.css */
body { direction: ltr; }

.column { float: left; }

.media-image { padding-right: 1rem; }
```

下一步也是最后一步是基于当前语言动态地使用这些生成的文件。我们将使用`$rootScope`的`default_direction`属性在第一次加载时设置方向，然后在更改语言时绑定它。

```
<link ng-href="css/{{ default_direction }}-app.css" rel="stylesheet">
```

## 结论

正如我们所见，使用 [angular-translate](https://angular-translate.github.io/) 是进行 AngularJS 平移的方法。它提供了许多方便的过滤器、指令和有趣的工具来使用。我们已经用许多不同的方式介绍了翻译过程，探索了如何在两种语言之间切换。我们还讨论了如何在用户浏览器存储中存储选定的语言，以及如何使用 CSS 使表示层更好地响应语言方向。

我希望你喜欢这个教程。我为这篇文章创建了一个 GitHub repo，你可以在这里查看代码。请在下面的部分分享您的意见。

## 分享这篇文章