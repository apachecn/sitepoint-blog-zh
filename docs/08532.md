# WordPress 中的管理菜单

> 原文：<https://www.sitepoint.com/admin-menus-in-wordpress/>

乍看之下，WordPress 的管理系统运行良好。内置的菜单功能处理了很多问题，比如添加和删除菜单项，但是你可能想了解更多关于 WordPress 如何在内部处理它的菜单来利用更多的控制。下面的例子将展示你如何实现一个插件，使你能够在解释 WordPress 菜单系统的细微差别的同时，按照你希望的顺序重新排列菜单项。

## WordPress 如何订购菜单项

为了更好地展示菜单项的位置是如何引起问题的，让我们创建一个小插件来演示 WordPress 在添加菜单项时排序菜单项的方式。

在`plugins`目录下创建一个目录，比如`<root-installation>/wp-content/plugins/adminpagearranger`。然后，在目录中创建一个名为`init.php`的文件，并使用 WordPress 插件描述符语法来识别系统的插件。

WordPress 文档建议添加管理页面应该用`admin_menu`操作钩子来完成，所以把它直接添加到描述符下面。

向管理面板添加菜单项的最简单方法是使用带有七个参数的`add_menu_page()`函数:

*   显示在页面标题中的文本
*   用于显示菜单项的菜单项文本
*   您定义的允许访问此菜单项的功能
*   一种菜单标记标识符，用于将该菜单与系统中的其他菜单区分开来
*   在浏览器中呈现页面的功能
*   与菜单一起显示的图标的路径，以及
*   新菜单项相对于其他菜单项应该出现的位置

将以下代码放入`init.php`:

```
<?php
/*
Plugin Name: Admin Page Arranger
Plugin URI: http://www.example.com/adminpagearranger
Description: A plugin to adminster admin pages
Version: 0.1
Author: Tim Smith
Author URI: http://www.example.com/
License: GPL2
*/

add_action("admin_menu", "addMenu");

function addMenu() {
    add_menu_page("My New Menu", "My New Menu", "edit_posts",
        "mynewmenu", "displayPage", null, 1);
}
```

该代码将生成一个新的页面链接，它位于菜单结构的顶部，就在仪表板的上方:

但是，请考虑下面的`addMenu()`实现:

```
<?php
function addMenu() {
    add_menu_page("My New Menu", "My New Menu", "edit_posts",
        "mynewmenu", "displayPage", null, 2);
}
```

只需将位置从 1 更改为 2，就可以用新菜单替换仪表板条目！为什么？这都与 WordPress 如何存储它的菜单配置有关。

WordPress 使用一个名为`$menu`的全局数组来存储菜单配置，数组中的每个键代表菜单中的位置。数组中的位置越高，表示菜单中的位置越高。然而，WordPress 在启动时用默认值填充这个数组。例如:

```
Array
(
    [2] => Array
        (
            [0] => Dashboard
            [1] => read
            [2] => index.php
            [3] => 
            [4] => menu-top menu-top-first menu-icon-dashboard
            [5] => menu-dashboard
            [6] => div
        )

    [4] => Array
        (
            [0] => 
            [1] => read
            [2] => separator1
            [3] => 
            [4] => wp-menu-separator
        )

    [5] => Array
        (
            [0] => Posts
            [1] => edit_posts
            [2] => edit.php
            [3] => 
            [4] => open-if-no-js menu-top menu-icon-post
            [5] => menu-posts
            [6] => div
        )

    [10] => Array
        (
            [0] => Media
            [1] => upload_files
            [2] => upload.php
            [3] => 
            [4] => menu-top menu-icon-media
            [5] => menu-media
            [6] => div
        )
...
)
```

数组中的每个条目实际上是另一个数组，提供每个菜单项的细节。因此，当执行在位置 2 添加菜单项的调用时，它实际上替换了之前可能在位置 2 的内容。在这种情况下，这是仪表板的信息。

当您添加新菜单项时，请注意您指定的位置。还要注意，提供给`add_menu_page()`函数的参数现在已经用于填充全局数组。

## 指定自定义菜单顺序

