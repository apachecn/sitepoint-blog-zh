# 用 Sass 和 Grunt 管理 RTL CSS

> 原文：<https://www.sitepoint.com/manage-rtl-css-sass-grunt/>

作为一个母语为阿拉伯语的人，我以前在多语言网站上工作过，一些已经存在 LTR(从左到右)CSS 支持，然后我必须添加对 RTL(从右到左)的支持，一些项目已经从零开始。在我开始使用 Sass 和 Grunt 之前，支持两个方向是一场噩梦，而且是一个浪费时间的过程，代码重复很多。

在使用两个方向进行多语言项目时，重要的是以有效、自动化和动态的方式编写支持 RTL 和 LTR 的 CSS，这样我们就不必重复或覆盖 CSS。

这两个方向之间的区别通常是浮动方向、文本对齐、填充和边距值。

## 问题是

让我们看看在同一个项目中支持 RTL 和 LTR 在实践中会有多麻烦，以及我们如何解决这个问题。在某些情况下，在添加方向支持之前，我们会在头部添加一个新的 CSS 文件，并开始覆盖和重复代码，以改变不同组件的一些 CSS 属性，如`floats`、`padding-left`或`text-align`。

假设这是 RTL 语言模板的代码，我们已经添加了用于语言支持的`lang="ar"`属性，该属性值可能会基于服务器端的语言检测而动态改变。

```
<!DOCTYPE html>
<html lang="ar">
  <head>
    <link rel="stylesheet" href="css/app.css">
    <link rel="stylesheet" href="css/rtl-app.css">
  </head>
  <body>
    <main>Main contant</main>
    <aside>Sidebar</aside>
  </body>
</html>
```

布局要求在法线方向(LTR)上，`main`部分应该向左浮动，而`aside`应该向右浮动。

```
/* app.css */
main  { float: left; }
aside { float: right; }
```

现在，对于 RTL 方向，我们应该做上面同样的事情，但在相反的方向或镜像布局，这将是覆盖同一文件中的原始样式的代码。

```
/* app.css */
[lang='ar'] main  { float: right; }
[lang='ar'] aside { float: left; }
```

或者在一个新的 CSS 文件中附加在文件头中(如上面的例子)，我们可以这样做。

```
/* rtl-app.css */
main  { float: right; }
aside { float: left; }
```

这里的问题是我们编写了更多的代码来覆盖原始代码，加载了不止一个 CSS 文件。这不是一个好的做法，而且很耗时。

