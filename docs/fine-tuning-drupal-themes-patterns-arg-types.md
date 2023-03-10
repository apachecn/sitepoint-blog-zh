# 用模式、参数和类型微调 Drupal 主题

> 原文：<https://www.sitepoint.com/fine-tuning-drupal-themes-patterns-arg-types/>

在本文中，我们将讨论如何利用各种 Drupal API 函数来实现更细粒度的主题化。我们将讨论模板预处理和使用路径模式、类型和参数()改变钩子。我们将使用 arg()函数，该函数返回当前 Drupal URL 路径的一部分和一些模式匹配，例如当您想要匹配 URL 中的模式时。我们还将看看如何为一组内容类型创建一个变量。

模板预处理是定义页面中使用的变量的一种方法。例如，您可以定义一个 body 类变量。反过来，您可以在您的 Sass 或 CSS 中使用结果类。alter 或 build 挂钩是在页面呈现之前运行的，因此您可以做一些事情，例如向特定页面或内容类型添加 JS 或 CSS。

我将解释和演示这些挂钩，以及如何使用它们来:

*   将一个`<body>`类添加到一个特定的页面中，以获得更好的主题
*   向特定页面和路径添加 Javascript 或 CSS
*   使用通配符路径参数
*   使用`preg_match`进行 URL 模式匹配
*   创建一个内容类型数组，用作参数中的变量
*   使用路径参数作为参数

我们在这里讨论的函数将被添加到你的主题的*template.php*文件中。虽然您也可以在自定义模块中使用这些函数，但是您需要指定这些函数不是用于管理页面的，除非这是您的意图，我将介绍如何做到这一点。

## 入门指南

当在你的主题中使用预处理或修改函数时，你需要确保*template.php*存在，如果不存在，你可以在你的主题的根目录下创建这个文件。所以如果我的主题名是 *foobar* ，那么到【template.php】T4 的路径将是:

```
/sites/all/themes/foobar/template.php
```

API 函数以你的主题的机器名开头。例如，如果你使用的是`hook_page_alter`，你的主题名是 foobar，我们就把它写成`function foobar_page_alter()`。(机器名就是主题的文件夹名。)

## 使用内容类型数组的自定义正文类

body 类是添加到 HTML `<body>`标签中的类。例如，在博客页面上，您可能会看到如下内容:

```
<body class="page-node page-node-blog">
```

您可以在您的 Sass 或 CSS 中利用该类来微调您的主题化，只需对您站点上的博客页面进行如下操作:

```
.page-node-blog h1 {
// custom Sass here
}
```

开箱即用，Drupal 7 附带了一些非常好的 body 类，通常这些对于大多数用例来说都很好。此外，Drupal contribution 或 *Contrib* 主题如 Zen 添加了增强和扩展的类。

在我们的例子中，我们希望向一些页面添加一个类，这些页面共享一些公共属性，但不一定来自相同的内容类型。比方说，我们有两种内容类型，我们想添加一个特定的类，以主题相似，但可能不同于我们网站上的其他页面。我们可以构建一个 Drupal 内容类型数组，然后使用这个数组来添加类。一旦我们定义了数组，我们只需检查以确保给定的节点存在，然后将数组传入。

```
<?php

/**
 * Implements template_preprocess_html().
 *
 * Define custom classes for theming.
 */
function foobar_preprocess_html(&$vars) {

  // Build a node types array from our targeted content types.
  $foo_types = array(
    'landing_page',
    'our_services',
  );

    // Define the node.
    $node = menu_get_object();

  // Use the array to add a class to those content types.
  if (!empty($node) && in_array($node->type, $foo_types)) {
    $vars['classes_array'][] = 'page-style-foobar';
    }
  }
```

这个函数为通常在结束 HTML `</head>`标签之前的任何东西预处理变量，结束 HTML`</head>`标签在 Drupal 的核心模板*html.tpl.php*中。我们使用`$vars['classes_array']`添加 body 类。这个变量用标签`<body>`中的`<?php print $classes; ?>`来呈现。在我们的例子中，这个类将只在 *landing_page* 和 *our_services* 内容类型中呈现。现在我们可以在 Sass 或 CSS 中使用`.page-style-foobar`来设计这些页面的样式。

## URL 模式匹配

您还可以使用 URL 模式匹配来添加有用的自定义类。假设我们有一个“我们的服务”登录页面，然后在该路径下有一些子页面。URL 架构可能如下所示:

```
example.com/our-services
- example.com/our-services/subpage-1
- example.com/our-services/subpage-2
```

我们将使用`preg_match`、regex、PHP matches 和 Drupal 的`request_uri`函数向这些页面添加一个自定义的 body 类。再一次，我们把它放在上面的`foobar_preprocess_html`中。

```
<?php
function foobar_preprocess_html(&$vars) {

  // Define the URL path.
  $path = request_uri();

  // Add body classes to various pages for better theming.
  if (preg_match('|^/our-services((?:/[a-zA-Z0-9_\-]*)*)?|', $path, $matches)) {
    $vars['classes_array'][] = 'page-services';
  }
}
```

现在你可以使用`.page-services .some-common-element`来为这些“我们的服务”页面设置主题。很明显，如果你的 URL 结构改变了，这种方法会有缺陷，所以它可能只适合一些用例。

## 路径参数

定制站点特定部分主题的另一个聪明的方法是使用 arg()将它们分开。Drupal 倾向于使用大量的脚本和 CSS，所以理想情况下，如果你添加额外的 CSS 或 JS，并且你并不需要在每个页面都使用它们(通常使用你的主题的*)。info* 文件)，您可以使用 path arg()将它们添加到需要它们的页面中。例如，如果我想向 Drupal 注册页面添加一个定制脚本，我可以创建一个 path arg() *if 语句*，然后在其中添加脚本。这里我们将关注的 URL 路径是*/用户/注册*，您将看到 arg()是如何分解 URL 结构的。

我们将在这里使用`hook_page_alter`,它可以在页面呈现之前对其进行修改。首先我们定义主题路径并使用 Drupal 的附属函数。

```
<?php

/**
 * Implements hook_page_alter().
 *
 * Add custom functions such as adding js or css.
 */
function foobar_page_alter(&$page, $form) {

  // Define the module path for use below.
  $theme_path = drupal_get_path('theme', 'foobar');

  if (arg(0) == "user" && arg(1) == "register") {
     $foobar_js = array(
      '#attached' => array(
        'js' => array(
          $theme_path . '/js/custom.js' => array(
            'group' => JS_THEME,
          ),
        ),
      ),
    );
    drupal_render($foobar_js);

  }
}
```

在这个函数中，注意我们已经用主题名替换了*钩子*，所以`hook_page_alter`变成了`foobar_page_alter`。arg()数字表示 URL 路径中的位置。零是第一，一是第二，以此类推。通过添加更多的参数，你可以变得非常有创造力。假设您想将 JS 添加到用户页面中，但是它下面没有路径。您可以在初始 arg()后面添加一个 *NULL* arg()。

```
if (arg(0) == "user" && arg(1) == NULL) {
// code here
}
```

在上面的例子中，我们已经在主题的*template.php*文件中实现了各种功能。你也可以在一个自定义模块中使用它们，在这种情况下，你只需要在函数中使用模块名，而不是主题名。当从一个模块主题化时，你可能想要排除管理路径，因为一个模块可以指向你站点中的任何地方。您可以在排除管理路径的情况下这样做。

```
if (!path_is_admin(current_path())) {
// code here
}
```

## 结论

如您所见，利用 Drupal API 工具箱进行主题化扩展了开发人员的视野。上面的例子不是决定性的，但是它们确实给了你一种可能的感觉。作为一名 Drupal 主题开发者，使用这些帮助我扩展了主题化和构建站点的技巧。评论？反馈？把它们留在下面！

## 资源

*   [主 Drupal API 引用](https://api.drupal.org/api/drupal/7)
*   [函数模板 _ 预处理 _html](https://api.drupal.org/api/drupal/includes%21theme.inc/function/template_preprocess_html/7)
*   [函数 hook_page_alter](https://api.drupal.org/api/drupal/modules%21system%21system.api.php/function/hook_page_alter/7)
*   [设置模板中使用的变量(预处理和处理功能)](https://www.drupal.org/node/223430)
*   [函数参数](https://api.drupal.org/api/drupal/includes%21bootstrap.inc/function/arg/7)
*   [函数请求 _uri](https://api.drupal.org/api/drupal/includes%21bootstrap.inc/function/request_uri/7)
*   [函数 drupal_process_attached](https://api.drupal.org/api/drupal/includes%21common.inc/function/drupal_process_attached/7)

## 分享这篇文章