现在你知道了 WordPress 如何存储它的菜单配置，你可以用它来控制菜单项出现在`$menu`数组中的位置，从而改变菜单出现的顺序。WordPress 提供了两个过滤器，可以用来改变菜单:`menu_order`和`custom_menu_order`。

`custom_menu_order`要求返回一个布尔值，表明是否应该使用自定义菜单。实际上，从`custom_menu_order`返回的值决定了是否应用`menu_order`过滤器。

`custom_menu_order`过滤器的返回值应该返回一个数组，该数组详细描述了您希望按顺序出现的菜单的 slugs。您可以使用下表选择与标准菜单项相关的 slugs。

```
<?php
add_filter("custom_menu_order", "allowMenuStructure");
add_filter("menu_order", "loadMenuStructure");

function allowMenuStructure() {
    return true;
}

function loadMenuStructure() {
    return array("index.php", "tools.php");
}
```

从`custom_menu_order`返回数组时，不必指定要显示的所有 slugsWordPress 将会自动地用菜单中的剩余菜单项来填充菜单的剩余部分，按照它们通常被放置的顺序。

## 创建自定义菜单管理器

我们可以利用`custom_menu_order`过滤器，通过在 WordPress 中存储一个数组，在应用该过滤器时返回该数组，从而以期望的顺序填充菜单。您可以通过 update_option 来实现这一点，update _ option 要么创建一个值，要么更新一个值，稍后您可以用`get_option()`调用这个值。WordPress 将数据存储在数据库表`wp_options`中，以便以后检索。如果该选项尚未发送，或者由于某种原因而不存在，您可以基于默认菜单结构创建它。

您应该做的第一件事是添加您自己的菜单项，以便您可以管理菜单顺序。从`add_menu_page()`返回的值是一个“钩子后缀”,你可以把它附加到一些特定于页面的动作钩子上。这里我们感兴趣的是`load-*page_hook*`，这里的`*page_hook*`应该替换为钩子后缀。

这个钩子是处理表单提交的理想位置，因为它只有在加载这个页面时才会出现。

更改我们之前用来测试菜单功能的`addMenu()`函数，如下所示:

```
<?php
function addMenu() {
    $page = add_menu_page("Admin Manager", "Admin Manager",
        "manage_options", "adminmanager", "displayAdminManager",
        null, null);
    add_action("load-" . $page, "handleMenu");
}
```

这段简短的代码要求我们再定义两个函数——`displayAdminManager()`负责显示页面，而`handleMenu()`用于处理我们对菜单顺序所做的任何更改。

然而，在定义这些之前，定义一个函数加载从`wp_options`表获得的菜单结构或设置一个默认值是有意义的。这个函数将在几个地方使用。

```
<?php
function getMenuStructure() {
    $structure = get_option("menustructure");
    if (!$structure) {
        global $menu;
        $newMenu = array();
        foreach ($menu as $menuItem) {
            $newMenu[] = $menuItem[2];
        }
        return $newMenu;
    }
    else {
        return $structure;
    }
}
```

`getMenuStructure()`使用`get_option()`尝试从数据库中提取菜单结构。如果它不可用，那么它构造一个数组来传递回来。这个函数将用于实际填充你的新菜单结构，并在显示插件时列出菜单项，这样你可以在结构中上下移动菜单项。

你可能会发现一些插件开发者把他们自己的菜单项放在他们想放的任何地方。这有时会很不方便，因为你会发现正常的菜单项会与它们应该在的地方分开。插件开发者一般不建议这样做，但是你会发现偶尔有人觉得他们的插件比其他的更重要，所以他们把它添加到菜单系统的顶部。如果您想解决这个问题，并确保所有其他附加菜单项都放在其他所有菜单项的下面，您可以将该函数更改为:

```
<?php
function getMenuStructure() {
    $structure = get_option("menustructure");
    if (!$structure) {
        return array("index.php", "separator1", "edit.php",
            "upload.php", "link-manager.php",
            "edit.php?post_type=page",
            "edit-comments.php", "separator2", "themes.php",
            "plugins.php", "users.php", "tools.php",
            "options-general.php", "separator-last");
    }
    else {
        return $structure;
    }
}
```

