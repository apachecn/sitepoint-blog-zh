# 用 PostCSS 提高你的 CSS 质量

> 原文：<https://www.sitepoint.com/improving-the-quality-of-your-css-with-postcss/>

“代码质量”这个术语对程序员来说并不陌生。毕竟，每个开发人员都知道代码仅仅工作是不够的。它还应该具有其他的品质:它应该是可读的，格式良好的和一致的。它还应该符合一定的量化标准。不幸的是，这一点在编写 CSS 时经常被忽略。我们可以花很多时间争论为什么会发生这种情况，但重要的是 CSS 和 JavaScript、PHP 或其他任何东西一样都是代码，我们应该注意我们编写它的方式。否则，可能会导致事情变得比应该的更复杂。

在本文中，我们将探索如何利用 PostCSS 来帮助我们保持 CSS 代码的高质量。首先，让我们试着指出我们所说的“更好的 CSS 代码”到底是什么意思。有几件事需要注意:

*   **代码在风格上应该一致—**你可以自由选择如何命名你的类，在哪里放置新的行，或者如何列出属性，但是你应该在所有的样式表上以同样的方式来做。一致的风格提高了可读性，使代码更容易理解。
*   **代码应该遵守一些度量标准—**有一些量化的度量标准，我们可以测量并保持在某个阈值，例如任何选择器的最大精度或页面上使用的独特颜色的数量。
*   **应该避免黑客攻击—**某些构造，例如`!important`指令有时看起来是一个可行的解决方案，但它们通常只会使代码更加复杂。

当然这不是一个完整的列表，但是现在让我们把注意力集中在上面的问题上。它们可能看起来显而易见，但是当在一个大型团队中工作时，人们的技能可能会有所不同，它们很容易被忽略。我们想要的是一个工具，它可以通过代码分析来帮助我们自动执行这些标准。这就是 PostCSS 的用武之地。

## PostCSS 能有什么帮助

在 SitePoint 上一篇关于 PostCSS 的文章中，我们介绍了 PostCSS 是什么，以及它的插件如何用于各种目的。在本文中，我们将关注几个 PostCSS 插件，它们可以帮助我们保持 CSS 代码的最佳质量。

