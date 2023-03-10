# 创建自己的创世纪儿童主题

> 原文：<https://www.sitepoint.com/creating-your-own-genesis-child-themes/>

有这么多的 WordPress 主题框架，很难选择哪一个适合你。你怎么知道哪一个是有助于推广你的内容的优质主题，哪一个会拖你的后腿？最流行的框架之一是创世纪主题框架。让我们来看看 Genesis 框架，以及在创建一个 Genesis 子主题时您可能会期望什么。

![WordPress and Genesis](img/8ae31c6f81928931a9a438e9a7ba490f.png)

StudioPress 的 Genesis 框架设计用于儿童主题。大多数 WordPress 主题是这样的，但是 Genesis 没有添加或者要求任何你需要导入或者带入你的孩子主题的特殊东西。

## 文件结构

子主题开发过程从一个新目录开始。转到开发区域的主题部分，在`themes`下创建一个新文件夹。如果你正在使用默认的创世纪主题，或者你正在使用一个特定的主题，你会想要选择主题的名称并添加“-child”到文件夹名称。你可以给它起任何你喜欢的名字，但是如果你正在处理一个特定的主题，我总是建议把它命名为你正在处理的主题的子主题，以减少混乱。你需要将`style.css`和`functions.php`文件复制到空的子文件夹中。

## 样式表

您可以将所有 CSS 保留在样式表中，也可以选择将其移除。因为子主题会覆盖或添加到父主题中，所以您实际上并不需要它两次。在 CSS 文件中，您需要添加几行文本来提供关于主题的信息。您需要包括:

*   主题名称:这里是子主题的名称。
*   主题 URI:这是一个网站，他们可以查看演示或找到更多关于儿童主题的信息。
*   描述:描述你的孩子主题和它的品质。
*   作者:你的名字给自己增光。
*   作者 URI:你的个人网址在这里。
*   版本:如果您计划改进和创建未来的版本。从 1.0 开始。
*   标签:这些标签是帮助人们找到你的主题的关键词。这可能是颜色、特征等。
*   模板:与父创世纪主题匹配的名称(区分大小写)。

## 导入 CSS

您可以从父 Genesis 主题中导入 CSS 样式，但是只有当您想要父主题中的所有 CSS 时才应该这样做。如果你看到的只是默认的没有任何样式的创世纪主题结构，你可能根本不想导入任何东西，这很好。你可能想从头开始，建立自己独特的外观。进口所有现有的风格，并不得不筛选和修改它们可能只会使事情复杂化。

如果您想引入父主题样式，一个好的方法是使用下面的脚本在您的`functions.php`文件中排队:

```
add_action( 'wp_enqueue_scripts', 'my_child_theme_scripts' );
function my_child_theme_scripts() {
    wp_enqueue_style( 'parent-theme-css', get_template_directory_uri() . '/style.css' );
}
```

## functions.php

这个文件对于您的子主题添加特性和功能是必不可少的。这是你添加所有钩子和过滤器的地方，Genesis 广泛使用了它们。首先，我们需要激活孩子主题中的核心创世纪功能。

```
<?php
//* this will bring in the Genesis Parent files needed:
include_once( get_template_directory() . '/lib/init.php' );

//* We tell the name of our child theme
define( 'Child_Theme_Name', __( 'Genesis Child', 'genesischild' ) );
//* We tell the web address of our child theme (More info & demo)
define( 'Child_Theme_Url', 'http://gsquaredstudios.com' );
//* We tell the version of our child theme
define( 'Child_Theme_Version', '1.0' );

//* Add HTML5 markup structure from Genesis
add_theme_support( 'html5' );

//* Add HTML5 responsive recognition
add_theme_support( 'genesis-responsive-viewport' );
```

## 儿童主题如何工作

如果您尝试激活子主题，您会注意到，与其他主题不同，您的子主题的图像是空白的。这可能会让你有点害怕，但这是一个简单的解决方法。打开你的图形程序。它可以是 Illustrator、Fireworks、Photoshop，甚至是免费的图像编辑软件，只要它能生成 PNG 文件。创建一个 300 像素 x225 像素的文档，放入一个图像或图形，告诉用户这是什么子主题。以`screenshot.png`的名字保存到你的儿童主题文件夹，现在它会显示出来。

要启用子主题，您应该首先安装/激活父主题。这是重要的一步。一旦你这样做了，你可以选择你的孩子主题并激活它。现在，您已经设置好了自定义外观和自定义功能。

## 添加到创世纪

为了增加功能，我们将添加到我们在子主题文件夹中创建的`functions.php`文件中。WordPress 中一个函数有两个重要部分，分别是**钩子**和**过滤器**。

## 挂钩和过滤器

钩子是我们在函数中调用的代码块。这是我们“挂钩”到创世纪中的一段代码，这就是这个名字的来源。

让我们举一个常见的例子，那就是你的博客页面的摘录长度。我受不了默认字数，55。我觉得太长了。您需要挂钩摘录长度并更改它，并将其作为过滤器添加到您的子主题中。代码应该是这样的。

```
function the_excerpt_length( $length ) {
$length = '25'; 
return $length; 
} 

add_filter( 'excerpt_length', 'the_excerpt_length' );
```

上面的代码创建了一个名为“the _ extract _ length”的函数，括号中的值被设置为您指定的值。代码被告知将它作为摘录的单词长度返回。现在，当你在一个页面上工作时，例如 index.php 或 archive.php，而不是 _ 内容，你可以用它来代替 _ 摘录，将单词减少到最多 25 个。这是一个有用的例子，但是我们还可以做得更多——我们将在以后的文章中看到。

## 结论

建立你自己的创世纪儿童主题听起来很吓人，但是比看起来容易。只需两个必要的文件，您就可以快速创建自己的自定义子主题。使用钩子和过滤器，您可以定制您的主题的功能，而不用担心在更新父主题时丢失您的更改。

## 分享这篇文章