现在我们如何改进这个工作流程，我使用的解决方案是使用 CSS 预处理器，如 [Sass](http://sass-lang.com/) 和 JavaScript 任务运行器，如 [Grunt](http://gruntjs.com/) 来自动化和增强工作流程。

## 设置咕哝声

通过使用 Grunt 和 Sass，我们可以自动化并解决我们遇到的问题，这里的理论是在一个核心文件中编写我们的样式，然后为每个方向生成两个其他文件，然后根据使用的语言将每个文件包含在标题中。

用于将 Sass 编译成 CSS 的 Grunt 任务是 [grunt-sass](https://github.com/sindresorhus/grunt-sass) 。

```
{
  "name": "rtl-sass-grunt",
  "version": "0.1.0",
  "devDependencies": {
    "grunt": "^0.4.5",
    "grunt-sass": "^0.16.1"
  }
}
```

```
module.exports = function(grunt) {
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),

    sass: {
      dist: {
        files: {
          'css/ltr-app.css': 'scss/ltr-app.scss',
          'css/rtl-app.css': 'scss/rtl-app.scss'
        }
      }
    }

  });

  grunt.loadNpmTasks('grunt-sass');

  grunt.registerTask('default', ['sass']);
};
```

Sass 任务获取两个文件，一个用于`ltr-app.scss`和`rtl-app.scss`，然后从它们生成 CSS 文件。在这两个文件中，我们将为 floats 和 directions 定义一些变量，然后在最后导入核心 Sass 文件。

`ltr-app.scss`文件

```
// LTR languages directions.

$default-float:          left;
$opposite-float:        right;

$default-direction:       ltr;
$opposite-direction:      rtl;

// Import the main style

@import 'style';
```

和`rtl-app.scss`

```
// RTL languages directions.

$default-float:         right;
$opposite-float:         left;

$default-direction:       rtl;
$opposite-direction:      ltr;

// Import the main style

@import 'style';
```

我们可以在外部文件中包含这些变量，但这只是为了让事情更清楚，并给你主要的想法。

`style.scss`将包含我们所有的 CSS 或者包含其他项目文件。该文件将被导入到`ltr-app.scss`和`rtl-app.scss`中。

这是一个关于`style.scss`的例子。

```
body {
 direction: $default-direction;
}

.media {
 float: $default-float;
 padding-#{$opposite-float}: 10px;
}

.button { background-image: url("images/arror-#{default-float}.png"); }
```

我们已经在`padding-#{$opposite-float}`中使用了像`$default-float`和 [Sass 插值](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#interpolation_)这样的 Sass 变量，然后 Grunt 可以处理这个问题并生成两个文件:

```
/* ltr-app.css */

body { direction: ltr; }

.media {
  float: left;
  padding-right: 10px;
}

.button { background-image: url(images/arror-left.png); }
```

```
/* rtl-app.css */

body { direction: rtl; }

.media {
  float: right;
  padding-left: 10px;
}

.button { background-image: url(images/arror-right.png); }
```

我之前经历过的一个很好的技巧是如何添加一个特定方向的图像作为 CSS 背景，我们可以创建两个图像`arror-left.png`和`arror-right.png`并且在上面的 Sass 代码中变量会在`left`和`right`之间变化。

## 服务器端设置

为了在处理多语言项目时获得更大的灵活性，配置后端以提供一些变量是很重要的。

我们从后端需要的是定义类似的变量，就像我们对 Sass 所做的那样，但这次我们将在模板或视图中使用它们。每个后端解决方案都应该能够提供创建这些变量并在视图中传递它们的方法。像`def-float`和`def-direction`这样的变量。

设置后端配置将使我们能够在为检测到的方向生成的 CSS 文件之间切换。

```
<!DOCTYPE html>
<html lang="lang">
  <head>
    <%= if def-direction is ltr %>
    <link rel="stylesheet" href="css/ltr-app.css">
    <%= else %>
    <link rel="stylesheet" href="css/rtl-app.css">
    <%= end %>
  </head>
  <body>
    <main>Main contant</main>
    <aside>Sidebar</aside>
  </body>
</html>
```

或者我们可以在 CSS 文件名中使用变量。

```
<link rel="stylesheet" href="css/#{def-direction}-app.css">
```

上面的 if 语句和文件名变量只是一个[伪代码](http://www.bfoit.org/itp/Pseudocode.html)，它将取决于你的模板引擎和你的服务器端配置。

## 使用助手类和模板

当使用混合了 HTML、erb 或任何其他模板引擎等模板文件的[助手类](https://www.sitepoint.com/using-helper-classes-dry-scale-css/)时，我们需要根据方向动态更改类名，让我们来看一个例子。

```
<div class="text-left">
  <!-- content -->
</div>
```

```
.text-left   { text-align: left; }
.text-right  { text-align: right; }
```

当使用`text-left`类时，`div`内容将总是将内容向左对齐，但是我们需要一种方法来动态地将 RTL 的`-left`部分更改为`-right`。

我们可以通过使用一个模板变量来解决这个问题。

```
<div class="text-#{default-float}">
  <!-- content -->
</div>
```

## 萨斯混合食品

我们已经使用了 Sass 变量和插值，另一种简化的方法是构建另一组 Sass 混合。

```
@mixin float($dir) {
 @if $dir == left {
 float: $def-float;
    } @else if $dir == right {
 float: $opp-float;
    } @else {
 float: $dir;
  }
}

@mixin text-align($dir) {
 @if $dir == left {
 text-align: $def-float;
    } @else if $dir == right {
 text-align: $opp-float;
    } @else {
 text-align: $dir;
  }
}

@mixin padding-left($unit) {
 padding-#{$def-float}: $unit;
}

@mixin padding-right($unit) {
 padding-#{$opp-float}: $unit;
}
```

我们以后可以像这样使用这些混合

```
.media {
 @include float(left);
 @include padding-right(10px);
 @include text-align(left);
}
```

如果你想要更多受 LTR / RTL 影响的不同 CSS 的混合，你可以看看 [bi-app-sass](http://anasnakawa.github.io/bi-app-sass/) 。

## 结论

在这里，我们已经讨论了制作多语言网站不应该意味着我们重复代码块。我们学习了如何使用 Sass 处理 RTL 和 LTR 语言。我们已经创建了几个 mixins 来演示我们如何做到这一点，并讨论了如何让后端帮助事情变得更加简单。

除了 Grunt 和 Sass 之外，您可以使用任何工具来做同样的事情，核心思想是使用 Sass 变量来操作方向和更改模板文件中的变量。我为这篇文章创建了一个 Github repo。你可以查看一下在[RTL-咕噜-萨斯](https://github.com/ahmadajmi/rtl-grunt-sass)中使用的代码。

## 分享这篇文章