在我们开始之前，让我们建立一个沙盒项目来进行实验。与上一篇文章中通过命令行使用 PostCSS 相反，我们将使用 Gulp。首先创建一个新文件夹，并在那里启动一个 npm 项目。之后，我们需要安装 Gulp，PostCSS 插件，和一个 [reporter 插件](https://github.com/postcss/postcss-reporter)，用于查看 PostCSS 插件的输出。为此，导航到新创建的项目并运行:

```
npm i gulp gulp-postcss postcss-reporter --save-dev
```

然后创建一个空的`style.css`文件和一个包含以下内容的`gulpfile.js`:

```
var gulp = require('gulp');
gulp.task('analyze-css', function () {
  var postcss = require('gulp-postcss');
  var reporter = require('postcss-reporter');

  return gulp.src('style.css')
    .pipe(postcss([ 
      reporter()
    ]));
});
```

这将定义一个任务，该任务将扫描`style.css`的内容，并通过一系列 PostCSS 插件运行它。你已经可以运行`gulp analyze-css`了，但是它不会做太多，因为只有一个报告插件，没有什么可报告的。让我们添加我们的第一个林挺插件。

## Stylelint

到目前为止，任何语言都有它的短柄，CSS 也不例外。 [Stylelint](https://github.com/stylelint/stylelint) 允许你根据一组预定义的规则来验证你的 CSS 代码，这些规则可以检查你的代码的格式是否一致，是否使用了某些规则、单位或指令，以及潜在的错误(比如不正确的颜色)。它允许你定义超过一百条规则的[——其中一些做基本的事情，例如，确保选择器和后面的花括号之间有一个空格，或者只使用单引号。其他的更有意思。这里有几个例子:](https://github.com/stylelint/stylelint/blob/master/docs/user-guide/rules.md)

*   `property-blacklist`和`unit-blacklist`允许您指定不能使用的属性和单位列表。
*   `property-no-vendor-prefix`根据来自[我可以使用](http://caniuse.com/)的数据，警告您对不需要供应商前缀的属性使用供应商前缀。
*   `declaration-no-important`不允许使用`!important`指令。
*   `selector-max-specificity`限制选择器的最大特异性。

默认情况下，Stylelint 附带的所有规则都是禁用的，因此希望您自己配置这些规则。考虑到规则的数量，这可能需要一段时间。或者，您可以使用预定义的配置，如[stylelint-config-standard](https://github.com/stylelint/stylelint-config-standard)，并使用您自己的规则扩展它。

让我们用一个标准规则集来设置 stylelint:

```
npm i stylelint stylelint-config-standard --save-dev
```

您还需要扩展 gulpfile 来启用新插件:

```
var gulp = require('gulp');
gulp.task('analyze-css', function () {
  var postcss = require('gulp-postcss');
  var stylelint = require('stylelint');
  var reporter = require('postcss-reporter');

  return gulp.src('style.css')
    .pipe(postcss([
      stylelint(), 
      reporter()
    ]));
});
```

Stylelint 规则可以在 gulpfile 中内联配置，但是我更喜欢将它们保存在一个单独的文件中。在您的项目文件夹中创建一个`.stylelintrc`文件，并添加以下内容:

```
{
  "extends": "stylelint-config-standard"
}
```

这将告诉 stylelint 我们自己的规则集将基于标准配置。现在，让我们更新我们的`style.css`文件，并针对这个野蛮的 CSS 测试插件:

```
.title,.content{ 
  background: #FFFFFF; 
  font-size:0.9em;

  margin: 0;
}
```

只需运行`gulp analyze-css`，它就会方便地报告一大堆违规行为:

```
style.css
1:7  ⚠  Expected newline after "," (selector-list-comma-newline-after) [stylelint]
1:15 ⚠  Expected single space before "{" (block-opening-brace-space-before) [stylelint]
1:17 ⚠  Expected newline after "{" of a multi-line block (block-opening-brace-newline-after) [stylelint]
1:17 ⚠  Unexpected whitespace at end of line (no-eol-whitespace) [stylelint]
2:5  ⚠  Expected indentation of 2 spaces (indentation) [stylelint]
2:17 ⚠  Expected "#FFFFFF" to be "#ffffff" (color-hex-case) [stylelint]
2:17 ⚠  Expected "#FFFFFF" to be "#FFF" (color-hex-length) [stylelint]
2:25 ⚠  Expected newline after ";" in a multi-line rule (declaration-block-semicolon-newline-after) [stylelint]
2:25 ⚠  Unexpected whitespace at end of line (no-eol-whitespace) [stylelint]
3:5  ⚠  Expected indentation of 2 spaces (indentation) [stylelint]
3:15 ⚠  Expected single space after ":" with a single-line value (declaration-colon-space-after) [stylelint]
4:4  ⚠  Unexpected whitespace at end of line (no-eol-whitespace) [stylelint]
5:4  ⚠  Unexpected whitespace at end of line (no-eol-whitespace) [stylelint]
6:5  ⚠  Expected indentation of 2 spaces (indentation) [stylelint]
7:1  ⚠  Unexpected missing newline at end of file (no-missing-eof-newline) [stylelint]
```

使用这个插件可以真正帮助你在编写 CSS 时实施良好的实践。继续浏览规则列表，并覆盖标准配置中您不同意的规则。您可以在以后将这些规则作为您自己的项目和团队集进行分发。如果没有符合你需求的规则，你总是可以[自己实现一个](https://github.com/stylelint/stylelint/blob/master/docs/developer-guide/rules.md)。

## 我用吗

编写 CSS 时的许多痛苦来自于试图记住你的目标是哪些浏览器，以及哪些功能可以在这些浏览器中使用。 [Do I Use](https://github.com/anandthakker/doiuse) 是一个插件，可以帮助你确保你写的 CSS 被你的目标浏览器支持。你首先要定义你的目标是支持哪些浏览器。之后，当你运行插件时，它会扫描你的代码，对照 caniuse.com[的数据库进行验证，如果某些代码不受支持，就会触发一个错误。](http://caniuse.com)

使用这个插件相当简单。安装它:

```
npm i doiuse --save-dev
```

并更新您的 gulp 文件:

```
return gulp.src('style.css')
  .pipe(postcss([
    doiuse({
      browsers: ['ie >= 9', 'last 2 versions'],
    }),
    reporter()
]));
```

这个配置表明我们的目标是支持每个浏览器的最后两个主要版本，以及 IE9 和更新的版本。

为了演示结果，我们将在一些新奇的 CSS 上运行插件，这些 CSS 使用了网格布局模块中的特性。

```
body {
  display: grid;
  grid-columns: 200px 1% 1fr;
  grid-rows: auto 15px auto 15px auto;
}
```

以下是杜豪斯对此的看法:

```
style.css
11:2    ⚠  CSS3 Multiple column layout not supported by: IE (9), Firefox (43,44), Chrome (48,49), Safari (8,9), Opera (34,35), iOS Safari (8.1-8.4,9.0-9.2) (multicolumn) [doiuse]
```

很遗憾，但是在写作的时候，CSS 网格模块还不够成熟。但是让我们关注好的部分:我们现在有一个工具来帮助我们保持跟踪浏览器的能力！

## 不可变 CSS

样式表中错误和复杂性的主要来源之一是覆盖 CSS 规则。即使使用现代的调试工具，有时也很难找出样式被覆盖的地方和原因。这就是为什么不重写样式而是向选择器添加额外的修饰符被认为是好的实践。不可变 CSS 插件可以在代码中出现这种样式覆盖的情况时警告你。

它有两种操作模式。默认情况下，只有当您试图重写不同文件中的样式时，它才会警告您。当多个文件被打包成一个文件时，它将利用源地图来找出这些样式的来源。这意味着它可以很好地与 Sass imports 或 [postcss-import](https://github.com/postcss/postcss-import) 插件配合使用。如果您想更进一步，您可以启用严格模式，如果您在单个文件中重写样式，它也会警告您。

这里有一个简单的例子来说明这一切。像往常一样，我们需要首先安装插件:

```
npm i immutable-css --save-dev
```

并在 gulpfile 中启用严格模式的插件:

```
return gulp.src('style.css')
  .pipe(postcss([
    immutableCss({
      strict: true
    }),
    reporter()
  ]))
```

然后喂它一些讨厌的 CSS:

```
.title {
  color: blue;
  font-weight: bold;
}

.title {
  color: green;
}

.article .title {
  font-size: 1.2em;
}
```

该插件将方便地报告`.title`类已经变异:

```
⚠  .title was mutated 3 times
[line 1, col 1]: /Users/pavels/Documents/projects/sandbox/postcss/style.css
[line 6, col 1]: /Users/pavels/Documents/projects/sandbox/postcss/style.css
[line 10, col 1]: /Users/pavels/Documents/projects/sandbox/postcss/style.css
```

你可以在它的[互动游乐场](http://immutablecss.com/)里摆弄这个插件。

## CSS 统计和列表选择器

我们要看的最后两个插件是 [CSS stats](https://github.com/cssstats/postcss-cssstats) 和[列表选择器](https://github.com/davidtheclark/list-selectors)。它们与我们到目前为止检查过的插件有些不同:它们的目的不是指出问题，而是为定制分析提供数据。

CSS stats 提供了关于样式表的广泛信息:使用了多少规则、选择器或声明，它们是什么，选择器的平均特异性是什么，或者代码中出现了什么字体大小。这只是可以在生成的报告中找到的信息的一个示例。更详细的描述可以在[它的 GitHub 页面](https://github.com/cssstats/cssstats-core#returned-object)上找到。你也可以访问 cssstats.com[的](http://cssstats.com)获取使用插件数据生成的报告示例。

List selectors 是一个更简单的插件，它专注于提取样式表中使用的选择器列表，并按类别对它们进行分组——类选择器、属性、id 或标签。

这两个插件都可以用于各种代码分析。仅举几个例子:

*   将你使用的实体的特异性、大小和数量保持在某个阈值。
*   确保所有的选择器都是根据你的编码风格编写的。
*   确保您的媒体查询一致。

这些只是我想到的一些想法。一个更实际的方法是设置所有以前的插件，返回到这两个插件，看看它们是否能传递更多有用的信息。

## 把一切都包起来

代码林挺和分析只是使用 PostCSS 的一种方式，但它本身可以为您的开发过程增加很多价值，并节省一些时间和开发人员的一些时间。尽管这在其他编程领域已经成为一种常见的做法，但当涉及到 CSS 时，它仍然经常被忽略。我相信配置 PostCSS 和这几个插件是一个简单的步骤，可以使你的开发更加容易和可靠。

## 分享这篇文章