# 在 Drupal 中创建定制菜单

> 原文：<https://www.sitepoint.com/creating-custom-menus-in-drupal/>

Drupal 有一个非常可定制的架构，因此您可以扩展 Drupal 来满足您的需求。

Drupal 提供了各种钩子，模块可以使用这些钩子来定制功能，并且有一个强大的菜单系统来在 Drupal 站点中创建菜单项。

Drupal 还为模块开发人员提供了挂钩，以挂钩到 Drupal 菜单系统，并为模块的运行创建菜单项。Drupal 中的菜单系统钩子允许注册 URL，以便模块可以定义如何处理对 URL 的请求，以及模块是否希望将该链接添加到菜单中。

在这篇文章中，我们将看到你的模块如何连接到 Drupal 菜单系统来注册 URL 的回调，以及如何在需要的时候将它们添加到导航菜单中。

## 创建模块

让我们首先创建一个名为 *menudemo* 的独立模块，我们将使用它来演示 Drupal 提供给我们的各种菜单系统功能。

要创建 menudemo 模块，请在 Drupal 安装的`menudemo sitesallmodulescustom`中创建一个文件夹。在该文件夹中创建一个名为`menudemo.info`的文件，包含以下内容。

```
;$Id$
name = menudemo
description = A module to  demo drupals menu system
package = Menu Demo Package
core = 7.x
files[] = menudemo.module
```

文件向 Drupal 系统描述了我们的模块。在 info 文件中，我们描述了各种参数，如模块名、描述、包名等。

同样在你的 menudemo 文件夹中创建你的`menudemo.module`文件。

您的文件夹结构如下:

![menu structure](img/1e7453a7b1cdb9fc5d0e226de3f7193d.png)

一旦添加了文件，您应该能够在 Drupal 模块列表中看到您的模块。您应该去启用模块，如下所示。

![menu module](img/b1af2218289f7699019ea937937cf840.png)

## 通过菜单回调响应 URL

一旦我们用必要的文件创建了我们的模块，让我们看看 Drupal 提供给模块开发人员的钩子，作为一种钩入 Drupal 菜单系统的方法。

要让一个模块挂钩到 Drupal 菜单系统，必须实现`hook_menu`挂钩。这个钩子的实现需要返回模块应该添加的菜单项的关联数组。关联数组将包含菜单的路径作为键，其属性作为数组项的值。

因此，在我们的模块中，我们通过提供函数`menudemo_menu`来实现`hook_menu`，如下所示

```
/**

* Implementation of hook_menu().

*/

function menudemo_menu() {

    $menuitems['menudemo'] = array(

    'title' => 'My Menu',

    'page callback' => 'menudemo_mymenu_page_callback',

    'access callback' => TRUE,

    'type' => MENU_CALLBACK,

    );

    return $menuitems;

}

function menudemo_mymenu_page_callback() {

    return 'My Menu URL was hit';

}
```

在上面的函数`menudemo_menu`中，我们创建了一个关联数组，键为 menudemo，这是我们模块的名字。但是您可以在这里使用一个键，这个键基于您想要在模块中处理和响应的 URL。

然后我们已经传递了定义菜单项的参数，比如它的标题。将类型定义为`MENU_CALLBACK`只是意味着注册一个路径，当被请求时，该路径将能够处理请求。不会创建新的菜单项。

我们还定义了访问回调，并将其设置为`TRUE`，这样每个人都可以访问这个菜单项。

我们已经定义了页面回调，您可以在其中指定在访问此 URL 时应该调用的函数。我们将其定义为`menudemo_mymenu_page_callback`。在函数`menudemo_mymenu_page_callback`中，我们刚刚返回了一个字符串，当我们注册的 URL 被请求时，我们将显示这个字符串。

如果您启用了干净网址，现在只需转到网址`<your drupal installation URL>/menudemo`，如果没有启用干净网址，则转到`<your drupal installation URL>/?q=menudemo`。您应该能够在屏幕上看到页面回调的输出，如下所示

注意:–Drupal 缓存模块实现的挂钩，因此您可能需要转到管理配置开发性能并执行*清除缓存*，以考虑 Drupal 7 中模块实现的挂钩的新变化。

***![my menu](img/a2e58432d871bcfb1e01558601633bc8.png)***

## 从菜单 URL 获取参数

