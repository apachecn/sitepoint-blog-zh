# Sass 入门

> 原文：<https://www.sitepoint.com/getting-started-with-sass/>

![Getting Started with Sass](img/cf1801cd70aca7486455dfbb1b1183b3.png)

这是一篇文章的更新版本，最初发表于 2013 年 3 月 1 日。变化包括:更新信息，修复损坏的链接，代码语法突出，增加了特色图片。

随着网站和页面变得越来越复杂，CSS 很快就不堪重负。就布局而言，它仍然比表格好一千倍，但对于使用它的人来说，它可能存在一些限制。

这就是 CSS 预处理程序的用武之地。他们在我们已经知道并喜爱的语法之上添加了一个新的令人敬畏的层。有许多 CSS 预处理程序，但是我将把重点放在抓住我的心的一个上:[](http://sass-lang.com/)。

您将了解 Sass 缩进语法和`.scss`语法之间的区别，您可以从中选择使用 Sass 的工具范围，基本的语言特性和语法，以及一些可以使网页设计更快更有趣的 Sass 扩展。

## 萨斯:快速定义

Sass 是一个 CSS 预处理器。这是一款软件，它为 CSS 添加了一个漂亮的新功能集，比如变量、嵌套和混合。您编写了一个稍微修改的类似 CSS 的语法，然后被处理成 plain-ol'-CSS。这种处理既可以在编码时在开发环境中进行，也可以作为部署过程的一部分。

我更喜欢的方法——因为我不是一个服务器呆子，也不害怕部署脚本——是在我的开发工作流中正确地进行预处理。(稍后将详细介绍这一点。)

## 为什么要顶嘴

这是一个大问题，没有单一的答案。对我来说，Sass 提供了正确的工具、语法和扩展。Sass 背后有一个庞大的社区，并且在不断成长和发展。有许多优秀的附加组件和扩展使得 Sass 变得更好。Sass 还提供了两种写作风格选项。

### Sass 缩进语法

这就像 CSS 中疯狂而聪明的叔叔:古怪，看起来有点滑稽，但最终相当聪明。这种语法一点也不像 CSS——它的目标是简洁。它使用行的缩进来指定块，而不是括号和分号。在使用分号分隔语句的地方，它使用新行。

```
#main
 color: blue
 font-size: 0.3em

  a
 font:
 weight: bold
 family: serif
 &:hover
 background-color: #eee
```

### 。scss

`.scss`本质上是 CSS3 语法的一个子集。这意味着**每个有效的 CSS3 样式表也是有效的`.scss`** 。

```
#main {
 color: blue;
 font-size: 0.3em;

  a {
 font: {
 weight: bold;
 family: serif;
    }
 &:hover {
 background-color: #eee;
    }
  }
}
```

这是我喜欢的语法，也是我在本文中用来展示例子的语法。所以事不宜迟，让我们开始吧。

## Sass 工作流程和工具

### Sass 工作流

进入 Sass 方式需要稍微改变一下范式。要点是你将**永远不会编辑或提交你的。css 文件**。改变 CSS 的唯一方法是通过`.scss`文件。一旦你保存了你的`.scss`文件，预处理程序将会把它转换成普通的 CSS。当你开始编辑你的。css 文件是天空开始充满黑洞的时刻…

有两种常见的方法将预处理器加入到您的工作流中。第一种是在构建时完成。这将需要在您的开发和实际环境中使用您选择的编程语言的解析器进行一些设置。Rails 3.1 及更高版本附带了 Sass。如果你使用 PHP。你很幸运，因为有这些平台的端口。你也可以把它变成一个咕哝/吞咽的任务，或者[让 Webpack](https://www.bensmithett.com/smarter-css-builds-with-webpack/) 在构建时运行处理。

如果这些对你来说都没有意义，不要害怕！第二种选择对初学者更友好:在开发机器上使用一个简单的 GUI，边走边编译。您将需要以下工具之一。

### 用于 Sass 工作流的 GUI(图形用户界面)工具

*   #### [带电作业](http://livereload.com/)

    这个神奇的工具做了一系列很酷的事情，其中之一就是编译 Sass。LiveReload 监控文件系统的变化。保存文件后，会根据需要对其进行预处理，并刷新浏览器。这是一个伟大的快速踢你的网站开发工作流程

*   #### [编码解码器](http://incident57.com/codekit/)

    这是一个非常类似 LiveReload 的 Mac 专用应用，包括脚本缩小、图像压缩和代码提示。这是我选择并深爱的软件

*   #### [Prepros](https://prepros.io/)

    这是一个跨平台(Linux、Mac 和 Windows)的 GUI 工具，用于编译 Sass 和其他预处理程序语言。它的功能非常丰富，在撰写本文时，可以免费无限试用。

## 让我们开始编码 Sass，已经！

### 萨斯嵌套

这可能是萨斯最有趣也是最危险的部分。让我们考虑下面两个例子。

#### 示例 1 —良好的嵌套

```
#navbar {
 width: 80%;
 height: 23px;

  ul { list-style-type: none; }
  li {
 float: left;
    a { font-weight: bold; }
  }
}
```

#### 示例 2 —错误的嵌套

```
div#main {
  #sidebar {
    #navbar {
 width: 80%;
 height: 23px;

      aside {
        div {
          ul { 
 list-style-type: none;

            li {
 float: left;
              a { 
 font-weight: bold;
              }
            }
          }
        }
      }
    }
  }
}
```

示例 2 之所以糟糕，是因为生成的 CSS 将具有过于具体的选择器:

```
div#main #sidebar #navbar {}
div#main #sidebar #navbar aside div ul {}
div#main #sidebar #navbar aside div ul li {}
div#main #sidebar #navbar aside div ul li a {}
```

我们都同意这是一场噩梦。

现代 CSS 的指导原则之一应该是**模块化代码**的原则，也就是说，你可以重用的代码比特，而不必依赖于你的标记的结构。关于这个想法的更多信息，请看 [OOCSS](https://www.sitepoint.com/first-look-object-oriented-css/) 和 [SMACSS](https://www.sitepoint.com/snack-on-snooks-smacss/) 。

有一个事实上的规则叫做[Inception Rule](http://thesassway.com/beginner/the-inception-rule),简单地说，你不应该嵌套超过四个选择器。

### Sass 变量

```
$site_max_width: 960px;
$font_color: $333;
$link_color: $00c;
$font_family: Arial, sans-serif;
$font_size: 16px;
$line_height: percentage(20px / $font_size);

body {
 color: $font_color;
  font {
 size: $font_size;
 family: $font_family;
  }
 line-height: $line_height;
}

#main {
 width: 100%;
 max-width: $site_max_width;
}
```

我们都熟悉变量，并在我们的 JavaScript 等中使用它们。这是完全相同的想法，但是使用 CSS。现在，当客户要求您将蓝色改为稍微绿一点的蓝色(带有更多的 pop)，您可以简单地转到您的变量并更新那个实例。您还可以使用内置的 Sass 函数和运算符，以及在声明中引用其他变量。

### Sass 函数和运算符

像任何编程语言一样，Sass 附带了一套函数和标准操作符。有许多功能可以帮助你处理数字、文本和颜色。数字(包括有单位的数字)有你需要的所有数学运算符:`+`、`-`、`*`、`/`和`%`。

看一下文档,查看可用的优点列表。

### 萨斯混合食品

Mixins 是 Sass 最强大的部分之一，它真的可以让你变得有创造力。mixin 是可重用样式、属性和选择器的集合。您可以将 mixin 视为一个宏或代码片段，以便定期重用。

mixin 的一个很好的例子是可以处理那些烦人的供应商前缀，如下所示:

```
@mixin box-shadow($shadow) {
 -webkit-box-shadow: $shadow;
 -moz-box-shadow: $shadow;
 box-shadow: $shadow;
}
```

或者用于隐藏文本(通常与图像替换技术一起使用):

```
@mixin hide-text {
 font: 0/0 a;
 color: transparent;
 text-shadow: none;
 background-color: transparent;
 border: 0;
}
```

不用说，你将花费大量的时间来开发你自己的 mixins 库，用于你所有的项目。

## 你的第一个 Sass 项目

使用 Sass 的美妙之处在于，你可以根据自己的需要制作任意多的小`.scss`文件，因为它们将被组合成一个主文件。css 文件在预处理阶段。考虑下面的`style.scss`文件:

```
@import "global/mixins";
@import "global/variables";
@import "global/normalize";

@import "content/links";
@import "content/lists";
@import "content/mediaObjects";
@import "content/panels";
@import "content/spacingOverrides";
@import "content/typography";
@import "content/textUtilities";

@import "layout/border";
@import "layout/grid";
@import "layout/scaffolding";

@import "navigation/breadcrumbs";
@import "navigation/master";
@import "navigation/leftnav";
@import "navigation/topnav";
@import "navigation/pagination";
@import "navigation/tabs";
@import "navigation/footer";

@import "forms/buttons";
@import "forms/forms";

@import "tables/tables";
@import "tables/forum";
```

这些`@import`语句中的每一个都包含一个新的`.scss`文件。当命名这些组件`.scss`文件时，确保在文件名的开头使用下划线(`_`)字符。

例子:`_variables.scss`，`_normalize.scss`。Sass 编译器会将这些文件解释为代码片段，实际上不会单独对它们进行预处理。只有您希望预处理器编译的文件才可以不带下划线命名，例如`style.scss`、`special-page-override.scss.`

当您将片段文件导入到主样式文件中时，不需要包含文件扩展名的下划线，因为 Sass 知道您正在处理`.scss`文件。

正如您可能已经猜到的，将您的 Sass 文件组织到文件夹中以方便逻辑和结构的分离是完全有意义的——您可以根据需要变得尽可能复杂，但请记住，您所做的一切都需要在六个月后当您回来修改内容时有某种意义。不要给你未来的自己(或者不同的开发者)增加难度。

## 扩展 Sass

有许多优秀的插件或框架构建在 Sass 之上。但是我将只谈论一个——全能的指南针。

### 指南针

用 Sass 和 Compass 工作让我微笑。事实上，当我写关于 Compass 的文章时，我在微笑，我希望当你读完这篇文章时，你也会这样。Compass 是建立在 Sass 之上的全功能框架，包括可重用模式、精灵生成、排版节奏和所有你需要的 CSS3 混合。

我上面说的编译软件都将可以处理 Compass，所以 Compass 入门是一个非常简单的过程。

在 web 项目的根目录下创建一个名为`config.rb`的文件。这个文件将告诉 Sass 您将在您的项目中使用 Compass。下面的代码是运行 Compass 所需的最少代码:

```
http_path = "/"
css_dir = "css"
sass_dir = .scss"
images_dir = "images"
javascripts_dir = "js"
fonts_dir = "fonts"

output_style = :expanded
environment = :development
```

查看[这个要点](https://gist.github.com/nathansmith/1179395)中的`config.rb`文件示例。

下一步是将 Compass 导入到主样式文件中。您可以在需要时导入整个 Compass 框架或其中的一部分:

```
@import "compass";

@import "global/mixins";
```

或者…

```
@import "compass/utilities";
@import "compass/css3";
@import "compass/typography/vertical_rhythm";

@import "global/mixins";
```

我可以再花 5000 个字来抒情地描述 Compass，因为它有太多的内容，但我要挖掘它最强大的功能之一…

### 用指南针生成精灵

Sprites 已经迅速成为使用 CSS 在网络上传递图像的默认标准。简而言之，您创建了一个包含页面所需的所有较小图像的大图像，从而减少了 HTTP 服务器请求，这对网站性能有好处。如果你对这个概念不熟悉，那么我建议你快速阅读一下。

精灵对于网络来说是很棒的，但是维护它们会很快变得很痛苦。随着项目的进展，我经常发现我必须更新我的精灵，这变成了一件苦差事，因为你不仅要更新你的主精灵表，还要更新你的 CSS(可能还有 HTML)。

这些年来，已经引入了一些很棒的工具来帮助简化这个过程，但是没有一个工具让我满意。直到现在。

这里的主要思想是，你应该创建单一的图像，因为你会在过去的日子里。重要的是将你的图片组织到文件夹中，如下图所示。

![](img/1c9c7f137833b6559328b1aff997a042.png)

*Screenshot of organised image folder*

下一步是包含所需的 compass mixins 和 utils，并导入您的图像。在下面的例子中，我将说明在使用`images/icons`文件夹时这是如何工作的:

```
// include the compass spite utility if not already included
@import "compass/utilities/sprites";

// import your images
// note: compass already knows my images live in thimg/ folder
@import "icons/*png";

// automagically generate class names
@include all-icons-sprites;
```

我将带您看一下上面代码片段中发生了什么。

*   第一个导入是导入正确的罗盘精灵工具
*   下一个导入是告诉 Compass 你想用作精灵的图像在哪里
*   include 行是一些奇迹发生的地方。这一行将根据图像文件夹的名称和其中的 png 名称自动生成一组类名。

生成的 CSS 如下所示:

```
.icons-actions-add-mini,
.icons-actions-delete-mini,
[...]
.icons-multimedia-camera {
  background: urlimg/icons-s34fe0604ab.png') no-repeat;
}

.icons-actions-add-mini {background-position: 0 0;}
.icons-actions-delete-mini {background-position: 0 -16px;}
[...]
.icons-multimedia-camera { background-position: 0 -96; }
```

现在，你可以走开，在任何你想去的地方愉快地使用这些生成的类。任务完成。谢谢，指南针！

然而，我经常发现我需要添加自己的类名——也许是基于嵌套元素。别担心。指南针允许我们这样做。

**注意:** *在下面的例子中，param 传递给那些`icons-sprite-*()` utils 的名字是。您的`images/icons`文件夹中的 png 文件。*

```
@import "icons/*.png";

.actions {
  .add    { @include icons-sprite(actions-add-mini); }
  .delete { @include icons-sprite(actions-delete-mini); }
}

.contact {
  .mail  { @include icons-sprite(contact-mail); }
  .phone { @include icons-sprite(contact-phone); }
}

.some-random-class-name { @include icons-sprite(hardware-television); }
```

要详细浏览用指南针建造精灵的过程，请点击 SitePoint 上的 [George Martsoukos 的文章。](https://www.sitepoint.com/css-sprites-sass-compass/)

Compass 允许您进行许多其他定制。下一个例子是我经常使用的:

```
// making use of width/height magic selectors
.icon-giant-killer-bear {
 $width: icons-sprite-width(giant-killer-bear);
 $height: icons-sprite-height(giant-killer-bear);
 @include icons-sprite(giant-killer-bear);
 @include size($width, $height);
 margin: 0 4px 0 0;
 vertical-align: text-bottom;
}
```

上面的例子说明了从你的精灵中获取一个特定图像的尺寸是多么的快。

我还没有深入挖掘所有的精灵工具，但是可以在 Compass 网站上查看完整的精灵文档,因为我相信那里还有很多其他很棒的东西。

## 综上

前端代码有一个令人兴奋的世界，这只是其中很小的一部分。现在就开始使用 Sass 吧！这让我的代码编写更加高效，也让编写 CSS 更加有趣。

### 后续步骤

![](img/4d20665ed0fea79b8a8c2ebcd8c26400.png)[Get a Jump Start on Sass today!](https://www.sitepoint.com/premium/books/jump-start-sass)SitePointThis book provides a comprehensive introduction to the popular CSS preprocessor Sass for beginners.[](https://www.sitepoint.com/premium/books/jump-start-sass)[](https://www.sitepoint.com/premium/books/jump-start-sass)![](img/b7a9b8af48f7a3bfe541853f110823eb.png)[A collection of Sass tips to get you started](https://www.sitepoint.com/premium/courses/starting-sass-2970)SitePointLearn how to install and set up Sass and work with variables. Understand the difference between Sass and SCSS, and create color palettes and partials. Lastly, you'll cover functions and mixins.[](https://www.sitepoint.com/premium/courses/starting-sass-2970)[](https://www.sitepoint.com/premium/courses/starting-sass-2970)

## 分享这篇文章