这将强制执行默认的 WordPress 菜单结构，任何其他项目将在它之后显示。

在硬编码结构中，注意我也使用了分隔符。分隔符应该用单词“separator”后跟一个整数进行唯一标识，最后一个应该标识为“separator-last”。

现在你有了一个获取菜单结构的通用函数，你可以定义这个函数来显示菜单管理器，这样菜单就可以被操作了。您将需要包括链接，以便当点击时，它们重新加载页面，然后运行`load-*page_hook*`动作。

```
<?php
function displayAdminManager() {
    $menu = getMenuStructure();
    foreach($menu as $key => $menuitem) {
        $uplink = "admin.php?page=adminmanager&menuitem=" . $key . "&direction=up";
        $downlink = "admin.php?page=adminmanager&menuitem=" . $key . "&direction=down";
        echo $menuitem . ' - <a href="' . $uplink . '">Up</a> <a href="' . $downlink . '">Down</a><br>';
    }
}
```

注意，我们调用页面本身；该页面的 URL 是`/admin.php?page=adminmanager`。`adminmanager`标识符是通过第四个参数`add_menu_page()`添加页面时设置的 slug。还有几个参数详细说明了菜单项应该移动的方向(即向上或向下)以及哪个菜单项会受到影响。

所以，现在你有了一种方法，我们可以通过定义为`handleMenu()`的`load-*page_hook*`将参数传递给进程。这样做的关键是，我们根据指定的方向交换菜单项，完成后，你可以使用`update_option()`来存储你的新菜单结构。那么查看者被重定向回“这个”页面。这确保了新菜单被加载和实现。

通过测试要操作的菜单项是数组中的第一个还是最后一个来确保菜单项不会从结构中移出也是一个好主意。

```
<?php
function handleMenu() {

    if (isset($_GET["menuitem"]) && isset($_GET["direction"])) {
        $structure = get_option("menustructure");
        $numberItems = count($structure);
        $lastIndex = $numberItems - 1;

        if (!$structure) {
            $menu = getMenuStructure();
        }
        else {
            $menu = $structure;
        }

        // if the menu item is to be moved UP the menu
        if ($_GET["direction"] == "down" && $_GET["menuitem"] != $lastIndex) {
            // move the menu up
            $currentIndex = $_GET["menuitem"];
            $nextIndex = $currentIndex + 1;

            $currentMenuItem = $menu[$currentIndex];
            $nextMenuItem = $menu[$nextIndex];

            $menu[$currentIndex] = $nextMenuItem;
            $menu[$nextIndex] = $currentMenuItem;

            update_option("menustructure", $menu);
        }
        //if the menu item is to be moved DOWN the menu
        elseif ($_GET["direction"] == "up" && $_GET["menuitem"] != 0)
        {
            //move the menu down
            $currentIndex = $_GET["menuitem"];
            $previousIndex = $currentIndex - 1;

            $currentMenuItem = $menu[$currentIndex];
            $previousMenuItem = $menu[$previousIndex];

            $menu[$currentIndex] = $previousMenuItem;
            $menu[$previousIndex] = $currentMenuItem;

            update_option("menustructure", $menu);
        }

        wp_redirect("admin.php?page=adminmanager");
    }
}
```

由于动作发生的顺序，重定向是必要的。`load-*page_hook*`动作发生在`custom_menu_order`过滤器之后，所以更新“menustructure”选项发生了，但是您看不到效果，因为自定义菜单顺序已经加载。重定向将确保新的菜单结构通过本质上“刷新”页面和 WordPress 生命周期来正确实现。

## 摘要

本教程教你如何在 WordPress 中构建内部管理菜单。您构建了一个简单的插件来实现一个可以改变菜单项顺序的系统。我们使用 WordPress 的内部选项来存储和检索这些数据，并使用自定义过滤器来确保我们的自定义菜单顺序得以实现。

<small>[詹姆斯投出](http://www.shutterstock.com/gallery-74155p1.html) / [快门](http://www.shutterstock.com)</small>

## 分享这篇文章