Drupal 还允许您通过将参数传递给回调函数来从 Drupal URL 获取参数。

所以，如果你注册了一个 URL 路径，并且在它后面有值，那么这些值将作为参数传递给你的回调函数。当您希望菜单项根据传递给它的参数以不同的方式工作时，这变得非常方便。

例如，如果你想在你的站点上添加一个菜单项来处理产品，你可以使用相同的回调函数来处理不同的 URL，比如`menudemo/product/add`或者`menudemo/product/view`等等。为了捕捉参数，我们必须修改回调函数，如下所示

```
function menudemo_mymenu_page_callback($firstparameter = '', $secondparameter ='') {
    $result = 'My Menu URL was hit';
    $result.='<br> The first parameter passed to the url is :'.$firstparameter;
    $result.='<br> The second parameter passed to the url is :'.$secondparameter;
    return $result;
}
```

这里有两个参数被捕获并传递给回调函数。然后在回调函数中，我们只是在屏幕上显示参数。因此，如果您访问 URL `<your drupal installation URL>/menudemo/product/edit`,输出将如下所示

![your menu](img/cf358aa966d1b723057d9ca4f76f4eed.png)

## 在导航栏中添加菜单链接

到目前为止，我们已经看到了如何在 Drupal 系统中注册 URL，这样对这些 URL 的任何请求都将由我们的回调函数来处理。

如果我们希望 URL 显示在导航菜单中，我们可以通过更改菜单项的类型来实现。修改后的`menudemo_menu`功能如下

```
/**

* Implementation of hook_menu().

*/

function menudemo_menu() {

    $menuitems['menudemo'] = array(

    'title' => 'My Menu',

    'page callback' => 'menudemo_mymenu_page_callback',

    'access callback' => TRUE,

    'type' => MENU_NORMAL_ITEM,

    );

    return $menuitems;

}
```

在上面的函数中，我们将类型作为`MENU_NORMAL_ITEM`传递。这将使菜单项出现在导航菜单中，如下所示。

![my menu url](img/76e3d579ae9e0a8350c4a209ffd68a11.png)

## 在导航栏中创建子菜单

在导航菜单中创建了该项后，您可能希望在它下面创建子菜单或嵌套菜单项。我们可以将子菜单添加到菜单项中，方法是将子菜单项路径追加到我们在`menudemo_menu function`中传回的关联数组中的菜单项路径之后。

为了添加子菜单，我们将如下更新功能`menudemo_menu`

```
/**

* Implementation of hook_menu().

*/

function menudemo_menu() {

    $menuitems['menudemo'] = array(

    'title' => 'My Menu',

    'page callback' => 'menudemo_mymenu_page_callback',

    'access callback' => TRUE,

    'type' => MENU_NORMAL_ITEM,

    );

    $menuitems['menudemo/submenu'] = array(

    'title' => 'My Sub menu',

    'page callback' => 'menudemo_mysubmenu_page_callback',

    'access callback' => TRUE,

    'type' => MENU_NORMAL_ITEM,

    );

    return $menuitems;

}

function menudemo_mysubmenu_page_callback() {

    $result = 'My  Sub Menu URL was hit';

    return $result;

}
```

这里，我们在关联数组中添加了一个条目，关键字为`menudemo/submenu`。这将在 menudemo 菜单项下创建一个子菜单。我们将函数`menudemo_mysubmenu_page_callback`定义为子菜单项的回调函数，该子菜单项只将文本显示为`'My  Sub Menu URL was hit'`。

现在，如果我们单击子菜单项，我们将能够看到如下输出

![my sub menu](img/0ed184c4b649cacabf5ead8dbf4cef9f.png)

## 结论

Drupal 系统允许模块开发人员通过 Drupal 钩子插入 Drupal 主处理。在本文中，我们看到了如何使用 Drupal 菜单系统的`hook_menu`钩子来注册我们自己的回调函数，以处理对特定 URL 的请求或向导航菜单添加菜单项。

Drupal 系统还将 URL 中的参数传递给回调函数，这有助于编写一个通用回调函数，该函数可以根据传递给它的参数处理多个任务。

现在，根据您试图在 Drupal 中创建的模块，您可能希望添加简单的菜单项或嵌套菜单项，以便让用户轻松访问您试图提供的功能。

祝您在下一个 Drupal 模块中添加菜单时愉快。

## 分享这篇文章