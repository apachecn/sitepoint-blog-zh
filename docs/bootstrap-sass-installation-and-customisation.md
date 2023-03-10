# 引导 Sass 安装和定制

> 原文：<https://www.sitepoint.com/bootstrap-sass-installation-and-customisation/>

Bootstrap 是一个流行的开源框架。通过预构建的组件，它允许所有技能水平的 web 设计人员快速构建一个站点。Bootstrap 的最新版本使用 Sass 作为首选的预处理器。在本文中，我将向您展示如何配置和定制一个基于 Sass 的引导项目。

## 引导安装

有多种方法可以获取和安装引导源文件。请记住，无论您选择什么方法和包管理器，都要确保您有一个 Sass 编译器和 [Autoprefixer](https://github.com/postcss/autoprefixer) 启动并运行。Bootstrap 需要这两者才能工作。

### 下载引导文件

我们可以从[引导下载](http://getbootstrap.com/docs/4.0/getting-started/download/)页面下载引导文件。下载完成后，我们可以将文件内容提取到我们项目的文件夹中。

### 结节

安装了 [Node.js](https://nodejs.org/en/) 之后，我们可以通过在命令行中键入以下命令来快速安装用于引导的 [npm 包](https://www.npmjs.com/package/bootstrap):

```
npm install bootstrap
```

### 红宝石

我们可以使用 [Bundler](https://bundler.io/) 和 [Ruby Gems](https://rubygems.org/) 将 Bootstrap 包含在 Ruby 应用程序中，在 Gemfile 中使用以下命令:

```
gem 'bootstrap', '~> 4.0.0'
```

## 引导 Sass 设置

一旦我们安装了引导程序，我们需要设置我们的项目。我们要做的第一件事是创建一个`sass`文件夹来保存我们的 SCSS 文件，并创建一个`stylesheets`文件夹来存储编译后的 CSS。之后，我们在`sass`文件夹中创建一个名为`app.scss`的文件。

`app.scss`文件用于导入引导组件。

接下来我们要做的是在 Bootstrap Sass 包中找到`_variables.scss`文件，并将其复制到我们的`sass`文件夹中。接下来，我们将文件重命名为`_customVariables.scss`，并为`_customVariables.scss`向`app.scss`添加一个导入语句。首先导入`_customVariables.scss`很重要，原因我稍后会解释。

最后一次导入是一个可选的`_custom.scss`文件。许多人会直接在他们的 import 语句之后或者在他们的`app.scss`文件中包含定制的 CSS 规则，但是我们将把任何定制的规则分离到他们自己的部分中。

注意，我们首先导入我们的`_customVariables.scss`文件。原因是 Bootstrap 的变量被设置为`default!`值，所以我们需要通过首先导入我们的变量来覆盖这些值。

## 定制

当我们编辑变量时，最好制作一份原始变量的副本，然后修改副本。复制后，我们只需注释掉原始变量。这样，万一我们不喜欢这个结果，我们可以回到之前的设置。例如，假设我们要将基本字体大小更改为 20px。首先，我们将查看我们的`_customVariable.scss`文件。变量被分解成几个部分，我们正在寻找字体部分。在那里，我们需要`$font-size-base:1rem !default;`变量，这样我们就可以将它复制到我们的自定义文件中，并注释掉原来的文件。之后就简单到把值改成 20px:

```
//$font-size-base:1rem !default;
$font-size-base:20px;
```

上面，我们已经注释掉了原始变量，并更改了副本。

在尝试定制 Bootstrap 时，请记住有许多变量需要处理。当查找要修改的变量时，建议充分利用文本编辑器的搜索功能。查看一下`_customVariables.scss`文件并熟悉其中的变量也是一个好主意。

另一个有效的方法是在编译之前查看组成 Bootstrap 的原始 SCSS 文件。从那里，我们可以看到在那个模块中使用了哪些变量。例如，假设我们对`.navbar-dark`元素的颜色不满意。我们可以查看`_navbar.scss`文件内部，向下滚动(或使用搜索功能)并查找对颜色变量的引用，而不是试图找出我们需要更改的变量。

```
// navbar-dark
.navbar-dark {
  .navbar-brand {
 color: $navbar-dark-active-color;

 @include hover-focus {
 color: $navbar-dark-active-color;
    }
  }
  // more Sass here
}
```

通过查看这条规则，我们确定需要改变的变量是`$navbar-dark-active-color`。然后我们将进入`_customVariables.scss`，复制/注释掉原始变量，并创建我们自己的变量。

当使用 Bootstrap 的源文件时，我们也可以利用它的 mixins。他们不仅有助于理解 Bootstrap 是如何组合在一起的，他们实际上可能会帮助我们建立我们的网站。例如，我们来看一下`@mixin make-container`:

```
@mixin make-container() {
 width: 100%;
 padding-right: ($grid-gutter-width / 2);
 padding-left: ($grid-gutter-width / 2);
 margin-right: auto;
 margin-left: auto;
}
```

从这个 mixin 中，我们可以看到哪些变量影响了我们的容器。我们现在知道我们可以改变`$grid-gutter-width`来改变容器元素的填充。

## 结论

使用 Bootstrap 可能会很复杂，尤其是对于不熟悉框架的人来说。使用我演示的方法，您应该能够轻松地建立一个引导 Sass 设置和定制框架。既然你知道要寻找什么，找到你需要改变的变量应该更容易。请记住，你的文本编辑器的搜索功能是你的朋友，当你有疑问时，查看 mixins 会有所帮助。

如果你已经掌握了 Bootstrap 的基础知识，但想知道如何将 Bootstrap 技能提升到一个新的水平，请查看我们的[用 Bootstrap 4](https://www.sitepoint.com/premium/courses/building-your-first-website-with-bootstrap-4-2995) 建立你的第一个网站课程，快速而有趣地了解 Bootstrap 的强大功能。

## 分享这篇文章